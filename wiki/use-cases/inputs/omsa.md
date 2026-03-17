# OMSA Use Cases

OMSA (Open MaaS Standard Architecture, also referred to as the Common Booking API) is a Mobility-as-a-Service interoperability standard built on the **OGC API Processes** framework. Every mutating operation is expressed as a named process invoked via `POST /processes/{processId}/execute`, and all result sets are exposed as OGC-style feature collections via `GET /collections/{collectionId}/items`. Process executions may be either synchronous or asynchronous; in the async case the caller supplies a `subscriber` callback URI and the server posts the result when the job completes.

OMSA covers multimodal door-to-door trip booking: offer search, offer selection, package management, traveller management, physical asset assignment, ancillary services, purchase (single-phase and two-phase), cancellation, refund handling, and travel document retrieval. Its closest peer in the EUDIT scope is TOMP-API, which also uses the OGC Processes pattern.

---

## Authentication

### obtain access token

A client application obtains a Bearer access token from the OMSA authorisation server before calling any authenticated endpoint. Supports `client_credentials`, `password`, and `refresh_token` grant types; the mTLS variant derives identity directly from the client certificate (O or CN field), ignoring request body credentials.

**Comparison with TOMP-API:** The OMSA and TOMP-API `POST /oauth/token` implementations are near-identical — same three grant types, same mTLS variant (certificate O or CN field; body parameters ignored), same `application/x-www-form-urlencoded` encoding, and the spec description text is essentially word-for-word the same. TOMP-API additionally exposes `POST /connect/token` (OpenID Connect / dataspace JWT) which OMSA does not have. In practice, an implementation of one can be adapted to the other with minimal effort on the `/oauth/token` path.

**Endpoint(s):** `POST /oauth/token`
**Unique to OMSA:** No — TOMP-API also specifies `POST /oauth/token` with the same grant types and mTLS variant, plus `POST /connect/token` for OIDC. OSDM assumes an external IdP; BoB uses TLS client certificate + JWT (`GET /auth/{entityId}`) rather than OAuth.

---

## API Discovery

The following endpoints implement the OGC API landing-page and conformance pattern. They require no authentication (`OpenData` security scheme) and allow generic clients to introspect the server before issuing any business calls.

### retrieve landing page

A client (human or machine) retrieves the root landing page of the OMSA server, which describes how the API must be used and provides hypermedia links to all sub-resources. An `f=html` query parameter requests a human-readable HTML rendering.

**Endpoint(s):** `GET /`
**Unique to OMSA:** No — TOMP-API also exposes an OGC API landing page.

---

### retrieve api definition

A client retrieves the OpenAPI document that formally describes this server instance (i.e. the OMSA YAML spec itself). Useful for automated client-code generation and integration testing.

**Endpoint(s):** `GET /api`
**Unique to OMSA:** No — standard OGC API pattern, shared with TOMP-API.

---

### retrieve conformance declaration

A client retrieves the list of OGC conformance classes the server implements (e.g. `ogcapi-processes-1/1.0/conf/core`, callback conformance class). A generic MaaS aggregator uses this to decide which optional features (async execution, dismissal) it may rely on.

**Endpoint(s):** `GET /conformance`
**Unique to OMSA:** No — TOMP-API also declares OGC conformance classes.

---

### list available collections

A client retrieves metadata for all data collections exposed by this server (offers, packages, legs, assets, ancillaries, travel documents, payments, support tickets, refund options, etc.). Returns a `collections` object with hypermedia links.

**Endpoint(s):** `GET /collections`
**Unique to OMSA:** No — standard OGC API Features / Processes pattern, shared with TOMP-API.

---

### describe a collection

A client retrieves the metadata descriptor for a single named collection, including its supported coordinate reference systems, data type, and links to the items endpoint. Used to validate assumptions before querying collection items.

**Endpoint(s):** `GET /collections/{collectionId}`
**Unique to OMSA:** No — standard OGC API pattern, shared with TOMP-API.

