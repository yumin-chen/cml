---
Version: 0.0.1
Date: 2025-01-19
---

# CML Architecture Design Document

**CML (Computation Modelling Language)** is a component-driven meta-programming code-as-data markup language that declaratively states what the modelled computation does. 

This architecture codifies one of its implementation as a Multi-Stage Programming (MSP) framework that treats *"Code"* as immutable, structurally-typed, content-addressed, and context-dependent values.

## Executive Summary

CML is a closed, deterministic algebra for representing executable semantics as pure data. Unlike traditional languages that store logic as text, CML stores logic as a Merkle-DAG and state as a Ledger of Ref-Transitions. This ensures that the logic is immutable and verifiable, while the system remains dynamic and collaborative. Its cannonical representation is a subset and an extension of the standard XML.

**Key Traits:**
- **Content-Addressed Logic**: Code stored as immutable Merkle-DAG with CID identity
- **Distributed State Management**: Git-like ref namespace for collaborative state
- **Reconciliation Engine**: Desired vs Observed feedback loop for dynamic systems
- **Triple-Projection Storage**: Human, machine, and database interoperability

---

## Why CML Exists

> CML fills the gap in the Code as Data systems landscape: Stable meaning with immutable identity and conflict-free evolution.

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

## The 4-Layer MOF Stack

CML extends the standard Meta-Object Facility (MOF) to account for decentralised state:

| Layer | Domain | Responsibility | Stability |
|-------|--------|----------------|-----------|
| **M3** | **Meta** | PKL Schemas defining the rules of CML | Constant |
| **M2** | **Language** | Component Definitions (e.g., MathOp, Unit) | Versioned |
| **M1** | **Logic** | The Merkle-DAG of specific logic instances | Immutable (CID) |
| **M4** | **State** | The Git-Ref namespace mapping Names to CIDs | Fluid (DID) |

### Layer Descriptions

#### M3 — Meta Layer (Constant)
- **Domain**: Meta-programming schemas
- **Responsibility**: PKL schemas that define the structural rules of CML
- **Stability**: Constant - foundational rules that rarely change
- **Example**: Base class definitions, semantic annotation rules

#### M2 — Language Layer (Versioned)  
- **Domain**: Component type definitions
- **Responsibility**: Specific CML component types (MathOp, Unit, Literal, etc.)
- **Stability**: Versioned - evolves with language specification
- **Example**: `Unit`, `BinOp`, `Literal` component definitions

#### M1 — Logic Layer (Immutable CID)
- **Domain**: Specific computational instances
- **Responsibility**: The Merkle-DAG of actual logic instances
- **Stability**: Immutable - content-addressed with CID identity
- **Example**: A specific `<Unit name="add">...</Unit>` with CID `b3a123...`

#### M4 — State Layer (Fluid DID)
- **Domain**: Named references and collaborative state
- **Responsibility**: Git-Ref namespace mapping names to CIDs
- **Stability**: Fluid - mutable references managed by DIDs
- **Example**: `refs/cml/desired/tax-rate → CID_987`

## Storage & Data Rendering

CML uses a **Triple-Projection strategy** to ensure interoperability across human, machine, and database environments.

### M1 Blob Store (Merkle-DAG)
- **Format**: `pklbinary` (deterministic MessagePack)
- **Indexing**: BLAKE3 Content-Identifier (CID)
- **Invariant**: Only `@Semantic` children are hashed. Metadata (M4) is excluded to maintain CID stability

### M4 Relational Layer (PostgreSQL/SQLite)
- **Ref Table**: Maps `did + ref_path` to `target_cid`
- **Assertion Log**: An append-only table of signed transitions (the COB ledger)
- **Interop**: Uses BYTEA for Postgres and BLOB for SQLite to store M1 payloads

### Recursive Identity Calculation
Every CML node's identity is calculated as:

```
CID = BLAKE3(type.cid + children.cids)
```

If the node is a `CmlPrimitiveNode` (Leaf), the recursion terminates by hashing the raw value.

