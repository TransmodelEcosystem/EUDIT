# OSDM → Transmodel Mapping

**Source:** `wiki/specifications/OSDM 3.7.1.yaml` (UIC IRS 90918-10, OpenAPI 3.0.3)  
**Reference:** Transmodel (EN 12896)  
**Annotations:** OSDM 3.7.1 carries `x-semantics` blocks on virtually every schema and property, mapping each directly to Transmodel concepts. This file captures those mappings in the standard table format.

---

## Table of Contents

1. [Trip & Journey Planning](#1-trip--journey-planning)
2. [Offers](#2-offers)
3. [Offer Parts](#3-offer-parts)
4. [Booking & Booking Parts](#4-booking--booking-parts)
5. [Passengers & Purchaser](#5-passengers--purchaser)
6. [Fulfillments & Documents](#6-fulfillments--documents)
7. [Refund, Exchange & Release](#7-refund-exchange--release)
8. [Reimbursement & Complaints](#8-reimbursement--complaints)
9. [Place / Seat Availability & Coach Layout](#9-place--seat-availability--coach-layout)
10. [Fares & Regional Validity](#10-fares--regional-validity)
11. [Products & Master Data](#11-products--master-data)
12. [Travel Accounts](#12-travel-accounts)
13. [On-Demand / Continuous Services](#13-on-demand--continuous-services)
14. [Pricing & Fees](#14-pricing--fees)
15. [Supporting / Utility Types](#15-supporting--utility-types)

---

## 1. Trip & Journey Planning

### Trip

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Trip | id | TRIP | id | exact |
| Trip | duration | TRIP | duration | exact |
| Trip | startTime | TRIP | departureTime | exact |
| Trip | endTime | TRIP | arrivalTime | exact |
| Trip | transfers | TRIP | numberOfTransfers | exact |
| Trip | distance | TRIP | distance | partial |
| Trip | origin | SCHEDULED STOP POINT / PLACE | id | exact |
| Trip | destination | SCHEDULED STOP POINT / PLACE | id | exact |
| Trip | legs | TRIP | legs (array of LEGs) | exact |
| Trip | operatingDays | OPERATING DAY | — | exact |
| Trip | tripStatus | — | — | none — OSDM real-time status extension |
| Trip | situationFullRefs | SITUATION | id | partial |

x-semantics equivalent:
- **concepts**: TRIP (Journey Planning)
- **conditions**: Covers a complete planned journey from origin to destination; maps to TRIP in Transmodel Journey Planning package

---

### TripLeg

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TripLeg | id | SERVICE JOURNEY / TRIP LEG | id | exact |
| TripLeg | timedLeg | TIMED LEG / SERVICE JOURNEY | — | exact |
| TripLeg | transferLeg | CONNECTION LEG / ACCESS LEG | — | partial |
| TripLeg | continuousLeg | ALTERNATIVE MODE LEG | — | partial |

x-semantics equivalent:
- **concepts**: SERVICE JOURNEY (Journey Planning), TIMED LEG, CONNECTION LEG
- **conditions**: Polymorphic container for a single leg of a Trip; discriminated by type into TIMED LEG, TRANSFER LEG or CONTINUOUS LEG

---

### TimedLeg

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TimedLeg | board | CALL / DEPARTURE | departureTime, stopPlaceRef | exact |
| TimedLeg | alight | CALL / ARRIVAL | arrivalTime, stopPlaceRef | exact |
| TimedLeg | intermediates | CALL | stopPlaceRef, arrivalTime, departureTime | exact |
| TimedLeg | service | SERVICE JOURNEY | id, serviceCode | exact |
| TimedLeg | legTrack | — | — | none — graphical track, no Transmodel equivalent |
| TimedLeg | legAttributes | SERVICE FACILITY | — | partial |

x-semantics equivalent:
- **concepts**: SERVICE JOURNEY (Journey Planning), CALL (Journey Planning)
- **conditions**: A leg operated by a public transport service; CALL objects at board/alight/intermediate stops correspond to CALL/DEPARTURE/ARRIVAL views in Transmodel

---

### Board / Alight

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Board | stopPlaceRef | SCHEDULED STOP POINT | id | exact |
| Board | stopPlaceName | SCHEDULED STOP POINT | name | exact |
| Board | plannedStopPointName | QUAY | name | partial |
| Board | serviceDeparture | DEPARTURE | departureTime (planned/estimated) | exact |
| Board | status | STOP CALL STATUS | — | partial |
| Alight | stopPlaceRef | SCHEDULED STOP POINT | id | exact |
| Alight | serviceArrival | ARRIVAL | arrivalTime (planned/estimated) | exact |

x-semantics equivalent:
- **concepts**: DEPARTURE (Journey Planning), ARRIVAL (Journey Planning), SCHEDULED STOP POINT (Fixed Objects in Public Transport)

---

### Intermediate / Intermediates

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Intermediate | stopPlaceRef | SCHEDULED STOP POINT | id | exact |
| Intermediate | serviceArrival | ARRIVAL | arrivalTime | exact |
| Intermediate | serviceDeparture | DEPARTURE | departureTime | exact |
| Intermediate | status | STOP CALL STATUS | — | partial |

x-semantics equivalent:
- **concepts**: CALL (Journey Planning), SCHEDULED STOP POINT
- **conditions**: Intermediate stops on a timed leg; each corresponds to a CALL in a VEHICLE JOURNEY

---

### DatedJourney

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| DatedJourney | journeyRef | DATED VEHICLE JOURNEY | id | exact |
| DatedJourney | serviceCode | SERVICE JOURNEY | serviceCode | exact |
| DatedJourney | mode | TRANSPORT MODE | — | exact |
| DatedJourney | operatingDays | OPERATING DAY | — | exact |
| DatedJourney | line | LINE | id, name | exact |
| DatedJourney | name | DESTINATION DISPLAY | — | partial |

x-semantics equivalent:
- **concepts**: DATED VEHICLE JOURNEY (Journey Planning), SERVICE JOURNEY
- **conditions**: A specific scheduled transport service operating on a given day; maps directly to DATED VEHICLE JOURNEY

---

### TransferLeg

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TransferLeg | mode | INDIVIDUAL MODE / ACCESS MODE | — | partial |
| TransferLeg | walkDuration | CONNECTION | walkDuration | partial |
| TransferLeg | startPlace | SCHEDULED STOP POINT | id | partial |
| TransferLeg | endPlace | SCHEDULED STOP POINT | id | partial |

x-semantics equivalent:
- **concepts**: CONNECTION LEG (Journey Planning), ACCESS LEG (Journey Planning)
- **conditions**: Walking or transfer leg between public transport services; maps to CONNECTION LEG or ACCESS LEG depending on context

---

### TripSearchCriteria

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TripSearchCriteria | origin | LOCATION CONTEXT | origin place | exact |
| TripSearchCriteria | destination | LOCATION CONTEXT | destination place | exact |
| TripSearchCriteria | travelDateTime | — | tripDateTime | exact |
| TripSearchCriteria | tripDataFilter | TRANSPORT MODE | ptMode | partial |
| TripSearchCriteria | tripMobilityFilter | CONVENTIONAL MODE TRAVEL PREFERENCES | — | partial |
| TripSearchCriteria | tripParameters | — | — | none — OSDM-specific result count/pagination |

x-semantics equivalent:
- **concepts**: TRIP REQUEST (Journey Planning, OJP)
- **conditions**: Parameters for requesting a trip search; maps to OJP TripRequest which Transmodel aligns with TRIP REQUEST

---

### TripSpecification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TripSpecification | board | SCHEDULED STOP POINT | id | exact |
| TripSpecification | alight | SCHEDULED STOP POINT | id | exact |
| TripSpecification | legs | SERVICE JOURNEY | — | partial |

x-semantics equivalent:
- **concepts**: TRIP (Journey Planning)
- **conditions**: Specifies a known trip by its constituent legs; used instead of a search when trip is already known

---

### TripCoverage

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TripCoverage | coveredTripId | TRIP | id | exact |
| TripCoverage | coveredLegIds | TRIP LEG | id | exact |
| TripCoverage | coveredSections | FARE STRUCTURE ELEMENT / SECTION | — | partial |

x-semantics equivalent:
- **concepts**: TRAVEL SPECIFICATION (Sales & Distribution), OFFERED TRAVEL SPECIFICATION
- **conditions**: Defines which portion of a trip an offer part or booking part covers; maps to TRAVEL SPECIFICATION

---

### TripLegCoverage

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TripLegCoverage | legId | TRIP / SERVICE JOURNEY | id | exact |
| TripLegCoverage | board | SCHEDULED STOP POINT | id | partial |
| TripLegCoverage | alight | SCHEDULED STOP POINT | id | partial |

x-semantics equivalent:
- **concepts**: TRAVEL SPECIFICATION (Sales & Distribution)
- **conditions**: Refined coverage down to boarding/alighting stop on a specific leg

---

### StopPlace / StopPlaceRef

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| StopPlace | id | SCHEDULED STOP POINT / STOP PLACE | id | exact |
| StopPlace | name | SCHEDULED STOP POINT / STOP PLACE | name | exact |
| StopPlaceRef | stopPlaceRef | SCHEDULED STOP POINT | id | exact |

x-semantics equivalent:
- **concepts**: SCHEDULED STOP POINT (Fixed Objects in Public Transport), STOP PLACE (Fixed Objects)
- **conditions**: OSDM uses the OJP `StopPlace` abstraction which encompasses both SCHEDULED STOP POINT and STOP PLACE

---

### Place / Address / GeoPosition

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Place | id | TOPOGRAPHIC PLACE / POINT OF INTEREST | id | partial |
| Place | geoPosition | POINT | geoPosition | exact |
| Address | name | TOPOGRAPHIC PLACE | name | partial |
| Address | countryCode | TOPOGRAPHIC PLACE | countryRef | partial |
| Address | postCode | TOPOGRAPHIC PLACE | — | partial |
| GeoPosition | longitude | POINT | longitude | exact |
| GeoPosition | latitude | POINT | latitude | exact |
| GeoPosition | altitude | POINT | altitude | exact |

x-semantics equivalent:
- **concepts**: TOPOGRAPHIC PLACE (Fixed Objects), POINT (Utility), STOP PLACE
- **conditions**: Generic geographic reference point; OSDM Place is a discriminated union (StopPlace, Address, PointOfInterest, FareConnectionPoint) — each variant maps to the nearest Transmodel concept

---

### ServiceTime

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ServiceTime | plannedTime | CALL PART | plannedTime | exact |
| ServiceTime | estimatedTime | CALL PART | estimatedTime | partial |
| ServiceTime | actualTime | CALL PART | actualTime | partial |

x-semantics equivalent:
- **concepts**: CALL PART (Journey Planning), DEPARTURE, ARRIVAL
- **conditions**: Encapsulates planned/estimated/actual times at a stop; corresponds to CALL PART in Transmodel

---

### OperatingDays

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OperatingDays | byDayOfWeek | DAY TYPE | daysOfWeek | exact |
| OperatingDays | byBitString | OPERATING DAY | bitSet | exact |
| OperatingDays | byDateRange | VALIDITY PERIOD | startDate, endDate | partial |

x-semantics equivalent:
- **concepts**: OPERATING DAY (Journey Planning), DAY TYPE (Journey Planning)

---

## 2. Offers

### Offer

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Offer | offerId | SALES OFFER PACKAGE | id | exact |
| Offer | createdOn | SALES OFFER PACKAGE | — | partial — creation timestamp |
| Offer | preBookableUntil | BOOKING POLICY | latestBookingTime | partial |
| Offer | passengerRefs | TRANSPORT CUSTOMER | id | exact |
| Offer | admissionOfferParts | SALES OFFER PACKAGE ELEMENT (admission) | — | exact |
| Offer | reservationOfferParts | SALES OFFER PACKAGE ELEMENT (reservation) | — | exact |
| Offer | ancillaryOfferParts | SALES OFFER PACKAGE ELEMENT (ancillary) | — | exact |
| Offer | continuousServiceOfferParts | SALES OFFER PACKAGE ELEMENT | — | partial |
| Offer | fees | FARE PRICE | — | partial |
| Offer | fares | FARE PRODUCT / PRICE GROUP | — | partial |
| Offer | products | FARE PRODUCT | id | exact |
| Offer | tripCoverage | TRAVEL SPECIFICATION | — | exact |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Fare Structure)
- **conditions**: A priced and validity-constrained package of travel rights presented to the customer; maps to SALES OFFER PACKAGE

---

### OfferCollectionRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OfferCollectionRequest | tripIds | TRIP | id | exact |
| OfferCollectionRequest | tripSpecifications | TRIP | — | exact |
| OfferCollectionRequest | tripSearchCriteria | TRIP REQUEST | — | partial |
| OfferCollectionRequest | anonymousPassengerSpecifications | TRANSPORT CUSTOMER / USER PROFILE | — | partial |
| OfferCollectionRequest | offerSearchCriteria | SALES OFFER PACKAGE | — | partial |
| OfferCollectionRequest | corporateCodes | COMMERCIAL PROFILE | — | partial |
| OfferCollectionRequest | promotionCodes | SALE DISCOUNT RIGHT | — | partial |

x-semantics equivalent:
- **concepts**: FARE REQUEST (Sales & Distribution, OJP)
- **conditions**: Parameters to request a set of SALES OFFER PACKAGEs for a given trip and passenger specification

---

### OfferCollectionResponse

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OfferCollectionResponse | offers | SALES OFFER PACKAGE | — | exact |
| OfferCollectionResponse | trips | TRIP | — | exact |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Fare Structure), TRIP (Journey Planning)

---

### OfferSearchCriteria

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OfferSearchCriteria | requestedOfferParts | SALES OFFER PACKAGE ELEMENT | objectType | partial |
| OfferSearchCriteria | productTags | FARE PRODUCT / PRODUCT TAG | — | partial |
| OfferSearchCriteria | flexibilities | RESELLING | flexibility | partial |
| OfferSearchCriteria | travelClasses | TRAVEL CLASS | — | exact |
| OfferSearchCriteria | serviceClasses | SERVICE CLASS | — | exact |

x-semantics equivalent:
- **concepts**: FARE STRUCTURE ELEMENT (Fare Structure), SALES OFFER PACKAGE
- **conditions**: Filters for narrowing offer search results by product type, class, flexibility

---

### OfferSummary

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OfferSummary | totalPrice | FARE PRICE / CUSTOMER PURCHASE PACKAGE PRICE | amount | exact |
| OfferSummary | flexibility | RESELLING | — | partial |
| OfferSummary | travelClass | TRAVEL CLASS | — | exact |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Fare Structure)

---

### OfferOverviewTrip / OfferOverviewRoute

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OfferOverviewTrip | price | FARE PRICE | amount | exact |
| OfferOverviewTrip | travelClass | TRAVEL CLASS | — | exact |
| OfferOverviewTrip | flexibility | RESELLING | — | partial |
| OfferOverviewRoute | routeSpecification | FARE STRUCTURE ELEMENT / ROUTE | — | partial |
| OfferOverviewRoute | startDateTime | VALIDITY PERIOD | startDate | partial |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Fare Structure), FARE PRICE
- **conditions**: Lightweight price overview without full offer details; partial equivalent to a simplified SALES OFFER PACKAGE price query

---

### OnHoldOffer

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OnHoldOffer | id | SALES OFFER PACKAGE | id | partial |
| OnHoldOffer | validUntil | BOOKING POLICY | latestBookingTime | partial |
| OnHoldOffer | status | CUSTOMER PURCHASE STATUS | — | partial |

x-semantics equivalent:
- **concepts**: BOOKING POLICY (Fare Structure), CUSTOMER PURCHASE PACKAGE
- **conditions**: A pre-booking temporarily held in reservation; corresponds to an on-hold CUSTOMER PURCHASE PACKAGE

---

## 3. Offer Parts

### AbstractOfferPart (base of AdmissionOfferPart, ReservationOfferPart, AncillaryOfferPart, ContinuousServiceOfferPart)

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AbstractOfferPart | id | SALES OFFER PACKAGE ELEMENT | id | exact |
| AbstractOfferPart | validFrom | USAGE VALIDITY PERIOD | startDate | exact |
| AbstractOfferPart | validUntil | USAGE VALIDITY PERIOD | endDate | exact |
| AbstractOfferPart | price | FARE PRICE | amount | exact |
| AbstractOfferPart | products | FARE PRODUCT | — | exact |
| AbstractOfferPart | offerMode | — | — | none — OSDM-specific offer/fare mode distinction |
| AbstractOfferPart | passengerRefs | TRANSPORT CUSTOMER | id | exact |
| AbstractOfferPart | refundable | REFUNDING | — | exact |
| AbstractOfferPart | exchangeable | EXCHANGING | — | exact |
| AbstractOfferPart | afterSalesConditions | RESELLING / REFUNDING / EXCHANGING | — | exact |
| AbstractOfferPart | appliedCorporateCodes | COMMERCIAL PROFILE | — | partial |
| AbstractOfferPart | appliedPassengerTypes | USER PROFILE / TYPE OF CONCESSION | — | exact |
| AbstractOfferPart | appliedPromotionCodes | SALE DISCOUNT RIGHT | — | exact |
| AbstractOfferPart | availableFulfillmentOptions | FULFILMENT METHOD | — | exact |
| AbstractOfferPart | regionalValiditySummary | REGIONAL VALIDITY | — | exact |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE ELEMENT (Fare Structure)
- **conditions**: A single component of a SALES OFFER PACKAGE (admission, reservation, or ancillary service right)

---

### AdmissionOfferPart

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AdmissionOfferPart | regionalValidities | FARE STRUCTURE ELEMENT / REGIONAL VALIDITY | — | exact |
| AdmissionOfferPart | reservations | RESERVATION (linked) | — | partial |
| AdmissionOfferPart | ancillaries | ANCILLARY (linked) | — | partial |
| AdmissionOfferPart | throughTicketTags | — | — | none — OSDM-specific through-ticketing extension |

x-semantics equivalent:
- **concepts**: PREASSIGNED FARE PRODUCT (Fare Structure) — specifically the admission / travel right element
- **conditions**: Represents the entitlement to travel onboard (no seat); maps to the admission-type FARE PRODUCT element within a SALES OFFER PACKAGE ELEMENT

---

### ReservationOfferPart

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ReservationOfferPart | placeAllocations | SPOT / VEHICLE OCCUPANCY | — | partial |
| ReservationOfferPart | availablePlaces | VEHICLE OCCUPANCY / SPOT AVAILABILITY | — | partial |
| ReservationOfferPart | reservedPlaces | SPOT | placeNumber | partial |
| ReservationOfferPart | isIncluded | — | — | none — indicates whether reservation is included in admission price |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE ELEMENT (reservation type), CUSTOMER SPOT AVAILABILITY EVENT
- **conditions**: Seat or berth reservation element of an offer; corresponds to a VEHICLE OCCUPANCY or SPOT reservation within a SALES OFFER PACKAGE ELEMENT

---

### AncillaryOfferPart

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AncillaryOfferPart | type | SUPPLEMENTARY PRODUCT | — | partial |
| AncillaryOfferPart | category | SUPPLEMENTARY PRODUCT | category | partial |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE ELEMENT (ancillary), SUPPLEMENTARY PRODUCT
- **conditions**: Supplementary service (luggage, meal, Wi-Fi) bundled with or sold alongside a journey right

---

### AfterSaleCondition

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AfterSaleCondition | condition | RESELLING / REFUNDING / EXCHANGING | — | exact |
| AfterSaleCondition | validFrom | USAGE VALIDITY PERIOD | startDate | partial |
| AfterSaleCondition | validUntil | USAGE VALIDITY PERIOD | endDate | partial |
| AfterSaleCondition | afterSaleFee | FARE PRICE | amount | partial |
| AfterSaleCondition | reimbursementMethod | — | — | partial — OSDM-specific reimbursement channel |

x-semantics equivalent:
- **concepts**: RESELLING (Fare Structure), REFUNDING (Fare Structure), EXCHANGING (Fare Structure)
- **conditions**: Specifies after-sale conditions (refund penalty, exchange window, place-change fee); maps to RESELLING/REFUNDING/EXCHANGING usage parameters

---

## 4. Booking & Booking Parts

### Booking

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Booking | id | CUSTOMER PURCHASE PACKAGE | id | exact |
| Booking | bookingCode | FARE CONTRACT | externalCode | partial |
| Booking | externalRef | FARE CONTRACT | externalRef | partial |
| Booking | createdOn | CUSTOMER PURCHASE PACKAGE | purchaseDate | exact |
| Booking | passengers | INDIVIDUAL TRAVELLER | — | exact |
| Booking | purchaser | TRANSPORT CUSTOMER (purchaser role) | — | exact |
| Booking | bookedOffers | SALES OFFER PACKAGE | — | exact |
| Booking | trips | TRIP | — | exact |
| Booking | provisionalPrice | FARE PRICE | amount | partial |
| Booking | confirmedPrice | CUSTOMER PURCHASE PACKAGE PRICE | amount | exact |
| Booking | confirmationTimeLimit | BOOKING POLICY | latestConfirmationTime | partial |
| Booking | fulfillmentType | FULFILMENT METHOD | — | exact |
| Booking | fulfillments | TRAVEL DOCUMENT | — | exact |
| Booking | refundOffers | CUSTOMER REFUND EVENT | — | partial |
| Booking | exchangeOperations | CUSTOMER EXCHANGE EVENT | — | partial |
| Booking | paymentMethods | CUSTOMER PAYMENT MEANS | — | partial |
| Booking | relatedBookingIds | FARE CONTRACT | — | none — split booking OSDM extension |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Sales & Distribution), FARE CONTRACT (Sales & Distribution)
- **conditions**: A confirmed purchase of one or more SALES OFFER PACKAGEs; maps to CUSTOMER PURCHASE PACKAGE (pre-paid model) or FARE CONTRACT (post-paid model)

---

### BookedOffer

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookedOffer | offerId | CUSTOMER PURCHASE PACKAGE | id | exact |
| BookedOffer | admissions | CUSTOMER PURCHASE PACKAGE ELEMENT (admission) | — | exact |
| BookedOffer | reservations | CUSTOMER PURCHASE PACKAGE ELEMENT (reservation) | — | exact |
| BookedOffer | ancillaries | CUSTOMER PURCHASE PACKAGE ELEMENT (ancillary) | — | exact |
| BookedOffer | fares | FARE PRODUCT | — | partial |
| BookedOffer | tripCoverage | TRAVEL SPECIFICATION | — | exact |
| BookedOffer | products | FARE PRODUCT | — | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE ELEMENT (Sales & Distribution)
- **conditions**: The booked realisation of an OFFER within a BOOKING

---

### AbstractBookingPart (base of Admission, Reservation, Ancillary, ContinuousServiceBookingPart)

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AbstractBookingPart | id | CUSTOMER PURCHASE PACKAGE ELEMENT | id | exact |
| AbstractBookingPart | createdOn | CUSTOMER PURCHASE PACKAGE ELEMENT | creationDate | exact |
| AbstractBookingPart | validFrom | USAGE VALIDITY PERIOD | startDate | exact |
| AbstractBookingPart | validUntil | USAGE VALIDITY PERIOD | endDate | exact |
| AbstractBookingPart | price | CUSTOMER PURCHASE PACKAGE PRICE | amount | exact |
| AbstractBookingPart | refundAmount | FARE PRICE | refundAmount | partial |
| AbstractBookingPart | tripCoverage | TRAVEL SPECIFICATION | — | exact |
| AbstractBookingPart | status | CUSTOMER PURCHASE STATUS | — | exact |
| AbstractBookingPart | pricingStatus | CHARGING MOMENT | — | partial |
| AbstractBookingPart | refundable | REFUNDING | — | exact |
| AbstractBookingPart | exchangeable | EXCHANGING | — | exact |
| AbstractBookingPart | afterSaleConditions | RESELLING / REFUNDING / EXCHANGING | — | exact |
| AbstractBookingPart | passengerIds | TRANSPORT CUSTOMER | id | exact |
| AbstractBookingPart | appliedCorporateCodes | COMMERCIAL PROFILE | — | partial |
| AbstractBookingPart | appliedPassengerTypes | USER PROFILE / TYPE OF CONCESSION | — | exact |
| AbstractBookingPart | appliedPromotionCodes | SALE DISCOUNT RIGHT | — | exact |
| AbstractBookingPart | summaryProductId | FARE PRODUCT | id | exact |
| AbstractBookingPart | products | SALES OFFER PACKAGE ELEMENT | — | exact |
| AbstractBookingPart | availableFulfillmentOptions | FULFILMENT METHOD | — | exact |
| AbstractBookingPart | confirmedFulfillmentOptions | FULFILMENT METHOD | — | exact |
| AbstractBookingPart | bookingPartCode | CUSTOMER PURCHASE PACKAGE ELEMENT | externalCode | partial |
| AbstractBookingPart | distributorBookingRef | FARE CONTRACT | id (distributor) | partial |
| AbstractBookingPart | retailerBookingRef | FARE CONTRACT | id (retailer) | partial |
| AbstractBookingPart | grantedReductionAmounts | SALE DISCOUNT RIGHT / FARE PRICE | — | partial |
| AbstractBookingPart | accountingRef | — | — | none — OSDM-specific billing integration |
| AbstractBookingPart | indicatedConsumption | CUSTOMER PURCHASE STATUS | — | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE ELEMENT (Sales & Distribution)
- **conditions**: A confirmed booking element (admission, reservation, ancillary, or continuous service); corresponds to one CUSTOMER PURCHASE PACKAGE ELEMENT within a CUSTOMER PURCHASE PACKAGE

---

### BookingPartStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookingPartStatus | PREBOOKED | CUSTOMER PURCHASE STATUS | — | partial |
| BookingPartStatus | CONFIRMED | CUSTOMER PURCHASE STATUS | — | exact |
| BookingPartStatus | FULFILLED | CUSTOMER PURCHASE STATUS | — | exact |
| BookingPartStatus | CANCELLED | CUSTOMER PURCHASE STATUS | — | exact |
| BookingPartStatus | REFUNDED | CUSTOMER PURCHASE STATUS | — | exact |
| BookingPartStatus | EXCHANGE_ONGOING | CUSTOMER PURCHASE STATUS | — | partial |
| BookingPartStatus | EXCHANGED | CUSTOMER PURCHASE STATUS | — | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE STATUS (Sales & Distribution)

---

### BookingRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookingRequest | offers | SALES OFFER PACKAGE | id | exact |
| BookingRequest | passengerSpecifications | TRANSPORT CUSTOMER | — | exact |
| BookingRequest | purchaser | TRANSPORT CUSTOMER (purchaser role) | — | exact |
| BookingRequest | promotionCodes | SALE DISCOUNT RIGHT | — | partial |
| BookingRequest | requestedFulfillmentType | FULFILMENT METHOD | — | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER PRODUCT PURCHASE EVENT (Sales & Distribution)
- **conditions**: Input for creating a CUSTOMER PURCHASE PACKAGE from selected SALES OFFER PACKAGEs

---

### BookingSplit / BookingSplitRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookingSplit | bookingId | CUSTOMER PURCHASE PACKAGE | id | partial |
| BookingSplit | passengerIds | TRANSPORT CUSTOMER | id | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Sales & Distribution)
- **conditions**: Splitting one booking into multiple; no direct Transmodel equivalent — OSDM operational extension

---

## 5. Passengers & Purchaser

### Passenger

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Passenger | id | TRANSPORT CUSTOMER | id | exact |
| Passenger | externalRef | TRANSPORT CUSTOMER | externalRef | partial |
| Passenger | type | USER PROFILE / PASSENGER TYPE | type | exact |
| Passenger | dateOfBirth | TRANSPORT CUSTOMER | dateOfBirth | exact |
| Passenger | age | USER PROFILE | age | partial |
| Passenger | gender | TRANSPORT CUSTOMER | gender | exact |
| Passenger | detail | TRANSPORT CUSTOMER | name, address, contact | exact |
| Passenger | identificationCard | TRAVEL DOCUMENT (ID) | — | partial |
| Passenger | cards | ENTITLEMENT PRODUCT / REDUCTION CARD | — | exact |
| Passenger | prmNeeds | PRM ELIGIBILITY | — | partial |
| Passenger | transportableDetails | — | — | partial — non-person transportables (bike, pet, car) |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Sales & Distribution)
- **conditions**: Individual person (or non-person item) for whom a booking part is purchased; maps to TRANSPORT CUSTOMER

---

### AnonymousPassengerSpecification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AnonymousPassengerSpecification | externalRef | TRANSPORT CUSTOMER | externalRef | partial |
| AnonymousPassengerSpecification | type | USER PROFILE | type | exact |
| AnonymousPassengerSpecification | dateOfBirth | TRANSPORT CUSTOMER | dateOfBirth | partial |
| AnonymousPassengerSpecification | cards | ENTITLEMENT PRODUCT | — | partial |
| AnonymousPassengerSpecification | residency | CUSTOMER ELIGIBILITY | country | partial |

x-semantics equivalent:
- **concepts**: USER PROFILE (Fare Structure), TRANSPORT CUSTOMER
- **conditions**: Minimal passenger data sufficient for offer pricing without GDPR-sensitive personal data

---

### PassengerSpecification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PassengerSpecification | externalRef | TRANSPORT CUSTOMER | externalRef | partial |
| PassengerSpecification | type | USER PROFILE | type | exact |
| PassengerSpecification | cards | ENTITLEMENT PRODUCT | — | partial |
| PassengerSpecification | detail | TRANSPORT CUSTOMER | name, contact | exact |
| PassengerSpecification | gender | TRANSPORT CUSTOMER | gender | exact |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Sales & Distribution), USER PROFILE (Fare Structure)
- **conditions**: Full passenger specification used at booking time

---

### PassengerType / ActualPassengerType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PassengerType | ADULT / YOUTH / CHILD / … | USER PROFILE / TYPE OF CONCESSION | type | exact |
| PassengerType | DOG / PET / BICYCLE / CAR | — | — | none — non-person transportables have no Transmodel USER PROFILE equivalent |

x-semantics equivalent:
- **concepts**: USER PROFILE (Fare Structure), TYPE OF CONCESSION (Fare Structure)

---

### PersonDetail

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PersonDetail | firstName | TRANSPORT CUSTOMER | firstName | exact |
| PersonDetail | lastName | TRANSPORT CUSTOMER | lastName | exact |
| PersonDetail | email | TRANSPORT CUSTOMER | email | exact |
| PersonDetail | phoneNumber | TRANSPORT CUSTOMER | contactDetails | exact |
| PersonDetail | address | TRANSPORT CUSTOMER | address | exact |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Sales & Distribution)

---

### Purchaser / PurchaserSpecification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Purchaser | id | TRANSPORT CUSTOMER (CORPORATE PURCHASER ROLE) | id | exact |
| Purchaser | detail | TRANSPORT CUSTOMER | name, contact | exact |
| PurchaserSpecification | externalRef | TRANSPORT CUSTOMER | externalRef | partial |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Sales & Distribution), CORPORATE PURCHASER ROLE (Sales & Distribution)
- **conditions**: The entity making the purchase, which may differ from the travelling passenger; maps to CORPORATE PURCHASER ROLE or TRANSPORT CUSTOMER with purchaser role

---

### IdentificationCard

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| IdentificationCard | type | TRAVEL DOCUMENT | type | partial |
| IdentificationCard | number | TRAVEL DOCUMENT | documentNumber | partial |
| IdentificationCard | countryOfIssue | TRANSPORT CUSTOMER | nationality | partial |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Sales & Distribution)
- **conditions**: Government-issued ID for identity verification; partial match — Transmodel TRAVEL DOCUMENT covers transport-specific documents

---

### PassengerCategory

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PassengerCategory | title | USER PROFILE | name | exact |
| PassengerCategory | specification | USER PROFILE / ANONYMOUS PASSENGER SPEC | — | exact |

x-semantics equivalent:
- **concepts**: USER PROFILE (Fare Structure)
- **conditions**: Pre-defined passenger type with associated offer specification; maps to a named USER PROFILE

---

## 6. Fulfillments & Documents

### Fulfillment

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Fulfillment | id | TRAVEL DOCUMENT | id | exact |
| Fulfillment | status | TRAVEL DOCUMENT | status | exact |
| Fulfillment | bookingRef | CUSTOMER PURCHASE PACKAGE | id | exact |
| Fulfillment | controlNumber | TRAVEL DOCUMENT | controlNumber | exact |
| Fulfillment | bookingParts | CUSTOMER PURCHASE PACKAGE ELEMENT | id | exact |
| Fulfillment | issuer | TRANSPORT OPERATOR (issuer role) | id | exact |
| Fulfillment | fulfillmentDocuments | TRAVEL DOCUMENT | content | exact |
| Fulfillment | fulfillmentParts | TRAVEL DOCUMENT (distributor parts) | — | partial |
| Fulfillment | tripCoverage | TRAVEL SPECIFICATION | — | exact |
| Fulfillment | availableUsage | CUSTOMER PURCHASE STATUS | — | partial |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Sales & Distribution), CUSTOMER FULFILMENT EVENT
- **conditions**: Materialisation of purchased travel rights as a document (ticket, pass, barcode); maps to TRAVEL DOCUMENT

---

### FulfillmentStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FulfillmentStatus | CREATED | TRAVEL DOCUMENT | status=issued | exact |
| FulfillmentStatus | USED | TRAVEL DOCUMENT | status=consumed | exact |
| FulfillmentStatus | ARCHIVED | TRAVEL DOCUMENT | status=archived | partial |
| FulfillmentStatus | CANCELLED | TRAVEL DOCUMENT | status=cancelled | exact |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Sales & Distribution)

---

### FulfillmentDocument

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FulfillmentDocument | medium | FULFILMENT METHOD | mediaType | exact |
| FulfillmentDocument | type | TRAVEL DOCUMENT | documentType | exact |
| FulfillmentDocument | format | TRAVEL DOCUMENT | format | partial |
| FulfillmentDocument | downloadLink | TRAVEL DOCUMENT | downloadUrl | partial |
| FulfillmentDocument | content | TRAVEL DOCUMENT | binaryContent | exact |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Sales & Distribution), FULFILMENT METHOD
- **conditions**: The binary/downloadable representation of a TRAVEL DOCUMENT in a specific media format

---

### FulfillmentMediaType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FulfillmentMediaType | RETAILER_APP | FULFILMENT METHOD | mediaType=mobileApp | exact |
| FulfillmentMediaType | UIC_PDF | FULFILMENT METHOD | mediaType=PDF | exact |
| FulfillmentMediaType | PRINT_AT_HOME | FULFILMENT METHOD | mediaType=printAtHome | exact |
| FulfillmentMediaType | PAPER_TICKET | FULFILMENT METHOD | mediaType=paper | exact |
| FulfillmentMediaType | FULFILLMENT_PARTS | FULFILMENT METHOD | mediaType=parts | partial |

x-semantics equivalent:
- **concepts**: FULFILMENT METHOD (Sales & Distribution)
- **conditions**: Delivery channel for the TRAVEL DOCUMENT; each value maps to a FULFILMENT METHOD media type

---

### FulfillmentOption

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FulfillmentOption | type | FULFILMENT METHOD | type | exact |
| FulfillmentOption | constraint | FULFILMENT METHOD | constraints | partial |

x-semantics equivalent:
- **concepts**: FULFILMENT METHOD (Sales & Distribution)

---

### FulfillmentConstraint

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FulfillmentConstraint | acceptedControlSecurityTypes | CONTROL MEANS | — | partial |
| FulfillmentConstraint | acceptedBarCodes | TRAVEL DOCUMENT | barCodeType | partial |
| FulfillmentConstraint | requiredBarCodes | TRAVEL DOCUMENT | barCodeType | partial |

x-semantics equivalent:
- **concepts**: CONTROL MEANS (Fare Collection), TRAVEL DOCUMENT
- **conditions**: Technical constraints on acceptable security/barcode types for a ticket; partially maps to CONTROL MEANS parameters

---

### FulfillmentPart

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FulfillmentPart | id | TRAVEL DOCUMENT (sub-document) | id | partial |
| FulfillmentPart | referenceToFare | FARE PRODUCT | id | partial |
| FulfillmentPart | securityFeatures | CONTROL MEANS | securityData | partial |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Sales & Distribution)
- **conditions**: Distributor-mode partial fulfillment item for integration into a carrier-issued ticket; partial Transmodel equivalent

