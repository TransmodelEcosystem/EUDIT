# Roles in CoRoM, derived from Transmodel

| name | explanation | user-representative | operator-representative | other | CoRoM Scope | EUDIT Scope |
| --- | --- | --- | --- | --- | --- | --- | 
| **FARE ORGANISATION ROLE** | Any corporate role in providing or managing transport services. | No | Yes | No | - | - | 
| FARE PRODUCT DISTRIBUTOR ROLE | Organises and accounts for wholesale distribution of FARE PRODUCTs, supplies products to retailers. | No | Yes | No | Yes | ? |
| FARE PRODUCT OWNER ROLE | Has commercial ownership of FARE PRODUCT, responsible for pricing, usage conditions, marketing, etc. | No | Yes | No | Yes. Fare rule definition is typically done before the purchase process but can be accessed during the purchase process. | ? | 
| FARE PRODUCT ISSUER ROLE | Makes portfolio of products available to distributors to be sold via retailers to customers. | No | Yes | No | Yes | ? | 
| FARE PRODUCT ATTRIBUTOR ROLE | Agent of FARE PRODUCT OWNER, intermediary with delegated responsibilities (e.g., technical implementation). | No | Yes | No | Yes | ? | 
| FARE PRODUCT RETAILER ROLE | Sells products to TRANSPORT CUSTOMERs, as authorised by FARE PRODUCT OWNER. | No | Yes | No | Yes. May manage personal details of customer (and other persons travelling) relevant for travel purchases. | ? | 
| FARE DATA COLLECTOR ROLE | Collects and forwards fare data between parties (retailer, owner, etc.). | No | Yes | Yes |Secondary. The data collection is often done after the purchase process but can be needed for after-sales action. | ? |
| FARE REGISTRAR ROLE | Coordinates issue of unique registration codes for fare management data (contracts, documents, etc.). | No | No | Yes | Yes | ? |
| FARE REGISTRAR ROLE (FARE CONTRACT & TRAVEL DOCUMENT) | Coordinates unique registration codes for FARE CONTRACTs and TRAVEL DOCUMENTs. | No | No | Yes | Yes. Subsumed within FARE REGISTRAR ROLE | ? |
| FARE SECURITY MANAGER ROLE | Establishes and coordinates security policy for fare data, certifies and audits organisations. | No | No | Yes | Yes | ? |
| **TRAVEL ORGANISATION ROLE** | Role in providing any kind of travel related service. | No | Yes | No | - | - | 
| SERVICE OPERATOR ROLE | Provides service to customer (carrier for PT, ancillary services like catering, parking, etc.). | No | Yes | No | Yes. The timetable definition is theoretically given before the purchase process except for perturbations (travel guarantees) | ? | 
| CUSTOMER SERVICE PROVIDER ROLE | Provides help line and retail support for customers (replacement of documents, installation assistance). | No | Yes | No | Yes | ? |
| TRAVEL DOCUMENT CONTROLLING ORGANISATION ROLE | Checks validity of passenger's travel documents for journey access rights. | No | Yes | No |"Secondary. The travel document can be, but rarely, controlled by retailer or customer service provider during the purchase process. | ? |
| **TRANSPORT USER ROLE** | General user role for consuming travel services, including information services. | Yes | No | No | - | - |
| PURCHASER ROLE | Party who pays for travel service for one or more TRANSPORT CUSTOMERs. | Yes | No | No | Yes | - | 
| INDIVIDUAL PURCHASER ROLE | Person purchasing FARE PRODUCT as private individual (often same as PASSENGER). | Yes | No | No | Subsumed within PURCHASER ROLE. | ? |
| CORPORATE PURCHASER ROLE | Purchases FARE PRODUCTs on behalf of corporate body (company, school, government, etc.). | Yes | No | No | Subsumed within PURCHASER ROLE. | ? |
| PASSENGER ROLE | General role of consuming a travel service. | Yes | No | No | Secondary. Access rights consumption is rarely involved in purchase process except for some aftersale actions. | ? |
| INDIVIDUAL PASSENGER ROLE | Passenger consuming travel service as single individual. | Yes | No | No | Secondary. Subsumed within PASSENGER ROLE. | ? | 
| OFFENDER ROLE | Someone attempting to travel with no or insufficient access rights. | Yes | No | No | Secondary. Subsumed within PASSENGER ROLE. | ? | 
| COMPANION ROLE | Accompanying another passenger under their ticket's access rights. | Yes | No | No | Secondary. Subsumed within PASSENGER ROLE. | ? | 
| GROUP MEMBER ROLE | Passenger travelling under access rights granted by GROUP TICKET. | Yes | No | No | Secondary. Subsumed within PASSENGER ROLE. | ? | 
| TRANSPORT CUSTOMER ROLE | Specific person/organisation involved in fare process with FARE CONTRACT. | Yes | No | No | Yes | ? |
| **ADMINISTRATIVE ORGANISATION ROLE** | Organisational role involving management or use of data. | No | No | Yes | - | - | 
| REGISTRAR ROLE | Coordinates issue of unique registration codes for organisations, products, travel documents, etc. | No | No | Yes | Subsumed within FARE REGISTRAR ROLE. | ? |
| SECURITY MANAGER ROLE | Establishes and coordinates security policy, certifies and audits organisations/applications. | No | No | Yes | Subsumed within FARE SECURITY ROLE. | ? |
| DATA COLLECTOR ROLE | Collects and forwards data between parties (retailer, owner, etc.). | No | No | Yes | Secondary. Subsumed within FARE DATA COLLECTOR ROLE. | ? | 
| **TECHNOLOGY ORGANISATION ROLE** | Provides electronic fare or information services and products. | No | No | Yes | - | - | 
| ACCOUNT PROVIDER ROLE | Provides online account for product delivery (ABT), direct relationship with customer. | No | Yes | Yes | Yes | ? | 
| MEDIUM APPLICATION PROVIDER ROLE | Supplies software application hosting TRAVEL DOCUMENT for control and validation. | No | Yes | Yes | Secondary. Application is typically pre-coded for the media before the purchase process. | ? |
| MEDIUM APPLICATION OWNER ROLE | Has commercial ownership of software MEDIA APPLICATION hosting product. | No | Yes | Yes | Secondary. Application is typically pre-coded on the media before the purchase process. | ? |
| PAYMENT PROVIDER ROLE | Channels funds from customer to retailer (EMV, ApplePay, PayPal, etc.). | No | No | Yes | Secondary. Out of the document scope | ? |
| IDENTITY PROVIDER ROLE | Supplies trusted identity token for CUSTOMER ACCOUNT, TRAVEL DOCUMENT, etc. | No | No | Yes | Yes | ? |
| MEDIA PROVIDER ROLE | Supplies media for TRAVEL DOCUMENT (smartcards, phones, paper, watches, NFC devices, etc.). | No | No | Yes | Secondary. Media are typically pre-coded and supplied before the purchase process. | ? |
