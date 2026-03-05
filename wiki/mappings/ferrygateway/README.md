# FerryGateway → Transmodel Mapping

## Overview

This document maps the concepts and attributes of the **FerryGateway** XML Schema (namespace `http://schemas.ferrygateway.org/1.3.1`, version 1.3.1) to the **Transmodel** reference data model.

FerryGateway is a B2B ferry booking and timetable API standard maintained by the Ferrygateway Association. It covers the full booking lifecycle: route discovery, timetable lookup, sailing search, pricing, service selection, reservation, booking confirmation, cancellation, and invoicing, plus connecting bus-transfer services.

Match quality is indicated in the **Notes** column:
- **exact** — one-to-one semantic correspondence
- **partial** — overlapping scope; some attributes or nuances differ
- **none** — no Transmodel equivalent; FerryGateway concept is domain-specific

---

## 1. Network & Geography

### AbstractRouteType / RouteType

`AbstractRouteType` is the abstract base for all route references (departure and destination port pair + operator). `RouteType` extends it with port coordinates, country codes, notes, media, and transport-option declarations.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| AbstractRouteType | OperatorCode | OPERATOR | id | partial — FGW uses a proprietary association-assigned code, not a general OPERATOR id |
| AbstractRouteType | DepartPort | SCHEDULED STOP POINT | id | partial — port identified by UN/LOCODE (5-char); maps to a STOP PLACE or QUAY in Transmodel |
| AbstractRouteType | DestinationPort | SCHEDULED STOP POINT | id | partial — same as DepartPort |
| RouteType | DepartPortName | STOP PLACE | name | exact |
| RouteType | DepartPortCountry | STOP PLACE | countryRef | exact |
| RouteType | DepartPortLatitude | STOP PLACE | centroid/Location/latitude | exact |
| RouteType | DepartPortLongitude | STOP PLACE | centroid/Location/longitude | exact |
| RouteType | DestinationPortName | STOP PLACE | name | exact |
| RouteType | DestinationPortCountry | STOP PLACE | countryRef | exact |
| RouteType | DestinationPortLatitude | STOP PLACE | centroid/Location/latitude | exact |
| RouteType | DestinationPortLongitude | STOP PLACE | centroid/Location/longitude | exact |
| RouteType | RouteGroup | GROUP OF LINES | name | partial — operator-named grouping of routes |
| RouteType | RouteNote | LINE | description | partial — general note on the route/line |
| RouteType | MandatoryFacilities | BOOKING POLICY | — | partial — mandatory facility declarations (seat, cabin, meal, passport, VISA) constrain access rights; see MandatoryFacilitiesType below |
| RouteType | PetTransportOptions | LOCAL SERVICE | — | partial — pet transport options are service characteristics; no direct Transmodel attribute |
| RouteType | VehicleTransportOptions | — | — | none — vehicle transport rules on a route have no direct Transmodel equivalent |
| RouteType | Medias | — | — | none — media attachments (images, documents) are not part of Transmodel |

x-semantics equivalent:
- **concepts**: LINE (Transmodel Network), ROUTE (Transmodel Network), STOP PLACE (Transmodel Stops)
- **conditions**: A FerryGateway Route corresponds to a Transmodel LINE connecting two STOP PLACEs (ports). The OperatorCode identifies the OPERATOR. RouteGroup maps to GROUP OF LINES.

---

### PortCodeType / PlaceQualifierType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PortCodeType | (value) | SCHEDULED STOP POINT | id | partial — UN/LOCODE 5-char port code; used as stop identifier |
| PlaceQualifierType | Port / Group / Country | STOP PLACE / GROUP OF LINES / — | — | partial — qualifies whether a place code refers to a port, route group, or country |

x-semantics equivalent:
- **concepts**: SCHEDULED STOP POINT (Transmodel Network), STOP PLACE (Transmodel Stops)
- **conditions**: PortCodeType encodes a UN/LOCODE 5-character identifier and is used wherever a port is referenced; PlaceQualifierType disambiguates between port, route group, and country scope.

---

## 2. Timetable

### GetTimeTablesRequestType / GetTimeTablesResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetTimeTablesRequestType | OperatorCode | OPERATOR | id | partial |
| GetTimeTablesRequestType | DepartPort | SCHEDULED STOP POINT | id | exact |
| GetTimeTablesRequestType | DestinationPort | SCHEDULED STOP POINT | id | exact |
| GetTimeTablesRequestType | FromSailingDate | VEHICLE JOURNEY | departureTime (lower bound) | partial — date range filter for timetable query |
| GetTimeTablesRequestType | ToSailingDate | VEHICLE JOURNEY | departureTime (upper bound) | partial |
| GetTimeTablesRequestType | AllotmentAgreementId | — | — | none — allotment/quota agreements have no direct Transmodel equivalent |
| GetTimeTablesResponseType | SailingInfos | DATED VEHICLE JOURNEY | — | partial — collection of scheduled sailings; see TimetableSailingInfoResponseType |

x-semantics equivalent:
- **concepts**: DATED VEHICLE JOURNEY (Transmodel Journey), OPERATOR (Transmodel Organisation), SCHEDULED STOP POINT (Transmodel Network)
- **conditions**: Retrieves the scheduled sailing timetable for a given operator and port pair within a date range; each returned sailing corresponds to a DATED VEHICLE JOURNEY.

---

### TimetableSailingInfoResponseType / TimetableSailingInfosType

Extends `SailingInfoResponseType` with vessel identification attributes.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TimetableSailingInfoResponseType | SailingId | DATED VEHICLE JOURNEY | id | exact |
| TimetableSailingInfoResponseType | DepartDateTime | DATED VEHICLE JOURNEY | departureTime | exact |
| TimetableSailingInfoResponseType | ArriveDateTime | DATED VEHICLE JOURNEY | arrivalTime | exact |
| TimetableSailingInfoResponseType | AdjustedDepartDateTime | ESTIMATED PASSING TIME | expectedDepartureTime | partial — adjusted time (e.g., tidal) |
| TimetableSailingInfoResponseType | AdjustedArriveDateTime | ESTIMATED PASSING TIME | expectedArrivalTime | partial |
| TimetableSailingInfoResponseType | DepartPort | SCHEDULED STOP POINT | id | exact |
| TimetableSailingInfoResponseType | DestinationPort | SCHEDULED STOP POINT | id | exact |
| TimetableSailingInfoResponseType | DepartPortName | STOP PLACE | name | exact |
| TimetableSailingInfoResponseType | DestinationPortName | STOP PLACE | name | exact |
| TimetableSailingInfoResponseType | DepartPortTerminal | QUAY | name | partial — terminal name within port |
| TimetableSailingInfoResponseType | DestinationPortTerminal | QUAY | name | partial |
| TimetableSailingInfoResponseType | CheckinStartDateTime | — | — | none — check-in window is not a Transmodel concept |
| TimetableSailingInfoResponseType | CheckinEndDateTime | — | — | none |
| TimetableSailingInfoResponseType | IsAccommodationMandatory | BOOKING POLICY | — | partial — mandatory accommodation booking constraint |
| TimetableSailingInfoResponseType | ShipName | VEHICLE | name | partial — vessel name |
| TimetableSailingInfoResponseType | ShipIMO | VEHICLE | registrationNumber | partial — IMO vessel number |
| TimetableSailingInfoResponseType | IsFastCraft | VEHICLE TYPE | — | partial — vehicle type characteristic (fast craft vs. conventional) |

x-semantics equivalent:
- **concepts**: DATED VEHICLE JOURNEY (Transmodel Journey), ESTIMATED PASSING TIME (Transmodel Journey), VEHICLE (Transmodel Vehicle), VEHICLE TYPE (Transmodel Vehicle), QUAY (Transmodel Stops)
- **conditions**: Represents a single scheduled or tidal-adjusted sailing; the vessel identified by ShipName/ShipIMO is modelled as a VEHICLE instance of a VEHICLE TYPE.

---

## 3. Sailings (Journey Search & Availability)

### GetSailingsRequestType / GetSailingsResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetSailingsRequestType | Passengers | TRANSPORT CUSTOMER / USER PROFILE | — | partial — passenger list with category; see PassengerType |
| GetSailingsRequestType | Pets | — | — | none — no Transmodel concept for pets |
| GetSailingsRequestType | Vehicles | VEHICLE TYPE | — | partial — vehicle dimensions and type; see VehicleType |
| GetSailingsRequestType | FerryComponent | SERVICE JOURNEY | — | partial — encapsulates the sailing search criteria |
| GetSailingsRequestType | IsOnlyMinicruise | — | — | none — mini-cruise filter is FGW-specific |
| GetSailingsRequestType | ShowAlternativeRoutes | — | — | none — alternative route suggestion is FGW-specific |
| GetSailingsRequestType | AllotmentAgreementId | — | — | none |
| GetSailingsResponseType | FerryComponents | DATED VEHICLE JOURNEY | — | partial — one or more bookable sailing options |

x-semantics equivalent:
- **concepts**: DATED VEHICLE JOURNEY (Transmodel Journey), TRANSPORT CUSTOMER (Transmodel Customer), USER PROFILE (Transmodel Customer), VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: Performs an availability search for one or more sailings; each FerryComponent in the response groups one or more bookable DATED VEHICLE JOURNEYs.

