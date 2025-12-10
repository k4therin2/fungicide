# fungicide

A CLI tool for monitoring multi-agent communication over NATS JetStream. Built for watching AI agents (like Claude Code) coordinate in real-time with IRC/Slack-style formatting.

```
=== Last 10 messages ===
2025-12-10 09:53:43 AM #coordination Agent-StreamA: Phase 2 Stream A COMPLETE
    ... 8 lines hidden (use -a to expand)
    - tools/vacuum.py - updated for 2 vacuums
    - ha-config/configuration.yaml - removed template
    - plans/REQUIREMENTS.md - marked COMPLETE
2025-12-10 09:33:46 AM #coordination Agent-Main: Testing the new formatter!
2025-12-10 09:33:46 AM #roadmap Agent-Main: Phase 2 roadmap updated

=== Live tail on 'agent-chat.>' (Ctrl+C to exit) ===
2025-12-10 10:15:22 AM #coordination Agent-New: Starting new task...
```

## Why "fungicide"?

It kills the fungus growing in your agent chat logs. Also, it sounds cool.

## Features

- **Colorized output**: Timestamps dimmed, channels colored (consistent per channel), handles bold
- **Timezone conversion**: Converts UTC timestamps to local time (or specify with `-z`)
- **12/24 hour time**: AM/PM by default, use `-m` for military time
- **Multiline collapse**: Long messages show first line + "N lines hidden" + last 3 lines
- **Auto-wrap**: Long single-line messages wrap at terminal width
- **History + live tail**: See recent messages then watch for new ones
- **Auto-detection**: Finds streams and subjects automatically

## Installation

### Manual (recommended for now)

```bash
git clone https://github.com/k4therin2/fungicide.git
cd fungicide
chmod +x fungicide

# Option 1: Symlink to PATH
mkdir -p ~/.local/bin
ln -s $(pwd)/fungicide ~/.local/bin/fungicide
# Add to ~/.zshrc or ~/.bashrc: export PATH="$HOME/.local/bin:$PATH"

# Option 2: Copy to system path
sudo cp fungicide /usr/local/bin/
```

### Dependencies

```bash
brew install nats-io/nats-tools/nats jq python3
```

## Usage

```bash
# Show last 50 messages + live tail (auto-detects streams/subjects)
fungicide

# Show last 20 messages + live tail
fungicide -n 20

# Show all lines (don't collapse long messages)
fungicide -a

# 24-hour military time
fungicide -m

# Specific timezone
fungicide -z America/New_York

# Custom subject pattern
fungicide "myapp.events.>"

# Specific streams
fungicide -s "ORDERS,USERS" -n 10

# No history, just live tail
fungicide -n 0

# Disable line wrapping
fungicide -w 0

# Help
fungicide --help
```

## Options

| Option | Description | Default |
|--------|-------------|---------|
| `-n, --history COUNT` | Number of historical messages to show | 50 |
| `-s, --streams LIST` | Comma-separated stream names | auto-detect |
| `-a, --all` | Show all lines (don't collapse long messages) | false |
| `-m, --military` | Use 24-hour military time | false (12-hour AM/PM) |
| `-z, --timezone TZ` | Timezone for display (e.g., America/New_York) | local |
| `-w, --width WIDTH` | Wrap messages at WIDTH chars (0=no wrap) | terminal width |
| `-h, --help` | Show help message | |

## Message Format

The tool automatically extracts common fields from JSON messages:

- **handle/user/from**: Who sent the message
- **message/text/content/data**: The message content
- **timestamp/time/ts**: When it was sent (converted from UTC)

Output format:
```
YYYY-MM-DD HH:MM:SS AM #channel handle: message content
```

For multiline messages (>5 lines):
```
YYYY-MM-DD HH:MM:SS AM #channel handle: First line of message
    ... 8 lines hidden (use -a to expand)
    Third to last line
    Second to last line
    Last line of message
```

## Primary Use Case: Multi-Agent Coordination

This tool was built specifically for monitoring AI agent communication. When running multiple Claude Code agents in parallel (or any multi-agent system), they can coordinate via NATS channels:

- `#coordination` - Status updates, task handoffs, sync points
- `#roadmap` - Planning discussions, priority changes
- `#errors` - Bug reports, blockers, issues

Example agent chat setup (using [mcp-agent-chat](https://github.com/k4therin2/mcp-agent-chat)):

```bash
# Start NATS with JetStream
nats-server -js

# In one terminal, watch the chatter
fungicide

# Agents post messages via MCP tools:
# post_message(channel="coordination", message="Starting task X")
```

## Other Use Cases

While built for agent coordination, fungicide works for any NATS JetStream messages:

- Microservices event debugging
- IoT sensor monitoring
- Development/testing message flow
- Live demos of distributed systems

## Requirements

- [NATS CLI](https://github.com/nats-io/natscli) (`brew install nats-io/nats-tools/nats`)
- [jq](https://stedolan.github.io/jq/) (`brew install jq`)
- Python 3 (for timezone conversion)
- NATS server with JetStream enabled

## License

MIT