## The Reconciliation Engine

CML manages "Dynamic Reality" through a **Desired vs. Observed feedback loop**.

### State Definitions
- **Desired State**: The "Intent" authored by a user/controller, stored in `refs/cml/desired/`
- **Observed State**: The "Reality" witnessed by an M0 agent, stored in `refs/cml/observed/`
- **Status/Delta**: The computed difference between Intent and Reality

### The Reconciliation Node
A specialised M₁ node that takes two M₄ pointers as input:

```
Status = Logic(DesiredRef, ObservedRef)
```

This enables continuous reconciliation between intended state and actual system state, making CML suitable for:
- Infrastructure as Code
- Continuous Deployment
- System Monitoring
- Collaborative Development

## Implementation Specifications

### Semantic Promotion
The M2 layer (PKL) is responsible for "promoting" XML attributes into Merkle child nodes:

**Input**: `<Literal value="10" author="Alice" />`
- **M1 Process**: `value` is wrapped in a `CmlPrimitiveNode` and hashed
- **M4 Process**: `author` is written to the SQL `m4_assertions` table

This ensures clean separation between semantic content (hashed) and metadata (not hashed).

## Git Interoperability (The Social Layer)

CML uses the **Git refs/ namespace** as a decentralised symbol table:

- **Namespace**: `refs/cml/`
- **Heads**: `heads/main` points to the latest root CmlManifest
- **COBs**: `cobs/{uuid}` tracks the head of collaborative objects (issues, PRs, state logs)

This enables decentralised collaboration without requiring central coordination.

### Layer Mapping

| Layer | Entity | Identity | Responsibility |
|-------|--------|----------|----------------|
| **M4** | CML State | DID + RefPath | State: Named references to current CIDs |
| **M3** | Meta-CML | Schema | Meta: PKL schemas defining CML rules |
| **M2** | CML Language | Version | Language: Component definitions |
| **M1** | CML Logic | CID | Logic: Immutable computational expressions |
| **M0** | Runtime | Pointer | Event: CPU executing the resolved mathematics |

## Strategic Road Map

### Phase 1: PKL Foundation
- Finalise PKL M₃ base classes and `@Semantic` annotation
- Implement semantic promotion for XML attributes
- Build core Merkle-DAG infrastructure

### Phase 2: Storage Layer
- Build the SqlRenderer for Postgres/SQLite binary interoperability
- Implement M1 blob store with BLAKE3 indexing
- Create triple-projection storage system

### Phase 3: State Management
- Implement the Git Ref-Watcher to sync M₄ state into the relational database
- Build reconciliation engine for Desired vs Observed state
- Add COB (Collaborative Object) support

### Phase 4: Analysis & Tooling
- Develop the Panda.js Static Analysis suite for "Delta" detection across repositories
- Build collaborative development tools
- Create distributed truth verification systems

## Practical Example: Multi-Stage Tax Calculator

**M3 (Meta)**: PKL schema defines how tax components are structured
**M2 (Language)**: `TaxCalculator` component type with semantic rules
**M1 (Logic)**: Specific tax logic `Amount * 0.21` with CID `b3a123...`
**M4 (State)**: `refs/cml/desired/tax-rate → CID_b3a123...` enables collaborative updates

The reconciliation engine continuously compares:
- **Desired**: `refs/cml/desired/tax-rate` (what the policy should be)
- **Observed**: `refs/cml/observed/tax-rate` (what the system is actually using)
- **Status**: Computed delta showing any drift between intent and reality
## 🔑 Key Insight: CML is a "Model of Computation," Not a "Mechanism of Execution"

In traditional programming (C++, Java), an executable is a file where the meaning of the code and the instructions for the CPU are smashed together into a single blob of binary.

**In CML, we decouple them.** CML artifacts are pure data that model computation - they don't execute themselves.

### The M0/M1 Separation

