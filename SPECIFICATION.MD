
# Universal Contextual Language (UCL) 5.0 "GraphNative" - Specification

**Version:** 5.0.0
**Status:** Draft
**Date:** 2025-06-02
**Editors:** NeuroTinkerLab (Conceptual Lead), AI Language Model (Drafting Assistant)

## Abstract

The Universal Contextual Language (UCL) Version 5.0 "GraphNative" is a formal language specification designed for precise, unambiguous, and richly contextualized communication, primarily targeting interactions with advanced Artificial Intelligence (AI) systems, including Large Language Models (LLMs), and facilitating robust machine-to-machine (M2M) data exchange. UCL 5.0 introduces a graph-native payload representation based on semantic triples (RDF-like) and a sophisticated "Context Mixer" framework for granular contextual control, evolving significantly from previous UCL versions.

This document provides the normative specification for UCL 5.0.

## Table of Contents

1.  Introduction
    1.1. Goals of UCL 5.0
    1.2. Key Features and Advancements
    1.3. Relationship to UCL 4.2
    1.4. Terminology and Conventions
    1.5. Conformance
2.  Core Language Concepts
    2.1. UCL Message Structure
    2.2. UCL-IDs (URIs and CURIEs)
    2.3. Namespaces and Prefixes (`@prefix`)
    2.4. Literals
    2.5. Graph-Native Payloads (Semantic Triples)
    2.6. The Context Mixer (`cm:`) Framework
    2.7. Optional Context Stack (`#`)
3.  Textual Syntax
    3.1. Character Encoding and Whitespace
    3.2. Comments
    3.3. Overall Message Syntax (Conceptual ABNF Outline)
    3.4. Prefix Declarations Syntax
    3.5. Envelope Syntax
        3.5.1. Source UCL-ID
        3.5.2. Direction Operator (Implicit `>`)
        3.5.3. Target UCL-ID
        3.5.4. `execute` Verb
        3.5.5. Operation UCL-ID
        3.5.6. Modifiers (`^Predicate Value`)
    3.6. Payload Graph Separator (`:`)
    3.7. Payload Graph Block Syntax
        3.7.1. Triples (`Subject Predicate Object .`)
        3.7.2. Subject Representation (UCL-ID, Blank Node)
        3.7.3. Predicate Representation (UCL-ID)
        3.7.4. Object Representation (UCL-ID, Literal, Blank Node)
        3.7.5. Literal Syntax (Strings, Numbers, Booleans, Typed Literals, Language Tags)
        3.7.6. Blank Node Syntax (`[]`, `_:label`)
        3.7.7. RDF Collection Syntax (`(...)`)
    3.8. Optional Context Stack Syntax
4.  Semantic Model
    4.1. Core UCL Namespaces and Terms
        4.1.1. `ucl:` (Core UCL Terms)
        4.1.2. `cm:` (Context Mixer Terms)
        4.1.3. `ucl_meta:` (Metadata Terms)
        4.1.4. `llm:` (LLM Interaction Terms)
    4.2. Interpretation of the Payload Graph
    4.3. Interpretation of the Context Mixer
        4.3.1. `cm:Profile` Semantics
        4.3.2. `cm:FocusElement`, `cm:withWeight`, `cm:withFilter` Semantics
        4.3.3. `cm:Strategy` Semantics (Conceptual)
        4.3.4. `cm:guidesOutput` Semantics
    4.4. Interpretation of the Optional Context Stack
5.  Interaction with LLMs and AI Systems
    5.1. Providing UCL 5.0 to LLMs
    5.2. LLM Generation of UCL 5.0
    5.3. Reasoning Traces
6.  Extensibility
    6.1. Defining Custom Vocabularies and Namespaces
    6.2. Defining Custom Context Mixer Profiles and Strategies
7.  Binary Serialization (UCL-Bin 5.0 - Conceptual Outline)
    7.1. Principles
    7.2. Conceptual Structure
8.  Security Considerations (Informative)
9.  References
    9.1. Normative References
    9.2. Informative References

---
Appendix A: Core Vocabulary Definitions (Conceptual)
Appendix B: ABNF Grammar (Conceptual Draft)
---

## 1. Introduction

### 1.1. Goals of UCL 5.0
(As detailed in `docs/02_introduction_and_goals_ucl5.md`, focusing on deep semantic unambiguity, graph-native data, sophisticated contextual control, enhanced LLM instructability, true semantic interoperability, automation of complex AI workflows, and verifiable AI behavior.)

### 1.2. Key Features and Advancements
*   Graph-Native `PayloadGraph` based on semantic triples.
*   Advanced Context Mixer (`cm:`) framework with Profiles, Focus Elements, Weights, Filters, Strategies, and Output Directives.
*   Streamlined Message Envelope with `execute` as the primary verb and integrated `^Modifiers`.
*   Strong emphasis on URI-based semantics for all components.
*   Optional, complementary Context Stack.

