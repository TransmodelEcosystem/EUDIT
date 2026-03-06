# APDS — Alliance for Parking Data Standards

**APDS** (Alliance for Parking Data Standards) is an open specification for the exchange of parking-related data between operators, municipalities, enforcement authorities, mobility platforms, and end-users. It defines a common data model and REST API for parking locations, rights/products, pricing, sessions, occupancy, and organisations.

* **Version**: 1.x (living standard; published as a suite of specification documents)
* **Namespace**: `https://www.allianceforparkingdatastandards.org/`
* **License**: Open — freely available to implementers
* **Source**: [allianceforparkingdatastandards.org](https://www.allianceforparkingdatastandards.org/)
* **Maintained by**: Alliance for Parking Data Standards — a joint initiative of the British Parking Association (BPA), European Parking Association (EPA), and International Parking & Mobility Institute (IPMI)

**Relevance to EUDIT**: APDS covers the parking/kerb layer of multimodal mobility — physical parking locations, kerbside zones, pricing rules, permits/rights, and session events. Within EUDIT, APDS concepts are relevant for place/location data (parking facilities as stops or interchange points), pricing structures, operator identity, area geometry (parking zones), and booking/session lifecycle where parking is part of a door-to-door journey.

---

## Glossary of APDS Data Objects

**Place**: The top-level APDS object representing a physical parking location or kerbside segment where parking can occur. A Place has a unique `id`, `name`, `type` (OFF_STREET, ON_STREET, KERBSIDE), `geometry` (geographic boundary or point), `address`, `operator` (Organisation reference), `tariff` references, `characteristics` (capacity, height limit, etc.), and `rightSpecifications` (list of applicable parking rights). A Place is the authoritative record of where parking occurs.

**PlaceLocation**: The geographic representation of a Place. Can be a `Point` (latitude/longitude for a specific access point), a `Polygon` (boundary of a surface lot or parking zone), or a `MultiPolygon` (composite zone). Uses WGS84 coordinates. Equivalent to the spatial footprint of a parking facility or kerbside zone.

**RightSpecification**: A definition of a parking right or product — what conditions must be met to park legally at a Place during a given period. Contains `id`, `description`, `type` (CONDITION, RESTRICTION, REGULATION), `validity` (time/day windows), `vehicle_types` (which vehicles are eligible), `holders` (required permits or credentials), `rateTable` reference, and `maxStay`. A RightSpecification is the parking equivalent of a fare product.

**RateTable**: A pricing structure associated with a RightSpecification or directly with a Place. Contains `id`, `currency`, `rateLines` (list of Rate entries with `duration_start`, `duration_end`, `rate`, `rate_unit`). Defines the cost of parking over time, equivalent to a tariff or fare structure.

**Rate**: A single pricing entry within a RateTable. Contains `rate` (monetary amount as a decimal), `currency` (ISO 4217 code), `rate_unit` (MINUTE, HOUR, DAY, WEEK, MONTH, EVENT, FLAT), `vat`, and optional `duration_start`/`duration_end` for stepped pricing. Represents the per-unit cost of using a parking facility.

**AssignedRight**: An instance of a parking right granted to a specific user or vehicle for a specific Place and time window. Contains `id`, `rightSpecification` reference, `placeId`, `holder` (user/vehicle identity), `validFrom`, `validTo`, and `status` (ACTIVE, EXPIRED, CANCELLED, PENDING). The AssignedRight is the booking or permit record for a parking product — analogous to a ticket or reservation.

**OccupancyEvent** / **Session**: A record of a specific parking transaction or occupancy event at a Place. Contains `id`, `placeId`, `start_time`, `end_time`, `duration`, `vehicle` (plate or token reference), `rightAssigned` reference, `total_cost`, and `status` (ACTIVE, COMPLETED, OVERSTAY, VIOLATION). Captures the actual usage of a parking space, equivalent to a service journey for the parking domain.

**Organisation**: An APDS entity representing a parking operator, authority, or enforcement body. Contains `id`, `name`, `type` (OPERATOR, AUTHORITY, ENFORCEMENT, PROVIDER), `contactDetails` (address, email, phone, URL), and `roles`. Corresponds to the transport operator or authority concept in mobility standards.

**Segment**: A portion of a kerbside or on-street parking area, typically a stretch of road between two intersection points. A Segment belongs to a Place and has its own geometry (LineString), capacity, and applicable RightSpecifications. Used for kerbside management and on-street parking regulation.

**Polygon**: The geometric boundary of a Place or Zone, expressed as an ordered list of WGS84 lat/lon coordinate pairs forming a closed ring. Used for off-street lots (single polygon) and tariff zones (multi-polygon). Equivalent to a GeoJSON Polygon geometry in the APDS data model.

**MultiPolygon**: A composite geographic boundary composed of two or more Polygons, used to represent non-contiguous parking zones or permit areas. Equivalent to GeoJSON MultiPolygon.

**AreaLocation**: A circular or polygonal geographic area used to express a parking zone or operating boundary for enforcement or permit validity. Contains either a `circle` (centre point + radius in metres) or a `polygon` (ordered coordinate list). Used in RightSpecifications to define spatial validity of parking rights.

---

## Key Field-Level Concepts

**placeId**: Unique identifier for an APDS Place, used as a foreign key in AssignedRight, OccupancyEvent, and RateTable to link pricing and session records back to the physical location.

**rate_unit**: Enumeration of billing units for parking: `MINUTE`, `HOUR`, `DAY`, `WEEK`, `MONTH`, `EVENT` (per entry/exit), `FLAT` (fixed one-time charge). Determines how the `rate` amount is applied to the duration of a parking session.

**maxStay**: The maximum permitted parking duration defined in a RightSpecification, expressed in minutes. Enforces time-limited parking rules (e.g., "2-hour maximum Mon–Fri").

**holder**: The credential or identity required to use a RightSpecification. May be a permit reference, a vehicle category, an access token, or a user account identifier. Used to link parking rights to specific entitlement holders, analogous to a travel entitlement in Transmodel.

**vehicle_types**: A filter in RightSpecification specifying which vehicle categories the right applies to (e.g., PASSENGER_CAR, MOTORCYCLE, BICYCLE, ELECTRIC_VEHICLE, HGV, COACH). Equivalent to eligibility constraints on a fare product.

**currency**: ISO 4217 three-letter currency code used in Rate and RateTable objects to denominate parking prices (e.g., EUR, GBP, USD).

**geometry**: The spatial representation of a Place or Segment, expressed as GeoJSON (Point, Polygon, MultiPolygon, or LineString). Used to render parking zones on maps and evaluate whether a vehicle's position is within a valid parking area.
