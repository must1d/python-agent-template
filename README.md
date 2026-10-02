# Python agent template

Starting point for a Python project that is built together with a coding agent. It contains a package managed with uv, `ruff` and `ty` as pre-commit hooks, agent rules in `AGENTS.md`, and the caveman and ponytail rules for Cursor.

You can hand this README to your coding agent and ask it to set the project up. The steps below are written so that an agent can follow them in order.

## Project setup

1. Install [uv](https://docs.astral.sh/uv/getting-started/installation/) if `uv --version` fails. uv manages the Python version, the virtual environment and the dependencies.
2. Rename the package. Change `name` in `pyproject.toml` to the new project name, and rename `src/python_agent_template/` to the same name with underscores instead of hyphens.
3. Create the virtual environment and install all dependencies:

   ```sh
   uv sync
   ```

   This creates `.venv/` and installs everything listed in `pyproject.toml`, including the development tools. If the Python version from `.python-version` is missing, uv downloads it. The versions come from `uv.lock`.
4. Install the git hooks. This is needed once per clone, because git does not version `.git/hooks`:

   ```sh
   uv run pre-commit install
   ```

5. Check that the hooks pass:

   ```sh
   uv run pre-commit run --all-files
   ```

   Each commit runs `ruff check --fix`, `ruff format` and `ty check`.
6. Fill in `docs/DESIGN.md` with the goal of the project, and put the first open tasks into `docs/TODO.md`. Replace the title and the first paragraph of this README.

## Agent setup

### Rules in the repository

`AGENTS.md` holds the rules for coding agents: how to commit, how to write code, how to write docs. Cursor and most other agent harnesses read it automatically from the repository root. It points to `docs/DESIGN.md` for the design and to `docs/TODO.md` for the open work.

Two always-on Cursor rules are already in `.cursor/rules/`, so Cursor needs no further setup:

- `caveman.mdc` makes the agent answer in short, plain fragments.
- `ponytail.mdc` makes the agent pick the simplest solution that works.

To turn a rule off, disable it in the Cursor settings or delete the file. To update a rule, copy the newer file from its repository.

### Caveman

Repository: https://github.com/JuliusBrussee/caveman

Caveman has no Cursor plugin and no Cursor hooks. [Issue 405](https://github.com/JuliusBrussee/caveman/issues/405) asks for them and is still open. Cursor plugins need a `.cursor-plugin/plugin.json`, which caveman does not ship. The issue, written in May 2026, also says that Cursor only offers a `sessionStart` hook and no hook that runs on each prompt. The workaround from that issue is the one this template uses: a rule file with `alwaysApply: true`. The cost is that `/caveman lite`, `/caveman ultra` and "stop caveman" do not switch the level in Cursor.

`.cursor/rules/caveman.mdc` is the text of `src/rules/caveman-activate.md` from the caveman repository, with the frontmatter that caveman's own installer writes.

Optional, and it writes outside this repository, so an agent should ask before running it. This installs the caveman skills (for example `caveman-commit` and `caveman-review`) for Cursor in your user directory:

```sh
npx skills add JuliusBrussee/caveman -a cursor -g
```

### Ponytail

Repository: https://github.com/DietrichGebert/ponytail

`.cursor/rules/ponytail.mdc` is copied unchanged from that repository.

Ponytail also has real Cursor hooks, which add level switching with `/ponytail lite`, `/ponytail full`, `/ponytail ultra` and `/ponytail off`. They are installed per machine from a clone and need `node` on the PATH, so they cannot be part of this template. They are optional, and an agent should ask before running this:

```sh
git clone https://github.com/DietrichGebert/ponytail
node ponytail/scripts/cursor-hooks.js install
```

This writes to `~/.cursor/hooks.json`. Add `--project` to write `.cursor/hooks.json` in the project instead. The rule file and the hooks are alternatives: while `.cursor/rules/ponytail.mdc` exists, the hooks inject nothing. Delete the rule file if you install the hooks.

### Claude Code

Claude Code does not use the Cursor rule files. Both tools are plugins there, with hooks and all commands. In a terminal:

```sh
claude plugin marketplace add JuliusBrussee/caveman && claude plugin install caveman@caveman
```

Inside Claude Code, as two separate prompts:

```
/plugin marketplace add DietrichGebert/ponytail
```

```
/plugin install ponytail@ponytail
```

## Running code

Prefix commands with `uv run` to execute them inside the virtual environment:

```sh
uv run python
uv run pre-commit run --all-files
```

## Adding dependencies

```sh
uv add numpy
uv add --dev pytest
```

Use `--dev` for tools that are only needed during development. Both commands update `pyproject.toml` and `uv.lock`, so commit those two files together.
