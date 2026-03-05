# TOMP-API 2.0.0 → Transmodel Mapping

Source spec: `wiki/specifications/TOMP-API 2.0.0.yaml`

TOMP-API is the Technical Interface between Transport Operators (TO) and MaaS Providers (MP). It is organised into six modules: **offers**, **pre-sales**, **purchase**, **execution**, **support**, and **after-sales**. The spec carries extensive `x-semantics` annotations referencing Transmodel concepts, which are used directly in this mapping.

---

## 1. API Endpoints (Operations)

### 1.1 Offer Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| search-offers | POST /processes/search-offers/execution | FARE QUERY, TRIP REQUEST | — | exact: searches offers on travel specification + fare info |
| GET /collections/offers/items | requestOffer | SALES OFFER PACKAGE QUERY | — | exact: retrieves available SALES OFFER PACKAGEs by reference |
| GET /collections/assets/items | requestAssets | TRAVEL PACKAGE SPOT AVAILABILITY QUERY | — | partial: shared-mobility assets are VEHICLE, PARKING BAY or SPOT |
| GET /collections/ancillaries/items | requestAncillaries | SUPPLEMENT PRODUCT, THIRD PARTY PRODUCT | — | partial: no dedicated Transmodel ancillary query; nearest is SUPPLEMENT PRODUCT |

### 1.2 Pre-Sales Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| select-offers | POST /processes/select-offers/execution | TRAVEL PACKAGE CREATION REQUEST | — | exact: converts selected offers into CUSTOMER PURCHASE PACKAGE |
| update-traveller | POST /processes/update-traveller/execution | TRAVEL PACKAGE ALTERATION REQUEST | — | exact: modifies traveller attributes on existing package |
| update-travel-specification | POST /processes/update-travel-specification/execution | TRAVEL PACKAGE ALTERATION REQUEST | — | exact: changes start/end location or time |
| assign-asset | POST /processes/assign-asset/execution | TRAVEL PACKAGE SPOT RESERVATION REQUEST, TRAVEL PACKAGE SPOT CANCELLATION REQUEST | — | assign=SPOT RESERVATION; removal=SPOT CANCELLATION |
| assign-ancillary | POST /processes/assign-ancillary/execution | TRAVEL PACKAGE ALTERATION REQUEST | — | partial: nearest Transmodel equivalent; no dedicated ancillary operation |
| remove-offer | POST /processes/remove-offer/execution | TRAVEL PACKAGE ALTERATION REQUEST | — | exact: removes specific offer from package |
| release-package | POST /processes/release-package/execution | — | — | none: release of PENDING package before purchase; no Transmodel equivalent |

### 1.3 Purchase Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| purchase-offers | POST /processes/purchase-offers/execution | TRAVEL PACKAGE PURCHASE REQUEST | — | exact: purchase of one or more selected offers |
| purchase-package | POST /processes/purchase-package/execution | TRAVEL PACKAGE PURCHASE REQUEST | — | exact: purchase of a complete package |
| purchase-product | POST /processes/purchase-product/execution | TRAVEL PACKAGE PURCHASE REQUEST | — | partial: product purchase without travel specification |
| use-asset | POST /processes/use-asset/execution | TRAVEL PACKAGE PURCHASE REQUEST | — | partial: direct asset activation without prior package |
| confirm-purchase | POST /processes/confirm-purchase/execution | — | — | none: TOMP-specific two-phase purchase confirmation |
| rollback-purchase | POST /processes/rollback-purchase/execution | — | — | none: TOMP-specific rollback within rollbackExpiryTime |
| extend-expiry-time | POST /processes/extend-expiry-time/execution | — | — | none: no Transmodel equivalent for expiry extension |
| GET /collections/travel-documents/items | getTravelDocuments | FULFILMENT | — | exact: retrieval of travel documents after successful purchase |

