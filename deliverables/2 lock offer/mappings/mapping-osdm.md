# Mapping: Lock Offer → OSDM 3.7.1

Maps each EUDIT **Lock Offer** concept to the corresponding concept/property in **OSDM 3.7.1**.

- **Endpoint 1**: `POST /bookings` (lock / create a provisional booking)
- **Endpoint 2**: `GET /bookings/{bookingId}` (retrieve booking detail)
- **EUDIT concept / Property** — as defined in `lock-offer.yaml`
- **OSDM concept** — the matching schema object in OSDM 3.7.1
- **OSDM property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

In OSDM 3.7.1 the *lock offer* step corresponds to `POST /bookings`, which creates an **unconfirmed booking** from one or more offer parts. The booking has a `confirmationLimit` (= lock expiry) and a `state` that starts as `UNCONFIRMED`. The booking detail is retrieved via `GET /bookings/{bookingId}`.

---

## LockOfferRequest

> Root request body for the lock operation.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `booking` | `offerParts[].offerId` | Each OSDM offer part references the offer it is derived from. A single EUDIT `offerReference` typically maps to one `offerParts[]` entry. |
| aftersalesByRetailerOnly | boolean | 0..1 | — | — | No direct equivalent in OSDM 3.7.1. After-sales channel restrictions are typically governed by bilateral operator/retailer agreements encoded in the fare conditions, not in the booking request. |
| externalRef | string | 0..1 | `booking` | `externalId` | OSDM `externalId` is a caller-assigned reference for correlation with the retailer's system. |
| allocations | AllocationSelection | 0..* | `booking` | `offerParts[].reservationOptions[].reservationOptionId` | In OSDM, seat/space reservations are selected by referencing a `reservationOptionId` returned in the offer's `offerParts[].reservationOptions[]`. |
| ancillaries | AncillarySelection | 0..* | `booking` | `offerParts[].ancillaryOptions[].ancillaryOptionId` | Ancillary selections reference an `ancillaryOptionId` from the offer's `ancillaryOptions[]`. |

---

## AllocationSelection

> Selection of a specific allocation offer element.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| allocationReference | string | 1..1 | `offerPart` | `reservationOptions[].reservationOptionId` | Direct mapping: the EUDIT `allocationReference` is the OSDM `reservationOptionId`. |

---

## AncillarySelection

> Selection of a specific ancillary offer element.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| ancillaryReference | string | 1..1 | `offerPart` | `ancillaryOptions[].ancillaryOptionId` | Direct mapping: the EUDIT `ancillaryReference` is the OSDM `ancillaryOptionId`. |

---

## LockedOfferDetailRequest

> Request to retrieve the full detail of a locked offer.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `booking` | `bookingId` (path parameter) | For the OSDM `GET /bookings/{bookingId}` endpoint the identifier is passed as a path parameter, not in a request body. This EUDIT field maps to that path parameter. |

---

## LockOfferDelivery

> Server response to the lock-offer request.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `booking` | `bookingId` | Server-assigned booking identifier. |
| expiryTime | dateTime | 1..1 | `booking` | `confirmationLimit` | Date-time by which the booking must be confirmed before the hold is released. |
| offerRef | string | 1..1 | `booking` | `offerParts[].offerId` | Reference back to the source offer. |
| lockedOffer | LockedOffer | 0..1 | `booking` | (full `booking` response body) | OSDM always returns the full booking object in the `POST /bookings` response; no separate detail fetch is required. This field echoes that inline detail. |
| warnings | Warning | 0..* | — | — | No structured warning array in OSDM 3.7.1 booking response; informational messages may appear in `notes`. |
| links | Link | 0..* | — | — | No hypermedia links in OSDM booking response. Subsequent operations (confirm, cancel) are inferred from standard OSDM endpoints. |

---

## LockedOffer

> The time-limited held offer with full detail.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `booking` | `bookingId` | |
| name | string | 0..1 | — | — | No name field on OSDM booking. Human-readable name derived from fare product descriptions. |
| summary | string | 0..1 | — | — | No summary field. |
| matching | string | 0..1 | — | — | No equivalent. OSDM booking is always an exact match to the selected offer. |
| status | string | 1..1 | `booking` | `state` | OSDM `state` values: `UNCONFIRMED` (= locked), `CONFIRMED`, `CANCELLED`. EUDIT adds `expired` as a distinct state. |
| afterSalesFlexibility | string | 0..* | `fareProduct` | `afterSalesConditions[]` | OSDM after-sales conditions describe refund and exchange rules per fare product element. |
| personalInformationRequired | boolean | 0..1 | `booking` | `passengerSpecificationRequired` | OSDM indicates whether passenger personal data must be provided before confirmation. |
| externalRef | string | 0..1 | `booking` | `externalId` | Echo of the caller-assigned external reference. |
| elements | OfferElement | 1..* | `booking` | `offerParts[]` | Each OSDM `offerPart` groups fare products, reservations, and ancillaries. See **OfferElement** below. |
| minimumPrice | Price | 0..1 | `booking` | `price` | OSDM `booking.price` is the total booking price (equivalent to EUDIT minimum price). |
| summaryDetails | SummaryDetail | 0..* | — | — | No equivalent. Journey summary is derived from the trip specification in the offer. |
| providedSections | ProvidedSections | 0..* | `booking` | `offerParts[].tripCoverage` | OSDM `tripCoverage` indicates which legs an offer part covers. |
| guarantees | Guarantee | 0..* | — | — | No structured guarantee array in OSDM 3.7.1. Connection guarantees are operator policy, not returned in booking response. |
| links | Link | 0..* | — | — | No hypermedia links in OSDM booking response. |

