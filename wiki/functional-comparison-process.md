# Functional Comparison — Process Notes

This document records the reasoning, intermediate decisions, and edge-case analysis that
produced `wiki/functional-comparison.md`. It is intended as a methodology appendix so
future contributors can understand why specific cells were scored the way they were and
can reproduce or challenge the analysis.

---

## 1. Deriving the 25 functional areas

The functional areas were derived by taking the union of all endpoints, operations, and
schema groups across the five standards. The raw material was:

- OSDM 3.7.1 — 27 API tag groups, ~80 unique endpoint paths
- TOMP-API 2.0.0 — 8 modules, ~50 unique endpoint paths; plus TOMP-API-MP 2.0.0 (3 callback endpoints, TO→MP direction)
- OMSA 0.1.0 — OGC Processes model, ~30 unique endpoint paths
- BoB (full suite) — 9 sub-APIs: Authentication 1.3.3, Booking 2.0.1, Product 3.4.0, Ticket 3.4.0, Token 1.5.0, Traveller 3.0.0, Device 2.1.1, Inspection 2.3.0, Validation 3.4.0, ParticipantMetadata 2.3.1 — ~60+ unique endpoint paths
- FerryGateway 1.3.1 — 21 request/response message pairs (XML Schema)

From the union of those, I identified recurring themes and grouped them into 25 named
areas. The grouping decisions are explained below.

**Why separate "timetable" (2) from "trip search" (3)?**
Timetable is a *publication* capability (distribute schedules in bulk or by query). Trip
search is a *planning* capability (find me a journey from A to B at time T). OSDM, TOMP-API,
and OMSA do trip search but not timetable distribution. FerryGateway does both. Keeping them
separate makes the distinction visible.

**Why separate "availability query" (4) from "offer search" (5)?**
Availability is a *capacity* check (how many seats/berths/vehicles are free on service X).
Offer search is a *commercial* query (give me priced options for passenger Y travelling
from A to B). In OSDM these are distinct endpoint groups (`/availabilities/*` vs. `/offers`).
In TOMP-API asset availability (`GET /collections/assets`) is closer to capacity, while
offer search (`search-offers`) is commercial. Keeping them separate exposes that OMSA and
BoB have no dedicated capacity check.

**Why separate "offer selection / on-hold" (6) from "booking creation" (8)?**
Several standards have an explicit *hold* or *reservation* step before a binding purchase
(OSDM `OnHoldOffer`, TOMP-API `select-offers`, OMSA `select-offers/execute`, FerryGateway
`ReservationRequest`). BoB has no such step — it goes directly to booking. Treating them
separately highlights the two-phase vs. one-phase architectural difference.

**Why separate "seat/spot/berth reservation" (10) from "booking creation" (8)?**
A seat reservation is an *optional* or *additional* element on top of a booking (travel
right). In OSDM a `ReservationOfferPart` is separate from an `AdmissionOfferPart`. In
TOMP-API `assign-asset` is a separate process from `purchase-package`. BoB has no seat
concept at all.

**Why combine "refund / exchange / after-sales" (16) into one area?**
They are closely related commercially and TOMP-API bundles them under a single "after-sales"
module. OSDM has three separate endpoint groups (`/refund-offers`, `/exchange-operations`,
`/reimbursements`). Splitting further would make the table harder to read without adding
analytical value at this stage.

**Why include "API discovery / capability" (25)?**
TOMP-API and OMSA both follow OGC API conventions, which mandate a `GET /api` (OpenAPI
description), `GET /conformance` (conformance classes), and `GET /processes` (process list)
endpoint. This is architecturally significant: it enables machine-readable capability
negotiation. OSDM and FerryGateway have none of this. BoB's `GET /participantMetadata`
provides a different but analogous machine-readable registry of participant capabilities,
scored ⚠️.

---

## 2. Pre-scoring analysis per standard

### 2.1 OSDM 3.7.1

