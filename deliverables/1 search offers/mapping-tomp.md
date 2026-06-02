# Mapping: Search Offers → TOMP-API 2.0.0

Maps each EUDIT **Search Offers** concept to the corresponding concept/property in **TOMP-API 2.0.0**.

- **Endpoint**: `POST /processes/search-offers/execution`
- **EUDIT concept / Property** — as defined in `search-offers.yaml`
- **TOMP concept** — the matching schema object in TOMP-API
- **TOMP property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

---

## OfferRequest

> Root request body for the offer search.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| tripPatterns | TripPattern | 1..* | `searchOfferRequest` | `tripPattern` / `travelSpecification` | TOMP accepts either a fixed `tripPattern` (timetabled journeys) or an open-ended `travelSpecification` (O&D + time window). Multiple EUDIT `TripPattern`s have no direct equivalent; TOMP supports one `tripPattern` or one `travelSpecification` per request. |
| travellers | TravellingEntity | 1..* | `searchOfferRequest` | `travellers[]` | Traveller sub-types map differently; see individual sections below. `Animal`, `PassengerVehicle`, `VehicleRack`, `Luggage` have no equivalent in TOMP offer search. |
| filter | SearchOfferFilter | 0..1 | `searchOfferRequest` | `travellerRequirements` (per traveller) | TOMP applies mode/class/operator preferences per traveller, not as a global filter. `mediaTypes` and `requestedSections` have no equivalent. |
| policy | SearchOfferPolicy | 0..1 | — | — | No equivalent. TOMP offer search is synchronous with no pagination or currency control. |

---

## SearchOfferPolicy

> Pagination and currency preferences. **No equivalent in TOMP-API.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| numberOfResultsBefore | integer | 0..1 | — | — | Not supported. TOMP returns results without pagination control. |
| numberOfResultsAfter | integer | 0..1 | — | — | Not supported. |
| currency | string | 0..1 | — | — | Not supported at request level; currency is part of operator configuration. |

---

## SearchOfferFilter

> Constraints on which offers to return. Only partially covered in TOMP.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| modes | string | 0..* | `travellerRequirements` | `mode`, `subMode` | Applied per traveller in TOMP, not as a global filter. |
| classOfUse | string | 0..* | `travellerRequirements` | `class` | Maps to TOMP `class` (e.g. `FIRST`, `SECOND`). Per traveller. |
| mediaTypes | string | 0..* | — | — | No equivalent. TOMP does not filter by fulfilment media type at offer-search stage. |
| requestedSections | RequestedSections | 0..* | — | — | No equivalent. TOMP does not support section-level offer targeting. |

---

## RequestedSections

> Targets offer search to a subset of legs. **No equivalent in TOMP-API.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| startLegId | string | 0..1 | — | — | No equivalent. |
| endLegId | string | 0..1 | — | — | No equivalent. |
| startPlace | string | 0..1 | — | — | No equivalent. |
| endPlace | string | 0..1 | — | — | No equivalent. |
| tripReference | string | 0..1 | — | — | No equivalent. |

---

## TravellingEntity

> Base class; concrete sub-types mapped below.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | `traveller` | `id` | Used as correlation identifier. |
| entityType | string | 1..1 | — | — | Discriminator only; no TOMP equivalent. `Animal`, `PassengerVehicle`, `Luggage` sub-types have no TOMP equivalent in offer search. |
| entitlementRights | EntitlementRight | 0..* | `card` / `entitlement` | (nested in traveller) | See **EntitlementRight** section. |

---

## Traveller

> Human traveller.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | `traveller` | `id` | |
| age | integer | 0..1 | `traveller` | `age` | Direct mapping. |
| assistant | boolean | 0..1 | — | — | No equivalent. TOMP PRM needs expressed via `travellerRequirements.prmNeeds[]`. |
| dateOfBirth | date | 0..1 | — | — | No equivalent in TOMP traveller object. Age-band eligibility via `userProfile.minimumAge` / `maximumAge`. |
| externalReference | string | 0..1 | `traveller` | `customerReference` | |
| personalNeeds | PersonalNeed | 0..* | `travellerRequirements` | `prmNeeds[]` | TOMP uses operator-defined PRM need codes. |
| qualifyingCharacteristics | TravellerQualifyingCharacteristics | 0..1 | `userProfile` | (see section) | |

---

## TravellerQualifyingCharacteristics

> Demographic eligibility data.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| fullName | string | 0..1 | `traveller` | `name` | TOMP has a top-level `name` field on traveller. |
| nationality | string | 0..1 | — | — | No equivalent in TOMP. |
| residency | string | 0..1 | — | — | No equivalent in TOMP. |
| dateOfBirth | date | 0..1 | — | — | No equivalent in TOMP. |
| licenseTypes | LicenseType | 0..* | — | — | Driver licence held by traveller. TOMP exposes licence types via `GET /collections/license-types/items` but does not accept them in offer-search input. |

---

## PersonalNeed

