# OSDM Use Cases

This document catalogues the OSDM-specific use cases for the EUDIT standards-harmonisation project. Each entry describes who does what, which OSDM v3.7.1 endpoint(s) are involved, and whether the use case is unique to OSDM or shared with other in-scope standards (TOMP-API, BoB, FerryGateway, OMSA).

---

## Search non-trip-based offers

Non-trip-based offers cover FARE PRODUCTs that are not tied to a specific scheduled trip: season passes, multi-ride cards, reduction cards, zone-based products, and merchandising articles. The TRANSPORT CUSTOMER or RETAILER supplies validity criteria (region, start date, passenger types, product tags) instead of an origin-destination pair or trip ID.

### search non-trip-based offers

The RETAILER requests SALES OFFER PACKAGEs for products that are not bound to a specific trip (e.g. rail passes, zone passes, reduction cards, merchandise). Search parameters include begin of validity, region expressed as NUTS codes or PLACE references, FARE CLASS, flexibility, and PRODUCT TAGs.

**Endpoint(s):** 
  - `POST /offers`
  - `POST /product-search`     optional preliminary search for products
  - `GET /product-tags`        get catalog selection options
    
**Unique to OSDM:** Partially — OMSA and TOMP-API support purchasing assets and on-demand services without a trip specification, which serves as a non-trip-based alternative. However, OSDM's structured `nonTripSearchCriteria` block supporting rail passes, zone passes, reduction cards, and merchandise by region and PRODUCT TAG is specific to the rail catalogue domain and has no direct equivalent in TOMP-API, BoB, or FerryGateway.

---

## Search trip-based offers

Trip-based offers are SALES OFFER PACKAGEs priced for one or more specific scheduled services. OSDM supports three input modes — trip search parameters (origin/destination/date), pre-fetched trip IDs, and fully specified trip structures (required for fare-rule providers) — and extends all three modes to cover return trips.

### search trip-based offers by trip specification

The RETAILER submits a full trip structure (sequence of legs with service numbers, departure times, and origin/destination) to request fares from a fare provider that works on explicit itinerary input rather than an OD search. The `tripkey` echoed in the response correlates request and result.

**Endpoint(s):** `POST /offers`
**Unique to OSDM:** No — OMSA supports equivalent trip-specification-based offer requests via `searchOfferInput.specification` (TRIP PATTERN / TRAVEL SPECIFICATION), and TOMP-API has a comparable planning endpoint accepting structured leg specifications. OSDM's `tripSpecification` pattern is rail-specific (service numbers, UIC stop codes, departure times). Note that OJP (Open Journey Planning), which is compatible with OSDM trip IDs, also uses trip specifications as a shared data model.

### search trip-based offers by trip id

The RETAILER reuses trip IDs obtained from an earlier `/trips-collection` search to request bookable offers for known trips, avoiding a repeated journey-planner call.

**Endpoint(s):** `POST /offers`
**Unique to OSDM:** Yes — offer search using pre-fetched trip IDs from a prior `/trips-collection` call has no equivalent in OMSA or TOMP-API, which do not expose a trip-ID-based offer search independent of a fresh origin-destination query.

### search trip-based offers by OD

The RETAILER searches for SALES OFFER PACKAGEs by specifying an origin PLACE, a destination PLACE, a travel date/time, and optional criteria (FARE CLASS, flexibility, currency, reduction cards). The provider resolves the trip and returns an offer collection.

**Endpoint(s):** `POST /offers`, `POST /trips-collection`, `GET /trips-collections/{tripsCollectionId}`
**Unique to OSDM:** No — OD-based offer search is a common capability across TOMP-API, BoB, and FerryGateway.

### search trip-based offers for return trips

The RETAILER requests outbound and inbound SALES OFFER PACKAGEs in a single call by supplying both `tripSearchCriteria` (or trip IDs / trip specifications) and their return equivalents. OSDM models dependent return offers via shared `returnTags` (pre-v3.7) or a combined `tripCoverage`/`inboundTripCoverage` structure (v3.7+).

