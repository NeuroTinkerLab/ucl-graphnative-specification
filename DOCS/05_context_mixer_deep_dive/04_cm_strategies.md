# Context Mixer Deep Dive: Mixing Strategies (`cm:Strategy`)

Once a Context Mixer Profile (`cm:Profile`) has defined its various Focus Elements (`cm:FocusElement`) along with their weights and filters, a **Mixing Strategy (`cm:Strategy`)** determines *how* these individual contextual influences are combined, prioritized, and synthesized to guide the AI or processing system.

The strategy is a crucial component that dictates the overall behavior of the Context Mixer for a given profile.

## What is a `cm:Strategy`?

A `cm:Strategy` is a UCL-ID that identifies a specific algorithm, set of rules, or logical procedure for integrating multiple contextual inputs. The `cm:Profile` declares which strategy it employs using the `cm:appliesStrategy` property.

*   **Property:** `cm:appliesStrategy`
*   **Value:** A UCL-ID identifying the chosen strategy (e.g., `cm:strategy:WeightedSum`, `cm:strategy:PrioritizedOverride`).

```turtle
# Within a cm:Profile definition:
# ...
cm:appliesStrategy cm:strategy:WeightedFocusIntegration ; # Example strategy
# ...


The actual implementation of a given strategy resides within the system interpreting the UCL 5.0 message (e.g., the LLM or the AI service). The UCL message itself only names the strategy to be used.

Purpose of Mixing Strategies

Different tasks and desired outcomes may require different ways of handling multiple contextual inputs:

Blending vs. Selecting: Sometimes you want to smoothly blend influences (e.g., 70% formal style, 30% creative); other times, you want one context to strictly override others if present (e.g., a security constraint overriding a functional requirement).

Handling Conflicts: If different focus elements imply contradictory actions or interpretations, the strategy defines how such conflicts are resolved.

Complexity Management: For profiles with many focus elements, a strategy provides a systematic way to manage their combined effect.

Examples of Conceptual Mixing Strategies

The cm: namespace would define a set of standard, well-understood strategies, and application-specific strategies could also be defined. Here are some conceptual examples:

cm:strategy:PrioritizedOverride

Logic: Focus elements are considered in a predefined order of priority (which could be derived from weights or an explicit order). The highest-priority active focus element that applies to a given situation dictates the outcome, potentially overriding or ignoring lower-priority ones.

Use Case: Enforcing critical constraints (e.g., legal compliance, safety protocols) that must always take precedence.

Example: If legal:regulation:GDPR (high priority) and marketing:goal:MaximizeEngagement (lower priority) are both active, GDPR considerations would trump engagement tactics if they conflict.

cm:strategy:WeightedSum or cm:strategy:WeightedAverage

Logic: The influence of each active focus element is scaled by its cm:withWeight. The system then calculates a "sum" or "average" of these weighted influences. This is more applicable when influences are quantifiable or can be mapped to a common scale.

Use Case: Blending stylistic elements (e.g., 0.7 weight for ucl_style:Formal + 0.3 weight for ucl_style:Engaging), or combining scores from different recommendation criteria.

Note: How "influences" are numerically represented and "summed" is highly dependent on the LLM's internal architecture or the specific AI service.

cm:strategy:SimpleAdditive

Logic: All active focus elements are considered, and their instructions (e.g., from cm:withFilter strings) are effectively concatenated or applied collectively to the task. This assumes focus elements are largely complementary rather than conflicting.

Use Case: Building up a set of requirements or stylistic guidelines where each adds to the overall picture.

Example: "Be concise" + "Use technical terms" + "Target expert audience."

cm:strategy:FirstApplicable (Fallback Chain)

Logic: Focus elements are evaluated in a specific order. The first one whose conditions (potentially defined in cm:withFilter) are met is applied, and subsequent ones in the chain might be ignored for that specific decision point.

Use Case: Defining a series of preferred approaches with fallbacks (e.g., "Try to use data_source:RealTimeAPI; if unavailable, use data_source:HourlyCache; if also unavailable, use data_source:DailySnapshot").

cm:strategy:ConditionalLogic

Logic: A more complex strategy that might involve IF-THEN-ELSE rules, where the application of certain focus elements depends on conditions evaluated from the payload or other contextual factors.

Use Case: "IF payload:customerType is enterprise:PremiumCustomer, THEN apply cm:focus:PremiumSupportProtocol; ELSE apply cm:focus:StandardSupportProtocol."

Note: Defining this entirely within UCL might require a sub-language for expressing conditions, or the conditions might be implicit in how the LLM is trained to interpret the profile.

cm:strategy:LLMManaged (Implicit Strategy)

Logic: The profile provides all the focus elements, weights, and filters, and the LLM itself uses its advanced reasoning capabilities to determine the best way to synthesize these influences for the given task. This relies heavily on the LLM's training and its ability to interpret the intent behind the profile structure.

Use Case: For highly nuanced tasks where explicit algorithmic combination is difficult, leveraging the LLM's "judgment." This is often how LLMs handle complex meta-prompts today.

Defining and Implementing Strategies

UCL's Role: UCL 5.0, via the cm:Profile, names the strategy.

System's Role: The receiving system (LLM, AI service) is responsible for implementing the logic of that named strategy.

Standard vs. Custom Strategies:

A core UCL 5.0 specification for the Context Mixer would likely define a small set of standard strategy UCL-IDs (like cm:strategy:PrioritizedOverride, cm:strategy:SimpleAdditive).

Systems could also support custom, proprietary strategy UCL-IDs if they have unique mixing capabilities. For interoperability, using standard strategies is preferred.

The choice of an appropriate cm:Strategy is critical to achieving the desired contextual behavior. It translates the collection of individual contextual directives within a cm:Profile into a cohesive and effective guide for the AI's processing.

Next: Context Mixer Deep Dive: Output Directives (cm:guidesOutput)

