# AWS Cost Explorer MCP

A command-line interface and API for interacting with AWS Cost Explorer data through [Anthropic's MCP (Model Control Protocol)](https://www.anthropic.com/news/model-context-protocol).

**Demo video**

[![AWS Cost Explorer MCP Server Deep Dive](https://img.youtube.com/vi/WuVOmYLRFmI/maxresdefault.jpg)](https://youtu.be/WuVOmYLRFmI)

## Overview

This tool provides a convenient way to analyze and visualize AWS cloud spending data using Anthropic's Claude model as an interactive interface. It functions as an MCP server that exposes AWS Cost Explorer API functionality to Claude, allowing you to ask questions about your AWS costs in natural language.

## Features

- **EC2 Spend Analysis**: View detailed breakdowns of EC2 spending for the last day
- **Service Spend Reports**: Analyze spending across all AWS services for the last 30 days
- **Detailed Cost Breakdown**: Get granular cost data by day, region, service, and instance type
- **Interactive Interface**: Use Claude to query your cost data through natural language

## Requirements

- Python 3.13+
- AWS credentials with Cost Explorer access
- Anthropic API access (for Claude integration)

## Installation

1. Install `uv`:
   ```bash
   # On macOS and Linux
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```
   
   ```powershell
   # On Windows
   powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
   ```

2. Clone this repository:
   ```
   git clone https://github.com/aarora79/aws-cost-explorer-mcp.git
   cd aws-cost-explorer-mcp
   ```

3. Set up the Python virtual environment and install dependencies:
   ```
   uv venv && source .venv/bin/activate && uv pip sync pyproject.toml
   ```
   
4. Configure your AWS credentials:
   ```
   mkdir -p ~/.aws
   # Set up your credentials in ~/.aws/credentials and ~/.aws/config
   ```

## Usage

### Starting the Server

Run the server using:

```
python server.py
```

By default, the server uses stdio transport for communication with MCP clients.

### Claude Desktop Configuration

There are two ways to configure this tool with Claude Desktop:

#### Option 1: Using Docker

Add the following to your Claude Desktop configuration file:

```json
{
  "mcpServers": {
    "aws-cost-explorer": {
      "command": "docker",
      "args": [ "run", "-i", "--rm", "-e", "AWS_ACCESS_KEY_ID", "-e", "AWS_SECRET_ACCESS_KEY", "-e", "AWS_REGION", "aws-cost-explorer-mcp:latest" ],
      "env": {
        "AWS_ACCESS_KEY_ID": "YOUR_ACCESS_KEY_ID",
        "AWS_SECRET_ACCESS_KEY": "YOUR_SECRET_ACCESS_KEY",
        "AWS_REGION": "us-east-1"
      }
    }
  }
}
```

> **IMPORTANT**: Replace `YOUR_ACCESS_KEY_ID` and `YOUR_SECRET_ACCESS_KEY` with your actual AWS credentials. Never commit actual credentials to version control.

#### Option 2: Using UV (without Docker)

If you prefer to run the server directly without Docker, you can use UV:

```json
{
  "mcpServers": {
    "aws_cost_explorer": {
      "command": "uv",
      "args": [
          "--directory",
          "/path/to/aws-cost-explorer-mcp-server",
          "run",
          "server.py"
      ]
    }
  }
}
```

Make sure to replace the directory path with the actual path to your repository on your system.

### Available Tools

The server exposes the following tools that Claude can use:

1. **`get_ec2_spend_last_day()`**: Retrieves EC2 spending data for the previous day
1. **`get_detailed_breakdown_by_day(days=7)`**: Delivers a comprehensive analysis of costs by region, service, and instance type

### Example Queries

Once connected to Claude through an MCP-enabled interface, you can ask questions like:

- "What was my EC2 spend yesterday?"
- "Show me my top 5 AWS services by cost for the last month"
- "Analyze my spending by region for the past 14 days"
- "Which instance types are costing me the most money?"

## Docker Support

A Dockerfile is included for containerized deployment:

```
docker build -t aws-cost-explorer-mcp .
docker run -v ~/.aws:/root/.aws aws-cost-explorer-mcp
```

## Development

### Project Structure

- `server.py`: Main server implementation with MCP tools
- `pyproject.toml`: Project dependencies and metadata
- `Dockerfile`: Container definition for deployments

### Adding New Cost Analysis Tools

To extend the functionality:

1. Add new functions to `server.py`
2. Annotate them with `@mcp.tool()`
3. Implement the AWS Cost Explorer API calls
4. Format the results for easy readability

# Google Search Tool (Extra Credit)

As an additional feature, this project includes a Google Search Tool that allows Claude to perform web searches and fetch webpage content. This is an extra credit assignment.

## Overview

This tool enables querying Google search for relevant URLs and retrieving webpage text content for further analysis. It integrates seamlessly with Claude via MCP.

## Features

- **Google Search Integration**: Fetch top search results for a given query.
- **Webpage Text Extraction**: Retrieve and process webpage content into plain text format.
- **MCP Integration**: Exposed as an MCP tool for Claude to use.

## Implementation

To enable this feature, create a new file named `google_search_server.py`. (*Hint: You can take Lab 5 as a starting point/resource.*) <br>
**Please commit and push this file to the repo as part of the final submission**

### Dependencies

Ensure the following dependencies are installed in your `pyproject.toml`:

```toml
dependencies = [
    "requests",
    "beautifulsoup4",
    "googlesearch-python",
    "mcp[cli]"
]
```

### Configuration

Update your Claude MCP configuration file (`.claude-mcp-config.json`) to include the Google Search Tool:

```json
{
  "mcpServers": {
    "google_search_tool": {
      "command": "uv",
      "args": [
          "--directory",
          "/path/to/aws-cost-explorer-mcp-server",
          "run",
          "google_search_server.py"
      ]
    }
  }
}
```

Replace `/path/to/aws-cost-explorer-mcp-server/` with the actual directory path of your project.

### Example Queries

Once connected to Claude, you can ask:

- "Search google for the best machine learning frameworks in 2025."
- "Search google to find the latest research papers on generative AI."
- "Search google to retrieve content from the first result of a Google search on climate change policies."

## Deliverables

- google_search_server.py
- screenshot of your mcp tool results when used with claude:
  
![Screenshot 2025-03-12 at 10 35 55 PM](https://github.com/user-attachments/assets/44b4f9e1-3dbc-4c22-9784-30b6a1118b38)


## Helpful links

- [For Server Developers](https://modelcontextprotocol.io/quickstart/server#claude-for-desktop-integration-issues)
- [None of this is working, what do I do?](https://modelcontextprotocol.io/quickstart/user#none-of-this-is-working-what-do-i-do)
- [MCP Tools Debugging Guide](https://modelcontextprotocol.io/docs/tools/debugging)


## License

[MIT License](LICENSE)

## Acknowledgments

- This tool uses Anthropic's MCP framework
- Powered by AWS Cost Explorer API
- Built with [FastMCP](https://github.com/jlowin/fastmcp) for server implementation
- README was generated by providing a text dump of the repo via [GitIngest](https://gitingest.com/) to Claude
