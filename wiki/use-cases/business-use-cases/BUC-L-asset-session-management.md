## Use Case Overview (draft)

- **Business Use Case ID & Name:** BUC-L — Manage the usage of an on-street or shared asset (start, pause, resume, stop, extend, postpone)
- **Goal (Objective):** Enable the Traveller to control the full lifecycle of an active or scheduled on-street or shared asset usage session — from activation through all in-session operations (pause, resume, extend, postpone) to final termination — while keeping the Traveller informed of elapsed time, accrued cost, and applicable constraints at every step.
- **Scope:**  
  - Activation of the usage session (start)
  - In-session operations: pause, resume, extend, postpone
  - Termination of the usage session (stop / return the asset)
  - Handling asset operations, like (un)lock the asset, open/close a locker, operate a pedestrian side door (parking garage), open a helmet box
  - give instructions to the traveller
  
## Terminology note —

**Usage session**: the bounded, time-stamped period during which the Traveller actively uses a specific asset, from activation (START) to termination (STOP). Intermediate states (PAUSED, EXTENDED) are sub-states of an active session. 

**Session state**: the current operational status of a usage session. Values relevant to this BUC: PREPARING (created, not yet started), ACTIVE (started, asset in use), PAUSED (temporarily suspended by the Traveller), ENDED (terminated, asset returned), CANCELLED (abandoned before start). Corresponds to VEHICLE RENTAL STATUS or PARKING RENTAL STATUS.

**Pause**: a Traveller-initiated temporary suspension of an active session during which the asset remains reserved for the Traveller but active usage (movement, charging) is interrupted. The asset is not released for other users. Chargeable conditions during pause depend on the Distributor's fare rules (e.g. a reduced pause rate may apply).

**Resume**: the Traveller-initiated transition from PAUSED back to ACTIVE, restarting the chargeable usage period under the original or updated fare conditions.

**Extend**: an in-session purchase of additional usage time, applied to a session that is still ACTIVE or PAUSED, before the originally scheduled end time is reached. Corresponds to an additional PARKING CHARGE BAND or TIME INTERVAL applied to the existing FARE CONTRACT ENTRY. Optionally, the requested usage time can be negative, to reduce the usage time.

**Postpone**: a Traveller-initiated shift of the scheduled start time of the session to a later point. The duration stays the same.

**Stop / End**: the Traveller-initiated or system-initiated termination of the session. The asset is released and returned to available status. The final fare is calculated from actual usage (duration, distance, events).

**Asset operation**: a command sent from the Retailer to the Distributor to physically control the asset state: unlock, lock, ring, open-locker, etc. 

**Leg operation**: a command sent from the Retailer to the Distributor to control the session state: start, end, pause, resume. Often, the leg operations trigger asset operations, and in that case, there is no need to explicitly execute the asset operations.

## Actors & Context

- **Primary Actors:**
  - **Traveller (INDIVIDUAL TRAVELLER):** the person actively using the asset. The Traveller initiates and controls session operations (start, pause, resume, extend, postpone, stop) via a Retailer app or channel.

- **Supporting Actors / Stakeholders:**
  - **Retailer (FARE PRODUCT RETAILER ROLE (API consumer)):** mediates all session operations between the Traveller and the Distributor. Sends asset and leg operation commands, monitors session state, delivers real-time feedback to the Traveller.

  - **Distributor (FARE PRODUCT DISTRIBUTOR ROLE (API provider)):** physically controls the asset (unlock, lock, etc) and manages the session state and fare accumulation. Returns updated state, elapsed cost, constraint zone alerts, and termination confirmation to the Retailer. May correspond to a TOMP-API TO or OMSA operator, an APDS parking operator, or an OCPI charge point operator.

- **Assumptions (context at start):**
  - The Traveller has a confirmed Traveller PURCHASE PACKAGE for an asset fare offer (output of the companion BUC K — Asset Based Offer Search or BUC C).
  - The Retailer has a valid session context (asset identifier, Distributor endpoint, booking reference, ..).