### 1.3. Relationship to UCL 4.2
UCL 5.0 is a significant evolution. While it retains the core philosophy of explicit, contextual communication, it introduces fundamental changes to the payload structure and context management. A migration guide is provided in `docs/01_migrating_from_ucl4.2.md`. Key UCL 4.2 concepts like JSON-like payloads and the primary reliance on a linear context stack are superseded or made complementary in UCL 5.0.

### 1.4. Terminology and Conventions
*   **UCL-ID:** A Universal Contextual Language Identifier, canonically a URI, often represented as a CURIE.
*   **URI:** Uniform Resource Identifier (RFC 3986).
*   **IRI:** Internationalized Resource Identifier (RFC 3987). UCL uses IRIs which map to URIs.
*   **CURIE:** Compact URI Expression (e.g., `prefix:local_part`).
*   **Triple:** A `(Subject Predicate Object)` statement, the basic unit of the `PayloadGraph`.
*   **Literal:** A concrete data value (string, number, boolean, etc.).
*   **Blank Node:** A resource in a graph without a URI, used for local structuring.
*   The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

### 1.5. Conformance
A UCL 5.0 message is conformant if it adheres to the syntax and semantic rules defined in this specification. A UCL 5.0 processor is conformant if it correctly parses and can interpret (or declare inability to interpret) conformant UCL 5.0 messages according to their specified semantics.

## 2. Core Language Concepts

(This section would summarize the concepts detailed in `docs/04_core_concepts_ucl5/` and `docs/05_context_mixer_deep_dive/`, providing formal definitions where possible.)

### 2.1. UCL Message Structure
A UCL 5.0 message consists of optional Prefix Declarations, a mandatory Envelope, a mandatory Payload Graph Separator, a mandatory Payload Graph Block, and an optional Context Stack Separator followed by an optional Context Stack Part.

### 2.2. UCL-IDs (URIs and CURIEs)
All semantic elements (types, properties, individuals, actions, contexts, profile names, strategies, etc.) within UCL 5.0 MUST be identified by UCL-IDs, which are canonically IRIs/URIs. CURIEs are used for brevity.

### 2.3. Namespaces and Prefixes (`@prefix`)
Namespaces, identified by base URIs, group UCL-IDs. `@prefix` declarations map short prefixes to these base URIs.

### 2.4. Literals
Data values such as strings, numbers (integers, decimals), booleans, date/time, duration, and binary. Literals MAY be explicitly typed using XSD datatypes. Strings MAY have language tags.

### 2.5. Graph-Native Payloads (Semantic Triples)
The `PayloadGraph` is a set of RDF-like triples forming a graph. This is the primary means of conveying structured data and parameters.

### 2.6. The Context Mixer (`cm:`) Framework
The primary mechanism for advanced contextual control, utilizing `cm:Profile`s which define `cm:FocusElement`s, `cm:withWeight`s, `cm:withFilter`s, `cm:Strategy`s, and `cm:guidesOutput` directives.

### 2.7. Optional Context Stack (`#`)
A linear stack of UCL-IDs providing broader, complementary contextual framing.

## 3. Textual Syntax

(This section provides a more formal (though still conceptually outlined ABNF) description of the syntax detailed in `docs/06_syntax_in_detail_ucl5.md`.)

### 3.1. Character Encoding and Whitespace
UCL 5.0 messages MUST be encoded in UTF-8. Whitespace handling should generally follow conventions that allow for human readability while having a defined canonical form (e.g., single spaces between major tokens).

### 3.2. Comments
Single-line comments start with `//` and extend to the end of the line. They are ignored.

### 3.3. Overall Message Syntax (Conceptual ABNF Outline)
```abnf
UCL5Message        = *PrefixDeclaration Envelope PayloadGraphSeparator PayloadGraphBlock [ContextStackSeparator ContextStackPart]

PrefixDeclaration  = "@prefix" 1*WS PN_PREFIX ":" 1*WS "<" IRIREF ">" 1*WS (*COMMENT) NL
Envelope           = [SourceUCLID 1*WS ">" 1*WS] TargetUCLID 1*WS "execute" 1*WS OperationUCLID *Modifier (*COMMENT) NL
SourceUCLID        = UCLID
TargetUCLID        = UCLID
OperationUCLID     = UCLID
Modifier           = 1*WS "^" UCLID 1*WS (UCLID / Literal)
PayloadGraphSeparator = 1*WS ":" 1*WS (*COMMENT) NL
PayloadGraphBlock  = "{" NL *WS *Triple *WS "}" NL ; Conceptual delimiters, actual content is Turtle-like triples
Triple             = (Subject 1*WS Predicate 1*WS Object 1*WS "." (*COMMENT) NL) / ; and other Turtle constructs
Subject            = UCLID / BlankNode
Predicate          = UCLID
Object             = UCLID / Literal / BlankNode / Collection
; ... Further ABNF details for UCLID, Literal, BlankNode, Collection (RDF List), etc.
; ... This would require a full Turtle/RDF-subset ABNF.

ContextStackSeparator = 1*WS "#" 1*WS (*COMMENT) NL
ContextStackPart   = UCLIDwithModifiers *(1*WS "/" 1*WS UCLIDwithModifiers) *ModifierInContext (*COMMENT) NL
ModifierInContext  = 1*WS "^" UCLID 1*WS (UCLID / Literal) ; Optional feature for context stack items

UCLID              = CURIE / ("<" IRIREF ">")
CURIE              = [PN_PREFIX] ":" PN_LOCAL
Literal            = StringLiteral / NumericLiteral / BooleanLiteral / TypedLiteral / LangTaggedString / NullLiteral
; ... Definitions for PN_PREFIX, PN_LOCAL, IRIREF, StringLiteral, etc., from RDF/Turtle specs.
WS                 = %x20 / %x09 / %x0D / %x0A ; Whitespace
NL                 = %x0A / %x0D / %x0D.0A
COMMENT            = "//" *(%x00-09 / %x0B-0C / %x0E-7F) NL ; Basic comment
```

