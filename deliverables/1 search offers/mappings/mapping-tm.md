# Mapping: Search Offers → Transmodel v6.2

This file maps each EUDIT **Search Offers** concept to the **Transmodel v6.2** concept(s) it realises.
Unlike the other mapping files, this is not a standard-to-standard API mapping but a conceptual anchor:
it shows which Transmodel concepts underpin each EUDIT schema.

- **EUDIT concept** — as defined in `search-offers.yaml`
- **Transmodel concept** — the TM v6.2 concept(s) realised
- **TM package** — the Transmodel part / package
- **Notes** — alignment remarks

---

## Request-side concepts

### OfferRequest

| EUDIT concept | Transmodel concept | TM package | Notes |
|---|---|---|---|
| OfferRequest | FARE REQUEST | Part 6 — Fare management | Root request for fare calculation; TM FARE REQUEST aggregates the trip context and passenger data. |

---

### SearchOfferPolicy

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| numberOfResultsBefore / numberOfResultsAfter | AVAILABILITY CONDITION | Part 1 — Framework | Bounds the time window in which results are sought. |
| currency | CURRENCY | Part 6 | ISO 4217 currency for price expression; TM uses CURRENCY TYPE. |

---

### SearchOfferFilter

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| modes | TRANSPORT MODE | Part 1 | TM TRANSPORT MODE (rail, bus, ferry, etc.). |
| classOfUse | FARE CLASS | Part 6 | TM FARE CLASS (first, second, premium). |
| mediaTypes | TRAVEL DOCUMENT TYPE | Part 6 | TM TRAVEL DOCUMENT TYPE categorises fulfilment media (paper, mobile, smart card). |
| requestedSections | SECTION | Part 2 — Public transport network topology | TM SECTION — a part of a route between two points. |

---

### RequestedSections

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| startLegId / endLegId | SECTION | Part 2 | Bounds of the requested section identified by leg references. |
| startPlace / endPlace | SCHEDULED STOP POINT / PLACE | Part 2 / Part 1 | Origin and destination of the requested section. |
| tripReference | TRIP | Part 3 — Timing information | The specific trip this section belongs to. |

---

### TravellingEntity

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| travellingEntityId | TRAVELLING ENTITY | Part 6 | TM TRAVELLING ENTITY — the root class for all participants in a journey (TM v6.2 §6.4). |
| entityType | (discriminator) | — | Implemented as a discriminator over TM sub-types. |
| entitlementRights | ENTITLEMENT PRODUCT | Part 6 | TM ENTITLEMENT PRODUCT — a product that gives the holder rights to a discount or access condition. |

---

### Traveller

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Traveller (class) | TRANSPORT CUSTOMER | Part 6 | TM TRANSPORT CUSTOMER — an individual (or group) purchasing or using transport services. |
| age | USER PROFILE | Part 6 | Age is a qualifying attribute of USER PROFILE. |
| assistant | ASSISTANCE NEED | Part 1 | Whether this person acts as an assistant for another. |
| dateOfBirth | USER PROFILE | Part 6 | Date of birth is a qualifying attribute used for age-band fare calculation. |
| externalReference | CUSTOMER ACCOUNT | Part 6 | External identifier linking to the customer's account in the origin system. |
| personalNeeds | ACCESSIBILITY NEED | Part 1 | TM ACCESSIBILITY NEED — a specific accessibility requirement of a person. |
| qualifyingCharacteristics | USER PROFILE | Part 6 | See TravellerQualifyingCharacteristics below. |

---

### TravellerQualifyingCharacteristics

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| TravellerQualifyingCharacteristics (class) | USER PROFILE | Part 6 | TM USER PROFILE — a demographic / eligibility profile used for fare qualification (§6.4.3). |
| fullName | PASSENGER | Part 1 | Personal identity attribute. |
| nationality | USER PROFILE | Part 6 | Nationality as a qualifying attribute. |
| residency | USER PROFILE | Part 6 | Country of residence as a qualifying attribute. |
| dateOfBirth | USER PROFILE | Part 6 | Date of birth qualifying attribute. |
| licenseTypes | LICENSE TYPE | Part 6 | Driving licence categories held by the traveller. |

