# Ticketing Use Cases

This use cases are developped by proprietary as single REST/JSON endpoints (or XML in some cases).
They cover the full purchase lifecycle in urban ticketing environnement: timetable lookup, trip search, pricing, service selection, additional information, payment and fulfilment with simple or complex fare products. 
It is also used for aftersales operations : refund, replacement, suspension, blacklist,...


## 1. Reference Data

### get sale labels
Returns the names, descriptions, labels, comments of FARE PRODUCTs, SALES OFFER PACKAGEs and other purchase process data (payment means, social status,...) 

### get network labels
Returns the names, descriptions, labels, comments of STOP POINTs, NETWORKs, OPERATORs, LINEs, ZONEs.


## 2. Passenger information

### search-non-trip-offers-media
Retrieve suitable SALES OFFER PACKAGES, limited to the customer’s valid media.
Many other filters can be used : product type, network, stop point, fare zone, ...
The list of offers is limited to those that the customer can use with their valid media. 
Detailed in : wiki/use-cases/6.5.2.1-search-non-trip-offers-media


## 3. Get the real price

### Get fare price
With the customer account (anonymous or connected) and parameters, the 

### Get payment terms and instalments details
With the customer account and other entries, the customer requests for the calculated payment terms and all the instalments details (period, amount, ended or not, free periods, etc...) for a SALES OFFER PACKAGE.


## 4. Purchase basket managment

### Add to basket
Request to add a CUSTOMER PURCHASE PACKAGE to the basket. The first element of a new basket creates the basket.

### Modify basket element
Update one travel basket element (quantity, reduction,..) on the same SALES OFFER PACKAGE.

### Delete basket element
Remove an element from the travel basket.

### Delete basket
Delete logically the travel basket.


## 5. Purchase

### Add payment mean
The customer can register a payment mean to his customer account. (bankCard, PaymentTerms SEPA, ..)

### Entering a SEPA Direct Debit Mandate to complete the sale
For some FARE PRODUCT, having a valid mandate is mandatory in order to complete the sale. The customer enters his bank coordinates on his customer account and uses this payment method for the purchase.

### Checkout travel basket
Check the elements in travel basket (consistency) and lock the basket 
It is used to wait for other actions results (reservation, payment, etc ...) before final purchase.

### Confirm purchase
The basket is checked and each operation described by a travel basket element is executed (FARE CONTRACTs and TRAVEL DOCUMENTs are created and fulfilled)

### Retrieve travel document
Deliver fulfilment with the purchase result (boarding pass equivalent) 
The raw media for QR code delivery is sent upon delivery of the purchase, taking advantage of the connection being established between the customer and the distributor, for immediate use of the travel ticket.


## 6. After-Sales

### Retrieve suitable aftersale operations, limited to the customer’s media and fare contracts
The list of available aftersales operations limited to those that the customer can request with their media and fare contracts.

### Customer aftersale request
The cutomer submits an after-sale request (refund, suspension) to the ticketing system. An after-sale agent will manage the request in bakc-office.

### Agent aftersale request
The cutomer submits an after-sale request to an agent (refund, suspension, replacement, exchange). An after-sale agent manages directely with the customer the after-sale operation.


## 7. Customer account managment

### consult customer account
Retrieves the list and details of FARE CONTRACTs and associated TRAVEL DOCUMENTs.

## 8. Security managment

### Mobile application secuirty key request
The enrollment of the mobile application.

### Mobile application secuirty key update
Renew of the key for enrollment of the mobile application.

## Summary

