# Migrating from UCL 4.2 "Enhanced" to UCL 5.0 "GraphNative"

Welcome to UCL 5.0 "GraphNative"! This version represents a significant evolution from UCL 4.2 "Enhanced," aiming for even greater semantic precision, contextual control, and alignment with advanced AI processing paradigms, particularly for Large Language Models (LLMs).

This guide is designed to help users familiar with UCL 4.2 understand the key differences and make a smooth transition to UCL 5.0. While some core philosophies remain, several structural and conceptual changes have been introduced to unlock new capabilities.

## Core Motivations for UCL 5.0

UCL 4.2 laid a strong foundation for structured, contextual communication. However, as AI systems (especially LLMs) have advanced, the need for even more expressive and flexible ways to convey complex intent and manage multifaceted contexts became apparent. UCL 5.0 addresses this by:

1.  **Embracing a Graph-Native Payload:** Moving from a JSON-like payload to a payload structured as a graph of semantic triples (RDF-like). This allows for richer relationship modeling and aligns more closely with how knowledge is often represented and processed internally by advanced AI.
2.  **Introducing the Context Mixer (`cm:`):** A sophisticated mechanism for defining, combining, and weighting various contextual aspects, offering far more granular control than the linear Context Stack of UCL 4.2.
3.  **Streamlining the Message Envelope:** Refining the top-level message structure for clarity and to better support the new payload and context mechanisms.
4.  **Enhancing Semantic Rigor:** Further emphasizing the use of URIs for all identifiable concepts and promoting well-defined namespaces.

## Key Conceptual and Structural Changes

Let's compare the core components:

| Feature / Component       | UCL 4.2 "Enhanced"                                  | UCL 5.0 "GraphNative"                                                                | Key Change & Rationale                                                                                                                               |
| :------------------------ | :---------------------------------------------------- | :----------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Overall Structure**     | `[Src] [Dir] Tgt OpVerb OpID [: Payload] # CtxStack` | `[Src] > Tgt execute OpID ^Mods... : {PayloadGraph} # [CtxStack]`                  | Envelope streamlined. `execute` as primary verb. Modifiers explicit. Payload is a graph. Context Stack is optional/complementary to Context Mixer. |
| **Payload (`PayloadPart`)** | Single `Value` (Literal, UCL-ID, List, Map JSON-like) | A graph of semantic triples `{ (s p o). (s p' o'). ... }`                               | **Fundamental Shift.** Enables rich, explicit relationships directly in the payload. More expressive than nested JSON-like structures for complex data. |
| **Context Handling**      | `ContextStackPart` (Mandatory, linear stack)          | **Context Mixer (`cm:`)** as primary. Optional `ContextStackPart` for broader context. | Context Mixer allows fine-grained, weighted, and conditional application of multiple contextual aspects. More powerful and flexible.                |
| **UCL-IDs & Prefixes**    | URIs/CURIEs, `@prefix`, predefined prefixes.            | Same fundamental principles, with stronger emphasis on custom namespaces for clarity.  | Consistency, but UCL 5.0 might encourage more explicit prefix declarations for non-standard ontologies.                                         |
| **Operations**            | `OperationVerb OperationID_UCLID`                       | `execute Operation_UCLID` (primarily). Other verbs possible but `execute` is central. | `execute` simplifies the main verb for LLM interaction, with specificity in `Operation_UCLID` and payload.                                        |
| **Modifiers (`^`)**       | `ModifiersPart` (`^mod1 ^mod2`) after Operation.      | Modifiers (`^pred val`) directly on the Operation line (e.g., `^cm:profile ...`).    | Tighter association of high-level modifiers (like Context Mixer profile) with the main operation.                                                  |
| **Direction**             | Explicit token (`>`, `<`, `<>`).                        | `>` is the primary direction, often implicit in `Source > Target`.                   | Simplified; other directions might be specified via `Operation_UCLID` semantics if needed.                                                      |

## Migrating Specific UCL 4.2 Constructs

Let's look at how to translate common UCL 4.2 patterns to UCL 5.0.

### 1. Message Envelope

