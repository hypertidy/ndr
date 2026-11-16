# Open a dataset from a file or URL

Read a multidimensional data source into an ndr Dataset, through one of
two engines:

## Usage

``` r
open_dataset(dsn, vars = NULL, ..., group = NULL, engine = NULL)
```

## Arguments

- dsn:

  Data source name. A file path, URL, or GDAL connection string (e.g.
  `'ZARR:"/vsicurl/https://example.com/store.parq"'`).

- vars:

  Character vector of variable names to include. Default `NULL` includes
  all data variables. Use
  [`character()`](https://rdrr.io/r/base/character.html) for schema +
  coords only. All variables are loaded lazily on first access via `$`.

- ...:

  Passed to
  [`zaro::zaro()`](https://hypertidy.github.io/zaro/reference/zaro.html)
  for the `"zaro"` engine (e.g. `anonymous = TRUE`, `region =`);
  otherwise unused.

- group:

  Path of the group to open in a nested store, e.g.
  `"measurements/reflectance/r10m"`. Default `NULL`, the root group (or
  the group named in `dsn` after `.zarr/`, see Details).

- engine:

  `"gdal"` or `"zaro"`. The default picks `"zaro"` for zaro objects and
  for `s3://`, `gs://`, `virtualizarr://`, `byterefs://`, `mdimrefs://`,
  `flatrefs://` and `reference+json://`/`reference+parquet://` sources,
  and for local or `http(s)://` paths ending in `.zarr` (or with a group
  after it) when zaro is installed; else `"gdal"` (including `ZARR:`
  connection strings and `/vsi` paths).

## Value

A [Dataset](https://hypertidy.github.io/ndr/reference/Dataset.md) with
coordinates, global attributes, and lazy data variables whose values are
read only when used.

## Details

- `"gdal"`: any source GDAL's multidim API can read (NetCDF, HDF5, Zarr
  v2/v3, kerchunk-parquet virtual stores, VRT multidim), from local
  paths, `/vsicurl/`, `/vsis3/` and other GDAL virtual filesystems.
  Needs the GDAL7 package (`remotes::install_github("rgdal-dev/GDAL7")`,
  GDAL \>= 3.10).

- `"zaro"`: Zarr V2/V3 and virtual Zarr (kerchunk, VirtualiZarr Parquet,
  mdim-refs) read by the zaro package
  (`remotes::install_github("hypertidy/zaro")`), with no GDAL in the
  path. Takes anything
  [`zaro::zaro()`](https://hypertidy.github.io/zaro/reference/zaro.html)
  opens, such as `s3://` and `gs://` URIs, `virtualizarr://`,
  `byterefs://` (or `mdimrefs://`) and `flatrefs://` (a byterefs flat
  refs store, read by row group) sources, or a store or
  [`zaro::zaro_dataset()`](https://hypertidy.github.io/zaro/reference/zaro_dataset.html)
  object.

Reading data needs the altarr package
(`remotes::install_github("hypertidy/altarr")`).

### Lazy loading

`open_dataset()` reads only coordinates and metadata. Accessing a data
variable via `ds$var_name` returns a
[DataArray](https://hypertidy.github.io/ndr/reference/DataArray.md)
whose data is a lazy chunked array (see
[lazy-data](https://hypertidy.github.io/ndr/reference/lazy-data.md)):
still no array data is read.
[`sel()`](https://hypertidy.github.io/ndr/reference/indexing.md) and
[`isel()`](https://hypertidy.github.io/ndr/reference/indexing.md) stay
lazy, reductions stream the array chunk by chunk, and arithmetic or
[`collect()`](https://hypertidy.github.io/ndr/reference/collect.md) read
the selected values. This allows opening large datasets (e.g. 12TB
BRAN2023) without reading any array data. Use `vars` to limit which
variables are available. Lazy reads need the altarr package
(`remotes::install_github("hypertidy/altarr")`).

### Variable classification

Arrays are classified as coordinates or data variables based on CF
conventions: a 1D array whose name matches its dimension name is treated
as a coordinate. Every other array with one or more dimensions is a data
variable, including 1D arrays along another dimension (such as a mean
per band) and bounds arrays (e.g. `time_bnds`). Scalar arrays are
skipped.

Numeric and logical data variables are lazy. String data variables can't
be (lazy arrays are numeric), so they are read at open when they have at
most `getOption("ndr.eager_string_max", 1e6)` elements, and skipped with
a message when larger. String coordinates are read like any other. The
GDAL engine reads strings with GDAL7 from rgdal-dev/GDAL7#15; with an
older GDAL7 it skips string arrays with a message (string coordinates
too, leaving that dimension without coordinate values).

### Coordinate types

Regular spatial grids (equal spacing within floating-point tolerance)
are stored as
[ImplicitCoord](https://hypertidy.github.io/ndr/reference/ImplicitCoord.md)
(offset + step, no data allocation). Irregular grids and time
coordinates are stored as
[ExplicitCoord](https://hypertidy.github.io/ndr/reference/ExplicitCoord.md).

### CF time decoding

Time dimensions (GDAL type "TEMPORAL") are automatically decoded from
their CF units (e.g. "days since 1800-01-01") to R Date or POSIXct
values using
[`cf_decode_time()`](https://hypertidy.github.io/ndr/reference/cf_decode_time.md).

### The zaro engine

Consolidated metadata is one request for the whole store. Data variables
are CF-decoded as they are read: fill and missing values read as `NA`,
and `scale_factor`/`add_offset` are applied. Those attributes move from
the Variable's `attrs` to its `encoding`. Arrays without dimension names
get names from zaro (see
[`zaro::zaro_dataset()`](https://hypertidy.github.io/zaro/reference/zaro_dataset.html)).

### Arrays a store can't decode

A byterefs store (`byterefs://`) names the arrays whose codecs zaro does
not know, rather than let them be misread (byterefs decision 0007). zaro
warns about them at open; the Dataset leaves them out of its coordinates
and variables, lists them with the reason when printed, and asking for
one (`ds$v`, `vars = "v"`) is an error giving the reason.

### Georeferenced grids

A georeferenced grid becomes one
[AffineIndex](https://hypertidy.github.io/ndr/reference/AffineIndex.md),
by the same rules in both engines:

- GeoZarr (`spatial:dimensions`, `spatial:transform`,
  `spatial:registration`, optional `spatial:shape`, on the group or the
  data variable): the transform defines the grid, so the x/y coordinate
  arrays are not read. The CRS comes from `proj:code`, `proj:wkt2` or
  `proj:projjson`, else the CF grid mapping.

- CF: a data variable whose `grid_mapping` variable has `crs_wkt` (or
  `spatial_ref`), and whose first two dims (R order) have regular
  coordinates.

- GDAL engine only, before the CF route: a data variable with a CRS from
  GDAL whose HORIZONTAL_X / HORIZONTAL_Y dimensions (as netCDF declares
  them) have regular coordinates.

### Groups

A nested store (e.g. an EOPF Sentinel-2 product or other GeoZarr) keeps
its arrays in groups below the root. Open one group with `group =`, or
name it in the path: `"https://host/product.zarr/measurements/r10m"` is
read as `dsn = "https://host/product.zarr"`,
`group = "measurements/r10m"`, so the store's consolidated metadata (at
its root) is used. The group's attributes become the Dataset's. List a
store's groups with
[`zaro::zaro_groups()`](https://hypertidy.github.io/zaro/reference/zaro_groups.html).

### Dimension ordering

Arrays are stored in R's column-major (Fortran) order, matching GDAL7's
`read_mdarray()` `$gis$dim` convention. Dimension names follow the same
order. For a NetCDF variable with dimensions (time, lat, lon), the R
array has `dim = c(nlon, nlat, ntime)` and
`dims = c("lon", "lat", "time")`.

## Examples

``` r
if (FALSE) { # \dontrun{
# Open lazily - no data read yet
ds <- open_dataset("sst.mnmean.nc")
ds  # shows variables with [not loaded]

# Selecting is lazy too; collect() reads just the selected values
ds$sst |> sel(time = as.Date("2020-06-15"), lat = c(-60, -30)) |> collect()

# Reductions stream the array chunk by chunk
ds$sst |> sel(lat = c(-60, -30)) |> nd_mean("time", na.rm = TRUE)

# Scope to specific variables (still lazy)
ds <- open_dataset("sst.mnmean.nc", vars = "sst")

# Remote kerchunk-parquet - only sst schema, 12TB never touched
dsn <- 'ZARR:"/vsicurl/https://example.com/store.parq"'
ds <- open_dataset(dsn, vars = "temp")
ds$temp  # a lazy DataArray: nothing read yet

# One group of a nested (sharded GeoZarr) store
eopf <- paste0("https://s3.explorer.eopf.copernicus.eu/esa-zarr-sentinel-explorer-fra/",
               "tests-output/sentinel-2-l2a/",
               "S2C_MSIL2A_20260120T084301_N0511_R064_T36UXA_20260120T122910.zarr")
r10m <- open_dataset(eopf, group = "measurements/reflectance/r10m")
r10m$b04 |> isel(x = 5001:6000, y = 5001:6000) |> collect()
} # }
```
