In review – version 2
## Use Case Overview

- **Business Use Case ID & Name:** BUC-D — Pay for one or more selected offers
- **Goal (Objective):** Enable the Buyer to pay for one or more selected offers (CUSTOMER PURCHASE PACKAGEs), possibly provided by several Distributors, through a coherent customer payment experience managed by Retailer, while supporting different payment architectures (retailer-side payment, distributor-side payment, shared payment, or payment via a third-party payment provider (PAYMENT PROVIDER, PAYMENT PROVIDER ROLE)).

> **Comment (BIGEX Olivier, 2026-05-12):**
> This wording avoids talking about the commercial business object on retailer side, commonly called «order». No debate about «travel package». That is smart. No problem with that. But we shall tackle that one day or another.

> **Comment (BIGEX Olivier, 2026-05-12):**
> And for me, the retailer is first and foremost a merchant, which collects payments. Thus, The customer pays rather basket elements. These basket elements could be CPP, or not. It can manage one single payment for CPP + newspapers + goodies + …
> In other words, the retailer transforms CPP from the Distributor into its own business objets: basket elements. Then collects payments for that and that is the end of the life cycle of these basket elements (when CPP becomes fare contracts on distributor side).

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> Rewritten to match with the business vocabulary used in BUC-A and BUC-B version 2 (Buyer, offer). For ‘order’, it will be used interbally to retailer. See chapter Terminology)

- **Scope** (Summary): 
  - offer completion/ confirmation with confirming payable content, charging timing (CHARGING MOMENT) and accepted payment means (PAYMENT METHOD)
  -  final payable amount refresh if needed (fees, taxes, expiry/TTL, charging milestones) (PRICE)
  - coordination management between Retailer, Distributor(s) and Payment Service Provider(s) (TRAVEL BASKET ELEMENTs / PAYMENT METHOD) 
  - payment execution (authorisation/capture/settlement trigger depending on method) 
  - payment results consolidation per selected offer and for the overall purchase 
  - payment proofs and billing information 
  - confirm purchase record (SALES TRANSACTION) for next use case (BUC-E fulfilment).

## Terminology note: basket, basket element, purchase record and order

At the beginning of this use case, the TRANSPORT CUSTOMER may still manage a **locked** validated basket (TRAVEL BASKET) containing one or more basket element (TRAVEL BASKET ELEMENT), still part of the shopping/purchase preparation state. Each basket element contains one offer (CUSTOMER PURCHASE PACKAGE), as defined in BUC-B.

The term **order** is not defined as a reference business concept in Transmodel. It may be implemented from the moment the purchase has been confirmed sufficiently (and "order" may be already pre-created at the beginning of the use-case). It may be explicitly used by a local implementation or by an external retailing system. Instead, the appropriate Transmodel concept to use when the purchase is confirmed is SALES TRANSACTION, named purchase record in the following document. A purchase record should be used from the moment the Buyer has validated the basket and the purchase is sufficiently confirmed (has reached a sufficiently binding state; typically after successful payment confirmation) to be recorded by the Retailer :

> **Comment (BIGEX Olivier, 2026-05-12):**
> Add: on retailer side

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> In 80% cases. Added : has reached a sufficiently binding state; typically after successful payment confirmation)
    - the TRAVEL BASKET becomes the purchase input;
    - each TRAVEL BASKET ELEMENT contributes to the confirmed purchase content;
    - each CUSTOMER PURCHASE PACKAGE remains traceable;
    - the confirmed sale is represented by a SALES TRANSACTION;
    - payment may be associated with the SALES TRANSACTION and, indirectly, with the reference of CUSTOMER PURCHASE PACKAGE(s).

> **Comment (BIGEX Olivier, 2026-05-12):**
> Associated rather with Basket Elements (which are bound to CPP). If transmodel does a direct like with CPP, then I think it is an error.

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Yes, the payment is associated with basket element, not directly with CPP. 
> Reformulated to keep the tracability

