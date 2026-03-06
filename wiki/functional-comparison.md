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
* *MP callback* (`TOMP-API-MP v2.0.0`) — TO→MP notifications (`POST /processes/notification/execution`),
  confirmation requests, and payment requests

**Notable gaps:** No standalone timetable endpoint, no dedicated authentication endpoint
(OAuth assumed external). Disruption/situation feed is absent in the TO→MP direction; the
MP callback covers operational notifications only.

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

### 2.4 BoB (full suite)

BoB (Biljetter och Bokning) is a suite of nine interoperability APIs covering authentication,
product distribution, booking, ticket lifecycle, traveller management, validation, inspection,
device management, and participant registry. The security model is based on JWT over mutual
TLS with MTB (Mobile Ticket Binary) as the travel credential format; there is no OAuth.

**Sub-APIs and versions:**

| Sub-API | Version | Key capabilities |
|---------|---------|-----------------|
| Authentication | 1.3.3 | `GET /auth/{entityId}` — JWT via TLS client certificate |
| Booking | 2.0.1 | `POST /booking`, `GET /booking`, `GET /booking/{bookingId}`, `PATCH /booking/{bookingId}` |
| Product | 3.4.0 | `POST /product` (area/group/route filters, discount codes), fare categories, manifest pre-distribution (`POST /manifest`, `GET /pds`) |
| Ticket | 3.4.0 | Full lifecycle: issue, activate, refundable-status, refund-status, revoke, suspend, event log, ticket bundles |
| Token | 1.5.0 | Token retrieval, revocation list, PSP hints |
| Traveller | 3.0.0 | Full CRUD `GET/POST/PUT/DELETE /traveller/{id}`, wallet, notifications, MTB activation |
| Device | 2.1.1 | Device key provisioning, KDK, user-agent registry |
| Inspection | 2.3.0 | Online and offline batch ticket inspection (`POST /inspection/{ticketId}`) |
| Validation | 3.4.0 | `POST /validation/{ticketId}`, blacklist/whitelist, tickle-macros, fraud check |
| ParticipantMetadata | 2.3.1 | `GET /participantMetadata` — participant registry with MTB/auth public keys, interface endpoints, issuer constraints |

**Functional areas covered:**

* Offer/product search (`POST /product` with area, group, route, and discount-code filters)
* Pre-distribution step (`POST /manifest`, `GET /pds`)
* Full traveller management (CRUD, wallet, notifications)
* Booking creation, retrieval, and status change
* Ticket issuance, activation, revocation, suspension, and event log
* Refundable-status and refund-status queries and updates
* Fare category queries (`GET /productcat/fare`, `GET /productcat/generic`, `GET /productcat/traveller`)
* Wallet and transaction management (`/traveller/{id}/wallet/transaction`)
* Discount code support (`POST /product` filter parameters)
* Explicit JWT/TLS authentication endpoint (`GET /auth/{entityId}`)
* Validation and inspection (unique in this standard set — no other in-scope standard has dedicated ticket-validation endpoints)
* Participant registry and cross-operator trust model (`GET /participantMetadata`, administering-body model)

**Notable gaps:** No trip planning, no timetable, no sailing/availability query, no seat
reservation, no ancillary services, no complaints/support workflow, no travel guarantees,
no disruption feed, no OGC capability discovery.

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

