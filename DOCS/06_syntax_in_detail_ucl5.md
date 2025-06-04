# UCL 5.0 Syntax in Detail

This document provides a detailed explanation of the textual syntax for UCL (Universal Contextual Language) Version 5.0 "GraphNative". It builds upon the core concepts introduced earlier and clarifies how each part of a UCL 5.0 message is constructed.

While UCL 5.0 aims for semantic richness, its textual syntax strives for a balance between expressiveness and parsability, drawing inspiration from RDF Turtle for its graph payload.

## 1. Overall Message Structure

A UCL 5.0 message is a single UTF-8 text string. Its top-level structure consists of:

`[PrefixDeclarations]`
`Envelope`
`PayloadGraphSeparator`
`PayloadGraphBlock`
`[ContextStackSeparator ContextStackPart]`

Let's examine each component.

## 2. Prefix Declarations (`PrefixDeclarations`)

*   **Purpose:** To define short aliases (prefixes) for long base URIs, making UCL-IDs within the message more concise and readable.
*   **Syntax:** `@prefix declared_prefix: <URI_Base_IRI>`
    *   Must appear at the very beginning of the UCL message, before any other content.
    *   Each declaration is typically on its own line, followed by a `NEWLINE` (`%x0A`).
    *   The `URI_Base_IRI` must be enclosed in angle brackets (`<...>`).
*   **Example:**
    ```ucl
    @prefix schema: <http://schema.org/>
    @prefix cm: <http://ucl-spec.org/5.0/context-mixer#>
    @prefix myapp: <http://example.com/myapp-ontology#>
    # Rest of the UCL message follows...
    ```
*   Refer to [Core Concepts: UCL-IDs, Prefixes, and URIs](./04_core_concepts_ucl5/04_ucl_ids_prefixes_and_uris.md) for more details.

## 3. Envelope

The envelope contains the primary routing and operational directives.

*   **Syntax:** `[Source_UCLID] > Target_UCLID execute Operation_UCLID [^ModifierPredicate1 ModifierValue1] ...`
*   **Components:**
    *   **`Source_UCLID` (Optional):** A UCL-ID identifying the message sender.
    *   **`>` (Direction Operator):** Indicates flow from source to target.
    *   **`Target_UCLID` (Mandatory):** A UCL-ID identifying the message recipient or primary subject.
    *   **`execute` (Keyword):** The primary verb for UCL 5.0 operations.
    *   **`Operation_UCLID` (Mandatory):** A UCL-ID specifying the precise action.
    *   **`^ModifierPredicate ModifierValue` (Optional Modifiers):** Zero or more modifiers, each starting with `^`, providing high-level parameters for the operation.
*   **Whitespace:** Canonical space (a single space character `%x20`) is generally used between these top-level components of the envelope. Parsers in a permissive mode may tolerate more flexible whitespace.
*   Refer to [Core Concepts: The UCL Message Envelope](./04_core_concepts_ucl5/02_ucl_message_envelope.md) and [Modifiers](./04_core_concepts_ucl5/03_modifiers_and_payload.md) for details.

## 4. Payload Graph Separator (`PayloadGraphSeparator`)

*   **Purpose:** Marks the end of the envelope and the beginning of the `PayloadGraph`.
*   **Syntax:** `:` (a single colon character).
*   **Mandatory:** This separator is mandatory if a `PayloadGraphBlock` is present.
*   Refer to [Core Concepts: The Payload Graph Separator](./04_core_concepts_ucl5/03_modifiers_and_payload.md).

## 5. Payload Graph Block (`PayloadGraphBlock`)

*   **Purpose:** Contains the core data, parameters, and detailed semantic information for the operation, structured as a graph of triples.
*   **Conceptual Delimiters:** While not strictly part of a formal Turtle-like grammar for embedded graphs, in textual UCL 5.0, the graph payload is often conceptually enclosed or visually grouped, for example:
    ```ucl
    ... execute myapp:action:DoSomething :
    {  // Conceptual start of graph block
        (ucl:this) myapp:hasProperty "value1" ;
                   myapp:anotherProperty 123 .
        // More triples
    } // Conceptual end of graph block
    # ...
    ```
    The curly braces `{...}` here are primarily for human readability and to clearly delineate the graph payload within the overall UCL message structure, especially when the payload might be multi-line. The actual parsing relies on valid triple syntax. Some UCL 5.0 implementations or style guides might formalize these delimiters.