In this use case, **basket / basket element** is used when describing the payment process until payment validation, and purchase record is used after the payment validation. However, this may vary depending on the system. The confirmed sale should be represented by purchase record created or confirmed only when the purchase has reached a sufficiently binding state, typically after successful payment validation.

> **Comment (BIGEX Olivier, 2026-05-12):**
> Ok, so retailer’s order (like the one from Amazon) = sales transaction in Transmodel ?That is not intuitive at all, but since the management of the retailer’s order is pure internal process of the retailer, that is not a problem here for EUDIT.

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Yes, not perfect matching between business voc and Transmodel voc. Order on some system covers also post payment actions (delayed card building, …).

## Actors & Context

- **Primary Actors:**
  - Buyer (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)): wants to pay with his chosen payment method(s) (PAYMENT METHOD(s)) and receive all proofs of payment for himself and/or for other travellers.
  - 
    - 
    - 
    - 
- Other end-customer Actor: Traveller: the person(s) who will actually travel and use the entitlement. The Traveller and the Buyer can be the same person but not always.  
- **Supporting Actors / Stakeholders:**
  - **Retailer** (FARE PRODUCT RETAILER ROLE (API consumer)): orchestrates the shopping flow and customer interaction, presents the payable basket to the Buyer and may manage a logical and/or technical basket (basket consistency, requests sent to one or more Distributors). The Retailer can manage the interface with the bank (Payment Provider), coordinates the payment and keeps coherent the purchase process during payment.
  - **Distributor** (FARE PRODUCT DISTRIBUTOR ROLE (API provider)): Provides or confirms the business data needed for payment. The Distributor may confirm payable amount, charging timing (CHARGING MOMENT(s)) with time limit(s), payment means (PAYMENT METHOD(s)) from a business point of view (payment method restrictions may be provided when applicable, but are not assumed mandatory in all cases). The Distributor confirms reservation, holding, ancillary or guarantee status (next use case). The Distributor indicates whether payment must be completed before final confirmation and applies business consequences of payment success, failure or expiry.

> **Comment (BIGEX Olivier, 2026-05-12):**
> Change into «may confirm».
> In some business cases, the distributor is not in position to force any accepted payment methods. For example, a Travel Agency (retailer) can collect payments without refering to any distributor rule.

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Yes, changed. The Distributor only needs a business confirmation that payment conditions are satisfied
  - Payment Service Provider (PSP) (PAYMENT PROVIDER ROLE (API not managed by EUDIT project)): Executes or supports the payment transaction. The PSP :
    - authorises, captures, rejects, secures, schedules or confirms payment;
    - may manage card payment, account debit, voucher, wallet, loyalty redemption, travel account debit or other payment instruments;
    - returns the payment transaction result to the responsible Retailer and/or Distributor.

- **(context at start):**
  - The TRANSPORT CUSTOMER can provide the additional information required to pay for the selected offer(s).
  - 
    - 
    - 

> **Comment (Juanjo Quesada, 2026-05-29):**
> Should also be included or fere HOW to release the BASKET andtheir elements??
    - or another delegated payment system.
  - The interfaces with the banks and the Payment Service Provider equipment(s) are available.
  - Depending on the implementation, 
  - 
  - 
  - 
  - 
  - 

## Preconditions & Postconditions

- Assumptions and **Preconditions (must be true before start) :**

> **Comment (BIGEX Olivier, 2026-05-12):**
> To be added:
> (or in BUC-C ?) The customer has been informed by the retailer of any additional service provided by the retailer like insurance or extension of aftersales conditions. If the customer wants to purchase them, these products become basket elements and change the total price of the basket.
> The customer has been informed by the retailer of any extra fees independent of Distributor’s rule or fare product.

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> First point : treated as basket element but mention here because they are not «offer» (coming from distributor catalogue) => mention here in BUC-D
> Second point : it impacts the final amount to pay.  
> => added in point 2 in main scenario, added precision in point 3, point 4
  - The Buyer has already one or more selected offers ready to be paid, represented in the logical basket (TRAVEL BASKET, TRAVEL BASKET ELEMENT, CUSTOMER PURCHASE PACKAGE).
  - The Retailer can access the relevant Distributor(s) for payable confirmation and can access the relevant PSP(s) for payment execution.
  - The Distributor’s selling system and related pricing rules (with time limits) with dependency information are available (online service or accessible dataset).
  - The Buyer context needed to continue the purchase is available or can be entered, including traveller data, rights, options, delivery data, invoicing data or VAT context where required.

