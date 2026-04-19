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
Note : In Transmodel improved with COROM project proposals, a CUSTOMER OFFER PACKAGE is created when a SALES OFFER PACKAGE has been selected and parameterised for a specific customer context; it is then inserted into the TRAVEL BASKET as part of a TRAVEL BASKET ELEMENT.

#### TRAVEL BASKET management
1. The TRANSPORT CUSTOMER shall start this use case after having selected one or more candidate FARE PRODUCT(s) and their corresponding SALES OFFER PACKAGE(s); fulfilling at least one CUSTOMER OFFER PACKAGE in the previous use case.
2. The retailer shall open, identify or initialise to the TRANSPORT CUSTOMER a logical TRAVEL BASKET, regardless of whether the underlying technical basket is managed by the retailer, by one or more distributor(s), or by both. The logical TRAVEL BASKET may be created explicitly at the TRANSPORT CUSTOMER’s request or implicitly when the first CUSTOMER OFFER PACKAGE is added associated with the current shopping session. This TRAVEL BASKET can be transparent for the TRANSPORT CUSTOMER and not requested for a basic purchase.
- **Display TRAVEL BASKET**  
3. At each step, the retailer shall present to the TRANSPORT CUSTOMER the current shopping context and so, the TRANSPORT CUSTOMER can see a representation/summary of what he is purchasing (current state of TRAVEL BASKET if already exists) including the details of each operation:
   - the list of TRAVEL BASKET ELEMENTs;
   - the CUSTOMER OFFER PACKAGE contained in each TRAVEL BASKET ELEMENT;
   - the detailed PRICE of each TRAVEL BASKET ELEMENT;
   - the total PRICE of the TRAVEL BASKET;
   - the quotation validity period applicable to each concerned CUSTOMER OFFER PACKAGE; and
   - the main conditions attached to each TRAVEL BASKET ELEMENT and to the basket as a whole.
- **Creation of an empty TRAVEL BASKET (optional)**
4. If the TRANSPORT CUSTOMER requests the creation of an empty TRAVEL BASKET, the retailer shall create or request creation of the corresponding basket state in the relevant basket implementation(s), retrieve the resulting basket state where applicable, and present the empty logical TRAVEL BASKET to the TRANSPORT CUSTOMER.
- **Addition of one or many TRAVEL BASKET ELEMENT(s)**
5. The TRANSPORT CUSTOMER may add a CUSTOMER OFFER PACKAGE (selected and defined in previous use case) to his TRAVEL BASKET as a TRAVEL BASKET ELEMENT either :
   - one selected CUSTOMER OFFER PACKAGE in a single action; or
   - several selected CUSTOMER OFFER PACKAGE(s) in one action, including for a multimodal, multi-leg or multi-operator TRAVEL with protection or guarantee option related to connections.
