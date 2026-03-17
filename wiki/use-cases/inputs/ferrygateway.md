# FerryGateway Use Cases

FerryGateway (version 1.3.1, namespace `http://schemas.ferrygateway.org/1.3.1`) is a B2B XML standard maintained by the Ferrygateway Association. Unlike OSDM or TOMP-API — which expose REST/JSON endpoints — FerryGateway uses synchronous XML **request/response message pairs** over a single transport binding. Every operation is identified by its request message type and its corresponding response message type. The standard covers the full booking lifecycle: route discovery, timetable lookup, sailing search, pricing, service selection, reservation, booking confirmation, retrieval, cancellation, and invoicing, as well as connecting bus-transfer services and promotional offer codes.

---

## 1. Network & Reference Data

### get routes

Returns the set of routes (port pairs + operator) that an operator makes available for booking. The TRANSPORT CUSTOMER's system uses this to populate origin/destination selectors.

**Message(s):** `GetRoutes` / `GetRoutesResponse`
**Unique to FerryGateway:** Yes — routes are modelled as port-pair lines identified by UN/LOCODE codes rather than as stop-based networks. No equivalent flat-route-catalogue message exists in OSDM, TOMP-API, BoB, or OMSA.

---

### get passenger and vehicle types

Returns the operator's master reference tables for passenger age-band categories (Adult/Child/Infant with min/max age bounds) and vehicle dimension categories (lead vehicle and trailer), so that clients can build correctly validated search requests.

**Message(s):** `GetPassengerAndVehicleTypesRequest` / `GetPassengerAndVehicleTypesResponse`
**Unique to FerryGateway:** Partially — TOMP-API and OMSA expose user profile catalogues covering passenger type definitions, and TOMP-API 1.6+ includes a product model that can describe vehicle types. What remains distinctive to FerryGateway is the combination of passenger age-band categories and vehicle dimensional catalogues (lead vehicle and trailer) in a single reference-data query, along with the ferry-specific age-range bounds and vehicle dimension parameters.

---

## 2. Timetable

### get timetables

Retrieves the scheduled sailing timetable for a given operator and port pair within a specified date range. Each returned sailing is a DATED VEHICLE JOURNEY and may include vessel name, IMO number, and tidal-adjusted departure/arrival times.

**Message(s):** `GetTimeTablesRequest` / `GetTimeTablesResponse`
**Unique to FerryGateway:** Partially — timetable queries exist in other standards, but the ferry-specific attributes (vessel identification by IMO number, tidal adjustment, check-in window, fast-craft flag, terminal-within-port) are unique to FerryGateway.

---

## 3. Sailing Search & Availability

### search sailings

Performs a real-time availability search across one or more ferry legs for a given party of passengers, vehicles, and pets on a requested departure date. The response returns bookable DATED VEHICLE JOURNEYs grouped into FerryComponents, each with per-sailing availability flags (passenger, vehicle deck space, accommodation, pet space, and connecting transfer).

**Message(s):** `GetSailingsRequest` / `GetSailingsResponse`
**Unique to FerryGateway:** Yes — the combined passenger + road vehicle + pet availability search against a port-pair timetable (including vehicle deck-space availability, `IsVehicleAvailable`, `IsPetAvailable`, and `IsAccommodationAvailable` flags) has no equivalent in OSDM, TOMP-API, BoB, or OMSA.

---

### filter mini-cruise sailings

Restricts a sailing search to mini-cruise itineraries only (`IsOnlyMinicruise` flag) and optionally requests alternative routing suggestions (`ShowAlternativeRoutes`). This allows travel agents to surface short leisure crossings distinct from standard point-to-point sailings.

**Message(s):** `GetSailingsRequest` / `GetSailingsResponse` (with `IsOnlyMinicruise` = `true`)
**Unique to FerryGateway:** Yes — the mini-cruise filter and alternative-route suggestion flags are FerryGateway-specific concepts with no equivalent in OSDM, TOMP-API, BoB, or OMSA.

---

## 4. Offer & Pricing

### get price

Requests the fare price for a confirmed selection of passengers, vehicles, and sailings (a FerryComponent). The response returns a fully broken-down FARE PRICE (per passenger, vehicle, taxes, surcharges, discounts) together with a time-limited validity token (`Token` + `TokenExpiryTime`) that locks the price for subsequent booking.