**UCL 4.2:**
```
@prefix schema: <http://schema.org/>
ucl:id:MyAgent > ucl:service:KB query schema:Book ...


UCL 5.0:

@prefix schema: <http://schema.org/>
@prefix ucl: <http://ucl-spec.org/5.0/core#>
@prefix cm: <http://ucl-spec.org/5.0/context-mixer#>

ucl:id:MyAgent > ucl:service:KB execute schema:QueryBookAction ^cm:profile cm:profile:BookQueryProfile ...
```

Change: query schema:Book becomes execute schema:QueryBookAction. The specific action (QueryBookAction) is now a UCL-ID itself.

New: A Context Mixer profile (^cm:profile cm:profile:BookQueryProfile) is typically associated with the operation.

2. Payload

This is the most significant change.

UCL 4.2 (JSON-like Map):
```
// ... OperationPart ... :
{
  schema:name: "1984",
  schema:author: wd:Q3395,
  ucl:param:editionYear: 1949
}
// ... ContextStackPart ...
IGNORE_WHEN_COPYING_START
```

UCL 5.0 (Graph Payload):
```
// ... Operation_UCLID ^Modifiers ... :
{
  (ucl:this) ucl:hasParameter [
    a schema:PropertyValue ;
    schema:propertyID schema:name ;
    schema:value "1984"
  ] ;
  (ucl:this) ucl:hasParameter [
    a schema:PropertyValue ;
    schema:propertyID schema:author ;
    schema:value wd:Q3395
  ] ;
  (ucl:this) ucl:hasParameter [
    a schema:PropertyValue ;
    schema:propertyID ucl:param:editionYear ; // Assuming ucl:param namespace for parameters
    schema:value 1949
  ] .
}
// ... Optional ContextStackPart ...
```

Change: Instead of a simple key-value map, the payload now consists of explicit semantic triples. (ucl:this) refers to the main subject of the payload (e.g., the query instance or the entity being described).

Structure: We use constructs like schema:PropertyValue (or similar patterns) to clearly define parameters with their property ID and value. This is more verbose but far more explicit and flexible for complex relationships.

Alternative Simpler Graph (if ontologies define properties directly):
```
// ... Operation_UCLID ^Modifiers ... :
{
  (ucl:this) a schema:BookQueryCriteria ; // Or a custom type for the query
             schema:name "1984" ;
             schema:author wd:Q3395 ;
             ucl:param:editionYear 1949 .
}
```

This simpler graph form is preferred if your Operation_UCLID implies a subject that can directly have these properties (e.g., schema:name is a property applicable to schema:BookQueryCriteria).

UCL 4.2 (List):
```
// ... : [ "apple", "banana", "cherry" ] ...
```


UCL 5.0 (RDF List in Graph):
```
// ... :
{
  (ucl:this) ucl:hasFruitList ( "apple" "banana" "cherry" ) . // Using RDF list shorthand
}
```

Or, more explicitly with individual items:
```
// ... :
{
  (ucl:this) ucl:hasFruit item:Apple ;
             ucl:hasFruit item:Banana ;
             ucl:hasFruit item:Cherry .
  item:Apple a ucl:Fruit ; schema:name "apple" .
  // ... similar for Banana, Cherry
}
```

The choice depends on the desired level of semantic detail for list items.

3. Context

UCL 4.2 (Mandatory Context Stack):
```
# ucl:context:LiteraryWork / ucl:context:InformationRequest
```

UCL 5.0 (Context Mixer + Optional Stack):
The primary way to provide context is via a Context Mixer Profile, specified as a modifier:
```
// ... execute myapp:action:AnalyzeSentiment ^cm:profile cm:profile:LiteraryReviewAnalysis ... :
// ...
// Optional, broader stack:
# ucl_meta:project:Alpha / ucl:department:Research
```