> **Comment (Vinke, Bob BGH, 2026-05-06):**
> use case C

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> Yes, it is platform fees and other addtional services that can be added
  - Any additional Retailer-provided payable items (e.g. platform fees, delivery fees, optional services such as extended conditions/insurance when applicable) are disclosed to the Buyer and included in the payable summary if purchased.
  - Refunds, compensation, voucher issuance after refund and other after-sales processes are out of scope of present use case and are handled in BUC-**F** and BUC-**J** dedicated to after-sales. But they refer to the same PSP interactions and APIs.
  - The PAYMENT PROVIDER ROLE may be performed, for each payment, by:
    - the Retailer or the Distributor;
    - a third-party PSP (can be a bank, a travel account provider, a voucher provider, a loyalty provider,...)
  - The use case is PSP implementation-neutral and therefore specifies the required business entries and results, not the internal technical bank exchange. Each payment operation may be executed only in a retailer side; only in a distributor side; or in a third-party system.
  - 

- **Postconditions — Success guarantees:**
  - Each basket element has a Retailer payment status and proofs (receipt, invoice, payment terms, payment schedule, confirmation where applicable), consolidated in a global outcome and given to Buyer by the Retailer. Each concerned Distributor receives only the minimum confirmation required to proceed (e.g. ‘ok to fulfil’), without necessarily receiving proof that the Buyer paid via the Retailer’s PSP.

> **Comment (BIGEX Olivier, 2026-05-12):**
> Basket elements on retailer’s side have a payment status. CPP on distributor’s side have a status bound to their final confirmation.
> As an example, a distributor doesn’t know if a basket element has been really paid by the customer of a travel agency, the distributor only knows that all is ok, that he can proceed to fulfilment and that the settlement process will be ok.

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Clarified that retailer manages the TB element status and the distributor only knows about the offer if the pyment is confirmed or not and the fullfilment should be done or not
  - The basket state presented to the customer is consistent, regardless of whether the underlying implementation relies on a retailer basket, a distributor basket, or coordinated baskets across both.
  - This use case ends when the basket is ready for the next step; payment status is known.
  - Reservation, option, seat, ancillary, service or guarantee depending on payment will be finalized according to Distributor rules in BUC-C.

> **Comment (Vinke, Bob BGH, 2026-05-06):**
> I would say Use case C

> **Comment (Bourdelin, Sonia [2], 2026-05-10):**
> yes, changed
  - Fulfilment (travel rights / documents) is managed in BUC-E (access to TRAVEL DOCUMENT(s)).

- **Postconditions — Minimal guarantees:**
  - If the Buyer abandons the process or no suitable payment solution is found, the Retailer preserves or updates the basket state as applicable, and prevents continuation for impacted basket elements until resolved.
  - The Distributor indicates which payment part remains held, is released, is cancelled, is partially cancelled or requires revalidation.
  - The Retailer consolidates the basket state and prevents fulfilment if a required payment condition is not met.
  - If funds or accounting information must be distributed, cleared or reconciled between the Retailer and one or more Distributor(s) are described in BUC-K.
  - The TRANSPORT CUSTOMER actions can be logged/audited (if required by the system).
  - If many basket elements are involved, allocation remains traceable per Distributor, purchase record, payment instrument and settlement rule.

## Scenarios

### Main scenario
1. The Buyer shall start this use case after the preceding shopping and, when required, reservation phases. One or more offers have been selected, parameterised, inserted into a basket and locked or stabilized for payment. At this moment, it may remain basket elements that modify the final payment: shipping costs, payment method fees, charging timing, Retailer platform fees or promotions and operation fees (by example: after-sales fees).

- **Payment options choice**
2. The Retailer provides to the Buyer a payable summary including :

