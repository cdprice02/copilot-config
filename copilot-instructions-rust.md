# Rust-Specific Copilot Instructions

## Overview
These instructions supplement [copilot-instructions-general.md](./copilot-instructions-general.md) with Rust-specific guidance. Refer to the general file for cross-project standards.

---

## Code Style & Formatting

### Rustfmt
- **Always run `cargo fmt`** before finalizing changes
- Follow standard Rust formatting conventions
- Code should pass `cargo fmt --check` without modifications

### Clippy Linting
- Code should pass `cargo clippy` without warnings
- Use clippy suggestions as guidance for idiomatic Rust
- `#[allow(...)]` comments should be rare and justified

### Naming Conventions
- Use lowercase_snake_case for functions, variables, and module names
- Use CamelCase for structs, enums, and traits
- Use SCREAMING_SNAKE_CASE for constants
- Prefer clarity over brevity in naming

### Modern Module Organization
- **Prefer file-based modules over mod.rs files** where possible:
  - Use `src/lexer.rs` instead of `src/lexer/mod.rs` for single-file modules
  - Use `mod.rs` only when you need to re-export items or manage complex submodule hierarchies
- Benefits: Flatter structure, faster navigation, easier imports
- Example: `use crate::lexer;` (from src/lexer.rs) instead of `use crate::lexer::lexer;`

---

## Comments & Documentation

### Public API Documentation (rustdoc)
- All public items must have doc comments using `///`
- Include brief summary and usage examples for complex items
- Use markdown in doc comments for clarity
- Example:
  ```rust
  /// Evaluates the given expression in the current environment.
  ///
  /// # Arguments
  /// * `expr` - The expression to evaluate
  /// * `env` - The evaluation environment
  ///
  /// # Returns
  /// A Result containing the evaluated value or an error
  ///
  /// # Example
  /// ```
  /// let result = evaluate(expr, &env)?;
  /// ```
  pub fn evaluate(expr: &Expr, env: &Environment) -> Result<Value> { ... }
  ```

### Internal Comments
- Use moderate commenting: document **why** code exists, not **what** it does
- Code should be self-documenting; complex logic gets brief explanatory comments
- Avoid comments that restate the code
- Example of good commenting:
  ```rust
  // Cloning is necessary here because we need to modify the environment
  // without affecting subsequent evaluations in the loop
  let mut env_clone = env.clone();
  ```

### TODO/FIXME Comments
- Use sparingly; prefer GitHub issues for tracked work
- Format: `// TODO: description` or `// FIXME: description`
- Link to issue numbers when possible: `// TODO: #42 - implement caching`

---

## Error Handling

### Error Types
- Use `anyhow::Result<T>` for simple error handling in binaries/applications
- Use `thiserror` to define custom error types for libraries
- Combine `thiserror` with `anyhow` for rich error context

### Error Type Pattern (Libraries)
```rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum LexError {
    #[error("Unexpected character: {char:?}")]
    UnexpectedChar { char: char, line: usize },

    #[error("Unterminated string literal")]
    UnterminatedString,
}

pub type Result<T> = std::result::Result<T, LexError>;
```

### Error Type Pattern (Binaries)
```rust
use anyhow::{Context, Result};

fn main() -> Result<()> {
    let config = load_config().context("Failed to load configuration")?;
    process(&config)?;
    Ok(())
}
```

### String Context with miette
- Use `miette` for errors involving source code spans (lexer, parser, REPL)
- Provides rich, formatted error messages with context
- Example:
  ```rust
  use miette::{miette, Result};

  if !is_valid {
      return Err(miette!("Invalid syntax at line {}", line));
  }
  ```

### Error Handling Best Practices
- Use `?` operator for propagation; avoid `.unwrap()` in library code
- Provide context with `.context("description")` for error chains
- Match on specific error types when recovery is possible
- Panic only for truly unrecoverable internal errors (not user input)

---

## Testing

### Test Organization

#### Unit Tests (in-file)
- Place in a `#[cfg(test)] mod tests { ... }` block at the end of each file
- Test private and public functions
- One test function per behavior or assertion group
- Example:
  ```rust
  #[cfg(test)]
  mod tests {
      use super::*;

      #[test]
      fn test_lexer_tokenizes_simple_expression() {
          let input = "1 + 2";
          let tokens = lex(input).unwrap();
          assert_eq!(tokens.len(), 3);
      }
  }
  ```

#### Integration Tests (tests/ directory)
- Create `tests/` directory at crate root
- Test complete workflows and public APIs
- One file per major feature or workflow
- Example: `tests/end_to_end_repl.rs`, `tests/parsing.rs`

