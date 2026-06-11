# Copilot Instructions

This directory contains instructions for the GitHub Copilot CLI, guiding how Copilot should interact with projects and code.

---

## Quick Navigation

### For All Projects

Start here: **[copilot-instructions-general.md](./copilot-instructions-general.md)**

- Cross-project standards
- Code organization & architecture
- Problem-solving & decision-making
- Copilot CLI tools & efficiency patterns
- Cutting-edge project structure principles
- Documentation, testing, and dependency standards
- When to ask vs. decide

### Language-Specific Guidance

- **Rust**: [copilot-instructions-rust.md](./copilot-instructions-rust.md)
  - Code style, formatting, and naming conventions
  - Modern module organization (file-based modules)
  - Comments, documentation, and error handling
  - Testing patterns and dependency management

- **Python**: [copilot-instructions-python.md](./copilot-instructions-python.md)
  - Python-specific coding standards and patterns

### Project-Specific Instructions

Each project repository may include a `.github/copilot-instructions.md` file with:

- Project overview and core principles
- Module/component structure and responsibilities
- Project-specific workflows and conventions
- References to this general guidance (`$HOME/.copilot/`)

---

## How to Use These Instructions

1. **Always start with [copilot-instructions-general.md](./copilot-instructions-general.md)** for cross-project standards
2. **Reference language-specific files** (copilot-instructions-rust.md, etc.) for concrete implementation guidance
3. **Check project repositories** for `.github/copilot-instructions.md` with project-specific details
4. **Follow the decision-making principles** in the general file to know when to ask vs. decide autonomously

---

## Key Principles

- **Minimal changes achieve goals faster** and are easier to review
- **Verification gates** (tests, linting, builds) are mandatory checkpoints
- **Cutting-edge structure** is preferred: use modern patterns in each language/ecosystem
- **Efficiency matters**: Use parallel tool calls, command chaining, and focused output
- **Clarity over cleverness**: Code should be self-documenting and explicit

---

## Document Maintenance

These instructions are living documents. Updates should:

- Maintain backward compatibility with existing guidance
- Add sections for new language support or tools
- Link to project-specific guidance where appropriate
- Keep language-specific files focused on their domain
