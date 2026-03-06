# GBFS — General Bikeshare Feed Specification

**GBFS** (General Bikeshare Feed Specification) is an open data standard for shared mobility systems — bikeshare, scootershare, carshare, and moped-share. It defines a set of JSON feeds that operators publish in real time so that journey planners and MaaS platforms can discover vehicles, stations, pricing, and operational zones.

* **Version**: 3.0 (released 2023)
* **Namespace / base URL**: `https://gbfs.mobilitydata.org/spec/en/latest/`
* **License**: Creative Commons Attribution 4.0 International (CC BY 4.0)
* **Source**: [github.com/MobilityData/gbfs](https://github.com/MobilityData/gbfs)
* **Maintained by**: MobilityData (formerly NABSA)

**Relevance to EUDIT**: GBFS covers the shared and on-demand personal-mobility layer — free-floating vehicles, docked stations, dynamic pricing, and geofenced operating zones. Within EUDIT, GBFS concepts are relevant for asset/vehicle discovery, station-as-stop, pricing plans, and operational constraints (geofencing, alerts).

---

## Glossary of GBFS v3.0 Feed Objects

The following objects are the top-level JSON feed files defined in GBFS v3.0. Each feed is a JSON document published at a stable URL and referenced from the root `gbfs.json` manifest.

**gbfs** (`gbfs.json`): The root discovery feed. Contains the list of all other feeds provided by the system, with their URLs and languages. Every compliant GBFS deployment must publish this file.

**gbfs_versions** (`gbfs_versions.json`): Optional feed listing all GBFS versions supported by this data source, allowing clients to select a preferred version.

**system_information** (`system_information.json`): Metadata about the shared-mobility system itself — operator identity, brand name, timezone, language, contact email, phone number, terms of service URL, and privacy policy URL. Equivalent to the operator/agency identity record.

**vehicle_types** (`vehicle_types.json`): Catalogue of vehicle types offered by the system. Each entry includes a unique `vehicle_type_id`, `form_factor` (bicycle, cargo_bicycle, car, moped, scooter_standing, scooter_seated, other), `propulsion_type` (human, electric_assist, electric, combustion, combustion_diesel, hybrid, plug_in_hybrid, hydrogen_fuel_cell), maximum range in metres, name, description, and cargo/accessibility attributes.

**station_information** (`station_information.json`): Static metadata for all docking stations — `station_id`, name, short name, latitude/longitude, address, cross-street, region, post code, rental methods (key, creditcard, paypass, apponly, etc.), whether vehicles can be left outside the docking area (`is_virtual_station`), and capacity per vehicle type. Represents a fixed physical location where vehicles are made available or returned.

**station_status** (`station_status.json`): Real-time availability at each docking station — number of vehicles available by vehicle type, number of empty docks, number of disabled vehicles and docks, whether the station is currently returning or renting vehicles, and last-reported timestamp.

**vehicle_status** (`vehicle_status.json`): Real-time location and status of individual vehicles — `vehicle_id`, `vehicle_type_id`, latitude/longitude (free-floating), current range in metres, rental URI (deep-link), pricing plan ID, vehicle equipment, whether the vehicle is reserved or disabled, and the station it is currently at (if docked).

**system_regions** (`system_regions.json`): Geographical regions within the system's operating area, used to group stations and define sub-zones. Each region has a `region_id` and name.

**system_pricing_plans** (`system_pricing_plans.json`): Pricing plans offered by the system. Each plan has a `plan_id`, name, currency, price (base per-use or per-minute/per-km), whether the plan is taxable, a description, and optionally a per-km rate, per-minute rate, and surge pricing indicator. Represents a fare or rental tariff applicable to a vehicle type.

**system_alerts** (`system_alerts.json`): Active alerts about the system — closures, maintenance, strikes, weather disruptions. Each alert has an `alert_id`, type (SYSTEM, STATION, VEHICLE, OTHER), times (start/end), affected station IDs, summary, and description URL.

**geofencing_zones** (`geofencing_zones.json`): GeoJSON `FeatureCollection` describing geographic areas with operational rules. Each zone feature carries a `rules` array specifying: vehicle types allowed, maximum and minimum speeds, whether riding/parking is allowed, and a reservation-required flag. Used for no-ride zones, slow zones, and preferred parking areas.

---

## Key Field-Level Concepts

**vehicle_type_id**: Unique identifier for a vehicle type within a GBFS feed. Referenced by `vehicle_status`, `station_information`, and `system_pricing_plans` to link capacity, availability, and pricing to specific vehicle types.

**form_factor**: Enumeration classifying the physical form of a vehicle: `bicycle`, `cargo_bicycle`, `car`, `moped`, `scooter_standing`, `scooter_seated`, `other`. Determines the regulatory and routing treatment of the vehicle.

**propulsion_type**: Enumeration classifying the energy source: `human`, `electric_assist`, `electric`, `combustion`, `combustion_diesel`, `hybrid`, `plug_in_hybrid`, `hydrogen_fuel_cell`.

**station_id**: Unique identifier for a docking station. Used as a foreign key across `station_information`, `station_status`, and `vehicle_status`.

**rental_uris**: Deep-link URIs (Android, iOS, web) for launching the operator's app directly to the vehicle or station rental flow. Enables app-to-app handoff in MaaS scenarios.

**current_range_meters**: For electric or electric-assist vehicles, the estimated remaining range in metres at the current state of charge.

**is_virtual_station**: Boolean flag on a station indicating that it is a designated parking area without physical docking hardware — vehicles may be parked within the station's geofenced boundary rather than locked to a dock.

**pricing_plan_id**: Reference from `vehicle_status` or `vehicle_types` to a plan in `system_pricing_plans`. Allows per-vehicle or per-type dynamic pricing.

**rules** (geofencing_zones): Array of operational rules attached to a geofencing zone feature, controlling speed limits, ride/park permissions, and vehicle-type applicability within that zone.
