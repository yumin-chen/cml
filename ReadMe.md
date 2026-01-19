---
Version: 0.0.2
Date: 2025-01-20
Keywords:
  - Meta-Programming
  - Canonical Serialisation
  - Rust ADT
  - Triple Projection
---

# CML

> The missing *Code-as-Data* framework designed for multi-stage meta-programming that treats *"Code"* as immutable, structurally typed, content-addressed, and context-dependent values.

**CML (Computation Modelling Language)** is a component-driven meta-programming code-as-data markup language that declaratively states what the modelled computation does. 

Syntactically, CML is a subset and an extension of [XML (Extensible Markup Language)](https://en.wikipedia.org/wiki/XML) as its cannonical syntax serialisation format. However, a valid CML document can be carried in various formats of code artefacts, such as: `.cml` or `.xml` or `.html` or `.md` or `.mdx` or `.tsx` or `.ts` or `.jsx` or `.js`, because any CML document can be defined by the dynamic runtime meta component that "compiles" (or executes/evaluates) to a static `CmlElement`. The same <CmlElement> represented in XML can be stored in a Markdown document with YAML frontmatter metadata, or as an MDX document to have JS-in-CML, or as an JSX document to do CML-in-JS, or just pure vanila JavaScript/TypeScript but you use a helper function to `createElement`.

Sound familiar? This gives us the entire component-centric development ecosystem. We can take advantage of frameworks like Panda.js for static analysis and our CmlElement actually becomes the meta-language.

If you want a more technical definition: CML is  a closed, deterministic algebra for representing executable semantics as pure data. Unlike traditional languages that store logic as text, CML stores logic as a Merkle-DAG and state as a Ledger of Ref-Transitions. 

This ensures that:
- **Logic is immutable and verifiable** (content-addressed Merkle-DAG)
- **System remains dynamic and collaborative** (Git-like ref namespace)
- **Truth is distributed** (no central authority required)
- **State is reconcilable** (Desired vs Observed feedback loop)

## The 4-Layer MOF Stack: Multi-Stage Programming Framework

CML extends the standard Meta-Object Facility (MOF) to account for decentralised state management:

| Layer | Domain | Responsibility | Stability |
|-------|--------|----------------|-----------|
| **M3** | **Meta** | PKL Schemas defining the rules of CML | Constant |
| **M2** | **Language** | Component Definitions (e.g., MathOp, Unit) | Versioned |
| **M1** | **Logic** | The Merkle-DAG of specific logic instances | Immutable (CID) |
| **M4** | **State** | The Git-Ref namespace mapping Names to CIDs | Fluid (DID) |

### Storage & Data Rendering: Triple-Projection Strategy

CML uses a **Triple-Projection strategy** to ensure interoperability across human, machine, and database environments:

#### M1 Blob Store (Merkle-DAG)
- **Format**: `pklbinary` (deterministic MessagePack)
- **Indexing**: BLAKE3 Content-Identifier (CID)
- **Invariant**: Only `@Semantic` children are hashed. Metadata (M4) is excluded to maintain CID stability

#### M4 Relational Layer (PostgreSQL/SQLite)
- **Ref Table**: Maps `did + ref_path` to `target_cid`
- **Assertion Log**: An append-only table of signed transitions (the COB ledger)
- **Interop**: Uses BYTEA for Postgres and BLOB for SQLite to store M1 payloads

#### Recursive Identity Calculation
Every CML node's identity is calculated as:

```
CID = BLAKE3(type.cid + children.cids)
```

If the node is a `CmlPrimitiveNode` (Leaf), the recursion terminates by hashing the raw value.

### The Reconciliation Engine: Dynamic Reality Management

CML manages "Dynamic Reality" through a **Desired vs. Observed feedback loop**:

#### State Definitions
- **Desired State**: The "Intent" authored by a user/controller, stored in `refs/cml/desired/`
- **Observed State**: The "Reality" witnessed by an M0 agent, stored in `refs/cml/observed/`
- **Status/Delta**: The computed difference between Intent and Reality

#### The Reconciliation Node
A specialised M₁ node that takes two M₄ pointers as input:

```
Status = Logic(DesiredRef, ObservedRef)
```

This enables continuous reconciliation between intended state and actual system state.

### Git Interoperability: The Social Layer

CML uses the **Git refs/ namespace** as a decentralised symbol table:

- **Namespace**: `refs/cml/`
- **Heads**: `heads/main` points to the latest root CmlManifest
- **COBs**: `cobs/{uuid}` tracks the head of collaborative objects (issues, PRs, state logs)

This enables decentralised collaboration without requiring central coordination.

### Semantic Promotion: M2 Layer Responsibility

The M2 layer (PKL) is responsible for "promoting" XML attributes into Merkle child nodes:

**Input**: `<Literal value="10" author="Alice" />`
- **M1 Process**: `value` is wrapped in a `CmlPrimitiveNode` and hashed
- **M4 Process**: `author` is written to the SQL `m4_assertions` table

This ensures clean separation between semantic content (hashed) and metadata (not hashed).

### M1: Logic Layer - The Merkle-DAG

- **Entity**: **Specific computational trees and expressions**
- **Role**: Pure mathematical logic - the actual computational meaning
- **Identification**: CID (Content Identifier / Hash)
- **Storage**: Immutable Merkle-DAG with content-addressed identity
- **Key Insight**: CML artifacts are pure data that model computation but don't execute themselves

### M4: State Layer - The Git-Ref Namespace

- **Entity**: Named References, Assertions, COB State
- **Role**: Maps names to CIDs, enabling mutable references to immutable logic
- **Identification**: DID + RefName (e.g., `alice/repo/issues/1/status`)
- **Storage**: Relational database with append-only assertion log
- **Key Insight**: Provides collaborative state management without contaminating algebraic truth

## Strategic Road Map

### Phase 1: PKL Foundation
- Finalise PKL M₃ base classes and `@Semantic` annotation
- Implement semantic promotion for XML attributes

### Phase 2: Storage Layer
- Build the SqlRenderer for Postgres/SQLite binary interoperability
- Implement M1 blob store with BLAKE3 indexing

### Phase 3: State Management
- Implement the Git Ref-Watcher to sync M₄ state into the relational database
- Build reconciliation engine for Desired vs Observed state

### Phase 4: Analysis & Tooling
- Develop the Panda.js Static Analysis suite for "Delta" detection across repositories
- Build collaborative development tools

## � Why the Hermetic Seal at M3 is Critical

**M3 Reflexivity Maintains Algebraic Purity:**
- **The Theory**: M3 describes itself using its own rules - the structural "ladder" stops here
- **The Seal**: M3 is the highest layer that deals with pure computation - no social concerns
- **The Benefit**: Any machine running the same M1 CID gets identical results, regardless of social context

**M4+ Handles Social Coordination:**
- **M4 (CML-COB)**: Manages collaborative operations without polluting algebraic truth
- **M5 (User Node)**: Provides actor identity for signing operations
- **The Separation**: Social concerns never leak into the pure mathematical layers

## 🚫 Why We Must Maintain the Seal

Violating the hermetic seal creates "Invariant Collapse":

1. **Algebraic Contamination**: If social concerns leak into M1-M3, mathematical truth becomes context-dependent
2. **CID Instability**: If M1 artifacts contain social metadata, identical logic produces different CIDs
3. **Reproducibility Loss**: If M3 rules depend on social context, the same code behaves differently across systems

## 📐 The Boundary: Algebraic vs Social Truth

| Property | Localised (M1-M3) | Social (M4-M5) |
|----------|-------------------|----------------|
| **Identity** | CID (Hash of the code) | DID (Identity of the actor) |
| **Stability** | Immutable (Algebraic) | Mutable (Historical/Versioned) |
| **Context** | "What is the result of 1+1?" | "Who says 1+1 should be the rule?" |
| **Storage** | Content-Addressed Blobs | Collaborative Object (COB) Logs |
| **Truth Type** | Mathematical/Universal | Social/Contextual |

## Complete System: Pure + Social

With the hermetic seal properly maintained:

- **Pure Algebraic Truth (M1-M3)**: Universal, immutable, context-free mathematical logic
- **Social Coordination (M4-M5)**: Collaborative processes, governance, and actor identity
- **The Bridge**: M4 carries pure M1 logic into social contexts without contamination

### 🔒 M3: Meta-CML (The "Physics" - Hermetic Seal)
- **Entity**: The CmlComponent Base Class / Rust enum system
- **Role**: Self-describing rules that define component structure - the "physics" of modeling
- **Identification**: FQN (Fully Qualified Name) of the Meta-type  
- **Why the seal?** M3 is the highest layer dealing with pure computation - no social concerns
- **Example**: The Rust `CmlNode` enum defining universal component rules

### 🔒 M2: CML Metamodel (The "Grammar" - Pure Algebraic)
- **Entity**: Specific CML node type definitions (e.g., Unit, BinOp, Literal)
- **Role**: The Language Dictionary - defines semantics of mathematical operations
- **Identification**: The TagType name
- **Why pure?** Mathematical operations have universal meaning regardless of social context
- **Example**: The definition of what a `BinOp` is and how it behaves mathematically

### 🔒 M1: CML Artifacts (The "Frozen Snapshot" - **WHERE CML LIVES**)
- **Entity**: **Your specific, content-addressed logic values**
- **Role**: Pure mathematical logic - the actual computational meaning you wrote
- **Identification**: CID (Content Identifier / Hash)
- **Why frozen?** Mathematical truth is immutable - 2+2=4 regardless of who says it
- **Key Insight**: Every CML artifact at M1 is pure algebraic truth with no social metadata
- **Example**: `<Unit name="AddTen">...</Unit>` with CID `b3a123...`

### M0: Runtime (The "Event" - Pure Execution)
- **Entity**: TruffleNode instances, interpreters, compilers
- **Role**: Physical execution of mathematical operations
- **Identification**: Memory Address / Pointer
- **Why separate?** Execution tools (M0) operate on pure mathematical logic (M1) without contaminating it

## The "Self/This" Problem and M4 Solution

### The Problem
Traditional systems cannot represent collaborative, dynamic references within immutable mathematical structures:
- A CID cannot point to "the current price" (mutable)
- A CID can only point to a specific observation of a price (immutable)

### The M4 Solution: Named References
M4 provides a **registry** that maps named references to current CIDs:

```
Ref:CurrentPrice → CID_987
Ref:UserStatus → CID_abc
Ref:IssueTitle → CID_def
```

When CML logic contains `self.amount`, the system:
1. **M1**: Recognizes this as a `CmlReferenceNode` (syntactically complete)
2. **M4**: Looks up `Lookup(Subject: M4_CONTEXT_SELF, Path: ["amount"])` 
3. **Resolution**: Returns the current CID that the named reference points to
4. **M0**: Executes the resolved mathematical logic

### COB (Collaborative Object) Implementation
For collaborative objects like Issues or Tasks:
- **M1**: Contains the computational logic (empty for pure data objects)
- **M4**: Contains the assertion log with operations like `SET_TITLE`, `UPDATE_STATUS`
- **Resolution**: System reduces M4 log to find latest state and returns corresponding M1 node

## M4 SQL Schema for Named References

```sql
-- M4: Named reference registry (Git-like refs)
CREATE TABLE m4_refs (
    did TEXT NOT NULL,             -- Decentralised identifier
    ref_path TEXT NOT NULL,        -- e.g., 'desired/tax-rate'
    target_cid TEXT NOT NULL,      -- Points to current M1 state
    seq_number BIGINT NOT NULL,    -- Incremental version for COB ordering
    PRIMARY KEY (did, ref_path)
);

-- M1: Pure mathematical logic storage
CREATE TABLE m1_blobs (
    cid TEXT PRIMARY KEY,
    node_type TEXT NOT NULL,
    data BYTEA NOT NULL,           -- pklbinary (MessagePack) representation
    created_at TIMESTAMP DEFAULT NOW()
);

-- M4: Assertion log for collaborative objects (COB ledger)
CREATE TABLE m4_assertions (
    id SERIAL PRIMARY KEY,
    did TEXT NOT NULL,             -- Who made the assertion
    ref_path TEXT NOT NULL,        -- What reference was updated
    operation TEXT NOT NULL,       -- SET, UPDATE, DELETE, etc.
    payload_cid TEXT NOT NULL,     -- References m1_blobs.cid
    created_at TIMESTAMP DEFAULT NOW(),
    seq_number BIGINT NOT NULL,
    FOREIGN KEY (payload_cid) REFERENCES m1_blobs(cid)
);
```

### CmlReferenceNode Support

The system supports CmlReferenceNode as a "Variable" that the M0 Runtime fills in by looking at the M4 state:

```rust
#[derive(Debug, Clone, PartialEq, Serialise, Deserialise)]
pub enum CmlNode {
    // ... other variants
    Reference {
        subject: String,    // M4_CONTEXT_SELF or specific DID
        path: Vec<String>,  // ["amount"], ["status"], etc.
    },
}

// M4 Resolution Logic
impl CmlNode {
    pub fn resolve_reference(&self, m4_context: &M4Context) -> Result<CID, ReferenceError> {
        match self {
            CmlNode::Reference { subject, path } => {
                let ref_path = format!("{}/{}", subject, path.join("/"));
                m4_context.lookup_ref(&ref_path)
            },
            _ => Err(ReferenceError::NotAReference)
        }
    }
}
```

### The "Self/This" Problem Solution

When CML logic contains `self.amount`, the system:

1. **M1**: Recognizes this as a `CmlReferenceNode` (syntactically complete)
2. **M4**: Looks up `Lookup(Subject: M4_CONTEXT_SELF, Path: ["amount"])` 
3. **Resolution**: Returns the current CID that the named reference points to
4. **M0**: Executes the resolved mathematical logic

### COB (Collaborative Object) Implementation

For collaborative objects like Issues or Tasks:

- **M1**: Contains the computational logic (empty for pure data objects)
- **M4**: Contains the assertion log with operations like `SET_TITLE`, `UPDATE_STATUS`
- **Resolution**: System reduces M4 log to find latest state and returns corresponding M1 node

Example COB workflow:
1. Look up the DID of the Issue
2. Fetch the M4 Assertion Log (the COB)
3. Reduce the log to find the latest SET_TITLE operation
4. Return the M1 Leaf Node contained in that operation

## 🔑 Why M1 is the "CID Layer"

M1 is the layer where **Content Addressing (CID) becomes the primary citisen**.

- **At M3/M2**: You are defining the shape of the language
- **At M1**: You are using that shape to create a specific **Value**
- **CID Assignment**: Because every CML artifact at M1 is a pure value, it gets a CID
- **Immutability**: If you change a single character, you create a **new** M1 artifact with a different CID

## 📂 The Distinction: M1 (Fact) vs. M4 (Assertion)

| Layer | What Lives Here | Example |
|-------|----------------|---------|
| **M1 (The Artifact)** | `CID_Alpha` — "A function that adds 1." | Timeless, Anonymous, Absolute |
| **M4 (The Assertion)** | `DID_Alice` says "This CID_Alpha is version 1.0 of my math library." | Social context, authorship, versioning |

**The M1 layer is "Blind"** - it doesn't know who created it or why. It only knows its own algebraic truth. This makes CML artifacts perfectly interoperable without the "baggage" of social context.

## 🧬 How CML "Operates" at M1

Because CML artifacts are at M1, they are **Homoiconic** (Code is Data):

- **Your program**: A Tree of CIDs
- **A "Compiler"**: Just another M1 artifact that takes a CML Tree as input
- **Self-describing**: The compiler logic is also at M1, making the entire system reflexive

## The Complete System: Static + Dynamic

**CID + DID = Complete System**

- **CID (M1-M3)**: Universal Truth - "This logic is always this logic"
- **DID/Ref (M4)**: Situational Truth - "This is what Alice currently thinks the status is"

By separating them, CML builds a system that is:
- **As stable as a mathematical proof** (M1-M3)
- **As dynamic as a Git repository** (M4)

### Practical Example: Collaborative Tax Calculator

**M3 (Physics)**: Universal rules ensure all tax calculations follow component structure rules
**M2 (Biology)**: The `Multiply` operation has universal mathematical meaning  
**M1 (Anatomy)**: The logic `Amount * 0.21` produces `CID_b3a123...`
**M4 (Memory)**: `Ref:TaxRate → CID_b3a123...` allows collaborative updates to tax logic
**M0 (Event)**: Any CPU executing the resolved CID produces identical results

**Key Insight**: By separating static mathematical truth (CID) from dynamic collaborative state (DID/Ref), the system ensures computation is universal while enabling practical collaboration.

## Summary Mapping Table

| Layer | Name | Identity | Primary Key | Responsibility |
|-------|------|----------|-------------|----------------|
| **M4** | **CML State** | **DID + RefName** | **Named Refs** | Memory: Named references to current CIDs |
| **M3** | **Meta-CML** | **Code/Schema** | **Reflexive Rules** | Physics: Universal component structure rules |
| **M2** | **CML Schema** | **CID** | **Component Definitions** | Biology: Mathematical operation definitions |
| **M1** | **CML Logic** | **CID** | **Specific Trees** | Anatomy: Immutable computational expressions |
| **M0** | **Runtime** | **Pointer** | **Execution** | Event: CPU executing the resolved mathematics |

## 2. Ontological Commitments

### 2.1 What CML Is

**CML operates as a 4-layer dynamic MOF architecture that bridges static mathematical truth with collaborative state:**

#### M4 — CML State (The "Memory")
- **The Causal Layer**: Named references and collaborative assertions
- **Dynamic State**: Git-like refs that point to current CIDs
- **Artifact**: Named reference registry with DID-based ownership
- **Role**: Bridges static CIDs with mutable collaborative state

#### M3 — Meta-CML (The "Physics")
- **The Reflexive Meta-Metamodel**: Defines "What is a Component?" using itself
- **Structural Rules**: Universal component structure rules
- **Artifact**: PKL-based self-describing meta-metamodel
- **Role**: Language of Languages - defines the "physics" of modeling

#### M2 — CML Schema (The "Biology")
- **Component Definitions**: The standard library of component types
- **Mathematical Operations**: Defines the semantics of specific operations
- **Artifacts**: `Unit`, `BinOp`, `Literal`, `Var`, `Invoke` definitions
- **Role**: The vocabulary that validates MDX tags and defines execution semantics

#### M1 — CML Artifacts (The "Identity")
- **Algebraic Identity**: The immutable, "cold" representation of logic
- **Pure Values**: CML artifacts are values, not processes - they don't "run"
- **Unique Identity**: Each instance has a specific CID (Content Identifier)
- **Role**: Stable "truth" of the program structure - **this is where CML lives**
- **Key Insight**: The separation between representation (M1 values) and realisation (M0 execution)

#### M0 — TruffleNode (The "Execution")
- **Physical Execution**: The "hot" object living in GraalVM/JVM heap
- **Runtime Optimisation**: JIT-compiled code and type-profiled data
- **Machine Identity**: Memory address / pointer in running system
- **Role**: High-performance CPU reduction of the computation

CML represents:

- functions (as M2 tagType definitions, M1 instances)
- control flow (as component compositions)
- expressions (as nested CmlComponent structures)
- types (as classification systems)
- constraints (as logical restrictions)

As **values** with **content-addressed identity**.

## 📂 The "MDX Sandwich" Across the Stack

An MDX file in the system is actually a "Multi-Layer Object." When you save a `.mdx` file, you are saving data across three MOF layers simultaneously:

| Part of the File | MOF Layer | Identifier | Role |
|------------------|-----------|------------|------|
| **YAML Frontmatter** | M4 (Subjective) | DID | Context: Author, Date, Social Tags |
| **XML `<Body>`** | **M1 (Model)** | **CID** | **The CML Artifact: The Merkle-DAG logic** |
| **Imported Tags** | M2 (Metamodel) | Type FQN | Grammar: The definitions of the tags used |

### 🎨 Usage: Building the AST with TSX

Because we use the createElement signature, you can use TSX (TypeScript JSX) to write CML:

```typescript
/** @jsx createElement */

const MyComputation = (
  <Unit name="Global_Calc" author="did:key:alice">
    <PrimitiveValue value={100} />
    {/* This Ref node has a CID based on the string "TAX_RATE",
         but its value is resolved via the M4 Repo lookup */}
    <NamedRef name="TAX_RATE" />
  </Unit>
);

console.log(MyComputation.cid); // Deterministic M1 Identity
```

### 📝 Key Implementation Notes

- **Stable Ordering**: Children are hashed in the order provided (CML is often non-commutative)
- **Referential Stability**: Identical trees produce identical CIDs, enabling structural deduplication
- **Truffle-Ready**: Output serializes directly to pklbinary format with pre-calculated CIDs

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

### 2.2 CML in the MDX Ecosystem

**CmlComponent** is the M3-level formalisation of MDX Component concept, designed for content-addressed, computational environments:

#### The Three Sectors of CmlComponent

1. **Hashed Body (Children)**: Core identity - if children change, CID changes
2. **Non-Hashed Metadata (Props/Attributes)**: XML attributes and YAML frontmatter excluded from CID
3. **tagType (FQN)**: Fully-qualified name ensuring type identity uniqueness

#### Strategic Ontology Mapping

| Concept | MDX Equivalent | CML/MOF Positioning | Role |
|---------|----------------|-------------------|------|
| **CmlComponent** | React.Component base | M3 (Meta-CML) | Defines the "Rules" (Hashed vs. Non-Hashed) |
| **tagType** | A specific .jsx file | M2 (CML Core) | Defines a specific "Symbol" (Unit, Literal) |
| **XML Tag** | `<MyComp />` | M1 (Artifact) | The specific "Logic" with a unique CID |

---

### 2.2 What CML Is Not

CML does **not** represent:

- runtime state or execution semantics
- threads
- memory
- IO
- system calls
- time
- authorship
- mutation of reality

Those belong to **M0** or **M4**.

---

## 3. Core Design Rules

### Rule 1 — Value Semantics Only

Every CML node is a **value**.
Nodes do not “run”, “execute”, or “happen”.

---

### Rule 2 — Closed Algebra

Only the node kinds defined in this specification are valid.
No user-defined node kinds are allowed at M3.

---

### Rule 3 — Structural Determinism

Two CML documents with identical structure and content **must** serialise to the same canonical form and produce the same CID.

---

### Rule 4 — No Identity, No Time

CML nodes do not contain:

- timestamps
- authors
- environment references
- mutable identifiers

---

## 4. Document Structure

```xml
<CML version="0.1">
  <Unit>...</Unit>
</CML>
```

A CML document contains **one or more `Unit` nodes**.

---

## 5. The Closed Algebra of Nodes

The following node kinds constitute the **complete M3 algebra**.

---

## 6. Unit

### Definition

A `Unit` represents a callable computational value.

### Properties

* name
* parameters
* return type
* body

### Semantics

A `Unit` denotes a **pure mapping** from inputs to output.

### XML Form

```xml
<Unit name="add">
  <Params>
    <Binding name="a" type="Int32"/>
    <Binding name="b" type="Int32"/>
  </Params>
  <Returns type="Int32"/>
  <Block>...</Block>
</Unit>
```

---

## 7. Block

### Definition

A `Block` is an ordered list of statements.

### Properties

* sequence of statements

### Semantics

A block denotes **sequential composition**.

### XML Form

```xml
<Block>
  <Binding name="x" type="Int32"/>
  <Return>
    <Var name="x"/>
  </Return>
</Block>
```

---

## 8. Expression (Abstract)

### Definition

An `Expression` denotes a value.

### Semantics

Expressions are **side-effect-free** and **total**.

### Subtypes

* Literal
* Var
* Call
* BinOp
* Match

---

## 9. Call

### Definition

A `Call` represents function application.

### Properties

* target
* arguments

### Semantics

A call denotes **evaluation of a Unit value**.

### XML Form

```xml
<Call target="add">
  <Arg><Var name="a"/></Arg>
  <Arg><Var name="b"/></Arg>
</Call>
```

---

## 10. Loop

### Definition

A `Loop` represents bounded or unbounded iteration.

### Properties

* kind
* condition or iterator
* body

### Semantics

A loop denotes **recursive structural repetition**, not runtime looping.

### XML Form

```xml
<Loop kind="while">
  <Condition>
    <BinOp op="<">
      <Var name="i"/>
      <Literal type="Int32" value="10"/>
    </BinOp>
  </Condition>
  <Block>...</Block>
</Loop>
```

---

## 11. Match

### Definition

A `Match` represents structural branching.

### Properties

* value
* arms

### Semantics

A match denotes **pattern-based selection**.

### XML Form

```xml
<Match>
  <Value><Var name="x"/></Value>
  <Arm>
    <Pattern type="Literal" value="0"/>
    <Block>...</Block>
  </Arm>
</Match>
```

---

## 12. Type

### Definition

A `Type` is a value describing a classification of values.

### Semantics

Types are **descriptive**, not enforced at runtime.

### Subtypes

* PrimitiveType
* CompositeType
* FunctionType
* ArrayType
* ReferenceType

### XML Form

```xml
<Type kind="Primitive" name="Int32"/>
```

---

## 13. Constraint

### Definition

A `Constraint` restricts type parameters.

### Semantics

Constraints are **logical predicates**, not runtime checks.

### XML Form

```xml
<Constraint kind="Trait" target="Comparable"/>
```

---

## 14. Canonical Serialisation

CML nodes are serialisable into a canonical form (e.g., canonical XML, length-prefixed binary, or JSON-LD), from which content identifiers (CIDs) are derived.

Canonicalisation rules:

1. Node type name
2. Attributes sorted lexicographically
3. Children serialised in declaration order
4. No insignificant whitespace

Example canonical form:

```
Unit(name=add,params=[Int32,Int32],returns=Int32,body=...)
```

---

## 15. Hashing and Identity

Each node has:

* no intrinsic identity
* identity = hash(canonical form)

Hashes are:

* deterministic
* content-derived
* immutable

---

## 16. Interpretation Boundary

CML **does not execute itself**.

Execution requires:

* an interpreter
* a compiler
* a runtime

Those are **outside CML**.