---

### list available processes

A client retrieves a summary catalogue of every OMSA process available on this server (e.g. `search-offers`, `purchase-package`, `cancel-package`). Each summary includes the process id, version, supported job-control options (sync/async/dismiss), and a link to the full process description.

**Endpoint(s):** `GET /processes`
**Unique to OMSA:** No — TOMP-API also uses `GET /processes` from the OGC API Processes standard.

---

### describe a process

A client retrieves the full input/output schema for a specific OMSA process by its `processID`. This allows a client to discover the exact JSON structure required before calling the execute endpoint. Valid `processID` values are the fixed enumeration of named OMSA processes (e.g. `search-offers`, `add-traveller`, `cancel-package`).

**Endpoint(s):** `GET /processes/{processID}`
**Unique to OMSA:** No — standard OGC API Processes pattern; TOMP-API uses the same mechanism.

---

### retrieve external data sources

A client retrieves the list of external data sources (NeTEx, GBFS, etc.) referenced in OMSA requests and responses. This allows a client to resolve external identifiers (stop places, journey IDs) independently and verify data without requiring the OMSA server to embed all referenced data inline.

**Endpoint(s):** `GET /collections/datasources/items`
**Unique to OMSA:** No — TOMP-API has equivalent discovery for referenced data formats. The specific NeTEx/GBFS registry pattern is however more explicit in OMSA than in other standards.

---

## Offer Management

### search for offers

The TRANSPORT CUSTOMER (or a MaaS Platform acting on their behalf) searches for available SALES OFFER PACKAGEs by submitting a TRAVEL SPECIFICATION — origin, destination, optional via points, intended departure or arrival time — together with one or more TRANSPORT CUSTOMER or USER PROFILE descriptions and optionally travelling assets (e.g. a bicycle). The server returns an `offerCollection` of priced SALES OFFER PACKAGEs, each containing legs, FARE PRODUCTs, ancillaries, and an expiry time. Supports synchronous response and asynchronous callback (OGC subscriber pattern).

**Endpoint(s):** `POST /processes/search-offers/execute`
**Unique to OMSA:** No — equivalent to OSDM `POST /offers`, TOMP-API offer search, and BoB offer search. The OGC Processes framing is OMSA-specific but the functional intent is shared.

---

### select offers into a package

After receiving an offer collection, the TRANSPORT CUSTOMER (or MaaS Platform) selects one or more specific SALES OFFER PACKAGEs by their IDs to be assembled into a CUSTOMER PURCHASE PACKAGE. The server creates or updates a `package` object in `selected` status containing the chosen offers and their legs. This is a prerequisite step before purchase.

**Endpoint(s):** `POST /processes/select-offers/execute`
**Unique to OMSA:** No — equivalent to the OSDM pre-booking / offer selection step and the TOMP-API two-phase booking initiation / planning confirmation step with offer reference.

---

## Traveller Management

### add traveller to a package

The MaaS Platform adds a new TRANSPORT CUSTOMER (identified or anonymous USER PROFILE) to an existing package. The traveller's entitlements, commercial profiles, licences, and access-right requirements are included so the OPERATOR can validate eligibility. The server returns the updated `package`.

**Endpoint(s):** `POST /processes/add-traveller/execute`
**Unique to OMSA:** No — equivalent functionality exists in TOMP-API (booking details / passenger update). OSDM embeds passenger data within the booking flow rather than as a separate step.

---

### update traveller on a package

The MaaS Platform updates the personal attributes or entitlements of a TRANSPORT CUSTOMER already associated with a package (e.g. correcting age, adding a disability card, updating a commercial profile). The server returns the updated `package`.

**Endpoint(s):** `POST /processes/update-traveller/execute`
**Unique to OMSA:** No — mirrors TOMP-API passenger update functionality.

---

### remove traveller from a package

The MaaS Platform removes a TRANSPORT CUSTOMER from a package (e.g. a traveller withdraws before purchase is confirmed). The server returns the updated `package`. If the traveller was the sole occupant, downstream effects on pricing and validity conditions apply.

