# Spike API - MCP Server Integration

## Overview

Spike provides a **Model Context Protocol (MCP)** server that enables AI assistants and LLMs to query health data directly. This allows building AI-powered health coaching, analysis, and reporting systems that can access real-time user health metrics through natural language interactions.

**MCP Server URL:** `https://app-api.spikeapi.com/v3/mcp`

**Authentication:** Bearer JWT token (same token used for standard API calls)

---

## Available Tools

The MCP server exposes 6 tools that AI models can invoke:

### 1. query_statistics_hourly

Query health metrics aggregated by hour.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | string | Yes | Spike user ID |
| `metrics` | array of strings | Yes | Metrics to query (e.g., `["heart_rate_bpm", "steps"]`) |
| `start_date` | string (ISO 8601) | Yes | Start of query range |
| `end_date` | string (ISO 8601) | Yes | End of query range |
| `provider_slug` | string | No | Filter to specific provider |

**Returns:** Hourly aggregated data points for each requested metric.

### 2. query_statistics_daily

Query health metrics aggregated by day.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | string | Yes | Spike user ID |
| `metrics` | array of strings | Yes | Metrics to query |
| `start_date` | string (ISO 8601) | Yes | Start date |
| `end_date` | string (ISO 8601) | Yes | End date |
| `provider_slug` | string | No | Filter to specific provider |

**Returns:** Daily aggregated data points for each requested metric.

### 3. query_sleep

Query detailed sleep data including stages, scores, and timing.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | string | Yes | Spike user ID |
| `start_date` | string (ISO 8601) | Yes | Start date |
| `end_date` | string (ISO 8601) | Yes | End date |
| `provider_slug` | string | No | Filter to specific provider |

**Returns:** Sleep sessions with stages (light, deep, REM, awake), efficiency, latency, and scores.

### 4. query_workout

Query workout/activity data.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | string | Yes | Spike user ID |
| `start_date` | string (ISO 8601) | Yes | Start date |
| `end_date` | string (ISO 8601) | Yes | End date |
| `activity_type` | string | No | Filter by activity type |
| `provider_slug` | string | No | Filter to specific provider |

**Returns:** Workout sessions with duration, calories, heart rate zones, and activity-specific metrics.

### 5. get_user_info

Get basic user profile information.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | string | Yes | Spike user ID |

**Returns:** User profile data including connected providers, account status, and preferences.

### 6. get_user_properties

Get user properties and settings.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `user_id` | string | Yes | Spike user ID |

**Returns:** User-specific configuration, connected provider details, and data availability.

---

## OpenAI Integration

### Using curl

```bash
curl https://api.openai.com/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-4o",
    "messages": [
      {
        "role": "system",
        "content": "You are a health coach assistant. Use the available tools to query user health data and provide personalized insights."
      },
      {
        "role": "user",
        "content": "How did I sleep last week? Any trends I should know about?"
      }
    ],
    "tools": [
      {
        "type": "mcp",
        "server_label": "spike",
        "server_url": "https://app-api.spikeapi.com/v3/mcp",
        "headers": {
          "Authorization": "Bearer YOUR_SPIKE_JWT_TOKEN"
        }
      }
    ]
  }'
```

### Python (OpenAI SDK)

```python
from openai import OpenAI

client = OpenAI()

SPIKE_JWT_TOKEN = "your_spike_jwt_token"
SPIKE_USER_ID = "user_xyz789"

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "system",
            "content": (
                "You are a health and wellness coach. Use the Spike API tools to "
                "query the user's health data and provide actionable insights. "
                f"The user ID is {SPIKE_USER_ID}."
            ),
        },
        {
            "role": "user",
            "content": "Analyze my recovery trends over the past 2 weeks. "
                       "How is my HRV trending and what should I adjust?",
        },
    ],
    tools=[
        {
            "type": "mcp",
            "server_label": "spike",
            "server_url": "https://app-api.spikeapi.com/v3/mcp",
            "headers": {
                "Authorization": f"Bearer {SPIKE_JWT_TOKEN}",
            },
        }
    ],
)

print(response.choices[0].message.content)
```

### Go (OpenAI)

```go
package main

import (
	"context"
	"fmt"
	"log"

	"github.com/openai/openai-go"
	"github.com/openai/openai-go/option"
)

func main() {
	client := openai.NewClient(option.WithAPIKey("your_openai_api_key"))

	spikeJWT := "your_spike_jwt_token"
	userID := "user_xyz789"

	resp, err := client.Chat.Completions.New(
		context.Background(),
		openai.ChatCompletionNewParams{
			Model: "gpt-4o",
			Messages: []openai.ChatCompletionMessageParamUnion{
				openai.SystemMessage(fmt.Sprintf(
					"You are a health coach. Query user data via Spike tools. User ID: %s", userID,
				)),
				openai.UserMessage(
					"What were my top 5 workout days this month by calorie burn?",
				),
			},
			Tools: []openai.ChatCompletionToolParam{
				{
					Type:        "mcp",
					ServerLabel: openai.String("spike"),
					ServerURL:   openai.String("https://app-api.spikeapi.com/v3/mcp"),
					Headers: map[string]string{
						"Authorization": fmt.Sprintf("Bearer %s", spikeJWT),
					},
				},
			},
		},
	)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println(resp.Choices[0].Message.Content)
}
```

