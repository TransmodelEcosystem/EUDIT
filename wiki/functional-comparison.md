# Functional Comparison of Standards

This document compares the five transport/mobility API standards in scope for EUDIT —
**OSDM**, **TOMP-API**, **OMSA**, **BoB**, and **FerryGateway** — across a common set of
functional areas. The goal is to identify overlaps, gaps, and harmonisation opportunities
ahead of Phase 2 (The Forge).

InterMOD is listed as a column for completeness; no specification is currently available.

---

## 1. Methodology

### Functional area identification

Twenty-five functional areas were derived by taking the union of all endpoints, operations,
and schema groups found across the five standards. Each area represents a coherent business
capability that one or more standards address.

### Scoring

Each cell in the matrix carries a **coverage symbol** and a **short note** referencing
the primary endpoint, schema, or message type that provides the coverage.

| Symbol | Meaning |
|--------|---------|
| ✅ | Fully supported — explicit endpoints or data types present |
| ⚠️ | Partially supported — concept present but limited, implicit, or context-only |
| ❌ | Not supported — out of scope for this standard |
| ❓ | Unknown — no specification available |

### Source material

Scores are grounded in:

- Frozen specification files in `wiki/specifications/`
- Transmodel mapping documents in `wiki/mappings/`

---

## 2. Functional Areas per Standard

### 2.1 OSDM 3.7.1

OSDM (UIC IRS 90918-10) is a comprehensive, rail-oriented API covering the full lifecycle
from trip planning through booking, fulfillment, refund, and passenger rights. It is the most
feature-rich standard in scope with 27 API tag groups.

**Functional areas covered:**

* Trip search and multi-leg journey planning (`/trips`, `TripSearchCriteria`)
* Network reference data (`/places`, `/zones`, `StopPlace`, `Address`)
* Real-time status overlay (`tripStatus`, `situationFullRefs`)
* Full offer lifecycle: search → on-hold → book (`/offers`, `/bookings/{id}/on-hold-offer`)
* Seat and berth reservation (`ReservationOfferPart`, `/coach-layouts`, `PlaceSelection`)
* Ancillary services (`AncillaryOfferPart`, supplementary products)
* Fulfillment and ticket issuance (`/bookings/{id}/fulfillments`, `FulfillmentDocument`)
* Refund, exchange, and release (`/refund-offers`, `/exchange-operations`, `/release-offers`)
* Passenger rights reimbursement (`/reimbursements`)
* Complaint management (`/complaints`)
* Pricing and regional fare validity (`/products`, `Fare`, `FareStructure`, `/zones`)
* Promotion and corporate discount codes (`promotionCodes`, `corporateCodes`, `/reduction-cards`)
* On-demand and continuous services (`/availabilities/on-demand-services`)

**Notable gaps:** No dedicated customer account CRUD, no leg-execution operations, no travel
guarantee schema, no OGC-style capability discovery.

---

### 2.2 TOMP-API 2.0.0

TOMP-API (Transport Operator–MaaS Provider Interface) is the most operationally broad
standard. It covers not only the booking lifecycle but also real-time asset control and B2B
payment settlement across eight modules.

**Functional areas covered:**

* *Offers* — offer and asset search (`/collections/offers`, `search-offers`)
* *Pre-sales* — traveller management, offer selection, asset and ancillary assignment
* *Purchase* — purchase, two-phase commit, confirm/rollback, travel document retrieval
* *Execution* — leg operations (`{legOperation}-leg`: start/pause/resume/end/extend/postpone)
  and asset operations (`{assetOperation}-asset`: lock/unlock/open-trunk)
* *Support* — support ticket creation and listing
* *After-sales* — redress options, cancellation, refund deposit, B2B payment confirmation
* *Customer management* — full CRUD on customer accounts (`/collections/customers`)
* *Travel information* — fares, user profiles, entitlements, card types, license types

**Notable gaps:** No standalone timetable endpoint, no disruption/situation feed, no
dedicated authentication endpoint (OAuth assumed external).

---

### 2.3 OMSA 0.1.0

OMSA (Open MaaS Standard Architecture) follows the OGC API Processes + Features pattern
with an asynchronous callback model. It covers the core MaaS booking flow but delegates
trip routing to external sources.

**Functional areas covered:**