**Message(s):** `GetPriceRequest` / `GetPriceResponse`
**Unique to FerryGateway:** No — fare/offer pricing queries exist in OSDM (`GET /offers`), TOMP-API, and BoB. The vehicle pricing dimension and the explicit price-lock token are FerryGateway-specific details.

---

### query available services for a sailing

Retrieves the catalogue of purchasable on-board and land services available for a specific sailing and passenger/vehicle combination. The `Mode` parameter filters by service category: generic on-board (`OnBoard`), meals (`OnBoardMeal`), cabin/berth accommodation (`OnBoardAccommodations`), or shore-based services (`Land`).

**Message(s):** `GetServicesRequest` / `GetServicesResponse`
**Unique to FerryGateway:** Partially — ancillary service catalogues exist in TOMP-API and OSDM; however, the combined query covering cabin accommodation, named meal options with diet types, on-board leisure services (spa, cinema, lounge, excursion), kennel services, and shore parking/hotel is specific to the ferry domain and has no direct equivalent.

---

## 5. Reservation & Booking

### create temporary reservation

Places a temporary hold on a sailing and fare for a specified party without committing to a binding purchase. The reservation has status `Reserved` and creates a CUSTOMER RESERVATION CREATION EVENT. The booking may later be confirmed or allowed to expire.

**Message(s):** `ReservationRequest` / `ReservationResponse`
**Unique to FerryGateway:** No — temporary reservation/hold workflows exist in OSDM, BoB, OMSA (`select-offers/execute` placing a package in `selected` status), and TOMP-API booking initiation. The FerryGateway implementation is distinguished by its inclusion of vehicles and pets in the held party.

---

### confirm reservation

Converts an existing temporary reservation (status `Reserved`) into a binding confirmed booking (status `Booked`). Triggers the transition from a CUSTOMER RESERVATION CREATION EVENT to a confirmed CUSTOMER PURCHASE PACKAGE.

**Message(s):** `ConfirmReservationRequest` / `ConfirmReservationResponse`
**Unique to FerryGateway:** No — a two-step reserve-then-confirm pattern also appears in OSDM, BoB, OMSA (`confirm-package/execute`), and TOMP-API booking confirmation. The FerryGateway variant is structurally equivalent.

---

### create binding booking

Creates a confirmed booking directly in a single step, without a preceding temporary reservation. The response returns the full CUSTOMER PURCHASE PACKAGE with booking reference, confirmed sailings, passenger details, vehicle details, selected services, cost breakdown, and optional QR code travel documents.

**Message(s):** `BookRequest` / `BookResponse`
**Unique to FerryGateway:** No — direct booking creation exists in OSDM, TOMP-API, BoB, and OMSA. FerryGateway is distinguished by its inclusion of road vehicle and pet data in the same booking record.

---

### retrieve booking

Retrieves the full details of an existing booking by its booking reference. Returns the complete CUSTOMER PURCHASE PACKAGE including all passengers, vehicles, pets, sailings, selected services, cost, and current status.

**Message(s):** `RecallBookingRequest` / `RecallBookingResponse`
**Unique to FerryGateway:** No — booking retrieval by reference exists in OSDM, TOMP-API, BoB, and OMSA.

---

## 6. Ancillary Services — Cabins, Meals & On-Board

### select cabin or berth accommodation

Books a cabin or berth category for specified passengers on a sailing. The accommodation service carries occupancy constraints (min/max occupants, berth count), accessibility flag, pet-allowed flag, and allergy-friendly flag. Mandatory cabin routes declare `IsAccommodationMandatory` in the sailing response.

**Message(s):** `GetServicesRequest` / `GetServicesResponse` (Mode = `OnBoardAccommodations`), then included in `BookRequest` / `ReservationRequest`
**Unique to FerryGateway:** Partially — OMSA `assign-asset/execute` and TOMP-API can model cabin or berth as a generic assignable asset. What remains distinctive to FerryGateway is the structured treatment of cabin/berth as a first-class booking entity with occupancy constraints (min/max occupants, berth count), accessibility flags, pet-allowed flags, allergy-friendly flags, and per-passenger assignment — attributes that are specific to the ferry accommodation domain.

