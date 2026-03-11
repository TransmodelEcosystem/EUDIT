# Endpoint-Level Functional Comparison

This document extends [`functional-comparison.md`](functional-comparison.md) by listing the
**exact endpoints** (or message pairs for FerryGateway) that each standard exposes for each
of the 25 functional areas. The goal is to give EUDIT harmonisation work a concrete, operation-
level view alongside the high-level coverage matrix.

Column headers follow the same standard versions used in `functional-comparison.md`.
TOMP-API-MP 2.0.0 (the reverse TO→MP callback channel) is merged into the TOMP-API 2.0.0
column; its three endpoints are labelled _(MP callback)_ to distinguish direction.
BoB cells include the sub-API name in parentheses. FerryGateway cells use the XSD message
pair name. A `—` means the standard has no endpoint for that operation.

InterMOD is omitted — no specification is available.

---

## 1. Network & geography

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| List/search places | `GET /places` `POST /places` `GET /places/{placeId}` | — | — | — | — |
| List zones / fare zones | `GET /zones` | — | — | — | — |
| List coach layouts / seat maps | `GET /coach-layouts` `GET /coach-layouts/{layoutId}` `GET /coach-deck-layouts` `GET /coach-deck-layouts/{layoutId}` | — | — | — | — |
| Get routes / ports | — | — | — | — | `GetRoutesRequest/Response` |
| Passenger / vehicle type reference | `GET /passenger-categories` | — | — | `GET /productcat/traveller` (Product) | `GetPassengerAndVehicleTypesRequest/Response` |
| Participant registry | — | — | — | `GET /participantMetadata` `GET /participantMetadata/{pid}` (ParticipantMetadata) | — |

---

## 2. Timetable / schedule

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Query timetable / schedule | — | — | — | — | `GetTimeTablesRequest/Response` |
| Get API versions | `GET /versions` | — | — | — | — |

---

## 3. Trip / journey search

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Search trips / journeys | `POST /trips-collection` `GET /trips-collections/{tripsCollectionId}` `POST /trips` `GET /trips/{tripId}` | `POST /processes/search-offers/execution` | `POST /processes/search-offers/execute` | — | `GetSailingsRequest/Response` |
| Get offer price overview by route | `POST /offer-overview-route` | — | — | — | — |
| Get offer price overview by trip | `POST /offer-overview-trip` | — | — | — | — |

---

## 4. Availability query

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get vehicle place map / seat availability | `POST /availabilities/vehicle-place-map` `GET /availabilities/place-map` `POST /availabilities/place-map` | — | — | — | — |
| Get nearby availability | `GET /availabilities/nearby` | — | — | — | — |
| Get preference-based availability | `GET /availabilities/preferences` | — | — | — | — |
| Get on-demand / continuous service availability | `POST /availabilities/on-demand-services` | — | — | — | — |
| Request available assets | — | `GET /collections/assets/items` | `GET /collections/assets/items` | — | — |
| Query sailing availability / services | — | — | — | — | `GetServicesRequest/Response` |

---

## 5. Offer search / fare query

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Search offers | `POST /offers` | `GET /collections/offers/items` | `POST /processes/search-offers/execute` | `POST /product` `GET /product` `GET /product/{productId}` (Product) | — |
| Get additional offers on booked offer | `GET /bookings/{bookingId}/booked-offers/{bookedOfferId}/additional-offers` | — | — | — | — |
| Get price / fare details | — | `GET /collections/fares/items` | — | `GET /productcat/fare` `GET /productcat/generic` `GET /productcat/product` (Product) | `GetPriceRequest/Response` |
| Get product list / product search | `GET /products` `GET /products/{productId}` `GET /product-tags` `POST /products-search` | — | — | — | — |
| Get reduction cards | `GET /reduction-cards` | — | — | — | — |
| Get entitlements / card types / licence types / user profiles | — | `GET /collections/entitlements/items` `GET /collections/card-types/items` `GET /collections/license-types/items` `GET /collections/user-profiles/items` | — | — | — |
| Get pre-distributed product identifiers | — | — | — | `GET /pds` (Product) | — |
| Get offer / promo codes | — | — | — | — | `GetOfferCodesRequest/Response` |