---

### Document

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Document | id | TRAVEL DOCUMENT / BOOKING DOCUMENT | id | partial |
| Document | type | TRAVEL DOCUMENT | documentType | partial |
| Document | format | TRAVEL DOCUMENT | format | partial |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Sales & Distribution)
- **conditions**: Booking-level document (e.g. booking receipt, invoice); maps to TRAVEL DOCUMENT of document type

---

## 7. Refund, Exchange & Release

### RefundOffer

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| RefundOffer | id | CUSTOMER REFUND EVENT | id | partial |
| RefundOffer | status | CUSTOMER PURCHASE STATUS | — | partial |
| RefundOffer | fulfillments | TRAVEL DOCUMENT | — | exact |
| RefundOffer | refundFee | FARE PRICE (penalty) | amount | partial |
| RefundOffer | refundableAmount | FARE PRICE | refundAmount | exact |
| RefundOffer | validUntil | USAGE VALIDITY PERIOD | endDate | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER REFUND EVENT (Sales & Distribution), REFUNDING (Fare Structure)
- **conditions**: A proposed refund with amount and conditions; corresponds to a CUSTOMER REFUND EVENT with associated REFUNDING usage parameter

---

### RefundStatus / RefundType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| RefundStatus | PROPOSED | CUSTOMER PURCHASE STATUS | — | partial |
| RefundStatus | CONFIRMED | CUSTOMER PURCHASE STATUS | — | exact |
| RefundType | REFUNDABLE | REFUNDING | — | exact |
| RefundType | NON_REFUNDABLE | REFUNDING | — | exact |
| RefundType | PARTIAL | REFUNDING | — | exact |

