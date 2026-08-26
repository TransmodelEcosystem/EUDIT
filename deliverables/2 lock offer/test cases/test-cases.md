# Lock Offer — Test Cases for Request Bodies

This document describes the test cases for the lock-offer endpoints of the *OTI Lock Offer* API.

The two request schemas covered are:

- **`LockOfferRequest`** — used by `POST /lock-offers`, `POST /processes/lock-offers/execute`, and `POST /processes/lock-offers/execution`.
- **`LockedOfferDetailRequest`** — used by `POST /processes/locked-offer-details/execute` and `POST /processes/locked-offer-details/execution`. (The OSDM `GET /lock-offers/{lockedOfferId}` endpoint uses a path parameter instead and requires no request body.)

The test cases are organised in two sections:

1. **Generic test cases (TC-001 – TC-005)** — applicable to all three `lock-offers` endpoints.
2. **Standard-specific test cases**:
   - **TC-006**: OSDM 3.7.1 (`POST /lock-offers`)
   - **TC-007**: OMSA 0.1.0 (`POST /processes/lock-offers/execute`)
   - **TC-008**: TOMP-API 2.0.0 (`POST /processes/lock-offers/execution`)
3. **Locked offer detail (TC-009)** — for the two POST detail endpoints (OMSA and TOMP-API).

---

## Generic Test Cases (`LockOfferRequest`)

These cases cover the core request model and apply regardless of which endpoint or standard is used.
The `offerReference` values are representative server-assigned UUIDs as they would appear in a real `LockOfferDelivery`.

---

### TC-001 — Minimal lock

**Purpose:** the most basic possible request: lock an offer identified solely by its reference.
No extra selections, no external reference, no flags.

**Scenario:**
A traveller has received a list of offers from a search-offers call and selects one to lock.
The caller sends only the mandatory `offerReference` and relies on the server to apply defaults for
all other options.

**Key characteristics:**
- `offerReference` only — all other fields absent
- No allocations, no ancillaries, no external reference, no flag

**Example file:** `TC-001-minimal-lock.json`

---

### TC-002 — Lock with external reference

**Purpose:** verify that the optional `externalRef` field is correctly transmitted, enabling the
caller's booking system to correlate the locked offer with its own internal booking record.

**Scenario:**
A retailer's backend system locks an offer and attaches its own internal booking reference
(`booking-ref-NL-20260315-00042`) for later correlation during confirmation and fulfilment.

**Key characteristics:**
- `offerReference` + `externalRef`
- No allocations, no ancillaries, no flag

**Example file:** `TC-002-external-reference.json`

---

### TC-003 — After-sales restricted to retailer only

**Purpose:** verify that the `aftersalesByRetailerOnly` flag is correctly transmitted, instructing
the server that the passenger must not be able to perform after-sales operations (refund, exchange)
directly — only the retailer may do so.

**Scenario:**
A corporate travel management company locks an offer on behalf of an employee. Company policy
requires that all post-purchase changes go through the TMC, not through the passenger-facing
channel. The TMC sets `aftersalesByRetailerOnly: true` and attaches its internal reference.

**Key characteristics:**
- `offerReference` + `aftersalesByRetailerOnly: true` + `externalRef`
- No allocations, no ancillaries

**Example file:** `TC-003-aftersales-retailer-only.json`

---

### TC-004 — Lock with one allocation selection

**Purpose:** verify that a specific allocation offer element (e.g. a seat reservation returned by
the search) can be selected by reference as part of the lock request.

**Scenario:**
A traveller searches for offers and receives a travel right together with a seat reservation offer
element. The traveller selects a specific seat (`offer-element-seat-001`) to include in the lock.
The `allocationReference` points to the `offerElementId` of that reservation as returned by the
search-offers response.

**Key characteristics:**
- `offerReference` + 1 allocation selection
- `allocationReference` references an `offerElementId` from the preceding search result
- No ancillaries, no external reference, no flag

**Example file:** `TC-004-allocation-selection.json`

---

### TC-005 — Lock with one ancillary selection

**Purpose:** verify that a specific ancillary offer element (e.g. a bicycle transport ticket) can
be selected by reference and included in the lock alongside the base travel right.

