# BoB → Transmodel Mapping

BoB (Biljetter och Bokning — Tickets and Booking) is a Swedish/Nordic interoperability standard for public transport ticketing and booking. It is structured as a family of nine sub-APIs that together cover the full lifecycle of a travel credential: authentication, product discovery and manifest creation, ticket issuance and lifecycle management, token management, traveller account and wallet operations, device key provisioning, validation, inspection, and participant metadata coordination.

This document maps every schema definition from each sub-API to the **Transmodel v6.2** reference data model. Where a BoB concept has no Transmodel equivalent — particularly BoB-specific cryptographic mechanisms such as JWTs, KDKs, MTB structures, and TICKLE macros — this is noted explicitly in the **Notes** column.

Match quality is indicated in the **Notes** column:
- **exact** — one-to-one semantic correspondence
- **partial** — overlapping scope; some attributes or nuances differ
- **none** — no Transmodel equivalent; BoB concept is standard-specific

---

## Booking API (v2.0.1)

The Booking API covers the creation, retrieval, confirmation, and cancellation of bookings, together with the associated ride and place information. The BoB YAML specification contains inline `x-semantics` annotations on every field; this section reorganises those annotations into the project mapping table format.

### bookingCall

A `bookingCall` is the request body used to create a preliminary booking. It carries a signed MTB (Mobile Ticket Binary) manifest, a traveller identifier, and a request idempotency key.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| bookingCall | (object) | CUSTOMER RESERVATION CREATION EVENT | — | exact — initiates reservation of a seat or space on a service journey |
| bookingCall | (object) | BOOK VEHICLE RIDE EVENT | — | partial — when booking is for demand-responsive transport |
| bookingCall | manifest | TRAVEL DOCUMENT | — | partial — signed MTB manifest carries fare product and entitlement specification; base64url encoding is BoB-specific |
| bookingCall | manifest | SALES OFFER PACKAGE | — | partial — the manifest content specifies the applicable sales offer |
| bookingCall | travellerId | TRANSPORT CUSTOMER | id | exact — uniquely identifies a registered customer; partial when anonymous token |
| bookingCall | travellerId | CUSTOMER ACCOUNT | id | exact — may reference the customer account rather than the customer directly |
| bookingCall | travellerPhone | TRANSPORT CUSTOMER | contactDetails/phone | partial — contact attribute; no dedicated Transmodel attribute at this granularity |
| bookingCall | requestId | — | — | none — API-level idempotency key for deduplication of retransmissions; not a Transmodel business concept |

### booking

A `booking` represents the state of a booking — preliminary or confirmed — including its status, associated rides, and traveller information.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| booking | (object) | CUSTOMER PURCHASE PACKAGE | — | exact — completed purchase of a SALES OFFER PACKAGE giving access rights for one or more rides |
| booking | (object) | CUSTOMER RESERVATION CREATION ENTRY | — | partial — when booking is in 'pending' status (reservation not yet confirmed/paid) |
| booking | (object) | FARE CONTRACT | — | partial — encapsulates contractual basis for transport consumption; a FARE CONTRACT may span multiple bookings |
| booking | bookingId | CUSTOMER PURCHASE PACKAGE | id | exact — unique booking identifier |
| booking | bookingId | FARE CONTRACT | id | exact — alternatively the identifier of the fare contract |
| booking | confirmBefore | PURCHASE WINDOW | (upper bound) | partial — deadline for completing purchase; function of PURCHASE WINDOW |
| booking | confirmBefore | BOOKING POLICY | — | partial — time constraint condition in BOOKING POLICY |
| booking | confirmedWhen | CUSTOMER RESERVATION CREATION ENTRY | eventTime | partial — timestamp of confirmation action |
| booking | confirmedWhen | SALES TRANSACTION | — | partial — recorded in audit trail of FARE CONTRACT ENTRY |
| booking | status | CUSTOMER PURCHASE STATUS | — | exact — see BookingStatusType below |
| booking | rides | PT RIDE LEG | — | partial — ordered list of legs within a PT TRIP or TRIP PATTERN |
| booking | rides | SINGLE JOURNEY | — | partial — when each ride corresponds to movement on a specific service journey |
| booking | travellerId | TRANSPORT CUSTOMER | id | exact — registered customer; partial when anonymous |
| booking | travellerId | CUSTOMER ACCOUNT | id | exact — may reference customer account |
| booking | travellerPhone | TRANSPORT CUSTOMER | contactDetails/phone | partial |
| booking | confirmationCode | SERVICE ACCESS CODE | — | partial — human-readable code to prove/access a service |
| booking | confirmationCode | TRAVEL DOCUMENT | — | partial — when code serves as boarding entitlement proof |
| booking | travellerInformation | PT SITUATION MESSAGE | — | partial — free-text journey information may include disruption notices |
| booking | travellerInformation | — | — | none — no single Transmodel concept covers a free-text passenger-facing journey note on a booking |

### BookingStatusType

| BoB Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| pending | CUSTOMER PURCHASE STATUS (booked) | partial — reservation made, not yet confirmed/paid |
| confirmed | CUSTOMER PURCHASE STATUS (paid/activated) | exact |
| cancelled | CUSTOMER RESERVATION CANCELLATION EVENT | exact |
| miss | RIDER NO SHOW EVENT | exact — passenger no-show |
| completed | CUSTOMER PURCHASE STATUS (consumed) | exact |

### statusChangeRequest

Used to confirm or cancel an existing booking by providing the target status and a signed MTB as proof of purchase.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| statusChangeRequest | (object) | CUSTOMER RESERVATION CANCELLATION EVENT | — | exact — when status is set to 'cancelled' |
| statusChangeRequest | (object) | CUSTOMER PRODUCT PURCHASE EVENT | — | partial — when status is set to 'confirmed', completing the purchase |
| statusChangeRequest | status | CUSTOMER PURCHASE STATUS | — | exact — requested status transition |
| statusChangeRequest | status | FARE CONTRACT STATUS | — | exact — transition in fare contract lifecycle |
| statusChangeRequest | mtb | TRAVEL DOCUMENT | — | partial — signed MTB is the machine-readable materialisation of the fare product access right |
| statusChangeRequest | mtb | MEDIUM ACCESS DEVICE | — | partial — when MTB is stored on a mobile device |

### cancellation

Records the cancellation of a booking.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| cancellation | (object) | CUSTOMER RESERVATION CANCELLATION ENTRY | — | exact — records cancellation of a reservation by a TRANSPORT CUSTOMER |
| cancellation | (object) | FARE PRODUCT CANCELLATION ENTRY | — | partial — when cancellation also rolls back a purchase |
| cancellation | cancellationId | CUSTOMER RESERVATION CANCELLATION ENTRY | id | partial — Transmodel identifies the cancellation via its LOG ENTRY / FARE CONTRACT ENTRY, not a separate cancellationId |

### ride

A `ride` is one leg of travel on a single public transport vehicle between a boarding and alighting point.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ride | (object) | PT RIDE LEG | — | exact — one leg on a single PT vehicle between two stops |
| ride | (object) | SINGLE JOURNEY | — | partial — planned movement on a specific service journey |
| ride | (object) | SERVICE JOURNEY | — | partial — linked via serviceJourneyReference |
| ride | boardingPlace | SCHEDULED STOP POINT | id | exact — functional boarding point in the network topology |
| ride | boardingPlace | STOP PLACE | — | partial — physical stop facility |
| ride | boardingPlace | BOARDING POSITION | — | partial — sub-stop granularity |
| ride | alightingPlace | SCHEDULED STOP POINT | id | exact — functional alighting point |
| ride | alightingPlace | STOP PLACE | — | partial |
| ride | alightingPlace | BOARDING POSITION | — | partial |
| ride | serviceJourneyReference | SERVICE JOURNEY | id | exact — identifies the passenger-carrying vehicle journey |
| ride | serviceJourneyReference | DATED VEHICLE JOURNEY | id | partial — when reference identifies a journey on a specific operating day |
| ride | departureDateTime | DEPARTURE | departureTime | exact — scheduled departure at boarding stop |
| ride | departureDateTime | DATED DEPARTURE | — | exact — combined with operating-day context |
| ride | arrivalDateTime | ARRIVAL | arrivalTime | exact — scheduled arrival at alighting stop |
| ride | arrivalDateTime | DATED ARRIVAL | — | exact |
| ride | productRequested | FARE PRODUCT | — | partial — indicates a supplementary fare product is requested for the leg |
| ride | productRequested | PRE-ASSIGNED FARE PRODUCT | — | partial — Transmodel models this as ACCESS RIGHT IN PRODUCT, not a boolean |
| ride | earlierArrivalAcceptable | TRIP REQUEST POLICY | — | partial — passenger preference allowing earlier arrival than requested |
| ride | earlierArrivalAcceptable | CONVENTIONAL MODE TRAVEL PREFERENCES | — | partial — expressed through BOOKING POLICY flexibility parameters |
| ride | laterDepartureAcceptable | TRIP REQUEST POLICY | — | partial — passenger preference allowing later departure |
| ride | laterDepartureAcceptable | BOOKING POLICY | — | partial — expressed through USAGE PARAMETER flexibility settings |

### place

A `place` represents a boarding or alighting location, identified either by a stop identifier or by geographic coordinates.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| place | (object) | SCHEDULED STOP POINT | — | exact — when identified by stopId |
| place | (object) | STOP PLACE | — | partial — physical stop facility |
| place | (object) | PLACE | — | partial — general geographic place when only coordinates are given |
| place | stopId | SCHEDULED STOP POINT | id | exact — network topology stop reference |
| place | stopId | STOP PLACE | id | partial — physical stop reference |
| place | coordinate | POINT | — | partial — geographic location of the place |
| place | coordinate | LOCATION | — | partial — WGS84 coordinates as a Transmodel LOCATION |

### geoPosition

WGS84 decimal geographic coordinates. Shared definition reused across multiple BoB APIs.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| geoPosition | (object) | LOCATION | — | exact — WGS84 decimal coordinate corresponds to Transmodel LOCATION (longitude, latitude, altitude) |
| geoPosition | (object) | POINT | — | partial — LOCATION is an attribute of POINT subtypes in Transmodel; in BoB it is a standalone object |
| geoPosition | lat | LOCATION | latitude | exact |
| geoPosition | long | LOCATION | longitude | exact |
| geoPosition | alt | LOCATION | altitude | exact — optional altitude component |