(Note: The ABNF above is a high-level conceptual outline and would need significant refinement and detail to be a complete, normative grammar, especially for the PayloadGraphBlock which should align with a subset of Turtle.)

3.4. - 3.8. (Syntax Breakdown)

(These sections would further break down the ABNF for each component: Prefix Declarations, Envelope components, Payload Graph Separator, Payload Graph Block components (Triples, Subject, Predicate, Object, Literals, Blank Nodes, Collections), and Optional Context Stack components, as described textually in docs/06_syntax_in_detail_ucl5.md and related core concept files.)

4. Semantic Model

(This section details the meaning and intended interpretation of UCL 5.0 constructs.)

4.1. Core UCL Namespaces and Terms

(This section refers to Appendix A for the conceptual definitions of terms in ucl:, cm:, ucl_meta:, llm:. A formal specification would fully define these, potentially as an ontology.)

4.2. Interpretation of the Payload Graph

The PayloadGraph is interpreted as a set of RDF triples. Its semantics are determined by the semantics of the UCL-IDs used as subjects, predicates, and objects (if they are URIs), and by standard RDF/RDFS/OWL entailment rules if applicable. The graph provides the specific data and relational context for the Operation_UCLID.

4.3. Interpretation of the Context Mixer

The Context Mixer, invoked via a ^cm:profile modifier, guides the interpretation and processing of the message.
* cm:Profile Semantics: A cm:Profile acts as a set of processing instructions. The receiving system uses the profile's defined cm:FocusElements, cm:withWeights, cm:withFilters, cm:Strategy, and cm:guidesOutput directives to modulate its behavior.
* (Refer to docs/05_context_mixer_deep_dive/ for detailed explanations of each component.)

4.4. Interpretation of the Optional Context Stack

If present, the ContextStackPart provides a broader, ordered list of contextual UCL-IDs. These contexts are typically more general than those managed by the Context Mixer. The most specific (leftmost) context in the stack has the greatest influence. The Context Mixer MAY reference or incorporate contexts from this stack into its own logic.

5. Interaction with LLMs and AI Systems

(This section would formalize the guidelines and patterns discussed in docs/08_ucl5_and_llms/, including how LLMs are instructed to parse UCL 5.0, the role of system prompts, UCL 5.0 as an output format, and the requesting/interpretation of reasoning traces.)

6. Extensibility

UCL 5.0 is designed for extensibility:

Custom Vocabularies/Ontologies: Users MUST define new UCL-IDs within their own namespaces (using custom @prefix declarations) for domain-specific concepts, actions, properties, and contextual aspects. These SHOULD be documented and, ideally, published as formal ontologies (e.g., RDF/OWL) for shared understanding.

Custom cm:Profiles: Users WILL define cm:Profiles tailored to their specific tasks and AI interaction needs. These profiles are themselves identified by UCL-IDs.

Custom cm:Strategys: While a set of standard strategies might be defined, systems MAY support custom strategy UCL-IDs if they implement unique mixing logic.

7. Binary Serialization (UCL-Bin 5.0 - Conceptual Outline)

A canonical binary serialization, UCL-Bin 5.0, is envisioned for efficient M2M communication.

Principles: Lossless round-tripping with textual UCL 5.0, optimized for graph structures, efficient UCL-ID and literal encoding, type tagging, extensibility, and platform independence.

Conceptual Structure: Would likely include a magic number, header/versioning, interning tables for URIs/strings, and serialized representations of the envelope, payload graph, and optional context stack.

(Refer to docs/12_binary_serialization_ucl5.md for a more detailed conceptual overview. A normative byte-level specification is future work.)

8. Security Considerations (Informative)

Input Validation: Processors MUST validate UCL 5.0 messages syntactically and SHOULD validate semantically where possible to prevent processing errors or malicious inputs.

