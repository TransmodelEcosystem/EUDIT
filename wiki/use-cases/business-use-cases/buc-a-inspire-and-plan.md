
## Use Case Overview

- **Business Use Case ID & Name:** BUC-A — Plan your trip and choose your means to travel and/or your FARE PRODUCT
- **Goal (Objective):** Enable the Transport Customer to select the most suitable mobility offer (transport mode, product, package, price and guarantees) for his TRAVEL.
- **Scope:** Offer discovery (journey planner proposals) / catalogue consultation (FARE PRODUCT catalogue) / PRICE calculation / CUSTOMER OFFER PACKAGE constitution
---

## Actors & Context

- **Primary Actor:** **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** needs to purchase products suited to his travel needs. He can be the manager of a group, a PRM, the purschaser for a minor traveler or other with specific needs or not.
- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** supports the customer and initiates catalogue consultation
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** provides the FARE PRODUCT catalogue, prices, availability and guarantees

- **Assumptions (context at start):**
  - The retailer is authorised to consult the distributor’s FARE PRODUCT catalogue.
  - The catalogue and related pricing/guarantee information are available (online service or accessible dataset).
  - The TRANSPORT CUSTOMER can provide the additional information required to compute/confirm the best offer.

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the distributor’s FARE PRODUCT catalogues is available to the retailer and/or TRANSPORT CUSTOMER.
  - The relevant catalogue (for the distributor/network/area) is identified.
  - Basic travel intent is known (at minimum: the customer wants to travel; optionally: zone/route/date/passenger profile).

- **Postconditions — Success guarantees:**
  - One or more candidate offers are identified, including:
    - selected **FARE PRODUCT(s)** and **SALES OFFER PACKAGE(s)** (if applicable)
    - the associated **Price**
    - applicable **TRAVEL GUARANTEEs and aftersales conditions**
    - **availability** information (where applicable)
  - The selected option (or shortlist) is available for the next step (e.g., purchase/booking).

- **Postconditions — Minimal guarantees:**
  - If no suitable solution is found, the customer receives a clear “no matching offer” outcome (with a reason where possible).
  - The consultation outcome can be logged/audited (if required by the system).

---

## Scenarii

### Main 
- **Retreiving product list**
1. The TRANSPORT CUSTOMER is planning his TRAVEL, which can be composed of one or many TRIPs, each consisting of one or many LEGs. He wants to go from TRIP ORIGIN PLACE to his TRIP DESTINATION PLACE using public transport means and, optionally, come back to the departure PLACE (can be a POINT, a SECTION or a ZONE). 

2.  The TRANSPORT CUSTOMER can connect or not to a CUSTOMER ACCOUNT on a digital platform. The TRANSPORT CUSTOMER may (if he wants) prove a particular USER PROFILE (under 25 years old, PRM indications, corporate participation). The TRANSPORT CUSTOMER can candidate to a particular COMMERCIAL PROFILE. He can have his USER PROFILE(s) and /or COMMERCIAL PROFILE(s) already registred with his CUSTOMER ACCOUNT. He may wish to use specific features of his CUSTOMER ACCOUNT (TRAVEL DOCUMENTs, reduction cards, entitlements, vehicle plate, driving licence).

3.  
   a. **If the TRAVEL is complex** (multi-LEGs, multi-TRIPs), and/or if the TRANSPORT CUSTOMER does not know how to make his travel, he can use a journey planner feeded or not with the information from CUSTOMER ACCOUNT. It gives one or many answers with detailed TRIP PATTERNs: schedule, transport mode, lines, stops, and connections.  
   b. The journey planner can be associated with a fare calculator: the TRANSPORT CUSTOMER can provide required details on travellers (date of birth, name, reduction cards, disabilities, PRM services needed, companion, bike spot) or remain, by default, with a basic COMMERCIAL PROFILE (anonymous adult - for only one trip - without reduction). The TRANSPORT CUSTOMER can indicate that he wants to include THIRD-PARTY PRODUCT in the solution (example : museum entry) 
   c. At least, the TRANSPORT CUSTOMER has a web-link towards website of each distributor on each LEG/JOURNEY to browse their catalogue (equivalent to the following step).
   d. If available, a fare calculator can provide FARE PRODUCTs that fit the TRIP(s), from one basic solution on each origin-destination LEG (defaults values used like : "for today", "anonymous" "single-trip ticket on the network") to many elaborated solutions (for the travel specified day, combined ticket, multimodal offers, with reduction and guarantees, including already brought passes). The availability of some assets or mandatory reservations can also be displayed.
    e. The retailer can manage the result displayed with additional indicators, ranking, filtering (operator, line, categories, product name), comparing : price, duration, number of interchanges, mode mix, GHG/CO₂ impact, comfort, accessibility or operator.

5.  
   a. **If the TRAVEL is easy to plan**, the TRANSPORT CUSTOMER can browse a digital platform, go to a travel agency, an distributor desk, or a ticket vending machine in a station to choose mobility tickets. He wants to choose by himself between tickets, composing himself the transport means and the prices they cost. He can also include THIRD-PARTY PRODUCTs in his search. 
   b. Either the TRANSPORT CUSTOMER or the retailer on behalf of the TRANSPORT CUSTOMER starts the catalogue consultation. The retailer browses the catalogue to retrieve an initial set of candidate FARE PRODUCT(s) and SALES OFFER PACKAGE(s). This catalogue can be statically built with an aggregation and rework of one or many distributor catalogues, themselves built based on fare owner catalogues. This catalogue can be partialy or totally dynamically build using real-time requests to distributors. 
   c. The TRANSPORT CUSTOMER reviews the initial results and may filter or order the results. If he cannot finalise his selection, he can refine the catalogue to narrow the displayed FARE PRODUCT(s) and SALES OFFER PACKAGE(s) according to the consultation context and the data he provides (new or modified data and/or data coming from journey planner requirements).