Going through the 25 areas:

1. **Network & geography** → ✅. `/places`, `/zones`, `StopPlace`, `Place`, `Address`,
   `GeoPosition` are all present. Clear full support.

2. **Timetable / schedule** → ⚠️. `DatedJourney` (with `operatingDays`) and `OperatingDays`
   encode schedule information, but only as part of trip search results. There is no
   standalone timetable distribution endpoint. OSDM is not a replacement for NeTEx.

3. **Trip / journey search** → ✅. `TripSearchCriteria`, `POST /trips`, `TimedLeg`,
   `TransferLeg`, `ContinuousLeg` all present and well-specified.

4. **Availability** → ✅. `/availabilities/nearby`, `/availabilities/on-demand-services`,
   `/availabilities/place-map`, `/availabilities/vehicle-place-map` for on-demand;
   `PlaceAvailability` and `VehicleAvailability` schemas for scheduled.

5. **Offer search** → ✅. `OfferCollectionRequest` / `OfferCollectionResponse`, `POST /offers`,
   `OfferSearchCriteria`.

6. **Offer selection / on-hold** → ✅. `OnHoldOffer`, `/bookings/{id}/on-hold-offer` endpoints.

7. **Traveller management** → ✅. `Passenger`, `AnonymousPassengerSpecification`,
   `PassengerSpecification`, `PassengerCategory`, `/bookings/{id}/passengers`.

8. **Booking creation** → ✅. `BookingRequest`, `POST /bookings`.

9. **Booking retrieval** → ✅. `GET /bookings/{id}`, `/bookings-search`.

10. **Seat / berth reservation** → ✅. `ReservationOfferPart`, `PlaceSelection`,
    `PlaceAllocation`, `/coach-layouts`, `/coach-deck-layouts`.

11. **Ancillary services** → ✅. `AncillaryOfferPart`, supplementary products, `/ancillaries`
    sub-resource on booked offers.

12. **Fulfillment / travel documents** → ✅. `Fulfillment`, `FulfillmentDocument`,
    `FulfillmentMediaType`, `/bookings/{id}/fulfillments`.

13. **Physical asset management** → ❌. OSDM is rail-oriented. No lock/unlock or shared
    mobility asset control whatsoever.

14. **Trip execution / leg operations** → ❌. No start/pause/end-leg operations. OSDM
    models ticketing, not execution.

15. **Cancellation** → ✅. `/bookings/{id}/release-offers`, `/bookings/{id}/refund-offers`,
    the release-then-refund two-step pattern.

16. **Refund / exchange / after-sales** → ✅. `RefundOffer`, `ExchangeOperation`,
    `ReleaseOffer`, `/reimbursements` (passenger rights).

17. **Pricing & fare structure** → ✅. `Fare`, `FareStructure`, `FareStructureElement`,
    `PriceGroup`, `RegionalValidity`, `/products`, `/zones`.

18. **Customer account management** → ⚠️. `Purchaser` schema and `/travel-accounts` exist
    but there is no full CRUD customer management. Closer to ⚠️ than ✅.

19. **Invoicing & payment** → ⚠️. `paymentMethods` on `Booking` schema. No dedicated
    invoice endpoint.

20. **Complaints & support** → ✅. `Complaint`, `CustomerComplaint`, `ComplaintDecision`,
    `/complaints`, `/bookings/{id}/reimbursements` (passenger rights reimbursement).

21. **Travel guarantees & redress** → ❌. Nothing in the OSDM schema. Passenger rights
    reimbursement exists (area 20) but that is not the same as proactive service guarantees.

22. **Promotions & discount codes** → ✅. `promotionCodes` on `OfferCollectionRequest` and
    `AbstractBookingPart`, `corporateCodes`, `/reduction-cards`.

23. **Authentication** → ⚠️. OAuth is assumed by OSDM but the spec does not define an
    `/oauth/token` endpoint. External identity provider.