---

## OfferElement

> Base class for purchasable elements within the locked offer.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| offerElementId | string | 1..1 | `fareProduct` / `reservation` / `ancillaryProduct` | `fareProductId` / `reservationId` / `ancillaryProductId` | OSDM uses separate ID fields per element type. |
| offerElementType | string | 1..1 | — | — | Discriminator only; OSDM distinguishes element types by array placement. |
| travellingEntities | TravellingEntityReference | 0..* | `offerPart` | `passengerRef` | Each OSDM offer part references the passenger(s) it applies to via `passengerRef`. |
| matching | string | 0..1 | — | — | No equivalent. |
| requiredInformation | RequiredInformation | 0..1 | `offerPart` | `requiredPassengerSpecification` | OSDM indicates per offer part which passenger data fields are required. |
| price | Price | 0..1 | `offerPart` | `price { amount, currency }` | |
| fareProduct | FareProduct | 0..1 | `offerPart` | `fareProducts[].fareProductRef` | |
| guarantees | Guarantee | 0..* | — | — | No equivalent at offer-part level. |
| providedSections | ProvidedSections | 0..* | `offerPart` | `tripCoverage` | |

---

## TravelRight

> Right to travel on a specific section.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| offerElementType (travelRight) | — | — | `offerPart` | `fareProducts[]` | OSDM encodes travel rights as fare products within an offer part. |
| ancillaries | Ancillary | 0..* | `offerPart` | `ancillaryProducts[]` | Ancillaries co-located in the same OSDM offer part. |
| allocations | Allocation | 1..* | `offerPart` | `reservations[]` | OSDM reservations are co-located in the same offer part as the travel right. |

---

## Ancillary

> Optional ancillary service.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| ancillaryId | string | 0..1 | `ancillaryProduct` | `ancillaryProductId` | |
| type | string | 0..1 | `ancillaryProduct` | `type` | |

---

## SpotAllocation

> Allocation to a specific physical spot.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| legId | string | 0..1 | `reservation` | `serviceJourneyRef` | OSDM reservation references the service journey the seat is on. |
| startPlace | string | 0..1 | `reservation` | `boardingStopPlaceRef` | |
| endPlace | string | 0..1 | `reservation` | `alightingStopPlaceRef` | |
| typeOfSpot | string | 0..1 | `reservation` | `seatInformation.placeType` | OSDM `placeType` (e.g. `SEAT`, `BERTH`, `COUCHETTE`). |

---

## AssetAllocation

> Allocation to a named asset. **Partial equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| (no additional properties) | — | — | `reservation` | `seatInformation.placeType` | OSDM does not have a dedicated named-asset allocation concept; vehicle slot or cabin reservations are modelled as reservations with an appropriate `placeType`. |

---

## TravellingEntityReference

> Reference to a travelling entity.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| travellingEntityRef | integer | 1..1 | `offerPart` | `passengerRef` | OSDM `passengerRef` links an offer part to the passenger it applies to. |

---

## Price

> Monetary amount in a currency.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| currencyCode | string | 1..1 | `price` | `currency` | ISO 4217. |
| amount | number | 1..1 | `price` | `amount` | |
| vat | Vat | 0..* | — | — | OSDM 3.7.1 does not return a structured VAT breakdown; gross price only. |

---

## Vat

> VAT component of a price. **No equivalent in OSDM 3.7.1.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| amount / currencyCode / country / percentage | various | — | — | — | OSDM returns gross prices only; VAT breakdown is not part of the booking response. |

---

## ProvidedSections

> Journey section covered by an offer or element.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| startLegId | string | 0..1 | `tripCoverage` | `legs[].legId` (first) | OSDM `tripCoverage` lists the specific legs covered by an offer part. |
| endLegId | string | 0..1 | `tripCoverage` | `legs[].legId` (last) | |
| startPlace | string | 0..1 | `tripCoverage` | `originStopPlaceRef` | |
| endPlace | string | 0..1 | `tripCoverage` | `destinationStopPlaceRef` | |
| tripPatternRef | string | 0..1 | — | — | No equivalent; OSDM offer parts reference legs directly. |

---

## SummaryDetail

> Human-readable journey summary. **No equivalent in OSDM.**

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| geometry / temporal / conditions | string | 0..1 | — | — | Derivable from the trip specification and fare product conditions; not returned as a discrete summary object. |

---

## FareProduct

> Fare product reference.

| Property | Type | Mult. | OSDM concept | OSDM property | Notes |
|---|---|---|---|---|---|
| productRef | string | 0..1 | `fareProduct` | `fareProductRef` | Reference to the immutable fare product in the operator's fare catalogue. |

---

## Guarantee / RequiredInformation / Warning

> Stub types.

| EUDIT concept | OSDM concept | OSDM property | Notes |
|---|---|---|---|
| Guarantee | — | — | No structured guarantee in OSDM 3.7.1 booking response. |
| RequiredInformation | `offerPart` | `requiredPassengerSpecification` | OSDM lists required passenger data fields per offer part. |
| Warning | — | — | No structured warning type in OSDM booking response. |
