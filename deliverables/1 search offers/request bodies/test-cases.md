# Search Offers — Test Cases for Request Bodies

This document describes the test cases for the `POST /offers`, `POST /processes/search-offers/execute`,
and `POST /processes/search-offers/execution` endpoints of the *OTI Search Offers* API.

Each test case has a corresponding JSON example file with the same number prefix.

The test cases are organised in two sections:

1. **Generic test cases (TC-001 – TC-010)** — applicable to all three endpoints.
2. **Standard-specific test cases** — grouped by the standard that primarily drives the scenario:
   - **TC-011 – TC-013**: OSDM 3.7.1 (`POST /offers`)
   - **TC-014 – TC-016**: OMSA 0.1.0 (`POST /processes/search-offers/execute`)
   - **TC-017 – TC-019**: TOMP-API 2.0.0 (`POST /processes/search-offers/execution`)

---

## Generic Test Cases

These cases cover the core request model and apply regardless of which endpoint or standard is used.

---

### TC-001 — Single traveller, single timed leg

**Purpose:** the most basic possible request: one traveller, one timetabled leg, no filter or policy.

**Scenario:**  
An adult traveller wants to travel by train from Amsterdam Centraal to Utrecht Centraal on
15 September 2025, departing at 09:12. No special requirements or discounts apply.
The system must return a list of available offers for this trip.

**Key characteristics:**
- 1 traveller (`entityType: traveller`), no age specified
- 1 trip pattern containing 1 timed leg
- Service: Intercity 930, operating date 2025-09-15
- No filter, no policy

**Example file:** `TC-001-single-traveller.json`

---

### TC-002 — Family group, two adults and two children

**Purpose:** verify that the API correctly processes multiple travellers with different ages and
can return age-banded fares.

**Scenario:**  
A family of four wants to travel from Den Haag Centraal to Rotterdam Centraal on 20 October 2025,
departing at 14:00. The travel party consists of two adults and two children aged 8 and 12.
The system must be able to compose a suitable offer for each member of the family.

**Key characteristics:**
- 2 travellers without age (adults)
- 2 travellers with `age: 12` and `age: 8`
- 1 trip pattern containing 1 timed leg
- No filter, no policy

**Example file:** `TC-002-family.json`

---

### TC-003 — Traveller with a discount entitlement right

**Purpose:** verify that an entitlement right (e.g. a rail discount subscription or Railcard) is
correctly transmitted and can result in adjusted fares.

**Scenario:**  
A traveller holds an off-peak discount subscription (40% reduction outside peak hours).
The journey is from Utrecht Centraal to Eindhoven on 3 November 2025, departing at 11:30
(off-peak). The system must take the entitlement into account when determining offers.

**Key characteristics:**
- 1 traveller with 1 entitlement right: `entitlementType: discountSubscription`, `issuer: NS`, `code: DALVDL`
- 1 trip pattern containing 1 timed leg (mode: rail)
- No filter, no policy

**Example file:** `TC-003-entitlement-right.json`

---

### TC-004 — Traveller with accessibility need (PRM)

**Purpose:** verify that wheelchair-related personal needs are correctly transmitted, so that
the operator returns only accessible products and spaces.

**Scenario:**  
A traveller uses a motorised wheelchair. The journey is from Leiden Centraal to Schiphol on
5 December 2025, departing at 07:45. The system must transmit the personal need so that the
operator returns only wheelchair-accessible offers and seat assignments.

**Key characteristics:**
- 1 traveller with `personalNeeds: [{ type: motorizedWheelchair }]`
- 1 trip pattern containing 1 timed leg (mode: rail)
- No filter, no policy

**Example file:** `TC-004-prm-traveller.json`

---

### TC-005 — Traveller with a passenger vehicle (ferry)

**Purpose:** verify that a passenger travelling with a vehicle can be correctly specified as a
travel party for a ferry crossing.

**Scenario:**  
A traveller wants to cross from Harwich to Hoek van Holland with their car on 10 January 2026,
sailing at 23:00. The car is 430 cm long, 180 cm wide, and 155 cm tall. In addition to the
vehicle transport, the traveller expects the server to return a cabin as an ancillary offer.

