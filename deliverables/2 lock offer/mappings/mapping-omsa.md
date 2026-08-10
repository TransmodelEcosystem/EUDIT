# Mapping: Lock Offer → OMSA 0.1.0

Maps each EUDIT **Lock Offer** concept to the corresponding concept/property in **OMSA 0.1.0**.

- **Endpoint 1**: `POST /processes/lock-offers/execute`
- **Endpoint 2**: `POST /processes/locked-offer-details/execute`
- **EUDIT concept / Property** — as defined in `lock-offer.yaml`
- **OMSA concept** — the matching schema object in OMSA 0.1.0
- **OMSA property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

OMSA follows the OGC API Processes pattern: both endpoints use a `POST .../execute` call with a typed input object, returning a synchronous result. OMSA 0.1.0 does not define a dedicated *lock-offer* process; the closest equivalent is creating a **provisional booking** (`bookingInput` / `bookingResult`) from a previously returned offer. Key differences compared to OSDM are noted below.

---

## LockOfferRequest

> Root request body for the lock operation.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `bookingInput` | `offerId` | The OMSA `bookingInput.offerId` references the offer to be booked/locked. |
| aftersalesByRetailerOnly | boolean | 0..1 | — | — | No equivalent in OMSA 0.1.0. After-sales channel restriction is not a parameter in the OMSA booking input. |
| externalRef | string | 0..1 | `bookingInput` | `externalId` | Caller-assigned reference for correlation. |
| allocations | AllocationSelection | 0..* | `bookingInput` | `selectedOptions[].optionId` | OMSA represents seat or space selection as generic `selectedOptions[]` within the booking input; no separate allocation type. |
| ancillaries | AncillarySelection | 0..* | `bookingInput` | `selectedOptions[].optionId` | Same `selectedOptions[]` pattern; the `optionId` identifies whether the selected option is a seat reservation or ancillary. |

---

## AllocationSelection

> Selection of a specific allocation offer element.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| allocationReference | string | 1..1 | `bookingInput` | `selectedOptions[].optionId` | The `allocationReference` maps to an `optionId` from the preceding offer's `options[]` array. |

---

## AncillarySelection

> Selection of a specific ancillary offer element.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| ancillaryReference | string | 1..1 | `bookingInput` | `selectedOptions[].optionId` | Same pattern as allocation; `ancillaryReference` maps to an `optionId`. |

---

## LockedOfferDetailRequest

> Request to retrieve the full detail of a locked offer.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| offerReference | string | 1..1 | `bookingDetailInput` | `bookingId` | The `offerReference` (which is the `lockedOfferId`) maps to the OMSA `bookingId` supplied in the process input to retrieve booking detail. |

---

## LockOfferDelivery

> Server response to the lock-offer request.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `bookingResult` | `bookingId` | Server-assigned booking identifier. |
| expiryTime | dateTime | 1..1 | `bookingResult` | `validUntil` | Date-time by which the booking must be confirmed or it lapses. |
| offerRef | string | 1..1 | `bookingResult` | `offerId` | Echo of the source offer identifier. |
| lockedOffer | LockedOffer | 0..1 | `bookingResult` | (full `bookingResult` body) | OMSA typically returns the full booking result inline in the process execution response. |
| warnings | Warning | 0..* | `bookingResult` | `warnings[]` | OMSA may return a `warnings[]` array for non-fatal conditions (e.g. availability change). |
| links | Link | 0..* | — | — | No hypermedia links in the OMSA process result body; next steps are standard process endpoints. |

---

## LockedOffer

> The time-limited held offer with full detail.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| lockedOfferId | string | 1..1 | `bookingResult` | `bookingId` | |
| name | string | 0..1 | — | — | No name field on OMSA booking result; derivable from offer description. |
| summary | string | 0..1 | `bookingResult` | `description` | OMSA `description` is the closest equivalent to a human-readable summary. |
| matching | string | 0..1 | — | — | No equivalent; OMSA booking always reflects the selected offer exactly. |
| status | string | 1..1 | `bookingResult` | `state` | OMSA `state` values: `PENDING` (= locked), `CONFIRMED`, `CANCELLED`. EUDIT adds `expired`. |
| afterSalesFlexibility | string | 0..* | `bookingResult` | `conditions[].type` | OMSA conditions array describes after-sales rules; types include `REFUNDABLE`, `EXCHANGEABLE`. |
| personalInformationRequired | boolean | 0..1 | `bookingResult` | `personalDataRequired` | OMSA flag indicating whether passenger personal data must be supplied before confirmation. |
| externalRef | string | 0..1 | `bookingResult` | `externalId` | Echo of the caller's external reference. |
| elements | OfferElement | 1..* | `bookingResult` | `legs[].pricing[]` | OMSA booking legs carry pricing per leg, similar to the offer-search response structure. |
| minimumPrice | Price | 0..1 | `bookingResult` | `totalPrice { amount, currencyCode }` | |
| summaryDetails | SummaryDetail | 0..* | — | — | No equivalent; summary is derivable from the `legs[]` data. |
| providedSections | ProvidedSections | 0..* | `bookingResult` | `legs[]` (start/end stop references) | OMSA `legs[]` implicitly define the covered sections via their origin/destination references. |
| guarantees | Guarantee | 0..* | — | — | No structured guarantee in OMSA 0.1.0 booking result. |
| links | Link | 0..* | `bookingResult` | `links[]` | OMSA process results may include OGC-style `links[]` for follow-up actions. |

