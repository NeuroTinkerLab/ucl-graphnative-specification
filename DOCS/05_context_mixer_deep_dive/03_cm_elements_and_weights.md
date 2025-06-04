# Context Mixer Deep Dive: Focus Elements, Weights, and Filters

Within a UCL 5.0 Context Mixer Profile (`cm:Profile`), the core components that define its behavior are **Focus Elements (`cm:FocusElement`)**, their associated **Weights (`cm:withWeight`)**, and optional **Filters (`cm:withFilter`)**. These allow for a nuanced specification of how different contextual aspects should be considered by the AI or processing system.

## Focus Elements (`cm:FocusElement`)

A `cm:FocusElement` represents a distinct contextual aspect, theme, constraint, or viewpoint that the profile instructs the processing system to pay attention to.

*   **Declaration:** An entity is declared as a focus element typically by stating `a cm:FocusElement` (i.e., `rdf:type cm:FocusElement`). Often, focus elements are described using blank nodes within the definition of a `cm:Profile`.

*   **Core Property: `cm:onAspect`**
    *   **Purpose:** This mandatory property links the `cm:FocusElement` to the actual contextual concept it represents.
    *   **Value:** A UCL-ID (URI or CURIE) that identifies the specific aspect. This UCL-ID should ideally point to a well-defined term in a relevant vocabulary or ontology.
    *   **Examples of Aspects:**
        *   `ucl_style:FormalWriting`
        *   `market:targetAudience:GenZ`
        *   `legal:regulation:GDPRCompliance`
        *   `ucl_emotion:Joyful`
        *   `data_source:RealTimeStockAPI`
        *   `persona_trait:Empathetic`

*   **Conceptual Role:** Each `cm:FocusElement` essentially tells the system: "When processing this operation, consider *this specific thing* (`cm:onAspect`) with the following qualifications."

## Weights (`cm:withWeight`)

Weights provide a mechanism to indicate the relative importance or influence of different `cm:FocusElement`s within the same `cm:Profile`.

*   **Property:** `cm:withWeight`
*   **Value:** Typically a numeric literal (often a float, e.g., between 0.0 and 1.0, though the exact scale can be defined by the mixing strategy).
    *   A higher value generally implies greater importance or influence.
    *   A weight of `0.0` might effectively disable that focus element for the current mix.
*   **Purpose:**
    *   Allows the profile to guide the AI on how to prioritize when multiple contextual aspects are at play.
    *   Enables a "blending" of influences rather than a simple on/off application of context.
*   **Example Snippet within a `cm:Profile` definition:**
    ```turtle
    cm:definesFocus
        [
            a cm:FocusElement ;
            cm:onAspect ucl_style:Concise ;
            cm:withWeight 0.9       // High importance for conciseness
        ],
        [
            a cm:FocusElement ;
            cm:onAspect ucl_style:TechnicalDetail ;
            cm:withWeight 0.6       // Moderate importance for technical detail
        ],
        [
            a cm:FocusElement ;
            cm:onAspect ucl_style:Humorous ;
            cm:withWeight 0.2       // Low importance for humor in this profile
        ] ;
    ```
*   **Interpretation:** The exact interpretation of these weights depends on the `cm:Strategy` employed by the profile (e.g., a weighted sum, a probabilistic model, etc.).

## Filters (`cm:withFilter`)

Filters provide additional instructions or criteria that refine or constrain how a specific `cm:FocusElement` is applied.

*   **Property:** `cm:withFilter`
*   **Value:**
    *   Most commonly, a **string literal** containing human-readable instructions or machine-processable rules (if the target system is designed to parse them).
    *   Alternatively, it could be a **UCL-ID** pointing to a more complex, predefined filter definition or a set of rules in a knowledge base.
*   **Purpose:**
    *   To add specificity to a broader contextual aspect.
    *   To set conditions or boundaries for the application of a focus element.
    *   To provide detailed guidance beyond a simple URI aspect.
*   **Example Snippet within a `cm:Profile` definition:**
    ```turtle
    cm:definesFocus
        [
            a cm:FocusElement ;
            cm:onAspect market:trend:Sustainability ;
            cm:withWeight 0.85 ;
            cm:withFilter "Focus on initiatives impacting European markets reported in the last 12 months. Exclude purely speculative trends."
        ],
        [
            a cm:FocusElement ;
            cm:onAspect ucl_output:FormatConstraint ;
            cm:withWeight 1.0 ; // Filter acts as a hard constraint
            cm:withFilter "<http://example.org/schemas/ReportSchema_V2>" // Points to a schema
        ] ;
    ```
*   **Interaction with AI/LLMs:** For LLMs, filter strings often act as further detailed instructions that the model should incorporate when considering the `cm:onAspect`.

## Combining Focus Elements, Weights, and Filters

Within a `cm:Profile`, multiple `cm:FocusElement` definitions are typically listed (e.g., as objects of the `cm:definesFocus` property, often using a list structure in RDF).

**Conceptual Example of a `cm:FocusElement` block:**
```turtle
@prefix cm: <http://ucl-spec.org/5.0/context-mixer#> .
@prefix ucl_style: <http://ucl-spec.org/style#> .
@prefix ucl_audience: <http://ucl-spec.org/audience#> .

# ... part of a cm:Profile definition ...
cm:definesFocus ( # Start of an RDF list of focus elements
    [
        a cm:FocusElement ;
        cm:onAspect ucl_style:PersuasiveWriting ;
        cm:withWeight 0.95 ;
        cm:withFilter "Employ rhetorical questions and strong calls to action. Maintain an optimistic tone."
    ]
    [
        a cm:FocusElement ;
        cm:onAspect ucl_audience:NonTechnicalExecutives ;
        cm:withWeight 0.8 ;
        cm:withFilter "Avoid jargon. Summarize key benefits and ROI clearly. Keep it under 2 pages."
    ]
    # ... more focus elements in the list ...
) ; // End of RDF list
# ...


The combination of these specifically defined focus elements, each potentially carrying its own weight and filtering instructions, allows a cm:Profile to construct a rich, multi-faceted contextual directive. The cm:Strategy (discussed next) then determines how these individual pieces of guidance are synthesized into a coherent operational context for the AI.

Next: Context Mixer Deep Dive: Mixing Strategies (cm:Strategy)