---

### SailingSearchCriterionType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| SailingSearchCriterionType | DepartDateTime | VEHICLE JOURNEY | departureTime | exact |
| SailingSearchCriterionType | DepartPlace | SCHEDULED STOP POINT | id | partial — may be port, country, or group code |
| SailingSearchCriterionType | DepartPlaceQualifier | — | — | none — place type qualifier is FGW-specific |
| SailingSearchCriterionType | DestinationPlace | SCHEDULED STOP POINT | id | partial |
| SailingSearchCriterionType | DestinationPlaceQualifier | — | — | none |
| SailingSearchCriterionType | OperatorCode | OPERATOR | id | partial |
| SailingSearchCriterionType | BeforeDepartHours / AfterDepartHours | — | — | none — search window parameters have no Transmodel equivalent |
| SailingSearchCriterionType | BeforeDepartSailings / AfterDepartSailings | — | — | none |
| SailingSearchCriterionType | RequestedServices | LOCAL SERVICE | — | partial — filter for sailings offering specific on-board or land services |

x-semantics equivalent:
- **concepts**: VEHICLE JOURNEY (Transmodel Journey), SCHEDULED STOP POINT (Transmodel Network), OPERATOR (Transmodel Organisation)
- **conditions**: Specifies the search parameters for a single ferry leg; DepartPlace and DestinationPlace are resolved against SCHEDULED STOP POINTs using the accompanying PlaceQualifier.

---

### SailingInfoRequestType / SailingInfoResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| SailingInfoRequestType | DepartPort | SCHEDULED STOP POINT | id | exact |
| SailingInfoRequestType | DestinationPort | SCHEDULED STOP POINT | id | exact |
| SailingInfoRequestType | DepartDateTime | VEHICLE JOURNEY | departureTime | exact |
| SailingInfoResponseType | DepartDateTime | DATED VEHICLE JOURNEY | departureTime | exact |
| SailingInfoResponseType | ArriveDateTime | DATED VEHICLE JOURNEY | arrivalTime | exact |
| SailingInfoResponseType | DepartPortName | STOP PLACE | name | exact |
| SailingInfoResponseType | DestinationPortName | STOP PLACE | name | exact |
| SailingInfoResponseType | DepartPortTerminal | QUAY | name | partial |
| SailingInfoResponseType | DestinationPortTerminal | QUAY | name | partial |
| SailingInfoResponseType | IsAccommodationMandatory | BOOKING POLICY | — | partial |
| SailingInfoResponseType | MandatoryFacilities | BOOKING POLICY | — | partial |

x-semantics equivalent:
- **concepts**: DATED VEHICLE JOURNEY (Transmodel Journey), STOP PLACE (Transmodel Stops), QUAY (Transmodel Stops), BOOKING POLICY (Transmodel Fare)
- **conditions**: Retrieves details of a specific sailing identified by port pair and departure date/time; the response represents a single DATED VEHICLE JOURNEY with associated booking constraints.

---

### SailingGetSailingsResponseType / SailingPriceResponseType / SailingBookResponseType (availability flags)

These three types share a common set of availability flags that are FGW-specific sailing-level availability indicators.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| Sailing\*ResponseType | IsAvailable | CUSTOMER SPOT AVAILABILITY EVENT | — | partial — boolean availability at response time |
| Sailing\*ResponseType | IsVehicleAvailable | — | — | none — vehicle deck space availability has no Transmodel equivalent |
| Sailing\*ResponseType | IsAccommodationAvailable | — | — | none |
| Sailing\*ResponseType | IsPetAvailable | — | — | none |
| Sailing\*ResponseType | IsTransferToFerryAvailable | CONNECTION | — | partial — indicates connecting transfer exists |
| Sailing\*ResponseType | IsTransferFromFerryAvailable | CONNECTION | — | partial |
| Sailing\*ResponseType | SailingInformation | — | — | none — free-text sailing note; no direct Transmodel attribute |
| Sailing\*ResponseType | LegSetNumber (GetSailings only) | JOURNEY PATTERN | — | partial — groups sailings by leg set for selection rules |

x-semantics equivalent:
- **concepts**: CUSTOMER SPOT AVAILABILITY EVENT (Transmodel Customer), CONNECTION (Transmodel Journey), JOURNEY PATTERN (Transmodel Network)
- **conditions**: Availability flags are sailing-level indicators attached to any Sailing response variant; most vehicle/accommodation/pet flags have no direct Transmodel equivalent.

---

### CombinationRuleType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| CombinationRuleType | None / UseLegSetNumber | — | — | none — FGW-specific combination-selection rule; no direct Transmodel equivalent |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: Controls how individual sailings may be selected from a FerryComponent; `None` means the whole component must be taken; `UseLegSetNumber` allows per-leg selection. No Transmodel concept governs this selection mechanism.

---

### FerryComponent types (request/response containers)

`FerryComponentGetSailingsRequestType`, `FerryComponentGetSailingsResponseType`, `FerryComponentPriceRequestType`, `FerryComponentPriceResponseType`, `FerryComponentBookRequestType`, `FerryComponentBookResponseType`, `FerryComponentTransferServicesRequestType`, `FerryComponentTransferServicesResponseType` are container types grouping one or more Sailings. They correspond to the Transmodel notion of a **TRIP PATTERN** or a **SERVICE JOURNEY** group.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FerryComponent\* | Id | TRIP PATTERN | id | partial |
| FerryComponent\* | DeepLink | — | — | none — operator URL; no Transmodel equivalent |
| FerryComponent\* | CombinationRule | — | — | none — see CombinationRuleType |
| FerryComponent\* | OfferCode | SALES OFFER PACKAGE | — | partial — promotional code restricting or modifying the offer |
| FerryComponent\* | Cost | FARE PRICE / CUSTOMER PURCHASE PACKAGE | totalPrice | partial — see CostType |
| FerryComponent\* | BookingReference | CUSTOMER PURCHASE PACKAGE | id | partial |

x-semantics equivalent:
- **concepts**: TRIP PATTERN (Transmodel Journey), SALES OFFER PACKAGE (Transmodel Fare), CUSTOMER PURCHASE PACKAGE (Transmodel Customer), FARE PRICE (Transmodel Fare)
- **conditions**: A FerryComponent is a multi-leg fare/journey container; at search/price stage it is analogous to a TRIP PATTERN or SERVICE JOURNEY group; at booking stage it is the basis of a CUSTOMER PURCHASE PACKAGE.

---

## 4. Pricing & Fare

### GetPriceRequestType / GetPriceResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetPriceRequestType | Passengers | TRANSPORT CUSTOMER | — | partial |
| GetPriceRequestType | Vehicles | — | — | none |
| GetPriceRequestType | FerryComponent | SALES OFFER PACKAGE | — | partial — encapsulates the priced fare component |
| GetPriceResponseType | Token | — | — | none — price validity token is FGW-specific; loosely related to FARE DEMAND FACTOR validity |
| GetPriceResponseType | TokenExpiryTime | FARE DEMAND FACTOR | endDate | partial |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Transmodel Fare), FARE DEMAND FACTOR (Transmodel Fare), TRANSPORT CUSTOMER (Transmodel Customer)
- **conditions**: Requests the fare price for a specific combination of passengers, vehicles, and selected sailings; the returned Token is a time-limited validity token loosely analogous to a FARE DEMAND FACTOR validity window.

---

### CostType / CostDetailsType / CostDetailType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| CostType | CostDetails (collection) | FARE PRICE | — | partial — aggregated price breakdown |
| CostDetailsType | GrossAmount | FARE PRICE | amount | exact |
| CostDetailsType | SailingId | DATED VEHICLE JOURNEY | id | partial — links cost breakdown to a specific sailing |
| CostDetailType | Category | FARE STRUCTURE ELEMENT / FARE PRODUCT | — | partial — cost category (passenger, vehicle, tax, etc.); see CostCategoryType |
| CostDetailType | TotalAmount | FARE PRICE | amount | exact |
| CostDetailType | Label | FARE STRUCTURE ELEMENT | name | partial |
| CostDetailType | Quantity | — | — | none — quantity of units for this cost item |

x-semantics equivalent:
- **concepts**: FARE PRICE (Transmodel Fare), FARE STRUCTURE ELEMENT (Transmodel Fare), FARE PRODUCT (Transmodel Fare)
- **conditions**: CostType is the FerryGateway price container; CostDetailsType breaks the total down per sailing; each CostDetailType line item corresponds to a FARE STRUCTURE ELEMENT or FARE PRODUCT sub-total.

---

### CostCategoryType