x-semantics equivalent:
- **concepts**: REFUNDING (Fare Structure), CUSTOMER PURCHASE STATUS

---

### ExchangeOperation / ExchangeOffer

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ExchangeOperation | id | CUSTOMER EXCHANGE EVENT | id | partial |
| ExchangeOperation | status | EXCHANGE STATUS | — | partial |
| ExchangeOperation | originalBookingParts | CUSTOMER PURCHASE PACKAGE ELEMENT | id | partial |
| ExchangeOffer | newOffer | SALES OFFER PACKAGE | — | partial |
| ExchangeOffer | exchangeFee | FARE PRICE | amount | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER EXCHANGE EVENT (Sales & Distribution), EXCHANGING (Fare Structure)
- **conditions**: An exchange of a booked right for a new right; maps to CUSTOMER EXCHANGE EVENT triggered by EXCHANGING usage parameter

---

### ReleaseOffer

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ReleaseOffer | id | — | — | none — two-step refund (release + refund) specific to OSDM |
| ReleaseOffer | status | CUSTOMER PURCHASE STATUS | — | partial |
| ReleaseOffer | fulfillments | TRAVEL DOCUMENT | — | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER REVOKE FULFILMENT EVENT (Sales & Distribution)
- **conditions**: First step of a two-step refund process (mark release date, invalidate ticket); partially maps to CUSTOMER REVOKE FULFILMENT EVENT

