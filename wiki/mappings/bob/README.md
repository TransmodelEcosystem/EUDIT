# BoB Booking → Transmodel Mapping

## Overview

This document maps the concepts and attributes of the **BoB Booking API** (version 2.0.1, `BoB.Booking.API`) to the **Transmodel** reference data model.

BoB (Biljetter och Bokning — Tickets and Booking) is a Swedish/Nordic interoperability standard for public transport ticketing and booking. The Booking API covers the creation, retrieval, confirmation, and cancellation of bookings, plus the associated ride and place information.

The BoB YAML specification already contains inline `x-semantics` annotations on every field. This document reorganises those annotations into the standard project mapping table format and adds cross-references.

Match quality is indicated in the **Notes** column:
- **exact** — one-to-one semantic correspondence
- **partial** — overlapping scope; some attributes or nuances differ
- **none** — no Transmodel equivalent; BoB concept is standard-specific

---

## 1. bookingCall

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

**x-semantics equivalent:**
- **concepts**: CUSTOMER RESERVATION CREATION EVENT (Sales & Distribution), BOOK VEHICLE RIDE EVENT (Mobility Services)
- **conditions**: Exact match for scheduled PT; partial match for demand-responsive (DRT) context. The signed MTB manifest has no direct Transmodel structural equivalent.

---

## 2. booking

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

**x-semantics equivalent:**
- **concepts**: CUSTOMER PURCHASE PACKAGE (Sales & Distribution), CUSTOMER RESERVATION CREATION ENTRY (Sales & Distribution), FARE CONTRACT (Sales & Distribution)
- **conditions**: Exact match for completed purchases; partial match in 'pending' status.

---

## 3. BookingStatusType

| BoB Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| pending | CUSTOMER PURCHASE STATUS (booked) | partial — reservation made, not yet confirmed/paid |
| confirmed | CUSTOMER PURCHASE STATUS (paid/activated) | exact |
| cancelled | CUSTOMER RESERVATION CANCELLATION EVENT | exact |
| miss | RIDER NO SHOW EVENT | exact — passenger no-show |
| completed | CUSTOMER PURCHASE STATUS (consumed) | exact |

---

## 4. statusChangeRequest

Used to confirm or cancel an existing booking by providing the target status and a signed MTB as proof of purchase.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| statusChangeRequest | (object) | CUSTOMER RESERVATION CANCELLATION EVENT | — | exact — when status is set to 'cancelled' |
| statusChangeRequest | (object) | CUSTOMER PRODUCT PURCHASE EVENT | — | partial — when status is set to 'confirmed', completing the purchase |
| statusChangeRequest | status | CUSTOMER PURCHASE STATUS | — | exact — requested status transition |
| statusChangeRequest | status | FARE CONTRACT STATUS | — | exact — transition in fare contract lifecycle |
| statusChangeRequest | mtb | TRAVEL DOCUMENT | — | partial — signed MTB is the machine-readable materialisation of the fare product access right |
| statusChangeRequest | mtb | MEDIUM ACCESS DEVICE | — | partial — when MTB is stored on a mobile device |

**x-semantics equivalent:**
- **concepts**: CUSTOMER RESERVATION CANCELLATION EVENT (Sales & Distribution), CUSTOMER PRODUCT PURCHASE EVENT (Sales & Distribution)
- **conditions**: 'cancelled' triggers CUSTOMER RESERVATION CANCELLATION EVENT; 'confirmed' triggers CUSTOMER PRODUCT PURCHASE EVENT. MTB signing/encoding is BoB-specific.

---

## 5. cancellation

Records the cancellation of a booking.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| cancellation | (object) | CUSTOMER RESERVATION CANCELLATION ENTRY | — | exact — records cancellation of a reservation by a TRANSPORT CUSTOMER |
| cancellation | (object) | FARE PRODUCT CANCELLATION ENTRY | — | partial — when cancellation also rolls back a purchase |
| cancellation | cancellationId | CUSTOMER RESERVATION CANCELLATION ENTRY | id | partial — Transmodel identifies the cancellation via its LOG ENTRY / FARE CONTRACT ENTRY, not a separate cancellationId |

---

## 6. ride

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

**x-semantics equivalent:**
- **concepts**: PT RIDE LEG (Trip Planning), SINGLE JOURNEY (Mobility Services), SERVICE JOURNEY (Network Timetable)
- **conditions**: Exact match to PT RIDE LEG for scheduled PT. The ordered list of rides maps to the sequence of LEGs in a PT TRIP or TRIP PATTERN.

---

## 7. place

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

---

## 8. geoPosition

WGS84 decimal geographic coordinates.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| geoPosition | (object) | LOCATION | — | exact — WGS84 decimal coordinate corresponds to Transmodel LOCATION (longitude, latitude, altitude) |
| geoPosition | (object) | POINT | — | partial — LOCATION is an attribute of POINT subtypes in Transmodel; in BoB it is a standalone object |
| geoPosition | lat | LOCATION | latitude | exact |
| geoPosition | long | LOCATION | longitude | exact |
| geoPosition | alt | LOCATION | altitude | exact — optional altitude component |

---

## 9. API endpoints

The BoB Booking API exposes four HTTP operations. These have no direct Transmodel schema equivalents (Transmodel models the data, not the protocol), but their semantics map as follows:

| Endpoint | Operation | Transmodel Concept(s) | Notes |
|----------|-----------|----------------------|-------|
| GET /booking | getAllBookings | CUSTOMER PURCHASE PACKAGE | partial — retrieves list of bookings for a traveller or request ID |
| POST /booking | requestBooking | CUSTOMER RESERVATION CREATION EVENT | exact — creates a preliminary booking |
| GET /booking/{bookingId} | getBooking | CUSTOMER PURCHASE PACKAGE | exact — retrieves a specific booking by id |
| PATCH /booking/{bookingId} | confirmBooking | CUSTOMER PRODUCT PURCHASE EVENT / CUSTOMER RESERVATION CANCELLATION EVENT | exact — transitions booking status to confirmed or cancelled |

---

## Summary

| Domain | BoB Types | Primary Transmodel Concepts |
|--------|-----------|----------------------------|
| Booking creation | bookingCall | CUSTOMER RESERVATION CREATION EVENT, TRAVEL DOCUMENT |
| Booking state | booking, BookingStatusType | CUSTOMER PURCHASE PACKAGE, CUSTOMER PURCHASE STATUS, FARE CONTRACT |
| Status transition | statusChangeRequest | CUSTOMER RESERVATION CANCELLATION EVENT, CUSTOMER PRODUCT PURCHASE EVENT |
| Cancellation record | cancellation | CUSTOMER RESERVATION CANCELLATION ENTRY |
| Ride / leg | ride | PT RIDE LEG, SERVICE JOURNEY, DATED VEHICLE JOURNEY |
| Stop / place | place | SCHEDULED STOP POINT, STOP PLACE |
| Geography | geoPosition | LOCATION |
