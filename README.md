# 3D City Models Extension Specification

- **Title:** 3D City Models
- **Identifier:** <https://stac-extensions.github.io/3d-city-models/v0.1.0/schema.json>
- **Field Name Prefix:** city3d
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @cityjson

This document explains the 3D City Models Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

This extension provides STAC metadata fields specific to 3D city model datasets, supporting multiple formats including CityJSON, CityGML, and 3D Tiles. It describes encoding format, city object types, levels of detail (LoD), and appearance information.

- Examples:
  - [Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Collection example](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Related Extensions

The 3D City Models Extension is designed to work alongside the following STAC extensions. While this extension does not enforce their use in the schema, they are highly recommended for providing complete metadata:

### Projection Extension

The [Projection Extension](https://github.com/stac-extensions/projection) **SHOULD** be used to specify the coordinate reference system (CRS) of 3D city model datasets.

3D city models commonly use coordinate systems other than WGS84. Use the `proj:code`, `proj:wkt2`, or `proj:projjson` fields to specify the CRS:

```json
{
  "properties": {
    "proj:code": "EPSG:7415"
  }
}
```

### File Extension

The [File Extension](https://github.com/stac-extensions/file) **SHOULD** be used to provide additional file-specific metadata for 3D city model assets:

- `file:checksum` - MD5, SHA-256, or other checksum of the file
- `file:size` - File size in bytes
- `file:values` - Statistics about the file content

### Statistics Extension

The [Statistics Extension](https://github.com/stac-extensions/stats) **MAY** be used in Collections to provide detailed statistical information about the properties of the 3D city model dataset:

```json
{
  "summaries": {
    "file:size": {
      "minimum": 1024,
      "maximum": 1048576,
      "mean": 524288
    }
  }
}
```

### Language Extension

The [Language Extension](https://github.com/stac-extensions/language) **MAY** be used to specify the language of metadata and attribute values in multilingual 3D city model datasets.

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [x] Collections (properties, summaries)
- [x] Item Properties (incl. Summaries in Collections)
- [x] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [ ] Links

| Field Name | Type | Description |
| ---------- | ---- | ----------- |

| city3d:version | string | Specification version (e.g., "2.1" for CityJSON, "3.0" for CityGML). |
| city3d:encoding_version | string | Version of the specific encoding format. |
| city3d:city_objects | integer or [City Objects Statistics](#city-objects-statistics) | Number of city objects in the dataset. |
| city3d:lods | \[number\] | Available Levels of Detail (LoD) in the dataset. |
| city3d:co_types | \[string\] | City object types present in the dataset. |
| city3d:attributes | [[Attribute Definition](#attribute-definition)] | Schema definitions for semantic attributes on city objects. |
| city3d:semantic_surfaces | boolean | Indicates whether the dataset contains semantic surfaces. |
| city3d:textures | boolean | Indicates whether the dataset includes texture information. |
| city3d:materials | boolean | Indicates whether the dataset includes material information. |

## Field Definitions

### city3d:version

The specification version of the 3D city model format. Follows semantic versioning (e.g., `1.0`, `1.1`, `2.0`).

### city3d:encoding_version

Version of the specific encoding format, when the encoding has its own versioning independent of the base specification. For example, FlatCityBuf may have version `0.2.0` regardless of CityJSON version.

### city3d:city_objects

The number of city objects in the dataset.

For **Items**, this is a single integer representing the count.

For **Collections** (in `summaries`), this can be an object with statistical aggregation:

```json
{
  "min": 100,
  "max": 50000,
  "total": 125000
}
```

#### City Objects Statistics

| Field Name | Type    | Description                                      |
| ---------- | ------- | ------------------------------------------------ |
| min        | integer | Minimum number of city objects across all items. |
| max        | integer | Maximum number of city objects across all items. |
| total      | integer | Total number of city objects across all items.   |

### city3d:lods

Available Levels of Detail (LoD) in the dataset. LoD values are numbers to support:

- Integer LoDs: `0`, `1`, `2`, `3`, etc.
- Decimal LoDs (per Biljecki et al. improved specification): `1.2`, `1.5`, `2.3`, etc.

### city3d:co_types

City object types present in the dataset. Types vary by format but common ones include:

**Building types:** `Building`, `BuildingPart`, `BuildingInstallation`, `BuildingStorey`, `BuildingRoom`

**Infrastructure:** `Bridge`, `BridgePart`, `Road`, `Railway`, `Tunnel`, `TunnelPart`

**Environment:** `WaterBody`, `WaterSurface`, `PlantCover`, `SolitaryVegetationObject`, `TINRelief`, `LandUse`

**Other:** `CityFurniture`, `CityObjectGroup`, `GenericCityObject`, `TransportSquare`

**Extension types:** Prefixed with `+` (e.g., `+Noise`, `+Energy`, `+TrafficArea`)

### city3d:attributes

Schema definitions for semantic attributes on city objects. This describes the structure of custom attributes that may be present on city objects beyond the standard properties of the 3D city model format.

#### Attribute Definition

| Field Name  | Type    | Description                                                                           |
| ----------- | ------- | ------------------------------------------------------------------------------------- |
| name        | string  | **REQUIRED**. The name of the attribute.                                              |
| type        | string  | **REQUIRED**. Data type: `String`, `Number`, `Boolean`, `Date`, `Array`, or `Object`. |
| description | string  | A description of what the attribute represents.                                       |
| required    | boolean | Whether this attribute is required for the city object.                               |

### city3d:semantic_surfaces

Boolean flag indicating whether the dataset contains semantic surfaces. Semantic surfaces provide detailed geometry breakdown with specific semantic meaning (e.g., `RoofSurface`, `WallSurface`, `GroundSurface`). This is particularly relevant for formats like CityJSON and CityGML.

### city3d:textures

Boolean flag indicating whether the dataset includes texture information for visual appearance of surfaces.

### city3d:materials

Boolean flag indicating whether the dataset includes material information (e.g., color, shininess, transparency) for surface appearance.

## Relation types

The following types should be used as applicable `rel` types in the [Link Object](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#link-object).

| Type       | Description                                                 |
| ---------- | ----------------------------------------------------------- |
| city-model | This link points to the original 3D city model dataset.     |
| preview    | This link points to a 3D preview or viewer for the dataset. |

## Examples

### Minimal Item Example

```json
{
  "type": "Feature",
  "stac_version": "1.1.0",
  "stac_extensions": [
    "https://stac-extensions.github.io/projection/v2.1.0/schema.json",
    "https://stac-extensions.github.io/3d-city-models/v0.1.0/schema.json"
  ],
  "id": "delft-3d-city-model",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [4.357, 52.011],
        [4.357, 52.012],
        [4.359, 52.012],
        [4.359, 52.011],
        [4.357, 52.011]
      ]
    ]
  },
  "bbox": [4.357, 52.011, 4.359, 52.012],
  "properties": {
    "datetime": "2024-01-01T00:00:00Z",
    "proj:code": "EPSG:7415",
    "city3d:version": "1.1",
    "city3d:city_objects": 15000,
    "city3d:lods": [1.2, 1.3, 2.2],
    "city3d:co_types": ["Building", "Bridge", "Road"],
    "city3d:semantic_surfaces": true,
    "city3d:textures": false,
    "city3d:materials": true
  },
  "links": [
    {
      "rel": "self",
      "href": "https://example.com/collections/delft/items/delft-3d"
    },
    {
      "rel": "parent",
      "href": "https://example.com/collections/delft"
    }
  ],
  "assets": {
    "cityjson": {
      "href": "https://example.com/data/delft.city.json",
      "type": "application/json",
      "roles": ["data"]
    }
  }
}
```

### CityGML Item Example

```json
{
  "type": "Feature",
  "stac_version": "1.1.0",
  "stac_extensions": [
    "https://stac-extensions.github.io/projection/v2.1.0/schema.json",
    "https://stac-extensions.github.io/3d-city-models/v0.1.0/schema.json"
  ],
  "id": "hamburg-citygml-lod2",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [9.93, 53.55],
        [9.93, 53.56],
        [9.95, 53.56],
        [9.95, 53.55],
        [9.93, 53.55]
      ]
    ]
  },
  "bbox": [9.93, 53.55, 9.95, 53.56],
  "properties": {
    "datetime": "2024-01-01T00:00:00Z",
    "proj:code": "EPSG:25832",
    "city3d:version": "2.0",
    "city3d:city_objects": 42800,
    "city3d:lods": [0, 1, 2],
    "city3d:co_types": ["Building", "BuildingPart", "Road", "WaterBody"],
    "city3d:semantic_surfaces": true,
    "city3d:textures": true,
    "city3d:materials": true
  },
  "assets": {
    "data": {
      "href": "https://example.com/data/hamburg_lod2.gml",
      "type": "application/gml+xml",
      "roles": ["data"]
    }
  }
}
```

### Collection Example

```json
{
  "type": "Collection",
  "stac_version": "1.1.0",
  "stac_extensions": [
    "https://stac-extensions.github.io/projection/v2.1.0/schema.json",
    "https://stac-extensions.github.io/3d-city-models/v0.1.0/schema.json"
  ],
  "id": "netherlands-3d-cities",
  "title": "Netherlands 3D City Models",
  "description": "Collection of 3D city models for major Dutch cities.",
  "license": "CC-BY-4.0",
  "extent": {
    "spatial": {
      "bbox": [[3.2, 50.7, 3.3, 50.8]]
    },
    "temporal": {
      "interval": [["2024-01-01T00:00:00Z", "2024-12-31T23:59:59Z"]]
    }
  },
  "links": [
    {
      "rel": "self",
      "href": "https://example.com/collections/netherlands-3d-cities"
    }
  ],
  "summaries": {
    "city3d:version": ["1.1", "2.0"],
    "city3d:city_objects": {
      "min": 1000,
      "max": 50000,
      "total": 125000
    },
    "city3d:lods": [0, 1, 1.2, 2, 2.2],
    "city3d:co_types": ["Building", "Bridge", "Road", "Tunnel"],
    "city3d:semantic_surfaces": [true],
    "city3d:textures": [true, false],
    "city3d:materials": [true],
    "proj:code": ["EPSG:7415", "EPSG:28992", "EPSG:25832"]
  }
}
```

## Contributing

All contributions are subject to the [STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md). For contributions, please follow the [STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md). Instructions for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on your command line run:

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
