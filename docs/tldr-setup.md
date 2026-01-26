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

```bash
echo 'alias tldr-start="tldr warm . && tldr semantic index ."' >> ~/.bashrc
```

Run once per project for full features:

```bash
cd /path/to/project
tldr-start
```

First run of `semantic index` downloads a 1.3GB model to `~/.cache/huggingface/` (one-time, shared across all projects).

## Usage

```bash
tldr structure .                           # See functions/classes
tldr calls .                               # Build call graph
tldr impact funcname .                     # Find all callers
tldr context funcname --project .          # LLM-ready summary
tldr semantic search "error handling" .    # Natural language search
```

## Claude Instructions

Add to `~/.claude/CLAUDE.md` to ensure Claude uses TLDR:

```markdown
## Code Exploration

- Prefer TLDR MCP tools (mcp__tldr__*) over Grep/Read when exploring code
- Use mcp__tldr__semantic for finding code by behavior
- Use mcp__tldr__structure for understanding file/function layout
- Use mcp__tldr__context for getting function summaries with call graph
- Fall back to Read only for specific files you already know the path to
```
