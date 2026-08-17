## Use Case Overview

- **Business Use Case ID & Name:** BUC-E — Ticketing and fulfilment
- **Goal (Objective):**  
- **Scope:**  ).

---

## Terminology note — 

## Actors & Context

- **Primary Actors:**
   - **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wants to pay with his chosen PAYMENT METHOD(s) a consistent basket suited to his TRAVEL, for himself and/or for other travellers and receive all proofs of payment.
   
- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** manages the shopping flow and customer interaction, presents the payable TRAVEL BASKET to the TRANSPORT CUSTOMER and may manage a logical and/or technical basket (basket consistency, requests sent to one or more distributors). He can manage the interface with the bank (Payment Provider), coordinates the payment and keeps coherent the purchase process during payment. 
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** Provides or confirms the business data needed for payment of the CUSTOMER PURCHASE PACKAGE(s). The Distributor confirms payable amount (based on PRICE(s) calculation) and CHARGING MOMENT(s) with time limit(s), confirms accepted PAYMENT METHOD(s) from a business point of view and confirms reservation, holding, ancillary or guarantee status (next use case). He - indicates whether payment must be completed before final confirmation and applies business consequences of payment success, failure or expiry.


- **Assumptions (context at start):**
  

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
 

- **Postconditions — Success guarantees:**
  

- **Postconditions — Minimal guarantees:**
 

---


## Scenarios

### Main scenario
1.     
   

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

 

### Diagram 
UML activity diagram


### Links with inputs
wiki/use-cases/inputs/EUDIT.use.cases_20260324_shared.docx
wiki/use-cases/inputs/BRM_EUDIT_V2.3.xlsx

To be completed