---

## 6. Offer selection / on-hold

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Create on-hold offer | `POST /bookings/{bookingId}/on-hold-offer` | — | — | — | — |
| Confirm on-hold offer | `PATCH /bookings/{bookingId}/on-hold-offer/{onHoldOfferId}` | — | — | — | — |
| Delete on-hold offer | `DELETE /bookings/{bookingId}/on-hold-offer/{onHoldOfferId}` | — | — | — | — |
| Select offers into package | — | `POST /processes/select-offers/execution` | `POST /processes/select-offers/execute` | — | — |
| Remove offer from package | — | `POST /processes/remove-offer/execution` | — | — | — |
| Release package | — | `POST /processes/release-package/execution` | `POST /processes/release-package/execute` | — | — |
| Create purchase manifest (pre-distribution) | — | — | — | `POST /manifest` `GET /manifest/{manifestId}` (Product) | — |
| Make sailing reservation | — | — | — | — | `ReservationRequest/Response` |

---

## 7. Traveller / passenger management

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get / update booking passenger(s) | `GET /bookings/{bookingId}/passengers` `GET /bookings/{bookingId}/passengers/{passengerId}` `PATCH /bookings/{bookingId}/passengers` `PATCH /bookings/{bookingId}/passengers/{passengerId}` | — | — | — | — |
| Get / update purchaser on booking | `GET /bookings/{bookingId}/purchaser` `PATCH /bookings/{bookingId}/purchaser` `POST /bookings/{bookingId}/purchaser` | — | — | — | — |
| Update traveller in package | — | `POST /processes/update-traveller/execution` | `POST /processes/update-traveller/execute` | — | — |
| Add traveller to package | — | — | `POST /processes/add-traveller/execute` | — | — |
| Remove traveller from package | — | — | `POST /processes/remove-traveller/execute` | — | — |
| Traveller CRUD | — | `POST /collections/customers/items` `GET /collections/customers/items/{customerId}` `PATCH /collections/customers/items/{customerId}` `DELETE /collections/customers/items/{customerId}` | — | `GET /traveller` `POST /traveller` `GET /traveller/{travellerId}` `PUT /traveller/{travellerId}` `DELETE /traveller/{travellerId}` (Traveller) | — |
| Traveller wallet | — | — | — | `GET /traveller/{travellerId}/wallet` `POST /traveller/{travellerId}/wallet/transaction` `GET /traveller/{travellerId}/wallet/transaction/{transactionId}` `PATCH /traveller/{travellerId}/wallet/transaction/{transactionId}` (Traveller) | — |
| Traveller notifications | — | — | — | `POST /traveller/{travellerId}/notification` `GET /traveller/{travellerId}/notification/{notificationId}` (Traveller) | — |
| Get passenger & vehicle types | — | — | — | — | `GetPassengerAndVehicleTypesRequest/Response` |

---

## 8. Booking creation

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Create booking | `POST /bookings` | `POST /processes/purchase-offers/execution` `POST /processes/purchase-package/execution` `POST /processes/purchase-product/execution` | `POST /processes/purchase-offers/execute` `POST /processes/purchase-package/execute` `POST /processes/2-phase-purchase-package/execute` | `POST /booking` (Booking) | — |
| Confirm purchase (two-phase commit) | — | `POST /processes/confirm-purchase/execution` | `POST /processes/confirm-package/execute` | `PATCH /booking/{bookingId}` (Booking) | — |
| Rollback / cancel purchase | — | `POST /processes/rollback-purchase/execution` | — | — | — |
| Extend purchase expiry time | — | `POST /processes/extend-expiry-time/execution` | `POST /processes/extend-expiry-time/execute` | — | — |
| Confirm sailing reservation | — | — | — | — | `ConfirmReservationRequest/Response` |
| Add booked offers to booking | `POST /bookings/{bookingId}/booked-offers` | — | — | — | — |
| Update booking (non-confirm) | `PATCH /bookings/{bookingId}` | — | — | — | — |
| Split booking | `POST /bookings/{bookingId}/split` | — | — | — | — |
| Cleanup booking | `POST /bookings/{bookingId}/cleanup` | — | — | — | — |
| Use asset (shared mobility) | — | `POST /processes/use-asset/execution` | — | — | — |