---

## 8. Reimbursement & Complaints

### Reimbursement / ReimbursementRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Reimbursement | id | — | — | none — passenger rights reimbursement not in Transmodel |
| Reimbursement | status | — | — | none |
| Reimbursement | reason | — | — | none |
| Reimbursement | method | — | — | none |
| ReimbursementRequest | bookingRef | CUSTOMER PURCHASE PACKAGE | id | partial |
| ReimbursementRequest | fulfillmentIds | TRAVEL DOCUMENT | id | partial |
| ReimbursementRequest | refundAmount | FARE PRICE | amount | partial |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent — reimbursement under passenger rights regulation (EU 1371/2007 and related) is an OSDM domain extension beyond Transmodel fare scope

---

### Complaint / CustomerComplaint / ComplaintDecision

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Complaint | id | — | — | none |
| Complaint | bookingRef | CUSTOMER PURCHASE PACKAGE | id | partial |
| Complaint | status | — | — | none |
| CustomerComplaint | subject | — | — | none |
| CustomerComplaint | description | — | — | none |
| ComplaintDecision | status | — | — | none |
| ComplaintDecision | reason | — | — | none |

x-semantics equivalent:
- **concepts**: COMPLAINTS SERVICE (Fixed Objects — Local Service) — very partial
- **conditions**: Complaint management workflow; Transmodel only models complaints service endpoints (local service), not the complaint lifecycle itself

