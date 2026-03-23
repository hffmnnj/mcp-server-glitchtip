# mcp-server-glitchtip

MCP server enabling LLMs to query issues, stacktraces, and resolve errors in GlitchTip.

[GlitchTip](https://glitchtip.com) is an open-source, self-hosted error tracking platform that's API-compatible with Sentry. This MCP server lets AI assistants like Claude directly access your error data to help debug and fix issues faster.

## Features

- **List Issues** - Query all unresolved, resolved, or ignored issues
- **Get Issue Details** - Retrieve full stacktraces and error context
- **Resolve Issues** - Mark issues as resolved after fixing them

## Installation

### Using pip

```bash
pip install mcp-server-glitchtip
```

### From source

```bash
git clone https://github.com/hffmnnj/mcp-server-glitchtip.git
cd mcp-server-glitchtip
pip install -e .
```

## Configuration

### 1. Create a GlitchTip API Token

1. Go to your GlitchTip instance: `https://your-glitchtip.com/profile/auth-tokens`
2. Click **Create New Token**
3. Copy the token

### 2. Find Your Organization and Project Slugs

Your organization slug is in the URL when viewing your organization:
```
https://your-glitchtip.com/organizations/{org-slug}/issues
```

Your project slug is visible in your project settings or URL:
```
https://your-glitchtip.com/organizations/{org-slug}/projects/{project-slug}
```

### 3. Add to Claude Code

```bash
claude mcp add mcp-server-glitchtip \
  -s user \
  -e GLITCHTIP_AUTH_TOKEN=your_token_here \
  -e GLITCHTIP_API_URL=https://your-glitchtip.com/api/0/ \
  -e GLITCHTIP_ORGANIZATION=your-org-slug \
  -e GLITCHTIP_PROJECT=your-project-slug \
  -- mcp-server-glitchtip
```

### 4. Add to Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "glitchtip": {
      "command": "mcp-server-glitchtip",
      "env": {
        "GLITCHTIP_AUTH_TOKEN": "your_token_here",
        "GLITCHTIP_API_URL": "https://your-glitchtip.com/api/0/",
        "GLITCHTIP_ORGANIZATION": "your-org-slug",
        "GLITCHTIP_PROJECT": "your-project-slug"
      }
    }
  }
}
```

## Environment Variables

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `GLITCHTIP_AUTH_TOKEN` | Yes | API token from GlitchTip | `abc123...` |
| `GLITCHTIP_API_URL` | Yes | Base API URL (include trailing slash) | `https://glitchtip.example.com/api/0/` |
| `GLITCHTIP_ORGANIZATION` | Yes | Organization slug | `my-org` |
| `GLITCHTIP_PROJECT` | Yes | Project slug | `my-app` |

## Available Tools

### `get_glitchtip_issues`

List all issues from your GlitchTip project.

**Parameters:**
- `status` (optional): Filter by status - `unresolved`, `resolved`, or `ignored`. Default: `unresolved`

**Example response:**
```
GlitchTip Issues (unresolved):

---
ID: 123 (PROJ-1)
Title: TypeError: Cannot read property 'foo' of undefined
Level: error | Count: 42
Culprit: app.js in handleClick
First: 2024-01-15T10:30:00Z | Last: 2024-01-15T14:22:00Z
```

### `get_glitchtip_issue`

Get detailed information about a specific issue including the full stacktrace.

**Parameters:**
- `issue_id` (required): The numeric issue ID

### `resolve_glitchtip_issue`

Mark an issue as resolved after fixing the underlying bug.

**Parameters:**
- `issue_id` (required): The numeric issue ID to resolve

## Usage Examples

Once configured, you can ask Claude:

- *"Show me all unresolved errors in GlitchTip"*
- *"What's the stacktrace for issue 123?"*
- *"What errors are happening most frequently?"*
- *"I fixed that null pointer bug, mark issue 456 as resolved"*

## Compatibility

This server works with any GlitchTip instance. GlitchTip uses a Sentry-compatible API, so the endpoints follow Sentry's API structure.

Tested with:
- GlitchTip 3.x+
- Python 3.10+

## Development

```bash
# Clone the repo
git clone https://github.com/hffmnnj/mcp-server-glitchtip.git
cd mcp-server-glitchtip

# Create virtual environment
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows

# Install in development mode
pip install -e .

# Run the server locally
GLITCHTIP_AUTH_TOKEN=xxx \
GLITCHTIP_API_URL=https://your-glitchtip.com/api/0/ \
GLITCHTIP_ORGANIZATION=your-org \
GLITCHTIP_PROJECT=your-project \
mcp-server-glitchtip
```

## Related Projects

- [GlitchTip](https://glitchtip.com) - Open source error tracking
- [MCP](https://modelcontextprotocol.io) - Model Context Protocol
- [mcp-sentry](https://github.com/MCP-100/mcp-sentry) - Similar MCP server for Sentry.io

## License

MIT License - see [LICENSE](LICENSE) for details.