---

## 9. Booking retrieval

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get booking by ID | `GET /bookings/{bookingId}` | `GET /collections/packages/items` | — | `GET /booking` `GET /booking/{bookingId}` (Booking) | `RecallBookingRequest/Response` |
| Search bookings | `POST /bookings-search` | — | — | — | — |
| Get booking history | `GET /bookings/{bookingId}/history` | — | — | — | — |
| Get booked offer | `GET /bookings/{bookingId}/booked-offers/{bookedOfferId}` | — | — | — | — |
| Add / delete booking parts (reservations, ancillaries, admissions) | `POST /bookings/{bookingId}/booked-offers/{bookedOfferId}/offer-parts` `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}/offer-parts` `POST /bookings/{bookingId}/booked-offers/{bookedOfferId}/reservations` `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}/reservations/{reservationId}` `POST /bookings/{bookingId}/booked-offers/{bookedOfferId}/ancillaries` `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}/ancillaries/{ancillaryId}` `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}/admissions/{admissionId}` `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}` | — | — | — | — |
| Get / add / delete booking documents | `POST /bookings/{bookingId}/documents` `GET /bookings/{bookingId}/documents/{documentId}` `DELETE /bookings/{bookingId}/documents/{documentId}` | — | — | — | — |
| Get external datasources | — | `GET /collections/datasources/items` | `GET /collections/datasources/items` | — | — |

---

## 10. Seat / spot / berth reservation

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Assign asset (seat / spot) to leg | — | `POST /processes/assign-asset/execution` | `POST /processes/assign-asset/execute` | — | — |
| Add reservation to booking | `POST /bookings/{bookingId}/booked-offers/{bookedOfferId}/reservations` | — | — | — | — |
| Get coach / seat layout | `GET /coach-layouts` `GET /coach-layouts/{layoutId}` `GET /coach-deck-layouts` `GET /coach-deck-layouts/{layoutId}` | — | — | — | — |
| Get vehicle place map | `POST /availabilities/vehicle-place-map` | — | — | — | — |
| Ferry cabin / berth / deck reservation | — | — | — | — | `ReservationRequest/Response` `ConfirmReservationRequest/Response` |

---

## 11. Ancillary services

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| List available ancillaries | — | `GET /collections/ancillaries/items` | `GET /collections/ancillaries/items` | — | — |
| Assign ancillary to package / leg | — | `POST /processes/assign-ancillary/execution` | `POST /processes/assign-ancillary/execute` | — | — |
| Add ancillary to booking | `POST /bookings/{bookingId}/booked-offers/{bookedOfferId}/ancillaries` | — | — | — | — |
| Remove ancillary from booking | `DELETE /bookings/{bookingId}/booked-offers/{bookedOfferId}/ancillaries/{ancillaryId}` | — | — | — | — |
| Get on-board / shore services | — | — | — | — | `GetServicesRequest/Response` |
| Get connecting bus transfers | — | — | — | — | `GetBusTransferRequest/Response` |

---

