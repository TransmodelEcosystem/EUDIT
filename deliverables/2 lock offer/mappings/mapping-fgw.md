# Mapping: Lock Offer → FerryGateway 1.3.1

Maps each EUDIT **Lock Offer** concept to the corresponding concept/property in **FerryGateway 1.3.1**.

- **EUDIT concept / Property** — as defined in `lock-offer.yaml`
- **FerryGateway concept** — the matching XML message / element in FerryGateway 1.3.1
- **FerryGateway element** — the matching element/attribute name
- **Notes** — mapping remarks, gaps, or open questions

**Important scope note**: FerryGateway 1.3.1 uses XML request/response messages (not REST/JSON).
Locking an offer (time-limited hold before payment) maps to the **`CreateReservationRequest`** /
**`CreateReservationResponse`** message flow. FerryGateway does not have a two-phase lock/confirm
split identical to OTI; a reservation in FerryGateway is already a committed hold and may require
payment immediately or within a configurable time window.

This mapping covers:

1. **`CreateReservationRequest`** — the closest equivalent to a lock-offer request
2. **`CreateReservationResponse`** — the closest equivalent to the lock-offer delivery / locked offer

---

## LockOfferRequest

> Root request body — selects an offer and optionally specifies allocations and ancillaries to include.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `CreateReservationRequest` | `QuoteId` / `PriceId` | The previously retrieved price-quote reference is passed back to identify the offer to book. |
| aftersalesByRetailerOnly | boolean | 0..1 | — | — | No equivalent; after-sales permissions are operator policy, not a request flag. |
| externalRef | string | 0..1 | `CreateReservationRequest` | `AgentReference` | Caller's own booking reference. |
| allocations | AllocationSelection | 0..* | `CreateReservationRequest` | `ServiceItems[]` | Seat/cabin/vehicle-deck allocation; FerryGateway uses service codes to select specific accommodation types. No direct spot-reference mechanism. |
| ancillaries | AncillarySelection | 0..* | `CreateReservationRequest` | `ServiceItems[]` | Optional services (meals, pets, etc.) passed as service code items. |

---

## AllocationSelection

> Selects a specific allocation offer element.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| allocationReference | string | 1..1 | `CreateReservationRequest` | `ServiceCode` | FerryGateway identifies accommodation by service code (e.g. cabin grade), not by a server-assigned allocation reference. |

---

## AncillarySelection

> Selects a specific ancillary offer element.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| ancillaryReference | string | 1..1 | `CreateReservationRequest` | `ServiceCode` | Same as allocation — ancillaries selected by service code. |

---

## LockOfferDelivery

> Server response to a lock-offer request — contains the locked-offer identifier and expiry.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `CreateReservationResponse` | `ReservationId` | FerryGateway assigns a `ReservationId` that identifies the hold. |
| expiryTime | dateTime | 1..1 | `CreateReservationResponse` | `ExpiryDateTime` | Time by which payment must be made before the reservation is released. |
| offerRef | string | 1..1 | `CreateReservationResponse` | `QuoteId` | Echo of the price-quote reference used in the request. |
| lockedOffer | LockedOffer | 0..1 | `CreateReservationResponse` | (inline details) | FerryGateway typically returns full reservation details in the same response. |
| warnings | Warning | 0..* | `CreateReservationResponse` | `Warnings[]` | Non-fatal conditions (e.g. accommodation changed, price adjusted). |
| links | Link | 0..* | — | — | No hypermedia links in FerryGateway XML. |

---

## LockedOffer

> Full detail of the time-limited hold.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `CreateReservationResponse` | `ReservationId` | |
| name | string | 0..1 | — | — | No equivalent; a human-readable name is not part of the reservation response. |
| summary | string | 0..1 | — | — | No equivalent. |
| matching | string (x-enum) | 0..1 | — | — | No equivalent; FerryGateway either fulfils the request or returns an error. |
| status | string (enum) | 1..1 | `CreateReservationResponse` | `ReservationStatus` | FerryGateway statuses: `Confirmed`, `Cancelled`, `AwaitingPayment`. Mapped to OTI `locked` / `expired` / `cancelled` / `confirmed`. |
| afterSalesFlexibility | string[] | 0..* | `CreateReservationResponse` | `TicketConditions` | Conditions describing refundability / exchangeability returned as coded values. |
| personalInformationRequired | boolean | 0..1 | — | — | Not a flag in FerryGateway; passenger details (name, passport) may be required depending on route/operator configuration. |
| externalRef | string | 0..1 | `CreateReservationResponse` | `AgentReference` | Echo of caller's reference. |
| elements | OfferElement | 1..* | `CreateReservationResponse` | `ReservationItems[]` | See offer-element mapping below. |
| minimumPrice | Price | 0..1 | `CreateReservationResponse` | `TotalPrice { Amount, Currency }` | |
| summaryDetails | SummaryDetail | 0..* | — | — | No equivalent. |
| providedSections | ProvidedSections | 0..* | `CreateReservationResponse` | `SailingId`, `RouteCode` | Sailing identity echoed in the response. |
| guarantees | Guarantee | 0..* | — | — | No equivalent in FerryGateway. |
| links | Link | 0..* | — | — | No hypermedia links. |