| # | Functional Area | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 | InterMOD |
|---|----------------|-----------|---------------|-----------|-----------------|-------------------|---------|
| 1 | **Network & geography** | ✅ `/places`, `/zones`, `StopPlace` | ⚠️ `place` schema only | ❌ | ❌ | ✅ `GetRoutes`, ports | ❓ |
| 2 | **Timetable / schedule** | ⚠️ `DatedJourney`, `OperatingDays` (trip context only) | ❌ | ❌ | ❌ | ✅ `GetTimeTables` | ❓ |
| 3 | **Trip / journey search** | ✅ `TripSearchCriteria`, `POST /trips` | ✅ `search-offers` (TRIP REQUEST) | ⚠️ `travelSpecification` O/D/time only | ❌ | ✅ `GetSailings`, connections | ❓ |
| 4 | **Availability query** | ✅ `PlaceAvailability`, `/availabilities/*` | ✅ `GET /collections/assets` | ⚠️ asset availability via collections | ❌ | ✅ sailing capacity | ❓ |
| 5 | **Offer search / fare query** | ✅ `POST /offers`, `OfferCollectionRequest` | ✅ `GET /collections/offers` | ✅ `search-offers/execute` | ✅ `POST /product`, area/group/route filters | ✅ `GetPrice`, fare details | ❓ |
| 6 | **Offer selection / on-hold** | ✅ `OnHoldOffer`, `/on-hold-offer` | ✅ `select-offers`, `remove-offer` | ✅ `select-offers/execute` | ⚠️ `POST /manifest`, `GET /pds` pre-distribution step | ✅ `ReservationRequest` | ❓ |
| 7 | **Traveller / passenger management** | ✅ `Passenger`, `PassengerCategory`, `/passengers` | ✅ `traveller`, `update-traveller` | ✅ `add/update/remove-traveller` | ✅ full CRUD `/traveller/{id}`, wallet, notifications | ✅ `GetPassengerAndVehicleTypes`, contact | ❓ |
| 8 | **Booking creation** | ✅ `POST /bookings`, `BookingRequest` | ✅ `purchase-offers`, `purchase-package` | ✅ `purchase-package/execute` | ✅ `POST /booking` | ✅ `ConfirmReservation` | ❓ |
| 9 | **Booking retrieval** | ✅ `GET /bookings/{id}`, `/bookings-search` | ✅ `GET /collections/packages` | ✅ package collections | ✅ `GET /booking/{id}` | ✅ `RecallBooking` | ❓ |
| 10 | **Seat / spot / berth reservation** | ✅ `ReservationOfferPart`, `PlaceSelection`, `/coach-layouts` | ✅ `assign-asset` (SPOT RESERVATION) | ⚠️ `spotReservationParameter` in requirements | ❌ | ✅ cabins, berths, deck | ❓ |
| 11 | **Ancillary services** | ✅ `AncillaryOfferPart`, `/ancillaries`, supplementary | ✅ `GET /collections/ancillaries`, `assign-ancillary` | ✅ `GET /collections/ancillaries`, `assign-ancillary` | ❌ | ✅ `GetServices`, meals, pets, shore | ❓ |
| 12 | **Fulfillment / travel documents** | ✅ `Fulfillment`, `FulfillmentDocument`, `/fulfillments` | ✅ `GET /collections/travel-documents` | ✅ `GET /collections/travel-documents` | ✅ `POST /ticket`, `GET /ticket/{id}`, `PUT /ticket/{id}/active` | ✅ `QrCode`, boarding pass, PDF | ❓ |
| 13 | **Physical asset management** | ❌ | ✅ `{assetOperation}-asset` lock/unlock/open-trunk | ⚠️ `assign-asset` only, no operational control | ❌ | ⚠️ vehicle assignment on ferry | ❓ |
| 14 | **Trip execution / leg operations** | ❌ | ✅ `{legOperation}-leg` start/pause/resume/end | ❌ | ❌ | ❌ | ❓ |
| 15 | **Cancellation** | ✅ `/release-offers`, refund endpoints | ✅ `cancel-package`, `rollback-purchase` | ✅ `cancel-package/execute` | ✅ `PATCH /booking` status=cancelled | ✅ `CancelBooking`, `GetCancelCharge` | ❓ |
| 16 | **Refund / exchange / after-sales** | ✅ `RefundOffer`, `ExchangeOperation`, `/reimbursements` | ✅ `redress-options`, `claim/confirm-redress`, `refund-deposit` | ✅ `claim/confirm-refund-option/execute` | ✅ `POST /ticket/{id}/refundableStatus`, `POST /ticket/{id}/refundStatus` | ⚠️ cancellation charges only | ❓ |
| 17 | **Pricing & fare structure** | ✅ `Fare`, `FareStructure`, `PriceGroup`, `/products` | ✅ `fareStructure`, `GET /collections/fares` | ✅ `amountOfMoney`, `financialDetail` | ⚠️ `GET /productcat/fare` fare categories, product properties | ✅ `GetPrice`, cost categories | ❓ |
| 18 | **Customer account management** | ⚠️ `Purchaser` schema, `/travel-accounts` | ✅ CRUD `/collections/customers` | ❌ | ✅ Traveller API full CRUD + wallet + notifications | ❌ | ❓ |
| 19 | **Invoicing & payment** | ⚠️ `paymentMethods` on `Booking` | ✅ `GET /collections/payments`, `confirm-payment` | ❌ | ⚠️ wallet + `/traveller/{id}/wallet/transaction` | ✅ `GetInvoices`, payment status | ❓ |
| 20 | **Complaints & support** | ✅ `Complaint`, `CustomerComplaint`, `/reimbursements` | ✅ `support-tickets`, `request-support` | ❌ | ❌ | ❌ | ❓ |
| 21 | **Travel guarantees & redress** | ❌ | ✅ `guarantee`, `redressOption`, `claim/confirm-redress` | ⚠️ `guarantees` field (not Transmodel-aligned) | ❌ | ❌ | ❓ |
| 22 | **Promotions & discount codes** | ✅ `promotionCodes`, `corporateCodes`, `/reduction-cards` | ⚠️ `SALE DISCOUNT RIGHT` via fare element | ❌ | ⚠️ discount codes in `POST /product` filter | ✅ `GetOfferCodes` | ❓ |
| 23 | **Authentication / security** | ⚠️ external OAuth assumed | ❌ | ✅ `POST /oauth/token` (3 grant types) | ✅ `GET /auth/{entityId}` explicit JWT/TLS auth endpoint | ❌ | ❓ |
| 24 | **Disruption & real-time info** | ⚠️ `tripStatus`, `situationFullRefs` | ⚠️ `POST /processes/notification/execution` on MP callback (TOMP-API-MP) | ❌ | ❌ | ❌ | ❓ |
| 25 | **API discovery / capability** | ❌ | ✅ `/api`, `/conformance`, `/processes` | ✅ `/api`, `/conformance`, `/processes` | ⚠️ `GET /participantMetadata` participant registry | ❌ | ❓ |

