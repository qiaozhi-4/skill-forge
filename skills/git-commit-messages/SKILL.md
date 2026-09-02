---
name: git-commit-messages
description: Generate, rewrite, or validate git commit messages under strict constraints such as Conventional Commits format, length limits, required types, scopes, and language rules. Use when the user asks to write a commit message, generate a commit msg, improve a commit message, enforce conventional commits, or apply any commit message style guide or constraints.
---

# Git Commit Messages

## Overview

Produce high-quality, constrained git commit messages that follow a consistent structure. Default to Conventional Commits 1.0.0 unless the user specifies different rules.

## Core Rules (always apply)

When generating or rewriting a commit message:

1. **Structure** (Conventional Commits default):
   ```
   <type>[optional scope][optional !]: <description>

   [optional body]

   [optional footer(s)]
   ```

2. **Type** (required): Use one of the standard types below. Prefer the most specific match.
   - `feat` — new feature
   - `fix` — bug fix
   - `docs` — documentation only
   - `style` — formatting, whitespace, no code change
   - `refactor` — code change that neither fixes a bug nor adds a feature
   - `perf` — performance improvement
   - `test` — adding or correcting tests
   - `build` — build system or external dependencies
   - `ci` — CI configuration
   - `chore` — other changes that don't modify src or test files
   - `revert` — reverts a previous commit

3. **Scope** (optional): A noun in parentheses describing the section of the codebase, e.g. `feat(api):`, `fix(parser):`.

4. **Breaking change**:
   - Append `!` after type/scope → `feat(api)!:` or
   - Add a footer `BREAKING CHANGE: <description>`

5. **Description**:
   - Imperative mood ("add", not "added" or "adds")
   - No period at the end
   - Maximum 72 characters (hard limit 100)
   - Lowercase after the colon (unless proper noun or acronym)
   - Focus on *why* and *what*, not *how*

6. **Body** (optional but preferred for non-trivial changes):
   - Separated by a blank line
   - Explain motivation, contrast with previous behavior
   - Wrap at 72 characters

7. **Footers** (optional):
   - `BREAKING CHANGE: ...`
   - `Refs: #123`, `Closes: #456`, `Reviewed-by: ...`
   - Use `-` instead of spaces in tokens

## Generation Workflow

1. Analyze the provided diff, staged changes, or description of changes.
2. Determine the primary type and optional scope.
3. Write a concise description following the rules above.
4. Add body only when the change needs explanation.
5. Add footers when relevant (issues, breaking changes, co-authors).
6. Output **only** the final commit message unless the user asks for alternatives or explanation.

## Custom Constraints

If the user provides additional constraints (e.g. "must be in Chinese", "max 50 chars", "always include scope", "use emoji", company style guide), treat them as higher priority than the defaults and apply them strictly.

## Validation Mode

When asked to check or improve an existing commit message:
- Point out violations of the rules
- Provide a corrected version
- Keep the original intent

## Output Format

- By default return a single ready-to-use commit message.
- When multiple options make sense, offer 2–3 ranked alternatives with brief rationale.
- Never wrap the commit message in markdown code fences unless the user explicitly requests it.

## References

- Full Conventional Commits rules: see `references/conventional-commits.md`
