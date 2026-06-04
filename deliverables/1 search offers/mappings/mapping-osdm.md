# Mapping: Search Offers → OSDM 3.7.1

Maps each EUDIT **Search Offers** concept to the corresponding concept/property in **OSDM 3.7.1**.

- **Endpoint**: `POST /offers`
- **EUDIT concept / Property** — as defined in `search-offers.yaml`
- **OSDM concept** — the matching schema object in OSDM 3.7.1
- **OSDM property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

---

## OfferRequest

> Root request body for the offer search.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| tripPatterns | TripPattern | 1..* | `offerRequest` | `tripSpecification` / `tripIds[]` / `tripSearchCriteria` | OSDM supports three trip-input modes: (1) explicit `tripSpecification.legs[]`, (2) pre-searched `tripIds[]`, (3) open O&D `tripSearchCriteria`. EUDIT `TripPattern` maps to mode (1). Multiple trip patterns map to `inboundTripSpecification` for a return journey. |
| travellers | TravellingEntity | 1..* | `offerRequest` | `tripCoverage.passengers[]` | Only `Traveller` sub-type maps; `Animal`, `PassengerVehicle`, `VehicleRack`, `Luggage` have no equivalent as passengers. |
| filter | SearchOfferFilter | 0..1 | `offerRequest` | `searchCriteria` | |
| policy | SearchOfferPolicy | 0..1 | `offerRequest` | `searchCriteria` (partial) | Partial; see **SearchOfferPolicy**. |

---

## SearchOfferPolicy

> Pagination and currency preferences.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| numberOfResultsBefore | integer | 0..1 | — | — | No equivalent. OSDM does not support pagination relative to departure time. |
| numberOfResultsAfter | integer | 0..1 | `searchCriteria` | `maxOffers` | Partial match: `maxOffers` limits total results, not results after a given time. |
| currency | string | 0..1 | `searchCriteria` | `currency` | Direct mapping (ISO 4217). |

---

## SearchOfferFilter

> Constraints on which offers to return.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| modes | string | 0..* | — | — | No explicit mode filter in OSDM offer request; modes are implicit in the trip specification. |
| classOfUse | string | 0..* | `searchCriteria` | `fareClass` | OSDM `fareClass` (e.g. `FIRST`, `SECOND`). Single value in OSDM vs. array in EUDIT. |
| mediaTypes | string | 0..* | — | — | No equivalent. OSDM does not filter by fulfilment media type at offer-search stage. |
| requestedSections | RequestedSections | 0..* | — | — | No equivalent. |

---

## RequestedSections

> Targets offer search to a subset of legs. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| startLegId | string | 0..1 | — | — | No equivalent. |
| endLegId | string | 0..1 | — | — | No equivalent. |
| startPlace | string | 0..1 | — | — | No equivalent. |
| endPlace | string | 0..1 | — | — | No equivalent. |
| tripReference | string | 0..1 | — | — | No equivalent. |

---

## TravellingEntity

> Base class.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | `passenger` | `passengerRef` | Caller-assigned reference used for cross-referencing. |
| entityType | string | 1..1 | — | — | Discriminator only. `Animal`, `PassengerVehicle`, `Luggage` not modelled as OSDM passengers. |
| entitlementRights | EntitlementRight | 0..* | `passenger` | `reductionCards[]` | See **EntitlementRight**. |

---

## Traveller

> Human traveller.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | `passenger` | `passengerRef` | |
| age | integer | 0..1 | `passenger` | `age` | |
| assistant | boolean | 0..1 | — | — | No equivalent. Carer/assistant needs expressed via `accessibilityNeeds`. |
| dateOfBirth | date | 0..1 | — | — | No date-of-birth field in OSDM passenger; age used instead. |
| externalReference | string | 0..1 | — | — | No equivalent. |
| personalNeeds | PersonalNeed | 0..* | `passenger` | `accessibilityNeeds[]` | |
| qualifyingCharacteristics | TravellerQualifyingCharacteristics | 0..1 | `passenger` | `type` (partial) | OSDM uses a coarse passenger type enum (`ADULT`, `CHILD`, `INFANT`) rather than detailed characteristics. |

---

## TravellerQualifyingCharacteristics

> Demographic eligibility data.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| fullName | string | 0..1 | — | — | Not a fare-search input in OSDM; provided at booking stage. |
| nationality | string | 0..1 | — | — | No equivalent in OSDM offer request. |
| residency | string | 0..1 | — | — | No equivalent. |
| dateOfBirth | date | 0..1 | — | — | No equivalent. |
| licenseTypes | LicenseType | 0..* | — | — | No equivalent. |

