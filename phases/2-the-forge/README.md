# The Forge

## Purpose

The Forge is a focused, time-boxed work phase in which a small group of experts produces the first technical proposal for the EUDIT Technical Specification (TS). Its goal is to answer the central question:

> **How do we harmonise OSDM, TOMP-API, BoB, FerryGateway, OMSA and InterMOD into a single Transmodel-compliant interface — without invalidating the investments already made by implementors?**

The Forge takes the foundation laid in [The Kidling](../1-the-kidling/README.md) — scope, principles, roles, use cases and semantic mappings — and turns it into a concrete, actionable first draft of the TS.

---

## Core Strategy: Semantic Alignment over Protocol Replacement

The key insight that guides all Forge work is:

**Implementors do not replace their stack. They expose a thin EUDIT facade grounded in a shared Transmodel vocabulary.**

This directly satisfies the principles of *"With respect to already made investments"*, *"Short Return on Investment"* and *"Low threshold for adoption"* from [Basic Principles](../../wiki/basic-principles/README.md):

* Existing OSDM, TOMP-API and BoB deployments continue to function unchanged.
* Compliance is achieved by publishing a semantic mapping declaration and, where needed, a translation adapter — not by rewriting core systems.
* The Transmodel anchor ensures all standards speak the same semantic language, even when their wire formats differ.

This strategy is realised through six concrete processes described below.

---

## Process 1 — Use-Case Driven Requirements Extraction

**Goal:** Determine the minimal set of capabilities the EUDIT TS must mandate, based on what the [7 CoRoM use cases](../../wiki/use-cases/README.md) actually require — and nothing more.

**Steps:**

1. Take each use case as the unit of analysis.
2. Walk through every step of the use case and check it against the existing standards using the [functional comparison matrix](../../wiki/mappings/README.md).
3. Classify each required capability:

   | Classification | Meaning | TS action |
   |----------------|---------|-----------|
   | **Covered** | ≥2 standards handle it compatibly | Adopt the most Transmodel-aligned formulation |
   | **Partially covered** | Standards handle it differently | Resolve via Process 4 (overlap resolution) |
   | **Gap** | No standard handles it adequately | New design required |

4. Produce a **requirements delta table** per use case — the minimal new work the TS must introduce.

**Why it protects investment:** Anything already covered in the implementor's existing stack requires zero new code — only a mapping declaration.

---

## Process 2 — Semantic Mapping as the Adoption Artefact

**Goal:** Make the Transmodel mapping — not a new YAML schema — the primary compliance deliverable for early adopters.

**Steps:**

1. Each implementor publishes a **Semantic Mapping Document (SMD)**: a machine-readable file (using the `x-semantics` format established in Phase 1) declaring how their existing API's concepts map to Transmodel.
2. A community-maintained **Mapping Registry** collects all SMDs. Reference SMDs for OSDM, TOMP-API, BoB, OMSA and FerryGateway were produced in Phase 1 and are available in [`wiki/specifications/`](../../wiki/specifications/).
3. Automated tooling validates SMDs against the Transmodel concept catalogue ([`TM.csv`](../../wiki/concepts/TM.csv) / [`Transmodel.md`](../../wiki/concepts/Transmodel.md)).

**The SMD format (established in Phase 1):**

```yaml
x-semantics:
  Transmodel:
    concepts:
      - "CONCEPT NAME (Module Name MODEL)"
    definition: "Transmodel definition text."
    conditions:
      - "Exact/Partial/No match rationale."
```

**Why it protects investment:** Existing implementations do not change. The SMD is metadata, not code. A Level 0 adopter publishes an SMD and is immediately EUDIT-aware.

---

## Process 3 — Conformance Level Framework

**Goal:** Allow implementors to adopt incrementally, protecting prior investments, while providing a clear path toward full compliance.

**The three conformance levels:**

| Level | Name | Requirement | Typical effort |
|-------|------|-------------|----------------|
| **L0** | Semantically Mapped | Publish an SMD for your existing API | Hours — mapping exercise only |
| **L1** | EUDIT-Facade | Expose EUDIT-defined endpoint(s) wrapping your existing implementation | Days to weeks per use case |
| **L2** | EUDIT-Native | Implement the EUDIT information model natively | Significant — new implementation |

**Key design constraint:** L1 facades must be derivable from L0 SMDs mechanically. Where the mapping is fully published, tooling can scaffold the adapter automatically. This directly satisfies *"Easy to understand, implement and operate"* and *"Reasonable implementation costs"*.

**Governance rule:** CEN TS compliance requires **L1** for the in-scope use cases. L2 is encouraged but not mandated. L0 is the baseline for ecosystem participation and directory listing.

[TO DISCUSS / COMPLETE] — exact L1 scope per use case to be agreed in The Forge.

---

## Process 4 — Overlap Resolution via the Transmodel Anchor

**Goal:** When two or more standards cover the same concept differently, produce a single EUDIT canonical form without discarding either standard's model.

**Steps:**

1. Identify the overlapping concept via the Transmodel anchor. For example: OSDM `Offer`, BoB `productSetInformation` and TOMP-API `package` all map to **CUSTOMER PURCHASE PACKAGE**.
2. Assemble a **concept comparison table**:

   | Attribute | OSDM field | BoB field | TOMP-API field | Transmodel attribute | Include in EUDIT canonical? |
   |-----------|-----------|---------|--------------|---------------------|--------------------------|
   | Identifier | `offerId` | `productSetId` | `id` | `id` | Yes — mandatory |
   | Validity start | `validFrom` | `startOfValidity` | — | `fromDate` (VALIDITY CONDITION) | Yes — mandatory |
   | … | … | … | … | … | … |

