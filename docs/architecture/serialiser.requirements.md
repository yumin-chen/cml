---
Version: 0.0.1
Date: 2025-01-19
---

# CML Canonical Serialisation & CID Stability Requirements (M3)

## Introduction

This specification defines the requirements for implementing canonical serialisation of **CML (Computation Modeling Language)** — a closed, deterministic algebra for representing executable semantics as pure data. CML values are immutable, structrually-typed, content-addressed, and context-dependent. The serialiser is implemented using PKL's Reflection module and its Pantry Syntax module and related renderer for XML. The system must ensure deterministic, lossless, context-free mapping from CML values to byte sequences while maintaining CID (Content Identifier) stability and perfect diff capability.

The implementation follows a strict Merkle-DAG construction pipeline:
1. **Introspection**: PKL_Reflection reads the CML instance
2. **AST Mapping**: pkl.experimental.syntax maps reflected data into stable AST structures  
3. **Canonicalisation**: Ordering and absence rules create a Normal Form
4. **Serialisation**: Normal Form reduces to deterministic byte sequence
5. **Hashing**: BLAKE3 generates the CID

Canonical serialisation is defined abstractly. XML is a non-normative rendering that MUST round-trip to the canonical form but SHALL NOT define it.

## Glossary

- **CML**: **CML (Computation Modeling Language)** — a closed, deterministic algebra for representing executable semantics as pure data. CML values are immutable, content-addressed, and context-independent. **CML maintains a "Hermetic Seal" at M3 - pure algebraic truth below, social truth above.**
- **CID**: Content Identifier - a deterministic hash derived from canonical serialised form
- **Hermetic Seal**: The boundary at M3 that separates pure algebraic truth (M1-M3) from social truth (M4-M5)
- **M0**: Runtime Instances - TruffleNode instances executing pure mathematical operations
- **M1**: **CML Artifacts** - "Frozen snapshots" of pure mathematical logic with CID identity (WHERE CML LIVES)
- **M2**: CML Metamodel - Mathematical "grammar" defining operations with universal meaning (Unit, BinOp, Literal, etc.)
- **M3**: Meta-CML - Self-describing "physics" of component structure - the hermetic seal boundary
- **M4**: CML-COB - "Envelope" layer carrying M1 logic into social world via signed operations
- **M5**: User Node - "Actor" layer with Node Identity (NID/DID) for entities who sign operations
- **Model of Computation**: CML artifacts are pure mathematical data that describe computation universally
- **Mechanism of Execution**: Interpreters and compilers (M0 tools) that execute pure mathematical logic
- **COB**: Collaborative Object - manages evolution of M1 artifacts through signed operations without contaminating them
- **NID**: Node Identity - Decentralised identifier for actors who can sign operations (social layer)
- **Algebraic Truth**: Universal mathematical reality that produces identical results regardless of social context
- **Social Truth**: Collaborative reality involving actors, governance, and historical processes
- **Rust_Enum**: Rust's enum system for pattern matching and compile-time guarantees
- **Serde**: Rust serialisation framework with derive macros for deterministic binary output
- **Bincode**: Deterministic binary serialisation format for Rust
- **Canonical_Serialiser**: The system component responsible for deterministic serialisation using Rust
- **MDX_Renderer**: Rust-based MDX output generation component for enum instances
- **BLAKE3_Hasher**: Native Rust hashing component for CID generation using blake3 crate
- **CML_Node**: A Rust enum variant belonging to the closed set of node kinds defined by the CML algebra
- **Meta-CML**: The M3 meta-metamodel that defines the grammar for creating CML language constructs using Rust enums
- **Merkle_Tree**: A tree structure where each node is identified by the hash of its content

## Requirements

### Requirement 1: Deterministic Serialisation

**User Story:** As a CML system architect, I want deterministic serialisation of CML nodes, so that identical semantic content always produces identical byte sequences.

#### Acceptance Criteria

