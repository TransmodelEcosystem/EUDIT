# Use cases

## CoRoM

## 6.5.2.1 Choice of a solution by FARE PRODUCT catalogue consultation
The use case covers the choice of a SALES OFFER PACKAGE by a customer using a FARE PRODUCT catalogue of products set with an availability check (which may constitute a short-term pre-reservation). This use case does not involve a trip planner.  

### Summary
Using the FARE PRODUCT catalogue, the TRANSPORT CUSTOMER chooses one or more catalogue items and enters additional information to allow determining the best offer (FARE PRODUCT, SALES OFFER PACKAGE, the related PRICE and TRAVEL GUARANTEEs).

### Steps
* select / filter by user -> presents SALES OFFERS to user
* availability check (optional)
* request prices for the SALES OFFER(s): calculate the price

### Description
It is assumed that the TRANSPORT CUSTOMER knows where he/she wants to travel, and when or at which frequency; and also he/she may already have
reduction cards, etc.. 
The TRANSPORT CUSTOMER opens the FARE PRODUCT catalogue with a mobile application, web site or vending machine, or it is done by desk agent. 
The catalogue is organised to be easy to use. It can have pages organised by TRANSPORT OPERATOR; or it can present the most requested products first, pre-filtered with locally usable products, etc.. The display can also be driven by platform marketing rules or other operator priorities.
The FARE PRODUCT can be characterised in detail by USAGE PARAMETERs describing conditions of travel (e.g., are interchanges allowed, can the journey be broken, for how long is the ticket valid, etc.), as well as commercial conditions for EXCHANGING, REFUNDING, CANCELLING, RESERVING, etc.. 
Depending on the offer, these parameters may be mandatory, optional, or optional at an additional price (e.g., for differently priced LUGGAGE 
ALLOWANCEs).  
If the TRANSPORT CUSTOMER is not satisfied by the presented products, he/she can navigate through the pages or use pre-defined filters to refine his/her search, until finding a SALES OFFER PACKAGE fitting his/her requirements. The TRANSPORT CUSTOMER can be helped by detailed information on each SALES OFFER PACKAGE (network and lines, stops, connections, etc..).  
Either the SALES OFFER PACKAGE is a “simple” product (Example: a Single ticket on a bus for immediate travel) or it needs additional data to be supplied by the TRANSPORT CUSTOMER to be completely defined (for example: time of travel, class of seating, possesion of specific rail-card, etc.).  
The SALES OFFER PACKAGE may be sold with or without reservation following their definition (not needed, optional, mandatory). This process is 
described in the use case below.  
With reservation, on some platforms, the TRANSPORT CUSTOMER must start the reservation process to obtain availability and final price. The reservation can be a simple quota or a seat to select on a map for a scheduled journey. If there is no availability, the TRANSPORT CUSTOMER must change either an option (e.g., access right parameter such as comfort class) or FARE PRODUCT.  
Without a reservation or additional data, an indicative price can be given early, i.e., at the time of the product selection.   
In case of reservation, the TRANSPORT CUSTOMER obtains the price for a completely defined TRAVEL PACKAGE.  
After these steps, the TRANSPORT CUSTOMER can choose to finalise the purchase process or not. 

## 6.5.2.2 Information on and choice of a solution for a specific trip
### Summary
After having received trip proposals from the trip planner, the TRANSPORT CUSTOMER chooses one of them and requests information on the 
corresponding TRAVEL PACKAGE (FARE PRODUCTs, SALES OFFER PACKAGEs, PRICEs and TRAVEL GUARANTEEs).

### Steps
* Show trip proposals
* Consult details of a trip
* Check availability
* Calculate on secured solutions
* Calculate with basic guarantees

### Description
This use-case aims to prepare a purchasable package.  
It is assumed that possible trip options (TRIP PATTERNs) are provided by a trip planner which determines the mobility solutions for trips according to the required locations and time. The trip planner answers with one or many solutions presented as TRIP PATTERNs which, in general, comprise a sequence of the different LEGs of the trip; the related transport modes; and the points where a connection is necessary together with the corresponding timing.  
For each of the TRIP PATTERNs, the TRANSPORT CUSTOMER may request the fare corresponding to the different TRIP PATTERNs if the fare is not already provided in the delivery. The output he/she will get is a solution for the request. Depending on the operators and systems involved, a TRAVEL PACKAGE can be proposed in two ways: 
1. A solution with SALES OFFER PACKAGEs as it exists in a catalogue of pre-assigned FARE PRODUCTs, i.e., a basic proposal without checks for space availability, customer profile, …. It includes only basic, pre-planned TRAVEL GUARANTEEs. It may comprise one or several simple SALES OFFER PACKAGEs or several SALES OFFER PACKAGEs in sequence. It provides pre-planned or estimated prices.
2. The solution can be more elaborated and based on a non-exhaustive list of strategies that adapts in real-time the resulting solution to a particular context and secures the travel. The TRAVEL PACKAGE is based on an exhaustive FARE PRODUCT catalogue. It involves several checks as regards seat availability, class options, transfer options, information on travel guarantees.   


Both cases (1) and (2) include a price calculation that can adapt according to the customer profile and/or an already existing contract (information stored in the customer account).
The TRANSPORT CUSTOMER choses an offer among the TRAVEL PACKAGEs before proceeding to the purchase stage
