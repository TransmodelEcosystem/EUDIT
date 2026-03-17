# BoB Use Cases

BoB (Biljetter och Bokning — Tickets and Booking) is a **B2B backend-to-backend ticketing interoperability ecosystem**. It was developed under Samtrafiken auspices as a Nordic/European standard for exchanging signed Mobile Ticketing Blocks (MTBs) between transport operators, distributors, and validators.

BoB is structured as a **suite of 9 sub-APIs**, each covering a distinct layer of the ticketing lifecycle:

| Sub-API | Version | Responsibility |
|---------|---------|----------------|
| Authentication | v1.3.3 | JWT token issuance via TLS client certificate |
| Participant Metadata | v2.3.1 | Central AB-hosted registry of keys, endpoints, and issuer constraints |
| Product | v3.4.0 | FARE PRODUCT search, category lookup, and manifest creation |
| Ticket | v3.4.0 | Full ticket lifecycle: issuance, status, activation, refund, revocation |
| Token | v1.5.0 | Physical/logical token (smart card, NFC) key lookup and revocation |
| Traveller | v3.0.0 | TRANSPORT CUSTOMER account, wallet, notifications, MTB activation |
| Device | v2.1.1 | Validator device key provisioning (KDK — Key Derivation Keys) |
| Inspection | v2.3.0 | On-board ticket inspection (online and offline batch) |
| Validation | v3.4.0 | Gate/validator ticket validation, blacklist, whitelist, TICKLE macros, fraud check |
| Booking | v2.0.1 | Seat-reserved B2B booking lifecycle (preliminary → confirmed/cancelled) |

Authentication in BoB is not OAuth-based. Every participant authenticates using a **TLS client certificate**; the Authentication API exchanges that certificate fingerprint for a short-lived JWT (the `X-BoB-AuthToken` header used by all other APIs). The JWT carries a mandatory `bobAuthZ` authorisation class claim that scopes what the bearer may call.

---

## Architecture overview

BoB operates on a **federated participant model** coordinated by an **Administering Body (AB)**.

* Every organisation that issues, sells, or validates MTBs must be a registered **participant** with a numeric `pid` (participant identifier).
* The AB hosts the Participant Metadata registry — a signed JWS document listing every participant's public signing keys, API endpoint URLs, and issuer-signature constraints.
* Participants discover each other's endpoints and keys exclusively through this registry; there is no out-of-band endpoint discovery.
* The **MTB (Mobile Ticketing Block)** is a CBOR/COSE binary ticket format (defined in MTS1). Each MTB carries one or more FARE PRODUCT entitlements, a cryptographic issuer signature (EC/RSA JWK), and optionally a device signature (for validator-bound tickets). The MTB is the TRAVEL DOCUMENT that circulates between all parties.
* **Issuer signature constraints** in the registry control which participants may countersign MTBs issued by another participant — enabling sales-channel delegation without losing accountability.
* **Ticket bundles** group tickets issued against the same manifest into a single settlement unit, carrying a `settlementId` for billing reconciliation between operators and distributors.

How the sub-APIs relate to each other in a typical purchase-and-travel flow:

```
Auth API          → every participant calls this first to obtain JWT
Participant Metadata → participants look up each other's keys and endpoints
Product API       → sales channel fetches FARE PRODUCTs and creates manifest
Ticket API        → operator issues tickets from manifest; manages lifecycle
Token API         → looks up smart-card/NFC token keys; checks revocation
Traveller API     → sales channel manages TRANSPORT CUSTOMER account and wallet
Booking API       → operator handles seat-reserved journeys alongside tickets
Device API        → validators download KDK for offline device-signed MTBs
Validation API    → validator gates record validation events, check blacklist
Inspection API    → on-board inspectors record inspection events
```

---

## Authentication API (v1.3.3)

The Authentication API is the entry point for every BoB interaction. A participant presents its TLS client certificate during the HTTPS handshake; the API server or reverse proxy extracts the SHA-1 certificate fingerprint (the `X-SSL-Client-SHA1` header) and mints a short-lived JWT for the requesting entity. The resulting JWT is passed as the `X-BoB-AuthToken` header in all subsequent API calls.

The JWT follows RFC 7519 and adds two BoB-specific claims: `bobHok` (holder-of-key — the certificate fingerprint, binding the token to the TLS identity) and `bobAuthZ` (authorisation class, e.g. `val` for validators, which gates what endpoints the bearer may call).

### get authentication token

A participant entity (identified by `entityId`) requests a JWT by presenting its registered TLS client certificate. The server returns the token in both compact JWS serialisation and as parsed header/payload objects to ease client-side parsing without JWT library dependencies.

**Endpoint(s):** `GET /auth/{entityId}`
**Unique to BoB:** Yes — TLS client certificate as the exclusive authentication mechanism, with the SHA-1 fingerprint embedded in the JWT as a holder-of-key (`bobHok`) claim. OMSA and TOMP-API also support mTLS as an optional variant alongside OAuth 2.0, but BoB has no OAuth path at all: the certificate is the only recognised credential, making it the only certificate-first (no-OAuth) standard in the EUDIT set.

---

## Participant Metadata API (v2.3.1)

The Participant Metadata API is hosted exclusively by the Administering Body. It is the authoritative source of truth for the entire BoB ecosystem: public keys, API endpoint URLs, issuer-signature constraints, and participant identity. Every participant is responsible for keeping their own metadata current. New participants and removal of existing participants are handled out-of-band with the AB; this API only manages metadata for already-registered participants.

The full participant file is served as a signed JWS document (RFC 7515) so that recipients can verify its integrity without trusting the transport layer.

### get full participant metadata file

