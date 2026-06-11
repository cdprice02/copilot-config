# General Copilot Instructions

## Overview
These instructions guide how Copilot should interact with all projects. Language-specific guidance is provided in separate files.

---

## Code Organization & Architecture

### Existing Patterns
- Follow established patterns from the codebase first
- Ask for approval only on larger architectural decisions
- Base architectural decisions on patterns from established, well-known projects
- Do not create new organizational patterns without explicit request or clear justification

### File Placement
- Use existing directory structure as reference
- Place new files logically near related code
- Ask before making significant directory structure changes

---

## Problem-Solving & Decision-Making

### Autonomy
- Make reasonable technical decisions independently
- Prefer well-established approaches over novel solutions
- Document trade-offs briefly when presenting changes

### Breaking Changes to APIs
- **Always warn** if a change would break existing public APIs
- Suggest the change with clear warnings about impact
- Proceed only if the user confirms or the change is explicitly requested
- Prioritize backward compatibility for projects that may have users

### Explanation Style
- Explain reasoning clearly but concisely
- Show all changes and explain why they're reasonable
- Do not overcomplicate explanations
- Focus on "what changed" and "why" rather than lengthy rationale

---

## Copilot CLI Tools & Efficiency

### Tool Usage Strategy
- **task (parallel agents)**: Use for complex multi-step tasks (explore, task, code-review, general-purpose agents)
- **explore**: Use for understanding codebases and answering "how does X work?" questions
- **grep/glob**: Use for targeted file searches and content matching
- **bash**: Use for running commands, builds, tests, and ecosystem tools
- **report_intent**: Use to communicate progress in the UI (called with other tools, never in isolation)

### Efficiency Patterns
- **Parallel tool calls**: Make independent tool calls simultaneously in one response (multiple reads, multiple edits to different files)
- **Command chaining**: Use && in bash to run dependent commands sequentially
- **Suppressing output**: Use --quiet, --no-pager, or pipe to grep/head to keep output focused
- **Batched edits**: Use multiple edit calls in one response to modify the same file efficiently

### Example: Efficient Multi-Tool Usage
Instead of sequential calls:
```
1. grep to find files
2. view to read file 1
3. view to read file 2
4. edit file 1
5. edit file 2
```

Call in parallel where possible:
```
1. grep + glob in parallel to find files + view files in parallel
2. edit files in same response after understanding changes needed
```

---

## Git Workflow

### Commits
- Group **related changes** into logical, single-purpose commits
- Write clear, descriptive commit messages that explain intent
- Reference issue numbers or milestones when applicable (e.g., "Closes #123", "Implements Phase 1")
- Use conventional commit format where appropriate (feat:, fix:, refactor:, docs:, etc.)

### Pull Requests / Code Review
- Keep changes focused and minimal (follow the "smallest change principle")
- Summarize changes in commit messages rather than verbose PR descriptions
- Flag any breaking changes or significant decisions in commit messages

---

## Documentation Standards

### README.md
- Write somewhat comprehensive READMEs
- Include: project purpose, core principles, installation, basic usage, contributing guidelines
- Link to supplementary docs (ARCHITECTURE.md, CONTRIBUTING.md, etc.) for deep dives
- Follow patterns from well-established GitHub projects

### Supplementary Documentation
- Use additional .md files for large-scale topics:
  - **ARCHITECTURE.md** - Design decisions, module structure, data flow
  - **CONTRIBUTING.md** - Development setup, code standards, PR process
  - **SECURITY.md** - Security policies, reporting vulnerabilities
  - **CHANGELOG.md** - Release notes and breaking changes
- Keep supplementary docs focused and cross-linked from README

### File Comments & Documentation
- Document public APIs thoroughly (use language-specific doc comments)
- Use comments to improve readability, not to restate obvious code
- Explain **why** complex logic exists, not **what** it does (code shows what)

---

## Code Style & Quality

### Formatting
- Always run the language's standard formatter (rustfmt for Rust, black for Python, prettier for JS, etc.)
- Follow community style conventions and established patterns
- Consistent style makes code easier to follow for both humans and AI

### Code Review Principles
- Minimal changes achieve goals faster and are easier to review
- Delete unnecessary code; don't comment it out
- Avoid premature optimization; focus on clarity and correctness first
- Test code thoroughly before considering it complete

---

## Cutting-Edge Project Structure

### Modern Module Organization
- Prefer **file-based modules** over deep directory hierarchies where appropriate (e.g., single files for utilities, clear module boundaries)
- Language-specific patterns vary: follow current best practices for each ecosystem
- Structure should enable rapid navigation and minimize cognitive overhead
- Avoid overly-flat or overly-nested hierarchies

### Keeping Current
- Stay informed of modern patterns in each language's ecosystem
- Example approaches:
  - **Rust**: Prefer workspace organization, feature flags, clear module visibility
  - **Python**: Use clear package hierarchies with __init__.py, modern typing hints
  - **JavaScript/TypeScript**: Consider ESM, barrel exports, clear entry points
- Do not force dated patterns (monolithic files, deeply-nested directories, etc.)

### Language-Specific Details
- See language-specific instruction files for deep dives
- This file documents the cross-language principle: use modern, well-established patterns

---

## Testing Standards

### Test Philosophy
- Tests should catch real bugs and validate behavior
- Test the public API and important private functions
- Integration tests verify end-to-end workflows
- Use property-based testing where it reveals edge cases

### Test Organization
Language-specific guidance varies. See language-specific instructions.

---

## Dependency Management

### Adding Dependencies
- Use well-established packages first (proven reliability, good maintenance)
- Ask before adding less-familiar or experimental dependencies
- Minimize dependency count; evaluate necessity carefully
- Prefer standard library solutions when reasonable

### Version Strategy
- Pin major and minor versions for stability
- Allow patch updates for bug fixes and features

---

## When to Ask vs. Decide

### Ask Before:
- Making architectural decisions (new modules, major refactors)
- Adding unfamiliar dependencies or tools
- Breaking existing public APIs
- Significant design trade-offs with multiple reasonable approaches
- Changes outside the immediate scope of the current task

### Decide Autonomously:
- Following established code patterns
- Fixing bugs or implementing minor features
- Organizing code within existing structure
- Choosing between equivalent solutions (use community standards)
- Refactoring code that improves clarity without changing behavior

### Verification Gates (Mandatory Checkpoints)
- **Tests**: Run existing test suites; verify all tests pass before considering work complete
- **Linting/Formatting**: Run language formatters and linters; fix any violations
- **Build**: Verify code builds/parses without errors in the target language
- Do not consider a change "done" until these gates pass; they are non-negotiable checkpoints

---

## Best Practices Checklist

- [ ] Changes follow existing patterns and community standards
- [ ] Code is formatted and passes linting
- [ ] Tests are included for new functionality
- [ ] Public APIs are documented
- [ ] Commit messages clearly explain intent
- [ ] Breaking changes are flagged with warnings
- [ ] No unnecessary comments; documentation is literate
- [ ] Dependencies are justified and well-established
- [ ] Changes are minimal and focused
