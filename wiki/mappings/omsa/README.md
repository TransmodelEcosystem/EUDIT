# OMSA → Transmodel Mapping

## Overview

This document maps the concepts and attributes of the **OMSA Common Booking API** (version 0.1.0) to the **Transmodel** reference data model.

OMSA (Open MaaS Standard Architecture) is a Mobility-as-a-Service interoperability standard built on OGC API Processes and GeoJSON. It covers offer search, offer selection, package management, traveller management, asset assignment, ancillary services, refund handling, and travel document retrieval. The API follows an asynchronous process model (OGC-callback pattern).

The OMSA YAML specification contains inline `x-tm` and `x-semantics` annotations on many schemas. This document organises those annotations into the standard project mapping table format.

Match quality is indicated in the **Notes** column:
- **exact** — one-to-one semantic correspondence
- **partial** — overlapping scope; some attributes or nuances differ
- **none** — no Transmodel equivalent; OMSA concept is standard-specific

---

## 1. package

The central entity in OMSA. A `package` represents an agreement between traveller and transport operator to execute a set of legs according to a specification, including all conditions and price.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| package | (object) | CUSTOMER PURCHASE PACKAGE | — | exact — completed purchase of a set of SALES OFFER PACKAGEs |
| package | (object) | TRAVEL OFFER PACKAGE | — | partial — when offered but not yet purchased |
| package | id | CUSTOMER PURCHASE PACKAGE | id | exact |
| package | status | CUSTOMER PURCHASE STATUS | — | exact — see packageStatus below |
| package | price | FARE PRICE | amount | exact |
| package | offers | SALES OFFER PACKAGE | — | partial — list of selected offers within the package |
| package | travellers | TRANSPORT CUSTOMER / USER PROFILE | — | partial — travellers associated with the package |
| package | guarantees | — | — | none — OMSA-specific service guarantee contracts |
| package | from / to / via | SCHEDULED STOP POINT / PLACE | — | partial — origin, destination, and via points |
| package | startTime / endTime | VALIDITY CONDITION | fromDate / toDate | partial — travel time window |

**x-semantics:**
- **concepts**: CUSTOMER PURCHASE PACKAGE (Sales & Distribution), TRAVEL OFFER PACKAGE (Fare Structure)
- **conditions**: `package` maps to CUSTOMER PURCHASE PACKAGE once purchased; to TRAVEL OFFER PACKAGE while still an offer. Leg contents map to SERVICE JOURNEY legs.

---

## 2. packageStatus

| OMSA Value | Transmodel Concept(s) | Notes |
|------------|----------------------|-------|
| offered | CUSTOMER PURCHASE STATUS (offered) | partial — package presented as an option |
| selected | CUSTOMER PURCHASE STATUS | partial — package chosen for modification/purchase |
| pending | CUSTOMER PURCHASE STATUS (booked) | partial — purchase initiated but not yet confirmed |
| confirmed | CUSTOMER PURCHASE STATUS (confirmed) | exact — purchase confirmed by both parties |
| released | — | none — internal archiving state; no Transmodel equivalent |
| expired | CUSTOMER PURCHASE STATUS (expired) | partial — offer expired before completion |
| cancelled | CUSTOMER RESERVATION CANCELLATION EVENT | exact |
| refund_claimed | EXCHANGING / CANCELLING | partial — refund claim submitted |
| refunded | CANCELLING | partial — refund paid out |
| started | CUSTOMER PURCHASE STATUS (activated) | partial — trip execution begun |
| ended | CUSTOMER PURCHASE STATUS (consumed) | exact — trip completed |

---

## 3. offer

An `offer` is a priced combination of legs, ancillaries, and products available for selection.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| offer | (object) | SALES OFFER PACKAGE | — | exact |
| offer | id | SALES OFFER PACKAGE | id | exact |
| offer | properties/legs | PT RIDE LEG / SERVICE JOURNEY | — | partial — legs within the offer |
| offer | properties/ancillaries | LOCAL SERVICE / ANCILLARY | — | partial — supplementary services |
| offer | properties/products | FARE PRODUCT | — | partial — fare products included in the offer |
| offer | properties/price | FARE PRICE | amount | exact |
| offer | properties/expiryTime | PURCHASE WINDOW | toDate | partial — offer validity deadline |
| offer | properties/guarantees | — | — | none — service guarantee commitments |