---

## 9. Place / Seat Availability & Coach Layout

### PlaceAvailability

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PlaceAvailability | vehicle | VEHICLE | — | exact |
| PlaceAvailability | preSelections | SPOT (pre-selected) | — | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER SPOT AVAILABILITY EVENT (Sales & Distribution), VEHICLE OCCUPANCY
- **conditions**: Returns availability map of physical places on a vehicle; maps to VEHICLE OCCUPANCY / CUSTOMER SPOT AVAILABILITY

---

### AvailablePlace

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AvailablePlace | accommodationType | ACCOMMODATION | type | exact |
| AvailablePlace | accommodationSubType | ACCOMMODATION | subType | exact |
| AvailablePlace | numericAvailability | VEHICLE OCCUPANCY | remainingCapacity | exact |
| AvailablePlace | tripLegCoverage | TRAVEL SPECIFICATION | — | partial |

x-semantics equivalent:
- **concepts**: VEHICLE OCCUPANCY (Journey Planning), ACCOMMODATION (Vehicle Equipment)

---

### PlaceAllocation / ReservedPlace / SelectedPlace

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PlaceAllocation | accommodationType | ACCOMMODATION | type | exact |
| PlaceAllocation | reservedPlaces | SPOT | placeNumber | exact |
| PlaceAllocation | tripLegCoverage | TRAVEL SPECIFICATION | — | partial |
| ReservedPlace | placeNumber | SPOT | placeNumber | exact |
| ReservedPlace | coachNumber | VEHICLE / TRAIN COMPONENT | coachNumber | exact |
| SelectedPlace | coachNumber | TRAIN COMPONENT | coachNumber | exact |
| SelectedPlace | placeNumber | SPOT | placeNumber | exact |

x-semantics equivalent:
- **concepts**: SPOT (Vehicle Equipment), VEHICLE / TRAIN COMPONENT (Vehicle)
- **conditions**: A specific physical seat/berth/bike hook assigned to a booking; maps to SPOT within a VEHICLE COMPONENT

---

### PlaceSelection / PlacePreSelection

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PlaceSelection | placeNumber | SPOT | placeNumber | exact |
| PlaceSelection | coach | TRAIN COMPONENT | coachNumber | exact |
| PlacePreSelection | coach | TRAIN COMPONENT | coachNumber | exact |
| PlacePreSelection | place | SPOT | placeNumber | exact |

x-semantics equivalent:
- **concepts**: SPOT (Vehicle Equipment), CUSTOMER SPOT AVAILABILITY EVENT

---

### Vehicle / VehicleAvailability

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Vehicle | id | VEHICLE | id | exact |
| Vehicle | coaches | TRAIN COMPONENT | — | exact |
| VehicleAvailability | trainNumber | VEHICLE JOURNEY | trainNumber | exact |
| VehicleAvailability | coaches | TRAIN COMPONENT | — | exact |

x-semantics equivalent:
- **concepts**: VEHICLE (Vehicle), TRAIN COMPONENT (Vehicle)
- **conditions**: Physical train or vehicle with its coaches/cars; maps to VEHICLE and its TRAIN COMPONENT parts

---

### CoachDeck / CoachLayout / PlaceGroup / PlaceLayout / Compartment

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| CoachDeck | id | DECK / TRAIN COMPONENT PART | id | partial |
| CoachLayout | id | TRAIN COMPONENT | coachNumber | partial |
| CoachLayout | coachDeckLayout | DECK | — | partial |
| PlaceGroup | serviceClass | SERVICE CLASS | — | exact |
| PlaceGroup | accommodationType | ACCOMMODATION | type | exact |
| PlaceGroup | places | SPOT | — | exact |
| PlaceLayout | number | SPOT | placeNumber | exact |
| PlaceLayout | rectangle | SPOT (graphical) | coordinates | none — graphical layout |
| Compartment | id | TRAIN COMPONENT PART | id | partial |
| Compartment | compartmentNumber | TRAIN COMPONENT PART | number | partial |