**Endpoint(s):** `POST /processes/remove-traveller/execute`
**Unique to OMSA:** No — mirrors TOMP-API passenger management.

---

## Asset and Ancillary Assignment

### assign asset to a leg

The MaaS Platform (or OPERATOR) assigns a specific physical VEHICLE or asset (car, bicycle, parking bay, charging point) to a named leg within a package. The `assetInput` references both the package, the leg, and the asset to be assigned; an optional `replaceAssetId` enables asset substitution. The server returns the updated `package`.

**Endpoint(s):** `POST /processes/assign-asset/execute`
**Unique to OMSA:** No — TOMP-API has equivalent asset assignment in its legs/booking lifecycle. FerryGateway has vehicle-spot assignment. The explicit replace-asset pattern is closest to TOMP-API.

---

### assign ancillary to a leg

The MaaS Platform assigns a supplementary service (ANCILLARY — comparable to a LOCAL SERVICE or SUPPLEMENT PRODUCT) to a specific leg within a package. Examples include luggage, a meal, a helmet, or a child seat. An optional `replaceAncillaryId` enables ancillary substitution. The server returns the updated `package`.

**Endpoint(s):** `POST /processes/assign-ancillary/execute`
**Unique to OMSA:** No — TOMP-API and OSDM both support adding optional ancillary/extra products to a booking. The concept is broadly shared.

---

### list available assets for a leg

A client retrieves a GeoJSON feature collection of physical assets (vehicles, parking bays, charging points, bicycles) that are available for a specific leg within a package. Assets are returned as GeoJSON `Feature` objects, enabling geo-based filtering via a bounding box (`bbox`) parameter.

**Endpoint(s):** `GET /collections/assets/items`
**Unique to OMSA:** No — TOMP-API has equivalent asset availability endpoints. FerryGateway provides vehicle slot availability. The GeoJSON/bbox spatial query flavour is characteristic of OMSA/TOMP-API vs. OSDM.

---

### list available ancillaries for a leg

A client retrieves the catalogue of ancillary services available for a specific leg within a package — the pool from which `assign-ancillary` selections may be made. Results are filtered by `packageId` and optionally `legId`.

**Endpoint(s):** `GET /collections/ancillaries/items`
**Unique to OMSA:** No — OSDM and TOMP-API expose equivalent optional-product catalogues.

---

## Purchase

### purchase offers (single-phase, from offer selection)

The MaaS Platform directly purchases a set of pre-selected SALES OFFER PACKAGEs in a single step. The `purchaseOffersInput` combines the offer selection criteria with full traveller and travel-specification data. On success the server returns a `package` in `confirmed` status. This is a shortcut that collapses search → select → purchase into one call.

**Endpoint(s):** `POST /processes/purchase-offers/execute`
**Unique to OMSA:** No — equivalent to OSDM `POST /bookings` (direct booking from offer IDs). TOMP-API has a comparable single-step booking confirm.

---

### purchase package (single-phase, from search criteria)

The MaaS Platform directly purchases a package by submitting combined search and traveller criteria, without a prior `search-offers` round-trip. The `purchasePackageInput` extends `searchOfferInput` and triggers an atomic search-and-buy. The server returns a `package` in `confirmed` status.

**Endpoint(s):** `POST /processes/purchase-package/execute`
**Unique to OMSA:** No — TOMP-API supports a comparable single-call booking creation.

---

### initiate two-phase package purchase

The MaaS Platform initiates a two-phase (prepare/commit) purchase of a package to accommodate atomic multi-modal transactions. Phase 1 (`2-phase-purchase-package`) reserves the package and moves it to `pending` status; phase 2 (`confirm-package`) finalises the purchase. The split is necessary when the MaaS Platform must coordinate simultaneous purchases across multiple operators.