---

## 4. leg

A `leg` is a single planned consumption of a product within a package — one journey segment on one mode.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| leg | (object) | LEG | — | exact |
| leg | id | LEG | id | exact |
| leg | sequenceNumber | LEG | order | partial — sequence within package |
| leg | traveller | TRANSPORT CUSTOMER | id | partial — traveller assigned to this leg |
| leg | state | — | — | partial — see legState below |
| leg | price | FARE PRICE | amount | exact — final price of the leg |
| leg | products | FARE PRODUCT | id | partial — references to products from the offer |
| leg | ancillaries | LOCAL SERVICE | id | partial — supplementary service references |
| leg | mode | MODE | — | exact — transport mode (NeTEx-based) |
| leg | assets | VEHICLE | id | partial — physical assets used to execute the leg |
| leg | operator | OPERATOR | id | partial |
| leg | from / to / via | SCHEDULED STOP POINT | — | partial — journey endpoints |
| leg | startTime / endTime | DEPARTURE / ARRIVAL | departureTime / arrivalTime | exact |

---

## 5. legState

| OMSA Value | Transmodel Concept(s) | Notes |
|------------|----------------------|-------|
| not_started | CUSTOMER PURCHASE STATUS (activated) | partial — leg confirmed, not yet started |
| preparing | — | none — preparation phase; no Transmodel equivalent |
| prepared | — | none |
| in_use | — | partial — leg being executed |
| paused | — | none — pause state; no Transmodel equivalent |
| ended | CUSTOMER PURCHASE STATUS (consumed) | exact |
| issue_reported | — | none — operational issue state |
| cancelled | CUSTOMER RESERVATION CANCELLATION EVENT | exact |
| abended | — | none — abnormal end; no Transmodel equivalent |

---

## 6. product

A `product` is a fare product associated with a leg or offer.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| product | (object) | FARE PRODUCT | — | exact |
| product | productId | FARE PRODUCT | id | exact |
| product | productName | FARE PRODUCT | name | exact |
| product | guarantees | — | — | none — service guarantees are OMSA-specific |

---

## 7. travelSpecification / requestedTravelSpecification

The `travelSpecification` defines the origin, destination, via, and time constraints for a journey.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travelSpecification | (object) | TRAVEL SPECIFICATION | — | exact — labelled `x-tm: TRAVEL SPECIFICATION` in spec |
| travelSpecification | from | SCHEDULED STOP POINT / PLACE | id | partial |
| travelSpecification | to | SCHEDULED STOP POINT / PLACE | id | partial |
| travelSpecification | via | SCHEDULED STOP POINT / PLACE | id | partial — up to 3 via points |
| travelSpecification | startTime | VEHICLE JOURNEY | departureTime | exact — intended departure time |
| travelSpecification | endTime | VEHICLE JOURNEY | arrivalTime | partial — intended arrival time; implies guaranteed arrival |

---

## 8. individualTraveller

A known traveller with personal attributes (age, gender, name).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| individualTraveller | (object) | TRANSPORT CUSTOMER | — | exact |
| individualTraveller | id | TRANSPORT CUSTOMER | id | exact |
| individualTraveller | age | USER PROFILE | maximumAge / minimumAge | partial — actual age |
| individualTraveller | gender | TRANSPORT CUSTOMER | gender | exact |
| individualTraveller | fullName | TRANSPORT CUSTOMER | firstName + lastName | exact |
| individualTraveller | isValidated | TRANSPORT CUSTOMER | — | partial — identity verification flag |
| individualTraveller | customerReference | CUSTOMER ACCOUNT | id | partial |
| individualTraveller | entitlements | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial — via travelParty |

---

## 9. travelParty

