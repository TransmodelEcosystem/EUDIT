# Mapping: Search Offers → OMSA 0.1.0

Maps each EUDIT **Search Offers** concept to the corresponding concept/property in **OMSA 0.1.0**.

- **Endpoint**: `POST /processes/search-offers/execute`
- **EUDIT concept / Property** — as defined in `search-offers.yaml`
- **OMSA concept** — the matching schema object in OMSA 0.1.0
- **OMSA property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

OMSA follows the OGC API Processes pattern and is conceptually very close to TOMP-API 2.0.0
(uses `/execute` instead of TOMP's `/execution`). Key differences are noted below.

---

## OfferRequest

> Root request body for the offer search.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| tripPatterns | TripPattern | 1..* | `searchOfferInput` | `specification` (tripPattern or travelSpecification) | OMSA accepts either a structured `tripPattern` or an open `travelSpecification`. Multiple EUDIT `TripPattern`s have no direct equivalent. |
| travellers | TravellingEntity | 1..* | `searchOfferInput` | `travellers[]` | `Animal`, `VehicleRack` sub-types have no equivalent. `PassengerVehicle` and `Luggage` map to `assets[]` (see below). |
| filter | SearchOfferFilter | 0..1 | `searchOfferInput` | `travellerRequirements` (per traveller) | Applied per traveller, not as a global filter. `mediaTypes` and `requestedSections` have no equivalent. |
| policy | SearchOfferPolicy | 0..1 | — | — | No equivalent. OMSA is synchronous with no pagination or currency control. |

---

## SearchOfferPolicy

> Pagination and currency preferences. **No equivalent in OMSA.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| numberOfResultsBefore | integer | 0..1 | — | — | Not supported. |
| numberOfResultsAfter | integer | 0..1 | — | — | Not supported. |
| currency | string | 0..1 | — | — | Not supported at request level. |

---

## SearchOfferFilter

> Constraints on which offers to return. Only partially covered in OMSA.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| modes | string | 0..* | `travellerRequirements` | `mode`, `subMode` | Per traveller in OMSA. |
| classOfUse | string | 0..* | `travellerRequirements` | `class` | Per traveller. |
| mediaTypes | string | 0..* | — | — | No equivalent. |
| requestedSections | RequestedSections | 0..* | — | — | No equivalent. |

---

## RequestedSections

> Targets offer search to a subset of legs. **No equivalent in OMSA.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| startLegId / endLegId / startPlace / endPlace / tripReference | various | 0..1 | — | — | No equivalent. |

---

## TravellingEntity

> Base class.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | `traveller` | `id` | |
| entityType | string | 1..1 | — | — | Discriminator only. `PassengerVehicle` and `Luggage` map to `assets[]`; `Animal` has no equivalent. |
| entitlementRights | EntitlementRight | 0..* | `card` / `entitlement` | (nested in traveller) | See **EntitlementRight**. |

---

## Traveller

> Human traveller.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | `traveller` | `id` | |
| age | integer | 0..1 | `traveller` | `age` | |
| assistant | boolean | 0..1 | — | — | No direct field; carer/PRM needs via `travellerRequirements.prmNeeds[]`. |
| dateOfBirth | date | 0..1 | — | — | No equivalent. |
| externalReference | string | 0..1 | `traveller` | `customerReference` | |
| personalNeeds | PersonalNeed | 0..* | `travellerRequirements` | `prmNeeds[]` | |
| qualifyingCharacteristics | TravellerQualifyingCharacteristics | 0..1 | `userProfile` | (see section) | |

---

## TravellerQualifyingCharacteristics

> Demographic eligibility data.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| fullName | string | 0..1 | `traveller` | `name` | |
| nationality | string | 0..1 | — | — | No equivalent. |
| residency | string | 0..1 | — | — | No equivalent. |
| dateOfBirth | date | 0..1 | — | — | No equivalent. |
| licenseTypes | LicenseType | 0..* | — | — | No equivalent in OMSA offer-search input. |

---

## PersonalNeed

> Accessibility or personal need.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | `travellerRequirements` | `prmNeeds[]` | Operator-defined PRM need codes. |

---

## LicenseType

> Driving licence. **No equivalent in OMSA offer-search input.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | — | — | No equivalent. |

---

## Animal

> Animal travelling alongside its owner. **No equivalent in OMSA.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | No equivalent. |
| type | string | 1..1 | — | — | No equivalent. |
| assistant | boolean | 0..1 | `travellerRequirements` | `prmNeeds[]` | Assistance animal status may be expressed as a PRM need on the accompanying traveller. |

---

## PassengerVehicle

> Traveller-owned vehicle to be transported. Maps to OMSA `assets[]`.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | `asset` | `type` | OMSA uses an `assets[]` array in the request for physical items to transport. |
| type | string | 1..1 | `asset` | `category` | Vehicle category (car, bicycle, etc.). |
| height | integer | 0..1 | `asset` | `height` | |
| width | integer | 0..1 | `asset` | `width` | |
| length | integer | 0..1 | `asset` | `length` | |
| weight | integer | 0..1 | `asset` | `weight` | |
| trailer | PassengerVehicle | 0..1 | — | — | Nested trailer not modelled in OMSA assets. May be a separate asset entry. |
| vehicleRacks | VehicleRack | 0..* | — | — | No equivalent. |

---

## VehicleRack

> Rack mounted on a vehicle. **No equivalent in OMSA.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | No equivalent. |

---

## Luggage

> Bulky item. Maps to OMSA `assets[]`.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | `asset` | `type` | |
| type | string | 1..1 | `asset` | `category` | Luggage category (bicycle, pram, etc.). |
| length / width / height / weight | integer | 0..1 | `asset` | `length` / `width` / `height` / `weight` | |

---

## EntitlementRight

> Credential qualifying for reduced fares.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| issuer | string | 0..1 | `card` | — / `entitlement` | No explicit issuer field; may be embedded in type code. |
| entitlementType | string | 1..1 | `card` | `type` / `entitlement` `type` | |
| code | string | 0..1 | `card` | `id` / `entitlement` `id` | Card or voucher instance number. |

---

## TripPattern

> Proposed sequence of legs.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| tripPatternId | string | 0..1 | — | — | No equivalent. |
| legs | Leg | 1..* | `tripPattern` | `serviceJourneys[]` (timed) OR `travelSpecification` (open O&D) | Same pattern as TOMP-API. |

---

## TimedLeg

> Leg on a fixed timetabled service.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | Discriminator only. |
| legId | string | 0..1 | — | — | No equivalent. |
| sequenceNumber | integer | 0..1 | — | — | Implied by array order. |
| mode | string | 0..1 | `travellerRequirements` | `mode` | Per traveller. |
| serviceJourneyRef | string | 0..1 | `ServiceJourneyReference` | `id` | |
| operatingDate | date | 0..1 | `tripPattern` | `travelDate` | |
| lineNumber | string | 0..1 | `ServiceJourneyReference` | `lineId` | |
| brand | string | 0..1 | — | — | No equivalent. |
| start | LegBoard | 0..1 | — | — | Stop-level boarding data not in OMSA `tripPattern`. |
| end | LegAlight | 0..1 | — | — | No equivalent. |
| intermediateStops | StopPlace | 0..* | — | — | No equivalent. |

---

## ContinuousLeg

> Leg without timetabled stops. Partially covered.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| startTime | dateTime | 0..1 | `travelSpecification` | `startTime` | |
| endTime | dateTime | 0..1 | `travelSpecification` | `endTime` | |
| startLocation | Place | 0..1 | `travelSpecification` | `origin` (placeReference) | |
| endLocation | Place | 0..1 | `travelSpecification` | `destination` (placeReference) | |

---

## TransferLeg

> Transfer between services. **No equivalent in OMSA.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | No equivalent. |

---

## LegBoard

> Boarding event.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `placeReference` | `id` | |
| plannedDepartureTime | dateTime | 1..1 | `tripPattern` | `travelDate` | Date only; no per-leg departure time. |
| pickupLocation | string | 0..1 | — | — | No equivalent. |
| trainNumbers | TrainNumber | 0..* | — | — | No equivalent. |

---

## LegAlight

> Alighting event.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `placeReference` | `id` | |
| plannedArrivalTime | dateTime | 1..1 | — | — | No per-leg arrival time in OMSA. |
| setDownLocation | string | 0..1 | — | — | No equivalent. |

---

## TrainNumber

> Commercial train number. **No equivalent in OMSA offer-search input.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| trainNumberId | string | 1..1 | — | — | No equivalent. |

---

## StopPlace

> Reference to an intermediate stop.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `placeReference` | `id` | |

---

## Place / StopPlaceInput / AddressInput / PointOfInterest / TopologicalPlace

> Same mappings as TOMP-API; OMSA uses the same `placeReference { id?, coordinates?, name? }` pattern.

| EUDIT concept | OMSA concept | OMSA property | Notes |
|---|---|---|---|
| StopPlaceInput.placeRef | `placeReference` | `id` | |
| AddressInput.addressLine1 | `placeReference` | `name` | No structured address. |
| PointOfInterest.name | `placeReference` | `name` | |
| TopologicalPlace.area | `travellerRequirements` | `zones[]` | Zone IDs, not GeoJSON polygon. |
| GeoPosition.longitude / latitude | `GeoCoordinate` | `lon` / `lat` | |

---

## Offer

> Purchasable combination of offer elements.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| offerId | string | 1..1 | `offer` | `offerId` | |
| elements | OfferElement | 1..* | `offer` | `legs[].pricing[]` | OMSA returns pricing per leg, similar to TOMP. |
| farePrice | FarePrice | 0..1 | `offer` | aggregated from `legs[].pricing[]` | Client must aggregate. |
| travelDocument | TravelDocument | 0..1 | — | — | Not returned at offer-search stage. |

---

## OfferElement / TravelRight / Ancillary / Reservation / CompositeOfferElement

> Same pattern as TOMP-API: pricing is per-leg, no discrete offer-element objects in OMSA response.

| EUDIT concept | OMSA concept | OMSA property | Notes |
|---|---|---|---|
| TravelRight | `leg` | `id`, `pricing[]` | Travel right implicit per leg. |
| Ancillary | — | — | No equivalent in offer-search response. |
| Reservation | — | — | Not at offer-search stage. |
| CompositeOfferElement | — | — | No equivalent. |
| FarePrice | `pricing` | `estimated`, `parts[{ amount, currencyCode }]` | |
| TravelDocument | — | — | Not at offer-search stage. |
| Guarantee | — | — | No equivalent. |