### API endpoints

| Endpoint | Operation | Transmodel Concept(s) | Notes |
|----------|-----------|----------------------|-------|
| GET /booking | getAllBookings | CUSTOMER PURCHASE PACKAGE | partial — retrieves list of bookings for a traveller or request ID |
| POST /booking | requestBooking | CUSTOMER RESERVATION CREATION EVENT | exact — creates a preliminary booking |
| GET /booking/{bookingId} | getBooking | CUSTOMER PURCHASE PACKAGE | exact — retrieves a specific booking by id |
| PATCH /booking/{bookingId} | confirmBooking | CUSTOMER PRODUCT PURCHASE EVENT / CUSTOMER RESERVATION CANCELLATION EVENT | exact — transitions booking status to confirmed or cancelled |

### Summary

| Domain | BoB Types | Primary Transmodel Concepts |
|--------|-----------|----------------------------|
| Booking creation | bookingCall | CUSTOMER RESERVATION CREATION EVENT, TRAVEL DOCUMENT |
| Booking state | booking, BookingStatusType | CUSTOMER PURCHASE PACKAGE, CUSTOMER PURCHASE STATUS, FARE CONTRACT |
| Status transition | statusChangeRequest | CUSTOMER RESERVATION CANCELLATION EVENT, CUSTOMER PRODUCT PURCHASE EVENT |
| Cancellation record | cancellation | CUSTOMER RESERVATION CANCELLATION ENTRY |
| Ride / leg | ride | PT RIDE LEG, SERVICE JOURNEY, DATED VEHICLE JOURNEY |
| Stop / place | place | SCHEDULED STOP POINT, STOP PLACE |
| Geography | geoPosition | LOCATION |

---

## Authentication API (v1.3.3)

The Authentication API issues JWT bearer tokens (JWS Compact Serialization per RFC 7515/7519) to authenticated entities using mutual TLS. These tokens carry BoB-specific authorization claims and are used as the `X-BoB-AuthToken` header across all other BoB APIs.

### authToken

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| authToken | (object) | — | — | none — BoB-specific JWT credential; no direct Transmodel equivalent. No direct Transmodel equivalent — BoB-specific security mechanism |
| authToken | jwtCompact | — | — | none — JWS Compact Serialization of the token; cryptographic encoding is BoB-specific. No direct Transmodel equivalent — BoB-specific security mechanism |
| authToken | header | — | — | none — JWS protected header (RFC 7515); see jwsHeader below |
| authToken | payload | — | — | none — JWT claims payload; see jwtClaim below |

### jwsHeader

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| jwsHeader | (object) | — | — | none — RFC 7515 JWS header; no direct Transmodel equivalent. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwsHeader | alg | — | — | none — JWA algorithm identifier (e.g., ES256). No direct Transmodel equivalent — BoB-specific security mechanism |
| jwsHeader | kid | — | — | none — Key identifier linking to the signing key in the participant metadata register. No direct Transmodel equivalent — BoB-specific security mechanism |

### jwtClaim

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| jwtClaim | (object) | — | — | none — RFC 7519 JWT claims set. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwtClaim | iss | OPERATOR | id | partial — the issuing participant corresponds to a Transmodel OPERATOR or DATA SOURCE |
| jwtClaim | sub | EQUIPMENT | id | partial — the subject entity (e.g., a validator) corresponds to a Transmodel device or operator role; no direct concept |
| jwtClaim | nbf | VALIDITY CONDITION | fromDate | partial — start of claim validity window maps to a Transmodel VALIDITY CONDITION |
| jwtClaim | iat | — | — | none — token issuance timestamp; audit/security metadata with no Transmodel structural equivalent |
| jwtClaim | exp | VALIDITY CONDITION | toDate | partial — expiration of claim maps to end of VALIDITY CONDITION |
| jwtClaim | bobHok | — | — | none — SHA1 fingerprint of TLS client certificate (holder-of-key). No direct Transmodel equivalent — BoB-specific security mechanism |
| jwtClaim | bobAuthZ | — | — | none — BoB authorization class (e.g., "val" for validator). No direct Transmodel equivalent — BoB-specific security mechanism |

### API endpoints

| Endpoint | Operation | Transmodel Concept(s) | Notes |
|----------|-----------|----------------------|-------|
| GET /auth/{entityId} | getAuthenticationToken | — | none — issues a JWT; authentication/authorization infrastructure with no Transmodel data model equivalent |

---

## Product API (v3.4.0)

The Product API provides product discovery (filter and retrieve products), category lookups, manifest creation (a pre-purchase signed MTB that authorises ticket issuance), and PDS (Product Data Set) lookups for offline validators.

### product

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| product | (object) | FARE PRODUCT | — | exact — an immaterial marketable element with defined access rights and pricing |
| product | (object) | SALES OFFER PACKAGE | — | partial — the full purchasable offer including conditions, price, and fulfilment channel |
| product | productId | FARE PRODUCT | id | exact |
| product | productTitle | FARE PRODUCT | name | exact — short human-readable name |
| product | productDescription | FARE PRODUCT | description | exact — long descriptive text |
| product | validityPeriod | VALIDITY PERIOD | duration | partial — relative duration from issuance; deprecated in favour of temporalValidity |
| product | genericCategories | FARE PRODUCT | — | partial — key-value dictionary of standardised category names (e.g., fareClass, transportMode); maps to ACCESS RIGHT PARAMETER ASSIGNMENT or EQUIPMENT VALIDITY PARAMETERS |
| product | productCategoryId | CHARGING MOMENT | — | partial — classifies when/how the ticket is valid (e.g., single trip, day pass); aligns with CHARGING MOMENT classification |
| product | fareCategoryId | FARE CLASS | id | partial — deprecated; superseded by genericCategories; maps to FARE CLASS or USER PROFILE |
| product | travellerCategoryId | USER PROFILE | id | partial — identifies the eligible traveller type (adult, youth, student, etc.) |
| product | travellersPerCategory | GROUP TICKET | — | partial — describes the composition of travellers entitled under the product |
| product | discountCodes | SALE DISCOUNT RIGHT | — | partial — codes applied to unlock discounted pricing; maps to SALE DISCOUNT RIGHT |
| product | fares | FARE PRICE | — | exact — list of fare parts (base fare, discounts, VAT) |
| product | productExpire | VALIDITY PERIOD | toDate | partial — latest date/time at which the product offer remains purchasable |
| product | productProperties | EQUIPMENT VALIDITY PARAMETERS | — | partial — supplementary product options (seat class, meal, bike carriage, etc.); maps to ACCESS RIGHT PARAMETER ASSIGNMENT or GENERIC PARAMETER ASSIGNMENT |
| product | temporalValidity | VALIDITY PERIOD | — | exact — structured temporal validity replacing the deprecated validityPeriod string |
| product | spatialValidity | FARE ZONE, TOPOGRAPHIC PLACE | — | partial — geographic constraints on product usage; maps to FARE ZONE for zone-based, TOPOGRAPHIC PLACE for area-based |
| product | conditions | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial — textual usage conditions (e.g., peak/off-peak); maps to GENERIC PARAMETER ASSIGNMENT or ACCESS RIGHT PARAMETER ASSIGNMENT |

### temporalValidity

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| temporalValidity | (object) | VALIDITY PERIOD | — | exact — structured definition of when a ticket may be used |
| temporalValidity | absoluteValidity | VALIDITY PERIOD | fromDate / toDate | exact — fixed calendar interval in ISO 8601 time interval format |
| temporalValidity | active | — | — | partial — flag indicating ticket is issued in immediately active state; maps to CUSTOMER PRODUCT ACTIVATION EVENT semantics |
| temporalValidity | relativeValidity | FREQUENCY OF USE | — | partial — duration from activation; maps to relative VALIDITY PERIOD or FREQUENCY OF USE |
| temporalValidity | relativeValidityStart | VALIDITY PERIOD | fromDate | partial — start anchor for relative validity if not at activation time |
| temporalValidity | numberOfActivations | FREQUENCY OF USE | maximumNumberOfUsages | partial — total allowed activations of the product |

### spatialValidity

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| spatialValidity | (object) | FARE ZONE, TOPOGRAPHIC PLACE | — | partial — geographic scope within which the product grants access rights |
| spatialValidity | routes | ROUTE | — | partial — ordered list of stop/zone/coordinate sequences defining valid travel paths |
| spatialValidity | areas | TOPOGRAPHIC PLACE | — | partial — geographic areas (circles, polygons) defining the spatial validity boundary |
| spatialValidity | polylines | — | — | none — polygonal paths; no direct Transmodel structural equivalent |
| spatialValidity | groups | FARE ZONE | id | partial — named groups of stop areas or zones (e.g., Zone 1, Zone A) |

### productFilter

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productFilter | (object) | FARE PRODUCT REQUEST | — | partial — request parameters for discovering available FARE PRODUCTs |
| productFilter | route | ROUTE | — | partial — ordered stop/zone sequence filter |
| productFilter | group | FARE ZONE | id | partial — zone-group based spatial filter |
| productFilter | area | TOPOGRAPHIC PLACE | — | partial — geographic area filter |
| productFilter | genericCategories | FARE PRODUCT REQUEST FILTER | — | partial — structured category filters on the request |
| productFilter | fareCategoryIds | FARE CLASS | id | partial — deprecated; fare class filter |
| productFilter | productCategoryIds | CHARGING MOMENT | — | partial — product type filter |
| productFilter | travellerCategoryIds | USER PROFILE | id | partial — passenger category filter |
| productFilter | travellersPerCategory | GROUP TICKET | — | partial — multi-passenger group composition |
| productFilter | discountCodes | SALE DISCOUNT RIGHT | — | partial — discount code filter |
| productFilter | productProperties | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial — supplementary property filter |
| productFilter | adjacencies | FARE SCHEDULED STOP POINT | — | partial — ingress/egress border points for cross-participant products |
| productFilter | temporal | FARE PRODUCT REQUEST FILTER | — | partial — time-based constraints on the product search |
| productFilter | isPartOfInternationalTrip | — | — | none — BoB flag with no direct Transmodel structural equivalent |