Each enumeration value maps to a Transmodel fare concept:

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Passenger | FARE PRODUCT | exact — passenger base fare |
| Vehicle / VehicleAndPassenger | FARE PRODUCT | partial — vehicle-inclusive fare product |
| Pet | FARE PRODUCT | partial — no specific Transmodel animal transport fare concept |
| OnBoardAccommodation | SALES OFFER PACKAGE | partial — accommodation service sold as add-on |
| OnBoardService | LOCAL SERVICE / CATERING SERVICE | partial |
| LandService | LOCAL SERVICE | partial |
| FuelSurcharge | FARE STRUCTURE ELEMENT | partial — surcharge element |
| PortTaxes | FARE STRUCTURE ELEMENT | partial — tax element |
| ValueAddedTax | FARE STRUCTURE ELEMENT | partial — VAT element |
| CancellationFee | CANCELLING | partial — cancellation condition price |
| AmendmentFee | EXCHANGING | partial — exchange condition fee |
| Commission | FARE ORGANISATION ROLE | partial — agent commission; not a fare product attribute |
| CommissionableAmount | — | none — no direct Transmodel equivalent |
| Deposit | CHARGING POLICY | partial — deposit amount |
| Balance | CUSTOMER PURCHASE PACKAGE | partial — outstanding balance |
| LoyaltyMemberDiscount | SALE DISCOUNT RIGHT | partial — loyalty scheme discount |
| OfferCodeDiscount | SALE DISCOUNT RIGHT | partial — promotional code discount |
| ReturnTripDiscount | SALE DISCOUNT RIGHT | partial — return journey discount |
| TravelAgentDiscount | SALE DISCOUNT RIGHT | partial — distribution-channel discount |
| PackageCost | SALES OFFER PACKAGE | partial — package pricing |
| OtherCosts / OtherDiscount / OtherTaxes | — | none — catch-all categories |
| AdministrationFee | — | none — no specific Transmodel equivalent |

x-semantics equivalent:
- **concepts**: FARE PRODUCT (Transmodel Fare), FARE STRUCTURE ELEMENT (Transmodel Fare), SALES OFFER PACKAGE (Transmodel Fare), SALE DISCOUNT RIGHT (Transmodel Fare), CANCELLING (Transmodel Fare), EXCHANGING (Transmodel Fare)
- **conditions**: CostCategoryType is an enumeration of cost line-item types; each value maps to the most appropriate Transmodel fare sub-concept.

---

### FareDetailsType / FareDetailsRequestType / FareDetailsResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| FareDetailsType | FareType | FARE PRODUCT | typeOfFareProductRef | partial — operator-defined fare type code |
| FareDetailsType | Productcode | FARE PRODUCT | id | partial — FGW Association product code |
| FareDetailsResponseType | FareTypeMessage | FARE PRODUCT | description | partial |
| FareDetailsResponseType | ProductDescription | FARE PRODUCT | description | partial |

x-semantics equivalent:
- **concepts**: FARE PRODUCT (Transmodel Fare)
- **conditions**: FareDetailsType carries the FGW Association product classification and operator fare type code; these map to the id and typeOfFareProductRef attributes of a FARE PRODUCT.

---

## 5. Offer Codes (Promotions & Discounts)

### GetOfferCodesRequestType / GetOfferCodesResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetOfferCodesRequestType | OperatorCode | OPERATOR | id | partial |
| GetOfferCodesResponseType | OfferCodeDetails | SALES OFFER PACKAGE / SALE DISCOUNT RIGHT | — | partial — list of promotional offer codes |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Transmodel Fare), SALE DISCOUNT RIGHT (Transmodel Fare), OPERATOR (Transmodel Organisation)
- **conditions**: Returns the promotional offer codes defined by an operator; each code corresponds to a conditional SALES OFFER PACKAGE or SALE DISCOUNT RIGHT.

---

### OfferCodeType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OfferCodeType | Code | SALES OFFER PACKAGE | id | partial — promotional code acting as a sales offer package identifier |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Transmodel Fare)
- **conditions**: The promotional code string acts as the identifier of a conditionally applicable SALES OFFER PACKAGE.

---

### OfferCodeDetailType / OfferCodeDetailsType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OfferCodeDetailType | OfferCode | SALES OFFER PACKAGE | id | partial |
| OfferCodeDetailType | ProductCode | FARE PRODUCT | id | partial |
| OfferCodeDetailType | FareType | FARE PRODUCT | typeOfFareProductRef | partial |
| OfferCodeDetailType | BookingStartDate | VALIDITY CONDITION / BOOKING POLICY | fromDate | partial — booking window start |
| OfferCodeDetailType | BookingEndDate | VALIDITY CONDITION / BOOKING POLICY | toDate | partial |
| OfferCodeDetailType | TravelStartDate | VALIDITY CONDITION | fromDate | exact — travel validity start |
| OfferCodeDetailType | TravelEndDate | VALIDITY CONDITION | toDate | exact |
| OfferCodeDetailType | MinJourneyDuration | FARE STRUCTURE ELEMENT | minimumDuration | partial |
| OfferCodeDetailType | MaxJourneyDuration | FARE STRUCTURE ELEMENT | maximumDuration | partial |
| OfferCodeDetailType | OfferCodeDescription | SALES OFFER PACKAGE | description | partial |
| OfferCodeDetailType | ProductCodeDescription | FARE PRODUCT | description | partial |
| OfferCodeDetailType | FareTypeDescription | FARE PRODUCT | typeOfFareProductRef (description) | partial |
| OfferCodeDetailType | Description | — | — | none — free-text routes/conditions description |

x-semantics equivalent:
- **concepts**: SALES OFFER PACKAGE (Transmodel Fare), FARE PRODUCT (Transmodel Fare), VALIDITY CONDITION (Transmodel Common), FARE STRUCTURE ELEMENT (Transmodel Fare), BOOKING POLICY (Transmodel Fare)
- **conditions**: OfferCodeDetailType fully describes a promotional offer code including its fare product, booking and travel validity windows, and journey duration constraints.

---

## 6. Booking

### BookBaseRequestType / BookRequestType / ReservationRequestType

`BookBaseRequestType` is the shared base for `BookRequestType` (binding booking) and `ReservationRequestType` (temporary reservation). Both carry passengers, contact details, vehicles, pets, the ferry component with selected sailings, and an optional comment.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookBaseRequestType | Context | — | — | partial — session/agent/currency context; see ContextType |
| BookBaseRequestType | Passengers | TRANSPORT CUSTOMER | — | partial |
| BookBaseRequestType | ContactDetails | TRANSPORT CUSTOMER | contactDetails | partial; see ContactDetailsType |
| BookBaseRequestType | Pets | — | — | none |
| BookBaseRequestType | Vehicles | VEHICLE TYPE | — | partial |
| BookBaseRequestType | FerryComponent | CUSTOMER PURCHASE PACKAGE | — | partial — selected sailings and services to book |
| BookBaseRequestType | Comment | — | — | none — free-text booking comment |
| BookBaseRequestType | Token | — | — | none — price validity token from GetPrice |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Transmodel Customer), TRANSPORT CUSTOMER (Transmodel Customer), VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: BookRequestType creates a binding CUSTOMER PURCHASE PACKAGE; ReservationRequestType creates a temporary hold that results in a CUSTOMER RESERVATION CREATION EVENT. The Token carries the price lock from GetPrice.

---

### BookBaseResponseType / BookResponseType / ReservationResponseType / RecallBookingResponseType / ConfirmReservationResponseType

All booking responses share the same base structure.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookBaseResponseType | Passengers | TRANSPORT CUSTOMER | — | partial |
| BookBaseResponseType | ContactDetails | TRANSPORT CUSTOMER | contactDetails | partial |
| BookBaseResponseType | Pets | — | — | none |
| BookBaseResponseType | Vehicles | — | — | partial |
| BookBaseResponseType | FerryComponents | CUSTOMER PURCHASE PACKAGE | — | partial — confirmed booking details per sailing |
| BookBaseResponseType | QrCode | TRAVEL DOCUMENT | — | partial — QR code as a travel document materialisation |
| BookBaseResponseType | Messages | — | — | none — operational messages |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Transmodel Customer), TRANSPORT CUSTOMER (Transmodel Customer), TRAVEL DOCUMENT (Transmodel Customer)
- **conditions**: The booking response confirms the CUSTOMER PURCHASE PACKAGE; the optional QrCode is a materialisation of a TRAVEL DOCUMENT issued for the booking.

---

### BookingReferenceRequestType / BookingReferenceResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookingReferenceRequestType | Reference | CUSTOMER PURCHASE PACKAGE | id | exact — unique booking reference |
| BookingReferenceRequestType | Version | CUSTOMER PURCHASE PACKAGE | version | partial |
| BookingReferenceResponseType | Reference | CUSTOMER PURCHASE PACKAGE | id | exact |
| BookingReferenceResponseType | Version | CUSTOMER PURCHASE PACKAGE | version | partial |
| BookingReferenceResponseType | BookingStatus | CUSTOMER PURCHASE STATUS | — | partial; see BookingStatusType |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Transmodel Customer), CUSTOMER PURCHASE STATUS (Transmodel Customer)
- **conditions**: The Reference/Version pair uniquely identifies a CUSTOMER PURCHASE PACKAGE in the awarding system; BookingStatus tracks the lifecycle state of that package.

---

### BookingStatusType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Reserved | CUSTOMER RESERVATION CREATION EVENT | partial — temporary hold without binding commitment |
| Booked | CUSTOMER PURCHASE PACKAGE (status: confirmed) | exact — binding booking |
| Paid | CUSTOMER PURCHASE PACKAGE (status: paid) | exact |
| Partially Consumed | CUSTOMER PURCHASE PACKAGE (status: partiallyConsumed) | partial |
| Fully Consumed | CUSTOMER PURCHASE PACKAGE (status: fullyConsumed) | partial |
| Cancelled | CUSTOMER RESERVATION CANCELLATION EVENT | exact |
| Readonly | — | none — API-level restriction; no Transmodel equivalent |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Transmodel Customer), CUSTOMER RESERVATION CREATION EVENT (Transmodel Customer), CUSTOMER RESERVATION CANCELLATION EVENT (Transmodel Customer)
- **conditions**: BookingStatusType enumerates the lifecycle states of a booking; most states correspond to status values of a CUSTOMER PURCHASE PACKAGE; Reserved and Cancelled map to specific Transmodel reservation events.