### 1.4 Execution Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| {legOperation}-leg | POST /processes/{legOperation}-leg/execution | — | — | none: start/pause/resume/end/extend/postpone have no Transmodel operation equivalents |
| {assetOperation}-asset | POST /processes/{assetOperation}-asset/execution | — | — | none: lock/unlock/open-trunk are shared-mobility specific |
| {productOperation}-product | POST /processes/{productOperation}-product/execution | — | — | none: activate/initiate-leg are TOMP-specific |

### 1.5 Support Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GET /collections/support-tickets/items | getSupportTickets | — | — | none: customer-initiated support tickets are outside Transmodel scope |
| request-support | POST /processes/request-support/execution | — | — | none: nearest concept is DETECTED FAULT or INCIDENT but not customer-side |

### 1.6 After-Sales Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GET /collections/redress-options/items | getRedressOptions | REFUND, EASEMENT | — | exact: REFUND=financial; EASEMENT=alternative service |
| claim-redress-option | POST /processes/claim-redress-option/execution | REMEDY, REFUNDING | — | exact: REFUNDING=financial; REMEDY=broader compensation |
| confirm-redress-option | POST /processes/confirm-redress-option/execution | REMEDY | — | partial: confirmation step for claimed redress |
| cancel-package | POST /processes/cancel-package/execution | TRAVEL PACKAGE CANCELLATION REQUEST | — | exact: cancellation of confirmed package |
| GET /collections/payments/items | getPaymentOverview | — | — | none: payment journal; nearest is FARE CONTRACT but scope differs |
| confirm-payment | POST /processes/confirm-payment/execution | — | — | none: B2B payment confirmation between MP and TO |
| refund-deposit | POST /processes/refund-deposit/execution | REFUNDING | — | partial: deposit refund |

### 1.7 Customer Management Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| POST /collections/customers/items | createCustomer | CUSTOMER ACCOUNT | — | exact: creates CUSTOMER ACCOUNT on TO side |
| GET /collections/customers/items/{id} | getCustomer | CUSTOMER ACCOUNT | — | exact |
| PATCH /collections/customers/items/{id} | updateCustomer | CUSTOMER ACCOUNT | — | exact |
| DELETE /collections/customers/items/{id} | deleteCustomer | CUSTOMER ACCOUNT | — | exact |

### 1.8 Travel Information Module

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| GET /collections/entitlements/items | getEntitlements | ENTITLEMENT PRODUCT | — | exact |
| GET /collections/card-types/items | getCardTypes | TYPE OF PAYMENT METHOD, MEDIUM ACCESS DEVICE | — | exact |
| GET /collections/license-types/items | getLicenseTypes | TYPE OF DRIVER PERMIT | — | exact |
| GET /collections/user-profiles/items | getUserProfiles | USER PROFILE | — | exact |
| GET /collections/fares/items | getFare | TARIFF | — | exact: fare info per product/user-profile/entitlement/card-type |

---

## 2. Core Data Schemas

### 2.1 package / packageProperties

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| packageProperties | type=package | CUSTOMER PURCHASE PACKAGE | — | exact: a purchased agreement between end user and TO |
| packageProperties | type=offer | SALES OFFER PACKAGE | — | exact: an offered (not yet purchased) package |
| packageProperties | id | CUSTOMER PURCHASE PACKAGE | id | exact |
| packageProperties | status | CUSTOMER PURCHASE STATUS | — | exact: lifecycle state (OFFERED/PENDING/CONFIRMED/CANCELLED/STARTED/ENDED/SETTLED) |
| packageProperties | price | TARIFF | — | exact: mapped to fareStructure |
| packageProperties | guarantees | TRAVEL GUARANTEE | — | exact: array of TRAVEL GUARANTEE conditions |
| packageProperties | legs | LEG | — | exact: array of LEGs composing the package |
| packageProperties | offers | SALES OFFER PACKAGE | — | partial: offered sub-packages within a package |
| packageProperties | customFields | — | — | none: bilateral extension point |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE PACKAGE (type=package), SALES OFFER PACKAGE (type=offer)
- **conditions**: when `type == offer` the object is an offered package (pre-purchase); when `type == package` it is a purchased package