---

### select meal service

Books a meal option for specified passengers on a sailing. Meal options carry diet-type specifications (Meat, Fish, Vegetarian, Vegan, Celiac, Jewish, Islamic), time slot, and on-board facility code. Passenger age-band (adult/child) determines applicable meal choices.

**Message(s):** `GetServicesRequest` / `GetServicesResponse` (Mode = `OnBoardMeal`), then included in `BookRequest` / `ReservationRequest`
**Unique to FerryGateway:** No — OMSA `assign-ancillary/execute` and TOMP-API ancillary assignment both cover catering and meal services as assignable ancillaries. The FerryGateway-specific elements are the structured diet-type specification (Meat, Fish, Vegetarian, Vegan, Celiac, Jewish, Islamic), the time slot and on-board facility code, and the per-passenger age-band meal selection.

---

### select generic on-board service

Books a generic on-board facility (cinema, conference room, lounge, spa, internet, excursion) for a specified number of units or passengers on a sailing.

**Message(s):** `GetServicesRequest` / `GetServicesResponse` (Mode = `OnBoard`), then included in `BookRequest` / `ReservationRequest`
**Unique to FerryGateway:** No — OMSA `assign-ancillary/execute` and TOMP-API ancillary assignment both support generic on-board service selection. The FerryGateway-specific elements are the ferry-domain service categories (cinema, conference room, lounge, spa, internet, excursion, kennel) and the unit-count booking model.

---

## 7. Land & Transfer Services

### query connecting bus transfers

Queries available connecting bus services to or from the ferry port for a specific sailing. The response describes bus departure/arrival times, stop identifiers, per-passenger-category pricing, and direction (to sailing / from sailing). This connects the ferry leg into a door-to-port multimodal journey.

**Message(s):** `GetBusTransferRequest` / `GetBusTransferResponse`
**Unique to FerryGateway:** Partially — multimodal connection queries exist in TOMP-API and InterMOD. Note that InterMOD is an in-scope EUDIT standard; its specifications are available under `wiki/specifications/`. A purpose-built ferry-port bus transfer query (with `ToSailing` / `FromSailing` directionality and per-category fares) remains specific to FerryGateway; InterMOD and TOMP-API do not model the sailing-relative direction parameter.

---

### select land service

Books a shore-based service (hotel, car parking, or other land service) associated with a sailing within a specified validity window (`FromDateTime` / `ToDateTime`).

**Message(s):** `GetServicesRequest` / `GetServicesResponse` (Mode = `Land`), then included in `BookRequest` / `ReservationRequest`
**Unique to FerryGateway:** Partially — land services as structured booking add-ons (shore-side parking, hotel) within a sailing booking are not modelled as a workflow in OSDM, TOMP-API, BoB, or OMSA.

---

## 8. Cancellation & After-Sales

### query cancellation charge

Retrieves the cancellation fee that would apply to a booking without yet executing the cancellation. The returned cost maps to the CANCELLING condition's `cancellationCharge`. Allows the TRANSPORT CUSTOMER to make an informed decision before committing.

**Message(s):** `GetCancelChargeRequest` / `GetCancelChargeResponse`
**Unique to FerryGateway:** No — pre-cancellation fee queries exist in OSDM and BoB.

---

### cancel booking

Submits the cancellation of a confirmed booking. The request includes the expected cancellation charge (as returned by `GetCancelCharge`) to guard against race conditions. The response confirms the CUSTOMER RESERVATION CANCELLATION EVENT and the actual charge applied.

**Message(s):** `CancelBookingRequest` / `CancelBookingResponse`
**Unique to FerryGateway:** No — booking cancellation exists in OSDM, TOMP-API, BoB, and OMSA.

---

## 9. Invoicing & Payment

### retrieve invoices

Retrieves the list of financial invoices issued to an agent or operator within a specified date range. Each invoice aggregates one or more confirmed CUSTOMER PURCHASE PACKAGEs and carries totals for amount, commission, tax base, tax amount, and tax rate. The invoice lifecycle (`Issued` / `Paid` / `Payment Delayed`) has no Transmodel equivalent.

