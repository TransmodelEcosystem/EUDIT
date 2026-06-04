# Mapping: Search Offers → BoB Product API v3.4.0

Maps each EUDIT **Search Offers** concept to the corresponding concept/property in the **BoB Product API v3.4.0**.

- **Endpoint**: `POST /product`
- **EUDIT concept / Property** — as defined in `search-offers.yaml`
- **BoB concept** — the matching schema object in BoB Product API
- **BoB property** — the matching field name
- **Notes** — mapping remarks, gaps, or open questions

**Important scope note**: BoB operates at the distribution layer. It searches for purchasable *products*
by area/route/group, not by a specific trip pattern with individual service journeys. Travellers are
represented as *category codes* (e.g. ADULT, CHILD) rather than individual profiles. As a result,
many EUDIT concepts have no BoB equivalent, and the mapping is structural rather than semantic.

---

## OfferRequest

> Root request body for the offer search.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| tripPatterns | TripPattern | 1..* | `productFilter` | `route?` / `area?` / `group?` | BoB does not accept a trip pattern (timetabled legs). The closest equivalent is a spatial/route filter: `route` (origin–destination pair), `area` (geographic zone), or `group` (product group). |
| travellers | TravellingEntity | 1..* | `productFilter` | `travellers[{ categoryId, quantity }]` | Travellers are expressed as category codes with counts, not individual profiles. `Animal`, `PassengerVehicle`, `VehicleRack`, `Luggage` have no equivalent. |
| filter | SearchOfferFilter | 0..1 | `productFilter` | `fareIds[]`, `genericCategoryIds[]` | No equivalent for `modes`, `classOfUse`, `mediaTypes`, `requestedSections`. |
| policy | SearchOfferPolicy | 0..1 | — | — | No equivalent. BoB is synchronous; no pagination or currency control. |

---

## SearchOfferPolicy

> Pagination and currency preferences. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| numberOfResultsBefore | integer | 0..1 | — | — | Not supported. |
| numberOfResultsAfter | integer | 0..1 | — | — | Not supported. |
| currency | string | 0..1 | — | — | Currency is operator configuration, not a request parameter. |

---

## SearchOfferFilter

> Constraints on which offers to return.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| modes | string | 0..* | — | — | No equivalent. BoB products are not filtered by transport mode. |
| classOfUse | string | 0..* | — | — | No equivalent. |
| mediaTypes | string | 0..* | — | — | No equivalent. |
| requestedSections | RequestedSections | 0..* | — | — | No equivalent. |

---

## RequestedSections

> Targets offer search to a subset of legs. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | No equivalent. |

---

## TravellingEntity

> Base class.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | — | — | No per-traveller identifier in BoB; travellers are aggregated by category. |
| entityType | string | 1..1 | — | — | Discriminator only. `Animal`, `PassengerVehicle`, `Luggage` have no BoB equivalent. |
| entitlementRights | EntitlementRight | 0..* | `productFilter` | `discountCodes[]` | See **EntitlementRight**. |

---

## Traveller

> Human traveller.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| travellingEntityId | string | 1..1 | — | — | Not used; travellers aggregated by `categoryId`. |
| age | integer | 0..1 | — | — | Age band encoded in `categoryId` (e.g. a "CHILD" category has age bounds defined in `GET /productcat/traveller`). |
| assistant | boolean | 0..1 | — | — | No equivalent. |
| dateOfBirth | date | 0..1 | — | — | No equivalent. |
| externalReference | string | 0..1 | — | — | No equivalent. |
| personalNeeds | PersonalNeed | 0..* | — | — | No equivalent. |
| qualifyingCharacteristics | TravellerQualifyingCharacteristics | 0..1 | — | — | No equivalent; eligibility is encapsulated in category codes. |

---

## TravellerQualifyingCharacteristics

> Demographic eligibility data. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | Demographic eligibility is abstracted into traveller categories obtained via `GET /productcat/traveller`. |

---

## PersonalNeed

> Accessibility or personal need. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | — | — | No equivalent. |

---

## LicenseType

> Driving licence. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| type | string | 1..1 | — | — | No equivalent. |

---

## Animal