## 12. Fulfillment / travel documents

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Create / confirm fulfillment | `POST /bookings/{bookingId}/fulfillments` `PATCH /bookings/{bookingId}/fulfillments` | — | — | — | — |
| Fulfillment pre-check | `GET /bookings/{bookingId}/fulfillment-check` | — | — | — | — |
| Get fulfillment / ticket | `GET /fulfillments/{fulfillmentId}` `PATCH /fulfillments/{fulfillmentId}` | — | — | — | — |
| Get travel documents | — | `GET /collections/travel-documents/items` | `GET /collections/travel-documents/items` | — | — |
| Issue ticket | — | — | — | `POST /ticket` (Ticket) | — |
| Get / update ticket | — | — | — | `GET /ticket` `GET /ticket/{ticketId}` `PATCH /ticket/{ticketId}` (Ticket) | — |
| Activate ticket | — | — | — | `PUT /ticket/{ticketId}/active` (Ticket) | — |
| Get ticket activation status | — | — | — | `GET /ticket/{ticketId}/active` `GET /ticket/{ticketId}/activationStatus` (Ticket) | — |
| Create / get ticket bundle | — | — | — | `POST /ticketbundle` `GET /ticketbundle/{ticketBundleId}` `PATCH /ticketbundle/{ticketBundleId}` (Ticket) | — |
| QR code / boarding pass | — | — | — | — | _(embedded in `ConfirmReservationResponse`)_ |
| Continuous service usage (on-demand) | `GET /fulfillments/{fulfillmentId}/continuous-service-usage/{continuousServiceUsageId}` `PATCH /fulfillments/{fulfillmentId}/continuous-service-usage/{continuousServiceUsageId}` | — | — | — | — |
| Token — get / manage tokens | — | — | — | `GET /token` `GET /token/{thumbprint}` `GET /token/{thumbprint}/revoked` `GET /token/revocationlist` (Token) | — |
| Token hints | — | — | — | `GET /token/{thumbprint}/hint` `POST /token/{thumbprint}/hint` `GET /token/{thumbprint}/hint/{hintId}` `DELETE /token/{thumbprint}/hint/{hintId}` (Token) | — |
| Token PSP preference | — | — | — | `GET /token/{thumbprint}/psp` `PUT /token/{thumbprint}/psp` `DELETE /token/{thumbprint}/psp` (Token) | — |
| Traveller token operations | — | — | — | `GET /token/{tokenId}/productSets` `GET /token/{tokenId}/challenge` `POST /token/{tokenId}/ticket` `GET /token/{tokenId}/wallet` `POST /token/{tokenId}/wallet/transaction` `GET /token/{tokenId}/wallet/transaction/{transactionId}` `PATCH /token/{tokenId}/wallet/transaction/{transactionId}` (Traveller) | — |
| MTB product sets / activate MTB | — | — | — | `GET /mtb/{issuerSignature}/productSets` `POST /mtb/{issuerSignature}/activate` (Traveller) | — |
| Product set management | — | — | — | `POST /productSet` `GET /productSet/{productSetId}` `DELETE /productSet/{productSetId}` `PATCH /productSet/{productSetId}` (Traveller) | — |
| Ticket notification (event push) | — | — | — | `POST /ticketNotification` (Traveller) | — |

---

## 13. Physical asset management

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Perform asset operation (lock/unlock/open-trunk) | — | `POST /processes/{assetOperation}-asset/execution` | — | — | — |
| Perform product operation | — | `POST /processes/{productOperation}-product/execution` | — | — | — |
| Device key provisioning | — | — | — | `POST /device/key` (Device) | — |
| Device key derivation keys (KDK) | — | — | — | `GET /device/kdk` (Device) | — |
| Device user-agent registry | — | — | — | `GET /device/userAgent` `GET /device/userAgent/{ua}` (Device) | — |

---

## 14. Trip execution / leg operations

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Perform leg operation (start/pause/resume/end/extend/postpone) | — | `POST /processes/{legOperation}-leg/execution` | — | — | — |
| Update travel specification | — | `POST /processes/update-travel-specification/execution` | — | — | — |

---

