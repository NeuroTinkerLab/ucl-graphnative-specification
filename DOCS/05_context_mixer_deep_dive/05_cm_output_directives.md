``
# Context Mixer Deep Dive: Output Directives (`cm:guidesOutput`)

While Focus Elements, Weights, Filters, and Mixing Strategies within a Context Mixer Profile (`cm:Profile`) primarily guide the *input interpretation and internal processing* by an AI or system, **Output Directives (`cm:guidesOutput`)** provide a way to influence or constrain the *form, structure, or content of the generated response or result*.

## What are Output Directives?

Output Directives are instructions, constraints, or preferences specified within a `cm:Profile` that relate specifically to the characteristics of the output produced by the `Operation_UCLID`.

*   **Property:** `cm:guidesOutput`
*   **Value:**
    *   Typically, a **string literal** containing human-readable instructions for an LLM or machine-processable rules/schema references for other systems.
    *   Could also be a **UCL-ID** pointing to a predefined output template, a data schema (like a JSON Schema or an RDF Shape), or a set of formatting guidelines.
*   **Placement:** Defined as a property of a `cm:Profile`. A profile can have one or more `cm:guidesOutput` directives if needed, though often one comprehensive directive is sufficient.

```ucl
# Within a cm:Profile definition:
# ...
cm:guidesOutput "The summary must be between 200-250 words, presented as three distinct paragraphs. Highlight key financial figures in bold." ;
# ...
```

## Purpose of Output Directives

### Controlling Output Format
Specify desired formats like JSON, XML, Markdown, a specific UCL structure, or plain text with particular layout requirements (e.g., bullet points, numbered lists).
*   **Example:** `cm:guidesOutput "<http://example.org/schemas/StandardReportV3.json#schema>"` (pointing to a JSON Schema for the output).

### Shaping Content and Structure
Guide the inclusion or exclusion of specific information, the ordering of sections, or the level of detail.
*   **Example:** `cm:guidesOutput "Ensure the report includes: 1. Executive Summary, 2. Detailed Findings, 3. Recommendations. Appendix for raw data is optional."`

### Enforcing Constraints
Set limits on length (word count, character count), number of items, or adherence to specific terminologies or stylistic conventions in the output.
*   **Example:** `cm:guidesOutput "Response must be a single sentence. Use only vocabulary from the approved 'MedicalTerms_Cardiology_v1' list."`

### Guiding LLM Generation Style (Output-Specific)
While focus elements might guide the overall tone (e.g., `ucl_style:Formal`), output directives can be more specific about the presentation of that tone.
*   **Example:** `cm:guidesOutput "Maintain a formal tone. All recommendations should be phrased as actionable imperatives."`

### Facilitating Machine Processability of Output
By guiding an LLM to produce structured output (e.g., UCL or JSON conforming to a schema), the response becomes immediately consumable by downstream software components, enabling automation.

## How Output Directives Work with the Rest of the Mixer

*   **Post-Processing Guidance:** Output directives are typically applied after the core operation has been processed under the influence of the focus elements and mixing strategy. They shape the "final packaging" of the result.
*   **Complementary, Not Overriding Core Logic:** An output directive generally shouldn't contradict the fundamental outcome determined by the operation and its primary contextual influences. For instance, it can ask for a "concise summary" but not change a "positive sentiment analysis" into a "negative" one if the core analysis determined positivity.
*   **Interaction with `ucl_meta:outputFormat`:**
    *   The `^ucl_meta:outputFormat` modifier on the message envelope provides a high-level request for the output format (e.g., `"PDF"`, `ucl_format:JSONLD`).
    *   `cm:guidesOutput` within a profile can provide more detailed instructions within that general format or provide alternative formatting if the high-level one isn't specific enough. For example, if `^ucl_meta:outputFormat "JSON"`, `cm:guidesOutput` could point to a specific JSON schema the output must adhere to.

## Example of `cm:guidesOutput` in a Profile

```turtle
@prefix cm: <http://ucl-spec.org/5.0/context-mixer#> .
@prefix schema: <http://schema.org/> .
@prefix ucl_meta: <http://ucl-spec.org/5.0/metadata#> .
@prefix myapp: <http://example.com/myapp-ontology#> .

cm:profile:ProductComparisonReport a cm:Profile ;
    schema:name "Product Comparison Report Profile" ;
    schema:description "Generates a structured comparison report for two products." ;

    cm:definesFocus
        [ a cm:FocusElement ; cm:onAspect myapp:feature:TechnicalSpecs ; cm:withWeight 0.8 ],
        [ a cm:FocusElement ; cm:onAspect myapp:feature:UserReviewsSummary ; cm:withWeight 0.6 ],
        [ a cm:FocusElement ; cm:onAspect myapp:feature:PriceAnalysis ; cm:withWeight 0.7 ] ;

    cm:appliesStrategy cm:strategy:FeatureBreakdownAndSynthesis ; // Hypothetical strategy

    cm:guidesOutput """
    The report should be structured as follows:
    1.  Introduction: Brief overview of the products being compared.
    2.  Feature-by-Feature Comparison:
        - Use a table format for Technical Specifications.
        - Summarize User Reviews for each product in 2-3 bullet points.
        - Present Price Analysis with current retail prices and value-for-money assessment.
    3.  Pros and Cons: List 3-5 key pros and cons for each product.
    4.  Recommendation: Conclude with a clear recommendation based on the analysis.
    Output should be in Markdown format.
    """ .
```

In this example, after the AI processes the product data according to the focus elements (technical specs, reviews, price), the `cm:guidesOutput` directive ensures the final report is well-structured, in Markdown, and includes specific sections.

Output Directives complete the Context Mixer's toolkit by allowing not only the guidance of how an AI thinks and processes information but also how it presents its conclusions, making UCL 5.0 a comprehensive language for controlled AI interaction.

This concludes the main conceptual parts of the Context Mixer Deep Dive.

**Next, we would typically move to:**

*   Syntax in Detail for UCL 5.0 (a revised version of UCL 4.2's `04_syntax_in_detail.md`)
``
