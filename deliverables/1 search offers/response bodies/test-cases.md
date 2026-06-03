# Search Offers — Test Cases for Response Bodies

This document describes the expected response bodies for each test case of the *OTI Search Offers* API.

Each response is the server's answer to the corresponding request body (`request bodies/TC-NNN-*.json`).
Response files follow the updated OTI model (June 2026 revision):

- `SearchOfferDelivery` includes `offers[]`, `travellers[]` (echoed from request), and
  `tripPatterns[]` (echoed from request).
- Each `Offer` carries metadata: `name`, `summary`, `matching`, `status`, `afterSalesFlexibility`,
  `personalInformationRequired`, `minimumPrice`, `summaryDetails[]`, `providedSections[]`, and `links[]`.
- Each `OfferElement` (`travelRight`, `ancillary`, `spotAllocation`) uses `price` (`currencyCode` + `amount`)
  instead of the former `farePrice` stub.
- `TravelRight` may carry `requiredAllocations[]` and `requiredAncillaries[]`.
- `travellingEntities` within offer elements is an array of `{ "t.e.Ref": <integer> }` objects.

---

## Generic Test Cases

---

### TC-001 — Single offer for a single traveller on a single timed leg

**Corresponds to request:** `TC-001-single-traveller.json`

**Scenario:** One adult traveller, IC 930, Amsterdam Centraal → Utrecht Centraal, 2025-09-15, 09:12.

**Expected response:**
- 1 offer with 1 `travelRight` for `leg-001`
- `matching: exact`
- Price: EUR 7.20 (second class single)
- No required allocations or ancillaries
- Echoed traveller and trip pattern

**Example file:** `TC-001-single-offer.json`

---

### TC-002 — Family offers: age-banded fares for four travellers

**Corresponds to request:** `TC-002-family.json`

**Scenario:** Two adults + two children (ages 8 and 12), Den Haag Centraal → Rotterdam Centraal, 2025-10-20, 14:00.

**Expected response:**
- 4 offers (one per traveller), each with 1 `travelRight`
- Adults: EUR 6.00 each
- Child aged 12: EUR 3.00 (50% discount)
- Child aged 8: EUR 0.00 (free under-12 fare)
- Echoed travellers (all four) and trip pattern

**Example file:** `TC-002-family-offers.json`

---

### TC-003 — Discounted offers based on a discount entitlement right

**Corresponds to request:** `TC-003-entitlement-right.json`

**Scenario:** Traveller with 40% off-peak subscription (`DALVDL`), Utrecht Centraal → Eindhoven, 2025-11-03, 11:30 (off-peak).

**Expected response:**
- 2 offers:
  1. Full-price offer (EUR 18.50)
  2. Discounted offer with entitlement applied (EUR 11.10, 40% off)
- Both offers have 1 `travelRight` for `leg-001`
- Echoed traveller and trip pattern

**Example file:** `TC-003-discounted-offers.json`

---

### TC-004 — PRM offer with a required seat reservation

**Corresponds to request:** `TC-004-prm-traveller.json`

**Scenario:** Traveller with motorised wheelchair, Leiden Centraal → Schiphol, 2025-12-05, 07:45.

**Expected response:**
- 1 offer with 1 `travelRight` for `leg-001`
- `requiredAllocations`: 1 required wheelchair space (`typeOfSpot: wheelchairSpace`)
- `matching: exact`
- Price: EUR 4.50 (regional single, accessible product)
- Echoed traveller and trip pattern

**Example file:** `TC-004-prm-offer-with-reservation.json`

---

### TC-005 — Ferry offer with a vehicle transport ancillary

**Corresponds to request:** `TC-005-vehicle-ferry.json`

**Scenario:** Traveller + car, Harwich → Hoek van Holland, 2026-01-10, 23:00.

**Expected response:**
- 1 offer with 2 offer elements:
  1. `travelRight` for traveller (passenger fare, EUR 62.00)
  2. `ancillary` of type `vehicleTransport` (car transport, EUR 110.00)
- `requiredAncillaries` on the `travelRight`: cabin ancillary (min 0, max 1)
- Echoed travellers (traveller + vehicle) and trip pattern

**Example file:** `TC-005-ferry-offer-with-vehicle-ancillary.json`

---

### TC-006 — Offer with a bicycle transport ancillary

**Corresponds to request:** `TC-006-luggage-bicycle.json`

**Scenario:** Traveller with bicycle, Amsterdam → Zwolle, 2026-03-22, 08:30.

**Expected response:**
- 1 offer with 1 `travelRight` for traveller (EUR 22.90)
- `requiredAncillaries` on the `travelRight`: bicycle transport (min 0, max 1)
- The `ancillary` element (`type: bicycleTransport`, EUR 9.50) included in the ancillaries list
- Echoed travellers (traveller + bicycle luggage) and trip pattern

