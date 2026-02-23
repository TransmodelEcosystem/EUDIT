
# Index of User Actions

These 'user actions' are derived from the existing standards.

1.  **MMTIS / Static Values**
    *   [see entitlements](#see-entitlements)
    *   [see card types](#see-card-types)
    *   [see license types](#see-license-types)
    *   [see user profiles](#see-user-profiles)
    *   [see fares (static)](#see-fares-static)
2.  **Offers**
    *   [search offers, a-b](#search-offers-a-b): OJP like?
    *   [search offers a](#search-offers-a): here and now, is it in scope?
    *   [search offers, travel spec](#search-offers-travel-spec)
    *   [get offer details](#get-offer-details)
    *   search product offers?
3.  **Pre-sales**
    *   [select offer(s) into a package](#select-offers-into-a-package) package can also be read here as 'cart'?
    *   [release package](#release-package)
    *   [change/complete traveller' details](#changecomplete-traveller-details)
    *   [add/remove ancillaries](#addremove-ancillaries)
    *   [add/remove offers](#addremove-offers)
    *   [assign seat / assets](#assign-seat--assets)
    *   [change travel spec](#change-travel-spec) start time, end time, locations
    *   [change travel class](#change-travel-class)
4.  **Purchase**
    *   [purchase offer(s)](#purchase-offers)
    *   [purchase package](#purchase-package)
    *   [purchase product](#purchase-product)
    *   [purchase usage of an asset](#purchase-usage-of-an-asset)
    *   [automatic confirmed purchase](#automatic-confirmed-purchase)
    *   [reversable purchase](#reversable-purchase)
    *   [2-phase purchase](#2-phase-purchase)
    *   [extend purchase window](#extend-purchase-window)
    *   [get travel documents](#get-travel-documents)
5.  **After Sales**
    *   [exchange package](#exchange-package)
    *   [redress - offer](#redress---offer)
    *   [redress - claim](#redress---claim)
    *   [redress - confirm](#redress---confirm)
    *   [refund deposit](#refund-deposit)
    *   [get payment overview](#get-payment-overview)
    *   [cancel package](#cancel-package)
    *   [confirm payment](#confirm-payment)
6.  **Execution**
    *   [start leg](#start-leg)
    *   [pause leg](#pause-leg)
    *   [resume leg](#resume-leg)
    *   [end leg](#end-leg)
    *   [extend leg](#extend-leg)
    *   [postpone leg](#postpone-leg)
    *   [activate product](#activate-product)
    *   [initiate leg](#initiate-leg)
    *   swipe-in/out
    *   be-in/out
    *   swipe-in/be-out
7.  **Support**
    *   [request assistance](#request-assistance)
    *   [report incident/accident](#report-incidentaccident)
    *   [report trip issues](#report-trip-issues)
    *   [register evidence](#register-evidence)
    *   [send instruction](#send-instruction)
    *   [send warnings](#send-warnings)
    *   [get support overview](#get-support-overview)
    *   [send information (ETA etc)](#send-information-eta-etc)
8.  **Additional**
    *   [authorisation](#authorisation)
    *   [user registration](#user-registration)

---

# User Action Details

## 1. MMTIS / Static Values

### see entitlements
*   **Description:** Return the definitions of the entitlements.
*   **Pre-condition:** N/A
*   **Post-condition:** A list of valid entitlements is returned.

### see card types
*   **Description:** Checking which types of physical or digital cards (e.g., travel passes) are supported.
*   **Pre-condition:** N/A
*   **Post-condition:** Supported card types are presented to the user.

### see license types
*   **Description:** Return the definitions of (required?) license types 
*   **Pre-condition:** N/A
*   **Post-condition:** License types are presented

### see user profiles
*   **Description:** Return the definitions of the user profiles (like age-based, or PRM)
*   **Pre-condition:** N/A
*   **Post-condition:** Profile information is returned.

### see fares (static)
*   **Description:** Viewing fixed price information for specific routes or zones.
*   **Pre-condition:** A travel zone or trip pattern is supplied, maybe a user profile
*   **Post-condition:** Fares are returned

---

## 2. Offers

### search offers, a-b
*   **Description:** Searching for travel options between a specific origin (A) and destination (B).
*   **Pre-condition:** Origin and destination locations are provided, optional user (group) characteristics and preferences
*   **Post-condition:** A list of available offers is returned.

### search offers a
*   **Description:** Searching for available transport assets or offers at or around a single location (A).
*   **Pre-condition:** A location or current GPS position is known, optional user (group) characteristics and preferences
*   **Post-condition:** A list of available offers is returned.

### search offers, travel spec
*   **Description:** Searching for offers based on specific trip pattern, optional user (group) characteristics and preferences.
*   **Pre-condition:** Trip pattern, optional user (group) characteristics and preferences are supplied
*   **Post-condition:** A list of available offers is returned.

### get offer details
*   **Description:** Retrieving in-depth information about a specific selected offer.
*   **Pre-condition:** An offer has been selected from a search list.
*   **Post-condition:** Full details (e.g., conditions, guarantees, exact pricing) are shown.

---

## 3. Pre-sales

### select offer(s) into a package
*   **Description:** Combining one or more individual offers into a single travel package ('cart').
*   **Pre-condition:** One or more offers have been retrieved.
*   **Post-condition:** A pending package is created in the user's cart.

### release package
*   **Description:** Releasing the hold on a compiled package before the purchase is finalized.
*   **Pre-condition:** A package has been created, but not purchased
*   **Post-condition:** Reserved resources within the package are made available back to the system.

### change/complete traveller' details
*   **Description:** Adding or updating the personal information of the people traveling.
*   **Pre-condition:** A package or offer has been selected for purchase.
*   **Post-condition:** Personal details are validated and saved to the booking.

### add/remove ancillaries
*   **Description:** Managing extra services such as additional baggage or insurance.
*   **Pre-condition:** A primary offer or package is selected.
*   **Post-condition:** The package is updated.

### add/remove offers
*   **Description:** Modifying the contents of a package by adding or deleting specific offer components.
*   **Pre-condition:** A package is currently being edited.
*   **Post-condition:** The package reflects the new selection of offers.

### assign seat / assets
*   **Description:** Selecting a specific seat or a specific vehicle asset for the trip.
*   **Pre-condition:** The selected transport mode supports reservations.
*   **Post-condition:** A specific asset is reserved for the user.

### change travel spec
*   **Description:** Updating the parameters of the trip during the pre-sales phase.
*   **Pre-condition:** Initial travel specs were entered.
*   **Post-condition:** The system updates search results based on the new specs.

### change travel class
*   **Description:** Adjusting the level of service (e.g., 2nd class to 1st class).
*   **Pre-condition:** The selected offer provides multiple classes.
*   **Post-condition:** The price and offer details are updated to the new class.

---

## 4. Purchase

### purchase offer(s)
*   **Description:** Finalizing the payment for individual travel offers.
*   **Pre-condition:** Offers are selected and payment details are provided.
*   **Post-condition:** The user receives a confirmation of purchase.

### purchase package
*   **Description:** Executing the final transaction for a full bundle of services.
*   **Pre-condition:** A package has been successfully compiled.
*   **Post-condition:** All items in the package are paid for and confirmed.

### purchase product
*   **Description:** Buying a specific product like a monthly subscription or a day pass.
*   **Pre-condition:** A product has been selected.
*   **Post-condition:** The product is activated or linked to the user's account.

### purchase usage of an asset
*   **Description:** Paying for the actual time or distance used with a vehicle (often post-trip).
*   **Pre-condition:** Usage of the asset has concluded.
*   **Post-condition:** Payment is processed based on actual consumption.

### automatic confirmed purchase
*   **Description:** A purchase that is automatically finalized based on pre-set user permissions.
*   **Pre-condition:** User has enabled automatic confirmation for specific services.
*   **Post-condition:** Transaction is completed without manual user intervention at each step.

### reversable purchase
*   **Description:** A purchase that allows for a "cooling off" period or cancellation with a refund.
*   **Pre-condition:** The purchase is made under terms that allow reversal.
*   **Post-condition:** The purchase is successfully voided and funds are returned.

### 2-phase purchase
*   **Description:** A transaction divided into an authorization phase and a final settlement phase.
*   **Pre-condition:** Payment method supports two-phase processing.
*   **Post-condition:** Funds are reserved initially and captured upon completion.

### extend purchase window
*   **Description:** Increasing the time allowed to complete a purchase before a reservation expires.
*   **Pre-condition:** An active reservation is nearing its expiration.
*   **Post-condition:** The expiration deadline is moved further into the future.

### get travel documents
*   **Description:** Downloading or receiving tickets, QR codes, or other necessary travel papers.
*   **Pre-condition:** The purchase is confirmed and payment is verified.
*   **Post-condition:** User has digital or physical access to travel documents.

---

## 5. After Sales

### exchange package
*   **Description:** Swapping an existing purchased package for a different one.
*   **Pre-condition:** A package has been purchased and the exchange policy allows it.
*   **Post-condition:** The old package is voided and a new one is issued.

### redress - offer
*   **Description:** Requesting a correction or compensation regarding a specific offer's delivery.
*   **Pre-condition:** The service provided did not match the offer purchased.
*   **Post-condition:** A request for redress is logged.

### redress - claim
*   **Description:** Filing a formal claim for financial compensation (e.g., for delays).
*   **Pre-condition:** A qualifying event (like a significant delay) has occurred.
*   **Post-condition:** The claim is submitted for provider review.

### redress - confirm
*   **Description:** Confirming the outcome or acceptance of a redress request.
*   **Pre-condition:** A redress request has been processed by the provider.
*   **Post-condition:** User is notified of the final resolution.

### refund deposit
*   **Description:** Returning a security deposit to the user.
*   **Pre-condition:** An asset has been returned without damage.
*   **Post-condition:** The deposit amount is credited back to the user.

### get payment overview
*   **Description:** Accessing a history of all financial transactions and receipts.
*   **Pre-condition:** Transactions have been recorded in the user account.
*   **Post-condition:** A detailed financial statement is displayed.

### cancel package
*   **Description:** Formally cancelling a purchased travel package.
*   **Pre-condition:** The package is active and within the allowed cancellation period.
*   **Post-condition:** The booking is terminated and refund processing begins if applicable.

### confirm payment
*   **Description:** Manually verifying that a payment has been made (e.g., through a third-party app).
*   **Pre-condition:** A payment request is pending.
*   **Post-condition:** The system marks the transaction as paid.

---

## 6. Execution

### start leg
*   **Description:** Officially beginning a specific segment of the journey.
*   **Pre-condition:** User is at the starting point with valid authorization.
*   **Post-condition:** The leg status is changed to 'active'.

### pause leg
*   **Description:** Temporarily stopping the timer or usage of a leg (common in shared mobility).
*   **Pre-condition:** A leg is currently active.
*   **Post-condition:** The asset is locked but remains reserved for the user.

### resume leg
*   **Description:** Continuing a previously paused travel segment.
*   **Pre-condition:** A leg is currently in a paused state.
*   **Post-condition:** The leg returns to an active status.

### end leg
*   **Description:** Finishing a specific segment of the trip at the destination.
*   **Pre-condition:** User has arrived at the intended end point.
*   **Post-condition:** The leg is closed and final costs are calculated.

### extend leg
*   **Description:** Increasing the planned duration or distance of an active leg.
*   **Pre-condition:** A leg is active and the system allows extensions.
*   **Post-condition:** The end-time or distance limit is updated.

### postpone leg
*   **Description:** Delaying the start time of a planned travel segment.
*   **Pre-condition:** The leg has not yet started.
*   **Post-condition:** The scheduled start time is moved to a later slot.

### activate product
*   **Description:** Triggering the validity of a product (like a 24-hour pass) for immediate use.
*   **Pre-condition:** A product is purchased but currently inactive.
*   **Post-condition:** The product's validity period begins.

### initiate leg
*   **Description:** Create a new leg using a purchased product (swipe-in, check-in)
*   **Pre-condition:** User is within proximity of the asset.
*   **Post-condition:** The asset is ready for physical use.

---

## 7. Support

### request assistance
*   **Description:** Asking for help, often related to physical accessibility needs.
*   **Pre-condition:** A booking exists or is being planned.
*   **Post-condition:** Support staff are notified of the request.

### report incident/accident
*   **Description:** Notifying the provider of a collision or emergency.
*   **Pre-condition:** An incident has occurred during a trip or asset usage.
*   **Post-condition:** Incident report is filed and emergency protocols are triggered.

### report trip issues
*   **Description:** Reporting non-emergency problems like delays or vehicle cleanliness.
*   **Pre-condition:** User is experiencing or has experienced a service issue.
*   **Post-condition:** Feedback is submitted to the operator.

### register evidence
*   **Description:** Documenting the state of an asset (e.g., taking a photo of existing damage).
*   **Pre-condition:** User is at the start or end of a rental.
*   **Post-condition:** Proof is uploaded and linked to the transaction.

### send instruction
*   **Description:** System or support sends specific guidance to the user.
*   **Pre-condition:** A specific situation requires user action.
*   **Post-condition:** User receives actionable instructions via the app.

### send warnings
*   **Description:** Issuing alerts regarding safety or service disruptions.
*   **Pre-condition:** A disruption is detected in the travel network.
*   **Post-condition:** The user is alerted of the potential impact.

### get support overview
*   **Description:** Viewing the status of all current and past support requests.
*   **Pre-condition:** One or more support tickets have been created.
*   **Post-condition:** A list of tickets and their statuses is shown.

### send information (ETA etc)
*   **Description:** Sharing real-time updates like Estimated Time of Arrival with others.
*   **Pre-condition:** A trip leg is currently in progress.
*   **Post-condition:** Receivers are updated with the latest trip progress.

---

## 8. Additional

### authorisation
*   **Description:** The process of validating a user's permission to perform a specific function.
*   **Pre-condition:** A request is made that requires verified access.
*   **Post-condition:** Permission is either granted or denied based on user credentials.

### user registration
*   **Description:** The process to add new (or connect to existing) users
*   **Pre-condition:** A request is made to register a user.
*   **Post-condition:** A user is registered at the TO side (or the user is mapped to an external user at the reseller side)