### productCategory / fareCategory / travellerCategory / genericCategory

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productCategory | (object) | CHARGING MOMENT | — | partial — classifies validity type (single, day pass, 30-day); aligns with CHARGING MOMENT or FARE DEMAND FACTOR |
| productCategory | productCategoryId | CHARGING MOMENT | id | partial |
| productCategory | productCategoryDescription | CHARGING MOMENT | description | partial |
| fareCategory | (object) | FARE CLASS | — | partial — deprecated; superseded by genericCategories; describes how travel affects the fare (1st/2nd class, express, night) |
| fareCategory | fareCategoryId | FARE CLASS | id | partial |
| fareCategory | fareCategoryDescription | FARE CLASS | description | partial |
| travellerCategory | (object) | USER PROFILE | — | exact — defines the eligible passenger type with conditions |
| travellerCategory | travellerCategoryId | USER PROFILE | id | exact |
| travellerCategory | travellerCategoryDescription | USER PROFILE | description | exact |
| travellerCategory | conditions | CUSTOMER ELIGIBILITY | — | partial — age, proof-document requirements map to CUSTOMER ELIGIBILITY or ENTITLEMENT REQUIRED |
| genericCategory | (object) | EQUIPMENT VALIDITY PARAMETERS | — | partial — standardised or proprietary key-value categories (fareClass, transportMode); maps to EQUIPMENT VALIDITY PARAMETERS or ACCESS RIGHT PARAMETER ASSIGNMENT |
| genericCategory | name | — | — | partial — standardised category key (e.g., fareClass) |
| genericCategory | description | — | — | partial — human-readable label |
| genericCategory | filterable | FARE PRODUCT REQUEST FILTER | — | partial — controls whether this category acts as a filter dimension |
| genericCategory | mandatoryInFilter | FARE PRODUCT REQUEST FILTER | — | partial |
| genericCategory | proprietary | — | — | none — BoB extension flag; no Transmodel equivalent |
| genericCategory | values | EQUIPMENT VALIDITY PARAMETERS | — | partial — enumerated allowed values |

### condition

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| condition | (object) | CUSTOMER ELIGIBILITY | — | exact — specifies a restriction a traveller must satisfy to be eligible |
| condition | age | USER PROFILE | minimumAge / maximumAge | exact — age-band restriction |
| condition | proofsRequired | ENTITLEMENT REQUIRED | — | partial — required proof documents (e.g., student card, ID) map to ENTITLEMENT REQUIRED |
| condition | description | CUSTOMER ELIGIBILITY | description | exact |

### fare

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| fare | (object) | FARE PRICE | — | exact — monetary amount with currency and VAT breakdown |
| fare | amount | FARE PRICE | amount | exact — net fare excluding VAT |
| fare | currency | FARE PRICE | currency | exact — ISO 4217 currency code |
| fare | label | FARE PRICE | name | partial — human-readable label for a fare part (e.g., "Group discount") |
| fare | vatAmount | FARE PRICE | vatAmount | exact |
| fare | vatPercent | FARE PRICE | vatPercent | exact |

### productSetManifest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productSetManifest | (object) | SALES TRANSACTION | — | partial — represents a pre-purchase authorisation token for a set of products; closest to a SALES TRANSACTION record |
| productSetManifest | manifestId | SALES TRANSACTION | id | partial |
| productSetManifest | productSetTitle | SALES OFFER PACKAGE | name | partial |
| productSetManifest | productSetDescription | SALES OFFER PACKAGE | description | partial |
| productSetManifest | manifest | TRAVEL DOCUMENT | — | partial — cryptographically signed MTB manifest; contains serialised fare product specification. No direct Transmodel equivalent — BoB-specific security mechanism |
| productSetManifest | manifestExpire | VALIDITY PERIOD | toDate | partial — time-to-live of the purchase authorisation token |
| productSetManifest | distinct | — | — | none — single-use manifest flag; no Transmodel structural equivalent |
| productSetManifest | bookingRequired | BOOKING POLICY | — | partial — signals that the product requires a booking; maps to BOOKING POLICY |
| productSetManifest | fares | FARE PRICE | — | exact — list of fare parts for the manifest |
| productSetManifest | products | SALES OFFER PACKAGE | — | partial — list of product identifiers included in the manifest |
| productSetManifest | discountCodes | SALE DISCOUNT RIGHT | — | partial — codes applied in the purchase |
| productSetManifest | recoverTicketIds | TRAVEL DOCUMENT | id | partial — tickets being recovered/replaced in this transaction |

### productManifestRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productManifestRequest | (object) | CUSTOMER PRODUCT PURCHASE EVENT | — | partial — request to initiate a purchase for a set of products |
| productManifestRequest | recoverTickets | CUSTOMER MEDIA APPLICATION RESTORE EVENT | — | partial — MTBs being recovered into the new purchase transaction |
| productManifestRequest | productSelections | SALES OFFER PACKAGE | — | partial — selected products with their property assignments |
| productManifestRequest | discountCodes | SALE DISCOUNT RIGHT | — | partial |

### productSelection

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productSelection | (object) | CUSTOMER PURCHASE PACKAGE ELEMENT | — | partial — a single product choice within a purchase |
| productSelection | productId | FARE PRODUCT | id | exact |
| productSelection | productProperties | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial — specific property values selected for this product instance |

### adjacency

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| adjacency | (object) | FARE SCHEDULED STOP POINT | — | partial — border crossing point with another participant; maps to a BORDER POINT or FARE SCHEDULED STOP POINT |
| adjacency | pid | OPERATOR | id | partial — the neighbouring participant (transport operator) |
| adjacency | coordinate | LOCATION | — | partial — geographical position of the border crossing |
| adjacency | extendedValidityPeriod | VALIDITY PERIOD | duration | partial — deprecated; requested validity extension at cross-participant boundary |

### route / group / area / circle / polygon / polyline

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| route | (object) | ROUTE | — | partial — ordered list of stop/zone/coordinate waypoints defining a spatial filter or validity constraint |
| route | stopId | SCHEDULED STOP POINT | id | exact |
| route | groupId | FARE ZONE | id | partial — zone or other group identifier |
| route | coordinate | LOCATION | — | partial |
| group | (object) | FARE ZONE | — | partial — named collection of stop areas or other spatial entities |
| group | groupType | — | — | none — proprietary group type discriminator (e.g., "zone") |
| group | groupIds | FARE ZONE | id | partial |
| area | (object) | TOPOGRAPHIC PLACE | — | partial — union of geographic shapes defining a spatial boundary |
| circle | (object) | TOPOGRAPHIC PLACE | — | partial — circular geographic area |
| circle | center | LOCATION | — | partial |
| circle | radius | — | — | none — radius in metres; no direct Transmodel structural equivalent |
| polygon | (object) | TOPOGRAPHIC PLACE | — | partial — polygonal geographic area defined by a sequence of coordinates |
| polyline | (object) | — | — | none — polygonal path; no direct Transmodel structural equivalent |

### pdsInfo

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| pdsInfo | (object) | FARE PRODUCT | — | partial — product data set entry used by offline validators to look up product metadata from MTB `pds` attribute |
| pdsInfo | pds | FARE PRODUCT | id | partial — PDS code embedded in MTB |
| pdsInfo | title | FARE PRODUCT | name | exact |
| pdsInfo | description | FARE PRODUCT | description | exact |
| pdsInfo | productCategoryId | CHARGING MOMENT | id | partial |

### productPropertyDeclaration / productProperty / productPropertyFilter

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productPropertyDeclaration | (object) | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial — declares an available product option (e.g., meal choice, seat class, bike supplement) |
| productPropertyDeclaration | name | ACCESS RIGHT PARAMETER ASSIGNMENT | id | partial |
| productPropertyDeclaration | group | — | — | none — grouping of related property options; no Transmodel structural equivalent |
| productPropertyDeclaration | values | EQUIPMENT VALIDITY PARAMETERS | — | partial — enumerated valid values with optional surcharges |
| productPropertyDeclaration | mandatory | ENTITLEMENT REQUIRED | — | partial — mandatory properties equate to required access right parameters |
| productPropertyDeclaration | surcharges | FARE PRICE | — | partial — additional costs for specific property values |
| productProperty | (object) | CUSTOMER PURCHASE PARAMETER ASSIGNMENT | — | partial — a selected assignment of a product property value for a specific purchase |
| productProperty | name | CUSTOMER PURCHASE PARAMETER ASSIGNMENT | — | partial |
| productProperty | value | CUSTOMER PURCHASE PARAMETER ASSIGNMENT | — | partial |
| productPropertyFilter | (object) | FARE PRODUCT REQUEST FILTER | — | partial — property-name/value pair used as a search filter |

### productCondition

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productCondition | (object) | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial — free-text usage condition with a category tag |
| productCondition | category | — | — | partial — recommended category names (temporal, spatial, etc.) map loosely to VALIDITY PARAMETER types |
| productCondition | text | ACCESS RIGHT PARAMETER ASSIGNMENT | description | partial |

### travellersPerCategory

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travellersPerCategory | (object) | GROUP TICKET | — | partial — describes the passenger composition entitled under the product |
| travellersPerCategory | cat | USER PROFILE | id | partial — traveller category identifier |
| travellersPerCategory | cls | FARE CLASS | id | partial — class of service |
| travellersPerCategory | tra | GROUP TICKET | maximumNumberInGroup | partial — number of travellers in this category |
| travellersPerCategory | trs | TRANSPORT CUSTOMER | id | partial — named traveller identifiers |
| travellersPerCategory | sts | — | — | none — seating reservations; no direct Transmodel concept in fare/product model |

---

## Ticket API (v3.4.0)

The Ticket API manages the full lifecycle of an issued ticket: issuance from a manifest, retrieval, updating, refund, recovery, revocation, suspension, and hindrance. It also records validation/inspection events against each ticket.