24. **Disruption & real-time** → ⚠️. `tripStatus` and `situationFullRefs` on `Trip` give
    partial real-time overlay. There is no dedicated disruption feed or SIRI/NeTEx SX
    endpoint.

25. **API discovery** → ❌. No `/api`, `/conformance`, or `/processes` endpoint.

---

### 2.2 TOMP-API 2.0.0

1. **Network & geography** → ⚠️. `place` schema (`POSTAL ADDRESS`, `TOPOGRAPHIC PLACE`)
   exists but there is no network topology API (no stops list, no line catalogue).

2. **Timetable / schedule** → ❌. No timetable concept. TOMP-API is a booking interface,
   not a timetable system.

3. **Trip / journey search** → ✅. `POST /processes/search-offers/execution` with
   `searchOfferRequest.specification` is explicitly mapped to TRIP REQUEST + FARE QUERY.
   The result contains trips with legs.

4. **Availability** → ✅. `GET /collections/assets/items` returns available assets
   (mapped to TRAVEL PACKAGE SPOT AVAILABILITY QUERY in the mapping).

5. **Offer search** → ✅. `GET /collections/offers/items` and `search-offers`.

6. **Offer selection** → ✅. `select-offers`, `remove-offer`, `update-travel-specification`.

7. **Traveller management** → ✅. `traveller` schema, `update-traveller` process.

8. **Booking creation** → ✅. `purchase-offers`, `purchase-package`, `purchase-product`,
   `use-asset`, `confirm-purchase`.

9. **Booking retrieval** → ✅. `GET /collections/packages/items`.

10. **Seat / spot reservation** → ✅. `assign-asset` is explicitly mapped to
    TRAVEL PACKAGE SPOT RESERVATION REQUEST.

11. **Ancillary services** → ✅. `GET /collections/ancillaries/items`, `assign-ancillary`.

12. **Fulfillment / travel documents** → ✅. `GET /collections/travel-documents/items`.

13. **Physical asset management** → ✅. `{assetOperation}-asset/execution` covers lock,
    unlock, open-trunk. Full operational control — the strongest implementation of this
    area across all five standards.

14. **Trip execution** → ✅. `{legOperation}-leg/execution` covers start, pause, resume,
    end, extend, postpone. TOMP-API is the only standard with this capability.

15. **Cancellation** → ✅. `cancel-package`, `rollback-purchase`.

16. **Refund / exchange / after-sales** → ✅. `GET /collections/redress-options`,
    `claim-redress-option`, `confirm-redress-option`, `refund-deposit`.

17. **Pricing & fare structure** → ✅. `fareStructure`, `fareStructureElement`,
    `GET /collections/fares/items`.

18. **Customer account management** → ✅. Full CRUD: POST/GET/PATCH/DELETE on
    `/collections/customers/items/{customerId}`.

19. **Invoicing & payment** → ✅. `GET /collections/payments/items`, `confirm-payment`.

20. **Complaints & support** → ✅. `GET /collections/support-tickets/items`,
    `request-support`.

21. **Travel guarantees & redress** → ✅. `guarantee` schema (TRIP REPAIR GUARANTEE,
    TRAVEL QUALITY GUARANTEE, INFORMATION QUALITY GUARANTEE), `redressOption`.

22. **Promotions & discounts** → ⚠️. `SALE DISCOUNT RIGHT` appears via
    `fareStructureElement.priceCondition=DISCOUNT` but there is no dedicated discount
    code endpoint or corporate code concept.

23. **Authentication** → ✅. `POST /oauth/token` with `client_credentials`, `password`, and `refresh_token` grant types plus mTLS variant (identity from certificate O or CN; body ignored). Additionally `POST /connect/token` for OpenID Connect token issuance (dataspace JWT). Grant-type coverage is identical to OMSA; extends it with OIDC support.