Abstract base for `individualTraveller`, `userProfile`, and `travellingAsset`. Carries entitlements and access right parameters.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travelParty | (object) | ACCESS RIGHT PARAMETER ASSIGNMENT | — | partial — carries usage parameters and entitlements |
| travelParty | entitlements/commercialProfiles | COMMERCIAL PROFILE | — | exact |
| travelParty | entitlements/entitlementsGiven | ENTITLEMENT GIVEN | — | exact |
| travelParty | entitlements/cardTypes | TYPE OF PAYMENT METHOD | — | partial |
| travelParty | entitlements/cards | CUSTOMER PAYMENT MEANS | — | partial |
| travelParty | entitlements/licenseTypes | TYPE OF DRIVER PERMIT | — | exact |
| travelParty | entitlements/licenses | ACCEPTED DRIVER PERMIT | — | exact |
| travelParty | requirements/organisational | ORGANISATIONAL VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/spotReservations | SEATING VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/distribution | DISTRIBUTION VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/network | NETWORK VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/routing | ROUTING VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/service | SERVICE VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/temporal | TEMPORAL VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/equipment | EQUIPMENT VALIDITY PARAMETERS | — | exact |
| travelParty | requirements/cancellation | CANCELLING | — | exact |
| travelParty | requirements/purchase | PURCHASE WINDOW | — | exact |

---

## 10. userProfile

A demographic profile used when no individual traveller is identified (e.g., adult, child, senior).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| userProfile | (object) | USER PROFILE | — | exact |
| userProfile | id | USER PROFILE | id | exact |
| userProfile | count | USER PROFILE | — | partial — number of travellers of this profile type |
| userProfile | minimumAge | USER PROFILE | minimumAge | exact |
| userProfile | maximumAge | USER PROFILE | maximumAge | exact |
| userProfile | ageGroup | USER PROFILE | — | exact — infant/child/youth/adult/senior |
| userProfile | minimumHeight / maximumHeight | USER PROFILE | — | partial — height constraints |
| userProfile | localResident | COMMERCIAL PROFILE | — | partial — residency-based entitlement |
| userProfile | genderLimitation | USER PROFILE | — | partial — gender-based access restriction |

---

## 11. commercialProfile

A commercial classification of a traveller (e.g., loyalty tier, corporate account).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| commercialProfile | (object) | COMMERCIAL PROFILE | — | exact |
| commercialProfile | id | COMMERCIAL PROFILE | id | exact |
| commercialProfile | code | COMMERCIAL PROFILE | — | partial — operator-defined code |
| commercialProfile | name | COMMERCIAL PROFILE | name | exact |

---

## 12. entitlementGiven

A specific entitlement granted to a traveller (e.g., disability card, discount entitlement).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| entitlementGiven | (object) | ENTITLEMENT GIVEN | — | exact |
| entitlementGiven | id | ENTITLEMENT GIVEN | id | exact |
| entitlementGiven | entitlementType | ENTITLEMENT GIVEN | typeOfEntitlement | exact |

---

## 13. Validity Parameters

OMSA models access right conditions as typed parameter objects, each mapping directly to a Transmodel validity parameter concept.

| OMSA Schema | Transmodel Concept(s) | Notes |
|-------------|----------------------|-------|
| equipmentParameter | EQUIPMENT VALIDITY PARAMETERS | exact |
| temporalParameter | TEMPORAL VALIDITY PARAMETERS | exact |
| spotReservationParameter | SEATING VALIDITY PARAMETERS | exact |
| networkParameter | NETWORK VALIDITY PARAMETERS | exact |
| routingParameter | ROUTING VALIDITY PARAMETERS | exact |
| serviceParameter | SERVICE VALIDITY PARAMETERS | exact |
| distributionParameter | DISTRIBUTION VALIDITY PARAMETERS | exact |
| organisationalParameter | ORGANISATIONAL VALIDITY PARAMETERS | exact |
| cancellationParameter | CANCELLING | exact |
| purchaseParameter | PURCHASE WINDOW | exact |

---

## 14. spotReservationParameter

Detailed seat/spot reservation for vehicle, passenger, or luggage.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| spotReservationParameter | spotType (vehicle_spot / passenger_spot / luggage_spot) | SEATING VALIDITY PARAMETERS | — | exact |
| spotReservationParameter | deck / space / spot | SEATING VALIDITY PARAMETERS | — | partial — physical location on vehicle |
| spotReservationParameter | width / length / height | VEHICLE TYPE | vehicleWidth/Length/Height | partial — dimensional constraints |
| spotReservationParameter | fuel | VEHICLE TYPE | — | partial — fuel type constraint |
| spotReservationParameter | weight | VEHICLE TYPE | — | partial |
| spotReservationParameter | mode | MODE | — | exact |

