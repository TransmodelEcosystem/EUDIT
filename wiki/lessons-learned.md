# Lessons Learned from Source Standards

This document synthesises what the EUDIT Technical Specification should **adopt**, **adapt**, or **avoid** from each of the five source standards — OSDM, TOMP-API, OMSA, FerryGateway, and BoB. It is intended as a decision-support reference for Phase 2 (The Forge) and later phases. Findings are grounded in the functional comparison, per-standard use-case analyses, and Transmodel mapping tables produced during Phase 1 (The Kidling).

Each section follows the same structure: what the standard does well and should be carried forward, followed by what should be left behind or substantially redesigned. A cross-cutting synthesis table closes the document.

---

## OSDM

OSDM is the most feature-complete standard in the set, covering approximately 74% of the EUDIT functional scope across 27 API tag groups. It is the strongest Transmodel-aligned standard, with `x-semantics` annotations on virtually every schema linking fields to named Transmodel concepts. It is the natural starting point for the TS's rail and long-distance coach segments.

### What to adopt

* **Offer → confirm purchase pattern.** The two-step `POST /offers` → `POST /admissions` (or `fulfillments`) flow mirrors the Transmodel SALES OFFER PACKAGE → CUSTOMER PURCHASE lifecycle cleanly. This pattern should be the TS baseline for all booking modes.
* **After-sales offer step.** Before any after-sales transaction (refund, exchange, release), OSDM requires an explicit offer step that returns the financial consequence to the traveller before they commit. This "query before commit" principle is excellent UX and should be universal in the TS.
* **`OnHoldOffer` mechanism.** A time-boxed pre-booking lock (with optional reservation fee) allowing a traveller to hold a specific offer while completing ancillary steps. Maps to SALES TRANSACTION reservation semantics. The TS should adopt this for any mode where seat or slot scarcity is relevant.
* **Exchange flow as a first-class operation.** OSDM distinguishes exchange (same trip, new conditions) from cancel + rebook. This distinction is commercially important and has no equivalent in any other source standard. The TS should define EXCHANGE as a distinct AFTER-SALES TRANSACTION type.
* **`x-semantics` annotation pattern.** Embedding Transmodel concept references directly in the OpenAPI schema is a low-cost, high-value interoperability aid. The TS should mandate an equivalent annotation mechanism on all TS schema objects.
* **TRAVEL ACCOUNT and account-based ticketing.** OSDM's account model (load value, query balance, apply to purchase) is well-structured and maps cleanly to Transmodel FARE CONTRACT and TRAVEL DOCUMENT. The TS should adopt it for any mode supporting stored-value or season products.
* **Graphical seat reservation.** Coach layout and seat-map endpoints (`GET /coachLayouts`, `GET /graphicalReservations`) are domain-specific to rail/coach but the pattern — expose a spatial resource, then reference it in booking — generalises to ferry cabin selection and MaaS hub berth booking.
* **Webhook notification for post-sale changes.** OSDM defines a separate webhook specification for operator-initiated changes (schedule alteration, cancellation, disruption). The TS should adopt this push pattern rather than requiring polling.

### What to avoid

* **Nested schema polymorphism.** `AbstractOfferPart` and `AbstractBookingPart` with deep inheritance chains make client implementation unnecessarily complex. The TS should favour flat, discriminated unions with explicit `type` fields over deep abstract hierarchies.
* **Missing explicit auth endpoint.** OSDM assumes OAuth2 but does not specify a token endpoint. The TS must define a mandatory authentication endpoint (see OMSA lesson below).
* **Non-Transmodel extension concepts without alignment.** `BookingSplit`, `relatedBookingIds`, `accountingRef`, `Reimbursement`, and `Complaint` resources have no defined Transmodel equivalents in OSDM. The TS should either map them explicitly to Transmodel concepts or exclude them until a mapping is established.
* **Coach-specific detail leaking into generic schemas.** IMO-style coach identifiers and NRT-specific fields appear in schemas that are presented as generic. The TS should isolate mode-specific extensions into clearly-labelled extension schemas.
* **Implicit rollback semantics.** OSDM does not specify what happens to an `OnHoldOffer` if payment fails partway through the confirmation step. The TS must define explicit rollback and timeout semantics for every transactional step.

---

## TOMP-API

TOMP-API has the broadest functional coverage in the set at approximately 80%, driven by its MaaS (Mobility-as-a-Service) scope spanning shared bikes, scooters, cars, taxis, and public transport legs in a single API. Its leg execution lifecycle and financial deposit patterns are unique and valuable. Its fare and product model is comparatively thin.

### What to adopt