---

## PersonalNeed

> Accessibility or personal need.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | `passenger` | `accessibilityNeeds[]` | OSDM uses NeTEx `AccessibilityNeedType` values. |

---

## LicenseType

> Driving licence. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | — | — | No equivalent. |

---

## Animal

> Animal travelling alongside its owner. **No equivalent as a passenger in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | Pets may appear as ancillary offer parts in OSDM response but are not modelled as input passengers. |
| type | string | 1..1 | — | — | No equivalent. |
| assistant | boolean | 0..1 | `passenger` | `accessibilityNeeds[]` | Assistance animal may be expressed as an accessibility need on the accompanying traveller. |

---

## PassengerVehicle

> Traveller-owned vehicle to be transported. **No equivalent in OSDM (rail-focused standard).**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | Vehicle transport (car/ferry) is outside OSDM scope. |
| type / height / width / length / weight | various | 0..1 | — | — | No equivalent. |
| trailer | PassengerVehicle | 0..1 | — | — | No equivalent. |
| vehicleRacks | VehicleRack | 0..* | — | — | No equivalent. |

---

## VehicleRack

> Rack on a passenger vehicle. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | No equivalent. |

---

## Luggage

> Bulky item a traveller brings.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| entityType | string | 1..1 | — | — | Luggage is not an input entity in OSDM; it may appear as an ancillary in the offer response. |
| type / dimensions / weight | various | 0..1 | — | — | No equivalent as offer-request input. |

---

## EntitlementRight

> Credential qualifying for reduced fares.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| issuer | string | 0..1 | `reductionCard` | `issuerCode` | |
| entitlementType | string | 1..1 | `reductionCard` | `cardType` | OSDM uses a structured `cardType` enum (e.g. `BAHNCARD`, `RAILPLUS`). |
| code | string | 0..1 | — | — | Card instance number not standard in OSDM reduction card; may be passed as `passengerRef` extension. |

---

## TripPattern

> Proposed sequence of legs.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| tripPatternId | string | 0..1 | — | — | No equivalent. |
| legs | Leg | 1..* | `tripSpecification` | `legs[]` | Each EUDIT `TimedLeg` maps to an OSDM leg. `ContinuousLeg` and `TransferLeg` have no equivalent. |

---

## TimedLeg

> Leg on a fixed timetabled service.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | Discriminator only. |
| legId | string | 0..1 | — | — | No equivalent. |
| sequenceNumber | integer | 0..1 | — | — | Implied by array order in `tripSpecification.legs[]`. |
| mode | string | 0..1 | — | — | No explicit mode field on OSDM leg; implicit in `serviceJourneyRef`. |
| serviceJourneyRef | string | 0..1 | `leg` | `serviceJourneyRef` | Direct mapping. |
| operatingDate | date | 0..1 | `leg` | `departureDateTime` (date part) | OSDM uses a full `departureDateTime`; date extracted from it. |
| lineNumber | string | 0..1 | — | — | No equivalent on OSDM leg input. |
| brand | string | 0..1 | — | — | No equivalent. |
| start | LegBoard | 0..1 | `leg` | `originStopPlaceRef`, `departureDateTime` | |
| end | LegAlight | 0..1 | `leg` | `destinationStopPlaceRef`, `arrivalDateTime` | |
| intermediateStops | StopPlace | 0..* | — | — | Not an input field in OSDM. |

---

## ContinuousLeg

> Leg without fixed timetabled stops. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | No equivalent. OSDM is timetabled-service only. |
| startTime / endTime | dateTime | 0..1 | — | — | No equivalent. |
| startLocation / endLocation | Place | 0..1 | — | — | No equivalent. |

---

## TransferLeg

> Transfer between services. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| legType | string | 1..1 | — | — | No equivalent. Transfers are implicit in the leg sequence. |

---

## LegBoard

> Boarding event at the start of a timed leg.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `leg` | `originStopPlaceRef` | UIC or NeTEx stop ID. |
| plannedDepartureTime | dateTime | 1..1 | `leg` | `departureDateTime` | |
| pickupLocation | string | 0..1 | — | — | No equivalent. |
| trainNumbers | TrainNumber | 0..* | — | — | Not an input field in OSDM. |

---

## LegAlight

> Alighting event at the end of a timed leg.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | `leg` | `destinationStopPlaceRef` | |
| plannedArrivalTime | dateTime | 1..1 | `leg` | `arrivalDateTime` | |
| setDownLocation | string | 0..1 | — | — | No equivalent. |

---

