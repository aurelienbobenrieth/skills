# Library-shipped skills (TanStack Intent)

TanStack Intent (`@tanstack/intent`) is CLI tooling for shipping Agent Skills inside npm packages. The artifact is the same `SKILL.md` format - Intent adds distribution, validation, and scaffolding on top.

## Extended frontmatter

Library-shipped skills use additional fields beyond the base spec:

```yaml
---
name: core
description: Core patterns for TanStack Query. Use when...
type: core                              # core | sub-skill | framework | lifecycle | composition | security
library: "@tanstack/react-query"
library_version: ">=5.0.0"
sources:                                # Docs the skill was derived from
  - "TanStack/query:docs/overview.md"
  - "TanStack/query:docs/guides/*.md"
---
```

Framework skills add:

```yaml
framework: react                        # react | vue | solid | svelte | angular
requires: [core]                        # Must reference the core skill
```

Sub-skills use nested names matching their directory path: `name: db/core/live-queries` lives at `skills/db/core/live-queries/SKILL.md`.

## Body conventions

Structure the body as:

1. **Setup** - minimal working configuration
2. **Core Patterns** (2-4) - the essential patterns with code examples
3. **Common Mistakes** (minimum 3, complex skills 5-6) - each with:
   - Priority: CRITICAL, HIGH, or MEDIUM
   - Wrong code and correct code
   - One-sentence explanation of the mechanism
   - Source reference

Mistakes must be plausible (an agent would actually make them), silent (no obvious error), and grounded (sourced from docs, issues, or real failures).

## Validation rules

`npx @tanstack/intent@latest validate` checks:

- Frontmatter exists and parses as valid YAML
- `name` and `description` are present
- `name` matches directory path
- `description` under 1024 characters
- SKILL.md under 500 lines
- `framework` type skills have a `requires` array
- Artifact files exist in `skills/_artifacts/` (non-monorepo): `domain_map.yaml`, `skill_spec.md`, `skill_tree.yaml`

## Packaging

1. Run `npx @tanstack/intent@latest edit-package-json` to add:
   - `"tanstack-intent"` to `keywords`
   - `"skills"` to `files`
   - `"!skills/_artifacts"` to `files`
2. Add `@tanstack/intent` as a `devDependency`
3. Run `npx @tanstack/intent@latest setup-github-actions` for CI validation

## Scaffolding workflow

`npx @tanstack/intent@latest scaffold` prints a structured prompt for an AI agent to execute three phases:

1. **Domain Discovery** - scans docs, source, issues; interviews the maintainer; produces `domain_map.yaml` and `skill_spec.md`
2. **Tree Generation** - designs skill taxonomy; produces `skill_tree.yaml`
3. **Skill Generation** - writes individual SKILL.md files

Each phase has a maintainer review gate before proceeding.

## Other commands

- `intent list` - discover intent-enabled packages in `node_modules`
- `intent stale` - detect version drift and undeclared source changes
- `intent meta <name>` - access built-in meta-skills (domain-discovery, tree-generator, generate-skill, feedback-collection, skill-staleness-check)
- `intent install` - generate skill mappings for agent config files (AGENTS.md, CLAUDE.md, .cursorrules)
