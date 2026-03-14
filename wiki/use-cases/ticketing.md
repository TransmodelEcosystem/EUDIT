# Ticketing Use Cases

These use cases are delivered as proprietary single REST/JSON endpoints (or XML in some cases).
They cover the full purchase lifecycle in an urban ticketing environment: timetable lookup, trip search, pricing, service selection, additional information, payment and fulfilment for simple or complex fare products.
They are also used for after-sales operations such as refunds, replacements, suspensions and blacklisting.


## 1. Reference Data

### get sale labels
Returns the names, descriptions, labels, comments of FARE PRODUCTs, SALES OFFER PACKAGEs and other purchase-process reference data (payment means, social status,...) 

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
Using the customer account (anonymous or authenticated) and pricing parameters, returns the calculated price for CUSTOMER PURCHASE PACKAGE. 

### Get payment terms and instalments details
Using the customer account and other inputs, returns the calculated payment terms and full instalment details (period, amount, whether an instalment has ended, free periods, etc.) for a SALES OFFER PACKAGE.


## 4. Purchase basket managment

### Add to basket
Adds a CUSTOMER PURCHASE PACKAGE to the basket. The first item added to a new basket creates the basket.

### Modify basket element
Updates a travel basket element (quantity, discount/reduction,..) on the same SALES OFFER PACKAGE.

### Delete basket element
Removes an element from the travel basket.

### Delete basket
Deletes logically the travel basket.


## 5. Purchase

### Add payment mean
Registers a payment mean on the customer account. (bank card, SEPA Payment Terms, ..)

### Enter a SEPA Direct Debit Mandate to complete the sale
For some fare products, a valid mandate is required to complete the purchase.
The customer enters their bank details on their customer account and uses this payment method for the purchase.

### Checkout travel basket
Checks the basket elements for consistency and locks the basket.
This is used to wait for the results of other actions (reservation, payment, etc.) before final purchase.

### Confirm purchase
Checks the basket and executes each operation described by a basket element (fare contracts and travel documents are created and fulfilled).

### Retrieve travel document
Delivers fulfilment with the purchase result (boarding-pass equivalent).
The raw media for QR-code delivery is sent upon delivery of the purchase, taking advantage of the established connection between the customer and the distributor for immediate use.

### Automatic store-value reload
When the traveller taps on validator equipment with a FARE PRODUCT of AMOUNT OF PRICE UNITS type (on boarding or at checkout on closed networks), the system calculates the trip price and decreases the traveller's amount by the price. With a specific parameter, when the amount falls below a threshold, the system automatically sells a reload for the amount. It is an automatic purchase for the traveller: they will see a sale on their customer account.


## 6. After-Sales

### Retrieve suitable aftersale operations, limited to the customer’s media and fare contracts
The list of available aftersales operations limited to those that the customer can request with their media and fare contracts.

### Customer aftersale request
The customer submits an after-sales request (refund, suspension, etc.) to the ticketing system.
An after-sales agent will manage the request in the back office.

### Agent aftersale request
The customer submits an after-sales request to an agent (refund, suspension, replacement, exchange, etc.).
An after-sales agent manages the operation directly with the customer.

## 7. Customer account managment

### consult customer account
Retrieves the list and details of FARE CONTRACTs and associated TRAVEL DOCUMENTs.

## 8. Security managment

### Mobile application secuirty key request
Manages enrolment of the mobile application.

### Mobile application secuirty key update
Renews of the key for enrollment of the mobile application.

## Summary
| Domain | Use case | Summary |
|---|---|---|
| Reference data | `get sale labels` | Provides labels and descriptive metadata for fare products, sales offer packages and purchase reference data. |
| Reference data | `get network labels` | Provides labels and descriptive metadata for stop points, networks, operators, lines and zones. |
| Passenger information | `search-non-trip-offers-media` | Lists eligible sales offer packages based on the customer’s valid media and optional filters. |
| Pricing | `get fare price` | Calculates and returns the price for a selected fare product / sales offer package for a given customer context. |
| Pricing | `get payment terms and instalments details` | Returns payment terms and detailed instalment schedule for a sales offer package. |
| Basket management | `add to basket` | Creates a basket if needed and adds a customer purchase package to it. |
| Basket management | `modify basket element` | Updates an existing basket item (quantity, reduction/discount, etc.). |
| Basket management | `delete basket element` | Removes a single item from the basket. |
| Basket management | `delete basket` | Logically deletes the entire basket. |
| Purchase | `add payment method` | Stores a payment method on the customer account. |
| Purchase | `enter SEPA Direct Debit mandate` | Captures and validates mandate/bank details when required for SEPA Direct Debit payments. |
| Purchase | `checkout travel basket` | Validates and locks the basket to prepare for downstream steps (reservation/payment) before final confirmation. |
| Purchase | `confirm purchase` | Executes the purchase, creating and fulfilling fare contracts and travel documents. |
| Purchase | `retrieve travel document` | Delivers the travel document (e.g., QR-code payload) for immediate use. |
| After-sales | `retrieve suitable after-sales operations` | Lists after-sales actions available for the customer’s media and fare contracts. |
| After-sales | `customer after-sales request` | Submits an after-sales request for back-office processing. |
| After-sales | `agent after-sales request` | Submits an after-sales request handled directly by an agent. |
| Customer account | `consult customer account` | Retrieves fare contracts and associated travel documents for the customer account. |
| Security | `mobile application security key request` | Issues an enrolment key for the mobile application. |
| Security | `mobile application security key update` | Renews the mobile application enrolment key. |

