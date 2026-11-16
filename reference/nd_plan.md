# What reading lazy data will cost

Works out what a read of lazy data (from
[`open_dataset()`](https://hypertidy.github.io/ndr/reference/open_dataset.md),
after any
[`sel()`](https://hypertidy.github.io/ndr/reference/indexing.md) or
[`isel()`](https://hypertidy.github.io/ndr/reference/indexing.md)) would
touch, without reading it: the selection's size, the source chunks it
needs, and with the zaro engine the requests, bytes, hosts and
transports, from
[`zaro::zaro_plan()`](https://hypertidy.github.io/zaro/reference/zaro_plan.html).
Call it before
[`collect()`](https://hypertidy.github.io/ndr/reference/collect.md) on
something large or remote.

## Usage

``` r
nd_plan(x, ..., max_chunks = getOption("ndr.plan_max_chunks", 1e+05))
```

## Arguments

- x:

  a Variable, DataArray or Dataset.

- ...:

  unused.

- max_chunks:

  the most chunks to plan byte references for: above this, only the
  counts are given (a whole large array can have millions of chunks).
  Default `getOption("ndr.plan_max_chunks", 1e5)`.

## Value

An `nd_plan` (printed as a summary). For a Variable or DataArray, a list
with `var`, `engine`, `lazy`, `selection` (the selected sizes of the
source dims), `values`, `chunks` (touched), `chunks_total`,
`chunk_shape`, `chunk_values` (values in the touched chunks), and with
the zaro engine `requests`, `bytes` (known), `unknown_size`, `hosts`,
`transport` and `refs` (the
[`zaro::zaro_plan()`](https://hypertidy.github.io/zaro/reference/zaro_plan.html)
table). For a Dataset, a data frame with one row per data variable.

## Details

With the GDAL engine the chunks are GDAL's block size and the requests
are not estimated (they depend on the driver). In-memory data has
nothing to read.

## Examples

``` r
if (FALSE) { # \dontrun{
ds <- open_dataset("https://host/store.zarr", engine = "zaro")
ds$temp |> sel(time = "2023-01-01") |> nd_plan()
nd_plan(ds)
} # }
```