* **Leg execution lifecycle.** The `{legOperation}-leg` endpoints (`start-leg`, `pause-leg`, `resume-leg`, `end-leg`, `extend-leg`, `postpone-leg`) represent a real-time operational lifecycle with no equivalent in any other source standard. For MaaS and shared-mobility modes, this lifecycle maps to Transmodel DATED VEHICLE JOURNEY execution events and should be adopted in the TS's mobility-execution layer.
* **Two-phase purchase with explicit rollback.** `POST /bookings/{id}/purchase` followed by `POST /bookings/{id}/rollback-purchase` within a `rollbackExpiryTime` window is the most explicit and implementable two-phase commit pattern in the set. The TS should adopt this with a mandatory expiry timestamp on every purchase response.
* **Deposit request and release pattern.** `POST /payment/deposit-request` and `POST /payment/deposit-release` model the hold-and-release of funds for open-ended rentals (where final cost is unknown at booking time). This has no Transmodel equivalent but is operationally necessary for shared mobility and should be introduced as a TS extension with a clear mapping note.
* **`guarantee` schema for TRAVEL GUARANTEE.** TOMP-API's `guarantee` object maps cleanly to Transmodel TRAVEL GUARANTEE (covering TRIP REPAIR GUARANTEE, TRAVEL QUALITY GUARANTEE, and INFORMATION QUALITY GUARANTEE). The TS should adopt this schema as the canonical guarantee representation.
* **Full CUSTOMER ACCOUNT CRUD.** TOMP-API provides create, read, update, delete, and preference-management for traveller accounts. This maps to Transmodel TRANSPORT CUSTOMER and CUSTOMER ACCOUNT. The TS should adopt this as the baseline account management interface.
* **`financialDetail` payment categories.** The `paymentCategory` enum (`DAMAGE`, `LOSS`, `FINE`, `SURGE`, `REFUND`, `DEPOSIT`) captures post-trip financial events that Transmodel does not yet model. These should be adopted as TS extensions with explicit `[no Transmodel equivalent]` notes.
* **Operator and planning metadata.** `GET /operator/information`, `GET /operator/available-assets`, and `GET /planning/offers` provide discoverable service metadata that maps to Transmodel OPERATOR, VEHICLE TYPE, and SERVICE JOURNEY. The TS should standardise this discovery pattern across all modes.

### What to avoid

* **GeoJSON collection responses for booking APIs.** TOMP-API returns booking and leg collections as GeoJSON `FeatureCollection` objects. This conflates geographic representation with booking data and makes schema validation harder. The TS should use plain JSON arrays for booking collections and provide GeoJSON only in dedicated map/geo endpoints.
* **MDS-based geofencing and driving-support endpoints.** Endpoints inherited from the Mobility Data Specification (`GET /geofencing-zones`, `GET /vehicle-types`, driving-support webhooks) are OMF-specific and have no Transmodel equivalent. The TS should exclude these or relegate them to an optional extension module.
* **Thin fare structure.** TOMP-API's fare model does not support corporate discount codes, non-trip products (season passes, stored value), or structured tariff descriptions. The TS must supplement TOMP-API's booking patterns with OSDM's fare and product model.
* **No dedicated auth endpoint.** Like OSDM, TOMP-API assumes OAuth2 but does not specify a token endpoint. This must be resolved at the TS level (see OMSA lesson).
* **Overlapping MaaS scope with OMSA.** Several TOMP-API patterns (especially operator discovery and booking confirmation) duplicate OMSA functionality. The TS must choose one model for MaaS booking rather than carrying both.

---

## OMSA

OMSA achieves approximately 50% functional coverage, focused on MaaS booking orchestration using the OGC API Processes pattern. Its most distinctive and universally applicable contribution is the explicit in-specification authentication endpoint — the only standard in the set to define this. Its process-based execution model is powerful but adds implementation complexity.

### What to adopt

* **Explicit OAuth2 token endpoint.** `POST /oauth/token` with three grant types (`client_credentials`, `password`, `refresh_token`) is defined in-spec with full schema. The TS **must** adopt this as a mandatory endpoint for all conformant implementations. This is the single most portable, operationally critical lesson from any source standard.
* **`GET /processes` + `GET /conformance` machine-readable discovery.** Advertising supported operations and conformance classes as queryable resources is aligned with OGC API and W3C patterns. The TS should adopt this for capability discovery, replacing ad-hoc documentation-driven integration.
* **Two-step execute → confirm for purchase and refund.** OMSA's consistent use of an asynchronous execute step followed by an explicit confirm step for both purchase and refund operations mirrors OSDM's pattern and reinforces it as the correct TS baseline.
* **OGC subscriber / callback pattern.** `POST /subscriber` for registering asynchronous callbacks is a clean webhook registration model. The TS should adopt this alongside (or instead of) OSDM's separate webhook YAML, providing a single unified push-notification mechanism.
* **Process status polling.** For long-running operations, OMSA provides `GET /jobs/{jobID}` status polling as a fallback when callbacks are unavailable. The TS should mandate both the callback and the polling alternative for all asynchronous operations.
* **Structured error responses.** OMSA's error schemas are more consistently structured than OSDM's or TOMP-API's. The TS should define a single canonical error schema (HTTP status + machine-readable code + human-readable message + optional detail) and mandate its use.

