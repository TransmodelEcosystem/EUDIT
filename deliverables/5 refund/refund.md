# Refund Offers Request - delivery

_Required items_

| object | description |
| --- | --- |
| REFUND OFFERS REQUEST | The request to obtain refund offers |
| REFUND OFFERS DELIVERY | The accompaning response |
| REFUND REQUEST | The request to claim a refund offers |
| REFUND DELIVERY | The accompaning response |

## REFUND OFFERS REQUEST DETAILS

| field | type | optional/conditions | description |
| --- | --- | --- | --- |
| requestId | string | condition: a-sync | a unique ID, the response to this request MUST have the same ID (in case of a-sync call) |
| contentLanguage | string | optional | (policy) The language/localization of user-facing content, One IETF BCP 47 (RFC 5646) language tag. If missing, the local accepted language |
| packageId | string | required | the reference of the package to refund |
| version | string | required | the version of the package to (partial) refund |
| packageElementRefs | list of string | optional | parts to refund |
| travellingEntityRefs | list of string | optional | travelling entities to refund |
| fulfillmentRefs | list of string | optional | fullfillments to refund |

### Example (indicative)

```json
{
    "requestId": "32490-df324",
    "packageId": "offer-1",
    "version": "305e50bfa0767185c3f0987277b60f53"
}
```

## REFUND OFFER DELIVERY DETAILS

| field | type | optional/conditions | description |
| --- | --- | --- | --- |
| requestId | string | condition: a-sync | a unique ID, the response to this request MUST have the same ID (in case of a-sync call) |
| refundOfferId | string | required | a unique ID, to identify the offer |
| summary | string | required | human readable clarification of the offer, in the requested content language, if possible |
| fee | Price | required | the fee to pay |
| refundableAmount | Price | required | the amount that can be refunded |
| packageId | string | required | the reference of the package |
| version | string | required | the version of the package to (partial) refund |
| expiryTime | date-time | required | expiry time of rollback window, ISO 8601 |
| problems | list of string | required | a list of string, describing blocking issues, in the requested language (or local if not the requested language is not supported) |
| warnings | list of string | required | a list of string, describing non-blocking issues, in the requested language (or local if not the requested language is not supported) |
| delayedReimbursement | date-time | optional | in case the reimbursement will be settled in the future |
| paymentMethod | string | optional | |
| status | string | optional | enumeration for tracking the offer |

# REFUND

## REFUND REQUEST DETAILS

| field | type | optional/conditions | description |
| --- | --- | --- | --- |
| requestId | string | condition: a-sync | a unique ID, the response to this request MUST have the same ID (in case of a-sync call) |
| contentLanguage | string | optional | (policy) The language/localization of user-facing content, One IETF BCP 47 (RFC 5646) language tag. If missing, the local accepted language |
| refundOfferId | string | required | a unique ID, to identify the offer |
| packageId | string | required | the reference of the package |
| version | string | required | the version of the package to (partial) refund |

## REFUND DELIVERY DETAILS

| field | type | optional/conditions | description |
| --- | --- | --- | --- |
| requestId | string | condition: a-sync | a unique ID, the response to this request MUST have the same ID (in case of a-sync call) |
| packageId | string | required | the reference of the package |
| version | string | required | the new version of the package |
| summary | string | required | textual explanaition, which parts are refunded |
| status | string | optional | enumeration for tracking the refund (offer status =  refunded or scheduled_refund) |
| problems | list of string | required | a list of string, describing blocking issues, in the requested language (or local if not the requested language is not supported) |
| warnings | list of string | required | a list of string, describing non-blocking issues, in the requested language (or local if not the requested language is not supported) |
