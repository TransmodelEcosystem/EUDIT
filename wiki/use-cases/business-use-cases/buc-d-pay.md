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

- **Primary Actors:**
   - **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wants to pay with his chosen PAYMENT METHOD(s) a consistent basket suited to his TRAVEL, for himself and/or for other travellers and receive all proofs of payment.
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
  - This use case ends when the TRAVEL BASKET is ready for the next step; payment status is known.
  - Reservation, option, seat, ancillary, service or guarantee depending on payment will be finalized according to Distributor rules in Business Use Case E.
  - The TRANSPORT CUSTOMER receives the appropriate proof(s): receipt, invoice, payment terms, payment schedule, confirmation. Fulfilment is managed in Business Use Case E (access to TRAVEL DOCUMENT(s)).

- **Postconditions — Minimal guarantees:**
  - If the TRANSPORT CUSTOMER abandons the process or no suitable payment solution is found, the TRAVEL BASKET may remain as it is for a delay. The purchase process is suspended or ended.
  - The Distributor indicates which payment part remains held, is released, is cancelled, is partially cancelled or requires revalidation.
  - The Retailer consolidates the TRAVEL BASKET state and prevents fulfilment if a required payment condition is not met.
  - If funds or accounting information must be distributed, cleared or reconciled between the Retailer and one or more Distributor(s) are described in Business Use Case K.
  - The TRANSPORT CUSTOMER actions can be logged/audited (if required by the system).
  - If many CUSTOMER PURCHASE PACKAGE(s) are involved, allocation remains traceable per Distributor, ORDER component, payment instrument and settlement rule.

---


## Scenarios

### Main scenario
1. The TRANSPORT CUSTOMER shall start this use case after the preceding shopping, reservation and order-related use case(s), when one or more CUSTOMER PURCHASE PACKAGE(s) have been selected, parameterised, inserted into a TRAVEL BASKET or ORDER and locked or stabilized for payment.At this moment, it may remain elements that modify the final payment : shipping costs, payment method fees, CHARGING MOMENT, Retailer platform feees or  promotions and operation fees (by example : after-sales fees).

- **Payment options choice**
2. The Retailer presents to the TRANSPORT CUSTOMER a summarized payable view :
   - CUSTOMER PURCHASE PACKAGE(s) to be paid;
   - PRICE of each and total amount for the current options;
   - selected or available PAYMENT METHOD(s);
   - CHARGING MOMENT(s);
   - payment deadline(s) or milestones for group purchase;
   - fees, taxes and currency(s) information including available shipping possibilities and operation fees;
   - any deferred amount or future instalment amount, where applicable;
   - main conditions attached to payment, linked to deferred payment, B2B invoicing or corporate approval;
   - main consequences of payment failure or expiry.

3. The TRANSPORT CUSTOMER selects and/or confirms the PAYMENT METHOD(s) and options. 
4. If required, the Retailer and/or Distributor shall refresh the final payable PRICE before payment confirmation and present the updated state to TRANSPORT CUSTOMER, especially if:
   - the locked validity period is close to expiry or one or more component deadlines are close to expiry;
   - one or many options changes the final amount to pay. (PAYMENT METHOD, taxes, invoicing, delivery, corporate rules or currency conversion affect the payable amount).

- **Distributor and PSP contraints synchonization**  
5. For each concerned Distributor, the Retailer verifies and confirms:
   - CUSTOMER PURCHASE PACKAGE identifier;
   - final PRICE;
   - currency;
   - taxes and invoicing constraints;
   - CHARGING MOMENT(s);
   - accepted PAYMENT METHOD(s);
   - payment time limit;
   - holding or reservation status;
   - rule in case of payment success;
   - rule in case of payment pending, failure or expiry;
   - whether the Distributor requires payment execution by a specific Payment Provider.