**Endpoint(s):** `POST /offers`
**Unique to OSDM:** Yes — the explicit return-trip offer model (combined pricing, `inboundTripCoverage`, return tags) is rail-specific and has no direct equivalent in TOMP-API, BoB, or FerryGateway.

---

## Select offer

Once offers are returned, the TRANSPORT CUSTOMER selects one and begins a provisional booking (pre-booking). This section covers all interactions that manipulate a booking while it is in `PREBOOKED` state, before final confirmation.

### select an offer for booking and start the booking process

The RETAILER creates a provisional booking by `POST /bookings` with one or more selected offer IDs (and optionally associated reservation/ancillary offer-part IDs). The booking enters `PREBOOKED` state and a ticket time limit is set. Multiple offers for a return trip must be submitted together.

**Endpoint(s):** `POST /bookings`
**Unique to OSDM:** No — provisional booking creation is present in BoB, TOMP-API, and FerryGateway.

### add required personal data of passengers

After pre-booking, `requestedInformation` on the booking indicates which TRANSPORT CUSTOMER attributes are still required (e.g. first/last name, date of birth, contact). The RETAILER PATCHes the passenger sub-resource to supply them.

**Endpoint(s):** `PATCH /bookings/{bookingId}/passengers/{passengerId}`
**Unique to OSDM:** No — passenger data collection during booking is a common workflow, though OSDM's boolean-expression `requestedInformation` grammar is distinctive.

### change the selection of optional offer parts

The RETAILER updates the set of optional OFFER PARTs (ancillaries, optional reservations) on a pre-booked offer by patching or replacing the booked-offer's offer-parts list.

**Endpoint(s):** `PATCH /bookings/{bookingId}/booked-offers/{bookedOfferId}/offer-parts`
**Unique to OSDM:** No — modifying optional booking items is a general pattern present in other standards.

### get the available seat preferences or available places for reservation

Before committing to a specific seat, the RETAILER queries availability information: either a list of available places with properties (window, aisle, table, quiet zone) or a graphical coach-layout map suitable for visual seat selection. The request context can target an OFFER or a BOOKING.

**Endpoint(s):** `GET /availabilities/preferences`, `GET /availabilities/place-map`, `GET /availabilities/vehicle-place-map`
**Unique to OSDM:** Partially — OMSA and TOMP-API expose `GET /collections/assets/items` returning GeoJSON feature collections of available physical assets (vehicles, bays, berths), which serves a comparable purpose of querying available places before assignment. What is OSDM-specific is the graphical coach-layout rendering (`GET /availabilities/place-map`, `GET /availabilities/vehicle-place-map`) with named seat IDs, seat properties (window, aisle, table, quiet zone), and seat-selection fees — a model specific to rail carriage seating.

### select places for reservation

The RETAILER submits specific place selections (seat numbers from a graphical map) for one or more reservation OFFER PARTs on the pre-booked booking. The reservation is updated with the `reservedPlaces` element.

**Endpoint(s):** `PATCH /bookings/{bookingId}/booked-offers/{bookedOfferId}/reservations/{reservationId}`
**Unique to OSDM:** Partially — OMSA `assign-asset/execute` and TOMP-API support assigning a specific physical asset (vehicle, berth, bay) to a leg, which is functionally comparable to place selection. What is OSDM-specific is the use of explicit graphical place IDs referencing a COACH LAYOUT, where seat positions are identified by their position in a rendered deck plan — a model specific to rail carriage seat reservation.

### select nearby places for reservation

The RETAILER requests seats near a specified reference place (e.g. to keep a group together). The provider returns available adjacent or nearby places.

**Endpoint(s):** `GET /availabilities/nearby`
**Unique to OSDM:** Yes — proximity-based seat selection against a coach layout is rail-specific.

