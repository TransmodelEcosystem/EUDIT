# Lock Offer — Test Cases for Response Bodies

This document describes the test cases for the `200 OK` response bodies of the lock-offer
endpoints of the *OTI Lock Offer* API.

The two response schemas covered are:

- **`LockOfferDelivery`** — returned by `POST /lock-offers`, `POST /processes/lock-offers/execute`,
  and `POST /processes/lock-offers/execution`.
- **`LockedOffer`** — returned by `GET /locked-offers/{lockedOfferId}`,
  `GET /collections/locked-offer-details/items/{lockedOfferId}`, and the two POST detail endpoints.

The numbering mirrors the request body test cases so that each request TC-NNN has a matching
response TC-NNN representing the expected server reply.

The test cases are organised in two sections:

1. **Generic test cases (TC-001 – TC-005)** — applicable to all three `lock-offers` endpoints.
2. **Standard-specific test cases**:
   - **TC-006**: OSDM 3.7.1 (`POST /lock-offers`) — full locked offer inline
   - **TC-007**: OMSA 0.1.0 (`POST /processes/lock-offers/execute`)
   - **TC-008**: TOMP-API 2.0.0 (`POST /processes/lock-offers/execution`)
3. **Locked offer detail (TC-009)** — `LockedOffer` returned by a detail endpoint.

---

## Generic Test Cases (`LockOfferDelivery`)

---

### TC-001 — Minimal delivery (no inline locked offer)

**Purpose:** verify that the server returns the minimum required `LockOfferDelivery` fields —
`lockedOfferId`, `expiryTime`, `offerRef` — without embedding the full `LockedOffer` inline.
Clients must fetch the detail separately via the locked-offer-detail endpoint.

**Corresponds to request:** TC-001 (minimal lock)

**Scenario:**
The server acknowledges the lock request and returns a `lockedOfferId`, the time at which the
hold expires, and the source `offerRef`. No inline `lockedOffer` object is included. Two
hypermedia links (`confirm` and `self`) guide the client's next steps.

**Key characteristics:**
- `lockedOfferId`, `expiryTime`, `offerRef` — all required fields present
- No `lockedOffer` embedded
- `warnings: []`
- 2 links: `confirm` (POST) and `self` (GET)

**Example file:** `TC-001-minimal-delivery.json`

---

### TC-002 — Delivery with external reference echoed in locked offer

**Purpose:** verify that the server echoes the caller-assigned `externalRef` in the `LockedOffer`
body, enabling correlation with the retailer's internal booking record.

**Corresponds to request:** TC-002 (lock with external reference)

**Scenario:**
The server returns a full `LockOfferDelivery` including an inline `LockedOffer` for the
Amsterdam–Utrecht journey. The `LockedOffer` includes the echoed `externalRef`, a single
`travelRight` element with price, `afterSalesFlexibility`, and a `confirm` link.

**Key characteristics:**
- `lockedOffer.externalRef` = `"booking-ref-NL-20260315-00042"`
- 1 element: `travelRight` with `Price { EUR, 45.00 }`
- `afterSalesFlexibility: ["nonRefundable", "nonExchangeable"]`

**Example file:** `TC-002-delivery-with-external-ref.json`

---

### TC-003 — Delivery with flexible after-sales conditions (retailer-only)

**Purpose:** verify that when `aftersalesByRetailerOnly: true` was requested, the server
returns after-sales conditions that are `refundable` and `exchangeable` (the retailer-only
restriction is recorded in the booking system, not exposed in the schema).

**Corresponds to request:** TC-003 (after-sales restricted to retailer)

**Scenario:**
A corporate TMC locks a Rotterdam–Amsterdam business fare. The `LockedOffer` returns
`afterSalesFlexibility: ["refundable", "exchangeable"]`, reflecting the flexible fare class.
The after-sales channel restriction is enforced server-side.

**Key characteristics:**
- `lockedOffer.afterSalesFlexibility: ["refundable", "exchangeable"]`
- `personalInformationRequired: false`
- 1 `travelRight` with `Price { EUR, 89.00 }`

**Example file:** `TC-003-delivery-aftersales-retailer-only.json`

---

### TC-004 — Delivery with spot allocation confirmed in locked offer

**Purpose:** verify that the server confirms the selected seat allocation by including a
`spotAllocation` nested under the `travelRight.allocations[]` array in the `LockedOffer`.

**Corresponds to request:** TC-004 (allocation selection)

**Scenario:**
The server locks a Leiden–Schiphol journey with the requested seat (`coach 2, seat 12A`) confirmed.
The `LockedOffer` contains a `travelRight` with a nested `spotAllocation` element specifying
the assigned coach, seat, boarding, and alighting stops.

**Key characteristics:**
- 1 `travelRight` with 1 nested `spotAllocation`
- `spotAllocation.typeOfSpot: "seat"`, `legId: "leg-001"`
- `Price { EUR, 55.00 }` on the travel right

**Example file:** `TC-004-delivery-with-allocation.json`

---

### TC-005 — Delivery with ancillary confirmed in locked offer

**Purpose:** verify that the server confirms the selected bicycle transport ancillary by
including it as a nested element under `travelRight.ancillaries[]` in the `LockedOffer`.

**Corresponds to request:** TC-005 (ancillary selection)

**Scenario:**
The server locks an Amsterdam–Zwolle journey with the bicycle transport ancillary confirmed.
The `LockedOffer` contains a `travelRight` (EUR 34.00) with a nested `ancillary` of type
`bicycleTransport` (EUR 6.40). The `minimumPrice` reflects the combined total.