Retrieve the complete signed JWS document containing metadata for all registered participants. Validators and sales systems download this periodically to keep their local key stores current.

**Endpoint(s):** `GET /participantMetadata`
**Unique to BoB:** Yes — a centralised AB-hosted signed registry of all ecosystem participants is unique to BoB. No other EUDIT standard has an equivalent federated trust registry.

### get participant metadata by pid

Retrieve all metadata for a single participant: organisation name, domain name, MTB public keys, authtoken public keys, API interface endpoints, and issuer signature constraints.

**Endpoint(s):** `GET /participantMetadata/{pid}`
**Unique to BoB:** Yes — same rationale as above; the `pid`-keyed federated participant model is BoB-specific.

### manage participant domain name

A participant registers, updates, or removes the FQDN associated with their `pid`. The domain name enables dynamic key fetching via DNS (MTS1 §2.4) as an alternative to the central registry.

**Endpoint(s):** `GET /participantMetadata/{pid}/domainName`, `PUT /participantMetadata/{pid}/domainName`, `DELETE /participantMetadata/{pid}/domainName`
**Unique to BoB:** Yes — DNS-based dynamic key discovery keyed to a participant's domain is a BoB-specific mechanism.

### manage MTB public keys

A participant publishes, retrieves, updates (emergency only), or deletes the EC/RSA public keys used to verify the issuer signature on MTBs they have issued. Keys are immutable by design; the update endpoint is reserved for correcting erroneous entries only.

**Endpoint(s):** `GET /participantMetadata/{pid}/mtbPublicKey`, `POST /participantMetadata/{pid}/mtbPublicKey`, `GET /participantMetadata/{pid}/mtbPublicKey/{kid}`, `PUT /participantMetadata/{pid}/mtbPublicKey/{kid}`, `DELETE /participantMetadata/{pid}/mtbPublicKey/{kid}`
**Unique to BoB:** Yes — MTB signing keys are a BoB-specific concept tied to the MTB cryptographic format.

### manage authtoken public keys

A participant publishes, retrieves, updates (emergency only), or deletes the EC/RSA public keys used to verify the JWTs that participant's Authentication API issues.

**Endpoint(s):** `GET /participantMetadata/{pid}/authtokenPublicKey`, `POST /participantMetadata/{pid}/authtokenPublicKey`, `GET /participantMetadata/{pid}/authtokenPublicKey/{kid}`, `PUT /participantMetadata/{pid}/authtokenPublicKey/{kid}`, `DELETE /participantMetadata/{pid}/authtokenPublicKey/{kid}`
**Unique to BoB:** Yes — same rationale; authtoken key management is part of the BoB-specific TLS+JWT authentication scheme.

### manage interface endpoints

A participant registers, retrieves, updates, or deletes the API endpoint URLs for each sub-API type they implement (e.g. `ticket`, `validation`, `traveller`). Other participants use these entries to discover where to call each API.

**Endpoint(s):** `GET /participantMetadata/{pid}/interfaceEndpoint`, `POST /participantMetadata/{pid}/interfaceEndpoint`, `GET /participantMetadata/{pid}/interfaceEndpoint/{endpointId}`, `PUT /participantMetadata/{pid}/interfaceEndpoint/{endpointId}`, `DELETE /participantMetadata/{pid}/interfaceEndpoint/{endpointId}`
**Unique to BoB:** Yes — the AB-hosted central registry as the sole mechanism for cross-participant endpoint discovery is unique to BoB. TOMP-API and OMSA use the OGC API self-describing interface pattern (`GET /` landing page, `GET /api`, `GET /conformance`) for endpoint and capability discovery; BoB instead requires all participants to look up each other's endpoints exclusively from the AB-signed registry file.

### manage issuer signature constraints

A participant declares which other participants (by `pid`) are authorised to countersign MTBs the declaring participant has issued, and with what maximum signature lifetime. This enables a sales channel to resell another operator's FARE PRODUCT while keeping the operator in control of who may sign on their behalf.

**Endpoint(s):** `GET /participantMetadata/{pid}/issuerSignatureConstraint`, `POST /participantMetadata/{pid}/issuerSignatureConstraint`, `GET /participantMetadata/{pid}/issuerSignatureConstraint/{acceptablePid}`, `PUT /participantMetadata/{pid}/issuerSignatureConstraint/{acceptablePid}`, `DELETE /participantMetadata/{pid}/issuerSignatureConstraint/{acceptablePid}`
**Unique to BoB:** Yes — the issuer-signature constraint mechanism (delegated signing rights) is unique to BoB's MTB trust model.

### manage participant info

A participant publishes, retrieves, or deletes free-form descriptive metadata about themselves (as defined in the BoB participant-info JSON schema).

**Endpoint(s):** `GET /participantMetadata/{pid}/participantInfo`, `PUT /participantMetadata/{pid}/participantInfo`, `DELETE /participantMetadata/{pid}/participantInfo`
**Unique to BoB:** Yes — part of the AB participant registry model.

---

## Product API (v3.4.0)

The Product API is provided by each operator/issuer participant. It exposes FARE PRODUCT discovery, categorisation, and manifest creation. The manifest is the signed binary artefact (an MTB template) that is subsequently submitted to the Ticket API to issue actual tickets.

Product search supports three spatial filter modes — **area** (geographic circles/polygons), **group** (zone-based), and **route** (ordered stop sequence) — plus traveller categories, generic categories (fare class, transport mode), discount codes, and temporal constraints. Servers are not required to support all combinations.

### search products by filter (body)

Submit a structured `productFilter` to retrieve FARE PRODUCTs matching the given spatial, temporal, categorical, and property constraints. Returns a list of `productSetAlternatives`, each a candidate set of products that together cover the requested journey.

