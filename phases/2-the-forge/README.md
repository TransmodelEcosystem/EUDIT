# The Forge

## Purpose

The Forge is a focused, time-boxed work phase in which a small group of experts produces the **first draft EUDIT OpenAPI specification**. Its goal is to answer the central question:

> **How do we harmonise OSDM, TOMP-API, BoB, FerryGateway, OMSA and InterMOD into a single Transmodel-compliant interface — without invalidating the investments already made by implementors?**

The Forge takes the foundation laid in [The Kidling](../1-the-kidling/README.md) — scope, principles, roles, use cases, user actions, and semantic mappings — and turns it into a concrete draft OpenAPI YAML that can be hammered into a full specification during [The Anvil](../3-the-anvil/README.md).

---

## Primary Deliverable: Draft OpenAPI Specification

The single output that The Forge must produce is a draft OpenAPI specification (`eudit-draft.yaml`). Everything else — the analysis tables, the overlap resolutions, the conformance decisions — are working artefacts that feed into writing that file.

The draft spec is structured around the [user actions](../../wiki/user-actions/README.md):

* Each user action becomes one or more API **operations** (paths + HTTP verbs).
* Each schema is the **canonical Transmodel-aligned form** resolved from the overlapping standards.
* All schemas carry `x-semantics` annotations referencing the Transmodel concepts established in Phase 1.
* Standard-specific fields that do not belong in the canonical form are placed in clearly marked **extension blocks** (`x-<standard>-extension`).

The draft does not need to be complete or perfect. It needs to be specific enough that Phase 3 (The Anvil) can iterate on it biweekly.

---

## Core Strategy: Semantic Alignment over Protocol Replacement

The key insight that guides all Forge work is:

**Implementors do not replace their stack. They expose a thin EUDIT facade grounded in a shared Transmodel vocabulary.**

This directly satisfies the principles of *"With respect to already made investments"*, *"Short Return on Investment"* and *"Low threshold for adoption"* from [Basic Principles](../../wiki/basic-principles/README.md):

* Existing OSDM, TOMP-API and BoB deployments continue to function unchanged.
* Compliance is achieved by wrapping an existing implementation behind EUDIT endpoints — not by rewriting core systems.
* The Transmodel anchor ensures all standards speak the same semantic language, even when their wire formats differ.

### Conformance levels

To make adoption incremental, the spec defines three conformance levels:

| Level | Name | Requirement | Typical effort |
|-------|------|-------------|----------------|
| **L0** | Semantically Mapped | Publish a semantic mapping from your existing API to Transmodel | Hours |
| **L1** | EUDIT-Facade | Expose EUDIT-defined endpoints wrapping your existing implementation | Days to weeks per use case |
| **L2** | EUDIT-Native | Implement the EUDIT information model natively | Significant new build |

CEN TS compliance requires **L1** for the in-scope use cases. L0 is the baseline for ecosystem participation.

[TO DISCUSS / COMPLETE] — exact L1 scope per use case to be agreed during The Forge.

---

## Working Methods

The Forge uses four working methods to produce the draft spec. These are means to an end, not deliverables in themselves.

### Method 1 — Use-Case Driven Scoping

Before writing any schema, establish what the spec must cover. For each of the [7 CoRoM use cases](../../wiki/use-cases/README.md), walk through every step and classify it:

| Classification | Meaning | Action |
|----------------|---------|--------|
| **Covered** | ≥2 standards handle it compatibly | Adopt the most Transmodel-aligned formulation directly |
| **Partially covered** | Standards handle it differently | Resolve via Method 2 before writing the schema |
| **Gap** | No standard handles it adequately | Design the schema from the Transmodel concept up |

This produces a scoping table that drives Day 1 of the meeting.

### Method 2 — Overlap Resolution via the Transmodel Anchor

For every concept covered by more than one standard, resolve to a single canonical form before writing it into the spec. Use the following comparison table:

| Attribute | OSDM field | BoB field | TOMP-API field | Transmodel attribute | In EUDIT canonical? |
|-----------|-----------|---------|--------------|---------------------|---------------------|
| Identifier | `offerId` | `productSetId` | `id` | `id` | Yes — mandatory |
| Validity start | `validFrom` | `startOfValidity` | — | `fromDate` (VALIDITY CONDITION) | Yes — mandatory |
| … | … | … | … | … | … |

The canonical form is the **minimal mandatory subset** that covers all in-scope use cases. Standard-specific fields that fall outside it become extension points in the schema.

