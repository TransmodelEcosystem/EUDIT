## Use Case Overview

- **Business Use Case ID & Name:** BUC-B — Shop your FARE PRODUCT(s) and manage your TRAVEL BASKET with PRICE calculation at any time
- **Goal (Objective):** Enable the TRANSPORT CUSTOMER to create, build, review, modify and validate a TRAVEL BASKET containing one or more CUSTOMER OFFER PACKAGE(s), with updated PRICE information available at any time before the next step of reservation initiation and payment.
- **Scope:** TRAVEL BASKET creation and management / offer completion / PRICE calculation and recalculation / dependency management between basket elements / preparation of a basket ready for the next use case
---

## Actors & Context

- **Primary Actor:** **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wants to purchase offers suited to his TRAVEL, for himself and/or for other travellers. He can be the manager of a group, a PRM, the purchaser for a minor traveler or other with specific needs or none.

- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** manages the shopping flow, the TRAVEL BASKET, customer interaction, basket consistency, and the requests sent to one or more distributors.
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** provides offer completion rules, PRICE information, commercial conditions, after-sales conditions, guarantees, and dependency rules affecting basket elements. 

- **Assumptions (context at start):**
  - The retailer is authorised to request the distributor’s selling system.
  - The distributor’s selling system and related pricing, guarantee, after-sales and dependency information are available (online service or accessible dataset).
  - The TRANSPORT CUSTOMER can provide the additional information required to complete and price the selected offer(s).
  - The TRANSPORT CUSTOMER has already selected one or more candidate CUSTOMER OFFER PACKAGE(s) in the previous use case.
  - This use case ends when the TRAVEL BASKET is complete and ready for the next step; reservation initiation and payment are out of scope and belong to the following use case.

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the distributor’s selling system is available to the retailer and/or TRANSPORT CUSTOMER.
  - The relevant distributor(s) of the selected offer elements are identified.
  - At least one FARE PRODUCT is selected by the CUSTOMER (at minimum: the customer wants to purchase one FARE PRODUCT; optionally: many CUSTOMER OFFER PACKAGEs).
  - The TRANSPORT CUSTOMER context needed to continue the purchase is available or can be entered, including traveller data, rights, options, delivery data, invoicing data or VAT context where required.

- **Postconditions — Success guarantees:**
  - A TRAVEL BASKET exists and contains one or more completed and consistent TRAVEL BASKET ELEMENTs according to the TRANSPORT CUSTOMER’s actions.
  - Each TRAVEL BASKET ELEMENT is associated with::
    - selected **CUSTOMER OFFER PACKAGE(s)**, including quantity
    - the associated current **PRICE**
    - applicable **reductions, TRAVEL GUARANTEEs and aftersales conditions**
    - any dependency information linking it to other TRAVEL BASKET ELEMENTs
    - all customer selections and parameters needed for the next step (**identified reservations** (where applicable))
  - The whole TRAVEL BASKET has a calculated total **PRICE** (can be zero).
  - The selected basket is in a consistent state and is ready for the next use case (reservation initiation and payment).

- **Postconditions — Minimal guarantees:**
  - If the TRANSPORT CUSTOMER abandons the process or no suitable solution is found, the TRAVEL BASKET remains empty. The purchase process is suspended or ended.
  - The TRANSPORT CUSTOMER actions can be logged/audited (if required by the system).
  - After any TRAVEL BASKET operation, the retailer and/or distributor shall ensure that no TRAVEL BASKET ELEMENT remains with an outdated PRICE, an invalid status, or an unresolved dependency with another TRAVEL BASKET ELEMENT. The TRAVEL BASKET shall remain internally consistent after any operation.
  - Any creation, addition, modification, deletion or clearing operation can be logged or audited if required.

---

## Scenarios

### Main scenario

- **TRAVEL BASKET management**
1. The TRANSPORT CUSTOMER shall start this use case after having selected one or more candidate FARE PRODUCT(s) and their corresponding SALES OFFER PACKAGE(s); fulfilled at least one CUSTOMER OFFER PACKAGE in the previous use case.
has selected and  (previous use case) : he adds it in his TRAVEL BASKET as a TRAVEL BASKET ELEMENT.
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
2. The TRANSPORT CUSTOMER can modify one or many elements in the TRAVEL BASKET : he can modify the quantity, the invoice order, the VAT rate,
3. The TRANSPORT CUSTOMER can delete one or many elements in the TRAVEL BASKET  

- **Final price calculation**
4. The TRANSPORT CUSTOMER receives the information of the PRICE on each element, on the reduction amount,
5. The TRANSPORT CUSTOMER can enter a promotion code. The retailer requests each distributor with the promotion code on the relevant FARE PRODUCTs. The reduction amount and final price for the element is returned and dispyed to the TRANSPORT CUSTOMER.
6. The Retailer calculates the final price of the whole TRAVEL BASKET with reduction code (if applicable).  

- **TRAVEL BASKET finalization** 
7. The Retailer calculates the final price of the whole TRAVEL BASKET with reduction code (if applicable).  

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

- **Single anonymous travel**
1. The TRANSPORT CUSTOMER has selected a basic FARE PRODUCT which is immedialtly a CUSTOMER OFFER PACKAGE. He selects the "Purchase in one-clic" option.
2. The TRANSPORT CUSTOEMR does not see the TRAVEL BASKET : he directly arrives on payment interface.

- **Multimodal trip**
1. The TRANSPORT CUSTOMER has used a JOURNEY PLANNER associated with a fare calculator. He has a complete proposal with many CUSTOMER OFFER PACKAGEs for his whole TRAVEL.
2. The TRANSPORT CUSTOMER selects the "Select all in one-clic" option : all the CUSTOMER OFFER PACKAGEs are added in the TRAVEL BASKET.
3. The TRANSPORT CUSTOMER can add, remove, modify his TRAVEL BASKET as described in main scenario.



### Diagram 
UML activity diagram



### Links with use cases

Link to
To be completed