### ticket

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticket | (object) | TRAVEL DOCUMENT | — | exact — the materialisation of a FARE PRODUCT as a specific, unique credential |
| ticket | ticketId | TRAVEL DOCUMENT | id | exact — unique identifier for the issued ticket |
| ticket | issuedAt | CUSTOMER FULFILMENT EVENT | eventTime | exact — timestamp of ticket issuance |
| ticket | ticketPayload | TRAVEL DOCUMENT | — | partial — base64url-encoded JSON payload embedded in the ticket; BoB-specific encoding |
| ticket | mtb | TRAVEL DOCUMENT | — | partial — signed MTB (Mobile Ticket Binary) is the cryptographically secured carrier of the access right. No direct Transmodel equivalent — BoB-specific security mechanism |
| ticket | tokenId | MEDIUM ACCESS DEVICE | id | partial — identifier of the token/card to which the ticket is bound |
| ticket | tokenIssuer | OPERATOR | id | partial — participant that issued the token |
| ticket | ticketHolder | TRANSPORT CUSTOMER | — | partial — the registered holder of the ticket |
| ticket | activeStatus | CUSTOMER PURCHASE STATUS | — | partial — whether the ticket is currently active (usable) |
| ticket | activationStatus | TRAVEL DOCUMENT SECURITY LISTING | — | partial — detailed activation state including timing and activation counts |
| ticket | recoverStatus | CUSTOMER MEDIA RESTORE EVENT | — | partial — whether the ticket has been recovered (re-issued to replacement media) |
| ticket | refundStatus | CUSTOMER REFUND EVENT | — | partial — whether the ticket has been refunded |
| ticket | refundableStatus | FARE PRODUCT REFUND ENTRY | — | partial — whether the ticket is eligible for refund and the refundable amount |
| ticket | revokeStatus | ACCOUNT SUSPEND ENTRY | — | partial — revocation adds the ticket to the blacklist; maps to ACCOUNT SUSPEND ENTRY or FARE CONTRACT SECURITY LISTING |
| ticket | hinderedStatus | FARE EASEMENT | — | partial — indicates the passenger was prevented from using the ticket (disruption, operator failure); maps to FARE EASEMENT concept |
| ticket | suspendedStatus | ACCOUNT SUSPEND ENTRY | — | partial — temporary suspension of the ticket from use |

### ticketHolder

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketHolder | (object) | TRANSPORT CUSTOMER | — | exact — the registered holder of a travel credential |
| ticketHolder | travellerId | TRANSPORT CUSTOMER | id | exact |
| ticketHolder | travellerName | TRANSPORT CUSTOMER | name | exact |

### manifestCall

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| manifestCall | (object) | CUSTOMER PRODUCT PURCHASE EVENT | — | partial — the act of presenting a signed manifest to issue tickets |
| manifestCall | manifest | TRAVEL DOCUMENT | — | partial — signed MTB manifest; cryptographic authorisation token. No direct Transmodel equivalent — BoB-specific security mechanism |
| manifestCall | ticketHolder | TRANSPORT CUSTOMER | — | partial — registers traveller as ticket holder |
| manifestCall | deviceId | MEDIUM ACCESS DEVICE | id | partial — device to which the ticket will be bound |
| manifestCall | deviceSignatureIssuer | OPERATOR | id | partial — participant providing the device signing capability |
| manifestCall | signatureLifetime | — | — | none — maximum lifetime in seconds for the issuer signature. No direct Transmodel equivalent — BoB-specific security mechanism |
| manifestCall | tokenIssuer | OPERATOR | id | partial — participant issuing the token |
| manifestCall | tokenId | MEDIUM ACCESS DEVICE | id | partial |
| manifestCall | tokenPublicKey | — | — | none — JWK public key for token; cryptographic primitive. No direct Transmodel equivalent — BoB-specific security mechanism |
| manifestCall | recoverTicketId | TRAVEL DOCUMENT | id | partial — ticket being replaced in a recovery transaction |
| manifestCall | issueMtb | — | — | none — flag requesting MTB to be returned on issuance; protocol-level option |
| manifestCall | requestId | — | — | none — idempotency key; no Transmodel structural equivalent |
| manifestCall | startOfValidity | VALIDITY PERIOD | fromDate | partial — requested start of relative validity |
| manifestCall | bookingId | CUSTOMER PURCHASE PACKAGE | id | partial — links the ticket issuance to an existing booking |

### ticketIssueResponse

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketIssueResponse | (object) | CUSTOMER FULFILMENT EVENT | — | partial — response confirming successful ticket issuance |
| ticketIssueResponse | ticketIds | TRAVEL DOCUMENT | id | exact — array of issued ticket identifiers |
| ticketIssueResponse | settlementId | SALES TRANSACTION | id | partial — unique settlement reference for billing/accounting |
| ticketIssueResponse | ticketBundle | CUSTOMER PURCHASE PACKAGE | — | partial — grouping of issued tickets from the same manifest |

### ticketBundle

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketBundle | (object) | CUSTOMER PURCHASE PACKAGE | — | partial — a group of tickets issued from the same manifest, forming a logical purchase unit |
| ticketBundle | ticketBundleId | CUSTOMER PURCHASE PACKAGE | id | partial |
| ticketBundle | ticketIds | TRAVEL DOCUMENT | id | exact — constituent ticket identifiers |
| ticketBundle | settlementId | SALES TRANSACTION | id | partial — settlement/billing reference |
| ticketBundle | manifestId | SALES TRANSACTION | — | partial — links back to the originating product manifest |
| ticketBundle | mtb | TRAVEL DOCUMENT | — | partial — signed MTB for the bundle. No direct Transmodel equivalent — BoB-specific security mechanism |

### ticketBundleRequest / ticketBundleUpdateRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketBundleRequest | ticketIds | TRAVEL DOCUMENT | id | exact |
| ticketBundleRequest | deviceId | MEDIUM ACCESS DEVICE | id | partial — device binding |
| ticketBundleRequest | deviceSignatureIssuer | OPERATOR | id | partial |
| ticketBundleRequest | signatureLifetime | — | — | none — No direct Transmodel equivalent — BoB-specific security mechanism |
| ticketBundleRequest | tokenIssuer | OPERATOR | id | partial |
| ticketBundleRequest | tokenId | MEDIUM ACCESS DEVICE | id | partial |
| ticketBundleRequest | tokenPublicKey | — | — | none — No direct Transmodel equivalent — BoB-specific security mechanism |

### ticketActiveStatus / ticketActivationStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketActiveStatus | (boolean) | CUSTOMER PURCHASE STATUS | — | partial — simple flag indicating whether the ticket is currently usable |
| ticketActivationStatus | (object) | TRAVEL DOCUMENT SECURITY LISTING | — | partial — detailed activation state; closest Transmodel concept tracks security/validity state of a travel document |
| ticketActivationStatus | active | CUSTOMER PURCHASE STATUS | — | partial |
| ticketActivationStatus | relativeValidity | VALIDITY PERIOD | duration | partial — duration of each activation window |
| ticketActivationStatus | latestActivation | FARE TRIP ACTIVATION ENTRY | eventTime | partial — timestamp of the most recent activation |
| ticketActivationStatus | latestActivationExpire | VALIDITY PERIOD | toDate | partial — expiry of the most recent activation window |
| ticketActivationStatus | numberOfActivations | FREQUENCY OF USE | maximumNumberOfUsages | partial — total activations permitted |
| ticketActivationStatus | remainingActivations | FREQUENCY OF USE | — | partial — activations remaining |

### suspensionStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| suspensionStatus | (object) | ACCOUNT SUSPEND ENTRY | — | partial — temporary suspension of a travel document from use |
| suspensionStatus | suspended | FARE CONTRACT STATUS | — | partial — suspended status of fare contract |
| suspensionStatus | suspensionExpire | ACCOUNT SUSPEND ENTRY | — | partial — timestamp when the suspension ends |

### refundableStatus / refundStatus / recoverableStatus / recoverStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| refundableStatus | (object) | FARE PRODUCT REFUND ENTRY | — | partial — eligibility and amount for refund |
| refundableStatus | refundable | EXCHANGING | — | partial — whether the access right may be surrendered for a refund |
| refundableStatus | refundableAmount | FARE PRICE | amount | partial |
| refundStatus | (object) | CUSTOMER REFUND EVENT | — | exact — records that a refund has been granted |
| refundStatus | refunded | CUSTOMER REFUND EVENT | — | exact |
| refundStatus | refundedAmount | FARE PRICE | amount | partial |
| recoverableStatus | (object) | CUSTOMER MEDIA RESTORE EVENT | — | partial — indicates whether the ticket can be recovered to replacement media |
| recoverableStatus | recoverable | — | — | partial — BoB-specific recoverability flag |
| recoverableStatus | recoverableAmount | FARE PRICE | amount | partial |
| recoverStatus | (object) | CUSTOMER MEDIA APPLICATION RESTORE EVENT | — | partial — records that a ticket has been recovered |
| recoverStatus | recovered | — | — | partial |
| recoverStatus | recoveredAmount | FARE PRICE | amount | partial |

### hinderedStatus / hinderedCause

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| hinderedStatus | (object) | FARE EASEMENT | — | partial — records that the passenger was prevented from using a ticket; nearest Transmodel concept is FARE EASEMENT (disruption-related exception) |
| hinderedStatus | hindered | FARE EASEMENT | — | partial |
| hinderedStatus | hinderedCause | FARE EASEMENT | — | partial — cause classification (travellerHindered, operatorHindered, forceMajeure) |
| hinderedStatus | refundableAmount | FARE PRICE | amount | partial |
| hinderedCause | (enum) | FARE EASEMENT | — | partial — travellerHindered / operatorHindered / forceMajeure maps to disruption type taxonomy |

### revokeStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| revokeStatus | (object) | ACCOUNT SUSPEND ENTRY | — | partial — revocation of a ticket adds it to a security blacklist |
| revokeStatus | revoked | FARE CONTRACT SECURITY LISTING | — | partial — presence on a SECURITY LIST as revoked |
| revokeStatus | revokedAt | ACCOUNT SUSPEND ENTRY | eventTime | partial — timestamp of revocation |