The Phase 1 `x-semantics` annotations in [`wiki/specifications/`](../../wiki/specifications/) are the starting point — every concept is already anchored.

### Method 3 — User Action to Endpoint Mapping

Each [user action](../../wiki/user-actions/README.md) maps to one or more operations in the draft spec. Work through the user actions in use-case order:

1. Identify the HTTP verb and path that expresses the user action.
2. Identify the request and response schemas (using Method 2 output).
3. Write the operation into `eudit-draft.yaml`.
4. Annotate the schemas with `x-semantics`.

This is the main writing activity of Days 2 and 3.

### Method 4 — Governance: RFC and Impact Assessment

Any decision made during The Forge that changes a concept already mapped in Phase 1 must be recorded as a change decision with a rationale. This forms the seed of the RFC governance process that activates in Phase 3.

Change classifications:
* **Non-breaking** — new optional field or extension point.
* **Backward-compatible** — existing field renamed or restructured; L1 adapters need updating.
* **Breaking** — existing SMD invalidated; requires full group agreement and 6-month notice after TS publication.

[TO DISCUSS / COMPLETE] — governance body composition and voting rules to be agreed.

---

## Preparation (before the meeting)

To make the 4 days productive, the following must be prepared in advance:

* **Scoping table pre-fill** — one person walks the 7 use cases against the functional comparison matrix and produces a first-pass Covered / Partial / Gap classification.
* **Top-10 overlap list** — identify the 10 most-contested concepts across all standards (expected: offer/booking, ticket/travelDocument, identity/account, fare/price, stop/place, journey/ride, validation event).
* **Stub OpenAPI file** — create `eudit-draft.yaml` with the OpenAPI 3.0 header, info block, tag list (one tag per use case), and empty paths and components sections. Participants write into this file during the meeting.

---

## Deliverables

| # | Deliverable | Owner | Notes |
|---|-------------|-------|-------|
| 1 | **`eudit-draft.yaml`** — draft OpenAPI specification | All | Primary output; must be specific enough for Phase 3 iteration |
| 2 | Scoping table — Covered / Partial / Gap per use case | Pre-prepared | Working artefact; included in spec appendix or wiki |
| 3 | Overlap resolution decisions — one row per resolved concept | Working artefact | Recorded as change decisions (seed of RFC log) |
| 4 | Conformance level definition — L0/L1/L2 scope per use case | Agreement | Written into the spec's conformance section |
| 5 | Open issues list for Phase 3 | Day 4 output | Filed as GitHub Discussions |

---

## Agenda

[TO DISCUSS / COMPLETE] — logistics (who, where, budget) to be confirmed.

The Forge is planned as a **4-day physical meeting** of a small expert group (≤7 persons at the table).

### Before Day 1 (preparation)

* Scoping table pre-filled
* Top-10 overlap list prepared
* `eudit-draft.yaml` stub created with OpenAPI header, tags, and empty sections

### Day 1 — Scope and Data Model

* Walk the scoping table; agree on all Covered / Partial / Gap classifications
* Agree the conformance level framework and L1 scope
* Resolve the top-10 overlapping concepts (Method 2)
* Write the `components/schemas` section of `eudit-draft.yaml` for the resolved concepts

### Day 2 — Endpoints: Discovery and Offers

* Map user actions in the *search* and *offer* use cases to API operations (Method 3)
* Write the corresponding paths into `eudit-draft.yaml`
* Resolve any schema gaps that emerge

### Day 3 — Endpoints: Booking, Fulfilment and Inspection

* Map user actions in the *booking*, *fulfilment*, *validation* and *account* use cases to API operations
* Write the corresponding paths into `eudit-draft.yaml`
* Resolve remaining schema gaps

### Day 4 — Review and Handover

* Review `eudit-draft.yaml` end-to-end against the [basic principles](../../wiki/basic-principles/README.md) checklist
* Verify every schema has an `x-semantics` annotation
* Compile the open issues list for Phase 3
* Assign ownership of each open issue
* Confirm The Anvil schedule and cadence

---

## How The Forge fits in the overall timeline

```
Phase 1 — The Kidling (complete)
  └─► Scope, principles, use cases, user actions,
      semantic mappings (x-semantics) for all 6 standards

Phase 2 — The Forge  ◄── you are here
  └─► eudit-draft.yaml  (draft OpenAPI specification)

Phase 3 — The Anvil
  └─► Biweekly iteration on eudit-draft.yaml
      → full specification + conformance test suite

Phase 4 — The Presenting
  └─► CEN ballot submission

Phase 5 — The Tempering
  └─► Feedback incorporation → final TS
```
