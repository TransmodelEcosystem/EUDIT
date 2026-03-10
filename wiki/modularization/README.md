# Modularisation Options for the EUDIT Technical Specification

This document analyses how the functions covered by the six harmonised standards —
**OSDM**, **TOMP-API** (+ TOMP-API-MP), **OMSA**, **BoB**, and **FerryGateway** —
can be grouped into modules for the EUDIT Technical Specification (TS).

It describes the current per-standard function layout, identifies the cross-cutting
functional domains that span all standards, and presents five distinct modularisation
options with their trade-offs.

A companion slide deck is available at [`modularization-options.pptx`](modularization-options.pptx).

---

## 1. Current Per-Standard Function Layout

### 1.1 OSDM 3.7.1

**Format:** OpenAPI 3.0.3 · **37 179 lines** · **68 paths** · **93 operations** · **491 schemas**

OSDM is the most comprehensive specification in the set. Its operations are organised
into **27 named tags**, which form a natural functional taxonomy: `Bookings`,
`BookedOffers`, `BookingPart`, `Fulfillments`, `Refund`, `Exchange`, `On Hold`,
`Release`, `Cancel Fulfillments`, `Reimbursement Management`, `Complaint Management`,
`Booking Documents`, `Offers`, `Offer Overview`, `Availabilities`, `Trips`, `Places`,
`Purchaser`, `Passengers`, `Bookings Search`, `Bookings Split`, `Travel Account`,
`On-Demand Services`, `Master Data`, `Search`, `Product`, and `Versions`.

All 491 schemas live in a single `components/schemas` block, listed alphabetically.
Schema inheritance uses `allOf` + `discriminator` (29 `allOf` occurrences, 6
`discriminator` declarations). Request/response bodies reference named schemas via
`$ref`; nothing is defined inline. Seven shared parameters (`acceptLanguage`,
`idempotencyKey`, `ifNoneMatch`, `requestor`, `traceParent`, `traceState`,
`acceptNamespace`) and 15 shared error responses are reused on virtually every
operation. The file is fully self-contained: all `$ref` targets are internal.

Global security: OAuth 2.0 client credentials.

### 1.2 TOMP-API 2.0.0

**Format:** OpenAPI 3.0.0 · **9 911 lines** · **51 paths** · **53 operations** · **137 schemas**

TOMP-API follows the **OGC API Processes** pattern: resources are accessed at
`/collections/{collectionId}/items` and operations are invoked at
`/processes/{processId}/execution`. Operations are grouped into **11 named tags**:
`discovery`, `offer`, `pre sales`, `purchase`, `execution`, `support`,
`after sales`, `tech`, `customer management`, `travel information`, and `core`.

Per-operation security is fine-grained: each endpoint declares which of five
schemes it accepts (`OpenData`, `BearerAuth`, `BasicAuth`, `OAuth`, `OAuthPKI`).
Most process endpoints declare an async `jobCompleted` callback, enabling
subscriber-push notifications. The file is fully self-contained.

The `info` block carries an `x-modules` extension listing seven API modules
(`module-1-planning` through `module-7-notifications`), signalling that the
TOMP working group already anticipated module-based decomposition.

### 1.3 TOMP-API-MP 2.0.0

**Format:** OpenAPI 3.0.0 · **417 lines** · **3 paths** · **3 operations** · **2 schemas**

This is the **reverse channel** spec: the Transport Operator calling the MaaS
Provider. It contains three `POST` operations under the `notifications` tag:
`/processes/notification/execution`, `/processes/request-confirmation/execution`,
and `/processes/request-payment/execution`.

This is the **only spec in the repository with external `$ref`**. It imports 11
components from `./TOMP-API 2.0.0.yaml` (parameters, responses, and scalar
schemas). It also carries one broken reference to
`../TOMP-API-6-AFTER-SALES.yaml`, a file not present in the repository — a trace
of a prior modular file layout that was later consolidated.

### 1.4 OMSA 0.1.0

**Format:** OpenAPI 3.0.0 · **5 085 lines** · **29 paths** · **29 operations** · **121 schemas**

OMSA uses the same OGC API Processes architecture as TOMP-API and shares the same
five-scheme security model. Operations are organised into **4 named tags**:
`discovery`, `collections`, `processes`, and `authentication`. It is a draft
(version 0.1.0) representing the EUDIT group's synthesis layer.

A notable internal inconsistency: OMSA uses `/execute` (imperative) for process
endpoints where TOMP-API uses `/execution` (nominal). OMSA carries a dual annotation
style: a short-form `x-tm` field (39 occurrences) alongside the fuller `x-semantics`
blocks (121 occurrences) applied by EUDIT.