1. WHEN the same CML node is serialised multiple times, THE Canonical_Serialiser SHALL produce identical output
2. WHEN two semantically equivalent CML nodes are serialised, THE Canonical_Serialiser SHALL produce identical output
3. WHEN serialisation occurs in different environments, THE Canonical_Serialiser SHALL produce identical output for the same input
4. WHEN serialisation occurs at different times, THE Canonical_Serialiser SHALL produce identical output for the same input
5. THE Canonical_Serialiser SHALL exclude all environment-specific, user-specific, and temporal data from output

### Requirement 2: Uniqueness Guarantee

**User Story:** As a CML system architect, I want unique serialisations for different semantic values, so that no two different CML nodes can produce the same canonical form.

#### Acceptance Criteria

1. WHEN two CML nodes have different semantic content, THE Canonical_Serialiser SHALL produce different serialised output
2. WHEN field values differ between nodes, THE Canonical_Serialiser SHALL produce different serialised output
3. WHEN node types differ, THE Canonical_Serialiser SHALL produce different serialised output
4. WHEN collection ordering differs semantically, THE Canonical_Serialiser SHALL produce different serialised output

### Requirement 3: Context Independence

**User Story:** As a CML system architect, I want context-independent serialisation, so that serialised output contains no external dependencies or environment references.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL NOT include user identity information in serialised output
2. THE Canonical_Serialiser SHALL NOT include timestamps in serialised output
3. THE Canonical_Serialiser SHALL NOT include environment variables in serialised output
4. THE Canonical_Serialiser SHALL NOT include file paths or system-specific references in serialised output
5. THE Canonical_Serialiser SHALL NOT include tool version information in serialised output

### Requirement 4: Order Normalisation

**User Story:** As a CML system architect, I want consistent ordering rules, so that semantically equivalent structures are serialised identically regardless of input order.

#### Acceptance Criteria

1. WHEN serialising CML node fields, THE Canonical_Serialiser SHALL order them according to specification-defined rules
2. WHEN serialising map collections, THE Canonical_Serialiser SHALL sort keys lexicographically
3. WHEN serialising list collections, THE Canonical_Serialiser SHALL preserve semantic order
4. WHEN serialising node attributes, THE Canonical_Serialiser SHALL sort them lexicographically

### Requirement 5: Type Explicitness

**User Story:** As a CML system architect, I want fully qualified type information, so that serialised output is self-describing and unambiguous.

#### Acceptance Criteria

1. WHEN serialising any CML node, THE Canonical_Serialiser SHALL include the fully-qualified type name
2. WHEN serialising nested nodes, THE Canonical_Serialiser SHALL include type information for all children
3. THE Canonical_Serialiser SHALL NOT rely on implicit type inference
4. THE Canonical_Serialiser SHALL use complete type paths without abbreviation

### Requirement 6: Explicit Absence Handling

**User Story:** As a CML system architect, I want explicit representation of absent values, so that optional fields are clearly distinguished from present fields.

#### Acceptance Criteria

1. WHEN an optional field is absent, THE Canonical_Serialiser SHALL serialise it with an explicit null marker (∅)
2. WHEN an optional field is present, THE Canonical_Serialiser SHALL serialise its actual value
3. THE Canonical_Serialiser SHALL NOT omit optional fields from serialised output
4. THE Canonical_Serialiser SHALL distinguish between empty collections and absent collections

### Requirement 7: Self-Contained Serialisation

**User Story:** As a CML system architect, I want self-contained serialisation, so that serialised output requires no external context for interpretation.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL ensure that all referenced types are resolved to immutable identifiers (CIDs), not inlined definitions
2. THE Canonical_Serialiser SHALL resolve all symbolic references to explicit identifiers
3. THE Canonical_Serialiser SHALL NOT include unresolved references in serialised output
4. WHEN external references exist, THE Canonical_Serialiser SHALL represent them by CID only

### Requirement 8: Reference Implementation - PKL-Based Canonical Serialiser

**User Story:** As a CML implementer, I want to use PKL's reflection and syntax modules, so that I can introspect CML node structures and represent them as data.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL use PKL's core reflection module to discover node structure at runtime
2. THE Canonical_Serialiser SHALL use pkl.experimental.syntax package to represent CML AST structures as PKL data
3. THE Canonical_Serialiser SHALL leverage ExpressionNode.pkl, TypeNode.pkl, and ClassNode.pkl templates for CML node representation
4. THE Canonical_Serialiser SHALL use PKL_Reflection to extract field names, types, and metadata
5. THE Canonical_Serialiser SHALL handle nested object structures through recursive reflection and syntax tree traversal

