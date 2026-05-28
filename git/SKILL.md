---
name: git
description: Git workflow skill covering conventional commits, intelligent staging, pull requests, and gh CLI usage. Use when committing, creating PRs, or managing git history.
---

# Git

## Commits

### Grouping

- Group changes into cohesive commits by unit of work: one commit per logical change that could be deployed, reverted, or cherry-picked independently.
- When a diff spans multiple concerns (feature + refactor, fix + test, rename + behavior change), split into separate commits. Stage files selectively with `git add <paths>`.
- Ask yourself: "if this commit were reverted, would exactly one coherent thing disappear?" If not, split it.

### Conventional Commit Format

```
<type>[scope]: <description>

[body]

[footer]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.

- Derive type and scope from the diff, not from the user's words.
- Description: imperative mood, present tense, under 72 chars. "add", not "added" or "adds".
- Body: explain *why*, not *what*. The diff already shows the what.
- Breaking changes: add `!` after type/scope and a `BREAKING CHANGE:` footer.
- Reference issues in the footer: `Closes #123`, `Refs #456`.

### Authoring

- Read author name and email from `git config user.name` and `git config user.email` before every commit. Use those values exclusively - never substitute another identity.
- Commits are the user's work. Omit `Co-Authored-By` trailers for LLMs.
- Honor the user's signing config (`commit.gpgsign`, `user.signingkey`). Never pass `--no-gpg-sign` or `-c commit.gpgsign=false`.

### Safety

- Read `git diff --staged` (or `git diff` when nothing is staged) and `git status --porcelain` before committing.
- Stage files explicitly by path. Avoid `git add -A` or `git add .` to prevent leaking secrets or binaries.
- Leave hooks enabled. When a hook fails, fix the issue and create a new commit - never amend the failed one.
- Treat protected or shared branches according to repo policy. Before any action that rewrites, deletes, overwrites, force-pushes, unstages, resets, rebases, cherry-picks, or otherwise alters existing work on those branches, ask for explicit human approval.
- Ask before any ambiguous or destructive action that could lose work in the current diff or worktree, including `push --force`, `reset`, `reset --hard`, `clean`, `checkout -- <path>`, `restore`, `branch -D`, branch deletion, history rewrite, conflicted rebase/cherry-pick resolution, or removing files already present in the diff.
- If the user asks for an operation that could destroy or overwrite work, first state the exact files, branch, and command class affected, then wait for confirmation.
- Prefer non-destructive inspection and narrowly scoped staging commands. When in doubt, stop and ask.

## Pull Requests

### Title

- Use conventional commit format: `<type>[scope]: <description>`.
- Keep under 70 characters. Put detail in the body.

### Body

```
## What changed
- <what changed, and why only when the diff or title does not make it obvious>
```

- Keep the body concise. A small PR may need only one `What changed` bullet.
- Write for another developer deciding how to review without opening every changed file.
- Include only information the diff does not communicate well: intent, context, decision making, tradeoffs, known limits, or non-obvious validation.
- Do not turn the body into an exhaustive changelog or repeat the file diff.
- Add optional sections only when they add decision-useful context:

```
## Evidence
- <behavior proof, live smoke result, migration proof, or non-obvious check that changes reviewer confidence>

## Notes
- <risk, compromise, follow-up, rollout note, or related issue>
```

- Do not list routine formatting, lint, typecheck, or test commands unless the command result is decision-useful evidence for this PR.
- Prefer evidence that proves behavior or risk: records created, side effects observed, smoke output, migration dry-run result, recovery path proof, or a specific failing/passing regression test.
- Omit optional sections when they would only say that normal checks were run.
- Link related issues in `Notes` when useful: `Closes #N`, `Refs #N`.

## gh CLI

- Use `gh pr create --title "..." --body "..."` with a heredoc for multi-line bodies.
- Use `gh pr view`, `gh pr checks`, `gh pr merge` for PR lifecycle management.
- Use `gh issue list`, `gh issue view` for issue context before writing PR descriptions.
- Push with `git push -u origin HEAD` before creating a PR when the remote branch is missing.