### What to avoid

* **`guarantees` field without Transmodel alignment.** The `guarantees` array on package and offer objects is OMSA-specific and not mapped to any Transmodel TRAVEL GUARANTEE subtype. Before adopting, the TS must establish an explicit mapping.
* **OGC Processes overhead for simple synchronous operations.** The full OGC API Processes lifecycle (create job → poll status → retrieve result) is appropriate for batch or long-running operations but is excessive for synchronous offer searches. The TS should reserve this pattern for genuinely asynchronous operations.
* **Absence of trip planning, fare catalogue, complaints, and invoicing.** OMSA covers only MaaS booking orchestration. The TS cannot rely on it for end-to-end coverage and must supplement it from OSDM (fares, after-sales) and FerryGateway (invoicing).
* **MaaS scope overlap with TOMP-API.** As noted under TOMP-API, the TS must select one primary model for MaaS booking. Carrying both OMSA and TOMP-API patterns without reconciliation will produce an incoherent specification.

---

## FerryGateway

FerryGateway covers approximately 58% of the EUDIT functional scope, operating exclusively as XML request/response pairs over SOAP-style HTTP. It is the most domain-specific standard in the set, optimised for ferry operators with passengers, vehicles, cabins, and sailing schedules. Several of its patterns are excellent and should be extracted and generalised regardless of transport mode.

### What to adopt

* **`GetCancelCharge` before `CancelBooking`.** Querying the cancellation penalty before committing to cancellation is the single best after-sales UX pattern in the entire set. The TS should mandate a "query before commit" step for every destructive after-sales operation — not just cancellation — across all modes.
* **Sailing availability embedded in offer response.** `GetSailings` returns availability counts alongside offer data in a single response, reducing client round-trips. The TS should adopt availability-embedded offers as the default, with a separate availability endpoint only for high-frequency polling.
* **`GetPassengerAndVehicleTypes` combined catalogue.** Returning USER PROFILE definitions (passenger types) and VEHICLE TYPE catalogues in one call is efficient and maps cleanly to the Transmodel USER PROFILE + VEHICLE TYPE pair. The TS should adopt combined-catalogue endpoints wherever two closely-related catalogues are always queried together.
* **Cabin/berth accommodation as a first-class booking entity.** FerryGateway models cabin occupancy constraints, accessibility flags, and berth configurations as structured objects. This generalises directly to rail sleeper carriages and could extend to MaaS hub parking/locker allocation. The TS should adopt this pattern as the canonical accommodation-booking model.
* **`GetInvoices` endpoint.** Returning structured invoices via API is absent from OSDM and TOMP-API. The TS should define an invoicing endpoint for all booking modes where a formal fiscal document is required (rail, ferry, coach).
* **Accessibility flags on accommodation and vehicle types.** FerryGateway includes wheelchair accessibility, reduced-mobility flags, and pet accommodation as structured fields rather than free-text notes. The TS should adopt structured accessibility attributes mapped to Transmodel ACCESSIBILITY LIMITATION.

### What to avoid

* **XML-only encoding.** All FerryGateway messages are XML. The TS must use JSON as the primary encoding (following OSDM and TOMP-API) with no XML requirement. FerryGateway implementations will require a translation layer.
* **No standardised authentication.** FerryGateway has no in-specification auth mechanism. The TS's mandatory OAuth2 endpoint (from OMSA) resolves this gap.
* **No webhook or push-notification support.** FerryGateway is entirely synchronous request/response with no provision for operator-initiated change notifications. The TS must layer OSDM/OMSA webhook patterns on top.
* **Ferry-specific attributes without Transmodel equivalents.** IMO vessel number, tidal adjustment flags, `IsHireCar`, `BikeOnBack`, `IsOnlyMinicruise`, and pets as first-class booking entities have no Transmodel mapping. The TS should isolate these in a clearly-labelled ferry extension module rather than promoting them to core schemas.
* **No OGC or machine-readable capability discovery.** FerryGateway has no equivalent of OMSA's `GET /conformance`. The TS should not carry forward SOAP-era implicit-capability assumptions; all conformant implementations must expose a capability discovery endpoint.

---

## BoB