**Endpoint(s):** `POST /product`
**Unique to BoB:** Partially — product/offer search by origin, destination, traveller category, and fare class is a core capability in OSDM and TOMP-API; OMSA `search-offers/execute` is also functionally comparable. However, OMSA and TOMP-API require a TRAVEL SPECIFICATION (trip pattern) as input and do not expose a standalone product-catalogue search independent of a journey; BoB's Product API can be queried without a pre-planned itinerary. The BoB-specific elements are the participant-scoped `pid` context and the binary MTB output downstream.

### search products by query parameters (simplified)

A simplified GET variant of the product filter for testing and simple TVM (Ticket Vending Machine) scenarios; accepts product, fare, and traveller category IDs and origin/destination stop area IDs as query parameters.

**Endpoint(s):** `GET /product`
**Unique to BoB:** Yes — a simplified GET query-parameter product search independent of any trip specification or journey plan has no equivalent in OMSA or TOMP-API, which rely on external data sources (NeTEx, GBFS) and have no comparable in-spec standalone product query. OSDM's `GET /product-tags` and `POST /product-search` serve related but different purposes.

### get product by identifier

Retrieve detailed information about a specific FARE PRODUCT by its `productId`, which may be a static well-known identifier or a dynamic identifier with expiry produced by a filter operation.

**Endpoint(s):** `GET /product/{productId}`
**Unique to BoB:** Yes — a standalone endpoint to retrieve a single FARE PRODUCT by its identifier has no equivalent in TOMP-API or OMSA, neither of which exposes an in-spec product-retrieval endpoint independent of an offer-search or booking flow.

### get generic categories

Retrieve all supported generic category names and their allowed values (e.g. `fareClass: [firstClass, secondClass]`, `transportMode: [bus, rail]`). Standardised category names are defined in the BoB schema repository; proprietary extensions are also permitted.

**Endpoint(s):** `GET /productcat/generic`
**Unique to BoB:** No — category enumeration is a common ancillary pattern. Comparable endpoints include OSDM `GET /product-tags` (product tag taxonomy), the OMSA process catalogue (`GET /processes`), and TOMP-API operator reference data.

### get product categories (deprecated)

Retrieve the list of product categories (e.g. `singleTrip`, `dayPass`, `30days`). Deprecated in favour of generic categories.

**Endpoint(s):** `GET /productcat/product`
**Unique to BoB:** No.

### get fare categories (deprecated)

Retrieve the list of fare categories (e.g. `night`, `1Class`). Deprecated in favour of generic categories.

**Endpoint(s):** `GET /productcat/fare`
**Unique to BoB:** No.

### get traveller categories

Retrieve the list of TRANSPORT CUSTOMER categories (e.g. `Adult`, `Youth`, `Family`) with their eligibility conditions (age ranges, required proof documents).

**Endpoint(s):** `GET /productcat/traveller`
**Unique to BoB:** No — traveller/passenger category enumeration exists in OSDM (`GET /passenger-categories`) and OMSA. TOMP-API and OMSA use the term "user profile" for the equivalent concept of passenger type with eligibility conditions.

### create purchase manifest

From a selected set of product identifiers (and optional product property overrides, discount codes, and recover-ticket references), generate a signed MTB manifest. The manifest encodes the SALES OFFER PACKAGE selection as a CBOR binary, signed by the issuing participant's MTB private key, and is the authoritative input to the Ticket API's issue operation. The response includes fare breakdown, a `manifestId`, expiry, and a flag indicating whether the manifest is `distinct` (single-use only).

**Endpoint(s):** `POST /manifest`
**Unique to BoB:** Partially — the functional step of assembling selected products into a purchase-ready artefact has equivalents in OSDM (`POST /bookings` from offer IDs), OMSA (`purchase-package/execute` or `select-offers/execute`), and TOMP-API booking initiation. What is unique to BoB is the signed binary MTB manifest itself: a CBOR/COSE-encoded artefact carrying cryptographic issuer signature that serves as both the product selection record and the input to a separate issuer's ticket-issuance call.

### get previously created manifest

Retrieve a previously created manifest by `manifestId`. Supports scenarios where the manifest was created by one system and needs to be retrieved by another before submission to the Ticket API.

**Endpoint(s):** `GET /manifest/{manifestId}`
**Unique to BoB:** Partially — retrieval of a previously created purchase artefact by identifier is a universal pattern (OSDM bookings, OMSA packages, TOMP-API bookings all support retrieval). What is BoB-specific is the signed binary MTB manifest format being the retrievable artefact.

### get PDS list (product data set lookup)

Retrieve the list of PDS (product data set) identifiers and their human-readable descriptions. Used by validators for offline lookup of PDS attributes embedded in an MTB.

**Endpoint(s):** `GET /pds`
**Unique to BoB:** Yes — PDS is an MTB-specific concept (defined in MTS3) with no equivalent in other EUDIT standards.

---

## Ticket API (v3.4.0)

The Ticket API is the central lifecycle API for issued tickets. A ticket is the server-side record of an MTB that has been issued by an operator to a TRANSPORT CUSTOMER. Tickets carry rich status: active/inactive, activation counts, suspension, revocation, refund, recover, and hindered states. Tickets may also be grouped into **ticket bundles** — a set of tickets issued against the same manifest, with a shared `settlementId` for billing.

### issue tickets

Submit a signed manifest to the operator's Ticket API to create one or more tickets. The `manifestCall` body includes the manifest, an idempotency `requestId`, optional ticket holder (TRANSPORT CUSTOMER identity), device binding parameters, token binding, and an optional `bookingId` linking to a Booking API reservation. On success the server returns ticket identifiers and a `settlementId`.

