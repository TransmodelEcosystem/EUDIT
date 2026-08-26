# EUDIT API Use Cases for Indirect Multimodal Travel Distribution
**Status:** Working Draft – Proposed by Amadeus for discussion in the EUDIT Use Case Working Group
**Version:** 1 – shared in EUDIT Github on 24 March 20264

## 0. Introduction
This document presents a structured list of use cases relevant for using EUDIT API in the context of **indirect, multimodal/multi - operator travel distribution**, excluding air transport distribution itself. This list is certainly not exhaustive and is expected to evolve during the EUDIT project.
The scope explicitly covers:
- Distribution of **non ‑ air transport services** (rail, coach, ferry, urban and regional transport, ancillary mobility services etc)
- **Multimodal journeys** combining several modes of transport
- **Multi ‑ operator journeys**, potentially involving several independent transport operators
- **Indirect distribution**, where retailers/independent distributors sell services on behalf of operators
Air transport distribution is **out of scopeas discussed in EUDIT workgroup**. Airlines may appear **only when acting as retailers or distributors of non ‑ air content**.
EUDIT does **not** standardise commercial rules or business models. Instead, it standardises the **ability to express, request, validate, compare, and lifecycle ‑ manage** journeys, offers, orders, passengers and services across multiple operators, modes and distribution channels.
Each use case is described using a **common structure**:
- **Trigger** – what initiates the use case
- **Description** – functional scope
- **Actors** – parties involved

## 1. Actors concerned by EUDIT API use cases
### 1.1 End Customers
- Traveler / Passenger
- Booker / Buyer (e.g. corporate travel arranger, family member)

### 1.2 Retailers / Sellers (Distribution side)
Actors that **interact with EUDIT as clients** of the API:
- **Independent Travel Intermediary / Retailer** 
  - GDS / Travel Platform
  - OTA (B2C)
  - Corporate TMC
  - Tour Operator
  - Metasearch / Aggregator
- **Airline acting as Retailer** 
  - Airline selling multimodal journeys beyond air (rail, transfers, coach, ferry, mobility services, etc.)
- **Rail Retailer** 
  - National or regional rail retailer
- **Multimodal Retail Platform** 
  - MaaS platforms
  - Public transport + long‑distance aggregators

### 1.3 Suppliers / Operators (Content side)
Actors that **provide inventory, services and conditions**:
- **Transport Operators** 
  - Rail operators
  - Coach / bus operators
  - Ferry / maritime operators
  - Urban / regional public transport authorities
  - Mobility providers
- **Ancillary & Mobility Providers** 
  - Seat / comfort / luggage services
  - PRM assistance providers
  - First/last mile mobility (taxi, ride‑hailing, micromobility)
- **Station / Airport Operators** (where relevant)
- **Infrastructure Managers** (where relevant for disruption data)

### 1.4 Financial & Settlement Actors
- **Payment Service Providers (PSP)**
- **Clearing & Settlement Bodies** 
  - BSP / ARC ‑like mechanisms (central accreditation, billing and settlement bodies)
  - Rail or multimodal clearing houses
- **Invoice Issuers / Tax Authorities**
- **Insurance / Guarantee Providers** 
  - Travel protection
  - Missed connection guarantees

### 1.5 Supporting & Regulatory Actors
- **Regulatory Authorities**
- **Passenger Rights Enforcement Bodies**
- **Data Providers** 
  - Timetables, fares, disruptions, accessibility, CO₂ data
### 1.6 Channel / Access Path (non‑actors)
- **Rail operator website** 
- **Airline.com (when distributing non airtransport)**
- **Mobile app** 
- **Self ‑ Booking Tool (Corporate OBT)** 
- **Call center / assisted sales** 
- **Metasearch redirect** 
- **API ‑ to ‑ API (B2B wholesale)**
### 1.7 Note on automated and AI-driven interactions
Automated systems, AI agents, or orchestration layers (e.g. MCP‑based agents) are not modeled as actors in EUDIT use cases.  
 They are considered delegated execution mechanisms or interaction channels, acting on behalf of an accountable actor (traveler, retailer, or operator).  
 All contractual, commercial, regulatory, and settlement responsibilities remain with the represented actor. To be checked how we can make the EUDIT API compliant with these interactions