---

### 2.2 packageStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| packageStatus | OFFERED | CUSTOMER PURCHASE STATUS | — | partial: pre-sales offer state |
| packageStatus | PENDING | CUSTOMER PURCHASE STATUS | — | exact: purchase initiated but not confirmed |
| packageStatus | CONFIRMED | CUSTOMER PURCHASE STATUS | — | exact: purchased and confirmed |
| packageStatus | CANCELLED | CUSTOMER PURCHASE STATUS | — | exact |
| packageStatus | ROLLBACK | CUSTOMER PURCHASE STATUS | — | partial: purchase reversible within rollbackExpiryTime |
| packageStatus | EXPIRED | CUSTOMER PURCHASE STATUS | — | partial: offer expired |
| packageStatus | STARTED | CUSTOMER PURCHASE STATUS | — | partial: execution started |
| packageStatus | ENDED | CUSTOMER PURCHASE STATUS | — | partial: trip executed |
| packageStatus | RELEASED | CUSTOMER PURCHASE STATUS | — | none: internal archiving state, no Transmodel equivalent |
| packageStatus | SETTLED | CUSTOMER PURCHASE STATUS | — | partial: financial settlement final state |

x-semantics equivalent:
- **concepts**: CUSTOMER PURCHASE STATUS (Sales & Distribution)

---

### 2.3 leg / legProperties

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| legProperties | id | LEG | id | exact |
| legProperties | specification | TRAVEL SPECIFICATION | — | exact: origin/destination/time parameters |
| legProperties | sequenceNumber | LEG | order | partial |
| legProperties | mode | MODE | — | exact |
| legProperties | status | — | — | none: legStatus has no direct Transmodel equivalent |
| legProperties | travellers | INDIVIDUAL TRAVELLER | — | exact: references to travellers on this leg |
| legProperties | products | FARE PRODUCT | — | exact: product(s) used for this leg |
| legProperties | ancillaries | ANCILLARY PRODUCT | — | exact |
| legProperties | assets | VEHICLE, PARKING BAY, CYCLE STORAGE EQUIPMENT | — | partial: physical asset for leg execution |
| legProperties | operatorId | OPERATOR | id | exact |
| legProperties | labels | — | — | none: TOMP-specific care labels (REFUNDABLE, EXCHANGABLE etc.) |

x-semantics equivalent:
- **concepts**: LEG (Journey Planning)

---

### 2.4 travelSpecification

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travelSpecification | from | SCHEDULED STOP POINT, TOPOGRAPHIC PLACE | — | exact: origin place |
| travelSpecification | via | SCHEDULED STOP POINT | — | partial: intermediate stops |
| travelSpecification | to | SCHEDULED STOP POINT, TOPOGRAPHIC PLACE | — | exact: destination place |
| travelSpecification | startTime | — | departureTime | exact: intended departure time |
| travelSpecification | endTime | — | arrivalTime | exact: intended arrival time |

x-semantics equivalent:
- **concepts**: TRAVEL SPECIFICATION (Sales & Distribution)

---

### 2.5 tripPattern

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| tripPattern | serviceJourneys | SERVICE JOURNEY | — | exact: ordered sequence of SERVICE JOURNEYs |
| tripPattern | travelDate | — | date | exact: travel date |

x-semantics equivalent:
- **concepts**: TRIP PATTERN (Journey Planning)
- **conditions**: partial — TOMP restricts to travelDate + serviceJourneyReferences; Transmodel TRIP PATTERN also contains times and per-day patterns

---

