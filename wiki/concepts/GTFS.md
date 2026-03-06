# GTFS — General Transit Feed Specification

**GTFS** (General Transit Feed Specification, also called GTFS Schedule) is an open standard for publishing fixed-route public transit schedules and associated geographic information. It is the de-facto interchange format between transit agencies and journey-planning applications worldwide. **GTFS-Flex** (now merged into GTFS as of the 2024 adoption) extends the base spec to support demand-responsive and on-demand transit services.

* **Version**: GTFS Schedule (static) — continuously revised; GTFS-Flex v2 adopted into GTFS in 2024
* **Namespace**: No formal URI namespace; files are identified by their `.txt` / `.geojson` filename
* **License**: Creative Commons Attribution 4.0 International (CC BY 4.0)
* **Source**: [gtfs.org/documentation/schedule/reference](https://gtfs.org/documentation/schedule/reference/)
* **Maintained by**: MobilityData

**Relevance to EUDIT**: GTFS is the primary format for scheduled public transport data exchange in multimodal journey planning. Within EUDIT, GTFS concepts are relevant for stops/stations, routes/lines, trips/service patterns, schedules, fare products and media, and (via GTFS-Flex) demand-responsive booking. GTFS-RT (real-time) is out of scope for this project.

---

## Glossary of GTFS Static Files and Key Fields

### agency.txt

Defines the transit agency (operator) providing service in this feed. A feed may contain multiple agencies.

* **agency_id**: Unique identifier for the agency within the feed. Required when multiple agencies are present.
* **agency_name**: Full name of the transit agency.
* **agency_url**: URL of the agency's website.
* **agency_timezone**: Timezone where the agency is located (IANA timezone name).
* **agency_lang**: BCP 47 language code for the primary language used by this agency.
* **agency_phone**: Voice telephone number for the agency.
* **agency_fare_url**: URL of a web page for purchasing tickets online.
* **agency_email**: Customer service email address.

---

### stops.txt

Defines individual stops, stations, entrances/exits, generic nodes, and boarding areas. A stop is a location where passengers board or alight from a transit vehicle.

* **stop_id**: Unique identifier for the stop/station.
* **stop_code**: Short public-facing code for the stop (e.g. displayed at the stop).
* **stop_name**: Name of the stop or station.
* **stop_desc**: Description providing additional information about the stop.
* **stop_lat / stop_lon**: WGS84 latitude and longitude of the stop.
* **zone_id**: Fare zone the stop belongs to. Used to calculate zone-based fares.
* **stop_url**: URL of a web page specific to this stop.
* **location_type**: Indicates the type of location — `0` (or empty) = Stop/Platform, `1` = Station, `2` = Entrance/Exit, `3` = Generic Node, `4` = Boarding Area.
* **parent_station**: For stops within a station (location_type 0, 2, 3, 4), the `stop_id` of the parent station (location_type 1).
* **stop_timezone**: Timezone at the stop, if different from agency timezone.
* **wheelchair_boarding**: Indicates wheelchair accessibility: `0` = no information, `1` = accessible path exists, `2` = no accessible path.
* **level_id**: Reference to `levels.txt` entry for multilevel stations.
* **platform_code**: Platform identifier for a platform stop (e.g. "A", "1").

---

### routes.txt

Defines transit routes — a group of trips displayed to passengers as a single service.

* **route_id**: Unique identifier for the route.
* **agency_id**: Reference to `agency.txt`. Required if multiple agencies.
* **route_short_name**: Short public name of the route (e.g. "32", "Red Line").
* **route_long_name**: Full name of the route (e.g. "City Centre — Airport").
* **route_desc**: Description of the route.
* **route_type**: GTFS route type code — `0` = Tram/Light Rail, `1` = Subway/Metro, `2` = Rail, `3` = Bus, `4` = Ferry, `5` = Cable Tram, `6` = Aerial Lift, `7` = Funicular, `11` = Trolleybus, `12` = Monorail.
* **route_url**: URL of a web page about the particular route.
* **route_color**: Hex colour for the route (e.g. `FF0000`). Used in map/UI display.
* **route_text_color**: Hex colour for text drawn against `route_color`.
* **route_sort_order**: Order in which to present routes to passengers.
* **continuous_pickup / continuous_drop_off**: Indicates whether passengers may board/alight at any point along the route shape (flag-stop service).
* **network_id**: Identifies a group of routes (network) for use in fare network rules.

---

### trips.txt

Defines individual trips — a sequence of stops served by a vehicle at specific times, on a particular service day.

* **route_id**: Reference to `routes.txt`.
* **service_id**: Reference to `calendar.txt` or `calendar_dates.txt` — defines which days the trip runs.
* **trip_id**: Unique identifier for the trip.
* **trip_headsign**: Text displayed on the vehicle identifying the trip destination.
* **trip_short_name**: Public short name for the trip (e.g. train number).
* **direction_id**: Binary flag indicating travel direction — `0` = outbound, `1` = inbound.
* **block_id**: Identifies the block to which a trip belongs (a block is a sequence of trips made by the same vehicle).
* **shape_id**: Reference to `shapes.txt` — the geographic path of the trip.
* **wheelchair_accessible**: `0` = no information, `1` = vehicle accommodates wheelchair, `2` = no accommodation.
* **bikes_allowed**: `0` = no information, `1` = at least one bicycle allowed, `2` = no bicycles allowed.

---

### stop_times.txt

Defines the arrival and departure times at each stop for every trip. This is typically the largest file in a GTFS feed.

* **trip_id**: Reference to `trips.txt`.
* **arrival_time / departure_time**: Arrival/departure time at the stop in `HH:MM:SS` format. Times may exceed 24:00:00 for trips spanning midnight.
* **stop_id**: Reference to `stops.txt`.
* **stop_sequence**: Non-negative integer giving the order of stops on a trip.
* **stop_headsign**: Text displayed to passengers indicating the trip destination from this stop onward.
* **pickup_type / drop_off_type**: Indicates boarding/alighting method — `0` = regularly scheduled, `1` = no pickup/drop-off, `2` = must phone agency, `3` = must coordinate with driver.
* **continuous_pickup / continuous_drop_off**: Indicates flag-stop service between this stop and the next.
* **shape_dist_traveled**: Actual distance traveled from the first stop of the trip to this stop, in the same units as `shapes.txt`.
* **timepoint**: Indicates whether arrival/departure times are exact (`1`) or approximate (`0`).

---

### calendar.txt

Defines service availability by day-of-week and date range.

* **service_id**: Unique identifier for the service schedule.
* **monday … sunday**: Binary flags (`0`/`1`) indicating if service runs on each weekday.
* **start_date / end_date**: Date range for which this schedule applies (`YYYYMMDD`).

---

### calendar_dates.txt

Adds or removes service on specific dates, overriding `calendar.txt`.

* **service_id**: Reference to `calendar.txt`.
* **date**: Date of the exception (`YYYYMMDD`).
* **exception_type**: `1` = service added, `2` = service removed.

---

### shapes.txt

Defines the geographic path of a trip as an ordered sequence of WGS84 coordinates.

* **shape_id**: Unique identifier for the shape.
* **shape_pt_lat / shape_pt_lon**: WGS84 latitude/longitude of a shape point.
* **shape_pt_sequence**: Non-negative integer ordering shape points.
* **shape_dist_traveled**: Cumulative distance from the first shape point, in the feed's distance units.

---

### transfers.txt

Defines preferred or required transfer rules between stops and routes.

* **from_stop_id / to_stop_id**: The stop where a passenger departs / arrives for the transfer.
* **from_route_id / to_route_id**: Optional — scope the transfer rule to specific routes.
* **from_trip_id / to_trip_id**: Optional — scope the rule to specific trips.
* **transfer_type**: `0` = recommended, `1` = timed transfer (vehicle waits), `2` = minimum time required, `3` = no transfer possible, `4` = in-seat transfer (passenger stays on vehicle), `5` = in-seat transfer not allowed.
* **min_transfer_time**: Minimum time in seconds required to transfer.

---

### fare_attributes.txt

Defines fare rules using the legacy GTFS fare model.

* **fare_id**: Unique identifier for a fare class.
* **price**: Fare price in the currency specified.
* **currency_type**: ISO 4217 currency code.
* **payment_method**: `0` = pay on board, `1` = pay before boarding.
* **transfers**: Number of transfers permitted — `0`, `1`, `2`, or empty (unlimited).
* **transfer_duration**: Length of time in seconds that a transfer is valid.

---

### fare_products.txt

*(GTFS Fares v2 — extended fare model)* Defines purchasable fare products.

* **fare_product_id**: Unique identifier for the fare product.
* **fare_product_name**: Name of the fare product (e.g. "Single Journey", "Day Pass").
* **fare_media_id**: Reference to `fare_media.txt` — the medium required to use this product.
* **amount**: Cost of the fare product.
* **currency**: ISO 4217 currency code.

---

### fare_media.txt

*(GTFS Fares v2)* Defines the physical or virtual media used to hold or validate fare products.

* **fare_media_id**: Unique identifier for the fare medium.
* **fare_media_name**: Name of the medium (e.g. "Oyster Card", "Mobile App", "Paper Ticket").
* **fare_media_type**: `0` = none (cash), `1` = paper ticket, `2` = transit card (stored-value), `3` = cEMV (contactless bank card), `4` = mobile app.

---

### fare_leg_rules.txt

*(GTFS Fares v2)* Maps fare products to specific legs of a journey based on network, from/to area, and distance.

* **leg_group_id**: Optional grouping identifier.
* **network_id**: Reference to a route network from `routes.txt`.
* **from_area_id / to_area_id**: Reference to `areas.txt` — origin/destination fare zones.
* **fare_product_id**: The fare product applicable to this leg.

---

### fare_transfer_rules.txt

*(GTFS Fares v2)* Defines how fare products apply when transferring between legs.

* **from_leg_group_id / to_leg_group_id**: The leg groups between which the transfer rule applies.
* **transfer_count**: Maximum number of sequential transfers allowed under this rule.
* **duration_limit**: Time window in seconds within which the transfer must occur.
* **fare_product_id**: Additional fare product (e.g. transfer discount) applicable.

---

### areas.txt

*(GTFS Fares v2)* Defines named fare zones/areas. Stops are assigned to areas via `stop_areas.txt`.

* **area_id**: Unique identifier for the area.
* **area_name**: Human-readable name (e.g. "Zone A", "Inner City").

---

## GTFS-Flex Extensions

GTFS-Flex adds support for demand-responsive and on-demand transit services. The following files are part of GTFS-Flex (now incorporated into GTFS Schedule).

### booking_rules.txt

Defines the rules for pre-booking a demand-responsive trip.

* **booking_rule_id**: Unique identifier for the booking rule.
* **booking_type**: `0` = real-time booking (no advance notice), `1` = same-day booking with advance notice, `2` = advance booking with minimum days notice.
* **prior_notice_duration_min / prior_notice_duration_max**: Minimum/maximum advance notice in minutes required for booking.
* **prior_notice_last_day**: Latest day before the trip on which booking must be made.
* **prior_notice_last_time**: Latest time on `prior_notice_last_day` by which booking must be made.
* **prior_notice_start_day / prior_notice_start_time**: Earliest day/time from which booking is accepted.
* **message**: Customer-facing message about how to book this service.
* **pickup_message / drop_off_message**: Messages about the pickup/drop-off procedure.
* **phone_number**: Phone number for booking.
* **info_url**: URL with booking information.
* **booking_url**: URL for online booking.

---

### location_groups.txt

Groups stops and/or GeoJSON locations into named zones for flexible pickup/drop-off.

* **location_group_id**: Unique identifier for the location group.
* **location_group_name**: Human-readable name.

### location_group_stops.txt

Associates stops with location groups.

* **location_group_id**: Reference to `location_groups.txt`.
* **stop_id**: Reference to `stops.txt`.

---

### locations.geojson

GeoJSON `FeatureCollection` describing geographic areas (polygons) where flexible pickup or drop-off is permitted. Each Feature's `id` is a `location_id` referenced in `stop_times.txt` (in place of a `stop_id`). Properties may include `stop_name` and `stop_desc`.

---

### stop_times.txt — GTFS-Flex fields

The following fields extend `stop_times.txt` for flexible services:

* **location_group_id**: In place of `stop_id` — reference to a `location_groups.txt` entry.
* **location_id**: In place of `stop_id` — reference to a GeoJSON feature in `locations.geojson`.
* **start_pickup_drop_off_window / end_pickup_drop_off_window**: Time window (in `HH:MM:SS`) during which a vehicle serves the flexible zone.
* **pickup_booking_rule_id / drop_off_booking_rule_id**: References to `booking_rules.txt` for how to book pickup/drop-off at this location.
