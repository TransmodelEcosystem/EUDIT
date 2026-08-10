# Mapping: Lock Offer → BoB Product API v3.4.0

Maps each EUDIT **Lock Offer** concept to the corresponding concept/property in the **BoB Product API v3.4.0**.

- **Endpoint 1**: `POST /reservations` (lock) — or `POST /order` in some BoB profiles
- **Endpoint 2**: `GET /reservations/{reservationId}` (detail)
- **EUDIT concept / Property** — as defined in `lock-offer.yaml`
- **BoB concept** — the matching schema object in BoB Product API
- **BoB property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

**Important scope note**: BoB Product API v3.4.0 operates at the product/reservation layer. A "lock" in EUDIT terms corresponds to a **provisional reservation** (`POST /reservations`) that holds a product before final purchase. BoB does not expose a generic "offer reference" flow; instead, the product to reserve is identified by `productId` and traveller categories. Several EUDIT fields have no BoB equivalent because BoB abstracts traveller and after-sales details.

---

## LockOfferRequest

> Root request body for the lock operation.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `reservationRequest` | `productId` | The EUDIT offer reference must be translated to a BoB `productId` by the integrating system; BoB does not carry a generic offer-session token. |
| aftersalesByRetailerOnly | boolean | 0..1 | — | — | No equivalent in BoB v3.4.0. After-sales channel restriction is not a reservation parameter. |
| externalRef | string | 0..1 | `reservationRequest` | `externalReference` | Caller-assigned reference for correlation purposes. |
| allocations | AllocationSelection | 0..* | `reservationRequest` | `seatPreferences[]` | BoB supports seat preferences as part of the reservation request; specific seat selection maps to `seatPreferences[].seatId`. |
| ancillaries | AncillarySelection | 0..* | `reservationRequest` | `ancillaryIds[]` | BoB ancillary selection uses `ancillaryIds[]` referencing products from the BoB product catalogue. |

---

## AllocationSelection

> Selection of a specific allocation offer element.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| allocationReference | string | 1..1 | `reservationRequest` | `seatPreferences[].seatId` | The `allocationReference` maps to a seat or space identifier in the BoB seat-map or allocation catalogue. |

---

## AncillarySelection

> Selection of a specific ancillary offer element.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| ancillaryReference | string | 1..1 | `reservationRequest` | `ancillaryIds[]` | The `ancillaryReference` maps to a BoB ancillary product ID from the product catalogue. |

---

## LockedOfferDetailRequest

> Request to retrieve the full detail of a locked offer.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `reservation` | `reservationId` | EUDIT `offerReference` (= `lockedOfferId`) maps to BoB `reservationId`; detail retrieved via `GET /reservations/{reservationId}`. |

---

## LockOfferDelivery

> Server response to the lock-offer request.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `reservation` | `reservationId` | Server-assigned BoB reservation identifier. |
| expiryTime | dateTime | 1..1 | `reservation` | `expirationDateTime` | BoB `expirationDateTime` — ISO 8601 date-time after which the provisional reservation lapses. |
| offerRef | string | 1..1 | `reservation` | `productId` | Echo of the source product identifier. |
| lockedOffer | LockedOffer | 0..1 | `reservation` | (full reservation body) | BoB returns a `reservation` object inline; `lockedOffer` corresponds to this object. |
| warnings | Warning | 0..* | — | — | BoB v3.4.0 does not define a `warnings[]` field on reservation responses; non-fatal conditions are conveyed through HTTP status codes or error payloads. |
| links | Link | 0..* | — | — | BoB uses REST resource URIs rather than HATEOAS `links[]` arrays. |

---

## LockedOffer

> The time-limited held offer with full detail.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `reservation` | `reservationId` | |
| name | string | 0..1 | — | — | No name field on BoB reservation. Product name derivable via `GET /product/{productId}`. |
| summary | string | 0..1 | `reservation` | `description` | BoB `description` (if present) is the closest equivalent. |
| matching | string | 0..1 | — | — | No equivalent. |
| status | string | 1..1 | `reservation` | `status` | BoB reservation statuses: `PROVISIONAL` (= locked), `CONFIRMED`, `CANCELLED`, `EXPIRED`. |
| afterSalesFlexibility | string | 0..* | — | — | No equivalent field on BoB reservation. After-sales conditions are embedded in the product definition (`GET /product/{productId}`). |
| personalInformationRequired | boolean | 0..1 | — | — | No equivalent Boolean flag in BoB. Passenger data requirements are defined at product level. |
| externalRef | string | 0..1 | `reservation` | `externalReference` | Echo of the caller's external reference. |
| elements | OfferElement | 1..* | `reservation` | `products[]` | Each BoB product within the reservation corresponds to a travel-right or ancillary offer element. |
| minimumPrice | Price | 0..1 | `reservation` | `totalPrice { amount, currency }` | BoB returns a `totalPrice` at reservation level. |
| summaryDetails | SummaryDetail | 0..* | — | — | No structured summary in BoB; derivable from product route/validity fields. |
| providedSections | ProvidedSections | 0..* | `reservation` | `route { from, to }` | BoB expresses the covered section as an origin–destination route reference. |
| guarantees | Guarantee | 0..* | — | — | No equivalent in BoB Product API v3.4.0. |
| links | Link | 0..* | — | — | No HATEOAS links in BoB reservation response. |

