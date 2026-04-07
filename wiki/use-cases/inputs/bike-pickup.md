#### Pre-booked bike pickup with locker guidance and condition check

The TRANSPORT CUSTOMER has pre-booked a shared bike at a specific hub for a defined pickup time. On arrival at the hub they are guided to the assigned LUGGAGE LOCKER EQUIPMENT (type: bike carriage), the locker is remotely opened, and the TRANSPORT CUSTOMER must confirm the bike is in a usable state — updating the PARKING BAY STATUS — before the leg formally starts. This use case covers the critical transition between the pre-trip and in-trip phases for docked shared-mobility assets stored in individual lockers.

**Steps:**
1. TRANSPORT CUSTOMER searches for a bike at a specific hub for a given pickup time and duration; system returns available SALES OFFER PACKAGEs tied to a MONITORED VEHICLE SERVICE PARKING BAY
2. TRANSPORT CUSTOMER selects an offer and confirms purchase; system assigns a specific bike and locker (LUGGAGE LOCKER EQUIPMENT) and issues a TRAVEL DOCUMENT with the booking reference, hub address, locker identifier, and pickup deep-link
3. TRANSPORT CUSTOMER arrives at the hub; the app detects proximity via GPS geofence or QR scan at the hub entrance and activates the pickup flow
4. System retrieves the exact locker position within the hub (aisle, row, floor level) and displays step-by-step navigation to the assigned LUGGAGE LOCKER EQUIPMENT
5. System sends a remote unlock command to the locker (`{assetOperation}-asset/execution` open-locker); TRANSPORT CUSTOMER receives in-app confirmation that the locker has opened
6. TRANSPORT CUSTOMER inspects the bike (tyres, brakes, lights, saddle height) and records the outcome in the app: either confirms usable state or reports a defect with category and photo
7a. If confirmed usable: PARKING BAY STATUS is set to occupied/in-use; leg transitions to active (`{legOperation}-leg/execution` start); timer and fare calculation begin
7b. If defect reported: PARKING BAY STATUS is set to unavailable; system re-assigns an alternative bike and locker at the same hub (if available) and remotely opens the replacement locker; the TRANSPORT CUSTOMER's booking guarantee and original pickup time are preserved; if no alternative is available, a full redress is triggered