---

### BookResponseLinkType / BookResponseLinksType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BookResponseLinkType | Label | — | — | none — operator-defined link label |
| BookResponseLinkType | URI | — | — | none — post-booking deep-link URL |
| BookResponseLinkType | Description | — | — | none |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: Post-booking hyperlinks provided by the operator for further actions (e.g., seat selection, check-in); these are operational deep-links with no Transmodel representation.

---

### RecallBookingRequestType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| RecallBookingRequestType | BookingReference | CUSTOMER PURCHASE PACKAGE | id | exact — retrieval of an existing booking |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Transmodel Customer)
- **conditions**: Retrieves the full details of an existing booking by its unique reference; the result is a BookBaseResponseType representing the persisted CUSTOMER PURCHASE PACKAGE.

---

### ConfirmReservationRequestType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ConfirmReservationRequestType | BookingReference | CUSTOMER PURCHASE PACKAGE | id | partial — converts a reservation into a confirmed booking |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Transmodel Customer), CUSTOMER RESERVATION CREATION EVENT (Transmodel Customer)
- **conditions**: Converts a temporary reservation (status: Reserved) into a confirmed booking (status: Booked); triggers the transition from CUSTOMER RESERVATION CREATION EVENT to a confirmed CUSTOMER PURCHASE PACKAGE.

---

## 7. Cancellation

### GetCancelChargeRequestType / GetCancelChargeResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetCancelChargeRequestType | BookingReference | CUSTOMER PURCHASE PACKAGE | id | exact — reference for which to query cancellation fees |
| GetCancelChargeResponseType | Cost | CANCELLING | cancellationCharge | partial — returned cancellation fee amount |
| GetCancelChargeResponseType | BookingReference | CUSTOMER PURCHASE PACKAGE | id | exact |

x-semantics equivalent:
- **concepts**: CANCELLING (Transmodel Fare), CUSTOMER PURCHASE PACKAGE (Transmodel Customer)
- **conditions**: Queries the cancellation fee that would apply before actually cancelling; the returned Cost maps to the cancellationCharge attribute of a CANCELLING condition.

---

### CancelBookingRequestType / CancelBookingResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| CancelBookingRequestType | BookingReference | CUSTOMER RESERVATION CANCELLATION EVENT | — | exact |
| CancelBookingRequestType | Cost | CANCELLING | cancellationCharge | partial — expected cancellation charge to confirm |
| CancelBookingResponseType | BookingReference | CUSTOMER RESERVATION CANCELLATION EVENT | — | exact — confirms cancellation |
| CancelBookingResponseType | Cost | CANCELLING | cancellationCharge | partial — actual charge applied |

x-semantics equivalent:
- **concepts**: CUSTOMER RESERVATION CANCELLATION EVENT (Transmodel Customer), CANCELLING (Transmodel Fare)
- **conditions**: Submits and confirms the cancellation of a booking; creates a CUSTOMER RESERVATION CANCELLATION EVENT; the actual charge applied maps to the CANCELLING condition's cancellationCharge.

---

## 8. Passengers

### PassengerType (abstract) / PassengerQuoteType / PassengerBookType

`PassengerType` is the abstract base. `PassengerQuoteType` adds no extra attributes (used for pricing/search). `PassengerBookType` extends with personal identity, travel document, and accessibility attributes.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PassengerType | Id | TRANSPORT CUSTOMER | id | exact — passenger reference within the message |
| PassengerType | Age | USER PROFILE | maximumAge / minimumAge | partial — actual age at travel |
| PassengerType | DateOfBirth | TRANSPORT CUSTOMER | dateOfBirth | exact |
| PassengerType | Category | USER PROFILE | — | partial — Adult / Child / Infant; see PassengerCategoryCodeType |
| PassengerType | SubCategory | USER PROFILE | — | partial — operator-defined sub-category (student, resident, etc.) |
| PassengerType | LoyaltyCardId | CUSTOMER ACCOUNT | id | partial — loyalty card number |
| PassengerType | LoyaltyCardCategory | COMMERCIAL PROFILE | — | partial — loyalty membership tier |
| PassengerBookType | Gender | TRANSPORT CUSTOMER | gender | exact |
| PassengerBookType | Title | TRANSPORT CUSTOMER | — | partial — salutation (Mr/Mrs/Ms) |
| PassengerBookType | Forename | TRANSPORT CUSTOMER | firstName | exact |
| PassengerBookType | Surname | TRANSPORT CUSTOMER | lastName | exact |
| PassengerBookType | PassengerNationality | TRANSPORT CUSTOMER | nationality | exact |
| PassengerBookType | IdentityNumber | TRANSPORT CUSTOMER | — | partial — passport or ID card number |
| PassengerBookType | IdentityCategory | — | — | partial — Passport / National Identity Card / Alien Passport / Refugee Passport; relates to EU Passenger Directive data |
| PassengerBookType | IdentityExpiryDate | — | — | partial — document expiry; no Transmodel attribute |
| PassengerBookType | IdentityIssueCountry | — | — | partial — document issuing country |
| PassengerBookType | SpecialRequirements | ASSISTANCE SERVICE | — | partial — accessibility/disability classification; see SpecialRequirementsType |
| PassengerBookType | PlaceOfBirth | TRANSPORT CUSTOMER | — | partial |
| PassengerBookType | CountryOfBirth | TRANSPORT CUSTOMER | — | partial |
| PassengerBookType | VisaPermitNumber | — | — | none — VISA/residence permit number; no Transmodel equivalent |
| PassengerBookType | ICEPhoneNumber | TRANSPORT CUSTOMER | — | partial — emergency contact; no explicit Transmodel attribute |
| PassengerBookType | ICEEmail | TRANSPORT CUSTOMER | — | partial |
| PassengerBookType | QrCode | TRAVEL DOCUMENT | — | partial — per-passenger QR code |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Transmodel Customer), USER PROFILE (Transmodel Customer), CUSTOMER ACCOUNT (Transmodel Customer), COMMERCIAL PROFILE (Transmodel Customer), ASSISTANCE SERVICE (Transmodel Stops), TRAVEL DOCUMENT (Transmodel Customer)
- **conditions**: PassengerType is the FerryGateway representation of a TRANSPORT CUSTOMER; the Category/SubCategory attributes correspond to USER PROFILE fare eligibility criteria; PassengerBookType adds personal identity data required under the EU Passenger Directive.

---

### PassengerCategoryCodeType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Adult | USER PROFILE (adult) | exact |
| Child | USER PROFILE (child) | exact |
| Infant | USER PROFILE (infant) | exact |

x-semantics equivalent:
- **concepts**: USER PROFILE (Transmodel Customer)
- **conditions**: Standard passenger age-band classification; each value corresponds directly to a Transmodel USER PROFILE instance differentiated by age range.

---

### PassengerCategoryType / PassengerSubCategoryType / PassengerCategoriesType

Used in `GetPassengerAndVehicleTypesResponse` to describe the operator's defined passenger type definitions.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PassengerCategoryType | Category | USER PROFILE | typeOfUsage | partial |
| PassengerCategoryType | MinAge | USER PROFILE | minimumAge | exact |
| PassengerCategoryType | MaxAge | USER PROFILE | maximumAge | exact |
| PassengerSubCategoryType | Category | USER PROFILE | id | partial — operator sub-category code |
| PassengerSubCategoryType | Description | USER PROFILE | description | exact |

x-semantics equivalent:
- **concepts**: USER PROFILE (Transmodel Customer)
- **conditions**: Describes the operator's own passenger type taxonomy, including age-band boundaries; each category/sub-category corresponds to a USER PROFILE instance with defined minimumAge and maximumAge.

---

### PassengerTitleType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| PassengerTitleType (Mr/Mrs/Ms) | TRANSPORT CUSTOMER | partial — salutation field; Transmodel does not define a specific title enumeration |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Transmodel Customer)
- **conditions**: The salutation (Mr/Mrs/Ms) is a personal attribute of a TRANSPORT CUSTOMER; Transmodel holds no dedicated title enumeration.

---

### SpecialRequirementsType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Deaf / Blind / Blind and deaf | ASSISTANCE SERVICE | partial — sensory disability |
| Wheelchair / Electric wheelchair | ASSISTANCE SERVICE | partial — mobility equipment |
| Walking difficulties | ASSISTANCE SERVICE | partial |
| Medical equipment / Medication | ASSISTANCE SERVICE | partial |
| Power required | — | none — power socket need; no Transmodel equivalent |
| Other | ASSISTANCE SERVICE | partial |

x-semantics equivalent:
- **concepts**: ASSISTANCE SERVICE (Transmodel Stops)
- **conditions**: SpecialRequirementsType enumerates accessibility and disability needs; each value triggers the provision of an appropriate ASSISTANCE SERVICE during the voyage.

---