24. **Disruption & real-time** → ⚠️. `TOMP-API-MP v2.0.0` (the TO→MP callback API)
    provides `POST /processes/notification/execution` for operational notifications from the
    Transport Operator to the MaaS Provider. This is a narrow operational channel, not a
    disruption/situation feed. Scored ⚠️ (up from ❌, since a notification mechanism now
    exists). The core TOMP-API 2.0.0 spec itself has no disruption feed.

25. **API discovery** → ✅. `/api`, `/conformance`, `/processes`, `/capabilities`
    (OGC API conventions).

---

### 2.3 OMSA 0.1.0

1. **Network & geography** → ❌. No network topology. OMSA is a booking interface
   that relies on external data for network information.

2. **Timetable** → ❌.

3. **Trip / journey search** → ⚠️. `searchOfferInput` carries a `travelSpecification`
   (from/to/via/startTime/endTime) but this is just an O/D/time query — the routing logic
   lives in the TO backend, not the API. Not a full trip planner. Scored ⚠️.

4. **Availability** → ⚠️. `GET /collections/assets/items` returns available assets in
   GeoJSON format. This is asset availability, not schedule or seat availability.
   Scored ⚠️ (not ❌ because a dedicated availability query does exist; not ✅ because
   it is limited to physical assets).

5. **Offer search** → ✅. `POST /processes/search-offers/execute`.

6. **Offer selection** → ✅. `POST /processes/select-offers/execute`.

7. **Traveller management** → ✅. `add-traveller`, `update-traveller`, `remove-traveller`.

8. **Booking creation** → ✅. `purchase-package/execute`, `purchase-offers/execute`,
   `2-phase-purchase-package/execute`, `confirm-package/execute`.

9. **Booking retrieval** → ✅. Package collections via `GET /collections` (GeoJSON features).

10. **Seat / spot reservation** → ⚠️. `spotReservationParameter` in `travelParty.requirements`
    lets you express seat constraints, but there is no dedicated seat-selection process.
    The assignment is implicit in the offer result. Scored ⚠️.

11. **Ancillary services** → ✅. `GET /collections/ancillaries/items`, `assign-ancillary`.

12. **Fulfillment / travel documents** → ✅. `GET /collections/travel-documents/items`.

13. **Physical asset management** → ⚠️. `assign-asset/execute` exists but there are no
    operational control operations (lock/unlock). Scored ⚠️ (TOMP-API is ✅ here).

14. **Trip execution** → ❌.

15. **Cancellation** → ✅. `cancel-package/execute`.

16. **Refund / exchange / after-sales** → ✅. `claim-refund-option/execute`,
    `confirm-refund-option/execute`.

17. **Pricing & fare structure** → ✅. `amountOfMoney`, `financialDetail`, `paymentCategory`.

18. **Customer account management** → ❌. No CRUD on customer accounts.

19. **Invoicing & payment** → ❌.

20. **Complaints & support** → ❌.

21. **Travel guarantees** → ⚠️. `guarantees` field on `package` and `offer` schemas is
    OMSA-specific and not Transmodel-aligned. Scored ⚠️ (concept exists but is
    under-specified).

22. **Promotions & discounts** → ❌.

23. **Authentication** → ✅. `POST /oauth/token` with three grant types (`client_credentials`, `password`, `refresh_token`) and mTLS variant (identity from certificate O or CN). One of two in-scope standards with an explicit in-spec auth endpoint — TOMP-API uses an identical `POST /oauth/token` schema plus `POST /connect/token` for OIDC.

24. **Disruption & real-time** → ❌.

25. **API discovery** → ✅. `/api`, `/conformance`, `/processes` (OGC conventions,
    same as TOMP-API).

---

### 2.4 BoB (full suite)

BoB has nine sub-APIs totalling ~60+ endpoints. Scoring covers all sub-APIs.

1. **Network & geography** → ❌. No network topology in any BoB sub-API.

2. **Timetable / schedule** → ❌. Explicitly out of scope across all sub-APIs.