## Preconditions & Postconditions

- **Preconditions (must be true before start):**
  - A Traveller PURCHASE PACKAGE for the chosen asset fare offer exists and is in CONFIRMED state (from the companion BUC K — Asset Fare Search, or from a prior booking via BUC C).
  - The asset is available and accessible at the Traveller's location (optionally confirmed by proximity check, QR/NFC, or GPS geofence).
  
- **Postconditions — Success guarantees:**
  - The session has been terminated (state: ENDED); the asset has been returned and released to available status (AVAILABILITY CONDITION updated).
  - The final fare has been calculated from actual usage (JOURNAL ENTRY / FARE CONTRACT ENTRY) and presented to the Traveller as a receipt including: total duration, total distance where applicable, pricing breakdown (unlock fee, per-unit charges, pause charges if applicable, extension charges), and final total (PRICE), see BUC-D.
  - Any mandatory end-of-session conditions have been fulfilled (geo-validated parking position, photo proof where required, asset condition report).

- **Postconditions — Minimal guarantees:**
  - If the session cannot be started or is interrupted by a system failure, the Traveller receives a clear status message, the asset is not charged for time the Traveller could not use it, and the Retailer provides a recovery path (retry, alternative asset, or cancellation with no charge).
  - If the session ends abnormally (connectivity loss, asset malfunction), the Distributor applies the REDRESSES of the broken GUARANTEES, based on available telemetry or last-known state, and informs the Traveller.

## Scenarios

### Main scenario

1. The Traveller has selected and confirmed a fare offer for a specific asset (output of the companion BUC K — Asset Fare Search). The session is in CONFIRMED state. The Traveller is at or near the asset location.

- **Start**

2. The Traveller requests activation of the session (e.g. taps "Start" or "Unlock" in the Retailer app, or scans the QR code on the asset). The Retailer verifies proximity (GPS geofence, QR/NFC confirmation) and sends the initial command to the Distributor (`{legOperation}` start / `{assetOperation}` unlock).
This command is specified in the output of the BUC K (or C). The session (LEG) will be in READY state.

3. Before the asset is physically unlocked, the Distributor (via the Retailer) may require the Traveller to acknowledge mandatory pre-conditions: safety instructions, usage rules, active MOBILITY SERVICE CONSTRAINT ZONEs in the area, and condition confirmation for the specific asset. The Traveller confirms each required item; the acknowledgement is timestamped and stored against the session (SALES NOTICE ASSIGNMENT). If confirmation is not completed within a defined timeout, the activation is cancelled and the session remains in READY state.

4. The Distributor unlocks or activates the asset and transitions the session to ACTIVE state (VEHICLE RENTAL STATUS / PARKING RENTAL STATUS: active). The Distributor returns the session start confirmation to the Retailer.

5. The Retailer presents the active session status to the Traveller.

- **Pause**

6. The Traveller requests a pause of the active session (e.g. stops the bike to enter a shop, parks the scooter temporarily). The Retailer sends the pause command to the Distributor (`{legOperation}` pause).

7. The Distributor transitions the session to PAUSED state. The asset may be remotely locked or remain in the Traveller's custody depending on the asset type and operator rules. The Distributor returns the pause confirmation. The Retailer confirms to the Traveller.

- **Resume**

8. The Traveller requests resumption of the paused session. The Retailer sends the resume command to the Distributor (`{legOperation}` resume).

9. The Distributor transitions the session back to ACTIVE state and returns the resume confirmation. The Retailer presents the updated session status to the Traveller.

- **Extend**

10. While the session is ACTIVE or PAUSED and the scheduled end time is approaching (or has been reached for time-limited sessions), the Traveller requests an extension. The Retailer requests from the Distributor the available extension options for the current session (additional time blocks, new maximum duration, applicable rate for the extension period).

11. The Distributor confirms the extension when it is possible/allowed. If it is not possible, the Distributor sends a clear message to the Retailer. The Retailer presents to the Traveller.

- **Stop**