### IdentityCategoryType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| IdentityCategoryType | — | none — identity document type classification has no direct Transmodel concept; relates to EU Passenger Directive compliance |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: IdentityCategoryType (Passport / National Identity Card / Alien Passport / Refugee Passport) is a regulatory data field mandated by the EU Passenger Directive; it has no corresponding concept in Transmodel.

---

### GenderType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| GenderType (Male/Female/Other) | TRANSPORT CUSTOMER | partial — Transmodel includes gender as an attribute of TRANSPORT CUSTOMER |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Transmodel Customer)
- **conditions**: Gender is a personal attribute of a TRANSPORT CUSTOMER; the FGW enumeration (Male/Female/Other) aligns with the gender attribute in Transmodel.

---

## 9. Pets

### PetType / PetsType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PetType | Id | — | — | none — no Transmodel concept for a pet as a transported entity |
| PetType | Category | — | — | none — pet type (SmallDog/BigDog/Cat/Other); no Transmodel equivalent |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: FerryGateway models pets as first-class booking entities; Transmodel has no concept for animals as transported objects distinct from passengers or freight.

---

### PetCategoryType / PetCategoriesType / PetsTransportOptionsType / PetTransportOptionsType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| PetCategoryType | SmallDog / BigDog / Cat / Other | — | — | none |
| PetTransportOptionsType | Pet Friendly Cabin / Vehicle / Kennel Service / Own Kennel | LOCAL SERVICE | — | partial — pet transport modes are a form of local service; no exact Transmodel match |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops)
- **conditions**: Pet transport options (cabin, vehicle, kennel) are characterised as forms of LOCAL SERVICE; the specific categories (SmallDog, BigDog, etc.) have no direct Transmodel analogue.

---

## 10. Vehicles

### VehicleType / LeadVehicleType / TrailerType / VehicleBaseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| VehicleType | Id | — | — | none — reference Id within message; not a transport vehicle in the Transmodel sense |
| VehicleBaseType | Height | VEHICLE TYPE | vehicleHeight | exact |
| VehicleBaseType | Length | VEHICLE TYPE | vehicleLength | exact |
| VehicleBaseType | Width | VEHICLE TYPE | vehicleWidth | exact |
| VehicleBaseType | OperatorCode | VEHICLE TYPE | id | partial — operator-defined vehicle type code |
| VehicleBaseType | Registration | VEHICLE | registrationNumber | exact |
| LeadVehicleType | Code | VEHICLE TYPE | id | partial — FGW standard lead vehicle category code |
| LeadVehicleType | IsHireCar | — | — | none — hire-car flag; no Transmodel equivalent |
| LeadVehicleType | BikeOnBack | — | — | none |
| LeadVehicleType | RoofLoad | — | — | none |
| LeadVehicleType | Make | VEHICLE | vehicleModel | partial — vehicle manufacturer |
| LeadVehicleType | Model | VEHICLE | vehicleModel | partial |
| LeadVehicleType | FuelType | VEHICLE TYPE | — | partial — fuel classification (Electric/Liquid/Liquified/Compressed Gas/Other); relates to vehicle type characteristics |
| TrailerType | Code | VEHICLE TYPE | id | partial — Trailer / Caravan classification |

x-semantics equivalent:
- **concepts**: VEHICLE (Transmodel Vehicle), VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: FerryGateway VehicleType represents a passenger-owned road vehicle being transported on the ferry; its dimensional attributes (Height, Length, Width) map directly to VEHICLE TYPE; Registration maps to VEHICLE. IsHireCar, BikeOnBack, and RoofLoad are FGW-specific operational attributes.

---

### LeadVehicleCodeType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Car / Van / Motorhome / Minibus / Motorcycle / MotorCycleWithSidecar / Bicycle / Freight / VehicleCombination | VEHICLE TYPE | partial — standard vehicle type classifications |

x-semantics equivalent:
- **concepts**: VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: LeadVehicleCodeType provides the FGW standard classification for the lead (towing) vehicle; each value corresponds to a VEHICLE TYPE instance.

---

### VehicleCategoryType / LeadVehicleCategoryType / TrailerCategoryType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| VehicleCategoryType | OperatorCode | VEHICLE TYPE | id | partial |
| VehicleCategoryType | Description | VEHICLE TYPE | description | exact |
| VehicleCategoryType | MinLength / MaxLength | VEHICLE TYPE | vehicleLength | partial — defines the dimensional range for this category |
| VehicleCategoryType | MinHeight / MaxHeight | VEHICLE TYPE | vehicleHeight | partial |
| LeadVehicleCategoryType | Code | VEHICLE TYPE | id | partial — FGW standard code for category |
| TrailerCategoryType | Code | VEHICLE TYPE | id | partial — Trailer / Caravan |

x-semantics equivalent:
- **concepts**: VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: Describes the operator's accepted vehicle categories with dimensional bounds; each category maps to a VEHICLE TYPE with its dimensional constraints.

---

### VehicleTransportOptionsType / TrailerCodeType / FuelType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| VehicleTransportOptionsType | VEHICLE TYPE | partial — declares which vehicle categories are accepted on the route |
| TrailerCodeType (Trailer/Caravan) | VEHICLE TYPE | partial |
| FuelType (Electric or Hybrid / Liquid / Liquified / Compressed Gas / Other) | VEHICLE TYPE | partial — fuel type classification for ferry deck safety/hazmat rules |

x-semantics equivalent:
- **concepts**: VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: VehicleTransportOptionsType declares which VEHICLE TYPE categories are accepted on a route; FuelType further qualifies the VEHICLE TYPE for ferry deck safety and hazmat compliance purposes.

---

## 11. On-Board Services

### ServiceOptionType / ServiceRequestType / ServiceResponseType (abstract bases)

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ServiceOptionType | Code | LOCAL SERVICE | id | exact — service code |
| ServiceOptionType | Category | LOCAL SERVICE / CATERING SERVICE | — | partial — service category classification |
| ServiceOptionType | QuantityAvailable | — | — | none — availability count; no Transmodel attribute |
| ServiceOptionType | UnitCost | FARE PRODUCT PRICE | amount | partial — price per unit of service |
| ServiceOptionType | Description | LOCAL SERVICE | description | exact |
| ServiceOptionType | ServiceRule | — | — | none; see ServiceRuleType |
| ServiceOptionType | ServiceRuleGroup | — | — | none |
| ServiceOptionType | Medias | — | — | none |
| ServiceRequestType | Code | LOCAL SERVICE | id | exact |
| ServiceRequestType | Quantity | — | — | none — quantity requested |
| ServiceResponseType | Code | LOCAL SERVICE | id | exact |
| ServiceResponseType | Quantity | — | — | none |
| ServiceResponseType | TotalCost | FARE PRODUCT PRICE | amount | partial — total for requested quantity |
| ServiceResponseType | Category | LOCAL SERVICE | — | partial |
| ServiceResponseType | OperatorAdded | — | — | none — system-default selection flag |
| ServiceResponseType | IsMandatory | BOOKING POLICY | — | partial — mandatory service flag |
| ServiceResponseType | Description | LOCAL SERVICE | description | exact |
| ServiceResponseType | AllotmentAgreementId | — | — | none |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops), CATERING SERVICE (Transmodel Stops), FARE PRODUCT PRICE (Transmodel Fare), BOOKING POLICY (Transmodel Fare)
- **conditions**: ServiceOptionType is the abstract base for all purchasable on-board and land services; Code and Description map directly to LOCAL SERVICE; UnitCost/TotalCost map to FARE PRODUCT PRICE amounts.

---

### ServiceCategoryType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Accommodation | LOCAL SERVICE (accommodation) | partial — cabin/berth reservation service |
| Meal | CATERING SERVICE | exact |
| Internet | COMMUNICATION SERVICE | partial |
| Cinema / Conference / Lounge / Spa / Excursion | LOCAL SERVICE | partial — specific on-board facility types |
| Kennel | — | none — pet kennel service; no Transmodel equivalent |
| Other | LOCAL SERVICE | partial |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops), CATERING SERVICE (Transmodel Stops), COMMUNICATION SERVICE (Transmodel Stops)
- **conditions**: ServiceCategoryType classifies on-board services; Meal maps exactly to CATERING SERVICE; all others map to LOCAL SERVICE sub-types except Kennel, which has no Transmodel equivalent.

---

### ServiceRuleType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| OnePerBooking / OnePerSailing | BOOKING POLICY | partial — service purchase quantity constraint |
| OnePerPassenger / OnePerAdult | USER PROFILE | partial — per-person constraint |
| OnePerVehicle | — | none |
| OnePerPet | — | none |

x-semantics equivalent:
- **concepts**: BOOKING POLICY (Transmodel Fare), USER PROFILE (Transmodel Customer)
- **conditions**: ServiceRuleType constrains how many times a service may be purchased; booking-level and sailing-level rules correspond to BOOKING POLICY; per-passenger rules relate to USER PROFILE eligibility.

---