3. The EUDIT canonical form is the **minimal mandatory subset** that covers all in-scope use cases, plus clearly labelled **extension points** for standard-specific fields.
4. Publish the canonical form as an OpenAPI schema annotated with `x-semantics`, referencing all contributing standards.

**Why it protects investment:** No standard is discarded. Standard-specific fields become extension points. The canonical form is additive, not a replacement.

---

## Process 5 — Governance: RFC and Cross-Standard Impact Assessment

**Goal:** Prevent the TS from drifting toward one standard's model over time, and give implementors predictability and protection against breaking changes.

**RFC workflow:**

```
Propose change (GitHub Discussion, biweekly meeting)
          ↓
Cross-standard impact assessment
  (using functional comparison matrix + SMD registry)
          ↓
Classify change type:
  Non-breaking        → maintainer approval + minor version bump
  Backward-compatible → meeting approval + patch notice
  Breaking            → biweekly meeting vote + minimum 6-month notice
          ↓
SMD Registry updated → tooling regenerated → adopters notified
```

**Definitions:**
* A **breaking change** is any change that invalidates an existing L0 SMD.
* A **backward-compatible change** is any change that requires L1 adapter updates but leaves core systems untouched.
* A **non-breaking change** is any additive change (new optional field, new extension point).

**Why it protects investment:** Implementors have contractual clarity on what will and will not change under their feet. The 6-month notice period for breaking changes gives organisations time to budget and plan.

[TO DISCUSS / COMPLETE] — governance body composition and voting rules to be agreed.

---

## Process 6 — Reference Adapter Library

**Goal:** Make L1 adoption cheap enough that the *"Short Return on Investment"* principle is demonstrably satisfied.

**Steps:**

1. For each major existing standard (OSDM, TOMP-API, BoB), publish an **open-source reference adapter** — a thin translation layer that receives a EUDIT request, translates it to the native API call, and maps the response back.
2. Adapters are generated from the SMD registry where possible.
3. Adapters are versioned against the EUDIT TS, not the underlying standard — so when an underlying standard updates independently, only the adapter needs updating, not the implementor's core system.

**Concrete deliverable types:**
* OpenAPI-to-EUDIT code generator templates
* Docker-composable mock server for each use case (for conformance testing)
* Automated conformance test suite (validates L0 and L1 compliance)

[TO DISCUSS / COMPLETE] — tooling language and hosting to be agreed.

---

## How the Processes Interlock

```
Phase 1 (complete)
  └─► SMDs published for all 6 standards            [Process 2]

Phase 2 — The Forge
  ├─► Use-case delta tables produced                 [Process 1]
  ├─► Overlap resolution tables drafted              [Process 4]
  ├─► Conformance level framework agreed             [Process 3]
  ├─► RFC governance process defined                 [Process 5]
  └─► Reference adapter plan agreed                  [Process 6]

Phase 3 — The Anvil
  ├─► Canonical schemas written (L1 facades)         [Process 4]
  ├─► Reference adapters built                       [Process 6]
  └─► RFC process activated                          [Process 5]

Phase 4/5 — CEN Ballot & Tempering
  └─► L0/L1/L2 conformance criteria finalised
```

---

## Deliverables

The Forge must produce:

1. **Requirements delta tables** — one per use case, classifying each capability as Covered / Partially covered / Gap. ([Process 1](#process-1--use-case-driven-requirements-extraction))
2. **Overlap resolution tables** — for each concept covered by more than one standard, a comparison table and a proposed EUDIT canonical form. ([Process 4](#process-4--overlap-resolution-via-the-transmodel-anchor))
3. **Conformance level specification** — the exact L0/L1/L2 definitions and the L1 scope per use case. ([Process 3](#process-3--conformance-level-framework))
4. **RFC governance charter** — roles, voting rules, change classification definitions, notice periods. ([Process 5](#process-5--governance-rfc-and-cross-standard-impact-assessment))
5. **Reference adapter plan** — tooling choices, hosting, and a delivery timeline. ([Process 6](#process-6--reference-adapter-library))
6. **First draft TS outline** — section structure and scope of the Technical Specification to be hammered in Phase 3.

---

## Agenda

[TO DISCUSS / COMPLETE] — logistics (who, where, budget) to be confirmed.

The Forge is planned as a **4-day physical meeting** of a small expert group (≤7 persons at the table).

### Day 1 — Requirements

* Present use-case delta analysis (pre-prepared)
* Agree on the Gap / Covered / Partial classification for each use case
* Agree on the conformance level framework (L0/L1/L2)

### Day 2 — Overlap Resolution

* Work through concept comparison tables for the highest-traffic overlaps (offer/booking, ticket/travelDocument, identity/account)
* Draft EUDIT canonical forms for each resolved overlap

### Day 3 — Governance and Tooling

* Define the RFC governance charter
* Agree reference adapter plan and tooling choices
* Draft the first TS section outline

### Day 4 — Validation and Wrap-up

* Review all deliverables against the [basic principles](../../wiki/basic-principles/README.md) checklist
* Identify open issues for Phase 3 (The Anvil)
* Assign ownership of each open issue
* Confirm The Anvil schedule
