## Use Case Overview

- **Business Use Case ID & Name:** BUC-D — Pay your CUSTOMER PURCHASE PACKAGE(s)
- **Goal (Objective):** Enable the TRANSPORT CUSTOMER to pay one or more CUSTOMER PURCHASE PACKAGE(s), possibly provided by several DISTRIBUTOR(s), through a coherent customer-facing payment process, while allowing different payment architectures: retailer-side payment, distributor-side payment, shared payment, or payment through a PAYMENT PROVIDER.

- **Scope:** CUSTOMER OFFER PACKAGE completion with CHARGING MOMENT and PAYMENT METHOD confirmation/ PRICE update is necessary / dependency management between TRAVEL BASKET ELEMENTs / PAYMENT METHOD update / coordination between Retailer, Distributor and Payment Provider / payment result consolidation / payment proof and billing information / ready for next use case (fulfilment).

---

## Terminology note — TRAVEL BASKET, TRAVEL BASKET ELEMENT and ORDER

At the beginning of this use case, the TRANSPORT CUSTOMER may still manage a **locked TRAVEL BASKET** containing one or more **TRAVEL BASKET ELEMENT(s)**, still part of the shopping/purchase preparation state. Each TRAVEL BASKET ELEMENT contains one CUSTOMER PURCHASE PACKAGE, as defined in BUC-B.

The term **ORDER** may be used from the moment the purchase has been confirmed sufficiently to create a binding or pre-binding order state. This is typically after the customer has validated the basket and the Retailer has created, requested or received an ORDER referencing one or more DISTRIBUTOR(s). In that case:
- the TRAVEL BASKET becomes the purchase input;
- each TRAVEL BASKET ELEMENT may become or feed an ORDER component;
- each CUSTOMER PURCHASE PACKAGE remains traceable inside the ORDER;
- payment may be managed at ORDER level and/or ORDER component level.

In this use case, **TRAVEL BASKET / TRAVEL BASKET ELEMENT** is used when describing the payment process until his validation, and **ORDER** is used after the payment validation. However, this may vary depending on the system (and ORDER may be already pre-created at the begining of the use-case).

## Actors & Context

- **Primary Actor:** **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wants to pay with his chosen PAYMENT METHOD(s) a consistent basket suited to his TRAVEL, for himself and/or for other travellers and receive all proofs of payment.
 - **Payment Service Provider (PSP) (PAYMENT PROVIDER ROLE (API not managed by EUDIT project)):** Executes or supports the payment transaction. The PSP :
    - authorises, captures, rejects, secures, schedules or confirms payment;
    - may manage card payment, account debit, voucher, wallet, loyalty redemption, travel account debit or other payment instruments;
    - returns the payment transaction result to the responsible Retailer and/or Distributor.

- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** manages the shopping flow and customer interaction, presents the payable TRAVEL BASKET to the TRANSPORT CUSTOMER and may manage a logical and/or technical basket (basket consistency, requests sent to one or more distributors). He can manage the interface with the bank (Payment Provider), coordinates the payment and keeps coherent the purchase process during payment. 
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** Provides or confirms the business data needed for payment of the CUSTOMER PURCHASE PACKAGE(s). The Distributor confirms payable amount (based on PRICE(s) calculation) and CHARGING MOMENT(s) with time limit(s), confirms accepted PAYMENT METHOD(s) from a business point of view and confirms reservation, holding, ancillary or guarantee status (next use case). He - indicates whether payment must be completed before final confirmation and applies business consequences of payment success, failure or expiry.


- **Assumptions (context at start):**
  - The TRANSPORT CUSTOMER can provide the additional information required to pay for the selected offer(s).
  - The PAYMENT PROVIDER ROLE may be performed, for each CUSTOMER PURCHASE PACKAGE, by:
      - the Retailer or the Distributor;
      - a third-party PSP (can be a bank, a travel account provider, a voucher provider, a loyalty provider,...)
      - or another delegated payment system.
  - The interfaces with the banks and the Payment Service Provider equipment(s) are available.
  - Depending on the implementation, each payment operation may be executed:
    - only in a retailer side;
    - only in a distributor side; or
    - in a third-party system.
  - The use case is PSP implementation-neutral and therefore specifies the required business entries and results, not the internal technical bank exchange.

---

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - The TRANSPORT CUSTOMER has already completed one or more CUSTOMER PURCHASE PACKAGE(s) in the previous use case. They are ready for payment.
  - The relevant payment processing system(s) of each TRAVEL BASKET ELEMENT are identified and can be accessed.
  - The Distributor’s selling system and related pricing rules (with time limits) with dependency information are available (online service or accessible dataset).
  - The TRANSPORT CUSTOMER context needed to continue the purchase is available or can be entered, including traveller data, rights, options, delivery data, invoicing data or VAT context where required.
  - Refunds, compensation, voucher issuance after refund and other after-sales processes are out of scope of present BUC and are handled in use cases **F** and **J** dedicated to after-sales. But they refers to the same PSP interactions and APIs.

- **Postconditions — Success guarantees:**
  - Each CUSTOMER PURCHASE PACKAGE has a payment status, consolidated and given to TRANSPORt CUSTOMER by the Retailer.
  - The basket state presented to the customer is consistent, regardless of whether the underlying implementation relies on a retailer basket, a distributor basket, or coordinated baskets across both.
  - This use case ends when the TRAVEL BASKET is ready for the next step; payment is done.
  - Reservation, option, seat, ancillary, service or guarantee depending on payment will be finalized according to Distributor rules in Business Use Case E.
  - The TRANSPORT CUSTOMER receives the appropriate proof(s): receipt, invoice, payment terms, payment schedule, confirmation. Fulfilment is managed in Business Use Case E (access to TRAVEL DOCUMENT(s)).

- **Postconditions — Minimal guarantees:**
  - If the TRANSPORT CUSTOMER abandons the process or no suitable payment solution is found, the TRAVEL BASKET remains as it is for a delay. The purchase process is suspended or ended.
  - The Distributor indicates whether the impacted component remains held, is released, is cancelled, is partially cancelled or requires revalidation.
  - The Retailer consolidates the resulting state and prevents fulfilment if a required payment condition is not met.
  - If funds or accounting information must be distributed, cleared or reconciled between the Retailer and one or more Distributor(s) are described in Business Use Case K.
  - The TRANSPORT CUSTOMER actions can be logged/audited (if required by the system).
  - If many CUSTOMER PURCHASE PACKAGE(s) are involved, allocation remains traceable per Distributor, ORDER component, payment instrument and settlement rule.

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
VAT on each fees, each amount

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
  Receipt with legal data following rule of each state

  Invoice

Payment terms and instalements

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

- **Negative amount**
1. The TRANSPORT CUSTOMER has selected purchase and after-sales operations that drives to a negative amount.
2. The Retailer 

- **Zero amount**
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