## 15. Cancellation

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Initiate release (cancellation without refund) | `POST /bookings/{bookingId}/release-offers` `GET /bookings/{bookingId}/release-offers/{releaseOfferId}` `PATCH /bookings/{bookingId}/release-offers/{releaseOfferId}` `DELETE /bookings/{bookingId}/release-offers/{releaseOfferId}` | — | — | — | — |
| Cancel fulfillments | `POST /bookings/{bookingId}/cancel-fulfillments-offers` `GET /bookings/{bookingId}/cancel-fulfillments-offers/{cancelFulfillmentsOfferId}` `PATCH /bookings/{bookingId}/cancel-fulfillments-offers/{cancelFulfillmentsOfferId}` `DELETE /bookings/{bookingId}/cancel-fulfillments-offers/{cancelFulfillmentsOfferId}` | — | — | — | — |
| Delete booking | `DELETE /bookings/{bookingId}` | — | — | — | — |
| Cancel package | — | `POST /processes/cancel-package/execution` | `POST /processes/cancel-package/execute` | — | — |
| Confirm / cancel booking (status change) | — | — | — | `PATCH /booking/{bookingId}` (Booking) | — |
| Cancel booking | — | — | — | — | `CancelBookingRequest/Response` |
| Get cancellation charge | — | — | — | — | `GetCancelChargeRequest/Response` |

---

## 16. Refund / exchange / after-sales

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Initiate refund offer | `POST /bookings/{bookingId}/refund-offers` | — | — | — | — |
| Pre-check refund | `GET /bookings/{bookingId}/refund-offers/{refundOfferId}/confirmation-check` | — | — | — | — |
| Get / accept / delete refund offer | `GET /bookings/{bookingId}/refund-offers/{refundOfferId}` `PATCH /bookings/{bookingId}/refund-offers/{refundOfferId}` `DELETE /bookings/{bookingId}/refund-offers/{refundOfferId}` | — | — | — | — |
| Initiate exchange | `POST /bookings/{bookingId}/exchange-offers` `POST /bookings/{bookingId}/exchange-operations` `GET /bookings/{bookingId}/exchange-operations/{exchangeOperationId}` `PATCH /bookings/{bookingId}/exchange-operations/{exchangeOperationId}` `DELETE /bookings/{bookingId}/exchange-operations/{exchangeOperationId}` | — | — | — | — |
| Get refund options | — | `GET /collections/redress-options/items` | `GET /collections/refund-options/items` | — | — |
| Claim / confirm redress or refund | — | `POST /processes/claim-redress-option/execution` `POST /processes/confirm-redress-option/execution` | `POST /processes/claim-refund-option/execute` `POST /processes/confirm-refund-option/execute` | — | — |
| Refund deposit | — | `POST /processes/refund-deposit/execution` | — | — | — |
| Get ticket refundable status | — | — | — | `GET /ticket/{ticketId}/refundableStatus` (Ticket) | — |
| Get / set ticket refund status | — | — | — | `GET /ticket/{ticketId}/refundStatus` `PUT /ticket/{ticketId}/refundStatus` (Ticket) | — |
| Get / set ticket hindered status | — | — | — | `GET /ticket/{ticketId}/hinderedStatus` `PUT /ticket/{ticketId}/hinderedStatus` (Ticket) | — |
| Get / set ticket recoverable / recover status | — | — | — | `GET /ticket/{ticketId}/recoverableStatus` `GET /ticket/{ticketId}/recoverStatus` `PUT /ticket/{ticketId}/recoverStatus` (Ticket) | — |
| Get / set ticket suspended status | — | — | — | `GET /ticket/{ticketId}/suspendedStatus` (Ticket) | — |
| Revoke ticket | — | — | — | `GET /ticket/{ticketId}/revoke` `PUT /ticket/{ticketId}/revoke` (Ticket) | — |
| Ticket event log | — | — | — | `GET /ticket/{ticketId}/event` `GET /ticket/{ticketId}/event/{eventId}` (Ticket) | — |
| Create reimbursement | `POST /bookings/{bookingId}/reimbursements` `GET /bookings/{bookingId}/reimbursements/{reimbursementId}` `PATCH /bookings/{bookingId}/reimbursements/{reimbursementId}` | — | — | — | — |

---

## 17. Pricing & fare structure

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get fare / fare structure | `GET /products` `GET /products/{productId}` | `GET /collections/fares/items` | — | `GET /productcat/fare` (Product) | `GetPriceRequest/Response` |
| Get product categories | — | — | — | `GET /productcat/generic` `GET /productcat/product` (Product) | — |

---

