# DATEX II

**DATEX II** is the European standard for the exchange of road traffic and travel information between road traffic control centres, service providers, and the media. It provides a common data model and communication framework enabling consistent real-time and static road-condition data exchange across European national access points (NAPs).

* **Version**: 3.6 (current; v3.x series in use since 2018)
* **Standard**: EN 16157 series (CEN)
* **Documentation**: [docs.datex2.eu](https://docs.datex2.eu)
* **License**: Freely available; maintained under NAPCORE and CEN TC 278/WG 8
* **Maintained by**: DATEX II community under NAPCORE (National Access Point Coordination Organisation for Europe)

**Relevance to EUDIT**: The EUDIT scope document (see `wiki/scope/README.md`) explicitly states that the TS is agnostic to DATEX II, but acknowledges it as a complementary boundary standard. DATEX II concepts are relevant where EUDIT APIs expose or consume location references, parking facilities, operator identity, vehicle characteristics, or situation alerts — all domains where DATEX II is widely deployed in national ITS ecosystems. Annotations record alignment at the boundary level, not normative dependency.

---

## Glossary of Native DATEX II Classes

DATEX II uses a UML-based Platform Independent Model (PIM) organised into three logical namespaces: **Common**, **LocationReferencing**, and **PayloadPublication**. Class names use PascalCase (matching the XSD schema types). The following classes are those with direct relevance to the EUDIT harmonisation scope.

### Common — Core Data Types

**PayloadPublication**: The abstract root class for all publications exchanged in DATEX II. Every message is a specialisation of `PayloadPublication` carrying a `publicationTime` and a reference to the publishing organisation.

**Organisation**: An organisation involved in road traffic management, data provision, or operation. Identified by a name and optionally a country code and contact details.

**InternationalIdentifier**: A composite identifier combining a country code and a national identifier string, used to uniquely identify organisations, measurement sites, and publication authors across jurisdictions.

**Source**: The source of a data item — typically an organisation, sensor system, or publication authority — including a `sourceName`, `sourceCountry`, and optional reliability rating.

**Validity**: A time period or set of recurring time periods during which a record (situation, measure, regulation) is valid. Carries a `validityStatus` (active, suspended, defined by versioning) and temporal extent.

**OverallPeriod**: The overall time period for a DATEX II record, encompassing one or more individual validity periods and recurring day/time specifications.

**MultilingualString**: A string value carrying one or more language-tagged text alternatives, enabling multilingual display of names, descriptions, and messages.

### Common — Vehicle Package

**Vehicle**: A road vehicle subject to DATEX II traffic management or measurement. Identified by registration number, country of registration, and optional colour and characteristics.

**VehicleCharacteristics**: A set of characteristics describing one or more vehicles, used to specify to which vehicles a restriction, measurement, or regulation applies. Covers `vehicleType`, `fuelType`, `loadType`, `vehicleUsage`, gross weight, length, height, and width.

**VehicleTypeEnum**: Enumeration of vehicle types: `anyVehicle`, `bicycle`, `bus`, `car`, `carOrLightVehicle`, `heavyGoodsVehicle`, `heavyGoodsVehicleWithTrailer`, `moped`, `motorcycle`, `motorcycleWithSideCar`, `motorscooter`, `tanker`, `tram`, `trolleyBus`, `van`, `other`.

**VehicleUsageEnum**: Enumeration of vehicle usage purposes: `taxi`, `publicTransport`, `emergencyServices`, `militaryVehicle`, `roadMaintenanceOrConstruction`, `snowPlough`, `privateAndOrCommercial`.

### Common — Measurement Package

**MeasurementSite**: A fixed location at which measurements of traffic conditions (speed, flow, occupancy) are taken. Carries a `measurementSiteLocation` and an optional `measurementSiteName`.

**BasicData**: Abstract base class for all measured or derived data values. All data items carry a `time` and a reference to the `measurementSiteReference`.

**TrafficData**: Measured or derived traffic conditions, including traffic flow, occupancy, and vehicle speed, at a specific measurement site.

**TravelTimeData**: Measured or calculated travel time information for a specific road section or route. Includes `travelTime`, `freeFlowTravelTime`, `normallyExpectedTravelTime`, and `travelTimeType` (measured, predicted, etc.).

**SpeedValue**: A measured or estimated vehicle speed value in kilometres per hour, with associated accuracy and reliability metadata.

### LocationReferencing — Point and Area Types

**Location**: Abstract base class for all location reference types in DATEX II. Specialised into point, linear, and area locations.

**PointLocation**: A single point location reference on the road network. Carries a `pointByCoordinates` (WGS84 latitude/longitude) and may include an `alertCPoint` (Alert-C location reference) or `tpegPointLocation`.

**PointCoordinates**: WGS84 geographical coordinates for a point: `latitude` (decimal degrees) and `longitude` (decimal degrees), with an optional `altitude` in metres. The standard reference coordinate system for all DATEX II point locations.

**LinearLocation**: A reference to a linear section of the road network between two endpoints. Used for road sections, closures, restrictions, and measurement corridors.

**AreaLocation**: A reference to a geographical area. Specialised into `NamedArea` (administrative regions) and `GmlGeometry`-based polygons.

**NamedArea**: A named geographic area identified by one or more administrative codes — country, nation, country subdivision, urban, municipality — without necessarily including geometric boundary data. Used for high-level area qualification.

**GmlLineString**: A GML 3.1.1 line string geometry (sequence of WGS84 coordinate pairs) representing a road alignment or route segment.

**GmlPolygon**: A GML 3.1.1 polygon geometry (exterior boundary as a coordinate ring) representing an area such as a geofencing zone, restricted area, or controlled zone.

### Common — Facilities Package (Parking)

**Parking**: A parking facility, including static attributes such as `parkingName`, `parkingType` (parkAndRide, streetParking, offStreetParking, etc.), `maximumParkingDuration`, `reopeningDate`, and associated `ParkingAccessibility` information.

**ParkingRecord**: The static record for a parking facility, including its physical location, total capacity by vehicle type, operator reference, access equipment, and service hours.

**ParkingStatusPublication**: A dynamic publication of real-time parking availability. Carries one or more `ParkingTableStatusPublication` objects, each specifying `parkingOccupancy`, `parkingStatusColour`, and `vacantParkingSpaces` for a referenced parking facility.

**ElectricChargingPoint**: A specific electric vehicle charging point within a charging station, with `chargingPointUsage`, `connectorType`, maximum power rating, and availability.

### Common — Situation Package

**Situation**: An abnormal or noteworthy road traffic situation composed of one or more `SituationRecord` objects. Carries a unique `situationId`, `overallSeverity`, and `creationReference`.

**SituationRecord**: An individual record within a `Situation` describing a specific event, condition, or management measure. Abstract base class for subtypes including `Accident`, `EnvironmentalObstruction`, `Roadworks`, `AbnormalTraffic`, `Activities`, and `OperatorAction`.

**Accident**: A subtype of `SituationRecord` describing a road accident including `accidentType`, `numberOfVehiclesInvolved`, and optional severity and weather conditions.

**Roadworks**: A subtype of `SituationRecord` describing planned or ongoing roadworks, including `roadworksType`, `urgency`, `roadMaintenanceType`, and expected impact on traffic flow.

**AbnormalTraffic**: A subtype of `SituationRecord` describing abnormal traffic conditions such as congestion or queuing, including `abnormalTrafficType`, `queueLength`, and delay information.

**OperatorAction**: A subtype of `SituationRecord` describing a traffic management action taken by a road operator — ramp metering, lane control, VMS messaging, diversion routing.

### Common — Controlled Zones and Traffic Regulation

**ControlledZone**: A geographic area subject to traffic regulation or access restrictions (e.g., a Low Emission Zone, congestion charge area, or urban access regulation zone).

**ZoneAccessRestrictions**: Access restriction rules applicable to a `ControlledZone`, including vehicle characteristic filters, time-of-day applicability, and exemption categories.

---

## Source

DATEX II v3.6, DATEX II Community / NAPCORE.
Documentation portal: [docs.datex2.eu](https://docs.datex2.eu)
Standard series: EN 16157 (CEN TC 278/WG 8)
License: Freely available for implementation.
