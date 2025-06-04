# Getting Started with UCL 5.0 "GraphNative"

This guide provides a quick introduction to the basic structure of a UCL (Universal Contextual Language) Version 5.0 message and how to begin thinking in terms of its core components, especially if you are new to UCL or migrating from earlier versions.

UCL 5.0 introduces a more expressive graph-native payload and a sophisticated Context Mixer, enabling even more precise communication with AI.

## Anatomy of a UCL 5.0 Message

Let's break down a UCL 5.0 message. Imagine you want to instruct an AI service to generate a short product description for a new eco-friendly water bottle, keeping a specific target audience and tone in mind.

```ucl
// 1. Prefix Declarations (Define shortcuts for URIs)
@prefix schema: <http://schema.org/>
@prefix ucl: <http://ucl-spec.org/5.0/core#>
@prefix cm: <http://ucl-spec.org/5.0/context-mixer#>
@prefix myprd: <http://example.com/products#>
@prefix ucl_style: <http://ucl-spec.org/style#>
@prefix ucl_audience: <http://ucl-spec.org/audience#>
@prefix myapp: <http://example.com/myapp-ontology#>

// 2. The UCL 5.0 Message Itself
ucl:id:MarketingDept > llm:agent:ProductDescWriter execute myapp:action:GenerateProductDescription
    ^cm:profile cm:profile:EcoProductBrief // Modifier: Use this Context Mixer Profile
    ^llm:languageForResponse "en"          // Modifier: Respond in English
: // Payload Graph Separator
{ // Start of PayloadGraph (graph of triples)
  (ucl:this) myapp:concernsProduct myprd:EcoBottleXZ100 .

  myprd:EcoBottleXZ100 a schema:Product ;
    schema:name "AquaLeaf Eco-Bottle" ;
    schema:description "A stylish, reusable water bottle made from 100% plant-based polymers." ;
    myprd:hasFeature "Leak-proof cap", "Biodegradable", "Lightweight design" ;
    myprd:capacity "750ml" .
}
# ucl_project:NewProductLaunch // Optional, broader Context Stack


Let's dissect this UCL 5.0 message:

1. Prefix Declarations
@prefix schema: <http://schema.org/>
@prefix ucl: <http://ucl-spec.org/5.0/core#>
// ... and so on
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Ucl
IGNORE_WHEN_COPYING_END

What it is: Same as in UCL 4.2, these lines define shortcuts (prefixes like schema:) for long URIs (like <http://schema.org/>). This makes the rest of the message more readable.

UCL 5.0 Namespaces: You'll often see ucl: for core UCL 5.0 terms, cm: for the Context Mixer, and application-specific ones like myapp: or myprd:.

2. The Message Envelope

This is the first line after prefixes, before the payload separator (:).
ucl:id:MarketingDept > llm:agent:ProductDescWriter execute myapp:action:GenerateProductDescription
^cm:profile cm:profile:EcoProductBrief
^llm:languageForResponse "en"

ucl:id:MarketingDept (Source - Optional): Who is sending/initiating.

> (Direction): Flow from source to target.

llm:agent:ProductDescWriter (Target - Mandatory): The AI agent or service to process the message.

execute (Operation Verb - Primary): The main verb in UCL 5.0, indicating the target should perform an action.

myapp:action:GenerateProductDescription (Operation_UCLID - Mandatory): A UCL-ID specifying the exact action.

^cm:profile cm:profile:EcoProductBrief (Modifier - Optional): This is new and crucial in UCL 5.0.

The ^ indicates a modifier.

cm:profile is the predicate (from the Context Mixer namespace).

cm:profile:EcoProductBrief is the value – a UCL-ID referencing a specific Context Mixer Profile. This profile (defined elsewhere or known by the LLM) would contain detailed rules on tone, audience focus, style, etc., for "EcoProductBriefs".

^llm:languageForResponse "en" (Modifier - Optional): Another modifier instructing the LLM about the response.

3. Payload Graph Separator
:
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Ucl
IGNORE_WHEN_COPYING_END

What it is: A single colon.

Purpose: Marks the end of the envelope and the beginning of the PayloadGraph.

4. PayloadGraph
{ // Conceptual start of graph block
  (ucl:this) myapp:concernsProduct myprd:EcoBottleXZ100 .

  myprd:EcoBottleXZ100 a schema:Product ;
    schema:name "AquaLeaf Eco-Bottle" ;
    schema:description "A stylish, reusable water bottle made from 100% plant-based polymers." ;
    myprd:hasFeature "Leak-proof cap", "Biodegradable", "Lightweight design" ;
    myprd:capacity "750ml" .
} // Conceptual end of graph block
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Ucl
IGNORE_WHEN_COPYING_END

What it is: This is the core data for the operation, structured as a graph of semantic triples (Subject-Predicate-Object statements). This is a major difference from UCL 4.2's JSON-like payload.

Triples:

(ucl:this) myapp:concernsProduct myprd:EcoBottleXZ100 .

(ucl:this): A conventional way to refer to the main context or subject of this payload.

myapp:concernsProduct: A predicate (a UCL-ID defining a relationship).

myprd:EcoBottleXZ100: An object (a UCL-ID for our specific product).

.: Terminates the triple.

myprd:EcoBottleXZ100 a schema:Product ;

myprd:EcoBottleXZ100: Now this product UCL-ID is the subject.

a: Shorthand for rdf:type (a standard RDF predicate).

schema:Product: The object, stating our bottle is a type of Product (as defined by Schema.org).

;: Semicolon allows stating another predicate for the same subject (myprd:EcoBottleXZ100).

schema:name "AquaLeaf Eco-Bottle" ;

Predicate schema:name, Object is the string literal "AquaLeaf Eco-Bottle".

myprd:hasFeature "Leak-proof cap", "Biodegradable", "Lightweight design" ;

Predicate myprd:hasFeature.

,: Comma allows specifying multiple objects for the same subject-predicate pair.

Clarity: Each piece of information is an explicit statement with semantically defined predicates.

5. Context Stack (Optional)
# ucl_project:NewProductLaunch
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Ucl
IGNORE_WHEN_COPYING_END

What it is: Similar to UCL 4.2, an optional stack for very broad contextual framing, prefixed by #.

Role in UCL 5.0: Often plays a secondary role to the more detailed Context Mixer Profile. It can provide overarching project or environmental context.

Thinking in UCL 5.0: From Intent to Graph

Core Intent: "Generate a product description." -> execute myapp:action:GenerateProductDescription.

Key Entity: "Eco-friendly water bottle." -> Define its properties as triples with a subject like myprd:EcoBottleXZ100.

Contextual Guidance:

"For eco-conscious young adults, positive and aspirational tone, highlight sustainability." -> This goes into a cm:Profile (e.g., cm:profile:EcoProductBrief). The prompt just references this profile using ^cm:profile ....

Assemble: Combine envelope, modifiers, payload graph separator, the graph data, and optional context stack.

Key Takeaways for UCL 5.0 Beginners

Embrace the Graph: The payload is now a set of explicit (Subject-Predicate-Object) statements. This offers much more power to define relationships.

Leverage the Context Mixer: Use ^cm:profile in the envelope to tell the AI how to think by referencing a profile that defines focus areas, weights, and styles.

UCL-IDs are Everywhere: Subject, Predicates, and many Objects are UCL-IDs (URIs/CURIEs), providing semantic precision.

Structure is Still Key: While more expressive, UCL 5.0 maintains a clear, parsable structure.

This is a brief introduction. The following documents will delve deeper into each concept, especially the graph payload and the Context Mixer, which are central to UCL 5.0's capabilities.