---

## OfferElement (base)

> Base class for all elements within the locked offer.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| offerElementId | string | 1..1 | `ReservationItem` | `ItemId` | |
| offerElementType | string | 1..1 | — | — | Discriminator only; not present in FerryGateway. |
| travellingEntities | string[] | 0..* | `ReservationItem` | `PassengerRef` / `VehicleRef` | FerryGateway links items to passengers/vehicles by reference. |
| matching | string (x-enum) | 0..1 | — | — | No equivalent. |
| requiredInformation | RequiredInformation | 0..1 | — | — | No equivalent at this stage; passenger data collected separately. |
| price | Price | 1..1 | `ReservationItem` | `Amount`, `Currency` | Per-item price. |
| fareProduct | string | 0..1 | `ReservationItem` | `ServiceCode` | Service or product code identifying the fare product. |
| guarantees | Guarantee | 0..* | — | — | No equivalent. |
| providedSections | ProvidedSections | 0..* | `ReservationItem` | `SailingId` | Sailing reference per item. |

---

## TravelRight

> Offer element conferring the right to travel on a section.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| (OfferElement properties) | — | — | `ReservationItem` | — | See OfferElement above. |
| ancillaries | Ancillary | 0..* | `ReservationItem` | `ServiceItems[]` | Included ancillaries returned as child service items. |
| allocations | SpotAllocation / AssetAllocation | 0..* | `ReservationItem` | `CabinGrade`, `DeckLevel` | Accommodation details returned per item; no individual seat reference at this level. |

---

## Ancillary

> Optional service element.

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| ancillaryId | string | 0..1 | `ServiceItem` | `ServiceCode` | |
| type | string (lookup) | 0..1 | `ServiceItem` | `ServiceCode` | The ancillary type (e.g. `meal`, `petTransport`) maps to a FerryGateway service code. |

---

## SpotAllocation

> Allocation to a specific spot (seat, berth, parking space).

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| legId | string | 0..1 | `ReservationItem` | `SailingId` | FerryGateway allocation is per sailing, not per NeTEx leg reference. |
| startPlace | string | 0..1 | — | — | Not applicable; boarding/alighting ports are at sailing level. |
| endPlace | string | 0..1 | — | — | Not applicable. |
| typeOfSpot | string (x-enum) | 0..1 | `AccommodationType` | `CabinGrade` / `DeckType` | FerryGateway uses cabin grade codes; exact `seat` / `berth` mapping depends on operator. |

---

## AssetAllocation

> Allocation to a named asset. **No direct equivalent in FerryGateway.**

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| (OfferElement properties) | — | — | — | — | Named asset allocations have no equivalent in FerryGateway 1.3.1. |

---

## Price

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| currencyCode | string | 1..1 | `Amount` | `Currency` | ISO 4217. |
| amount | number | 1..1 | `Amount` | `Value` | |
| vat | Vat | 0..* | — | — | No VAT breakdown in FerryGateway 1.3.1; price is typically inclusive. |

---

## ProvidedSections

| Property | Type | Mult. | FerryGateway concept | FerryGateway element | Notes |
|---|---|---|---|---|---|
| startLegId / endLegId | string | 0..1 | `ReservationItem` | `SailingId` | Single sailing per item in FerryGateway; no multi-leg section concept. |
| startPlace / endPlace | string | 0..1 | `CreateReservationResponse` | `RouteCode` (port parts) | Origin/destination ports in UN/LOCODE format. |
| tripPatternRef | string | 0..1 | — | — | No trip pattern concept in FerryGateway. |

---

## SummaryDetail / Guarantee / RequiredInformation / Warning

| EUDIT concept | FerryGateway concept | Notes |
|---|---|---|
| SummaryDetail | — | No equivalent; journey summary not included in FerryGateway reservation response. |
| Guarantee | — | No equivalent. |
| RequiredInformation | — | Personal data requirements communicated out-of-band or via operator configuration. |
| Warning | `CreateReservationResponse` `Warnings[]` | Non-fatal conditions returned as coded warning messages. |

---

## Link

| EUDIT concept | FerryGateway concept | Notes |
|---|---|---|
| Link | — | FerryGateway uses a request/response XML model; no hypermedia links. Follow-up operations (payment, cancellation) use dedicated message types (`ConfirmReservationRequest`, `CancelReservationRequest`). |
