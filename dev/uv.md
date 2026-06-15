# uv

A fast Python package and project manager written in Rust (by Astral). Can replace pip, pip-tools, pipx, poetry, pyenv, and virtualenv. 

## Install

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# Install via pip
pip install uv
```

## Manage Project

```bash
# Create a new project
uv init my-project
cd my-project

# Add dependencies
uv add requests
uv add pytest --dev          # dev dependency
uv add "numpy>=1.24"         # version constraint

# Remove a dependency
uv remove requests

# Sync dependencies (based on lockfile)
'''
uv run auto-syncs before running; use uv sync alone when we need the env ready without running (e.g., CI install step, IDE venv setup)
'''
uv sync 

# Run the project
uv run python main.py
uv run pytest
```

## Virtual Environment (venv)

```bash
# Create a virtual environment
uv venv
uv venv .venv --python 3.12  # specify Python version

# Activate
source .venv/bin/activate    # Linux/macOS
.venv\Scripts\activate       # Windows

# Run without activating venv (uv run handles it automatically)
uv run python -c "import sys; print(sys.version)"
```

## Python Version Management

```bash
# Install Python
uv python install 3.12
uv python install 3.11 3.12  # multiple versions

# List installed Python versions
uv python list

# Pin a specific version (creates .python-version file)
uv python pin 3.12
```

## Package Installation (pip replacement)

```bash
# Install packages
uv pip install requests
uv pip install -r requirements.txt

# Uninstall a package
uv pip uninstall requests

# List installed packages
uv pip list

# Generate requirements.txt
uv pip freeze > requirements.txt
```

## Script Execution (pipx replacement)

```bash
# Run a tool without installing
uvx ruff check .
uvx black .

# Run a specific version
uvx ruff@0.4.0 check .

# Install a tool permanently
uv tool install ruff
uv tool list
```

## pyproject.toml

a standardized format defined in [PEP 621](https://peps.python.org/pep-0621/) for Python project metadata and dependencies. It is more flexible and supports features like dependency groups, version constraints, and build system configuration.

```toml
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "requests>=2.31",
]

[dependency-groups]
dev = [
    "pytest>=8.0",
    "ruff>=0.4",
]

[tool.uv]
dev-dependencies = [
    "pytest>=8.0",
]
```
> **Q.** *pyproject.toml* vs *requirements.txt*  
> **A.** *requirements.txt* is a simpler format commonly used for listing dependencies but lacks the structure and features of *pyproject.toml*. For modern Python projects, using *pyproject.toml* is recommended.

## Lockfile (uv.lock)

- `uv.lock` — lockfile for reproducible builds, commit to git
- `uv sync` — sync environment with lockfile
- `uv lock` — update lockfile only (no environment changes)
- `uv lock --upgrade-package requests` — upgrade a specific package

> **Q.** pyproject.toml vs uv.lock, which one to edit?  
> **A.** Edit `pyproject.toml` for adding or updating dependencies. `uv.lock` is generated automatically and should not be edited manually.

## Workspace (monorepo)

```toml
# root pyproject.toml
[tool.uv.workspace]
members = ["packages/*"]
```

## Command Reference

| Command | Description |
|---------|-------------|
| `uv init` | Initialize a project |
| `uv add <pkg>` | Add a dependency |
| `uv remove <pkg>` | Remove a dependency |
| `uv sync` | Sync the environment |
| `uv run <cmd>` | Run a command in the project environment |
| `uv lock` | Update the lockfile |
| `uv python install` | Install Python |
| `uvx <tool>` | Run a tool temporarily |

## References

- Official docs: https://docs.astral.sh/uv/
- GitHub: https://github.com/astral-sh/uv
