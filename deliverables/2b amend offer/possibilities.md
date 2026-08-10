# Possible amend offer operations

* spot availability/allocate spots accomodation
* anc. availibility/allocate ancillary
* supply information ( personal, vehicle, payment method (voucher, promo), bearer of the ticket (device/token), language of the ticket ) [ per offer element ]
* remove offer element
* get additional travel right/(add travelling entity/leg) 
* get offer (on ID)

Pre-condition: An offer is searched for, and locked.   
It is now time to modify the offer, make it tailor-made for the travelling party.

## Add offer element

You can add new elements to the offer, or replace offer element (like seat allocations) for others. When adding/replacing element, 
it will be likely that there will be financial consequences.

### Seat/spot allocation

This functionality will consist of 2 parts: 
- request available seats/spots, including additional fees per seat. Optionally including a seat/spot to 'exchange'
- add the allocation of a (replacing) seat/spot

Removal of allocated spots/seats can be done using the 'remove not-mandatory offer elements'.

#### Get availability

__GET /available-spots__ or __GET /collections/available-spots/items__
will return information about the available spots, referring to the (external) deck specification. Including additional fees/reductions per seat (related to the offered product).

**Question**: should it also supply a deck-overview (to show to the customer (SVG?))

Filters: product(s), seat state(s), selected seat(s)
Policy: auto select

#### Allocate seat/spot

__POST /spot-allocation__ or __POST /processes/allocate-spot/execution__
+ offerRef
+ spotId[]

After executing this function, the complete, updated offer (list) will be returned.

**Question**: should we introduce a policy 'updatesOnly'? Default false, returning the complete offer (list). If true, only delivering the added/exchanged allocation(s), but in the same structure.

### Asset allocation

This functionality will consist of 2 parts:
- request available assets, including additional fees per asset. Optionally including an asset to 'exchange'
- add the allocation of a (replacing) asset

Removal of allocated assets can be done using the 'remove not-mandatory offer elements'.

**Remark** it is possible that in the offer is already an allocated asset, but only with a product related. 

#### Get availability

__GET /available-assets__ or __GET /collections/available-assets/items__
will return information about the available assets, referring to the (external) asset locations. Including additional fees/reductions per asset (related to the offered product).

**Question**: should we rely on e.g. GBFS or NeTEx to publish the locations of the available assets?

Filters: product(s), bbox, station(s)

#### Allocate asset

__POST /asset-allocation__ or __POST /processes/allocate-asset/execution__
+ offerRef
+ offerElementRef (travelRight)
+ assetId

After executing this function, the complete, updated offer (list) will be returned.

**Question**: should we introduce a policy 'updatesOnly'? Default false, returning the complete offer (list). If true, only delivering the added/exchanged allocation(s), but in the same structure.

### Ancillary allocation (upselling, cross-selling)

This functionality will consist of 2 parts:
- request available/applicable ancillaries, including additional fees per ancillary. Optionally including an ancillary to 'exchange'
- add the allocation of a (replacing) ancillaries

Removal of allocated ancillaries can be done using the 'remove not-mandatory offer elements'.

**Remark** it is possible that in the offer is already an allocated ancillary, but only with a product related. 

#### Get availability

__GET /available-ancillaries__ or __GET /collections/available-ancillaries/items__
will return information about the available ancillaries, referring to the (external) specifications. Including additional fees/reductions per ancillary (related to the offered product).

**Question**: should we rely on e.g. NeTEx to publish the definition of available ancillaries?

Filters: product(s)

#### Allocate ancillary

__POST /ancillary-allocation__ or __POST /processes/allocate-ancillary/execution__
+ offerRef
+ offerElementRef (travel right)
+ ancillaryId

After executing this function, the complete, updated offer (list) will be returned.

**Question**: should we introduce a policy 'updatesOnly'? Default false, returning the complete offer (list). If true, only delivering the added/exchanged allocation(s), but in the same structure.

### Add additional travelling entity (travel right)

This is a function to extend the travelling party. It must be possible to supply a travelling entity. The locked resources will remain locked during the process. It will not contain the (mandatory) information for this travelling entity. Use 'Supply information' for this.

After executing this function, the complete, updated offer (list) will be returned.

__POST /travelling-entity__ or __POST /processes/add-travelling-entity/execution__
+ offerRef
+ traveller details

### Add additional leg (travel right)

This functionality will consist of 2 parts:
- search for offers, and include the existing offer as input
- add one of the offers to the locked offer collection.

This function should also cover the use case to start with locking a product offer, and add a trip offer

__POST /search-offers__ or __POST /processes/search-offers/execution__
+ offerToExtend = offerRef
... returns offers

__POST /travel-right__ or __POST /processes/add-travel-right/execution__
+ offerRef
+ offerToExtend

## Supply information

The retailer should be able to submit information that is required, see the search-offer response.

__POST /offer-information__ or __POST /processes/add-offer-information/execution__
+ offerRef
+ travellingEntityData : [ { data-element, travellingEntityRef, value } ]
+ optionalReductionItems : [ { issuer, reductionType, code } ]

The information supplied here CANNOT be retrieved in the API, to avoid GDPR conflicts. 

## Retrieve offer details

This is a simple request to return the offer object.

__GET /offers__ or __GET /collections/offers/items__
+ offerRef

## Remove not-mandatory offer element (allocation, ancillary, travel right)

This requist should referring an offer & offer element(s) to remove. Removal of offer elements bundled by guarantees will be rejected,
or the guarantee will be removed. Mandatory offer elements cannot be removed.

__DELETE /offer-element__ or __POST /processes/remove-offer-element/execution__
+ offerRef
+ offerElementRef