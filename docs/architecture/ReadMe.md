---
Version: 0.0.1
Date: 2025-01-19
---

# CML

> CML freezes computation into data, so meaning can be shared without coordination.

**CML (Computation Modeling Language)** is a closed, deterministic algebra for representing executable semantics as pure data. CML values are immutable, content-addressed, and context-independent. CML defines what something is, not its execution or history.

CML is a semantic substrate that brings deterministic meaning, stable identity, and conflict-free evolution to the data modeling world, unifying principles from semantic web, event sourcing, and distributed version control into a single coherent architecture.**

CML is a pure data language for representing executable meaning as immutable structure.

It is designed to interoperate with **content addressing (CID)**, **append-only history (M4)**, and **conflict-free distributed systems**, without mixing concerns.

---

## Why CML Exists

> CML fills a real gap in the data systems landscape: Stable meaning with immutable identity and conflict-free evolution.

It solves:
- reproducibility
- merging
- deduplication
- history
- cross-repo semantics

Without requiring:
- central coordination
- runtime execution
- language binding

Modern systems conflate three fundamentally different things:

1. **Meaning** (what something *is*)
2. **Execution** (what is *happening now*)
3. **History** (who said what, and when)

This conflation makes:

* reproducibility fragile
* distribution hard
* “truth” mutable
* semantics unstable

CML exists to **separate these concerns cleanly**.

---

## The Layer Model (M0–M4)

CML is built around a strict ontological layering:

```
M0 — Runtime
M1 — Semantic primitives
M2 — Structural relations
M3 — Executable meaning (CML)
M4 — Assertions & history
```

**A layer is a conceptual domain with its own invariants. Higher layers may reference lower layers but must not alter their invariants.**

CML formalizes **M3**: the layer where computation exists as *data*, not as runtime, process, or state.

### M0 — Runtime

* CPU state
* memory
* processes
* clocks
* IO

**Not data. Not hashable. Not reproducible.**

---

### M1–M3 — Static Semantic Space

* immutable
* content-addressed
* authorless
* timeless

This is where **CML lives**.

---

### M4 — Assertions & History

* append-only
* attributed (DID)
* time-aware
* conflict-free

Git, CRDT logs, and audit trails live here.

---

## What CML Is

CML is:

* a **closed algebra** of computation nodes
* expressed as **pure data**
* serializable as **canonical XML**
* deterministically **hashable**
* free of time, identity, and side effects

Each CML node is:

* a **value**
* not an action
* not a process
* not runtime

---

## What CML Is Not

CML does **not**:

* execute code
* model runtime state or execution semantics
* perform IO
* encode authorship or timestamps
* resolve conflicts
* define governance

Those belong to **M0** (execution) or **M4** (history).

---

## Core Idea: Computation as Data

In CML:

> A computation is a *thing*, not an *event*.

You can:

* serialize it
* hash it
* store it
* transmit it
* interpret it later

If it cannot be deterministically serialized and hashed, it is **not M3**.

---

## The Closed Algebra (M3)

CML defines a **finite, closed set of node types**.
No user-defined node kinds are allowed at M3.

### Core Nodes

* **Unit** — callable computation
* **Block** — sequential composition
* **Expression** — value-producing term
* **Loop** — structural repetition
* **Call** — function application
* **Match** — pattern-based branching
* **Type** — classification of values
* **Constraint** — logical restriction

This algebra is sufficient to represent:

* ASTs
* IRs
* SSA-like forms
* bytecode-level semantics

---

## XML as a Data Substrate

CML uses a **restricted, canonicalized subset of XML**:

* no mixed content
* no insignificant whitespace
* deterministic attribute ordering
* structural meaning only

XML is used for:

* human readability
* tooling interoperability
* lossless structural encoding

The *meaning* of CML is defined by its algebra, not by XML itself.

---

## Identity and Hashing

CML nodes have:

* **no intrinsic identity**
* **no global names**
* **no authors**

Identity is derived solely from content:

```
CID = hash(canonical_serialization(node))
```

This guarantees:

* referential transparency
* deduplication
* semantic stability across repos

---

## Why There Is No DID in CML

Author identity, timestamps, and intent are **not part of meaning**.

Adding them would:

* destabilize hashes
* fragment semantics
* break interoperability

Authorship belongs to **M4**, where assertions are recorded *about* CML nodes.

---

## Relation to Git and CRDTs

* **Git** is an M4 system: an append-only log of assertions
* **CRDTs** are M4 merge strategies

CML does **not replace Git**.
It provides a **stable semantic substrate** that Git-like systems can reference.

In other words:

> Git tracks *who said what*.
> CML defines *what was said*.

---

## Why RDF/CRDT Is Not Enough

RDF/CRDT systems mix:

* semantic data
* provenance
* conflict metadata

This causes:

* unstable identities
* semantic drift
* poor reusability

CML avoids this by:

* keeping M3 pure
* pushing all history to M4
* preserving stable content identity

---

## Design Invariants

CML enforces the following invariants:

