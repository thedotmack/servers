# Developer Guide - Build and Testing Instructions

This guide provides comprehensive instructions for building and testing the MCP Servers repository on your local machine.

## Prerequisites

### Required Software

1. **Node.js** (v18 or later)
   - Download from [nodejs.org](https://nodejs.org/)
   - Verify installation: `node --version`

2. **npm** (comes with Node.js)
   - Verify installation: `npm --version`

3. **Python** (v3.10 or later) - Required for Python-based servers
   - Download from [python.org](https://python.org/)
   - Verify installation: `python --version`

4. **uv** (recommended for Python dependency management)
   - Install: `curl -LsSf https://astral.sh/uv/install.sh | sh`
   - Or via pip: `pip install uv`
   - Verify installation: `uv --version`

### Optional Tools

- **Docker** - For containerized testing
- **Git** - For version control (should already be installed)

## Repository Setup

### 1. Clone and Install Dependencies

```bash
# Clone the repository
git clone https://github.com/thedotmack/servers.git
cd servers

# Install all dependencies for all workspaces
npm install
```

### 2. Build All Servers

```bash
# Build all TypeScript servers
npm run build

# Or build individual servers
cd src/sequentialthinking
npm run build
```

## Sequential Thinking Server

The Sequential Thinking server is the focus of the recent changes. Here's how to work with it specifically:

### Build the Sequential Thinking Server

```bash
# Navigate to the server directory
cd src/sequentialthinking

# Install dependencies (if not already done from root)
npm install

# Build the server
npm run build

# Watch for changes during development
npm run watch
```

### Test the Sequential Thinking Server

Currently, the Sequential Thinking server doesn't have automated tests, but you can test it manually:

#### 1. Direct Server Testing

```bash
# Run the server directly
cd src/sequentialthinking
npx @modelcontextprotocol/server-sequential-thinking

# Or run from the built version
node dist/index.js
```

#### 2. Using npx (Global)

```bash
# Test the published version
npx -y @modelcontextprotocol/server-sequential-thinking
```

#### 3. Integration Testing with Claude Desktop

Add this configuration to your Claude Desktop config file:

**Location of config file:**
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
- Windows: `%APPDATA%\Claude\claude_desktop_config.json`

**Configuration:**
```json
{
  "mcpServers": {
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
    }
  }
}
```

#### 4. Testing the Output Formatting Changes

To test the recent user-friendly output formatting changes:

1. Start Claude Desktop with the server configured
2. Ask Claude to use the sequential thinking tool
3. Verify that you see formatted, human-readable output instead of raw JSON

**Expected output format:**
```
💭 Thought 1/3

Let me think about this problem step by step...

📊 Progress: 1/3 thoughts • ⏳ More thinking needed
📝 Total thoughts recorded: 1
```

**Old (incorrect) output:**
```json
{
  "thoughtNumber": 1,
  "totalThoughts": 3,
  "nextThoughtNeeded": true,
  "branches": [],
  "thoughtHistoryLength": 1
}
```

## Testing Other Servers

### TypeScript Servers with Tests (e.g., Filesystem)

```bash
cd src/filesystem

# Run tests
npm test

# Run tests with coverage
npm run test

# Run tests in watch mode
npm run test -- --watch
```

### Python Servers with Tests (e.g., Git)

```bash
cd src/git

# Install dependencies
uv sync

# Run tests
uv run pytest

# Run tests with coverage
uv run pytest --cov

# Run linting
uv run ruff check
uv run pyright
```

## Development Workflow

### 1. Making Changes

```bash
# Create a new branch
git checkout -b feature/your-feature-name

# Make your changes
# ...

# Build and test
npm run build
```

### 2. Testing Changes

```bash
# Test specific server
cd src/sequentialthinking
npm run build

# Test with Claude Desktop (restart Claude after config changes)
# Or test with other MCP clients
```

### 3. Debugging

#### Enable Debug Logging

For the Sequential Thinking server:

```bash
# Disable thought logging
DISABLE_THOUGHT_LOGGING=true npx @modelcontextprotocol/server-sequential-thinking
```

#### View Server Logs

When using with Claude Desktop, check the logs:
- macOS: `~/Library/Logs/Claude/`
- Windows: `%LOCALAPPDATA%\Claude\logs\`

## Docker Testing

### Build and Test with Docker

```bash
# Build the Sequential Thinking server Docker image
docker build -t mcp/sequentialthinking -f src/sequentialthinking/Dockerfile .

# Run the server in Docker
docker run --rm -i mcp/sequentialthinking

# Test with Claude Desktop using Docker
```

Add Docker configuration to Claude Desktop:

```json
{
  "mcpServers": {
    "sequential-thinking": {
      "command": "docker",
      "args": ["run", "--rm", "-i", "mcp/sequentialthinking"]
    }
  }
}
```

## Troubleshooting

### Common Issues

1. **Build Failures**
   ```bash
   # Clear node_modules and reinstall
   rm -rf node_modules package-lock.json
   npm install
   ```

2. **TypeScript Errors**
   ```bash
   # Check TypeScript configuration
   npx tsc --noEmit
   ```

3. **Permission Issues**
   ```bash
   # Fix executable permissions
   chmod +x dist/*.js
   ```

4. **Claude Desktop Not Connecting**
   - Restart Claude Desktop after config changes
   - Check that the server starts without errors
   - Verify JSON syntax in config file

### Testing the Output Formatting Fix

To specifically verify the output formatting changes work correctly:

1. Configure the server in Claude Desktop
2. Ask Claude: "Can you think through this problem step by step: How would I build a simple web application?"
3. Verify you see user-friendly formatted output with emojis and clear progress indicators
4. Should NOT see raw JSON objects in the response

## Additional Resources

- [Model Context Protocol Documentation](https://modelcontextprotocol.io/)
- [Claude Desktop Setup Guide](https://claude.ai/desktop)
- [MCP SDK Documentation](https://github.com/modelcontextprotocol/typescript-sdk)

## Getting Help

If you encounter issues:

1. Check the server logs for error messages
2. Verify all prerequisites are installed correctly
3. Test with minimal configuration first
4. Review the MCP protocol documentation
5. Open an issue on the GitHub repository with detailed error information