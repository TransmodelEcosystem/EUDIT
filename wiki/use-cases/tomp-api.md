# TOMP-API Use Cases

This document lists all TOMP-API-specific use cases for the EUDIT standards-harmonisation project. TOMP-API (Transport Operator MaaS Platform API) facilitates bi-directional communication between Transport Operators (TO) and MaaS Providers (MP) to fulfil a complete MaaS user journey.

Use cases are grouped by functional module, matching TOMP-API's own module structure. Each entry notes the relevant endpoint(s) and whether the use case is unique to TOMP-API (i.e. has no equivalent workflow in OSDM, OMSA, FerryGateway, or BoB).

---

## Authentication

### obtain access token

A client application obtains a Bearer access token from the TOMP-API authorisation server before calling any authenticated endpoint. TOMP-API supports two OAuth 2.0 token endpoint variants: `POST /oauth/token` (standard OAuth 2.0 client credentials / password / refresh_token grant) and `POST /connect/token` (OpenID Connect token endpoint). The MP submits client credentials or a refresh token; the server returns a Bearer access token for use in the `Authorization` header of subsequent calls.

**Endpoint(s):** `POST /oauth/token`, `POST /connect/token`
**Unique to TOMP-API:** No — OMSA also specifies `POST /oauth/token` as part of its API contract. OSDM and BoB reference external OAuth providers without specifying token endpoints in the spec itself.

---

## Administration

### Add a customer

Create a TRANSPORT CUSTOMER account on the Transport Operator side so that the operator can store customer-specific data independently of the MaaS Provider.

**Endpoint(s):** `POST /collections/customers/items`
**Unique to TOMP-API:** No — OSDM and BoB both support traveller/customer registration; however, the explicit TO-side CUSTOMER ACCOUNT management model is more detailed here.

---

### Modify a customer

Update the stored TRANSPORT CUSTOMER details on the TO side (e.g. personal data, cards, licences).

**Endpoint(s):** `PATCH /collections/customers/items/{customerId}`
**Unique to TOMP-API:** No — OSDM and BoB support updating traveller details, though the explicit TO-side account concept is TOMP-API-specific.

---

### Remove/decouple a customer

Delete or decouple the TRANSPORT CUSTOMER account from the TO system, e.g. when the customer leaves a MaaS platform.

**Endpoint(s):** `DELETE /collections/customers/items/{customerId}`
**Unique to TOMP-API:** No — account deletion is a general identity-management capability, but the TO-side account decoupling pattern is specific to the TOMP-API shared-mobility model.

---

### Validate a driver's licence

Needed to rent a car, use a scooter, etc.

**Endpoint(s):** `GET /collections/license-types/items` (retrieve accepted licence types); licence data is submitted as part of `POST /collections/customers/items` or `PATCH /collections/customers/items/{customerId}`
**Unique to TOMP-API:** Yes — driver's licence validation as a prerequisite for asset access has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Register/connect a traveller/customer

Needed to provide some customer details to determine if the customer is already a known customer on the operator side.

**Endpoint(s):** `POST /collections/customers/items`, `GET /collections/customers/items/{customerId}`
**Unique to TOMP-API:** No — all standards support some form of traveller identification, but TOMP-API's explicit TO-side account lookup is more granular than OSDM or BoB equivalents.

---

## Offering

### Provide detailed PRM information (specified size of wheel chairs, oxygen tanks, ...)

Needed for special group transport.

**Endpoint(s):** `POST /processes/search-offers/execution` (PRM needs submitted in the travel specification body); `POST /processes/update-traveller/execution` (to update PRM needs for an existing traveller in a package)
**Unique to TOMP-API:** No — OSDM and OMSA both carry PASSENGER ACCESSIBILITY NEED data; however, TOMP-API's `prmNeeds` schema explicitly enumerates equipment types (wheelchairs, oxygen tanks, pushchairs) at a level of detail not found in the other standards.

---

### Get damage report of an asset

Retrieve previously reported damage records for a specific asset before or during an offer step, to inform the TRANSPORT CUSTOMER of known defects.

**Endpoint(s):** `GET /collections/assets/items` (asset details including damage state are returned as part of asset data)
**Unique to TOMP-API:** Yes — per-asset damage state retrieval as part of the offer flow has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Get an overview of available assets in the future

Query which physical assets (vehicles, bikes, scooters, etc.) will be available for a future leg or time window.