1. **Immutability** — nodes never change
2. **Determinism** — same content → same hash
3. **Purity** — no side effects
4. **Closedness** — finite node algebra
5. **Separation of concerns** — meaning ≠ history ≠ runtime

---

## What CML Enables

With CML, you can:

* build **truth databases** without lying
* share computation across organizations
* deduplicate semantics globally
* reason about code as data
* replay, verify, and audit meaning
* scale without coordination

---

## Non-Goals

CML explicitly does **not** aim to be:

* a programming language
* a runtime
* a package manager
* a blockchain
* a governance system

**Explicit Non-Goals:**

* **No evaluation semantics**: CML does not define order of evaluation or execution strategy
* **No runtime effects**: CML cannot represent side effects like IO

Those can be built *on top*.

---

# 1. What M3 *Is* (Formal Restatement)

> **M3 is a closed, deterministic algebra of computation and structure expressed purely as immutable data.**

CML is the concrete syntax of M3.

M3 describes **what something is**, not:

* who created it
* when it was created
* why it exists
* how it evolved

Those belong to M4.

---

# 2. The M3 Invariants (Authoritative List)

These are **hard constraints**.
If any are violated, you are no longer in M3.

---

## Invariant 1 — Immutability

**Definition**

> An M3 node, once defined, can never change.

**Consequences**

* No updates
* No deletes
* No versioning
* No patching

**Codified in CML**

* Every node is content-addressed
* Identity = hash(content)
* Any change produces a new node

```text
Node₁ ≠ Node₂ if any field differs
```

---

## Invariant 2 — Deterministic Identity (CID)

**Definition**

> The identity of an M3 node is a pure function of its structure and values.

```text
CID = hash(canonical(node))
```

**Consequences**

* Same content → same CID globally
* Different content → different CID always
* No randomness
* No timestamps
* No author fields

**Codified in CML**

* Canonical serialization
* Stable field ordering
* Hash excludes metadata

---

## Invariant 3 — No Time

**Definition**

> M3 has no notion of time, order, or sequence.

**Consequences**

* No timestamps
* No “before / after”
* No history
* No “latest version”

**Codified in CML**

* No temporal fields allowed
* Ordering is structural only (lists)

---

## Invariant 4 — No Identity (No DID)

**Definition**

> M3 nodes have no owner, author, or authority.

**Consequences**

* No signatures
* No permissions
* No trust
* No provenance

**Codified in CML**

* No `actor`, `author`, `did`, `signature` fields
* All nodes are anonymous values

---

## Invariant 5 — Referential Transparency

**Definition**

> A node always means the same thing everywhere it appears.

**Consequences**

* No context sensitivity
* No environment dependence
* No global state

```text
Meaning(node, context₁) = Meaning(node, context₂)
```

**Codified in CML**

* No implicit imports
* All references are explicit by CID or symbol resolution
* No ambient scope

---

## Invariant 6 — Closed World (Total Semantics)

**Definition**

> Every valid CML document is fully interpretable without external information.

**Consequences**

* No network access
* No filesystem access
* No runtime lookup
* No late binding

**Codified in CML**

* Types, Units, Constraints fully declared
* External references only via explicit CIDs (imports are M4)

---

## Invariant 7 — Value Semantics Only

**Definition**

> Every M3 node is a value, not an action or process.

**Consequences**

* No execution
* No side effects
* No mutation
* No I/O

**Codified in CML**

* `Unit`, `Block`, `Expression` describe structure, not behavior
* “Calling” is structural, not operational

---

## Invariant 8 — Structural Equality

**Definition**

> Equality is structural, not nominal. Structural equality must treat variable binding and names as alpha-equivalent.

```text
A == B ⇔ canonical(A) == canonical(B)
```

**Consequences**

* No pointer identity
* No object identity
* No hidden fields
* Variable names do not affect CID identity

---

# 3. M3 Invariants Codified as CML Rules (Spec Text)

Below is **ready-to-drop-in specification language**.

---

## CML Core Invariants

### §1. Immutability

All CML nodes are immutable.
Any modification to a node SHALL result in a new node with a new content identifier (CID).

---

### §2. Deterministic Identity

Each CML node SHALL have a deterministic content identifier derived exclusively from its canonical serialized form.

Metadata, comments, whitespace, and external context SHALL NOT affect identity.

---

### §3. Absence of Time

CML SHALL NOT contain any temporal constructs including timestamps, ordering metadata, or version markers.

---

### §4. Absence of Identity

CML SHALL NOT encode authorship, ownership, authority, or provenance information.

Such information SHALL be expressed exclusively in M4.

---

### §5. Referential Transparency

The semantic meaning of a CML node SHALL be invariant under relocation, duplication, or reuse.

---

### §6. Closed Semantic World

All semantics required to interpret a CML document SHALL be contained within the document itself or referenced by immutable identifiers.

---

### §7. Value Semantics

All CML constructs represent values.

No construct SHALL represent execution, side effects, or state transitions.

---

### §8. Structural Equality

