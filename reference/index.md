# Package index

## Data structures

- [`Variable()`](https://hypertidy.github.io/ndr/reference/Variable.md)
  : Variable: a named-dimension array
- [`DataArray()`](https://hypertidy.github.io/ndr/reference/DataArray.md)
  : DataArray: a Variable with coordinates
- [`Dataset()`](https://hypertidy.github.io/ndr/reference/Dataset.md) :
  Dataset: a collection of aligned Variables on shared dimensions
- [`as_variable()`](https://hypertidy.github.io/ndr/reference/as_variable.md)
  : Quick Variable constructor from a named-dim array

## Coordinates and indexes

- [`Index()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  [`index_dims()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  [`index_sizes()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  [`index_coords()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  [`index_sel()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  [`index_isel()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  [`index_drop()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  [`index_equals()`](https://hypertidy.github.io/ndr/reference/indexes.md)
  : Indexes: the contract between labels and positions
- [`coordinates`](https://hypertidy.github.io/ndr/reference/coordinates.md)
  : Coordinate classes
- [`ImplicitCoord()`](https://hypertidy.github.io/ndr/reference/ImplicitCoord.md)
  : Create an implicit (regular-grid) coordinate
- [`ExplicitCoord()`](https://hypertidy.github.io/ndr/reference/ExplicitCoord.md)
  : Create an explicit (irregular) coordinate
- [`coord_dim()`](https://hypertidy.github.io/ndr/reference/coord_dim.md)
  : Get the dimension name of a coordinate
- [`coord_length()`](https://hypertidy.github.io/ndr/reference/coord_length.md)
  : Get the length of a coordinate
- [`coord_lookup()`](https://hypertidy.github.io/ndr/reference/coord_lookup.md)
  : Look up integer index for a coordinate value
- [`coord_slice()`](https://hypertidy.github.io/ndr/reference/coord_slice.md)
  : Slice a coordinate by integer indices
- [`coord_values()`](https://hypertidy.github.io/ndr/reference/coord_values.md)
  : Get the values of a coordinate
- [`AffineIndex()`](https://hypertidy.github.io/ndr/reference/AffineIndex.md)
  : Affine (geotransform) index
- [`as_geotransform()`](https://hypertidy.github.io/ndr/reference/as_geotransform.md)
  : Get the GDAL geotransform of an AffineIndex
- [`set_affine_index()`](https://hypertidy.github.io/ndr/reference/set_affine_index.md)
  : Combine two regular coordinates into an AffineIndex

## Selection

- [`isel()`](https://hypertidy.github.io/ndr/reference/indexing.md)
  [`sel()`](https://hypertidy.github.io/ndr/reference/indexing.md) :
  Coordinate-based indexing
- [`ds_dims()`](https://hypertidy.github.io/ndr/reference/ds_dims.md) :
  List dimension names and sizes across a Dataset

## Arithmetic and broadcasting

- [`ops`](https://hypertidy.github.io/ndr/reference/ops.md) : Arithmetic
  operations on Variables and DataArrays
- [`broadcasting`](https://hypertidy.github.io/ndr/reference/broadcasting.md)
  : Dimension-aware broadcasting

## Reductions

- [`nd_mean()`](https://hypertidy.github.io/ndr/reference/reductions.md)
  [`nd_sum()`](https://hypertidy.github.io/ndr/reference/reductions.md)
  [`nd_min()`](https://hypertidy.github.io/ndr/reference/reductions.md)
  [`nd_max()`](https://hypertidy.github.io/ndr/reference/reductions.md)
  : Reductions along named dimensions

## Reading data

- [`open_dataset()`](https://hypertidy.github.io/ndr/reference/open_dataset.md)
  : Open a dataset from a file or URL
- [`open_raster()`](https://hypertidy.github.io/ndr/reference/open_raster.md)
  : Open a raster band as a lazy DataArray
- [`cf_decode_time()`](https://hypertidy.github.io/ndr/reference/cf_decode_time.md)
  : Decode CF time values to R Date or POSIXct
- [`lazy-data`](https://hypertidy.github.io/ndr/reference/lazy-data.md)
  : Lazy data in a Variable
- [`collect()`](https://hypertidy.github.io/ndr/reference/collect.md) :
  Read lazy data into memory
- [`nd_plan()`](https://hypertidy.github.io/ndr/reference/nd_plan.md) :
  What reading lazy data will cost

## Methods

- [`ndim()`](https://hypertidy.github.io/ndr/reference/ndim.md)
  [`shape()`](https://hypertidy.github.io/ndr/reference/ndim.md) :
  Number of dimensions and shape of a Variable
- [`print-methods`](https://hypertidy.github.io/ndr/reference/print-methods.md)
  : Print methods for ndr objects
- [`as.data.frame(`*`<ndr::DataArray>`*`)`](https://hypertidy.github.io/ndr/reference/as.data.frame.ndr-DataArray.md)
  : Coerce to data frame (long format)
- [`as.list(`*`<ndr::Dataset>`*`)`](https://hypertidy.github.io/ndr/reference/as.list.ndr-Dataset.md)
  : Convert a Dataset to a list of data frames
- [`` `$`( ``*`<ndr::Dataset>`*`)`](https://hypertidy.github.io/ndr/reference/cash-.ndr-Dataset.md)
  : Extract a DataArray from a Dataset
- [`names(`*`<ndr::Dataset>`*`)`](https://hypertidy.github.io/ndr/reference/names.ndr-Dataset.md)
  : Variable names in a Dataset