---

### PersonalNeed

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| type | ACCESSIBILITY NEED | Part 1 | TM ACCESSIBILITY NEED TYPE — enumerated accessibility requirement (wheelchair, visual impairment, etc.). |

---

### LicenseType

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| type | LICENSE TYPE | Part 6 | EU driving licence category (AM, A1, B, C, D, etc.). |

---

### Animal

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Animal (class) | TRAVELLING ENTITY | Part 6 | TM TRAVELLING ENTITY sub-type for non-human participants. |
| type | ANIMAL TYPE | Part 6 | Category of animal (dog, cat, other). |
| assistant | ASSISTANCE ANIMAL | Part 1 | Whether the animal is a registered assistance/guide animal. |

---

### PassengerVehicle

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| PassengerVehicle (class) | VEHICLE | Part 1 / Part 6 | TM VEHICLE (traveller-owned); distinct from operator-owned VEHICLE used in scheduling. |
| type | VEHICLE TYPE | Part 1 | Category of vehicle (car, motorhome, bicycle, etc.). |
| height / width / length / weight | VEHICLE TYPE | Part 1 | Physical dimensions that may restrict loading capacity. |
| trailer | VEHICLE | Part 1 | Towed trailer modelled as a nested VEHICLE. |
| vehicleRacks | EQUIPMENT | Part 1 | Mounted equipment items (TM INSTALLED EQUIPMENT / PLACE EQUIPMENT). |

---

### VehicleRack

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| VehicleRack (class) | EQUIPMENT | Part 1 | TM INSTALLED EQUIPMENT — an item fitted to a vehicle. |
| type / mounting | EQUIPMENT TYPE | Part 1 | Category and attachment method. |
| height / width / length / weight | EQUIPMENT | Part 1 | Physical dimensions of the rack with load. |

---

### Luggage

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Luggage (class) | TRAVELLING ENTITY | Part 6 | TM TRAVELLING ENTITY sub-type for transported items. |
| type | LUGGAGE TYPE | Part 6 | Category of item (bicycle, pram, wheelchair, skis, etc.). |
| length / width / height / weight | LUGGAGE ALLOWANCE | Part 6 | TM LUGGAGE ALLOWANCE defines dimension/weight constraints on transported items. |

---

### EntitlementRight

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| EntitlementRight (class) | ENTITLEMENT PRODUCT | Part 6 | TM ENTITLEMENT PRODUCT — a credential (card, concession, voucher) conferring access or discount rights (§6.5.4). |
| issuer | RESPONSIBILITY SET | Part 1 | The organisation issuing the entitlement. |
| entitlementType | ENTITLEMENT PRODUCT TYPE | Part 6 | Classifies the entitlement (loyalty card, discount card, concession, etc.). |
| code | CUSTOMER ENTITLEMENT | Part 6 | The instance identifier linking a specific TRANSPORT CUSTOMER to the ENTITLEMENT PRODUCT. |

---

## Trip-context concepts

### TripPattern

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| TripPattern (class) | TRIP PATTERN | Part 3 | TM TRIP PATTERN — a planned sequence of legs a passenger intends to travel (§3.5). |
| tripPatternId | TRIP PATTERN | Part 3 | Caller-assigned identifier for the pattern. |
| legs | LEG | Part 3 | Each leg in the TRIP PATTERN. |

---

### Trip

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Trip (class) | TRIP | Part 3 | TM TRIP — a specific realisation of a TRIP PATTERN on fixed departures. |
| tripId | TRIP | Part 3 | |
| legs | LEG | Part 3 | |

---