### 1.5 BoB Suite (10 files)

**Format:** Swagger 2.0 · **10 independent files** · **~118 operations total**

The BoB (Biljetter och Betaltjänster) standard is already decomposed into ten
microservice files, each covering a distinct operational domain:

| File | Version | Paths | Operations | Primary domain |
|---|---|---|---|---|
| `BoB Authentication.yaml` | 1.3.3 | 1 | 1 | JWT token issuance |
| `BoB booking.yaml` | 2.0.1 | 2 | 4 | Preliminary booking |
| `BoB Device.yaml` | 2.1.1 | 4 | 4 | Device key management |
| `BoB Inspection.yaml` | 2.3.0 | 2 | 2 | Ticket inspection |
| `BoB ParticipantMetadata.yaml` | 2.3.1 | 12 | 28 | Operator/participant registry |
| `BoB Product.yaml` | 3.4.0 | 9 | 10 | Product catalogue & fares |
| `BoB Ticket.yaml` | 3.4.0 | 15 | 23 | Full ticket lifecycle |
| `BoB Token.yaml` | 1.5.0 | 7 | 11 | Token revocation & hints |
| `BoB Traveller.yaml` | 3.0.0 | 18 | 25 | Traveller account & wallet |
| `BoB Validation.yaml` | 3.4.0 | 7 | 7 | Ticket validation & fraud |

All ten files are Swagger 2.0 with a flat `definitions` block. None reference each
other. Authentication is handled via a custom `X-BoB-AuthToken` JWT header parameter
repeated on every individual operation rather than through a shared security scheme.
Spatial types (`geoPosition`, `circle`, `polygon`, `polyline`) are duplicated across
five of the ten files (Traveller, Product, Validation, Inspection, Ticket) — each
file is fully self-contained.

### 1.6 FerryGateway XSD 1.3.1

**Format:** W3C XML Schema (XSD 1.0) · **4 047 lines** · **15 message pairs** · **179 complexTypes + 27 simpleTypes**

FerryGateway uses an **RPC-style request/response message design**, predating REST.
Each of its 15 operation pairs bundles multiple lifecycle concerns into a single
exchange:

| Message pair | Functional scope |
|---|---|
| GetRoutes | Route discovery |
| GetPassengerAndVehicleTypes | Passenger/vehicle category catalogue |
| GetTimeTables | Schedule lookup |
| GetSailings | Availability + capacity search |
| GetServices | On-board service catalogue |
| GetPrice | Fare calculation |
| GetOfferCodes | Promotion codes |
| Book | Booking creation |
| RecallBooking | Booking retrieval |
| Reservation / ConfirmReservation | Two-phase reservation |
| GetCancelCharge | Cancellation fee enquiry |
| CancelBooking | Cancellation |
| GetInvoices | Invoice retrieval |
| GetBusTransfer | Bus transfer ancillary |

Type inheritance uses `xs:extension` (45 occurrences) and `xs:restriction` (26
occurrences). Referential integrity is enforced by 70 `xs:key` and 57 `xs:keyref`
constraints. Five `xs:include` files (`ISO_4127_Currency.xsd`,
`ISO_3166-1_Alpha-3_Country.xsd`, `ICAO_9303_Alpha-3_Country.xsd`,
`ISO_639-1_Alpha-2_Language.xsd`, `MessageCodes.xsd`) are referenced but not
present in the repository — they are inside the accompanying zip archive.

---

## 2. Cross-Cutting Functional Domains

The table below maps the **13 functional domains** identified across all standards.
A check mark indicates that the standard contains operations covering that domain.

| # | Domain | OSDM | TOMP-API | TOMP-MP | OMSA | BoB | FerryGateway |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|
| 1 | Network & Service Discovery | ✓ | ✓ | — | ✓ | ✓ | ✓ |
| 2 | Journey / Trip Planning | ✓ | ✓ | — | ✓ | — | ✓ |
| 3 | Pricing & Offers | ✓ | ✓ | — | ✓ | ✓ | ✓ |
| 4 | Booking & Reservation | ✓ | ✓ | — | ✓ | ✓ | ✓ |
| 5 | Fulfillment & Ticketing | ✓ | ✓ | — | ✓ | ✓ | — |
| 6 | After-Sales (Refund / Exchange / Cancel) | ✓ | ✓ | — | ✓ | ✓ | ✓ |
| 7 | Traveller & Account Management | ✓ | ✓ | — | ✓ | ✓ | — |
| 8 | Asset & Token Management | — | ✓ | — | ✓ | ✓ | — |
| 9 | Validation & Inspection | — | — | — | — | ✓ | — |
| 10 | Notifications & Async | — | ✓ | ✓ | ✓ | — | — |
| 11 | Identity, Auth & Participant Metadata | ✓ | ✓ | — | ✓ | ✓ | — |
| 12 | Documents & Invoices | ✓ | — | — | — | — | ✓ |
| 13 | Complaints & Support | ✓ | ✓ | — | ✓ | — | — |

