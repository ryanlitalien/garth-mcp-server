# garth-mcp-server

[![PyPI version](
    https://img.shields.io/pypi/v/garth-mcp-server.svg?logo=python&logoColor=brightgreen&color=brightgreen)](
    https://pypi.org/project/garth-mcp-server/)

Garmin Connect MCP server based on [garth](https://github.com/matin/garth). Fork of [matin/garth-mcp-server](https://github.com/matin/garth-mcp-server).

## Setup

### 1. Create the virtual environment

The repo includes a `.python-version` file (3.13.11). If using pyenv:

```bash
pyenv install $(cat .python-version)
```

Then create the venv and install:

```bash
$(pyenv prefix $(cat .python-version))/bin/python3 -m venv .venv
.venv/bin/pip install -e .
```

### 2. Authenticate with Garmin Connect

```bash
.venv/bin/python3 -c "import garth; garth.login(); print(garth.client.dumps())"
```

This will prompt for your Garmin email and password (+ MFA if enabled). Copy the output token.

Alternatively, if you have `uvx` installed:

```bash
uvx garth login
```

### 3. Add the token to `.mcp.json`

In the project root's `.mcp.json`, set the `GARTH_TOKEN` value:

```json
{
  "mcpServers": {
    "garmin": {
      "type": "stdio",
      "command": "./mcp/garmin/.venv/bin/garth-mcp-server",
      "args": [],
      "env": {
        "GARTH_TOKEN": "<paste token here>"
      }
    }
  }
}
```

### 4. Verify

Restart Claude Code. The garmin MCP tools should appear (prefixed with `garmin__` in the tool list).

## Tool Filtering

By default, all 30 tools are exposed. To reduce context size, filter with environment variables.

### Enable specific tools only (whitelist)

```json
"env": {
  "GARTH_TOKEN": "<token>",
  "GARTH_ENABLED_TOOLS": "get_activities,get_activity_details,nightly_sleep,daily_hrv,get_body_composition"
}
```

### Disable specific tools (blacklist)

```json
"env": {
  "GARTH_TOKEN": "<token>",
  "GARTH_DISABLED_TOOLS": "get_gear,get_gear_stats,get_device_settings,get_connectapi_endpoint"
}
```

Tool names are case-insensitive and comma-separated. If `GARTH_ENABLED_TOOLS` is set, `GARTH_DISABLED_TOOLS` is ignored.

## Tools

### Health & Wellness (using Garth data classes)

| Tool | Description |
|------|-------------|
| `user_profile` | Get user profile information |
| `user_settings` | Get user settings and preferences |
| `nightly_sleep` | Get detailed sleep data with optional movement data |
| `daily_sleep` | Get daily sleep summary data |
| `daily_stress` / `weekly_stress` | Get stress data |
| `daily_intensity_minutes` / `weekly_intensity_minutes` | Get intensity minutes |
| `daily_body_battery` | Get body battery data |
| `daily_hydration` | Get hydration data |
| `daily_steps` / `weekly_steps` | Get steps data |
| `daily_hrv` / `hrv_data` | Get heart rate variability data |

### Activities (using Garmin Connect API)

| Tool | Description |
|------|-------------|
| `get_activities` | Get list of activities with optional filters |
| `get_activities_by_date` | Get activities for a specific date |
| `get_activity_details` | Get detailed activity information |
| `get_activity_splits` | Get activity lap/split data |
| `get_activity_weather` | Get weather data for activities |

### Additional Health Data (using Garmin Connect API)

| Tool | Description |
|------|-------------|
| `get_body_composition` | Get body composition data |
| `get_respiration_data` | Get respiration data |
| `get_spo2_data` | Get SpO2 (blood oxygen) data |
| `get_blood_pressure` | Get blood pressure readings |

### Device & Gear (using Garmin Connect API)

| Tool | Description |
|------|-------------|
| `get_devices` | Get connected devices |
| `get_device_settings` | Get device settings |
| `get_gear` | Get gear information |
| `get_gear_stats` | Get gear usage statistics |

### Utility Tools

| Tool | Description |
|------|-------------|
| `monthly_activity_summary` | Get monthly activity overview |
| `snapshot` | Get snapshot data for date ranges |
| `get_connectapi_endpoint` | Direct access to any Garmin Connect API endpoint |