### Leg (base)

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Leg (class) | LEG | Part 3 | TM LEG — a single element of a TRIP PATTERN between two consecutive points (§3.5.2). |
| legId | LEG | Part 3 | |
| sequenceNumber | LEG | Part 3 | Ordering within the TRIP PATTERN. |
| mode | TRANSPORT MODE | Part 1 | TM TRANSPORT MODE associated with the leg. |
| legType | (discriminator) | — | |

---

### TimedLeg

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| TimedLeg (class) | SERVICE JOURNEY (LEG) | Part 3 | TM LEG realised by a SERVICE JOURNEY (timetabled). |
| serviceJourneyRef | SERVICE JOURNEY | Part 3 | Reference to TM SERVICE JOURNEY — a passenger-carrying journey on a specific day. |
| operatingDate | OPERATING DAY | Part 3 | TM OPERATING DAY on which the SERVICE JOURNEY runs. |
| lineNumber | LINE | Part 2 | Commercial LINE associated with the SERVICE JOURNEY. |
| brand | BRANDING | Part 1 | TM BRANDING — a commercial identity applied to a service. |
| start | CALL (boarding) | Part 3 | TM CALL at the boarding stop; specifically the departure CALL. |
| end | CALL (alighting) | Part 3 | TM CALL at the alighting stop; specifically the arrival CALL. |
| intermediateStops | CALL | Part 3 | Intermediate TM CALLs passed but not boarded/alighted. |

---

### ContinuousLeg

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| ContinuousLeg (class) | CONTINUOUS LEG | Part 3 | TM CONTINUOUS LEG — a leg without fixed timetabled stops (walk, cycle, taxi). |
| startTime / endTime | CONTINUOUS LEG | Part 3 | Planned start/end time. |
| startLocation / endLocation | PLACE | Part 1 | TM PLACE — origin/destination of the continuous leg. |

---

### TransferLeg

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| TransferLeg (class) | INTERCHANGE LEG | Part 3 | TM INTERCHANGE (or CONNECTION) — a leg representing a transfer between services. |

---

### LegBoard

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| LegBoard (class) | CALL (departure) | Part 3 | TM CALL — the event of a vehicle stopping at a SCHEDULED STOP POINT, specifically the departure event. |
| stopPlaceRef | STOP PLACE / SCHEDULED STOP POINT | Part 2 | NeTEx reference to TM SCHEDULED STOP POINT. |
| plannedDepartureTime | CALL | Part 3 | Planned departure time at the boarding CALL. |
| pickupLocation | QUAY / BOARDING POSITION | Part 2 | Specific platform or bay within the STOP PLACE. |
| trainNumbers | TRAIN NUMBER | Part 3 | TM TRAIN NUMBER — commercial number identifying a train service. |

---

### LegAlight

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| LegAlight (class) | CALL (arrival) | Part 3 | TM CALL at the alighting stop, specifically the arrival event. |
| stopPlaceRef | STOP PLACE / SCHEDULED STOP POINT | Part 2 | NeTEx reference to TM SCHEDULED STOP POINT. |
| plannedArrivalTime | CALL | Part 3 | Planned arrival time at the alighting CALL. |
| setDownLocation | QUAY / BOARDING POSITION | Part 2 | Specific platform or bay within the STOP PLACE. |

---

### TrainNumber

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| trainNumberId | TRAIN NUMBER | Part 3 | TM TRAIN NUMBER — commercial service number (e.g. ICE 123). |

---

### StopPlace

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| stopPlaceRef | STOP PLACE | Part 2 | TM STOP PLACE — a place where passengers board or alight. |

---

### Place hierarchy

| EUDIT concept | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Place | PLACE | Part 1 | TM PLACE — the root location concept. |
| StopPlaceInput | STOP PLACE / SCHEDULED STOP POINT | Part 2 | Identified by a NeTEx stop reference. |
| AddressInput | ADDRESS | Part 1 | TM ADDRESS — a postal address as a place. |
| PointOfInterest | POINT OF INTEREST | Part 1 | TM POINT OF INTEREST — a named place with no structural role in the network. |
| TopologicalPlace | TOPOGRAPHIC PLACE | Part 1 | TM TOPOGRAPHIC PLACE — a geographic area (municipality, region, zone). |
| GeoPosition | LOCATION | Part 1 | TM LOCATION — a geodetic position (WGS-84 latitude/longitude). |

