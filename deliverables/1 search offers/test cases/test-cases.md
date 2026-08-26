# Search Offers — Test Cases for Request Bodies

---

## Generic Test Cases

These cases cover the core request model and apply regardless of which endpoint or standard is used.

**TODO:** WT 1 should supply additional test cases.

---

### TC-001 — Single traveller, single timed leg (simple OJP outcome)

**Purpose:** the most basic possible request: just one traveller.

**Scenario:**  
An adult traveller wants to travel by train from Amsterdam Centraal to Utrecht Centraal on
15 September 2025, departing at 09:12. No special requirements or discounts apply.
The system must return a list of available offers for this trip.

---

### TC-002 — Family group, two adults and two children

**Purpose:** processes multiple travellers with different ages.

**Scenario:**  
A family of four wants to travel from Den Haag Centraal to Rotterdam Centraal on 20 October 2025,
departing at 14:00. The travel party consists of two adults and two children aged 8 and 12.
The system must be able to compose a suitable offer for each member of the family.

---

### TC-003 — Traveller with a discount card

**Purpose:** an entitlement right (e.g. a rail discount subscription or Railcard) can be specified.

**Scenario:**  
A traveller holds an off-peak discount subscription (40% reduction outside peak hours).
The journey is from Utrecht Centraal to Eindhoven on 3 November 2025, departing at 11:30
(off-peak). The system must take the entitlement into account when determining offers.

---

### TC-004 — Traveller with accessibility need (PRM)

**Purpose:** wheelchair-related personal can be specified, so the operator can supply suitable offers.

**Scenario:**  
A traveller uses a motorised wheelchair. The journey is from Leiden Centraal to Schiphol on
5 December 2025, departing at 07:45. The system must transmit the personal need so that the
operator returns only wheelchair-accessible offers and seat assignments.

---

### TC-005 — Traveller with a passenger vehicle (ferry)

**Purpose:** a normal car can be specified, as part of the travelling party, for a ferry crossing.

**Scenario:**  
A traveller wants to cross from Harwich to Hoek van Holland with their car on 10 January 2026,
sailing at 23:00. The car is 430 cm long, 180 cm wide, and 155 cm tall. In addition to the
vehicle transport, the traveller expects the server to return a cabin as an ancillary offer.

---

### TC-006 — Traveller with a bicycle

**Purpose:** a bicycle can be included as a part of the travelling party, enabling the operator to return a bicycle ticket or reservation alongside the passenger offer.

**Scenario:**  
A traveller takes their road bicycle on the train from Amsterdam to Zwolle on 22 March 2026,
departing at 08:30. The bicycle weighs 8 kg. The API should return offers for both the
traveller and the bicycle transport.

---

### TC-007 — Return journey with two trip patterns

**Purpose:** request return tickets in a single offer request (OJP outcome).

**Scenario:**  
A traveller wants to book a day return from Breda to Amsterdam Centraal. Outbound on 14 April 2026
at 08:00, returning the same day at 18:30. The API must return offers for both trip patterns,
potentially combined as a return fare.

---

### TC-008 — Filter on travel class and media type

**Purpose:** specify the travel class and media type as filter.

**Scenario:**  
A business traveller wants to receive only first-class offers available as a mobile ticket.
The journey is from Rotterdam Centraal to Amsterdam Centraal on 2 June 2026, departing at 07:15.

---

### TC-009 — Request trips before/after a requested (departure) time

**Purpose:** request a few offers before and after the requested time.

**Scenario:**  
A traveller wants to take the train from Amsterdam to Berlin on 25 July 2026, preferably
departing around 10:00. The caller wants at most 2 alternatives before and 5 after this time,
with prices expressed in euros.

---

### TC-010 — Traveller with an assistance animal

**Purpose:** verify that a guide dog or assistance animal can be included as a separate travelling
entity (animal), so it can be offered free of charge.