> **Comment (BIGEX Olivier, 2026-05-12):**
> «may present»
> We cannot interfere with the UX/UI of the retailer, but I understand that the underlying objective is to be sure that the distributor has provided all related data.

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> Yes reformulated
  - basket elements to pay,
  - current price (PRICE) of each and total amount for the current options,
  - selected or available payment method(s) (PAYMENT METHOD(s)),
  - charging timing (CHARGING MOMENT(s)),
  - payment deadline(s) or milestones for group purchase,
  - fees, taxes and currency(s) information including available shipping possibilities and operation fees,
  - any deferred amount or future instalment amount, where applicable,
  - main conditions attached to payment, linked to deferred payment, B2B invoicing or corporate approval,
  - main consequences of payment failure or expiry.
The payable summary also includes any Retailer-provided payable items (e.g. insurance, extended after-sales conditions) and any Retailer fees independent of Distributor rules (e.g. platform/service fees, payment method fees, delivery/shipping fees where applicable), clearly identified as Retailer items and included in the total amount.

3. The Buyer selects and/or confirms the payment method(s) and any payment options (split payment, voucher, corporate invoicing, instalments) including any Retailer-provided items/fees when applicable where applicable.
4. If required, the Retailer and/or Distributor shall refresh the final payable amount before payment confirmation and present the updated state to Buyer, especially if:
  - the locked validity period is close to expiry, or one or more component deadlines are close to expiry (charging milestone);
  - one or many options changes the final amount to pay. (payment method, taxes, invoicing, delivery, corporate rules or currency conversion affect the payable amount), 
  - Retailer fees or selected Retailer add-ons change the payable amount.

- **Distributor and PSP** constraints synchronization
5. For each concerned Distributor, the Retailer verifies and confirms the payable constraints: 
  - identifiers, 
  - final amount and currency,
  -  taxes and invoicing constraints, 
  - charging timing, 
  - accepted payment methods, 
  - payment time limits, 
  - holding or reservation status, 
  - rule in case of payment success, 
  - rule in case of payment pending, failure or expiry and 
  - whether the Distributor requires payment execution by a specific Payment Provider.

6. Each Distributor returns the payable state and any payment-related constraints.
7. The Retailer consolidates all Distributor responses into one coherent payment proposal for the Buyer. The Retailer also determines and manages the payment architecture: he identifies, for each basket element, who performs the PSP role (
  - Retailer-side
  - , Distributor-side, 
  - third-party PSP,
  -  shared).

> **Comment (Vinke, Bob BGH, 2026-05-06):**
> part of use cse C

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> Here, it is refresh of payment amount linked with payment means that are updated for any reason linked with the payment, not linked with the reservation

8. The Buyer may select one or mixed payment methods that may include, when applicable :
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
  - split into several charging timings;
  - made by instalments according to a payment plan;
  - made by one single payment for several basket elements/ offers,
  - verifies for particular payment method (voucher, loyalty points, B2B, travel credit) with the relevant provider a particular confirmation process;
  - made by travel account debit or B2B invoice/settlement arrangement; or
  - combined with voucher, loyalty redemption or other payment methods.

9. The retailer shall verify, with the relevant PSP and/or with the relevant Distributor(s) depending on the instrument, that the selected payment methods are accepted for:

> **Comment (BIGEX Olivier, 2026-05-12):**
> Not always for voucher, if the voucher is published by the operator. In that case, the retailer shall check with the distributor if the voucher is compatible with Travel basket elements, if the voucher is still valid and the amount taken by the voucher, which is subtracted from the total basket price.
> 
> Would the management of operator’s voucher deserve a dedicated section ?

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> Voucher management added
  - the concerned offers,
  - the concerned distributor(s),
  - the Buyer context,
  - the country, currency and tax context,
  - the amount and charging rule,
  - the requested charging timing, 
  - the applicable payment deadline.