### remove optional offer parts

The RETAILER removes a previously added optional OFFER PART (ancillary or optional reservation) from the pre-booked booking.

**Endpoint(s):** `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}/ancillaries/{ancillaryId}`, `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}/reservations/{reservationId}`
**Unique to OSDM:** No — removing optional booking items is a general pattern.

### add optional offer parts

The RETAILER adds an additional ancillary or optional reservation OFFER PART to an existing pre-booked booking.

**Endpoint(s):** `POST /bookings/{bookingId}/booked-offers/{bookedOfferId}/ancillaries`, `POST /bookings/{bookingId}/booked-offers/{bookedOfferId}/reservations`
**Unique to OSDM:** No — adding optional items to a booking is a general workflow.

### get an on-hold offer to extend the pre-booking time

The RETAILER requests an ON HOLD OFFER that, if accepted, will extend the ticket time limit on a `PREBOOKED` booking, preventing it from expiring while the TRANSPORT CUSTOMER completes payment or decision-making. The offer may carry a fee.

**Endpoint(s):** `POST /bookings/{bookingId}/on-hold-offer`
**Unique to OSDM:** Partially — OMSA `extend-expiry-time/execute` and TOMP-API both provide mechanisms to extend the confirmation deadline for a pending booking. What is distinctive to OSDM is the on-hold offer model: the extension requires a formal offer that may carry a fee, which the RETAILER must explicitly accept — making it a commercial transaction rather than a simple administrative extension.

### accept an on-hold offer to extend the pre-booking time

The RETAILER confirms the ON HOLD OFFER to extend the booking's ticket time limit. The booking remains in `PREBOOKED` state with a new expiry.

**Endpoint(s):** `POST /bookings/{bookingId}/on-hold-offer/{onHoldOfferId}`
**Unique to OSDM:** Yes — see above; specific to OSDM's on-hold mechanism.

### cleanup a preliminary booking in case of failure

When a multi-provider pre-booking partially fails, the RETAILER deletes the booking to trigger rollback of any sub-provider pre-bookings that succeeded, freeing reserved resources (seats, quotas).

**Endpoint(s):** `DELETE /bookings/{bookingId}`, `POST /bookings/{bookingId}/cleanup`
**Unique to OSDM:** No — cancellation of unconfirmed bookings is a standard pattern; the explicit `/cleanup` endpoint for partial-success rollback is OSDM-specific.

### check consistency before confirmation

Before calling the fulfillment endpoint, the RETAILER may invoke the fulfillment-check to validate that all required information is present and the booking is in a confirmable state, without actually confirming it.

**Endpoint(s):** `POST /bookings/{bookingId}/fulfillment-check`
**Unique to OSDM:** Yes — an explicit pre-confirmation consistency check endpoint is not present in the other in-scope standards.

---

## Confirm booking

Confirmation transforms a `PREBOOKED` booking into a fulfilled one. OSDM combines booking confirmation and FULFILLMENT issuance in a single step from the API consumer's perspective.

### get the fulfillments for a booking

The RETAILER calls `POST /bookings/{bookingId}/fulfillments` to confirm the booking and retrieve all FULFILLMENT documents (tickets, barcodes, PDF links) in one operation. The booking status transitions to `FULFILLED` (or `CONFIRMED` for providers that defer fulfillment).

**Endpoint(s):** `POST /bookings/{bookingId}/fulfillments`, `GET /bookings/{bookingId}/fulfillments`
**Unique to OSDM:** No — confirmation + fulfillment retrieval is a universal booking pattern present in BoB, TOMP-API, and FerryGateway.

### get individual fulfillments of a multi-journey booking

For MULTI-JOURNEY PRODUCTs (passes, multi-ride tickets) the FULFILLMENT enters state `AVAILABLE` rather than `FULFILLED`. The RETAILER or TRANSPORT CUSTOMER PATCHes the individual fulfillment to activate it by providing a travel date or zone selection, after which the fulfillment becomes `FULFILLED` for that usage instance.

