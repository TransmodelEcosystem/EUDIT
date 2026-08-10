# Relationship between Business Use Cases and API Endpoints

This document explains how the **Business Use Cases (BUC-A through BUC-J)** relate to the **API endpoints** defined in the YAML files in this `deliverables` folder.

---

## Overview

The BUC files describe the complete business process of a journey, from inspiration to post-trip aftersales. The YAML files contain the concrete API specifications (OpenAPI 3.1.0) that technically support this process. At this point, **two of the seven phases** have an API specification; the remaining phases are still in preparation.

```
BUC-A  Inspire & Plan       ──►  1 search offers/search-offers.yaml  ✓
BUC-B  Shop & Price         ──►  2 lock offer/lock-offer.yaml         ✓ (partial)
BUC-C  Order & Book         ──►  (not yet available)
BUC-D  Pay                  ──►  (not yet available)
BUC-E  Ticketing            ──►  (not yet available)
BUC-F  Pre-Trip Aftersales  ──►  (not yet available)
BUC-J  Post-Trip Aftersales ──►  (not yet available)

Lookup tables (supporting BUC-A and BUC-B):
                             ──►  1 search offers/lookup.yaml         ✓
                             ──►  2 lock offer/lookup.yaml            ✓
```

---

## BUC-A — Plan your trip / Inspire & Plan

**File:** `wiki/use-cases/business-use-cases/BUC-A-inspire-and-plan.md`  
**Status:** In review — version 3

### Goal
The traveller defines selection criteria (origin, destination, time, passenger profile) and receives a list of matching offers with price, travel guarantees, and sales conditions.

### Corresponding API specification
**File:** `deliverables/1 search offers/search-offers.yaml`

| Method | Path | Mapped standard | Description |
|--------|------|-----------------|-------------|
| `POST` | `/offers` | OSDM 3.7.1 | Search offers based on trip pattern and passengers |
| `POST` | `/processes/search-offers/execute` | OMSA 0.1.0 | Same function, OMSA-style |
| `POST` | `/processes/search-offers/execution` | TOMP-API 2.0.0 | Same function, TOMP-style |

All three endpoints share the same request/response schema, making the API compatible with multiple existing interoperability standards simultaneously.

### How BUC-A shapes the endpoint structure

| BUC-A step | Technical translation in the endpoint |
|------------|---------------------------------------|
| Traveller provides selection criteria (origin, destination, time) | `tripPatterns[]` in `OfferRequest` — `TimedLeg`, `ContinuousLeg` or `TransferLeg` |
| Passenger profile: age, special needs, assisted travel | `travellers[]` — `Traveller`, `PassengerVehicle`, `Animal`, `Luggage` (discriminator `entityType`) |
| Special needs (PRM, wheelchair, etc.) | `travellers[].personalNeeds[]` — codes from the `/personal-needs` lookup |
| Filter by transport mode, class of use, media type | `filter` object — `SearchOfferFilter` |
| Result policy: number of results before/after requested departure, currency | `policy` object — `SearchOfferPolicy` |
| Retailer consolidates offers with price, guarantees and conditions | `SearchOfferDelivery.offers[]` — each `Offer` contains `elements[]`, `minimumPrice`, `guarantees`, `afterSalesFlexibility` |
| Customer selects a candidate offer | `Offer.offerId` is passed on to BUC-B (Lock Offer) |

---

## BUC-B — Shop and Price / Basket management

**File:** `wiki/use-cases/business-use-cases/BUC-B-shop-and-price.md`  
**Status:** In review — version 3

### Goal
The customer builds a shopping basket with selected offers and keeps each element up to date (price, validity, dependencies) until deciding to proceed to reservation and payment.

### Corresponding API specification
**File:** `deliverables/2 lock offer/lock-offer.yaml`

Locking an offer (offer hold / pre-reservation) is the technical counterpart of the step in BUC-B where the retailer temporarily secures the availability of an offer for the customer.

| Method | Path | Mapped standard | Description |
|--------|------|-----------------|-------------|
| `POST` | `/lock-offers` | OSDM 3.7.1 | Lock a selected offer |
| `POST` | `/processes/lock-offers/execute` | OMSA 0.1.0 | Same function, OMSA-style |
| `POST` | `/processes/select-offer/execution` | TOMP-API 2.0.0 | Same function, TOMP-style |
| `GET` | `/locked-offers/{lockedOfferId}` | OSDM 3.7.1 | Retrieve details of a locked offer |
| `GET` | `/collections/locked-offer-details/items` | OMSA 0.1.0 | Same function, OMSA-style |
| `GET` | `/collections/offer-details/items` | TOMP-API 2.0.0 | Same function, TOMP-style |