### OnBoardAccommodationServiceOptionType / RequestType / ResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OnBoardAccommodationService\* | PassengerRefs | TRANSPORT CUSTOMER | id | partial — passengers assigned to this accommodation |
| OnBoardAccommodationService\* | Berths | — | — | none — berth count; no Transmodel equivalent |
| OnBoardAccommodationService\* | MaxAdults / MaxOccupancy / MinOccupancy | — | — | none — occupancy limits |
| OnBoardAccommodationService\* | IsInfantsOK | — | — | none |
| OnBoardAccommodationService\* | IsAccessible | ASSISTANCE SERVICE | — | partial — accessibility flag for the cabin |
| OnBoardAccommodationService\* | IsPetAllowed | — | — | none |
| OnBoardAccommodationService\* | IsAllergyFriendly | — | — | none |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops), TRANSPORT CUSTOMER (Transmodel Customer), ASSISTANCE SERVICE (Transmodel Stops)
- **conditions**: On-board cabin/berth accommodation is a LOCAL SERVICE sub-type; passengers are assigned to a cabin via PassengerRefs; IsAccessible maps to ASSISTANCE SERVICE provision.

---

### OnBoardMealServiceOptionType / RequestType / ResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OnBoardMealService\* | PassengerCategory | USER PROFILE | — | partial |
| OnBoardMealService\* | Time | — | — | none — meal service time slot |
| OnBoardMealService\* | FacilityCode | — | — | none — on-board location code |
| OnBoardMealService\* | MealSpecification | CATERING SERVICE | — | partial — specifies diet types and meal categories |
| OnBoardMealService\* | PassengerRefs | TRANSPORT CUSTOMER | id | partial |

x-semantics equivalent:
- **concepts**: CATERING SERVICE (Transmodel Stops), USER PROFILE (Transmodel Customer), TRANSPORT CUSTOMER (Transmodel Customer)
- **conditions**: On-board meal service is a CATERING SERVICE; MealSpecification further qualifies diet type; PassengerCategory links the service to a USER PROFILE age band.

---

### MealType / OnBoardMealServiceSpecification / DietType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| MealType | Id | CATERING SERVICE | id | partial |
| MealType | DietType | CATERING SERVICE | — | partial — diet classification (Meat/Fish/Vegetarian/Vegan/Celiak/Jewish/Islamic) |
| MealType | Description | CATERING SERVICE | description | exact |
| MealType | PassengerCategory | USER PROFILE | — | partial — adult/child meal distinction |

x-semantics equivalent:
- **concepts**: CATERING SERVICE (Transmodel Stops), USER PROFILE (Transmodel Customer)
- **conditions**: MealType represents a specific meal option offered as a CATERING SERVICE; diet classifications (Vegetarian, Vegan, etc.) are qualifiers on the CATERING SERVICE; PassengerCategory links to USER PROFILE for pricing differentiation.

---

### OnBoardServiceOptionType / RequestType / ResponseType

Generic on-board service (not accommodation or meal).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| OnBoardService\* | PassengerCategory | USER PROFILE | — | partial |
| OnBoardService\* | Time | — | — | none — service time |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops), USER PROFILE (Transmodel Customer)
- **conditions**: Generic on-board service (cinema, lounge, spa, etc.); modelled as a LOCAL SERVICE; PassengerCategory links to USER PROFILE for per-category pricing.

---

### ServicesOptionsType / ServicesRequestType / ServicesResponseType

Container types grouping all service sub-types.

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| ServicesOptionsType | LOCAL SERVICE / CATERING SERVICE | partial — catalogue of available services per sailing |
| ServicesRequestType | LOCAL SERVICE | partial — selected services to book |
| ServicesResponseType | LOCAL SERVICE | partial — confirmed/included services |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops), CATERING SERVICE (Transmodel Stops)
- **conditions**: These container types aggregate all service sub-types for a sailing; ServicesOptionsType is the offered service catalogue; ServicesRequestType carries the passenger's selection; ServicesResponseType confirms the booked services.

---

### ServiceModeCategoryType / ServiceModeType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| ServiceModeCategoryType (OnBoard / OnBoardMeal / OnBoardAccommodations / Land) | LOCAL SERVICE | partial — service mode filter |
| ServiceModeType | LOCAL SERVICE | partial — space-delimited list of service modes |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops)
- **conditions**: ServiceModeCategoryType filters service queries by mode (on-board generic, meal, accommodation, or land); each mode corresponds to a LOCAL SERVICE sub-category.

---

### GetServicesRequestType / GetServicesResponseType

Top-level message for querying available on-board and land services for a given sailing and passenger/vehicle set.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetServicesRequestType | Context | — | — | partial — session/operator context; see ContextType |
| GetServicesRequestType | Passengers | TRANSPORT CUSTOMER / USER PROFILE | — | partial — passenger list with categories; see PassengerType |
| GetServicesRequestType | Pets | — | — | none — no Transmodel concept for pets |
| GetServicesRequestType | Vehicles | VEHICLE TYPE | — | partial — vehicle dimensions; see VehicleType |
| GetServicesRequestType | FerryComponent | SERVICE JOURNEY | — | partial — identifies the sailing for which services are requested |
| GetServicesRequestType | Mode | LOCAL SERVICE | — | partial — filters service types: OnBoard, OnBoardMeal, OnBoardAccommodations, Land |
| GetServicesResponseType | FerryComponents | LOCAL SERVICE | — | partial — per-sailing service availability; see ServicesResponseType |

x-semantics equivalent:
- **concepts**: SERVICE JOURNEY (Transmodel Journey), LOCAL SERVICE (Transmodel Stops), TRANSPORT CUSTOMER (Transmodel Customer), USER PROFILE (Transmodel Customer), VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: Retrieves the available services (on-board, meal, accommodation, land) for a specific sailing and party combination; the Mode attribute selects a subset of LOCAL SERVICE categories; each returned FerryComponent contains the service options for one DATED VEHICLE JOURNEY.

---

### MandatoryFacilityType / MandatoryFacilitiesType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Vehicle | BOOKING POLICY | partial — vehicle must be present on this sailing |
| Meal | BOOKING POLICY / CATERING SERVICE | partial — mandatory meal booking |
| Seating | BOOKING POLICY | partial — seat reservation mandatory |
| Cabin | BOOKING POLICY | partial — cabin booking mandatory |
| Passport / National Identity Card or Passport | — | none — document requirement; no Transmodel equivalent |
| VISA | — | none |
| EU Passenger Directive | — | none — regulatory data collection requirement; no Transmodel equivalent |

x-semantics equivalent:
- **concepts**: BOOKING POLICY (Transmodel Fare), CATERING SERVICE (Transmodel Stops)
- **conditions**: MandatoryFacilitiesType declares facilities that must be booked or presented on a route or sailing; booking-related mandatory facilities correspond to BOOKING POLICY constraints; document requirements (Passport, VISA, EU Passenger Directive) are regulatory obligations with no Transmodel equivalent.

---

## 12. Land Services & Transfer Services

### LandServiceOptionType / LandServiceRequestType / LandServiceResponseType

Shore-based services available in connection with a sailing (e.g., hotel, parking).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| LandService\* | PassengerCategory | USER PROFILE | — | partial |
| LandService\* | FromDateTime / ToDateTime | VALIDITY CONDITION | fromDate / toDate | partial — service validity window |

x-semantics equivalent:
- **concepts**: LOCAL SERVICE (Transmodel Stops), USER PROFILE (Transmodel Customer), VALIDITY CONDITION (Transmodel Common)
- **conditions**: Shore-based services (hotel, parking, transfers) are modelled as LOCAL SERVICEs; their validity window (FromDateTime/ToDateTime) corresponds to a VALIDITY CONDITION; PassengerCategory links to USER PROFILE.

---

### TransferServicesRequestType / TransferServicesResponseType / TransferServicesDetailsType / TransferServicesOptionsType

Connecting transport services (currently: bus transfers).

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| TransferServices\* | CONNECTION LEG | partial — the bus transfer is a connecting leg; maps to CONNECTION LEG or ACCESS LEG in Transmodel |

x-semantics equivalent:
- **concepts**: CONNECTION LEG (Transmodel Journey), ACCESS LEG (Transmodel Journey)
- **conditions**: Transfer services describe connecting bus legs to or from the ferry port; these map to CONNECTION LEG (for scheduled connections) or ACCESS LEG (for access-mode connections) in Transmodel.

---

### GetBusTransferRequestType / GetBusTransferResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetBusTransferRequestType | Passengers | TRANSPORT CUSTOMER | — | partial |
| GetBusTransferRequestType | Pets | — | — | none |
| GetBusTransferRequestType | FerryComponent | SERVICE JOURNEY | — | partial — sailing to connect from/to |

x-semantics equivalent:
- **concepts**: SERVICE JOURNEY (Transmodel Journey), TRANSPORT CUSTOMER (Transmodel Customer)
- **conditions**: Queries available bus transfer services connecting to a specific ferry sailing; FerryComponent identifies the SERVICE JOURNEY that the transfer connects with.

---

### BusTransferServiceType / BusTransferServiceOptionsType / BusTransferServiceRequestType / BusTransferServiceResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BusTransferService\* | Direction (ToSailing / FromSailing) | CONNECTION LEG | — | partial — directionality of the connecting leg |
| BusTransferService\* | BusId | LINE | id | partial — bus line identifier |
| BusTransferService\* | DepartPlace | SCHEDULED STOP POINT | id | partial |
| BusTransferService\* | DestinationPlace | SCHEDULED STOP POINT | id | partial |
| BusTransferService\* | DepartDateTime | VEHICLE JOURNEY | departureTime | exact |
| BusTransferServiceResponseType | ArriveDateTime | VEHICLE JOURNEY | arrivalTime | exact |
| BusTransferServiceResponseType | TotalCost | FARE PRICE | amount | partial |
| BusTransferServiceResponseType | Remark | — | — | none |
| BusTransferService\* | PassengerRefs | TRANSPORT CUSTOMER | id | partial |
| BusTransferService\* | Quantity | — | — | none |
| BusTransferService\* | Code | LOCAL SERVICE | id | partial |