URI Dereferencing: Exercise caution when dereferencing URIs from untrusted UCL messages. Implement appropriate safeguards (e.g., allow-lists, sandboxing).

Data Privacy: UCL messages can convey sensitive information. Implementers are responsible for appropriate security measures (encryption in transit and at rest, access control).

Contextual Manipulation: Maliciously crafted cm:Profile definitions or ^Modifiers could potentially mislead AI systems. Systems SHOULD have mechanisms to vet or restrict the application of untrusted contextual directives.

Resource Consumption: Parsing very large or complex PayloadGraphs or processing deeply nested cm:Profile logic could be resource-intensive. Implementations SHOULD consider limits and safeguards against denial-of-service attacks.

9. References
9.1. Normative References

RFC 2119: Key words for use in RFCs to Indicate Requirement Levels. Bradner, S., 1997.

RFC 3986: Uniform Resource Identifier (URI): Generic Syntax. Berners-Lee, T., Fielding, R., Masinter, L., 2005.

RFC 3987: Internationalized Resource Identifiers (IRIs). Duerst, M., Suignard, M., 2005.

UTF-8: (Refer to The Unicode Standard, Version X.X or ISO/IEC 10646).

RDF 1.1 Concepts and Abstract Syntax. W3C Recommendation, 25 February 2014.

Turtle - Terse RDF Triple Language. W3C Recommendation, 25 February 2014. (Primary syntactic basis for PayloadGraphBlock).

ABNF: Augmented BNF for Syntax Specifications: ABNF. RFC 5234, Crocker, D., Ed., Overell, P., 2008.

9.2. Informative References

UCL 4.2 "Enhanced" Specification (For historical context and migration).

