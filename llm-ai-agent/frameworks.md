# Frameworks used in AI Agent

## LLM (Language Model)

## Agent related

### AGENTS.md

### SKILLS.md

### DDD/BDD/SDD


## MCP (Model Context Provider)

### FastMCP (python:`fastmcp`)
- **Repository**: [PrefectHQ/fastmcp](https://github.com/PrefectHQ/fastmcp) | **Docs**: [gofastmcp.com](https://gofastmcp.com/)
- The standard Python framework for building MCP servers and clients

#### Key Features
- **Servers**: Wraps Python functions into MCP-compliant tools, resources, and prompts with auto-generated schema, validation, and documentation
- **Clients**: Connects to any MCP server with full protocol support (transport negotiation, authentication, and lifecycle management handled automatically)
- **Apps**: Provides interactive UIs rendered directly in the LLM conversation

#### Quick Example

```python
# pip install fastmcp
from fastmcp import FastMCP

mcp = FastMCP("Demo")

@mcp.tool
def add(a: int, b: int) -> int:
    """Add two numbers"""
    return a + b

if __name__ == "__main__":
    mcp.run()
```