**Endpoint(s):** `POST /processes/2-phase-purchase-package/execute`
**Unique to OMSA:** No — TOMP-API explicitly defines the same two-phase commit pattern for multimodal purchases. This is a key shared feature of OGC-based MaaS APIs.

---

### confirm a pending package

The MaaS Platform confirms a package that was previously placed in `pending` status (via `2-phase-purchase-package`), completing the two-phase purchase transaction. The server moves the package to `confirmed` status and issues TRAVEL DOCUMENTs. If the confirmation is not received before the package's `expiryTime`, the package transitions to `expired`.

**Endpoint(s):** `POST /processes/confirm-package/execute`
**Unique to OMSA:** No — mirrors the TOMP-API booking confirmation step in the two-phase commit workflow.

---

### release a package (abort without purchase)

The MaaS Platform explicitly releases (discards) a package that was created but will not be purchased — for example, when the end user abandons the booking flow. The server moves the package to `released` status, freeing any held inventory. This is a housekeeping operation complementary to the two-phase purchase.

**Endpoint(s):** `POST /processes/release-package/execute`
**Unique to OMSA:** No — TOMP-API has an equivalent abort/dismiss step. OSDM provides a comparable pre-booking cleanup operation.

---

### extend package expiry time

The MaaS Platform requests an extension of the confirmation deadline for a `pending` package. The reason for the extension must be supplied (`purchase_pending`, `payment_pending`, or `other`). This prevents the package from expiring while the end user is mid-payment or while back-end processing is still ongoing.

**Endpoint(s):** `POST /processes/extend-expiry-time/execute`
**Unique to OMSA:** No — TOMP-API has an equivalent expiry extension mechanism. The `extensionReason` enumeration is an OMSA elaboration.

---

## After-Sales

### cancel a package

The TRANSPORT CUSTOMER or MaaS Platform cancels a previously confirmed CUSTOMER PURCHASE PACKAGE. The `packageInput` references the package to be cancelled. The server evaluates the applicable CANCELLING conditions (fees, refund eligibility) defined in the package's validity parameters and returns the updated `package` in `cancelled` status.

**Endpoint(s):** `POST /processes/cancel-package/execute`
**Unique to OMSA:** No — equivalent to OSDM `POST /bookings/{id}/refundOffers` + accept refund, BoB cancellation, and TOMP-API booking cancellation. Cancellation is a universal booking lifecycle operation.

---

### list available refund options

A client retrieves the refund options currently available for a package, optionally filtered by leg, traveller, or ancillary. Each `refundOption` describes the type of refund (`package_refund`, `remove_traveller`, `remove_ancillary`), the financial consequences (charges/refunds), and any applicable CANCELLING or EXCHANGING conditions. The client uses this collection to decide which option to claim.

**Endpoint(s):** `GET /collections/refund-options/items`
**Unique to OMSA:** No — OSDM has a dedicated refund-offer retrieval step; BoB and TOMP-API both expose after-sales options. The OGC collection framing is OMSA/TOMP-API specific.

---

### claim a refund option

The MaaS Platform submits a claim for a specific refund option (identified by `optionId`) from the available refund options collection. The claim is accepted provisionally and the package transitions to `refund_claimed` status. A second confirmation step is required before the refund is disbursed, enabling a two-phase refund transaction consistent with the purchase model.

**Endpoint(s):** `POST /processes/claim-refund-option/execute`
**Unique to OMSA:** No — OSDM uses a comparable accept-refund-offer step. The two-phase (claim → confirm) refund pattern parallels TOMP-API's after-sales flow.

---

### confirm a refund claim

The MaaS Platform confirms a previously submitted refund claim, completing the two-phase refund transaction. The server processes the financial settlement and moves the package to `refunded` status. This step allows the MaaS Platform to co-ordinate simultaneous refunds across multiple operators before committing any single one.

**Endpoint(s):** `POST /processes/confirm-refund-option/execute`
**Unique to OMSA:** No — TOMP-API has an equivalent two-phase after-sales confirmation step. The specific claim/confirm split is closest to TOMP-API.

