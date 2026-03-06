# ERA Ontology

The **ERA Ontology** (European Union Agency for Railways Vocabulary) is a formal OWL ontology covering European railway **infrastructure** (RINF — Register of Infrastructure) and **vehicle types** (ERATV — European Register of Authorised Types of Vehicles). It provides a shared semantic model for data exchange between infrastructure managers, railway undertakings, and regulatory bodies across the EU.

* **Version**: 3.1.8 (released 3 March 2026)
* **Base URI**: `http://data.europa.eu/949/`
* **License**: European Union Public Licence v1.2
* **Source**: [data-interop.era.europa.eu/era-vocabulary](https://data-interop.era.europa.eu/era-vocabulary/)
* **Maintained by**: European Union Agency for Railways (ERA), Interoperable Data Programme

**Relevance to EUDIT**: The ERA Ontology addresses the infrastructure and network layer — operational points, tracks, tunnels, platforms, and vehicle authorisations. It does not cover fares, tickets, or passengers. Within EUDIT, ERA concepts are relevant for the physical network topology that underpins journey planning and route compatibility checks.

---

## Glossary of Native ERA Classes

The following 58 classes are natively defined in the ERA Ontology (prefix `http://data.europa.eu/949/`). External vocabulary classes imported from GeoSPARQL, SKOS, OWL-Time, FOAF, PROV-O, and W3C Org are referenced by the ontology but not defined here.

**Activity**: ERA-specific activity aligned with `prov:Activity` (W3C PROV-O), used to describe changes affecting ERA resources.

**Body**: An organisation or a physical person.

**Bridge**: Structure constructed for the exclusive purpose of carrying railroad traffic across an obstruction. Can be used for defining non-stopping areas, big metal mass, resistance to traffic load, etc.

**CommonCharacteristicsSubset**: A set of different infrastructure objects sharing the same common technical characteristics. Parameters may not be restricted to only one railway subsystem; can include common characteristics from infrastructure, energy, and track-side CCS subsystems.

**ContactLineSystem**: System that is used to transmit electrical energy to road or rail vehicles.

**Document**: Document in any of the ERA systems, e.g. a reference document in RINF.

**ETCS**: TSI-compliant train protection system — ERTMS/ETCS application level and baseline — related to the track-side equipment.

**Feature**: Class that encompasses features that are part of the physical infrastructure (`InfrastructureElement`) and topological objects. Subclass of the geographical Feature class that has a spatial representation.

**FrenchTrainDetectionSystemLimitation**: A section with train detection limitation. Specific for route compatibility checks on the French network.

**HABD**: A trackside Hot Axle Box Detector (HABD), necessary for route compatibility checks. Specific for French, Italian, and Swedish networks.

**InfrastructureElement**: Encompasses all classes representing features implemented in the European railway infrastructure. Covers tracks, platforms, signals, tunnels, operational points, and sections of line.

**KilometricPost**: Represents a kilometric post in the linear positioning system.

**LevelCrossing**: An intersection where a railway line crosses a road or path at the same level. Can be used for ETCS trackside implementation or to identify potential collision scenarios.

**LinearElement**: Pieces of tracks composing the topology. Represents an edge in the topological graph.

**LinearElementSection**: Represents a section of a linear element. A finer granularity of a linear element, representing a specific portion of it.

**LinearPositioningSystem**: Represents a linear positioning system used for referencing positions on the network. Can also represent a sequence of one or more sections of line used for regular railway operations.

**LinearPositioningSystemCoordinate**: Represents coordinates in a linear positioning system.

**LinesideDistanceIndication**: Frequency, appearance, and positioning of a sign indicating distance as a reference post. Two types: kilometre post and hectometre post.

**LoadCapability**: A combination of the line category and speed at the weakest point of the track. Each track can have several load capability structured values with load model line category and load model speed.

**MaximumMagneticField**: Maximum magnetic field at a given location on the infrastructure. *(No formal definition in ontology.)*

**MaximumSpeedAndCantDeficiency**: Combination of maximum speed and maximum cant deficiency for which the vehicle was assessed.

**MinVehicleImpedance**: Impedance as defined in TSI CCS (Annex I, Appendix A, Table A.2 — Index 77). Minimum vehicle impedance between wheels and pantograph (only for vehicles equipped for 1500 V or 3000 V DC).

**MinimumVerticalRadius**: Minimum radius of vertical curve. *(Presentation of RINF parameter 1.2.2.0.3.3.)*

**NetAreaReference**: Represents an area reference in the network. Used for infrastructure elements positioned in relation to the topology as a subnetwork (e.g. tunnels, non-stopping areas, stations).

**NetBasicReference**: Basic positioning reference within the ERA ontology. Represents different types of geographical locations. Geographical coordinates are provided according to WGS84.

**NetElement**: Representation of all building blocks of the topology. References an associated topological network element.

**NetLinearReference**: Represents a linear reference in the network. Used for infrastructure elements positioned as a line or along a path (e.g. tracks, bridges, platform edges).

**NetPointReference**: Represents a point reference in the network. Used for infrastructure elements positioned as a point (e.g. signals, ETCS balises, buffer stops).

**NetRelation**: Defines a relation between two net elements. Models connectivity and navigability between consecutive linear elements, representing a transition from one element to another. Specifies permitted directions of travel: AB, BA, Both, or None.

**NonLinearElement**: Represents a non-linear element in the network. Serves as connection or interaction points such as stations, depots, or complex junctions. May optionally aggregate linear elements.

**OperationalPoint**: Any location for train service operations where train services may begin/end or change route, and where passenger or freight services may be provided; also any location at boundaries between Member States or infrastructure managers.

**OrganisationRole**: Represents an n-ary relationship between a `Body` and a role.

**Orientation**: Represents the orientation of a railway object in relation to a carrier linear element. Can be described by direction, orientation, and side.

**ParameterApplicability**: Applicability interval that defines the date interval in which a characteristic of an infrastructure element is applicable. Helps identify planned changes applied to technical parameters over time.

**PhaseInfo**: Indication of required information on phase separation.

**PlatformEdge**: In RINF, a platform concerns only the part of the structure neighbouring the track (interfaced with trains). A platform edge is the edge of that structure adjacent to the track.

**PrimaryLocation**: A place used by an Infrastructure Manager to define a path for a train in the TAF/TAP TSI framework/messages. A rail point where trains start, end, stop, run through, or change line. Managed by an IM identified by company code.

**RadioBlockCenter**: ETCS trackside centralised unit controlling ETCS train movements in level 2. Receives train position information via radio and sends movement authorities via radio to trains.

**RaisedPantographsDistanceAndSpeed**: Indication of the maximum number of raised pantographs per train allowed and the minimum spacing between adjacent pantograph heads at a given speed.

**ReferenceBorderPoint**: List of reference border points as specified in the RINF Application Guide.

**RunningTrack**: Any track used for train service movements; passing loops and meeting loops on plain line or track connections only required for train operation are not published.

**SectionOfLine**: The part of a line between adjacent operational points; may consist of several tracks.

**Siding**: All tracks where running trains in service movements end and which are not used for operational routing.

**Signal**: A railway signal is a visual display device (next to the tracks or via a DMI in the cabin) that conveys instructions or provides advance warning regarding the driver's authority to proceed.

**SpecialArea**: Encompasses non-stopping areas, safe areas (where it is safe to stop and evacuate), and industrial risk areas (where it is dangerous for the driver to step out).

**SpecialTunnelArea**: Area or location within a tunnel where there are safe areas (walkways, evacuation and rescue points) or restricted areas (non-stopping areas or industrial risk locations in a tunnel).

**SubsidiaryLocation**: Linked to a `PrimaryLocation`; specifies in more detail a part, attribute, or usage of that Primary Location. May be a non-rail point or a rail point not managed by an IM.

**Switch**: A unit of track comprising two fixed rails (stock rails) and two movable rails (switch rails) used to direct vehicles from one track to another.

**SystemSeparationInfo**: Indication of required information on system separation.

**TENTCorridor**: Represents the TEN-T network classification of an element, including its network level, type of traffic, and associated European transport corridors.

**TemporalFeature**: The union of `TemporalDuration` (a time extent) and `TemporalEntity` (a temporal interval or instant).

**TopologicalCoordinate**: Represents a topological coordinate within the ERA topology model.

**Track**: A pair of rails over which rail-borne vehicles can run.

**TrainDetectionSystem**: Safety system used to detect the presence of vehicles on the railway track.

**Tunnel**: A railway tunnel is an excavation or construction around the track provided to allow the railway to pass through higher land, buildings, or water. Length is measured as the fully enclosed section at rail level; tunnels in the TSI context are 0.1 km or longer.

**Vehicle**: A specific vehicle or wagon able and allowed to operate over railway infrastructure.

**VehicleType**: A vehicle type authorised to operate on the EU railway infrastructure, defining the basic design characteristics as covered by a type or design examination certificate (recorded in ERATV).

---

## External Classes Referenced

The ERA Ontology also imports and uses classes from the following external vocabularies. These are not defined in the ERA namespace and are not listed above:

| Vocabulary | Prefix | Example classes |
|---|---|---|
| GeoSPARQL | `geo:` | `geo:Feature`, `geo:Geometry` |
| SKOS | `skos:` | `skos:Concept`, `skos:ConceptScheme` |
| OWL-Time | `time:` | `time:TemporalEntity`, `time:Duration` |
| FOAF | `foaf:` | `foaf:Agent`, `foaf:Organization` |
| PROV-O | `prov:` | `prov:Activity`, `prov:Entity` |
| W3C Org | `org:` | `org:Organization`, `org:Role` |

---

## Source

ERA Ontology v3.1.8, European Union Agency for Railways.
Available at: [data-interop.era.europa.eu/era-vocabulary](https://data-interop.era.europa.eu/era-vocabulary/)
Base URI: `http://data.europa.eu/949/`
License: European Union Public Licence (EUPL) v1.2