### 2.6 searchOfferRequest

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| searchOfferRequest | specification | TRIP REQUEST, FARE QUERY | — | exact: TRIP REQUEST when result contains travel options; FARE QUERY when also tariffs |
| searchOfferRequest | requirements | USER NEEDS | — | partial |
| searchOfferRequest | travellers | INDIVIDUAL TRAVELLER | — | exact |
| searchOfferRequest | places | TOPOGRAPHIC PLACE | — | partial: non-standard places not in external data source |
| searchOfferRequest | packageToExchange | CUSTOMER PURCHASE PACKAGE | — | partial: package to exchange |

---

### 2.7 traveller

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| traveller | id | INDIVIDUAL TRAVELLER | id | exact |
| traveller | characteristics | INDIVIDUAL TRAVELLER | attributes | exact |
| traveller | requirements | USER NEEDS | — | partial |
| traveller | profile | USER PROFILE | — | exact |
| traveller | groupSize | GROUP | size | partial |

x-semantics equivalent:
- **concepts**: INDIVIDUAL TRAVELLER (Sales & Distribution)

---

### 2.8 travellerCharacteristics

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travellerCharacteristics | name | INDIVIDUAL TRAVELLER | — | exact |
| travellerCharacteristics | age | INDIVIDUAL TRAVELLER | age | exact |
| travellerCharacteristics | customerReference | TRANSPORT CUSTOMER | id | exact |
| travellerCharacteristics | entitlements | ENTITLEMENT PRODUCT, COMMERCIAL PROFILE | — | exact |
| travellerCharacteristics | cardTypes | TYPE OF PAYMENT METHOD | — | exact |
| travellerCharacteristics | cards | CUSTOMER PAYMENT MEANS | — | exact |
| travellerCharacteristics | licenseTypes | TYPE OF DRIVER PERMIT | — | exact |
| travellerCharacteristics | licenses | ACCEPTED DRIVER PERMIT | — | exact |

---

### 2.9 travellerRequirements

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travellerRequirements | mode | MODE | — | exact |
| travellerRequirements | class | CLASS OF USE | — | exact |
| travellerRequirements | operators | ORGANISATIONAL VALIDITY PARAMETERS | — | exact |
| travellerRequirements | products | SERVICE VALIDITY PARAMETERS | — | exact |
| travellerRequirements | assets | SEATING VALIDITY PARAMETERS | — | exact: where spot = asset |
| travellerRequirements | zones | NETWORK VALIDITY PARAMETERS | — | exact |
| travellerRequirements | distribution | DISTRIBUTION VALIDITY PARAMETERS | — | exact |
| travellerRequirements | avoid | ROUTING VALIDITY PARAMETERS | — | exact |
| travellerRequirements | prmNeeds | TYPE OF USER NEEDS | — | exact |

---

### 2.10 customer

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| customer | id | TRANSPORT CUSTOMER | id | exact |
| customer | firstName, lastName | INDIVIDUAL | name | exact |
| customer | phoneNumber | INDIVIDUAL | contactDetails | partial |
| customer | email | INDIVIDUAL | contactDetails | partial |
| customer | dateOfBirth | INDIVIDUAL | dateOfBirth | exact |
| customer | address | POSTAL ADDRESS | — | exact |

x-semantics equivalent:
- **concepts**: TRANSPORT CUSTOMER (Sales & Distribution)

---

### 2.11 customerAccount

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| customerAccount | id | CUSTOMER ACCOUNT | id | exact |
| customerAccount | customer | TRANSPORT CUSTOMER | — | exact |
| customerAccount | creationDate | CUSTOMER ACCOUNT | creationDate | exact |
| customerAccount | modificationDate | CUSTOMER ACCOUNT | modificationDate | exact |
| customerAccount | status | CUSTOMER ACCOUNT | status | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER ACCOUNT (Sales & Distribution)

---

