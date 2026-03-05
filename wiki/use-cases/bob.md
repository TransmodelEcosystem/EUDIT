# BoB Use Cases

BoB (Biljetter och Bokning — Tickets and Booking) is a **B2B backend-to-backend ticketing interoperability layer**. It was developed under UIC/ERA auspices as a Nordic/European standard for exchanging signed Mobile Ticketing Blocks (MTBs) between transport operators, distributors, and validators.

BoB is intentionally narrow by design. It does not replicate the full offer-search and booking capabilities of OSDM or TOMP-API. Instead it occupies the layer between them: once a sale has been decided upon (by any means), BoB defines how the resulting CUSTOMER PURCHASE PACKAGE is communicated backend-to-backend, how the signed MTB is conveyed as a TRAVEL DOCUMENT, and how validators (human inspectors or automated gates) confirm entitlement. In EUDIT architecture terms, BoB is the **ticketing and validation sub-layer** that OSDM and TOMP-API hand off to.

The BoB Booking API (v2.0.1) exposes exactly four HTTP operations, all under `/booking`. There is no separate endpoint set for MTB generation, cryptographic signing, or validation — those are handled by companion BoB sub-specifications (the MTB specification and the Traveller API). Only the booking call surface is covered by the YAML specification included in this repository.

---

## Booking

BoB's booking surface covers the full lifecycle of a B2B booking call: creation from a signed MTB manifest, retrieval, and status transition (confirm or cancel). All four API operations belong here.

### request booking (create preliminary booking)

A distributor backend submits a signed MTB manifest to an operator backend to create a preliminary booking. The `bookingCall` body carries the manifest (a base64url-encoded MTB container encoding the FARE PRODUCT entitlements), an optional TRANSPORT CUSTOMER identifier, and a mandatory idempotency `requestId` to allow safe retransmission. The operator returns a `booking` in `pending` status together with a `confirmBefore` deadline.

**Transmodel concepts:** CUSTOMER RESERVATION CREATION EVENT, TRAVEL DOCUMENT (manifest as IDENTITY TOKEN), TRANSPORT CUSTOMER (travellerId), CUSTOMER PURCHASE PACKAGE (pending state)

**Endpoint:** `POST /booking`

**Unique to BoB:** Yes — the `manifest` field carries a cryptographically signed MTB binary as the booking payload. No other standard in the EUDIT set uses a signed binary token as the primary input to a booking call. OSDM and TOMP-API carry structured offer/product identifiers instead.

---

### list bookings

A backend retrieves all bookings associated with a given traveller identifier or request ID. Used for reconciliation, idempotency checks, and distributor-side state synchronisation.

**Transmodel concepts:** CUSTOMER PURCHASE PACKAGE (list), TRANSPORT CUSTOMER (filter by travellerId)

**Endpoint:** `GET /booking`

**Unique to BoB:** No — listing bookings for a traveller exists across OSDM, TOMP-API, and OMSA. The BoB version is notably minimal (two optional query parameters only) compared to the richer filter surfaces in the other standards.

---

### retrieve booking

A backend retrieves the current state of a single booking by its machine-readable `bookingId`. Returns the full `booking` object including status, confirmation timestamps, ordered list of `ride` legs, and human-readable `confirmationCode`.

**Transmodel concepts:** CUSTOMER PURCHASE PACKAGE (by id), CUSTOMER PURCHASE STATUS, PT RIDE LEG (rides), SERVICE ACCESS CODE (confirmationCode)

**Endpoint:** `GET /booking/{bookingId}`

**Unique to BoB:** No — single-booking retrieval by identifier is a universal pattern. The BoB-specific element is the `confirmationCode` which maps to SERVICE ACCESS CODE and is intended for display to validators or gate systems.

---

### confirm or cancel booking

A backend transitions an existing booking from `pending` to either `confirmed` or `cancelled` by submitting a `statusChangeRequest`. The request must include the signed MTB container as proof of purchase (`mtb` field, base64url-encoded), and the target `status` (`confirmed` or `cancelled`). On confirmation the booking moves to `confirmed` and a `confirmedWhen` timestamp is recorded; on cancellation a CUSTOMER RESERVATION CANCELLATION ENTRY is produced.

**Transmodel concepts:** CUSTOMER PRODUCT PURCHASE EVENT (confirm path), CUSTOMER RESERVATION CANCELLATION EVENT (cancel path), CUSTOMER PURCHASE STATUS, TRAVEL DOCUMENT / MEDIUM ACCESS DEVICE (mtb as IDENTITY TOKEN)

**Endpoint:** `PATCH /booking/{bookingId}`

**Unique to BoB:** Yes — the requirement to supply a signed MTB as proof-of-purchase in the confirmation/cancellation request is unique to BoB. In OSDM and TOMP-API the confirmation step carries structured offer references or payment tokens, not a cryptographically signed binary ticket container.

---

## Booking Status Lifecycle

The `booking.status` field follows a defined state machine that maps directly onto Transmodel CUSTOMER PURCHASE STATUS values:

