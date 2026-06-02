# Mapping: Search Offers → FerryGateway 1.3.1

Maps each EUDIT **Search Offers** concept to the corresponding concept/property in **FerryGateway 1.3.1**.

- **EUDIT concept / Property** — as defined in `search-offers.yaml`
- **FerryGateway concept** — the matching XML message / element in FerryGateway 1.3.1
- **FerryGateway element** — the matching element/attribute name
- **Notes** — mapping remarks, gaps, or open questions

**Important scope note**: FerryGateway 1.3.1 uses XML request/response messages (not REST/JSON).
There is no single "offer search" endpoint; the closest equivalent is a two-step flow:

1. **`GetSailingsRequest`** → availability (timetable + availability per sailing)
2. **`GetPriceRequest`** → pricing for selected sailings

This mapping covers both messages where relevant. The response mapping covers
`GetSailingsResponse` and `GetPriceResponse`.

---

## OfferRequest

> Root request body for the offer search.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| tripPatterns | TripPattern | 1..* | `GetSailingsRequest` | `SailingDate`, `RouteCode` | FerryGateway selects sailings by route (UN/LOCODE port pair) and date, not by a NeTEx service journey reference. Multiple trip patterns (e.g. outbound + return) require separate `GetSailingsRequest` calls. |
| travellers | TravellingEntity | 1..* | `GetSailingsRequest` / `GetPriceRequest` | `Passengers`, `Vehicles` | Human travellers → `Passengers`; vehicles → `Vehicles`. `Animal` and `Luggage` have no direct equivalent. |
| filter | SearchOfferFilter | 0..1 | — | — | No filter equivalent in FerryGateway; all offer types for the sailing are returned. |
| policy | SearchOfferPolicy | 0..1 | — | — | No pagination or currency control. |

---

## SearchOfferPolicy

> Pagination and currency preferences. **No equivalent in FerryGateway.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| numberOfResultsBefore | integer | 0..1 | — | — | No equivalent. |
| numberOfResultsAfter | integer | 0..1 | — | — | No equivalent. |
| currency | string | 0..1 | — | — | Currency is operator configuration; not a request parameter. |

---

## SearchOfferFilter

> Constraints on which offers to return. **No equivalent in FerryGateway.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| modes | string | 0..* | — | — | Ferry is the only mode; filter not applicable. |
| classOfUse | string | 0..* | — | — | No equivalent; accommodation types returned from the sailing result. |
| mediaTypes | string | 0..* | — | — | No equivalent. |
| requestedSections | RequestedSections | 0..* | — | — | No equivalent. |

---

## RequestedSections

> Targets offer search to a subset of legs. **No equivalent in FerryGateway.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | No equivalent. |

---

## TravellingEntity

> Base class.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | — | — | No per-entity identifier; passengers aggregated by category code. |
| entityType | string | 1..1 | — | — | Discriminator only. `Animal` has no equivalent. |
| entitlementRights | EntitlementRight | 0..* | `GetOfferCodesRequest` | `OfferCode` | Promotional/discount codes are fetched separately; see **EntitlementRight**. |

---

## Traveller

> Human traveller.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | — | — | Not used; passengers aggregated by `CategoryCode`. |
| age | integer | 0..1 | `Passenger` | `CategoryCode` | Age band encoded in category code (Adult, Child, Infant); age bounds from `GetPassengerAndVehicleTypesRequest`. |
| assistant | boolean | 0..1 | — | — | No equivalent. |
| dateOfBirth | date | 0..1 | — | — | No equivalent. |
| externalReference | string | 0..1 | — | — | No equivalent. |
| personalNeeds | PersonalNeed | 0..* | — | — | No equivalent in FerryGateway offer search; wheelchair/accessibility needs handled at booking. |
| qualifyingCharacteristics | TravellerQualifyingCharacteristics | 0..1 | — | — | No equivalent; eligibility encoded in category code. |

---

## TravellerQualifyingCharacteristics

> Demographic eligibility data. **No equivalent in FerryGateway.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | Eligibility is abstracted into passenger category codes from `GetPassengerAndVehicleTypesRequest`. |

---

## PersonalNeed

> Accessibility or personal need. **No equivalent in FerryGateway offer search.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | — | — | No equivalent. Accessibility needs provided at booking stage. |

---

## LicenseType

> Driving licence. **No equivalent in FerryGateway.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | — | — | No equivalent. |

---

## Animal

> Animal travelling alongside its owner. **No equivalent in FerryGateway offer search.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | Pets may be handled at booking via service codes, not in offer search. |
| type / assistant | various | 0..1 | — | — | No equivalent. |

---

## PassengerVehicle

> Traveller-owned vehicle to be transported.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | `Vehicle` | — | Direct concept match; ferry bookings typically involve vehicle transport. |
| type | string | 0..1 | `Vehicle` | `CategoryCode` | Vehicle category from `GetPassengerAndVehicleTypesRequest` (car, motorhome, caravan, motorbike, etc.). |
| height | integer | 0..1 | `Vehicle` | `Height` | In centimetres (same unit). |
| width | integer | 0..1 | `Vehicle` | `Width` | In centimetres. |
| length | integer | 0..1 | `Vehicle` | `Length` | In centimetres. |
| weight | integer | 0..1 | — | — | No weight field on FerryGateway vehicle; some operators handle via category. |
| trailer | PassengerVehicle | 0..1 | `Vehicle` | `TrailerId` | FerryGateway has a `TrailerId` field for a towed trailer; full trailer dimensions not modelled. |
| vehicleRacks | VehicleRack | 0..* | — | — | No equivalent. |