3. **Trip search** → ❌. Explicitly out of scope.

4. **Availability** → ❌. No sailing or seat availability query in any sub-API.

5. **Offer search / fare query** → ✅. `POST /product` (Product API 3.4.0) accepts
   area, group, and route filters and returns product listings. This is a product/offer
   search with commercial filtering. Full support.

6. **Offer selection / on-hold** → ⚠️. `POST /manifest` and `GET /pds` (Product API 3.4.0)
   represent a pre-distribution step where a ticket manifest is prepared before issuance.
   This is analogous to an on-hold / reservation step but is not a classic offer-hold
   pattern. Scored ⚠️.

7. **Traveller management** → ✅. Traveller API 3.0.0 provides full CRUD:
   `GET /traveller`, `POST /traveller`, `GET /traveller/{travellerId}`,
   `PUT /traveller/{travellerId}`, `DELETE /traveller/{travellerId}`, plus wallet endpoints
   and notification endpoints. Full support.

8. **Booking creation** → ✅. `POST /booking` (Booking API 2.0.1).

9. **Booking retrieval** → ✅. `GET /booking`, `GET /booking/{bookingId}` (Booking API 2.0.1).

10. **Seat reservation** → ❌. No seat or spot selection in any BoB sub-API. The `ride`
    schema has `boardingPlace` / `alightingPlace` but no seat-selection mechanism.

11. **Ancillary services** → ❌. No ancillary or supplementary product concept in any
    BoB sub-API.

12. **Fulfillment / travel documents** → ✅. Ticket API 3.4.0 provides `POST /ticket`
    (issue ticket), `GET /ticket/{ticketId}` (retrieve ticket), `PUT /ticket/{ticketId}/active`
    (activate ticket). These represent a proper fulfillment flow separate from the booking.
    Full support.

13. **Physical asset management** → ❌. No lock/unlock or shared-mobility asset control
    in any BoB sub-API.

14. **Trip execution / leg operations** → ❌. No start/pause/end-leg operations.

15. **Cancellation** → ✅. `PATCH /booking/{bookingId}` with `status=cancelled`
    (Booking API 2.0.1). Additionally `PUT /ticket/{ticketId}/revoke` and
    `PUT /ticket/{ticketId}/suspendedStatus` in Ticket API 3.4.0.

16. **Refund / exchange / after-sales** → ✅. Ticket API 3.4.0 provides:
    `GET /ticket/{ticketId}/refundableStatus`, `PUT /ticket/{ticketId}/refundableStatus`,
    `GET /ticket/{ticketId}/refundStatus`, `PUT /ticket/{ticketId}/refundStatus`.
    These are explicit refund lifecycle endpoints. Full support.

17. **Pricing & fare structure** → ⚠️. Product API 3.4.0 provides fare category queries:
    `GET /productcat/fare`, `GET /productcat/generic`, `GET /productcat/traveller`.
    These return product properties and category metadata but there is no full fare structure
    with price breakdown, zone validity, or fare construction logic. Scored ⚠️.

18. **Customer account management** → ✅. Traveller API 3.0.0 provides full CRUD on
    traveller accounts plus wallet management (`GET/POST /traveller/{id}/wallet`,
    `/traveller/{id}/wallet/transaction`) and notification management
    (`GET/PUT /traveller/{id}/notification`). Full support.

19. **Invoicing & payment** → ⚠️. Wallet and transaction endpoints in Traveller API 3.0.0
    (`/traveller/{id}/wallet`, `/traveller/{id}/wallet/transaction`) cover internal account
    balance management but there is no invoice generation or external payment settlement
    endpoint. Scored ⚠️.

20. **Complaints & support** → ❌. No complaint or support ticket endpoint in any
    BoB sub-API.

21. **Travel guarantees & redress** → ❌. No guarantee or redress concept in any
    BoB sub-API.

