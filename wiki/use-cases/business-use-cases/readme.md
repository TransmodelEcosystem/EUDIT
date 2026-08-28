# Place for the Business Use Cases

EUDIT high-level use cases written with UML activity diagram and Transmodel vocabulary. 
Formatting with pattern : [format definition](https://github.com/TransmodelEcosystem/EUDIT/blob/main/wiki/use-cases/business-use-cases/business_use-case_format.md) 

## EUDIT Business Use Case Categories

A. **Inspire & Plan**
- retrieving product list and product detail (fare product discovery)
- asking for product-based offers (including optional disponibility check)
- selection of offers (including pre-reservation)

**After 'the Forge', this is re-scoped to:**
* asking for product-based offers (including optional disponibility check): "search for offers"
* the product list and product details are out of scope, they can be found in NeTEx files\
* selection of offers is moved to BUC-B

B. **Shop & Price**
- manage your products (add/remove/modify your cart)
- calulate the price (final pricing)

**After 'the Forge', this is re-scoped to:**
* selection of offer, including the price
* amend your offer
    * travel rights (related to products)
    * allocations (seats/assets), the reservations before the purchase
    * ancillaries
  the price will be recalculated after each modification. These functions can also be used for cross-selling.

C. **Order & Book** *(+ first stage of reservation)*
- fix your purchase (checkout) => To move in UC B ?
- begin reservation process (seat / bike / cabin / PRM / mandatory or optional)
- acheive reservation process

**After 'the Forge', this is re-scoped to:**
* validate the offer (just before the purchase)
* the actual purchase

D. **Pay** *(CHARGING MOMENT (complete or not))*
- enter your payment method
- select your payment method
- order a payment between banks/ pay with monney
- receives a receipt / bill

**After 'the Forge', this is re-scoped to:**
* Only enlisting financial elements that have to be communicated between the Distributor and the Retailer.
* The financial clearing is a responsibility of the Retailer.

E. **Ticketing & Fulfilment** *(FARE CONTRACT creation + Travel doc distribution)*
- creation of travel access rights (contracts)
- managment of orders (create, consult, modify, delete)
- receive TRAVEL DOCUMENT(s)
- acheive reservation process => moved in BUC C

**After 'the Forge', this is re-scoped to:**
- retrieve Travel Documents (tickets), taking into account tickets distribution by retailer, or distributor

F. **Pre-trip** *(service and aftersales)*
- inform traveler
- trip/service/reservation/media modification

**After 'the Forge', this is re-scoped to:**
- (partial) cancellation
- (partial) exchange (also used for upselling)
- retrieval of a purchased package

G. **Validation – inspection / In-trip** *(services and aftersales)*
- sale/pay on tap
- inspection durng travel
- trip modification (traveler request or operator disruption)

**After 'the Forge', this is re-scoped to:**
* Leg management (start, stop etc of a leg, postpone, extend)
* Asset management (open locks, chargers, custom urban mode functions)
* Check-in (is this in scope?)
* Inspection -> retrieval of the travel document (BUC-E)
* Trip modification in respect to facilitate other legs -> Exchange (BUC-F)
  
I. **Post-trip** *(+ end of payment (CHARGING MOMENT))*
- pay your trips
- receive the final price
- automatic purchase (renew your contract)

**After 'the Forge', this is re-scoped to:**
* payment is the responsibility of the Retailer, use this BUC to check if the Retailer has all information required
* look also to BUC-D.
  
J. **After trip** *(aftersales + claim + receipt)*
- traveler request for an after-sale operation
- operator after-sale operations (refund, complaint)

**After 'the Forge', this is re-scoped to:**
- Redressing:
  * refund (= cancel, and financial part is the responsibility of the Retailer)
  * claiming (based on guarantees?)
  
K. **Settlement** *(repartition between actors)* & **Reporting**
- invoices,
- VAT reporting, clearing, reconciliation
- in EUDIT scope ?

**After 'the Forge', this is re-scoped to:**
* out of EUDIT scope, but look at BUC-D and F, all required information regarding what has to be paid, is included.

L. **Governance** *(technical ?)*
- customer authentification
- give the versions and the errors

**After 'the Forge', this is re-scoped to:**
* customer authentication can be included, when there is time left.

M. **In-trip Support**
- request support by the traveller
- get support status

N. **In-trip Notifications**
- changes of status, routes, ...
- instructions
- validation requests to the retailer

O. **Travel document operations**
- revoke
- (de)activation

P. **Asset based offer search**
- search for offers for a specific asset

Q. **Location based offer search**
- search for offers for a specific station/location
 