---

## OfferElement

> Base class for purchasable elements within the locked offer.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| offerElementId | string | 1..1 | `leg` | `id` | OMSA does not return discrete offer-element IDs; the closest anchor is the leg `id`. |
| offerElementType | string | 1..1 | — | — | Discriminator only; OMSA does not use a typed element hierarchy. |
| travellingEntities | TravellingEntityReference | 0..* | `traveller` | `id` | OMSA booking associates travellers via the `travellers[]` array at booking level, not per element. |
| matching | string | 0..1 | — | — | No equivalent. |
| requiredInformation | RequiredInformation | 0..1 | `bookingResult` | `requiredPassengerData[]` | OMSA lists required passenger data fields at booking level. |
| price | Price | 0..1 | `leg` | `pricing.parts[{ amount, currencyCode }]` | |
| fareProduct | FareProduct | 0..1 | `leg` | `id` | Leg identifier serves as an implicit product reference in OMSA. |
| guarantees | Guarantee | 0..* | — | — | No equivalent. |
| providedSections | ProvidedSections | 0..* | `leg` | `from`, `to` | Origin/destination stop references of the leg. |

---

## TravelRight

> Right to travel on a specific section.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| offerElementType (travelRight) | — | — | `leg` | `pricing[]` | In OMSA a travel right is implicit per leg; pricing is attached to the leg object. |
| ancillaries | Ancillary | 0..* | — | — | Ancillaries are not nested under legs in OMSA; they are separate booking line items if present. |
| allocations | Allocation | 1..* | `leg` | `assetType` | OMSA `assetType` describes the type of asset allocated (seat, berth, etc.). |

---

## Ancillary

> Optional ancillary service.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| ancillaryId | string | 0..1 | — | — | No discrete ancillary ID in OMSA booking result. |
| type | string | 0..1 | `leg` | `assetType.subType` | OMSA `assetType.subType` can encode ancillary service categories in some implementations. |

---

## SpotAllocation

> Allocation to a specific physical spot.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| legId | string | 0..1 | `leg` | `id` | OMSA spot allocation is tied to a specific leg. |
| startPlace | string | 0..1 | `leg` | `from.stopReference.id` | |
| endPlace | string | 0..1 | `leg` | `to.stopReference.id` | |
| typeOfSpot | string | 0..1 | `leg` | `assetType.assetClass` | OMSA `assetClass` (e.g. `SEAT`, `BERTH`). |

---

## AssetAllocation

> Allocation to a named asset.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| (no additional properties) | — | — | `leg` | `assetType` | OMSA models all allocations via `assetType`; a named asset maps to `assetType.assetClass` with an asset descriptor. |

---

## TravellingEntityReference

> Reference to a travelling entity.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| travellingEntityRef | integer | 1..1 | `traveller` | `id` | OMSA `traveller.id` is the per-traveller correlation identifier within the booking. |

---

## Price

> Monetary amount in a currency.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| currencyCode | string | 1..1 | `pricing` | `currencyCode` | |
| amount | number | 1..1 | `pricing` | `amount` | OMSA `pricing.parts[].amount` (individual fare element) or `totalPrice.amount` (booking total). |
| vat | Vat | 0..* | — | — | OMSA 0.1.0 does not return a structured VAT breakdown. |

---

## Vat

> VAT component of a price. **No equivalent in OMSA 0.1.0.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| amount / currencyCode / country / percentage | various | — | — | — | Not part of OMSA booking response. |

---

## ProvidedSections

> Journey section covered by an offer or element.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| startLegId | string | 0..1 | `leg` | `id` (first leg) | |
| endLegId | string | 0..1 | `leg` | `id` (last leg) | |
| startPlace | string | 0..1 | `leg` | `from.stopReference.id` | |
| endPlace | string | 0..1 | `leg` | `to.stopReference.id` | |
| tripPatternRef | string | 0..1 | — | — | No trip-pattern reference in OMSA booking; the booking itself is the trip context. |

---

## SummaryDetail

> Human-readable journey summary. **No direct equivalent in OMSA 0.1.0.**

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| geometry | string | 0..1 | — | — | Derivable from `legs[].from`/`to` place references. |
| temporal | string | 0..1 | — | — | Derivable from `legs[].startTime`/`endTime`. |
| conditions | string | 0..1 | `bookingResult` | `conditions[].description` | OMSA condition descriptions are the closest equivalent. |

---

## FareProduct

> Fare product reference.

| Property | Type | Mult. | OMSA concept | OMSA property | Notes |
|---|---|---|---|---|---|
| productRef | string | 0..1 | `leg` | `id` | OMSA does not have an explicit fare product reference; the leg ID serves as the implicit product anchor. |

---

## Guarantee / RequiredInformation / Warning

> Stub types.

| EUDIT concept | OMSA concept | OMSA property | Notes |
|---|---|---|---|
| Guarantee | — | — | No structured guarantee in OMSA 0.1.0. |
| RequiredInformation | `bookingResult` | `requiredPassengerData[]` | OMSA lists required passenger data fields at booking level. |
| Warning | `bookingResult` | `warnings[]` | OMSA `warnings[]` array carries non-fatal booking warnings. |