**Example file:** `TC-006-offer-with-bicycle-ancillary.json`

---

### TC-007 — Return journey offers (outbound and return fares)

**Corresponds to request:** `TC-007-return-journey.json`

**Scenario:** Traveller, Breda → Amsterdam Centraal (08:00) and return (18:30), 2026-04-14.

**Expected response:**
- 3 offers:
  1. Single outbound offer (EUR 21.40)
  2. Single return offer (EUR 21.40)
  3. Combined day-return offer (EUR 35.00, covers both trip patterns)
- The combined offer has 1 `travelRight` with `providedSections` referencing both `tp-001` and `tp-002`
- Echoed traveller and both trip patterns

**Example file:** `TC-007-return-journey-offers.json`

---

### TC-008 — First-class mobile ticket

**Corresponds to request:** `TC-008-filter-class-media.json`

**Scenario:** Business traveller, Rotterdam Centraal → Amsterdam Centraal, 2026-06-02, 07:15. Filter: first class, mobile ticket only.

**Expected response:**
- 1 offer with 1 `travelRight` for `leg-001`
- `matching: exact` (filter satisfied)
- Price: EUR 42.60 (first class)
- `fareProduct.productRef` reflects first-class mobile-ticket product
- No required allocations or ancillaries
- Echoed traveller and trip pattern

**Example file:** `TC-008-first-class-mobile-ticket.json`

---

### TC-009 — Paginated offer list with a next-page link

**Corresponds to request:** `TC-009-policy-pagination.json`

**Scenario:** Traveller, Amsterdam → Berlin, 2026-07-25, ~10:00. Policy: 2 results before + 5 after, EUR.

**Expected response:**
- Multiple offers (up to 7 alternatives as requested by policy)
- Each offer: 1 `travelRight`, price in EUR
- Response includes a pagination `link` (`rel: next`) to retrieve further results
- Echoed traveller and trip pattern

**Example file:** `TC-009-paginated-offers-with-next-link.json`

---

### TC-010 — Offer for a traveller with an assistance animal

**Corresponds to request:** `TC-010-assistance-animal.json`

**Scenario:** Visually impaired traveller + guide dog, Groningen → Leeuwarden, 2026-08-08, 13:20.

**Expected response:**
- 1 offer with 2 offer elements:
  1. `travelRight` for the traveller (EUR 8.90)
  2. `travelRight` for the guide dog (`t.e.Ref` pointing to the animal entity), price EUR 0.00
- `personalInformationRequired: false`
- Echoed travellers (traveller + animal) and trip pattern

**Example file:** `TC-010-assistance-animal-offer.json`

---

## OSDM-Specific Test Cases

---

### TC-011 — International multi-operator rail offers

**Corresponds to request:** `TC-011-osdm-international-rail.json`

**Scenario:** Traveller, Amsterdam Centraal → Paris Gare du Nord, Eurostar 9300, 2026-03-10. Second class.

**Expected response:**
- 3 offers representing different fare classes and flexibility tiers:
  1. Standard (non-refundable, EUR 69.00)
  2. Standard Premier (exchangeable, EUR 119.00)
  3. Business Premier (fully flexible, EUR 249.00)
- Each offer has 1 `travelRight` for `leg-001` (full through journey)
- `afterSalesFlexibility` reflects the corresponding tier
- Echoed traveller and trip pattern

**Example file:** `TC-011-osdm-international-offers.json`

---

### TC-012 — Promotional discount applied via entitlement right

**Corresponds to request:** `TC-012-osdm-promo-code.json`

**Scenario:** Traveller with promo code `PROMO25`, Brussels-Midi → Cologne, 2026-05-15, 11:07.

**Expected response:**
- 2 offers:
  1. Full-price offer (EUR 89.00)
  2. Promotional offer with 25% discount applied (EUR 66.75)
- Both have 1 `travelRight` for `leg-001`
- Promotional offer references `entitlementType: promotionCode` in conditions
- Echoed traveller and trip pattern

**Example file:** `TC-012-osdm-promo-offers.json`

---

### TC-013 — Group booking: individual and group fares

**Corresponds to request:** `TC-013-osdm-group-booking.json`

**Scenario:** 10 adults, Paris Gare de Lyon → Lyon Part-Dieu, TGV, 2026-06-22, 07:00.

**Expected response:**
- 2 offers:
  1. Standard individual fare × 10 (EUR 49.00 per person, `minimumPrice: EUR 490.00`)
  2. Group fare (EUR 35.00 per person, `minimumPrice: EUR 350.00`)
- Each offer has 10 `travelRight` elements (one per traveller, each with the correct `t.e.Ref`)
- Echoed travellers (all 10) and trip pattern

**Example file:** `TC-013-osdm-group-offers.json`

---

## OMSA-Specific Test Cases