Schema.org vocabulary. (https://schema.org/)

XML Schema Definition Language (XSD) Part 2: Datatypes. W3C Recommendation. (For literal datatypes like xsd:date, xsd:integer).

SPARQL 1.1 Query Language. W3C Recommendation, 21 March 2013. (For ABNF inspiration for URI/Literal components).

OWL 2 Web Ontology Language Document Overview (Second Edition). W3C Recommendation, 11 December 2012.

Appendix A: Core Vocabulary Definitions (Conceptual)

This appendix provides conceptual definitions for key terms within the predefined UCL 5.0 namespaces: ucl:, cm:, ucl_meta:, and llm:. These definitions are intended to clarify the semantics and intended usage. A full formal ontology (e.g., in OWL/RDF Turtle) would provide machine-interpretable semantics.

Conventions used:

Term (CURIE): prefix:LocalName

URI: <Full_URI>

Type: (e.g., rdfs:Class, rdf:Property, owl:ObjectProperty, owl:DatatypeProperty, owl:NamedIndividual)

Label: Human-readable name.

Comment: Detailed description.

Domain (for properties): The class(es) of subjects it typically applies to (conceptual).

Range (for properties): The class(es) or datatype of objects/values it typically links to (conceptual).

A.1. ucl: Namespace

URI Base: <http://ucl-spec.org/5.0/core#>

Term: ucl:Message

URI: <http://ucl-spec.org/5.0/core#Message>

Type: rdfs:Class

Label: "UCL Message"

Comment: Represents the concept of a complete UCL 5.0 message, encompassing its envelope, payload graph, and optional context stack.

Term: ucl:this

URI: <http://ucl-spec.org/5.0/core#this>

Type: rdf:Property (conventionally used as a subject, but can be thought of as a way to anchor statements to the current message context)

Label: "This Message Context/Payload Subject"

Comment: A conventional UCL-ID used typically as the subject for triples within the PayloadGraph that directly relate to the overall message's intent or primary parameters, especially when no other specific entity URI is the central subject of the payload. Its precise interpretation can be further refined by the Operation_UCLID.

Domain: ucl:Message (conceptually, statements using ucl:this as subject are part of the message payload)

Range: Any resource or literal.

Term: ucl:hasOperation

URI: <http://ucl-spec.org/5.0/core#hasOperation>

Type: rdf:Property, owl:ObjectProperty

Label: "Has Operation"

Comment: Links a subject (often ucl:this or a representation of the message instance) to the Operation_UCLID that is being executed. This is primarily for graph representation within the payload if the operation needs to be referenced or qualified there, as the main operation is already in the envelope.

Domain: ucl:Message (or a subject representing the payload context)

Range: rdfs:Resource (specifically, a UCL-ID representing an operation)

Term: ucl:hasParameter

URI: <http://ucl-spec.org/5.0/core#hasParameter>

Type: rdf:Property

Label: "Has Parameter"

Comment: A generic property to link a subject (e.g., ucl:this or an operation instance) to a parameter. The parameter itself might be a literal or a structured entity (e.g., a blank node described by schema:PropertyValue).

Domain: rdfs:Resource

Range: rdfs:Resource or rdfs:Literal

Term: ucl:Value

URI: <http://ucl-spec.org/5.0/core#Value>

Type: rdfs:Class

Label: "UCL Value"

Comment: A superclass for all types of values that can appear as objects in triples or as modifier values. Literals and UCL-IDs are implicitly ucl:Values.

Term: ucl:Null

URI: <http://ucl-spec.org/5.0/core#Null>

Type: owl:NamedIndividual (representing the concept of null)

Label: "Null Value"

Comment: Represents the specific concept of a null or absent value. Used as an object in a triple.

Term: ucl:Error

URI: <http://ucl-spec.org/5.0/core#Error>

Type: rdfs:Class

Label: "UCL Error"

Comment: Represents an error condition or message, typically used in response messages.

Key Properties it uses: ucl:errorCode, ucl:errorMessage, ucl:errorDetails (optional).

Term: ucl:errorCode

URI: <http://ucl-spec.org/5.0/core#errorCode>

Type: rdf:Property, owl:DatatypeProperty

Label: "Error Code"

Comment: A code (string or integer) identifying a specific error type.

Domain: ucl:Error

Range: xsd:string or xsd:integer

Term: ucl:errorMessage

URI: <http://ucl-spec.org/5.0/core#errorMessage>

Type: rdf:Property, owl:DatatypeProperty

Label: "Error Message"

Comment: A human-readable message describing the error.

Domain: ucl:Error

Range: xsd:string

A.2. cm: Namespace (Context Mixer)

URI Base: <http://ucl-spec.org/5.0/context-mixer#>

Term: cm:Profile

URI: <http://ucl-spec.org/5.0/context-mixer#Profile>

Type: rdfs:Class

Label: "Context Mixer Profile"

Comment: A configuration defining how contextual influences are managed for an operation.

Key Properties it uses: schema:name, schema:description, cm:definesFocus, cm:appliesStrategy, cm:guidesOutput.

Term: cm:profile (lowercase, for modifier)

URI: <http://ucl-spec.org/5.0/context-mixer#profile>

Type: rdf:Property, owl:ObjectProperty

Label: "Applies Profile"

Comment: Used as a modifier predicate (^cm:profile) in the UCL message envelope to link an operation to a specific cm:Profile instance (UCL-ID).

Domain: (Implicitly, the UCL Operation in the envelope)

Range: cm:Profile

Term: cm:definesFocus

URI: <http://ucl-spec.org/5.0/context-mixer#definesFocus>

Type: rdf:Property, owl:ObjectProperty

Label: "Defines Focus"

Comment: Links a cm:Profile to one or more cm:FocusElement instances or descriptions (often an RDF list of blank nodes, each describing a focus element).

Domain: cm:Profile

Range: rdf:List (of cm:FocusElement descriptions) or cm:FocusElement

Term: cm:FocusElement

URI: <http://ucl-spec.org/5.0/context-mixer#FocusElement>

Type: rdfs:Class

Label: "Focus Element"

Comment: Represents a specific contextual aspect, theme, or constraint within a cm:Profile.

Key Properties it uses: cm:onAspect, cm:withWeight, cm:withFilter.

Term: cm:onAspect

URI: <http://ucl-spec.org/5.0/context-mixer#onAspect>

Type: rdf:Property, owl:ObjectProperty

Label: "On Aspect"

Comment: Links a cm:FocusElement to the UCL-ID representing the contextual concept itself.

Domain: cm:FocusElement

Range: rdfs:Resource (a UCL-ID for a contextual concept)

Term: cm:withWeight

URI: <http://ucl-spec.org/5.0/context-mixer#withWeight>

Type: rdf:Property, owl:DatatypeProperty

Label: "With Weight"

Comment: Specifies the numerical weight or importance of a cm:FocusElement.

Domain: cm:FocusElement

Range: xsd:decimal or xsd:float (typically normalized, e.g., 0.0-1.0)

Term: cm:withFilter

URI: <http://ucl-spec.org/5.0/context-mixer#withFilter>

Type: rdf:Property

Label: "With Filter"

Comment: Provides additional instructions or criteria (as a string literal or a UCL-ID pointing to a filter definition) that refine how a cm:FocusElement is applied.

Domain: cm:FocusElement

Range: xsd:string or rdfs:Resource (UCL-ID of a filter)

Term: cm:appliesStrategy

URI: <http://ucl-spec.org/5.0/context-mixer#appliesStrategy>

Type: rdf:Property, owl:ObjectProperty

Label: "Applies Strategy"

Comment: Links a cm:Profile to the UCL-ID of the mixing strategy to be used.

Domain: cm:Profile

Range: cm:Strategy (or a subclass/individual thereof)

Term: cm:Strategy

URI: <http://ucl-spec.org/5.0/context-mixer#Strategy>

Type: rdfs:Class

Label: "Mixing Strategy"

Comment: A superclass for different algorithms or logics for combining contextual influences. Specific strategies (e.g., cm:strategy:WeightedSum, cm:strategy:PrioritizedOverride, cm:strategy:LLMManaged) would be individuals or subclasses of this.

Term: cm:guidesOutput

URI: <http://ucl-spec.org/5.0/context-mixer#guidesOutput>

Type: rdf:Property

Label: "Guides Output"

Comment: Links a cm:Profile to instructions or constraints (as a string literal or a UCL-ID pointing to an output schema/template) regarding the desired characteristics of the operation's output.

Domain: cm:Profile

Range: xsd:string or rdfs:Resource (UCL-ID of an output definition)

A.3. ucl_meta: Namespace (Metadata)

URI Base: <http://ucl-spec.org/5.0/metadata#>

Term: ucl_meta:taskDescription

URI: <http://ucl-spec.org/5.0/metadata#taskDescription>

Type: rdf:Property, owl:DatatypeProperty

Label: "Task Description"

Comment: Used as a modifier predicate (^ucl_meta:taskDescription) to provide a human-readable description of the overall task of the UCL message.

Domain: (Implicitly, the UCL Operation in the envelope)

Range: xsd:string

Term: ucl_meta:outputFormat

URI: <http://ucl-spec.org/5.0/metadata#outputFormat>

Type: rdf:Property

Label: "Output Format"

Comment: Used as a modifier predicate (^ucl_meta:outputFormat) to specify the desired high-level format of the output (e.g., a string like "PDF", "JSON", or a UCL-ID like ucl_format:JSONLD).

Domain: (Implicitly, the UCL Operation in the envelope)

Range: xsd:string or rdfs:Resource (UCL-ID of a format)

Term: ucl_meta:priority

URI: <http://ucl-spec.org/5.0/metadata#priority>

Type: rdf:Property, owl:DatatypeProperty

Label: "Priority"

Comment: Used as a modifier predicate, typically for items in the optional Context Stack (e.g., # ucl_ctx:Emergency ^ucl_meta:priority 1.0) or potentially for other elements where prioritization is needed.

Domain: rdfs:Resource (e.g., a context UCL-ID)

Range: xsd:decimal or xsd:float or a UCL-ID representing a priority level (e.g. ucl_meta_val:High).

Term: ucl_meta:timestamp

URI: <http://ucl-spec.org/5.0/metadata#timestamp>

Type: rdf:Property, owl:DatatypeProperty

Label: "Timestamp"

Comment: Used as a modifier predicate or in a payload to specify a relevant timestamp (e.g., message creation time, event time).

Domain: rdfs:Resource

Range: xsd:dateTime

Term: ucl_meta:version

URI: <http://ucl-spec.org/5.0/metadata#version>

Type: rdf:Property, owl:DatatypeProperty

Label: "Version"

Comment: Used to specify the version of a resource, such as a cm:Profile or a custom vocabulary element.

Domain: rdfs:Resource

Range: xsd:string

Term: ucl_meta:value (Generic value for metadata properties)

URI: <http://ucl-spec.org/5.0/metadata#value>

Type: rdf:Property

Label: "Metadata Value"

Comment: A generic property that can be used when a metadata predicate needs to point to a specific, often enumerated or controlled, UCL-ID value (e.g. ^ucl_meta:someSetting ucl_meta:value:SpecificChoice). It's often better to define more specific properties, but this provides flexibility.

Domain: rdfs:Resource

Range: rdfs:Resource (UCL-ID)

A.4. llm: Namespace (LLM Interaction)

URI Base: <http://ucl-spec.org/5.0/llm#>

Term: llm:languageForResponse

URI: <http://ucl-spec.org/5.0/llm#languageForResponse>

Type: rdf:Property, owl:DatatypeProperty

Label: "Language for Response"

Comment: Used as a modifier predicate (^llm:languageForResponse) to specify the desired language (e.g., IETF BCP 47 language tag like "en-US", "fr-CA") for the LLM's textual response.

Domain: (Implicitly, the UCL Operation in the envelope)

Range: xsd:string (specifically a language tag)

Term: llm:requestReasoningTrace

URI: <http://ucl-spec.org/5.0/llm#requestReasoningTrace>

Type: rdf:Property, owl:DatatypeProperty

Label: "Request Reasoning Trace"

Comment: Used as a modifier predicate (^llm:requestReasoningTrace) with a boolean value to request a reasoning trace from the LLM.

Domain: (Implicitly, the UCL Operation in the envelope)

Range: xsd:boolean

Term: llm:reasoningTraceDetail

URI: <http://ucl-spec.org/5.0/llm#reasoningTraceDetail>

Type: rdf:Property, owl:ObjectProperty

Label: "Reasoning Trace Detail Level"

Comment: Used as a modifier predicate (^llm:reasoningTraceDetail) to specify the desired level of detail for the reasoning trace. The value is a UCL-ID representing a predefined detail level.

Domain: (Implicitly, the UCL Operation in the envelope)

Range: rdfs:Resource (UCL-ID for a detail level, e.g., from ucl_meta:value:*)

Term: llm:temperature

URI: <http://ucl-spec.org/5.0/llm#temperature>

Type: rdf:Property, owl:DatatypeProperty

Label: "LLM Temperature"

Comment: Used as a modifier predicate (^llm:temperature) to suggest a temperature setting for LLM generation (controls randomness).

Domain: (Implicitly, the UCL Operation in the envelope)

Range: xsd:decimal (typically 0.0 to 2.0)

Term: llm:maxTokens

URI: <http://ucl-spec.org/5.0/llm#maxTokens>

Type: rdf:Property, owl:DatatypeProperty

Label: "LLM Max Tokens"

Comment: Used as a modifier predicate (^llm:maxTokens) to suggest a maximum token limit for the LLM's response.

Domain: (Implicitly, the UCL Operation in the envelope)

Range: xsd:integer

(End of Conceptual Appendix A. A full version would be much more extensive, including terms from schema:, rdf:, xsd: that are heavily relied upon, or pointing to their canonical definitions.)

Appendix B: ABNF Grammar (Conceptual Draft)

This appendix provides a conceptual Augmented Backus-Naur Form (ABNF, RFC 5234) grammar for the textual syntax of UCL 5.0. This grammar is a draft and requires further refinement, formalization, and testing to ensure completeness, correctness, and lack of ambiguity. It particularly needs detailed expansion for the PayloadGraphBlock to fully cover Turtle-like triple constructs.

Core Rules (from RFC 5234 where applicable):

ALPHA          =  %x41-5A / %x61-7A   ; A-Z / a-z
DIGIT          =  %x30-39            ; 0-9
HEXDIG         =  DIGIT / "A" / "B" / "C" / "D" / "E" / "F" ; Case-insensitive for A-F
SP             =  %x20
HTAB           =  %x09
CR             =  %x0D
LF             =  %x0A
CRLF           =  CR LF
WSP            =  SP / HTAB                ; Whitespace
EOL            =  CRLF / LF / CR           ; End of line (parser should normalize)
COMMENT        =  "//" *(%x00-09 / %x0B-0C / %x0E-FF) EOL ; Basic comment to end of line
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Abnf
IGNORE_WHEN_COPYING_END

UCL 5.0 Message Structure:

UCL5Message    =  *PrefixDeclaration
                  Envelope
                  PayloadGraphSeparator
                  PayloadGraphBlock
                  [ContextStackSeparator ContextStackPart]
                  *WSP ; Allow trailing whitespace

PrefixDeclaration = "@prefix" 1*WSP PN_PREFIX ":" 1*WSP "<" IRIREF ">" *WSP (*COMMENT) EOL

Envelope       =  [SourceUCLID 1*WSP ">" 1*WSP]
                  TargetUCLID 1*WSP
                  "execute" 1*WSP OperationUCLID
                  *Modifier
                  *WSP (*COMMENT) EOL

SourceUCLID    =  UCLID
TargetUCLID    =  UCLID
OperationUCLID =  UCLID

Modifier       =  1*WSP "^" PredicateUCLID 1*WSP ValueToken

PayloadGraphSeparator = *WSP ":" *WSP (*COMMENT) EOL

PayloadGraphBlock   = *WSP "{" EOL      ; Conceptual Delimiter - actual content is Turtle subset
                      *WS_TRIPLES        ; Whitespace or comments allowed between triples
                      *(Triple *WS_TRIPLES)
                      *WSP "}" *WSP EOL ; Conceptual Delimiter
WS_TRIPLES     = *(WSP / COMMENT / EOL) ; Whitespace/comments allowed between triples

ContextStackSeparator = *WSP "#" *WSP (*COMMENT) EOL

ContextStackPart    = UCLIDwithModifiers *(1*WSP "/" 1*WSP UCLIDwithModifiers) *WSP (*COMMENT) EOL
UCLIDwithModifiers  = UCLID *ModifierInContext
ModifierInContext   = 1*WSP "^" PredicateUCLID 1*WSP ValueToken
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Abnf
IGNORE_WHEN_COPYING_END

UCL-IDs and Values:
UCLID          =  CURIE / AbsoluteIRI
AbsoluteIRI    =  "<" IRIREF ">"
CURIE          =  [PN_PREFIX] ":" PN_LOCAL
PN_PREFIX      =  PN_CHARS_BASE *((PN_CHARS / ".") *PN_CHARS)?
PN_LOCAL       =  (PN_CHARS_U / DIGIT / "%" HEXDIG HEXDIG / PN_LOCAL_ESC)
                  *((PN_CHARS / "." / ":" / "%" HEXDIG HEXDIG / PN_LOCAL_ESC) * (PN_CHARS / ":" / "%" HEXDIG HEXDIG / PN_LOCAL_ESC) )?
                  ; Based on SPARQL/Turtle PN_LOCAL, allowing ':' for things like myapp:action:DoThis

ValueToken     =  UCLID / Literal

Literal        =  StringLiteral / NumericLiteral / BooleanLiteral / TypedLiteral / LangTaggedString / NullLiteral
StringLiteral  =  DQUOTE *SCHAR DQUOTE / (TRIPLE_DQUOTE *TSCHAR TRIPLE_DQUOTE)
DQUOTE         =  %x22 ; "
TRIPLE_DQUOTE  =  DQUOTE DQUOTE DQUOTE
SCHAR          =  PN_CHARS_U / %x20 / %x21 / %x23-5B / %x5D-7E / ESCAPE_SEQ ; Chars in single-quoted string, excluding " and \
TSCHAR         =  PN_CHARS_U / %x20-7E / ESCAPE_SEQ ; Chars in triple-quoted string, allows " and \
ESCAPE_SEQ     =  "\\" (%x22 / %x5C / %x6E / %x72 / %x74 / %x62 / %x66 / UNICODE_ESC) ; \", \\, \n, \r, \t, \b, \f
UNICODE_ESC    =  ("u" HEXDIG HEXDIG HEXDIG HEXDIG) / ("U" HEXDIG HEXDIG HEXDIG HEXDIG HEXDIG HEXDIG HEXDIG HEXDIG)

NumericLiteral =  INTEGER / DECIMAL / DOUBLE
INTEGER        =  [SIGN] 1*DIGIT
DECIMAL        =  [SIGN] (1*DIGIT "." *DIGIT / "." 1*DIGIT)
DOUBLE         =  [SIGN] (1*DIGIT "." *DIGIT EXPONENT / "." 1*DIGIT EXPONENT / 1*DIGIT EXPONENT)
SIGN           =  "+" / "-"
EXPONENT       =  ("e" / "E") [SIGN] 1*DIGIT

BooleanLiteral =  "true" / "false"
NullLiteral    =  "ucl" ":" "Null" ; CURIE for ucl:Null

TypedLiteral   =  StringLiteral "^^" PredicateUCLID ; Datatype is a UCLID
LangTaggedString = StringLiteral "@" LANGTAG
LANGTAG        =  1*8ALPHA *("-" 1*8(ALPHA / DIGIT)) ; RFC 5646

PredicateUCLID =  UCLID ; Predicates are UCL-IDs
Use code with caution.
Abnf
Triple Structure (Simplified - Needs full Turtle subset ABNF):
Triple         =  Subject WS_TRIPLES PredicateOrVerbList WS_TRIPLES ObjectList WS_TRIPLES "." (*COMMENT) EOL
Subject        =  UCLID / BlankNode
PredicateOrVerbList = PredicateObjectList / (Verb WS_TRIPLES ObjectList)
Verb           =  "a" / PredicateUCLID ; 'a' is rdf:type
ObjectList     =  Object *(WS_TRIPLES "," WS_TRIPLES Object)
Object         =  UCLID / Literal / BlankNode / Collection

PredicateObjectList = Verb WS_TRIPLES ObjectList *(WS_TRIPLES ";" [WS_TRIPLES Verb WS_TRIPLES ObjectList])

BlankNode      =  BLANK_NODE_LABEL / ANON_BLANK_NODE
BLANK_NODE_LABEL = "_:" (PN_CHARS_U / DIGIT) *((PN_CHARS / ".") *PN_CHARS)?
ANON_BLANK_NODE  = "[" *WS_TRIPLES [PredicateObjectList] *WS_TRIPLES "]"

Collection     =  "(" *WS_TRIPLES * (Object *WS_TRIPLES) ")" ; RDF List
Use code with caution.
Abnf
Character Classes (Inspired by Turtle/SPARQL ABNF):
IRIREF         =  "<" *(compl_greater / UNICODE_ESC)* ">" ; Chars allowed in IRIs, excluding ">"
compl_greater  =  %x00-3B / %x3D / %x3F-FF

PN_CHARS_BASE  =  ALPHA / %xC0-D6 / %xD8-F6 / %xF8-2FF / %x370-37D / %x37F-1FFF /
                  %x200C-200D / %x2070-218F / %x2C00-2FEF / %x3001-D7FF /
                  %xF900-FDCF / %xFDF0-FFFD ; %x10000-EFFFF (outside BMP not shown easily in ABNF)
PN_CHARS_U     =  PN_CHARS_BASE / "_"
PN_CHARS       =  PN_CHARS_U / DIGIT / %xB7 / %x0300-036F / %x203F-2040
PN_LOCAL_ESC   =  "\\" ( "_" / "~" / "." / "-" / "!" / "$" / "&" / "'" / "(" / ")" /
                  "*" / "+" / "," / ";" / "=" / "/" / "?" / "#" / "@" / "%" )

; Note: Full PN_CHARS_BASE for %x10000-EFFFF would need surrogate pair handling or direct inclusion if ABNF tool supports.
; This grammar requires careful alignment with official Turtle ABNF for robustness.
