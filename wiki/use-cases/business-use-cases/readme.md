# Place for the Business Use Cases

EUDIT high-level use cases written with UML activity diagram and Transmodel vocabulary. 
Formatting with pattern : [format definition](https://github.com/TransmodelEcosystem/EUDIT/blob/main/wiki/use-cases/business-use-cases/business_use-case_format.md) 

## EUDIT Business Use Case Categories

A. **Inspire & Plan**
- retrieving product list and product detail (fare product discovery)
- asking for product-based offers (including optional disponibility check)
- selection of offers (including pre-reservation)

B. **Shop & Price**
- manage your products (add/remove/modify your cart)
- calulate the price (final pricing)

C. **Order & Book** *(+ first stage of reservation)*
- fix your purchase (checkout) => To move in UC B ?
- begin reservation process (seat / bike / cabin / PRM / mandatory or optional)
- acheive reservation process
  
D. **Pay** *(CHARGING MOMENT (complete or not))*
- enter your payment method
- select your payment method
- order a payment between banks/ pay with monney
- receives a receipt / bill

E. **Ticketing & Fulfilment** *(FARE CONTRACT creation + Travel doc distribution)*
- creation of travel access rights (contracts)
- managment of orders (create, consult, modify, delete)
- receive TRAVEL DOCUMENT(s)
- acheive reservation process => moved in BUC C

F. **Pre-trip** *(service and aftersales)*
- inform traveler
- trip/service/reservation/media modification

G. **Validation – inspection / In-trip** *(services and aftersales)*
- sale/pay on tap
- inspection durng travel
- trip modification (traveler request or operator disruption)
  
I. **Post-trip** *(+ end of payment (CHARGING MOMENT))*
- pay your trips
- receive the final price
- automatic purchase (renew your contract)
- Post-payment models, such as usage-based charging, account-based travel, EMV/check-in check-out or Fairtiq-like flows, require a different process because the travel right, usage events, charging and final amount may be confirmed after travel or after validation events
  
J. **After trip** *(aftersales + claim + receipt)*
- traveler request for an after-sale operation
- operator after-sale operations (refund, complaint)
  
K. **Settlement** *(repartition between actors)* & **Reporting**
- invoices,
- VAT reporting, clearing, reconciliation
- in EUDIT scope ?

L. **Governance** *(technical ?)*
- cutomer authentification
- give the versions and the errors

> To be cross-checked with EUDIT perimeter compliance.
> Each point in one Business Use Case **on one page**