**Endpoint(s):** `PATCH /fulfillments/{fulfillmentId}`, `GET /fulfillments/{fulfillmentId}`
**Unique to OSDM:** Partially — OMSA and TOMP-API both produce per-leg travel documents and track leg status, which implies similar per-journey activation states within a multi-leg booking. What is OSDM-specific is the explicit `AVAILABLE` → `FULFILLED` state transition model for MULTI-JOURNEY PRODUCTs (passes, multi-ride tickets) with quota management, where each individual usage instance must be activated by the RETAILER by supplying a travel date or zone selection.

---

## After sales

After-sales processes operate on confirmed bookings and their FULFILLMENTs. All flows follow a request-offer → accept-offer pattern to allow the TRANSPORT CUSTOMER to review terms before committing.

### search for additional offers to a booking

The RETAILER retrieves additional SALES OFFER PACKAGEs that can be added to an existing confirmed booking (e.g. adding a seat reservation or ancillary after initial booking).

**Endpoint(s):** `GET /bookings/{bookingId}/booked-offers/{bookedOfferId}/additional-offers`
**Unique to OSDM:** No — adding offers to an existing booking is a general post-booking capability.

### request refund offers for a booking

The RETAILER submits a `POST /bookings/{bookingId}/refund-offers` specifying the FULFILLMENT IDs to be refunded. The provider calculates and returns one or more REFUND OFFERs showing the refund amount, any cancellation fee, and validity window.

**Endpoint(s):** `POST /bookings/{bookingId}/refund-offers`
**Unique to OSDM:** No — refund offer calculation exists in BoB, FerryGateway, OMSA (`GET /collections/refund-options/items` for available refund options, `claim-refund-option/execute` for claiming), and TOMP-API (after-sales cancellation flow). The OSDM two-step request/confirm pattern is analogous to OMSA's claim/confirm refund model.

### request partial refund offers for a booking

The RETAILER requests a partial REFUND OFFER by specifying a subset of passengers, booking parts, or trip legs within a collective FULFILLMENT (e.g. cancel only the return leg, or remove one passenger from a group booking).

**Endpoint(s):** `POST /bookings/{bookingId}/refund-offers` (with `refundSpecification`)
**Unique to OSDM:** Partially — OMSA `claim-refund-option/execute` supports `remove_traveller` and `remove_ancillary` refund types, and TOMP-API supports partial after-sales operations, both of which cover partial refund scenarios. What is OSDM-specific is the `refundSpecification` model enabling passenger-level and segment-level partial refunds within collective FULFILLMENTs (collective ticketing), where a subset of passengers or trip legs can be refunded from a shared ticket.

### accept refund offer

The RETAILER confirms a REFUND OFFER to execute the cancellation and credit the refund amount. Confirming one offer in a set for the same FULFILLMENTs automatically cancels the others.

**Endpoint(s):** `POST /bookings/{bookingId}/refund-offers/{refundOfferId}` (confirm action), `PATCH /bookings/{bookingId}/refund-offers/{refundOfferId}`
**Unique to OSDM:** No — confirming a refund offer is a general workflow.

### request release offer for a booking

The RETAILER requests a RELEASE OFFER to invalidate the admission right and free reserved resources (e.g. reserved seat) without immediately refunding the TRANSPORT CUSTOMER. Release is used when a third party (e.g. a carrier) initiates the process; the money refund follows later.

**Endpoint(s):** `POST /bookings/{bookingId}/release-offers`
**Unique to OSDM:** Yes — the release (resource invalidation separate from financial refund) is a rail-specific concept with no direct counterpart in TOMP-API, BoB, or FerryGateway. Note that OMSA and TOMP-API `cancel-package` combine resource release and financial settlement into a single atomic operation; OSDM's distinctive feature is the deliberate separation of the two steps, allowing a carrier to free reserved resources (seats, quotas) before the financial refund is processed.