**Key characteristics:**
- 1 traveller (`entityType: traveller`)
- 1 vehicle (`entityType: vehicle`, `type: car`, dimensions specified)
- 1 trip pattern containing 1 timed leg (mode: ferry)
- No filter, no policy

**Example file:** `TC-005-vehicle-ferry.json`

---

### TC-006 — Traveller with a bicycle as luggage

**Purpose:** verify that a bulky luggage item (bicycle) can be included as a separate travelling
entity, enabling the operator to return a bicycle ticket or reservation alongside the passenger offer.

**Scenario:**  
A traveller takes their road bicycle on the train from Amsterdam to Zwolle on 22 March 2026,
departing at 08:30. The bicycle weighs 8 kg. The API should return offers for both the
traveller and the bicycle transport.

**Key characteristics:**
- 1 traveller (`entityType: traveller`)
- 1 luggage item (`entityType: luggage`, `type: bicycle`, `weight: 8`)
- 1 trip pattern containing 1 timed leg (mode: rail)
- No filter, no policy

**Example file:** `TC-006-luggage-bicycle.json`

---

### TC-007 — Return journey with two trip patterns

**Purpose:** verify that the API can process multiple trip patterns in a single request — such as
an outbound and a return leg — to enable combined offers (return tickets).

**Scenario:**  
A traveller wants to book a day return from Breda to Amsterdam Centraal. Outbound on 14 April 2026
at 08:00, returning the same day at 18:30. The API must return offers for both trip patterns,
potentially combined as a return fare.

**Key characteristics:**
- 1 traveller (`entityType: traveller`)
- 2 trip patterns, each containing 1 timed leg (mode: rail)
- No filter, no policy

**Example file:** `TC-007-return-journey.json`

---

### TC-008 — Filter on travel class and media type

**Purpose:** verify that the `classOfUse` and `mediaTypes` filter parameters are correctly
transmitted and that the server returns only matching offers.

**Scenario:**  
A business traveller wants to receive only first-class offers available as a mobile ticket.
The journey is from Rotterdam Centraal to Amsterdam Centraal on 2 June 2026, departing at 07:15.

**Key characteristics:**
- 1 traveller (`entityType: traveller`)
- 1 trip pattern containing 1 timed leg (mode: rail)
- Filter: `classOfUse: [first]`, `mediaTypes: [mobileTicket]`
- No policy

**Example file:** `TC-008-filter-class-media.json`

---

### TC-009 — Policy with pagination and currency

**Purpose:** verify that policy parameters are correctly transmitted, specifically pagination
around the requested departure time and the desired currency for price display.

**Scenario:**  
A traveller wants to take the train from Amsterdam to Berlin on 25 July 2026, preferably
departing around 10:00. The caller wants at most 2 alternatives before and 5 after this time,
with prices expressed in euros.

**Key characteristics:**
- 1 traveller (`entityType: traveller`)
- 1 trip pattern containing 1 timed leg (mode: rail, international)
- Policy: `numberOfResultsBefore: 2`, `numberOfResultsAfter: 5`, `currency: EUR`
- No filter

**Example file:** `TC-009-policy-pagination.json`

---

### TC-010 — Traveller with an assistance animal

**Purpose:** verify that a guide dog or assistance animal can be included as a separate travelling
entity (animal), flagged as an assistance animal, alongside its owner.

**Scenario:**  
A visually impaired traveller travels with their guide dog from Groningen to Leeuwarden on
8 August 2026, departing at 13:20. The guide dog travels free of charge; the system must
recognise the animal as an assistance animal and return only offers that permit it.

**Key characteristics:**
- 1 traveller with `personalNeeds: [{ type: accompanyingDog }]`
- 1 animal (`entityType: animal`, `type: dog`, `assistant: true`)
- 1 trip pattern containing 1 timed leg (mode: rail)
- No filter, no policy

**Example file:** `TC-010-assistance-animal.json`

---

## OSDM-Specific Test Cases (`POST /offers`)

These test cases target scenarios typical of the **OSDM 3.7.1** standard, which covers
international rail sales across multiple operators, promotions, and group travel.

---

### TC-011 — International multi-operator rail journey