* Offer search (`search-offers/execute` with `travelSpecification` O/D/time)
* Offer selection and package management (`select-offers/execute`)
* Traveller add, update, remove (`add/update/remove-traveller/execute`)
* Asset and ancillary assignment and catalogue
* Full purchase lifecycle including two-phase commit (`2-phase-purchase-package/execute`)
* Package cancellation and refund claim/confirmation
* Travel document retrieval (`GET /collections/travel-documents`)
* Authentication (`POST /oauth/token` with client\_credentials, password, refresh\_token)
* OGC discovery (landing page, conformance classes, process list)

**Notable gaps:** No trip planner, no timetable, no detailed seat map, no customer account
CRUD, no complaints, no invoicing, no disruption feed.

---

### 2.4 BoB Booking 2.0.1

BoB (Biljetter och Bokning) is deliberately narrow. It covers only the booking transaction
layer on top of a pre-existing trip-planning and fare-selection ecosystem. The security
mechanism is a signed MTB (Mobile Ticket Binary) manifest rather than OAuth.

**Functional areas covered:**

* Booking creation (`POST /booking` with signed MTB manifest)
* Booking retrieval (`GET /booking`, `GET /booking/{bookingId}`)
* Booking status change — confirm and cancel (`PATCH /booking/{bookingId}`)
* Embedded travel credential via the MTB manifest

**Notable gaps:** Trip planning, offer search, pricing, seat reservation, fulfillment,
ancillaries, refund, exchange, and customer management are all out of scope by design.
BoB is a complementary interoperability layer, not a full booking system.

---

### 2.5 FerryGateway 1.3.1

FerryGateway is a ferry-specialist standard defined in XML Schema (request/response message
pairs). It covers the complete ferry booking lifecycle including vessel/route timetables,
vehicle transport, on-board and shore services, and invoicing.

**Functional areas covered:**

* Network: routes and ports (`GetRoutes`)
* Timetable queries (`GetTimeTables`)
* Sailing search and availability (`GetSailings`, `GetServices`)
* Pricing and fare retrieval (`GetPrice`, cost categories, promotions)
* Temporary reservation and booking confirmation (`Reservation`, `ConfirmReservation`)
* Passenger types, categories, and contact details
* Vehicle assignment — cars and trailers on ferry (`Vehicles`, `GetPassengerAndVehicleTypes`)
* On-board services: accommodation/cabins, meals with dietary options, general services
* Shore services: hotel, parking, connecting bus transfers (`GetBusTransfer`)
* Booking retrieval and cancellation (`RecallBooking`, `CancelBooking`, `GetCancelCharge`)
* QR codes and digital boarding passes (`QrCode`)
* Invoicing and payment status (`GetInvoices`)
* Promotional and discount codes (`GetOfferCodes`)

**Notable gaps:** No customer account CRUD, no complaints/support workflow, no travel
guarantees, no disruption feed, no OAuth authentication, no OGC-style capability discovery.

---

### 2.6 InterMOD

No specification is currently available. This column is reserved for future completion once
the InterMOD specification has been published.

---

## 3. Comparison Matrix