The addition is the only operation that the TRANSPORT CUSTOMER can start the process with. This operation can be the only one available for the TRANSPORT CUSTOMER on the selling system.
If the TRANSPORT CUSTOMER is shopping for a group larger than the standard instant-shopping scope, the retailer may interrupt this flow : the retailer sends a specific request to the relevant distributor(s), including the group-related constraints, and resumes the present use case when the quotation is returned.  
6. For each addition, the retailer shall create one TRAVEL BASKET ELEMENT corresponding to the shopping intention of the TRANSPORT CUSTOMER for the concerned part of the TRAVEL. Each TRAVEL BASKET ELEMENT shall contain one CUSTOMER OFFER PACKAGE representing the customer-specific shopping component derived from the selected SALES OFFER PACKAGE and its customer parameterisation. On each addtion, the retailer shall :
   - identify the relevant distributor for the selected SALES OFFER PACKAGE;
   - confirm (and ends the collect if required) the data required to build thne corresponding CUSTOMER OFFER PACKAGE;
   - send the relevant request to the distributor (including retailer's TRAVEL BASKET if required), where distributor-side processing is required;
   - create or update the corresponding TRAVEL BASKET ELEMENT in the relevant basket implementation(s);
   - (if required) evaluate dependencies between TRAVEL BASKET ELEMENT to assure the consistency of the TRAVEL BASKET and inform the TRANSPORT CUSTOMER of the consequences; and
   - retrieve the resulting PRICE, validity period and applicable conditions for presentation to the TRANSPORT CUSTOMER.
Several CUSTOMER OFFER PACKAGE(s) added in one action may correspond to one multimodal, multi-leg or multi-operator TRAVEL and may be processed either as one combined basket update or recursively element by element.
Pre-reservation step can be started at this moment. The retailer may temporarily interrupt the present use case, start the relevant reservation initiation process with the concerned distributor(s), and then resume the shopping process with the updated TRAVEL BASKET state. This reservation initiation is managed in next use case.
- **Modification of one existing TRAVEL BASKET ELEMENT** 
7. The TRANSPORT CUSTOMER may request modification of one existing TRAVEL BASKET ELEMENT. It can be on, when applicable : quantity, date and time, validity options, eligibility data, traveller assignment, customer account context (including fare contracts and travel documents), reduction rights, class or comfort option (seat preference), ancillary selections, delivery preferences, invoicing order, VAT context. It can be on any other mandatory parameter required by the distributor.
8. On a modification request, the retailer shall :
   - identify the TRAVEL BASKET ELEMENT concerned and the permitted characteristics that may be changed;
   - request from the TRANSPORT CUSTOMER any additional data required to perform the modification;
   - send the relevant update request to the concerned distributor, where applicable;
   - retrieve the updated CUSTOMER OFFER PACKAGE, PRICE, validity period and other applicable conditions; and
   - (if required) evaluate dependencies between TRAVEL BASKET ELEMENT to assure the consistency of the TRAVEL BASKET and inform the TRANSPORT CUSTOMER of the consequences; and
   - retrieve the resulting PRICE, validity period and applicable conditions for presentation to the TRANSPORT CUSTOMER.
- **Removal of one or many existing TRAVEL BASKET ELEMENT(s)** 
9. The TRANSPORT CUSTOMER may request removal of one existing TRAVEL BASKET ELEMENT. He may do it by selecting it in the TRAVEL BASKET display or down the quantity to zero or with any other displayed option.
10. For each removal, the retailer shall :
  - identify the TRAVEL BASKET ELEMENT concerned;
  - send the relevant deletion request to the concerned distributor, where applicable;
  - update the relevant TRAVEL BASKET implementation(s);
  - determine whether the removal affects any other TRAVEL BASKET ELEMENT, any combined pricing rule, any pass condition, any ancillary condition, or any other basket dependency; and
  - retrieve the resulting TRAVEL BASKET state for presentation to the TRANSPORT CUSTOMER.
Severeal removals in one step for the TRANSPORT CUSTOMER can be managed as only one operation or recursively, one by one in order to manage element's dependencies.
- **Clearing the whole TRAVEL BASKET**  
11. The TRANSPORT CUSTOMER may request clearing of the whole TRAVEL BAKSET. He may do it by selecting the TRAVEL BASKET or, on some systems, downing the last element quantity to zero or with any other displayed option. A additional confirmation can be required. 
12. To execute this demand, the retailer shall :
    - request deletion or resetting of all TRAVEL BASKET content in the relevant basket implementation(s);
    - retrieve confirmation of the resulting empty basket state, where applicable; and
    - display the resulting empty logical TRAVEL BASKET to the TRANSPORT CUSTOMER.
On some systems, this operation is managed as a TRAVEL BASKET removeal.

#### Final price calculation
- **Change management**
13. If required, after any operation listed above and before displaying the operation result, the retailer and/or the concerned distributor(s) in interaction with the TRANSPORT CUSTOMER shall evaluate the impact of the operation on:
  - the affected TRAVEL BASKET ELEMENT;
  - any other TRAVEL BASKET ELEMENT;
  - the PRICE of one or more basket elements;
  - the total PRICE of the TRAVEL BASKET;
  - quotation validity;
  - fare combinability;
  - through-fare eligibility;
  - ancillary applicability;
  - pass validity conditions;
  - bundle conditions; and
  - any other dependency between TRAVEL BASKET ELEMENTs.
During each evaluation, the retailer shall send to the relevant request to the concerned distributor and each of them shall return the updated shopping result for the relevant CUSTOMER OFFER PACKAGE(s), including the applicable PRICE, validity period, conditions, restrictions and any detected consequence affecting other TRAVEL BASKET ELEMENTs.
14. The retailer shall consolidate all relevant distributor responses, together with any retailer-side basket processing result, into one updated logical TRAVEL BASKET state. If one or more quotations cannot continue without recalculation (expired), the retailer shall request a mandatory refresh of the CUSTOMER OFFER PACKAGE(s) before the logical TRAVEL BASKET can continue unchanged.
If the requested operation has consequences on one or more other TRAVEL BASKET ELEMENTs, the retailer shall present those consequences to the TRANSPORT CUSTOMER before finalising the TRAVEL BASKET state : 
   - repricing of one or more TRAVEL BASKET ELEMENTs;
   - refresh or expiry of a quotation;
   - loss, addition or modification of a reduction, negotiated fare, entitlement or pass condition;
   - invalidation or activation of a combined offer (fare combination evaluation);
   - change in ancillary eligibility or ancillary PRICE;
   - invalidation of one or more CUSTOMER OFFER PACKAGE(s);
   - deletion or replacement of dependent TRAVEL BASKET ELEMENTs; or
   - modification of the overall basket consistency.  
15. The TRANSPORT CUSTOMER shall either accept the proposed consequences and confirm the TRAVEL BASKET update, or reject the proposed consequences and cancel the operation. If the TRANSPORT CUSTOMER rejects the proposed consequences, the retailer shall preserve the previously consistent logical TRAVEL BASKET state. If the TRANSPORT CUSTOMER accepts the proposed consequences, the retailer shall confirm and apply the resulting basket state across the relevant basket implementation(s).
16. In any case,the retailer shall inform the TRANSPORT CUSTOMER with the result and present the updated logical TRAVEL BASKET, including:
   - all current TRAVEL BASKET ELEMENTs;
   - the CUSTOMER OFFER PACKAGE contained in each element;
   - the PRICE of each element, reduction amount, VAT;
   - the total PRICE of the TRAVEL BASKET, reduction amount, VATs; and
   - the main applicable conditions and validity constraints.    
- **Pricing**  
17. The TRANSPORT CUSTOMER may also provide a promotion code, discount code, entitlement reference, reduction right, corporate identifier (and agreements) or pass-related information applicable to one or more basket elements. The promotion or discount code may be accepted, rejected, or only partially applicable depending on distributor and retailer commercial rules. If the TRANSPORT CUSTOMER, or a corporate booker acting on his behalf, requires an internal approval before continuing, he or the retailer may request that the quotation or the TRAVEL BASKET remains valid for a defined period.
18. The retailer shall send the relevant pricing or validation request to the concerned distributor(s), and/or apply the relevant retailer-side rules, in order to update the affected CUSTOMER OFFER PACKAGE(s), TRAVEL BASKET ELEMENT(s), PRICE(s) and basket conditions. The distributor(s) shall return the updated pricing and conditions. If a holding mechanism is required, the shopping process is temporarily suspended until approval is obtained, rejected or expired.
19. The retailer shall present the resulting updated logical TRAVEL BASKET to the TRANSPORT CUSTOMER and ensure that the logical TRAVEL BASKET remains consistent and up to date, so that no TRAVEL BASKET ELEMENT remains with:
   - an outdated PRICE;
   - an expired quotation without being identified as such;
   - an invalid CUSTOMER OFFER PACKAGE;
   - an unresolved dependency; or
   - incomplete data required for the next use case.
20. The TRANSPORT CUSTOMER may repeat any basket operation until he decides to stop modifying the TRAVEL BASKET.

#### TRAVEL BASKET finalization
21. The retailer calculates the final price of the whole TRAVEL BASKET and shall present the final state of the TRAVEL BASKET to the TRANSPORT CUSTOMER, including the current contents, the applicable conditions, the current total PRICE, and any remaining validity constraints relevant for continuation.
23. When the TRANSPORT CUSTOMER decides to stop modifying the basket, the TRANSPORT CUSTOMER shall validate the selected TRAVEL BASKET as the intended purchase solution and request continuation to the next step. The use case shall end when the TRAVEL BASKET is complete, consistent and ready for the next use case dealing with reservation initiation (if not started yet) and payment.
24. Upon this validation, the retailer and/or the relevant distributor(s) shall ensure that the TRAVEL BASKET enters a locked state (frozen) in which no operation may alter the TRAVEL BASKET content, the CUSTOMER OFFER PACKAGE(s), the applicable PRICE(s), or the associated conditions (fozen maximal duration) and dependencies.
25. The locked TRAVEL BASKET shall constitute the stable input for the next use case dealing with reservation initiation and payment.  
  

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

- **Direct purchase**
1. The TRANSPORT CUSTOMER has selected a basic FARE PRODUCT which is immedialtly a CUSTOMER OFFER PACKAGE. He selects the "Purchase in one-clic" option.
2. The TRANSPORT CUSTOEMR does not see the TRAVEL BASKET : he directly arrives on payment interface. This scenario is a shortcut to main scenario.

- **Offer hold for approval**
1. The TRANSPORT CUSTOMER, or a corporate booker acting on his behalf, is willing to keep the TRAVEL BASKET stable for a period (longer than the automatic system does) before validating it, because an internal approval is required.
2. The retailer sends a request to the relevant distributor(s) to maintain the quotation or the basket content valid for a defined period, according to the applicable conditions. The purchase process is put on hold during this approval period.
3. The distributor(s) return the validity period and the applicable holding conditions. The retailer informs the TRANSPORT CUSTOMER of the approval deadline and of any constraint linked to this temporary hold.
4. If the approval is obtained in time, the purchase process continues with the validated TRAVEL BASKET. If the approval is not obtained in time, the TRAVEL BASKET must be refreshed, repriced, or abandoned.

- **Group quotation with dedicated group process**
1. The TRANSPORT CUSTOMER is welling to purchase for a group of travellers, larger than what it is proposed in the catalogue. He submits a specific group offer request, for example, by email for a group quotation (specific channel).
2. The retailer sends the request to relevant distributor(s), including group-related contraints. The purchase process is put on hold while awaiting the quotation (step 6).
3. An agent or several agents prepare the quotation and reply, for example, by email to the request. The purchase process then continues.     

- **Effective reservation during shopping**
1. The TRANSPORT CUSTOMER adds in his TRAVEL BASKET a CUSTOMER OFFER PACKAGE with a reservation. For this reservation a reservation-related process must be started in order to continue shopping on a consistent basis.
2. The retailer temporary leaves the the current use case and start reservation process as described in next use case with the relevant distribtor(s) using the current state of the affected CUSTOMER OFFER PACKAGE(s).
3. When the reservation is executed; only to secure the TRAVEL BASKET content for continuation of the shopping process, the retailer returns to the shopping process and updates the logical TRAVEL BASKET accordingly.
7. The retailer presents the updated TRAVEL BASKET state to the TRANSPORT CUSTOMER, including the consequences of the reservation-related process on the concerned TRAVEL BASKET ELEMENT(s) and on the basket as a whole.
8. The TRANSPORT CUSTOMER may then continue the shopping process, following presetn use case.

- **Mandatory Co-sale**
1. The TRANSPORT CUSTOMER adds, modifies or removes one TRAVEL BASKET ELEMENT, but this operation affects another TRAVEL BASKET ELEMENT because of a dependency rule, a bundle rule, or a mandatory co-sale condition.
2. The retailer sends the relevant update request to the concerned distributor(s) and asks for the consequences of this operation on the other TRAVEL BASKET ELEMENT(s). The purchase process is temporarily blocked while awaiting the dependency evaluation.
3. The distributor(s) return the consequences of the operation, for example repricing, invalidation, deletion, replacement or the need to add another element.The retailer informs the TRANSPORT CUSTOMER of these consequences and asks for confirmation.
5. If the TRANSPORT CUSTOMER accepts the consequences, the basket is updated and the purchase process continues. If he rejects them, the previous basket state is preserved.


### Diagram 
UML activity diagram


### Links with inputs
BRM_EUDIT_V2.3.xlsx
-  

To be completed