---

## Response-side concepts

### Offer

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Offer (class) | SALES OFFER PACKAGE | Part 6 | TM SALES OFFER PACKAGE — a combination of FARE PRODUCTs available for purchase (§6.5.2). |
| offerId | SALES OFFER PACKAGE | Part 6 | Server-assigned identifier for the offer. |
| elements | SALES OFFER PACKAGE ELEMENT | Part 6 | Components of the SALES OFFER PACKAGE. |
| farePrice | FARE PRICE | Part 6 | TM FARE PRICE — the monetary value assigned to a fare element. |
| travelDocument | TRAVEL DOCUMENT | Part 6 | TM TRAVEL DOCUMENT — the document issued as proof of entitlement. |

---

### OfferElement

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| OfferElement (class) | SALES OFFER PACKAGE ELEMENT | Part 6 | TM SALES OFFER PACKAGE ELEMENT — a component within a SALES OFFER PACKAGE. |
| offerElementId | SALES OFFER PACKAGE ELEMENT | Part 6 | |
| travellingEntities | TRAVELLING ENTITY | Part 6 | The entities to whom this element applies. |
| farePrice | FARE PRICE | Part 6 | |
| travelDocument | TRAVEL DOCUMENT | Part 6 | |
| guarantees | GUARANTEE | Part 6 | TM GUARANTEE — a commitment associated with an offer element (connection guarantee, seat guarantee). |

---

### TravelRight

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| TravelRight (class) | ACCESS RIGHT ASSIGNMENT | Part 6 | TM ACCESS RIGHT ASSIGNMENT — assigns a right to travel to a specific passenger on a specific leg or zone (§6.5.3). |
| legRef | SERVICE JOURNEY | Part 3 | The specific leg to which the travel right applies. |
| zoneRef | TARIFF ZONE | Part 6 | TM TARIFF ZONE — the zone scope of a zone-based travel right. |

---

### Ancillary

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Ancillary (class) | SUPPLEMENTARY PRODUCT | Part 6 | TM SUPPLEMENTARY PRODUCT — an optional add-on service (seat selection, meal, bicycle transport). |
| ancillaryId | SUPPLEMENTARY PRODUCT | Part 6 | |
| type | SUPPLEMENTARY PRODUCT TYPE | Part 6 | Category of ancillary service. |

---

### Reservation

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Reservation (class) | SEAT RESERVATION | Part 6 | TM SEAT RESERVATION — a reserved space on a specific service. |

---

### CompositeOfferElement

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| CompositeOfferElement (class) | SALES OFFER PACKAGE | Part 6 | A nested SALES OFFER PACKAGE grouping two or more SALES OFFER PACKAGE ELEMENTs sold as a unit. |
| elements | SALES OFFER PACKAGE ELEMENT | Part 6 | |

---

### FarePrice

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| FarePrice (class) | FARE PRICE | Part 6 | TM FARE PRICE — monetary value of a fare or fare element (§6.4.5). Detail TBD. |

---

### FareProduct

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| FareProduct (class) | FARE PRODUCT | Part 6 | TM FARE PRODUCT — an immutable element in a fare structure representing a type of access right (§6.5.1). Detail TBD. |

---

### TravelDocument

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| TravelDocument (class) | TRAVEL DOCUMENT | Part 6 | TM TRAVEL DOCUMENT — the issued proof of entitlement (ticket, barcode, smart card record) (§6.6.1). Detail TBD. |

---

### Guarantee

| EUDIT property | Transmodel concept | TM package | Notes |
|---|---|---|---|
| Guarantee (class) | GUARANTEE | Part 6 | TM GUARANTEE — a contractual commitment associated with an offer element. Detail TBD. |
