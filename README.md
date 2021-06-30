# xarray Assets Extension Specification

- **Title:** xarray Assets
- **Identifier:** <https://stac-extensions.github.io/xarray-assets/v1.0.0/schema.json>
- **Field Name Prefix:** xarray
- **Scope:** Asset
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @TomAugspurger

This document explains the Template Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.
This is the place to add a short introduction.

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