| BoB status | Transmodel equivalent | Meaning |
|------------|-----------------------|---------|
| `pending` | CUSTOMER PURCHASE STATUS (booked) | Reservation created, not yet confirmed or paid |
| `confirmed` | CUSTOMER PURCHASE STATUS (paid / activated) | Purchase finalised; CUSTOMER PURCHASE PACKAGE active |
| `cancelled` | CUSTOMER RESERVATION CANCELLATION EVENT | Booking cancelled; CUSTOMER RESERVATION CANCELLATION ENTRY recorded |
| `miss` | RIDER NO SHOW EVENT | Passenger did not board; no direct Transmodel CUSTOMER PURCHASE STATUS value |
| `completed` | CUSTOMER PURCHASE STATUS (consumed) | Travel completed; FARE PRODUCT consumed |

The `miss` status is the only value with no precise Transmodel CUSTOMER PURCHASE STATUS counterpart. It corresponds most closely to a void or expired state and would map to RIDER NO SHOW EVENT at the event-log level.

---

## Ticket Validation

Ticket validation is not covered by the `/booking` endpoints in the YAML specification included in this repository. It is handled by companion BoB sub-specifications — the MTB specification (cryptographic container format) and validator-side APIs — which define how an inspector or automated gate reads and verifies an MTB presented by the TRANSPORT CUSTOMER.

### validate ticket (human inspector)

A human inspector reads the MTB from the passenger's mobile device (via QR code, NFC, or visual display of the `confirmationCode`) and verifies the cryptographic signature and entitlement claims against the operator's key store. The inspector checks that the FARE PRODUCT encoded in the MTB covers the boarded SERVICE JOURNEY and that the booking `status` is `confirmed` or `completed`.

**Endpoint(s):** Defined in the BoB MTB/validator sub-specification, not in `BoB booking.yaml`. The `confirmationCode` from `GET /booking/{bookingId}` (SERVICE ACCESS CODE) is the human-readable surrogate used in this flow.

**Unique to BoB:** Yes — explicit cryptographic MTB validation by an inspector is a BoB-specific concept. OSDM and TOMP-API do not define a validator-facing endpoint or MTB format; OMSA and FerryGateway have domain-specific boarding checks but not a general signed-token validation layer.

---

### validate ticket (automated gate)

An automated gate (e.g., at a platform barrier or ferry boarding lane) reads the MTB from the passenger's device, verifies the signature, and grants or denies access. The gate communicates backend-to-backend with the operator system to check booking status and record the boarding event.

**Endpoint(s):** Defined in the BoB MTB/validator sub-specification.

**Unique to BoB:** Yes — same rationale as human inspector validation. The signed MTB binary format and the backend gate-validation protocol are BoB-specific and have no structural equivalent in the other EUDIT standards.

---

## Out of Scope

The following capabilities are explicitly outside BoB's design boundaries. They are handled by OSDM, TOMP-API, OMSA, or FerryGateway as appropriate, and BoB intentionally defers to those standards rather than duplicating them.

| Capability | Why out of scope for BoB |
|------------|--------------------------|
| Trip / journey search | BoB has no trip planning layer. OSDM and TOMP-API cover this. |
| Fare / offer search | BoB assumes the FARE PRODUCT and SALES OFFER PACKAGE have already been selected. There are no `/offer` or `/fare` endpoints. |
| Seat reservation search or selection | No seat-map, availability, or preference endpoints. OSDM covers seat allocation in detail. |
| Payment processing | BoB does not handle payment flows, card tokens, or settlement. Payment is assumed to have occurred outside BoB before the booking is confirmed. |
| Customer account management | No account creation, profile update, or loyalty points. OSDM's travel-account use cases and TOMP-API's administration use cases cover this. |
| After-sales beyond cancellation | No refund offers, exchange offers, or partial release operations. The `PATCH /booking/{bookingId}` only transitions to `confirmed` or `cancelled`; the full after-sales lifecycle is OSDM territory. |
| Disruption / re-accommodation | No disruption notification, re-routing, or re-accommodation workflow. OSDM use case 6.5.2.5/6.5.2.6 covers this. |
| Ancillary services | No luggage, meals, or on-board service add-ons. |
| Multimodal / on-demand legs | No DRT or MaaS-layer orchestration. TOMP-API covers shared mobility and on-demand. |
| FerryGateway / OMSA domain specifics | Vessel manifest, OMSA sales-channel reporting, and ferry-specific passenger data are outside the BoB booking layer. |

---

## Relationship to Other EUDIT Standards

```
OSDM / TOMP-API          BoB Booking API          BoB MTB / Validator
(offer search,      →    (backend booking    →    (signed ticket issuance
 booking,                 call, status             and validation at
 after-sales)             lifecycle)               boarding point)
```

In a full EUDIT-compliant integration:

1. The TRANSPORT CUSTOMER searches for offers and selects a SALES OFFER PACKAGE via **OSDM** or **TOMP-API**.
2. The distributor backend calls **BoB** `POST /booking` with a signed MTB manifest to reserve the ride on the operator backend.
3. Upon payment confirmation (outside BoB), the distributor calls **BoB** `PATCH /booking/{bookingId}` with `status: confirmed` and the signed MTB.
4. At boarding, the validator (human or gate) verifies the MTB using the **BoB MTB sub-specification**.