**Endpoint(s):** `POST /ticket`
**Unique to BoB:** Partially — ticket/TRAVEL DOCUMENT issuance as the outcome of a confirmed purchase is a universal concept present in OSDM (`POST /bookings/{id}/fulfillments`), OMSA (`GET /collections/travel-documents/items` after `confirm-package`), and TOMP-API. What is unique to BoB is the use of a signed MTB manifest as the issuance input (rather than an offer reference or booking ID) and the return of a `settlementId` for B2B billing reconciliation between operator and distributor.

### list tickets

Retrieve all issued tickets matching filter parameters: participant ID, request ID, traveller ID, token ID, and boolean flags for active, suspended, refunded, refundable, and hindered states.

**Endpoint(s):** `GET /ticket`
**Unique to BoB:** No — listing tickets for a traveller or token is a common pattern.

### get ticket

Retrieve complete data and status for a single ticket, including the signed MTB payload, ticket holder, and all embedded status objects (activation, recover, refund, revoke, hindered, suspension).

**Endpoint(s):** `GET /ticket/{ticketId}`
**Unique to BoB:** No — single ticket retrieval by identifier is common.

### update ticket

Update the device binding, device signature issuer, signature lifetime, or token binding of an existing ticket.

**Endpoint(s):** `PATCH /ticket/{ticketId}`
**Unique to BoB:** Yes — device-level and token-level binding updates on an issued ticket are unique to BoB's hardware-key model.

### activate or deactivate ticket

Set a ticket's active status to `true` (activate) or `false` (deactivate). Activation starts the relative validity clock for time-limited FARE PRODUCTs.

**Endpoint(s):** `GET /ticket/{ticketId}/active`, `PUT /ticket/{ticketId}/active`
**Unique to BoB:** No — ticket activation is a lifecycle state that exists in OSDM (USAGE PARAMETER: ACTIVATING) and TOMP-API (`activate-product` process). The BoB implementation is particularly explicit as a separate sub-resource endpoint; explicit deactivation (setting active to `false` after activation) is a more BoB-specific capability not present in OSDM or TOMP-API.

### get ticket activation status

Retrieve the current activation status, relative validity, latest activation timestamp, and remaining activation count for a ticket.

**Endpoint(s):** `GET /ticket/{ticketId}/activationStatus`
**Unique to BoB:** No — validity/activation status is a common concept. The `remainingActivations` counter reflects multi-ride FARE PRODUCTs, which also exist in OSDM.

### suspend or check suspension status of ticket

Retrieve or record the suspension status of a ticket, including the suspension expiry timestamp. A suspended ticket cannot be used for travel during the suspension period.

**Endpoint(s):** `GET /ticket/{ticketId}/suspendedStatus`
**Unique to BoB:** Yes — ticket suspension as a named, server-tracked status with an expiry is BoB-specific. OSDM models suspension as a USAGE PARAMETER but does not expose a dedicated status sub-resource.

### get and set ticket refundable status / refund status

Check whether a ticket is refundable and the refundable amount; then execute a refund operation that transitions the ticket to the refunded state.

**Endpoint(s):** `GET /ticket/{ticketId}/refundableStatus`, `GET /ticket/{ticketId}/refundStatus`, `PUT /ticket/{ticketId}/refundStatus`
**Unique to BoB:** No — refundability check and refund execution exist in OSDM after-sales use cases, OMSA (`GET /collections/refund-options/items` and `claim-refund-option/execute`), and TOMP-API (cancel/redress pattern). The BoB version is structurally simpler (no partial refund offer, no exchange).

### get and set ticket recoverable / recover status

Check whether a ticket is recoverable (i.e. eligible to be re-issued to replace a lost or expired MTB) and the recoverable amount; then execute a recover operation.

**Endpoint(s):** `GET /ticket/{ticketId}/recoverableStatus`, `GET /ticket/{ticketId}/recoverStatus`, `PUT /ticket/{ticketId}/recoverStatus`
**Unique to BoB:** Yes — the recover mechanism (re-issuance of an equivalent MTB for a lost ticket, with financial accounting) is BoB-specific and has no direct equivalent in OSDM or TOMP-API.

### get and set ticket hindered status

