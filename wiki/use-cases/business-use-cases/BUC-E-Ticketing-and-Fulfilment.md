In writing stage – version 1
## Use Case Overview

- **Business Use Case ID & Name:** BUC-E— Ticketing and Fulfilment 
- **Goal (Objective):** Enable the Customer to obtain travel access rights resulting of what he purchased, reserved and paid. His mobility contract(s) must be created according to the confirmed offers. The customer may also receive the appropriate supports/media (can be inspected).   
- **Scope (Summary) :** 
  - Confirmation that selected offer(s) / basket element(s) are ready for fulfilment,
  - Retailer request to Distributor for fulfilment constraints, supported fulfilment methods and required data
  - Creation or confirmation of travel rights and contract(s), 
  - Issuing, generation, registration, retrieval or loading of travel evidence with Retailer-side, Distributor-side, shared or third-party fulfilment architectures,

> **Comment (Bourdelin, Sonia, 2026-05-21):**
> Add the management of Activation and deactivation of travel documents must be supported where relevant (e.g. BoB activate-ticket, TOMP activate-product).
  - Distributor response with document, token, account/media update, retrieval reference, delivery reference, tele-distribution of all required media, or pending/failed status with management of delayed distributions, 
  - Coordination with reservation process (confirmation of contract creation and fulfilment, data exchange, failure management), 
  - Consolidation of fulfilment status by the Retailer and provision of the final fulfilment result to the Customer,
  - Management of fulfilment failure, partial fulfilment, delayed fulfilment, reissue or cancellation before delivery 
  - Traceability for later validation, inspection, account consultation and after-sales use cases. 

## Terminology note — 

The **travel right** (FARE CONTRACT) represents the created travel access right. It is the contractual right allowing the TRANSPORT CUSTOMER or another entitled passenger to access the transport service under defined conditions. In some systems it may be explicit; in others it may be implicit or account-based.

> **Comment (BIGEX Olivier, 2026-05-22):**
> Should be extended to all kinds of mobility contract, which are not sufficient to travel. Like reduction card, facility reservation...

The **purchase record** (SALES TRANSACTION) is a record of the confirmed sale on Retailer side once the purchase has reached a sufficiently binding state.

