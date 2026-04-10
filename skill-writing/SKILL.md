---
name: skill-writing
description: Guide for creating agent skills that follow the Agent Skills specification. Use when user wants to create, write, draft, or improve a skill. Covers structure, description optimization, progressive disclosure, scripts, and evaluation.
---

# Skill Writing

## Before you start

Ask yourself: is a skill the right tool? A skill encodes reusable, domain-specific knowledge that an agent lacks on its own. If the task is a one-off, a simple prompt handles it better. Push back when a skill request is really a script, a config file, or a one-time instruction.

When the user's intent is clear, proceed directly. When it is ambiguous, clarify with targeted questions:

- What task or domain does this cover?
- What specific use cases should it handle?
- Does it need scripts, or instructions alone?
- What source material exists (docs, runbooks, code reviews, past conversations)?

Ground the skill in real expertise - internal docs, actual incident reports, code review patterns. A skill synthesized from project-specific material outperforms one built from generic knowledge.

## Structure

```
skill-name/
├── SKILL.md          # Required: frontmatter + instructions
├── scripts/          # Optional: deterministic, reusable logic
├── references/       # Optional: detailed docs loaded on demand
└── assets/           # Optional: templates, schemas, static resources
```

## SKILL.md

### Frontmatter

```yaml
---
name: lowercase-hyphenated    # Must match directory name, max 64 chars
description: What it does and when to use it. Max 1024 chars.
license: MIT                  # Optional
compatibility: Requires X     # Optional, only if environment-specific
allowed-tools: Bash Read      # Optional, experimental
---
```

### Description

The description carries the entire triggering burden. The agent sees only `name` and `description` at startup.

- First sentence: what the skill does.
- Second sentence: "Use when [specific triggers]."
- Include keywords the user might say, even indirect ones.
- Be pushy about when to activate - list contexts explicitly.
- Stay under 1024 characters.

### Body

Write what the agent would get wrong without this skill. Skip what it already knows.

- Lead with the core workflow. Put edge cases and advanced content in `references/`.
- Keep `SKILL.md` under 500 lines and 5000 tokens.
- Use procedures over declarations: teach *how to approach* a class of problems, not *what to produce* for one instance.
- Provide defaults, not menus. Pick one tool/approach, mention alternatives briefly.
- Add a gotchas section for environment-specific facts that defy reasonable assumptions.

## Writing style

Follow the [agent-communication](../agent-communication/SKILL.md) guidelines:

- Lead with the answer. Reasoning follows only when it aids the next step.
- One sentence beats three. Cut filler, preamble, transitions.
- Write in affirmative form. State what to do, not what to avoid.
- Every token costs context, money, and human attention. Earn each one.
- Use hyphens or commas instead of em dashes.

## Progressive disclosure

1. **Frontmatter** (~100 tokens): loaded at startup for all skills.
2. **SKILL.md body** (<5000 tokens): loaded when the skill activates.
3. **References/scripts** (as needed): loaded on demand.

Tell the agent *when* to load each file: "Read `references/api-errors.md` if the API returns a non-200 status" beats "see references/ for details."

## Scripts

Add a script when:

- The operation is deterministic (validation, formatting, parsing).
- The agent would reinvent the same logic each run.
- Explicit error handling matters.

Script design:

- Accept all input via flags, env vars, or stdin. No interactive prompts.
- Include `--help` with description, flags, and examples.
- Write actionable error messages: what went wrong, what was expected, what to try.
- Output structured data (JSON, CSV) to stdout, diagnostics to stderr.
- Make scripts idempotent - agents retry.
- Use inline dependency declarations (PEP 723 for Python, `npm:` for Deno, `bundler/inline` for Ruby).

## Library-shipped skills (TanStack Intent)

When writing skills that ship inside an npm package, read [references/intent.md](references/intent.md) for extended frontmatter fields, body conventions, validation rules, and packaging steps. Use `npx @tanstack/intent@latest validate` to check compliance.

## Review checklist

Before delivering, verify:

- [ ] Description includes "Use when..." triggers and stays under 1024 chars
- [ ] SKILL.md under 500 lines, focused on what the agent lacks
- [ ] Directory name matches `name` field (lowercase, hyphenated)
- [ ] No time-sensitive information
- [ ] Consistent terminology throughout
- [ ] References one level deep from SKILL.md
- [ ] Writing follows agent-communication style (concise, affirmative, earned tokens)

## After delivery

Present the draft to the user. Ask:

- Does this cover your use cases?
- Anything missing or unclear?
- Should any section be more or less detailed?

Refine by running the skill against real tasks. Watch execution traces - if the agent wastes time on unproductive steps, the instructions are too vague or too numerous.
