## Use Case Overview

- **Business Use Case ID & Name:** BUC-B — Shop your FARE PRODUCT(s) and manage your TRAVEL BASKET with PRICE calculation at any time
- **Goal (Objective):** Enable the TRANSPORT CUSTOMER to build, review, modify and validate a logical TRAVEL BASKET containing one or more CUSTOMER OFFER PACKAGE(s), with updated PRICE information available at any time before the next use case dealing with reservation initiation and payment.
- **Scope:** TRAVEL BASKET creation and management / CUSTOMER OFFER PACKAGE completion / PRICE calculation and recalculation / dependency management between basket elements / basket consistency across possible retailer-side and/or distributor-side basket implementations / preparation of a ready basket for the next use case
---

## Actors & Context

- **Primary Actor:** **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wants to prepare a consistent basket suited to his TRAVEL, for himself and/or for other travellers. He can be the manager of a group, a PRM, the purchaser for a minor traveler or other with specific needs or none.

- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** manages the shopping flow and customer interaction, presents the TRAVEL BASKET to the TRANSPORT CUSTOMER and may manage a logical and/or technical basket (basket consistency, requests sent to one or more distributors).
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** provides offer completion rules, PRICE information, commercial conditions, after-sales conditions, guarantees, and dependency rules affecting basket elements. May manage a technical basket for one or more basket elements.

- **Assumptions (context at start):**
  - The TRANSPORT CUSTOMER has already selected one or more candidate CUSTOMER OFFER PACKAGE(s) in the previous use case.
  - The TRANSPORT CUSTOMER can provide the additional information required to complete and price the selected offer(s).
  - The TRAVEL BASKET is a logical business object from the customer perspective; its technical implementation may reside with the retailer, with one or more distributors, or be distributed across both.
  - Depending on the implementation, "basket" operations may be executed:
    - only in a retailer "basket";
    - only in a distributor "basket"; or
    - in a retailer "basket" and in one or more distributor "baskets" that must remain aligned.
  - The use case is implementation-neutral and therefore specifies the required business result, not the internal technical ownership of the "basket".

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the distributor’s selling system is available/authorized to the retailer and/or TRANSPORT CUSTOMER.
  - The relevant distributor(s) of the selected offer elements are identified.
  - The distributor’s selling system and related pricing, guarantee, after-sales and dependency information are available (online service or accessible dataset).
  - At least one FARE PRODUCT is selected by the CUSTOMER (at minimum: the customer wants to purchase one FARE PRODUCT; optionally: many CUSTOMER OFFER PACKAGEs).
  - The TRANSPORT CUSTOMER context needed to continue the purchase is available or can be entered, including traveller data, rights, options, delivery data, invoicing data or VAT context where required.

- **Postconditions — Success guarantees:**
  - A TRAVEL BASKET exists and contains one or more completed and consistent TRAVEL BASKET ELEMENTs according to the TRANSPORT CUSTOMER’s actions.
  - Each TRAVEL BASKET ELEMENT contains one CUSTOMER OFFER PACKAGE and the information needed for continuation.
OR ?
  - Each TRAVEL BASKET ELEMENT is associated with::
    - selected **CUSTOMER OFFER PACKAGE(s)**, including quantity
    - the associated current **PRICE**
    - applicable **reductions, TRAVEL GUARANTEEs and aftersales conditions**
    - any dependency information linking it to other TRAVEL BASKET ELEMENTs
    - all customer selections and parameters needed for the next step (**identified reservations** (where applicable))
  - The whole TRAVEL BASKET has a calculated total **PRICE** (can be zero or less).
  - The basket state presented to the customer is consistent, regardless of whether the underlying implementation relies on a retailer basket, a distributor basket, or coordinated baskets across both.
  - This use case ends when the TRAVEL BASKET is ready for the next step; reservation initiation and payment are out of scope and belong to the following use case.