### 2.12 customerAccountStatus

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| customerAccountStatus | CREATED | CUSTOMER ACCOUNT | status | partial |
| customerAccountStatus | TO_PENDING_VALIDATION | CUSTOMER ACCOUNT | status | partial |
| customerAccountStatus | ACTIVE | CUSTOMER ACCOUNT | status | exact |
| customerAccountStatus | BLOCKED | CUSTOMER ACCOUNT | status | exact |

---

### 2.13 asset

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| asset | id | VEHICLE, PARKING BAY, CYCLE STORAGE EQUIPMENT, VEHICLE CHARGING EQUIPMENT | id | partial: concept depends on mode |
| asset | visualId | VEHICLE | licensePlate / seatNumber | partial |
| asset | mode | MODE | — | exact |
| asset | subMode | — | — | none: more precise mode classification; no Transmodel equivalent |
| asset | damages | — | — | none: operational damage reporting; no Transmodel equivalent |
| asset | cargo | LUGGAGE ALLOWANCE | — | exact |
| asset | fee | TARIFF | — | partial |

x-semantics equivalent:
- **concepts**: VEHICLE (Physical Infrastructure), PARKING BAY (Physical Infrastructure), CYCLE STORAGE EQUIPMENT, VEHICLE CHARGING EQUIPMENT
- **conditions**: concept selection depends on mode

---

### 2.14 ancillary

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| ancillary | id | ANCILLARY PRODUCT | id | exact |
| ancillary | name | ANCILLARY PRODUCT | name | exact |
| ancillary | description | ANCILLARY PRODUCT | description | exact |
| ancillary | product | FARE PRODUCT | — | partial: linked product reference |
| ancillary | fee | TARIFF | — | partial |

x-semantics equivalent:
- **concepts**: ANCILLARY PRODUCT (Fare Management)

---

### 2.15 productProperties

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| productProperties | id | FARE PRODUCT | id | exact |
| productProperties | name | FARE PRODUCT | name | exact |
| productProperties | description | FARE PRODUCT | description | exact |
| productProperties | parts | FARE PRODUCT | composedOf | partial: composed products |
| productProperties | fare | TARIFF | — | exact |
| productProperties | conditions | USAGE PARAMETER | — | partial: labels like REFUNDABLE, EXCHANGABLE |
| productProperties | guarantees | TRAVEL GUARANTEE | — | exact |

x-semantics equivalent:
- **concepts**: FARE PRODUCT (Fare Management)

---

### 2.16 fareStructure / fareStructureElement

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| fareStructure | — | TARIFF | — | exact: complete tariff (sum of elements) |
| fareStructure | elements | FARE STRUCTURE ELEMENT | — | exact |
| fareStructure | temporalValidity | VALIDITY CONDITION | — | partial |
| fareStructure | userProfiles | USER PROFILE | — | exact |
| fareStructureElement | type=FIXED | DURATION UNIT PRICE, GEOGRAPHICAL UNIT PRICE | — | partial: fixed-rate fare element |
| fareStructureElement | type=FLEX | TIME INTERVAL PRICE, GEOGRAPHICAL INTERVAL PRICE | — | exact: distance or time-based pricing |
| fareStructureElement | type=MAX | LIMITING RULE | — | exact: price cap |
| fareStructureElement | units | TIME UNIT, DISTANCE UNIT | — | exact |
| fareStructureElement | priceCondition=DISCOUNT | SALE DISCOUNT RIGHT | — | partial |
| fareStructureElement | priceCondition=SURGE | — | — | none: surge pricing has no Transmodel equivalent |

x-semantics equivalent:
- **concepts**: TARIFF (Fare Management), FARE STRUCTURE ELEMENT (Fare Management)

---

### 2.17 interval

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| interval | from, to | TIME INTERVAL | startMinutes, endMinutes | exact (when units=minutes/hours) |
| interval | units | TIME UNIT, DISTANCE UNIT | — | exact |

x-semantics equivalent:
- **concepts**: TIME INTERVAL (Fare Management)

---

