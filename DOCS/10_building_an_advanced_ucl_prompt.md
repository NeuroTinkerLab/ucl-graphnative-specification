# Building an Advanced UCL 5.0 Prompt: A Step-by-Step Guide

UCL 5.0 "GraphNative" offers a powerful framework for crafting highly specific and contextually rich instructions for AI systems, especially LLMs. Building an advanced UCL 5.0 prompt involves more than just writing a command; it's about carefully designing a semantic message that precisely conveys your intent, data, and desired processing guidance.

This guide walks through the steps and considerations for constructing an advanced UCL 5.0 prompt. Let's use a hypothetical scenario: **"Instructing an AI to generate a detailed, balanced news article about a new technological breakthrough, targeted at a non-technical but educated audience."**

## Step 1: Define the Core Task - The `Operation_UCLID`

What is the fundamental action you want the AI to perform? This becomes your `Operation_UCLID`.

*   **Consider:** Is this a standard action (like from Schema.org if applicable) or a custom one?
*   **Our Scenario:** We want the AI to *generate an article*. This is a creative/generative task.
*   **`Operation_UCLID` Choice:** `myapp:action:GenerateNewsArticle` (assuming `myapp:` is our custom namespace for this application).

## Step 2: Identify Sender and Receiver - `Source_UCLID` and `Target_UCLID`

*   **`Target_UCLID` (Mandatory):** Who or what will execute this operation?
    *   **Our Scenario:** An advanced LLM capable of content generation.
    *   **Choice:** `llm:agent:ContentGenerationAI_v3`
*   **`Source_UCLID` (Optional):** Who is making the request?
    *   **Our Scenario:** An editor or a content management system.
    *   **Choice:** `ucl:id:NewsDeskEditor`

## Step 3: Design the Context Mixer Profile (`cm:Profile`)

This is crucial for advanced control. What contextual aspects must the AI consider, and how?

*   **Identify Key Contextual Aspects:**
    *   **Audience:** Non-technical but educated. (e.g., `ucl_audience:EducatedGeneralPublic`)
    *   **Tone/Style:** Balanced, objective, informative, engaging but not sensationalist. (e.g., `ucl_style:BalancedJournalism`, `ucl_style:Informative`, `ucl_style:Engaging`)
    *   **Topic Domain:** Technological breakthrough. (e.g., `ucl_topic:TechnologicalInnovation`)
    *   **Output Requirements:** Article structure, length.
    *   **Ethical Considerations:** Avoid hype, present potential downsides. (e.g., `ucl_ethics:ResponsibleReporting`)

*   **Assign Weights and Filters:**
    *   `ucl_audience:EducatedGeneralPublic`: High weight (e.g., 0.9), Filter: "Explain technical terms simply; avoid deep jargon."
    *   `ucl_style:BalancedJournalism`: High weight (e.g., 1.0), Filter: "Present multiple perspectives if available; clearly distinguish facts from speculation."
    *   `ucl_ethics:ResponsibleReporting`: High weight (e.g., 1.0), Filter: "Include a section on potential societal impact or ethical considerations."
    *   `ucl_style:Engaging`: Moderate weight (e.g., 0.7).

*   **Choose a Mixing Strategy:**
    *   Perhaps `cm:strategy:WeightedFocusIntegration` or `cm:strategy:LLMManaged` if we trust the LLM to synthesize these well.

*   **Define Output Directives:**
    *   "Article approx. 800-1000 words. Include: Introduction, Explanation of Tech, Potential Applications, Societal Impact/Ethical Points, Conclusion. Use clear headings."

*   **Name the Profile:** `cm:profile:TechBreakthroughNewsArticle`

*   **Association:** This will be added to the envelope as `^cm:profile cm:profile:TechBreakthroughNewsArticle`.

*(Note: The full definition of this profile would be a separate UCL graph, as shown in previous documents on `cm:Profile`.)*

## Step 4: Structure the `PayloadGraph` - Provide Core Data

What specific information does the `Operation_UCLID` need to act upon?

*   **Identify Key Data Points for `myapp:action:GenerateNewsArticle`:**
    *   The specific technological breakthrough (name, brief description, source of info).
    *   Key features/aspects of the breakthrough.
    *   Any known proponents or critics.
    *   Relevant dates.

*   **Represent as Triples:**
    ```turtle
    // Within the { PayloadGraph }
    (ucl:this) a myapp:NewsArticleRequest ; // Type for this request data
        myapp:hasSubject [
            a ucl_topic:TechnologicalInnovation ;
            schema:name "Quantum Entanglement Communication System" ;
            schema:description "A newly demonstrated system allowing for theoretically unhackable, instantaneous communication over vast distances using quantum entanglement." ;
            schema:source <http://example.com/press_releases/qcom_breakthrough> ; // Link to source
            myapp:keyFeature "Unhackable nature", "Instantaneous transmission" ;
            myapp:expertProponent "Dr. Alice Quantum" ;
            myapp:potentialConcern "High implementation cost, current fragility of quantum states."
        ] ;
        myapp:publicationDateTarget "2024-03-20"^^xsd:date .
    ```

## Step 5: Add Other Envelope Modifiers

Are there other high-level controls needed?

