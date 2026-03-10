### Part 1 — Use Case Overview

* **Use Case ID & Name:** 6.5.2.1 — Choice of a solution by FARE PRODUCT catalogue consultation
* **Goal (Objective):** Enable the Transport Customer to select the most suitable fare solution by consulting the Fare Product catalogue and providing the information required to determine the best offer (product, package, price and guarantees).
* **Scope:** Offer discovery / catalogue consultation (Fare Product catalogue)
* **Level:** User-goal
* **Trigger:** A customer intends to travel and requests a suitable fare solution.
* **Origin / Reason:** Derived from CoRoM; considered mandatory (no solution can operate without this capability).
---

### Part 2 — Actors & Context

* **Primary Actor:** Transport Customer (represented by the Reseller)
* **Supporting Actors / Stakeholders:**

  * **Reseller (API consumer / retailer):** supports the customer and initiates catalogue consultation
  * **Operator (represented by a Fare Product Distributor):** provides the Fare Product catalogue, prices, availability and guarantees
* **Assumptions:**

  * The reseller is authorised to consult the operator’s Fare Product catalogue.
  * The catalogue and related pricing/guarantee information are available (online service or accessible dataset).
  * The customer can provide the additional information required to compute/confirm the best offer.

---

### Part 3 — Preconditions & Postconditions

* **Preconditions (must be true before start):**

  * Access to the operator’s Fare Product catalogue is available to the reseller.
  * The relevant catalogue (for the operator/network/area) is identified.
  * Basic travel intent is known (at minimum: the customer wants to travel; optionally: zone/route/date/passenger profile).

* **Postconditions — Success guarantees:**

  * One or more candidate offers are identified, including:

    * selected **Fare Product(s)** and **Sales Offer Package(s)** (if applicable)
    * the associated **Price**
    * applicable **Travel Guarantees / conditions**
    * **availability** information (where applicable)
  * The selected option (or shortlist) is available for the next step (e.g., purchase/booking).

* **Postconditions — Minimal guarantees:**

  * If no suitable solution is found, the customer receives a clear “no matching offer” outcome (with a reason where possible).
  * The consultation outcome can be logged/audited (if required by the system).

---

### Part 4 — Main Success Scenario (Happy Path)

1. The reseller opens the operator’s **Fare Product catalogue** for the customer.
2. The system displays the list of available catalogue items relevant to the customer’s context.
3. The customer (via the reseller) reviews the product list and selects one or more catalogue items.
4. The reseller captures additional information required to determine the best offer (e.g., passenger profile, eligibility/discounts, validity zone, dates, quantity).
5. The system retrieves and/or calculates the corresponding **price**, including applicable rules and conditions.
6. The system provides the offer details, including **Sales Offer Package** options (if applicable), **travel guarantees/after-sales conditions**, and **availability** where relevant.
7. The reseller presents the results to the customer for comparison and selection.
8. The customer chooses the preferred solution (or requests a shortlist for the next step).

### Part 5 — Alternative Flows (Variants)

**A1 — Customer refines the search using filters**
Condition: The initial catalogue view does not show a suitable product.

* The customer navigates through catalogue pages and/or applies pre-defined filters (operator, locally usable products, popularity, marketing priorities).
* The system refreshes the displayed Sales Offer Packages until a suitable one is found. 

**A2 — “Simple” product (no additional data required)**
Condition: The selected Sales Offer Package is simple (e.g., an immediate single ticket).

* The customer selects the product.
* The system can provide an indicative price immediately at selection time. 

**A3 — Product requires additional customer inputs**
Condition: The selected Sales Offer Package is not fully defined.

* The system prompts for missing information (e.g., time of travel, seating class, possession of a railcard).
* Once provided, the system completes the product definition and updates price/conditions accordingly. 

**A4 — Reservation optional vs mandatory**
Condition: The product definition states reservation is not needed / optional / mandatory.

* If optional, the customer may proceed without reservation (indicative price possible) or start reservation to confirm availability/final price.
* If mandatory, the customer must start reservation to obtain availability and final price. 

---

### Part 6 — Exception Flows (Errors)

**E1 — No availability during reservation**
Condition: Reservation is started and availability is not confirmed (quota full / no seats).

* The system indicates “no availability”.
* The customer changes an option (e.g., comfort class) or selects another Fare Product / Sales Offer Package. 

**E2 — Offer remains undefined due to missing data**
Condition: Required parameters are not provided (e.g., class, railcard).

* The system cannot compute a final offer and prompts the customer to provide the missing data or choose a different product. 

---

### Part 7 — Business Rules

* **Catalogue presentation rules:** Products may be ordered by operator, popularity, local usability, marketing rules, or operator priorities. 
* **Usage parameters:** Travel conditions can include interchange allowance, break of journey, validity duration, etc. 
* **Commercial conditions:** Rules may apply for exchanging, refunding, cancelling, reserving, etc. 
* **Optional paid parameters:** Some parameters may be optional but available at an additional price (e.g., different luggage allowances). 
* **Reservation rule:** Reservation can be not needed / optional / mandatory depending on the Sales Offer Package definition. 
* **Pricing rule:**

  * Without reservation or without additional data, an **indicative** price may be provided early. 
  * With reservation, the customer obtains the price for a completely defined Travel Package (final price after availability). 

---

### Part 8 — Data (Inputs & Outputs)

**Inputs (from customer/channel):**

* Fare Product / Sales Offer Package selection (catalogue item). 
* Additional defining data when needed: time of travel, seating class, railcard/reduction cards, and other usage parameters. 
* Reservation/availability parameters (when applicable): quota vs seat selection on a map for scheduled journeys. 
* Optional extras selection (e.g., luggage allowance option). 

**Outputs (to customer/channel):**

* List of Sales Offer Packages and detailed information (network/lines/stops/connections). 
* Travel conditions and commercial conditions (exchange/refund/cancellation/reservation rules). 
* Availability result (if reservation process is started). 
* Price: indicative (early) and/or final price (after full definition and availability when relevant). 

---

### Part 9 — Interfaces / API (if applicable)

*(The source text describes the functional behaviour but not specific endpoints; below is the interface intent implied by the use case.)* 

* **Catalogue consultation:** Retrieve/present Fare Products and Sales Offer Packages, with filtering and ordering. 
* **Optional availability / reservation initiation:** Start a reservation process to obtain availability (quota or seat map selection). 
* **Pricing request:** Request and/or calculate prices for the selected Sales Offer Package(s), returning indicative or final prices depending on definition/reservation. 

---

### Part 10 — Non-functional Requirements & Traceability

* **Usability:** The catalogue should be organised to be easy to use (clear pages, filters, prioritisation rules). 
* **Performance:** Indicative prices should be available early when possible; reservation flows should return availability and final price promptly to support customer choice. 
* **Consistency:** Usage parameters and commercial conditions must be consistent with the chosen Sales Offer Package and reflected in the returned conditions/guarantees. 
* **Traceability / origin:** Use case derived from the EUDIT repository entry “6.5.2.1 catalogue offers”. 

If you want, I can also convert Parts 1–10 into **slide-ready bullets** (one part = one slide), matching exactly the deck format you added from slide 3.
