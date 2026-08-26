In review – version 3
## Use Case Overview

- **Business Use Case ID & Name:** BUC-B — Shop and manage your basket of selected offers with price updates at any time
- **Goal (Objective):** Enable the Customer to build, review, modify and validate a shopping basket containing one or more selected offers, while keeping each element up to date (price, validity, conditions, dependencies) until the Customer decides to proceed (TRAVEL BASKET, TRAVEL BASKET ELEMENT, CUSTOMER PURCHASE PACKAGE, PRICE). 
- **Scope (Summary) :** 
  - Basket creation and management (possibly implicit to the Customer), 
  - Completing any missing information needed to keep options priceable-valid,
  - Price and totals calculation after each change,
  - Handling dependency management between elements (bundles, combinability, mandatory co-sale),
  - Basket consistency across possible retailer-side and/or distributor-side basket implementations with validity/time limits, refresh/expiry, and optional “hold for approval” cases, 
  - Preparation of a stable basket for purchase continuation (TRAVEL BASKET, TRAVEL BASKET ELEMENT, CUSTOMER PURCHASE PACKAGE, PRICE, USAGE PARAMETER(s)),

## Actors & Context

- **Context**: This use case starts after the Customer has identified one or more suitable offers and wants to manage them together as a shopping basket, including checks and repricing that may require interactions with one or more Distributors.
- **Primary Actor:** Customer (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)): the person managing the shopping, for himself and/or for other travellers. He can be the manager of a group, a PRM, the purchaser for a minor traveller or other with specific needs or none. He decides what stays in the basket and when to proceed.
  - Traveller**:** the person(s) who will actually travel and use the entitlement. The Traveller and the Customer can be the same person but not always.
- **Supporting Actors / Stakeholders:**
  - **Retailer** (FARE PRODUCT RETAILER ROLE (API consumer)): manages the shopping flow and customer interaction, presents the basket to the Customer and may manage a logical and/or technical basket (basket consistency, requests sent to one or more distributors, consolidates responses from Distributor(s)).
  - **Distributor** (FARE PRODUCT DISTRIBUTOR ROLE (API provider)): provides offer completion rules, price information, commercial conditions, after-sales conditions, guarantees, and dependency rules affecting basket elements. May manage a technical basket for one or more basket elements.

> **Comment (BIGEX Olivier, 2026-05-06):**
> As discussed, no need for basket on distributor side at this step, since it manages on its sides offers which are already consistent. 
> 
> In some cases, with NFC cards, cohabitations rules must be checked by the distributor (e.g. only one pass by card and by fare owner), but no need to write here that the distributor creates a basket (internal distributor solution). 
> --> To be removed and add somewhere a step where items in retailer’s basket are checked by the distributor (only items concerned by this distributor).

> **Comment (Bourdelin, Sonia [2], 2026-05-06):**
> Some systems need a TB on their side (urabn transport to check hat the contactless card can manage the new contracts)

## Preconditions & Postconditions

- **Assumptions and Preconditions (must be true before start):**

  - The Customer has already selected one or more candidate offer(s) (CUSTOMER PURCHASE PACKAGE(s)) in the previous use case (BUC-A).
  - The Customer can provide the additional information required to complete and price the selected offer(s) (traveller data, eligibility, options, invoicing/VAT context if relevant).
  - The Retailer can identify the relevant Distributor(s) for each selected offer and access the required selling/pricing information, either via online services or accessible datasets.
- The basket (TRAVEL BASKET) is a logical business object representing the shopping state (it may be explicit or implicit to the Customer); its technical implementation may reside with the retailer, with one or more distributors, or be distributed across both. The use case is implementation-neutral and therefore specifies the required business result, not the internal technical ownership of the "basket" (can be only in a retailer "basket", only in a distributor "basket"; or in a retailer "basket" and in one or more distributor "baskets" that must remain aligned).

> **Comment (BIGEX Olivier, 2026-05-06):**
> I disagree, not the same objects and functions. But I don’t see the impact in OTI, and I don’t want to enter in an architecture debate.
> Maybe simply remove this sentence, which doesn’t bring anything to this BUC