### 2.18 summary (offer/package summary)

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| summary | specification | TRAVEL SPECIFICATION | — | exact |
| summary | price | FARE PRICE | — | exact |
| summary | cancellation | CANCELLING | — | exact: cancellable, fee |
| summary | exchangeable | EXCHANGING | exchangeable | exact |
| summary | exchanging | EXCHANGING | numberOfExchangesAllowed | exact |
| summary | refundable | REFUNDING | — | exact |
| summary | transferrable | TRANSFERABILITY | canTransfer | exact |
| summary | transfer | TRANSFERABILITY | fee, maximumNumberOfNamedUsers | exact |

---

### 2.19 guarantee

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| guarantee | id | TRAVEL GUARANTEE | id | exact |
| guarantee | name | TRAVEL GUARANTEE | name | exact |
| guarantee | organisation | OPERATOR | — | exact: guaranteeing organisation |
| guarantee | type=ALTERNATIVE_JOURNEY | TRIP REPAIR GUARANTEE | — | exact |
| guarantee | type=HOME_LEG | TRIP REPAIR GUARANTEE | — | exact: taxi home |
| guarantee | type=RETURN_TO_ORIGIN | TRIP REPAIR GUARANTEE | — | exact |
| guarantee | type=ON_TIME_TRAVEL | TRAVEL QUALITY GUARANTEE | — | exact |
| guarantee | type=TRIP_ON_TIME | TRAVEL QUALITY GUARANTEE | — | exact |
| guarantee | type=FACILITIES_AVAILABLE | TRAVEL QUALITY GUARANTEE | — | exact |
| guarantee | type=DISRUPTION_INFORMATION | INFORMATION QUALITY GUARANTEE | — | exact |
| guarantee | type=GENERAL_TRAVEL | OTHER TRAVEL GUARANTEE | — | exact |

x-semantics equivalent:
- **concepts**: TRAVEL GUARANTEE (Fare Management)

---

### 2.20 redressOption / redressOptions

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| redressOptions | — | TRAVEL REDRESSes | — | exact (shorthand annotation in spec) |
| redressOption | redressType=PACKAGE_REFUND | REFUND | — | exact |
| redressOption | redressType=OFFER_REFUND | REFUND | — | exact |
| redressOption | redressType=LEG_REFUND | REFUND | — | exact |
| redressOption | redressType=REPLACEMENT | EASEMENT | — | partial: alternative service provision |
| redressOption | compensations | FARE PRICE | — | partial: financial compensation amounts |
| redressOption | reason | TRAVEL GUARANTEE | — | exact: the unmet guarantee triggering redress |

---

### 2.21 supportTicket

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| supportTicket | — | — | — | none: nearest concept is DETECTED FAULT or INCIDENT (Operations); customer-initiated CRM outside Transmodel scope |
| supportTicket | supportType=BROKEN_DOWN | — | — | partial: nearest DETECTED FAULT |
| supportTicket | supportType=ACCIDENT | — | — | partial: nearest INCIDENT |

---

### 2.22 travelDocument / travelDocuments

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| travelDocuments | — | TRAVEL DOCUMENT | — | exact |
| travelDocument | validity | USAGE VALIDITY PERIOD | — | exact |
| travelDocument | travelDocumentType | TYPE OF TRAVEL DOCUMENT | — | exact |
| travelDocument | package | CUSTOMER PURCHASE PACKAGE | — | partial: TOMP couples document to package; Transmodel is broader |
| travelDocument | leg | LEG | — | partial |
| travelDocument | asset | VEHICLE | — | partial: asset access control |
| binaryTicket | — | — | — | none: binary payload representation; Transmodel does not model at this level |
| externalDigitalTicket | — | — | — | none: external URL access; implementation pattern without equivalent |
| eKey | — | MEDIUM ACCESS DEVICE | — | partial: Axa-specific digital key protocol |