**Endpoint(s):** `GET /collections/assets/items`
**Unique to TOMP-API:** Yes — physical shared-asset availability querying is specific to shared-mobility; no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Get an overview of available ancillaries

Retrieve the set of ancillaries (e.g. helmets, insurance, luggage) that can be added to a leg or package.

**Endpoint(s):** `GET /collections/ancillaries/items`
**Unique to TOMP-API:** Yes — ancillary add-ons at the leg level in the context of shared mobility have no direct equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Get an overview of available assets (including seat plan) for a leg in an offer or package

Retrieve assets scoped to a specific leg, including seat/space allocation data where relevant.

**Endpoint(s):** `GET /collections/assets/items`, `GET /collections/offers/items`
**Unique to TOMP-API:** No — OSDM supports seat-plan retrieval for scheduled transport; however, the shared-asset scoping to a specific leg is TOMP-API-specific.

---

### Modify start/end times and locations before purchasing

Adjust the travel specification (departure/arrival times and/or locations) of a selected offer before the package is purchased.

**Endpoint(s):** `POST /processes/update-travel-specification/execution`
**Unique to TOMP-API:** No — OSDM supports modification of travel parameters before purchase; the pre-sales state machine is more explicit in TOMP-API.

---

### Release a package that has been selected/modified

Release a PENDING package (freeing any reserved resources) when the TRANSPORT CUSTOMER no longer intends to purchase it.

**Endpoint(s):** `POST /processes/release-package/execution`
**Unique to TOMP-API:** No — OSDM and BoB support releasing/abandoning a booking in pre-purchase state; the explicit PENDING-state lifecycle is more formalised in TOMP-API.

---

### Remove an offer from a package

Remove a single offer from a multi-offer package without releasing the entire package.

**Endpoint(s):** `POST /processes/remove-offer/execution`
**Unique to TOMP-API:** No — partial removal of components from a composite booking exists in OSDM, though the package/offer compositing model is TOMP-API-specific.

---

## Purchase

### Use a specific asset (bike, scooter, ...)

Needed to pick a bike from the street or use NeTEx/GBFS-based locations of assets or stations.

**Endpoint(s):** `POST /processes/use-asset/execution`
**Unique to TOMP-API:** Yes — direct invocation of a specific shared physical asset (by asset ID or station ID) without a prior search/offer step has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Reserve a parking spot

Reserve a specific parking space as part of, or independently of, a mobility package.

**Endpoint(s):** `POST /processes/use-asset/execution` (with asset type parking spot), `POST /processes/purchase-offers/execution`
**Unique to TOMP-API:** Yes — parking-spot reservation as a first-class purchase action has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### 2-phase purchase

Needed to incorporate urban modes in multi-modal trips. This also requires a 'rollback purchase' and 'confirm purchase' functionality.

**Endpoint(s):** `POST /processes/purchase-offers/execution` or `POST /processes/purchase-package/execution` (initiates PENDING state), `POST /processes/confirm-purchase/execution`, `POST /processes/rollback-purchase/execution`
**Unique to TOMP-API:** No — OSDM supports a two-phase booking confirmation model; TOMP-API's version adds the multi-modal coordination concern and explicit rollback within a time window.

---

### Extend expiry time

Once you have purchased a package, but it's still before the confirmation, the expiry time might be too early. You can request to extend it.

**Endpoint(s):** `POST /processes/extend-expiry-time/execution`
**Unique to TOMP-API:** Yes — explicit expiry-time extension on a PENDING package has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### 'Pick up the bill'

Needed to redirect the payment to a reseller, once a parking session (or charging session) has already started.

**Endpoint(s):** `GET /collections/payments/items` (with `onBehalveOf` header to identify the reseller), `POST /processes/confirm-payment/execution`
**Unique to TOMP-API:** Yes — mid-session payment-responsibility transfer to a reseller/MaaS Provider has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

