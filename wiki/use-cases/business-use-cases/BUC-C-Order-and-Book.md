**Use Case Overview** 
- **Business Use Case ID & Name:** BUC-C — Order & Book (+ begin of reservation)​ 
- **Goal (Objective):** preparing the TRAVEL BASKET (BUC-B ) the Transport Customer selected, to make sure it is available before Retailer – Distributor payment takes place 

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Add the 2 steps between the basket and the payment : Secure inventory/options with a time limit and confirm the sales after the financial agreement
- **Scope:** adding reservations and ancillaries to the Travel Basket, entering personal data, creating a provisional booking (+ beginning of reservation) and making it final 

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> We could add here a terminology chapter to clarify "reservation / hold : a claim on a ressource" and "booking / finalisation : the action that make the sale definitive..."
> Same for "preliminary booking" and "final booking" if you want to use these terms (...‘preliminary booking’ refers to a temporary hold/reservation of inventory (reservation), while ‘final booking’ is confirmed only once the Retailer confirms financial commitment )

> **Comment (BIGEX Olivier, 2026-05-27):**
> Could be great. Add freeze of the price in preliminary booking (to give the customer time to pay if dynamic pricing).
**Actors & Context** 
- **Primary Actor:** **TRANSPORT CUSTOMER (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wanting to finalize what is selected in the TRAVEL BASKET  
He/she can be the manager of a group, a PRM, the purchaser for a minor traveler or other with specific needs or none.  A travel agent acting on behalf of a corporate client. A corporate travel manager. A multi-modal aggregator. 

> **Comment (Bourdelin, Sonia, 1900-01-01):**
> Proposition : change for business word Customer

- **Supporting Actors / Stakeholders:** 
- **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** supports the customer by making sure the selected CUSTOMER PURCHASE PACKAGE(s) is completely available, before payment process
- **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** creates the provisional booking and creates the reservation.   


> **Comment (Bourdelin, Sonia, 2026-05-26):**
> + manages time limit
- **Assumptions (context at start):** 
- The TRANSPORT CUSTOMER can provide the additional information required to finalize the transaction (anonymous booking app; traveler information; driver license etc..)   
**Preconditions & Postconditions** 
- **Preconditions (must be true before start):** 
- TRANSPORT CUSTOMER has a TRAVEL BASKET containing at least one TRAVEL BASKET ELEMENT with one CUSTOMER OFFER PACKAGE

> **Comment (Bourdelin, Sonia, 1900-01-01):**
> offer that need a reservation: "at least one element that requires inventory/options securing (AVAILABILITY CONDITION)"
- **Postconditions — Success guarantees :** 
- All items of the TRAVEL BASKET are confirmed / reserved so they will be available for fulfilment and Settlement between Retailer and Distributor and fulfilment. 

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> some element that require reservation. This BUC do not affect the others elements of the basket.

> **Comment (BIGEX Olivier, 2026-05-27):**
> The hypothesis here is that any offer is really bought on distributor side only if a confirmation of the purchase to the distributor is done. Thus all items shall be confirmed (and some of them reserved), in order to be ready for the fulfilment.
- **Postconditions — Minimal guarantees:** 
- If one of the offer parts cannot be confirmed, the customer receives a clear “no offer bookable” outcome (with a reason where possible).  

> **Comment (Bourdelin, Sonia, 1900-01-01):**
> And in some cases, new proposal and/or available actions (change option/leg/operator, refresh offer, restart selection in BUC-A/BUC-B)

**Scenarios** 
**Main scenario** 

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> I suppose that the first chapter "selecting..." and third 'customer details ...' are parts of pre-reservation ? Could be only 2 blocks in main scenario : pre-reservation and finalizing the booking ?

> **Comment (Bourdelin, Sonia, 2026-05-19):**
> We have to choose where we set the Frozen/locked state of the basket : in BUC-B at the end or in BUC-C at the begining.

> **Comment (BIGEX Olivier, 2026-05-21):**
> The backet elements need to be locked in case of dynamic pricing or of facilitiy/seat reservation. It is done in basket management (as soon as an offer is put in the basket). But it seems that the choice is to describ that here in BUC-C (Requesting preliminary booking).

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Add an explicit step here like that : "Retailer freezes the basket content for reservation securing (TRAVEL BASKET / TRAVEL BASKET ELEMENT). Changes after this point require restarting reservation securing for impacted elements. "
- **Selecting reservation preferences and ancillaries** 

> **Comment (BIGEX Olivier, 2026-05-11):**
> To be added somewhere: an option can have a price ==> an option is a sub-offer and the selection of this kind of offer follow the same process than BUC-B (basket management), with an update of the final price each time an option is selected/deselected.

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> add something like : "When an ancillary/option has a price, treat it like a selectable sub-offer: selecting/deselecting triggers repricing similarly to BUC-B (basket update)"

> **Comment (Edwin van den Belt, 2026-05-20):**
> Question: a reservation is not a booking, but a claim on a resource (seat, bike, etc). How about just requesting a travel right (without reservations)?

> **Comment (BIGEX Olivier, 2026-05-21):**
> This question probably comes with the structure of this document. Each bullet point is optional (except the last one: finalizing the booking). Maybe point out that ?
- The TRANSPORT CUSTOMER can request reservation and ancillary options if available. To do that Retailer asks Distributor(s) for all reservation and ancillary options  


> **Comment (Bourdelin, Sonia, 2026-05-26):**
> To securize the options (seat, bike spot, service) with a time limit (RESERVATION, AVAILABILITY CONDITION, SPOT ALLOCATION)
- Distributor supplies all reservation and ncillary options with the price and all relevant details.  
 

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> I would add once here the list of the concepts : available reservation/facility options, constraints per leg/operator, price where applicable, and availability status
- TRANSPORT CUSTOMER expresses seating preferences and constraints, including seat position, coach, deck, adjacency to another passenger, and mandatory vs preferred requirements, across operators and modes. The Retailer should know per leg what reservation possibilities are available. The Retailer should be able to assign seats or ancillaries per operator, ensuring consistency across the journey. The Retailer supports cases where not all the legs or operators or modes included in the journey can satisfy the requested preferences.  


> **Comment (BIGEX Olivier, 2026-05-11):**
> Or based on:
> - seat preferences stored in a customer account.
> - customer profile: a PRM with wheelchair will be automaticaly placed in a dedicated seat (if available). Same for a companion of a PRM (placed near the PRM)

> **Comment (Vinke, Bob BGH, 2026-05-15):**
> I find this a retailer internal function. Does not change EUDIT specification.

> **Comment (JUGELT Stefan, 2026-05-20):**
> This knowledge comes from the distributor.

> **Comment (BIGEX Olivier, 2026-05-21):**
> Indeed. How comes this information from the customer is an internal function of the retailer, based on possibilities provided by the distributor. But in that case, the sentence could be written the other way round: «the retailer collects seating preferences…»

> **Comment (BIGEX Olivier, 2026-05-11):**
> each option can be bound to a CPP or to a CPP element.

> **Comment (Vinke, Bob BGH, 2026-05-15):**
> CPP is transmodel.. What would you add business wise to this line?

> **Comment (BIGEX Olivier, 2026-05-21):**
> If we come back to business, language, then «leg» seems correct

> **Comment (Bourdelin, Sonia, 2026-05-21):**
> and in some cases, Retailer or Distributor can propose alternatives
- Customer can select seat preferences (window/aisle, facing direction, quiet zone, power socket, legroom) depending of each operator.  


> **Comment (Bourdelin, Sonia, 2026-05-21):**
> can be a bike sport, ... larger than a seat

> **Comment (BIGEX Olivier, 2026-05-21):**
> Could we use the generic wording «facility»?

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Yes, facility covers  seat, bike space, etc.(RESERVATION / SPOT ALLOCATION).
- Customer can choose his preferred seat if the operator supports seat map selection based on a graphical coach/deck layout, including available, reserved, and unavailable seat indicators. Retailer ensures consistent seat map representation despite heterogeneous seat models.  


> **Comment (Bourdelin, Sonia, 2026-05-21):**
> many possibilities : chose one seat, let the operator chose the best seat for you with your criteria, let the operator chose the best seat corresponding to your offer
- Retailer can support asset-specific ancillary services (helmet rental, child seat, cargo trailer, EV charging reservation; luggage; meals; seat upgrade) bookable alongside the leg. Ferry-specific extras: pet transport (IATA pet category), wheelchair-accessible cabins, dietary meal requests, and lounge access.  


> **Comment (Bourdelin, Sonia, 2026-05-21):**
> is it link with the seat ? In some cases yes, in some cases no (bike spot in second class in french TER is not linked with a seat reservation)

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Enlarge to all transportables, facility,
- Special PRM specifications can be taken into account at this stage in the process (…additional specific requirements?.)  


> **Comment (BIGEX Olivier, 2026-05-11):**
> That’s a tricky point. The station-based assistance is typically not provided by the operator but by the infrastructure provider. Thus out of scope of EUDIT ?

> **Comment (Vinke, Bob BGH, 2026-05-15):**
> How does the current PRM booking UIC work? Do you book ticket and assistandce at the same time? or is it a separate proces?

> **Comment (BIGEX Olivier, 2026-05-21):**
> At least in France, 2 separated services, but the purchase of a ticket must be proven to the infrastructure manager (PNR/ticket ref)

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> You can mark ‘station-based assistance request’ as out-of-scope or separate service, while keeping operator-reserved facilities in scope
- In case of reservation only, this step can take place based on trip only selection in use case A, where reservation only was selected. 

> **Comment (BIGEX Olivier, 2026-05-11):**
> To be developped here: the customer can resquest a reservation only, if he already has a valid fare contract (e.g. a pass or a single ticket). But to do that, he shall prove that he has a valid fare contract (or retrieved by the retailer)

> **Comment (Vinke, Bob BGH, 2026-05-15):**
> For what mode / standard is this proof needed? Currently OSDM supports seling reservation without proof.

> **Comment (BIGEX Olivier, 2026-05-21):**
> Indeed. We could consider that this proof process is an internal process of the distributor/fare provider. But at least the related travel right could (not mandatory) be added as customer context (booking/ticket ref ?).
It is possible this step is done as part of SALES OFFER PACKAGE selection in use case A

> **Comment (Bourdelin, Sonia, 2026-05-21):**
> option of SOP
- **Requesting preliminary booking**
- The TRANSPORT CUSTOMER confirms the selections and confirms to want to proceed to finalizing the sale.    


> **Comment (BIGEX Olivier, 2026-05-11):**
> Not necessarily. The preliminary booking can happen at the moment when a CPP is put in a Travel Basket, in order to secure the price and the seat availability. 
> Maybe delete simply this 4th point ?

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Or reformulate as the pre-booking can be, in some cases,  triggered earlier (when an offer is added to basket) in the purchase process.

> **Comment (BIGEX Olivier, 2026-05-27):**
> Ok with this reformulation
- The RETAILER requests for preliminary booking of the selected CUSTOMER OFFER PACKAGE(s) by requesting the SALES OFFER PARTS at all relevant DISTRIBUTOR(s)  


> **Comment (Bourdelin, Sonia, 2026-05-26):**
> SALES OFFER PACKAGE for the "offer". If you need to speak about a part of a SOP (= SALES OFFER PACKAGE ELEMENT if it is an association to a special TRAVEL DOCUMENT), you could use offer part for BUC. And explain if something is missing in Transmodel : we have in EUDIT to propose evolutions
- The Distributor(s) check(s) the availability of the SALES OFFER PARTS at the operator(s) and creates a preliminary booking and reservation(s) if required. (The reservation is actually created). The created booking may include a defined payment time limit, after which the order or parts of it may be automatically cancelled or released according to the operator rules. This time limit can differ, depending on the time left till the time of departure. 

> **Comment (Vinke, Bob BGH, 2026-04-28):**
> How does it work if an operator replies with a PENDING reply, where other operaters can provide direct or provisional confirmation?

> **Comment (BIGEX Olivier, 2026-05-11):**
> Or the operator needs technicaly more time to reply.
> Asynchroneous booking is way more complexe and requests specific technical architecture. In scope ?

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Operator ‘PENDING’ / async confirmation should be moved to an alternative scenario (requires polling/callback/timeouts). Nominal path should remain synchronous confirmation of securing (RESERVATION).

> **Comment (BIGEX Olivier, 2026-05-11):**
> Not a payment time limit, but a booking (=finalization) time limit, which includes a payment step on retailer’s side

> **Comment (Bourdelin, Sonia, 2026-05-21):**
> We have the both time limits : the one of payment is managed in BUC-D and the one on reservation is managed here. Please split in 2 and manage here reservation time limit
The Distributor confirms the success of the preliminary booking to the Retailer. This confirmation consists of a time limit, the preliminary booking will be secured for this time limit. The Distributor assigns unique booking references (booking code, external reference) that can be used by Distributor, Retailer and operator systems.  
The Distributor requests the personal data requested by the operator(s). (Legal name (title, forename, surname; Nationality (ISO 3166-1 alpha-3; Identity documents (type, number, issue country, expiry date); Legal name (title, forename, surname); Nationality (ISO 3166-1 alpha-3); Identity documents (type, number, issue country, expiry date); Emergency contact (ICE phone, email); Place of birth, VISA/permit number (for international crossings); **Vehicle data** (registration plate) etc..

> **Comment (Vinke, Bob BGH, 2026-04-28):**
> should FR-BOOK-015 be part of this step” The booking confirmation response shall include a detailed payment breakdown showing each payment instrument used (card, voucher, loyalty points, corporate account), the amount applied per instrument, and per-operator settlement allocation, enabling transparent multi-method payment reconciliation.”

> **Comment (BIGEX Olivier, 2026-05-11):**
> Yes, but not here but for the finalization

> **Comment (RECEVEUR Jean-Baptiste, 2026-05-28):**
> If the reference is unique only on the Distributor side, it might be "not unique" on the retailer side. They both need to assign a reference which will be unique in each system.
- **Providing relevant CUSTOMER details**

> **Comment (BIGEX Olivier, 2026-05-11):**
> does the customer provide fulfilment preferences and data (NFC smartcard) as well or not at this step ?

> **Comment (Bourdelin, Sonia, 2026-05-21):**
> Perhaps move the title just before peceeding sentence ?

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Traveller can be a group. Add refernces to this case and moreover, a group may require only capacity hold, not defined seats. Capacity hold can be also the only need for one traveller.
- The TRANSPORT CUSTOMER supplies all relevant personal data needed by the operators for the selected offer. This concerns the data that is not needed to calculate an offer and is personal, so should only be collected if the offer needs to be finalized. And only that data needs to be collected needed for the selected offers. Data is only sent to those distributors that need it.  


> **Comment (JUGELT Stefan, 2026-05-20):**
> Those data should be only provided if strictly needed for the contract execution.

> **Comment (Vinke, Bob BGH, 2026-05-21):**
> made a bit more specific.

> **Comment (Bourdelin, Sonia, 2026-05-21):**
> to be conform with ....RGPD
- When required by one or more operators, the gender passenger data is transmitted only when required by the operator for the specific service. Permissible values align with ERA/OSDM gender enumeration.  

- For a group booking the list of TRAVELERS might not be complete, to amend in later stage.  


> **Comment (JUGELT Stefan, 2026-05-20):**
> This might be difficult as the amount of travellers and their type should be fix at this stage.

> **Comment (Vinke, Bob BGH, 2026-05-21):**
> but the amount of travelers and type is not related to GDPR. it can not be related to a specific individual. so i do not see the problem.
- If relevant for one of related operators, the Retailer will assure licence type validation (driving licence category) as a precondition for reserving vehicle categories that require a licence.  


> **Comment (Bourdelin, Sonia, 2026-05-21):**
> validity in general ?
- 5. The TRANSPORT CUSTOMER confirms the final purchase of the TRAVEL BASKET. Confirming the responsibility for payment. 

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> remove number

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> basket
- **Finalizing the booking**
- The Retailer ensures the financial transaction with the TRANSPORT CUSTOMER. The Retailer confirms the booking to the Distributor after making sure the transaction will be payed by the TRANSPORT CUSTOMER.  


> **Comment (Bourdelin, Sonia, 2026-05-26):**
> The Customer wants to finalize the purchase after financial engagement in order to trigger the next step in BUC-E : receive the travel rights and documents (SALES OFFER PART(s), TRAVEL DOCUMENT(s))
- The retailer takes care of a B2C payment before finalizing the booking  

- The Retailer has another payment arrangement with the customer (account based; invoice etc. )  

- the TRAVEL may be a Group TRAVEL with special payment conditions.  


> **Comment (Vinke, Bob BGH, 2026-04-28):**
> Is this part of this step, or earlier before provisional booking?
- The TRANSPORT CUSTOMER might have an operator specific voucher as a payment means for the operator specific offer part. INTERACTION alternative flow?  


> **Comment (Vinke, Bob BGH, 2026-05-06):**
> is this covered in use case D? or only, when Retailer - Distributor interaction is needed in case of 9-d voucher?

> **Comment (BIGEX Olivier, 2026-05-11):**
> Yes, BUC-D.
> Coudl be reformulated by:
> «Once the retailer has ensure the financial transaction with the TRANSPORT CUSTOMER (see BUC-D), the retailer confirms the booking» 
> that’s all

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> yes move payment execution details in BUC-D and add a reference here

> **Comment (Bourdelin, Sonia, 2026-05-21):**
> Yes, in BUC-D is is explained also. Refers to BUC. But it is clearer to spoke about that in both cases for me
- The Distributor finalizes the sale of all SALES OFFER PARTS with all operators and confirms the completed booking to the Retailer.  
The Distributor returns a full price breakdown including: base fare, applicable VAT per jurisdiction, other charges (booking fees, distribution fees), and the grand total. For journeys crossing international borders, the applicable VAT rate can differ from domestic VAT in one or more countries traversed. 

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> offers that need reservation

> **Comment (BIGEX Olivier, 2026-05-27):**
> Not only. The assumption is that any offer shall be confirmed to the distributor before fulfilment

> **Comment (BIGEX Olivier, 2026-05-11):**
> Returns «through ticket» (according to PRR) final guarantee as well.

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> + Final guarantees relevant even for 'though journey'

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> + in addition explicitly returning final guarantees relevant to ‘through journey’ when applicable (TRAVEL GUARANTEE(s))
**Alternativesscenarios**

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> IS the Order splitting described in EUDIT.use.case... docx input is taken into account ?
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.   
- **Price change**
4.a When during the pre booking step, the inventory is still available but the price of a previously returned SALES OFFER PART changes during booking confirmation. The Distributor response shall clearly indicate price change, provide the updated offer with new price and validity, and allow the Retailer to present the change to the customer before proceeding. Use case A can be used to select a new SALES OFFER PACKAGE or the customer can abandon the flow.

> **Comment (JUGELT Stefan, 2026-05-20):**
> Is my understanding correct that the price can change even if a valid pre-booking exists?

> **Comment (Vinke, Bob BGH, 2026-05-21):**
> no. the pre-booking step is meant to block the price and fysical inventory for a specific period for this customer. 
> This part reflects to the fact that during the step to achieve the pre-booking, the distributor can find out that in the meanwhile the current price or inventory is no longer available. This can be the fact, as the offer does not reserve. it is just a view on the current situation in the inventory.

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> There is a case when once a reservation/hold is confirmed (RESERVATION), price/inventory should remain stable within the hold time limit; price change can occur during the securing attempt (if hold not yet confirmed). If you want you can precise this case

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> is it a reference to step 4?
- **No complete preliminary booking**
4.b The distributor cannot confirm one or more of the requested SALES OFFER PACKAGE PARTS. A failure message with reason and affected OFFER PART is sent.  
  
Retailer informs the customer of a booking failure and helps him to finalize an alternative offer selection, using BUC A and B. 
If the booking is aborted due to the partial failure, the Retailer performs a rollback to release locks on successfully locked legs, preventing customers from blocking partial journeys.

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> even a total failure need a rollback to release any succesful holds to avoid blocking ressources
- **Mand**a**tory seat selection/preference no longer available**
If the seat selection / preference is no longer available this is returned by the Distributor, so Retailer can inform the TRANSPORT CUSTOMER. If the seat selection was a fixed (payable) choice, the TRANSPORT CUSTOMER can change his selection. 
- **Passenger blacklist**
Retailer should know when one of the operators has a Passenger blacklist. If this is a case, the retailer must use the passenger data to check against this blacklist just before customer payment. The response returns an appropriate refusal reason without disclosing the specific blacklist reason to the end user.interaction with distributor

> **Comment (JUGELT Stefan, 2026-05-20):**
> Are we sure that this is part of the specification? Wouldn't it be the task for the Distributor in case of a ressource reservation?

> **Comment (Vinke, Bob BGH, 2026-05-21):**
> as the pre-booking fase is still anonimous AND as blacklist preferabely takes place bofore payment, a step between pre-booking and payment could be relevant. The PRE-booking reply could show that a blacklist check is manditory before payment. wiht a specifick API call in between. TO BE DISCUSSED.

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Yes it can be done but is it the retailer responsability ?

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Who is requesting that check ? To precise
- **Inventory release at time limit exceed**
If the time limit is exceeded, the Retailer can ask the TRANSPORT CUSTOMER if he still want to purchase. If so, the Retailer asks for a new time limit. If not the Retailer releases the preliminary booking. 
If the Retailer does not finalize the preliminary booked SALES OFFER PARTS within the time limit, the Distributor contacts all operators to release inventory. 
- **Single anonymous travel** 
If the TRANSPORT CUSTOMER has an anonymous travel profile, the Retailer uses this function for those operators that support this, making sure that anonymous travel is used where supported.   
- **Post- BookingAdd-On**
The Retailer can add ancillary services, seat upgrades, or optional reservations to an existing confirmed booking without cancelling the original booking. The Distributor returns the updated order with new price and fulfillment details. 
- **Price calculation afterthe trip**

> **Comment (Bourdelin, Sonia, 2026-05-26):**
> Can we have a reservation and travel with an EMV card ? I think it is the case we must cover : reserve a bike space/ something and travel with ENV card or any other post-payment contract.
In sales processes like Fairtiq BeIN-BeOUT or chipcard / EMV CheckIN-CheckOUT this process can be followed after the trip. The Retailer can collect all relevant TRIP and product information, during the TRAVEL. After the TRAVEL, the Retailer can finalize the transaction. 

> **Comment (BIGEX Olivier, 2026-05-11):**
> To me reviewed more deeply. With EMV or smart card post-payment, the retailer is not involved, but validating stakeholders: checkin/out events are directly sent to the operator.

> **Comment (BIGEX Olivier, 2026-05-11):**
> BUC-C could only deal with the (optional)subscription of a post-payment service. Another BUC could detailed waht happens after the Trip

> **Comment (Vinke, Bob BGH, 2026-05-15):**
> how does EMV work in relation to a sales proces of a multi model trip?

> **Comment (BIGEX Olivier, 2026-05-21):**
> If multimodal trip I assume that the customer is simply informed that for that leg he will only need to checkin(/checkout). Or handle subscription if mandatory.
- **Longer time limit for special Transport customer processes**
Holding an offer beyond the standard offer lifetime can be a chargeable service defined unilaterally by the operator(s). The operator publishes the applicable hold fee, maximum hold duration (e.g. 24 hours), and the fee collection method. The Distributor returns the hold fee amount and currency in the offer response when an on-hold option is available; a hold requires explicit distributor acceptance of the fee before activating the hold by the Retailer. The distributor returns a hold reference, hold expiry timestamp, and confirmation of the amount that will be charged. 
The Retailer can release the hold before expiry with appropriate fee treatment per operator rules (e.g. fee forfeited or refunded). The consumer-facing price can be distinguishes the hold fee from the transport fare.

**Diagram** 
UML activity diagram 

<xx> 

**Links with use cases**
Link to (https://github.com/TransmodelEcosystem/EUDIT/discussions/36#discussioncomment-16183779)   
To be completed