x-semantics equivalent:
- **concepts**: CONNECTION LEG (Transmodel Journey), LINE (Transmodel Network), SCHEDULED STOP POINT (Transmodel Network), VEHICLE JOURNEY (Transmodel Journey), FARE PRICE (Transmodel Fare)
- **conditions**: BusTransferServiceType represents a single connecting bus leg as a CONNECTION LEG; Direction determines whether the leg is inbound (to the ferry) or outbound (from the ferry); BusId identifies the LINE.

---

### BusTransferDetailsResponseType / BusTransferDetailsUnitCostType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| BusTransferDetailsResponseType | DepartPlace / DestinationPlace | SCHEDULED STOP POINT | — | partial |
| BusTransferDetailsResponseType | DepartDateTime / ArriveDateTime | VEHICLE JOURNEY | departureTime / arrivalTime | exact |
| BusTransferDetailsUnitCostType | PassengerCategory | USER PROFILE | — | partial |
| BusTransferDetailsUnitCostType | Amount | FARE PRICE | amount | exact — per-category unit price |

x-semantics equivalent:
- **concepts**: SCHEDULED STOP POINT (Transmodel Network), VEHICLE JOURNEY (Transmodel Journey), FARE PRICE (Transmodel Fare), USER PROFILE (Transmodel Customer)
- **conditions**: Provides per-category pricing details for a bus transfer service; Amount is the per-passenger-category unit price corresponding to a FARE PRICE amount.

---

### TransferPlaceType / BusTransferPlaceType / BusTransferPlacesType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| TransferPlaceType | Id | SCHEDULED STOP POINT | id | partial |
| TransferPlaceType | Name | STOP PLACE | name | exact |
| BusTransferPlaceType | Id | SCHEDULED STOP POINT | id | partial |
| BusTransferPlaceType | Description | STOP PLACE | description | partial |

x-semantics equivalent:
- **concepts**: SCHEDULED STOP POINT (Transmodel Network), STOP PLACE (Transmodel Stops)
- **conditions**: Transfer place types identify bus pick-up and drop-off locations; Id corresponds to a SCHEDULED STOP POINT identifier; Name/Description correspond to the associated STOP PLACE.

---

### TransferServiceDirection

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| ToSailing | CONNECTION LEG (inbound) | partial — connecting leg to the ferry port |
| FromSailing | CONNECTION LEG (outbound) | partial — onward leg from the ferry port |

x-semantics equivalent:
- **concepts**: CONNECTION LEG (Transmodel Journey)
- **conditions**: TransferServiceDirection distinguishes whether a bus CONNECTION LEG carries passengers towards the ferry embarkation port (ToSailing) or away from the disembarkation port (FromSailing).

---

## 13. Invoicing

### GetInvoicesRequestType / GetInvoicesResponseType / InvoicesResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetInvoicesRequestType | OperatorCode | OPERATOR | id | partial |
| GetInvoicesRequestType | DateFrom / DateTo | VALIDITY CONDITION | fromDate / toDate | partial — invoice date range filter |
| InvoicesResponseType | Invoice (collection) | — | — | none — invoices are a financial accounting concept with no direct Transmodel equivalent |

x-semantics equivalent:
- **concepts**: OPERATOR (Transmodel Organisation), VALIDITY CONDITION (Transmodel Common)
- **conditions**: Retrieves invoices for an operator within a date range; the invoice list itself has no direct Transmodel equivalent as Transmodel does not model financial accounting documents.

---

### InvoiceResponseType / InvoiceBookingsResponseType / InvoiceBookingResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| InvoiceResponseType | Number | — | — | none — invoice number |
| InvoiceResponseType | Date | — | — | none — invoice issue date |
| InvoiceResponseType | DueDate | — | — | none — payment due date |
| InvoiceResponseType | Currency | — | — | partial — ISO currency code |
| InvoiceResponseType | Bookings | CUSTOMER PURCHASE PACKAGE | — | partial — bookings included in this invoice |
| InvoiceResponseType | InvoiceTotals | FARE PRICE | amount | partial — invoice payment totals |
| InvoiceBookingResponseType | BookingReference | CUSTOMER PURCHASE PACKAGE | id | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (Transmodel Customer), FARE PRICE (Transmodel Fare)
- **conditions**: An invoice aggregates one or more CUSTOMER PURCHASE PACKAGEs into a financial settlement document; the booking references within the invoice correspond to CUSTOMER PURCHASE PACKAGE identifiers.

---

### InvoiceTotalsType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| InvoiceTotalsType | TotalAmount | FARE PRICE | amount | exact |
| InvoiceTotalsType | CommissionAmount | FARE ORGANISATION ROLE | — | partial — agent commission |
| InvoiceTotalsType | TaxBase | FARE STRUCTURE ELEMENT | — | partial — taxable base amount |
| InvoiceTotalsType | TaxAmount | FARE STRUCTURE ELEMENT | — | partial — tax amount |
| InvoiceTotalsType | TaxPercentage | FARE STRUCTURE ELEMENT | — | partial — tax rate |

x-semantics equivalent:
- **concepts**: FARE PRICE (Transmodel Fare), FARE STRUCTURE ELEMENT (Transmodel Fare), FARE ORGANISATION ROLE (Transmodel Organisation)
- **conditions**: InvoiceTotalsType provides the financial summary of an invoice; TotalAmount maps to a FARE PRICE; tax sub-totals are FARE STRUCTURE ELEMENTs; CommissionAmount relates to the FARE ORGANISATION ROLE of a distribution agent.

---

### InvoicePaymentStatusType

| FGW Value | Transmodel Concept(s) | Notes |
|-----------|----------------------|-------|
| Issued | — | none — no Transmodel invoice payment lifecycle concept |
| Paid | — | none |
| Payment Delayed | — | none |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: InvoicePaymentStatusType tracks the payment lifecycle of an invoice; Transmodel does not model invoice payment states as it does not cover financial accounting.

---

## 14. Context & Session

### ContextType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ContextType | AgentAccountNumber | FARE PRODUCT RETAILER ROLE / ACCOUNT PROVIDER ROLE | id | partial — identifies the requesting agent/system |
| ContextType | TimeStamp | — | — | none — request timestamp; no Transmodel concept |
| ContextType | CountryCode | — | — | none — originating country of the agent; no direct Transmodel attribute |
| ContextType | Currency | FARE FRAME | defaultCurrency | partial — ISO 4217 currency for prices in the response |
| ContextType | LanguagePrefCode | — | — | none — language preference; Transmodel has no direct equivalent |
| ContextType | TransactionId | — | — | none — session correlation id |
| ContextType | ExternalBookingReference | CUSTOMER PURCHASE PACKAGE | id | partial — external reference from the requesting system |
| ContextType | User | — | — | none — operator user identifier |
| ContextType | OriginatingSystem | — | — | none — calling system identifier |

x-semantics equivalent:
- **concepts**: FARE PRODUCT RETAILER ROLE (Transmodel Organisation), ACCOUNT PROVIDER ROLE (Transmodel Organisation), FARE FRAME (Transmodel Fare), CUSTOMER PURCHASE PACKAGE (Transmodel Customer)
- **conditions**: ContextType carries session and agent identification metadata; AgentAccountNumber identifies the distribution agent as a FARE PRODUCT RETAILER ROLE or ACCOUNT PROVIDER ROLE; Currency maps to the defaultCurrency of a FARE FRAME.

---

### MessageRequestType / MessageResponseType (abstract message bases)

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| MessageRequestType | Context | — | — | partial — see ContextType |
| MessageRequestType | TPE | — | — | none — trading partner extension; see TradingPartnersExtensionType |
| MessageResponseType | Context | — | — | partial |
| MessageResponseType | Request | — | — | none — echo of original request XML |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent for the message envelope itself
- **conditions**: MessageRequestType and MessageResponseType are the abstract XML message bases for all FerryGateway operations; Context carries session information (see ContextType); the message envelope pattern has no corresponding concept in Transmodel.

---

### TradingPartnersExtensionType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| TradingPartnersExtensionType | — | none — bilateral extension point; no Transmodel equivalent |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: TradingPartnersExtensionType (TPE) is a bilateral extension mechanism agreed between specific trading partners; it carries proprietary data outside the standard schema and has no Transmodel representation.

---

### ReturnedRequestType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| ReturnedRequestType | — | none — echo of the request XML for debugging; no Transmodel concept |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: ReturnedRequestType echoes the original request XML back in the response for diagnostic and traceability purposes; this is a protocol-level construct with no semantic Transmodel equivalent.

---

## 15. Messaging & Errors

