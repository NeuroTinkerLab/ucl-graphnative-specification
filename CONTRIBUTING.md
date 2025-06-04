# Contributing to UCL 5.0 "GraphNative"

Thank you for your interest in contributing to the Universal Contextual Language (UCL) 5.0 "GraphNative" specification, documentation, examples, and the broader ecosystem! We enthusiastically welcome contributions from everyone.

UCL 5.0 is an ambitious project aiming to redefine precision in AI communication. Your expertise, insights, and efforts can make a significant impact. There are many ways to contribute:

## How to Contribute

### 1. Reporting Issues, Inconsistencies, or Ambiguities

*   If you find a potential bug, an inconsistency, an ambiguity, or an area for improvement within the UCL 5.0 specification documents (including files in `docs/`, `examples/`, or the eventual formal `SPECIFICATION.MD`), please **open an issue** on the GitHub repository.
*   When opening an issue, please provide:
    *   The specific file(s) and section(s) affected.
    *   A clear description of the problem or area for improvement.
    *   Why you believe it's an issue (e.g., contradicts another part, lacks clarity, could lead to misinterpretation).
    *   Concrete suggestions for resolution or improvement, if you have them.

### 2. Suggesting Enhancements or New Features for UCL 5.0

*   Have an idea for a new feature, a modification to an existing UCL 5.0 concept (like the Context Mixer or payload structure), or a way to make the language more powerful or easier to use?
*   Please **open an issue first** to propose and discuss your idea with the community. This allows for collective input before significant design or documentation work is undertaken.
*   Clearly outline:
    *   The proposed enhancement.
    *   The motivation behind it (what problem does it solve, what new capability does it enable?).
    *   Potential benefits and any foreseeable drawbacks or complexities.

### 3. Improving Documentation and Explanations

*   High-quality, clear, and comprehensive documentation is paramount for a language specification like UCL 5.0.
*   If you find sections that are unclear, could be explained better, are missing crucial information, or contain errors, please:
    *   Open an issue detailing the documentation gap or error.
    *   **Submit a pull request** with your proposed improvements.
*   This applies to all `.md` files, comments within `.ucl` examples, and any diagrams or explanatory materials.

### 4. Providing More Examples of UCL 5.0 Messages

*   Practical, diverse examples are invaluable for learning and understanding UCL 5.0. We encourage contributions of:
    *   More migration examples from UCL 4.2 to UCL 5.0.
    *   New examples showcasing basic operations in UCL 5.0.
    *   Advanced examples demonstrating sophisticated uses of the Context Mixer, complex graph payloads, and multi-step reasoning.
    *   Examples for specific domains or use cases.
*   Submit new examples via a pull request to the appropriate subdirectory within `examples/`.
*   Please ensure your examples are well-commented, explaining their purpose, key UCL 5.0 features demonstrated, and the structure of the message.

### 5. Developing Tooling for UCL 5.0

*   The success of UCL 5.0 will be greatly enhanced by a rich ecosystem of open-source tools. We highly encourage the development of:
    *   Parsers and serializers for UCL 5.0 in various programming languages.
    *   Linters and validators for UCL 5.0 syntax and semantics (potentially using SHACL/ShEx for graph payloads).
    *   IDE plugins for syntax highlighting, autocompletion, and validation.
    *   "NL-to-UCL 5.0" and "UCL 5.0-to-NL" translators/compilers.
    *   Visual editors or builders for UCL 5.0 messages and Context Mixer Profiles.
*   If you are working on or have developed such a tool, please consider:
    *   Linking to it from the main UCL 5.0 repository (e.g., in a dedicated "Tooling" section in the `README.md` or a separate document).
    *   Ensuring your tool adheres closely to the UCL 5.0 specification.

### 6. Participating in Discussions

*   Engage actively in discussions on GitHub issues and pull requests.
*   Share your use cases, experiences using or implementing UCL 5.0, and provide constructive feedback on proposals.
*   Help answer questions from other community members.

## Pull Request Process

1.  **Fork the repository** and create your feature branch from the `main` (or relevant development) branch.
2.  **Make your changes.**
    *   If adding new specification text or modifying existing text, ensure it is clear, consistent, and well-reasoned.
    *   If adding examples, ensure they are correct and illustrative.
    *   If contributing to tooling (if code is hosted in this repo in the future), add appropriate tests.
3.  **Update documentation** if your changes impact the specification or how UCL 5.0 is used.
4.  Ensure your commit messages are clear, descriptive, and follow any established conventions.
5.  Open a **pull request** to the `main` branch of the original UCL 5.0 repository.
6.  In your pull request description, clearly explain the purpose of your changes, reference any related issues, and summarize what you've done.
7.  Be responsive to feedback and be prepared to discuss your changes and make adjustments as part of the review process.

## Code of Conduct

All contributors are expected to adhere to the project's [Code of Conduct](./CODE_OF_CONDUCT.md) (Note: This file will need to be created). Our aim is to foster an open, welcoming, and collaborative environment.

## Questions?

If you have questions about contributing, or about UCL 5.0 in general, feel free to open an issue with the "question" label.

---

Thank you for considering contributing to UCL 5.0! Your involvement is key to its success and evolution.