### Requirement 9: XML Output Generation (Non-Normative Rendering)

**User Story:** As a CML implementer, I want to generate XML output using PKL renderers, so that I can leverage existing PKL infrastructure.

#### Acceptance Criteria

1. THE XML_Renderer SHALL generate well-formed XML output
2. THE XML_Renderer SHALL use pkl.xml renderer with indent = "" and newline = "" to eliminate insignificant whitespace
3. THE XML_Renderer SHALL NOT include XML comments in output
4. THE XML_Renderer SHALL use consistent attribute ordering via lexicographic sort
5. THE XML_Renderer SHALL ensure XML output round-trips to canonical form without loss

### Requirement 10: BLAKE3 Hashing Integration

**User Story:** As a CML system architect, I want BLAKE3 hashing for CID generation, so that I can create cryptographically secure content identifiers.

#### Acceptance Criteria

1. THE BLAKE3_Hasher SHALL generate deterministic hashes from serialised content
2. THE BLAKE3_Hasher SHALL integrate with PKL via external tool or Java/Kotlin host integration
3. THE BLAKE3_Hasher SHALL use standard BLAKE3 parameters without customisation
4. THE BLAKE3_Hasher SHALL output hashes in canonical hexadecimal format
5. THE BLAKE3_Hasher SHALL handle byte sequence input consistently across platforms

### Requirement 11: CML Node Type Support

**User Story:** As a CML implementer, I want support for all CML node types, so that the serialiser can handle the complete CML algebra.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL support Unit nodes with name, parameters, return type, and body
2. THE Canonical_Serialiser SHALL support Block nodes with ordered statement sequences
3. THE Canonical_Serialiser SHALL support Expression nodes including Literal, Var, Call, BinOp, and Match
4. THE Canonical_Serialiser SHALL support Loop nodes with kind, condition, and body
5. THE Canonical_Serialiser SHALL support Type nodes with all type variants
6. THE Canonical_Serialiser SHALL support Constraint nodes with logical restrictions

### Requirement 12: Merkle Tree Structure

**User Story:** As a CML system architect, I want Merkle tree structure support, so that I can achieve structural sharing and efficient referencing.

#### Acceptance Criteria

1. WHEN serialising nested structures, THE Canonical_Serialiser SHALL serialise children before parents
2. WHEN referencing child nodes, THE Canonical_Serialiser SHALL use their CIDs
3. THE Canonical_Serialiser SHALL support recursive CID calculation
4. THE Canonical_Serialiser SHALL enable structural sharing through CID references

### Requirement 13: CID Stability

**User Story:** As a CML system architect, I want CID stability across implementations, so that different compilers produce identical CIDs for semantically identical CML.

#### Acceptance Criteria

1. WHEN two different implementations serialise the same CML node, THE system SHALL produce identical CIDs
2. WHEN the same CML node is processed by different tools, THE system SHALL produce identical CIDs
3. THE Canonical_Serialiser SHALL follow specification-defined canonicalisation rules exactly
4. THE Canonical_Serialiser SHALL produce implementation-independent output

### Requirement 14: Diff and Merge Support

**User Story:** As a CML system user, I want perfect diff capability, so that I can reliably compare and merge CML structures.

#### Acceptance Criteria

1. WHEN comparing two CML structures, THE system SHALL enable precise structural diffing
2. WHEN merging CML structures, THE system SHALL preserve semantic integrity
3. THE Canonical_Serialiser SHALL produce output optimised for diff algorithms
4. THE Canonical_Serialiser SHALL enable conflict-free merging of compatible changes

### Requirement 15: M3 Compliance

**User Story:** As a CML system architect, I want M3 layer compliance, so that the serialisation system adheres to CML's architectural principles.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL maintain immutability of serialised representations
2. THE Canonical_Serialiser SHALL ensure referential transparency
3. THE Canonical_Serialiser SHALL exclude all temporal and identity information
4. THE Canonical_Serialiser SHALL support the closed algebra of CML node types only
5. THE Canonical_Serialiser SHALL maintain value semantics throughout serialisation

