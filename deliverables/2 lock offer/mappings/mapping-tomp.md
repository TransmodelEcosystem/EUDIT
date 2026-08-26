# Mapping: Lock Offer → TOMP-API 2.0.0

Maps each EUDIT **Lock Offer** concept to the corresponding concept/property in **TOMP-API 2.0.0**.

- **Endpoint 1**: `POST /processes/select-offer/execution`
- **Endpoint 2**: `GET /bookings/{bookingId}` (or `POST /processes/locked-offer-details/execution`)
- **EUDIT concept / Property** — as defined in `lock-offer.yaml`
- **TOMP concept** — the matching schema object in TOMP-API
- **TOMP property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

In TOMP-API 2.0.0 a "lock" corresponds to creating a **booking** in state `PENDING`. The client selects a leg/option and the operator holds it before confirmation. Seat and ancillary selections are expressed through `legs[].assetType` and related sub-objects. TOMP has no dedicated "lock offer" process; the closest lifecycle event is `POST /legs/{legId}/events` with `eventType: PREPARE` or `POST /bookings` returning `state: PENDING`.

---

## LockOfferRequest

> Root request body for the lock operation.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `booking` | `id` (of the offer/leg being locked) | TOMP identifies the offer as a leg reference. The `offerReference` is passed as the leg or offer ID in the booking body. |
| aftersalesByRetailerOnly | boolean | 0..1 | — | — | No equivalent in TOMP 2.0.0. After-sales channel control is not a booking input parameter. |
| externalRef | string | 0..1 | `booking` | `customerReference` | Caller-assigned external reference carried on the booking. |
| allocations | AllocationSelection | 0..* | `leg` | `assetType` (selected seat/berth) | Seat/space selection supplied as `assetType` on the relevant leg. |
| ancillaries | AncillarySelection | 0..* | `leg` | `subAssets[]` | Ancillary services expressed as sub-assets on the leg (e.g. meal, bicycle). |

---

## AllocationSelection

> Selection of a specific allocation offer element.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| allocationReference | string | 1..1 | `leg` | `assetType.assetClass` / `assetType.assetSubClass` | The allocation reference maps to the asset type descriptor that identifies the selected spot. |

---

## AncillarySelection

> Selection of a specific ancillary offer element.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| ancillaryReference | string | 1..1 | `leg` | `subAssets[].assetType.assetClass` | TOMP sub-assets on a leg carry ancillary service references; the `ancillaryReference` maps to a sub-asset asset class. |

---

## LockedOfferDetailRequest

> Request to retrieve the full detail of a locked offer.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `booking` | `id` | TOMP retrieves booking detail via `GET /bookings/{id}`; the `offerReference` (= `lockedOfferId`) maps to the TOMP booking `id`. |

---

## LockOfferDelivery

> Server response to the lock-offer request.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `booking` | `id` | TOMP server-assigned booking identifier returned in `POST /bookings` response. |
| expiryTime | dateTime | 1..1 | `booking` | `expiresBefore` | TOMP `expiresBefore` (ISO 8601) — the time by which the booking must be confirmed or it expires. |
| offerRef | string | 1..1 | `booking` | `legs[].id` | Echo of the source offer/leg identifier. |
| lockedOffer | LockedOffer | 0..1 | `booking` | (full booking body) | TOMP returns the full booking inline; `lockedOffer` corresponds to the booking object itself. |
| warnings | Warning | 0..* | — | — | TOMP 2.0.0 does not define a `warnings[]` array on the booking response; non-fatal conditions may appear in operator-specific extensions. |
| links | Link | 0..* | `booking` | `links[]` | TOMP booking objects carry HATEOAS `links[]` for follow-up actions (e.g. confirm, cancel). |

---

## LockedOffer

> The time-limited held offer with full detail.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `booking` | `id` | |
| name | string | 0..1 | — | — | No name field on TOMP booking; derivable from leg descriptions. |
| summary | string | 0..1 | `booking` | `legs[0].departureTime` + `legs[-1].arrivalTime` | TOMP does not return a free-text summary; a summary can be constructed from leg origin/destination and times. |
| matching | string | 0..1 | — | — | No equivalent. |
| status | string | 1..1 | `booking` | `state` | TOMP `state` values: `PENDING` (= locked), `CONFIRMED`, `CANCELLED`, `EXPIRED`. Maps directly. |
| afterSalesFlexibility | string | 0..* | `booking` | `conditions[].conditionType` | TOMP condition types include `CONDITIONAL_REFUND`, `NO_REFUND`, `EXCHANGE`. |
| personalInformationRequired | boolean | 0..1 | — | — | No equivalent Boolean flag. TOMP uses required customer-data schemas in the booking flow but does not expose a dedicated flag. |
| externalRef | string | 0..1 | `booking` | `customerReference` | Echo of the caller's external reference. |
| elements | OfferElement | 1..* | `booking` | `legs[]` | Each TOMP leg corresponds to a travel-right or ancillary offer element. |
| minimumPrice | Price | 0..1 | `booking` | `pricing.estimated` / `pricing.parts[]` | TOMP booking pricing is expressed per leg; an aggregate must be computed by the client. |
| summaryDetails | SummaryDetail | 0..* | — | — | No equivalent; derivable from `legs[]` departure/arrival times and stop references. |
| providedSections | ProvidedSections | 0..* | `booking` | `legs[]` (from/to stop references) | TOMP legs implicitly define covered sections via their `from` and `to` place references. |
| guarantees | Guarantee | 0..* | — | — | No structured guarantee concept in TOMP 2.0.0 booking. |
| links | Link | 0..* | `booking` | `links[]` | TOMP HATEOAS links for booking actions. |

