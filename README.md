# SpatioTemporal Asset Catalog (STAC) Validator

[//]: # "Badges"

<p align="center">
  <a href="https://github.com/sparkgeo/stac-validator/actions/workflows/test-runner.yml" target="_blank">
      <img src="https://github.com/sparkgeo/stac-validator/actions/workflows/test-runner.yml/badge.svg" alt="Package version">
  </a>
  <a href="https://pypi.org/project/stac-validator" target="_blank">
      <img src="https://img.shields.io/pypi/v/stac-validator?color=%2334D058&label=pypi" alt="Package version">
  </a>
  <a href="https://github.com/sparkgeo/stac-validator/blob/master/LICENSE" target="_blank">
      <img src="https://img.shields.io/github/license/sparkgeo/stac-validator.svg" alt="License">
  </a>
</p>

Validate STAC json files against the [STAC spec](https://github.com/radiantearth/stac-spec).

```bash
stac_validator https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json
[
    {
        "version": "1.0.0",
        "path": "https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json",
        "schema": [
            "https://stac-extensions.github.io/eo/v1.0.0/schema.json",
            "https://stac-extensions.github.io/projection/v1.0.0/schema.json",
            "https://stac-extensions.github.io/scientific/v1.0.0/schema.json",
            "https://stac-extensions.github.io/view/v1.0.0/schema.json",
            "https://stac-extensions.github.io/remote-data/v1.0.0/schema.json",
            "https://schemas.stacspec.org/v1.0.0/item-spec/json-schema/item.json"
        ],
        "valid_stac": true,
        "asset_type": "ITEM",
        "validation_method": "default"
    }
]
```

## Requirements

- Python 3.6+
  - Requests
  - Click
  - Pytest
  - Pystac
  - Jsonschema

## Install

Installation from PyPi

```bash
pip install stac-validator
```

Installation from Repo

```bash
pip install .
```

or (for development)

```bash
pip install --editable .["test"]
```

The [Makefile](./Makefile) has convenience commands if Make is installed.

```bash
make help
```

## Versions supported

| STAC         |
| ------------ |
| 0.8.0        |
| 0.8.1        |
| 0.9.0        |
| 1.0.0-beta.1 |
| 1.0.0-beta.2 |
| 1.0.0-rc.1   |
| 1.0.0-rc.2   |
| 1.0.0-rc.3   |
| 1.0.0-rc.4   |
| 1.0.0        |


---

# CLI

**Basic Usage**

```bash
stac-validator --help
Usage: stac-validator [OPTIONS] STAC_FILE

Options:
  --lint                   Use stac-check to lint stac object instead of
                           validating it.
  --core                   Validate core stac object only without extensions.
  --extensions             Validate extensions only.
  --links                  Additionally validate links. Only works with
                           default mode.
  --assets                 Additionally validate assets. Only works with
                           default mode.
  -c, --custom TEXT        Validate against a custom schema (local filepath or
                           remote schema).
  -r, --recursive          Recursively validate all related stac objects.
  -m, --max-depth INTEGER  Maximum depth to traverse when recursing. Ignored
                           if `recursive == False`.
  -v, --verbose            Enables verbose output for recursive mode.
  --no_output              Do not print output to console.
  --log_file TEXT          Save full recursive output to log file (local
                           filepath).
  --version                Show the version and exit.
  --help                   Show this message and exit.
```

---

# Deployment

## Docker

The validator can run using docker containers.

```bash
docker build -t stac_validator .
docker run stac_validator https://raw.githubusercontent.com/stac-extensions/projection/main/examples/item.json
[
    {
        "version": "1.0.0",
        "path": "https://raw.githubusercontent.com/stac-extensions/projection/main/examples/item.json",
        "schema": [
            "https://stac-extensions.github.io/projection/v1.0.0/schema.json",
            "https://schemas.stacspec.org/v1.0.0/item-spec/json-schema/item.json"
        ],
        "valid_stac": true,
        "asset_type": "ITEM",
        "validation_method": "default"
    }
]
```

## AWS (CDK)
An example [AWS CDK](https://aws.amazon.com/cdk/) deployment is available in [cdk-deployment](./cdk-deployment/README.md)
```bash
cd cdk-deployment
cdk diff
```

---

# Python

**Remote source**

```python
from stac_validator import stac_validator

stac = stac_validator.StacValidate("https://raw.githubusercontent.com/stac-utils/pystac/main/tests/data-files/examples/0.9.0/collection-spec/examples/landsat-collection.json")
stac.run()
print(stac.message)
[
    {
        "version": "0.9.0",
        "path": "https://raw.githubusercontent.com/stac-utils/pystac/main/tests/data-files/examples/0.9.0/collection-spec/examples/landsat-collection.json",
        "schema": [
            "https://cdn.staclint.com/v0.9.0/collection.json"
        ],
        "valid_stac": true,
        "asset_type": "COLLECTION",
        "validation_method": "default"
    }
]
```

**Local file**

```python
from stac_validator import stac_validator

stac = stac_validator.StacValidate("tests/test_data/1beta1/sentinel2.json", extensions=True)
stac.run()
print(stac.message)
[
    {
        "version": "1.0.0-beta.1",
        "path": "tests/test_data/1beta1/sentinel2.json",
        "schema": [
            "https://cdn.staclint.com/v1.0.0-beta.1/collection.json"
        ],
        "valid_stac": true,
        "asset_type": "COLLECTION",
        "validation_method": "extensions"
    }
]
```

---

# Testing


```bash
make test
# or
pytest -v
```

See the [tests](./tests/test_stac_validator.py) files for examples on different usages.

---
# Additional Examples

**--core**

```bash
stac_validator https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json --core
[
    {
        "version": "1.0.0",
        "path": "https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json",
        "schema": [
            "https://schemas.stacspec.org/v1.0.0/item-spec/json-schema/item.json"
        ],
        "valid_stac": true,
        "asset_type": "ITEM",
        "validation_method": "core"
    }
]
```

**--custom**

```bash
stac_validator https://radarstac.s3.amazonaws.com/stac/catalog.json --custom https://cdn.staclint.com/v0.7.0/catalog.json
[
    {
        "version": "0.7.0",
        "path": "https://radarstac.s3.amazonaws.com/stac/catalog.json",
        "schema": [
            "https://cdn.staclint.com/v0.7.0/catalog.json"
        ],
        "asset_type": "CATALOG",
        "validation_method": "custom",
        "valid_stac": true
    }
]
```

**--extensions**

```bash
stac_validator https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json --extensions
[
    {
        "version": "1.0.0",
        "path": "https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json",
        "schema": [
            "https://stac-extensions.github.io/eo/v1.0.0/schema.json",
            "https://stac-extensions.github.io/projection/v1.0.0/schema.json",
            "https://stac-extensions.github.io/scientific/v1.0.0/schema.json",
            "https://stac-extensions.github.io/view/v1.0.0/schema.json",
            "https://stac-extensions.github.io/remote-data/v1.0.0/schema.json"
        ],
        "valid_stac": true,
        "asset_type": "ITEM",
        "validation_method": "extensions"
    }
]
```

**--lint**

```bash
stac_validator https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json --lint
[
    {
        "version": "1.0.0",
        "path": "https://raw.githubusercontent.com/radiantearth/stac-spec/master/examples/extended-item.json",
        "schema": [
            "https://stac-extensions.github.io/eo/v1.0.0/schema.json",
            "https://stac-extensions.github.io/projection/v1.0.0/schema.json",
            "https://stac-extensions.github.io/scientific/v1.0.0/schema.json",
            "https://stac-extensions.github.io/view/v1.0.0/schema.json",
            "https://stac-extensions.github.io/remote-data/v1.0.0/schema.json",
            "https://schemas.stacspec.org/v1.0.0/item-spec/json-schema/item.json"
        ],
        "valid_stac": true,
        "asset_type": "ITEM",
        "validation_method": "default",
        "linting": {
            "searchable_identifiers": [
                "Item name '20201211_223832_CS2' should only contain Searchable identifiers",
                "Identifiers should consist of only lowercase characters, numbers, '_', and '-'"
            ],
            "check_item_id": [
                "Item file names should match their ids: 'extended-item' not equal to '20201211_223832_CS2"
            ],
            "bloated_metadata": [
                "You have 21 properties. Please consider using links to avoid bloated metadata"
            ]
        }
    }
]
```

**--recursive**

```bash
stac_validator https://spot-canada-ortho.s3.amazonaws.com/catalog.json --recursive --max-depth 1 --verbose
[
    {
        "version": "0.8.1",
        "path": "https://canada-spot-ortho.s3.amazonaws.com/canada_spot_orthoimages/canada_spot4_orthoimages/collection.json",
        "schema": "https://cdn.staclint.com/v0.8.1/collection.json",
        "asset_type": "COLLECTION",
        "validation_method": "recursive",
        "valid_stac": true
    },
    {
        "version": "0.8.1",
        "path": "https://canada-spot-ortho.s3.amazonaws.com/canada_spot_orthoimages/canada_spot5_orthoimages/collection.json",
        "schema": "https://cdn.staclint.com/v0.8.1/collection.json",
        "asset_type": "COLLECTION",
        "validation_method": "recursive",
        "valid_stac": true
    },
    {
        "version": "0.8.1",
        "path": "https://spot-canada-ortho.s3.amazonaws.com/catalog.json",
        "schema": "https://cdn.staclint.com/v0.8.1/catalog.json",
        "asset_type": "CATALOG",
        "validation_method": "recursive",
        "valid_stac": true
    }
]
```
