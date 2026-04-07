### Proposed shared-mobility business use cases

These four use cases focus on the shared-mobility dimension of EUDIT (TOMP-API, OMSA, and their intersection with OSDM/rail). They are written at the same level as the CoRoM use cases (6.5.2.x) but cover scenarios not addressed there.

---

#### Spontaneous walk-up shared asset use

The TRANSPORT CUSTOMER is on foot and spots an available shared asset (bike, e-scooter, e-moped) directly in front of them — no prior trip plan, no pre-booking. They access the asset via QR code scan or NFC tap, unlock it, use it, and pay based on actual usage.

**Steps:**
1. TRANSPORT CUSTOMER sees nearby asset in app map or scans the QR code on the asset
2. System retrieves asset details and real-time availability
3. TRANSPORT CUSTOMER selects the asset and requests immediate use (no prior SALES OFFER selection)
4. System creates a mobility session in PENDING state and unlocks the asset (Bluetooth/NFC/remote)
5. TRANSPORT CUSTOMER rides to destination, ends the session, and confirms correct parking
6. System calculates the fare from actual usage (time / distance), generates a JOURNAL ENTRY
7. Payment is charged to the pre-authorised MaaS account; TRAVEL DOCUMENT / receipt issued

---

#### In-trip leg substitution due to changed conditions

The TRANSPORT CUSTOMER is mid-way through a planned multimodal TRAVEL PACKAGE (e.g. train + bike + walking). Before starting the bike leg it starts raining. They request a leg substitution — replacing the bike segment with an alternative mode — without cancelling or replanning the rest of the journey.

**Steps:**
1. TRANSPORT CUSTOMER is in execution of a multimodal TRAVEL PACKAGE; the next leg is a booked shared bike
2. TRANSPORT CUSTOMER decides not to take the bike (weather, injury, asset not where expected)
3. MaaS app surfaces available alternatives for that specific leg (e-scooter, taxi, additional PT)
4. TRANSPORT CUSTOMER selects an alternative; system cancels the bike-leg SALES OFFER PACKAGE (partial redress)
5. System searches for and prices the replacement leg; two-phase purchase appends the new leg to the existing package
6. New leg is started; the cancelled bike-leg amount is refunded or credited
7. TRANSPORT CUSTOMER continues the remainder of the original package unchanged

---

#### Integrated Park & Ride with EV charging and onward rail

The TRANSPORT CUSTOMER drives their private EV to a Park & Ride facility connected to a railway station. They reserve a parking bay with a charging socket, the EV charging session starts on plug-in, and they board the next train towards their final destination — all coordinated within a single multimodal TRAVEL PACKAGE.

**Steps:**
1. TRANSPORT CUSTOMER searches for a combined parking + EV charging + rail package
2. System retrieves available parking bays with charging (TOMP-API) and rail offers (OSDM); customer selects one of each
3. Two-phase purchase across both providers: parking/charging (TOMP-API) and rail (OSDM) are confirmed together
4. TRANSPORT CUSTOMER drives to parking; asset is assigned; charging session starts automatically on plug-in
5. TRANSPORT CUSTOMER boards the train with the OSDM TRAVEL DOCUMENT
6. On return, charging session ends; actual kWh consumed are calculated; JOURNAL ENTRY is sent to the MaaS Provider for settlement
7. TRANSPORT CUSTOMER retrieves vehicle; payment confirmed for the full package

---

#### Retroactive bill pickup for unattended off-street parking

The TRANSPORT CUSTOMER drives into an unattended off-street parking facility. The session starts automatically on entry (barrier / licence-plate recognition). While parked, the TRANSPORT CUSTOMER uses the MaaS app to pick up the bill — attributing the running session to their MaaS account — so that payment is handled centrally at exit instead of at a local pay station.

**Steps:**
1. TRANSPORT CUSTOMER drives in; parking session starts automatically (no pre-booking)
2. TRANSPORT CUSTOMER opens the MaaS app and links the active session to their account (facility ID / QR code at machine / licence plate)
3. MaaS Provider calls the parking operator to register itself as the responsible payer ( with )
4. On exit, barrier opens; operator calculates the final fare
5. Operator pushes a payment request to the MaaS Provider ( on TOMP-API-MP)
6. MaaS Provider settles the payment and confirms ()
7. TRANSPORT CUSTOMER receives a JOURNAL ENTRY / receipt in the MaaS app