---

## 15. serviceParameter

Service-level access right conditions (class of use, accommodation, on-board stay, user needs).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| serviceParameter | product | FARE PRODUCT | id | partial |
| serviceParameter | asset | VEHICLE | id | partial |
| serviceParameter | serviceJourney | SERVICE JOURNEY | id | exact |
| serviceParameter | userNeeds | USER NEED | — | partial — accessibility/special needs |
| serviceParameter | class | CLASS OF USE | — | exact — first/second/economy/business class |
| serviceParameter | onboardStay | ONBOARD STAY | — | exact |
| serviceParameter | accommodations | ACCOMMODATION | — | exact |

---

## 16. onboardStay

Permission to board early or stay on board after journey end.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| onboardStay | (object) | ONBOARD STAY | — | exact |
| onboardStay | classOfUse | CLASS OF USE | — | exact |
| onboardStay | permission | ONBOARD STAY | — | exact |
| onboardStay | duration | ONBOARD STAY | duration | exact |

---

## 17. accommodation

A combination of accommodation characteristics on a service (cabin type, berth, class).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| accommodation | (object) | ACCOMMODATION | — | exact |
| accommodation | classOfUse | CLASS OF USE | — | exact |
| accommodation | name | ACCOMMODATION | name | exact |
| accommodation | gender | ACCOMMODATION | — | partial — gender allocation |
| accommodation | berthType | ACCOMMODATION | berthType | partial — sleeper berth type |
| accommodation | nuisanceFacility | ACCOMMODATION | — | none — OMSA-specific nuisance facility attributes |

---

## 18. cancellationParameter

Conditions governing cancellation of a purchased package.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| cancellationParameter | cancellationAllowed | CANCELLING | cancellationAllowed | exact |
| cancellationParameter | cancellationFee | CANCELLING | cancellationCharge | exact |
| cancellationParameter | cancellationFeePercentage | CANCELLING | — | partial — percentage of fare; not a direct Transmodel attribute |

---

## 19. purchaseParameter

Conditions governing purchasing (booking window and required licences).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| purchaseParameter | maximumPeriodBeforeDeparture | PURCHASE WINDOW | MaximumPeriodBeforeDeparture | exact |
| purchaseParameter | requiredLicenseTypes | ENTITLEMENT REQUIRED | — | exact |

---

## 20. travelDocument / typeOfTravelDocument

A materialisation of fare entitlement (barcode, QR code, link, NFC token, etc.).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travelDocument | (object) | TRAVEL DOCUMENT | — | exact |
| travelDocument | startValidity | TRAVEL DOCUMENT | startOfValidity | exact |
| travelDocument | endValidity | TRAVEL DOCUMENT | endOfValidity | exact |
| travelDocument | travelDocumentType | TYPE OF TRAVEL DOCUMENT | — | exact |
| typeOfTravelDocument | link | TRAVEL DOCUMENT (link/URL type) | — | partial |
| typeOfTravelDocument | barcode | TRAVEL DOCUMENT (barcode) | — | exact |
| typeOfTravelDocument | qrcode | TRAVEL DOCUMENT (QR code) | — | exact |
| typeOfTravelDocument | azteccode | TRAVEL DOCUMENT (Aztec code) | — | exact |
| typeOfTravelDocument | bluetooth / nfc / physical_key / external_card | MEDIUM ACCESS DEVICE | — | partial — device-based access medium |
| typeOfTravelDocument | none | — | — | none — absence of materialisation |
| binaryTicket | base64 | TRAVEL DOCUMENT | — | partial — binary (image/cert) materialisation; lacks specific Transmodel type |
| externalTicket | (object) | TRAVEL DOCUMENT | — | exact — travel document accessible via external link |

---

## 21. asset

A physical entity used to execute a leg (vehicle, bike, parking bay, etc.).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| asset | (object) | VEHICLE | — | exact for motorised transport assets |
| asset | (object) | PARKING BAY | — | partial — when asset is a parking space |
| asset | (object) | CYCLE STORAGE EQUIPMENT | — | partial — when asset is a bicycle |
| asset | (object) | VEHICLE CHARGING EQUIPMENT | — | partial — when asset is a charging point |
| asset | id | VEHICLE | id | exact |
| asset | visualId | VEHICLE | registrationNumber | partial — licence plate or seat number |
| asset | mode | MODE | — | exact — transport mode |
| asset | product | FARE PRODUCT | id | partial — product associated with the asset |
| asset | equipment | — | — | none — equipment list is OMSA-specific |

