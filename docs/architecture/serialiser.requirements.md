# CML Canonical Serialisation & CID Stability Requirements (M3)

## Introduction

This specification defines the requirements for implementing canonical serialisation of CML (Computation Modeling Language), a subset and an extension of XML using a metamodel-driven approach. The serialiser is implemented using PKL's Reflection module and its Pantry Syntax module and related renderer for XML. The system must ensure deterministic, lossless, context-free mapping from CML values to byte sequences while maintaining CID (Content Identifier) stability and perfect diff capability.

The implementation follows a strict Merkle-DAG construction pipeline:
1. **Introspection**: PKL_Reflection reads the CML instance
2. **AST Mapping**: pkl.experimental.syntax maps reflected data into stable AST structures  
3. **Canonicalisation**: Ordering and absence rules create a Normal Form
4. **Serialisation**: Normal Form reduces to deterministic byte sequence
5. **Hashing**: BLAKE3 generates the CID

Canonical serialisation is defined abstractly. XML is a non-normative rendering that MUST round-trip to the canonical form but SHALL NOT define it.

## Glossary

- **CML**: Computation Modeling Language - a pure data language for representing executable semantics as immutable structure
- **CID**: Content Identifier - a deterministic hash derived from canonical serialised form
- **M3**: The closed, immutable algebra of computation modeled by CML. Canonical serialisation is a mechanism used to derive stable identifiers for M3 values
- **PKL**: A configuration language with reflection and rendering capabilities (reference implementation)
- **PKL_Reflection**: PKL's core reflection module for runtime type introspection
- **PKL_Syntax**: The pkl.experimental.syntax package for representing AST structures as data
- **PKL_Pantry**: PKL's package ecosystem containing experimental and utility modules
- **Canonical_Serialiser**: The system component responsible for deterministic serialisation
- **XML_Renderer**: PKL's XML output generation component (non-normative rendering)
- **BLAKE3_Hasher**: The hashing component for CID generation
- **CML_Node**: Any valid node in the CML algebra (Unit, Block, Expression, etc.)
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

**User Story:** As a CML author, I want to use MDX for component authoring, so that I can write literate CML with excellent developer experience.

#### Acceptance Criteria

1. THE System SHALL support MDX as a primary authoring format for CML components
2. THE System SHALL map MDX tags directly to importable CmlComponent classes
3. THE System SHALL render MDX files to produce Canonical Manifests containing CIDs of all defined components
4. THE System SHALL treat MDX Content (Markdown text) as Meta-Attributes excluded from semantic hash
5. THE System SHALL ensure MDX-authored components produce identical CIDs to manually constructed equivalent components

### Requirement 23: External Resource Reader Protocol

**User Story:** As a CML implementer, I want robust external BLAKE3 integration, so that hashing works reliably for large component trees.

#### Acceptance Criteria

1. THE System SHALL support URI-based external resource reader protocol for BLAKE3 hashing
2. THE System SHALL support file-pointer URI format (cid:file:///tmp/content.bin) for large component trees
3. THE System SHALL handle shell/buffer limits gracefully when passing binary content
4. THE System SHALL provide deterministic BLAKE3 hashing via external Go/Rust binary
5. THE System SHALL integrate with PKL via --external-resource-reader flag