And request to Buyer additional information (payer identity, billing address, invoice data, VAT number or corporate identifier, travel account identifier, voucher or travel credit reference, loyalty identifier, mandate consent, card or account credentials, strong customer authentication data, instalment acceptance conditions). 
For operator-issued vouchers, the Retailer verifies with the Distributor: voucher validity, compatibility with the concerned basket elements, and deductible amount.
The Buyer may see only one unique payment (MarketPlace case) and later, the Retailer distributes, settles or reconciles the corresponding amounts with several distributor(s) according to commercial agreements (see BUC-K).

- **Architecture Retailer-side payment**

10. The Retailer, acting directly or through its Payment Provider, initiates payment for one or more basket elements and receives the transaction result. The Retailer checks that the payment result has been obtained within the applicable business validity/time limits for each concerned basket element. If a time limit is breached, the Retailer flags the impacted element(s) as expired/revalidation-required and does not treat them as confirmed.
11. The Retailer may notify each concerned Distributor a payment confirmation or payment status notification and may include in the flow :

> **Comment (BIGEX Olivier, 2026-05-12):**
> Not necessarily. «may send»

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Yes corrected
  - paid amount only if required by Distributor business rules;
  - payment status, timestamp and reference;
  - paid basket elements identifiers / offers;
  - allocation amount and currency if required;
  - payment instrument type where required for audit, invoicing or after-sales (No PSP instrument details unless legally required/agreed.).

> **Comment (Vinke, Bob BGH, 2026-05-06):**
> to much information to share with Distributor

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Depending on the case. Sentence updated
12. Each Distributor verifies that the payment status satisfies its business rule and returns a component confirmation where applicable (e.g. “ok to proceed to fulfilment” or “revalidation required”). 

> **Comment (Vinke, Bob BGH, 2026-05-06):**
> If the payment is done within the time limit, the only result could be confirmed.. We must make sure before payment that this is always the case

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Time limit management added in point 10

- **Architecture Distributor -side payment**

> **Comment (Vinke, Bob BGH, 2026-05-06):**
> do you have an example?

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> 1 - when the payment UX is implemented as a white-label payment page. The Buyer remains on Retailer web site but the payment is executed on Distributor’s PSP
> 2 - Single operator shopping web site : the retailer acts as front channel and the Distributor/Operator is the merchant with PSP

13. The Distributor indicates that payment must be executed through its own Payment Provider or delegated payment process. The Retailer sends the required payment initiation context to the Distributor for the concerned basket element(s).

> **Comment (Vinke, Bob BGH, 2026-05-06):**
> How can this work if there are multiple distributors?

> **Comment (BIGEX Olivier, 2026-05-12):**
> For pre-payment, the retailer splits the payment. Old school and not optimised.
> A post-payment service with NFC smartcard or EMV or Fairtiq-like is rather a operator-side payment

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Optional architecure solution. Added exemples to see that is not used/ no more (performance) used in multi-operator context. But widly used in monomodal operator/retailer/distribuor context when the retailer only distributes one operator offers.
14. The Distributor, acting with its PSP, initiates payment, receives the transaction result from the Payment Provider (authorised/captured/pending/refused and timestamp, depending on the method).
15. The Distributor checks that the payment result has been obtained within the applicable business validity/time limits and returns the payment result and status to the Retailer:
  - paid and confirmed when within time limits;
  - 
  - expired/revalidation required and requests the appropriate next action (refresh/revalidation) before continuation if a limit is breached 
  - 
  - 
  - .
The Retailer checks business validity/time limits for the concerned basket element(s) before treating them as confirmed, then records the status and consolidates.

In multi-Distributor purchases, Distributor-side payment typically results in split/parallel payments per Distributor; if a single coordinated payment step is required, Retailer-side or shared payment is usually used instead.

Examples: Distributor-side payment may occur: 
- when the Retailer redirects the Buyer to, or embeds, a white-label payment page operated by the Distributor (or its PSP), 
- when the Retailer acts mainly as a front channel (e.g. portal/agent), while the Distributor/Operator is the merchant of record and executes payment through its own PSP (PAYMENT PROVIDER ROLE).
- 
- **Architecture Shared Retailer-Distributor payment**

