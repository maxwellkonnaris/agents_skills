---
name: python-rust-package-methods-check
description: Review Python packages, Rust crates, and mixed scientific software for correctness, numerical robustness, APIs, tests, performance, and docs.
---

# Python/Rust Package Methods Check

Review changed Python, Rust, or mixed-language scientific software with emphasis on methodological correctness, numerical robustness, package behavior, tests, and documentation.

Use this for:
- Python packages
- Rust crates
- Python bindings to Rust, C, C++, or Fortran
- scientific CLI tools
- simulation code
- numerical methods
- statistical estimators
- bioinformatics pipelines
- pre-PR review before merging

Do not focus mainly on formatting unless formatting affects correctness, maintainability, or user-facing behavior.

## 1. Scientific and statistical correctness

Ask:

- What is the target quantity?
- What is observed versus inferred?
- What is an input, hyperparameter, estimator, posterior quantity, diagnostic, or output?
- Are assumptions stated and consistent with the implementation?
- Are transformations, offsets, links, scales, and parameterizations handled correctly?
- Are probability, log-probability, likelihood, loss, and objective functions clearly distinguished?
- Are quantities returned on the intended scale?
- Are uncertainty-related quantities labeled and computed correctly?
- Are defaults scientifically defensible?
- Are preprocessing, filtering, normalization, pseudocounts, or thresholds hiding assumptions?

## 2. Numerical robustness

Check for:

- unstable subtraction, division, exponentiation, or log operations
- underflow and overflow
- division by zero or near-zero values
- unguarded `log`, `exp`, `softmax`, `logsumexp`, sigmoid, or likelihood calculations
- singular, ill-conditioned, or non-positive-definite matrix operations
- incorrect dtype behavior, integer division, precision loss, or silent casting
- incorrect handling of `NaN`, `Inf`, `None`, missing values, or masked arrays
- poor behavior for small sample sizes, sparse data, rare events, extreme counts, heavy tails, or skewed distributions
- boundary cases such as empty arrays, one-row inputs, one-column inputs, all-zero rows, ties, duplicate IDs, and one-level factors

For Rust specifically, also check:

- panic risk from `unwrap`, `expect`, indexing, slicing, or unchecked assumptions
- misuse of `Result`, `Option`, or error propagation
- unsafe blocks and whether they are justified
- ownership, borrowing, cloning, and allocation patterns that may affect correctness or performance
- numeric conversions using `as` that may truncate, overflow, or lose precision
- parallel code for race conditions or nondeterministic outputs

For Python specifically, also check:

- mutable default arguments
- shape/broadcasting bugs
- pandas index alignment bugs
- dtype coercion
- chained assignment
- hidden global state
- random seed behavior
- inconsistent NumPy, pandas, Polars, PyTorch, JAX, or SciPy array semantics

## 3. API and package behavior

Ask:

- Are public and private APIs separated clearly?
- Are names, arguments, defaults, and return values coherent?
- Are argument checks early and informative?
- Are error messages actionable?
- Is backward compatibility preserved where reasonable?
- Are dependencies necessary and minimal?
- Are examples safe, fast, and reproducible?
- Are CLI arguments documented and stable?
- Are outputs deterministic when they should be?
- Are files written only where the user expects?

For Python packages, check:

- `pyproject.toml`
- package layout
- imports
- optional dependencies
- type hints
- tests
- wheels/build behavior
- CLI entry points

For Rust crates, check:

- `Cargo.toml`
- feature flags
- public API exports
- examples
- docs
- benchmarks
- tests
- error types

## 4. Tests

Propose the smallest tests that would catch real breakage.

Prefer:

- one minimal regression test over broad rewrites
- edge-case tests for boundary inputs
- deterministic tests for random algorithms
- property-style checks when exact values are hard to specify
- numerical tolerance checks with justified tolerances
- cross-language consistency tests for Python/Rust bindings

Do not suggest large test rewrites unless the current tests cannot detect the risk.

## 5. Performance

Flag performance only when it affects usability, scalability, or correctness.

Check:

- avoidable quadratic or cubic complexity
- unnecessary copies
- repeated parsing or repeated allocation
- slow loops that should be vectorized or moved to Rust
- memory growth with large matrices, count tables, sparse arrays, or simulation grids
- parallelization that increases memory more than expected

## 6. Documentation

Flag documentation that no longer matches behavior:

- README
- docstrings
- examples
- vignettes/tutorials
- CLI help
- changelog
- API reference
- mathematical notation
- default values
- output schema

## Output format

Return exactly these sections:

## Summary
2-4 sentences on what changed and the main risk.

## Major issues
For each issue, give:
- Issue
- Why it matters
- Smallest fix

## Suggested tests
List minimal tests to add or update.

## Documentation updates
List docs, examples, or CLI help that should be updated.

## Performance or scalability concerns
Only include concerns that matter for realistic use.

## Nice-to-have improvements
Optional cleanup that is not required for correctness.

Behavior:
- Be skeptical about scientific correctness, not just syntax.
- Prefer the smallest correct patch.
- Distinguish correctness issues from maintainability issues.
- If uncertain, say exactly what is uncertain.
- Review first; do not start editing unless explicitly asked.
