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

