# Search Offers Request - delivery

_Required items_

| object | description |
| --- | --- |
| SEARCH OFFERS REQUEST | The search request |
| SEARCH OFFERS DELIVERY | The accompaning response |

## SEARCH OFFERS REQUEST DETAILS

| field | type | optional/conditions | description |
| --- | --- | --- | --- |
| requestId | string | condition: a-sync | a unique ID, the response to this request MUST have the same ID (in case of a-sync call) |
| travellers | List of travellingEntities | required | See the 'Who' part |
| trip | Trip | required | See the 'What' part |
| filters | List of Filters | optional | See the PR of the 'What' part |
| amendingOfferedPackageRef | string | conditional | (policy) only to be used in the 'amend' phase, where additional offers can be searched |
| contentLanguage | string | optional | (policy) The language/localization of user-facing content, One IETF BCP 47 (RFC 5646) language tag. If missing, the local accepted language |
| currency | string | optional | (policy) The preferred currency to be used in the response. The response can contain the currency, but is not obliged to be used. |

### Example (indicative)

```json
{
    "requestId": "32490-df324",
    "trip": { 
        ... see the 'what' part ... 
    },
    "travellers": [
        { ... see the 'who' part ... }
    ],
    "filters": [
        { "exclude": false,
          "filterType": "distribution",
          "fulfillmentMethod": "mobile"
        }
    ]
}
```

## SEARCH OFFER DELIVERY DETAILS

See the 'Search offers - Delivery' part