**Purpose:** verify that an international journey spanning services from multiple operators
(e.g. Eurostar + SNCF) is correctly expressed as a multi-leg trip pattern, with each operator's
service journey identified separately.

**Scenario:**  
A traveller wants to travel from Amsterdam Centraal to Paris Gare du Nord on 10 March 2026.
The journey consists of a Thalys/Eurostar high-speed service with a single through service
journey. The traveller holds a standard second-class ticket. No entitlement rights apply.
The OSDM server is expected to return cross-border fare combinations from all participating operators.

**Key characteristics:**
- 1 traveller, no entitlement rights
- 1 trip pattern with 1 timed leg (international high-speed rail)
- Service: Eurostar 9300 Amsterdam – Paris Nord, 2026-03-10, departure 08:17
- Filter: `classOfUse: [second]`
- Policy: `currency: EUR`

**Example file:** `TC-011-osdm-international-rail.json`

---

### TC-012 — Promotional code as entitlement right

**Purpose:** verify that a promotional discount code is correctly transmitted as an entitlement
right so that the OSDM server can apply campaign pricing where applicable.

**Scenario:**  
A traveller has received a promotional discount code (`PROMO25`) from a campaign run by an
operator. The traveller wants to travel from Brussels-Midi to Cologne on 15 May 2026, departing
at 11:07. The server should recognise the code and include discounted offers where the promotion
applies, alongside full-price offers.

**Key characteristics:**
- 1 traveller with 1 entitlement right: `entitlementType: promotionCode`, `issuer: DB`, `code: PROMO25`
- 1 trip pattern with 1 timed leg (mode: rail, international)
- No filter, no policy

**Example file:** `TC-012-osdm-promo-code.json`

---

### TC-013 — Large group booking

**Purpose:** verify that a large travel party (10 travellers) is correctly processed and that
the OSDM server can return group fares where applicable, which typically require a minimum party size.

**Scenario:**  
A group of 10 adults (e.g. a corporate team) wants to travel from Paris Gare de Lyon to Lyon
Part-Dieu by TGV on 22 June 2026, departing at 07:00. The server should detect the group size
and return group-rate offers alongside individual offers where available.

**Key characteristics:**
- 10 travellers (`entityType: traveller`), no ages specified
- 1 trip pattern with 1 timed leg (mode: rail, high-speed)
- `travelParty.type: group`
- No filter, no policy

**Example file:** `TC-013-osdm-group-booking.json`

---

## OMSA-Specific Test Cases (`POST /processes/search-offers/execute`)

These test cases target scenarios typical of the **OMSA 0.1.0** standard, which emphasises
flexible origin–destination search, partial journey sections, and urban/regional mobility.

---

### TC-014 — Flexible origin–destination search with mode filter

**Purpose:** verify that an OMSA request can express an origin–destination pair without a fixed
service journey reference (i.e. an open search), combined with a mode filter.

**Scenario:**  
A traveller wants to travel from Den Haag Centraal to Delft on any rail or bus service on
18 September 2026, arriving before 09:00. The caller does not know the exact service; the server
must search for all matching departures and return offers. Only bus and rail modes are accepted.

**Key characteristics:**
- 1 traveller
- 1 trip pattern with 1 timed leg, no `serviceJourneyRef` (open search)
- Filter: `modes: [rail, bus]`
- Policy: `numberOfResultsAfter: 5`

**Example file:** `TC-014-omsa-flexible-od.json`

---

### TC-015 — Requested sections on a through journey

**Purpose:** verify that the `requestedSections` filter correctly limits the offer search to a
specific portion of a longer through journey (e.g. boarding mid-route or alighting early).

**Scenario:**  
A traveller is already on a through train from Amsterdam to Eindhoven but wants to purchase
a ticket only for the Utrecht–Eindhoven section. The request includes the full trip pattern
but a `requestedSections` entry restricts the offer search to the segment starting at Utrecht.

**Key characteristics:**
- 1 traveller
- 1 trip pattern with 2 timed legs (Amsterdam→Utrecht, Utrecht→Eindhoven)
- Filter: `requestedSections` specifying `startLegId: leg-002` (Utrecht onwards)
- No policy

**Example file:** `TC-015-omsa-requested-sections.json`

---

