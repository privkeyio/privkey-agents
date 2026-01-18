# Installation

## Quick Start (Temporary)

```bash
git clone https://github.com/privkeyio/privkey-agents.git
claude --plugin-dir ./privkey-agents
```

## Permanent Installation

Clone the repo:
```bash
git clone https://github.com/privkeyio/privkey-agents.git ~/privkey-agents
```

Add to `~/.claude/settings.json`:
```json
{
  "extraKnownMarketplaces": {
    "privkey-agents": {
      "source": {
        "source": "directory",
        "path": "/home/YOUR_USERNAME/privkey-agents"
      }
    }
  },
  "enabledPlugins": {
    "privkey-agents@privkey-agents": true
  }
}
```

Replace `YOUR_USERNAME` with your actual username.

Restart Claude Code to load the plugin.

## Requirements

- Claude Code installed
- Git repository (for PR agents)

## Updating

Pull the latest changes:
```bash
cd ~/privkey-agents && git pull
```

Then restart Claude Code or run:
```bash
claude plugins uninstall privkey-agents && claude plugins install ~/privkey-agents
```
