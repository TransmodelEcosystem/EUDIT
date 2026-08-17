
# Guidelines on the use of enums, extensible enums and lookup data

These guidelines must be respected when implementing/designing the OTI, when using enums, x-enums or lookup lists.

## Enum

Enums define a _final_ list of values for an API version.  
_Deleting a value is a mayor verion change_ and _adding an enum a minor version change_. 
In case an additional value should be used with older API versions patch versions are needed for the older versions.

__Values of an enum cannot be deprecated.__

Use an Enum only if:
* The values will not change within the next years until the next mayor version
* The values are assumed to be exhaustive in the context of the API version

*Example*
```json
      type: string
      description: |
        VALUE_1 : English description of what this value means  
        VALUE_2 : English description of what this value means
      enum:
      -	VALUE_1
      -	VALUE_2
      default: VALUE_2
```      

> __NOTE!__  
> Enums like these must be described in the OTI specification, and should be taken 'as is'; they are inmutable.

## Extensible Enum

Extensible enums are used to specify a list of values not bound to an API version.  
New values can be added to the list at any time by the governing body of the API.  
The list needs to be documented separately from the API, but it is not bound to an API version.  
Implementers must be able to handle new values. A documentation on the expected implementations should be added in the comment on the enumeration. In general, the behaviour will be to ignore the unknown values.

Extensible enums might be used as new values are defined frequentely or because they are open for bilateraly or unilateraly defined values. It must be documented whether bilateral or unilateral additional values are allowed.

*Example*
```json
  someExtendibleEnum:
    type: string
    externalDocs:
        description: This url refers to the definition of the code list 'someExtendibleEnum'
        url: https:/..../oti/codelists?#someExtendibleEnum
    description: |
      VALUE_3 : Only for uni- or bilateral values
    x-extensible-enum:
    - VALUE_1
    - VALUE_2
    - VALUE_3
```
> __NOTE!__  
> Avoid extending these list as much as possible (interoperability).  
> If it is not avoidable, reach out to the governing body to request the addition of a new value.  
> In the meantime, you can add it as a uni- or bilateral value.

*Usage conditions*  
> * If, and only if, you have uni- or bilateral values, you MAY extend this list. Notify the governing body.  
> * only codelist values must be accepted by requestors (Retailer),
> * unknown (to the requestor) values should be ignored

*Useful instructions*
> * Use the pipe ("|") in the description, this means that linefeeds will be taken literally.  
> * Use 2 spaces in the description to specify a line feed:  
>   VALUE_1 : English description of what this value means  <-- ADDED TO SPACES HERE

## Lookup lists

This refers to an API endpoint to get possible selectable items, instead of specifying them in the specification. It is not included in the API messages themselfes due to:
* operator/distributor-specific
* frequent updates

_Some example scenarios_
If the information is needed before the API message flow starts:
* the reduction cards accepted by a provider so they can be used in a customer account prior to the sales process
* product search options needed to setup the search UI prior to a product based search.

If the information might require some preprocessing to be adopted to sales channels:
* Layouts for a graphical selection of seats 

If supportive information is needed for use cases:
* Detailed definition of zones to be displayed graphically.

*Example without ETag*
This is an EXAMPLE. It is not likely that the statusses will need a flexible list. 

> Request
```http
GET /statusses
Accept: application/json
```
> Response 1: small datasets
```http
HTTP/1.1 200 OK
Content-Type: application/json
ETag: "ETag1"
Cache-Control: max-age=0, must-revalidate
{
  "new": {
    "nl": "Nieuw",
    "en": "New",
    "de": "Neu"
  },
  "pending": {
    "nl": "Wachtend",
    "en": "Pending",
    "de": "Ausstehend"
  }
}
```
> Response 2: large datasets
```http
HTTP/1.1 303 See Other
Location: https://.../statusses_v1
ETag: "ETag1"
Cache-Control: max-age=0, must-revalidate
```
The data can be fetched directly from the 'Location' (rerouting).

*Example using the ETag*

The initial retrieval of the data is the same as without using the ETag. But now it is possible to 
use the ETag header field, to check if it is possible to reuse data retrieved earlier, to reduce the
amount of transmitted data.

> ETag enabled request
```http
GET /statusses
Accept: application/json
If-None-Match: "eTag1"
```
> Response 1: data is not modified, the ETag remains the same
```http
HTTP/1.1 304 Not Modified
ETag: "ETag1"
```
> Response 2: data is modified, the ETag is different, for small datasets
```http
HTTP/1.1 200 OK
Content-Type: application/json
ETag: "ETag2"

{
  "new": {
    "nl": "Nieuw",
    "en": "New",
    "de": "Neu"
  },
  "pending": {
    "nl": "In afwachting",
    "en": "Pending",
    "de": "Ausstehend"
  }
}

```
> Response 2: data is modified, the ETag is different, for large datasets
```http
HTTP/1.1 303 See Other
Location: https://.../statusses_v2
ETag: "ETag2"
Cache-Control: max-age=0, must-revalidate
```

*Usage conditions*
> Lookup endpoints MUST provide:
> * multilanguage support for all text

> Endpoints for larger data sets MUST support:
> * cache control using a strong ETag as version tag 
    ETag = MD5, SHA-1 of SHA-256 hash of the result, between double quotes.
> * http reply 303 with a download location

> __NOTE!__  
> In the OTI, it will be prescribed per data lookup endpoint if it requires the 'large data set' regime.

### ETag references
* RFC 9110: HTTP Semantics - Section 8.8.3 (ETag)
* RFC 9110: HTTP Semantics - Section 13.1.2 (If-None-Match)
* RFC 9111: HTTP Caching
