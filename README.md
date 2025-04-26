# xarray Assets Extension Specification

- **Title:** xarray Assets
- **Identifier:** <https://stac-extensions.github.io/xarray-assets/v1.0.0/schema.json>
- **Field Name Prefix:** xarray
- **Scope:** Asset
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Pilot
- **Owner**: @TomAugspurger

This document explains the xarray Assets Extension to the
[SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

This extension helps users open STAC Assets with [xarray](https://xarray.pydata.org/en/stable/). It gives a place
for catalog maintainers to specify various required or recommended options. Without this extension,
users would somehow need to know which options are required in order to load the dataset.
See [Python Example](#python-example) for an example of how consumers of this extension can use it to simplify data loading.

See also [xpystac](https://github.com/stac-utils/xpystac), which enables opening a variety of STAC assets with xarray.

- Examples:
  - [Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Collection example](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Item Properties and Collection Fields

| Field Name               | Type                      | Description                                           |
| ------------------------ | ------------------------- | ----------------------------------------------------- |
| xarray:open_kwargs       | Map<string, Any>          | Keyword arguments to provide to the xarray opener     |
| xarray:storage_options   | Map<string, Any>          | Additional keywords to provide to `fsspec.filesystem` |

### Additional Field Information

#### xarray:open_kwargs

Keyword arguments to provide to the xarray opener, for example  [`xarray.open_zarr`](https://xarray.pydata.org/en/stable/generated/xarray.open_zarr.html).
The opener should be determined by the media type of the asset.

The are in addition to the positional argument, for example the `store`, which is obtained from the Assets `href`. For example, to specify [consolidated metadata](https://zarr.readthedocs.io/en/stable/tutorial.html):

```json
{
  "xarray:open_kwargs": {
    "consolidated": true
  }
}
```

#### xarray:storage_options

[`fsspec.filesystem`](https://filesystem-spec.readthedocs.io/en/latest/api.html#fsspec.filesystem) enables opening a filesystem from URI (e.g. `abfs://path/to/blob`, `https://path/to/file`, `s3://path/to/file`). The various filesystems support and require backend-specific keyword arguments, which can be provided as `**storage_options`.

## Python Example

This example demonstrates how consumers of this extension can use the data to simplify the process of loading
an asset from STAC into an xarray Dataset.

```python
>>> import pystac, planetary_computer, xarray as xr

>>> collection = planetary_computer.sign(
...     pystac.read_file("https://planetarycomputer.microsoft.com/api/stac/v1/collections/terraclimate")
... )
>>> asset = collection.assets["zarr-abfs"]
>>> asset.media_type

>>> ds = xr.open_dataset(
...    asset.href,
...    **asset.extra_fields["xarray:open_kwargs"]
... )
>>> ds
<xarray.Dataset> Size: 2TB
Dimensions:  (time: 768, lat: 4320, lon: 8640, crs: 1)
Coordinates:
  * crs      (crs) int16 2B 3
  * lat      (lat) float64 35kB 89.98 89.94 89.9 89.85 ... -89.9 -89.94 -89.98
  * lon      (lon) float64 69kB -180.0 -179.9 -179.9 ... 179.9 179.9 180.0
  * time     (time) datetime64[ns] 6kB 1958-01-01 1958-02-01 ... 2021-12-01
Data variables: (12/14)
    aet      (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    def      (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    pdsi     (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    pet      (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    ppt      (time, lat, lon) float64 229GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    q        (time, lat, lon) float64 229GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    ...       ...
    swe      (time, lat, lon) float64 229GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    tmax     (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    tmin     (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    vap      (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    vpd      (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
    ws       (time, lat, lon) float32 115GB dask.array<chunksize=(12, 1024, 1024), meta=np.ndarray>
Attributes: (12/52)
    Conventions:                     CF-1.6
    acknowledgment:                  Please cite the references included here...
    cdm_data_type:                   GRID
    contributor_email:               khegewisch@ucmerced.edu
    contributor_name:                Katherine Hegewisch
    contributor_role:                Postdoctoral Fellow
    ...                              ...
    time_coverage_duration:          P1Y
    time_coverage_end:               1958-12-01T00:0
    time_coverage_resolution:        P1M
    time_coverage_start:             1958-01-01T00:0
    title:                           TerraClimate: monthly climate and climat...
    version:                         v1.0
```

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```
