**Train ride followed by long-distance bus ride - two-phase commit and redress**

The TRANSPORT CUSTOMER needs a train ticket and a bus ticket to go from A to Z 

Steps:

1. TRANSPORT CUSTOMER searches for an itinerary from A to Z on the RETAILER app or website, specifies the trip: destination, latest arrival 14:00 tomorrow.

2. RETAILER app backoffice system uses OJP to find possible combinations. 
RETAILER system finds enough information in the OJP to conclude that it is possible to combine a long distance bus ride from DISTRIBUTOR/aggregator D1 with a train ride from DISTRIBUTOR D2 consisting of two separate RIDE LEGs.

3. RETAILER contacts D1 using the EUDIT API and asks for offers for relevant RIDE LEG(s) based on OJP.  The TRAVELER(s) is/are presented as an/several anonymous passengers with listed entitlements and age.

4.  RETAILER contacts D2 using the EUDIT API and asks for offers for relevant RIDE LEG(s) based on OJP.  The TRANSPORT CUSTOMER is presented as an anonymous passenger with listed entitlements and age.

5. D1 and D2 responds through the EUDIT API with offers.  There may be multiple options.

6. RETAILER presents one or several combined offers to the TRANSPORT CUSTOMER who accepts one of the combinations.
7. RETAILER reserves the selected offers towards D1 and D2 respectively through the EUDIT API 

8. RETAILER request SPOT AVAILABILTY  for any needed SPOT RESERVATIONS ( seats, beths, cabins, pet cage, brought along vehicle etc) and ancillary products  (meals etc) towards D1 and D2 respectively through the EUDIT API 

9. RETAILER presents the accommodation/ancillary options to the TRANSPORT CUSTOMER who indicates preferred options.

10. RETAILER adds SPOT RESERVATIONs and ancillary products to the respective RIDE LEGs towards D1 and D2 respectively through the EUDIT API 

11. RETAILER provides any additional required passenger details to D2 and/or D1 through the EUDIT API 

12. RETAILER starts payment transaction ( or user account process)

13. RETAILER starts a two-phase commit purchase towards D1 and D2 through the EUDIT API 

14. Immediately after confirmation of stage one from both D1 and D2 through the EUDIT API, the purchase is confirmed  towards D1 and D2 through the EUDIT API and D1/D2 respectively returns a token that allows the RETAILER, in a further FULFILMENT step, to get the actual TRAVEL DOCUMENT(alternatively FULFILMENT/provision of TRAVEL DOCUMENT is immediate). After that the RETAILER can finalize the payment process. (Payment process is out of scope for EUDIT, and  I am also not clear on if the FULFILMENT process itself is in scope of EUDIT)


Aftermath:

TRANSPORT CUSTOMER receives by email (or on the RETAILER app) a TRAVEL DOCUMENT or two TRAVEL DOCUMENTs (barcodes)
TRANSPORT CUSTOMER boards the first train with the TRAVEL DOCUMENT in the indicated coach 
The first train is so delayed that the TRANSPORT CUSTOMER arrives so late at the first trains destination that the second train has already left.

Now a REDRESS-process starts.  The EUDIT API should have transmitted enough details of involved DATED VEHICLE JOURNEYs, who to contact for REDRESS management , what TRAVEL GUARANTEEs that apply for the different parts or the complete A-Z trip etcetera. 

It should be possible to automate the REDRESS process using EUDIT API information in combination with NeTEx and SIRI information, thus identifiers of STOP PLACEs  and DATED VEHICLE JOURNEYs used in EUDIT should be the same as the ones prescribed for NeTEx and SIRI. ( This should thus be assured in the OJP-content)

Also note that EU law states that the broken connection on the train ride needs to be covered with appropriate REDRESS.
However it is more open if the resulting indirect broken connection to the bus ride is covered.  However the EUDIT API should have transmitted enough details to clarify this, and it should be possible in an aggregator or at the RETAILER to add "insurances"  ( maybe at an extra cost) providing TRAVEL GUARANTEEs across combined offers.

