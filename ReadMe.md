---
Version: 0.0.1
---

# CML

**CML (Computation Modeling Language)** is a closed, deterministic algebra for representing executable semantics as pure data. CML values are immutable, content-addressed, and context-independent. CML defines what something is, not its execution or history.

> CML is a closed, pure, immutable algebra of computational meaning expressed as data.

## 1. Purpose and Scope

CML is a pure data language for representing executable semantics as immutable structure.

CML describes computation but **does not perform computation**.

It is:

* **closed** (finite, enumerable node kinds)
* **pure** (no side effects, no time, no identity)
* **deterministic** (hashable, replayable)
* **referentially transparent**

CML occupies **M3** in the layered system.

---

## 2. Ontological Commitments

### 2.1 What CML Is

CML represents:

- functions
- control flow
- expressions
- types
- constraints

As **values**.

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