### Requirement 16: No Operational Semantics

**User Story:** As a CML system architect, I want purely descriptive serialisation, so that no execution or runtime behavior is encoded.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL NOT encode execution order
2. THE Canonical_Serialiser SHALL NOT encode evaluation strategy
3. THE Canonical_Serialiser SHALL NOT encode runtime behavior
4. THE Canonical_Serialiser SHALL maintain that CML artifacts are pure data that model computation but don't execute themselves
5. THE Canonical_Serialiser SHALL ensure that interpreters and compilers (M0 tools) are separate from CML artifacts (M1 data)
### Requirement 17: PKL Pantry Integration

**User Story:** As a CML implementer, I want to leverage PKL Pantry modules, so that I can use proven AST manipulation and code generation patterns.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL import pkl.experimental.syntax@1.1.0 from PKL Pantry
2. THE Canonical_Serialiser SHALL vendor or pin the specific version digest in PklProject.deps.json to ensure CID stability
3. THE Canonical_Serialiser SHALL use operators.pkl for consistent operator representation
4. THE Canonical_Serialiser SHALL use TypeAnnotationNode.pkl for type signature handling
5. THE Canonical_Serialiser SHALL handle API changes gracefully since pkl.experimental.syntax is in experimental namespace

### Requirement 18: Numeric Determinism

**User Story:** As a CML system architect, I want deterministic numeric representation, so that floating point values produce identical serialisations across platforms.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL represent floating point numbers using IEEE 754 hexadecimal format
2. THE Canonical_Serialiser SHALL NOT use decimal-to-string conversions that vary by platform
3. THE Canonical_Serialiser SHALL normalise special values (NaN, Infinity, -Infinity) to canonical representations
4. THE Canonical_Serialiser SHALL ensure numeric serialisation is independent of CPU architecture

### Requirement 19: String Normalisation

**User Story:** As a CML system architect, I want consistent string representation, so that semantically identical strings produce identical CIDs.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL normalise all strings to Unicode NFC (Canonical Decomposition followed by Canonical Composition)
2. THE Canonical_Serialiser SHALL handle combining characters consistently
3. THE Canonical_Serialiser SHALL ensure string serialisation is independent of input encoding
4. THE Canonical_Serialiser SHALL validate that normalised strings round-trip correctly

### Requirement 20: Acyclic Graph Enforcement

**User Story:** As a CML system architect, I want to prevent circular references, so that Merkle tree hashing terminates successfully.

#### Acceptance Criteria

1. THE Canonical_Serialiser SHALL detect circular references during traversal
2. THE Canonical_Serialiser SHALL reject CML structures containing cycles
3. THE Canonical_Serialiser SHALL ensure all CML structures form a Directed Acyclic Graph (DAG)
4. WHEN circular references are detected, THE Canonical_Serialiser SHALL return a descriptive error

### Requirement 21: Component-Oriented Architecture

**User Story:** As a CML implementer, I want component-oriented architecture with strict Props/Children separation, so that metadata never affects CID stability.

#### Acceptance Criteria

1. THE System SHALL implement Props-Transparent Rule: XML attributes (metadata) are NEVER hashed
2. THE System SHALL implement Child-Identity Rule: Semantic content (children) ALWAYS defines identity and is hashed
3. THE System SHALL implement Type-Identity Rule: Different component types with identical children produce different CIDs
4. THE System SHALL use @Semantic annotation to mark properties that contribute to CID calculation
5. THE System SHALL ignore all non-@Semantic properties during canonicalisation

### Requirement 22: MDX Authoring Layer

**User Story:** As a CML author, I want to use MDX for component authoring with localisation support, so that I can write literate CML with excellent developer experience while maintaining CID stability across different contexts.

#### Acceptance Criteria