### request partial release offer for a booking

The RETAILER requests a RELEASE OFFER covering only a subset of FULFILLMENTs or passengers in a booking, freeing selected resources while leaving the rest of the booking intact.

**Endpoint(s):** `POST /bookings/{bookingId}/release-offers` (with partial scope)
**Unique to OSDM:** Yes — same reasoning as full release; partial scope adds further specificity.

### accept release offer

The RETAILER confirms the RELEASE OFFER, executing the resource release. The affected FULFILLMENTs are invalidated; a subsequent refund flow handles financial settlement.

**Endpoint(s):** `PATCH /bookings/{bookingId}/release-offers/{releaseOfferId}`, `DELETE /bookings/{bookingId}/release-offers/{releaseOfferId}`
**Unique to OSDM:** Yes — same as release offer; the confirm step is part of the same OSDM-specific flow.

### request exchange offers

The RETAILER submits the FULFILLMENT IDs to be exchanged (and optionally an overrule code for carrier-initiated exchanges) to receive a list of EXCHANGE OFFERs for alternative trips or products. This does not yet change the booking state.

**Endpoint(s):** `POST /bookings/{bookingId}/exchange-offers`
**Unique to OSDM:** Partially — OMSA and TOMP-API handle exchanges by including the original package reference as context in a new offer search or booking, effectively an implicit exchange. What is OSDM-specific is the dedicated exchange-offer resource: a formal multi-step flow where EXCHANGE OFFERs are computed, presented to the RETAILER for selection, and then executed — distinct from a cancel + rebook pattern.

### select exchange offer and start the exchange process

The RETAILER selects an EXCHANGE OFFER and initiates an exchange operation, which pre-books the replacement booking parts while leaving the existing FULFILLMENTs unaffected until confirmation.

**Endpoint(s):** `POST /bookings/{bookingId}/exchange-operations`, `PATCH /bookings/{bookingId}/exchange-operations/{exchangeOperationId}`
**Unique to OSDM:** Yes — the multi-step exchange operation (pre-book replacement before releasing original) is OSDM-specific.

### confirm the exchange

The RETAILER confirms the exchange by calling `POST /bookings/{bookingId}/fulfillments`, which issues new FULFILLMENTs for the replacement booking parts and releases the original. The exchange operation can also be abandoned with DELETE.

**Endpoint(s):** `POST /bookings/{bookingId}/fulfillments`, `DELETE /bookings/{bookingId}/exchange-operations/{exchangeOperationId}`
**Unique to OSDM:** Partially — OMSA `confirm-package/execute` and TOMP-API booking confirmation serve the equivalent role of finalising a rebooked journey. What is OSDM-specific is the confirmation of an exchange via the fulfillment endpoint with automatic release of the original FULFILLMENTs as an atomic operation — an integral part of OSDM's dedicated exchange-operation resource model.

---

## View travel account

A TRAVEL ACCOUNT in OSDM represents an account-based entitlement: a `TravelPassAccount` (zone/region pass), a `MultiRideAccount` (limited number of journeys), or a `ReductionCardAccount` (reduction card used as a travel account). The use cases below cover viewing account status; actual sales using the account go through the standard offer/booking flow.

### view the travel account of a customer

The RETAILER or TRANSPORT CUSTOMER retrieves the current state of a TRAVEL ACCOUNT, including account type, issuer, remaining usage (balance, travel dates, or rides), and a history of consumptions.

**Endpoint(s):** `GET /travel-accounts`, `GET /travel-accounts` (with `travelAccount` query parameter)
**Unique to OSDM:** Yes — an explicit TRAVEL ACCOUNT resource representing persistent account-based ticketing state (pass balance, multi-ride quota, reduction card status) is unique to OSDM among the in-scope standards. OMSA and TOMP-API expose traveller attributes (USER PROFILE, entitlements, commercial profiles) as per-booking data associated with a package, but do not model a standing account with its own balance and consumption history independent of any specific booking.