**Key characteristics:**
- 1 `travelRight` with 1 nested `ancillary` (bicycleTransport)
- `minimumPrice: { EUR, 40.40 }` (travel right + ancillary)

**Example file:** `TC-005-delivery-with-ancillary.json`

---

## OSDM-Specific Test Cases (`POST /lock-offers`)

---

### TC-006 — Full OSDM locked offer: two seat allocations + meal

**Purpose:** verify that the OSDM server returns a complete `LockOfferDelivery` with the full
`LockedOffer` embedded inline, including both confirmed seat allocations and the meal ancillary,
as is typical for international rail responses where all offer elements are confirmed immediately.

**Corresponds to request:** TC-006 (two seat reservations and a meal ancillary)

**Scenario:**
The server confirms the Amsterdam–Paris Eurostar lock. The inline `LockedOffer` contains one
`travelRight` for both travellers, with two `spotAllocation` sub-elements (seats 32A and 32B,
coach 4) and one `meal` ancillary. Personal information is flagged as required before
confirmation. Price details are fully populated.

**Key characteristics:**
- Full inline `LockedOffer`
- `personalInformationRequired: true`
- 1 `travelRight` with 2 `spotAllocation`s + 1 `ancillary` (meal)
- `minimumPrice: { EUR, 190.00 }` (fare 178 + meal 12)
- `summaryDetails[]` and `providedSections[]` populated
- `links[]: [confirm, cancel]`

**Example file:** `TC-006-osdm-full-locked-offer.json`

---

## OMSA-Specific Test Cases (`POST /processes/lock-offers/execute`)

---

### TC-007 — OMSA locked offer: train + OV-fiets bicycle rental

**Purpose:** verify that the OMSA server returns a `LockOfferDelivery` with the full
`LockedOffer` embedded, including the OV-fiets bicycle rental as a nested ancillary, with
OMSA-style links pointing to the OGC API Processes `execute` endpoint for confirmation.

**Corresponds to request:** TC-007 (OMSA lock with OV-fiets ancillary)

**Scenario:**
The server confirms the Den Haag–Delft + OV-fiets lock. The inline `LockedOffer` contains a
`travelRight` (EUR 4.20) with a nested `bicycleRental` ancillary (EUR 3.85). The `externalRef`
from the OMSA trip ID is echoed. Confirmation link points to the OMSA processes endpoint.

**Key characteristics:**
- Full inline `LockedOffer`
- 1 `travelRight` with 1 nested `ancillary` (bicycleRental)
- `minimumPrice: { EUR, 8.05 }`
- OMSA-style confirm link: `POST /processes/confirmations/execute`
- OMSA-style self link: `GET /collections/locked-offer-details/items/{id}`

**Example file:** `TC-007-omsa-locked-offer-with-ovfiets.json`

---

## TOMP-Specific Test Cases (`POST /processes/lock-offers/execution`)

---

### TC-008 — TOMP locked offer: transit + luggage storage ancillary

**Purpose:** verify that the TOMP server returns a `LockOfferDelivery` with the full
`LockedOffer` embedded, including the luggage storage ancillary, with TOMP-style links
pointing to the `execution` confirmation endpoint.

**Corresponds to request:** TC-008 (TOMP retailer-only + luggage storage ancillary)

**Scenario:**
The MaaS platform's lock is confirmed. The inline `LockedOffer` contains a `travelRight` for
the transit leg (EUR 3.60) with a nested `luggageStorage` ancillary (EUR 7.00). The external
MaaS booking reference is echoed. The TOMP `confirm` link points to the `execution` pattern;
the `self` link uses a `POST /processes/locked-offer-details/execution` pattern with a body
template.

**Key characteristics:**
- Full inline `LockedOffer`
- 1 `travelRight` with 1 nested `ancillary` (luggageStorage)
- `minimumPrice: { EUR, 10.60 }`
- TOMP-style links using the `execution` path pattern
- `self` link uses a `body` template (OGC API Processes pattern)

**Example file:** `TC-008-tomp-locked-offer-with-luggage.json`

---

## Locked Offer Detail Test Cases (`LockedOffer`)

These cases apply to the `GET /locked-offers/{lockedOfferId}` (OSDM),
`GET /collections/locked-offer-details/items/{lockedOfferId}` (OMSA),
and the two POST detail endpoints. The schema for the response body is `LockedOffer` directly
(not wrapped in a `LockOfferDelivery`).

---

### TC-009 — Full locked offer detail (OSDM GET)

**Purpose:** verify the complete `LockedOffer` response returned by the detail endpoint,
containing all offer element sub-types, price breakdowns, summary details, provided sections,
and hypermedia links.

**Corresponds to request:** TC-009 (basic locked offer detail request)

**Scenario:**
The client requests the full detail of a previously locked Amsterdam–Paris Eurostar offer
(same offer as TC-006). The server returns the complete `LockedOffer` directly as the response
body, with all elements, allocations, ancillaries, prices, conditions, and links populated.

**Key characteristics:**
- Root object is `LockedOffer` (not `LockOfferDelivery`)
- `personalInformationRequired: true`
- 1 `travelRight` with 2 `spotAllocation`s + 1 `ancillary` (meal)
- Full `summaryDetails[]` and `providedSections[]`
- `links[]: [confirm, cancel]`

**Example file:** `TC-009-locked-offer-detail.json`
