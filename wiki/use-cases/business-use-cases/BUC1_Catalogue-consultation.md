
## Part 1 : Use Case Overview

- **Business Use Case ID & Name:** BUC1 — Choice of a solution by FARE PRODUCT catalogue consultation
- **Goal (Objective):** Enable the Transport Customer to select the most suitable fare solution by consulting the Fare Product catalogue and providing the information required to determine the best offer (product, package, price and guarantees).
- **Scope:** Offer discovery / catalogue consultation (FARE PRODUCT catalogue)
---

## Part 2 : Actors & Context

- **Primary Actor:** Transport Customer (represented by the Reseller) :contentReference[oaicite:6]{index=6}

- **Supporting Actors / Stakeholders:**
  - **Reseller (API consumer / retailer):** supports the customer and initiates catalogue consultation :contentReference[oaicite:7]{index=7}
  - **Operator (represented by a Fare Product Distributor):** provides the Fare Product catalogue, prices, availability and guarantees :contentReference[oaicite:8]{index=8}

- **Assumptions (context at start):**
  - The reseller is authorised to consult the operator’s Fare Product catalogue. :contentReference[oaicite:9]{index=9}
  - The catalogue and related pricing/guarantee information are available (online service or accessible dataset). :contentReference[oaicite:10]{index=10}
  - The customer can provide the additional information required to compute/confirm the best offer. :contentReference[oaicite:11]{index=11}

- **Diagram:** *(not provided in source document)* :contentReference[oaicite:12]{index=12}

---

## Part 3 : Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the operator’s Fare Product catalogue is available to the reseller. :contentReference[oaicite:13]{index=13}
  - The relevant catalogue (for the operator/network/area) is identified. :contentReference[oaicite:14]{index=14}
  - Basic travel intent is known (at minimum: the customer wants to travel; optionally: zone/route/date/passenger profile). :contentReference[oaicite:15]{index=15}

- **Postconditions — Success guarantees:**
  - One or more candidate offers are identified, including:
    - selected **Fare Product(s)** and **Sales Offer Package(s)** (if applicable) :contentReference[oaicite:16]{index=16}
    - the associated **Price** :contentReference[oaicite:17]{index=17}
    - applicable **Travel Guarantees / conditions** :contentReference[oaicite:18]{index=18}
    - **availability** information (where applicable) :contentReference[oaicite:19]{index=19}
  - The selected option (or shortlist) is available for the next step (e.g., purchase/booking). :contentReference[oaicite:20]{index=20}

- **Postconditions — Minimal guarantees:**
  - If no suitable solution is found, the customer receives a clear “no matching offer” outcome (with a reason where possible). :contentReference[oaicite:21]{index=21}
  - The consultation outcome can be logged/audited (if required by the system). :contentReference[oaicite:22]{index=22}

---

## Part 4 : Main Success Scenario (Happy Path)

- **User actions:** « list » :contentReference[oaicite:23]{index=23}

1. The reseller opens the operator’s **Fare Product catalogue** for the customer. :contentReference[oaicite:24]{index=24}
2. The system displays the list of available catalogue items relevant to the customer’s context. :contentReference[oaicite:25]{index=25}
3. The customer (via the reseller) reviews the product list and selects one or more catalogue items. :contentReference[oaicite:26]{index=26}
4. The reseller captures additional information required to determine the best offer (e.g., passenger profile, eligibility/discounts, validity zone, dates, quantity). :contentReference[oaicite:27]{index=27}
5. The system retrieves and/or calculates the corresponding **price**, including applicable rules and conditions. :contentReference[oaicite:28]{index=28}
6. The system provides the offer details, including **Sales Offer Package** options (if applicable), **travel guarantees/after-sales conditions**, and **availability** where relevant. :contentReference[oaicite:29]{index=29}
7. The reseller presents the results to the customer for comparison and selection. :contentReference[oaicite:30]{index=30}
8. The customer chooses the preferred solution (or requests a shortlist for the next step). :contentReference[oaicite:31]{index=31}

- **Sequence diagram:** *(not provided in source document)* :contentReference[oaicite:32]{index=32}

---

## Part 5 Alternative Flows (Variants)