- **Asking for product-based offers**
5. One or many times, the TRANSPORT CUSTOMER selects one candidate SALES OFFER PACKAGE of one or many distributors (multiple transport modes, multiple operators). He requests details so that he can consult the detailed content, conditions, guarantees, and optional parts of the selected SALES OFFER PACKAGE. This consultation can be done for more than one SALES OFFER PACKAGE at the same time, as long as they can be displayed on the same screen at the same time. 

6. If the selected SALES OFFER PACKAGE is not yet fully defined, the TRANSPORT CUSTOMER enters additional customer parameters by providing the required information, such as traveller profile, eligibility, accessibility needs, class, date, zone, quantity, or extras. Once the required information is filled in, the retailer checks the entries and gives a price with the applicable commercial and travel conditions (aftersales including exchange or refund, specific restrictions, guaranteees, passenger rights) for the selected SALES OFFER PACKAGE. It may be composed of several products. 

7.  
   a. If this PRICE is an indicative price depending on mandatory reservation (for example, a seat), or if the TRANSPORT CUSTOMER wishes to check availability and the system allows it, the TRANSPORT CUSTOMER checks availability and, on some distributor's system, may start the reservation process: the retailer checks availability and may start a temporary hold for the selected SALES OFFER PACKAGE.
   b. If the final price is a yield price, at this moment, the retailer requests for a final price (valid for a duration)
   c. If availability is also confirmed, the retailer retrieves the final price and returns it with the final conditions for the selected SALES OFFER PACKAGE.

- **Selection of offers**

8. Before making a final choice, the TRANSPORT CUSTOMER may change options to modify class, extras, or other defining elements, and if needed the retailer refreshes the offer and recalculates the corresponding conditions and price.
9. One by one, the TRANSPORT CUSTOMER can consult, select or discard FARE PRODUCT(s) to build a final complete solution. At each step, on each selected product with reservation, the retailer can start the resevation process with the distributor in order to ensure coherent multiple reservations (transaction managment with multiple distributors). 
10. The TRANSPORT CUSTOMER chooses the preferred solution, and the retailer can keep the selected option in a "whish list" so that the CUSTOMER OFFER PACKAGE is ready for the next reservation and purchase step. The retailer can inform the TRANSPORT CUSTOMER that a part or all the proposition is expired and propose a new price and availabiltiy calculation.  
   
### Alternatives
Alternative scenarii **full compatible** with the main scenario; unsing shortcuts or very detailed specific point of the main scenario.

- **Specific trip**
1. The TRANSPORT CUSTOMER chooses his origin station and his destination station on the ticket vending machine with the option "around the stations : x kilometers". 
2. The TRANSPORT CUSTOMER enters required data (customer account, reduction card).
3. The TRANSPORT CUSTOMER chooses between a few SALES OFFER PACKAGEs dispplayed with their PRICE, on different JOURNEYs with different stations (radius-baseed). 

- **Single anonymous travel**
1. The TRANSPORT CUSTOMER starts his mobile application; the home page displays a shortcut for single-trip purchase in one action.
2. He chooses a anonymous single-trip ticket by clicking on this shortcut.

- **PRM journey**
1. The TRANSPORT CUSTOMER has specific needs that must be guaranted to be filled during his TRAVEL (on the LEG and between LEGs). The journey planner gives solutions to TRAVEL SPECIFICATION with mobility services even on first LEG (from house/postal adress to first STOP POINT), during interchange and on last LEG (from last STOP POINT to final destination).
2. If the journey planner is associated with a fare calculator, the TRANSPORT CUSTOMER receives a fare offer compliant with USER PROFILE and with TRAVEL SPECIFICATION, including continuous additional services and travel guaranteees from house to destination.
3. If the journey planner only proposes basic mobility solutions, the TRANSPORT CUSTOMER must browse the catalogue to select one by one the tickets, services and guaranteees he needs. When his selection fulfills his complete TRAVEL, he has a satisfactory solution.

- **Return trip**
1 . The TRANSPORT CUSTOMER plans a TRAVEL composed of an outward journey and the corresponding return journey. The journey planner can manage the full TRAVEL, requesting only minimal criteria for the return trip (return time and reversing origin-destination).
2. If the journey planner is associated with a fare calculator, the TRANSPORT CUSTOMER can receive a fare offer that takes into account the whole TRAVEL (for example, a daily pass less expensive than two separate tickets).
3. If the journey planner only proposes basic mobility solutions, the TRANSPORT CUSTOMER must browse the catalogue to select one by one the tickets, looking for solution that includes the two TRIPs.
  

### Diagramm 
UML activity diagram

<img width="989" height="972" alt="image" src="https://github.com/user-attachments/assets/be52d6c1-97bd-4ed7-ad41-8c0a3a6f6642" />



### Links with use cases

Link to (https://github.com/TransmodelEcosystem/EUDIT/discussions/36#discussioncomment-16183779)