---

## Use on-demand services

OSDM extends its booking flow to cover continuous (unscheduled) on-demand transport modes such as bicycles, e-bikes, scooters, and taxis. Integration can be shallow (OSDM provides a link to the provider app) or deep (OSDM manages the full allocation and usage lifecycle).

### find on-demand service in trip search

The RETAILER searches for trips that include a continuous on-demand service as a first-mile or last-mile leg. The trip planner returns a `ContinuousService` within the trip, and available pick-up places and vehicle types are included.

**Endpoint(s):** `POST /trips-collection`, `GET /availabilities/on-demand-services`
**Unique to OSDM:** Partially — TOMP-API supports multimodal packages that can include rail legs alongside on-demand mobility legs, and OMSA `search-offers/execute` can return mixed-mode itineraries. What is OSDM-specific is the `ContinuousService` integration within a rail-first trip search: on-demand legs are surfaced as `ContinuousServiceOfferPart` items within a trip returned by `/trips-collection`, allowing a single OSDM booking to cover both the rail leg and the first/last-mile on-demand component.

### book an on-demand service

The RETAILER books a `ContinuousServiceOfferPart` as part of the overall trip booking. The pricing model (fixed, pre-paid with post-use refund, or post-payment) is indicated in the `ContinuousServiceBookingPart`.

**Endpoint(s):** `POST /bookings`, `POST /bookings/{bookingId}/fulfillments`
**Unique to OSDM:** Partially — TOMP-API's core function is on-demand service booking, and OMSA `purchase-package/execute` can book multimodal packages including on-demand legs. What is OSDM-specific is the `ContinuousServiceOfferPart` model: the on-demand leg is booked as an integrated OFFER PART within the same OSDM booking as the rail leg, using OSDM's standard `POST /bookings` and fulfillment flow rather than a separate provider API call.

### allocate a vehicle for on-demand service

After the booking is confirmed, the TRANSPORT CUSTOMER selects a specific vehicle and pick-up place. The RETAILER PATCHes the FULFILLMENT with a `ContinuousServiceVehicleSelection`; the provider blocks the vehicle and returns credentials to start usage.

**Endpoint(s):** `GET /availabilities/continuousServiceBookingPart/{id}` (conceptual; maps to `GET /availabilities/on-demand-services`), `PATCH /fulfillments/{fulfillmentId}`
**Unique to OSDM:** Partially — OMSA `assign-asset/execute` and TOMP-API both support assigning a specific vehicle to a leg, which is functionally equivalent to vehicle allocation. What is OSDM-specific is the FULFILLMENT-based vehicle selection model: the TRANSPORT CUSTOMER patches a confirmed FULFILLMENT with a `ContinuousServiceVehicleSelection`, after which the provider blocks the vehicle and returns usage credentials (unlock codes, app deep links) within the FULFILLMENT response.

### start and end usage of on-demand service

The TRANSPORT CUSTOMER starts the on-demand service (unlocking the vehicle) and ends it (locking/returning), both modelled as PATCH operations on the `ContinuousServiceUsage` sub-resource of the FULFILLMENT. The provider updates pricing at the end of usage (post-payment) or issues a refund (pre-payment).

**Endpoint(s):** `PATCH /fulfillments/{fulfillmentId}/continuous-service-usage/{continuousServiceUsageId}`, `GET /fulfillments/{fulfillmentId}/continuous-service-usage/{continuousServiceUsageId}`, `DELETE /fulfillments/{fulfillmentId}/continuous-service-usage/{continuousServiceUsageId}`
**Unique to OSDM:** Partially — TOMP-API `start-leg`/`end-leg` processes and OMSA leg lifecycle management are direct functional equivalents for starting and ending usage of an on-demand service. What is OSDM-specific is the `ContinuousServiceUsage` sub-resource model: start and end are PATCH operations on a named sub-resource of the FULFILLMENT, with the provider updating pricing at end of usage (post-payment) or issuing a refund (pre-payment) within the same OSDM booking record.

