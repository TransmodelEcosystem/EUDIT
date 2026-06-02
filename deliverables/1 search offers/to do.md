## Things to do

* cope with the group id / fare zones / networks / geospatial filters / custom filter in the request offer filters

* add sorting, limit/offset in search offer policy

* find other name of 'ENTITLEMENT RIGHT'. It must also be used for reduction purposes like mediatype, distribution channel, payment types, ...

* errors/warnings on offers

* status on offer elements (to discuss)


***** 
- how to limit the amount of offers? => functional, 
- temporal & spatial validity (on the offer)
- name & description
- 

[ {
    id: 1
    objectType: seat allocation,
    seatType: BERTH
  },
  {
    id: 2,
    objectType: seat allocation,
    seatType: BERTH
  },
  {
    id: 3,
    objectType: seat allocation,
    seatType: COUCHETTE
  },
  { 
    id: 4,
    objectType: travel right,
    providedSections: [ { legRef: 1, legRef: 2 } ] // or trip pattern
    reqSpotAlloc: [ { legRef: 1, min: 1, max: 2, chooseFrom: [ 1, 2 ] } } ]
  }
]