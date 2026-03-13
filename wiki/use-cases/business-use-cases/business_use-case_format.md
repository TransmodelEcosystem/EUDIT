
## Part 1 : Use Case Overview

- **Use Case ID & Name:** \<ID\> — \<Use Case Name\>
- **Goal (Objective):** \<Business value in one sentence\>
- **Scope:** \<System / product / domain boundary\>
- **Level:** User-goal / Sub-function / Summary
- **Trigger:** \<What starts the use case\>

---

## Part 2 : Actors & Context

- **Primary Actor:** \<Who initiates and benefits\>

- **Supporting Actors / Stakeholders:**
  - \<Actor/System\> — \<Role\>
  - \<Actor/System\> — \<Role\>

- **Assumptions (context at start):**
  - \<Assumption 1\>
  - \<Assumption 2\>

- **Diagram:**
  - <img width="540" height="467" alt="image" src="https://github.com/user-attachments/assets/705809e9-7809-4393-a287-d36b1287ff88" />

---

## Part 3 : Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - \<Authorisation / access\>
  - \<Required data exists\>
  - \<Service/configuration available\>

- **Postconditions — Success guarantees:**
  - \<What is true if successful\>
  - \<Data/state stored or returned\>

- **Postconditions — Minimal guarantees:**
  - \<What is guaranteed even on failure (logs, no corruption, etc.)\>

---

## Part 4 : Main Success Scenario (Happy Path)

- **User actions:** « list »
1. \<Actor action\>
2. \<System response\>
3. \<Actor action\>
4. \<System response\>
5. \<Outcome achieved\>  
   *(Keep steps observable and testable; typically 5–12 steps.)*

- **Sequence diagram:**
  - <img width="590" height="388" alt="image" src="https://github.com/user-attachments/assets/4d3ff9cc-0e49-4290-b5a9-057a3fed6793" />

---

## Part 5 Alternative Flows (Variants)

- **A1 — \<Variant name\>** (Condition: \<when it happens\>)
  - Change at Step \<n\>: \<what changes\>
  - Outcome: \<result\>

- **A2 — \<Variant name\>** (Condition: \<when it happens\>)
  - Change at Step \<n\>: \<what changes\>
  - Outcome: \<result\>

---

## Part 6 — Exception Flows (Errors)

- **E1 — \<Error name\>** (Condition: \<error condition\>)
  - System behaviour: \<handling / message / retry\>
  - Result: \<failure outcome or return to step n\>

- **E2 — \<Error name\>** (Condition: \<error condition\>)
  - System behaviour: \<handling\>
  - Result: \<failure outcome\>

---

## Part 7 — Business Rules

- **BR1:** \<Eligibility / pricing / validity rule\>
- **BR2:** \<Rule\>
- **BR3:** \<Rule\>

*(If you have a rules catalogue, add rule IDs and references.)*

---

## Part 8 — Data (Inputs & Outputs)

- **Inputs (source → data):**
  - \<Actor/System\> → \<Key inputs\>
  - \<Actor/System\> → \<Key inputs\>

- **Outputs (recipient ← data):**
  - \<Actor/System\> ← \<Key outputs\>
  - \<Actor/System\> ← \<Key outputs\>

- **Stored / persisted information (if any):** \<what is saved\>

---

## Part 9 — Interfaces / API / user-actions

- **Operations / Endpoints:** \<operation names\>

| Format-origin | API name or User action                                                      | Short description                                                                                                                    |
| --------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Ticketing  (example)            | **Catalogue consultation**                                                  | Retrieve/present Fare Products and Sales Offer Packages, with filtering and ordering.                                             |
| OSDM   (example)                | **Search within catalogue (user: “Search a product”)**                      | Query the catalogue using keywords/filters (operator, local products, popularity, marketing priorities, etc.).                       |
| OSDM      (example)             | **Get offer/package details (user: “View product details”)**                | Return the selected package’s full content: usage parameters, commercial conditions, guarantees, optional parts and selection rules. |
| OSDM                  | **Get indicative price (user: “Check price”)**                              | Provide an indicative price when the package is sufficiently defined and no reservation/availability confirmation is required.       |
| OSDM  (example)                 | **Start availability / reservation (user: “Check availability / Reserve”)** | Initiate an availability check (quota or seat map) and, where applicable, create a short-term pre-reservation.                       |
| OSDM (example)                  | **Get final price (user: “Confirm price after availability”)**              | Return the final price for the completely defined travel package, typically after availability/reservation confirmation.             |
| OSDM  (example)                   | **Update selection / options (user: “Change class / add options”)**         | Recalculate conditions and prices when the user changes defining parameters (class, railcard, extras such as luggage allowance).     |
| BoB (example)         | **Catalogue consultation (user: “Browse offers”)**                          | Equivalent capability in BoB-style APIs: consult available fare products/packages and their conditions.                              |
| BoB (example)         | **Availability & pricing (user: “Validate availability and price”)**        | Equivalent capability in BoB-style APIs: check availability (if relevant) and return price/conditions for the chosen package.        |

- **Pagination rules:** \<page size, tokens, ordering\>
- **Error handling:** \<main codes and meanings\>


---

## Part 10 — Non-functional Requirements & Traceability

- **Performance:** \<response time / throughput targets\>
- **Security & privacy:** \<authentication/authorisation, data minimisation\>
- **Availability:** \<SLA expectations\>
- **Auditability:** \<logging, traceability, identifiers\>
- **Accessibility / usability:** \<if relevant\>
- **Related use cases:** \<IDs / names\>
- **References / Origin:** \<spec links, standards, CoRoM, etc.\>
- **Open points:** \<decisions / questions to confirm\>

