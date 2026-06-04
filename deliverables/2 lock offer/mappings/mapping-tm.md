# Mapping: Lock Offer → Transmodel v6.2

This file maps each EUDIT **Lock Offer** concept to the **Transmodel v6.2** concept(s) it realises.
Unlike the other mapping files, this is not a standard-to-standard API mapping but a conceptual anchor:
it shows which Transmodel concepts underpin each EUDIT schema.

- **EUDIT concept** — as defined in `lock-offer.yaml`
- **Transmodel concept** — the TM v6.2 concept(s) realised
- **TM package** — the Transmodel part / package
- **Notes** — alignment remarks

---

## Request-side concepts

### LockOfferRequest

| EUDIT concept | Transmodel concept | TM package | Notes |
|---|---|---|---|
| LockOfferRequest | PRE-BOOKING REQUEST | Part 6 — Fare management | TM PRE-BOOKING REQUEST — the request to reserve a SALES OFFER PACKAGE instance before final purchase. Initiates the provisional-hold lifecycle. |

---

### LockOfferRequest properties

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| offerReference | SALES OFFER PACKAGE | Part 6 | Reference to the SALES OFFER PACKAGE instance to be locked. |
| aftersalesByRetailerOnly | SELLING CONDITION | Part 6 | Constrains after-sales operations to the originating retailer; relates to TM SELLING CONDITION and RESPONSIBILITY SET. |
| externalRef | CUSTOMER ACCOUNT | Part 6 | External identifier linking the request to the customer's account in the origin system. |
| allocations | SEAT RESERVATION (selection) | Part 6 | Requested seat or space allocation within the SALES OFFER PACKAGE; TM SEAT RESERVATION or PLACE ASSIGNMENT. |
| ancillaries | SUPPLEMENTARY PRODUCT (selection) | Part 6 | Selection of an optional ancillary product; TM SUPPLEMENTARY PRODUCT (add-on service). |

---

### AllocationSelection

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| AllocationSelection (class) | SEAT RESERVATION REQUEST | Part 6 | The act of selecting a specific physical spot; TM SEAT RESERVATION is the realised assignment. |
| allocationReference | VEHICLE JOURNEY EQUIPMENT PLACE ASSIGNMENT | Part 6 | Reference to the specific PLACE (seat, berth, bicycle space) being requested within the vehicle. |

---

### AncillarySelection

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| AncillarySelection (class) | SUPPLEMENTARY PRODUCT | Part 6 | The act of selecting an optional ancillary; TM SUPPLEMENTARY PRODUCT captures the chosen add-on. |
| ancillaryReference | SUPPLEMENTARY PRODUCT | Part 6 | Reference to the specific SUPPLEMENTARY PRODUCT instance selected. |

---

### LockedOfferDetailRequest

| EUDIT concept | Transmodel concept | TM package | Notes |
|---|---|---|---|
| LockedOfferDetailRequest | PRE-BOOKING REQUEST (detail retrieval) | Part 6 | A query against an existing PRE-BOOKING / PROVISIONAL BOOKING to retrieve its full content. |
| offerReference | PROVISIONAL BOOKING | Part 6 | Identifier of the PROVISIONAL BOOKING whose detail is being requested. |

---

## Response-side concepts

### LockOfferDelivery

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| LockOfferDelivery (class) | PRE-BOOKING / PROVISIONAL BOOKING | Part 6 | TM PROVISIONAL BOOKING — the time-limited hold on a SALES OFFER PACKAGE created in response to a PRE-BOOKING REQUEST. |
| lockedOfferId | PROVISIONAL BOOKING | Part 6 | Server-assigned identifier for the PROVISIONAL BOOKING instance. |
| expiryTime | PROVISIONAL BOOKING | Part 6 | Time limit after which the PROVISIONAL BOOKING lapses if not confirmed; TM validity window attribute. |
| offerRef | SALES OFFER PACKAGE | Part 6 | Back-reference to the source SALES OFFER PACKAGE that was locked. |
| lockedOffer | PROVISIONAL BOOKING (detail) | Part 6 | The full PROVISIONAL BOOKING content; see LockedOffer below. |
| warnings | — | — | Non-modelled in Transmodel core; operational message concept. |
| links | — | — | Hypermedia navigation; not a TM concept. |

---

