**accommodation** : A combination of accommodation characteristics available on a service, e.g. "First Class Couchette with shower and 2 bunks".  
**amountOfMoney** : an amount of money, usable in fares, fare calculations or in extra costs.  
**asset** : the asset that can by applied to execute a leg.  
**cancellationParameter** : conditions regarding cancellation of a purchased package  
**card** : Any kind of card that isn't a license, only provide the cards that are required  
**classOfUse** : A classification of fare and other service classes by category of user entitled to use them.  
**country** : two-letter country codes according to ISO 3166-1  
**currencyCode** : ISO 4217 currency code  
**customProperties** : dictionary for extra fields (bilatural agreements)  
**date** : https://www.rfc-editor.org/rfc/rfc3339#section-5.6, full-date (2019-10-12)  
**dateTime** : https://www.rfc-editor.org/rfc/rfc3339#section-5.6, date-time (2019-10-12T07:20:50.52Z)  
**error** : JSON schema for exceptions based on RFC 7807  
**float** : the travelled distance. Only if applicable.  
**geojsonLine** : An array of WGS84 coordinate pairs  
**geojsonMultiPolygon** : geojson representation of a multi polygon. See also https://geojson.org/geojson-spec.html#multipolygon  
**geojsonPoint** : Geojson Coordinate  
**geojsonPolygon** : geojson representation of a polygon. First and last point must be equal. See also https://geojson.org/geojson-spec.html#polygon and example https://geojson.org/geojson-spec.html#id4. The order should be lon, lat [[[lon1, lat1], [lon2,lat2], [lon3,lat3], [lon1,lat1]]], the first point should match the last point.  
**httpDate** : A HTTP date string  
**legState** : status of a leg _NOT_STARTED_ the leg is not started, initial state _PREPARING_ the _PREPARE_ operation has been received _PREPARED_ the leg is ready to use _IN_USE_ the travelers are on their way _PAUSED_ the asset is paused _ENDED_ the travelers have arrived at their destination _ISSUE_REPORTED_ due to an issue, there is (temporarily) no progress to report, when the issue isn't solved, this is a final state _CANCELLED_ the leg has been cancelled, before execution _ABENDED_ the leg is abnormally ended (e.g. due to an issue)  
**license** : driver or usage license for a specific user. Contains the number and the assetType you're allowed to operate (e.g. driver license for CAR)  
**longInt** : long number, for distances etc. (>1.000)  
**longString** : long string, memos etc (length 0-10.000)  
**mode** : These classes are taken from the NeTeX standard, but ALL and UNKNOWN are removed. On the other hand OTHER and PARKING are added.  
**normalInt** : default length for an integer (0-1000)  
**normalString** : default string, full names etc (length 0-200)  
**onboardStay** : Permission to board early before the journey or stay on board after the journey.  
**packageStatus** : The life-cycle state of the package (from NEW to ENDED) _OFFERED_ the package is offered _SELECTED_ the package is selected to modify _PENDING_ the purchase of the package is not confirmed (the end user has shown intentions to purchase this offer), must be finalized with the package-confirm operation _CONFIRMED_ a finalized package ('purchased'). Both parties agreed to deliver services in return of payment _CANCELLED_ the package is cancelled after it is purchased. The agreement will specify whether there is a refund, or under which conditions _EXPIRED_ the MP didn't respond on time, the package offer has been expired _STARTED_ the package is started, the trip execution module is needed now to manage the execution of the package _ENDED_ the package has ended, the trip has been executed _RELEASED_ for internal archiving, the package has not been purchased.  
**paymentCategory** : The category of the journalled item _ALL_ - for filtering purposes only TO initiated payments To request these payments, use the notifications, send a notification containing the payment confirmation. _DEPOSIT_ - a deposit, to refund, use _REFUND_ _DAMAGE_ - extra costs that must be paid by the MP due to damage to the asset or ancillaries _LOSS_ - extra costs that must be paid by the MP due to loss of asset or ancillaries _STOLEN_ - the asset (and ancillaries) are stolen and should be paid for _EXTRA_USAGE_ - the asset is paid for in advance, additional usage must be paid for (can also be a refund when used less! The amount should be negative in that case) _FINE_ - a fine that arrived later on _OTHER_ASSET_USED_ - additional costs for a replaced asset _FARE_ - the normal costs of the purchased and executed leg(s) _OTHER_ - unspecified _CREDIT_ - generic CREDIT, e.g. for kick-backs _VOUCHER_ - part of the fare that is covered by a voucher (no need to pay) _REFUND_ - refund of the deposit or upfront paid fare _REBATE_ - (partial) rebate of the fare _REIMBURSEMENT_ - reimbursement of the fare  
**postalAddress** : address parts, where addressLine1 and 2 should contain the complete address, matches Content-Language  
**purchaseParameter** : specify required information to complete a purchase  
**shortInt** : a bit short integer (0-100)  
**shortString** : short string, display names (length 0-75)  
**subscriber** : Optional URIs for callbacks for this job. Support for this parameter is not required and the parameter may be removed from the API definition, if conformance class **'callback'** is not listed in the conformance declaration under `/conformance`.  
**tinyInt** : for really small numbers (0-10)  
**tinyString** : real short string, codes (length 0-10)  
**typeOfTravelDocument** : how this type is implemented?  
**url** : valid URL  
**uuid** : https://en.wikipedia.org/wiki/Universally_unique_identifier see also https://www.ietf.org/rfc/rfc4122.txt (ae76f51c-a1a6-46af-b9ab-8233564adcae)  
