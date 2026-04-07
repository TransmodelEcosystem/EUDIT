
## Part 1 : Overview

- **Use Case ID & Name:** \<ID\> — \<Use Case Name\>
- **Goal (Objective):** \<Business value in one sentence\>
- **Scope:** <System / product / domain boundary>
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

### 4.1 User actions: « list » :
1. \<Actor action\>
2. \<System response\>
3. \<Actor action\>
4. \<System response\>
5. \<Outcome achieved\>  
   *(Keep steps observable and testable; typically 5–12 steps.)*
   *user-actions in bold*

### 4.2 Sequence diagram:
   *with user-actions*
   <img width="590" height="388" alt="image" src="https://github.com/user-attachments/assets/4d3ff9cc-0e49-4290-b5a9-057a3fed6793" />


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

### 9.1 Business user-actions and interface mapping
| User action                           | Business intent                                              | Expected result                                                      |
| ------------------------------------- | ------------------------------------------------------------ | -------------------------------------------------------------------- |
| Browse catalogue                      | Open the fare/product catalogue                              | Visible list of products / packages relevant to the customer context |

### 9.2 Interfaces by user-action and standard

#### For each user-action
#### A. User action: “xxxxxxxxxx”

Business meaning:
The reseller opens......


| Standard / source | API / message / user-action                                           | Type                      | Role in this BUC                                                                                         | Parameters / returned data                                                                                                                                                                               |
| ----------------- | --------------------------------------------------------------------- | ------------------------- | -------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ticketing         | `get sale labels`                                                     | Reference data            | Retrieve labels, names and descriptions of FARE PRODUCTs and Sales Offer Packages for catalogue browsing | Returns names, descriptions, labels, comments of FARE PRODUCTs, Sales Offer Packages, payment means, social status and other purchase reference data                                                     |


### **9.3 Operations / Endpoints:** 
Consolidated interface inventory

| Format-origin | API name | User-action | Short description |
|---|---|---|---|
| Ticketing | `get sale labels` | **Browse catalogue** | Retrieves labels, names, and descriptions used to expose Fare Product(s) and Sales Offer Package(s) in catalogue consultation. |

### 9.4 Pagination and state management
**Pagination rules:** \<page size, tokens, ordering\>
  
**State management:** \<data lifecycle modification during use-case\>

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