> Accessibility or personal need.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | `travellerRequirements` | `prmNeeds[]` | TOMP uses operator-defined PRM need codes (e.g. `WHEELCHAIR`, `VISUAL_IMPAIRMENT`). |

---

## LicenseType

> Driving licence held by a traveller. **No equivalent in TOMP offer-search input.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | — | — | TOMP exposes licence categories via a separate collection endpoint; not an input to offer search. |

---

## Animal

> Animal travelling alongside its owner. **No equivalent in TOMP offer-search input.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | TOMP does not model pet transport in the offer-search request. |
| type | string | 1..1 | — | — | No equivalent. |
| assistant | boolean | 0..1 | `travellerRequirements` | `prmNeeds[]` | Assistance animal status may be expressed as a PRM need on the accompanying traveller. |

---

## PassengerVehicle

> Traveller-owned vehicle to be transported. **No equivalent in TOMP offer-search input.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | TOMP does not support vehicle transport in offer search. |
| type | string | 1..1 | — | — | No equivalent. |
| height / width / length / weight | integer | 0..1 | — | — | No equivalent. |
| trailer | PassengerVehicle | 0..1 | — | — | No equivalent. |
| vehicleRacks | VehicleRack | 0..* | — | — | No equivalent. |

---

## VehicleRack

> Rack mounted on a passenger vehicle. **No equivalent in TOMP-API.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| type | string | 0..1 | — | — | No equivalent. |
| mounting | string | 0..1 | — | — | No equivalent. |
| height / width / length / weight | integer | 0..1 | — | — | No equivalent. |

---

## Luggage

> Bulky item a traveller brings. **No equivalent in TOMP offer-search input.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | No equivalent. TOMP shared-mobility asset catalogue is separate from offer search. |
| type | string | 1..1 | — | — | No equivalent. |
| length / width / height / weight | integer | 0..1 | — | — | No equivalent. |

---

## EntitlementRight

> Credential qualifying for reduced fares.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| issuer | string | 0..1 | `card` | — / `entitlement` | No explicit issuer field in TOMP; issuer may be embedded in the `type` code or `id`. |
| entitlementType | string | 1..1 | `card` | `type` / `entitlement` `type` | Maps to `card.type` (e.g. `DISCOUNT_CARD`) or `entitlement.type`. |
| code | string | 0..1 | `card` | `id` / `entitlement` `id` | The instance identifier (card number, voucher code). |

---

## TripPattern

> Proposed sequence of legs.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| tripPatternId | string | 0..1 | — | — | No equivalent; TOMP identifies the trip by the journey reference set, not a caller ID. |
| legs | Leg | 1..* | `tripPattern` | `serviceJourneys[]` (timed) OR `travelSpecification` (open O&D) | A `TripPattern` containing `TimedLeg`s maps to `tripPattern.serviceJourneys[]`. An open-ended trip maps to `travelSpecification`. `ContinuousLeg` and `TransferLeg` have no TOMP equivalent. |

---

## TimedLeg

> Leg on a fixed timetabled service.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | Discriminator only. |
| legId | string | 0..1 | — | — | No equivalent. |
| sequenceNumber | integer | 0..1 | — | — | Implied by order in `serviceJourneys[]`. |
| mode | string | 0..1 | `travellerRequirements` | `mode` | TOMP applies mode at traveller-requirements level, not per leg. |
| serviceJourneyRef | string | 0..1 | `ServiceJourneyReference` | `id` | Direct mapping. |
| operatingDate | date | 0..1 | `tripPattern` | `travelDate` | TOMP has a single `travelDate` for the whole trip pattern. |
| lineNumber | string | 0..1 | `ServiceJourneyReference` | `lineId` | |
| brand | string | 0..1 | — | — | No equivalent. |
| start | LegBoard | 0..1 | — | — | TOMP `tripPattern` does not include stop-level boarding data; only `serviceJourneys[]` and `travelDate`. |
| end | LegAlight | 0..1 | — | — | No equivalent. |
| intermediateStops | StopPlace | 0..* | — | — | No equivalent. |

---

## ContinuousLeg

> Leg without fixed timetabled stops (walk, taxi, etc.). **Partially covered by `travelSpecification`.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | Discriminator only. |
| startTime | dateTime | 0..1 | `travelSpecification` | `startTime` | |
| endTime | dateTime | 0..1 | `travelSpecification` | `endTime` | |
| startLocation | Place | 0..1 | `travelSpecification` | `from` (placeReference) | |
| endLocation | Place | 0..1 | `travelSpecification` | `to` (placeReference) | |

---

## TransferLeg

> Transfer between services. **No equivalent in TOMP-API.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | No equivalent. TOMP does not model interchange legs in offer-search input. |

---

## LegBoard

> Boarding event at the start of a timed leg.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `placeReference` | `id` | TOMP references places by ID or coordinates. Stop references in a timed-leg context are not part of `tripPattern.serviceJourneys[]`. |
| plannedDepartureTime | dateTime | 1..1 | `tripPattern` | `travelDate` | TOMP has date only, not full departure time per leg. |
| pickupLocation | string | 0..1 | — | — | No equivalent. |
| trainNumbers | TrainNumber | 0..* | — | — | No equivalent; train number not in TOMP offer search. |