## TrainNumber

> Commercial train number.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| trainNumberId | string | 1..1 | — | — | Partially encoded in `serviceJourneyRef`; no separate train-number input in OSDM. |

---

## StopPlace

> Reference to an intermediate stop.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| stopPlaceRef | string | 1..1 | — | — | Intermediate stops are not input in OSDM; derived from `serviceJourneyRef`. |

---

## Place

> Base place type.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| placeId | string | 0..1 | — | — | No equivalent. |
| placeType | string | 1..1 | — | — | Discriminator only. |
| location | GeoPosition | 0..1 | — | — | OSDM uses stop references only; geo-coordinate input is not supported. |

---

## StopPlaceInput

> Stop place identified by reference.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| placeRef | string | 0..1 | `leg` | `originStopPlaceRef` / `destinationStopPlaceRef` | UIC or NeTEx stop ID. |

---

## AddressInput

> Postal address. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| addressLine1 / addressLine2 | string | 0..1 | — | — | OSDM does not support address-based place input. |

---

## PointOfInterest

> Named point of interest. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| name | string | 0..1 | — | — | No equivalent. |

---

## TopologicalPlace

> Geographic area or zone.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| area | string | 0..1 | — | — | No equivalent as trip origin/destination input. |
| range | integer | 0..1 | — | — | No equivalent. |

---

## GeoPosition

> WGS-84 coordinate pair. **No equivalent in OSDM offer-request input.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| longitude | number | 1..1 | — | — | OSDM uses stop-reference IDs only. |
| latitude | number | 1..1 | — | — | No equivalent. |

---

## Offer

> Purchasable combination of offer elements.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| offerId | string | 1..1 | `offer` | `offerId` | Direct mapping. |
| elements | OfferElement | 1..* | `offer` | `fareProducts[]`, `reservations[]`, `ancillaryProducts[]` | Sub-types map to different OSDM arrays. |
| farePrice | FarePrice | 0..1 | `offer` | `totalPrice { amount, currency }` | |
| travelDocument | TravelDocument | 0..1 | `offer` | `fulfillmentOptions[]` | OSDM returns fulfilment options (media/barcode type) at offer stage. |

---

## OfferElement

> Base class for purchasable elements.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| offerElementId | string | 1..1 | `fareProduct` | `fareProductId` | Or `reservationId` for reservation elements. |
| offerElementType | string | 1..1 | — | — | Discriminator only. |
| travellingEntities | string | 0..* | `fareProduct` | `passengerRef` | OSDM links fare products to specific passengers. |
| farePrice | FarePrice | 0..1 | `fareProduct` | `price { amount, currency }` | |
| travelDocument | TravelDocument | 0..1 | — | — | Fulfilment detail at offer level, not per element. |
| guarantees | Guarantee | 0..* | — | — | No direct equivalent; connection guarantee is a separate concept in OSDM. |

---

## TravelRight

> Right to travel on a specific leg or zone.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| legRef | string | 0..1 | `fareProduct` | `legRef` / `serviceJourneyRef` | OSDM fare products reference the legs they cover. |
| zoneRef | string | 0..1 | `fareProduct` | `tariffZoneRef` | Zone-based fares in OSDM reference tariff zones. |

---

## Ancillary

> Optional ancillary service.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| ancillaryId | string | 0..1 | `ancillaryProduct` | `ancillaryProductId` | |
| type | string | 0..1 | `ancillaryProduct` | `type` | |

---

## Reservation

> Seat or space reservation.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| offerElementType | string | 1..1 | `reservation` | — | OSDM has a discrete `reservation` object with `reservationId`, `seatInformation`. |

---

## CompositeOfferElement

> Grouped offer elements. Partial equivalent in OSDM via offer parts.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| elements | OfferElement | 2..* | `offerPart` | `fareProducts[]` | OSDM groups fare products into offer parts; no explicit "composite" type. |

---

## FarePrice

> Price of an offer or offer element. (Stub — detail TBD.)

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| (TBD) | — | — | `price` | `amount`, `currency` | OSDM `price` object carries amount + ISO 4217 currency code. |

---

## TravelDocument

> Proof of entitlement. (Stub — detail TBD.)

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| (TBD) | — | — | `fulfillmentOption` | `mediaType`, `barcodeType` | OSDM returns available fulfilment options (paper, mobile, smart card) per offer. |

---

## Guarantee

> Guarantee associated with an offer element. (Stub — detail TBD.)

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| (TBD) | — | — | — | — | No direct equivalent. Connection guarantee is a separate concept in OSDM trip search, not offer search. |