**Key observations:**

* Domains 1–6 are present in five or six standards — they form the **core** of any
  EUDIT TS module structure.
* Domain 9 (Validation & Inspection) is **BoB-only** in this standard set; it
  represents the physical inspection use case absent from the other API-focused specs.
* Domain 10 (Notifications) is present only in TOMP-API-based specs as async
  callbacks and the dedicated reverse-channel TOMP-API-MP file.
* FerryGateway bundles Domains 2, 3, and 4 into single message pairs (e.g. `Book`
  covers reservation, pricing confirmation, and booking creation simultaneously).
* The broken `$ref` in TOMP-API-MP to `../TOMP-API-6-AFTER-SALES.yaml` confirms
  that TOMP originally anticipated a modular file layout aligned with these domains.

---

## 3. Modularisation Options

Five distinct ways to organise the 13 domains into modules for the EUDIT TS are
presented below. Each option is described, then evaluated with explicit PROs and CONs.

---

### Option A — Functional Layer Model

**One module per functional domain.** The 13 cross-cutting domains become 13
chapters in the TS. Each chapter opens with a description of the function, then
maps the relevant operations from each standard into that function.

**Proposed modules:**

1. Network & Service Discovery
2. Journey / Trip Planning
3. Pricing & Offers
4. Booking & Reservation
5. Fulfillment & Ticketing
6. After-Sales
7. Traveller & Account Management
8. Asset & Token Management
9. Validation & Inspection
10. Notifications & Async
11. Identity, Auth & Participant Metadata
12. Documents & Invoices
13. Complaints & Support

#### PROs

* Maps directly to the existing internal tagging in OSDM (27 tags compress cleanly
  into these 13 groups) and TOMP-API's 11 tags — minimal editorial reinterpretation
  needed.
* Granular and unambiguous: every operation can be assigned to exactly one module.
* Standard practice in API design documentation; familiar to implementors.
* New standards can be mapped module-by-module without restructuring the TS.
* Alignment with the `x-modules` hint already present in TOMP-API's `info` block.

#### CONs

* 13 modules is a large chapter count for a single TS document.
* Risk of overlap: Domains 3 (Pricing), 4 (Booking), and 5 (Fulfillment) are
  tightly coupled across all standards — the module boundaries are blurry
  (OSDM's `/offers` feeds directly into `/bookings`; FerryGateway's `GetPrice`
  and `Book` are a single workflow).
* Does not align with how transport industry stakeholders reason — operators and
  integrators think in workflows and journeys, not isolated function categories.
* Produces a reference-heavy TS: chapters 3, 4, and 5 will contain many forward
  and backward cross-references.

---

### Option B — Travel Lifecycle Model

**One module per phase of the travel journey.** Operations are grouped by *when*
in the journey lifecycle they occur, producing five phases plus a cross-cutting
cluster.

**Proposed modules:**

1. **Pre-Trip** — Discovery, Journey Search, Pricing, Offers
2. **Purchase** — Booking, Reservation, Payment Confirmation
3. **Pre-Departure** — Fulfillment, Documents, Traveller Management
4. **In-Journey** — Validation, Inspection, Asset Operations
5. **Post-Trip** — After-Sales, Invoices, Complaints
6. **Cross-cutting** — Authentication, Notifications, Participant Metadata

#### PROs

* Natural narrative flow; accessible to non-technical CEN working-group members
  and policy makers.
* Directly aligned with the seven CoRoM use cases in `wiki/use-cases/` and the
  user-action entries in `wiki/user-actions/README.md`, which already follow the
  journey lifecycle.
* Reduces the chapter count to six — a coherent, book-like structure.
* Module 4 (In-Journey) cleanly isolates the BoB-specific validation/inspection
  domain from the rest.

#### CONs

* Some operations are ambiguous in phase: OSDM's "on-hold offers" and "release
  offers" sit between Pre-Trip and Purchase; FerryGateway's `Book` message bundles
  phases 1–2 in a single exchange.