---

## VehicleRack

> Rack mounted on a vehicle. **No equivalent in FerryGateway.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | No equivalent. |

---

## Luggage

> Bulky item a traveller brings. **No equivalent in FerryGateway offer search.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | Not modelled in FerryGateway offer search. Bicycles and special luggage are handled via service codes at booking. |

---

## EntitlementRight

> Credential qualifying for reduced fares.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| issuer | string | 0..1 | — | — | Not explicitly modelled; implicit in the offer code value. |
| entitlementType | string | 1..1 | `GetOfferCodesRequest` | `OfferType` | FerryGateway has a dedicated `GetOfferCodesRequest` / `GetOfferCodesResponse` message flow to retrieve applicable promotional and loyalty codes. |
| code | string | 0..1 | `GetPriceRequest` | `OfferCode` | The promotional or discount code passed to `GetPriceRequest`. |

---

## TripPattern

> Proposed sequence of legs.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| tripPatternId | string | 0..1 | — | — | No equivalent. |
| legs | Leg | 1..* | `GetSailingsRequest` | `RouteCode`, `SailingDate` | FerryGateway addresses a single sailing route per request, not a multi-leg trip pattern. Consecutive sailings require multiple calls. |

---

## TimedLeg

> Leg on a fixed timetabled service.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| serviceJourneyRef | string | 0..1 | `Sailing` | `SailingId` | Returned in `GetSailingsResponse`; passed back in `GetPriceRequest`. |
| operatingDate | date | 0..1 | `GetSailingsRequest` | `SailingDate` | |
| lineNumber | string | 0..1 | `Sailing` | `RouteCode` | Route code (port pair) is the closest equivalent. |
| brand | string | 0..1 | — | — | No equivalent. |
| start (LegBoard) | LegBoard | 0..1 | `GetSailingsRequest` | `RouteCode` (origin port) | Origin port (UN/LOCODE) from `RouteCode`. |
| end (LegAlight) | LegAlight | 0..1 | `GetSailingsRequest` | `RouteCode` (destination port) | Destination port from `RouteCode`. |
| intermediateStops | StopPlace | 0..* | — | — | Not applicable to direct ferry sailings. |

---

## ContinuousLeg / TransferLeg

> **No equivalent in FerryGateway.**

| EUDIT concept | Notes |
|---|---|
| ContinuousLeg | Ferry sailings are timetabled; no continuous leg concept. |
| TransferLeg | No equivalent. |

---

## LegBoard

> Boarding event at the start of a timed leg.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `GetSailingsRequest` | `RouteCode` (origin part) | UN/LOCODE port code. |
| plannedDepartureTime | dateTime | 1..1 | `Sailing` | `DepartureTime` | Returned in `GetSailingsResponse`. |
| pickupLocation | string | 0..1 | — | — | No equivalent. |
| trainNumbers | TrainNumber | 0..* | — | — | No equivalent. |

---

## LegAlight

> Alighting event.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `GetSailingsRequest` | `RouteCode` (destination part) | UN/LOCODE port code. |
| plannedArrivalTime | dateTime | 1..1 | `Sailing` | `ArrivalTime` | Returned in `GetSailingsResponse`. |
| setDownLocation | string | 0..1 | — | — | No equivalent. |

---

## TrainNumber / StopPlace

| EUDIT concept | FerryGateway concept | Notes |
|---|---|---|
| TrainNumber | — | No equivalent. |
| StopPlace.stopPlaceRef | UN/LOCODE port code | Used in `RouteCode` (e.g. `NLRTM-GBHLL`). |

---

## Place / StopPlaceInput / AddressInput / PointOfInterest / TopologicalPlace / GeoPosition

| EUDIT concept | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|
| StopPlaceInput.placeRef | `GetSailingsRequest` | `RouteCode` (port part) | UN/LOCODE port identifier. |
| AddressInput | — | — | No equivalent. |
| PointOfInterest | — | — | No equivalent. |
| TopologicalPlace | — | — | No equivalent. |
| GeoPosition | — | — | No equivalent; FerryGateway uses port codes only. |

---

## Offer

> Purchasable combination of offer elements.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| offerId | string | 1..1 | `GetPriceResponse` | `PriceId` / `QuoteId` | FerryGateway returns a price quote with a reference ID. |
| elements | OfferElement | 1..* | `GetPriceResponse` | `PriceItems[]` | Price items per passenger/vehicle/service category. |
| farePrice | FarePrice | 0..1 | `GetPriceResponse` | `TotalPrice { Amount, Currency }` | |
| travelDocument | TravelDocument | 0..1 | — | — | Not returned at price-quote stage; QR/barcode issued at `ConfirmReservationResponse`. |

---

## OfferElement / TravelRight / Ancillary / Reservation

| EUDIT concept | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|
| TravelRight | `PriceItem` | `Sailing`, `CategoryCode`, `Amount` | Right to travel encoded in a price item per passenger/vehicle. |
| Ancillary | `ServiceItem` | `ServiceCode`, `Amount` | Optional services (meals, pet transport, etc.) are `ServiceItem`s. |
| Reservation | — | — | Reservations created via `CreateReservationRequest`, not at price-quote stage. |
| CompositeOfferElement | — | — | No equivalent. |
| FarePrice | `PriceItem` | `Amount`, `Currency` | |
| TravelDocument | — | — | Issued at booking confirmation as a QR code / PDF. |
| Guarantee | — | — | No equivalent. |