---

## Travel Documents

### retrieve travel documents for a package

A TRANSPORT CUSTOMER or MaaS Platform retrieves the TRAVEL DOCUMENTs (tickets, barcodes, QR codes, Aztec codes, NFC tokens, Bluetooth keys, external links) issued for a confirmed package. Results are filtered by `packageId` and optionally `legId`. Each document carries `startValidity`, `endValidity`, and a `typeOfTravelDocument` discriminator.

**Endpoint(s):** `GET /collections/travel-documents/items`
**Unique to OMSA:** No — equivalent to OSDM `GET /bookings/{id}/fulfillments`, BoB ticket retrieval, and FerryGateway boarding pass retrieval. The breadth of document types (including device-based media such as Bluetooth/NFC) reflects OMSA's MaaS heritage.

---

## Summary table

The table below cross-references every OMSA process and collection endpoint against the Transmodel concepts they realise and their uniqueness status.

| Use Case | Endpoint | Primary Transmodel Concept(s) | Unique to OMSA |
|----------|----------|-------------------------------|----------------|
| obtain access token | `POST /oauth/token` | — | No |
| retrieve landing page | `GET /` | — | No |
| retrieve api definition | `GET /api` | — | No |
| retrieve conformance declaration | `GET /conformance` | — | No |
| list available collections | `GET /collections` | — | No |
| describe a collection | `GET /collections/{collectionId}` | — | No |
| list available processes | `GET /processes` | — | No |
| describe a process | `GET /processes/{processID}` | — | No |
| retrieve external data sources | `GET /collections/datasources/items` | — | No |
| search for offers | `POST /processes/search-offers/execute` | TRAVEL SPECIFICATION, SALES OFFER PACKAGE | No |
| select offers into a package | `POST /processes/select-offers/execute` | CUSTOMER PURCHASE PACKAGE, SALES OFFER PACKAGE | No |
| add traveller to a package | `POST /processes/add-traveller/execute` | TRANSPORT CUSTOMER | No |
| update traveller on a package | `POST /processes/update-traveller/execute` | TRANSPORT CUSTOMER | No |
| remove traveller from a package | `POST /processes/remove-traveller/execute` | TRANSPORT CUSTOMER | No |
| assign asset to a leg | `POST /processes/assign-asset/execute` | VEHICLE | No |
| assign ancillary to a leg | `POST /processes/assign-ancillary/execute` | LOCAL SERVICE | No |
| list available assets for a leg | `GET /collections/assets/items` | VEHICLE | No |
| list available ancillaries for a leg | `GET /collections/ancillaries/items` | LOCAL SERVICE | No |
| purchase offers (single-phase) | `POST /processes/purchase-offers/execute` | CUSTOMER RESERVATION CREATION EVENT | No |
| purchase package (single-phase) | `POST /processes/purchase-package/execute` | CUSTOMER RESERVATION CREATION EVENT | No |
| initiate two-phase package purchase | `POST /processes/2-phase-purchase-package/execute` | CUSTOMER RESERVATION CREATION EVENT | No |
| confirm a pending package | `POST /processes/confirm-package/execute` | CUSTOMER PRODUCT PURCHASE EVENT | No |
| release a package | `POST /processes/release-package/execute` | — | No |
| extend package expiry time | `POST /processes/extend-expiry-time/execute` | PURCHASE WINDOW | No |
| cancel a package | `POST /processes/cancel-package/execute` | CUSTOMER RESERVATION CANCELLATION EVENT | No |
| list available refund options | `GET /collections/refund-options/items` | EXCHANGING / CANCELLING | No |
| claim a refund option | `POST /processes/claim-refund-option/execute` | EXCHANGING / CANCELLING | No |
| confirm a refund claim | `POST /processes/confirm-refund-option/execute` | EXCHANGING / CANCELLING | No |
| retrieve travel documents | `GET /collections/travel-documents/items` | TRAVEL DOCUMENT | No |