| # | Functional Area | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB 2.0.1 | FerryGateway 1.3.1 | InterMOD |
|---|----------------|-----------|---------------|-----------|----------|-------------------|---------|
| 1 | **Network & geography** | ✅ `/places`, `/zones`, `StopPlace` | ⚠️ `place` schema only | ❌ | ❌ | ✅ `GetRoutes`, ports | ❓ |
| 2 | **Timetable / schedule** | ⚠️ `DatedJourney`, `OperatingDays` (trip context only) | ❌ | ❌ | ❌ | ✅ `GetTimeTables` | ❓ |
| 3 | **Trip / journey search** | ✅ `TripSearchCriteria`, `POST /trips` | ✅ `search-offers` (TRIP REQUEST) | ⚠️ `travelSpecification` O/D/time only | ❌ | ✅ `GetSailings`, connections | ❓ |
| 4 | **Availability query** | ✅ `PlaceAvailability`, `/availabilities/*` | ✅ `GET /collections/assets` | ⚠️ asset availability via collections | ❌ | ✅ sailing capacity | ❓ |
| 5 | **Offer search / fare query** | ✅ `POST /offers`, `OfferCollectionRequest` | ✅ `GET /collections/offers` | ✅ `search-offers/execute` | ❌ | ✅ `GetPrice`, fare details | ❓ |
| 6 | **Offer selection / on-hold** | ✅ `OnHoldOffer`, `/on-hold-offer` | ✅ `select-offers`, `remove-offer` | ✅ `select-offers/execute` | ❌ | ✅ `ReservationRequest` | ❓ |
| 7 | **Traveller / passenger management** | ✅ `Passenger`, `PassengerCategory`, `/passengers` | ✅ `traveller`, `update-traveller` | ✅ `add/update/remove-traveller` | ⚠️ `travellerId` on booking only | ✅ `GetPassengerAndVehicleTypes`, contact | ❓ |
| 8 | **Booking creation** | ✅ `POST /bookings`, `BookingRequest` | ✅ `purchase-offers`, `purchase-package` | ✅ `purchase-package/execute` | ✅ `POST /booking` | ✅ `ConfirmReservation` | ❓ |
| 9 | **Booking retrieval** | ✅ `GET /bookings/{id}`, `/bookings-search` | ✅ `GET /collections/packages` | ✅ package collections | ✅ `GET /booking/{id}` | ✅ `RecallBooking` | ❓ |
| 10 | **Seat / spot / berth reservation** | ✅ `ReservationOfferPart`, `PlaceSelection`, `/coach-layouts` | ✅ `assign-asset` (SPOT RESERVATION) | ⚠️ `spotReservationParameter` in requirements | ❌ | ✅ cabins, berths, deck | ❓ |
| 11 | **Ancillary services** | ✅ `AncillaryOfferPart`, `/ancillaries`, supplementary | ✅ `GET /collections/ancillaries`, `assign-ancillary` | ✅ `GET /collections/ancillaries`, `assign-ancillary` | ❌ | ✅ `GetServices`, meals, pets, shore | ❓ |
| 12 | **Fulfillment / travel documents** | ✅ `Fulfillment`, `FulfillmentDocument`, `/fulfillments` | ✅ `GET /collections/travel-documents` | ✅ `GET /collections/travel-documents` | ⚠️ MTB manifest embedded in booking | ✅ `QrCode`, boarding pass, PDF | ❓ |
| 13 | **Physical asset management** | ❌ | ✅ `{assetOperation}-asset` lock/unlock/open-trunk | ⚠️ `assign-asset` only, no operational control | ❌ | ⚠️ vehicle assignment on ferry | ❓ |
| 14 | **Trip execution / leg operations** | ❌ | ✅ `{legOperation}-leg` start/pause/resume/end | ❌ | ❌ | ❌ | ❓ |
| 15 | **Cancellation** | ✅ `/release-offers`, refund endpoints | ✅ `cancel-package`, `rollback-purchase` | ✅ `cancel-package/execute` | ✅ `PATCH /booking` status=cancelled | ✅ `CancelBooking`, `GetCancelCharge` | ❓ |
| 16 | **Refund / exchange / after-sales** | ✅ `RefundOffer`, `ExchangeOperation`, `/reimbursements` | ✅ `redress-options`, `claim/confirm-redress`, `refund-deposit` | ✅ `claim/confirm-refund-option/execute` | ❌ | ⚠️ cancellation charges only | ❓ |
| 17 | **Pricing & fare structure** | ✅ `Fare`, `FareStructure`, `PriceGroup`, `/products` | ✅ `fareStructure`, `GET /collections/fares` | ✅ `amountOfMoney`, `financialDetail` | ❌ | ✅ `GetPrice`, cost categories | ❓ |
| 18 | **Customer account management** | ⚠️ `Purchaser` schema, `/travel-accounts` | ✅ CRUD `/collections/customers` | ❌ | ❌ | ❌ | ❓ |
| 19 | **Invoicing & payment** | ⚠️ `paymentMethods` on `Booking` | ✅ `GET /collections/payments`, `confirm-payment` | ❌ | ❌ | ✅ `GetInvoices`, payment status | ❓ |
| 20 | **Complaints & support** | ✅ `Complaint`, `CustomerComplaint`, `/reimbursements` | ✅ `support-tickets`, `request-support` | ❌ | ❌ | ❌ | ❓ |
| 21 | **Travel guarantees & redress** | ❌ | ✅ `guarantee`, `redressOption`, `claim/confirm-redress` | ⚠️ `guarantees` field (not Transmodel-aligned) | ❌ | ❌ | ❓ |
| 22 | **Promotions & discount codes** | ✅ `promotionCodes`, `corporateCodes`, `/reduction-cards` | ⚠️ `SALE DISCOUNT RIGHT` via fare element | ❌ | ❌ | ✅ `GetOfferCodes` | ❓ |
| 23 | **Authentication / security** | ⚠️ external OAuth assumed | ❌ | ✅ `POST /oauth/token` (3 grant types) | ⚠️ MTB signing (no OAuth endpoint) | ❌ | ❓ |
| 24 | **Disruption & real-time info** | ⚠️ `tripStatus`, `situationFullRefs` | ❌ | ❌ | ❌ | ❌ | ❓ |
| 25 | **API discovery / capability** | ❌ | ✅ `/api`, `/conformance`, `/processes` | ✅ `/api`, `/conformance`, `/processes` | ❌ | ❌ | ❓ |