> Animal travelling alongside its owner. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | No equivalent. Pets may be represented as a traveller category if the operator defines one. |

---

## PassengerVehicle

> Traveller-owned vehicle. **No equivalent in BoB product search.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | Vehicle transport outside BoB scope at offer-search level. May appear as a category in some operator configurations. |

---

## VehicleRack

> Rack on a vehicle. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | No equivalent. |

---

## Luggage

> Bulky item. **No equivalent in BoB product search.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| (all) | — | — | — | — | May be represented as a traveller category if the operator defines one. No dimension/weight input. |

---

## EntitlementRight

> Credential qualifying for reduced fares.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| issuer | string | 0..1 | — | — | No issuer field; discount codes are opaque strings in BoB. |
| entitlementType | string | 1..1 | — | — | Encoded in `discountCodes[]` string value. |
| code | string | 0..1 | `productFilter` | `discountCodes[]` | The discount/promotional code is passed directly. |

---

## TripPattern

> Proposed sequence of legs. **No equivalent in BoB.**

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| tripPatternId | string | 0..1 | — | — | No equivalent. |
| legs | Leg | 1..* | `productFilter` | `route { from, to }` | At most an origin–destination pair; no leg-level detail. |

---

## TimedLeg / ContinuousLeg / TransferLeg

> All leg sub-types. **No equivalent in BoB.**

| EUDIT concept | BoB concept | Notes |
|---|---|---|
| TimedLeg | — | No equivalent. `serviceJourneyRef`, `operatingDate`, `LegBoard`, `LegAlight` not supported. |
| ContinuousLeg | — | No equivalent. |
| TransferLeg | — | No equivalent. |

---

## LegBoard / LegAlight / TrainNumber / StopPlace

> All stop/boarding concepts. **No equivalent in BoB.**

| EUDIT concept | BoB concept | Notes |
|---|---|---|
| LegBoard | — | No equivalent. |
| LegAlight | — | No equivalent. |
| TrainNumber | — | No equivalent. |
| StopPlace | — | No equivalent. |

---

## Place / StopPlaceInput / AddressInput / PointOfInterest / TopologicalPlace / GeoPosition

> Place hierarchy.

| EUDIT concept | BoB concept | BoB property | Notes |
|---|---|---|---|
| StopPlaceInput | `productFilter` | `route { from, to }` | Station codes (e.g. UIC) if supported. |
| AddressInput | — | — | No equivalent. |
| PointOfInterest | — | — | No equivalent. |
| TopologicalPlace | `productFilter` | `area` | BoB `area` is a zone/area code, not a GeoJSON polygon. |
| GeoPosition | — | — | No equivalent; BoB does not accept geo-coordinates in product search. |

---

## Offer

> Purchasable combination of offer elements.

| Property | Type | Mult. | BoB concept | BoB property | Notes |
|---|---|---|---|---|---|
| offerId | string | 1..1 | `product` | `productId` | BoB returns `product` objects rather than `offer` objects. |
| elements | OfferElement | 1..* | `product` | `fareIds[]`, `conditions[]` | No discrete offer-element hierarchy; product has fare IDs and conditions. |
| farePrice | FarePrice | 0..1 | `product` | `price { amount, currency }` | |
| travelDocument | TravelDocument | 0..1 | `product` | `mediaTypes[]` | BoB lists supported media types per product. |

---

## OfferElement / TravelRight / Ancillary / Reservation / CompositeOfferElement

> Offer element sub-types.

| EUDIT concept | BoB concept | BoB property | Notes |
|---|---|---|---|
| OfferElement | `fareProduct` | `fareId`, `name`, `conditions[]` | BoB fare products are the closest equivalent. |
| TravelRight | `fareProduct` | — | Right to travel implicit in product conditions. |
| Ancillary | `ancillaryProduct` | — | Some BoB implementations expose ancillary products. |
| Reservation | — | — | Not modelled in BoB product search. |
| CompositeOfferElement | `productBundle` | `products[]` | BoB supports product bundles in some implementations. |
| FarePrice | `price` | `amount`, `currency` | |
| TravelDocument | — | `mediaTypes[]` | Media type list at product level. |
| Guarantee | — | — | No equivalent. |
