# Mappings

Purpose: map concepts & attributes of the specifications to Transmodel.

Each sub-directory contains a `README.md` with a detailed concept-by-concept mapping table from the source standard to the Transmodel reference model.

| Standard | Spec file | Mapping status |
|----------|-----------|----------------|
| [BoB booking](bob/README.md) | `wiki/specifications/BoB booking.yaml` (v2.0.1) | Complete |
| [FerryGateway](ferrygateway/README.md) | `wiki/specifications/ferrygateway.xsd` (v1.3.1) | Complete |
| [OMSA](omsa/README.md) | `wiki/specifications/OMSA.yaml` | Complete |
| [OSDM](osdm/README.md) | `wiki/specifications/OSDM 3.7.1.yaml` | Complete |
| [TOMP-API](tomp-api/README.md) | `wiki/specifications/TOMP-API 2.0.0.yaml` | Complete |
| InterMOD | (no spec file available) | Pending — awaiting spec |

## Match quality legend

All mapping tables use the following quality indicators in the **Notes** column:

| Value | Meaning |
|-------|---------|
| **exact** | One-to-one semantic correspondence |
| **partial** | Overlapping scope; some attributes or nuances differ |
| **none** | No Transmodel equivalent; concept is standard-specific |