x-semantics equivalent:
- **concepts**: VEHICLE / TRAIN COMPONENT (Vehicle), SPOT (Vehicle), DECK (Vehicle)
- **conditions**: Physical layout of a train coach; OSDM provides graphical rendering data not present in Transmodel

---

## 10. Fares & Regional Validity

### Fare

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Fare | id | FARE PRODUCT / PRICE GROUP | id | exact |
| Fare | type | FARE PRODUCT | type | exact |
| Fare | prices | FARE PRICE | amount | exact |
| Fare | regionalConstraint | FARE STRUCTURE ELEMENT / GEOGRAPHICAL INTERVAL | — | exact |
| Fare | serviceConstraint | FARE STRUCTURE ELEMENT | serviceConstraint | partial |
| Fare | carrierConstraint | OPERATOR | — | partial |
| Fare | travelClass | TRAVEL CLASS | — | exact |
| Fare | serviceClass | SERVICE CLASS | — | exact |
| Fare | afterSalesCondition | RESELLING / REFUNDING / EXCHANGING | — | exact |
| Fare | combinationConstraint | FARE COMBINATION CONSTRAINT | — | exact |
| Fare | travelValidityConstraint | USAGE VALIDITY PERIOD | — | exact |
| Fare | passengerRefs | TRANSPORT CUSTOMER | id | exact |
| Fare | passengerConstraints | USER PROFILE | — | partial |
| Fare | luggageConstraint | — | — | none — luggage restrictions are OSDM-specific |
| Fare | fulfillmentConstraint | FULFILMENT METHOD | constraints | partial |
| Fare | legacyAccountingIdentifier | — | — | none — UIC legacy billing identifier |

x-semantics equivalent:
- **concepts**: FARE PRODUCT (Fare Structure), PRICE GROUP (Fare Structure)
- **conditions**: Distributor-mode online fare representation; maps to FARE PRODUCT with associated FARE PRICE and usage parameters

---

### FareType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FareType | ADMISSION | PREASSIGNED FARE PRODUCT | — | exact |
| FareType | RESERVATION | SUPPLEMENTARY FARE PRODUCT | — | partial |
| FareType | ANCILLARY | SUPPLEMENTARY FARE PRODUCT | — | partial |

x-semantics equivalent:
- **concepts**: FARE PRODUCT (Fare Structure)

---

### FareCombinationModel

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FareCombinationModel | model | FARE COMBINATION CONSTRAINT | combinationModel | exact |
| FareCombinationModel | combinableCarriers | OPERATOR | — | partial |
| FareCombinationModel | referenceCluster | FARE COMBINATION CONSTRAINT | clusterRef | partial |
| FareCombinationModel | allowedClusters | FARE COMBINATION CONSTRAINT | allowedClusters | partial |

x-semantics equivalent:
- **concepts**: FARE COMBINATION CONSTRAINT (Fare Structure)
- **conditions**: Rules for combining fares from multiple carriers; maps to FARE COMBINATION CONSTRAINT

---

### FareConnectionPoint / FareConnectionPointRef

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FareConnectionPoint | stationSets | BORDER POINT | — | exact |
| FareConnectionPoint | name | BORDER POINT | name | exact |

x-semantics equivalent:
- **concepts**: BORDER POINT (Fare Structure)
- **conditions**: A virtual point connecting two fare regimes at a border; maps to BORDER POINT in Transmodel fare structure

---

### RegionalConstraint / RegionalValidity

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| RegionalConstraint | entryConnectionPoints | BORDER POINT | — | exact |
| RegionalConstraint | exitConnectionPoints | BORDER POINT | — | exact |
| RegionalConstraint | viaConnectionPoints | BORDER POINT | — | partial |
| RegionalConstraint | routes | FARE STRUCTURE ELEMENT / ROUTE | — | partial |
| RegionalValidity | viaStations | FARE ZONE / ROUTE | — | partial |
| RegionalValidity | trainLink | FARE STRUCTURE ELEMENT | trainLink | partial |
| RegionalValidity | zones | FARE ZONE | — | exact |
| RegionalValidity | line | LINE | id | exact |

x-semantics equivalent:
- **concepts**: FARE STRUCTURE ELEMENT (Fare Structure), GEOGRAPHICAL INTERVAL, FARE ZONE
- **conditions**: Geographic scope of a fare; maps to a GEOGRAPHICAL INTERVAL or combination of BORDER POINTs and FARE ZONEs

---

### Zone / ZoneDefinition

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Zone | id | FARE ZONE | id | exact |
| Zone | name | FARE ZONE | name | exact |
| Zone | stations | SCHEDULED STOP POINT (in zone) | — | exact |
| ZoneDefinition | id | FARE ZONE | id | exact |
| ZoneDefinition | polygon | FARE ZONE | polygon | partial |

x-semantics equivalent:
- **concepts**: FARE ZONE (Fare Structure)

---

### TravelValidity / ValidityRange

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TravelValidity | validFrom | USAGE VALIDITY PERIOD | startDate | exact |
| TravelValidity | validUntil | USAGE VALIDITY PERIOD | endDate | exact |
| TravelValidity | validDuration | USAGE VALIDITY PERIOD | duration | exact |
| ValidityRange | startDate | USAGE VALIDITY PERIOD | startDate | exact |
| ValidityRange | endDate | USAGE VALIDITY PERIOD | endDate | exact |

x-semantics equivalent:
- **concepts**: USAGE VALIDITY PERIOD (Fare Structure)

---

### TrainLink / TrainValidity

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TrainLink | trainNumber | VEHICLE JOURNEY | serviceCode | exact |
| TrainLink | board | SCHEDULED STOP POINT | id | exact |
| TrainLink | alight | SCHEDULED STOP POINT | id | exact |
| TrainValidity | trainLinks | VEHICLE JOURNEY / ROUTE | — | partial |

x-semantics equivalent:
- **concepts**: SERVICE JOURNEY (Journey Planning), DATED VEHICLE JOURNEY
- **conditions**: Identifies a specific train service as scope of a fare; maps to a DATED VEHICLE JOURNEY or SERVICE JOURNEY

---

### Route / RouteItem / RouteSpecification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Route | viaStations | ROUTE / JOURNEY PATTERN | waypoints | partial |
| Route | origin | ROUTE | originRef | exact |
| Route | destination | ROUTE | destinationRef | exact |
| RouteItem | station | SCHEDULED STOP POINT | id | exact |
| RouteSpecification | origin | SCHEDULED STOP POINT | id | exact |
| RouteSpecification | destination | SCHEDULED STOP POINT | id | exact |

x-semantics equivalent:
- **concepts**: ROUTE (Topology), JOURNEY PATTERN
- **conditions**: Defines origin-destination routing constraint for fare validity; maps to a simplified ROUTE/JOURNEY PATTERN routing specification

---

### Section

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Section | from | FARE STRUCTURE ELEMENT / BORDER POINT | — | partial |
| Section | to | FARE STRUCTURE ELEMENT / BORDER POINT | — | partial |

x-semantics equivalent:
- **concepts**: FARE STRUCTURE ELEMENT (Fare Structure), SECTION (Topology)
- **conditions**: A contiguous portion of a route between two connection points; maps to a SECTION or FARE STRUCTURE ELEMENT sub-section

---

## 11. Products & Master Data

### Product

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Product | id | FARE PRODUCT / SALES OFFER PACKAGE | id | exact |
| Product | name | FARE PRODUCT | name | exact |
| Product | type | FARE PRODUCT | type | exact |
| Product | description | FARE PRODUCT | description | exact |
| Product | tags | FARE PRODUCT | tags | partial |
| Product | travelClass | TRAVEL CLASS | — | exact |
| Product | serviceClass | SERVICE CLASS | — | exact |
| Product | flexibility | RESELLING | — | partial |
| Product | category | FARE PRODUCT CATEGORY | — | partial |
| Product | luggageConstraint | — | — | none — OSDM extension |
| Product | accommodationType | ACCOMMODATION | type | partial |

x-semantics equivalent:
- **concepts**: FARE PRODUCT (Fare Structure), SALES OFFER PACKAGE (Fare Structure)
- **conditions**: Commercial product definition used to represent offer parts; maps primarily to FARE PRODUCT

---

### ProductType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ProductType | ADMISSION | PREASSIGNED FARE PRODUCT | — | exact |
| ProductType | RESERVATION | SUPPLEMENTARY FARE PRODUCT | — | partial |
| ProductType | ANCILLARY | SUPPLEMENTARY FARE PRODUCT | — | partial |
| ProductType | PASS | AMOUNT OF PRICE UNIT | — | partial |