1. THE System SHALL support MDX as a primary authoring format for CML components
2. THE System SHALL map MDX tags directly to importable CmlComponent classes
3. THE System SHALL render MDX files to produce Canonical Manifests containing CIDs of all defined components
4. THE System SHALL treat MDX Content (Markdown text) as Meta-Attributes excluded from semantic hash
5. THE System SHALL ensure MDX-authored components produce identical CIDs to manually constructed equivalent components
6. THE System SHALL support YAML frontmatter for localisable context metadata
7. THE System SHALL inject frontmatter values as Props (non-hashed attributes) into CML components
8. THE System SHALL maintain CID stability when frontmatter values change (locale, author, date, etc.)

### Requirement 23: M3 Localisation Pattern

**User Story:** As a CML system architect, I want strict separation between Identity (hashable code) and Context (localisable metadata), so that the same semantic content can be presented in different locales without affecting CIDs.

#### Acceptance Criteria

1. THE System SHALL implement M3 Localisation Pattern with YAML frontmatter as Context container
2. THE System SHALL treat YAML frontmatter as "outside the executable tree" and exclude from CID calculation
3. THE System SHALL inject frontmatter values as Props (meta attributes) into CML components
4. THE System SHALL support dynamic rendering based on locale-aware frontmatter props
5. THE System SHALL enable infinite modification of frontmatter without changing CML logic CIDs
6. THE System SHALL provide Context Provider for mapping frontmatter keys to component props
7. THE System SHALL support multi-locale presentation of identical semantic content

### Requirement 24: MOF Architecture Compliance with Hermetic Seal

**User Story:** As a CML system architect, I want MOF (Meta-Object Facility) architecture compliance with proper hermetic seal, so that CML maintains pure algebraic truth below M3 and enables social coordination above M3.

#### Acceptance Criteria

1. THE System SHALL maintain a "Hermetic Seal" at M3 separating pure algebraic truth from social truth
2. THE System SHALL ensure M1-M3 contain only pure mathematical logic with no social metadata
3. THE System SHALL position CML Artifacts as M1 ("Frozen Snapshots") - pure mathematical logic with CID identity (WHERE CML LIVES)
4. THE System SHALL position CML Metamodel as M2 ("Grammar") - mathematical operations with universal meaning
5. THE System SHALL implement Meta-CML as M3 ("Physics") - self-describing component structure rules
6. THE System SHALL position Runtime as M0 - pure mathematical execution separate from social concerns
7. THE System SHALL position CML-COB as M4 ("Envelope") - carries M1 logic into social world without contamination
8. THE System SHALL position User Node as M5 ("Actor") - provides Node Identity (NID/DID) for signing operations
9. THE System SHALL ensure identical M1 CIDs produce identical results regardless of social context
10. THE System SHALL prevent social concerns from leaking into the pure mathematical layers (M1-M3)

### Requirement 25: CML Artifact Ontology (MDX Integration)

**User Story:** As a CML system architect, I want clear ontological positioning of CML artifacts within the MDX ecosystem, so that the relationship between MDX components and CML components is precisely defined.

#### Acceptance Criteria

1. THE System SHALL position CmlComponent as M3-level formalisation of MDX Component concept
2. THE System SHALL implement CmlComponent as Merkle-Node Template with three distinct sectors
3. THE System SHALL ensure Hashed Body (Children) defines core identity where children changes alter CID
4. THE System SHALL ensure Non-Hashed Metadata (Props/Attributes) provides tooling hints without affecting CID
5. THE System SHALL ensure tagType (FQN) provides type identity uniqueness
6. THE System SHALL implement Canonical Projection rendering flow from MDX to Merkle
7. THE System SHALL ensure different tagTypes with identical content produce different CIDs
8. THE System SHALL map MDX ecosystem concepts to MOF layers correctly

### Requirement 26: External Resource Reader Protocol

**User Story:** As a CML implementer, I want robust external BLAKE3 integration, so that hashing works reliably for large component trees.

#### Acceptance Criteria

1. THE System SHALL support URI-based external resource reader protocol for BLAKE3 hashing
2. THE System SHALL support file-pointer URI format (cid:file:///tmp/content.bin) for large component trees
3. THE System SHALL handle shell/buffer limits gracefully when passing binary content
4. THE System SHALL provide deterministic BLAKE3 hashing via external Go/Rust binary
5. THE System SHALL integrate with PKL via --external-resource-reader flag