16. Some parts should be paid through the Retailer PSP (architecture Retailer-side payment) and others through one or more Distributor PSP(s) (architecture Distributor-side payment). The Retailer and Distributor(s) have agreements on:
  - which basket elements are paid by which Payment Provider;
  - whether the Buyer sees one payment step or several coordinated payment steps;
  - how mixed payment methods are allocated;
  - how partial success is handled;
  - how payment deadlines are enforced.
following the role repartition, each, Retailer or Distributor, requests for the payment part.
17. Each PSP returns the transaction result to its requestor. If necessary, each Distributor returns the business status of its payment part to the Retailer.

- **Architecture Third-party Payment Provider**

18. The Retailer and/or Distributor sends the payment initiation data to a third-party Payment Provider. The Payment is executed or scheduled, and the transaction result is returned to his requestor.
19. The Retailer and Distributor(s) exchange the necessary payment result information so that:
  - every basket element has a clear payment state;
  - any reservation or holding can be finalized or released;
  - billing and settlement data remain traceable;
  - the basket status remains coherent.

- **Payment result**
20. If mixed payment is used, the Retailer coordinates the authorization and allocation of each part of the payment while preserving one coherent basket. Depending on the result, the Distributor may confirm or release reservation elements (see BUC-E).
21. The Retailer consolidates all payment part statuses (Distributor/third-party) responses into one coherent purchase outcome for the Buyer.

22. The Retailer and/or Distributor provides the relevant proof of payment (receipt with legal data following rule of each state), invoice, payment terms with instalments, and fulfilment trigger. It can be document(s) and/or Buyer account update or another format.
23. The Retailer may inform the Distributor(s) with each basket element data to create/update confirmed purchase record.

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

## Payment failure
1. One payment part fails or expires.
2. The Retailer receives the failure or expiry status from the Distributor or PSP with 
indications of the consequence for the impacted basket element(s).
3. The Retailer either asks to retry with another payment method and/or requests revalidation or informs the Buyer that the purchase cannot continue with the impacted basket element(s).

## Negative amount

> **Comment (Bourdelin, Sonia [2], 2026-05-27):**
> Manage refund here as a negative payment
1. The resulting financial amount is negative so 
no Buyer payment is collected in this use case.
2. The Retailer redirects the case to BUC-F or BUC-J for refund, compensation, credit note or voucher handling.

## Payment after approval (corporate / group)
1. A corporate or group purchase requires approval before payment: the payment is delayed by an approval requirement
2. The Retailer may request the Distributor(s) with quotation validity extension where supported.
3. Once approval is obtained, the Retailer restarts the payment coordination flow.
4. If the deadline has expired, the Distributor requires refresh, revalidation or repricing.

## Zero amount to pay
1. If the payable amount for the current charging moment is equal to zero, the payment use case may be executed as a zero-payment confirmation flow or the Retailer may skip PSP execution when allowed by business/technical rules, while still confirming with Distributors.

> **Comment (BIGEX Olivier, 2026-05-12):**
> Not necessarily. It is a solution (payment of zero Euro). But another way to confirm a free CPP is simply to skip the payment step and proceed to next BUC

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> Yes. Two valid options: zero-payment confirmation flow, or skip PSP execution
No monetary transaction is initiated with a Payment Provider, unless a technical payment authorization is required by implementation rules.
2. The Retailer shall confirm with each concerned Distributor that:
  - the total amount due is zero (not necessary the Distributor part => see settlement in Business Use Case K);
  - no payment mean is required, or it has already covered the amount;
  - the offer can be confirmed without payment;
  - any reservation, ancillary, guarantee or service depending on payment can be finalized.
3. The Retailer consolidates the statuses and informs the Buyer that no payment is due.
4. The Retailer and/or Distributor provides the relevant proof of purchase, zero-amount receipt, invoice if legally required, confirmation and fulfilment trigger.

### Diagram
UML activity diagram

### Links with inputs
wiki/use-cases/inputs/EUDIT.use.cases_20260324_shared.docx wiki/use-cases/inputs/BRM_EUDIT_V2.3.xlsx

To be completed