- **A1 — Customer refines the search using filters** (Condition: The initial catalogue view does not show a suitable product.) :contentReference[oaicite:33]{index=33}
  - Change at Step 2: The customer navigates through catalogue pages and/or applies pre-defined filters (operator, locally usable products, popularity, marketing priorities). :contentReference[oaicite:34]{index=34}
  - Outcome: The system refreshes the displayed Sales Offer Packages until a suitable one is found. :contentReference[oaicite:35]{index=35}

- **A2 — “Simple” product (no additional data required)** (Condition: The selected Sales Offer Package is simple, e.g., an immediate single ticket.) :contentReference[oaicite:36]{index=36}
  - Change at Step 4: No additional information is needed; the system can provide an indicative price immediately at selection time. :contentReference[oaicite:37]{index=37}
  - Outcome: The customer can compare/choose with an immediate indicative price. :contentReference[oaicite:38]{index=38}

- **A3 — Product requires additional customer inputs** (Condition: The selected Sales Offer Package is not fully defined.) :contentReference[oaicite:39]{index=39}
  - Change at Step 4: The system prompts for missing information (e.g., time of travel, seating class, railcard). :contentReference[oaicite:40]{index=40}
  - Outcome: Once provided, the system completes the product definition and updates price/conditions accordingly. :contentReference[oaicite:41]{index=41}

- **A4 — Reservation optional vs mandatory** (Condition: The product definition states reservation is not needed / optional / mandatory.) :contentReference[oaicite:42]{index=42}
  - Change at Step 6: If optional, the customer may proceed without reservation (indicative price) or start reservation for availability/final price; if mandatory, reservation is required to obtain availability and final price. :contentReference[oaicite:43]{index=43}
  - Outcome: Either an indicative price is provided early, or a reservation flow is initiated to confirm availability and final price. :contentReference[oaicite:44]{index=44}

---

## Part 6 — Exception Flows (Errors)

- **E1 — No availability during reservation** (Condition: Reservation is started and availability is not confirmed: quota full / no seats.) :contentReference[oaicite:45]{index=45}
  - System behaviour: The system indicates “no availability”. :contentReference[oaicite:46]{index=46}
  - Result: The customer changes an option (e.g., comfort class) or selects another Fare Product / Sales Offer Package. :contentReference[oaicite:47]{index=47}

- **E2 — Offer remains undefined due to missing data** (Condition: Required parameters are not provided, e.g., class, railcard.) :contentReference[oaicite:48]{index=48}
  - System behaviour: The system cannot compute a final offer and prompts the customer to provide the missing data or choose a different product. :contentReference[oaicite:49]{index=49}
  - Result: Failure to compute final offer until missing parameters are provided (or alternative product chosen). :contentReference[oaicite:50]{index=50}

---

## Part 7 — Business Rules

- **BR1:** Catalogue presentation rules: products may be ordered by network, zone, product type, popularity, local usability, marketing rules, or operator/platform priorities. :contentReference[oaicite:51]{index=51}
- **BR2:** Usage parameters: travel conditions can include interchange allowance, break of journey, validity duration, etc. :contentReference[oaicite:52]{index=52}
- **BR3:** Commercial conditions: rules may apply for exchanging, refunding, cancelling, reserving, etc. :contentReference[oaicite:53]{index=53}
- **BR4:** Optional paid parameters: some parameters may be optional but available at an additional price (e.g., different luggage allowances). :contentReference[oaicite:54]{index=54}
- **BR5:** Reservation rule: reservation can be not needed / optional / mandatory depending on the Sales Offer Package definition. :contentReference[oaicite:55]{index=55}
- **BR6:** Pricing rule:
  - Without reservation or without additional data, an **indicative** price may be provided early. :contentReference[oaicite:56]{index=56}
  - With reservation, the customer obtains the price for a completely defined Travel Package (final price after availability). :contentReference[oaicite:57]{index=57}

---

## Part 8 — Data (Inputs & Outputs)

- **Inputs (source → data):**
  - Customer/channel → Fare Product / Sales Offer Package selection (catalogue item). :contentReference[oaicite:58]{index=58}
  - Customer/channel → Additional defining data when needed: time of travel, seating class, railcard/reduction cards, and other usage parameters. :contentReference[oaicite:59]{index=59}
  - Customer/channel → Reservation/availability parameters (when applicable): quota vs seat selection on a map for scheduled journeys. :contentReference[oaicite:60]{index=60}
  - Customer/channel → Optional extras selection (e.g., luggage allowance option). :contentReference[oaicite:61]{index=61}