### How BUC-B shapes the endpoint structure

| BUC-B step | Technical translation in the endpoint |
|------------|---------------------------------------|
| Customer adds an offer to the basket | `LockOfferRequest.offerReference` — references `Offer.offerId` from BUC-A |
| Selection of a specific seat or berth | `allocations[]` — `AllocationSelection` with `allocationReference` |
| Selection of ancillary services (meal, luggage, upgrade) | `ancillaries[]` — `AncillarySelection` with `ancillaryReference` |
| Offer is temporarily held with a time limit | `LockOfferDelivery.lockedOfferId` + `expiryTime` |
| Track the status of the lock | `LockedOffer.status` — `locked`, `expired`, `cancelled`, `confirmed` |
| Non-fatal changes in availability or conditions | `LockOfferDelivery.warnings[]` |
| After-sales restricted to the retailer only | `LockOfferRequest.aftersalesByRetailerOnly` |
| Hypermedia navigation to the next step (confirm, release) | `LockOfferDelivery.links[]` |

> **Note:** The full basket management logic (adding, removing, repricing, dependency evaluation) as described in BUC-B falls outside the scope of the current lock-offer API. That API covers only the moment at which an offer is held after selection by the customer.

---

## Lookup tables (supporting BUC-A and BUC-B)

**Files:**
- `deliverables/1 search offers/lookup.yaml`
- `deliverables/2 lock offer/lookup.yaml`

Both lookup YAMLs are identical in structure and provide read-only access to operator-defined code lists. They support both BUC-A and BUC-B.

| Method | Path | Purpose in the BUCs |
|--------|------|----------------------|
| `GET` | `/personal-needs` | Valid values for `travellers[].personalNeeds[]` (BUC-A: PRM traveller, wheelchair user, etc.) |
| `GET` | `/collections/personal-needs/items` | Same, OGC Records API style |
| `GET` | `/ancillary-types` | Valid values for `Ancillary.type` (BUC-A/B: meal, luggage, upgrade, etc.) |
| `GET` | `/collections/ancillary-types/items` | Same, OGC Records API style |
| `GET` | `/entitlement-types` | Valid values for `EntitlementRight.entitlementType` (BUC-A: discount right, subscription, etc.) |
| `GET` | `/collections/entitlement-types/items` | Same, OGC Records API style |

Each endpoint is available in two patterns:
- **REST style** — simple GET on the resource path
- **OGC Records API style** — via `/collections/{name}/items`, for interoperability with geodata ecosystems

---

## Business Use Cases not yet covered

The following BUCs are described in the wiki but do not yet have a corresponding API specification in `deliverables`:

| BUC | Title | Expected API scope |
|-----|-------|--------------------|
| **BUC-C** | Order & Book | Reservation confirmation, collection of personal data, preliminary and final booking |
| **BUC-D** | Pay | Payment execution (retailer-side, distributor-side, shared, third-party PSP) |
| **BUC-E** | Ticketing & Fulfilment | Creation of travel rights, issuance of travel documents (PDF, QR, NFC, wallet) |
| **BUC-F** | Pre-Trip Aftersales | Cancellation, exchange, cross-sell, operator-initiated changes before travel |
| **BUC-J** | Post-Trip Aftersales | Vouchers, claims, no-show, urban transport lifecycle |

---

## Summary: coverage per phase

```
Phase           BUC     API status
──────────────────────────────────────────────────────────────
Inspire         BUC-A   ✓  search-offers.yaml  (3 endpoints)
Basket          BUC-B   ✓  lock-offer.yaml     (6 endpoints)
                        ✓  lookup.yaml         (6 endpoints, supporting)
Reservation     BUC-C   ○  not yet available
Payment         BUC-D   ○  not yet available
Fulfilment      BUC-E   ○  not yet available
Pre-trip        BUC-F   ○  not yet available
Post-trip       BUC-J   ○  not yet available
```

All existing API specifications follow **OpenAPI 3.1.0**, version `0.1.0-draft`, and are each available in three interoperability variants: **OSDM 3.7.1**, **OMSA 0.1.0**, and **TOMP-API 2.0.0**.