---

## Complaints

OSDM implements an asynchronous complaints workflow aligned with EU Passenger Rights Regulation (PRR) and COTIF. Complaints concern service degradation (delays, cancellations, service quality issues) and can result in monetary compensation or vouchers.

### file a complaint

The RETAILER or TRANSPORT CUSTOMER creates a complaint record on behalf of a passenger, linking it to affected booking parts or trips. The complaint is processed asynchronously by the carrier or fare provider within PRR deadlines.

**Endpoint(s):** `POST /complaints`, `GET /complaints`, `GET /complaints/{complaintId}`, `PATCH /complaints/{complaintId}`
**Unique to OSDM:** Yes — a structured complaints endpoint aligned to EU PRR and COTIF is specific to OSDM; none of the other in-scope standards provide a complaints resource.

---

## Reimbursements

Reimbursements apply to tickets that were unused or only partially used, where the TRANSPORT CUSTOMER must provide proof (e.g. a carrier-issued non-use certificate per UIC IRS 90918-4 / TAP-TSI B.14). The flow is asynchronous and produces a claim record; if accepted, the overrule code `TICKET_UNUSED` enables a refund of otherwise non-refundable bookings.

### request reimbursement for an unused or partially used ticket

The RETAILER submits a reimbursement request on behalf of the TRANSPORT CUSTOMER, including proof documents where available. The provider investigates and, if the claim is valid, enables a refund via the standard refund flow with the appropriate overrule code.

**Endpoint(s):** `POST /bookings/{bookingId}/reimbursements`, `GET /bookings/{bookingId}/reimbursements`, `GET /bookings/{bookingId}/reimbursements/{reimbursementId}`
**Unique to OSDM:** Partially — OMSA and TOMP-API cover reimbursement for unused services via their refund/redress flows. What is OSDM-specific is the documentary proof requirement: the RETAILER may submit formal proof documents (e.g. a carrier-issued non-use certificate per UIC IRS 90918-4 / TAP-TSI B.14), and the OSDM reimbursement resource is explicitly aligned with EU Passenger Rights Regulation (PRR) and COTIF obligations, which have no equivalent framing in OMSA or TOMP-API.

---

## Inform the API consumer on changes

OSDM provides a webhook (asynchronous push) mechanism allowing the DISTRIBUTOR or FARE PROVIDER to notify the RETAILER of changes to bookings, fulfillments, or prices that occur outside the normal request/response flow (e.g. schedule changes, carrier-initiated cancellations, price adjustments).

### receive webhook notification on booking or fulfillment change

The RETAILER registers a webhook endpoint and receives push notifications when a booking, fulfillment, or booked-offer state changes on the provider side. The consumer then retrieves the updated booking to reconcile its own records.

**Endpoint(s):** Webhook (push, separate webhook YAML spec); `GET /bookings/{bookingId}`, `GET /bookings/{bookingId}/fulfillments`
**Unique to OSDM:** Partially — TOMP-API defines a notifications endpoint (`POST /notifications`) that serves a comparable purpose of proactively informing the consumer of booking or leg state changes. What is OSDM-specific is the definition of the webhook in a separate, dedicated webhook YAML specification (maintained alongside the main OSDM YAML), providing a formally standardised push contract rather than an optional callback pattern.

---

## Provide master data via the API

OSDM exposes read-only master-data endpoints that the RETAILER can poll periodically to build local reference data: PLACE catalogues, COACH LAYOUT definitions for graphical seat reservation, REDUCTION CARD types, FARE PRODUCTs, PRODUCT TAGs and PRODUCT TAG GROUPs, and zone definitions.

### retrieve places

