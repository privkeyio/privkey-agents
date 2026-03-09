# Quickstart: Gastown + PrivKey-Agents + Beads

Multi-agent orchestration for Claude Code with persistent work tracking.

## What Each Tool Does

| Tool | Purpose |
|------|---------|
| **Gastown** | Orchestrates multiple agents across projects |
| **PrivKey-Agents** | Defines agent behaviors (code-execution, pr-review, etc.) |
| **Beads** | Git-native issue tracking - shared memory between sessions |

## Installation

```bash
# 1. Beads CLI (issue tracking)
curl -fsSL https://raw.githubusercontent.com/steveyegge/beads/main/scripts/install.sh | bash

# 2. Gastown (orchestration)
curl -fsSL https://raw.githubusercontent.com/steveyegge/gastown/main/scripts/install.sh | bash

# 3. PrivKey-agents plugin (in Claude Code)
/plugin add privkey-agents

# 4. Optional: Beads plugin for Claude Code
/plugin add beads
```

## Initial Setup

### Global gitignore

Copy the example gitignore to keep tool files out of your repos:

```bash
cp .gitignore.example ~/.gitignore
git config --global core.excludesfile ~/.gitignore
```

### Create workspace

```bash
gt install ~/gt --git
```

### Add your projects

```bash
cd ~/gt
gt rig add myproject /path/to/repo
gt rig add another /path/to/another/repo
```

### Create your crew workspace (for hands-on work)

```bash
gt crew add yourname --rig myproject
```

## Daily Use

### Start the Mayor

```bash
cd ~/gt && gt mayor attach
```

The Mayor is your main interface. Tell it what to do:

```
"Review PR #42 on myproject"
"Implement feature X on myproject"
"Run security review on another"
"Stress test the parser module on myproject"
```

### Check status

```bash
gt rig list          # List all projects
gt agents            # Show active workers
gt mail inbox        # Messages from agents
gt ready             # Show work ready to be done
```

### Clean up stale workers

```bash
gt polecat stale myproject --cleanup
```

### Daily hygiene

```bash
bd doctor --fix      # Fix beads issues
bd upgrade           # Upgrade beads CLI
bd cleanup           # Remove old closed issues
```

## Pro Tips

### Don't watch agents work
Give them orders, then work on something else. When they finish, **stop everything and read their output**. Act on it, then move to the next agent.

### Use `gt handoff` liberally
After every task, run `gt handoff` or say "let's hand off" to start a fresh session. Keeps context clean, saves money.

### Crew vs Polecats

| Type | Use For |
|------|---------|
| **Crew** | Design work, complex decisions, PR reviews, planning |
| **Polecats** | Well-defined, well-specified beads epics |

Polecats thrive on work that doesn't require decisions. Crew handles the thinking.

### Code Review Sweeps
Run regular review sweeps followed by fix sweeps:
1. Have agents review code and file beads for issues found
2. Have agents fix all the filed issues
3. Repeat until reviews are clean

### Convoys for Swarm Work
For large features, create convoys:
1. Plan outside beads (use your planning tool)
2. Have agent file detailed beads epics with dependencies
3. Have agent review and refine the epics
4. Kick off convoy to swarm through the work

## How It Works

```
You → Mayor → "Implement feature X"
              ↓
        Creates beads issue
              ↓
        Spawns polecat (ephemeral worker)
              ↓
        Polecat uses privkey-agents behavior
              ↓
        Closes issue when done
              ↓
        You get notified
```

## Key Concepts

| Concept | Description |
|---------|-------------|
| **Mayor** | Singleton coordinator - one per workspace |
| **Rig** | A project added to gastown |
| **Polecat** | Ephemeral worker - spawns, completes task, disappears |
| **Crew** | Persistent workspace for hands-on work |
| **Hook** | Git worktree where polecat work persists |
| **Witness** | Per-rig supervisor monitoring polecat health |

## Agent Behaviors (PrivKey-Agents)

| Agent | Use Case |
|-------|----------|
| `code-execution` | Implement features, fix bugs |
| `pr-review` | Audit PRs for issues |
| `pr-fixes` | Rebase and fix PR issues |
| `security-review` | OWASP security audit |
| `stress-test` | Find crashes, memory issues |
| `refactor` | Simplify code, split large files |

## Common Commands

```bash
# Workspace
gt install ~/gt --git              # Create workspace
gt rig add <name> <path>           # Add project
gt rig remove <name>               # Remove project
gt rig list                        # List projects

# Workers
gt crew add <name> --rig <rig>     # Create crew workspace
gt agents                          # List active agents
gt polecat stale <rig> --cleanup   # Clean dead workers

# Communication
gt mail inbox                      # Check messages
gt mail send <addr> -m "msg"       # Send message

# Mayor
gt mayor attach                    # Start Mayor (interactive)
```

## Best Practices

### Beads
- **One task per session** - Restart agents frequently; beads preserves context
- **File issues liberally** - Anything >2 minutes should be a beads issue
- **Keep database small** - Run `bd cleanup` every few days (~200 issues max)
- **Short prefixes** - Use 2-3 char prefixes (`ke-` not `keep-project-`)
- **Daily hygiene** - Run `bd doctor --fix` and `bd upgrade` regularly

### Gastown
- **Upgrade daily** - Gastown evolves fast: `gt upgrade`
- **Learn tmux** - Essential for managing multiple agents
- **Mayor-only mode** - Good way to start; Mayor can do everything alone
- **Clean sandboxes** - Before big work, reset all crew to clean state

## Troubleshooting

### Polecats piling up

```bash
gt polecat stale <rig> --cleanup
```

### Check what a polecat changed

```bash
git -C ~/gt/<rig>/polecats/<name> diff
```

### Re-add a removed rig

```bash
cd ~/gt && gt rig add <name> /path/to/repo
```

## Links

- [Gastown](https://github.com/steveyegge/gastown)
- [Beads](https://github.com/steveyegge/beads)
- [PrivKey-Agents](https://github.com/privkeyio/privkey-agents)