> **Comment (Bourdelin, Sonia [2], 2026-05-06):**
> On OTI, we may manage API that coordinate the 2 TB.

> **Comment (BIGEX Olivier, 2026-05-19):**
> I still think that this is not the same business object, thus I am not confortable with this wording. BUT :
> on retailer’s side, the retailer’s functions could be supported by a technical agregator (e.g. a B2B multimodal digital mobility service), which manages a basket. And it would be great if OTI support interactions between the retailer and the agregator as well.
> A basket could be seen as a booking in a non confirmed status. On retailer’s side (= retailer’s order) and on distributor’s side.
> ==> maybe we have both a similar thing in mind, but we don’t use the same wording in our architecture. Thus I still suggest to avoid writting implementation solutions and replace these wordings (here and above) by the needs for interactions between retailer and distributor. E.g. «the retailer may inform the distributor that an offer has been put/removed from the basket».

> **Comment (Bourdelin, Sonia, 2026-05-31):**
> Yes, we should concentrate on the exchanges between retailer and distributor. 
> Text modified + last point added
- When needed to keep elements up to date (pricing, validity, constraints, dependencies), the Retailer may inform the relevant Distributor(s) that an offer has been added/updated/removed in the basket and request the corresponding checks/updates for the concerned element(s).  
- **Postconditions — Success guarantees:**
- 
  - A basket exists and contains one or more completed and consistent basket elements according to the Customer’s actions : ; any constraints required by the configuration of an offer (or combinations of offers) are identified and either satisfied or clearly flagged (TRAVEL BASKET, TRAVEL BASKET ELEMENT).
  - Each basket element contains one selected offer and the information needed for continuation:
    - selected **offer(s)** and quantity, including validity when applicable (CUSTOMER PURCHASE PACKAGE)
    - the associated current price (PRICE),
    - applicable reductions, guarantees and aftersales conditions (TRAVEL GUARANTEE(s), USAGE PARAMETER(s)),
    - any dependency information linking it to other basket elements (bundles/co-sale/combinability),
    - optional protection or guarantee services selected during shopping, where applicable,
    - group-specific quotation results, where applicable,
    - approval-hold status, where applicable,
    - identified reservation-related constraints or intermediate reservation results, where applicable.
  - The whole basket has a calculated total price (totals can be zero or less).
  - The basket state presented to the customer is consistent reflects the latest known status of each element (valid/expired/needs refresh) and any constraints affecting continuation., not where the “basket” is technically stored. 
  - This use case ends when the basket is ready for the next step; reservation initiation or continuation and payment are out of scope and belong to the use cases BUC-C and BUC-D.

- **Postconditions — Minimal guarantees:**
- 
  - If the Customer abandons the process or no suitable solution is found, the basket remains empty or unchanged. Any time-limited elements are clearly marked as expired/not usable until refreshed.
  - After any basket operation, the Retailer and/or Distributor shall ensure that no basket element remains with an outdated price, an invalid status, or an unresolved dependency with another basket element. The basket shall remain internally consistent after any operation.
  - If synchronization between retailer-side and distributor-side basket states is required, the system detects any divergence and prevents continuation until the basket state is made consistent again. Divergence may occur for example after an expiry/refresh on Distributor side, an availability change, or a partial update failure; in such cases the Retailer requires a refresh and shows the updated state before proceeding.
  - The Customer actions can be logged/audited (if required by the system).

## Terminology – 

CUSTOMER PURCHASE PACKAGE : In Transmodel improved with COROM project proposals, a CUSTOMER PURCHASE PACKAGE is created when a SALES OFFER PACKAGE has been selected and parameterised for a specific customer context. Where basket management is required, this CUSTOMER PURCHASE PACKAGE may be inserted into the TRAVEL BASKET as part of a TRAVEL BASKET ELEMENT. However, for a simple purchase flow, the Retailer may present the CUSTOMER PURCHASE PACKAGE directly to the TRANSPORT CUSTOMER without exposing or explicitly managing a TRAVEL BASKET. 