## 
## 2. Passenger, Eligibility & Entitlement Model (Cross‑cutting)
EUDIT use cases assume the ability to represent passenger attributes consistently **across multiple operators and modes**, without mandating a harmonised taxonomy.
Passenger categories include (non‑exhaustive): Adult, Child, Infant, Youth/Student, Senior, Unaccompanied Minor, Passenger with Reduced Mobility (PRM), Accompanying Person, Group Passenger, Resident / Reduction Beneficiary.
Eligibility attributes include age, residency, reduction programs, corporate identifiers, loyalty identifiers, PRM codes and companion linkage, enabling:
- Correct fare and product mapping per operator
- Comparison of offers across operators and modes
- End‑to‑end servicing of multimodal journeys

## 3. Detailed EUDIT Use Cases
### A. Inspire & Plan
**UCA1 – Multimodal & Multi ‑ operator Journey Discovery**
**Trigger:** A traveler or retailer initiates a journey search.
**Description:** Search journeys across multiple transport modes and multiple operators, including schedules, connections, interchanges, transfer constraints and accessibility attributes.
Filtering options could be of type: exclude, include, preferably or mandatory
**Actors:** Traveler, Retailer, Transport Operators 

**UCA2 – Journey Filtering, Comparison & Ranking**
**Trigger:** A set of journey options across operators and/or modes is returned.
**Description:** Filter, compare and rank journeys by price, duration, number of interchanges, mode mix, GHG/CO₂ impact, comfort, accessibility or operator.
**Actors:** Traveler, Retailer

**UCA3 – Accessibility & PRM Discovery Across Operators**
**Trigger:** Journey search includes accessibility or PRM requirements.
**Description:** Retrieve and compare accessibility characteristics and PRM service availability across all journey legs and operators.
**Actors:** Traveler, Retailer, Transport Operators 

**UCA4 – Fare, Condition & Operator Transparency**
**Trigger:** Journey options are displayed.
**Description:** Retrieve and compare fares, fare families, exchange/refund conditions, passenger rights and operator‑specific restrictions.
**Actors:** Traveler, Retailer, Transport Operators 

**UCA5 – Passenger ‑ aware Multimodal Feasibility Check**
**Trigger:** Search includes specific passenger types (e.g. PRM, unaccompanied minor, companion).
**Description:** Validate feasibility of multimodal and multi‑operator journeys against passenger constraints and operator rules, in particular inform of minimum connection times between several modes or operators and inform about accessibility.
**Actors:** Traveler, Retailer, Transport Operators

**UCA6 – Radius ‑ based & Multi ‑ location Search**
**Trigger:** Origin and/or destination is defined as a zone or radius.
**Description:** Search and compare journeys using multiple nearby stations, stops or terminals across modes.
**Actors:** Traveler, Retailer

**UCA7 – Time ‑ flexible & Duration ‑ based Journey Search**
**Trigger:** Traveler specifies time windows or journey duration constraints.
**Description:** Search and compare journeys matching temporal and duration criteria across operators and modes.
**Actors:** Traveler, Retailer 

**UCA8 – Direct vs Connecting & Mode ‑ mix Filtering**
**Trigger:** Journey results are available.
**Description:** Filter journeys by direct vs connecting, single‑mode vs multimodal, or preferred mode combinations.
Filtering options could be of type: exclude, include, preferably or mandatory
**Actors:** Traveler, Retailer

### B. Shop & Price
**UCB1 – Price Quotation / Offer Creation (Multi ‑ operator)**
**Trigger:** A journey option is selected for pricing.
**Description:**  
The Retailer requests a price quotation for a journey and receives one or more offers, each including pricing conditions, applicable rules, and a defined validity period (timetolive).
Each quotation includes a validity period after which the price and/or availability may no longer be guaranteed.
**Actors:** Retailer, Transport Operators 

**UCB2 – Multimodal & Multi ‑ operator Offer Construction**
**Trigger:** Several legs, operators or modes are combined.
**Description:** The Retailer constructs a multimodal offer composed of multiple operator products.  
The offer includes:
- an overall quotation validity period,
- and, where applicable, componentlevel validity periods reflecting operatorspecific rules.  
The Retailer is informed of the earliest expiry impacting the offer.
**Actors:** Retailer, Transport Operators

