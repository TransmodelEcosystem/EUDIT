#### Guaranteed-arrival PRM journey with medical equipment

The TRANSPORT CUSTOMER is a wheelchair user who travels with a pressurised medical oxygen tank. They must attend a wedding the next day and require a **guaranteed** arrival before 14:00. The journey requires coordinating TRIP MOBILITY FILTER constraints across all legs, verifying that every operator accepts medical oxygen on board, booking ASSISTANCE SERVICE at each interchange, and obtaining a binding TYPE OF GUARANTEE on the arrival time — none of which are straightforwardly interoperable across OSDM, TOMP-API, and BoB today.

**Steps:**
1. TRANSPORT CUSTOMER specifies the trip: destination (wedding venue), latest arrival 14:00 tomorrow; declares accessibility needs (wheelchair) and special equipment (pressurised medical oxygen tank)
2. System searches using TRIP MOBILITY FILTER constraints: step-free boarding, onboard wheelchair space, and confirmation that each leg's operator policy permits pressurised medical oxygen in the passenger cabin
3. System returns only offers where every leg has a confirmed TYPE OF GUARANTEE covering the arrival time; itineraries that cannot commit to arrival before 14:00 are excluded from results
4. TRANSPORT CUSTOMER selects a combined offer; system books ASSISTANCE SERVICE at the departure stop, each interchange, and the arrival stop (ramp, porter, wheelchair-ramp handover); assistance references are attached to the booking
5. System issues a consolidated TRAVEL DOCUMENT covering all legs, embedding the arrival-time guarantee, ASSISTANCE SERVICE booking references, and a medical-equipment carriage note readable by each operator
6. Day of travel: TRANSPORT CUSTOMER receives push notifications per leg (platform, ramp readiness, estimated boarding time); if any leg is delayed and the guarantee is at risk, the system proactively re-routes and re-books ASSISTANCE SERVICE on the alternative leg
7. TRANSPORT CUSTOMER arrives at the wedding venue before 14:00 as guaranteed; arrival is confirmed and logged in the TRANSPORT CUSTOMER's journey history