**Basket** is the logical business object that represents the **current shopping state** of one customer session: it groups one or more selected offers, keeps track of their required parameters, current prices, validity/time limits, and dependencies until the customer validates the intended purchase solution. It may be **explicit** (visible to the customer) or **implicit** (not shown), but it still exists as the reference state used to keep elements up to date. *(TRAVEL BASKET, TRAVEL BASKET ELEMENT, CUSTOMER PURCHASE PACKAGE, PRICE, VALIDITY CONDITION(s), OFFER RULE(s))* 
### 
### Cart (difference with Basket)
A **Cart** is the **customer-facing shopping-cart feature** (UI/UX) used by a retailer to display and manage what the customer intends to buy. A cart may include **items outside the interoperability scope** (e.g. hotels, events, flights), and it is therefore not a reliable interoperability object by itself. In EUDIT/OTI terms, the **Basket** is the scope-relevant logical object used to manage the in-scope selected offers and their constraints, whether or not the retailer exposes it as a cart feature. *(TRAVEL BASKET)*

***A* *B **ooking* *is the customer-facing result of securing and then confirming a travel purchase: it starts when one or more selected offers are secured (inventory/options held where required) for a limited time, and it becomes effective when the sale is confirmed and can proceed to fulfilment. In* *Transmodel **terms, it corresponds to: a secured state of selected offer(s) requiring reservation/hold (RESERVATION, AVAILABILITY CONDITION, SPOT ALLOCATION) linked to selected offer(s) (CUSTOMER PURCHASE PACKAGE, TRAVEL BASKET ELEMENT), followed by confirmation of the sale (SALES TRANSACTION) which triggers delivery of travel rights/documents (TRAVEL DOCUMENT(s)).***

## Scenarios

### Main scenario

- **Basket management**
- 
1. The Customer shall start this use case after having selected one or more priced offers (CUSTOMER PURCHASE PACKAGE) in the Business Use Case A.
2. The Retailer opens or initialises a basket for the current shopping session (explicitly if the Customer requests it, or implicitly when the first offer is added) (TRAVEL BASKET). 
The logical basket may be created explicitly at the Customer’s request or implicitly when the first offer is added associated with the current shopping session. For a simple purchase, This basket can be transparent for the Customer and may be replaced in the display by the direct presentation of the offer.

**Display basket**
3. At each step, the Retailer shall present the current shopping context and so, the Customer can see a representation/summary of what he is purchasing (current state of basket if already exists) including the details of each operation:
  - current basket elements (TRAVEL BASKET ELEMENT(s)), where applicable,
  - the offer contained in each element (CUSTOMER PURCHASE PACKAGE),
  - the detailed price of each element (PRICE),
  - the total price of the basket,
  - the quotation validity periods applicable to each concerned,
  - any validity extension possibilities, 
  - the key conditions and constraints attached to each element and to the basket as a whole or only part of it (USAGE PARAMETER(s), VALIDITY CONDITION(s)).

**Creation of an empty basket (optional)**
4. If the Customer requests the creation of an empty basket, the Retailer shall create or request creation of the corresponding basket state, retrieve the resulting basket state where applicable, and present the empty basket.

**Addition of one or many basketselement (s)**
5. The Customer may add an offer (selected and defined in previous use case) to his basket either:
  - one selected offer in a single action; or
  - several selected offers in one action, including for a multimodal, multi-leg or multi-operator travels with protection or guarantee option related to connections.