#### Benchmarks (benches/ directory)
- Create `benches/` directory at crate root
- Use `criterion` crate for benchmarking
- Benchmark performance-critical paths
- Example: `benches/lexing_performance.rs`

#### Examples (examples/ directory)
- Create `examples/` directory for runnable examples
- Use for demonstrating API usage
- Examples serve as documentation and verification

### Test Naming
- Unit test function names: `test_<what_is_being_tested>_<expected_outcome>`
  - `test_lexer_handles_strings`, `test_parser_rejects_invalid_syntax`
- Integration test file names: descriptive and specific
  - `end_to_end_shell_session.rs`, `command_execution.rs`

### Test Coverage
- Aim for high coverage of public APIs and critical paths
- Unit tests should catch edge cases and error conditions
- Integration tests verify real-world workflows
- Use code coverage tools (preferably `tarpaulin`) in CI

### Test Practices
- Write tests as you write code; do not leave them for later
- Tests should be deterministic and not flaky
- Use fixtures or helper functions for common setup
- Keep tests focused: one assertion or one behavior per test
- Use descriptive assertion messages: `assert_eq!(a, b, "expected {} but got {}", expected, actual)`

---

## Module Organization

### Public vs. Private
- Mark items `pub` only if they're part of the public API
- Use `pub(crate)` for internal public items
- Private module structure is an implementation detail; don't expose it

### Module Structure Example
```
src/
├── lib.rs           # Public API entry point
├── lexer/
│   ├── mod.rs       # Lexer public API
│   └── token.rs     # Token definition
├── parser/
│   ├── mod.rs       # Parser public API
│   └── ast.rs       # AST definitions
├── evaluator/
│   ├── mod.rs       # Evaluator public API
│   └── env.rs       # Environment handling
└── main.rs          # Binary entry point (optional)

tests/
├── lexing.rs        # Lexer integration tests
├── parsing.rs       # Parser integration tests
└── end_to_end.rs    # Full workflow tests

benches/
├── lexing.rs        # Lexer performance benchmarks
└── parsing.rs       # Parser performance benchmarks

examples/
└── basic_usage.rs   # Example usage
```

### Module Documentation
- Add module-level doc comments: `//! Module description`
- Example:
  ```rust
  //! The lexer module is responsible for tokenizing input strings.
  //!
  //! It converts raw source code into a stream of tokens that the parser consumes.
  ```

---

## Dependencies

### Well-Established Crates
Prefer these for common needs:
- **Error handling**: `anyhow`, `thiserror`, `miette`
- **CLI args**: `clap` (feature-complete, well-documented)
- **Serialization**: `serde`, `serde_json`, `toml`
- **Testing**: `criterion` (benchmarking), `proptest` (property testing)
- **Logging**: `tracing`, `log`
- **Async**: `tokio` (if needed)

### Before Adding Dependencies
- Check if std library provides the functionality
- Research maintenance and community adoption
- Ask before adding unfamiliar crates

### Versioning
- Pin major and minor versions for stability
- Allow patch updates for bug fixes and features
- Use MSRV (Minimum Supported Rust Version) where applicable

---

## Common Patterns

### Result Chains
```rust
fn process(input: &str) -> anyhow::Result<Output> {
    let tokens = lex(input)?;
    let ast = parse(tokens)?;
    let result = evaluate(ast)?;
    Ok(result)
}
```

### Option/Result Matching
```rust
match result {
    Ok(value) => println!("{}", value),
    Err(e) => eprintln!("Error: {}", e),
}
```

### Builder Pattern (for complex configs)
```rust
pub struct LexerConfig {
    pub allow_unicode: bool,
    pub line_comments: bool,
}

impl LexerConfig {
    pub fn new() -> Self {
        Self {
            allow_unicode: true,
            line_comments: true,
        }
    }

    pub fn with_unicode(mut self, allow: bool) -> Self {
        self.allow_unicode = allow;
        self
    }
}
```

---

## Best Practices Checklist (Rust)

- [ ] Code passes `cargo fmt --check`
- [ ] Code passes `cargo clippy` without warnings
- [ ] Public APIs have doc comments with examples
- [ ] Tests are included for new functionality
- [ ] Error handling uses Result types appropriately
- [ ] Tests are organized: unit tests in-file, integration tests in tests/
- [ ] No `.unwrap()` in library code (use `?` operator)
- [ ] Module structure is logical and follows examples
- [ ] Dependencies are well-established and justified
- [ ] Changes follow established patterns and principles in the project