### ticketEventResult / ticketMetadata

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketEventResult | (object) | FARE CONTRACT ENTRY | — | partial — result of a validation or inspection event registered against a ticket |
| ticketEventResult | ticketEventPassengerMessage | PT SITUATION MESSAGE | — | partial — passenger-facing result message |
| ticketEventResult | ticketEventInspectMessage | CONTROL ENTRY | — | partial — inspector-facing result message |
| ticketEventResult | ticketEventStatus | CONTROL ENTRY | — | partial — numeric result code (0=success, 1–9=failure reasons) |
| ticketEventResult | ticketMetadata | TRAVEL DOCUMENT | — | partial — metadata associated with the ticket |
| ticketEventResult | ticketEvent | FARE CONTRACT ENTRY | — | partial — the event record itself |
| ticketEventResult | ticketActivationStatus | TRAVEL DOCUMENT SECURITY LISTING | — | partial — activation state returned alongside the event result |
| ticketMetadata | relativeValidityStart | VALIDITY PERIOD | fromDate | partial — start of the relative validity window |
| ticketMetadata | travellerPhotoURL | TRANSPORT CUSTOMER | photoUrl | partial — URL to traveller photo for visual inspection |
| ticketMetadata | ticketEventIds | FARE CONTRACT ENTRY | id | partial — list of all event identifiers associated with this ticket |

### ticketEvent

The `ticketEvent` schema is shared across the Ticket, Inspection, and Validation APIs. See the Validation API section for the full attribute mapping.

### ticketReference / mtbReference / deviceReference

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketReference | (object) | TRAVEL DOCUMENT | — | partial — cross-participant reference to a ticket |
| ticketReference | pid | OPERATOR | id | partial — participant (operator) identifier |
| ticketReference | ticketId | TRAVEL DOCUMENT | id | exact |
| mtbReference | (object) | TRAVEL DOCUMENT | — | partial — identifies an MTB by its issuer participant and cryptographic signature. No direct Transmodel equivalent — BoB-specific security mechanism |
| mtbReference | pid | OPERATOR | id | partial |
| mtbReference | issuerSignature | — | — | none — ECDSA/RSA signature over MTB content. No direct Transmodel equivalent — BoB-specific security mechanism |
| deviceReference | (object) | EQUIPMENT | — | partial — identifies the physical device (validator/inspector device) that generated an event |
| deviceReference | pid | OPERATOR | id | partial — participant that owns/operates the device |
| deviceReference | did | EQUIPMENT | id | partial — device identifier |
| deviceReference | ua | — | — | none — user agent identifier linking to the Device API; no Transmodel structural equivalent |

### fare (Ticket API)

Same mapping as `fare` in the Product API — see above.

---

## Token API (v1.5.0)

The Token API manages physical or digital tokens (smart cards, EMV cards, NFC tokens) that may be bound to tickets. It provides token information lookup by serial number or thumbprint, revocation status, a revocation list, hints for locating issued tickets, and preferred payment service provider registration.

### tokenInformation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| tokenInformation | (object) | MEDIUM ACCESS DEVICE | — | partial — a physical or digital token used to present travel entitlements; maps to MEDIUM ACCESS DEVICE or EMV CARD |
| tokenInformation | serial | MEDIUM ACCESS DEVICE | id | partial — serial number as a manufacturer-assigned identifier |
| tokenInformation | revoked | TRAVEL DOCUMENT SECURITY LISTING | — | partial — whether the token's public key is revoked; maps to FARE CONTRACT SECURITY LISTING or TRAVEL DOCUMENT SECURITY LISTING |
| tokenInformation | thumbprint | — | — | none — JWK SHA-256 thumbprint (RFC 7638) of the token's public key. No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenInformation | publicKey | — | — | none — JWK public key of the token. No direct Transmodel equivalent — BoB-specific security mechanism |

### revocationStatus / revocationList / revocationEntry

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| revocationStatus | (object) | TRAVEL DOCUMENT SECURITY LISTING | — | partial — presence/absence of a token on a security revocation list |
| revocationStatus | revoked | TRAVEL DOCUMENT SECURITY LISTING | — | partial |
| revocationList | (array) | SECURITY LIST | — | partial — ordered list of revoked token thumbprints; maps to a Transmodel SECURITY LIST |
| revocationEntry | (object) | ACCOUNT SUSPEND ENTRY | — | partial — a single revocation record on the list |
| revocationEntry | entryId | ACCOUNT SUSPEND ENTRY | id | partial — monotonically increasing entry identifier |
| revocationEntry | thumbprint | — | — | none — No direct Transmodel equivalent — BoB-specific security mechanism |
| revocationEntry | expire | ACCOUNT SUSPEND ENTRY | — | partial — expiry of the revocation record |

### tokenHints / hint / hintCompact

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| tokenHints | (array) | — | — | none — list of participant hints indicating where tickets tied to a token were issued; no direct Transmodel structural equivalent |
| hint | (object) | — | — | none — a hint entry indicating a ticket issuer participant; no Transmodel equivalent |
| hint | pid | OPERATOR | id | partial — participant (operator) identifier |
| hint | expire | VALIDITY PERIOD | toDate | partial — expiry of the hint record |
| hintCompact | pid | OPERATOR | id | partial |

### jwkPublic (Token API)

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| jwkPublic | (object) | — | — | none — RFC 7517 JSON Web Key (public). No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkPublic | kty | — | — | none — JWA key type (RSA or EC). No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkPublic | kid | — | — | none — key identifier. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkPublic | crv | — | — | none — EC curve type. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkPublic | x / y | — | — | none — EC public key coordinates. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkPublic | n / e | — | — | none — RSA modulus and exponent. No direct Transmodel equivalent — BoB-specific security mechanism |

### psp

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| psp | (object) | CUSTOMER PAYMENT MEANS | — | partial — preferred payment service provider registered to a token; maps to CUSTOMER PAYMENT MEANS |
| psp | pid | OPERATOR | id | partial — participant acting as payment service provider |

---

## Traveller API (v3.0.0)

The Traveller API manages traveller accounts and wallets, supports product purchases on behalf of travellers, sends notifications, provides MTB activation, and supports product set recreation (renewal). It is normally implemented by the sales channel that holds the customer relationship.

### travellerInformation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travellerInformation | (object) | TRANSPORT CUSTOMER | — | exact — a registered traveller with personal and account details |
| travellerInformation | travellerId | TRANSPORT CUSTOMER | id | exact |
| travellerInformation | travellerName | TRANSPORT CUSTOMER | name | exact |
| travellerInformation | travellerDOB | TRANSPORT CUSTOMER | dateOfBirth | exact — date of birth for eligibility verification |
| travellerInformation | travellerEmail | TRANSPORT CUSTOMER | contactDetails/email | exact |
| travellerInformation | travellerPhone | TRANSPORT CUSTOMER | contactDetails/phone | exact — E.164 phone number |
| travellerInformation | travellerPhotoURL | TRANSPORT CUSTOMER | photoUrl | partial — URL to photo used for visual ticket inspection |
| travellerInformation | travellerPrefLang | TRANSPORT CUSTOMER | preferredLanguage | partial — ordered list of preferred languages (RFC 3066) |
| travellerInformation | travellerTokens | MEDIUM ACCESS DEVICE | — | partial — list of tokens (smart cards, EMV cards) registered to the traveller |

### travellerToken

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travellerToken | (object) | MEDIUM ACCESS DEVICE | — | partial — a token registered against a traveller account |
| travellerToken | pid | OPERATOR | id | partial — participant (token issuer) identifier |
| travellerToken | serial | MEDIUM ACCESS DEVICE | id | partial — token serial number |
| travellerToken | tokenId | MEDIUM ACCESS DEVICE | id | partial — token identifier string |

### walletInformation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| walletInformation | (object) | FARE CONTRACT | — | partial — the traveller's wallet containing payment means; nearest Transmodel concept is FARE CONTRACT which encapsulates the financial relationship |
| walletInformation | paymentMeans | CUSTOMER PAYMENT MEANS | — | exact — list of registered payment means available in the wallet |

### paymentMeansItem

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| paymentMeansItem | (object) | CUSTOMER PAYMENT MEANS | — | exact — a single registered payment method |
| paymentMeansItem | id | CUSTOMER PAYMENT MEANS | id | exact |
| paymentMeansItem | preference | CUSTOMER PAYMENT MEANS | — | partial — preference rank; no direct Transmodel attribute |
| paymentMeansItem | name | CUSTOMER PAYMENT MEANS | name | exact — display name (e.g., "Kundkassa") |
| paymentMeansItem | type | CUSTOMER PAYMENT MEANS | — | partial — purse / paymentcard / mobile / invoice / other; maps loosely to payment method classification |
| paymentMeansItem | currency | CUSTOMER PAYMENT MEANS | — | partial — ISO 4217 currency of the payment means |
| paymentMeansItem | limitPIN | CHARGING POLICY | — | partial — transaction limit when used with PIN; maps to CHARGING POLICY credit limit |
| paymentMeansItem | limit | CHARGING POLICY | — | partial — transaction limit without PIN |
| paymentMeansItem | tokenProofRequired | — | — | none — flag requiring token cryptographic proof for all transactions; BoB-specific |
| paymentMeansItem | balance | AMOUNT OF PRICE UNIT | — | partial — current stored value balance; maps to AMOUNT OF PRICE UNIT (electronic purse) |
| paymentMeansItem | depositAllowed | — | — | none — whether negative amount (deposit/top-up) is permitted; BoB-specific |

### walletTransactionRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| walletTransactionRequest | (object) | FARE DEBIT | — | partial — request to debit or credit the wallet; maps to FARE DEBIT or BOOKING DEBIT |
| walletTransactionRequest | transactionReference | SALES TRANSACTION | id | partial — requestor's idempotency reference |
| walletTransactionRequest | description | FARE DEBIT | description | partial |
| walletTransactionRequest | amount | FARE DEBIT | amount | exact — positive for debit, negative for credit |
| walletTransactionRequest | currency | FARE PRICE | currency | exact |
| walletTransactionRequest | paymentMeansId | CUSTOMER PAYMENT MEANS | id | exact — selects the specific payment method |
| walletTransactionRequest | paymentMeansPIN | — | — | none — PIN code; security credential with no Transmodel structural equivalent |
| walletTransactionRequest | transactionCommit | — | — | none — two-phase commit flag (reserve vs. finalise); no Transmodel structural equivalent |
| walletTransactionRequest | tokenProof | — | — | none — cryptographic proof of token possession; No direct Transmodel equivalent — BoB-specific security mechanism |

