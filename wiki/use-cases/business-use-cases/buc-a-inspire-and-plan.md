
## Use Case Overview

- **Business Use Case ID & Name:** BUC-A — Plan your trip and choose your means to travel and/or your FARE PRODUCT
- **Goal (Objective):** Enable the Transport Customer to select the most suitable mobility offer (transport mode, product, package, price and guarantees) for his TRAVEL.
- **Scope:** Offer discovery (TRIP PLANNER proposals) / catalogue consultation (FARE PRODUCT catalogue) / PRICE calculation / CUSTOMER OFFER PACKAGE constitution
---

## Actors & Context

- **Primary Actor:** TRANSPORT CUSTOMER (represented by the RETAILER (FARE PRODUCT RETAILER role))
- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER role(API consumer)):** supports the customer and initiates catalogue consultation
  - **Operator (FARE PRODUCT DISTRIBUTOR role):** provides the FARE PRODUCT catalogue, prices, availability and guarantees

- **Assumptions (context at start):**
  - The retailer is authorised to consult the operator’s FARE PRODUCT catalogue.
  - The catalogue and related pricing/guarantee information are available (online service or accessible dataset).
  - The customer can provide the additional information required to compute/confirm the best offer.

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the operator’s FARE PRODUCT catalogues is available to the RETAILER and/or TRANSPORt CUSTOMER.
  - The relevant catalogue (for the operator/network/area) is identified.
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

1. The TRANSPORT CUSTOMER is planning his TRAVEL, which can be composed of one or many TRIPs, each consisting of one or many LEGs. He wants to go to his DESTINATION using public transport means and, optionally, come back to the departure point. The TRANSPORT CUSTOMER can connect or not to a customer account on a digital platform.

2.  
   a. If the TRAVEL is complex (multi-LEGs, multi-TRIPs), and/or if the TRANSPORT CUSTOMER does not know how to make his travel, he can use a TRIP PLANNER. It gives one or many answers with detailed TRIP PATTERNs: schedule, transport mode, lines, stops, and connections.  
   b. The TRIP PLANNER can be associated with a fare calculator: the TRANSPORT CUSTOMER can provide required details on travellers (date of birth, name, reduction cards, disabilities) or remain, by default, with a basic COMMERCIAL PROFILE (anonymous adult without reduction for only one trip).  
   c. The fare calculator can provide FARE PRODUCTs that fit the trip, from one basic solution (for today, anonymous single-trip ticket on the network and on the origin-destination) to many elaborated solutions (for the travel day, combined ticket, with reduction and guarantees, including already bought passes). The availability of some assets or mandatory reservations can also be displayed.

3.  
   a. If the TRAVEL is easy to plan, the TRANSPORT CUSTOMER can browse a digital platform, go to a travel agency, an operator desk, or a ticket vending machine in a station to choose mobility tickets. He wants to choose by himself between tickets, composing himself the transport means and the prices they cost.  
   b. Either the TRANSPORT CUSTOMER or the RETAILER on behalf of the TRANSPORT CUSTOMER starts the catalogue consultation. The RETAILER browses the catalogue to retrieve an initial set of candidate FARE PRODUCT(s) and SALES OFFER PACKAGE(s). This catalogue can be statically built with an aggregation and rework of one or many distributor catalogues, themselves built based on fare owner catalogues.  
   c. The TRANSPORT CUSTOMER reviews the initial results and may filter or order the results. If he cannot finalise his selection, he can refine the catalogue to narrow the displayed FARE PRODUCT(s) and SALES OFFER PACKAGE(s) according to the consultation context and the data he provides (new or modified data and/or data coming from TRIP PLANNER requirements).  
   d. One or many times, the TRANSPORT CUSTOMER selects one candidate SALES OFFER PACKAGE and requests details so that he can consult the detailed content, conditions, guarantees, and optional parts of the selected SALES OFFER PACKAGE. This consultation can be done for more than one SALES OFFER PACKAGE at the same time, as long as they can be displayed on the same screen at the same time.

4. If the selected SALES OFFER PACKAGE is not yet fully defined, the TRANSPORT CUSTOMER enters additional customer parameters by providing the required information, such as traveller profile, eligibility, class, date, zone, quantity, or extras. Once the required information is filled in, the RETAILER checks and gives a price with the applicable commercial and travel conditions for the selected SALES OFFER PACKAGE.

5.  
   a. If this PRICE is an indicative price depending on mandatory reservation (for example, a seat), or if the TRANSPORT CUSTOMER wishes to check availability and the system allows it, the TRANSPORT CUSTOMER checks availability and starts the reservation process: the RETAILER verifies availability or starts a temporary hold for the selected SALES OFFER PACKAGE.  
   b. If availability is confirmed, the RETAILER retrieves the final price and returns it with the final conditions for the selected SALES OFFER PACKAGE.

6. Before making a final choice, the TRANSPORT CUSTOMER may change options to modify class, extras, or other defining elements, and the RETAILER refreshes the offer and recalculates the corresponding conditions and price.

7. The TRANSPORT CUSTOMER chooses the preferred solution, and the RETAILER can keep the selected option so that the CUSTOMER OFFER PACKAGE is ready for the next reservation and purchase step.
   
### Alternatives

#### specific trip
1. The TRANSPORT CUSTOMER chooses his origin station and his destination station on the ticket vending machine. 
2. The TRANSPORT CUSTOMER enters required data (customer account, reduction card)
3. The TRANSPORT CUSTOMER chooses between a few SALES OFFER PACKAGEs dispplayed with their PRICE 

#### single anonymous travel
1. The TRANSPORT CUSTOMER starts his mobile application; the home page displays a shortcut for single-trip purchase in one action.
2. He chooses a anonymous single-trip ticket by clicing on this shortcut.





### Diagramm 
<img width="989" height="972" alt="image" src="https://github.com/user-attachments/assets/be52d6c1-97bd-4ed7-ad41-8c0a3a6f6642" />



### Links with use cases

Link to (https://github.com/TransmodelEcosystem/EUDIT/discussions/36#discussioncomment-16183779)