The ticket / travel evidence /**support** (TRAVEL DOCUMENT) is the medium or representation distributed to the TRANSPORT CUSTOMER or traveller to prove or use the right. It may be physical, digital, account-based, token-based, barcode-based, smartcard-based, mobile wallet-based, printed, dematerialised or retrievable later through a customer account.

> **Comment (BIGEX Olivier, 2026-05-22):**
> Enum ==> not sure that the list is exhaustive. A deep analyse of possible enums for fulfilment methods and fulfilment media should be done, based on existing standards. 
> --> write here that these are exemples.

A FARE CONTRACT may exist without an immediately distributed TRAVEL DOCUMENT if the access right is stored in an account, on a smart medium, or if the document is generated later. Conversely, a TRAVEL DOCUMENT should always refer to one or more underlying access rights, contracts or entitlement records.
The **distribution channel** (DISTRIBUTION CHANNEL) is the channel through which the travel evidence is delivered or made available.
The **fulfilment method** (FULFILMENT METHOD) is the way the travel right is made usable: online ticket, mobile ticket, smartcard loading, collection, account update, physical delivery, etc.: delivery of the proof of the access rights or the media support.

The term **order** is not used as a normative EUDIT / Transmodel business object in this use case.
A Retailer may internally manage an “order”, but this remains an implementation-specific retail object. In this BUC:
- before confirmation, the business flow is described through the basket and basket elements (TRAVEL BASKET, TRAVEL BASKET ELEMENT); 
- the selected Distributor offer remains traceable as a CUSTOMER PURCHASE PACKAGE; 
- after confirmation, the sale is traceable through a purchase record (SALES TRANSACTION); 
- fulfilment creates or confirms the travel right (FARE CONTRACT) and the travel evidence (TRAVEL DOCUMENT) or equivalent account/media update.

## Actors & Context

- **Primary Actors:**
  - **Customer (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** wants to receive usable access rights, documents, media updates, account updates or retrieval information to travel and being inspected (proof of travel rights). He acts for himself/herself and/or for the travellers represented, including groups, PRM travellers, minors, corporate travellers or other travellers with specific needs. 

    - **Traveller** (PASSENGER ROLE): the person(s) who will actually travel and use the travel rights. The Traveller and the Customer can be the same person but not always.
- **Supporting Actors / Stakeholders:**
  - **Retailer (**FARE PRODUCT RETAILER ROLE (API consumer)): manages the fulfilment process for the customer. He presents fulfilment choices and statuses, coordinates Distributor-side fulfilment when required, and may distribute directly when authorized.
- 
  - **Distributor** (FARE PRODUCT DISTRIBUTOR ROLE (API provider)): may create, confirm or validate the travel access right(s) and contract(s). He may manage the ticketing constraints, coordinate or have ticketing issuer role(s), provides or enable access to the support(s) (TRAVEL DOCUMENT(s)).

- **Other retailer/distributor-facing Actor :** where applicable
  - **Media and Medium Application Provider** (MEDIA PROVIDER ROLE, MEDIUM APPLICATION PROVIDER ROLE) : May load or activate a physical or digital support, such as a smartcard, NFC mobile application, RFID token or wallet.
  - **Fulfilment Provider** / Document Generator (FARE PRODUC ISSUER ROLE) : May generate a barcode, QR code, Aztec code, PDF, BoB BCT token, wallet pass, collection reference or other document artefact.

## Preconditions & Postconditions

- **Assumptions and Preconditions (must be true before start):**
  - Payment phase has ended with a known payment or confirmation status for the concerned basket element(s) and even zero-amount basket element are ready for fulfilment.
  - The Retailer knows which Distributor is responsible for each selected offer with its fulfilment rules (immediate fulfilment, delayed fulfilment, account update, media loading, collection, physical delivery, or no separate document because the right is account/media-based.)
  - Distribution channel is chosen by customer and accessible, even asynchronously. 

> **Comment (Vinke, Bob BGH, 2026-05-18):**
> one booking might even have different distribution chanals, if not all products support the same?

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Yes we manage it in the BUC
  - Different architectures are known and associated with relevant contracts and the chosen distribution channel for the media. The contract creation and / or the support distribution can be done by : retailer-side fulfilment (under delegation), distributor-side fulfilment, shared fulfilment, third-party fulfilment (fulfilment provider or complete ticketing system).
  - Different architectures are known and associated with relevant supports: The support can be delivered immediately after the payment, after a hold-on delay, after reservation finalization, after contract loading on contactless card, travel account, mobile application,… or delayed later to station equipment, travel agency,…
  - The BUC-E is contract creation and fulfilment architecture neutral. 
  - 
  - 
- 

- **Postconditions — Success guarantees:**
  - The contracts are created and ready to be used.
  - The Customer has received appropriate: 
    - travel right (**FARE CONTRACT** or equivalent access right) is created, confirmed or updated, 
    - travel evidence (**TRAVEL DOCUMENT**) or equivalent, account/media/retrieval reference is created or made available
  - The Retailer presents a coherent status to all parties:
    - each fulfilled selected offer has a clear fulfilment status,
    - each Distributor returns the identifiers and status needed for traceability,
    - each Retailer consolidates the status across all Distributor(s),
    - the Customer is informed of what is usable, what is pending, and any remaining action before travel.
  - The fulfilment result remains traceable to the basket element, selected offer, purchase record, travel right and travel evidence.

> **Comment (Vinke, Bob BGH, 2026-05-18):**
> why is this relevant for this UC?

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> The references (support, contract, payment) must be recorded for after-sales validation inspection, ...
- 
- **Postconditions — Minimal guarantees:**
  - If fulfilment cannot be completed, the Customer is informed with status, consequences and next possible actions for each impacted travel right.
  - The Retailer is responsible for the coherency between contract and supports : he prevents the Customer from misunderstanding. 

> **Comment (Vinke, Bob BGH, 2026-05-18):**
> do you have a definition of suports?

> **Comment (Bourdelin, Sonia, 2026-05-20):**
> Added terminology chapter
  - The Distributor returns a business status and next possible action: retry, alternative channel, pending notification, cancellation before distribution, reissue, manual support or transfer to after-sales.
  - Actions are logged and can be audited.

## Scenarios

### Main scenario
1. The use case starts when one or more selected offer(s) / basket element(s) are ready for fulfilment after BUC-D. The Retailer identifies, for each selected offer, the data required for fulfilment with at least: traveller(s), selected or available fulfilment method (FULFILMENT METHOD), delivery, account, medium or retrieval data (TYPE OF TRAVEL DOCUMENT).

- **Fulfilment preparation**
2. The Retailer checks the coherence of the selected offer(s) from a fulfilment point of view and coordinates: he prepares the fulfilment summary for the Customer and prepares the Distributor requests with: 

> **Comment (BIGEX Olivier, 2026-05-22):**
> «booked-offer» ?
> To insist on the fact that the offer has been purchased ?
  - selected offer(s) to fulfil and its Distributor responsible for each selected offer, 
  - requested fulfilment method(s), 
  - expected travel evidence type(s), 
  - whether fulfilment is immediate, delayed, account-based, media-based, collection-based or physically delivered, 
  - any remaining information needed before fulfilment.
This summary is shown to Customer who can confirm or update fulfilment according to business rules.
3. For each concerned Distributor, the Retailer requests fulfilment readiness and constraints with 
  - reservation / seat / ancillary / service references where applicable.
  - traveller data required for ticketing;
  - selected fulfilment method;
  - requested type of travel evidence (TYPE OF TRAVEL DOCUMENT);
  - delivery channel or retrieval channel;
  - customer account or target medium reference;
  - required timing;
  - any customer-facing constraints already presented by the Retailer
4. The Distributor returns whether fulfilment can proceed and under which conditions (revalidation required, missing data, who will generate the travel evidence and contract, security requirements, delays, medium or account constraints, rules in case of failure).

> **Comment (BIGEX Olivier, 2026-05-22):**
> A 2-steps interaction with the distributor for fulfilment (prepare + fulfil) cannot be imposed. Let the forge decides the endpoints. 
> --> change step 3 with «the retailer checks fulfilment readyness and constraints, based on information provided by the distributor :» and remove step 4 ?

- **Distributor constraints (depending on architecture)**
5. The Retailer consolidates all Distributor responses to identify for each selected offer the fulfilment roles repartition and delegations, delayed (asynchronous) fulfilment and impossibilities.

**Retailer-side fulfilment**

> **Comment (BIGEX Olivier, 2026-05-22):**
> Feedback of other BUC: add a small disclaimer just before telling that there could be several fulfilment process. 
> In order to make the reader understand that step 9 don’t follow step 8.
6. This architecture applies when the Retailer is allowed to issue, generate, distribute or load the travel evidence using Distributor-provided rules, templates, identifiers, security material or registration rules. The Distributor provides the Retailer with the required fulfilment data.

> **Comment (BIGEX Olivier, 2026-05-22):**
> Including security data (keys, algorithms…).
7. The Retailer creates or prepares the travel evidence (TRAVEL DOCUMENT) or updates the account/medium where authorised.
Examples:
  - generate PDF / mobile ticket / barcode / QR code;
  - generate wallet pass;
  - prepare customer account update;
  - perform NFC loading if delegated;
  - create a collection reference;
  - produce a customer-facing document combining several Distributor results.
8. The Retailer sends a fulfilment notification or registration request to the Distributor (what has been done and status). The Distributor validates and records the fulfilment result. The Retailer informs the Customer with fulfilment status accordingly.

**Distributor -side fulfilment**
9. This architecture applies when the Distributor creates the travel right and travel evidence itself. The Retailer sends the fulfilment initiation request with traveller data, selected fulfilment method, delivery, account, medium data, requested document type and other constraints.

> **Comment (BIGEX Olivier, 2026-05-22):**
> Could be plural. For SNCF, the retailer can get a pdf (a link to download it)+ encoded picture of the barcode with related data.
10. The Distributor creates or confirms the travel right (FARE CONTRACT or equivalent). He may also: 
  - finalise the reservation : confirm seat or ancillary allocation;
  - register the contract;
  - generate the document;
  - update an account;
  - prepare a medium loading order.
11. The Distributor returns the fulfilment result to the Retailer with confirmations/failures, status and references. The Retailer informs the Customer with fulfilment status accordingly.

**Shared Retailer–Distributor fulfilment**
12. This architecture applies when some fulfilment tasks are performed by the Retailer and others by the Distributor. The Retailer still coordinates fulfilment process. For each selected offer, the Retailer and Distributor identify who performs each fulfilment task: 
  - travel rights creation or confirmation,
  - support generation,
  - document registration,
  - account update, 
  - media loading,
  - Customer/Traveller distribution,
  - status notification (and who is notified).
13. Each party executes its fulfilment part and returns status and references. The Retailer consolidates the final status and ensures that the Customer receives a coherent result. 

**Third-party fulfilment provider**
14. The Retailer and/or Distributor may use a third-party fulfilment provider, document generator, registrar, account provider, media provider, wallet provider or ticketing system. The BUC does not standardise the third-party internal API. It only requires that the Retailer and Distributor exchange enough data to keep traceability.
15. The third party returns the result to its requestor. The Retailer and Distributor exchange the necessary references. The Retailer consolidates the final status and ensures that the Customer receives a coherent result.

- **Fulfilment resultconsolidation**

16. The Retailer consolidates all fulfilments status (Distributors, third-parties, …) and informs the Customer. The fulfilment can be: full, partial, pending, delayed, failed, cancelled, to be revalidated, need manual action. 
Partial, failed or blocked fulfilment is not handled as a separate alternative scenario. It is handled as a fulfilment status returned by the Distributor and consolidated by the Retailer. The Distributor must return the business consequence and the next possible action for each impacted selected offer.

17. The Retailer and/or Distributor provides the support(s) or/and information.
The Customer may receive:
- downloadable document; 
- email attachment; 
- mobile ticket; 
- wallet pass; 
- barcode / QR code; 
- account update confirmation; 
- smartcard loading confirmation; 
- ticket collection reference; 
- delivery tracking information; 
- confirmation that the access right is already associated with an account or medium.

18. The Retailer and Distributor record the fulfilment references needed for later processes

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

##### Immediate fulfilment
- The Distributor confirms that the selected offer can be fulfilled immediately.
- The Retailer or Distributor creates or confirms the travel right(s).
- The support or equivalent account/media update is generated immediately.
- The Distributor returns the final fulfilment status.
- The Retailer informs the Customer that the travel right is usable.

> **Comment (BIGEX Olivier, 2026-05-22):**
> According to conditions of use (e.g. could be usable in 2 months).
##### 
##### Delayed or asynchronous fulfilment
- The Distributor accepts the fulfilment request but cannot immediately provide the final travel evidence / support.
- The Distributor returns a pending or delayed status with expected availability time and references. 
- The Retailer informs the Customer that the travel right is not yet usable or is usable only under defined conditions.
- When fulfilment becomes ready, the Distributor or fulfilment provider notifies the Retailer, or the Retailer retrieves the fulfilment result.

> **Comment (BIGEX Olivier, 2026-05-22):**
> Add here «teledistribution» process ? 
> --> «when fulfilment becomes ready or when the fulfilment has been performed by another retailer or validating provider»
> Or add a dedicated scenario ?
- The Retailer provides the final travel evidence / support, account update, media loading confirmation, collection reference or delivery information to the Customer.
- If the delayed fulfilment fails, the Distributor returns the business consequence and next possible action.
- 
**Cancellation before distribution**
- A fulfilment operation must be cancelled before the travel evidence / support is distributed or before the travel right becomes usable. The Retailer requests cancellation before distribution from the Distributor. 

> **Comment (BIGEX Olivier, 2026-05-22):**
> «immediate cancellation» ? To make the difference with the cancellation for aftersales
- The Distributor checks whether the travel right or support has already been issued, distributed, loaded, activated or made usable. The Distributor returns the resulting status: 
  - cancelled before distribution; 
  - cannot cancel because already distributed; 
  - already usable; 
  - reissue required; 
  - transfer to after-sales required. 
- The Retailer updates the Customer-facing status. 

> **Comment (BIGEX Olivier, 2026-05-22):**
> May cancel the payment process too
- If cancellation before distribution is no longer possible, the case exits BUC-E and is transferred to the relevant after-sales use case.   
**Reissue before travel without changing the selected offer**
- A travel evidence / support must be reissued before travel because of a technical issue, wrong format, delivery issue, failed loading, corrupted document or authorised correction. The Retailer requests reissue from the Distributor or performs reissue under Distributor delegation. 

> **Comment (BIGEX Olivier, 2026-05-22):**
> or the customer has lost his document
- The Distributor confirms whether reissue is allowed without changing the selected offer. 
- The previous support is marked as replaced, voided, blocked or blacklisted where required. The new support is issued and remains linked to the same: 
  - selected offer (CUSTOMER PURCHASE PACKAGE); 
  - purchase record (SALES TRANSACTION); 
  - travel right (FARE CONTRACT); 
  - original fulfilment reference. 
- The Retailer informs the Customer which support must now be used. If the reissue implies a commercial change, refund, exchange or compensation, the case exits BUC-E and is transferred to the relevant after-sales use case.

### Diagram
UML activity diagram to point out the flows between Retailer and Distributor

### Links with inputs
wiki/use-cases/inputs/EUDIT.use.cases_20260324_shared.docx wiki/use-cases/inputs/BRM_EUDIT_V2.3.xlsx

.....

To be completed