Two CML nodes SHALL be considered equal if and only if their canonical serialized forms are identical.

---

# 4. Mapping Invariants to CML Constructs

| CML Construct | M3 Meaning                          |
| ------------- | ----------------------------------- |
| `Unit`        | Value describing callable structure |
| `Block`       | Value describing composition        |
| `Expression`  | Value describing computation        |
| `Loop`        | Structural recursion                |
| `Call`        | Graph edge, not execution           |
| `Match`       | Declarative branching               |
| `Type`        | Constraint over values              |
| `Constraint`  | Logical restriction                 |

None of these **do** anything.
They **describe** something.

---

# 5. Explicit Non-Goals of CML (Important)

CML explicitly does **not** model:

* execution
* scheduling
* concurrency
* effects
* processes
* threads
* users
* versions
* histories

**Explicit Non-Goals:**

* **No evaluation semantics**: CML does not define order of evaluation or execution strategy
* **No runtime effects**: CML cannot represent side effects like IO

Those are **M0 or M4 concerns**.

---

# 6. One-Line Definition (Canonical)

> **CML is a deterministic, immutable, time-free algebra of computation represented entirely as values.**

---

# 7. Why This Matters (Architectural Payoff)

Because M3 obeys these invariants:

* CIDs are globally stable
* deduplication is trivial
* caching is perfect
* merging is lossless
* meaning never drifts
* history never pollutes semantics

This is why **M4 must exist** — and why it must be separate.

---

# CML in the "Code as Data" Systems Landscape

## Current Landscape: Code as Data / No/Low-Code / Schema-Free Data Modeling

CML enters and improves upon the existing data systems landscape by addressing fundamental gaps in semantic stability and identity.

### Major Categories in the No-Code Space

| Category | Typical Tools | Traits |
|----------|---------------|--------|
| Schema-free JSON stores | MongoDB, CouchBase | Flexible, weak semantics |
| No-code platforms | Airtable, Notion, Retool | Easy UI, shallow semantics |
| Low-code ER modeling | Hasura, Prisma | Strong schema, limited evolution |
| Semantic web | RDF/SPARQL, OWL | Formal semantics, poor tooling |
| Event sourcing | EventStore, Kafka | Append-only events, weak semantics |
| Knowledge graphs | Neo4j, TigerGraph | Rich relationships, no evolution model |

## Common Pain Points and Gripes

### 1. Meaning vs State Confusion
Systems like MongoDB and Airtable store state, not semantic meaning. You get:
- lost context
- overwrites
- no reproducibility

**Why CML matters**: CML separates meaning (static) from state (runtime), so "what it means" is stable.

### 2. Lack of Stable Identity
Most systems identify records by:
- auto-generated IDs
- mutable keys
- implicit names

This creates:
- fragile references
- rename pain
- semantic drift

**Why CML matters**: Content-addressing via CID fixes identity to meaning, not labels.

### 3. Evolution History Is Missing or Shallow
Many platforms:
- drop history
- overwrite changes
- require external auditing

Even git only tracks snapshots, not artifact identity.

**Why CML+M4 matters**: Evolution is made explicit in M4, not conflated with structure.

### 4. Semantic Ambiguity
Schema-free stores lack meaning:
```json
{ "wingspan": 25 }
```
What is that?

CML forces:
- explicit types
- canonical structure
- closed algebra

### 5. Poor Diff/Merge Support
JSON diffs are messy. Merges are conflict prone.

CML solves this with deterministic canonical serialization and Merkle hashing.

### 6. Tooling Fragmentation
Semantic web (RDF/OWL) has formal meaning but poor adoption/UX. No-code has good UX but no semantics.

CML bridges real semantics with practical tooling.

### 7. Execution and Meaning Are Conflated
In many systems (e.g., SQL/ORM):
data + transformation + query + model all blur

CML separates meaning (M3) from execution (engine behavior) clearly.

## Gaps in Existing Systems That CML Addresses

| Gap | CML Capability |
|-----|----------------|
| No immutable semantics | Yes (M3 CML) |
| No clean identity | Yes (CID) |
| No evolution model | Yes (M4 assertions) |
| Poor diff/merge | Yes (canonical serialization) |
| Meaning/state conflation | Separated by design |
| Tool independence | Yes (canonical form) |
| Multi-language semantics | Yes (closed algebra) |

## Pitfalls in the Current Landscape

### 1. RDF Semantics Without Practical Identity
RDF treats meaning but:
- mixes provenance
- lacks canonical identity
- lacks evolution model

### 2. Event Sourcing Without Rich Semantics
Event stores:
- capture history
- but lack semantic models

CML + M4 gives semantics to events.

### 3. Graph DBs Without Canonical Identity
Neo4j and similar are great for relationships but lack:
- canonical serialization
- content hashing
- evolution models

CML fills the identity gap.

### 4. Low-Code / No-Code Mess
Platform limitations:
- lock-in
- weak semantics
- unintended mutability

CML provides:
- strong semantics
- reproducibility
- independent tooling