22. **Promotions & discount codes** → ⚠️. `POST /product` (Product API 3.4.0) accepts
    discount code filter parameters. This is not a dedicated discount endpoint but discount
    codes are a first-class input parameter on the product search. Scored ⚠️.

23. **Authentication / security** → ✅. Authentication API 1.3.3 provides
    `GET /auth/{entityId}` — an explicit endpoint that issues a JWT using mutual TLS client
    certificate authentication. This is the only in-spec, first-class auth endpoint in BoB.
    Full support.

24. **Disruption & real-time** → ❌. No disruption feed, situation feed, or real-time status
    overlay in any BoB sub-API.

25. **API discovery / capability** → ⚠️. ParticipantMetadata API 2.3.1 provides
    `GET /participantMetadata` — a machine-readable registry of participant capabilities,
    public keys, interface endpoints, and issuer constraints. This is analogous to OGC
    capability discovery but uses a proprietary participant-registry model rather than
    OGC conformance classes. Scored ⚠️.

---

### 2.5 FerryGateway 1.3.1

FerryGateway uses request/response XML message pairs. Its functional coverage maps as
follows:

1. **Network & geography** → ✅. `GetRoutes` / `GetRoutesResponse` returns route and
   port information.

2. **Timetable** → ✅. `GetTimeTables` / `GetTimeTablesResponse`.

3. **Trip / journey search** → ✅. `GetSailings` (sailing search), `GetServices`
   (services on a sailing), connection queries.

4. **Availability** → ✅. Sailing availability is part of `GetSailings` response.

5. **Offer search / fare query** → ✅. `GetPrice` / `GetPriceResponse`.

6. **Offer selection / on-hold** → ✅. `ReservationRequest` / `ReservationResponse`
   (temporary reservation before confirmation).

7. **Traveller / passenger management** → ✅. `GetPassengerAndVehicleTypes`, `Passengers`,
   `ContactDetails` elements.

8. **Booking creation** → ✅. `ConfirmReservationRequest` / `ConfirmReservationResponse`,
   `BookRequest` / `BookResponse`.

9. **Booking retrieval** → ✅. `RecallBookingRequest` / `RecallBookingResponse`.

10. **Seat / berth reservation** → ✅. Accommodation (cabins, berths, deck options) is
    part of the service selection in `GetServices` and `BookRequest`.

11. **Ancillary services** → ✅. `GetServices` covers meals (with dietary types),
    `Pets`, on-board services, shore services (hotel, parking),
    `GetBusTransfer` (connecting bus).

12. **Fulfillment / travel documents** → ✅. `QrCode` element, boarding pass, PDF
    attachment via `BookResponseLink`.

13. **Physical asset management** → ⚠️. `Vehicles` element covers car and trailer
    assignment to a ferry sailing. This is vehicle-on-ferry assignment, not operational
    control (lock/unlock). Scored ⚠️.

14. **Trip execution** → ❌.

15. **Cancellation** → ✅. `GetCancelChargeRequest` / `GetCancelChargeResponse`
    (query charge first), then `CancelBookingRequest` / `CancelBookingResponse`.

16. **Refund / exchange / after-sales** → ⚠️. Cancellation charge queries exist but
    there is no explicit refund or exchange flow. Scored ⚠️.

17. **Pricing & fare structure** → ✅. `GetPrice`, cost categories, fare details.

18. **Customer account management** → ❌.

19. **Invoicing & payment** → ✅. `GetInvoicesRequest` / `GetInvoicesResponse`,
    invoice totals, payment status.

20. **Complaints & support** → ❌.

21. **Travel guarantees** → ❌.

22. **Promotions & discount codes** → ✅. `GetOfferCodesRequest` / `GetOfferCodesResponse`.

23. **Authentication** → ❌. FerryGateway uses a `Context` / `TPE` (Travel Provider
    Envelope) with an agent identifier, not OAuth or token-based auth.

24. **Disruption & real-time** → ❌.

