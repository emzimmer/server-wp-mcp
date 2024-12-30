# WordPress MCP Server

[![smithery badge](https://smithery.ai/badge/server-wp-mcp)](https://smithery.ai/server/server-wp-mcp)

A [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server that enables AI assistants to interact with WordPress sites through the WordPress REST API. Supports multiple WordPress sites with secure authentication, enabling content management, post operations, and site configuration through natural language.

## Features

- **Multi-Site Support**: Connect to multiple WordPress sites simultaneously
- **REST API Integration**: Full access to WordPress REST API endpoints
- **Secure Authentication**: Uses application passwords for secure API access
- **Dynamic Endpoint Discovery**: Automatically maps available endpoints for each site
- **Flexible Operations**: Support for GET, POST, PUT, DELETE, and PATCH methods
- **Error Handling**: Graceful error handling with meaningful messages
- **Simple Configuration**: Easy-to-maintain JSON configuration file

## Installation

### Installing via Smithery

To install WordPress Server for Claude Desktop automatically via [Smithery](https://smithery.ai/server/server-wp-mcp):

```bash
npx -y @smithery/cli install server-wp-mcp --client claude
```

### Manual Installation
```bash
npm install server-wp-mcp
```

## Tools Reference

### `wp_discover_endpoints`
Maps all available REST API endpoints on a WordPress site.

**Arguments:**
```json
{
	"site": {
		"type": "string",
		"description": "Site alias (as defined in configuration)",
		"required": true
	}
}
```

**Returns:**
List of available endpoints with their methods and namespaces.

### `wp_call_endpoint`
Executes REST API requests to WordPress sites.

**Arguments:**
```json
{
	"site": {
		"type": "string",
		"description": "Site alias",
		"required": true
	},
	"endpoint": {
		"type": "string",
		"description": "API endpoint path",
		"required": true
	},
	"method": {
		"type": "string",
		"enum": ["GET", "POST", "PUT", "DELETE", "PATCH"],
		"description": "HTTP method",
		"default": "GET"
	},
	"params": {
		"type": "object",
		"description": "Request parameters or body data",
		"required": false
	}
}
```

## Configuration

### Getting an Application Password

1. Log in to your WordPress admin dashboard
2. Go to Users → Profile
3. Scroll to the "Application Passwords" section
4. Enter a name for the application (e.g., "MCP Server")
5. Click "Add New Application Password"
6. Copy the generated password (you won't be able to see it again)

Note: Application Passwords require WordPress 5.6 or later and HTTPS.

### Configuration File Setup

Create a JSON configuration file (e.g., `wp-sites.json`) with your WordPress site details:

```json
{
	"myblog": {
		"URL": "https://myblog.com",
		"USER": "yourusername",
		"PASS": "abcd 1234 efgh 5678"
	},
	"testsite": {
		"URL": "https://test.example.com",
		"USER": "anotherusername",
		"PASS": "wxyz 9876 lmno 5432"
	}
}
```

Each site configuration requires:
- `URL`: WordPress site URL (must include http:// or https://)
- `USER`: WordPress username
- `PASS`: Application password (spaces will be automatically removed)

The configuration key (e.g., "myblog", "testsite") becomes the site alias you'll use when interacting with the server.

### Usage with Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
	"mcpServers": {
		"wordpress": {
			"command": "node",
			"args": ["path/to/server/dist/index.js"],
			"env": {
				"WP_SITES_PATH": "/absolute/path/to/wp-sites.json"
			}
		}
	}
}
```

The `WP_SITES_PATH` environment variable must point to the absolute path of your configuration file.

### Example Usage

Once configured, you can ask Claude to perform various WordPress operations:

#### List and Query Posts
```
Can you show me all posts from myblog published in the last month?
```
```
Find all posts on testsite tagged with "technology" and "AI"
```
```
Show me draft posts from myblog that need review
```

#### Create and Edit Content
```
Create a new draft post on testsite titled "The Future of AI" with these key points: [points]
```
```
Update the featured image on myblog's latest post about machine learning
```
```
Add a new category called "Tech News" to myblog
```

#### Manage Comments
```
Show me all pending comments on myblog's latest post
```
```
Find comments from testsite that might be spam
```
```
List the most engaged commenters on myblog
```

#### Plugin Management
```
What plugins are currently active on myblog?
```
```
Check if any plugins on testsite need updates
```
```
Tell me about the security plugins installed on myblog
```

#### User Management
```
Show me all users with editor role on testsite
```
```
Create a new author account on myblog
```
```
Update user roles and permissions on testsite
```

#### Site Settings and Configuration
```
What theme is currently active on myblog?
```
```
Check the permalink structure on testsite
```
```
Show me the current media library settings on myblog
```

#### Maintenance and Diagnostics
```
Check if there are any broken links on myblog
```
```
Show me the PHP version and other system info for testsite
```
```
List any pending database updates on myblog
```

## Error Handling

The server handles common errors including:
- Invalid configuration file path or format
- Invalid site configurations
- Authentication failures
- Missing or invalid endpoints
- API rate limiting
- Network errors

All errors are returned with descriptive messages to help diagnose issues.

## Security Considerations

- Keep your `wp-sites.json` file secure and never commit it to version control
- Consider using environment variables for sensitive data in production
- Store the config file outside of public directories
- Use HTTPS for all WordPress sites
- Regularly rotate application passwords
- Follow the principle of least privilege when assigning user roles

## Dependencies
- @modelcontextprotocol/sdk - MCP protocol implementation
- axios - HTTP client for API requests

## License
MIT