* Module 4 (In-Journey) is near-empty for most standards except BoB — creates a
  structurally thin chapter for a heavily annotated standard.
* Notifications and authentication are genuinely cross-cutting — a dedicated
  cross-cutting pseudo-module breaks the linear narrative structure.
* Risk: lifecycle phase boundaries may shift as the group refines the use cases in
  Phase 2 (The Forge), forcing TS restructuring.

---

### Option C — Actor / Role-Based Model

**One module per stakeholder role.** Operations are grouped by who calls them,
aligned with `wiki/roles.md` and the Transmodel role model.

**Proposed modules:**

1. **TRANSPORT CUSTOMER** — search, book, manage own trip, after-sales
2. **TRANSPORT OPERATOR** — offer, fulfil, validate, notify
3. **DISTRIBUTOR / MaaS Provider** — aggregate, compose, handle notifications
4. **INSPECTOR** — validate and inspect physical tokens
5. **System-to-System** — metadata, health, auth, participant registry

#### PROs

* Directly aligned with Transmodel role definitions and `wiki/roles.md`, which is
  already an authoritative document in the project.
* Clear ownership: each chapter = one actor's API contract, making conformance
  testing straightforward ("implements the TRANSPORT CUSTOMER module").
* Reduces the module count to five.
* Role-based decomposition is how the BoB standard is already internally structured
  (`BoB ParticipantMetadata.yaml` tags map precisely to participant roles).

#### CONs

* The same operation is often relevant to multiple roles: `GET /bookings/{id}` is
  used by both the TRANSPORT CUSTOMER and the DISTRIBUTOR; `POST /validation` is
  invoked by both the INSPECTOR and the OPERATOR system.
* OSDM, TOMP-API, and OMSA do not separate operations by role in their API design —
  the same endpoint serves any authorised caller, with role distinguished only by
  OAuth scope or JWT claim. The module boundary must be imposed editorially.
* BoB and FerryGateway have no explicit role model in their specs; role assignment
  requires significant editorial judgment and working-group consensus.
* Risk: role boundaries shift as `wiki/roles.md` is refined, requiring TS revision.

---

### Option D — Transmodel Entity-Centric Model

**One module per core Transmodel entity family.** Each chapter is anchored to a
Transmodel entity and shows how each standard implements operations on that entity.

**Proposed modules:**

1. **PLACE & NETWORK** — geography, stop points, network structure
2. **SERVICE JOURNEY & TRIP** — schedule, routing, timetables
3. **FARE PRODUCT & SALES OFFER PACKAGE** — products, pricing, offers
4. **BOOKING & RESERVED PLACE** — reservation and booking lifecycle
5. **TRAVEL DOCUMENT & FULFILMENT METHOD** — tickets, entitlements, MTBs
6. **TRANSPORT CUSTOMER & TRAVEL ACCOUNT** — traveller identity and accounts
7. **ASSET / VEHICLE** — shared-mobility asset management
8. **Cross-cutting** — ORGANISATION, auth, notifications

#### PROs

* Maximally aligned with EUDIT's core mission: producing a *Transmodel-compliant*
  harmonised interface.
* Every module chapter opens with the canonical Transmodel entity definition from
  `wiki/concepts/Transmodel.md` — strong traceability from TS to standard.
* Directly leverages the `x-semantics` annotation work already done on all 10 spec
  files: each annotated schema already declares its Transmodel concept.
* Provides the strongest justification for architectural choices to CEN reviewers.

#### CONs

* Technically demanding: readers without Transmodel literacy will struggle to
  navigate the TS.
* Some operations are purely procedural — health checks, auth token issuance,
  async callbacks — with no primary Transmodel entity; they become awkward
  residuals in the cross-cutting module.
* Transmodel entity boundaries do not always align with API call granularity:
  `POST /bookings` in OSDM simultaneously creates a BOOKING, a RESERVED PLACE,
  and references a SALES OFFER PACKAGE. The "primary entity" assignment requires
  consensus.
* FerryGateway's RPC pairs bundle multiple entity operations — the XSD has no
  `x-semantics` annotations to guide entity assignment.
* Highest editorial complexity of all five options; highest risk of stalling
  in Phase 1.

---

### Option E — Hybrid: Lifecycle × Functional (Recommended)

**Top-level structure follows the travel lifecycle (Option B) for narrative
coherence; within each phase, sub-modules follow functional grouping (Option A)
for precision.** Cross-cutting concerns get a dedicated standalone cluster.