x-semantics equivalent:
- **concepts**: TRAVEL DOCUMENT (Fare Management), TYPE OF TRAVEL DOCUMENT (Fare Management)

---

### 2.23 entitlementGiven

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| entitlementGiven | id | ENTITLEMENT PRODUCT, COMMERCIAL PROFILE | id | exact |
| entitlementGiven | type=entitlement | ENTITLEMENT PRODUCT | — | exact |
| entitlementGiven | type=commercialProfile | COMMERCIAL PROFILE | — | exact |
| entitlementGiven | description | ENTITLEMENT PRODUCT | description | exact |

---

### 2.24 userProfile

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| userProfile | name | USER PROFILE | name | exact |
| userProfile | ageGroup | USER PROFILE | ageGroup | exact |
| userProfile | age | USER PROFILE | minAge / maxAge | exact |
| userProfile | discountBasis | USER PROFILE | discountBasis | exact |
| userProfile | genderLimitation | USER PROFILE | genderLimitation | exact |
| userProfile | localResident | USER PROFILE | localResident | exact |

x-semantics equivalent:
- **concepts**: USER PROFILE (Fare Management)

---

### 2.25 card / cardType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| card | cardType | TYPE OF PAYMENT METHOD, MEDIUM ACCESS DEVICE | — | exact |
| card | cardNumber | CUSTOMER PAYMENT MEANS | number | exact |
| card | endValidity | — | validUntil | partial |
| cardType | id | TYPE OF PAYMENT METHOD | id | exact |
| cardType | cardCategory=DISCOUNT | SALE DISCOUNT RIGHT | — | partial |
| cardType | cardCategory=TRAVEL | TRAVEL DOCUMENT | — | partial |
| cardType | cardCategory=BANK / CREDIT | CUSTOMER PAYMENT MEANS | — | exact |

x-semantics equivalent:
- **concepts**: CUSTOMER PAYMENT MEANS (Sales & Distribution), TYPE OF PAYMENT METHOD, MEDIUM ACCESS DEVICE

---

### 2.26 license / licenseType

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| license | licenseType | TYPE OF DRIVER PERMIT | — | exact |
| license | licenseNumber | ACCEPTED DRIVER PERMIT | number | exact |
| license | endValidity | ACCEPTED DRIVER PERMIT | validUntil | exact |
| licenseType | modes | TYPE OF DRIVER PERMIT | modes | exact |
| licenseType | licenseCategory | TYPE OF DRIVER PERMIT | category | exact |
| licenseType | issuingCountry | TYPE OF DRIVER PERMIT | issuingCountry | exact |

x-semantics equivalent:
- **concepts**: ACCEPTED DRIVER PERMIT (Sales & Distribution), TYPE OF DRIVER PERMIT

---

### 2.27 mode

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| mode | AIR / BUS / TRAM / RAIL / METRO / WATER etc. | MODE | — | exact: values from NeTEx/Transmodel |
| mode | SELFDRIVE / BICYCLE / CAR / MOPED | MODE | — | partial: shared-mobility modes; also covered by Transmodel sub-modes |
| mode | PARKING | PARKING FACILITY | — | partial |
| mode | OTHER | — | — | none |

x-semantics equivalent:
- **concepts**: MODE (Journey Planning)

---

### 2.28 classOfUse

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| classOfUse | FIRST_CLASS / SECOND_CLASS / BUSINESS_CLASS / ECONOMY_CLASS / TURISTA / PREFERENTE / PREMIUM_CLASS | CLASS OF USE | — | exact |

x-semantics equivalent:
- **concepts**: CLASS OF USE (Fare Management)

---

### 2.29 prmNeeds

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| prmNeeds | mobilityNeed | TYPE OF USER NEEDS | mobilityNeed | exact |
| prmNeeds | psychoSensoryNeed | TYPE OF USER NEEDS | psychoSensoryNeed | exact |
| prmNeeds | medicalNeed | TYPE OF USER NEEDS | medicalNeed | exact |
| prmNeeds | encumbranceNeed | TYPE OF USER NEEDS | encumbranceNeed | exact |