| Component | Layer | Nature | Analogy |
|-----------|-------|--------|---------|
| **CML Node** | M1 | A static, immutable Merkle-tree (CID) | A blueprint for a car |
| **Interpreter** | M0 | A live process that "reads" the tree | A person following the blueprint to build/move the car |
| **Compiler** | M0 | A process that "translates" the tree into Machine Code | A factory that turns the blueprint into a finished physical car |

### The CML Runtime Interpreter (M0)

The Interpreter is a **Universal Reader** - a program (likely written in Go, Rust, or Zig) that performs a Tree-Walk:

1. **Input**: A CID (the "entry point" of the M1 model)
2. **Process**: 
   - Resolve the CID to its CML Value
   - Look at the nodeType (M2)
   - Perform the logic associated with that type (e.g., if BinOp(ADD), find the children and add them)
3. **Lifecycle**: Lives as a running process in memory with "State" that is not part of CML (clock time, stack pointer)

### The CML Static Compiler (M0)

The Compiler is a **Translator** - it doesn't "run" the logic; it transforms the Merkle-DAG into CPU-specific format:

1. **Input**: A CID
2. **Process**: Traverses the M1 graph and emits machine instructions that represent the same algebraic truth
3. **Output**: A non-CML artifact (an .exe or .wasm file)

## 🛡️ "CML is not executable" — Clarifying the Paradox

When we say "CML is not executable," we mean **the data structure itself has no agency**:

1. **Safety**: A CML file sitting on your disk can never "harm" your computer because it's just a value (like a JSON file). It only "does" something when an Interpreter decides to process it.

2. **Portability**: Because CML is just a model of computation, you can write one Interpreter in Python and another in Rust. They will both produce the exact same result because the M1 Value is the single source of truth.

**Example**: Think of an SVG file. Is an SVG "executable"? No. It's just a text file (M1). But when a Browser (M0 Interpreter) reads that text, it "executes" the drawing commands to show you a picture. **CML is like "SVG for Logic."**

## 🧠 Why "Computation Modeling Language" Makes Sense

The computation is executable in principle, but the artifact is pure data:

- **Logic (M1)**: `f(x) = x + 1` (This is a static fact)
- **Computation (M0)**: The CPU actually flipping bits to turn 5 into 6

By keeping the "Model" (M1) separate from the "Execution" (M0), you gain **Merkle-Stability**:
- You can sign the model
- Cache it  
- Verify it across a network
- All without ever needing to run it

You only "Realise" the computation at the last possible second.

## 🌟 Summary: The "Living" System

- **CML (M1)**: The "Spirit" / The Logic (Immutable CID)
- **Interpreter/Compiler (M0)**: The "Body" / The Physicality (Transient Process)

---

### M1–M3 — The CML Vertical Stack

* **M3 (Meta-CML)**: The grammar that defines component structure
* **M2 (CML Language)**: The vocabulary of specific component types
* **M1 (CML Documents)**: The user's semantic models
* immutable, content-addressed, authorless, timeless

**The Compiler/Canonicaliser lives at the M2/M3 boundary** - it uses M3 rules (Reflection) to read M1 data and produce M0 binaries.

---

### M4 — Assertions & History

* append-only
* attributed (DID)
* time-aware
* conflict-free

Git, CRDT logs, and audit trails live here.

---

## What CML Is (Corrected MOF Positioning)

**CML operates as a vertical stack across M3, M2, and M1 layers:**

### The Three Layers of CML

#### M3 — Meta-CML (The "DNA")
- **Abstract Base Class**: `CmlComponent` defines universal "Rules of Engagement"
- **Structural Rules**: Every component must have children (Identity) and meta (Context)
- **PKL Implementation**: Uses PKL to define how CML language constructs are built

#### M2 — CML Language (The "Vocabulary")
- **Standard Library**: `cml.Unit`, `cml.BinOp`, `cml.Literal`, `cml.Var`
- **Component Classes**: Templates used to instantiate actual models
- **Language Definition**: The metamodel that defines available constructs

#### M1 — CML Documents (The "Code")
- **User Applications**: MDX files with `<Unit name="add">...</Unit>`
- **Specific Instances**: Each with unique CID (Content Identifier)
- **Semantic Models**: The actual programs developers write