*The TO-side of this flow is implemented via `POST /processes/request-payment/execution` in TOMP-API-MP — see the [MaaS Provider Callbacks](#maas-provider-callbacks-tomp-api-mp-v200) section.*

---

### Purchase a product (like daycard)

Purchase a standalone FARE PRODUCT (e.g. a day card or zone pass) outside of a specific travel specification.

**Endpoint(s):** `POST /processes/purchase-product/execution`
**Unique to TOMP-API:** No — OSDM and BoB both support product (FARE PRODUCT) purchases; the product-centric purchase without a trip context is also available in OSDM.

---

### Purchase a travel package containing another travel package to exchange

Purchase a composite package that wraps or exchanges an existing travel package, supporting multi-modal chain ticketing.

**Endpoint(s):** `POST /processes/purchase-package/execution`
**Unique to TOMP-API:** No — nested or exchanged package purchase is conceptually supported in OSDM SALES OFFER PACKAGE composition, though the runtime exchange pattern is more explicit in TOMP-API.

---

## Generic

### Travel package details

Get information at any time about your travel package.

**Endpoint(s):** `GET /collections/packages/items`
**Unique to TOMP-API:** No — OSDM and BoB both support retrieval of booking/purchase details; equivalent to CUSTOMER PURCHASE PACKAGE retrieval.

---

### Find referenced data

Get source-references to used data (NeTEx, GBFS, etc). So you can retrieve it yourself and find more details (to validate). The API should work without retrieving this data.

**Endpoint(s):** `GET /collections/datasources/items`
**Unique to TOMP-API:** Yes — explicit discovery of external datasource references (NeTEx, GBFS, etc.) used within responses has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

## Execution

### ETA

Required for on-demand services, to notify the traveler that they're on their way to the pickup location.

**Endpoint(s):** `GET /collections/packages/items` (package/leg status containing ETA data); leg status updates are surfaced via the `POST /processes/{legOperation}-leg/execution` response
**Unique to TOMP-API:** Yes — real-time ETA reporting from operator to traveller during leg execution has no equivalent in OSDM, BoB, FerryGateway, or OMSA (OMSA supports on-demand booking but not this execution feedback).

---

### Unlock/close assets

Different options, like bluetooth, NFC, remote, and even manual locking.

**Endpoint(s):** `POST /processes/{assetOperation}-asset/execution` (with `assetOperation` = `unlock` or `lock`)
**Unique to TOMP-API:** Yes — physical asset lock/unlock operations have no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Open/close locker to get access to an asset

Open or close a locker (e.g. a bike box or helmet locker) to grant the TRANSPORT CUSTOMER access to an asset or accessory.

**Endpoint(s):** `POST /processes/{assetOperation}-asset/execution` (with custom `assetOperation`, e.g. `open-locker`)
**Unique to TOMP-API:** Yes — locker access as a physical execution operation has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Start/end the usage of an asset

Often includes the unlocking/locking, not always.

**Endpoint(s):** `POST /processes/{legOperation}-leg/execution` (with `legOperation` = `start` or `end`)
**Unique to TOMP-API:** Yes — explicit start/end of physical asset usage as a leg lifecycle operation has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Pause/resume the usage of an asset

Temporarily pause (e.g. during a short stop) and later resume an active leg without ending it.

**Endpoint(s):** `POST /processes/{legOperation}-leg/execution` (with `legOperation` = `pause` or `resume`)
**Unique to TOMP-API:** Yes — pause/resume of a physical mobility leg has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Custom functions of an asset

Like 'open-trunk' or 'open-helmet-box'.

**Endpoint(s):** `POST /processes/{assetOperation}-asset/execution` (with custom `assetOperation` values, e.g. `open-trunk`)
**Unique to TOMP-API:** Yes — extensible custom physical-asset operations have no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Activate a product

To activate a product before it can be used (like a week-card).

**Endpoint(s):** `POST /processes/{productOperation}-product/execution` (with `productOperation` = `activate`)
**Unique to TOMP-API:** No — OSDM and BoB support product activation; however, TOMP-API makes this an explicit leg-lifecycle operation enabling on-the-fly product activation.

---

### Extend the usage of an asset

E.g. extend parking session.

**Endpoint(s):** `POST /processes/{legOperation}-leg/execution` (with `legOperation` = `extend`)
**Unique to TOMP-API:** Yes — extending an active leg/session (e.g. a parking session) via the leg lifecycle has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Postpone the usage of an asset

Delay the start of a leg that has already been paid for (e.g. push back the start time of a reserved vehicle).

**Endpoint(s):** `POST /processes/{legOperation}-leg/execution` (with `legOperation` = `postpone`)
**Unique to TOMP-API:** Yes — in-execution postponement of a leg start has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Add ancillaries (like helmets) to a leg

Attach additional ancillary items to an already-purchased or in-progress leg.

**Endpoint(s):** `POST /processes/assign-ancillary/execution`
**Unique to TOMP-API:** Yes — attaching physical ancillaries (helmets, child seats, etc.) to an active leg has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Swap ancillaries

Replace one ancillary item assigned to a leg with a different one (e.g. swap a damaged helmet).

**Endpoint(s):** `POST /processes/assign-ancillary/execution` (re-assignment to swap the existing ancillary)
**Unique to TOMP-API:** Yes — physical ancillary swap has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Change assigned assets (with optionally other types/products)

Swap the asset assigned to a leg for a different one, optionally also changing the associated product type.

**Endpoint(s):** `POST /processes/assign-asset/execution`
**Unique to TOMP-API:** Yes — reassigning a physical asset during or before execution has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Deliver proof of correct parking

Submit evidence (e.g. a photo or GPS coordinate) that an asset has been parked in an approved location.

**Endpoint(s):** `POST /processes/request-support/execution` (proof payload included in the support request body); proof data is also passable via query parameters on `GET /collections/fares/items`
**Unique to TOMP-API:** Yes — proof-of-parking submission as an operational step has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Driving support

Like: parking location, no-go zones, low-speed zones. Could be used for publishing PT zones as well. Based on OMF's MDS.

**Endpoint(s):** Zone and geofence data are returned in asset/leg responses; `GET /collections/assets/items` returns zone references (`zones` field on asset schema)
**Unique to TOMP-API:** Yes — operational geofencing and driving-support zone data (no-go zones, low-speed zones, preferred parking) drawn from OMF's MDS has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Swipe in/out

This creates a new leg/trip based on a product.

**Endpoint(s):** `POST /processes/{productOperation}-product/execution` (with `productOperation` = `initiate-leg`)
**Unique to TOMP-API:** Yes — tap-on/tap-off style leg initiation from a product (equivalent to a transit card swipe) has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### See progress of a leg in execution

For on-demand services.

**Endpoint(s):** `GET /collections/packages/items` (returns current leg state and progress data)
**Unique to TOMP-API:** Yes — real-time leg execution progress polling for on-demand services has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Retrieve a (dynamic) ticket

Retrieve a TRAVEL DOCUMENT or dynamically generated ticket for a purchased leg or package (e.g. a QR code that refreshes over time).

**Endpoint(s):** `GET /collections/travel-documents/items`
**Unique to TOMP-API:** No — OSDM and BoB both support TRAVEL DOCUMENT/ticket retrieval; TOMP-API's dynamic ticket model (with time-limited QR codes) is more specific to shared mobility.

---

## Support

### Request help in case of incidents (flat tires, etc.)

Submit a support request for a non-emergency incident affecting the TRANSPORT CUSTOMER's mobility leg (e.g. a puncture or broken lock).

**Endpoint(s):** `POST /processes/request-support/execution`, `GET /collections/support-tickets/items`
**Unique to TOMP-API:** Yes — incident support ticketing within the mobility execution flow has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Request help in case of accidents

Submit an urgent support request following an accident, triggering operator assistance procedures.

**Endpoint(s):** `POST /processes/request-support/execution` (with support type `REQUEST_ASSISTANCE`), `GET /collections/support-tickets/items`
**Unique to TOMP-API:** Yes — accident-response support ticketing in a shared-mobility execution context has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Report damage

Report physical damage to an asset at any point in the journey lifecycle (before, during, or after use).

**Endpoint(s):** `POST /processes/request-support/execution` (damage details in request body; `damage` field on `supportTicket` schema)
**Unique to TOMP-API:** Yes — structured asset damage reporting has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Retrieve the status of your support request

Check the current lifecycle state of an open support ticket (e.g. `ISSUE_REQUESTED`, `ISSUE_OPEN`, `RESOLVED`).

**Endpoint(s):** `GET /collections/support-tickets/items`
**Unique to TOMP-API:** Yes — support-ticket lifecycle querying within a mobility execution context has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

## Payment

### Get an overview of items to pay

Retrieve all JOURNAL ENTRIES (fares, additional costs, fines, deposits, etc.) that are owed for a package or time period.

**Endpoint(s):** `GET /collections/payments/items`
**Unique to TOMP-API:** No — OSDM and BoB both support financial settlement overviews; TOMP-API's JOURNAL ENTRY model adds shared-mobility-specific categories (damage charges, deposit lines).

---

### Confirm payment

The MaaS Provider confirms that a specific financial transaction has been settled with the end user.

**Endpoint(s):** `POST /processes/confirm-payment/execution`
**Unique to TOMP-API:** No — payment confirmation flows exist in OSDM and BoB; TOMP-API's model is the B2B MP-to-TO confirmation step.

---

### Request a deposit

Request that the TRANSPORT CUSTOMER provides a deposit before or during the use of an asset (e.g. as a damage guarantee).

**Endpoint(s):** `GET /collections/payments/items` (deposit appears as `DEPOSIT` category payment detail); deposit is initiated as part of the purchase or execution flow responses
**Unique to TOMP-API:** Yes — deposit management as an explicit financial instrument tied to physical asset use has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

## Notification

### Report status changes to user

Push notifications to the TRANSPORT CUSTOMER when the state of their booking, leg, or support ticket changes (e.g. leg started, asset available, issue resolved).

**Endpoint(s):** Status changes are delivered as part of responses to `GET /collections/packages/items` and `GET /collections/support-tickets/items`; payment-state notifications are described in the `financialDetail` schema. Push delivery is outside the TO→MP sync model.
**Unique to TOMP-API:** Yes — proactive status-change notification as part of the MaaS execution loop has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Send alerts to user

Like: it's almost time to get back; you have parked at a location where it's not allowed.

**Endpoint(s):** Operator-to-MP alerts are conveyed via package/leg state responses (`GET /collections/packages/items`) and zone-violation data in asset responses; direct push alerting is a TO→MP out-of-band mechanism.
**Unique to TOMP-API:** Yes — time- and location-triggered operational alerts (return reminders, illegal-parking warnings) have no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

## Discovery

### Show the implemented endpoints

Expose a machine-readable description of which API endpoints and processes this Transport Operator has implemented, so a MaaS Provider can adapt its integration.

**Endpoint(s):** `GET /` (landing page), `GET /api` (OpenAPI document), `GET /conformance`, `GET /collections`, `GET /processes`
**Unique to TOMP-API:** No — OpenAPI-based self-description is standard practice; TOMP-API formalises it using the OGC API Processes pattern, which is specific to this standard.

---

### At each call, know the possible next steps

Each API response signals which follow-on operations are available (e.g. from PENDING you can confirm, rollback, or extend expiry), allowing a client to drive the workflow without hard-coded state assumptions.

**Endpoint(s):** `GET /processes/{processId}` (process description including links to execution endpoints); next-step links are also embedded in package response bodies
**Unique to TOMP-API:** Yes — explicit state-machine navigation via hypermedia/process descriptions embedded in every response has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

## After-Sales

### Request redresses

Like (partial) refunds. Works with delivering options, claiming one of them and confirm/rollback them in a second step. This 2-phase is required in a multi-modal trip (same as the purchase procedure).

**Endpoint(s):** `GET /collections/redress-options/items`, `POST /processes/claim-redress-option/execution`, `POST /processes/confirm-redress-option/execution`
**Unique to TOMP-API:** No — OSDM supports REFUND and EASEMENT flows; TOMP-API adds the explicit 2-phase claim/confirm pattern needed for multi-modal coordination.

---

### Request refund of a deposit

Reclaim a previously paid deposit once the asset has been returned in acceptable condition.

**Endpoint(s):** `POST /processes/refund-deposit/execution`
**Unique to TOMP-API:** Yes — deposit refund as a dedicated after-sales operation has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Cancel packages

Only required to fix technical problems.

**Endpoint(s):** `POST /processes/cancel-package/execution`
**Unique to TOMP-API:** No — OSDM and BoB both support booking cancellation; in TOMP-API this endpoint is intentionally limited to correcting technical errors (normal after-sales flows use the redress process).

---

## Information

### Get an overview of entitlements

Retrieve the entitlements (discount rights, eligibility criteria, etc.) that the operator accepts, so a MaaS Provider can match them to TRANSPORT CUSTOMER profiles.

**Endpoint(s):** `GET /collections/entitlements/items`
**Unique to TOMP-API:** No — OSDM and BoB carry ENTITLEMENT data; the explicit TO-side entitlement catalogue is more detailed in TOMP-API.

---

### Get an overview of card types

Retrieve the card types (loyalty cards, student cards, etc.) that the operator recognises, for use in offer personalisation.

**Endpoint(s):** `GET /collections/card-types/items`
**Unique to TOMP-API:** No — OSDM and BoB support card-based eligibility; the explicit card-type catalogue API is TOMP-API-specific.

---

### Get an overview of licence types

Retrieve the driving licence categories and other licence types that the operator requires or recognises.

**Endpoint(s):** `GET /collections/license-types/items`
**Unique to TOMP-API:** Yes — a queryable catalogue of accepted driving licence types has no equivalent in OSDM, BoB, FerryGateway, or OMSA.

---

### Get an overview of user profiles

Retrieve the user profile definitions (e.g. adult, student, senior) supported by the operator, for use in offer search and personalisation.

**Endpoint(s):** `GET /collections/user-profiles/items`
**Unique to TOMP-API:** No — OSDM and BoB carry USER PROFILE / FARE CLASS data; the explicit TO-side user-profile catalogue is more detailed in TOMP-API.

---

### Get an overview of a single fare

To prevent publishing the complete fare tables. You can anonymously search based on user profile, entitlements, card-types, products and start/end locations.

**Endpoint(s):** `GET /collections/fares/items`
**Unique to TOMP-API:** No — OSDM supports FARE PRODUCT / FARE TABLE queries; TOMP-API's anonymous single-fare lookup (without publishing complete tables) is a lighter-weight variant of the same capability.

---

## MaaS Provider Callbacks (TOMP-API-MP v2.0.0)

TOMP-API-MP v2.0.0 defines a companion set of endpoints implemented by the **MaaS Provider (MP)** that the **Transport Operator (TO)** calls. This is the reverse direction of the standard TOMP-API interface: whereas TOMP-API describes what the MP calls on the TO, TOMP-API-MP describes what the TO calls back on the MP. Together the two specifications form the complete bidirectional TOMP-API interface.

The three endpoints cover the three classes of TO-initiated communication: unsolicited notifications, requests for MP confirmation of a pending step, and requests for MP payment of a session that has already started.

---

### receive notification from transport operator

The TO pushes an unsolicited notification to the MP. Notification types span the full leg and asset lifecycle: status transitions (`PREPARING`, `STARTED`, `PAUSED`, `RESUMED`, `ENDED`), asset events (`LOCKED`, `UNLOCKED`, `VEHICLE_ARRIVED`), user-facing alerts (`ETA`, `WARNING`, `INFORMATION`, `USER_NO_SHOW`), financial events (`PRICE_FINAL`), and async process outcomes (`SUCCESS`, `FAILURE`, `ACCEPTED`, `NOT_ACCEPTED`).

**Endpoint(s):** `POST /processes/notification/execution`
**Unique to TOMP-API:** Yes — no other in-scope standard defines a standardised callback endpoint on which the operator pushes real-time execution notifications to the reseller.

---

### receive confirmation request from transport operator

The TO requests that the MP actively confirm a pending step before the TO proceeds. Currently defined confirmation types are `REPLACE_ASSET` (the TO wants to swap the asset assigned to a leg) and `START_LEG` (the TO is ready to start the leg and needs MP approval). The MP responds with HTTP 204 to grant confirmation or an error body to refuse.

**Endpoint(s):** `POST /processes/request-confirmation/execution`
**Unique to TOMP-API:** Yes — no other in-scope standard defines a standardised mechanism by which the operator can solicit explicit approval from the reseller before executing a step.

---

### receive payment request from transport operator

The TO requests that the MP settle a payment for a session that has already started — for example, a parking or EV-charging session initiated via the "pick up the bill" flow. The request body carries a `financialDetail` payload (the same schema used in `GET /collections/payments/items`) describing the amount and payment category. The MP responds with HTTP 204 to acknowledge receipt, or an error body to decline.

This is the server-side implementation of the "pick up the bill" use case: once the TO has identified the MP as the responsible payer (via `onBehalveOf` in the standard TOMP-API), it uses this endpoint to formally request settlement.

**Endpoint(s):** `POST /processes/request-payment/execution`
**Unique to TOMP-API:** Yes — pay-later settlement initiated by the TO towards the MP (for a session that has already started) is specific to TOMP-API's shared-mobility model and has no equivalent in OSDM, BoB, FerryGateway, or OMSA.
