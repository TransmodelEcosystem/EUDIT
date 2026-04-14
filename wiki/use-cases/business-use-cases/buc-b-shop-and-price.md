## Use Case Overview

- **Business Use Case ID & Name:** BUC-B — Shop your FARE PRODUCT(s) with PRICE information
- **Goal (Objective):** Enable the Transport Customer to select for purchase the most suitable mobility offer (transport mode, product, package, price, and guarantees) for his TRAVEL.
- **Scope:** Offer purchasing (basket management) / final PRICE information / CUSTOMER OFFER PACKAGE(s) in TRAVEL BASKET
---

## Actors & Context

- **Primary Actor:** **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** needs to purchase products suited to his travel needs. He can be the manager of a group, a PRM, the purchaser for a minor traveler or other with specific needs or none.
- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** supports the customer in purchase process, provide final price information.
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** provides the 

- **Assumptions (context at start):**
  - The retailer is authorised to request the distributor’s selling system.
  - The distributor’s selling system and related pricing/guarantee information are available (online service or accessible dataset).
  - The TRANSPORT CUSTOMER can provide the additional information required to compute/confirm his purchase.

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the distributor’s selling system is available to the retailer and/or TRANSPORT CUSTOMER.
  - The relevant distributor of each FARE PRODUCT is identified.
  - At least one FARE PRODUCT is selected by the CUSTOMER (at minimum: the customer wants to purchase one FARE PRODUCT; optionally: many).

- **Postconditions — Success guarantees:**
  - One or more candidate offers are ready to be purchased, including:
    - selected **CUSTOMER OFFER PACKAGE(s)**
    - the associated **final PRICE**
    - applicable **TRAVEL GUARANTEEs and aftersales conditions**
    - with **identified reservations** (where applicable)
  - The selected option (or shortlist) is available for the next step (e.g., reservation/payment).

- **Postconditions — Minimal guarantees:**
  - If no suitable solution is found, the TRAVEL BASKET of customer remains empty.
  - The customer actions can be logged/audited (if required by the system).

---

## Scenarios

### Main scenario

- **TRAVEL BASKET management**
1. The TRANSPORT CUSTOMER has selected and fulfilled a CUSTOMER OFFER PACKAGE (previous use case) : he adds it in his TRAVEL BASKET as a TRAVEL BASKET ELEMENT.
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


