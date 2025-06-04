# Core Concepts of UCL 5.0: Payload Graph Literals and Structures

The `PayloadGraph` in UCL 5.0 is where the core data and detailed parameters for an operation reside. As a graph of semantic triples `(subject predicate object)`, the `object` part of a triple can be either a UCL-ID (URI) or a **Literal Value**. Furthermore, complex structures like lists and nested descriptions are also represented using graph patterns.

This document details how different types of data are represented as objects in the `PayloadGraph` and how common structures are formed.

## 1. Literal Values as Objects

Literals are concrete data values. In a triple `(subject predicate object)`, if the `object` is a literal, it represents a direct value associated with the subject via the predicate.

### 1.1. Strings

*   **Representation:** A sequence of UTF-8 characters enclosed in double quotes (`"`).
*   **Escaping:** Standard Turtle-like escaping applies: `\` is used for double quotes (`\"`), backslashes (`\\`), and common control characters (`\n`, `\r`, `\t`). Unicode characters can be represented as `\uXXXX` or `\UXXXXXXXX`.
*   **Example:**
    ```turtle
    @prefix schema: <http://schema.org/> .
    @prefix ucl: <http://ucl-spec.org/5.0/core#> .

    (ucl:this) schema:name "Introduction to UCL 5.0" ;
               schema:description "A comprehensive guide with \"many\" examples." .
    ```

### 1.2. Numbers

*   **Representation:** Numeric literals, including integers (e.g., `42`, `-100`) and decimals (e.g., `3.14159`, `-0.005`). Scientific E-notation (e.g., `6.022e23`) is also permissible.
*   **Data Typing (Optional but Recommended for Precision):** For clarity, especially with non-integer numbers or very large integers, numbers can be explicitly typed using XSD (XML Schema Datatypes).
*   **Example:**
    ```turtle
    @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
    @prefix ucl: <http://ucl-spec.org/5.0/core#> .

    (ucl:this) ucl:hasCount 123 ;                             // Interpreted as integer by context
               ucl:hasRatio 0.75 ;                            // Interpreted as decimal
               ucl:hasBigNumber "299792458000"^^xsd:integer ;  // Explicitly typed large integer
               ucl:hasMeasurement "3.1415926535"^^xsd:decimal . // Explicitly typed decimal
    ```

### 1.3. Booleans

*   **Representation:** The lowercase literals `true` or `false`.
*   **Data Typing (Optional):** Can be typed with `xsd:boolean`.
*   **Example:**
    ```turtle
    @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
    @prefix ucl: <http://ucl-spec.org/5.0/core#> .

    (ucl:this) ucl:isActive true ;
               ucl:isComplete "false"^^xsd:boolean . // Explicitly typed
    ```

### 1.4. Null (Absence of Value)

*   **Representation:** The specific UCL-ID `ucl:Null` (from the `<http://ucl-spec.org/5.0/core#>` namespace).
*   **Usage:** Used as the *object* of a triple when a property is explicitly stated to have no value or is not applicable. This is distinct from omitting the triple entirely.
*   **Example:**
    ```turtle
    @prefix schema: <http://schema.org/> .
    @prefix ucl: <http://ucl-spec.org/5.0/core#> .

    (ucl:this) schema:middleName ucl:Null . // Explicitly stating no middle name
    ```

### 1.5. Date, Time, and DateTime

*   **Representation:** ISO 8601 formatted strings, typically typed using appropriate XSD datatypes for clarity.
    *   `xsd:date` (e.g., `"2024-03-15"`)
    *   `xsd:time` (e.g., `"14:30:00Z"`)
    *   `xsd:dateTime` (e.g., `"2024-03-15T14:30:00Z"` or `"2024-03-15T14:30:00-05:00"`)
*   **Example:**
    ```turtle
    @prefix schema: <http://schema.org/> .
    @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

    (ucl:this) schema:datePublished "2024-03-15"^^xsd:date ;
               schema:expires "2025-03-15T23:59:59Z"^^xsd:dateTime .
    ```