x-semantics equivalent:
- **concepts**: FARE PRODUCT (Fare Structure) subtypes

---

### ProductLegAssociation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ProductLegAssociation | productId | FARE PRODUCT | id | exact |
| ProductLegAssociation | tripCoverage | TRAVEL SPECIFICATION | — | exact |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE ELEMENT (Fare Structure)
- **conditions**: Associates a product with the trip legs it covers; maps to SALES OFFER PACKAGE ELEMENT leg association

---

### ProductTag / ProductTagGroup

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ProductTag | name | — | — | partial — product classification tag, no Transmodel equivalent |
| ProductTag | group | — | — | partial |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent — OSDM-specific product tagging for commercial classification

---

### ReductionCardType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ReductionCardType | code | ENTITLEMENT PRODUCT | id / code | exact |
| ReductionCardType | issuer | TRANSPORT OPERATOR | id | exact |
| ReductionCardType | name | ENTITLEMENT PRODUCT | name | exact |
| ReductionCardType | reductionsGranted | SALE DISCOUNT RIGHT | discountPercentage | exact |
| ReductionCardType | serviceClassTypes | SERVICE CLASS | — | exact |

x-semantics equivalent:
- **concepts**: ENTITLEMENT PRODUCT (Fare Structure), SALE DISCOUNT RIGHT (Fare Structure)
- **conditions**: A reduction/loyalty card that grants fare discounts; maps to ENTITLEMENT PRODUCT granting SALE DISCOUNT RIGHTs

---

### CardReference / CardTypeReference

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| CardReference | type | ENTITLEMENT PRODUCT | type | exact |
| CardReference | cardId | ENTITLEMENT PRODUCT | cardNumber | partial |
| CardTypeReference | code | ENTITLEMENT PRODUCT | code | exact |

x-semantics equivalent:
- **concepts**: ENTITLEMENT PRODUCT (Fare Structure)
- **conditions**: Reference to a reduction or loyalty card held by a passenger; maps to ENTITLEMENT PRODUCT reference

---

### PromotionCode

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PromotionCode | code | SALE DISCOUNT RIGHT | promotionCode | exact |

x-semantics equivalent:
- **concepts**: SALE DISCOUNT RIGHT (Fare Structure)
- **conditions**: A promotional code that grants a discount; maps to SALE DISCOUNT RIGHT

---

### CorporateCode

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| CorporateCode | code | COMMERCIAL PROFILE | code | exact |
| CorporateCode | carrier | TRANSPORT OPERATOR | id | exact |

x-semantics equivalent:
- **concepts**: COMMERCIAL PROFILE (Fare Structure)
- **conditions**: Corporate discount code applied by business customers; maps to COMMERCIAL PROFILE ELIGIBILITY

---

### CompanyRef / NamedCompany

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| CompanyRef | id | OPERATOR / ORGANISATION | id | exact |
| CompanyRef | name | OPERATOR | name | exact |
| NamedCompany | id | OPERATOR | id | exact |
| NamedCompany | name | OPERATOR | name | exact |

x-semantics equivalent:
- **concepts**: OPERATOR (Organisations), TRANSPORT AUTHORITY, FARE ORGANISATION ROLE
- **conditions**: Reference to a transport operator or carrier; maps to OPERATOR

---

### Line / LineNumber

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Line | carrier | OPERATOR | id | exact |
| Line | lineIds | LINE | id | exact |
| Line | entryStation | SCHEDULED STOP POINT | id | partial |
| Line | terminalStation | SCHEDULED STOP POINT | id | partial |
| LineNumber | — | LINE | publicCode | exact |

x-semantics equivalent:
- **concepts**: LINE (Network Topology), OPERATOR

---

### Mode / PTMode / ModeFilter

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Mode | ptMode | TRANSPORT MODE | ptMode | exact |
| Mode | name | TRANSPORT MODE | name | exact |
| ModeFilter | exclude | — | — | partial — inclusion/exclusion filter, no direct Transmodel concept |
| ModeFilter | transportModes | TRANSPORT MODE | — | exact |

x-semantics equivalent:
- **concepts**: TRANSPORT MODE (Common)

---

### ServiceClass / TravelClass / Flexibility

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ServiceClass | serviceClass | FARE CLASS | — | exact |
| TravelClass | travelClass | TRAVEL CLASS | — | exact |
| Flexibility | FULL_FLEXIBLE | RESELLING | flexibility=fully flexible | exact |
| Flexibility | SEMI_FLEXIBLE | RESELLING | flexibility=semi-flexible | exact |
| Flexibility | NON_FLEXIBLE | RESELLING | flexibility=non-flexible | exact |

x-semantics equivalent:
- **concepts**: TRAVEL CLASS (Common), FARE CLASS (Fare Structure), RESELLING (Fare Structure)

---

### AccommodationType / AccommodationSubType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AccommodationType | SEAT / COUCHETTE / BERTH | ACCOMMODATION | type | exact |
| AccommodationType | VEHICLE | — | — | partial — vehicle transport (car, motorcycle) |
| AccommodationSubType | SINGLE / DOUBLE / COMPARTMENT / … | ACCOMMODATION | subType | exact |
| AccommodationSubType | WHEELCHAIR / PRM variants | ACCESS EQUIPMENT | — | partial |

x-semantics equivalent:
- **concepts**: ACCOMMODATION (Vehicle Equipment), SPOT (Vehicle)

---

## 12. Travel Accounts

### AbstractTravelAccount / MultiRideAccount / TravelPassAccount / ReductionCardAccount

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AbstractTravelAccount | issuer | OPERATOR | id | exact |
| AbstractTravelAccount | validFrom | USAGE VALIDITY PERIOD | startDate | exact |
| AbstractTravelAccount | validUntil | USAGE VALIDITY PERIOD | endDate | exact |
| AbstractTravelAccount | number | CUSTOMER ACCOUNT | accountNumber | exact |
| AbstractTravelAccount | holder | TRANSPORT CUSTOMER | — | exact |
| MultiRideAccount | balance | AMOUNT OF PRICE UNIT | remainingValue | exact |
| TravelPassAccount | type | FARE PRODUCT | type | exact |
| ReductionCardAccount | type | ENTITLEMENT PRODUCT | type | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER ACCOUNT (Sales & Distribution), AMOUNT OF PRICE UNIT (Fare Structure), ENTITLEMENT PRODUCT
- **conditions**: Account-based representation of a travel right (multi-ride carnet, pass, or reduction card); maps to CUSTOMER ACCOUNT holding an AMOUNT OF PRICE UNIT or FARE PRODUCT

---

### TravelAccountConsumption

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TravelAccountConsumption | consumedUnits | CUSTOMER PURCHASE STATUS | consumedUnits | partial |
| TravelAccountConsumption | date | CUSTOMER PURCHASE STATUS | consumptionDate | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE STATUS (Sales & Distribution), ACCOUNT DETECT TRIP EVENT

---

### DayTravelAccountUnit / MoneyTravelAccountUnit / NamedTravelAccountUnit / TripTravelAccountUnit

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| DayTravelAccountUnit | — | AMOUNT OF PRICE UNIT (day-based) | — | partial |
| MoneyTravelAccountUnit | currency | AMOUNT OF PRICE UNIT (monetary) | currency | exact |
| NamedTravelAccountUnit | name | FARE PRODUCT | name | partial |
| TripTravelAccountUnit | — | AMOUNT OF PRICE UNIT (trip-based) | — | partial |

x-semantics equivalent:
- **concepts**: AMOUNT OF PRICE UNIT (Fare Structure)
- **conditions**: Unit types for multi-ride and stored value accounts

---

## 13. On-Demand / Continuous Services

### ContinuousServiceOfferPart

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ContinuousServiceOfferPart | type | ALTERNATIVE MODE LEG | mode | partial |
| ContinuousServiceOfferPart | service | — | — | none — shared mobility service, beyond Transmodel classical scope |
| ContinuousServiceOfferPart | pickUpPlaces | SCHEDULED STOP POINT / MEETING POINT | — | partial |
| ContinuousServiceOfferPart | vehicleTypes | VEHICLE TYPE | — | partial |

x-semantics equivalent:
- **concepts**: ALTERNATIVE MODE TRAVEL PREFERENCES (Journey Planning), VEHICLE POOLING SERVICE
- **conditions**: Shared-mobility / continuous service (bike, scooter, car-share) as an offer part; Transmodel covers this partially via VEHICLE SHARING / VEHICLE POOLING SERVICE

---

### ContinuousServiceBookingPart

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ContinuousServiceBookingPart | continuousServiceId | — | — | none |
| ContinuousServiceBookingPart | status | CUSTOMER PURCHASE STATUS | — | partial |
| ContinuousServiceBookingPart | usage | — | — | none |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE ELEMENT (Sales & Distribution)
- **conditions**: Booked on-demand service element; maps to CUSTOMER PURCHASE PACKAGE ELEMENT but service type has no Transmodel equivalent

---