25. **API discovery** → ❌.

---

## 3. Decisions on borderline cells

**OMSA area 4 (Availability) — ⚠️ not ❌**
Initial instinct was ❌ because OMSA has no seat or sailing availability query. On closer
inspection, `GET /collections/assets/items` returns real-time asset availability in GeoJSON.
That is a genuine availability query, just limited to physical assets. ⚠️ is more accurate.

**BoB area 6 (Offer selection / on-hold) — ⚠️ not ❌**
`POST /manifest` and `GET /pds` in BoB Product API 3.4.0 constitute a pre-distribution step
before ticket issuance. This is structurally analogous to an offer-hold or pre-booking
reservation, but it is not an explicit "hold this offer for me" pattern. ⚠️ captures the
partial fit.

**BoB area 12 (Fulfillment) — ✅ not ⚠️ (revised)**
In the original analysis covering only Booking API 2.0.1, the MTB manifest embedded in the
booking payload was scored ⚠️. With the full suite, Ticket API 3.4.0 provides a proper
fulfillment flow: `POST /ticket` (issue), `GET /ticket/{ticketId}` (retrieve), and
`PUT /ticket/{ticketId}/active` (activate). This is a genuine separate fulfillment flow.
Revised to ✅.

**BoB area 16 (Refund) — ✅ not ❌ (revised)**
The original analysis of Booking API 2.0.1 found no refund endpoint. Ticket API 3.4.0
adds explicit refund lifecycle endpoints (`refundableStatus`, `refundStatus`). Revised to ✅.

**BoB area 17 (Pricing) — ⚠️ not ❌ (revised)**
`GET /productcat/fare` and related endpoints in Product API 3.4.0 return fare category
metadata. This is not a full fare structure (no zone validity, no price construction),
but it is a genuine pricing-related query. Revised from ❌ to ⚠️.

**BoB area 18 (Customer account management) — ✅ not ❌ (revised)**
Traveller API 3.0.0 provides full CRUD plus wallet management. This is genuine customer
account management. Revised from ❌ to ✅.

**BoB area 19 (Invoicing & payment) — ⚠️ not ❌ (revised)**
Wallet and transaction endpoints in Traveller API 3.0.0 cover internal balance management.
Not a full invoicing system, but payment-related data is present. Revised from ❌ to ⚠️.

**BoB area 22 (Promotions) — ⚠️ not ❌ (revised)**
`POST /product` accepts discount code filter parameters. Not a dedicated discount endpoint
but discount codes are a first-class input. Revised from ❌ to ⚠️.

**BoB area 23 (Authentication) — ✅ not ⚠️ (revised)**
Authentication API 1.3.3 provides `GET /auth/{entityId}` — an explicit in-spec JWT
issuance endpoint using mutual TLS. This is more than a security mechanism embedded in
payloads (as the original ⚠️ for MTB signing implied). Revised from ⚠️ to ✅.

**BoB area 25 (API discovery) — ⚠️ not ❌ (revised)**
`GET /participantMetadata` in ParticipantMetadata API 2.3.1 is a machine-readable
capability registry. Not OGC-conformant, but functionally analogous. Revised from ❌ to ⚠️.

**FerryGateway area 13 (Physical asset management) — ⚠️ not ✅**
Vehicle assignment on a ferry sailing is fundamentally different from shared-mobility asset
control (lock/unlock/open-trunk in TOMP-API). It is closer to seat selection (area 10) than
to asset management. However, it involves assigning a specific physical vehicle object to a
booking, which is the essence of physical asset management. ⚠️ captures the partial fit.

**OSDM area 18 (Customer account management) — ⚠️ not ❌**
`/travel-accounts` exists in OSDM and `Purchaser` is a modelled entity. However, there is
no full CRUD lifecycle for a customer account. ⚠️ rather than ❌.