### transactionInformation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| transactionInformation | (object) | FARE DEBIT | — | partial — a recorded wallet transaction |
| transactionInformation | id | FARE DEBIT | id | exact |
| transactionInformation | transactionReference | SALES TRANSACTION | id | partial |
| transactionInformation | description | FARE DEBIT | description | partial |
| transactionInformation | amount | FARE DEBIT | amount | exact |
| transactionInformation | currency | FARE PRICE | currency | exact |
| transactionInformation | paymentMeansId | CUSTOMER PAYMENT MEANS | id | exact |
| transactionInformation | status | FARE CONTRACT STATUS | — | partial — pending / committed / cancelled maps to FARE CONTRACT STATUS lifecycle |
| transactionInformation | expire | VALIDITY PERIOD | toDate | partial — expiry of a pending (reserved) transaction |
| transactionInformation | cancellableExpire | — | — | partial — window within which a committed transaction may be cancelled; no direct Transmodel attribute |
| transactionInformation | tokenProof | — | — | none — No direct Transmodel equivalent — BoB-specific security mechanism |

### transactionStatus / transactionStatusUpdate

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| transactionStatus | (enum) | FARE CONTRACT STATUS | — | partial — pending / commited / cancelled |
| transactionStatusUpdate | (enum) | FARE CONTRACT STATUS | — | partial — commited / cancelled |

### travellerNotification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travellerNotification | (object) | PT SITUATION MESSAGE | — | partial — a message sent to the traveller through the sales channel; nearest Transmodel concept is PT SITUATION MESSAGE for passenger-facing messages |
| travellerNotification | notificationId | PT SITUATION MESSAGE | id | partial |
| travellerNotification | notificationStatus | PT SITUATION MESSAGE | — | partial — queued / sent / acknowledged / discarded maps to message delivery lifecycle |
| travellerNotification | notificationLang | PT SITUATION MESSAGE | language | partial |
| travellerNotification | notificationMessage | PT SITUATION MESSAGE | description | partial |
| travellerNotification | notificationExpire | PT SITUATION MESSAGE | validityPeriod | partial |

### ticketNotification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketNotification | (object) | FARE CONTRACT ENTRY | — | partial — notification to the sales channel that a ticket's status has changed |
| ticketNotification | pid | OPERATOR | id | partial |
| ticketNotification | ticketId | TRAVEL DOCUMENT | id | exact |
| ticketNotification | ticketLocation | — | — | none — URI for refreshing ticket data; protocol-level hint |
| ticketNotification | cause | FARE CONTRACT EVENT | — | partial — stateChange / validityChange / other maps to FARE CONTRACT EVENT type |
| ticketNotification | eventTime | FARE CONTRACT ENTRY | eventTime | partial |

### productSetInformation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productSetInformation | (object) | CUSTOMER PURCHASE PACKAGE | — | partial — represents a purchased product set (one or more tickets across participants) as viewed by the traveller |
| productSetInformation | productSetId | CUSTOMER PURCHASE PACKAGE | id | partial |
| productSetInformation | ticketBundles | TRAVEL DOCUMENT | — | partial — grouped tickets from constituent participants |
| productSetInformation | title | SALES OFFER PACKAGE | name | partial |
| productSetInformation | description | SALES OFFER PACKAGE | description | partial |
| productSetInformation | rides | PT RIDE LEG | — | partial — planned ride legs associated with the product set |
| productSetInformation | conditions | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial |
| productSetInformation | temporalConstraints | VALIDITY PERIOD | — | exact — temporal validity constraints for the product set |
| productSetInformation | spatialConstraints | FARE ZONE, TOPOGRAPHIC PLACE | — | partial — geographic validity constraints |
| productSetInformation | status | CUSTOMER PURCHASE STATUS | — | partial — activated / expired / issued / recreated / refunded / revoked / suspended |
| productSetInformation | transactionReference | SALES TRANSACTION | id | partial |

### productSetStatus / productSetStatusUpdate

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productSetStatus | (enum) | CUSTOMER PURCHASE STATUS | — | partial — activated / expired / issued / recreated / refunded / revoked / suspended |
| productSetStatusUpdate | (enum) | CUSTOMER PURCHASE STATUS | — | partial — activated / issued / refunded / revoked |

### temporalConstraints / spatialConstraint / geoAreas

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| temporalConstraints | (object) | VALIDITY PERIOD | — | exact — full temporal validity state of a product set |
| temporalConstraints | timeOfIssue | CUSTOMER FULFILMENT EVENT | eventTime | exact |
| temporalConstraints | startOfValidity | VALIDITY PERIOD | fromDate | exact |
| temporalConstraints | endOfValidity | VALIDITY PERIOD | toDate | exact |
| temporalConstraints | relativeValidity | FREQUENCY OF USE | — | partial — relative validity duration from activation |
| temporalConstraints | latestActivation | FARE TRIP ACTIVATION ENTRY | eventTime | partial |
| temporalConstraints | latestActivationExpire | VALIDITY PERIOD | toDate | partial |
| temporalConstraints | numberOfActivations | FREQUENCY OF USE | maximumNumberOfUsages | partial |
| temporalConstraints | remainingActivations | FREQUENCY OF USE | — | partial |
| temporalConstraints | activationPeriod | VALIDITY PERIOD | duration | partial — window within which all activations must occur |
| temporalConstraints | suspensionExpire | ACCOUNT SUSPEND ENTRY | — | partial — end of the current suspension period |
| spatialConstraint | (object) | FARE ZONE, TOPOGRAPHIC PLACE | — | partial — geographic constraints on where the product is valid |
| spatialConstraint | name | TOPOGRAPHIC PLACE | name | partial |
| spatialConstraint | areas | TOPOGRAPHIC PLACE | — | partial |
| geoAreas | (object) | TOPOGRAPHIC PLACE | — | partial — union of geographic shapes |

### tokenChallenge / tokenProof

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| tokenChallenge | (object) | — | — | none — cryptographic challenge-response for proving possession of a token's private key. No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenChallenge | authenticationChallenge | — | — | none — No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenProof | (object) | — | — | none — signed authentication response proving token possession. No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenProof | travellerToken | MEDIUM ACCESS DEVICE | — | partial — the token against which proof is provided |
| tokenProof | authenticationChallenge | — | — | none — No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenProof | authenticationResponse | — | — | none — No direct Transmodel equivalent — BoB-specific security mechanism |

### purchaseRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| purchaseRequest | (object) | CUSTOMER PRODUCT PURCHASE EVENT | — | partial — request to purchase a product on behalf of a token holder |
| purchaseRequest | pid | OPERATOR | id | partial — participant from whom to purchase |
| purchaseRequest | productId | FARE PRODUCT | id | exact |
| purchaseRequest | transactionReference | SALES TRANSACTION | id | partial |

### activationData

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| activationData | (object) | CUSTOMER PRODUCT ACTIVATION EVENT | — | exact — triggers activation of all product sets within an MTB |
| activationData | timeOfActivation | CUSTOMER PRODUCT ACTIVATION EVENT | eventTime | exact |
| activationData | pid | OPERATOR | id | partial — activating participant |
| activationData | ticketId | TRAVEL DOCUMENT | id | exact — triggering ticket identifier |
| activationData | eventId | FARE CONTRACT ENTRY | id | partial — event that triggered the activation |

### productSetOptions / productSets

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productSetOptions | (object) | CUSTOMER PURCHASE PARAMETER ASSIGNMENT | — | partial — parameters for recreating a product set (renewal) |
| productSetOptions | travellersPerCategory | GROUP TICKET | — | partial |
| productSetOptions | productProperties | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial |
| productSets | (array) | CUSTOMER PURCHASE PACKAGE | — | partial — list of product set information records |

### ticketBundle (Traveller API)

Same concept as ticketBundle in the Ticket API — see above.

---

## Device API (v2.1.1)

The Device API provides cryptographic key material to validator devices: individual symmetric device keys derived from a master Key Derivation Key (KDK), the complete KDK set for offline validation, and user-agent metadata for device identification.

### deviceKeyRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| deviceKeyRequest | (object) | EQUIPMENT | — | partial — request by a specific device for its symmetric signing key |
| deviceKeyRequest | did | EQUIPMENT | id | partial — device identifier (base64url-encoded) |

### deviceKeyResponse

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| deviceKeyResponse | (object) | — | — | none — symmetric key material provisioned to a validator device. No direct Transmodel equivalent — BoB-specific security mechanism |
| deviceKeyResponse | kid | — | — | none — master key identifier. No direct Transmodel equivalent — BoB-specific security mechanism |
| deviceKeyResponse | did | EQUIPMENT | id | partial — device identifier |
| deviceKeyResponse | kty | — | — | none — JWA key type (always "oct" for symmetric). No direct Transmodel equivalent — BoB-specific security mechanism |
| deviceKeyResponse | k | — | — | none — symmetric key value (base64url). No direct Transmodel equivalent — BoB-specific security mechanism |
| deviceKeyResponse | iat | — | — | none — key issuance timestamp. No direct Transmodel equivalent — BoB-specific security mechanism |
| deviceKeyResponse | exp | VALIDITY CONDITION | toDate | partial — key expiry timestamp |
| deviceKeyResponse | ua | — | — | none — user agent identifier for the device. No direct Transmodel equivalent — BoB-specific security mechanism |

### jwkSet / jwkSymmetric (KDK)

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| jwkSet | (object) | — | — | none — set of Key Derivation Keys (KDKs) for offline device validation. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkSet | keys | — | — | none — array of symmetric JWKs. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkSymmetric | (object) | — | — | none — a single symmetric JSON Web Key. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkSymmetric | kty | — | — | none — key type. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkSymmetric | kid | — | — | none — key identifier. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkSymmetric | k | — | — | none — symmetric key value. No direct Transmodel equivalent — BoB-specific security mechanism |
| jwkSymmetric | kdf | — | — | none — key derivation function identifier (default: mts2). No direct Transmodel equivalent — BoB-specific security mechanism |

### userAgent / userAgents

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| userAgent | (object) | EQUIPMENT | — | partial — a dictionary of client-hint properties describing the application and device platform of a sales or validation client |
| userAgent | (key-value properties) | EQUIPMENT | — | partial — platform, model, app version map to equipment description attributes |
| userAgents | (object) | — | — | partial — registry mapping user-agent identifiers to their descriptions; no single Transmodel structural equivalent |