*   **Syntax of Triples:**
    *   **Basic Form:** `Subject Predicate Object .` (Note the terminating period `.`)
    *   **Subject:** A UCL-ID (CURIE or full URI in `<>`) or a Blank Node.
    *   **Predicate:** A UCL-ID (CURIE or full URI in `<>`).
    *   **Object:** A UCL-ID, a Literal (String, Number, Boolean), or a Blank Node.
    *   **String Literals:** Enclosed in double quotes (`"..."`). Escaping rules apply (e.g., `\"`, `\\`, `\n`). Multi-line strings can be represented using `"""..."""`.
    *   **Numeric Literals:** Integers (`123`), decimals (`3.14`), scientific notation (`1e6`).
    *   **Boolean Literals:** `true`, `false`.
    *   **Typed Literals:** `"<value>"^^<Datatype_UCL-ID>` (e.g., `"2024-03-15"^^xsd:date`).
    *   **Language-Tagged Strings:** `"text"@language-tag` (e.g., `"Bonjour"@fr`).

*   **Syntactic Sugar (Turtle-like):**
    *   **Semicolon (`;`):** To specify multiple predicates and objects for the same subject.
        ```turtle
        (ucl:this)
            myapp:propertyA "valueA" ;
            myapp:propertyB 123 .
        ```
    *   **Comma (`,`):** To specify multiple objects for the same subject-predicate pair.
        ```turtle
        (ucl:this) myapp:hasKeyword "tag1", "tag2", "tag3" .
        ```
    *   **`a` shorthand for `rdf:type`:**
        ```turtle
        (ucl:this) a myapp:MyCustomType .
        ```
    *   **Blank Nodes:**
        *   `_:bnodeLabel` (labeled blank node)
        *   `[]` (anonymous blank node, often used for structuring)
        ```turtle
        (ucl:this) myapp:hasAddress [
                       a schema:PostalAddress ;
                       schema:streetAddress "123 Main St"
                   ] .
        ```
    *   **Collections (Lists):** RDF Lists using `(...)` notation.
        ```turtle
        (ucl:this) myapp:hasOrderedItems ( item:First item:Second item:Third ) .
        ```
*   Refer to [Core Concepts: Payload Graph Literals and Structures](./04_core_concepts_ucl5/05_payload_graph_literals_and_structures.md) for more details on data representation within the graph.

## 6. Context Stack Separator (`ContextStackSeparator`) (Optional)

*   **Purpose:** Marks the beginning of the optional `ContextStackPart`.
*   **Syntax:** `#` (a single hash character).
*   **Optionality:** Only present if a `ContextStackPart` follows.

## 7. Context Stack Part (`ContextStackPart`) (Optional)

*   **Purpose:** Provides a high-level, ordered list of contexts, complementary to the more granular Context Mixer.
*   **Syntax:** One or more `UCL-ID`s, separated by `/` (slash character).
    *   Order is significant: typically most specific on the left, most general on the right.
    *   Each context in the stack can also have its own modifiers using `^Predicate Value` (though this is an advanced feature and its interpretation would be highly dependent on the processing system).
        Example: `ucl_ctx:ProjectAlpha ^ucl_meta:version "2.1" / ucl_ctx:DevelopmentPhase`
*   **Optionality:** The Context Stack is optional in UCL 5.0, as the primary mechanism for detailed context management is the Context Mixer. It can be omitted entirely. If present, there must be at least one context UCL-ID.
*   **Example:**
    ```ucl
    # ucl_project:Phoenix / ucl_env:Staging ^ucl_meta:debugLevel "verbose"
    ```

## Whitespace and Comments

*   **Whitespace:**
    *   Between major envelope components and around the payload/context separators, a single space is canonical, but parsers in permissive mode may accept more.
    *   Within the `PayloadGraph`, whitespace rules generally follow those of Turtle (e.g., whitespace can be used freely between tokens like subjects, predicates, objects, and delimiters like `.`, `;`, `,`).
    *   Leading/trailing whitespace in the overall message should generally be trimmed by parsers.
*   **Comments:**
    *   Single-line comments start with `//` and extend to the end of the line. They can appear almost anywhere where whitespace is allowed (except within string literals).
    *   They are ignored by the parser and are for human readability.
    ```ucl
    // This is a UCL 5.0 message
    @prefix schema: <http://schema.org/> // For common types

    ucl:id:MySystem > llm:agent:MainAI // Target the main AI
    execute myapp:action:ProcessRequest // Perform this action
        ^cm:profile cm:profile:Default // Use this mixer profile
    :
    { // Start of payload graph
        (ucl:this) a myapp:RequestType ; // Define the request type
                   myapp:hasData "Sample data" . // Add some data
    }
    // No optional context stack in this example
    ```

This detailed syntax provides the framework for constructing rich, semantically precise, and contextually aware messages in UCL 5.0. Understanding these rules is key to both authoring valid UCL 5.0 messages and building parsers or interpreters for the language.