**Scenario:**
A traveller takes a bicycle on the train. The search returned a bicycle transport ancillary offer
element. The traveller selects it (`offer-element-bicycle-001`) to be locked together with
the base travel offer.

**Key characteristics:**
- `offerReference` + 1 ancillary selection
- `ancillaryReference` references an `offerElementId` from the preceding search result
- No allocations, no external reference, no flag

**Example file:** `TC-005-ancillary-selection.json`

---

## OSDM-Specific Test Cases (`POST /lock-offers`)

---

### TC-006 — Two seat reservations and a meal ancillary (international rail)

**Purpose:** verify that multiple allocation selections and an ancillary can be combined in a
single lock request, as is typical for international rail sales where seat reservations and
on-board catering are selected alongside the fare.

**Scenario:**
Two travellers are travelling from Amsterdam to Paris on a high-speed service. They have
received an offer from a prior OSDM search. Both travellers select adjacent seats in coach 4
(seats 32A and 32B) and add a standard meal ancillary. The lock request references all three
offer elements by their `offerElementId`s, plus the retailer's PNR as `externalRef`.

**Key characteristics:**
- `offerReference` + `externalRef` (PNR)
- 2 allocation selections (`allocationReference`: coach4/32A, coach4/32B)
- 1 ancillary selection (`ancillaryReference`: meal)

**Example file:** `TC-006-osdm-seat-reservations-and-meal.json`

---

## OMSA-Specific Test Cases (`POST /processes/lock-offers/execute`)

---

### TC-007 — Lock with ancillary (OV-fiets) and OMSA external reference

**Purpose:** verify that an OMSA lock request can select a shared-mobility ancillary offer element
(e.g. an OV-fiets bicycle rental included in a combined offer) alongside the base travel right.

**Scenario:**
A traveller books a combined train + OV-fiets offer from Den Haag Centraal to Delft via OMSA.
The search returned a travel right and a bicycle rental ancillary. The caller attaches its own
OMSA-style trip reference as `externalRef`.

**Key characteristics:**
- `offerReference` + `externalRef` (OMSA trip ID)
- 1 ancillary selection (OV-fiets rental offer element)
- No allocations, no flag

**Example file:** `TC-007-omsa-lock-with-ancillary.json`

---

## TOMP-Specific Test Cases (`POST /processes/lock-offers/execution`)

---

### TC-008 — Retailer-only after-sales + luggage storage ancillary (MaaS)

**Purpose:** verify that a TOMP lock request can combine the `aftersalesByRetailerOnly` flag
with an ancillary selection (e.g. left-luggage storage as part of a MaaS journey), while
attaching a MaaS-platform booking reference as `externalRef`.

**Scenario:**
A MaaS platform locks a combined transit + luggage-storage offer for a traveller arriving at
Rotterdam Centraal. The platform sets `aftersalesByRetailerOnly: true` so that the MaaS operator
retains sole control over any post-purchase operations. A luggage storage slot ancillary is
selected alongside the transit element.

**Key characteristics:**
- `offerReference` + `aftersalesByRetailerOnly: true` + `externalRef` (MaaS booking ID)
- 1 ancillary selection (luggage storage offer element)
- No allocation selections

**Example file:** `TC-008-tomp-retailer-only-lock.json`

---

## Locked Offer Detail Test Cases (`LockedOfferDetailRequest`)

These cases apply to `POST /processes/locked-offer-details/execute` (OMSA) and
`POST /processes/locked-offer-details/execution` (TOMP-API).

> **Note:** the OSDM `GET /lock-offers/{lockedOfferId}` endpoint does not use a request body —
> the locked offer identifier is passed as a path parameter.

---

### TC-009 — Basic locked offer detail request

**Purpose:** verify the minimal `LockedOfferDetailRequest`: retrieve the full detail of a
previously locked offer using the `lockedOfferId` returned in the `LockOfferDelivery`.

**Scenario:**
Following a successful lock, the caller received a `LockOfferDelivery` containing a
`lockedOfferId`. The caller now requests the full locked offer detail via the OMSA or TOMP
POST endpoint, supplying the `lockedOfferId` as `offerReference`.

**Key characteristics:**
- `offerReference` = the `lockedOfferId` from the preceding `LockOfferDelivery`
- No other fields (schema has no other properties)

**Example file:** `TC-009-locked-offer-detail-basic.json`