**UCB3 – Offer Construction with Private, Corporate or Reduced Fares**
**Trigger:** Corporate identifiers, reduction programmes or private fare entitlements are provided.
**Description:** The Retailer requests and constructs multimodal offers using corporatespecific or negotiated fare products, based on traveler eligibility, corporate agreements, and applicable usage conditions.
The following parameters/rules could be used in the API:
- corporate profiles,
- negotiated fare identifiers,
- fare visibility rules,
- policy constraints.
**Actors:** Retailer, Transport Operators, Corporate Customer

**UCB4 – Pricing & Fare Combination Across Operators**
**Trigger:** Offer spans multiple operators or fare systems.
**Description:** The Retailer requests pricing for combined products, including throughfares where available or aggregated prices where not.
**Actors:** Retailer, Transport Operators

**UCB5 – Offer Hold for Approval (Multimodal)**
**Trigger:** Deferred decision or approval is required.
**Description:** The Retailer requests that a quotation remains valid for a defined period to allow internal approval processes.  
The response indicates whether such a holding period is supported and under which conditions.
**Actors:** Retailer, Corporate Booker

**UCB6 – Quotation Expiry, Refresh & Re ‑ comparison**
**Trigger:** Offer TTL expires or pricing context changes.
**Description:** The Retailer is informed when a quotation expires or requests a refreshed quotation, which may result in updated pricing, conditions, or availability.
**Actors:** Retailer, Transport Operators

**UCB7 – Connection Protection & Guarantee Options**
**Trigger:** Unprotected multi‑operator or multimodal connections are identified.
**Description:** Propose optional protection or guarantee services covering operator or mode changes.
**Actors:** Retailer, Transport Operators, Protection Providers

**UCB8 – Group Travel Offer Request (Multi ‑ operator)**
**Trigger:** Group size exceeds instant booking thresholds.
**Description:** The Retailer requests a group travel quotation, including availability constraints, provisional pricing, and validity conditions across one or more operators or modes.
**Actors:** Retailer, Transport Operators

**UCB9 – Public vs Private Fare Requests Across Operators**
**Trigger:** Fare type is specified.
**Description:** Request, validate and compare public, private, resident or reduction fares across operators.
**Actors:** Retailer, Transport Operators

**UCB10 – One ‑ way, Round ‑ trip & Combinable Multimodal Pricing**
**Trigger:** Journey structure is defined.
**Description:** Price one‑way, round‑trip or combinable journeys involving multiple operators or modes.
**Actors:** Retailer, Transport Operators

**UCB11 – Complex Multimodal Itinerary Pricing**
**Trigger:** Multi‑city or open‑jaw journey is requested.
**Description:** Price complex itineraries involving several operators, modes or passenger sets (e.g. companions joins mid-trip).
**Actors:** Retailer, Transport Operators

**UCB12 – Ancillary Discovery Across Operators (Pricing Only)**
**Trigger:** Traveler explores optional services.
**Description:** Retrieve and compare ancillary pricing and conditions (seats, bags, cabins, PRM services) across operators and modes.
**Actors:** Traveler, Retailer, Transport Operators

**UCB13 – Seat / Space Availability & Layout Retrieval**
**Trigger:** Seat, cabin or space options are requested.
**Description:** Retrieve and compare availability and layouts across operators and transport modes.
**Actors:** Retailer, Transport Operators

**UCB14 – Information on Through Fares / Through Tickets**
**Trigger :** A journey involving multiple legs or operators is priced.
**Description:** Indicate whether a through fare or through ticket exists across multiple legs, including fare scope, conditions, passenger rights, and commercial advantages compared to separate tickets. Enable comparison between through and non-through pricing options.
**Actors:** Retailer, Transport Operators

**UCB15 – Fare Combinability Information**
**Trigger:** Multiple fare products or operators are considered for a single journey.
**Description:** Provide information on whether fares can be combined across legs, bounds, operators, or modes, including constraints, pricing impacts, and aftersales implications.
**Actors:**  
Retailer, Transport Operators 

**UCB16 – Agreement on Journey Continuation (AJC) Information**
**Trigger:** A multimodal or multi-operator journey is constructed.
**Description:** Expose whether agreements on journey continuation apply between operators, including implications for disruption handling, re-accommodation, and passenger protection in case of missed connections.
**Actors:** Retailer, Transport Operators