## 18. Customer account management

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get travel account | `GET /travel-accounts` | — | — | — | — |
| Create customer account | — | `POST /collections/customers/items` | — | — | — |
| Get / update / delete customer account | — | `GET /collections/customers/items/{customerId}` `PATCH /collections/customers/items/{customerId}` `DELETE /collections/customers/items/{customerId}` | — | — | — |
| Traveller CRUD (full account) | — | — | — | `GET /traveller` `POST /traveller` `GET /traveller/{travellerId}` `PUT /traveller/{travellerId}` `DELETE /traveller/{travellerId}` (Traveller) | — |
| Wallet & transactions | — | — | — | `GET /traveller/{travellerId}/wallet` `POST /traveller/{travellerId}/wallet/transaction` `GET /traveller/{travellerId}/wallet/transaction/{transactionId}` `PATCH /traveller/{travellerId}/wallet/transaction/{transactionId}` (Traveller) | — |

---

## 19. Invoicing & payment

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get payment details | — | `GET /collections/payments/items` | — | — | — |
| Confirm payment | — | `POST /processes/confirm-payment/execution` | — | — | — |
| B2B payment request | — | `POST /processes/request-payment/execution` _(MP callback)_ | — | — | — |
| B2B purchase confirmation request | — | `POST /processes/request-confirmation/execution` _(MP callback)_ | — | — | — |
| PSP preference on token | — | — | — | `GET /token/{thumbprint}/psp` `PUT /token/{thumbprint}/psp` `DELETE /token/{thumbprint}/psp` (Token) | — |
| Get invoices | — | — | — | — | `GetInvoicesRequest/Response` |

---

## 20. Complaints & support

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Create / get / update complaint | `POST /complaints` `GET /complaints/{complaintId}` `PATCH /complaints/{complaintId}` | — | — | — | — |
| Create support ticket | — | `POST /processes/request-support/execution` | — | — | — |
| Get support tickets | — | `GET /collections/support-tickets/items` | — | — | — |

---

## 21. Travel guarantees & redress

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get redress options | — | `GET /collections/redress-options/items` | — | — | — |
| Claim / confirm redress | — | `POST /processes/claim-redress-option/execution` `POST /processes/confirm-redress-option/execution` | — | — | — |

---

## 22. Promotions & discount codes

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Get reduction cards | `GET /reduction-cards` | — | — | — | — |
| Get product / promo codes | — | — | — | — | `GetOfferCodesRequest/Response` |
| Search products with discount filter | — | — | — | `POST /product` (Product, with discount code filter params) | — |

---

## 23. Authentication / security

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| OAuth token (client credentials / password / refresh) | — | `POST /oauth/token` `POST /connect/token` | `POST /oauth/token` | — | — |
| JWT / TLS authentication (explicit endpoint) | — | — | — | `GET /auth/{entityId}` (Authentication) | — |
| MTB public key management | — | — | — | `GET /participantMetadata/{pid}/mtbPublicKey` `POST /participantMetadata/{pid}/mtbPublicKey` `GET /participantMetadata/{pid}/mtbPublicKey/{kid}` `PUT /participantMetadata/{pid}/mtbPublicKey/{kid}` `DELETE /participantMetadata/{pid}/mtbPublicKey/{kid}` (ParticipantMetadata) | — |
| Auth-token public key management | — | — | — | `GET /participantMetadata/{pid}/authtokenPublicKey` `POST /participantMetadata/{pid}/authtokenPublicKey` `GET /participantMetadata/{pid}/authtokenPublicKey/{kid}` `PUT /participantMetadata/{pid}/authtokenPublicKey/{kid}` `DELETE /participantMetadata/{pid}/authtokenPublicKey/{kid}` (ParticipantMetadata) | — |
| Token revocation list | — | — | — | `GET /token/revocationlist` `GET /token/{thumbprint}/revoked` (Token) | — |
| Issuer signature constraints | — | — | — | `GET /participantMetadata/{pid}/issuerSignatureConstraint` `POST /participantMetadata/{pid}/issuerSignatureConstraint` `GET /participantMetadata/{pid}/issuerSignatureConstraint/{acceptablePid}` `PUT /participantMetadata/{pid}/issuerSignatureConstraint/{acceptablePid}` `DELETE /participantMetadata/{pid}/issuerSignatureConstraint/{acceptablePid}` (ParticipantMetadata) | — |

