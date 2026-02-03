# EUDIT

## Overview

This repository contains the documentation and work products for harmonizing various transportation and mobility standards with Transmodel. The project follows a structured approach through multiple phases, with a central wiki serving as the single source of truth.

---

## Issues

This tab contains remarks/request related to the technical specification(s). It will be used in the `anvil` phase.

## Pull request

Most likely, we will not use this, unless we decide during the process to have a process to make changes that requires pull requests.

## Actions

Not used

## Discussions

This is the main page for functional discussions. We will prepare discussion items per meeting. They will be placed in this page, and you can already react on the discussions in the weeks before the next meeting. So, if even when you're not in the meeting, your opinion can be taken into account. The discussions will be prefixed with the date of the meeting when they will be addressed.

Don't create discussions on your own, please request them in our biweekly meetings.

## Projects

Not used (for now), maybe later.

## Code (Repository Structure)

Contains most work-in-progress documentation, including documents we can use or have to build on.

### 📚 Wiki (Ground Truth Documentation)

The **[Wiki](../../wiki)** serves as the authoritative source for all foundational concepts, principles, and mappings. It contains:

#### Core Documentation

- **[Basic Principles](../../wiki/basic-principles)** (`basic-principles.md`)  
  Fundamental principles that underpin the entire project.

- **[Concepts & Definitions](../../wiki/concepts)**  
  Structured list of concepts and their definitions, based on Transmodel terminology.

- **[Scope](../../wiki/scope-options)** (`scope-options.md`)  
  Project scope definition and boundary conditions. This also contains the **CoRoM deliverables**!

- **[Roles](../../wiki/roles)**  
  Definition and description of stakeholder roles within the project.

- **[Use Cases](../../wiki/use-cases)** (`use-cases.md`)  
  Collection of use cases, CoRoM input.

- **[User Actions](../../wiki/user-actions)** (`user-actions.md`)  
  Detailed descriptions of user interactions and workflows.

- **[Documentation](../../wiki/documentation)** (`documentation.md`)  
  Resources we can use to deliver, also the technical specifications of the standards (fixate the version?)

#### Draft specification

**[Technical specification](../../specification/)** The draft specification(s), after each meeting, a copy will be made and the agreed changes will be applied.

#### Mappings

Each supported standard has its own directory containing mapping documentation to Transmodel:

- **[OSDM](../../wiki/standards/osdm)**
- **[TOMP-API](../../wiki/standards/tomp-api)**
- **[BoB](../../wiki/standards/bob)**
- **[FerryGateway](../../wiki/standards/ferrygateway)**
- **[OMSA](../../wiki/standards/omsa)**
- **[InterMOD](../../wiki/standards/intermod)**

##### Mapping Format  

Each standard mapping follows this structure:

| Concept | Attribute | Transmodel Concept(s) | Transmodel Attribute(s) | Notes |
|-----------------|------------------|----------------------|-------------------------|-------|
| Offer | id | SALES OFFER PACKAGE | id | |

---

## Project Phases

The project follows a **five-phase approach**, metaphorically inspired by the blacksmithing process:

### 🥚 Phase 1: [The Kidling](./the-kidling)
**Initiation and Foundation**

- **Purpose:** Project setup, team formation, and initial exploration
- **Deliverables:** 
  - Basic principles
  - Role description
  - Scope definition
  - Architecture/structure options
  - Use cases
  - Plan for the forge
- **Activities:** 
  - Gather basic principles
  - Define roles (simple)
  - Set the scope (functionality)
  - Define architecture & structure
  - Gather use cases
  - Gather usable (external) resources
  - Preparing the forge

[→ Read phase README](./the-kidling/README.md)

---

### 🔥 Phase 2: [The Forge](./the-forge)
**Make the first proposal**

- **Purpose:** Deliver the first technical documents, based on what we already have
- **Deliverables:**
  - Conceptual mappings
  - First draft (or options), based on the use cases and existing standards
- **Activities:**
  - design session

[→ Read phase README](./the-forge/README.md)

---

### 🔨 Phase 3: [The Anvil](./the-anvil)
**Hammering the drafts**

- **Purpose:** Complete, extend and finetune the drafts
- **Deliverables:**
  - Implementation specification
  - Prototypes and demos (?)
- **Activities:** 
  - Biweekly meetings, with a fixed agenda for each meeting (* based on the drafts *)
  - Once in the 5 meetings, a retro session, where we'll look back, and finalize the functionality we've addressed in the past months.

[→ Read phase README](./the-anvil/README.md)

---

### 📊 Phase 4: [The Presenting](./the-presenting)
**Validation and Stakeholder Review**

- **Purpose:** Results to CEN, ballotting
- **Deliverables:**
  - Presentation of the TS and the required document
- **Activities:** 
  - consistency check of the TS (OpenAPI)
  - finalize the documentation

[→ Read phase README](./the-presenting/README.md)

---

### ⚒️ Phase 5: [The Tempering](./the-tempering)
**Refinement and Finalization**

- **Purpose:** Incorporate feedback and finalize deliverables
- **Deliverables:**
  - Final specifications
  - Implementation guidelines
- **Activities:**
  - Refinement,
  - documentation,
  - knowledge transfer

[→ Read phase README](./the-tempering/README.md)

---