- **Postconditions — Minimal guarantees:**
  - If the TRANSPORT CUSTOMER abandons the process or no suitable solution is found, the TRAVEL BASKET remains empty. The purchase process is suspended or ended.
  - After any TRAVEL BASKET operation, the retailer and/or distributor shall ensure that no TRAVEL BASKET ELEMENT remains with an outdated PRICE, an invalid status, or an unresolved dependency with another TRAVEL BASKET ELEMENT. The TRAVEL BASKET shall remain internally consistent after any operation.
  - If synchronisation between retailer-side and distributor-side basket states is required, the system detects any divergence and prevents continuation until the customer basket state is made consistent again.
  - The TRANSPORT CUSTOMER actions can be logged/audited (if required by the system).

---

## Scenarios

### Main scenario
Note : In Transmodel improved with COROM project proposals, a CUSTOMER OFFER PACKAGE is created when a SALES OFFER PACKAGE has been selected and parameterised for a specific customer context; it is then inserted into the TRAVEL BASKET as part of a TRAVEL BASKET ELEMENT.

- **TRAVEL BASKET management**
1. The TRANSPORT CUSTOMER shall start this use case after having selected one or more candidate FARE PRODUCT(s) and their corresponding SALES OFFER PACKAGE(s); fulfilling at least one CUSTOMER OFFER PACKAGE in the previous use case.
2. The retailer shall expose to the TRANSPORT CUSTOMER a logical TRAVEL BASKET, regardless of whether the underlying technical basket is managed:
   - only by the retailer;
   - only by the distributor; or
   - jointly by the retailer and one or more distributors.
  The logical TRAVEL BASKET may be created explicitly at the TRANSPORT CUSTOMER’s request or implicitly when the first CUSTOMER OFFER PACKAGE is added.
  
3. At the begining of the purchase, the TRANSPORT CUSTOMER can only add to his TRAVEL BASKET as a TRAVEL BASKET ELEMENT either :
   - one selected CUSTOMER OFFER PACKAGE in a single action; or
   - several selected CUSTOMER OFFER PACKAGE(s) in one action, including for a multimodal, multi-leg or multi-operator TRAVEL.
4. For each addition to the TRAVEL BASKET, the retailer shall create one TRAVEL BASKET ELEMENT corresponding to the shopping intention of the TRANSPORT CUSTOMER for the concerned part of the TRAVEL. Each TRAVEL BASKET ELEMENT shall contain one CUSTOMER OFFER PACKAGE representing the customer-specific shopping component derived from the selected SALES OFFER PACKAGE and its customer parameterisation. 
5. With at least one element in the TRAVEL BASKET, the TRANSPORT CUSTOMER shall be able to perform the following operations on the logical TRAVEL BASKET:
   - add one or several in one action TRAVEL BASKET ELEMENT(s),
   - modify one or several in successive actions TRAVEL BASKET ELEMENT(s),
   - remove one or several in one action TRAVEKL BASKET ELEMENT(s),
   - clear the whole TRAVEL BASKET in a single operation.
6. At each step, the TRANSPORT CUSTOMER can see a representation/summary of what he is purchasing and, on demand, the details of the TRAVEL BASKET ELEMENT.
7. On addition or modification, the retailer shall require, and the TRANSPORT CUSTOMER shall provide, all data necessary to build or update the CUSTOMER OFFER PACKAGE, including, where applicable:
   - traveller assignment;
   - quantity;
   - eligibility data;
   - reduction rights;
   - corporate identifiers;
   - negotiated fare identifiers;
   - pass-related data;
   - class or comfort option;
   - ancillary selections;
   - delivery preferences;
   - invoicing order;
   - VAT context; and
   - any other mandatory parameter required by the distributor.
8. On modification operation, the TRANSPORT CUSTOMER can modify one or many elements in the TRAVEL BASKET : he can modify the quantity, the invoice order, the VAT rate,
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
3. On remove operation, the TRANSPORT CUSTOMER can delete one or many elements in the TRAVEL BASKET  

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