6. Each Distributor returns the payable state and any payment-related constraints.
7. The Retailer consolidates all Distributor responses into one coherent payment proposal for the TRANSPORt CUSTOMER. The Retailer also determines and manages the payment architecture : he identifies, for each CUSTOMER PURCHASE PACKAGE, which entity performs the PAYMENT PROVIDER ROLE:
   - Retailer-side Payment Provider;
   - Distributor-side Payment Provider;
   - third-party Payment Provider;
   - shared or mixed architecture.
     
8. The TRANSPORT CUSTOMER may select one or mixed PAYMENT METHOD(s) that may include, when applicable :
   - monney ? 
   - bank card or other card-based payment;
   - SEPA direct debit or similar mandate-based debit;
   - wallet or account-based payment (can be on Retailer's system);
   - travel account debit;
   - voucher or travel credit;
   - miles, points or loyalty redemption;
   - corporate account or B2B invoicing arrangement;
   - cash or point-of-sale payment;
   - split payment across several means of payment; or
   - scheduled payment for deferred charging or instalments.
 Depending on the business rules, payment (and additional data) may be:
   - immediate, with direct authorisation and capture or delayed, with deferred capture or later debit;
   - split into several CHARGING MOMENT(s);
   - made by instalments according to a payment plan;
   - made by one single payment for several CUSTOMER PURCHASE PACKAGE(s);
   - verifies for particular PAYMENT METHOD (voucher, loyalty points, B2B, travel credit) with the relevant provider a particular confirmation process;  
   - made by travel account debit or B2B invoice/settlement arrangement; or
   - combined with voucher, loyalty redemption or other PAYMENT METHOD(s).

9. The retailer shall verify, with the relevant PSP, that the selected PAYMENT METHOD(s) are accepted for:
   - the concerned CUSTOMER PURCHASE PACKAGE(s);
   - the concerned distributor(s);
   - the TRANSPORt CUSTOMER context;
   - the country, currency and tax context;
   - the amount and charging rule;
   - the requested CHARGING MOMENT; and
   - the applicable payment deadline.
   And request to TRANSPORT CUSTOMER additional information (payer identity, billing address, invoice data, VAT number or corporate identifier, travel account identifier, voucher or travel credit reference, loyalty identifier, mandate consent, card or account credentials, strong customer authentication data, instalment acceptance conditions). The TRANSPORT CUSTOMER may see only one unique mayment (MarketPlace case) and later, the Retailer distributes, settles or reconciles the corresponding amounts with several distributor(s) according to commercial agreements (see Business Use Case K). 

- **Architecture Retailer-side payment**

10. The Retailer, acting directly or through its Payment Provider, initiates payment for one or more CUSTOMER PURCHASE PACKAGE(s) and receivess the transaction result.
11. The Retailer sends each concerned Distributor a payment confirmation or payment status notification including:
   - paid amount;
   - payment status, timestamp and reference;
   - CUSTOMER PURCHASE PACKAGE(s);
   - allocation amount and currency;
   - payment instrument type where required for audit, invoicing or after-sales.
12. Each Distributor verifies that the payment status satisfies its business rule and returns the component result (confirmed / pending / rejected / expired / revalidation required).
13. The Retailer consolidates Distributor responses and updates the TRAVEL BASKET.

- **Architecture Distributor-side payment**

14. The Distributor indicates that payment must be executed through its own Payment Provider or delegated payment process. The Retailer sends the required payment initiation context to the Distributor.
15. The Distributor, acting with its PSP, initiates payment, receives the transaction result from the Payment Provider.
16. The Distributor returns the payment result and status to the Retailer:
   - paid and confirmed;
   - paid but fulfilment pending;
   - payment pending / refused / expired / cancelled;
   - revalidation required.
17. The Retailer consolidates Distributor responses and updates the TRAVEL BASKET.

- **Architecture Shared Retailer-Distributor payment**

20. Some parts should be paid through the Retailer PSP (arhictecture Retailer-side payment) and others through one or more Distributor PSP(s) (architecture Architecture Distributor-side payment). The Retailer and Distributor(s) have agreements on:
   - which CUSTOMER PURCHASE PACKAGE(s) are paid by which Payment Provider;
   - whether the TRANSPORT CUSTOMER sees one payment step or several coordinated payment steps;
   - how mixed PAYMENT METHODs are allocated;
   - how partial success is handled;
   - how payment deadlines are enforced.
 following the architecure of the paiment part, each, Retailer or Distributor, requests for the payment part.  
21. Each PSP returns the transaction result to its requestor. If necessary, each Distributor returns the business status of its paiment part to the Retailer.
22. The Retailer consolidates all results and updates the TRAVEL BASKET. 

- **Architecture Third-party Payment Provider**

23. The Retailer and/or Distributor sends the payment initiation data to a third-party Payment Provider. The Payment is executed or scheduled and the transaction result is returned to his requestor.
24. The Retailer and Distributor(s) exchange the necessary payment result information so that:
   - every CUSTOMER PURCHASE PACKAGE or ORDER component has a clear payment state;
   - any reservation or holding can be finalized or released;
   - billing and settlement data remain traceable;
   - the TRAVEL BASKET status remains coherent.
 
 - **Payment result**
25. If mixed payment is used, the Retailer coordinates the authorization and allocation of each part of the payment while preserving one coherent TRAVEL BASKET. Depending on the result, the Distributor may confirms or release reservation elements (see Business Use Case E).  
26. The Retailer consolidates all payment part statuses and informs the TRANSPORT CUSTOMER of the final payment result.
27. The Retailer and/or Distributor provides the relevant proof of payment (receipt with legal data following rule of each state), invoice, payment terms with installments, and fulfilment trigger. It can be document(s) and/or TRANSPORT CUSTOMER account update or other format.
28. The Retailer may inform the Distributor(s) with each CUSTOMER PURCHASE PACKAGE data to create/update ORDER(s).   
   

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

## Payment failure
1. One payment part fails or expires.
2. The Retailer receives the failure or expiry status from the Distributor or PSP.
3. The Distributor indicates the consequence for the impacted CUSTOMER PURCHASE PACKAGE.
4. The Retailer either asks for another PAYMENT METHOD, requests revalidation, or informs the TRANSPORt CUSTOMER that the purchase cannot continue.

## Negative amount
1. The resulting financial amount is negative.
2. No customer payment is collected in BUC-D.
3. The Retailer redirects the case to Business Use Case F or J for refund, compensation, credit note or voucher handling.

## Payment after approval
1. A corporate or group purchase requires approval before payment.
2. The Retailer asks the Distributor(s) whether the quotation can remain valid until approval.
3. Once approval is obtained, the Retailer restarts the payment coordination flow.
4. If the deadline has expired, the Distributor requires refresh, revalidation or repricing.

## Zero amount to pay
1. If the payable amount for the current CHARGING MOMENT is equal to zero, the payment use case shall still be executed as a zero-payment confirmation flow.
No monetary transaction is initiated with a Payment Provider, unless a technical payment authorization is required by implementation rules.
2. The Retailer shall confirm with each concerned Distributor that:
   - the total amount due is zero (not necessary the Distributor part => see settlement in Business Use Case K);
   - no PAYMENT METHOD is required, or the required PAYMENT METHOD has already covered the amount;
   - the CUSTOMER PURCHASE PACKAGE can be confirmed without payment;
   - any reservation, ancillary, guarantee or service depending on payment can be finalized.
3. The Retailer consolidates the statuses and informs the TRANSPORT CUSTOMER that no payment is due.
4. The Retailer and/or Distributor provides the relevant proof of purchase, zero-amount receipt, invoice if legally required, confirmation and fulfilment trigger.


### Diagram 
UML activity diagram


### Links with inputs
wiki/use-cases/inputs/EUDIT.use.cases_20260324_shared.docx
wiki/use-cases/inputs/BRM_EUDIT_V2.3.xlsx

To be completed