BoB (Booking of Bookings) is the most minimal standard in the set, covering approximately 22% of the EUDIT functional scope across only four endpoints. Its value lies not in breadth but in its ticket integrity and B2B distribution architecture. The MTB (Mobile Ticket Binary) signing model is the strongest approach to non-repudiation and validation in the entire source set.

### What to adopt

* **MTB (Mobile Ticket Binary) signing model.** MTB provides a signed, offline-verifiable binary ticket format with a clear chain of custody between issuing system, distributor, and validator. The TS should reference MTB (or an equivalent signed token standard) as the mandatory fulfillment format for any use case requiring offline validation.
* **Booking status lifecycle.** BoB's status progression (`pending` → `confirmed` → `completed` / `miss` / `cancelled`) maps cleanly to Transmodel CUSTOMER PURCHASE STATUS. The TS should adopt this five-state lifecycle as the canonical booking status model.
* **Explicit B2B distributor ↔ operator separation.** BoB architecturally separates the distributor role (sells to traveller) from the operator role (delivers the journey). This maps to Transmodel SALES CHANNEL / DISTRIBUTION CHANNEL and OPERATOR. The TS should mandate this separation in its role model.
* **Ticket validation endpoints (inspector + gate).** BoB's companion MTB sub-specification defines validation flows for both human inspectors and automated gates. The TS needs a validation model and should adopt BoB's two-actor validation pattern.
* **Short, role-scoped API surface.** BoB's four-endpoint design demonstrates that a distribution/validation interface does not need to replicate the full booking API. The TS should design role-scoped API profiles (distributor profile, operator profile, validator profile) in addition to the full conformance class.

### What to avoid

* **`miss` status without Transmodel equivalent.** The `miss` status (traveller no-show) has no direct CUSTOMER PURCHASE STATUS mapping in Transmodel. Before including it in the TS, an explicit mapping or extension must be defined.
* **Four-endpoint surface as a standalone API.** BoB's minimal scope is insufficient for end-to-end interoperability. It must be combined with richer booking and fare APIs from OSDM or TOMP-API; it cannot stand alone in the TS.
* **MTB format complexity as the only option.** MTB's binary signing model may be too complex for lightweight MaaS or multimodal token scenarios. The TS should provide a simpler signed-JSON token alternative for contexts where full MTB is disproportionate.
* **No trip planning, fare catalogue, or after-sales.** Like OMSA, BoB covers only a slice of the end-to-end journey. The TS must not extend BoB's scope assumptions when defining conformance classes.

---

## InterMOD

[TO DISCUSS / COMPLETE] — No OpenAPI specification or formal mapping document was available during Phase 1. This section will be completed once InterMOD specification materials are obtained and reviewed.

---

## Cross-Cutting Synthesis

The table below summarises the recommended TS decision for each cross-cutting concern, identifying which source standard(s) provide the best pattern.

| Concern | Recommended approach | Primary source |
|---|---|---|
| Authentication | Mandatory `POST /oauth/token` with `client_credentials`, `password`, `refresh_token` grant types | OMSA |
| Offer discovery | `POST /offers` returning SALES OFFER PACKAGE array with embedded availability | OSDM |
| Pre-booking hold | `OnHoldOffer` with time-boxed lock and optional fee | OSDM |
| Purchase confirmation | Two-phase execute → confirm with `rollbackExpiryTime` | TOMP-API |
| After-sales query | Mandatory offer step before any refund, exchange, or release | OSDM |
| Exchange | First-class EXCHANGE operation distinct from cancel + rebook | OSDM |
| Cancellation charge query | `GetCancelCharge`-style query before `CancelBooking` (generalised to all destructive after-sales) | FerryGateway |
| Ticket fulfillment | MTB signed binary for offline validation; signed-JSON token as lightweight alternative | BoB |
| Booking status | Five-state lifecycle: `pending` → `confirmed` → `completed` / `miss` / `cancelled` | BoB |
| Leg execution (MaaS) | `{legOperation}-leg` lifecycle: start / pause / resume / end / extend / postpone | TOMP-API |
| Financial deposits | Deposit-request / deposit-release pattern for open-ended rentals | TOMP-API |
| Push notifications | Webhook registration via `POST /subscriber`; OSDM webhook spec as supplementary reference | OMSA + OSDM |
| Capability discovery | `GET /processes` + `GET /conformance` machine-readable capability advertisement | OMSA |
| Invoicing | `GetInvoices`-style endpoint for all modes requiring fiscal documents | FerryGateway |
| Accommodation booking | Cabin/berth as first-class entity with occupancy and accessibility constraints | FerryGateway |
| Transmodel annotation | `x-semantics` (or equivalent) on every TS schema field | OSDM |
| B2B distribution | Explicit distributor ↔ operator role separation with scoped API profiles | BoB |