---

## 24. Disruption & real-time info

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Operational notification (TO→MP) | — | `POST /processes/notification/execution` _(MP callback)_ | — | — | — |
| Trip status / situation (contextual, on booking) | _(embedded in trip / booking response schemas)_ | — | — | — | — |
| Health check | — | `GET /health` | — | — | — |

---

## 25. API discovery / capability

| Operation | OSDM 3.7.1 | TOMP-API 2.0.0 | OMSA 0.1.0 | BoB (full suite) | FerryGateway 1.3.1 |
|-----------|-----------|---------------|-----------|-----------------|-------------------|
| Landing page | — | `GET /` | `GET /` | — | — |
| API specification | — | `GET /api` | `GET /api` | — | — |
| Conformance declaration | — | `GET /conformance` | `GET /conformance` | — | — |
| List feature collections | — | `GET /collections` `GET /collections/{collectionId}` | `GET /collections` `GET /collections/{collectionId}` | — | — |
| List / describe processes | — | `GET /processes` `GET /processes/{processId}` | `GET /processes` `GET /processes/{processID}` | — | — |
| Capabilities check | — | `GET /capabilities` | — | — | — |
| Get API versions | `GET /versions` | — | — | — | — |
| Participant registry (cross-operator discovery) | — | — | — | `GET /participantMetadata` `GET /participantMetadata/{pid}` (ParticipantMetadata) | — |
| Participant info | — | — | — | `GET /participantMetadata/{pid}/participantInfo` `PUT /participantMetadata/{pid}/participantInfo` `DELETE /participantMetadata/{pid}/participantInfo` (ParticipantMetadata) | — |
| Participant interface endpoints | — | — | — | `GET /participantMetadata/{pid}/interfaceEndpoint` `POST /participantMetadata/{pid}/interfaceEndpoint` `GET /participantMetadata/{pid}/interfaceEndpoint/{endpointId}` `PUT /participantMetadata/{pid}/interfaceEndpoint/{endpointId}` `DELETE /participantMetadata/{pid}/interfaceEndpoint/{endpointId}` (ParticipantMetadata) | — |
| Participant domain name | — | — | — | `GET /participantMetadata/{pid}/domainName` `PUT /participantMetadata/{pid}/domainName` `DELETE /participantMetadata/{pid}/domainName` (ParticipantMetadata) | — |

---

## Notes on coverage

* **OSDM** endpoints are hosted under a single base URL. Tags group them into functional areas:
  `Trips`, `Offers`, `On Hold`, `Bookings`, `BookedOffers`, `BookingPart`, `Fulfillments`,
  `Refund`, `Release`, `Cancel Fulfillments`, `Exchange`, `Reimbursement Management`,
  `Complaint Management`, `Passengers`, `Purchaser`, `Availabilities`, `Master Data`,
  `Travel Account`, `On-Demand Services`, `Booking Documents`.
* **TOMP-API** uses OGC API Processes + Features: `POST /processes/{operation}/execution` for
  state-changing calls; `GET /collections/{resource}/items` for read-only collections. The
  three TOMP-API-MP 2.0.0 reverse-channel endpoints (TO→MP callbacks) are included in this
  column and labelled _(MP callback)_ to indicate they flow in the opposite direction.
* **OMSA** mirrors the TOMP-API OGC pattern but uses `/execute` (not `/execution`) and has its
  own OAuth token endpoint.
* **BoB** endpoints are distributed across nine independent sub-APIs each deployed at its own
  base URL. The sub-API name is noted in parentheses in each cell.
* **FerryGateway** is an XML Schema standard. Each row uses the request/response message pair
  name from `ferrygateway.xsd` rather than an HTTP method + path.