### ContinuousServiceUsage / ContinuousServiceVehicleSelection

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ContinuousServiceUsage | status | — | — | none |
| ContinuousServiceVehicleSelection | vehicleType | VEHICLE TYPE | — | partial |
| ContinuousServiceVehicleSelection | pickUpPlace | SCHEDULED STOP POINT / VEHICLE MEETING POINT | — | partial |

x-semantics equivalent:
- **concepts**: VEHICLE MEETING POINT (Journey Planning), VEHICLE TYPE (Vehicle)
- **conditions**: Vehicle selection for on-demand service activation; VEHICLE MEETING POINT maps to pick-up place

---

### ContinuousServiceVehicleAvailability / ContinuousServicePickUpPlace

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ContinuousServiceVehicleAvailability | vehicleType | VEHICLE TYPE | — | partial |
| ContinuousServiceVehicleAvailability | available | — | — | none |
| ContinuousServicePickUpPlace | placeRef | VEHICLE MEETING POINT | id | partial |
| ContinuousServicePickUpPlace | name | VEHICLE MEETING POINT | name | partial |

x-semantics equivalent:
- **concepts**: VEHICLE MEETING POINT (Journey Planning), VEHICLE TYPE (Vehicle)

---

## 14. Pricing & Fees

### Price / CurrencyConversion

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Price | amount | FARE PRICE | amount | exact |
| Price | currency | FARE PRICE | currency | exact |
| Price | scale | FARE PRICE | scale | partial |
| Price | vat | VAT | vatRate | exact |
| CurrencyConversion | fromCurrency | — | — | partial — currency conversion auxiliary |
| CurrencyConversion | toCurrency | — | — | partial |
| CurrencyConversion | rate | — | — | partial |

x-semantics equivalent:
- **concepts**: FARE PRICE (Fare Structure), CUSTOMER PURCHASE PACKAGE PRICE
- **conditions**: Monetary amount with currency and scale; maps directly to FARE PRICE

---

### VAT

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| VAT | amount | FARE PRICE | vatAmount | exact |
| VAT | percentage | FARE PRICE | vatRate | exact |
| VAT | country | — | — | partial |

x-semantics equivalent:
- **concepts**: FARE PRICE (Fare Structure)
- **conditions**: VAT information on price; Transmodel includes VAT on FARE PRICE but does not model per-country tax breakdown

---

### Fee

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Fee | id | FARE PRICE (fee component) | id | partial |
| Fee | type | — | — | partial — seat selection fee, service fee |
| Fee | price | FARE PRICE | amount | exact |
| Fee | applicability | — | — | partial — booking/offer/leg scope |

x-semantics equivalent:
- **concepts**: FARE PRICE (Fare Structure)
- **conditions**: Additional charge (seat selection fee, booking fee) beyond the base fare; maps to a FARE PRICE surcharge component

---

### SelectionFee

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| SelectionFee | price | FARE PRICE | amount | exact |
| SelectionFee | type | — | — | partial — place-selection-specific fee |

x-semantics equivalent:
- **concepts**: FARE PRICE (Fare Structure)
- **conditions**: Fee applied when a specific seat is selected; maps to FARE PRICE surcharge linked to a reservation

---

### GrantedReductionAmount / GrantedReductionAmounts

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GrantedReductionAmount | cardRef | ENTITLEMENT PRODUCT | id | exact |
| GrantedReductionAmount | amount | SALE DISCOUNT RIGHT / FARE PRICE | discountAmount | exact |

x-semantics equivalent:
- **concepts**: SALE DISCOUNT RIGHT (Fare Structure), FARE PRICE
- **conditions**: Actual reduction amount granted for a specific reduction card; maps to discount value derived from SALE DISCOUNT RIGHT

---

### PaymentMethod / PaymentType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PaymentMethod | type | CUSTOMER PAYMENT MEANS | — | exact |
| PaymentMethod | voucherInformation | — | — | partial — voucher as payment method |
| PaymentType | CARD / CASH / INVOICE / … | CUSTOMER PAYMENT MEANS | paymentType | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER PAYMENT MEANS (Sales & Distribution)

---

## 15. Supporting / Utility Types

### Problem / Warning

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Problem | type | — | — | none — API error descriptor |
| Problem | title | — | — | none |
| Problem | detail | — | — | none |
| Warning | type | — | — | none |

x-semantics equivalent:
- **concepts**: No Transmodel equivalent — RFC 7807 problem details for API error responses

---

### Link

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Link | rel | — | — | none — JSON-HAL hypermedia link |
| Link | href | — | — | none |

x-semantics equivalent:
- **concepts**: No Transmodel equivalent — HATEOAS link convention

---

### Text / Translation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Text | default | — | — | partial — multilingual label |
| Text | translations | — | — | partial |
| Translation | language | — | — | partial |
| Translation | text | — | — | partial |

x-semantics equivalent:
- **concepts**: MULTILINGUAL STRING (Utility) — Transmodel uses MultilingualString for localised text; OSDM Text/Translation maps to this

---

### Event / EventType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Event | type | — | — | partial — booking lifecycle event log |
| Event | timestamp | — | — | partial |
| Event | bookingRef | CUSTOMER PURCHASE PACKAGE | id | partial |

x-semantics equivalent:
- **concepts**: CUSTOMER ACCOUNT EVENT (Sales & Distribution)
- **conditions**: Booking history event; loosely maps to CUSTOMER ACCOUNT EVENT in Transmodel

---

### GeneralAttribute / LegAttribute

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GeneralAttribute | text | SERVICE FACILITY | description | partial |
| LegAttribute | text | SERVICE FACILITY | — | partial |
| LegAttribute | code | SERVICE FACILITY | code | partial |
| LegAttribute | fromStopSeqNumber | — | — | partial — section-scoped attribute |

x-semantics equivalent:
- **concepts**: SERVICE FACILITY (Fixed Objects), FACILITY SET (Vehicle Equipment)

---

### Transportable / TransportableSpecification / TransportableType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Transportable | type | — | — | partial — non-person items (bike, car, pet) |
| TransportableSpecification | type | — | — | partial |
| TransportableType | BICYCLE / CAR / MOTORCYCLE / PET / … | — | — | none — OSDM extension for transportable items |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent; closest is TRANSPORTABLE ELEMENT (Vehicle) for luggage, or USER PROFILE for non-standard passengers

---

### RegulatoryCondition

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| RegulatoryCondition | type | — | — | partial — EU regulatory condition type |
| RegulatoryCondition | description | — | — | partial |

x-semantics equivalent:
- **concepts**: No Transmodel equivalent — regulatory conditions (EU passenger rights, customs, etc.) are OSDM-specific

---

### TravelDirectionType / TripDirectionType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TravelDirectionType | SINGLE / RETURN | ROUND TRIP / SINGLE JOURNEY | type | exact |
| TripDirectionType | OUT_BOUND / IN_BOUND | TRIP | direction | partial |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (outbound/return), TRIP

---

### VoucherInformation

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| VoucherInformation | voucherId | — | — | none — voucher as form of reimbursement |
| VoucherInformation | amount | FARE PRICE | amount | partial |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent — OSDM-specific voucher payment instrument

---

### SituationFullRef

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| SituationFullRef | situationNumber | SITUATION | id | exact |
| SituationFullRef | source | — | — | partial |

x-semantics equivalent:
- **concepts**: SITUATION (Operations Management) — cross-reference to SIRI/OPRA situation reports affecting a trip

---

### DateRange

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| DateRange | startDate | VALIDITY PERIOD | startDate | exact |
| DateRange | endDate | VALIDITY PERIOD | endDate | exact |

x-semantics equivalent:
- **concepts**: VALIDITY PERIOD (Common)

---

### BackOfficeStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BackOfficeStatus | INITIATED / EVALUATING / DECIDED / SETTLED | — | — | none — back-office workflow state, no Transmodel equivalent |

---

### LuggageConstraint / LuggageRestriction

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| LuggageConstraint | maxHandLuggage | — | — | none — luggage rules are OSDM-specific |
| LuggageConstraint | maxLargeLuggage | — | — | none |
| LuggageRestriction | dimensionRestrictions | — | — | none |

x-semantics equivalent:
- **concepts**: No Transmodel equivalent — luggage allowance constraints specific to UIC/OSDM domain

---

### LegacyAccountingIdentifier / LegacyReservationParameter

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| LegacyAccountingIdentifier | serialId | — | — | none — UIC 301 legacy accounting format |
| LegacyReservationParameter | travelClass | TRAVEL CLASS | — | partial |
| LegacyReservationParameter | serviceCode | SERVICE JOURNEY | serviceCode | partial |

x-semantics equivalent:
- **concepts**: No Transmodel equivalent — legacy UIC 90918-1 back-compatibility

---

### ApiVersion / ApiNextVersion

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ApiVersion | version | — | — | none — API versioning metadata |
| ApiNextVersion | version | — | — | none |

x-semantics equivalent:
- **concepts**: No Transmodel equivalent

---

*End of OSDM → Transmodel mapping. All 16 domain groups from OSDM 3.7.1 are covered.*
