# Python agent template

Starting point for a Python project that is built together with a coding agent. It contains a package managed with uv, `ruff` and `ty` as pre-commit hooks, and agent rules in `AGENTS.md`.

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

`AGENTS.md` holds the rules for coding agents: how to commit, how to write code, how to write docs. Cursor and most other agent harnesses read it automatically from the repository root. It points to `docs/DESIGN.md` for the design and to `docs/TODO.md` for the open work.

`AGENTS.md` asks for the caveman and ponytail style. Both are separate tools that have to be installed once per machine, so they are not part of this repository:

- [caveman](https://github.com/JuliusBrussee/caveman) makes the agent answer in short, plain fragments.
- [ponytail](https://github.com/DietrichGebert/ponytail) makes the agent pick the simplest solution that works.

The steps differ per harness. Most of them write outside this repository, into the user's home directory, so an agent should say what it is about to run and ask first. Both tools need `node` on the PATH.

### Cursor

Ponytail has Cursor hooks. Install them from a clone, and keep the clone where it is, because the hooks run from it:

```sh
git clone https://github.com/DietrichGebert/ponytail
node ponytail/scripts/cursor-hooks.js install
```

This writes to `~/.cursor/hooks.json`. Open a new chat afterwards. `/ponytail lite`, `/ponytail full`, `/ponytail ultra` and `/ponytail off`, sent as a plain message, switch the level.

Caveman has no Cursor plugin and no Cursor hooks yet. [Issue 405](https://github.com/JuliusBrussee/caveman/issues/405) describes what Cursor needs and how to build it. Read the issue and set up the `sessionStart` hook as it describes, so that caveman is active in every new chat. Keep the entries that are already in `~/.cursor/hooks.json`, such as ponytail's.

Also install the caveman skills (for example `caveman-commit` and `caveman-review`) into your user directory:

```sh
npx skills add JuliusBrussee/caveman -a cursor -g
```

### Claude Code

Both tools are plugins with hooks and all commands. In a terminal:

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

### Other harnesses

Follow the install section for your harness in the [caveman INSTALL.md](https://github.com/JuliusBrussee/caveman/blob/main/INSTALL.md) and the [ponytail README](https://github.com/DietrichGebert/ponytail#readme).

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