---

## Inspection API (v2.3.0)

The Inspection API records manual ticket inspections performed by revenue protection staff, both online (server validates) and offline (device pre-validated, result submitted later).

### ticketEvent (Inspection)

The `ticketEvent` schema is shared with the Validation and Ticket APIs.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ticketEvent | (object) | CONTROL ENTRY | — | exact — records the comparison of actual parameters to the access rights granted by a TRAVEL DOCUMENT |
| ticketEvent | time | CONTROL ENTRY | eventTime | exact — timestamp of the inspection event |
| ticketEvent | ticketId | TRAVEL DOCUMENT | id | exact — identifies the inspected ticket |
| ticketEvent | eventId | FARE CONTRACT ENTRY | id | partial — server-allocated event identifier |
| ticketEvent | localEventId | FARE CONTRACT ENTRY | id | partial — device-allocated event identifier (used for deduplication) |
| ticketEvent | eventType | CONTROL TYPE | — | partial — inspection / validation; maps to CONTROL TYPE classification |
| ticketEvent | eventResult | CONTROL ENTRY | — | partial — result code (0=success, 1–9=failure reasons including spatial, temporal, signature, blacklist, passback, fraud checks) |
| ticketEvent | eventReason | CONTROL ENTRY | description | partial — human-readable failure/success reason |
| ticketEvent | mtbReference | TRAVEL DOCUMENT | — | partial — reference to the MTB being inspected. No direct Transmodel equivalent — BoB-specific security mechanism |
| ticketEvent | deviceReference | EQUIPMENT | — | partial — the device that generated the event |
| ticketEvent | geo | LOCATION | — | partial — geographical position where the inspection occurred |
| ticketEvent | modeOfTransport | VEHICLE MODE | — | partial — high-level transport mode (bus, rail, tram, water) |
| ticketEvent | subModeOfTransport | VEHICLE MODE | subMode | partial — more specific submode (localBus, regionalBus, etc.) |
| ticketEvent | zone | FARE ZONE | id | partial — zone in which the inspection occurred |
| ticketEvent | zone/pid | OPERATOR | id | partial — participant owning the zone |
| ticketEvent | zone/zoneId | FARE ZONE | id | exact |
| ticketEvent | service | SERVICE JOURNEY | id | partial — service on which the inspection occurred |
| ticketEvent | service/pid | OPERATOR | id | partial |
| ticketEvent | service/serviceId | LINE | id | partial — line or service number |
| ticketEvent | service/tripId | SERVICE JOURNEY | id | partial — specific trip identifier |
| ticketEvent | service/blockId | BLOCK | id | partial — vehicle block identifier |
| ticketEvent | stopArea | STOP PLACE | id | partial — stop area where the inspection occurred |
| ticketEvent | stopArea/pid | OPERATOR | id | partial |
| ticketEvent | stopArea/stopAreaId | STOP PLACE | id | exact |
| ticketEvent | stopArea/nextStopAreaId | STOP PLACE | id | partial — next stop area identifier |
| ticketEvent | validator | EQUIPMENT | — | partial — the validator device that originated the event |
| ticketEvent | validator/validatorId | EQUIPMENT | id | exact |
| ticketEvent | validator/location | EQUIPMENT PLACE | — | partial — mounting location (e.g., vehicle number) |
| ticketEvent | validator/sublocation | EQUIPMENT POSITION | — | partial — more specific sub-location (e.g., door number) |
| ticketEvent | inspector | — | — | partial — the human inspector; maps loosely to a TRAVEL DOCUMENT CONTROLLER role |
| ticketEvent | inspector/inspectorId | — | — | partial — no dedicated Transmodel concept for inspector identity |
| ticketEvent | operatingDayDate | OPERATING DAY | — | partial — the operating day on which the event occurred |
| ticketEvent | tokenTransaction | — | — | none — cryptographic token authentication transaction (PICC challenge-response). No direct Transmodel equivalent — BoB-specific security mechanism |

### inspectionReport

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| inspectionReport | (array) | CONTROL ENTRY | — | partial — batch of offline inspection events submitted for back-office registration |

### ticketEventResult (Inspection)

See ticketEventResult mapping in the Ticket API section above.

### ticketActivationStatus (Inspection)

See ticketActivationStatus mapping in the Ticket API section above.

### ticketMetadata (Inspection)

See ticketMetadata mapping in the Ticket API section above.

### tokenTransaction / jwkPublic / aiData / aiRequest / aiResponse

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| tokenTransaction | (object) | — | — | none — PICC-based cryptographic token authentication transaction. No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenTransaction | tpk | — | — | none — token public key (JWK). No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenTransaction | aiData | — | — | none — authentication input data (time, terminal, AID, counter). No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenTransaction | aiRequest | — | — | none — authentication request sent to the PICC (base64url). No direct Transmodel equivalent — BoB-specific security mechanism |
| tokenTransaction | aiResponse | — | — | none — authentication response from the PICC (base64url). No direct Transmodel equivalent — BoB-specific security mechanism |
| aiData | pid | OPERATOR | id | partial — terminal participant identifier |
| aiData | terminalId | EQUIPMENT | id | partial — identifier of the terminal device |
| aiData | aid | — | — | none — NFC application identifier (AID). No direct Transmodel equivalent — BoB-specific security mechanism |
| aiData | signatureCounter | — | — | none — PICC signature counter for replay prevention. No direct Transmodel equivalent — BoB-specific security mechanism |
| aiData | time | CONTROL ENTRY | eventTime | partial |
| aiData | loc | LOCATION | — | partial — WGS84 position of the terminal |
| mtbReference (Inspection) | (object) | TRAVEL DOCUMENT | — | partial — same as mtbReference in Ticket API |
| deviceReference (Inspection) | (object) | EQUIPMENT | — | partial — same as deviceReference in Ticket API |
| geoPosition (Inspection) | (object) | LOCATION | — | exact — same as geoPosition in Booking API |

---

## Validation API (v3.4.0)

The Validation API records automated ticket validation events at entry gates and on-board validators, provides blacklist and whitelist management for offline validation, distributes TICKLE macro sets for the TICKLE rule engine, and provides a velocity-based fraud check service.

### ticketEvent (Validation)

Identical schema to ticketEvent in the Inspection API — see above for full attribute mapping. The `eventType` field will carry the value `validation`.

### validationReport

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| validationReport | (array) | FARE TRIP ACTIVATION ENTRY | — | partial — batch of offline validation events; maps to FARE TRIP ACTIVATION ENTRY for each boarding event |

### ticketEventResult (Validation)

See ticketEventResult mapping in the Ticket API section above.

### ticketActivationStatus (Validation)

See ticketActivationStatus mapping in the Ticket API section above.

### blacklist / blacklistEntry

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| blacklist | (array) | SECURITY LIST | — | partial — ordered list of blocked entities (tickets, travellers, tokens); maps to a Transmodel SECURITY LIST (blacklist variant) |
| blacklistEntry | (object) | ACCOUNT SUSPEND ENTRY | — | partial — a single entry on the blacklist |
| blacklistEntry | blacklistEntryId | ACCOUNT SUSPEND ENTRY | id | partial — monotonically increasing entry identifier |
| blacklistEntry | ticketId | TRAVEL DOCUMENT | id | partial — blacklisted ticket |
| blacklistEntry | travellerId | TRANSPORT CUSTOMER | id | partial — blacklisted traveller |
| blacklistEntry | tokenId | MEDIUM ACCESS DEVICE | id | partial — blacklisted token |
| blacklistEntry | inception | ACCOUNT SUSPEND ENTRY | eventTime | partial — when the entry became effective |
| blacklistEntry | expire | ACCOUNT SUSPEND ENTRY | — | partial — when the entry expires and may be discarded |

### whitelist / whitelistEntry

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| whitelist | (array) | SECURITY LIST | — | partial — ordered list of pre-authorised travel entitlements for offline ID-based validation; maps to a Transmodel SECURITY LIST (whitelist variant) |
| whitelistEntry | (object) | TRAVEL DOCUMENT SECURITY LISTING | — | partial — a single whitelisted travel entitlement distributed to validators |
| whitelistEntry | id | TRAVEL DOCUMENT SECURITY LISTING | id | partial — monotonically increasing entry identifier |
| whitelistEntry | kid | — | — | none — token public key thumbprint used to look up the whitelist entry. No direct Transmodel equivalent — BoB-specific security mechanism |
| whitelistEntry | mtb | TRAVEL DOCUMENT | — | partial — signed MTB conveying the travel entitlement. No direct Transmodel equivalent — BoB-specific security mechanism |
| whitelistEntry | priority | TRAVEL DOCUMENT SECURITY LISTING | — | partial — priority ordering when multiple entries match |
| whitelistEntry | val | VALIDITY PERIOD | — | exact — ISO 8601 time interval defining the entry's validity window |
| whitelistEntry | ticketId | TRAVEL DOCUMENT | id | exact |

### tickleMacros / tickleMacro

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| tickleMacros | (object) | — | — | none — a named set of TICKLE macro definitions distributed to validators. No direct Transmodel equivalent — BoB-specific security mechanism |
| tickleMacros | pid | OPERATOR | id | partial — participant providing the TICKLE macro set |
| tickleMacros | serial | — | — | none — macro set version/serial number. No direct Transmodel equivalent — BoB-specific security mechanism |
| tickleMacros | macros | — | — | none — array of macro definitions. No direct Transmodel equivalent — BoB-specific security mechanism |
| tickleMacro | (object) | — | — | none — a single TICKLE macro: a named recurring time or spatial condition expression. No direct Transmodel equivalent — BoB-specific security mechanism |
| tickleMacro | name | — | — | none — macro name (e.g., "#weekends"). No direct Transmodel equivalent — BoB-specific security mechanism |
| tickleMacro | description | — | — | none — human-readable description. No direct Transmodel equivalent — BoB-specific security mechanism |
| tickleMacro | condition | VALIDITY CONDITION | — | partial — ISO 8601 recurring time interval expression (e.g., "@R/2016W016/P1W/P2D"); functionally equivalent to a Transmodel VALIDITY CONDITION but expressed in TICKLE syntax. No direct Transmodel equivalent — BoB-specific security mechanism |

