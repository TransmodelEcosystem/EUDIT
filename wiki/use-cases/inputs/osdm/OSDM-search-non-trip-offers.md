## 6.5.2.1 Search for non-trip offers

The use case covers the search for offers not based on a trip.  

### Summary

Request the API provider for offers based on search parameters and/or product tag.

### Steps
- get the product tags and grouping 
- ask the customre for selection and additional information on passengers, area and travel time.
- ask the provider for offers.
    - additionally provides the fulfillment options available on the consumer side  
- display the offers for selection.

### Description

Request the API provider for offers based on search parameters and/or product tag.

In case product tags are supported the consumer requests the product tags and their grouping from the provider. The grouping provides the selection criteria the customer can use to select products for the search.

The customer provides the passengers and time range for his travel (if applicable).

The API consumer requests the offers and 

The provider returns the list of offers for the search parameters. 

Pagination is required.

The subsequent steps in the booking process are not specific to non-trip offers. 


### Use case add-ons

Allow to search promotional offers.

Allow to search for bilateral coorporate offers.

Fulfillment on chip cards might required the validation of the existing chip card content by the provider.


### Non-functional requirements

Avoid the exchange of catalogs to decople business logic between consumer and provider.

Keep business logic flexible by limiting the use case on search parameters.

Reduce costs significantly by limiting the exchange to search parameters.


### Known implementations

Sales API for Eurail and Interrail passes (OSDM)

see: https://osdm.io/spec/non-trip-based-sale/

