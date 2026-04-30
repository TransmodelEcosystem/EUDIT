## Use Case Overview

- **Business Use Case ID & Name:** BUC-D — Pay your CUSTOMER PURCHASE PACKAGE(s)
- **Goal (Objective):** Enable the TRANSPORT CUSTOMER to pay with different PAYMENT METHODs for each CHARGING MOMENT of each CUSTOMER OFFER PACKAGE, with PRICE information fixed at the end of preceding use case or updated with payment. The TRANSPORT CUSTOMER receives receipt, billing and payment terms with detailed instalments. 
- **Scope:** CUSTOMER OFFER PACKAGE completion with CHARGING MOMENT / PAYMENT METHOD confirmation/ PRICE update is necessary / dependency management between basket elements / PAYMENT METHOD update / payment / receipts and billing
---

## Actors & Context

- **Primary Actor:** **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wants to pay with his chosen PAYMENt METHOD a consistent basket suited to his TRAVEL, for himself and/or for other travellers and receive all proofs of payment.

- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** manages the shopping flow and customer interaction, presents the TRAVEL BASKET to the TRANSPORT CUSTOMER and may manage a logical and/or technical basket (basket consistency, requests sent to one or more distributors). He can manage the interface with the bank and/or purchase invoice
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** provides offer PRICE and information on available CHARGING MOMENT(s) for each CUSTOMER PURCHASE PACKAGE. He can manage the interface with the bank and/or purchase invoice.  

- **Assumptions (context at start):**
  - The TRANSPORT CUSTOMER has already complete one or more CUSTOMER PURCHASE PACKAGE(s) in the previous use case.
  - The TRANSPORT CUSTOMER can provide the additional information required to pay for the selected offer(s).
  - The interfaces with the banks and the Payment Service Provider equipment(s) are available but out of scope of this project.
  - Depending on the implementation, payment operations may be executed:
    - only in a retailer side;
    - only in a distributor side; or
    - in a third-party system.
  - The use case is PSP implementation-neutral and therefore specifies the required business entries and results, not the internal technical bank exchange.

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - Access to the distributor’s payment system is available/authorized to the retailer and/or TRANSPORT CUSTOMER.
  - The relevant payment processing system(s) of the selected offer elements are identified.
  - The distributor’s selling system and related pricing rules with dependency information (PAYMENT METHOD) are available (online service or accessible dataset).
  - At least one payment is to be done (amount different from 0).
  - The TRANSPORT CUSTOMER context needed to continue the purchase is available or can be entered, including traveller data, rights, options, delivery data, invoicing data or VAT context where required.

- **Postconditions — Success guarantees:**
  - A TRAVEL BASKET exists and contains one or more completed and consistent TRAVEL BASKET ELEMENTs according to the TRANSPORT CUSTOMER’s actions.
  - Each TRAVEL BASKET ELEMENT contains one CUSTOMER OFFER PACKAGE and the information needed for continuation : 
    - selected **CUSTOMER OFFER PACKAGE(s)**, including quantity
    - the associated current **PRICE**
    - applicable **reductions, TRAVEL GUARANTEEs and aftersales conditions**
    - any dependency information linking it to other TRAVEL BASKET ELEMENTs
    - optional protection or guarantee services selected during shopping, where applicable
    - group-specific quotation results, where applicable
    - approval-hold status, where applicable
    - identified reservation-related constraints or intermediate reservation results, where applicable
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
1. The TRANSPORT CUSTOMER shall start this use case after preceding use case : the travel basket is fixed but shipping costs and payment method fees have not yet been calculated. CHARGING MOMENT and operation fees (by example : after-sales fees) are validated by the TRANSPORt CUSTOMER

#### Options choice
Shipping costs
Platform feees and promotions
Payment method fees


2. 
- **Display options**  

- **Final price calculation**


#### PAYMENT METHODs
- **add payment method**
On his account
add SEPA mandate / CB direct debit

#### Payment
- **immediate**
monney
dematerialized CB : 

- **delayed**
immediate prélèvement

- **by installments**
each month
installment management with unpaid invoices management

#### Proofs of payment
21. The re  
  Receipt
  Invoice
Payment terms and instalements

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

- **Negative amount**
1. The TRANSPORT CUSTOMER has selected purchase and after-sales operations that drives to a negative amount.
2. The Retailer 

- **Marketplace**
1. The TRANSPORT CUSTOMER is purchasing on a retailer system managed as a marketplace : each distributor receives his payments after a delay (can be immediate) and with possibly fees applied by the retailer.
2. 


### Diagram 
UML activity diagram



### Links with inputs
wiki/use-cases/inputs/EUDIT.use.cases_20260324_shared.docx
wiki/use-cases/inputs/BRM_EUDIT_V2.3.xlsx

To be completed