**Scenario:**  
A visually impaired traveller travels with their guide dog from Groningen to Leeuwarden on
8 August 2026, departing at 13:20. The guide dog travels free of charge; the system must
recognise the animal as an assistance animal and return only offers that permit it.

---

### TC-011 — International multi-operator rail journey

**Purpose:** express an international journey spanning services from multiple operators
(e.g. Eurostar + SNCF), as a multi-leg trip pattern, with each operator's
service journey identified separately (OJP outcome).

**Scenario:**  
A traveller wants to travel from Amsterdam Centraal to Paris Gare du Nord on 10 March 2026.
The journey consists of a Thalys/Eurostar high-speed service with a single through service
journey. The traveller holds a standard second-class ticket. No entitlement rights apply.
The server is expected to return cross-border fare combinations from all participating operators.

---

### TC-012 — Promotional code as entitlement right

**Purpose:** verify that a promotional discount code is correctly transmitted as an entitlement
right so that the server can apply campaign pricing where applicable.

**Scenario:**  
A traveller has received a promotional discount code (`PROMO25`) from a campaign run by an
operator. The traveller wants to travel from Brussels-Midi to Cologne on 15 May 2026, departing
at 11:07. The server should recognise the code and include discounted offers where the promotion
applies, alongside full-price offers.

---

### TC-013 — Large group booking

**Purpose:** verify that a large travel party (10 travellers) is correctly processed and that
the server can return group fares where applicable, which typically require a minimum party size.

**Scenario:**  
A group of 10 adults (e.g. a corporate team) wants to travel from Paris Gare de Lyon to Lyon
Part-Dieu by TGV on 22 June 2026, departing at 07:00. The server should detect the group size
and return group-rate offers alongside individual offers where available.

---

### TC-014 — search with mode filter

**Purpose:** express an origin–destination pair without a fixed service journey reference combined with a mode filter.

**Scenario:**  
A traveller wants to travel from Den Haag Centraal to Delft on any rail or bus service on
18 September 2026, arriving before 09:00. The caller does not know the exact service; the server
must search for all matching departures and return offers. Only bus and rail modes are accepted.

--

### TC-015 — Requested sections on a through journey

**Purpose:** verify that the `requestedSections` filter correctly limits the offer search to a
specific portion of a longer through journey (e.g. boarding mid-route or alighting early).

**Scenario:**  
A traveller is already on a through train from Amsterdam to Eindhoven but wants to purchase
a ticket only for the Utrecht–Eindhoven section. The request includes the full trip pattern
but a `requestedSections` entry restricts the offer search to the segment starting at Utrecht.

---

### TC-016 — Traveller with mandatory information (nationality/residency)

**Purpose:** verify that nationality and country of residency, which determine eligibility for
domestic vs. international fares.

**Scenario:**  
A traveller with Belgian nationality and French residency wants to travel from Paris to Brussels
on 5 October 2026. The traveller's qualifying characteristics must be transmitted so that the
server can determine eligibility for domestic Belgian fares, domestic French fares, or full
international pricing.

---

### TC-017 — Multi-modal journey: timed leg + continuous leg (walk)

**Purpose:** verify that a trip pattern combining a timed public transport leg with a
continuous leg (taxi) is correctly expressed.

**Scenario:**  
A traveller wants to go from Rotterdam Centraal to a hotel near Beurs square. The trip consists
of a Metro leg (timed) followed by a 7-minute taxi ride (continuous).

---

### TC-018 — Place specified by geo-coordinates - Non trip based

**Purpose:** on-demand ride or last-mile service can specify its origin and destination 
as geo-coordinate–based places rather than stop references.

**Scenario:**  
A traveller wants to book a taxi or shared-ride from their current geo-location (52.3667°N,
4.9041°E — near Amsterdam Sloterdijk) to Amsterdam Centraal on 12 November 2026 around 08:00.
The leg is continuous (no fixed timetable). The server must accept the coordinate-based
`Place` objects and return mobility offers.

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
