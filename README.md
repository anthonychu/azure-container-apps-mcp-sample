[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/anthonychu-azure-container-apps-mcp-sample-badge.png)](https://mseep.ai/app/anthonychu-azure-container-apps-mcp-sample)

# Azure Container Apps remote MCP server example

This MCP server uses SSE transport and is authenticated with an API key.

## Running locally

Prerequisites:
* Python 3.11 or later
* [uv](https://docs.astral.sh/uv/getting-started/installation/)

Run the server locally:

```bash
uv venv
uv sync

# linux/macOS
export API_KEYS=<AN_API_KEY>
# windows
set API_KEYS=<AN_API_KEY>

uv run fastapi dev main.py
```

VS Code MCP configuration (mcp.json):

```json
{
    "inputs": [
        {
            "type": "promptString",
            "id": "weather-api-key",
            "description": "Weather API Key",
            "password": true
        }
    ],
    "servers": {
        "weather-sse": {
            "type": "sse",
            "url": "http://localhost:8000/sse",
            "headers": {
                "x-api-key": "${input:weather-api-key}"
            }
        }
    }
}
```

## Deploy to Azure Container Apps

```bash
az containerapp up -g <RESOURCE_GROUP_NAME> -n weather-mcp --environment mcp -l westus --env-vars API_KEYS=<AN_API_KEY> --source .
```

If the deployment is successful, the Azure CLI returns the URL of the app. You can use this URL to connect to the server from Visual Studio Code.

If the deployment fails, try again after updating the CLI and the Azure Container Apps extension:

```bash
az upgrade
az extension add -n containerapp --upgrade
```

