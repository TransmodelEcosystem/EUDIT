# CoRoM Use cases

![Roles](CoRoM%20-%20roles.png)

## [6.5.2.1 Choice of a solution by FARE PRODUCT catalogue consultation](6.5.2.1-catalogue-offers.md)
Using the FARE PRODUCT catalogue, the TRANSPORT CUSTOMER chooses one or more catalogue items and enters additional information to allow determining the best offer (FARE PRODUCT, SALES OFFER PACKAGE, the related PRICE and TRAVEL GUARANTEEs).

## [6.5.2.2 Information on and choice of a solution for a specific trip](6.5.2.2-trip-offers.md)
After having received trip proposals from the trip planner, the TRANSPORT CUSTOMER chooses one of them and requests information on the 
corresponding TRAVEL PACKAGE (FARE PRODUCTs, SALES OFFER PACKAGEs, PRICEs and TRAVEL GUARANTEEs).

## [6.5.2.3 Getting availability with or without reservation](6.5.2.3-getting-availability.md)
When choosing a TRAVEL PACKAGE for a specific trip, with or without making a reservation, the TRANSPORT CUSTOMER wants to be informed about the availabilities of each solution and their options before making a purchase.  
The customer may or may not place a reservation, temporarily or confirmed. An availability check may be done in advance to preplan (e.g., to check availability of product or seat) or also at the time of travel to check the current availability of assets.

## [6.5.2.4 Purchase process](6.5.2.4-purchase-process.md)
When a TRANSPORT CUSTOMER has selected one or more PASSENGER FARE OFFERs and any required choices for a TRAVEL PACKAGE, he/she proceeds to the purchase.

## [6.5.2.5 Change before the travel](6.5.2.5-change-before-travel.md)
UC5.1 “After-sale request by the traveller” Once a purchase has been made, a TRANSPORT CUSTOMER can request an after-sale action at any moment during travel, but the focus here is on the process before the travel.  
UC5.2 “Change under the responsibility of any providers involved in the trip for a through ticket (not the traveller)” Change in the previously advertised services (re-accommodation)

## [6.5.2.6 Change during the travel](6.5.2.6-change-during-travel.md)
UC6.1 “Change by the traveller” After-sale request by the traveller.  
After the trip departure, a personal event (e.g., a change of plans, sickness, a forgotten meal, missed connection) can lead the traveller to modify his/her passenger accommodation or to cancel or extend the trip or a part of the trip. 
UC6.2 “Change under the responsibility of any providers involved in the trip (not the traveller)” Disruptions (e.g., accidents) and related trip modifications made by the OPERATOR may modify the trip impacting SALES OFFER PACKAGEs, reservations, final prices, TRAVEL GUARANTEEs and 
TRAVEL DOCUMENTs.

## [6.5.2.7 Customer account consultation](6.5.2.7-customer-account-consultation.md)
A TRANSPORT CUSTOMER can consult his/her CUSTOMER ACCOUNT and perform some actions on FARE PRODUCTs-SALES OFFER PACKAGEs he/she has purchased.

# OSDM Use Cases

OSDM (Open Sales and Distribution Model) covers the full retail journey from offer search through booking, fulfillment, after-sales, and master data provision. It extends beyond the CoRoM scope into OSDM-specific areas such as graphical seat reservation, on-hold offers, the exchange flow, travel accounts, on-demand services, complaints, and API-driven webhooks.

See [osdm.md](osdm.md) for the complete list with endpoint references and uniqueness notes.

# TOMP-API Use Cases

TOMP-API (Transport Operator MaaS Platform API) targets shared-mobility and MaaS integration. In addition to standard offer/booking/after-sales flows it uniquely covers physical asset execution (lock/unlock, ETA, driving support), leg lifecycle management, driver's licence validation, deposit handling, and push notifications.

See [tomp-api.md](tomp-api.md) for the complete list with endpoint references and uniqueness notes.

# OMSA Use Cases

OMSA (Open MaaS Standard API) follows OGC API Processes conventions and covers the full mobility service lifecycle — authentication, discovery, offer search, traveller management, asset assignment, purchase, after-sales, travel documents, and payment. It is the only in-scope standard that specifies the OAuth2 token endpoint explicitly.

See [omsa.md](omsa.md) for the complete list with endpoint references and uniqueness notes.

# FerryGateway Use Cases

FerryGateway is an XML-based (request/response message pairs, not REST) standard for ferry booking. It covers sailing search, vessel timetables, route and port reference data, passenger and vehicle type catalogues, cabin and berth accommodation, ancillary services (meals, on-board), land/transfer connections, cancellation charges, invoicing, promotional codes, and QR-code travel documents.

See [ferrygateway.md](ferrygateway.md) for the complete list with message-pair references and uniqueness notes.

# BoB Use Cases

BoB (Backend of Backend) is an intentionally narrow B2B ticketing interoperability layer. It handles booking calls between a distributor and an operator backend and the exchange of signed Mobile Ticketing Blocks (MTBs) for validation. It is not a full booking API and deliberately omits trip search, offer search, seat reservation, and customer account management.

See [bob.md](bob.md) for the complete list with endpoint references, uniqueness notes, and an explicit out-of-scope section.

# Ticketing Specific use cases
## (payment) Entering a SEPA Direct Debit Mandate to complete the sale
For some FARE PRODUCT, having a valid mandate is mandatory in order to complete the sale. The customer enters his bank coordinates on his customer account and uses this payment method for the purchase. 

## (offers) Get payment terms and instalments details
With the customer account and all new entries, the customer requests for the calculated payment terms and all the instalments details (period, amount, ended or not, free periods, etc...)

## (offers) Retrieve suitable SALES OFFER PACKAGES, limited to the customer’s valid media
The list of offers is limited to those that the customer can use with their valid media. 
Detailed in : wiki/use-cases/6.5.2.1-search-non-trip-offers-media

## (purchase) Deliver fulfilment with the purchase result 
The raw media for QR code delivery is sent upon delivery of the purchase, taking advantage of the connection being established between the customer and the distributor, for immediate use of the travel ticket.  

## (aftersale) Retrieve suitable aftersale operations, limited to the customer’s media and fare contracts
The list of available aftersales operations limited to those that the customer can request with their media and fare contracts.
