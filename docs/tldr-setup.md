# TLDR Setup

Code analysis tool for AI agents. Extracts structure from codebases so agents can understand code without reading everything.

## Install

```bash
pip install llm-tldr
```

## Global Gitignore

```bash
echo -e ".tldr/\n.tldrignore" >> ~/.gitignore
git config --global core.excludesfile ~/.gitignore
```

## MCP Integration (optional)

Create `~/.mcp.json`:

```json
{
  "mcpServers": {
    "tldr": {
      "command": "tldr-mcp",
      "args": ["--project", "."]
    }
  }
}
```

## Per-Project Setup

Run once per project:

```bash
cd /path/to/project
tldr warm .
```

This builds the call graph index. Takes ~30 seconds.

## Usage

```bash
tldr structure .                    # See functions/classes
tldr calls .                        # Build call graph
tldr impact funcname .              # Find all callers
tldr context funcname --project .   # LLM-ready summary
```

## Semantic Search (optional)

First run downloads a 1.3GB model (cached globally in `~/.cache/huggingface/`):

```bash
tldr semantic index .
tldr semantic search "what you're looking for"
```

Slow on CPU, fast on GPU. Most features work without it.