**UCB17 – Search & Sale of Travel Passes**
**Trigger:** The customer searches for pass-based travel options.
**Description:** Enable search and sale of passes (e.g. Eurail, monthly, annual, regional passes), including eligibility, coverage across modes and operators, and usage constraints.
**Actors:** Traveler, Retailer, Transport Operators, Pass Issuers

**UCB18 – Pass Validity & Conditions Across Operators**
**Trigger:** A pass is selected or evaluated.
**Description:** Retrieve and display pass validity rules, conditions of use, blackout periods, and operator acceptance across multiple modes and operators.
**Actors:** Retailer, Transport Operators, Pass Issuers

### C. Order & Book
**UCC1 – Multimodal & Multi ‑ operator Order Creation**
**Trigger:** The customer confirms the purchase.
**Description:** The Retailer creates a single order referencing multiple suppliers, preserving operatorspecific commitments and retail ownership.
The created order may include a defined payment time limit, after which the order or parts of it may be automatically cancelled or released according to operator rules.
In multioperator orders, payment time limits may apply at order level and/or component level, and the earliest applicable deadline governs payment completion.
**Actors:** Retailer, Transport Operators

**UCC2 – Passenger & Entitlement Management Across Operators**
**Trigger:** Order creation requires passenger data.
**Description:** Transmit passenger details and entitlements consistently to all involved operators.
**Actors:** Retailer, Traveler, Transport Operators

**UCC3 – Order Validation & Partial Confirmation**
**Trigger:** Order is submitted.
**Description:** Validate, confirm or partially confirm components of a multi‑operator order.
**Actors:** Retailer, Transport Operators

**UCC4: Order partial confirmation**
**Trigger:** Booking response received only for a subset of the trip (not all legs confirmed booking)  
**Description:**  
Not all the legs are confirmed by the operator/supplier. The full order is not fully confirmed. 
**Actors:** Retailer, Transport Operators

**UCC5 – Group Order Creation (Multi ‑ operator)**
**Trigger:** Group offer is accepted.
**Description:** The Retailer confirms the group offer and creates a binding order, including passenger lists, payment milestones, and specific group conditions.
Passenger lists may be completed at a later stage (another use case to be added)
**Actors:** Retailer, Transport Operators

**UCC6 – Seat & Ancillary Assignment in Multimodal Orders**
**Trigger:** Seat or ancillary selection is requested.
**Description:** Assign seats or ancillaries per operator, ensuring consistency across the journey.
**Actors:** Traveler, Retailer, Transport Operators

**UCC7 – Seat Preferences & Seating Constraints**
**Trigger:**  
The customer specifies seating preferences.
**Description:**  
Capture and transmit seating preferences and constraints, including seat position, coach, deck, adjacency to another passenger, and mandatory vs preferred requirements, across operators and modes. Support cases where not all the legs or operators or modes included in the journey can satisfy the requested preferences.
**Actors:**  
Traveler, Retailer, Transport Operators

**UCC8 – Seat Maps & Seating Logic Across Operators**
**Trigger:** Seat selection is requested.
**Description:** Retrieve and present seat maps and seating logic across different operators and modes, ensuring consistent representation despite heterogeneous seat models.
**Actors:** Retailer, Transport Operators

**UCC9 – Transportables : Bikes, Cars, Equipment etc**
**Trigger:** The customer requests transport of additional items.
**Description:** Support search, booking, and aftersales of transportables such as bikes (critical), cars (rail or ferry), sport equipment, standalone wheelchairs etc, including availability, pricing, and constraints per operator and mode.
**Actors:** Traveler, Retailer, Transport Operators

**UCC10 – Standalone Seat Search, Order & Aftersales**
**Trigger:** A seat is searched or purchased independently from the journey.
**Description:** Enable search, ordering, modification, and refund of seats as standalone products, post-booking or independently of the original itinerary on journeys involving several modes or operators.  
Example of use case for travelers holding a EURAIL or a yearly pass and willing to reserve the seats on a journey.
**Actors:** Traveler, Retailer, Transport Operators

**UCC11 – Order Splitting (Passengers / Bounds / Modes)**
**Trigger:**  
An existing order needs to be split.
**Description:** Support splitting an order by passenger, by bound, or by mode to enable differentiated payment, servicing, or aftersales handling while preserving traceability to the original order.
**Actors:** Retailer, Transport Operators