---

## 22. seat

A specific seat on a vehicle.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| seat | id | — | — | partial — OMSA external seat reference; Transmodel models seat via SEATING VALIDITY PARAMETERS |
| seat | deck / space | SEATING VALIDITY PARAMETERS | — | partial |
| seat | row / seatNumber | SEATING VALIDITY PARAMETERS | — | partial |
| seat | facing | SEATING VALIDITY PARAMETERS | — | partial — direction facing |
| seat | byWindow / byAisle | SEATING VALIDITY PARAMETERS | — | partial — seat position preferences |
| seat | hasArmRest / hasTray / hasAirco / hasEnergySupply / hasLight | LOCAL SERVICE / EQUIPMENT VALIDITY PARAMETERS | — | partial — on-seat amenity flags |
| seat | legSpace | SEATING VALIDITY PARAMETERS | — | partial — legroom in cm |

---

## 23. ancillary

A supplementary product or service assignable to a leg.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ancillary | (object) | LOCAL SERVICE / SUPPLEMENT PRODUCT | — | partial — labelled `x-tm: ANCILLARY` in spec; no single Transmodel concept |
| ancillary | ancillaryId | LOCAL SERVICE | id | partial |
| ancillary | name | LOCAL SERVICE | name | partial |

---

## 24. card / cardType

Payment or identity cards associated with a traveller.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| card | (object) | CUSTOMER PAYMENT MEANS | — | exact |
| card | (object) | MEDIUM APPLICATION INSTANCE | — | partial — when card is a smartcard or NFC medium |
| card | cardNumber | CUSTOMER PAYMENT MEANS | — | exact |
| card | cardType | TYPE OF PAYMENT METHOD | — | exact |
| card | endValidity | CUSTOMER PAYMENT MEANS | endOfValidity | exact |
| card | country | — | — | partial — issuing country |
| cardType | (object) | TYPE OF PAYMENT METHOD | — | exact |
| cardType | (object) | MEDIUM ACCESS DEVICE | — | partial |
| cardType | cardCategory | TYPE OF PAYMENT METHOD | — | exact — discount/travel/bank/credit/id/passport |
| cardType | relatedProduct | FARE PRODUCT | id | partial |

---

## 25. license / licenseType

Driver or usage licences (e.g., driving licence required to operate a hire car).

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| license | (object) | ACCEPTED DRIVER PERMIT | — | exact |
| license | licenseType | TYPE OF DRIVER PERMIT | — | exact |
| license | licenseNumber | ACCEPTED DRIVER PERMIT | — | exact |
| license | endValidity | ACCEPTED DRIVER PERMIT | endOfValidity | exact |
| licenseType | (object) | TYPE OF DRIVER PERMIT | — | exact |
| licenseType | modes | MODE | — | exact — transport modes the licence covers |
| licenseType | licenseCode | TYPE OF DRIVER PERMIT | — | partial — category code (A, B, C, etc.) |
| licenseType | issuingCountry | — | — | partial — no direct Transmodel attribute |

---

## 26. amountOfMoney

A monetary amount with currency and VAT.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| amountOfMoney | (object) | FARE PRICE | — | exact |
| amountOfMoney | amount | FARE PRICE | amount | exact — inclusive VAT, in ISO 4217 base unit |
| amountOfMoney | taxPercentageUsed | FARE STRUCTURE ELEMENT | — | partial — VAT rate |
| amountOfMoney | currencyCode | FARE FRAME | defaultCurrency | partial |
| amountOfMoney | vatCountryCode | — | — | none — VAT jurisdiction country; no Transmodel equivalent |

---

## 27. refundOption / financialDetail / paymentCategory