Check whether the TRANSPORT CUSTOMER was hindered (unable to use the ticket's services) and record the cause (`travellerHindered`, `operatorHindered`, `forceMajeure`). A hindered ticket may be eligible for compensation.

**Endpoint(s):** `GET /ticket/{ticketId}/hinderedStatus`, `PUT /ticket/{ticketId}/hinderedStatus`
**Unique to BoB:** Yes — a named `hindered` status with structured cause codes, tracked at the ticket level, is unique to BoB.

### get and set ticket revoke status

Check whether a ticket has been revoked and when. Revoking a ticket sets it to a blocked state and adds it to the validation blacklist. Revocation reasons include refund, fraud, owner request, and loss.

**Endpoint(s):** `GET /ticket/{ticketId}/revoke`, `PUT /ticket/{ticketId}/revoke`
**Unique to BoB:** Yes — the direct link between ticket revocation and the validation blacklist (operated at the Validation API layer) is unique to BoB.

### get ticket events

Retrieve all validation and inspection events associated with a ticket, or a specific event by its server-assigned `eventId`.

**Endpoint(s):** `GET /ticket/{ticketId}/event`, `GET /ticket/{ticketId}/event/{eventId}`
**Unique to BoB:** No — event history retrieval for a travel document exists across standards. The BoB version includes detailed result codes (0–9) covering spatial, temporal, signature, blacklist, passback, and fraud failure modes.

### create ticket bundle

Group a set of tickets (issued from the same manifest) into a named bundle with a shared `settlementId`. The bundle may optionally bind to a device ID and token.

**Endpoint(s):** `POST /ticketbundle`
**Unique to BoB:** Yes — the ticket bundle concept, linking multiple MTBs under a single settlement record for B2B billing, is unique to BoB.

### get and update ticket bundle

Retrieve or update an existing ticket bundle by its `ticketBundleId`, including modifications to device binding, signature lifetime, and token binding.

**Endpoint(s):** `GET /ticketbundle/{ticketBundleId}`, `PATCH /ticketbundle/{ticketBundleId}`
**Unique to BoB:** Yes — same rationale as ticket bundle creation.

---

## Token API (v1.5.0)

The Token API covers physical or logical tokens — smart cards, NFC media, or any device with a key pair — that can have BoB tickets tied to them. It provides token key lookup (by serial number or JWK thumbprint), revocation status, a full revocation list for offline download, hints pointing to which participants have issued tickets to a token, and preferred payment service provider (PSP) registration.

### get token information by serial

Retrieve token information (public key, revocation status, thumbprint) by participant ID and serial number. Used to resolve a token's public key from the serial number read from the physical medium.

**Endpoint(s):** `GET /token?pid=&serial=`
**Unique to BoB:** Yes — hardware token key lookup keyed to a `pid`+serial pair is unique to BoB's physical medium model.

### get token information by thumbprint

Retrieve token information directly by the JWK SHA-256 thumbprint (RFC 7638) of the token's public key.

**Endpoint(s):** `GET /token/{thumbprint}`
**Unique to BoB:** Yes — same rationale.

### get token revocation status

Check whether a single token (identified by thumbprint) has been revoked. Returns a boolean `revoked` flag.

**Endpoint(s):** `GET /token/{thumbprint}/revoked`
**Unique to BoB:** Yes — per-token revocation status keyed to a cryptographic key thumbprint is unique to BoB's hardware-token model.

### get token revocation list

Download the full or incremental token revocation list, optionally starting from a given `revocationListEntryId`. Validators use this for offline revocation checking.

**Endpoint(s):** `GET /token/revocationlist`
**Unique to BoB:** Yes — a dedicated token revocation list for offline validators is unique to BoB. (Compare: OSDM has no equivalent; TOMP-API has no hardware token model.)

### manage token hints

Retrieve, add, or delete hints that point to which participants have issued tickets tied to a given token. Hints allow a sales channel to locate a TRANSPORT CUSTOMER's tickets across multiple issuer participants without a central ticket register.

**Endpoint(s):** `GET /token/{thumbprint}/hint`, `POST /token/{thumbprint}/hint`, `GET /token/{thumbprint}/hint/{hintId}`, `DELETE /token/{thumbprint}/hint/{hintId}`
**Unique to BoB:** Yes — the token hint mechanism for cross-participant ticket discovery is unique to BoB.

### manage preferred payment service provider

Retrieve, set, or remove the participant that provides the TRANSPORT CUSTOMER's preferred payment service for wallet transactions associated with a token.

**Endpoint(s):** `GET /token/{thumbprint}/psp`, `PUT /token/{thumbprint}/psp`, `DELETE /token/{thumbprint}/psp`
**Unique to BoB:** Yes — PSP registration keyed to a token thumbprint is unique to BoB's account-based ticketing model.

---

## Traveller API (v3.0.0)

The Traveller API is normally implemented by the **sales channel** — the participant that holds the direct TRANSPORT CUSTOMER relationship. It exposes TRANSPORT CUSTOMER account management, wallet operations, push notifications, token-based product set lookups and purchases, MTB lifecycle management, product set recreation, and ticket event notifications.

### search and create traveller

Search for a TRANSPORT CUSTOMER by name, email, phone, date of birth, or token ID; or create a new traveller record.

**Endpoint(s):** `GET /traveller`, `POST /traveller`
**Unique to BoB:** No — TRANSPORT CUSTOMER search and creation exist in OSDM and OMSA.

### get, update, and delete traveller information

Retrieve, fully replace, or delete the TRANSPORT CUSTOMER record for a given `travellerId`, including contact details, photo URL, preferred languages, and linked tokens.

**Endpoint(s):** `GET /traveller/{travellerId}`, `PUT /traveller/{travellerId}`, `DELETE /traveller/{travellerId}`
**Unique to BoB:** No — TRANSPORT CUSTOMER CRUD is common across standards.

### get traveller wallet information

Retrieve the payment means available in the TRANSPORT CUSTOMER's wallet (purse, payment card, mobile, invoice) including balance, transaction limits, and PIN requirements.

**Endpoint(s):** `GET /traveller/{travellerId}/wallet`
**Unique to BoB:** Yes — a structured traveller wallet resource exposing payment means, balances, transaction limits, and PIN requirements is unique to BoB among the in-scope EUDIT standards. OSDM, TOMP-API, OMSA, and FerryGateway do not expose an equivalent in-spec traveller wallet endpoint.

### perform and manage wallet transactions (traveller)

Create a wallet transaction (immediate or reserve-then-commit) on a TRANSPORT CUSTOMER's wallet. Supports optional token proof for high-value transactions. A separate PATCH endpoint commits or cancels a pending reservation.

**Endpoint(s):** `POST /traveller/{travellerId}/wallet/transaction`, `GET /traveller/{travellerId}/wallet/transaction/{transactionId}`, `PATCH /traveller/{travellerId}/wallet/transaction/{transactionId}`
**Unique to BoB:** Yes — wallet transaction management (create, retrieve, commit/cancel) on a TRANSPORT CUSTOMER's wallet is unique to BoB among the in-scope EUDIT standards. OSDM, TOMP-API, OMSA, and FerryGateway have no equivalent in-spec wallet transaction endpoints.

### send and retrieve traveller notifications

Post a push notification (with message, language, and expiry) to a TRANSPORT CUSTOMER through the sales channel; retrieve a previously sent notification by ID.

**Endpoint(s):** `POST /traveller/{travellerId}/notification`, `GET /traveller/{travellerId}/notification/{notificationId}`
**Unique to BoB:** No — notification delivery to a traveller is a common pattern.

### get product sets tied to token

Retrieve all FARE PRODUCT sets that have been issued and tied to a given physical/logical token. Supports an `all` flag to include archived or final-state products.

**Endpoint(s):** `GET /token/{tokenId}/productSets`
**Unique to BoB:** Yes — product set lookup keyed to a physical token ID, bridging the hardware-token and FARE PRODUCT layers, is unique to BoB.

### get token challenge

Retrieve a cryptographic challenge to be used as authentication input for proving possession of a token's private key.

**Endpoint(s):** `GET /token/{tokenId}/challenge`
**Unique to BoB:** Yes — challenge-response proof of token possession is part of BoB's hardware-security model.

### purchase ticket on behalf of token holder

Initiate a product purchase from a named operator participant on behalf of the TRANSPORT CUSTOMER registered as holder of the given token, charging the token's wallet.

**Endpoint(s):** `POST /token/{tokenId}/ticket`
**Unique to BoB:** Yes — a B2B delegated purchase flow triggered by a physical token presentation is unique to BoB's account-based ticketing architecture.

### get token wallet information and transactions

Retrieve the wallet linked to a token; create, retrieve, and commit/cancel wallet transactions on that token-linked wallet.

**Endpoint(s):** `GET /token/{tokenId}/wallet`, `POST /token/{tokenId}/wallet/transaction`, `GET /token/{tokenId}/wallet/transaction/{transactionId}`, `PATCH /token/{tokenId}/wallet/transaction/{transactionId}`
**Unique to BoB:** Yes — wallet operations keyed directly to a hardware token are unique to BoB.

### get product sets in MTB

Retrieve the FARE PRODUCT set(s) contained within a specific MTB identified by its issuer signature.

**Endpoint(s):** `GET /mtb/{issuerSignature}/productSets`
**Unique to BoB:** Yes — an MTB issuer-signature as a primary key for product set lookup is unique to BoB's binary ticket format.

### activate MTB

Activate all tickets within an MTB (potentially spanning multiple participant issuers) by providing the ticket identifier that triggered the activation. The sales channel orchestrates cross-participant activation on the TRANSPORT CUSTOMER's behalf.

**Endpoint(s):** `POST /mtb/{issuerSignature}/activate`
**Unique to BoB:** Yes — coordinated cross-participant MTB activation triggered by a single ticket event is a BoB-specific orchestration pattern with no equivalent in other EUDIT standards.

### recreate, get, update, and delete product set

Recreate an equivalent FARE PRODUCT set from an existing (typically expired) product set identifier — the BoB mechanism for renewing a subscription or replacing a lost product. The recreated product set can be retrieved, updated, or deleted.

**Endpoint(s):** `POST /productSet`, `GET /productSet/{productSetId}`, `PATCH /productSet/{productSetId}`, `DELETE /productSet/{productSetId}`
**Unique to BoB:** Yes — the `recreate` operation as a first-class product lifecycle action (distinct from refund+repurchase) is unique to BoB.

### ticket notification (status change callback)

The ticket issuer notifies the sales channel that the status of a ticket may have changed (state change, validity change, or other), providing the ticket identifier and a URI to refresh the ticket data.

**Endpoint(s):** `POST /ticketNotification`
**Unique to BoB:** Yes — an issuer-to-sales-channel push notification of ticket status changes, keyed to the BoB `pid`+`ticketId` address space, is unique to BoB's federated model.

---

## Device API (v2.1.1)

The Device API manages cryptographic key material for **validator devices** — the hardware scanners and gate terminals that verify MTBs offline. Validators need symmetric keys (derived from Key Derivation Keys — KDKs) to verify device-signed MTBs without a live network connection. The Device API provides those keys, plus a registry of **user agent** profiles describing the software/hardware characteristics of each device type.

### get device key by device ID

A validator device submits its device identifier (`did`, base64url-encoded) and receives its current symmetric key (`k`), the master key identifier (`kid`), key type, issued-at, and expiry timestamps, plus the user agent ID to embed in the device signature protected header of the MTB.

**Endpoint(s):** `POST /device/key`
**Unique to BoB:** Yes — the KDK-based symmetric key provisioning for offline MTB device-signature verification is unique to BoB. No other EUDIT standard has a validator key management layer.

### get key derivation keys (KDK)

Retrieve all currently valid Key Derivation Keys as a JWK Set. Validation infrastructure downloads these to derive per-device symmetric keys for offline validation without contacting the Device API on every scan.

**Endpoint(s):** `GET /device/kdk`
**Unique to BoB:** Yes — KDK distribution for offline derived-key cryptography is unique to BoB.

### get all user agents

Retrieve the complete dictionary of user agent profiles (indexed by integer `ua` identifier), each describing a specific validator or sales app device type using W3C UA Client Hints fields (`Sec-CH-UA`, platform, model, etc.).

**Endpoint(s):** `GET /device/userAgent`
**Unique to BoB:** Yes — a standardised registry of validator device profiles for audit and device-context-aware validation logic is unique to BoB.

### get user agent by identifier

Retrieve the user agent profile for a specific `ua` identifier as found in an MTB's device signature protected header.

**Endpoint(s):** `GET /device/userAgent/{ua}`
**Unique to BoB:** Yes — same rationale.

---

## Inspection API (v2.3.0)

The Inspection API is used by **on-board ticket inspectors** (revenue protection staff, not automated gates). It supports both **online inspection** (where the inspector's device submits the full MTB to the server for real-time validation) and **offline inspection** (where the device has already evaluated the MTB locally and reports the result back to the server). A batch reporting endpoint allows accumulated offline inspection events to be uploaded in bulk.

### inspect ticket online

Submit a ticket inspection event for a specific `ticketId`. If no `eventResult` is provided in the body, the server performs the inspection against the full MTB passed as a query parameter, returning a `ticketEventResult` with passenger message, inspector message, status code, ticket metadata, and activation status.

The inspection result codes cover: success (0), other failure (1), failed spatial constraints (2), failed time constraints (3), failed device header conditions (4), failed issuer signature validation (5), failed device signature validation (6), failed blacklist check (7), failed passback check (8), failed fraud check (9).

**Endpoint(s):** `POST /inspection/{ticketId}`
**Unique to BoB:** Yes — a dedicated online inspection endpoint that accepts the full MTB for server-side cryptographic verification by an on-board inspector is unique to BoB. The structured 9-code result taxonomy and the inclusion of a `tokenTransaction` field for NFC-token tap events are also BoB-specific.

### report offline inspections (batch)

Upload a batch of inspection events that were performed offline (e.g. on a train without connectivity). The entire batch is accepted or rejected atomically — there is no per-event status reporting. Online inspection is not supported through this endpoint.

**Endpoint(s):** `POST /inspection`
**Unique to BoB:** Yes — an atomic batch upload of offline inspection events, tied to BoB's idempotency (`localEventId`) model, is unique to BoB.

---

## Validation API (v3.4.0)

The Validation API is used by **automated gates and validator terminals** (platform barriers, bus validators, ferry boarding lanes). Like the Inspection API it supports online and offline modes, but adds four additional capabilities unique to the validation context: a **blacklist** (block specific tickets, travellers, or tokens), a **whitelist** (pre-distribute entitlements for offline ID-based travel), **TICKLE macro sets** (reusable time-condition expressions for offline validators), and a **fraud check** endpoint (velocity-based fraud detection).

### validate ticket online

Submit a ticket validation event for a specific `ticketId`. If no `eventResult` is in the body, the server validates the full MTB passed as a query parameter. Returns a `ticketEventResult` including passenger message, status code, ticket metadata, and activation status. Supports the same 9-code result taxonomy as Inspection.

**Endpoint(s):** `POST /validation/{ticketId}`
**Unique to BoB:** Yes — online server-side validation by a gate, consuming a full MTB and returning structured passenger/gate messages, is unique to BoB.

### report offline validations (batch)

Upload a batch of validation events performed offline. The entire batch is accepted or rejected atomically.

**Endpoint(s):** `POST /validation`
**Unique to BoB:** Yes — same rationale as offline inspection batch reporting.

### get blacklist

Download the blacklist of tickets, travellers, or tokens that must be blocked at validation, optionally incrementally from a given `blacklistEntryId`. Each entry optionally carries inception and expiry timestamps; removing an element from the blacklist is done by adding a new entry for the same ID with an expiry in the past.

**Endpoint(s):** `GET /blacklist`
**Unique to BoB:** Yes — a dedicated validation blacklist covering tickets, travellers, and tokens simultaneously, downloadable incrementally by validators, is unique to BoB. The direct link from `PUT /ticket/{ticketId}/revoke` to the blacklist is a key BoB design feature.

### get whitelist

Download the whitelist of token-keyed entitlements for offline ID-based travel. Each entry carries the token key thumbprint, a signed MTB, an entry validity interval, and optional priority. Validators use this to grant travel to account-based TRANSPORT CUSTOMERs even when the validation server is unreachable.

**Endpoint(s):** `GET /whitelist`
**Unique to BoB:** Yes — a pre-distributed whitelist of signed MTBs keyed to hardware token thumbprints, supporting offline account-based ticketing, is unique to BoB.

### get TICKLE macro sets

Retrieve all TICKLE macro sets or a specific set by serial number. TICKLE (Ticket Condition Language Expression) macros define reusable named time-condition expressions (e.g. `#weekends`) that are referenced inside MTBs. Validators download these periodically and evaluate them locally for offline time-constraint validation without re-fetching every individual product's conditions.

**Endpoint(s):** `GET /ticklemacros`, `GET /ticklemacros/{serial}`
**Unique to BoB:** Yes — TICKLE is a BoB-specific macro language for encoding recurring time conditions in MTBs. No other EUDIT standard has an equivalent offline condition-expression distribution mechanism.

### get fraud check

Submit an MTB reference, timestamp, and geographical position; receive the velocity (km/h) calculated from the position and time of that MTB's most recent prior validation event. A velocity exceeding the maximum speed of available transport modes indicates the MTB has likely been copied. The response is given at most once per validation event per client; velocities below 5 km/h or distances below 1 km return 0 km/h.

**Endpoint(s):** `GET /fraudcheck`
**Unique to BoB:** Yes — a standardised velocity-based fraud detection endpoint operated by the ticket issuer, returning a calculated speed rather than a binary decision, is unique to BoB.

---

## Booking API (v2.0.1)

The Booking API handles B2B seat-reserved journey bookings. It is used when a FARE PRODUCT requires a specific seat allocation on a specific SERVICE JOURNEY — a step beyond open-travel ticketing. The booking lifecycle moves through `pending → confirmed/cancelled`, with `miss` and `completed` as terminal states. All booking operations require the `X-BoB-AuthToken` JWT.

### request booking (create preliminary booking)

A distributor backend submits a signed MTB manifest to an operator backend to create a preliminary booking. The `bookingCall` body carries the manifest (base64url-encoded MTB template encoding the FARE PRODUCT entitlements), an optional TRANSPORT CUSTOMER identifier and phone number, and a mandatory idempotency `requestId`. The operator returns a `booking` in `pending` status with a `confirmBefore` deadline.

**Endpoint(s):** `POST /booking`
**Unique to BoB:** Partially — preliminary booking / reservation creation exists in OSDM (`POST /bookings` with offer IDs), OMSA (`select-offers/execute` or `2-phase-purchase-package/execute`), and TOMP-API booking initiation. What is unique to BoB is the use of a signed MTB manifest as the primary booking payload: the distributor backend must submit a cryptographically signed binary artefact rather than a structured offer reference.

### list bookings

Retrieve all bookings, optionally filtered by `travellerId` or `requestId`. Used for reconciliation, idempotency checks, and distributor-side state synchronisation.

**Endpoint(s):** `GET /booking`
**Unique to BoB:** No — listing bookings for a traveller is a universal pattern.

### retrieve booking

Retrieve the current state of a single booking by `bookingId`. Returns the full `booking` object including status, timestamps, ordered `ride` legs, `confirmationCode` (SERVICE ACCESS CODE), and human-readable `travellerInformation`.

**Endpoint(s):** `GET /booking/{bookingId}`
**Unique to BoB:** No — single booking retrieval by identifier is universal.

### confirm or cancel booking

Transition an existing `pending` booking to `confirmed` or `cancelled` by submitting a `statusChangeRequest`. The request must include the signed MTB container as proof of purchase and the target status. On confirmation a CUSTOMER PURCHASE PACKAGE is finalised; on cancellation a CUSTOMER RESERVATION CANCELLATION ENTRY is produced.

**Endpoint(s):** `PATCH /booking/{bookingId}`
**Unique to BoB:** Partially — booking confirmation/cancellation state transitions exist in OSDM (`PATCH /bookings/{bookingId}`), OMSA (`confirm-package/execute` or `cancel-package/execute`), and TOMP-API. What is unique to BoB is the requirement to supply a signed MTB container as proof-of-purchase in the confirmation request body, rather than a structured offer reference or payment token.

---

## Booking Status Lifecycle

The `booking.status` field maps directly onto Transmodel CUSTOMER PURCHASE STATUS values:

| BoB status | Transmodel equivalent | Meaning |
|------------|-----------------------|---------|
| `pending` | CUSTOMER PURCHASE STATUS (booked) | Reservation created, not yet confirmed or paid |
| `confirmed` | CUSTOMER PURCHASE STATUS (paid / activated) | Purchase finalised; CUSTOMER PURCHASE PACKAGE active |
| `cancelled` | CUSTOMER RESERVATION CANCELLATION ENTRY | Booking cancelled |
| `miss` | (no direct CUSTOMER PURCHASE STATUS value) | Passenger no-show; closest to void/expired state |
| `completed` | CUSTOMER PURCHASE STATUS (consumed) | Travel completed; FARE PRODUCT consumed |

---

## Out of scope

The following capabilities are explicitly outside BoB's design boundaries.

| Capability | Why out of scope for BoB |
|------------|--------------------------|
| Trip / journey planning | BoB has no trip planning layer. OSDM and TOMP-API cover this. |
| Offer / price search | BoB assumes the FARE PRODUCT and SALES OFFER PACKAGE have already been selected before the Product API is called. |
| Seat map and availability search | No seat-map or availability endpoints. OSDM covers seat allocation in detail. |
| Payment processing (card acquirer) | BoB does not handle card acquiring, card tokenisation, or payment gateway integration. Wallet transactions settle against pre-loaded balances or PSP delegation. |
| After-sales: exchange offers | No exchange offers or partial-release operations. After-sales beyond refund and recover is OSDM territory. |
| Disruption and re-accommodation | No disruption notification or re-routing workflow. |
| Ancillary services (luggage, meals) | No add-on service endpoints. |
| Multimodal / on-demand legs | No DRT or MaaS orchestration. TOMP-API covers shared mobility. |
| Adding new participants to registry | The AB handles participant onboarding out-of-band. The Participant Metadata API only manages metadata for existing participants. |
| FerryGateway / OMSA domain specifics | Vessel manifest, OMSA sales-channel reporting, and ferry-specific passenger data are outside BoB. |

---

## Relationship to other EUDIT standards

```
OSDM / TOMP-API               BoB Product + Ticket API       BoB Validation / Inspection
(offer search, booking,   →   (manifest creation,        →   (signed MTB verification
 after-sales, journey          ticket issuance,               at boarding point,
 planning)                     lifecycle management)          fraud check, blacklist)

                               BoB Participant Metadata + Auth
                               (federated trust, key registry,
                                TLS+JWT authentication)

                               BoB Traveller + Token
                               (TRANSPORT CUSTOMER account,
                                hardware token / NFC media,
                                wallet payments)
```

In a full EUDIT-compliant integration:

1. The TRANSPORT CUSTOMER searches for offers via **OSDM** or **TOMP-API**.
2. The sales channel calls **BoB Product API** `POST /manifest` to create a signed MTB manifest for the selected FARE PRODUCT.
3. The sales channel calls **BoB Ticket API** `POST /ticket` to issue the ticket at the operator backend.
4. If the journey requires seat reservation, **BoB Booking API** `POST /booking` creates a preliminary booking; `PATCH /booking/{bookingId}` confirms it.
5. At boarding, the validator calls **BoB Validation API** `POST /validation/{ticketId}` (online) or uses locally cached blacklist/whitelist/TICKLE macros (offline).
6. On-board revenue protection uses **BoB Inspection API** `POST /inspection/{ticketId}`.