### 1.6. Duration

*   **Representation:** ISO 8601 duration formatted string, typically typed with `xsd:duration`.
*   **Example:**
    ```turtle
    @prefix schema: <http://schema.org/> .
    @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

    (ucl:this) schema:processingTime "PT2H30M"^^xsd:duration . // 2 hours and 30 minutes
    ```

### 1.7. Binary Data

*   **Representation (Textual UCL 5.0):** For embedding binary data directly in textual UCL (less common for large data), it's represented as a Base64 encoded string literal, explicitly typed with `xsd:base64Binary`.
*   **Preference for Large Data:** For substantial binary objects, it's highly recommended to store the data externally and reference it via a URI (UCL-ID) in the payload.
*   **Example (Embedded):**
    ```turtle
    @prefix ucl: <http://ucl-spec.org/5.0/core#> .
    @prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

    (ucl:this) ucl:hasThumbnail "iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVR42mNkYAAAAAYAAjCB0C8AAAAASUVORK5CYII="^^xsd:base64Binary .
    ```

## 2. Representing Structures in the Graph

### 2.1. Lists (Ordered Collections)

UCL 5.0 uses RDF Collection vocabulary (often via Turtle's list shorthand `(...)`) to represent ordered sequences of items.

*   **Syntax (Shorthand):** `(item1 item2 item3 ...)`
*   **Usage:** The list itself becomes the object of a triple.
*   **Example:**
    ```turtle
    @prefix ucl: <http://ucl-spec.org/5.0/core#> .
    @prefix schema: <http://schema.org/> .

    (ucl:this) ucl:hasKeywords ( "semantic" "graph" "UCL 5.0" "AI" ) ;
               ucl:hasSteps (
                   [ a ucl:Step ; schema:name "Step 1" ; schema:description "Initialize system." ]
                   [ a ucl:Step ; schema:name "Step 2" ; schema:description "Process data." ]
                   [ a ucl:Step ; schema:name "Step 3" ; schema:description "Generate report." ]
               ) .
    ```
    In the second part, `ucl:hasSteps` points to a list where each item is a blank node describing a step.

### 2.2. Maps / Nested Structures (Describing an Entity's Properties)

What was a JSON map in UCL 4.2 becomes a set of triples sharing the same subject in UCL 5.0. This is the natural way to describe an entity's properties in a graph.

*   **UCL 4.2 Map:**
    ```json
    { "name": "Project Alpha", "status": "Active", "priority": 1 }
    ```
*   **UCL 5.0 Graph Equivalent (Describing an entity `proj:Alpha`):**
    ```turtle
    @prefix schema: <http://schema.org/> .
    @prefix ucl_meta: <http://ucl-spec.org/5.0/metadata#> .
    @prefix proj: <http://example.org/projects#> .

    proj:Alpha a ucl_meta:Project ;  // Assuming Project is a type in ucl_meta or a custom ontology
        schema:name "Project Alpha" ;
        ucl_meta:status "Active" ;   // Or a UCL-ID like ucl_status:Active
        ucl_meta:priority 1 .
    ```

*   **Nested Structures using Blank Nodes:** To represent nested "objects" without giving them their own explicit URIs, blank nodes are used.
    ```turtle
    @prefix schema: <http://schema.org/> .
    @prefix ucl: <http://ucl-spec.org/5.0/core#> .

    (ucl:this) schema:contactPoint [    // Blank node for the contact point
                   a schema:ContactPoint ;
                   schema:email "info@example.com" ;
                   schema:telephone "+1-800-555-1212" ;
                   schema:availableLanguage ( "English" "Spanish" )
               ] .
    ```
    Here, the `schema:contactPoint` property points to an anonymous resource (the blank node `[...]`) which is then further described with its own properties.

By leveraging these graph-based representations for literals and structures, UCL 5.0 payloads can express highly complex, interconnected, and semantically precise information, far exceeding the capabilities of traditional JSON-like structures for intricate data modeling.