*   **Language for Response:** `^llm:languageForResponse "en-US"`
*   **Reasoning Trace (for debugging/understanding):** `^llm:requestReasoningTrace true ^llm:reasoningTraceDetail ucl_meta:value:SummaryTrace`

## Step 6: Consider an Optional `ContextStackPart`

Is there any very broad, stable context to add?

*   **Our Scenario:** Perhaps the project or publication this article is for.
*   **Choice:** `# ucl_publication:FutureTechReview / ucl_project:SeriesOnBreakthroughs`

## Step 7: Assemble the Full UCL 5.0 Prompt

Now, put all the pieces together.

```ucl
@prefix schema: <http://schema.org/>
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix ucl: <http://ucl-spec.org/5.0/core#>
@prefix cm: <http://ucl-spec.org/5.0/context-mixer#>
@prefix llm: <http://ucl-spec.org/5.0/llm#>
@prefix ucl_meta: <http://ucl-spec.org/5.0/metadata#>
@prefix myapp: <http://example.com/myapp-ontology#> // Our custom terms
@prefix ucl_topic: <http://ucl-spec.org/topic#>     // Hypothetical namespaces
@prefix ucl_audience: <http://ucl-spec.org/audience#>
@prefix ucl_style: <http://ucl-spec.org/style#>
@prefix ucl_ethics: <http://ucl-spec.org/ethics#>
@prefix ucl_publication: <http://ucl-spec.org/publication#>
@prefix ucl_project: <http://ucl-spec.org/project#>

// Assume cm:profile:TechBreakthroughNewsArticle is defined elsewhere or known by the LLM.
// Its conceptual definition would look like:
// cm:profile:TechBreakthroughNewsArticle a cm:Profile ;
//     schema:description "For generating balanced news on tech breakthroughs for educated non-technical audience." ;
//     cm:definesFocus
//         [ a cm:FocusElement ; cm:onAspect ucl_audience:EducatedGeneralPublic ; cm:withWeight 0.9 ; cm:withFilter "Explain technical terms simply; avoid deep jargon." ],
//         [ a cm:FocusElement ; cm:onAspect ucl_style:BalancedJournalism ; cm:withWeight 1.0 ; cm:withFilter "Present multiple perspectives if available; clearly distinguish facts from speculation." ],
//         [ a cm:FocusElement ; cm:onAspect ucl_ethics:ResponsibleReporting ; cm:withWeight 1.0 ; cm:withFilter "Include a section on potential societal impact or ethical considerations." ],
//         [ a cm:FocusElement ; cm:onAspect ucl_style:Engaging ; cm:withWeight 0.7 ],
//         [ a cm:FocusElement ; cm:onAspect ucl_topic:TechnologicalInnovation ; cm:withWeight 1.0 ] ;
//     cm:appliesStrategy cm:strategy:LLMManaged ; // Or a more specific strategy
//     cm:guidesOutput "Article approx. 800-1000 words. Include: Introduction, Explanation of Tech, Potential Applications, Societal Impact/Ethical Points, Conclusion. Use clear headings." .

ucl:id:NewsDeskEditor > llm:agent:ContentGenerationAI_v3 execute myapp:action:GenerateNewsArticle
    ^cm:profile cm:profile:TechBreakthroughNewsArticle
    ^llm:languageForResponse "en-US"
    ^llm:requestReasoningTrace true
    ^llm:reasoningTraceDetail ucl_meta:value:SummaryTrace
:
{
    (ucl:this) a myapp:NewsArticleRequestParameters ; // More specific type for the payload subject
        myapp:hasSubject [
            a ucl_topic:TechnologicalInnovation ;
            schema:name "Quantum Entanglement Communication System" ;
            schema:description "A newly demonstrated system allowing for theoretically unhackable, instantaneous communication over vast distances using quantum entanglement." ;
            schema:source <http://example.com/press_releases/qcom_breakthrough> ;
            myapp:keyFeature "Unhackable nature", "Instantaneous transmission" ; // Multi-valued property
            myapp:expertProponent "Dr. Alice Quantum" ;
            myapp:potentialConcern "High implementation cost, current fragility of quantum states."
        ] ;
        myapp:publicationDateTarget "2024-03-20"^^xsd:date .
}
# ucl_publication:FutureTechReview / ucl_project:SeriesOnBreakthroughs

Considerations for Advanced Prompts

Vocabulary Management: Ensure that all UCL-IDs (especially custom ones like myapp:, ucl_style:, etc.) are well-defined, ideally in accessible ontologies or vocabularies that the target AI system can understand or be taught.

Profile Reusability vs. Dynamism: Decide whether to use predefined, reusable cm:Profiles or to dynamically construct parts of the profile within the PayloadGraph for highly specific, one-off tasks.

LLM Training/Familiarity: The target LLM needs to be adequately "trained" or instructed via its system prompt to understand UCL 5.0 syntax, the cm: namespace, and how to interpret profiles.

Iteration and Testing: Crafting the perfect advanced UCL 5.0 prompt, especially the cm:Profile definition, will likely involve iteration and testing to see how the AI responds and then refining the prompt for optimal results.

By following these steps, you can leverage UCL 5.0 to create instructions for AI that are far more nuanced, controlled, and powerful than what's achievable with simple natural language prompts alone.

