# privkey-agents

Surgical workflow agents for Claude Code.

## Installation

```bash
git clone https://github.com/privkeyio/privkey-agents.git
claude --plugin-dir ./privkey-agents
```

Or install permanently - see [Installation Guide](docs/installation.md).

## Agents

| Agent | Description |
|-------|-------------|
| `code-execution` | Execute code changes with verification |
| `pr-review` | Audit PR changes for bugs and security issues |
| `pr-fixes` | Rebase and fix PR issues for merge |
| `refactor` | Simplify code, keep files under 500 lines |
| `stress-test` | Find crashes, memory issues, vulnerabilities |
| `security-review` | OWASP security scan |

## Usage

Just describe what you want - agents are triggered automatically:

```
Implement user authentication
Review this PR for issues
Get this branch ready to merge
Stress test the parser module
```

## Documentation

- [Agent Details](docs/agents.md) - Full documentation for each agent
- [Installation Guide](docs/installation.md) - Permanent installation options
- [CLAUDE.md.example](CLAUDE.md.example) - Global Claude configuration template