15. The Traveller requests termination of the session (e.g. taps "End" or "Return"). The Retailer initiates the end-of-session flow.

16. Where required by the Distributor's rules, the Traveller must fulfil end-of-session conditions before the session can be formally closed:
  - **Geo-validation**: the Retailer verifies (via GPS) that the Traveller's position is within a valid parking or return zone (MOBILITY SERVICE CONSTRAINT ZONE: designated parking area). If not, the app indicates the nearest valid zone and prevents session closure until the asset is in a valid position.
  - **Photo proof**: where required, the Traveller takes a geo- and time-stamped photo of the returned asset. The photo is attached to the end event.
  - **Condition report**: the Traveller reports the end-of-session asset condition (usable / defect; if defect: category and photo). The Distributor records this against the asset (PARKING BAY STATUS / VEHICLE STATE updated).

17. All end-of-session conditions are send along to the Distributor by the Retailer; the stop command (`{legOperation}` end, with geo-coordinates, photo payload, and condition report where applicable). The Distributor locks or deactivates the asset, transitions the session to ENDED state, and releases the asset.

18. The Distributor calculates the final fare from actual usage data (start and end timestamps, pause intervals, extension charges, distance where applicable) and returns the final fare breakdown to the Retailer (JOURNAL ENTRY / FARE CONTRACT ENTRY), see BUC D. The Retailer presents the receipt to the Traveller, including: total session duration, total distance where applicable, itemised pricing breakdown (unlock fee, per-unit charges, pause charges, extension charges, ...), and final total (PRICE). Payment is finalised (handled in BUC-D or via the pre-authorised account).

### Alternatives scenarios
Alternative scenarios **fully compatible** with the main scenario; using shortcuts or very detailed specific points of the main scenario.

##### Postpone 

1. The Traveller requests postponement of the session's scheduled start time without purchasing additional time. The Retailer sends the postpone request to the Distributor with the requested new start time.

2. The Distributor checks availability and fare conditions for the requested postponement (no conflicting reservation on the asset or bay, maximum session duration not exceeded, ..). If approved, the Distributor returns the changed PACKAGE. The Retailer confirms the postponement to the Traveller and presents the updated session status.

##### System-initiated end (session timeout or maximum duration reached)
1. The session reaches the maximum permitted duration without a Traveller-initiated stop, or a scheduled automatic end time is reached.
2. The Distributor generates an imminent-expiry notification (e.g. T−10 minutes) and sends it to the Retailer, which delivers it to the Traveller as a push notification with a one-tap extend option.
3. If the Traveller does not extend and does not stop the session before the deadline, the Distributor auto-terminates the session, optionally locks the asset, and calculates the final fare (with any applicable overstay charge) OR charges the Traveller with additional fees. The Retailer informs the Traveller of the automatic termination/additional pricing.

##### Asset malfunction or connectivity loss during session
1. During an active session, the asset becomes unavailable (mechanical failure, battery depletion, connectivity loss) or the Retailer loses contact with the Distributor.
2. When the Retailer is able to address the problem, it should, otherwise 
3. The Traveller should be redirected to the Distributor, creating a Support request (BUC O).

##### Cancelled session before start
1. The Traveller has a session in NOT_STARTED state and decides not to use the asset.
2. The Retailer sends a cancellation request to the Distributor. The Distributor applies the applicable cancellation conditions (USAGE PARAMETER: CANCELLING): no charge if cancelled within the free-cancellation window, or a cancellation fee if outside it.
3. The Distributor confirms the cancellation, releases the asset to available status, and returns the applicable charge (zero or cancellation fee). The Retailer informs the Traveller and processes any charge or refund (BUC-D / BUC-J).

### Diagram
TBD

### Links with inputs
wiki/use-cases/inputs/shared-mobility-use-cases.md
wiki/use-cases/inputs/on-street-parking-session.md
wiki/use-cases/inputs/Shared-scooter.md
wiki/use-cases/inputs/bike-pickup.md
wiki/use-cases/inputs/tomp-api.md
wiki/use-cases/inputs/omsa.md
