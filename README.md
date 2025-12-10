# fungicide

IRC/Slack-style log output for NATS JetStream. Watch your NATS messages in a human-readable format.

```
=== Last 10 messages ===
[03:53:43] #coordination | Agent-A: Phase 2 Stream A COMPLETE
[04:33:39] #coordination | Agent-B: Completed Phase 2 Stream B
[17:29:19] #coordination | Agent-Main: Hello! The monitoring is working.

=== Live tail on 'agent-chat.>' (Ctrl+C to exit) ===
[17:33:46] #coordination | Agent-Main: New message just arrived!
```

## Installation

### Via Homebrew (recommended)

```bash
brew tap yourusername/homebrew-tap
brew install fungicide
```

### Manual

```bash
# Clone the repo
git clone https://github.com/yourusername/fungicide.git
cd fungicide

# Make executable and link to PATH
chmod +x fungicide
ln -s $(pwd)/fungicide /usr/local/bin/fungicide
```

### Dependencies

```bash
brew install nats-io/nats-tools/nats jq
```

## Usage

```bash
# Show last 50 messages then live tail (auto-detects streams/subjects)
fungicide

# Show last 20 messages then live tail
fungicide -n 20

# Custom subject pattern
fungicide "myapp.events.>"

# Specific streams
fungicide -s "ORDERS,USERS" -n 10

# No history, just live tail
fungicide -n 0

# Help
fungicide --help
```

## Options

| Option | Description | Default |
|--------|-------------|---------|
| `-n, --history COUNT` | Number of historical messages to show | 50 |
| `-s, --streams LIST` | Comma-separated stream names | auto-detect |
| `-h, --help` | Show help message | |

## Message Format

The tool automatically extracts common fields from JSON messages:

- **handle/user/from**: Who sent the message
- **message/text/content/data**: The message content
- **timestamp/time/ts**: When it was sent

Output format:
```
[HH:MM:SS] #channel | sender: message content
```

## Use Cases

- **Multi-agent coordination**: Watch Claude Code agents communicate in real-time
- **Microservices debugging**: Tail events across services
- **Development**: Monitor message flow during development
- **Demos**: Show stakeholders live system activity

## Requirements

- [NATS CLI](https://github.com/nats-io/natscli) (`brew install nats-io/nats-tools/nats`)
- [jq](https://stedolan.github.io/jq/) (`brew install jq`)
- NATS server with JetStream enabled

## License

MIT