The cm:profile:LiteraryReviewAnalysis would be defined (likely elsewhere, or in a dedicated part of the message payload if it's a dynamic profile) to include aspects like ucl:context:LiteraryWork and ucl:context:InformationRequest, possibly with weights or specific filters.

cm:Profile Definition Example (Conceptual, within a UCL message or a knowledge base):
```
@prefix cm: <http://ucl-spec.org/5.0/context-mixer#> .
@prefix ucl_ctx: <http://ucl-spec.org/context#> . # Hypothetical context namespace

cm:profile:LiteraryReviewAnalysis a cm:Profile ;
    cm:definesFocus
        [ a cm:FocusElement ; cm:onAspect ucl_ctx:LiteraryWork ; cm:withWeight 1.0 ] ,
        [ a cm:FocusElement ; cm:onAspect ucl_ctx:SentimentAnalysis ; cm:withWeight 0.8 ; cm:withFilter "focus on emotional tone" ] ,
        [ a cm:FocusElement ; cm:onAspect ucl_ctx:InformationRequest ; cm:withWeight 0.5 ] ;
    cm:appliesStrategy cm:WeightedSum .
```

Benefit: The Context Mixer allows for a much richer specification of how different contextual factors should influence processing. The old Context Stack can still be used for very high-level, less dynamic categorization.

4. Prefixes and UCL-IDs

The principles remain the same: use @prefix for brevity and URIs for canonical identification.
UCL 5.0 encourages clear namespacing for all custom terms.

UCL 4.2:
```
@prefix schema: <http://schema.org/>
// ... uses schema:Book ...
```

UCL 5.0 (No change in principle):
```
@prefix schema: <http://schema.org/>
// ... uses schema:Book ...
```

However, for custom application terms (like specific actions or parameters not in standard ontologies), UCL 5.0 would strongly recommend defining your own namespace:
```
@prefix myapp: <http://example.com/myapp-ontology#>
// ...
// ... execute myapp:action:ProcessUserData ...
// ... { (ucl:this) myapp:hasCustomParameter "value" . } ...
```

Example: Simple Query Migration

UCL 4.2 (from your examples01_simple_query.TXT):
```
@prefix schema: <http://schema.org/>
@prefix wd: <http://www.wikidata.org/entity/>
@prefix ucl: <http://ucl-spec.org/4.1/core#> // Note: Version 4.1 in example

ucl:id:UserQueryAgent001 > ucl:service:KnowledgeBaseService query schema:Book :
  {
    schema:name: "Nineteen Eighty-Four",
    schema:author: wd:Q3395 // Wikidata ID for George Orwell
  }
# ucl:context:LiteraryWork / ucl:context:InformationRequest
```

UCL 5.0 Equivalent:
```
@prefix schema: <http://schema.org/>
@prefix wd: <http://www.wikidata.org/entity/>
@prefix ucl: <http://ucl-spec.org/5.0/core#>
@prefix cm: <http://ucl-spec.org/5.0/context-mixer#>
@prefix ucl_ctx: <http://ucl-spec.org/context#>      # Hypothetical context namespace
@prefix myapp: <http://example.com/myapp-ontology#> # For custom actions

// Definition of the Context Mixer Profile (could be separate or part of a setup)
cm:profile:LiteraryBookQuery a cm:Profile ;
    cm:description "Profile for querying literary book information." ;
    cm:definesFocus
        [ a cm:FocusElement ; cm:onAspect ucl_ctx:LiteraryWork ; cm:withWeight 1.0 ] ,
        [ a cm:FocusElement ; cm:onAspect ucl_ctx:InformationRequest ; cm:withWeight 0.9 ] ;
    cm:appliesStrategy cm:PrioritizedOverride . // Example strategy

// The UCL 5.0 Message
ucl:id:UserQueryAgent001 > ucl:service:KnowledgeBaseService execute myapp:action:QueryBookByDetails
    ^cm:profile cm:profile:LiteraryBookQuery
    ^ucl_meta:taskDescription "Find book 'Nineteen Eighty-Four' by George Orwell"
:
{
  (ucl:this) a myapp:BookQueryCriteria ; // Or a more generic query type
             schema:name "Nineteen Eighty-Four" ;
             schema:author wd:Q3395 .
}
# ucl_meta:project:DigitalLibraryQuerySystem // Optional broader context
```

Summary of Key Mindset Shifts for Migration

Think in Triples for Payloads: Your data is now a set of explicit relationships, not just nested key-value pairs.

Embrace the Context Mixer: Design context profiles to precisely guide interpretation, rather than relying solely on a linear stack.

Define Specific Actions: Operations become more explicit UCL-IDs (e.g., myapp:action:QueryBookByDetails instead of a generic query verb with a schema:Book type).

Use Modifiers for High-Level Orchestration: Things like the active Context Mixer profile are specified in the envelope.

Migrating to UCL 5.0 involves a shift towards more explicit semantic modeling. While it may seem more verbose initially for simple cases, the benefits in terms of clarity, control, and extensibility for complex AI interactions are substantial.