### LockedOffer

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| LockedOffer (class) | PROVISIONAL BOOKING | Part 6 | TM PROVISIONAL BOOKING — the held instance of a SALES OFFER PACKAGE pending confirmation. |
| lockedOfferId | PROVISIONAL BOOKING | Part 6 | Identifier of the PROVISIONAL BOOKING. |
| name | SALES OFFER PACKAGE | Part 6 | Human-readable label derivable from the referenced SALES OFFER PACKAGE name. |
| summary | SALES OFFER PACKAGE | Part 6 | Summary text derivable from SALES OFFER PACKAGE description and trip context. |
| matching | — | — | Not modelled in TM; conformance-matching is an implementation concept. |
| status | BOOKING STATUS | Part 6 | TM BOOKING STATUS — the lifecycle state of the booking (PROVISIONAL, CONFIRMED, CANCELLED, EXPIRED). |
| afterSalesFlexibility | AFTER SALES CONDITION | Part 6 | TM AFTER SALES CONDITION — rules governing exchange (EXCHANGING) and refund (REFUNDING) of the booking. |
| personalInformationRequired | — | — | Not modelled in TM; data-collection requirement is an implementation concern. |
| externalRef | CUSTOMER ACCOUNT | Part 6 | External identifier for the customer's account in the origin system. |
| elements | SALES OFFER PACKAGE ELEMENT | Part 6 | Components of the locked SALES OFFER PACKAGE; see OfferElement below. |
| minimumPrice | FARE PRICE | Part 6 | TM FARE PRICE — the monetary value assigned to the locked offer. |
| summaryDetails | TRIP PATTERN | Part 3 | Journey summary derivable from the TRIP PATTERN and LEGs covered by the offer. |
| providedSections | SECTION | Part 2 | TM SECTION — the subset of the journey covered by this offer or element. |
| guarantees | GUARANTEE | Part 6 | TM GUARANTEE — a contractual commitment (connection guarantee, seat guarantee) associated with the booking. |
| links | — | — | Hypermedia navigation; not a TM concept. |

---

### OfferElement

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| OfferElement (class) | SALES OFFER PACKAGE ELEMENT | Part 6 | TM SALES OFFER PACKAGE ELEMENT — a component within a SALES OFFER PACKAGE (§6.5.2). |
| offerElementId | SALES OFFER PACKAGE ELEMENT | Part 6 | Server-assigned identifier for the element. |
| offerElementType | (discriminator) | — | Discriminator over TM sub-types (ACCESS RIGHT ASSIGNMENT, SUPPLEMENTARY PRODUCT, SEAT RESERVATION). |
| travellingEntities | TRAVELLING ENTITY | Part 6 | The TRAVELLING ENTITYs to whom this element applies. |
| matching | — | — | Not modelled in TM. |
| requiredInformation | — | — | Data-collection requirement; not a TM core concept. |
| price | FARE PRICE | Part 6 | TM FARE PRICE — the monetary value of the element. |
| fareProduct | FARE PRODUCT | Part 6 | TM FARE PRODUCT — the immutable element in the fare structure that defines the access right (§6.5.1). |
| guarantees | GUARANTEE | Part 6 | TM GUARANTEE — contractual commitment associated with the element. |
| providedSections | SECTION | Part 2 | Journey section covered by this element. |

---

### TravelRight

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| TravelRight (class) | ACCESS RIGHT ASSIGNMENT | Part 6 | TM ACCESS RIGHT ASSIGNMENT — assigns a right to travel to a specific passenger on a specific leg or zone (§6.5.3). |
| ancillaries | SUPPLEMENTARY PRODUCT | Part 6 | Optional add-on services included with or adjacent to the travel right. |
| allocations | SEAT RESERVATION | Part 6 | Seat or space reservations associated with the travel right. |

---

### Ancillary

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Ancillary (class) | SUPPLEMENTARY PRODUCT | Part 6 | TM SUPPLEMENTARY PRODUCT — an optional add-on service (meal, bicycle transport, lounge access) (§6.5.4). |
| ancillaryId | SUPPLEMENTARY PRODUCT | Part 6 | Identifier of the ancillary SUPPLEMENTARY PRODUCT instance. |
| type | SUPPLEMENTARY PRODUCT TYPE | Part 6 | Category of the ancillary (meal, bicycle, luggage, lounge, etc.). |

---

### Allocation (response)

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Allocation (class) | SEAT RESERVATION | Part 6 | TM SEAT RESERVATION — a confirmed allocation of a specific space within a vehicle. |

---