### D. Pay
**UCD1 – Single Customer Payment for Multi ‑ operator Orders**
**Trigger:** Order confirmation requires payment.
**Description:** The Retailer collects a single payment from the customer for the full multimodal order.
**Actors:** Traveler, Retailer, PSP

**UCD2 – Deferred Payment with Operator TTL Alignment**
**Trigger:** Payment is deferred.
**Description:** The Retailer completes payment for an existing order within the applicable payment time limit.  
If payment is not completed before expiry, the order or impacted components may be cancelled or revalidated.
**Actors:** Traveler, Retailer, PSP

**UCD3 – Payment Expiry & Inventory Release**
**Trigger:** Payment TTL expires.
**Description:** The Retailer is informed when a payment time limit expires and is notified of the resulting order status (e.g. cancellation, partial cancellation, or required revalidation).
**Actors:** Retailer, Transport Operators

**UCD4: Split Payment & Settlement Instruction**
**Trigger:** Payment completed  
**Actors:** Retailer, Settlement Bodies  
**Description:**  
The Retailer initiates settlement instructions to distribute funds to each operator according to commercial agreements.

**UCD5: Multicurrency & Tax Handling**
**Trigger:** Crossborder journey  
**Actors:** Retailer, Tax Authorities  
**Description:**  
Prices, taxes and currencies are handled in compliance with local fiscal and invoicing regulations.

**UCD6 – Voucher Payment (Full or Partial)**
**Trigger:**  
The customer chooses to pay all or part of an order using one or more vouchers or travel credits.
**Description:**  
Enable the use of vouchers as a payment instrument, either covering the full amount or combined with other payment methods. The retailer retrieves voucher attributes (value, currency, validity, scope of use), validates applicability across operators and modes, applies the voucher amount to the order, and ensures correct residual amount handling for settlement.
**Actors:**  
Traveler, Retailer, Transport Operators, Payment Service Provider (PSP)

**UCD7 – Voucher Issuance After Refund**
**Trigger:**  
A cancellation, change, or disruption results in a refundable amount that is settled as a voucher or credit.
**Description:**  
Issue a voucher representing all or part of the refundable amount, including validity period, usage constraints, currency, and traceability to the original order, passengers, legs, and operators. The voucher may be reusable across future journeys and applicable to multiple modes or operators depending on rules.
**Actors:**  
Retailer, Transport Operators, Traveler

**UCD8 – Mixed Modes of Payment**
**Trigger:**  
The customer elects to pay using multiple payment instruments.
**Description:**  
Support payment scenarios combining several payment modes (e.g. voucher + card, corporate account + personal payment, card + miles). Ensure coordinated authorization, confirmation, and allocation of amounts per operator and per payment method, while preserving a single customer-facing order.
**Actors:**  
Traveler, Retailer, Payment Service Provider (PSP)

**UCD9 – Payment with Miles / Loyalty Redemption**
**Trigger:**  
The customer chooses to redeem loyalty points or miles as a form of payment.
**Description:**  
Enable partial or full payment using loyalty points or miles, including validation of eligibility, conversion rules, and applicability across operators and modes. Support combined payment scenarios where miles are used together with monetary payment.
**Actors:**  
Traveler, Retailer, Transport Operators, Loyalty Program Provider

### E. Ticketing & Fulfilment
**UCE1 – Multi ‑ operator Ticket & Travel Document Issuance**
**Trigger:** Payment is confirmed.
**Description:** The Retailer retrieves and delivers all necessary travel documents (tickets, barcodes, references), possibly in heterogeneous formats.
**Actors:** Retailer, Transport Operators

**UCE2 – Unified Multimodal Itinerary Presentation**
**Trigger:** Order is fulfilled.
**Description:** The Retailer assembles a unified itinerary view covering all legs and operators.
**Actors:** Retailer, Traveler

### F. Pre‑trip & In‑trip Services
**UCF1 – Cross ‑ operator Travel Information & Notifications**
**Trigger:** Operational updates occur.
**Description:** The Retailer receives and relays operational updates (schedule changes, platform changes, service changes, delays) affecting any operator or mode.
**Actors:** Transport Operators, Retailer, Traveler

**UCF2 – Disruption & IROPs Management (Multi ‑ operator)**
**Trigger:** Disruption affects part of the journey.
**Description:** The Retailer retrieves disruption information and available recovery options across operators and modes.
**Actors:** Transport Operators, Retailer