---

## OfferElement

> Base class for purchasable elements within the locked offer.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| offerElementId | string | 1..1 | `leg` | `id` | TOMP `leg.id` is the closest equivalent to an offer-element identifier. |
| offerElementType | string | 1..1 | — | — | Discriminator only; TOMP does not use a typed element hierarchy at this level. |
| travellingEntities | TravellingEntityReference | 0..* | `booking` | `customers[]` | TOMP associates travellers at booking level via `customers[]`, not per leg element. |
| matching | string | 0..1 | — | — | No equivalent. |
| requiredInformation | RequiredInformation | 0..1 | `booking` | `requiredUserInfo` | TOMP booking may specify required user-info fields; see operator configuration. |
| price | Price | 0..1 | `leg` | `pricing.parts[{ amount, currencyCode }]` | |
| fareProduct | FareProduct | 0..1 | `leg` | `id` | Leg ID serves as implicit product reference. |
| guarantees | Guarantee | 0..* | — | — | No equivalent in TOMP 2.0.0. |
| providedSections | ProvidedSections | 0..* | `leg` | `from`, `to` | Origin and destination stop references of the leg. |

---

## TravelRight

> Right to travel on a specific section.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| offerElementType (travelRight) | — | — | `leg` | `pricing[]` | A travel right is implicit per TOMP leg; pricing is attached to the leg object. |
| ancillaries | Ancillary | 0..* | `leg` | `subAssets[]` | Ancillary services are modelled as sub-assets of the leg in TOMP. |
| allocations | Allocation | 1..* | `leg` | `assetType` | TOMP `assetType` describes the allocated asset (seat, berth, bicycle space, etc.). |

---

## Ancillary

> Optional ancillary service.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| ancillaryId | string | 0..1 | `leg.subAssets[]` | `id` | TOMP sub-asset `id` identifies the ancillary item. |
| type | string | 0..1 | `leg.subAssets[].assetType` | `assetClass` | TOMP `assetClass` encodes the ancillary category (e.g. `MEAL`, `BICYCLE_STORAGE`). |

---

## SpotAllocation

> Allocation to a specific physical spot.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| legId | string | 0..1 | `leg` | `id` | TOMP spot allocation is tied to a specific leg by `id`. |
| startPlace | string | 0..1 | `leg` | `from.stopReference.id` | |
| endPlace | string | 0..1 | `leg` | `to.stopReference.id` | |
| typeOfSpot | string | 0..1 | `leg.assetType` | `assetClass` | TOMP `assetClass` (e.g. `SEAT`, `BERTH`, `BICYCLE_STORAGE`). |

---

## AssetAllocation

> Allocation to a named asset.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| (no additional properties) | — | — | `leg` | `assetType` | TOMP models all allocations via `assetType`; a named asset maps to `assetType.assetClass` with an asset descriptor in `assetType.assetSubClass`. |

---

## TravellingEntityReference

> Reference to a travelling entity.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| travellingEntityRef | integer | 1..1 | `customer` | `id` | TOMP `customers[].id` is the per-traveller correlation identifier within the booking. |

---

## Price

> Monetary amount in a currency.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| currencyCode | string | 1..1 | `pricing` | `currencyCode` | |
| amount | number | 1..1 | `pricing` | `amount` | TOMP `pricing.parts[].amount` (per element) or booking-level total. |
| vat | Vat | 0..* | — | — | TOMP 2.0.0 does not return a structured VAT breakdown. |

---

## Vat

> VAT component of a price. **No equivalent in TOMP-API 2.0.0.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| amount / currencyCode / country / percentage | various | — | — | — | Not part of TOMP booking response. |

---

## ProvidedSections

> Journey section covered by an offer or element.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| startLegId | string | 0..1 | `leg` | `id` (first leg) | |
| endLegId | string | 0..1 | `leg` | `id` (last leg) | |
| startPlace | string | 0..1 | `leg` | `from.stopReference.id` | |
| endPlace | string | 0..1 | `leg` | `to.stopReference.id` | |
| tripPatternRef | string | 0..1 | — | — | No trip-pattern reference in TOMP booking; the booking ID is the trip context. |

---

## SummaryDetail

> Human-readable journey summary. **No direct equivalent in TOMP-API 2.0.0.**

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| geometry | string | 0..1 | — | — | Derivable from `legs[].from`/`to` place references. |
| temporal | string | 0..1 | — | — | Derivable from `legs[].departureTime`/`arrivalTime`. |
| conditions | string | 0..1 | `booking` | `conditions[].description` | TOMP condition descriptions are the closest equivalent. |

---

## FareProduct

> Fare product reference.

| Property | Type | Mult. | TOMP concept | TOMP property | Notes |
|---|---|---|---|---|---|
| productRef | string | 0..1 | `leg` | `id` | TOMP does not have an explicit fare product reference; the leg ID serves as the implicit product anchor. |

---

## Guarantee / RequiredInformation / Warning

> Stub types.

| EUDIT concept | TOMP concept | TOMP property | Notes |
|---|---|---|---|
| Guarantee | — | — | No structured guarantee concept in TOMP 2.0.0. |
| RequiredInformation | `booking` | `requiredUserInfo` | TOMP may specify required user-info fields via operator configuration. |
| Warning | — | — | No `warnings[]` array on TOMP booking response; non-fatal conditions are operator-specific. |