The RETAILER searches for PLACE references by name substring or code to support origin/destination autocomplete and to resolve PLACE IDs used elsewhere in the API.

**Endpoint(s):** `GET /places`, `GET /places/{placeId}`
**Unique to OSDM:** Partially — place/stop lookup is a common capability across BoB and FerryGateway. Note that TOMP-API does not include an in-spec place search; it relies on external data sources (NeTEx, GBFS, CHB) for stop and place resolution, making OSDM's `GET /places` endpoint more self-contained for stop discovery.

### retrieve coach layouts

The RETAILER downloads COACH LAYOUT definitions (deck plans with seat positions, properties, and numbering) to render a graphical seat-selection map for the TRANSPORT CUSTOMER.

**Endpoint(s):** `GET /coach-layouts`, `GET /coach-layouts/{layoutId}`, `GET /coach-deck-layouts`, `GET /coach-deck-layouts/{layoutId}`
**Unique to OSDM:** Partially — OMSA and TOMP-API expose `GET /collections/assets/items` returning GeoJSON layouts of physical assets (vehicles, bays, berths), which can represent spatial layouts of assignable places. What is OSDM-specific is the structured COACH LAYOUT model: named seat positions with explicit place IDs, deck plans with rendered coach-map data, and seat properties (window, aisle, table, quiet zone) designed for graphical seat-selection rendering in rail booking UIs.

### retrieve reduction card types

The RETAILER downloads the provider's supported REDUCTION CARD types so it can present valid card options to the TRANSPORT CUSTOMER during an offer request.

**Endpoint(s):** `GET /reduction-cards`
**Unique to OSDM:** Partially — TOMP-API provides a card-type collection as comparable reference data for loyalty or entitlement cards. What is OSDM-specific is the reduction-card catalogue in the rail fare reduction context: cards are modelled as SALE DISCOUNT RIGHTs with associated eligibility rules, age requirements, and discount percentages that feed directly into the OSDM fare-calculation engine.

### retrieve products

The RETAILER fetches FARE PRODUCT definitions (name, description, conditions) by product ID or via a structured search with product tags and filters. This supports product catalogue browsing before an offer search.

**Endpoint(s):** `GET /products`, `GET /products/{productId}`, `POST /products-search`
**Unique to OSDM:** Yes — a structured FARE PRODUCT catalogue searchable by PRODUCT TAGs and other criteria is unique to OSDM among the in-scope standards.

### retrieve product tags and product tag groups

The RETAILER fetches the provider's PRODUCT TAG taxonomy — grouped tags that categorise products by region, validity, type, or other dimensions — to drive structured offer searches and product browsing UIs.

**Endpoint(s):** `GET /product-tags`
**Unique to OSDM:** Yes — PRODUCT TAG groups as a structured navigation taxonomy for non-trip-based offer search are specific to OSDM.

### retrieve zones

The RETAILER downloads ZONE definitions (geographic fare zones used in zone-based FARE PRODUCTs) to support zone-based offer searches and to display zone maps to the TRANSPORT CUSTOMER.

**Endpoint(s):** `GET /zones`
**Unique to OSDM:** No — zone-based fare structures and zone lookup exist in BoB and other standards, though OSDM's zone endpoint is specific to its implementation.

### retrieve passenger categories

The RETAILER fetches the provider's defined PASSENGER CATEGORies (age groups, passenger types) to populate passenger selection UIs and to map them correctly when constructing offer requests.

**Endpoint(s):** `GET /passenger-categories`
**Unique to OSDM:** No — equivalent passenger/traveller category reference data exists in BoB (`GET /productcat/traveller`, returning TRANSPORT CUSTOMER categories with eligibility conditions), FerryGateway (`GetPassengerAndVehicleTypesRequest`, returning age-band categories with min/max age bounds), and OMSA/TOMP-API (user profile catalogues defining passenger types and eligibility). OSDM's `GET /passenger-categories` is the rail-specific instantiation of this shared concept.