### SpotAllocation

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| SpotAllocation (class) | SEAT RESERVATION | Part 6 | TM SEAT RESERVATION for a specific physical spot (seat, berth, bicycle rack). |
| legId | SERVICE JOURNEY | Part 3 | The SERVICE JOURNEY (leg) on which the spot is allocated. |
| startPlace | SCHEDULED STOP POINT | Part 2 | Boarding stop for the allocation scope. |
| endPlace | SCHEDULED STOP POINT | Part 2 | Alighting stop for the allocation scope. |
| typeOfSpot | VEHICLE EQUIPMENT PLACE | Part 1 | TM VEHICLE EQUIPMENT PLACE — the type of physical location within the vehicle (seat, berth, cycle hook). |

---

### AssetAllocation

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| AssetAllocation (class) | VEHICLE JOURNEY EQUIPMENT PLACE ASSIGNMENT | Part 6 | TM VEHICLE JOURNEY EQUIPMENT PLACE ASSIGNMENT — assignment of a named asset to a specific VEHICLE JOURNEY, extending SEAT RESERVATION to cover non-seat spaces (bicycle storage, car-loader bay). |

---

### TravellingEntityReference

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| travellingEntityRef | TRAVELLING ENTITY | Part 6 | Reference to a TM TRAVELLING ENTITY (passenger, animal, vehicle, luggage item) associated with an offer element. |

---

### Price

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Price (class) | FARE PRICE | Part 6 | TM FARE PRICE — monetary value expressed in a currency (§6.4.5). |
| currencyCode | CURRENCY TYPE | Part 6 | ISO 4217 currency; TM CURRENCY TYPE. |
| amount | FARE PRICE | Part 6 | Numeric monetary amount. |
| vat | FARE PRICE (VAT breakdown) | Part 6 | TM FARE PRICE may include VAT-related attributes; a structured VAT breakdown is an extension of the base TM model. |

---

### Vat

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Vat (class) | FARE PRICE (VAT component) | Part 6 | Tax component of a FARE PRICE; TM v6.2 models VAT as an attribute group on FARE PRICE. |
| amount | FARE PRICE | Part 6 | VAT amount. |
| currencyCode | CURRENCY TYPE | Part 6 | Currency of the VAT amount. |
| country | — | Part 6 | Country of VAT jurisdiction; not explicitly modelled in TM core. |
| percentage | FARE PRICE | Part 6 | VAT rate percentage. |

---

### ProvidedSections

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| ProvidedSections (class) | SECTION | Part 2 | TM SECTION — a defined part of a route between two points, scoping the offer element. |
| startLegId | SERVICE JOURNEY | Part 3 | The first SERVICE JOURNEY (leg) in the covered section. |
| endLegId | SERVICE JOURNEY | Part 3 | The last SERVICE JOURNEY (leg) in the covered section. |
| startPlace | SCHEDULED STOP POINT / PLACE | Part 2 | TM SCHEDULED STOP POINT or PLACE — origin of the covered section. |
| endPlace | SCHEDULED STOP POINT / PLACE | Part 2 | TM SCHEDULED STOP POINT or PLACE — destination of the covered section. |
| tripPatternRef | TRIP PATTERN | Part 3 | Reference to the TM TRIP PATTERN that this section belongs to. |

---

### SummaryDetail

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| SummaryDetail (class) | — | — | No direct TM equivalent; human-readable summary derivable from TRIP PATTERN + FARE PRODUCT attributes. |
| geometry | SECTION / ROUTE | Part 2 | Route geometry derivable from TM SECTION or ROUTE points. |
| temporal | CALL (departure/arrival) | Part 3 | Temporal summary derivable from TM CALL departure/arrival times at origin/destination. |
| conditions | AFTER SALES CONDITION | Part 6 | Human-readable condition text derivable from TM AFTER SALES CONDITION descriptions. |

---

### FareProduct

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| FareProduct (class) | FARE PRODUCT | Part 6 | TM FARE PRODUCT — an immutable element in a fare structure representing a type of access right (§6.5.1). |
| productRef | FARE PRODUCT | Part 6 | Reference to the specific FARE PRODUCT included in this offer element. |

---

### Guarantee

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Guarantee (class) | GUARANTEE | Part 6 | TM GUARANTEE — a contractual commitment associated with an offer element (connection guarantee, seat guarantee, price guarantee). |

---

### RequiredInformation

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| RequiredInformation (class) | — | — | No dedicated TM concept; relates to TRANSPORT CUSTOMER and CUSTOMER ACCOUNT data requirements. |

---

### Warning

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Warning (class) | — | — | Non-modelled in Transmodel core; an operational/informational message concept outside TM scope. |

---

### Link

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Link (class) | — | — | Hypermedia navigation concept; not part of Transmodel data model. |