- **Outputs (recipient ← data):**
  - Customer/channel ← List of Sales Offer Packages and detailed information (network/lines/stops/connections). :contentReference[oaicite:62]{index=62}
  - Customer/channel ← Travel conditions and commercial conditions (exchange/refund/cancellation/reservation rules). :contentReference[oaicite:63]{index=63}
  - Customer/channel ← Availability result (if reservation process is started). :contentReference[oaicite:64]{index=64}
  - Customer/channel ← Price: indicative (early) and/or final price (after full definition and availability when relevant). :contentReference[oaicite:65]{index=65}

- **Stored / persisted information (if any):** Consultation outcome may be logged/audited (if required by the system). :contentReference[oaicite:66]{index=66}

---

## Part 9 — Interfaces / API / user-actions

- **Operations / Endpoints:** Catalogue consultation; search within catalogue; get offer/package details; get indicative price; start availability/reservation; get final price; update selection/options. :contentReference[oaicite:67]{index=67}

| Format-origin | API name or User action                                                      | Short description                                                                                                                    |
| --------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Ticketing (example)   | **Catalogue consultation**                                                  | Retrieve/present Fare Products and Sales Offer Packages, with filtering and ordering.                                              |
| OSDM (example)        | **Search within catalogue (user: “Search a product”)**                      | Query the catalogue using keywords/filters (operator, local products, popularity, marketing priorities, etc.).                      |
| OSDM (example)        | **Get offer/package details (user: “View product details”)**                | Return the selected package’s full content: usage parameters, commercial conditions, guarantees, optional parts and selection rules. |
| OSDM                 | **Get indicative price (user: “Check price”)**                              | Provide an indicative price when the package is sufficiently defined and no reservation/availability confirmation is required.      |
| OSDM (example)        | **Start availability / reservation (user: “Check availability / Reserve”)** | Initiate an availability check (quota or seat map) and, where applicable, create a short-term pre-reservation.                      |
| OSDM (example)        | **Get final price (user: “Confirm price after availability”)**              | Return the final price for the completely defined travel package, typically after availability/reservation confirmation.             |
| OSDM (example)        | **Update selection / options (user: “Change class / add options”)**         | Recalculate conditions and prices when the user changes defining parameters (class, railcard, extras such as luggage allowance).    |
| BoB (example)         | **Catalogue consultation (user: “Browse offers”)**                          | Equivalent capability in BoB-style APIs: consult available fare products/packages and their conditions.                              |
| BoB (example)         | **Availability & pricing (user: “Validate availability and price”)**        | Equivalent capability in BoB-style APIs: check availability (if relevant) and return price/conditions for the chosen package.       |

- **Pagination rules:** Not specified in the source use case. :contentReference[oaicite:68]{index=68}
- **Error handling:** Main error cases described in Part 6 (no availability; missing required data). :contentReference[oaicite:69]{index=69}

---

## Part 10 — Non-functional Requirements & Traceability

- **Performance:** Indicative prices should be available early when possible; reservation flows should return availability and final price promptly to support customer choice. :contentReference[oaicite:70]{index=70}
- **Security & privacy:** Reseller must be authorised to consult the operator’s Fare Product catalogue; data minimisation implied by requesting only needed parameters. :contentReference[oaicite:71]{index=71}
- **Availability:** Catalogue and pricing/guarantee information should be available (online service or accessible dataset). :contentReference[oaicite:72]{index=72}
- **Auditability:** Consultation outcome can be logged/audited (if required). :contentReference[oaicite:73]{index=73}
- **Accessibility / usability:** The catalogue should be organised to be easy to use (clear pages, filters, prioritisation rules). :contentReference[oaicite:74]{index=74}
- **Related use cases:** Not specified in the source use case. :contentReference[oaicite:75]{index=75}
- **References / Origin:** Use case derived from the EUDIT repository entry “6.5.2.1 catalogue offers” and CoRoM. :contentReference[oaicite:76]{index=76}
- **Open points:** Not specified in the source use case. :contentReference[oaicite:77]{index=77}