### CmlComponent: MDX Component for Merkle Environments

In the MDX ecosystem, a Component transforms props into output. In CML, a **CmlComponent** is the M3-level formalisation for content-addressed, computational environments.

#### The Three Sectors of CmlComponent

| Sector | Purpose | Hashing | Role |
|--------|---------|---------|------|
| **Hashed Body (Children)** | Core identity | ✅ Hashed | If children change, CID changes |
| **Non-Hashed Metadata (Props)** | Tooling hints, social context | ❌ Not Hashed | M4 metadata without altering M1 truth |
| **tagType (FQN)** | Type identity | ✅ Hashed | Ensures different types have different CIDs |

CML is:

* a **closed algebra** of computation nodes (M2 metamodel)
* expressed as **pure data** (M1 models)
* serialisable into **canonical form** (deterministic representation)
* deterministically **hashable** (content-addressed identity)
* free of time, identity, and side effects (M3 invariants)

Each CML node is:

* a **value** (not an action, process, or runtime state)
* defined by the M2 metamodel
* instantiated as M1 models
* with unique CID based on tagType + children content

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

* serialise it
* hash it
* store it
* transmit it
* interpret it later

If it cannot be deterministically serialised and hashed, it is **not M3**.

---

## The Closed Algebra (M2 Metamodel)

**CML at M2 defines a finite, closed set of tagType definitions.**
No user-defined node kinds are allowed at the metamodel level.

### Core tagTypes (M2 Language Constructs)

* **Unit** — callable computation tagType
* **Block** — sequential composition tagType
* **Expression** — value-producing term tagType
* **Loop** — structural repetition tagType
* **Call** — function application tagType
* **Match** — pattern-based branching tagType
* **Type** — classification of values tagType
* **Constraint** — logical restriction tagType

Each tagType extends the M3 CmlComponent base class and provides:
- Unique FQN (Fully-Qualified Name) for type identity
- Specific semantic rules for children composition
- Deterministic CID calculation based on tagType + children

This M2 metamodel is sufficient to represent:

* ASTs (M1 models)
* IRs (M1 models)  
* SSA-like forms (M1 models)
* bytecode-level semantics (M1 models)

### Type Identity Guarantee

A `<Literal value="10"/>` and a `<Constant value="10"/>` have different CIDs because their M2 tagType (FQN) is different, even with identical content.

---

## XML as a Data Substrate

CML uses a **restricted, canonicalised subset of XML**:

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
CID = hash(canonical_serialisation(node))
```

This guarantees:

* referential transparency
* deduplication
* semantic stability across repos

---

## Why Git is M4 and not Meta-CML

**Git operates at M4** because it tracks the **History** of the M3/M2/M1 layers:

* **Meta-CML (M3)**: Says "A CML node must be immutable"
* **Git (M4)**: Says "Alice changed the M3 definition of a CML node on Tuesday"

Git provides the **Provenance** (the timeline), while Meta-CML provides the **Essence** (the structure). 

If Meta-CML were Git, then the rules of your language would change every time you made a commit. By keeping them separate:

* **Language Definition (M3)** remains a stable algebraic truth
* **Git (M4)** handles the messy human reality of versioning it

### MOF Layer Separation Benefits

This strict MOF layering ensures:

* **M3 (Meta-CML)**: Stable grammar for defining languages
* **M2 (CML)**: Stable language definition  
* **M1 (CML Documents)**: Stable semantic content
* **M0 (Runtime)**: Execution without affecting meaning
* **M4 (History)**: Evolution without breaking identity

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
* share computation across organisations
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

* Canonical serialisation
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

Each CML node SHALL have a deterministic content identifier derived exclusively from its canonical serialised form.

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

Two CML nodes SHALL be considered equal if and only if their canonical serialised forms are identical.

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

CML solves this with deterministic canonical serialisation and Merkle hashing.

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
| Poor diff/merge | Yes (canonical serialisation) |
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
- canonical serialisation
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
