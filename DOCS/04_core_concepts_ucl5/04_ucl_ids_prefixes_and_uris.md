# Core Concepts of UCL 5.0: UCL-IDs, Prefixes, and URIs

The foundation of semantic clarity and interoperability in UCL 5.0 "GraphNative," much like in its predecessor, rests upon **UCL-IDs (Universal Contextual Language Identifiers)**. These identifiers provide unique and unambiguous references to any conceivable resource, concept, property, or action.

This document reiterates their importance and usage within the UCL 5.0 framework, emphasizing consistency with established Semantic Web principles.

## What is a UCL-ID in UCL 5.0?

A UCL-ID is a unique identifier used to name and reference:

*   **Entities:** Specific things or instances (e.g., a particular user, a specific product, a defined Context Mixer Profile).
    *   Example: `product:ID_XYZ789`, `cm:profile:FinancialAnalysisDefault`
*   **Types/Classes:** Categories or classifications of things.
    *   Example: `schema:Book`, `ucl:Error`, `cm:FocusElement`
*   **Properties/Predicates:** Attributes of entities or relationships between them. These form the "verbs" or "links" in the `PayloadGraph` triples.
    *   Example: `schema:name`, `ucl:hasParameter`, `cm:onAspect`
*   **Actions/Operations:** Specific tasks or processes that can be executed.
    *   Example: `myapp:action:GenerateReport`, `llm_task:TranslateText`
*   **Contextual Aspects:** Concepts used within the Context Mixer or the optional Context Stack.
    *   Example: `market:trend:Sustainability`, `ucl_style:Formal`
*   **Controlled Values:** Specific enumerated values from a defined vocabulary.
    *   Example: `ucl:Null`, `ucl_meta:value:High` (for priority)

The core principle is that each UCL-ID should possess a **globally unique and stable meaning**, ideally defined within a published vocabulary or ontology.

## Canonical Form: URIs (Uniform Resource Identifiers)

The definitive, canonical representation of a UCL-ID is a **URI (Uniform Resource Identifier)**, as specified by RFC 3986. For international characters, IRIs (Internationalized Resource Identifiers, RFC 3987) are used, which map to URIs for transport.

*   **Why URIs are Central to UCL 5.0:**
    1.  **Global Unambiguity:** A URI like `http://schema.org/Person` refers to the *exact same concept* regardless of who uses it or in what UCL message it appears. This is fundamental for avoiding misinterpretations.
    2.  **Machine Processability:** URIs are designed to be easily parsed and processed by software.
    3.  **Interoperability:** Systems can achieve semantic interoperability by agreeing on the URIs (and their associated definitions in ontologies) for the terms they exchange.
    4.  **Linked Data Principles:** URIs can often be dereferenced (looked up on the web) to find more information about the resource they identify (e.g., its definition, related concepts). This links UCL messages to a broader web of data.

**Examples of UCL-IDs as full URIs in a UCL 5.0 context:**
*   `<http://schema.org/Book>` (as a type)
*   `<http://ucl-spec.org/5.0/context-mixer#withWeight>` (as a property for the Context Mixer)
*   `<http://example.com/myapp-ontology#action:ProcessOrder>` (as a custom operation)

## Compact Form: CURIEs (Prefixed Names)

To enhance readability and reduce the verbosity of UCL 5.0 messages, full URIs are typically shortened using a compact form similar to **CURIEs (Compact URI Expressions)**.

*   **Syntax:** `prefix:local_part`
    *   `prefix`: A short, memorable alias for a base URI (e.g., `schema`, `cm`, `myapp`).
    *   `local_part`: The segment of the URI that is unique within that prefix's namespace (e.g., `Book`, `withWeight`, `action:ProcessOrder`).

*   **Example:**
    *   `schema:Book` (compact form of `<http://schema.org/Book>`)
    *   `cm:withWeight` (compact form of `<http://ucl-spec.org/5.0/context-mixer#withWeight>`)

## `@prefix` Declarations: Mapping Prefixes to Base URIs

For a UCL 5.0 parser to expand a `prefix:local_part` back to its full URI, the `prefix` must be defined. This is achieved using **`@prefix` declarations** at the very beginning of a UCL 5.0 message.

*   **Syntax:** `@prefix declared_prefix: <FULL_URI_BASE_ENCLOSED_IN_ANGLE_BRACKETS>`
    *   Each declaration typically resides on its own line and is followed by a newline.
*   **Example:**
    ```ucl
    @prefix schema: <http://schema.org/>
    @prefix cm: <http://ucl-spec.org/5.0/context-mixer#>
    @prefix myapp: <http://example.com/myapp-ontology#>

    // ... rest of the UCL 5.0 message ...
    // Example usage later:
    // ... execute myapp:action:ProcessOrder ^cm:profile cm:profile:OrderHandling ...
    // ... { (ucl:this) schema:customer myapp:customer:Alice . } ...
    ```
*   **Scope:** `@prefix` declarations are lexically scoped to the message in which they appear.

### Predefined and Well-Known Prefixes in UCL 5.0

UCL 5.0 will continue to recognize a small set of **predefined prefixes** that generally do not require explicit declaration in every message, as their base URIs are fundamental or extremely common. The exact list will be part of the formal UCL 5.0 specification, but would likely include:

*   `ucl:` (for core UCL 5.0 terms, e.g., `<http://ucl-spec.org/5.0/core#>`)
*   `cm:` (for Context Mixer terms, e.g., `<http://ucl-spec.org/5.0/context-mixer#>`)
*   `ucl_meta:` (for UCL metadata, e.g., `<http://ucl-spec.org/5.0/metadata#>`)
*   `rdf:` (`<http://www.w3.org/1999/02/22-rdf-syntax-ns#>`)
*   `rdfs:` (`<http://www.w3.org/2000/01/rdf-schema#>`)
*   `xsd:` (`<http://www.w3.org/2001/XMLSchema#>`)
*   `owl:` (`<http://www.w3.org/2002/07/owl#>`)

Commonly used public prefixes like `schema:` (`<http://schema.org/>`) and `wd:` (`<http://www.wikidata.org/entity/>`) are also often supported by parsers even without explicit declaration, but for maximum clarity and to avoid ambiguity with potential custom prefixes, **explicitly declaring all non-predefined prefixes is best practice in UCL 5.0.**

## Namespaces and Vocabularies/Ontologies

*   A **Namespace** in UCL 5.0 is the conceptual space defined by a base URI. All UCL-IDs starting with that base URI belong to that namespace.
*   A **Vocabulary** or **Ontology** is a formal definition of terms (UCL-IDs/URIs) within one or more namespaces, specifying their meanings, types, properties, and relationships.
    *   UCL 5.0 strongly advocates for the **reuse of established, open vocabularies** (Schema.org, Dublin Core, FOAF, SKOS, etc.) to maximize interoperability.
    *   For domain-specific requirements, users will **define custom vocabularies/ontologies** using their own namespaces. Publishing these definitions (e.g., as RDF/OWL files accessible via their URIs) is crucial for shared understanding.

The rigorous use of URI-based UCL-IDs, managed via prefixes and grounded in well-defined vocabularies, is what enables UCL 5.0 to serve as a precise and semantically rich language for advanced AI communication and data representation.