---

## 4. Observations

### 4.1 Universal coverage — prime harmonisation targets

The following areas are supported (✅ or ⚠️) by **all five standards** and represent the
highest-priority candidates for a unified EUDIT interface:

| # | Functional area | Notes |
|---|----------------|-------|
| 7 | Traveller / passenger management | All five represent a traveller entity; BoB has full Traveller API |
| 8 | Booking creation | The universal core act — best starting point for harmonisation |
| 9 | Booking retrieval | All five support retrieval of an existing booking |
| 15 | Cancellation | All five support at least booking cancellation |

---

### 4.2 Broad coverage — strong harmonisation candidates

Supported by four or more of the five standards:

| # | Functional area | Missing from |
|---|----------------|-------------|
| 5 | Offer search / fare query | (none missing — all five now covered) |
| 6 | Offer selection / on-hold | BoB ⚠️ (pre-distribution step only) |
| 12 | Fulfillment / travel documents | (none missing — all five now covered) |
| 17 | Pricing & fare structure | BoB ⚠️ (fare categories only) |
| 18 | Customer account management | OMSA, FerryGateway |
| 11 | Ancillary services | BoB |

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
| 19 | Invoicing & payment | TOMP-API ✅, FerryGateway ✅, BoB ⚠️ | B2B settlement and ferry-specific billing |
| 20 | Complaints & support | OSDM ✅, TOMP-API ✅ | Passenger rights and CRM |
| 21 | Travel guarantees & redress | TOMP-API ✅, OMSA ⚠️ | MaaS service-level agreements |
| 25 | API discovery / capability | TOMP-API ✅, OMSA ✅, BoB ⚠️ | OGC-based standards + BoB participant registry |

---

### 4.4 Coverage gaps — not addressed well by any standard

| # | Functional area | Best available | Gap |
|---|----------------|---------------|-----|
| 24 | Disruption & real-time info | OSDM (`tripStatus`, `situationFullRefs`); TOMP-API-MP (`POST /processes/notification/execution`) | No standard provides a proper disruption or situation feed; TOMP-API-MP covers operational notifications only |
| 23 | Authentication / security | OMSA (`POST /oauth/token`); BoB (`GET /auth/{entityId}` JWT/TLS) | OSDM and TOMP-API assume external OAuth; FerryGateway uses TPE agent context — no unified auth model |
| 2 | Timetable / schedule | FerryGateway (full), OSDM (contextual) | TOMP-API, OMSA, and BoB have no timetable concept |
| 18 | Customer account management | TOMP-API (full CRUD), BoB (Traveller API) | OMSA and FerryGateway treat the customer minimally or not at all |

---

### 4.5 The role of BoB in the ecosystem

BoB has evolved from a narrow booking transaction layer into a **comprehensive ticketing
interoperability suite**. Its nine sub-APIs collectively cover product distribution,
full ticket lifecycle management, traveller account management, device provisioning,
and cross-operator trust infrastructure.

**What makes BoB architecturally distinct:**

* **Security model** — BoB is the only standard in scope with an explicit authentication
  endpoint (`GET /auth/{entityId}`) using JWT over mutual TLS with client certificates.
  The MTB (Mobile Ticket Binary) format is a BoB-native signed travel credential that
  other standards do not have an equivalent for.
* **Validation and Inspection APIs** — BoB is the only in-scope standard with dedicated
  ticket validation (`POST /validation/{ticketId}`, blacklist/whitelist, fraud check) and
  inspection endpoints (`POST /inspection/{ticketId}`). These represent a gap in all other
  standards.
* **Participant Metadata and Administering Body model** — `GET /participantMetadata`
  provides a machine-readable participant registry with public keys, interface endpoints,
  and issuer signature constraints. This cross-operator trust registry has no equivalent
  in OSDM, TOMP-API, OMSA, or FerryGateway.
* **What BoB still does not cover** — trip planning, timetable, availability query, seat
  reservation, ancillary services, complaints/support, travel guarantees, disruption feed,
  and OGC capability discovery. BoB remains a *ticketing* protocol, not a full journey
  planning or MaaS platform.

In EUDIT, BoB should be positioned as the reference model for **ticket lifecycle management,
cross-operator credential exchange, and participant trust infrastructure**, complementing
the journey-planning strengths of OSDM and the MaaS operational capabilities of TOMP-API.

---

### 4.6 Coverage heat map

The table below gives a quick overview of overall standard depth.

| Standard | ✅ Full | ⚠️ Partial | ❌ None | Coverage score |
|----------|--------|----------|--------|---------------|
| OSDM 3.7.1 | 16 | 5 | 4 | 74 % |
| TOMP-API 2.0.0 | 20 | 3 | 2 | 86 % |
| OMSA 0.1.0 | 12 | 5 | 8 | 58 % |
| BoB (full suite) | 9 | 5 | 11 | 46 % |
| FerryGateway 1.3.1 | 16 | 2 | 7 | 68 % |

*Coverage score = (✅ × 1 + ⚠️ × 0.5) / 25 × 100.*