Refund options available for a package and the associated financial consequences.

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| refundOption | (object) | EXCHANGING / CANCELLING | — | partial — refund option as an exchange or cancellation condition |
| refundOption | refundType | CANCELLING / EXCHANGING | — | partial — package_refund / remove_traveller / remove_ancillary |
| refundOption | consequences | FARE PRICE | — | partial — financial consequences (charges/refunds) |
| financialDetail | amount | FARE PRICE | amount | exact |
| financialDetail | category | — | — | partial — see paymentCategory below |
| paymentCategory | DEPOSIT | CHARGING POLICY | — | partial |
| paymentCategory | DAMAGE / LOSS / STOLEN | — | — | none — MaaS-specific liability categories |
| paymentCategory | FARE | FARE PRICE | — | exact |
| paymentCategory | REFUND / REBATE / REIMBURSEMENT | CANCELLING / EXCHANGING | — | partial |
| paymentCategory | VOUCHER | SALE DISCOUNT RIGHT | — | partial |
| paymentCategory | CREDIT | SALE DISCOUNT RIGHT | — | partial |
| paymentCategory | FINE / EXTRA_USAGE / OTHER | — | — | none — no direct Transmodel equivalent |

---

## 28. mode / classOfUse / day

NeTEx-derived enumeration types.

| OMSA Schema | Attribute/Value | Transmodel Concept(s) | Notes |
|-------------|-----------------|----------------------|-------|
| mode | AIR/BUS/TRAM/RAIL/METRO/WATER/FERRY/CAR/BICYCLE etc. | MODE | exact — taken directly from NeTEx/Transmodel |
| mode | PARKING / SELFDRIVE / MOPED | MODE | partial — MaaS-extended values |
| classOfUse | FIRST_CLASS / SECOND_CLASS / ECONOMY_CLASS / BUSINESS_CLASS etc. | CLASS OF USE | exact |
| day | MON/TUE/WED/THU/FRI/SAT/SUN | DAY OF WEEK | exact |

---

## 29. OGC API / Protocol schemas

These schemas implement the OGC API Processes framework. They have no Transmodel equivalents.

| OMSA Schema | Transmodel Concept(s) | Notes |
|-------------|----------------------|-------|
| subscriber | — | none — OGC async callback mechanism |
| defaultInput | — | none — generic process input base class |
| landingPage | — | none — OGC API landing page |
| confClasses | — | none — OGC conformance declaration |
| collections / collectionInfo | — | none — OGC collection metadata |
| processList / processSummary / process | — | none — OGC process catalogue |
| link | — | none — OGC hypermedia link |
| error | — | none — RFC 7807 error object |

---

## 30. Search & process input schemas

| OMSA Schema | Transmodel Concept(s) | Notes |
|-------------|----------------------|-------|
| searchOfferInput | CUSTOMER SPOT AVAILABILITY EVENT | partial — initiates offer search based on travel criteria and traveller profiles |
| selectOffersInput | CUSTOMER PURCHASE PACKAGE | partial — selects specific offers for inclusion in a package |
| packageInput | CUSTOMER PURCHASE PACKAGE | exact — references an existing package for lifecycle operations |
| travellerInput | TRANSPORT CUSTOMER | exact — add/update/remove a traveller on a package |
| assetInput | VEHICLE | partial — assigns a physical asset to a leg |
| ancillaryInput | LOCAL SERVICE | partial — assigns an ancillary service to a leg |
| purchasePackageInput | CUSTOMER RESERVATION CREATION EVENT | partial — combined search + purchase in one step |
| purchaseOffersInput | CUSTOMER RESERVATION CREATION EVENT | partial — purchase from pre-selected offers |
| refundOptionInput | EXCHANGING / CANCELLING | partial — claim or confirm a refund option |
| extendExpiryTimeInput | PURCHASE WINDOW | partial — extends the package's confirmation deadline |

---

## 31. API endpoints summary

