## Use Case Overview (draft)

- **Business Use Case ID & Name:** BUC-J— Aftersales servicing and claims (non-covered by BUC-F) 
- **Goal (Objective):** Enable the Customer (or an assisted agent (Retailer)) to handle after-sales servicing cases that are not covered in BUC-F, including voucher/credit lifecycle, claims and disputes, no-show, and urban transport lifecycle operations on customer accounts, media/supports and contracts.  
- **Scope:** This BUC covers after-sales servicing actions that:  
- are triggered after purchase or before/after travel,  
- do not fit the pre-trip cancel/exchange/cross-sell flows of BUC-F,  
- require specific servicing processes (vouchers/credits, disputes/claims, no-show, security lists, lifecycle management of supports and contracts).  


## Terminology note — 
Optionnal – Business conceopts + Transmodel concepts (existintg, new, proposed) used in this BUC

## Actors & Context

- **Primary Actors:**
  - **Customer (TRANSPORT USER ROLE including TRANSPORT CUSTOMER ROLE and PURCHASER ROLE (represented by the retailer)):** can identify the relevant item(s) concerned by the request (e.g., contract identifier, travel document, voucher reference, customer account, medium/support ID).

- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)) :** captures the request, orchestrates servicing, consolidates responses and provides customer-facing information.
- 
  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)) :** validate eligibility, apply servicing rules and confirm business outcomes for their products/contracts.
- 

## Preconditions & Postconditions

- **Assumptions & Preconditions (must be true before start):**
  - The Customer can identify the relevant item(s) concerned by the request (e.g., contract identifier, travel document, voucher reference, customer account, medium/support ID).
  - The Retailer has authorised access to the relevant Distributor servicing capabilities.
- 

- **Postconditions — Success guarantees:**
  - The requested servicing action is completed or clearly rejected with reasons and next actions (refresh, provide missing data, contact responsible party).
  - Where applicable, the corresponding object status is updated (voucher balance/status, contract state, support state, customer account linkage).

- **Postconditions — Minimal guarantees:**
  - What can be tested at least at the end (when everything goes wrong ) ?

## Scenarios

### Main scenario
The main scenario of BUC-J is the selection and execution of one of the servicing use cases below.
Hand-offs (references to other BUC)
- Payment execution and payment-side constraints remain in BUC-D when a servicing case requires a new payment or a payment adjustment.
- Settlement/reconciliation details remain in BUC-K where accounting allocation is required.
- Fulfilment / issuance of updated travel documents remains in BUC-E when servicing produces new travel rights/documents.

A) Voucher / credit / residual value servicing
1. Voucher validation and eligibility check (value, status, eligible scope) before use.
2. Apply voucher as a payment instrument; handle partial applicability and per-distributor eligibility.
3. Residual value handling when voucher exceeds eligible amount:
 - issue a new/updated voucher or credit,
 - treat residual as a separate eligible item (when supported),
 - record residual as an overpayment (“trop-perçu”) for later reconciliation/crediting.
4. Voucher issuance / update / cancellation after servicing outcomes (e.g., refund-to-voucher, goodwill credit).

B) Claims, disputes, passenger rights servicing
5. Create and manage a customer claim/dispute (service incident, eligibility disagreement, “consumed vs not consumed”, amount contested).
6. Provide “who to contact / servicing responsibility” information per leg/operator/contract.
7. Support evidence collection and status tracking (decision pending, accepted, rejected, additional info required).
8. Compensation decision outcomes (operator decision), including form of compensation (voucher/credit/other).

C) No-show and post-trip entitlement consequences
9. Handle no-show status and its consequences according to after-sales conditions (fees, loss of right, partial refund eligibility).
10. Handle “release then assess after travel” outcomes (where release was done pre-trip and eligibility is evaluated post-trip).

D) Customer account servicing
11. Customer account closure request (data suppression/retention rules + suspension of associated contracts).
12. Detach / attach a medium/support to another customer account.

E) Medium / support servicing (urban transport)
13. Renewal of a medium/support (new medium issued, same data/identifiers re-associated).
14. Reconstitution after damage (restore encoded rights and associated access rights).
15. Blacklisting of a medium/support (lost/stolen/compromised).
16. Return / restitution of a medium/support and deposit handling.
17. ABT medium invalidation (security key revoked; medium cannot be used again).
18. Update encoded data on a medium/support (user type change, social status update).
19. Lock/unlock of a digital credential or application instance (as defined in your glossary).

F) Contract / access-right servicing (urban transport)
20. Contract suspension list management (temporary / definitive / periodic).
21. Regularisation of unpaid instalment and unblocking of the contract.
22. Removal from “contracts to unblock” list once the blocking cause is resolved.
23. Blacklisting of a contract/access right.
24. Terminate a direct-debit subscription contract (stop payment terms and prevent further use as per rules).

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

##### Title
1. Step 1
2. The ....

### Diagram
UML activity diagram to point out the flows between Retailer and Distributor

### Links with inputs
wiki/use-cases/inputs/EUDIT.use.cases_20260324_shared.docx wiki/use-cases/inputs/BRM_EUDIT_V2.3.xlsx

.....

To be completed
