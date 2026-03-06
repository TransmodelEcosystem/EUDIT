# OCPI — Open Charge Point Interface

**OCPI** (Open Charge Point Interface) is an open, multi-party protocol for communication between EV charging infrastructure operators and e-mobility service providers. It covers location discovery, session management, tariff exchange, billing, and token-based driver authentication across operator networks.

* **Version**: 2.2.1 (current stable; v3.0 in progress)
* **Namespace**: `https://ocpi-protocol.org/` (EVRoaming Foundation)
* **License**: Apache License 2.0
* **Source**: [github.com/ocpi/ocpi](https://github.com/ocpi/ocpi)
* **Maintained by**: EVRoaming Foundation (formerly OCPI Working Group)

**Relevance to EUDIT**: OCPI covers the EV charging layer of multimodal MaaS — discovery of charging locations, booking and management of charging sessions, pricing (tariffs), and EV driver identification. Within EUDIT, OCPI concepts are relevant for location/stop data, asset management (EVSE as a bookable vehicle attachment point), pricing structures, operator identity, and session lifecycle.

---

## Glossary of OCPI 2.2.1 Data Objects

**Location**: The central OCPI object representing a physical site where one or more EVSEs are installed. A Location has a unique `id`, `name`, `address`, `city`, `country`, `coordinates` (latitude/longitude), `evses` (list of EVSE objects), `operator` (BusinessDetails), `time_zone`, and optional `related_locations`, `directions`, `facilities`, and `opening_times`. The Location corresponds to what a driver navigates to in order to charge.

**EVSE** (Electric Vehicle Supply Equipment): A specific charging unit at a Location, capable of delivering power to exactly one EV at a time. An EVSE has a `uid` (locally unique within the Location), `evse_id` (roaming identifier in ISO 15118 / eMI3 format), `status` (AVAILABLE, BLOCKED, CHARGING, INOPERATIVE, OUTOFORDER, PLANNED, REMOVED, RESERVED, UNKNOWN), `connectors` (list), `floor_level`, `coordinates`, `physical_reference`, and `capabilities`. An EVSE is the unit that a driver books or reserves.

**Connector**: A specific socket or fixed cable at an EVSE. A Connector has a `standard` (IEC_62196_T1, IEC_62196_T2, IEC_62196_T2_COMBO, CHADEMO, GBT_AC, GBT_DC, TESLA_S, etc.), `format` (SOCKET or CABLE), `power_type` (AC_1_PHASE, AC_2_PHASE, AC_2_PHASE_SPLIT, AC_3_PHASE, DC), `voltage`, `amperage`, `max_electric_power`, `last_updated`, and optional `tariff_ids`. Defines the physical and electrical properties of a single charging interface.

**Session**: Represents an ongoing or completed charging session. A Session has `id`, `start_date_time`, `kwh` (energy delivered so far), `cdr_token` (driver token), `auth_method`, `location_id`, `evse_uid`, `connector_id`, `currency`, `charging_periods` (list of ChargingPeriod), `total_cost` (Price), `status` (ACTIVE, COMPLETED, INVALID, PENDING, RESERVATION), and `last_updated`. The Session is the authoritative record of EV energy consumption during a charging event.

**CDR** (Charge Detail Record): The final billing record for a completed charging session. A CDR contains all Session fields plus itemised `charging_periods`, `total_energy`, `total_time`, `total_parking_time`, `total_cost`, and billing-relevant metadata. The CDR is exchanged between CPO and eMSP for invoicing.

**Tariff**: A set of pricing rules that govern the cost of a Session. A Tariff has an `id`, `currency`, `type` (AD_HOC_PAYMENT, PROFILE_CHEAP, PROFILE_FAST, PROFILE_GREEN, REGULAR), `tariff_alt_text` (multi-language description), `tariff_alt_url`, and `elements` (list of TariffElement). Tariffs are referenced by Connectors via `tariff_ids`.

**TariffElement**: A component of a Tariff that applies under specified conditions. Each element contains `price_components` (list of PriceComponent) and optional `restrictions` (TariffRestrictions defining day/time windows, min/max energy, parking limits).

**PriceComponent**: A single price within a TariffElement. Has `type` (ENERGY in kWh, FLAT per session, PARKING_TIME per hour of parking, TIME per hour of charging), `price` (amount per unit), `vat` (VAT percentage), and `step_size` (minimum billing unit).

**Token**: An EV driver's identification credential. A Token has `country_code`, `party_id`, `uid` (e.g., RFID UID), `type` (AD_HOC_USER, APP_USER, OTHER, RFID), `contract_id`, `visual_number`, `issuer`, `group_id`, `valid` (boolean), `whitelist` (status for offline authorisation), `language`, and `last_updated`. Tokens are used to authenticate the driver when a session starts.

**Command**: A remote-control instruction sent from eMSP to CPO. Commands include `CANCEL_RESERVATION`, `RESERVE_NOW` (reserve an EVSE for a Token), `START_SESSION` (remotely start charging for a Token), `STOP_SESSION` (remotely stop an active session), and `UNLOCK_CONNECTOR` (physically unlock a connector). Commands enable app-initiated and MaaS-integrated charging flows.

**ChargingPeriod**: A time-bounded segment of a Session in which a specific set of dimensions are measured. Each period has a `start_date_time` and `dimensions` (list of CdrDimension — ENERGY, FLAT, PARKING_TIME, TIME, MAX_CURRENT, MIN_CURRENT, MAX_POWER, MIN_POWER, STATE_OF_CHARGE, etc.). Used in CDRs for itemised billing.

**GeoLocation**: A geographic coordinate expressed as `latitude` (decimal degrees, WGS84) and `longitude` (decimal degrees, WGS84). Used on Location, EVSE, and RelatedLocation objects to place them on a map. Corresponds to a point in the WGS84 coordinate reference system.

**BusinessDetails**: Operator or provider identification. Contains `name` (human-readable organisation name), `website` (URL), and optional `logo` (Image object with URL, category, type, width, height). Referenced by Location (`operator`, `suboperator`, `owner`) and used in Credentials to identify a party.

**Credentials**: Authentication and discovery metadata exchanged between OCPI parties when establishing a connection. Contains `token` (bearer token for HTTP authentication), `url` (VersionDetails endpoint), and `roles` (list of CredentialsRole with role, BusinessDetails, country_code, party_id).

**Price**: A monetary amount in OCPI. Contains `excl_vat` (amount excluding VAT) and optionally `incl_vat` (amount including VAT). Used in Session, CDR, and PriceComponent to express costs.

---

## Key Field-Level Concepts

**country_code**: ISO 3166-1 alpha-2 country code used throughout OCPI to scope identifiers to a country. Combined with `party_id` forms the global identity of an OCPI party.

**party_id**: A 3-character identifier for an OCPI party (CPO or eMSP), combined with `country_code` to form a globally unique party identity.

**evse_id**: The roaming identifier for an EVSE, composed of `country_code` + `party_id` + a locally unique string. Follows the eMI3 format (e.g., `NL*TNM*E02003454*0`). Used by Tokens, Sessions, and CDRs to reference the specific charging unit.

**auth_id** / **contract_id**: The unique identifier of a driver's contract/subscription with an eMSP, used in Token and Session objects to link charging sessions to the correct billing account.

**status** (EVSE): Enumeration of EVSE operational states — AVAILABLE (ready to use), BLOCKED (occupied), CHARGING (active session), INOPERATIVE (out of service), OUTOFORDER (hardware fault), PLANNED (not yet commissioned), REMOVED (decommissioned), RESERVED (booked), UNKNOWN.

**tariff_ids**: List of Tariff identifiers linked to a Connector, allowing the CPO to publish which tariff applies to each physical connector.