### fraudcheckData / fraudcheckResponse

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| fraudcheckData | (object) | ACCOUNT DETECT SUSPICIOUS BEHAVIOUR EVENT | — | partial — inputs for velocity-based fraud detection; maps to ACCOUNT DETECT SUSPICIOUS BEHAVIOUR EVENT |
| fraudcheckData | time | CONTROL ENTRY | eventTime | partial |
| fraudcheckData | geo | LOCATION | — | partial — geographical position of the validating device |
| fraudcheckData | mtbReference | TRAVEL DOCUMENT | — | partial — reference to the MTB being checked |
| fraudcheckResponse | (object) | ACCOUNT DETECT SUSPICIOUS BEHAVIOUR EVENT | — | partial — velocity result; if above transport-mode threshold, indicates likely ticket copying fraud |
| fraudcheckResponse | velocity | — | — | partial — calculated velocity in km/h based on distance/time since last event; no single Transmodel attribute but informs ACCOUNT DETECT SUSPICIOUS BEHAVIOUR EVENT |

### mtbReference (Validation)

Same mapping as mtbReference in the Ticket API — see above.

### deviceReference (Validation)

Same mapping as deviceReference in the Ticket API — see above.

### tokenTransaction / aiData / aiRequest / aiResponse (Validation)

Same mapping as the equivalent objects in the Inspection API — see above.

---

## Participant Metadata API (v2.3.1)

The Participant Metadata API is operated by an Administering Body (AB) and maintains the central register of participant public keys, API endpoint URLs, issuer signature constraints, and domain names. It enables interoperability by giving participants access to each other's cryptographic metadata.

### participantMetadata

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| participantMetadata | (object) | OPERATOR | — | partial — a registered BoB participant (transport operator or sales channel) with its associated metadata |
| participantMetadata | pid | OPERATOR | id | exact — the unique numeric participant identifier |
| participantMetadata | organisationName | OPERATOR | name | exact — legal or trading name of the organisation |
| participantMetadata | domainName | OPERATOR | — | partial — FQDN used for DNS-based key fetching (MTS1 section 2.4); no direct Transmodel attribute |
| participantMetadata | mtbPublicKeys | — | — | none — public signing keys for MTB validation. No direct Transmodel equivalent — BoB-specific security mechanism |
| participantMetadata | authtokenPublicKeys | — | — | none — public keys for JWT authentication token validation. No direct Transmodel equivalent — BoB-specific security mechanism |
| participantMetadata | interfaceEndpoints | DATA SOURCE | — | partial — registered API endpoint URLs for each BoB sub-API; maps to DATA SOURCE in Transmodel (source of data/service) |
| participantMetadata | issuerSignatureConstraints | — | — | none — controls which other participants may sign MTBs on this participant's behalf. No direct Transmodel equivalent — BoB-specific security mechanism |
| participantMetadata | participantInfo | OPERATOR | — | partial — free-form participant info as per the participant-info JSON schema |

### participantMetadataFile

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| participantMetadataFile | (array) | — | — | partial — a signed (JWS) snapshot of all participant metadata records; closest Transmodel concept is a versioned DATA SOURCE export. No direct Transmodel equivalent — BoB-specific security mechanism |

### jws

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| jws | (object) | — | — | none — RFC 7515 JSON Web Signature (JWS) serialization wrapping the metadata file. No direct Transmodel equivalent — BoB-specific security mechanism |
| jws | payload | — | — | none — base64url-encoded signed payload. No direct Transmodel equivalent — BoB-specific security mechanism |
| jws | signatures | — | — | none — array of signatures with protected header and signature value. No direct Transmodel equivalent — BoB-specific security mechanism |

### mtbJwkPublic

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| mtbJwkPublic | (object) | — | — | none — public JWK used to verify issuer signatures on MTBs. No direct Transmodel equivalent — BoB-specific security mechanism |
| mtbJwkPublic | kty | — | — | none — JWA key type (EC or RSA). No direct Transmodel equivalent — BoB-specific security mechanism |
| mtbJwkPublic | kid | — | — | none — key identifier. No direct Transmodel equivalent — BoB-specific security mechanism |
| mtbJwkPublic | crv / x / y | — | — | none — EC key parameters. No direct Transmodel equivalent — BoB-specific security mechanism |
| mtbJwkPublic | n / e | — | — | none — RSA key parameters. No direct Transmodel equivalent — BoB-specific security mechanism |
| mtbJwkPublic | bob_sc | — | — | none — BoB signature constraint (id or ticket MTB). No direct Transmodel equivalent — BoB-specific security mechanism |

### authtokenJwkPublic

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| authtokenJwkPublic | (object) | — | — | none — public JWK used to verify JWT authentication tokens. No direct Transmodel equivalent — BoB-specific security mechanism |
| authtokenJwkPublic | kty / kid / crv / x / y / n / e | — | — | none — same as mtbJwkPublic above. No direct Transmodel equivalent — BoB-specific security mechanism |

### issuerSignatureConstraint

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| issuerSignatureConstraint | (object) | FARE PRODUCT ATTRIBUTOR ROLE | — | partial — defines which participants (sales channels) are permitted to sign MTBs on behalf of this participant; closest Transmodel concept is FARE PRODUCT ATTRIBUTOR ROLE (delegated signing authority). No direct Transmodel equivalent — BoB-specific security mechanism |
| issuerSignatureConstraint | pid | OPERATOR | id | partial — PID of the acceptable signing participant |
| issuerSignatureConstraint | signatureLifetime | VALIDITY PERIOD | duration | partial — maximum allowed lifetime (ISO 8601 duration) for signatures created by the acceptable participant |

### endpoint / endpointCall

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| endpoint | (object) | DATA SOURCE | — | partial — a registered API endpoint published by a participant |
| endpoint | endpointId | DATA SOURCE | id | partial |
| endpointCall | version | DATA SOURCE | — | partial — API version integer |
| endpointCall | type | DATA SOURCE | — | partial — BoB API type (account, authentication, booking, device, inspection, product, resource, ticket, token, traveller, validation) |
| endpointCall | uri | DATA SOURCE | url | exact — base URL of the participant's API implementation |
| endpointCall | tlsa | — | — | none — RFC 6698 TLSA parameters for certificate verification; No direct Transmodel equivalent — BoB-specific security mechanism |

### participantInfo

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| participantInfo | (object) | OPERATOR | — | partial — free-form participant information as defined by the participant-info JSON schema (external to this specification) |

---

## Cross-API Summary

| Domain | Key BoB Schemas | Primary Transmodel Concepts |
|--------|-----------------|-----------------------------|
| Authentication / security | authToken, jwsHeader, jwtClaim, jwkPublic, jwkSymmetric, KDK | No direct Transmodel equivalent — BoB-specific security mechanism |
| Product catalogue | product, productCategory, fareCategory, travellerCategory, genericCategory, fare | FARE PRODUCT, SALES OFFER PACKAGE, FARE PRICE, USER PROFILE, FARE CLASS |
| Product discovery | productFilter, route, group, area, adjacency | FARE PRODUCT REQUEST, ROUTE, FARE ZONE, TOPOGRAPHIC PLACE |
| Manifest / purchase auth | productSetManifest, productManifestRequest, productSelection, pdsInfo | SALES TRANSACTION, TRAVEL DOCUMENT, SALE DISCOUNT RIGHT |
| Booking | bookingCall, booking, BookingStatusType, statusChangeRequest, cancellation | CUSTOMER PURCHASE PACKAGE, CUSTOMER RESERVATION CREATION EVENT, FARE CONTRACT |
| Ticket lifecycle | ticket, manifestCall, ticketBundle, ticketIssueResponse | TRAVEL DOCUMENT, CUSTOMER FULFILMENT EVENT, CUSTOMER PURCHASE PACKAGE |
| Ticket status | ticketActiveStatus, ticketActivationStatus, suspensionStatus, revokeStatus, refundStatus, hinderedStatus | TRAVEL DOCUMENT SECURITY LISTING, ACCOUNT SUSPEND ENTRY, CUSTOMER REFUND EVENT, FARE EASEMENT |
| Traveller account | travellerInformation, travellerToken, walletInformation, paymentMeansItem | TRANSPORT CUSTOMER, CUSTOMER ACCOUNT, MEDIUM ACCESS DEVICE, CUSTOMER PAYMENT MEANS |
| Wallet / payment | walletTransactionRequest, transactionInformation, transactionStatus | FARE DEBIT, FARE CONTRACT STATUS, CHARGING POLICY |
| Token management | tokenInformation, revocationList, revocationEntry, tokenHints, psp | MEDIUM ACCESS DEVICE, TRAVEL DOCUMENT SECURITY LISTING, SECURITY LIST, CUSTOMER PAYMENT MEANS |
| Validation events | ticketEvent (validation), validationReport, ticketEventResult | FARE TRIP ACTIVATION ENTRY, FARE CONTRACT ENTRY, CONTROL ENTRY |
| Inspection events | ticketEvent (inspection), inspectionReport | CONTROL ENTRY, CONTROL PASSENGER TRIP ENTRY |
| Security lists | blacklist, blacklistEntry, whitelist, whitelistEntry | SECURITY LIST, ACCOUNT SUSPEND ENTRY, TRAVEL DOCUMENT SECURITY LISTING |
| TICKLE macros | tickleMacros, tickleMacro | No direct Transmodel equivalent — BoB-specific security mechanism |
| Fraud detection | fraudcheckData, fraudcheckResponse | ACCOUNT DETECT SUSPICIOUS BEHAVIOUR EVENT |
| Device key provisioning | deviceKeyRequest, deviceKeyResponse, jwkSet, jwkSymmetric, userAgent | EQUIPMENT (partial); No direct Transmodel equivalent — BoB-specific security mechanism |
| Participant registry | participantMetadata, mtbJwkPublic, authtokenJwkPublic, issuerSignatureConstraint, endpoint | OPERATOR, DATA SOURCE, FARE PRODUCT ATTRIBUTOR ROLE |
| Geography (shared) | geoPosition | LOCATION |
| Stop / place (shared) | place, stopArea | SCHEDULED STOP POINT, STOP PLACE |
| Journey leg (shared) | ride | PT RIDE LEG, SERVICE JOURNEY |