A basket element may cover one or several passengers; where passengers require different offers, the basket may contain multiple elements representing those passenger-specific selections), including the group-related constraints (TRAVEL BASKET ELEMENT, CUSTOMER PURCHASE PACKAGE). 
The addition is the only operation that the Customer can start the process with. This operation can be the only one available for the Customer. 
6. For each addition, the Retailer shall create one element corresponding to the shopping intention of the Customer for the concerned part of the travel. For each addition, the Retailer shall :
  - identify the relevant Distributor(s) for the added offer (output of BUC-A);
  - confirm (and ends the collect if required) the data required to build the corresponding valid and priceable offer,
  - send the relevant updates requests to the concerned Distributor (including retailer's basket if required), where distributor-side processing is required;
  - create or update the corresponding basket element (TRAVEL BASKET ELEMENT);
  - evaluate dependencies between basket elements when required to assure the consistency of the basket and inform the Customer of the consequences; 
  - retrieve the resulting price, validity and applicable conditions for presentation (PRICE).
Several offers added in one action may correspond to one multimodal, multi-leg or multi-operator travel and may be processed either as one combined basket update or recursively element by element.
Pre-reservation step can be started at any moment in BUC-B: If adding an element triggers a capacity hold or reservation-related action, the Retailer starts the reservation/hold process in parallel as described in BUC-C, and then updates the basket state with the resulting status/constraints.” (SPOT RESERVATION / SPOT ALLOCATION).

**Modification of one existing basket element**
7. The Customer may request modification of one existing basket element (when supported). It can be on : quantity, date and time, validity options, eligibility data, traveller assignment, customer account context (including fare contracts and travel documents), reduction rights, class or comfort option (seat preference), ancillary selections, delivery preferences, invoicing order, VAT context (TRAVEL BASKET ELEMENT, CUSTOMER PURCHASE PACKAGE). It can be on any other mandatory parameter required by the Distributor.
Quantity may represent either multiple instances of the same selected offer, or a parameter within one offer; the Retailer applies the appropriate update mechanism accordingly (CUSTOMER PURCHASE PACKAGE).
8. On a modification request, the Retailer shall :
  - identify what can be changed,
  - request from the Customer any additional required data ,
  - send the relevant update request to the concerned Distributor(s), where required,
  - retrieve the updated offer details, price, validity period and other applicable conditions,
  - evaluate dependencies between basket elements and prepares consequences for the Customer if other elements are impacted, 
  - retrieve the resulting price, validity period and applicable conditions for presentation to the Customer.
- 
**Removal of one or many existing TRAVEL BASKET ELEMENT(s)**
9. The Customer may request removal of one or many basket element. He may do it by selecting it in the basket display or down the quantity to zero or with any other displayed option.
10. For each removal, the retailer shall:
  - identify the basket element(s) concerned,
  - send the relevant deletion request to the concerned Distributor(s), where applicable,
  - determine whether the removal affects any other basket element , any combined pricing rule, any pass condition, any ancillary condition, or any other basket dependency (fare combinability / through-fare eligibility / bundle rules/ ancillaries applicability),
  - update the basket: repricing of affected elements and/or basket total and validity refresh or expiry handling,
  - retrieve the resulting basket state for presentation to the Customer.
Several removals in one step for the TRANSPORT CUSTOMER can be managed as only one operation or recursively, one by one in order to manage element's dependencies.

**Clearing the whole basket**
11. The Customer may request clearing of the whole basket. He may do it by selecting the basket or, on some systems, downing the last element quantity to zero or with any other displayed option. An additional confirmation can be required.
12. To execute this demand, the retailer shall :
  - request deletion or resetting entire basket content in the basket, 
  - retrieve confirmation of the resulting empty basket state, where applicable
  - display the resulting empty logical basket to the Customer.
On some systems, this operation is managed as a basket suppression.

- **Final price calculation**
- 
- **Change management**
13. If required, after any add/modify/remove operation and before displaying the operation result, the Retailer and/or the concerned Distributor(s) shall evaluate the impact of the operation and refresh what is needed :
  - repricing of affected elements and/or basket total, (PRICE) 
  - validity refresh or expiry handling, 
  - fare combinability / through-fare eligibility / bundle rules, 
  - ancillaries’ applicability and pricing, 
  - any other dependency between elements.
During each evaluation, the Retailer detects time-limit breaches (expiry/time limits) and external changes reported by Distributor(s) (e.g. a change impacting one leg/operator). The Retailer then either: 
- refreshes the affected offer(s), 
- flags them as expired/invalid, 
- removes them and informs the Customer before proceeding.
14. The retailer shall consolidate all relevant Distributor responses, together with any Retailer-side basket processing result, into one updated basket state. If one or more quotations cannot continue without recalculation (expired), the Retailer may request a mandatory refresh of the offer before the basket can continue unchanged or may remove expired elements.
15. If the requested operation has consequences on one or more other elements (repricing, invalidation, forced addition/removal, expiry/refresh), the Retailer presents them to the Customer before finalising the updated basket state. The Customer shall either accept the proposed consequences and confirm the basket update or reject the proposed consequences and cancel the operation (the retailer shall preserve the previously consistent basket state). If the Customer accepts the proposed consequences, the retailer shall confirm and apply the resulting basket state.
16. In any case, the retailer shall inform Customer with the result and present the updated basket including:
  - all current basket elements,
  - the offer contained in each element,
  - the price of each element, reduction amount, VAT;
  - the total price of the basket, reduction amount, VATs; and
  - the main applicable conditions and validity constraints.

**Pricing**
17. The Customer may also provide a promotion code, discount code, entitlement reference, reduction right, corporate identifier (and agreements) or pass-related information applicable to one or more basket elements. This information may be entered **during basket shopping** or **later during payment (BUC-D)**, depending on how it is defined and processed. When provided at this stage, the Retailer requests validation and repricing from the relevant Distributor(s) and updates the affected basket element(s), conditions and totals.  
In some implementations, the benefit may be represented as an additional basket element (e.g. a priced item with a negative amount); in others it is applied only at payment time (BUC-D). 
The code may be accepted, rejected, or only partially applicable depending on Distributor and/or Retailer commercial rules. In all cases, the Customer is informed of acceptance/rejection/partial applicability and any consequences on selected offers, validity and dependencies.
If the Customer, or a corporate booker acting on his behalf, requires an internal approval before continuing, he or the Retailer may request that the quotation or the basket remains valid for a defined period.
18. The retailer could send the relevant pricing or validation request to the concerned Distributor(s), and/or apply the relevant retailer-side rules, in order to update the affected offer(s), basket element(s), price(s) and basket conditions. The Distributor(s) shall return the updated pricing and conditions. If a holding mechanism is required, the shopping process is suspended until approval is obtained, rejected or expired.
19. The retailer shall present the resulting basket and ensure that it remains consistent and up to date, so that no element remains with:
- an outdated price;
- an expired quotation without being identified as such;
- an invalid offer;
- an unresolved dependency; or
- incomplete data required for the next use case.
20. The Customer may repeat any basket operation until he decides to stop modifying it.

- **Basket finalization**
- 
21. Throughout this use case, and in particular after each basket operation (add/modify/remove) or when a quotation validity changes, the Retailer calculates the final price of the whole basket and shall present the final state of the basket to the Customer, including the current contents, the applicable conditions, the current total price, and any remaining validity constraints or dependencies relevant for continuation.
23. The Customer shall validate the basket as the intended purchase solution and request continuation to the next step. Non-nominal situations may occur (examples: distributor unavailable/timeouts, partial availability changes, expired quotation, missing mandatory parameter, dependency conflict). In these cases, the Retailer flags the impacted element(s), requests refresh when possible and prevents continuation for the impacted element(s) until resolved. 
24. When the use case ends, the basket shall be complete, consistent and ready considered ready to proceed: for reservation (BUC-C if not started yet) and payment (BUC-D). If, at this moment, progressing requires a basket lock/freeze or the start of a reservation/hold process, these actions are handled in the dedicated BUC-C (The retailer and/or the relevant Distributor(s) shall ensure that the basket enters a locked state (frozen) in which no operation may alter the basket content). If reservation is implicitly started earlier during shopping in some implementations, it can be described in BUC-C.

### 
### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

- **Direct purchaseshortcut**
1. The Customer has selected a basic offer which is immediately complete and priced. He is invited to proceed directly to the next step (example: he selects the "Purchase in one-click" option) without explicit basket view. This scenario is a shortcut to main scenario.

- **Offer hold for approval**
This is an example of a corporate approval flow; it is not mandatory for all implementations.
1. The Customer, or a corporate booker acting on his behalf, is willing to keep the basket stable for a period (longer than the automatic system does) before validating it, because an internal approval (or any longer quotation validity) is required.
2. The Retailer sends a request to the relevant Distributor(s) to maintain the validity of the concerned quotation(s) for a defined period, according to the applicable conditions. This request may apply to one or more selected offers and/or to the relevant part of the basket impacted by those offers (i.e. all quotations covered by the concerned Distributor(s)). The purchase process is put on hold during this approval period (CUSTOMER PURCHASE PACKAGE(s), TRAVEL BASKET, TRAVEL BASKET ELEMENT(s), PRICE).
3. The Distributor(s) return the validity period and the applicable holding conditions, including, where relevant, any required or optional additional offer, ancillary service, protection or guarantee option needed to maintain the quotation /basket valid for the defined period. The Retailer informs the Customer of the approval deadline and of any constraint linked to this temporary hold.
4. If the approval is obtained in time, the purchase process continues with the validated basket. Otherwise, the basket must be refreshed, repriced, or abandoned.

> **Comment (Vinke, Bob BGH, 2026-05-07):**
> The travel basket is based on offers with no block of price bucket or seat. so a travel basket keeping can enlarge the chance of failure at provisional booking step in use case C. If this means a provisional booking, then we should move this to use case C

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> In BUC-B we only state that such a hold may be required and that the basket is updated with the resulting status/constraint. But provisonnal booking is not a mandatory step in BUC-B

- **Group quotation with dedicated group process**

> **Comment (Vinke, Bob BGH, 2026-05-07):**
> see comment on "offer hold" if a provisional booking is needed, use case C comes in

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> Added in step 2
This is an example of group quotation flow; it is not mandatory for all implementations.
1. The Customer is welling to purchase for a group of travellers, larger than what it is proposed in the catalogue. The Customer submits a specific group offer request, for example, by email for a group quotation (specific channel).
2. The Retailer sends the request to relevant Distributor(s), including group-related constraints. If the quotation can be returned immediately, it is handled like a standard offer in the main scenario; otherwise, the process may put on hold while awaiting the quotation. If providing the group quotation requires a provisional booking / capacity hold, the Retailer triggers BUC-C for the concerned elements; otherwise, this remains a quotation-validity hold within BUC-B.
3. Out of EUDIT scope: An agent or several agents may prepare the quotation and reply, for example, by email to the request. In EUDIT scope: The purchase process then continues when a quotation result is made available to the Retailer and can be used to update or create the relevant offer (CUSTOMER PURCHASE PACKAGE, TRAVEL BASKET ELEMENT).

> **Comment (Juanjo Quesada, 2026-04-24):**
> Is this EUDIT scope?

> **Comment (Bourdelin, Sonia [2], 2026-05-05):**
> I think yes for the feature but not the API to send mail nor agent work. Reformulated

- **Effective reservation during shopping ( if required to continue)**
1. When required for continuity, adding a basket element triggers the need for reservation/hold handling for that element. (SPOT ALLOCATION / SPOT RESERVATION).
2. The retailer starts reservation process in parallel as described in BUC-C.
3. When the reservation is executed, the Retailer returns to the shopping process and updates the basket accordingly.
7. The Retailer presents the updated basket state to the Customer, including the consequences of the reservation-related process. 

- **Mandatory co-sale**
1. The Customer adds, modifies or removes one basket element, but this operation affects another element due to a dependency rule, a bundle rule, or any mandatory co-sale rule (throughfares management).
2. The Retailer sends the relevant update request to the concerned distributor(s) and asks for the consequences of this operation on the other basket element(s). 
3. The Distributor(s) return the consequences of the operation, for example repricing, invalidation, deletion, replacement or the need to add another element. The Retailer informs the Customer of these consequences and asks for confirmation.
5. If the Customer accepts the consequences, the basket is updated, and the purchase process continues. If he rejects them, the previous basket state is preserved.

### Diagram
UML activity diagram

![Une image contenant texte, capture d’écran, diagramme, Parallèle

Description générée automatiquement](BUC-B-shop-and-price-images/image1.png)

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> To update

### Links with inputs
wiki/use-cases/inputs/EUDIT.use.cases_20260324_shared.docx
wiki/use-cases/inputs/BRM_EUDIT_V2.3.xlsx

To be completed