### TC-016 — Traveller with qualifying characteristics (nationality/residency)

**Purpose:** verify that nationality and country of residency, which determine eligibility for
domestic vs. international fares in OMSA scenarios, are correctly transmitted via
`qualifyingCharacteristics`.

**Scenario:**  
A traveller with Belgian nationality and French residency wants to travel from Paris to Brussels
on 5 October 2026. The traveller's qualifying characteristics must be transmitted so that the
server can determine eligibility for domestic Belgian fares, domestic French fares, or full
international pricing.

**Key characteristics:**
- 1 traveller with `qualifyingCharacteristics: { nationality: BE, residency: FR }`
- 1 trip pattern with 1 timed leg (mode: rail, international)
- No filter, no policy

**Example file:** `TC-016-omsa-qualifying-characteristics.json`

---

## TOMP-Specific Test Cases (`POST /processes/search-offers/execution`)

These test cases target scenarios typical of the **TOMP-API 2.0.0** standard, which covers
multi-modal MaaS journeys including continuous legs, geo-coordinate–based places, and transfers.

---

### TC-017 — Multi-modal journey: timed leg + continuous leg (walk)

**Purpose:** verify that a trip pattern combining a timed public transport leg with a
continuous leg (e.g. a walking segment to the final destination) is correctly expressed and
that the server can return offers covering the timed portion.

**Scenario:**  
A traveller wants to go from Rotterdam Centraal to a hotel near Beurs square. The trip consists
of a Metro leg (timed) followed by a 7-minute walk (continuous). The TOMP server should return
offers for the Metro leg; the walking segment requires no fare product.

**Key characteristics:**
- 1 traveller
- 1 trip pattern with 2 legs:
  - Leg 1: timed, Metro (mode: metro), Rotterdam Centraal → Beurs
  - Leg 2: continuous, walk (mode: other), `startTime`/`endTime` provided, `startLocation`/`endLocation` as geo-coordinates
- No filter, no policy

**Example file:** `TC-017-tomp-multimodal-walk.json`

---

### TC-018 — Place specified by geo-coordinates

**Purpose:** verify that a `ContinuousLeg` (e.g. on-demand ride or last-mile service) can
specify its origin and destination as geo-coordinate–based places rather than stop references,
as required by TOMP for demand-responsive and MaaS services.

**Scenario:**  
A traveller wants to book a taxi or shared-ride from their current geo-location (52.3667°N,
4.9041°E — near Amsterdam Sloterdijk) to Amsterdam Centraal on 12 November 2026 around 08:00.
The leg is continuous (no fixed timetable). The server must accept the coordinate-based
`Place` objects and return mobility offers.

**Key characteristics:**
- 1 traveller
- 1 trip pattern with 1 continuous leg
- `startLocation`: `{ placeType: topologicalPlace, location: { latitude: 52.3667, longitude: 4.9041 } }`
- `endLocation`: `{ placeType: stopPlace, placeRef: NL:StopPlace:AmsterdamCentraal }`
- No filter, no policy

**Example file:** `TC-018-tomp-geo-coordinates.json`

---

### TC-019 — Multi-leg journey with an explicit transfer leg

**Purpose:** verify that a trip pattern containing an explicit transfer leg between two timed
services is correctly expressed, and that the server does not attempt to price the transfer
itself but correctly associates offers with the surrounding timed legs.

**Scenario:**  
A traveller travels from Antwerp-Centraal to Amsterdam Centraal, requiring a change at
Rotterdam Centraal. The trip pattern contains three legs: a timed Thalys/IC service
(Antwerp→Rotterdam), an explicit transfer leg (platform change at Rotterdam), and a second
timed IC service (Rotterdam→Amsterdam). The TOMP server must return separate offers for each
timed leg and handle the transfer gracefully.

**Key characteristics:**
- 1 traveller
- 1 trip pattern with 3 legs:
  - Leg 1: timed (mode: rail), Antwerp-Centraal → Rotterdam Centraal
  - Leg 2: transfer, Rotterdam Centraal (platform change, ~12 min)
  - Leg 3: timed (mode: rail), Rotterdam Centraal → Amsterdam Centraal
- No filter, no policy

**Example file:** `TC-019-tomp-transfer-leg.json`