**Proposed structure:**

```
1. Network & Product Catalogue
   1.1  Service Discovery
   1.2  Network Data
   1.3  Product Catalogue & Pricing

2. Planning & Selection
   2.1  Journey Search
   2.2  Availability & Offers

3. Purchase
   3.1  Booking & Reservation
   3.2  Payment & Confirmation

4. Entitlements
   4.1  Fulfillment & Tickets
   4.2  Traveller & Account Management
   4.3  Documents & Invoices

5. In-Journey
   5.1  Validation & Inspection
   5.2  Asset & Token Management

6. Post-Journey
   6.1  After-Sales (Refund / Exchange / Cancel)
   6.2  Complaints & Support

Cross-cutting
   7.1  Identity & Authentication
   7.2  Notifications & Async
   7.3  Participant Metadata & Health
```

#### PROs

* Best of both worlds: lifecycle phases provide narrative clarity for the
  working group; functional sub-modules provide operational precision for
  implementors.
* The six top-level phases match the CoRoM use-case and user-action structure
  already established in `wiki/use-cases/` and `wiki/user-actions/README.md`.
* The cross-cutting cluster (`7.x`) cleanly isolates auth, notifications, and
  metadata — resolving Option B's structural awkwardness without losing the
  lifecycle narrative.
* Phase 1 (Network & Catalogue) and Phase 2 (Planning) compress the thinnest
  functional domains into digestible sub-sections, avoiding the imbalance problem
  of Option B's thin In-Journey chapter.
* Scales well: new standards or operations can be added to a sub-module without
  restructuring the phase-level headings.

#### CONs

* Two-level hierarchy (6 phases × N sub-modules + cross-cutting) produces more
  cross-references between chapters than flat options.
* More complex to author consistently: risk of sub-module boundary drift during
  the biweekly iteration cycles of Phase 3 (The Anvil).
* Risk of over-engineering the document structure during Phase 1 before TS content
  is sufficiently stable — the sub-module boundaries may need to be adjusted as
  Phase 2 (The Forge) develops the first technical proposals.

---

## 4. Comparison

The table below scores each option against six criteria. `✓✓` = strong fit,
`✓` = adequate, `~` = partial fit, `✗` = weak fit.

| Criterion | A — Functional | B — Lifecycle | C — Actor/Role | D — Transmodel | E — Hybrid |
|---|:---:|:---:|:---:|:---:|:---:|
| Transmodel alignment | ~ | ~ | ✓ | ✓✓ | ✓ |
| Narrative clarity for working group | ~ | ✓✓ | ✓ | ~ | ✓✓ |
| Traceability to CoRoM use cases | ~ | ✓✓ | ✓ | ~ | ✓✓ |
| Implementor usability | ✓✓ | ✓ | ✓ | ~ | ✓ |
| Coverage balance across standards | ✓ | ~ | ~ | ~ | ✓ |
| Editorial complexity | low | low | medium | high | medium |

**Reading:** Option E (Hybrid) scores consistently well across all criteria at the
cost of medium editorial complexity — the recommended default for the Phase 2
proposal. Option D (Transmodel) is the aspirational long-term target but carries
the highest risk of stalling in early phases. Option A is the lowest-friction
starting point if the group prefers to iterate on content before committing to
a narrative structure.

---

## 5. Open Questions

[TO DISCUSS / COMPLETE]

* **Validation & Inspection scope:** BoB is currently the only standard covering
  Domain 9. Should this domain be a full TS module, or a sub-section of Domain 5
  (Fulfillment & Ticketing) pending other standards adding inspection support?

* **FerryGateway RPC mapping:** FerryGateway's message pairs bundle multiple
  lifecycle phases into single exchanges. Under Option B or E, does a bundled pair
  (e.g. `Book`) appear in *both* the Planning and Purchase modules, or is it
  assigned to the phase of its primary outcome?

* **TOMP-API-MP placement:** The reverse notification channel is currently a
  standalone spec file. Under any option, should it be a standalone module, a
  sub-section of the Notifications module, or an annex?

* **Which option to carry into Phase 2 (The Forge)?** The group should select one
  option — or a hybrid — as the baseline TS structure to develop in the first
  technical proposal. This document recommends **Option E** as the default, but the
  decision requires working-group consensus.

* **Granularity of modules in Phase 3 (The Anvil):** As the TS is iterated
  biweekly, should module boundaries be treated as frozen, or may they be
  reorganised during Phase 3 iteration without triggering a full ballot reset?
