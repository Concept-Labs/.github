# Copilot Instructions for Concept-Labs Repositories

These rules are for GitHub Copilot and contributors. Follow them when generating code, writing docs, creating branches, and proposing changes.

## Branch naming

- Features: use `feature/human-readable-name`
  - Example: `feature/add-user-profile-endpoint`
- Bug fixes: use `bugfix/human-readable_name`
  - Example: `bugfix/fix_null_pointer_in_resolver`

Notes:
- Keep names concise, descriptive, and lowercase.
- Use hyphens for features as shown; use underscores for bugfix branches as shown.
- Do not include ticket numbers unless project conventions require them.

## Code quality and style

- Code must be clear, maintainable, and well-documented.
- Always include PHPDoc for:
  - Public classes, interfaces, traits
  - Public methods and properties
  - Non-trivial protected methods
  - Include `@param`, `@return`, and `@throws` where applicable
- Adhere to SOLID, DRY, and KISS principles.
- Follow relevant PSR standards where applicable:
  - PSR-1 (Basic Coding Standard)
  - PSR-4 (Autoloading)
  - PSR-12 (Extended Coding Style)
  - PSR-3 (Logging), PSR-7/15/17/18 as applicable to HTTP/Middleware/Factories/HTTP clients
- Prefer:
  - Strong typing and `declare(strict_types=1);`
  - Interfaces over concrete implementations for dependencies
  - Dependency Injection
  - Small, focused classes and methods
- Avoid:
  - Magic numbers and strings (extract named constants)
  - Unnecessary complexity and deep nesting
  - Global state and side effects in pure functions

## Performance

- Be mindful of algorithmic complexity and memory usage.
- Avoid N+1 database or IO patterns; batch or eager-load when appropriate.
- Cache results where it is safe and beneficial.
- Prefer streaming/iterators for large datasets over loading everything into memory.
- Measure where possible; do not micro-optimize prematurely.

## Documentation

All changes must be documented. Update the documentation whenever behavior, configuration, or public APIs change.

### Top-level README.md

The top-level README should include:

- Marketing-style overview
- Badges:
  - PHP version
  - License
  - PSR standards
  - Latest release version
- Symbols/branding (as applicable)
- Philosophy
  - Concept-Labs Ecosystem overview (dependency injection, Singularity configuration according to the core packages: https://github.com/Concept-Labs/singularity, https://github.com/Concept-Labs/config and related)
- Conventions
- Basic “Get Started”
- Table of Contents with links to sub-documentation
- General code and configuration examples (if needed)
- Quick Links
- FAQs
- Contributing
- IntelliSense/API documentation links
- Footer

### Detailed documentation

- Located in the `docs/` directory.
- Provide very detailed documentation with real-life examples and edge cases.
- Include configuration, extension points, and troubleshooting.

## Changelog

- All changes must be listed in `CHANGELOG.md`.
- Prefer “Keep a Changelog” format and Semantic Versioning when possible.

## Tests

- Tests must be created for new features and updated when code changes.
- Cover critical paths, edge cases, and error handling.
- Include unit tests; add integration/end-to-end tests where applicable.
- Keep tests deterministic, isolated, and fast.

## Pull Requests (PRs)

- Ensure the branch follows the naming convention above.
- Include a clear PR description:
  - What changed and why
  - Links to related issues (if any)
  - Notes on breaking changes or migrations
  - Performance considerations (if relevant)
  - Screenshots or logs where helpful
- Update documentation and changelog in the same PR when applicable.
- Ensure CI passes (linting, static analysis, tests).

## Copilot-specific guidance

When generating suggestions, Copilot should:

- Produce code that complies with PSR standards and the SOLID/DRY/KISS principles.
- Include complete PHPDoc blocks for public APIs.
- Prefer dependency injection and interfaces.
- Add or update tests matching the code changes.
- Propose updates to README.md, docs/ content, and CHANGELOG.md when behavior or public APIs change.
- Suggest branch names using the conventions:
  - `feature/...` for features (hyphen-separated)
  - `bugfix/...` for fixes (underscore-separated)

## Pre-merge checklist

- [ ] Branch name follows the convention
- [ ] Code adheres to PSR (PSR-1/4/12+) and SOLID/DRY/KISS
- [ ] PHPDoc is complete and accurate
- [ ] Performance considerations addressed
- [ ] Tests added/updated and passing
- [ ] README.md/docs updated as needed
- [ ] CHANGELOG.md updated
- [ ] No unnecessary files or debug code included
