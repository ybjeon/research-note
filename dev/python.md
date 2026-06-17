# Python note

## docstring

A string literal placed at the first line of a function, class, or module. Accessible via `__doc__` attribute or `help()`.

### Formats

| Format | Description |
|--------|-------------|
| Google style | Most readable, concise |
| NumPy style | Common in scientific/data fields |
| reStructuredText (reST) | Default format for Sphinx |
| Epytext | Javadoc-style, legacy |

### Google Style (recommended)

```python
def add(a: int, b: int) -> int:
    """Add two numbers.

    Args:
        a: First integer.
        b: Second integer.

    Returns:
        Sum of the two numbers.

    Raises:
        TypeError: If a or b is not an integer.
    """
    return a + b
```

### Key Sections

- `Args` / `Parameters` — parameter descriptions
- `Returns` / `Yields` — return value description
- `Raises` — possible exceptions
- `Example` / `Examples` — usage examples
- `Note` / `Warning` — additional notes

### Tips

- First line should be a one-sentence summary ending with a period.
- No need to repeat types in docstrings if type hints are already defined.
- Tools like `pydoc`, `Sphinx`, and `mkdocs` can auto-generate docs from docstrings.

### LangChain Tool Registration

When using `@tool` decorator in LangChain, the docstring is used as the tool description passed to the LLM. If the docstring is missing, LangChain raises a `ValueError` at registration time.

```python
from langchain_core.tools import tool

@tool
def get_weather(city: str) -> str:
    """Get the current weather for a given city."""  # required — used as tool description
    ...
```

- The first line of the docstring becomes the tool's `description` field.
- The LLM uses this description to decide when and how to call the tool.
- Args section is also parsed to describe each parameter to the LLM.
