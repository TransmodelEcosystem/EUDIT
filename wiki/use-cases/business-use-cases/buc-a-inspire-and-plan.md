
## Use Case Overview

- **Business Use Case ID & Name:** BUC-A — Plan your trip and choose your means to travel
- **Goal (Objective):** Enable the Transport Customer to select the most suitable mobility solution (transport mode, product, package, price and guarantees) for his TRAVEL.
- **Scope:** Offer discovery / catalogue consultation (FARE PRODUCT catalogue) / price calculation
---

## Actors & Context

- **Primary Actor:** TRANSPORT CUSTOMER (represented by the retailer (FARE PRODUCT RETAILER role))
- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER role(API consumer)):** supports the customer and initiates catalogue consultation
  - **Operator (FARE PRODUCT DISTRIBUTOR role):** provides the FARE PRODUCT catalogue, prices, availability and guarantees

- **Assumptions (context at start):**
  - The retailer is authorised to consult the operator’s FARE PRODUCT catalogue.
  - The catalogue and related pricing/guarantee information are available (online service or accessible dataset).
  - The customer can provide the additional information required to compute/confirm the best offer.

---

## Part 3 : Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the operator’s FARE PRODUCT catalogue is available to the reseller.
  - The relevant catalogue (for the operator/network/area) is identified.
  - Basic travel intent is known (at minimum: the customer wants to travel; optionally: zone/route/date/passenger profile).

- **Postconditions — Success guarantees:**
  - One or more candidate offers are identified, including:
    - selected **FARE PRODUCT(s)** and **SALES OFFER PACKAGE(s)** (if applicable)
    - the associated **Price**
    - applicable **Travel Guarantees / conditions**
    - **availability** information (where applicable)
  - The selected option (or shortlist) is available for the next step (e.g., purchase/booking).

- **Postconditions — Minimal guarantees:**
  - If no suitable solution is found, the customer receives a clear “no matching offer” outcome (with a reason where possible).
  - The consultation outcome can be logged/audited (if required by the system).

---

## Scenarii

### Main 

1. The TRANSPORT CUSTOMER is planning his TRAVEL, that can be composed by one or many TRIPS, each by one or many LEGs. He wants to go to his DESTINATION using public transport means. And, optionally, come back to departure point.
2. If the TRAVEL is complex (multi legs, multi trips) and/or if the TRANSPORT CUSTOMER does not know how do his travel, he can use a trip planner. It gives one or many answers with detailled TRIP PATTERNs : schedudle, transport mode, lines, stops, connexions.
3. The TRIP PLANNER can associated with a fare calculator : the TRANSPORT CUSTOMER can give required details on travellers (date od birth, name, reduction cards, disabilities). The fare calculator can give FARE PRODUCTs that fit with the trip, from one basic solution (for today, anonymous single trip ticket on the network et on the origin-destination) to many elaborated solutions (for the travel day, combined ticket, with reduction and guarantees).
4.If the TRAVEL is easy to plan, the TRANSPORT CUSTOMER can come in a travel agency, at an operator desk or on a ticket vending machine in a station to choose his mobility tickets.
-----------------
 Retailer starts the catalogue consultation on behalf of the Transport Customer, and the Retailer System performs **Browse catalogue** to retrieve an initial set of candidate Fare Product(s) and Sales Offer Package(s).
5. The Transport Customer reviews the initial results and may perform **Filter / refine catalogue** to narrow the displayed Fare Product(s) and Sales Offer Package(s) according to the consultation context.
6. The Transport Customer selects one candidate and performs **View product / package details** so that the Retailer can present the detailed content, conditions, guarantees, and optional parts of the selected Fare Product or Sales Offer Package.
7. If the selected Fare Product or Sales Offer Package is not yet fully defined, the Transport Customer performs **Enter customer parameters** by providing the required information, such as traveller profile, eligibility, class, date, zone, quantity, or extras.
8. Once the required information is available, the Retailer System performs **Check indicative price** and returns the indicative price together with the applicable commercial and travel conditions for the selected Fare Product or Sales Offer Package.
9. If needed, the Transport Customer performs **Check availability / start reservation**, and the Retailer System verifies availability or starts a temporary hold for the selected Fare Product or Sales Offer Package.
10. If availability is confirmed, the Retailer System performs **Get final price** and returns the final price and final conditions for the selected Fare Product or Sales Offer Package.
11. Before making a final choice, the Transport Customer may perform **Change options** to modify class, extras, or other defining elements, and the Retailer System refreshes the offer and recalculates the corresponding conditions and price.
12. The Transport Customer chooses the preferred solution, and the Retailer System performs **Keep selected option** so that the chosen Fare Product or Sales Offer Package is ready for the next booking or purchase step.

### Alternatives

#### rail
From 1. : 
1. The TRANSPORT CUSTOMER chooses on the ticket vending machine his origin station and his destination station. 
2. The TRANSPORT CUSTOMER enters required data (customer account...)

#### anonymous travel



### Diagramm 


### Links with use cases

Link to (https://github.com/TransmodelEcosystem/EUDIT/discussions/36#discussioncomment-16183779)