---

### TC-014 — Flexible OD offers (multiple departures returned)

**Corresponds to request:** `TC-014-omsa-flexible-od.json`

**Scenario:** Traveller, Den Haag Centraal → Delft, any rail/bus service, arriving before 09:00, 2026-09-18.

**Expected response:**
- 5 offers (matching `numberOfResultsAfter: 5` policy), each for a different service journey
- Each offer has 1 `travelRight` for the corresponding leg (short regional fare, ~EUR 3.50)
- Trip patterns in the response echo different service journeys
- Echoed traveller and trip patterns for all returned alternatives

**Example file:** `TC-014-omsa-flexible-od-offers.json`

---

### TC-015 — Offer covering only the requested section (Utrecht–Eindhoven)

**Corresponds to request:** `TC-015-omsa-requested-sections.json`

**Scenario:** Traveller already on train, requesting a ticket only for Utrecht → Eindhoven (`leg-002`).

**Expected response:**
- 1 offer with 1 `travelRight` for `leg-002` only (EUR 12.30)
- `providedSections` covers `startLegRef: leg-002`, `endLegRef: leg-002`
- `matching: partial` (only part of the trip pattern is offered)
- Echoed traveller and trip pattern (including both legs, but offer covers only `leg-002`)

**Example file:** `TC-015-omsa-section-offer.json`

---

### TC-016 — Nationality-based offers (Belgian domestic vs. international)

**Corresponds to request:** `TC-016-omsa-qualifying-characteristics.json`

**Scenario:** Belgian national, French resident, Paris → Brussels, 2026-10-05.

**Expected response:**
- 2 offers:
  1. Belgian domestic fare (EUR 22.50, eligible due to Belgian nationality)
  2. International fare (EUR 49.00, standard cross-border pricing)
- Both have 1 `travelRight` for `leg-001`
- Offer names indicate the fare type (domestic / international)
- Echoed traveller (with `qualifyingCharacteristics`) and trip pattern

**Example file:** `TC-016-omsa-nationality-based-offers.json`

---

## TOMP-Specific Test Cases

---

### TC-017 — Multi-modal offer: timed metro leg only (walking leg has no fare)

**Corresponds to request:** `TC-017-tomp-multimodal-walk.json`

**Scenario:** Traveller, Metro E Rotterdam Centraal → Beurs (timed, 08:04–08:07) + walk Beurs → Coolsingel 1 (continuous, 08:07–08:14).

**Expected response:**
- 1 offer covering **only** `leg-001` (metro timed leg)
- 1 `travelRight` for `leg-001`, `matching: partial` (trip pattern is only partially covered)
- Price: EUR 1.06 (OV-chipkaart distance-based fare)
- No offer element for `leg-002` (walking leg requires no fare product)
- `requiredAllocations: []`, `requiredAncillaries: []`
- Echoed traveller and full trip pattern (both legs)

**Example file:** `TC-017-tomp-multimodal-offer.json`

---

### TC-018 — On-demand taxi offer with geo-coordinate origin and seat allocation

**Corresponds to request:** `TC-018-tomp-geo-coordinates.json`

**Scenario:** Traveller, continuous leg: geo:52.3667,4.9041 → Amsterdam Centraal, 2026-11-12, 08:00–08:20 (on-demand taxi/ride).

**Expected response:**
- 1 offer with 1 `travelRight` for `leg-001`
- `matching: exact`
- Price: EUR 18.50 (flat on-demand fare)
- `requiredAllocations`: 1 seat allocation required (`typeOfSpot: seat`)
- `requiredAncillaries: []`
- `providedSections` references geo-coordinate start place as `geo:52.3667,4.9041`
- Echoed traveller and trip pattern

**Example file:** `TC-018-tomp-geo-offer.json`

---

### TC-019 — Multi-leg journey: separate and combined through-fare offers

**Corresponds to request:** `TC-019-tomp-transfer-leg.json`

**Scenario:** Traveller, Antwerpen Centraal → Rotterdam Centraal (IC 9227, leg-001) + transfer (leg-002) + Rotterdam Centraal → Amsterdam Centraal (IC 910, leg-003), 2026-11-20.

**Expected response:**
- 3 offers:
  1. Leg-1 offer: 1 `travelRight` for `leg-001` only (EUR 17.80, `matching: partial`)
  2. Leg-3 offer: 1 `travelRight` for `leg-003` only (EUR 18.20, `matching: partial`)
  3. Combined through-fare offer: 1 `travelRight` with `providedSections` spanning `leg-001` → `leg-003`
     (EUR 31.00, cheaper than two separate tickets, `matching: exact`)
- No offer element for `leg-002` (transfer leg — no fare product)
- All three offers echo the same single traveller and the full trip pattern (3 legs)

**Example file:** `TC-019-tomp-multi-leg-offers.json`
