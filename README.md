# CCT Agent MCP Toolkit

FastMCP-based MCP server for travel search functionality with SSE streaming support.

## Features

- ✅ FastMCP-based MCP server
- ✅ Travel search tool (sight, hotel, destination, train ticket)
- ✅ SSE (Server-Sent Events) streaming support
- ✅ Async API integration with retry mechanism
- ✅ Test client included

## Quick Start

### Prerequisites

- Python 3.8+
- uv or pip for package management

### Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Or using uv
uv pip install -r requirements.txt
```

**Important**: If you encounter `KeyError: 'websockets-sansio'`, ensure websockets is installed:

```bash
pip install websockets>=10.0 uvicorn>=0.20.0
```

### Running the Server

```bash
python main.py
```

Server will start on `http://0.0.0.0:8080`

#### Health Check Endpoints

The server provides health check endpoints for monitoring:

**Standard endpoint (JSON):**
```bash
curl http://localhost:8080/health
```

Response:
```json
{
  "status": "healthy",
  "service": "CCT Agent MCP Toolkit",
  "timestamp": "2025-10-27T10:30:00",
  "version": "1.0.0"
}
```

**Legacy endpoint (Plain text):**
```bash
curl http://localhost:8080/vi/health
```

Response: `succ`

See `HEALTH_CHECK.md` for detailed configuration and Kubernetes integration.

### Testing with Client

In another terminal:

```bash
python fastmcp_client.py
```

The client will test:
1. List available tools
2. Sight search: "上海迪士尼攻略"
3. Hotel search: "上海浦东酒店推荐"

## Project Structure

```
cct_agent_mcp_toolkit/
├── main.py                 # FastMCP server entry point
├── fastmcp_client.py       # Test client
├── tools/
│   └── travel_search.py    # Travel search tool implementation
├── utils/
│   ├── intent_type.py      # Intent types
│   └── status_code.py      # Status codes
├── global_context.py       # Global context management
├── requirements.txt        # Python dependencies
└── README.md              # This file
```

## API Usage

### Search Travel

```python
await client.call_tool("search_travel", {
    "query": "上海迪士尼攻略",
    "category": "sight",  # sight, hotel, destination, train ticket
    "ext_info": {}        # Optional extra parameters
})
```

### Response Format

```json
{
  "status_code": 0,
  "result": {
    "pageItems": [
      {
        "title": "Search Results",
        "content": "...",
        "source": "travel_api",
        "extInfo": {...},
        "rerankScore": 1.0
      }
    ]
  },
  "error_msg": ""
}
```

## Configuration

### API Endpoint

Configure in `tools/travel_search.py`:

```python
api_url = "http://debug-wendao-sight-agent-tool.ctripcorp.com/agent_tool"
```

### Timeout Settings

- Total timeout: 60 seconds
- Connect timeout: 5 seconds
- Read timeout: 30 seconds (supports long SSE streams)

### Categories

- `sight` - Sightseeing/attractions
- `hotel` - Hotel search
- `destination` - Destination information
- `train ticket` - Train ticket search

## SSE Support

The tool automatically detects and handles SSE streaming responses:

- Detects `text/event-stream` content type
- Accumulates streaming chunks
- Falls back to regular JSON if not SSE
- Supports `[DONE]` termination marker

## Development

### Running Tests

```bash
# Run the test client
python fastmcp_client.py
```

### Adding New Tools

Register tools in `main.py`:

```python
@mcp.tool()
async def your_tool(param: str) -> Dict[str, Any]:
    """Your tool description"""
    # Implementation
    return result
```

## Status Codes

- `0` - Success
- `-1` - Error (invalid request or server error)

## Error Handling

The server includes:
- Automatic retry (max 2 attempts)
- Detailed error messages
- Exception tracking
- Graceful fallbacks

## Client Usage

The `fastmcp_client.py` handles `CallToolResult` objects correctly:

```python
response = await client.call_tool("search_travel", {...})

# Extract content from CallToolResult
if hasattr(response, 'content'):
    content = response.content
    if isinstance(content, list) and len(content) > 0:
        result_text = content[0].text
        result_data = json.loads(result_text)
```

## Troubleshooting

### Connection Refused
- Ensure server is running: `python main.py`
- Check port 8080 is available: `lsof -i :8080`

### Import Errors
- Install dependencies: `pip install -r requirements.txt`
- Check Python version: `python --version` (need 3.8+)

### API Errors
- Verify API endpoint is accessible
- Check network connectivity
- Review server logs for details

## Dependencies

- `fastmcp>=0.1.0` - FastMCP framework
- `aiohttp>=3.8.0` - Async HTTP client
- `pytest>=7.0.0` - Testing framework (dev)
- `pytest-asyncio>=0.21.0` - Async test support (dev)

## License

Internal use only.

## Support

For issues or questions, please contact the development team.
