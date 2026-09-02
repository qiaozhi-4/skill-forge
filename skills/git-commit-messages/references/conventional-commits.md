# Conventional Commits 1.0.0 (Summary)

Source: https://www.conventionalcommits.org/en/v1.0.0/

## Format

```
<type>[optional scope][optional !]: <description>

[optional body]

[optional footer(s)]
```

## Type

MUST be a noun: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`, etc.

- `feat` → MINOR in SemVer
- `fix` → PATCH in SemVer
- Breaking change → MAJOR in SemVer

## Scope

OPTIONAL noun in parentheses: `feat(parser):`, `fix(api):`

## Description

- Immediate after `: `
- Short summary of the change
- Imperative, present tense
- No capitalization of first letter required, but consistent style preferred
- No trailing period

## Body

- One blank line after description
- Free-form, can contain multiple paragraphs
- Explain the motivation for the change and contrast with previous behavior

## Footer

- One blank line after body
- Token + `:` or `#` + value
- Tokens use `-` for spaces (e.g. `Reviewed-by`)
- Special case: `BREAKING CHANGE: <description>` (may also use `!` after type/scope)

## Examples

```
feat: allow provided config object to extend other configs

BREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

```
feat(lang): add polish language
```

```
fix: prevent racing of requests

Introduce a request id and a reference to latest request. Dismiss
incoming responses other than from latest request.

Remove timeouts which were used to mitigate the racing issue but are
obsolete now.

Reviewed-by: Z
Refs: #123
```

```
docs: correct spelling of CHANGELOG
```

```
feat!: send an email to the customer when a product is shipped
```

```
feat(api)!: send an email to the customer when a product is shipped
```
