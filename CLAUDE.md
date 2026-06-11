# CLAUDE.md — Copilot CLI Configuration

This file provides guidance for GitHub Copilot CLI with Claude models across all projects.
Project-specific conventions belong in per-repo `.github/copilot-instructions.md`, not here.

## About Me

- **Role**: AI/ML engineer — classification, anomaly detection, data engineering
- **Focus areas**: Classification, anomaly detection, and data engineering
- **Primary language**: Python (data science, ML pipelines, ETL, automation)
- **Secondary languages**: Rust (performance-critical tooling, CLI utilities), Julia (numerical/scientific computing)
- **Values**: Organization, readability, and clarity — code should make sense to both AI and humans
- **Decision-making context**: When choosing between approaches, prefer solutions that are **explainable and auditable** — I often work in environments where model decisions and data lineage need to be defensible. Favor well-established, documented libraries over cutting-edge-but-unstable ones. When in doubt, choose the approach that is easier to read and reason about over the one that is marginally more performant.
- **Communication style**: Lead with code, follow with concise rationale. Skip preamble. If something is ambiguous, ask rather than assume.

## Core Principles

1. **Clarity First** - When uncertain about requirements, approach, or structure, ask rather than assume
2. **Explainable & Auditable** - Prefer solutions that are defensible and maintainable over marginally more performant ones
3. **Minimal Scope** - Do what is asked; avoid unnecessary scope creep or "improvements"
4. **Standards-Driven** - Follow organizational coding standards; consistency matters

## Workflow Rules

- **Read before modify** - Always understand the existing code before suggesting changes
- **Edit over Write** - Prefer editing existing files over creating new ones
- **Code over prose** - Lead with code, follow with concise rationale if needed. Keep explanations concise.
- **No secrets** - Never suggest committing credentials, API keys, .env files, or sensitive data
- **Markdown emphasis** - Use **bold**/_italic_ instead of ALL-CAPS for emphasis in comments, documentation, and generated text
- **Root-level configs** - Files like `pyproject.toml`, `Cargo.toml`, `Makefile`, `README.md` belong in project root — don't over-nest config files

## Problem-Solving & Decision-Making

- **When to ask vs. decide**: Ask for clarification when design choices significantly affect the implementation approach, requirements are ambiguous, or multiple valid architectural approaches exist
- **Assume reasonable defaults**: For minor implementation details, proceed with standard patterns
- **Verify changes**: Always run tests, linters, and builds after changes to catch issues early
- **Minimal changes**: Change only what is necessary to achieve the goal; avoid scope creep

## Data Science Conventions

- Separate data loading, transformation, and analysis into distinct steps/functions
- **Never** hardcode file paths — use arguments, config, or environment variables
- Intermediate results should be inspectable (return DataFrames, dicts, or named tuples — not print statements)
- For notebooks: keep cells focused; one logical step per cell
- Prefer reproducible pipelines over ad-hoc scripts when the workflow will be reused
- When appropriate, add lightweight validation (shape checks, null counts, dtype assertions) rather than full test suites
- For ML work: track experiments, log hyperparameters, and version datasets. Prefer deterministic seeds for reproducibility.

## File Organization Guidance

| Directory | Contents |
|-----------|----------|
| `src/` | **All source code** (the project directory name provides the package name) |
| `notebooks/` | Jupyter notebooks |
| `data/` | Raw and processed data (gitignored as appropriate) |
| `tests/` | Test files |
| `scripts/` | One-off or utility scripts |
| `docs/` | Documentation |
| `config/` | Configuration files (YAML, TOML, etc.) |

Root-level files like `pyproject.toml`, `Cargo.toml`, `Makefile`, `justfile`, `README.md`, `.gitignore`, and `CLAUDE.md` are expected in the root.

Use `src/` for source code. Do not name the source directory after the package — the project directory provides that identity. Language-conventional alternatives like `lib/` (Rust crates, Julia packages) or `app/` (application entry points) are acceptable when they follow the language's standard layout.

## Git Conventions

Follow conventional commit format:

```
<type>(<scope>): <short summary>

[optional body]
```

Common types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

- Commits should be atomic and focused on a single change
- Never commit secrets, credentials, or sensitive data
- Write commit messages in the imperative mood ("add feature" not "added feature")

## Testing Standards

- **Coverage targets**: Aim for >80% coverage on core logic
- **Meaningful test names**: Use descriptive names that explain what is being tested and expected
- **Test organization**: One test file per module, organized by functionality
- Use pytest for Python; `#[cfg(test)]` modules for Rust

## Documentation Standards

- **Code comments**: Only comment code that needs clarification; self-documenting code is preferred
- **Docstrings**: Include purpose, parameters, and return values for public APIs
- **Type hints**: Use language-native type hints for all public function signatures

## Obsidian Knowledge Base

Your comprehensive knowledge base lives in an Obsidian vault. Consult it for related notes,
past decisions, known patterns, and research findings before starting new work.

> **Availability:** Obsidian connects via the Local REST API plugin at `http://localhost:27123`.
> If unreachable, skip Obsidian steps gracefully.

### Vault Structure

**Primary domains:**

- **projects/** — Work and academic projects (active and archived)
  - `projects/work/<project>/` — Work projects
  - Each project has: `MOC - ProjectName.md`, `context/`, `meetings/`
- **knowledge/** — Technical knowledge base:
  - `knowledge/remote-sensing/` — Satellite imagery, cloud detection
  - `knowledge/ai-ml/` — ML algorithms, computer vision
  - `knowledge/infrastructure/` — AWS, Azure, HPC, containerization
  - `knowledge/development/` — Testing, frontend, backend, tools
- **journal/** — Daily notes (`YYYY/MM - MMM/YYYY-MM-DD.md`)

### When to Consult Obsidian

- **At project start**: Search for project name, technology stack, or domain concepts
- **Unfamiliar concepts**: Check for past evaluations of libraries, frameworks, or patterns
- **Architectural decisions**: Review related past decisions and their rationale

### What to Capture

- **Research findings** → `knowledge/<domain>/<subdomain>/<topic>.md`
- **Troubleshooting patterns** → `knowledge/<domain>/<subdomain>/<problem-pattern>.md`
- **Project decisions** → `projects/<work|academic>/<project>/context/<topic>.md`

---

## Project-Specific Overrides

Each repository may include a `.github/copilot-instructions.md` file with project-specific
conventions. **Resolution order:**

1. `.github/copilot-instructions.md` (project-specific, highest priority)
2. Language-specific file (`copilot-instructions-rust.md`, `copilot-instructions-python.md`)
3. This file (general guidance)

## What Belongs in a Project-Level File (Not Here)

- Build/test/run commands specific to that project
- Architecture diagrams and component maps
- Release and deployment processes
- Project-specific environment setup
- CI/CD pipeline details
- Dependencies and version constraints
