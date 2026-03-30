#### Shared-car damage reporting after accidental impact during parking

The TRANSPORT CUSTOMER has an active shared-car rental. While manoeuvring into a parking spot at the end of the trip, the vehicle strikes a large stone, causing visible damage. Before ending the leg the TRANSPORT CUSTOMER must file a REPORT DAMAGE TO VEHICLE EVENT through the MaaS app. The report is geo- and time-stamped, supported by photographic evidence, and transmitted to the operator before the leg is formally closed. This use case covers the damage-reporting sub-flow within the vehicle-return lifecycle.

**Steps:**
1. TRANSPORT CUSTOMER has an active shared-car leg; drives to the return location and begins parking
2. Vehicle strikes a large stone; TRANSPORT CUSTOMER notices visible damage (bumper, wheel arch, or bodywork)
3. TRANSPORT CUSTOMER opens the in-app damage-report flow *before* ending the leg; system presents a structured form: damage category, location on vehicle diagram, estimated severity, and mandatory photos
4. TRANSPORT CUSTOMER submits the report; system records a REPORT DAMAGE TO VEHICLE ENTRY with timestamp, GPS coordinates, photo attachments, and links it to the active leg and SALES OFFER PACKAGE
5. Operator platform receives the event and triggers a DETECT DAMAGE TO VEHICLE EVENT; an incident reference number is returned to the MaaS app and displayed to the TRANSPORT CUSTOMER
6. System updates PARKING BAY CONDITION at the return location to reflect the damaged vehicle; the asset is flagged as requiring inspection before re-assignment
7. TRANSPORT CUSTOMER ends the leg (`{legOperation}-leg/execution` end) with the damage reference embedded in the closing event; TRAVEL DOCUMENT is annotated with the incident reference
8. System notifies TRANSPORT CUSTOMER of next steps: applicable insurance excess, inspection timeline, and any charge hold on their payment method pending operator assessment; TRANSPORT CUSTOMER receives a receipt confirming the report was filed prior to leg end
