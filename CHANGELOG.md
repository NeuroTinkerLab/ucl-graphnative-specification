# Changelog for UCL (Universal Contextual Language)

All notable changes to the UCL specification will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html) conceptually for its specification versions.

## [5.0.0] - 2025-06-04 (To Be Dated Upon "Release")

### Added

*   **UCL 5.0 "GraphNative" Specification - Initial Version**
    *   **Graph-Native Payload:** Introduced representation of message payloads as semantic graphs using RDF-like triple structures. This replaces the JSON-like map/list structure of UCL 4.2 for the primary payload, enabling richer semantic modeling and explicit relationship definition.
    *   **Context Mixer (`cm:`) Framework:** Introduced a sophisticated mechanism for advanced context management.
        *   `cm:Profile`: Allows defining named configurations for how multiple contextual influences are weighted, filtered, and combined.
        *   `cm:FocusElement`: Specifies distinct contextual aspects to be considered.
        *   `cm:withWeight`: Assigns relative importance to focus elements.
        *   `cm:withFilter`: Provides refining instructions for focus elements.
        *   `cm:Strategy`: Defines algorithms for how focus elements are synthesized.
        *   `cm:guidesOutput`: Allows profiles to influence the structure and content of the AI's response.
    *   **Streamlined Message Envelope:**
        *   Standardized on `execute` as the primary operation verb in the envelope.
        *   `Operation_UCLID` now carries the full specificity of the action.
        *   Introduced explicit `^ModifierPredicate ModifierValue` syntax directly on the operation line for high-level parameters like `^cm:profile`.
    *   **Enhanced Emphasis on URI-Based Semantics:** Reinforced the use of UCL-IDs (URIs/CURIEs) for all identifiable concepts, properties, actions, and contextual aspects.
    *   **Updated Documentation Structure:** New and revised documents in `docs/` to explain UCL 5.0 concepts, migration from UCL 4.2, and usage with LLMs.
    *   **New Examples:** Added examples in `examples/` specifically demonstrating UCL 5.0 features, including graph payloads and Context Mixer usage, alongside migration examples.
    *   **Namespaces:** Defined conceptual namespaces for UCL 5.0 core (`ucl:`), Context Mixer (`cm:`), metadata (`ucl_meta:`), and LLM interaction (`llm:`).

### Changed

*   **Evolution from UCL 4.2 "Enhanced":**
    *   Payload structure fundamentally changed from JSON-like to graph-native.
    *   Context Stack (`# ...`) is now optional and complementary to the primary Context Mixer.
    *   Message envelope syntax revised for clarity and to accommodate UCL 5.0 features.
    *   (Refer to `docs/01_migrating_from_ucl4.2.md` for a detailed comparison and migration guide).

### Deprecated

*   The UCL 4.2 specific payload structure (direct JSON-like maps/lists as the primary payload) is deprecated in favor of the graph-native payload in UCL 5.0.
*   The UCL 4.2 `ModifiersPart` as a separate block is deprecated; modifiers are now integrated into the operation line.
*   The UCL 4.2 specific `OperationVerb`s like `query`, `create` in the envelope are generally superseded by using `execute` with a more specific `Operation_UCLID` that implies the verb's semantics.

### Removed

*   (Consider if any specific UCL 4.2 features are explicitly *removed* rather than just changed/deprecated).

### Fixed

*   (N/A for initial release of 5.0 specification from scratch).

### Security

*   (N/A for initial language specification, but a point for future consideration for implementations).

---

*Older changelog entries for previous conceptual versions (e.g., UCL 4.x) would reside in the changelog of their respective specifications/repositories.*