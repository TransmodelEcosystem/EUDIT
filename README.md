# EUDIT

## Overview

This repository contains the documentation and work products for harmonizing various transportation and mobility standards with Transmodel. The project follows a structured approach through multiple phases, with a central wiki serving as the single source of truth.

---

<!-- ## [Issues](https://github.com/TransmodelEcosystem/EUDIT/issues)

This tab contains remarks/request related to the technical specification(s). It will be used in the `anvil` phase.

## [Pull request](https://github.com/TransmodelEcosystem/EUDIT/pulls)

Most likely, we will not use this, unless we decide during the process to have a process to make changes that requires pull requests. -->

## [Discussions](https://github.com/TransmodelEcosystem/EUDIT/discussions)

This is the main page for functional discussions. We will prepare discussion items per meeting. They will be placed in this page, and you can already react on the discussions in the weeks before the next meeting. So, if even when you're not in the meeting, your opinion can be taken into account. The discussions will be prefixed with the date of the meeting when they will be addressed.

> Don't create discussions on your own, please request them in our biweekly meetings.

## [Code](https://github.com/TransmodelEcosystem/EUDIT) (Repository Structure)

Contains most work-in-progress documentation, including documents we can use or have to build on. `PLEASE DON'T MODIFY!!!` Use the discussions, so we can analyse the requested changes before they're applied to this part!

### 📚 Wiki

The **[Wiki](../../wiki)** serves as the authoritative source for all foundational concepts, principles, and mappings. It contains:

#### Core Documentation

- **[Scope](../../wiki/scope)**
  Project scope definition and boundary conditions. This also contains the **CoRoM deliverables**!

- **[Basic Principles](../../wiki/basic-principles)**
  Fundamental principles that underpin the entire project.

- **[Roles](../../wiki/roles.md)** 
  Definition and description of stakeholder roles within the project.

- **[Interoperability & Governance](../../wiki/interoperability-governance)**
  Where can we expect problems when merging? What are the 'soft' aspects around the standard and what are the options for these, to mitigate risks?

- **[Concepts & Definitions](../../wiki/concepts)** 
  Structured list of concepts and their definitions, based on Transmodel terminology.

- **[Architecture](../../wiki/architecture)**
  Project scope definition and boundary conditions. 

- **[Use Cases](wiki/use-cases)** 
  Collection of use cases, CoRoM input.

- **[User Actions](../../wiki/user-actions)**
  Detailed descriptions of user interactions and workflows.

- **[External documentation](../../wiki/external-resources)**
  External resources we can use to deliver. This also contains the **CoRoM deliverables**!
  
- **[Specifications](../../wiki/specifications)** 
  The technical specifications of the standards (fixate the version?)

#### Draft specification

**[Technical specification](../../specification/)** The draft specification(s), after each meeting, a copy will be made and the agreed changes will be applied.

#### Mappings

Each supported standard has its own directory containing mapping documentation to Transmodel:

- **[OSDM](../../wiki/mappings/osdm)**
- **[TOMP-API](../../wiki/mappings/tomp-api)**
- **[BoB](../../wiki/mappings/bob)**
- **[FerryGateway](../../wiki/mappings/ferrygateway)**
- **[OMSA](../../wiki/mappings/omsa)**
- **[InterMOD](../../wiki/mappings/intermod)**

##### Mapping Format  

Each standard mapping follows this structure:

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|-----------------|------------------|----------------------|-------------------------|-------|
| Offer | id | SALES OFFER PACKAGE | id | |

---

## Project Phases

The project follows a **five-phase approach**, metaphorically inspired by the blacksmithing process:

### 🥚 Phase 1: [The Kidling](./phases/the-kidling)
**Initiation and Foundation**

- **Purpose:** Project setup, team formation, and initial exploration
- **Deliverables:** 
  - Scope definition
  - Basic principles
  - Role description
  - Interoperability issues & governance options
  - Concept / definitions
  - Use cases / user actions
  - Architecture/structure options
  - Plan for the forge

[→ Read phase README](./phases/the-kidling/README.md)

---

### 🔥 Phase 2: [The Forge](./phases/the-forge)
**Make the first proposal**

- **Purpose:** Deliver the first technical documents, based on what we already have
- **Deliverables:**
  - Conceptual mappings
  - First draft (or options), based on the use cases and existing standards
- **Activities:**
  - design session

[→ Read phase README](./phases/the-forge/README.md)

---

### 🔨 Phase 3: [The Anvil](./phases/the-anvil)
**Hammering the drafts**

- **Purpose:** Complete, extend and finetune the drafts
- **Deliverables:**
  - Implementation specification
  - Prototypes and demos (?)
- **Activities:** 
  - Biweekly meetings, with a fixed agenda for each meeting (* based on the drafts *)
  - Once in the 5 meetings, a retro session, where we'll look back, and finalize the functionality we've addressed in the past months.

[→ Read phase README](./phases/the-anvil/README.md)

---

### 📊 Phase 4: [The Presenting](./phases/the-presenting)
**Validation and Stakeholder Review**

- **Purpose:** Results to CEN, ballotting
- **Deliverables:**
  - Presentation of the TS and the required document
- **Activities:** 
  - consistency check of the TS (OpenAPI)
  - finalize the documentation

[→ Read phase README](./phases/the-presenting/README.md)

---

### ⚒️ Phase 5: [The Tempering](./phases/the-tempering)
**Refinement and Finalization**

- **Purpose:** Incorporate feedback and finalize deliverables
- **Deliverables:**
  - Final specifications
  - Implementation guidelines
- **Activities:**
  - Refinement,
  - documentation,
  - knowledge transfer

[→ Read phase README](./phases/the-tempering/README.md)

---