**UCF3 – Assisted Re ‑ accommodation After Disruption**
**Trigger:** Journey is disrupted.
**Description:** Performs reshopping/rebooking, respecting passenger rights, fare conditions, and commercial rules on the concerned operators/modes.
**Actors:** Retailer, Transport Operators, Traveler

**UCF4 – ReshoppingAfter Disruption**
**Trigger:** A disruption affects one or more legs of the journey.
**Description:** Enable the retailer to retrieve alternative journey options across operators and modes, allowing reshopping while preserving passenger rights, fare conditions, and operator responsibilities.
**Actors:** Retailer, Transport Operators

**UCF5 – Partial Reshopping , Rebooking & Refund After Disruption**
**Trigger:** Only part of the journey is disrupted. But it may impact the rest of the journey
**Description:** Support scenarios where only specific legs, bounds, or passengers are reshopped, rebooked, or refunded. Preserve consistency of the remaining journey components and ensure correct recalculation of prices, rights, and responsibilities.
**Actors:**  
Retailer, Transport Operators, Traveler

**UCF6 – Servicing Responsibility Information**
**Trigger:** The customer requires assistance before or during travel.
**Description:** Provide clear information on servicing responsibilities per operator, mode, leg, and bound, including whom to contact, through which channel, and for which type of issue.
**Actors:** Retailer, Transport Operators, Traveler

### G. Aftersales & Servicing
**UCG1 – Change & Exchange Across Operators**
**Trigger:** Traveler requests a voluntary change.
**Description:** Modify multi‑operator itineraries and reprice impacted components.
**Actors:** Traveler, Retailer, Transport Operators

**UCG2 – Aftersales Management per Passenger / Leg / Bound**
**Trigger:**  
An aftersales action is requested.
**Description:**  
Support aftersales operations (change, refund, cancellation, exchange) scoped at different levels: per passenger, per leg, per bound, for all passengers, or for the entire journey, while respecting operator-specific rules.
**Actors:**  
Retailer, Transport Operators, Traveler

**UCG3 – Cancellation, Refund & Voucher Management**
**Trigger:** Cancellation or refund is requested.
**Description:** Apply cancellation rules and refunds per operator and mode.
**Actors:** Retailer, Transport Operators, PSP

**UCG4 – No ‑ show Handling in Multimodal Journeys**
**Trigger:** Passenger does not show up for one component.
**Description:** Apply no‑show consequences and cascading impacts across operators.
**Actors:** Transport Operators, Retailer

**UCG5 – Claims Handling per Mode and per Operator**
**Trigger:**  
A passenger submits a claim (delay, cancellation, service issue).
**Description:** Enable submission and tracking of claims per operator and per mode, including determination of applicable regulations, compensation eligibility, and responsible party.
**Actors:** Traveler, Retailer, Transport Operators, Passenger Rights Bodies

**UCG6 – Responsibility & Liability Information (Detailed)**
**Trigger:** The customer or retailer requests clarification on responsibility.
**Description:** Provide explicit information on responsibility and liability per portion of the journey (leg, bound, operator, mode) and overall, including implications for servicing, compensation, and claims.
**Actors:** Retailer, Transport Operators, Traveler

**UCG7 : Group Change, Cancellation or Reduction**  
**Trigger:** Group composition changes  
**Actors:** Retailer, Transport Operators  
**Description:**  
The Retailer manages group size changes, substitutions, or cancellations according to operatorspecific group conditions.

### H. Post‑trip, Settlement & Reporting
**UCH1 – Multi ‑ operator Settlement & Reconciliation**
**Trigger:** Travel is completed.
**Description:** Perform settlement and reconciliation across retailer and all operators involved.
**Actors:** Retailer, Transport Operators, Clearing Bodies

**UCH2: Invoicing & Accounting**
**Trigger:** Posttrip accounting  
**Actors:** Retailer, Operators  
**Description:**  
Invoices and accounting data are issued in compliance with national and EU regulations.

**UCH3 – Reporting, Analytics & Comparison**
**Trigger:** Reporting is requested.
**Description:** Produce operational, financial, CO2/GHG reports enabling comparison across operators and modes.
**Actors:** Retailer, Transport Operators