### MessageType / MessagesType / MessageSeverityType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| MessageType | Code | — | — | none — FGW-defined or SLA-defined error/info code |
| MessageType | Description | — | — | none |
| MessageType | Severity | — | — | none — Fatal / Recoverable / Warning / Information |
| MessageType | Location | — | — | none — XPath pointer into request |
| MessageType | xml:lang | — | — | none — language of message text |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: MessageType carries operational, warning, and error feedback in responses; the severity enumeration (Fatal / Recoverable / Warning / Information) and XPath location pointer are protocol-level constructs with no Transmodel representation.

---

## 16. Media

### MediaType / MediasType / MediaCategoryType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| MediaType | Type | — | — | none — MIME type; no Transmodel equivalent |
| MediaType | ByteSize | — | — | none |
| MediaType | Priority | — | — | none |
| MediaType | URI | — | — | none — media URL |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: MediaType carries links to images, PDFs, and other binary assets attached to routes, sailings, or services; Transmodel does not model media attachments.

---

## 17. QR Codes & Travel Documents

### QrCodeType / QrCodesType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| QrCodeType | Data | TRAVEL DOCUMENT | — | partial — QR code is a materialisation of a TRAVEL DOCUMENT in digital form |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Transmodel Customer)
- **conditions**: QrCodeType carries the raw QR code data (base64 or URI) used as a boarding pass or ticket; it is a digital materialisation of a TRAVEL DOCUMENT issued against a CUSTOMER PURCHASE PACKAGE or individual passenger.

---

### RefType / RefsType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| RefType | Id | — | — | partial — intra-message reference to another element (passenger, pet, vehicle); analogous to Transmodel cross-reference by id |
| RefType | QrCode | TRAVEL DOCUMENT | — | partial — per-reference QR code |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Transmodel Customer)
- **conditions**: RefType is an intra-message cross-reference mechanism linking sailings to passengers, pets, or vehicles; the optional QrCode attribute attaches an individual TRAVEL DOCUMENT to the referenced entity.

---

## 18. Product Information

### ProductInformationType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ProductInformationType | DepartureInformation | — | — | none — pre-departure passenger information text |
| ProductInformationType | OnBoardInformation | — | — | none — on-board passenger information text |
| ProductInformationType | ArrivalInformation | — | — | none — post-arrival information text |
| ProductInformationType | MarketingInformation | — | — | none — marketing copy; no Transmodel equivalent |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: ProductInformationType carries passenger-facing informational text (pre-departure, on-board, post-arrival) and marketing copy; Transmodel has no concept for operator-authored passenger information texts of this nature.

---

## 19. Ship / Vessel

### ShipType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ShipType | ShipName | VEHICLE | name | exact — vessel name |
| ShipType | ShipIMO | VEHICLE | registrationNumber | partial — IMO number as vessel identifier |
| ShipType | IsFastCraft | VEHICLE TYPE | — | partial — fast craft flag (hydrofoil, catamaran, etc.) |
| ShipType | Medias | — | — | none |

x-semantics equivalent:
- **concepts**: VEHICLE (Transmodel Vehicle), VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: ShipType represents the ferry vessel as a VEHICLE instance of a VEHICLE TYPE; ShipName maps to VEHICLE name; ShipIMO maps to VEHICLE registrationNumber; IsFastCraft is a VEHICLE TYPE characteristic distinguishing high-speed craft from conventional ferries.

---

## 20. Contact Details

### ContactDetailsType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ContactDetailsType | Title | TRANSPORT CUSTOMER | — | partial |
| ContactDetailsType | Forename | TRANSPORT CUSTOMER | firstName | exact |
| ContactDetailsType | Surname | TRANSPORT CUSTOMER | lastName | exact |
| ContactDetailsType | AddressLine1 / AddressLine2 / AddressLine3 | TRANSPORT CUSTOMER | address | partial |
| ContactDetailsType | City | TRANSPORT CUSTOMER | address/town | partial |
| ContactDetailsType | CountryCode | TRANSPORT CUSTOMER | address/countryRef | exact |
| ContactDetailsType | ZipPostCode | TRANSPORT CUSTOMER | address/postCode | exact |
| ContactDetailsType | MobileNumber | TRANSPORT CUSTOMER | contactDetails/phone | exact |
| ContactDetailsType | TelephoneNumber | TRANSPORT CUSTOMER | contactDetails/phone | partial — non-mobile phone |
| ContactDetailsType | Email | TRANSPORT CUSTOMER | contactDetails/email | exact |
| ContactDetailsType | PassengerId | TRANSPORT CUSTOMER | id | partial — links contact to a passenger in the same message |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Transmodel Customer)
- **conditions**: ContactDetailsType carries the lead booker's contact information; all attributes map to the address and contactDetails sub-structures of a TRANSPORT CUSTOMER; PassengerId optionally links the contact person to a passenger in the same message.

---

## 21. Utility Types

### IdType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| IdType | — | partial — alphanumeric identifier (max 36 chars) used for intra-message cross-references; corresponds to Transmodel id attribute pattern |

x-semantics equivalent:
- **concepts**: No direct Transmodel concept (corresponds to the id attribute pattern)
- **conditions**: IdType is a schema-level primitive used to assign unique identifiers to passengers, pets, and vehicles within a single message; it corresponds to the Transmodel convention of referencing entities by id but is not itself a named Transmodel concept.

---

### RestrictedStringType

| Concept | Transmodel Concept(s) | Notes |
|---------|----------------------|-------|
| RestrictedStringType | — | none — string max 256 chars; a schema constraint, not a semantic concept |

x-semantics equivalent:
- **concepts**: No direct Transmodel equivalent
- **conditions**: RestrictedStringType is a schema constraint (xs:string with maxLength=256) applied to most string attributes in the FerryGateway schema; it has no semantic meaning in Transmodel.

---

### GetPassengerAndVehicleTypesRequestType / GetPassengerAndVehicleTypesResponseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GetPassengerAndVehicleTypesRequestType | OperatorCode | OPERATOR | id | partial — queries the type definitions for a specific operator |
| GetPassengerAndVehicleTypesResponseType | PassengerCategories | USER PROFILE | — | partial — operator's defined passenger age-band categories |
| GetPassengerAndVehicleTypesResponseType | LeadVehicleCategories | VEHICLE TYPE | — | partial — operator's defined lead vehicle dimension categories |
| GetPassengerAndVehicleTypesResponseType | TrailerVehicleCategories | VEHICLE TYPE | — | partial — operator's defined trailer dimension categories |

x-semantics equivalent:
- **concepts**: OPERATOR (Transmodel Organisation), USER PROFILE (Transmodel Customer), VEHICLE TYPE (Transmodel Vehicle)
- **conditions**: Returns the operator's master reference data for passenger and vehicle type classifications; PassengerCategories correspond to USER PROFILE age-band definitions; LeadVehicleCategories and TrailerVehicleCategories correspond to VEHICLE TYPE dimensional range definitions.

---

## Summary

| Domain | FGW Types | Primary Transmodel Concepts |
|--------|-----------|----------------------------|
| Network/Geography | AbstractRouteType, RouteType, RoutesType, PortCodeType | LINE, ROUTE, STOP PLACE, SCHEDULED STOP POINT |
| Timetable | GetTimeTablesRequest/Response, TimetableSailingInfo\* | DATED VEHICLE JOURNEY, VEHICLE JOURNEY |
| Journey search | GetSailingsRequest/Response, SailingSearchCriterionType, SailingInfo\* | SERVICE JOURNEY, DATED VEHICLE JOURNEY, CONNECTION |
| Pricing/Fare | GetPriceRequest/Response, CostType, CostDetailsType, FareDetailsType | FARE PRICE, FARE PRODUCT, FARE STRUCTURE ELEMENT, SALES OFFER PACKAGE |
| Promotions | OfferCodeType, OfferCodeDetailType | SALES OFFER PACKAGE, SALE DISCOUNT RIGHT |
| Booking/Reservation | BookRequest/Response, ReservationRequest/Response, BookingReference\*, BookingStatusType | CUSTOMER PURCHASE PACKAGE, CUSTOMER RESERVATION CREATION EVENT |
| Cancellation | CancelBookingRequest/Response, GetCancelChargeRequest/Response | CUSTOMER RESERVATION CANCELLATION EVENT, CANCELLING |
| Passenger | PassengerType, PassengerBookType, PassengerCategoryType | TRANSPORT CUSTOMER, USER PROFILE |
| Vehicles | VehicleType, LeadVehicleType, TrailerType, VehicleCategoryType | VEHICLE TYPE, VEHICLE |
| On-board services | Service\*Type, OnBoardAccommodation\*, OnBoardMeal\*, OnBoardService\* | LOCAL SERVICE, CATERING SERVICE |
| Land/Transfer services | LandService\*, BusTransferService\*, TransferServices\* | LOCAL SERVICE, CONNECTION LEG, ACCESS LEG |
| Invoicing | InvoiceResponseType, InvoiceTotalsType | CUSTOMER PURCHASE PACKAGE, FARE PRICE |
| Context/Session | ContextType, MessageRequestType, MessageResponseType | FARE PRODUCT RETAILER ROLE, ACCOUNT PROVIDER ROLE |
| Vessel | ShipType | VEHICLE, VEHICLE TYPE |
| Contact | ContactDetailsType | TRANSPORT CUSTOMER |
| QR / Travel doc | QrCodeType, RefType | TRAVEL DOCUMENT |