**Message(s):** `GetInvoicesRequest` / `GetInvoicesResponse`
**Unique to FerryGateway:** No — TOMP-API `GET /collections/payments/items` provides a comparable B2B payment/invoice collection endpoint. OSDM and BoB have no structured invoice-retrieval workflow. The FerryGateway invoicing message remains distinctive in its aggregation of CUSTOMER PURCHASE PACKAGEs into a single invoice with commission, tax base, and tax-rate breakdown.

---

## 10. Promotions & Offer Codes

### retrieve offer codes

Returns the promotional offer codes defined by an operator, together with each code's associated FARE PRODUCT, booking validity window, travel validity window, and journey duration constraints. Clients pass an applicable code (`OfferCode`) in pricing and booking requests to trigger the associated SALE DISCOUNT RIGHT or conditional SALES OFFER PACKAGE.

**Message(s):** `GetOfferCodesRequest` / `GetOfferCodesResponse`
**Unique to FerryGateway:** No — promotional/discount code catalogues exist in OSDM and BoB. The FerryGateway implementation is structurally equivalent, returning the same SALES OFFER PACKAGE / SALE DISCOUNT RIGHT information.

---

## 11. Travel Documents

### issue QR code travel document

A QR code travel document (boarding pass equivalent) is returned as part of any booking or booking-retrieval response (`BookResponse`, `RecallBookingResponse`, `ConfirmReservationResponse`). QR codes may be issued at the booking level and/or per-passenger. They are the digital materialisation of a TRAVEL DOCUMENT against the CUSTOMER PURCHASE PACKAGE.

**Message(s):** Embedded in `BookResponse` / `RecallBookingResponse` / `ConfirmReservationResponse` (`QrCode` element)
**Unique to FerryGateway:** No — QR-code / barcode travel documents are produced by OSDM, BoB, and OMSA (`GET /collections/travel-documents/items`). TOMP-API also covers digital travel documents. OMSA notably supports multiple travel document formats (QR, Aztec, NFC token, Bluetooth key, external link) in addition to QR codes. The FerryGateway implementation is equivalent, returning base64-encoded QR data in the booking response.

---

## Summary

| Use Case | Message Pair | Unique to FerryGateway |
|----------|-------------|------------------------|
| get routes | `GetRoutes` / `GetRoutesResponse` | Yes |
| get passenger and vehicle types | `GetPassengerAndVehicleTypesRequest` / `GetPassengerAndVehicleTypesResponse` | Partially |
| get timetables | `GetTimeTablesRequest` / `GetTimeTablesResponse` | Partially |
| search sailings | `GetSailingsRequest` / `GetSailingsResponse` | Yes |
| filter mini-cruise sailings | `GetSailingsRequest` / `GetSailingsResponse` | Yes |
| get price | `GetPriceRequest` / `GetPriceResponse` | No |
| query available services for a sailing | `GetServicesRequest` / `GetServicesResponse` | Partially |
| create temporary reservation | `ReservationRequest` / `ReservationResponse` | No |
| confirm reservation | `ConfirmReservationRequest` / `ConfirmReservationResponse` | No |
| create binding booking | `BookRequest` / `BookResponse` | No |
| retrieve booking | `RecallBookingRequest` / `RecallBookingResponse` | No |
| select cabin or berth accommodation | `GetServicesRequest` + `BookRequest` | Partially |
| select meal service | `GetServicesRequest` + `BookRequest` | No |
| select generic on-board service | `GetServicesRequest` + `BookRequest` | No |
| query connecting bus transfers | `GetBusTransferRequest` / `GetBusTransferResponse` | Partially |
| select land service | `GetServicesRequest` + `BookRequest` | Partially |
| query cancellation charge | `GetCancelChargeRequest` / `GetCancelChargeResponse` | No |
| cancel booking | `CancelBookingRequest` / `CancelBookingResponse` | No |
| retrieve invoices | `GetInvoicesRequest` / `GetInvoicesResponse` | No |
| retrieve offer codes | `GetOfferCodesRequest` / `GetOfferCodesResponse` | No |
| issue QR code travel document | Embedded in booking responses | No |