---

## 4. Observations

### 4.1 Universal coverage — prime harmonisation targets

The following areas are supported (✅ or ⚠️) by **all five standards** and represent the
highest-priority candidates for a unified EUDIT interface:

| # | Functional area | Notes |
|---|----------------|-------|
| 7 | Traveller / passenger management | All five represent a traveller entity; BoB is minimal |
| 8 | Booking creation | The universal core act — best starting point for harmonisation |
| 9 | Booking retrieval | All five support retrieval of an existing booking |
| 15 | Cancellation | All five support at least booking cancellation |

---

### 4.2 Broad coverage — strong harmonisation candidates

Supported by four of the five standards (BoB absent in most cases due to its narrow scope):

| # | Functional area | Missing from |
|---|----------------|-------------|
| 5 | Offer search / fare query | BoB |
| 6 | Offer selection / on-hold | BoB |
| 7 | Traveller / passenger management | (BoB: partial) |
| 11 | Ancillary services | BoB |
| 12 | Fulfillment / travel documents | (BoB: partial — MTB only) |
| 17 | Pricing & fare structure | BoB |

These six areas, together with the four universal areas above, form the **core functional
surface** that a EUDIT unified interface should address first.

---

### 4.3 Narrow or mode-specific coverage

These areas appear in only one or two standards and reflect mode-specific capabilities
or architectural choices:

| # | Functional area | Present in | Character |
|---|----------------|-----------|-----------|
| 2 | Timetable / schedule | FerryGateway ✅, OSDM ⚠️ | Scheduled transport only |
| 13 | Physical asset management | TOMP-API ✅, OMSA ⚠️, FerryGateway ⚠️ | Shared mobility and ferry vehicles |
| 14 | Trip execution / leg operations | TOMP-API only | Shared mobility specific |
| 19 | Invoicing & payment | TOMP-API ✅, FerryGateway ✅ | B2B settlement and ferry-specific billing |
| 20 | Complaints & support | OSDM ✅, TOMP-API ✅ | Passenger rights and CRM |
| 21 | Travel guarantees & redress | TOMP-API ✅, OMSA ⚠️ | MaaS service-level agreements |
| 25 | API discovery / capability | TOMP-API ✅, OMSA ✅ | OGC-based standards only |

---

### 4.4 Coverage gaps — not addressed well by any standard

| # | Functional area | Best available | Gap |
|---|----------------|---------------|-----|
| 24 | Disruption & real-time info | OSDM (`tripStatus`, `situationFullRefs`) | No standard provides a proper disruption or situation feed |
| 23 | Authentication / security | OMSA (`POST /oauth/token`) | OSDM and TOMP-API assume external OAuth; BoB uses MTB signing; FerryGateway uses TPE agent context |
| 2 | Timetable / schedule | FerryGateway (full), OSDM (contextual) | TOMP-API, OMSA, and BoB have no timetable concept |
| 18 | Customer account management | TOMP-API (full CRUD) | The other four standards treat the customer either minimally or not at all |

---

### 4.5 The role of BoB in the ecosystem

BoB is a *booking transaction layer*, not a full-stack booking API. It assumes trip
planning, offer selection, and pricing have already been performed by other systems.
Its MTB security model is Nordic-specific. In EUDIT, BoB should be positioned as a
**complementary ticketing interoperability protocol** (notably for operator-to-operator
credential exchange) rather than a candidate for feature parity with OSDM or TOMP-API.

---

### 4.6 Coverage heat map

The table below gives a quick overview of overall standard depth.

| Standard | ✅ Full | ⚠️ Partial | ❌ None | Coverage score |
|----------|--------|----------|--------|---------------|
| OSDM 3.7.1 | 16 | 5 | 4 | 74 % |
| TOMP-API 2.0.0 | 18 | 4 | 3 | 80 % |
| OMSA 0.1.0 | 10 | 5 | 10 | 50 % |
| BoB 2.0.1 | 4 | 3 | 18 | 22 % |
| FerryGateway 1.3.1 | 13 | 3 | 9 | 58 % |

*Coverage score = (✅ × 1 + ⚠️ × 0.5) / 25 × 100.*
