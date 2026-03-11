# OJP — Open API for Distributed Journey Planning

**OJP** (Open API for Distributed Journey Planning) is a CEN XML-based standard for requesting journey planning information from one or more distributed transport information systems. It defines a set of request/response service pairs conveyed over HTTP, expressed as XML messages. OJP is part of the Transmodel family of standards alongside NeTEx, SIRI, and OpRa, and incorporates elements from DATEX II and SIRI.

* **Version**: 2.0 (latest release, September 2024); 1.0 described in CEN/TS 17118:2017
* **Standard reference**: CEN/TS 17118:2017 — "Intelligent transport systems — Public transport — Open API for distributed journey planning"
* **Schema format**: XSD (XML Schema); namespace `OJP` (changed from `SIRI` in v2.0)
* **Source**: [github.com/VDVde/OJP](https://github.com/VDVde/OJP)
* **Maintained by**: VDVde (Verband Deutscher Verkehrsunternehmen) / CEN TC 278

**Relevance to EUDIT**: OJP is the European standard for distributed multimodal journey planning and is mandated by the ITS Directive Delegated Regulation for EU-wide multimodal travel information services. Within EUDIT, OJP concepts are relevant for trip search, stop discovery, fare information, availability, line data, and the exchange-point model for federating regional journey planners. OJP 2.0 introduces explicit Transmodel annotations throughout its XSD, making concept alignment with EUDIT straightforward.

---

## Services (Request/Delivery Pairs)

**OJPLocationInformationRequest / OJPLocationInformationDelivery**: Resolves free-text input or a geographic bounding box into one or more `Location` objects (stop points, stop places, addresses, POIs, topographic places). Supports restricting the query to specific OJP systems (`AllowedSystem`), sorting by distance or relevance, and optionally including operators. The primary entry point for place search in a distributed environment.

**OJPStopEventRequest / OJPStopEventDelivery**: Retrieves departure or arrival board events (`StopEventResultStructure`) at a given stop or stop place for a specified time window. Each stop event references the `DatedJourneyStructure` of the service, the call times, and optionally real-time updates from SIRI. Supports `IncludeHierarchy` to expand multi-level stop places.

**OJPTripRequest / OJPTripDelivery**: The core journey planning service. Takes an origin, destination, optional via points, departure/arrival time, passenger profile, mode filters, and optimisation criteria and returns `TripResultStructure` objects each containing a `TripStructure` with an ordered list of `LegStructure`s. Supports accessibility filters, real-time data, sustainability data (CO₂), elevation, cycling/hiking profiles, and `IncludeAlternativeOptions`.

**OJPMultiPointTripRequest / OJPMultiPointTripDelivery**: A batch trip-planning service with multiple origins and multiple destinations (`MultiPointType` controls whether results must cover all pairs or any pair). Used for distributed journey planning where exchange points between regional systems need to be explored in parallel.

**OJPTripInfoRequest / OJPTripInfoDelivery**: Retrieves detailed information about a specific journey identified by `JourneyRef` (and optional `OperatingDayRef`), or by train number and operator (v2.0 addition). Returns the full `DatedJourneyStructure` including formation information (from SIRI), journey relations, interchange data, and real-time status.

**OJPTripRefineRequest / OJPTripRefineDelivery** *(new in v2.0)*: Takes a previously planned trip and requests a refined version — for example resolving an unresolved leg from another system or updating it with current real-time data. `OperatorFilter` and `SystemId` control which system is queried.

**OJPFareRequest / OJPFareDelivery**: Returns fare information (`FareResultStructure`, `TripFareResultStructure`) for a given trip or set of trip legs. In v2.0 also supports `PlaceFareRequest` / `PlaceFareResult` for a location-based fare enquiry without a full trip. Includes `FareProductStructure`, `EntitlementProductStructure`, `FareQuota`, and `FareEstimated` indicators.

**OJPTripChangeRequest / OJPTripChangeDelivery** *(new in v2.0)*: Handles changes to a trip in progress — e.g. requesting an alternative continuation when a service is disrupted or missed. Distinct from `OJPTripRefineRequest` in that the traveller is already en route.

**OJPAvailabilityRequest / OJPAvailabilityDelivery** *(new in v2.0)*: Queries the physical availability of seats, spaces, or vehicles for specific trip legs — relevant for demand-responsive transport, booking-required services, and reserved-seat journeys.

**OJPLineInformationRequest / OJPLineInformationDelivery** *(new in v2.0)*: Returns information about one or more lines — schedule pattern, stops served, area geometry, operators, and mode. Provides the `LineResultStructure` with `AreaGeometry` for spatial queries.

**OJPExchangePointsRequest / OJPExchangePointsDelivery**: Returns the exchange points (`ExchangePointsResultStructure`) at which a journey can cross from one OJP system boundary to another. Each result includes a `Place`, the neighbouring `SystemId`, and a `WaitDuration`. Used by the `OJPMultiPointTripRequest` orchestration layer.

**OJPStatusRequest / OJPStatusDelivery** *(new in v2.0)*: Returns the operational status of the OJP server itself — used by aggregators to check availability before dispatching requests.

---

## Core Data Structures

**OJPRequest**: The XML envelope for all OJP requests, wrapping a SIRI `ServiceRequest` container and one or more service-specific sub-requests. In v2.0 the root namespace is `OJP` (previously `SIRI`).

**OJPResponse**: The XML envelope for all OJP responses, wrapping a SIRI `ServiceDelivery` container and one or more service-specific sub-deliveries. Each delivery carries a `Status` and optional `ProblemStructure`.

**ResponseContextStructure**: Shared context object included once per response and referenced by individual results. Contains `Places` (all `Location` objects referenced in the response) and `Situations` (all SIRI PT situations referenced by legs or stop events). Avoids repeating large objects inline.

**TripStructure**: A complete door-to-door journey from origin to destination. Contains an ordered list of `LegStructure`s, a `Duration`, `StartTime`, `EndTime`, `Transfers` count, `TripStatus` (Cancelled/Delayed/Infeasible), `IsAlternativeOption`, and optional `SustainabilityGroup` (CO₂ emissions). `Id` is a unique identifier within the response.

**LegStructure**: A single segment of a `TripStructure`. Discriminated by type: `TimedLeg` (public transport), `ContinuousLeg` (walking, cycling, driving — with optional `PathGuidance`), or `TransferLeg` (in-station transfer or interchange). Contains `Id`, `Duration`, `Length`, accessibility `Feasibility`, and real-time `AccessFeatureStatus`.

**TimedLegStructure**: A `LegStructure` sub-type representing a public transport leg. References a `DatedJourneyStructure`, `LegBoardStructure` (boarding call), `LegAlightStructure` (alighting call), and zero or more `LegIntermediateStructure`s (passed stops). Also carries `ParallelService`, `AlternativeService`, `JourneyRelations`, `GeneralAttributes`, and formation data.

**DatedJourneyStructure**: A specific vehicle journey on a specific operating day. Contains `JourneyRef`, `OperatingDayRef`, `PublicCode`, `ServiceArrival/Departure`, `Mode`, `PublishedServiceName`, `OperatorRef`, `ProductCategory`, `ReservationNeeded`, `Occupancy`, and `SituationFullRefs`. Aligns with the Transmodel DATED VEHICLE JOURNEY concept.

**ContinuousLegStructure**: A `LegStructure` sub-type for non-timetabled, continuous-mode travel (walking, cycling, car, shared vehicle). References `LegStart`, `LegEnd`, an `AlternativeService` or `ParallelService` (if applicable), and optionally `PathGuidance` with turn-by-turn instructions.

**TransferLegStructure**: An in-station or out-of-station transfer between two services. Contains `TransferMode`, `WalkDuration`, `BufferTime`, `InterchangeRef`, `ExtraInterchange`, `InterchangeCancellation`, and interchange property data from SIRI.

**StopEventResultStructure**: A result from `OJPStopEventDelivery`. Contains a `StopEventStructure` (times, service reference, platform, real-time data) and an `Id`. Groups multiple events at the same stop.

**LocationStructure** (formerly `PlaceStructure`): Represents a named geographic entity that can be used as origin/destination/via in trip planning. Sub-types: `StopPointStructure` (a single quay or platform), `StopPlaceStructure` (a multi-modal stop complex), `AddressStructure`, `PointOfInterestStructure`, `TopographicPlaceStructure`. Each carries a `Name`, `GeoPosition` (WGS84), optional `PrivateCode`/`PublicCode`, and system identifier.

**PlaceRefStructure**: A lightweight reference to a `Location` (stop, address, POI, coordinate) used in request parameters. Contains a `StopPointRef` or `GeoPosition` or `AddressRef` etc., plus optional `Name` and `AllowedSystem` to scope resolution to a specific regional OJP system.

**FareResultStructure**: Fare information returned by `OJPFareDelivery`. Contains a list of `FareProductStructure`s, total price, `VatRate`, currency, booking URL, and optionally `FareEstimated` (flag indicating the fare is an estimate). `TripFareResultStructure` extends this with the `TripId` and `BookingId` to link the fare to a specific trip result.

**FareProductStructure**: A single fare product applicable to one or more legs. Contains `FareProductId`, `FareProductName`, validity conditions, `TravelClass` (now `siri:FareClass`), `EntitlementProducts`, `BookingArrangements`, `FareQuota`, and an optional `ProtoProduct` for partial/distributed fare assembly.

**BookingArrangementStructure**: Describes how and when a service or leg can be booked. Heavily extended in v2.0 for NeTEx alignment. Contains `BookingUrl`, `InfoUrl`, `BookingNote`s (multiple), `MinimumBookingPeriod`, booking methods, and contact information.

**EntitlementProductStructure**: An entitlement (discount right, eligibility, pass) that qualifies the passenger for a particular fare or service. Contains `EntitlementProductRef`, `EntitlementProductName`, and `ValidityPeriod`.

**ModeStructure**: A transport mode used by a leg or as a filter. In v2.0 reorganised into `PersonalModesEnumeration`, `PersonalModesOfOperationEnumeration`, `ConventionalModesOfOperationEnumeration`, and `AlternativeModesOfOperationEnumeration`. Contains a `PtMode` (bus, rail, tram, metro, water, etc.) or individual mode (walk, cycle, car, park-and-ride, shared vehicle types), plus optional `AdditionalTime`.

**GeneralAttributeStructure**: A general notice or attribute attached to a leg board/alight/intermediate call. Contains `Text`, `Code`, and `Url`. Used for platform notes, service notices, accessibility information.

**SituationStructure**: A SIRI PT situation (disruption, service alteration, information notice) referenced from `ResponseContextStructure.Situations`. Situations are referenced by `SituationFullRefs` in `DatedJourneyStructure` and leg structures rather than embedded inline.

**ExchangePointsResultStructure**: A result from `OJPExchangePointsDelivery` describing a geographic location where a journey can be handed from one OJP system to another. Contains a `Location`, the target `SystemId`, `WaitDuration`, `Priority`, and routing information.

**OJPErrorStructure / ProblemStructure**: Structured error information. In v2.0 present both at the delivery level and at the result level. `ProblemStructure` has `Type` (enumerated per service), `Title`, `Details`, and `LogData`. Replaces the legacy `ErrorMessage` / `Code` / `Text` pattern.

**PathLinkStructure**: An accessibility path segment connecting two places or levels within a stop complex. Contains `PathLinkStart`, `PathLinkEnd` (`PathLinkEndStructure` with level and place), `Length`, `Duration`, `AccessibilityFeature`s (stepFreeAccess, visualSigns, etc.), and `AccessFeature`s (singleStep, shuttle, etc.).

---

## Key Field-Level Concepts

**JourneyRef**: A system-scoped identifier for a vehicle journey within a specific OJP system. Combined with `OperatingDayRef` to uniquely identify a `DatedJourneyStructure`. Corresponds to the Transmodel VEHICLE JOURNEY reference.

**SystemId**: Identifies a specific OJP system (regional journey planner) in a distributed environment. Used in `PlaceRefStructure.AllowedSystem`, `OJPExchangePointsDelivery`, and `OJPTripRefineRequest` to route requests to the correct regional server.

**OperatingDayRef**: A date reference identifying the operating day of a `DatedJourneyStructure`. Corresponds to the Transmodel OPERATING DAY concept.

**GeoPosition**: A WGS84 coordinate pair (`Longitude`, `Latitude`) used to represent a place or a leg track point. Used in `LocationStructure` and `PlaceRefStructure`.

**NumberOfResultsBefore / NumberOfResultsAfter**: Parameters in `OJPTripRequest` and `OJPStopEventRequest` that control how many results to return before and after the requested time. Default 0 in v2.0 (changed from prior implicit default).

**OptimisationMethod**: Enumeration controlling the trip-planning algorithm objective — `leastTime`, `leastInterchanges`, `leastWalking`, `leastDistance`, `environmentalSafety`, `extraSafe`, `extraReliable`, `scenic`, `quietTravel`. In v2.0 only a single method is allowed (reverted from a multi-method option).

**TransferLimit**: Maximum number of transfers allowed in a trip result. Accepts `0` (direct services only).

**UseRealTimeData**: Switch in `OJPTripRequest` specifying how real-time data is incorporated — whether to include real-time updates, fall back to schedule, or exclude real-time entirely.

**MultiPointType**: In `OJPMultiPointTripRequest`, controls whether the result must include a trip for each origin/destination pair (`allToAll`) or any combination (`anyPoint`, the default).

**DomainCode** (formerly `PrivateCode`): An internal system identifier for a place, journey, or product within a specific OJP system. Distinguished from `PublicCode`, which is the externally visible code.

**ReservationNeeded**: Flag on `DatedJourneyStructure` indicating whether a reservation is required — for the service operation itself, the call at a specific stop, or the seat.

**FareEstimated**: Boolean flag on `FareResultStructure` indicating that the returned fare is an estimate (e.g., because the full tariff data is not available in the distributed context).

**ProtoProduct**: A partial fare product structure in `FareProductStructure` carrying product-related data for further processing by downstream tariffing systems in a distributed environment where no single OJP node has the complete fare product.