| Endpoint | Operation | Transmodel Concept(s) | Notes |
|----------|-----------|----------------------|-------|
| POST /processes/search-offers/execute | searchOfferHandler | CUSTOMER SPOT AVAILABILITY EVENT | partial — searches for available transport offers |
| POST /processes/select-offers/execute | selectOffersHandler | CUSTOMER PURCHASE PACKAGE | partial — selects offers into a package |
| POST /processes/add-traveller/execute | addTravellerHandler | TRANSPORT CUSTOMER | exact |
| POST /processes/update-traveller/execute | updateTravellerHandler | TRANSPORT CUSTOMER | exact |
| POST /processes/remove-traveller/execute | removeTravellerHandler | TRANSPORT CUSTOMER | exact |
| POST /processes/assign-asset/execute | assignAssetHandler | VEHICLE | partial |
| POST /processes/assign-ancillary/execute | assignAncillaryHandler | LOCAL SERVICE | partial |
| POST /processes/purchase-offers/execute | purchaseOffersHandler | CUSTOMER RESERVATION CREATION EVENT | exact |
| POST /processes/purchase-package/execute | purchasePackageHandler | CUSTOMER RESERVATION CREATION EVENT | exact |
| POST /processes/2-phase-purchase-package/execute | twoPhasePurchasePackageHandler | CUSTOMER RESERVATION CREATION EVENT | partial — two-phase commit pattern |
| POST /processes/confirm-package/execute | confirmPackageHandler | CUSTOMER PRODUCT PURCHASE EVENT | exact |
| POST /processes/release-package/execute | releasePackageHandler | — | none — internal release; no Transmodel equivalent |
| POST /processes/extend-expiry-time/execute | extendExpiryTimeProcessHandler | PURCHASE WINDOW | partial |
| POST /processes/cancel-package/execute | cancelPackageHandler | CUSTOMER RESERVATION CANCELLATION EVENT | exact |
| POST /processes/claim-refund-option/execute | claimRefundHandler | EXCHANGING / CANCELLING | partial |
| POST /processes/confirm-refund-option/execute | confirmRefundClaimHandler | EXCHANGING / CANCELLING | partial |
| GET /collections/assets/items | assetCollectionHandler | VEHICLE | partial |
| GET /collections/ancillaries/items | ancillariesCollectionHandler | LOCAL SERVICE | partial |
| GET /collections/refund-options/items | refundOptionHandler | EXCHANGING / CANCELLING | partial |
| GET /collections/travel-documents/items | travelDocumentsCollectionHandler | TRAVEL DOCUMENT | exact |
| GET /collections/datasources/items | getDataSources | — | none — external data source registry |

---

## Summary

| Domain | OMSA Schemas | Primary Transmodel Concepts |
|--------|-------------|----------------------------|
| Package lifecycle | package, packageStatus | CUSTOMER PURCHASE PACKAGE, CUSTOMER PURCHASE STATUS |
| Offer | offer | SALES OFFER PACKAGE |
| Journey leg | leg, legState, travelSpecification | LEG, SERVICE JOURNEY, DEPARTURE, ARRIVAL |
| Product / Fare | product, amountOfMoney | FARE PRODUCT, FARE PRICE |
| Traveller | individualTraveller, userProfile, travelParty | TRANSPORT CUSTOMER, USER PROFILE, ACCESS RIGHT PARAMETER ASSIGNMENT |
| Commercial / entitlements | commercialProfile, entitlementGiven, cardType, card | COMMERCIAL PROFILE, ENTITLEMENT GIVEN, CUSTOMER PAYMENT MEANS |
| Licences | license, licenseType | ACCEPTED DRIVER PERMIT, TYPE OF DRIVER PERMIT |
| Validity parameters | temporal/network/routing/service/equipment/distribution/organisational Parameters | TEMPORAL/NETWORK/ROUTING/SERVICE/EQUIPMENT/DISTRIBUTION/ORGANISATIONAL VALIDITY PARAMETERS |
| Cancellation | cancellationParameter, refundOption, paymentCategory | CANCELLING, EXCHANGING |
| Asset | asset, seat | VEHICLE, SEATING VALIDITY PARAMETERS |
| Ancillary | ancillary | LOCAL SERVICE |
| Travel document | travelDocument, typeOfTravelDocument, binaryTicket, externalTicket | TRAVEL DOCUMENT, TYPE OF TRAVEL DOCUMENT, MEDIUM ACCESS DEVICE |
| Accommodation | accommodation, onboardStay, serviceParameter | ACCOMMODATION, ONBOARD STAY, CLASS OF USE |
| Mode / classification | mode, classOfUse, day | MODE, CLASS OF USE, DAY OF WEEK |
| OGC/protocol | subscriber, landingPage, confClasses, collections, processList | (none) |