x-semantics equivalent:
- **concepts**: TYPE OF USER NEEDS (Accessibility)

---

### 2.30 distribution

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| distribution | accessType | TYPE OF TRAVEL DOCUMENT | — | exact |
| distribution | distributionChannel | DISTRIBUTION CHANNEL | — | exact |

x-semantics equivalent:
- **concepts**: DISTRIBUTION VALIDITY PARAMETERS (Fare Management)

---

### 2.31 place

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| place | id | TOPOGRAPHIC PLACE, SCHEDULED STOP POINT | id | partial |
| place | addressLine1, addressLine2 | POSTAL ADDRESS | street, city | exact |
| place | street | POSTAL ADDRESS | streetName | exact |
| place | postalCode | POSTAL ADDRESS | postCode | exact |
| place | city | POSTAL ADDRESS | town | exact |
| place | country | COUNTRY | — | exact |

x-semantics equivalent:
- **concepts**: POSTAL ADDRESS (General Framework)

---

### 2.32 cargoLimits

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| cargoLimits | volume | LUGGAGE ALLOWANCE | volume | exact |
| cargoLimits | weight | LUGGAGE ALLOWANCE | weight | exact |

x-semantics equivalent:
- **concepts**: LUGGAGE ALLOWANCE (Fare Management)

---

### 2.33 bankAccount

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| bankAccount | — | — | — | none: bank account details have no Transmodel equivalent |

---

### 2.34 financialDetail / paymentCategory

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| financialDetail | — | — | — | none: TOMP-specific B2B invoice journal; nearest concept is FARE CONTRACT but semantics differ |
| paymentCategory | FARE | FARE PRICE | — | partial |
| paymentCategory | REFUND / REBATE / REIMBURSEMENT | REFUNDING | — | partial |
| paymentCategory | DEPOSIT | — | — | none |
| paymentCategory | DAMAGE / LOSS / FINE | — | — | none: operational cost categories not in Transmodel |

---

### 2.35 OGC Discovery Schemas

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|---------|-----------|----------------------|-------------------------|-------|
| landingPage | — | — | — | none: OGC API technical description |
| conformance | — | — | — | none: OGC conformance declaration |
| collection | — | — | — | none: OGC feature collection descriptor |
| collections | — | — | — | none |
| processList / process | — | — | — | none: OGC Processes API description |
| processSummary | — | — | — | none |
| extent | spatial/temporal | — | — | none: OGC bounding-box/temporal extent |

---

## 3. Reference / Primitive Types

| Concept | Attribute | Transmodel Concept(s) | Notes |
|---------|-----------|----------------------|-------|
| packageReference | — | CUSTOMER PURCHASE PACKAGE id | exact |
| legReference | — | LEG id | exact |
| travellerReference | — | INDIVIDUAL TRAVELLER id | exact |
| customerReference | — | TRANSPORT CUSTOMER id | exact |
| assetReference | — | VEHICLE / infrastructure id | partial |
| productReference | — | FARE PRODUCT id | exact |
| userProfileReference | — | USER PROFILE name | exact |
| cardTypeReference | — | TYPE OF PAYMENT METHOD id | exact |
| entitlementReference | — | ENTITLEMENT PRODUCT id | exact |
| offerReference | — | SALES OFFER PACKAGE id | exact |
| operatorReference | — | OPERATOR id | exact |
| zoneReference | — | TARIFF ZONE id | exact |
| lineReference | — | LINE id | exact |
| serviceJourneyReference | — | SERVICE JOURNEY id | exact |
| ancillaryReference | — | ANCILLARY PRODUCT id | exact |
| typeOfTravelDocument | — | TYPE OF TRAVEL DOCUMENT | exact |
