# Claude Code Sandbox

This is a minimal sandbox environment for running Claude Code safely.

## What This Does

- Creates an isolated Docker container with Claude Code installed
- Mounts your projects into `/workspace` (read/write)
- Mounts this sandbox configuration as read-only (Claude cannot modify its own setup)
- Persists your Anthropic credentials across container restarts

## Security Model

| Path | Access | Purpose |
|------|--------|---------|
| `/workspace/*` | Read/Write | Your project files |
| `/workspace/sandbox` | Read-Only | This configuration (protected) |
| `/home/vscode/.anthropic` | Persistent | Your API credentials |

Claude Code can modify your project files but **cannot** modify its own container configuration.

## Verifying Safety

Before trusting this sandbox, you can verify:

1. **Check the Dockerfile** - It only installs Node.js and Claude Code
2. **Check docker-compose.yml** - See the volume mounts and their permissions
3. **Inside the container** - Run `touch /workspace/sandbox/test` - it should fail

## Usage

### First Time Setup

```bash
# 1. Create .env file with your user ID (prevents permission issues)
echo "USER_UID=$(id -u)" > .env
echo "USER_GID=$(id -g)" >> .env

# 2. Build and start the container
docker compose up -d

# 3. Enter the sandbox
docker compose exec claude-sandbox bash

# 4. Run Claude Code
claude
```

### Daily Use

```bash
# Start the sandbox
docker compose up -d

# Enter it
docker compose exec claude-sandbox bash

# Stop when done
docker compose down
```

### VS Code Integration

You can also attach VS Code to this container:
1. Install "Dev Containers" extension
2. Click the green button in VS Code's bottom-left corner
3. Select "Attach to Running Container"
4. Choose `claude-sandbox`

## Cleanup

When you're done with this sandbox entirely:

```bash
# Stop and remove containers
docker compose down

# Remove the entire sandbox directory
cd ..
rm -rf claude-sandbox
```

## Customization

- **Add languages**: Edit the Dockerfile to add Java, Python, Go, etc.
- **Add tools**: Add more `apt-get install` or `npm install` commands
- **Change mounts**: Edit docker-compose.yml to add more project directories

## Troubleshooting

### Permission denied on files
Your host UID/GID must match the container. Check your `.env` file:
```bash
cat .env
# Should show your actual UID/GID from `id -u` and `id -g`
```

### Claude not authenticated
Run `claude` and follow the authentication prompts. Credentials are persisted in a Docker volume.

### Container won't start
Check Docker logs:
```bash
docker compose logs claude-sandbox
```
