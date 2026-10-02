# Agent rules

## Docs

- Follow `docs/DESIGN.md`. Design change? Update it first, then code.
- Open work in `docs/TODO.md`. Item done: delete its line in same commit.
- Suggest review of all `.md` files often, at least after each feature.
- Review: check docs against code, list stale parts, fix after user OK.

## Commits

- Small commits. One logical step each.
- No commit without user review and OK.
- Title only, Conventional Commits: `type: imperative summary`.
- Lowercase after colon, no period, under 72 chars.
- Never mention Claude or AI. No `Co-Authored-By` trailer.
- Stage only files of that step.

## Code

- Rule 1: simplest working solution. Wins over every other code rule.
- No abstraction for later.
- Smallest diff that solves the task. No drive-by refactors.

### Clean code

- Code reads like prose.
- Descriptive names. Small functions, one job each.
- Comments rare. Only non-obvious rationale, constraints or units.
- No comments about a fix or change. History goes in commits.

### Black boxes

- Every function, class and module is a black box: clear interface, hidden internals.
- Behavior depends only on inputs and injected collaborators.
- No hidden global state. No reaching into another unit's internals.
- Test through the interface only: inputs in, outputs and effects out.
- Hard to test that way? Interface is wrong, fix the interface.

### Dependency injection

- For collaborators with side effects that get swapped: external APIs, subprocesses, clock, filesystem.
- Pass them in via constructor or parameter. Wire them at top of the program.
- Tests pass in fakes. No patching of internals.
- `Protocol` only with two real implementations. No DI framework, no container.
- Config constants and pure helpers stay plain module code.

### Structure

- Flat first: all modules directly in the package, no subpackages.
- Project grows? Suggest one level of subpackages, grouped by logic.
- Each subpackage exports its interface in `__init__.py`. Others import only that.
- Second level of nesting: very rarely needed. Advise against it.
- Imports between subpackages hint at a structural problem.
- Many cross imports: grouping is wrong. Suggest regrouping.
- Common module used by many is fine. It imports none of its users.
- No circular imports.

## Tooling

- CLI tools: use `click`.
- Run everything through `uv run`.
- Add dependency: `uv add`. Dev tool: `uv add --dev`.
- Hooks: `ruff check --fix`, `ruff format`, `ty check`.
- Run all hooks: `uv run pre-commit run --all-files`.

## Writing style

- Caveman and Ponytail style: most important info only, concise.
- `AGENTS.md`, `docs/DESIGN.md`, `docs/TODO.md`: caveman style, fragments OK.
- `README.md`: plain sentences.

## Subagents

- Delegate well-defined tasks to subagents. Keeps main context small.