**TOMP-API area 22 (Promotions & discounts) — ⚠️ not ✅**
`SALE DISCOUNT RIGHT` appears in `fareStructureElement` but there is no dedicated discount
code endpoint. There are no corporate codes or promotional code parameters on offer search
requests (unlike OSDM). ⚠️ is the right score.

**OMSA area 21 (Travel guarantees) — ⚠️ not ❌**
The `guarantees` field appears on `package` and `offer` schemas in OMSA. However, the
spec says this is OMSA-specific and the mapping notes it is "not Transmodel-aligned".
The concept exists but is under-specified and not actionable via a process endpoint.
⚠️ captures "present but limited".

**TOMP-API area 24 (Disruption & real-time) — ⚠️ not ❌ (revised)**
`TOMP-API-MP v2.0.0` adds `POST /processes/notification/execution` — a callback from TO
to MP for operational notifications. This is a narrow notification channel, not a
disruption situation feed. However, the concept of real-time notifications does now exist
in the TOMP-API ecosystem. Revised from ❌ to ⚠️.

---

## 4. Coverage score calculation

The heat map in section 4.6 of the main document uses a simple formula:

```
score = (count_full × 1.0 + count_partial × 0.5) / 25 × 100
```

Counts per standard:

| Standard | ✅ | ⚠️ | ❌ | Score |
|----------|---|---|---|-------|
| OSDM 3.7.1 | 16 | 5 | 4 | (16 + 2.5) / 25 = 74 % |
| TOMP-API 2.0.0 | 20 | 3 | 2 | (20 + 1.5) / 25 = 86 % |
| OMSA 0.1.0 | 12 | 5 | 8 | (12 + 2.5) / 25 = 58 % |
| BoB (full suite) | 9 | 5 | 11 | (9 + 2.5) / 25 = 46 % |
| FerryGateway 1.3.1 | 16 | 2 | 7 | (16 + 1.0) / 25 = 68 % |

**BoB full-suite cell-by-cell summary:**
- ✅ (9): areas 5, 7, 8, 9, 12, 15, 16, 18, 23
- ⚠️ (5): areas 6, 17, 19, 22, 25
- ❌ (11): areas 1, 2, 3, 4, 10, 11, 13, 14, 20, 21, 24

**TOMP-API cell-by-cell summary:**
- ✅ (20): areas 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 25
- ⚠️ (3): areas 1, 22, 24 (area 24 revised from ❌ to ⚠️ with TOMP-API-MP v2.0.0 notification endpoint)
- ❌ (2): areas 2, 23

**OMSA cell-by-cell summary:**
- ✅ (12): areas 5, 6, 7, 8, 9, 11, 12, 15, 16, 17, 23, 25
- ⚠️ (5): areas 3, 4, 10, 13, 21
- ❌ (8): areas 1, 2, 14, 18, 19, 20, 22, 24

**FerryGateway cell-by-cell summary:**
- ✅ (16): areas 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 15, 17, 19, 22
- ⚠️ (2): areas 13, 16
- ❌ (7): areas 14, 18, 20, 21, 23, 24, 25

---

## 5. What was intentionally left out

- **InterMOD** — no specification available; column reserved.
- **Endpoint-level detail** — the matrix notes reference primary endpoints only. A full
  endpoint-by-endpoint cross-reference would require a much larger table and is deferred
  to Phase 2 (The Forge).
- **Data model comparison** — the matrix is functional (what can you do?) not structural
  (how is data modelled?). The Transmodel alignment work in `wiki/mappings/` covers the
  structural dimension.
- **Protocol and transport comparison** (REST vs. OGC-Processes vs. XML-RPC) — relevant
  for the TS but deliberately excluded here to keep the focus on business functionality.
- **BoB sub-API internal workflows** — the Validation and Inspection APIs (unique to BoB
  among in-scope standards) are noted in the matrix but a full workflow analysis of
  online vs. offline validation, blacklist/whitelist management, and fraud-check logic
  is deferred to Phase 2.