---

## OfferElement

> Base class for purchasable elements within the locked offer.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| offerElementId | string | 1..1 | `product` | `productId` | BoB `productId` is the closest equivalent to an offer-element identifier. |
| offerElementType | string | 1..1 | — | — | Discriminator only; BoB uses product categories instead. |
| travellingEntities | TravellingEntityReference | 0..* | `reservationRequest` | `travellers[{ categoryId, quantity }]` | BoB associates travellers at reservation level using category codes, not per element. |
| matching | string | 0..1 | — | — | No equivalent. |
| requiredInformation | RequiredInformation | 0..1 | — | — | No equivalent per-element required-information structure in BoB. |
| price | Price | 0..1 | `product` | `price { amount, currency }` | Per-product price as returned by BoB. |
| fareProduct | FareProduct | 0..1 | `product` | `fareId` | BoB `fareId` references the fare that applies to the product. |
| guarantees | Guarantee | 0..* | — | — | No equivalent in BoB v3.4.0. |
| providedSections | ProvidedSections | 0..* | `product` | `route { from, to }` | Origin–destination scope of the product. |

---

## TravelRight

> Right to travel on a specific section.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| offerElementType (travelRight) | — | — | `product` | `fareId` | A travel right is implicit in the BoB fare product; `fareId` links to the applicable fare conditions. |
| ancillaries | Ancillary | 0..* | `reservation` | `ancillaryIds[]` | Ancillaries are listed as separate ancillary product IDs on the reservation. |
| allocations | Allocation | 1..* | `reservation` | `seatPreferences[]` | Seat/space allocations are expressed as seat preferences on the reservation. |

---

## Ancillary

> Optional ancillary service.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| ancillaryId | string | 0..1 | `ancillaryProduct` | `ancillaryId` | BoB ancillary product identifier from the product catalogue. |
| type | string | 0..1 | `ancillaryProduct` | `type` | BoB ancillary type code (e.g. `MEAL`, `BICYCLE`, `LUGGAGE`). |

---

## SpotAllocation

> Allocation to a specific physical spot.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| legId | string | 0..1 | — | — | BoB does not reference allocations by leg ID; allocation scope is implicit from the product/service journey. |
| startPlace | string | 0..1 | `seatPreference` | `from` | Origin stop for the allocated spot (if directional). |
| endPlace | string | 0..1 | `seatPreference` | `to` | Destination stop for the allocated spot (if directional). |
| typeOfSpot | string | 0..1 | `seatPreference` | `type` | Seat/berth type code (e.g. `WINDOW`, `AISLE`, `LOWER_BERTH`). |

---

## AssetAllocation

> Allocation to a named asset.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| (no additional properties) | — | — | `seatPreference` | `seatId` | BoB uses `seatId` to identify a named/specific allocated seat or space. |

---

## TravellingEntityReference

> Reference to a travelling entity.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| travellingEntityRef | integer | 1..1 | `traveller` | `categoryId` | BoB aggregates travellers by category; there is no per-traveller identifier within a reservation. |

---

## Price

> Monetary amount in a currency.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| currencyCode | string | 1..1 | `price` | `currency` | |
| amount | number | 1..1 | `price` | `amount` | BoB `price.amount` at product or reservation level. |
| vat | Vat | 0..* | — | — | BoB v3.4.0 does not return a structured VAT breakdown. |

---

## Vat

> VAT component of a price. **No equivalent in BoB Product API v3.4.0.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| amount / currencyCode / country / percentage | various | — | — | — | Not part of BoB reservation response. |

---

## ProvidedSections

> Journey section covered by an offer or element.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| startLegId | string | 0..1 | — | — | No leg-level identifier in BoB; scope expressed as route origin. |
| endLegId | string | 0..1 | — | — | No leg-level identifier in BoB; scope expressed as route destination. |
| startPlace | string | 0..1 | `route` | `from` | Origin station/stop code (e.g. UIC). |
| endPlace | string | 0..1 | `route` | `to` | Destination station/stop code (e.g. UIC). |
| tripPatternRef | string | 0..1 | — | — | No trip-pattern reference in BoB; the reservation is the trip context. |

---

## SummaryDetail

> Human-readable journey summary. **No direct equivalent in BoB Product API v3.4.0.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| geometry | string | 0..1 | — | — | Derivable from product route `from`/`to`. |
| temporal | string | 0..1 | — | — | Derivable from product validity dates/times. |
| conditions | string | 0..1 | `product` | `conditions[].description` | Product conditions carry after-sales and validity descriptions. |

---

## FareProduct

> Fare product reference.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| productRef | string | 0..1 | `product` | `fareId` | BoB `fareId` is the fare-level product reference. |

---

## Guarantee / RequiredInformation / Warning

> Stub types.

| EUDIT concept | BoB concept | BoB property | Notes |
|---|---|---|---|
| Guarantee | — | — | No equivalent in BoB Product API v3.4.0. |
| RequiredInformation | — | — | Passenger data requirements are defined in product configuration, not returned per element. |
| Warning | — | — | No `warnings[]` array on BoB reservation response. |