### Node.js (OpenAI)

```javascript
import OpenAI from 'openai';

const client = new OpenAI();

const SPIKE_JWT_TOKEN = 'your_spike_jwt_token';
const SPIKE_USER_ID = 'user_xyz789';

async function analyzeHealth(userQuery) {
  const response = await client.chat.completions.create({
    model: 'gpt-4o',
    messages: [
      {
        role: 'system',
        content: `You are a health coach assistant. Use Spike API tools to query health data. User ID: ${SPIKE_USER_ID}.`,
      },
      {
        role: 'user',
        content: userQuery,
      },
    ],
    tools: [
      {
        type: 'mcp',
        server_label: 'spike',
        server_url: 'https://app-api.spikeapi.com/v3/mcp',
        headers: {
          Authorization: `Bearer ${SPIKE_JWT_TOKEN}`,
        },
      },
    ],
  });

  return response.choices[0].message.content;
}

// Usage
const insight = await analyzeHealth(
  'Compare my sleep quality on workout days vs rest days this month.'
);
console.log(insight);
```

---

## Anthropic Integration

### Using curl

```bash
curl https://api.anthropic.com/v1/messages \
  -H "Content-Type: application/json" \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "max_tokens": 4096,
    "system": "You are a health coach. Use the Spike tools to query user health data. User ID: user_xyz789.",
    "messages": [
      {
        "role": "user",
        "content": "How has my resting heart rate changed over the past month?"
      }
    ],
    "mcp_servers": [
      {
        "type": "url",
        "url": "https://app-api.spikeapi.com/v3/mcp",
        "name": "spike",
        "authorization_token": "YOUR_SPIKE_JWT_TOKEN"
      }
    ]
  }'
```

### Python (Anthropic SDK)

```python
import anthropic

client = anthropic.Anthropic()

SPIKE_JWT_TOKEN = "your_spike_jwt_token"
SPIKE_USER_ID = "user_xyz789"

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=4096,
    system=(
        "You are a health and wellness coach. Use the Spike API tools to "
        "query the user's health data and provide detailed, actionable insights. "
        f"The user ID is {SPIKE_USER_ID}."
    ),
    messages=[
        {
            "role": "user",
            "content": "I've been feeling tired lately. Can you look at my sleep, "
                       "HRV, and recovery data from the past 2 weeks and tell me "
                       "what might be going on?",
        }
    ],
    mcp_servers=[
        {
            "type": "url",
            "url": "https://app-api.spikeapi.com/v3/mcp",
            "name": "spike",
            "authorization_token": SPIKE_JWT_TOKEN,
        }
    ],
)

print(response.content[0].text)
```

---

## Claude Desktop Setup

To use Spike health data with Claude Desktop, configure the MCP server using `mcp-proxy`.

### Prerequisites

Install the MCP proxy:

```bash
npm install -g mcp-proxy
# or
pip install mcp-proxy
```

### Configuration

Add to your Claude Desktop config file (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "spike": {
      "command": "mcp-proxy",
      "args": [
        "--transport", "sse",
        "https://app-api.spikeapi.com/v3/mcp"
      ],
      "env": {
        "AUTHORIZATION": "Bearer YOUR_SPIKE_JWT_TOKEN"
      }
    }
  }
}
```

### Config File Locations

| Platform | Path |
|----------|------|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

After adding the configuration, restart Claude Desktop. The Spike tools will appear in the tools panel.

---

## Use Cases

### 1. Health Analysis

Ask the AI to analyze trends, detect patterns, and identify potential issues in health data.

**Example prompts:**
- "Analyze my sleep quality trends over the past month. Are there any concerning patterns?"
- "How does my HRV correlate with my workout intensity?"
- "What's my average resting heart rate trend? Is it improving?"

### 2. Coaching & Recommendations

Use health data to provide personalized coaching recommendations.

**Example prompts:**
- "Based on my recovery score and sleep data, should I do a hard workout today?"
- "Create a training plan for next week based on my current fitness level and recovery trends."
- "I want to improve my deep sleep. What patterns do you see in my data that could help?"

### 3. Correlation Analysis

Discover relationships between different health metrics.

**Example prompts:**
- "Is there a correlation between my step count and sleep quality?"
- "How does my caffeine intake (from Cronometer) affect my sleep latency?"
- "Compare my workout performance on high-recovery vs low-recovery days."

### 4. Reporting

Generate health reports and summaries.

**Example prompts:**
- "Create a weekly health summary for the past week including sleep, activity, and recovery."
- "Generate a monthly fitness progress report."
- "Summarize my health data for a doctor's appointment."

---

## Best Practices

1. **Specify date ranges** - Always provide clear date ranges in your queries to avoid retrieving excessive data.
2. **Use provider filters** - When a user has multiple providers, filter by `provider_slug` to get data from a specific device.
3. **Combine tools** - Use multiple tool calls to correlate data across categories (e.g., sleep + recovery + workout performance).
4. **Handle missing data** - Not all providers report all metrics. Check for null values and note data gaps.
5. **Respect rate limits** - The MCP server follows the same rate limits as the standard API.
6. **Cache user info** - Call `get_user_info` once per session to understand available providers and data, then use that context for subsequent queries.