---

## LegAlight

> Alighting event at the end of a timed leg.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `placeReference` | `id` | See LegBoard note above. |
| plannedArrivalTime | dateTime | 1..1 | — | — | No equivalent per-leg arrival time in TOMP. |
| setDownLocation | string | 0..1 | — | — | No equivalent. |

---

## TrainNumber

> Commercial train number. **No equivalent in TOMP offer-search input.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| trainNumberId | string | 1..1 | — | — | No equivalent. |

---

## StopPlace

> Reference to an intermediate stop along a timed leg.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `placeReference` | `id` | TOMP `placeReference` accepts a stop ID or coordinates. |

---

## Place

> Base place type.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| placeId | string | 0..1 | `placeReference` | — | Caller-assigned ID; no direct field in TOMP `placeReference`. |
| placeType | string | 1..1 | — | — | Discriminator only; TOMP does not distinguish place sub-types explicitly. |
| location | GeoPosition | 0..1 | `placeReference` | `coordinates` (GeoCoordinate) | See **GeoPosition** section. |

---

## StopPlaceInput

> Stop place identified by reference.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| placeRef | string | 0..1 | `placeReference` | `id` | |

---

## AddressInput

> Postal address as a place.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| addressLine1 | string | 0..1 | `placeReference` | `name` | TOMP `placeReference` has a single `name` field; no structured address. |
| addressLine2 | string | 0..1 | — | — | No equivalent. |

---

## PointOfInterest

> Named point of interest.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| name | string | 0..1 | `placeReference` | `name` | |

---

## TopologicalPlace

> Geographic area or zone.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| area | string | 0..1 | `travellerRequirements` | `zones[]` | TOMP supports zone-based filtering via `zones[]` (zone IDs), not GeoJSON polygons. |
| range | integer | 0..1 | — | — | No equivalent; TOMP does not support radius-based place references. |

---

## GeoPosition

> WGS-84 coordinate pair.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| longitude | number | 1..1 | `GeoCoordinate` | `lon` | |
| latitude | number | 1..1 | `GeoCoordinate` | `lat` | |

---

## Offer

> Purchasable combination of offer elements returned by the server.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| offerId | string | 1..1 | `leg` | `id` (leg-level) | TOMP does not return a discrete "offer" object; pricing is returned per leg via `leg.pricing[]`. No offer-level ID. |
| elements | OfferElement | 1..* | `leg.pricing` | (array) | See **OfferElement** section. |
| farePrice | FarePrice | 0..1 | `leg.pricing` | `estimated`, `parts[]` | Aggregated across legs by the client. |
| travelDocument | TravelDocument | 0..1 | — | — | Not returned in offer-search response; issued at booking stage. |

---

## OfferElement

> Base class for purchasable elements within an offer.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| offerElementId | string | 1..1 | — | — | No equivalent per-element ID in TOMP pricing. |
| offerElementType | string | 1..1 | — | — | Discriminator only. |
| travellingEntities | string | 0..* | — | — | TOMP pricing is leg-level, not traveller-level. |
| farePrice | FarePrice | 0..1 | `leg.pricing` | `parts[{ amount, currencyCode }]` | |
| travelDocument | TravelDocument | 0..1 | — | — | Not in offer-search response. |
| guarantees | Guarantee | 0..* | — | — | No equivalent. |

---

## TravelRight

> Right to travel on a specific leg or zone.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| legRef | string | 0..1 | `leg` | `id` | TOMP prices are attached to `leg` objects; the leg ID is the implicit travel-right scope. |
| zoneRef | string | 0..1 | `travellerRequirements` | `zones[]` | Zone scope expressed as zone filter, not a response field. |

---

## Ancillary

> Optional ancillary service. **No direct equivalent in TOMP offer-search response.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| ancillaryId | string | 0..1 | — | — | No equivalent. |
| type | string | 0..1 | — | — | No equivalent. |

---

## Reservation

> Seat or space reservation. **No equivalent in TOMP offer-search response.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| offerElementType | string | 1..1 | — | — | No equivalent at offer-search stage in TOMP. |

---

## CompositeOfferElement

> Grouped offer elements sold as a unit. **No equivalent in TOMP.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| elements | OfferElement | 2..* | — | — | No equivalent. |

---

## FarePrice

> Price of an offer or offer element. (Stub — detail TBD.)

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| (TBD) | — | — | `leg.pricing` | `estimated` (bool), `parts[{ amount, currencyCode }]` | TOMP pricing is per leg; totals must be aggregated. |

---

## TravelDocument

> Proof of entitlement issued to the traveller. (Stub — detail TBD.)

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| (TBD) | — | — | — | — | Not part of TOMP offer-search response; issued at booking/confirmation stage. |

---

## Guarantee

> Guarantee associated with an offer element. (Stub — detail TBD.) **No equivalent in TOMP.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| (TBD) | — | — | — | — | No equivalent. |
