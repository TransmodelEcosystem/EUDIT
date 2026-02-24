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

# OSDM Specific use cases

# TOMP Specific use cases

## (administration) Add a customer

## (administration) Modify a customer

## (administration) Remove/decouple a customer

## (administration) Validate a driver's license
Needed to rent a car, use a scooter, etc.

## (administration) Register/connect a traveller/customer
Needed to provide some customer details to determine if the customer is already a known customer on the operator side.

## (offering) Profide detailed PRM information (specified size of wheel chairs, oxigen tanks, ...)
Needed for special group transport

## (offering) Get damage report of an asset

## (offering) Get an overview of available assets in the future

## (offering) Get an overview of available ancillaries

## (offering) Get an overview of available assets (including seatplan) for a leg in an offer or package

## (offering) Modify start/end times & locations before purchasing

## (offering) Release a package that has been selected/modified

## (offering) Remove an offer from a package

## (purchase) Use a specific asset (bike, scooter, ...)
Needed to pick a bike from the street or use NeTEx/GBFS-based locations of assets or stations.

## (purchase) Reserve a parking spot

## (purchase) 2-phase purchase  
Needed to incorporate urban modes in multi-modal trips. This also requires a 'rollback purchase' and 'confirm purchase' functionality.

## (purchase) Extend expiry-time
Once you have purchased a package, but it's still before the confirmation, the expiry time might be too early. You can request to extend it.

## (purchase) 'Pick up the bill'
Needed to redirect the payment to a reseller, once a parking session (or charging session) has already started.

## (purchase) purchase a product (like daycard)

## (purchase) purchase a travel package, containing another travel package to exchange

## (generic) Travel package details
Get information at any time about your travel package

## (generic) Find referenced data
Get source-references to used data (NeTEx, GBFS, etc). So you can retrieve it yourself and find more details (to validate). The API should work without retrieving this data.

## (execution) ETA
Required for on-demand services, to notify the traveler that they're on their way to the pickup location.

## (execution) Unlock/close assets
Different options, like bluetooth, NFC, remote, and even manual locking.

## (execution) Open/close locker to get access to an asset

## (execution) Start/end the usage of an asset
Often includes the unlocking/locking, not always

## (execution) Pause/resume the usage of an asset

## (execution) Custom functions of an asset
Like 'open-trunk' or 'open-helmet-box'.

## (execution) Activate a product
To activate a product before it can be used (like a week-card).

## (execution) Extend the usage of an asset
E.g. extend parking session

## (execution) Postpone the usage of an asset

## (execution) Add ancillaries (like helmets) to a leg

## (execution) Swap ancillaries

## (execution) Change assigned assets (with optionally other types/products)

## (execution) Deliver proof of correct parking

## (execution) Driving support
Like:
* parking location
* no-go zones
* low-speed zones
* .. 

Could be used for publishing PT zones as well. Based on OMF's MDS.

## (execution) Swipe in/out
This creates a new leg/trip based on a product.

## (execution) See progress of a leg in execution
For on-demand services

## (execution) Retrieve a (dynamic) ticket

## (support) Request help in case of incidents (flat tires, etc)

## (support) Request help in case of accidents

## (support) Report damage

## (support) Retrieve the status of your support request

## (payment) Get an overview of items to pay

## (payment) Confirm payment

## (payment) Request a deposit

## (notification) Report status changes to user

## (notification) Send alerts to user 
Like:
* it's almost time to get back  
* you have parked at a location where it's not allowed

## (discovery) Show the implemented end-points

## (discovery) At each call, know the possible next steps

## (after-sales) Request redresses
Like (partial) refunds. Works with delivering options, claiming one of them and confirm/rollback them in a second step. This 2-phase is required in a multi-modal trip (same as the purchase procedure).

## (after-sales) Request refund of a deposit

## (after-sales) Cancel packages
Only required to fix technical problems.

## (information) Get an overview of entitlements

## (information) Get an overview of card types

## (information) Get an overview of license types

## (information) Get an overview of user profiles

## (information) Get an overview of a single fare
To prevent publishing the complete fare tables. You can anomously search based on user profile, entitlements, card-types, products and start/end locations.

# OMSA Specific use cases

# FerryGateWay Specific use cases

# BoB Specific use cases

## Validate tickets
Human: inspector
Gate: machine

# Ticketing Specific use cases
## (payment) Entering a SEPA Direct Debit Mandate to complete the sale
For some FARE PRODUCT, having a valid mandate is mandatory in order to complete the sale. The customer enters his bank coordinates on his customer account and uses this payment method for the purchase. 

## (offers) Get payment terms and instalments details
With the customer account and all new entries, the customer requests for the calculated payment terms and all the instalments details (period, amount, ended or not, free periods, etc...)

## (offers) Retrieve suitable SALES OFFER PACKAGES, limited to the customer’s valid media
The list of offers is limited to those that the customer can use with their valid media.

## (aftersale) Retrieve suitable aftersale operations, limited to the customer’s media and fare contracts
The list of available aftersalesoperations limited to those that the customer can request with their valid media and fare contracts.
