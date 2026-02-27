# Spike API - Webhooks

## Overview

Spike uses webhooks to deliver real-time notifications when data changes occur. Instead of polling the API, your server receives HTTP POST requests with event payloads whenever new health data arrives, provider integrations change, or async processing (lab reports, nutrition) completes.

Spike supports **three separate webhook URLs** for different event categories:
1. **Main webhook** - Health data changes and provider integration events
2. **Lab reports webhook** - Lab report processing results
3. **Nutrition records webhook** - Nutrition analysis results

---

## Event Types

| Event Type | Webhook URL | Description |
|-----------|-------------|-------------|
| `record_change` | Main | New or updated health data from a connected provider |
| `provider_integration_created` | Main | User connected a new health data provider |
| `provider_integration_deleted` | Main | User disconnected a health data provider |
| `lab_report_completed` | Lab Reports | Lab report processing finished successfully |
| `lab_report_failed` | Lab Reports | Lab report processing failed |
| `nutrition_record_completed` | Nutrition Records | Nutrition analysis finished successfully |
| `nutrition_record_failed` | Nutrition Records | Nutrition analysis failed |

---

## Payload Schema

### record_change

Sent when new health data is available from a connected provider.

```json
{
  "event_type": "record_change",
  "user_id": "user_xyz789",
  "customer_id": "cust_abc123",
  "provider_slug": "whoop",
  "data_types": ["sleep", "recovery", "heart_rate"],
  "timestamp": "2026-02-27T08:30:00Z",
  "start_date": "2026-02-26T22:00:00Z",
  "end_date": "2026-02-27T06:30:00Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `event_type` | string | Always `"record_change"` |
| `user_id` | string | Spike user ID |
| `customer_id` | string | Your application's customer/user ID |
| `provider_slug` | string | Provider identifier (e.g., `whoop`, `oura`, `garmin`, `apple_health`) |
| `data_types` | array of strings | Types of data that changed (e.g., `sleep`, `heart_rate`, `workout`) |
| `timestamp` | string (ISO 8601) | When the event was generated |
| `start_date` | string (ISO 8601) | Start of the data time range |
| `end_date` | string (ISO 8601) | End of the data time range |

### provider_integration_created

Sent when a user connects a new health data provider.

```json
{
  "event_type": "provider_integration_created",
  "user_id": "user_xyz789",
  "customer_id": "cust_abc123",
  "provider_slug": "oura",
  "timestamp": "2026-02-27T10:15:00Z"
}
```

### provider_integration_deleted

Sent when a user disconnects a health data provider.

```json
{
  "event_type": "provider_integration_deleted",
  "user_id": "user_xyz789",
  "customer_id": "cust_abc123",
  "provider_slug": "oura",
  "timestamp": "2026-02-27T14:00:00Z"
}
```

### lab_report_completed / lab_report_failed

See [05-lab-reports.md](./05-lab-reports.md) for detailed payload schemas.

### nutrition_record_completed / nutrition_record_failed

See [04-nutrition-ai.md](./04-nutrition-ai.md) for detailed payload schemas.

---

## HMAC-SHA256 Signature Verification

Every webhook request includes an `X-Body-Signature` header containing an HMAC-SHA256 signature of the request body. You **must** verify this signature to ensure the request originated from Spike and was not tampered with.

### How It Works

1. Spike computes `HMAC-SHA256(webhook_signature_key, request_body)` and includes the hex digest in the `X-Body-Signature` header.
2. Your server computes the same HMAC using your Webhook Signature Key (configured in the admin console).
3. Compare the two values using a constant-time comparison to prevent timing attacks.

**Important:** The HMAC is computed over the raw request body bytes, not a parsed/re-serialized JSON object. Always use the raw body.

---

## Retry Logic

If your server does not respond with an HTTP 200 status code within **30 seconds**, Spike will retry the webhook delivery.

### Retry Schedule

| Attempt | Delay After Previous |
|---------|---------------------|
| 1 | Immediate (first attempt) |
| 2 | 5 seconds |
| 3 | 2 minutes |
| 4 | 30 minutes |
| 5 | 2 hours |
| 6 | 12 hours |
| 7 | 12 hours |
| 8 | 12 hours |
| 9 | 12 hours |
| 10 | 12 hours |

**Total: 10 attempts** with exponential backoff, then every 12 hours for the final attempts.

### Requirements

- Respond with **HTTP 200** to acknowledge receipt
- Respond within **30 seconds**
- Any non-200 response or timeout triggers a retry
- After 10 failed attempts, the webhook is marked as failed

### Best Practices for Reliability

1. **Acknowledge immediately** - Return HTTP 200 as soon as you receive the webhook, then process the data asynchronously.
2. **Idempotent processing** - Webhooks may be delivered more than once. Use the event data to deduplicate.
3. **Queue processing** - Push webhook payloads to a queue (Redis, SQS, etc.) and process them in workers.

---

## Webhook URLs Configuration

Configure webhook URLs in the [Spike Admin Console](https://admin.spikeapi.com/).

| Webhook Type | Purpose | Events |
|--------------|---------|--------|
| **Main Webhook URL** | Health data and provider events | `record_change`, `provider_integration_created`, `provider_integration_deleted` |
| **Lab Reports Webhook URL** | Lab report processing results | `lab_report_completed`, `lab_report_failed` |
| **Nutrition Records Webhook URL** | Nutrition analysis results | `nutrition_record_completed`, `nutrition_record_failed` |

Each webhook URL can be configured independently. You can use the same URL for all three or different URLs for each category.

---

## Webhook Handler Code Examples

### Python (FastAPI)

```python
from fastapi import FastAPI, Request, HTTPException, Response
import hmac
import hashlib
import json

app = FastAPI()

WEBHOOK_SIGNATURE_KEY = "your_webhook_signature_key"


def verify_signature(body: bytes, signature: str) -> bool:
    """Verify HMAC-SHA256 signature using constant-time comparison."""
    expected = hmac.new(
        WEBHOOK_SIGNATURE_KEY.encode("utf-8"),
        body,
        hashlib.sha256,
    ).hexdigest()
    return hmac.compare_digest(expected, signature)


@app.post("/webhooks/spike")
async def handle_spike_webhook(request: Request):
    # 1. Read raw body
    body = await request.body()

    # 2. Verify signature
    signature = request.headers.get("X-Body-Signature", "")
    if not verify_signature(body, signature):
        raise HTTPException(status_code=401, detail="Invalid signature")

    # 3. Parse payload
    payload = json.loads(body)
    event_type = payload.get("event_type")

    # 4. Handle event types
    if event_type == "record_change":
        user_id = payload["user_id"]
        provider = payload["provider_slug"]
        data_types = payload["data_types"]
        print(f"New data from {provider} for user {user_id}: {data_types}")

        # Queue for async processing
        await enqueue_data_fetch(user_id, provider, data_types)

    elif event_type == "provider_integration_created":
        user_id = payload["user_id"]
        provider = payload["provider_slug"]
        print(f"User {user_id} connected {provider}")

        # Trigger initial data backfill
        await trigger_backfill(user_id, provider)

    elif event_type == "provider_integration_deleted":
        user_id = payload["user_id"]
        provider = payload["provider_slug"]
        print(f"User {user_id} disconnected {provider}")

        # Clean up provider data
        await cleanup_provider_data(user_id, provider)

    # 5. Respond immediately with 200
    return Response(status_code=200)


@app.post("/webhooks/spike/lab-reports")
async def handle_lab_report_webhook(request: Request):
    body = await request.body()
    signature = request.headers.get("X-Body-Signature", "")
    if not verify_signature(body, signature):
        raise HTTPException(status_code=401, detail="Invalid signature")

    payload = json.loads(body)

    if payload["event_type"] == "lab_report_completed":
        report_id = payload["report_id"]
        sections = payload["data"]["sections"]
        # Process completed lab report
        await process_lab_report(report_id, sections)

    elif payload["event_type"] == "lab_report_failed":
        report_id = payload["report_id"]
        error = payload["error"]
        # Handle failed processing
        await handle_lab_report_failure(report_id, error)

    return Response(status_code=200)


@app.post("/webhooks/spike/nutrition")
async def handle_nutrition_webhook(request: Request):
    body = await request.body()
    signature = request.headers.get("X-Body-Signature", "")
    if not verify_signature(body, signature):
        raise HTTPException(status_code=401, detail="Invalid signature")

    payload = json.loads(body)

    if payload["event_type"] == "nutrition_record_completed":
        record_id = payload["record_id"]
        totals = payload["data"]["totals"]
        nutri_score = payload["data"]["nutri_score"]
        await save_nutrition_data(record_id, totals, nutri_score)

    return Response(status_code=200)
```

### JavaScript (Express)

```javascript
const express = require('express');
const crypto = require('crypto');

const app = express();

const WEBHOOK_SIGNATURE_KEY = 'your_webhook_signature_key';

// IMPORTANT: Use raw body for signature verification
app.use('/webhooks', express.raw({ type: 'application/json' }));

function verifySignature(body, signature) {
  const expected = crypto
    .createHmac('sha256', WEBHOOK_SIGNATURE_KEY)
    .update(body)
    .digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(expected, 'hex'),
    Buffer.from(signature, 'hex')
  );
}

app.post('/webhooks/spike', (req, res) => {
  // 1. Verify signature
  const signature = req.headers['x-body-signature'];
  if (!signature || !verifySignature(req.body, signature)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }

  // 2. Parse payload
  const payload = JSON.parse(req.body.toString());

  // 3. Respond immediately
  res.status(200).send('OK');

  // 4. Process asynchronously
  switch (payload.event_type) {
    case 'record_change':
      console.log(`New ${payload.data_types.join(', ')} data from ${payload.provider_slug}`);
      processRecordChange(payload).catch(console.error);
      break;

    case 'provider_integration_created':
      console.log(`User ${payload.user_id} connected ${payload.provider_slug}`);
      handleProviderConnected(payload).catch(console.error);
      break;

    case 'provider_integration_deleted':
      console.log(`User ${payload.user_id} disconnected ${payload.provider_slug}`);
      handleProviderDisconnected(payload).catch(console.error);
      break;

    default:
      console.log(`Unknown event type: ${payload.event_type}`);
  }
});

app.listen(3000, () => console.log('Webhook server running on port 3000'));
```

### Go

```go
package main

import (
	"crypto/hmac"
	"crypto/sha256"
	"encoding/hex"
	"encoding/json"
	"fmt"
	"io"
	"net/http"
)

const webhookSignatureKey = "your_webhook_signature_key"

type WebhookPayload struct {
	EventType  string   `json:"event_type"`
	UserID     string   `json:"user_id"`
	CustomerID string   `json:"customer_id"`
	Provider   string   `json:"provider_slug"`
	DataTypes  []string `json:"data_types"`
	Timestamp  string   `json:"timestamp"`
	StartDate  string   `json:"start_date"`
	EndDate    string   `json:"end_date"`
}

func verifySignature(body []byte, signature string) bool {
	mac := hmac.New(sha256.New, []byte(webhookSignatureKey))
	mac.Write(body)
	expected := hex.EncodeToString(mac.Sum(nil))
	return hmac.Equal([]byte(expected), []byte(signature))
}

func webhookHandler(w http.ResponseWriter, r *http.Request) {
	// 1. Read raw body
	body, err := io.ReadAll(r.Body)
	if err != nil {
		http.Error(w, "Failed to read body", http.StatusBadRequest)
		return
	}
	defer r.Body.Close()

	// 2. Verify signature
	signature := r.Header.Get("X-Body-Signature")
	if !verifySignature(body, signature) {
		http.Error(w, "Invalid signature", http.StatusUnauthorized)
		return
	}

	// 3. Respond immediately
	w.WriteHeader(http.StatusOK)

	// 4. Parse and process
	var payload WebhookPayload
	if err := json.Unmarshal(body, &payload); err != nil {
		fmt.Printf("Failed to parse payload: %v\n", err)
		return
	}

	switch payload.EventType {
	case "record_change":
		fmt.Printf("New data from %s for user %s: %v\n",
			payload.Provider, payload.UserID, payload.DataTypes)
		go processRecordChange(payload)

	case "provider_integration_created":
		fmt.Printf("User %s connected %s\n", payload.UserID, payload.Provider)
		go handleProviderConnected(payload)

	case "provider_integration_deleted":
		fmt.Printf("User %s disconnected %s\n", payload.UserID, payload.Provider)
		go handleProviderDisconnected(payload)
	}
}

func main() {
	http.HandleFunc("/webhooks/spike", webhookHandler)
	fmt.Println("Webhook server running on :3000")
	http.ListenAndServe(":3000", nil)
}
```

### TypeScript (Deno / Bun)

```typescript
import { Hono } from 'hono';

const app = new Hono();

const WEBHOOK_SIGNATURE_KEY = 'your_webhook_signature_key';

async function verifySignature(body: ArrayBuffer, signature: string): Promise<boolean> {
  const key = await crypto.subtle.importKey(
    'raw',
    new TextEncoder().encode(WEBHOOK_SIGNATURE_KEY),
    { name: 'HMAC', hash: 'SHA-256' },
    false,
    ['sign'],
  );

  const sig = await crypto.subtle.sign('HMAC', key, body);
  const expected = Array.from(new Uint8Array(sig))
    .map((b) => b.toString(16).padStart(2, '0'))
    .join('');

  return expected === signature;
}

interface WebhookPayload {
  event_type: string;
  user_id: string;
  customer_id: string;
  provider_slug?: string;
  data_types?: string[];
  timestamp: string;
  start_date?: string;
  end_date?: string;
  report_id?: string;
  record_id?: string;
  status?: string;
  data?: Record<string, unknown>;
  error?: Record<string, unknown>;
}

app.post('/webhooks/spike', async (c) => {
  const body = await c.req.arrayBuffer();
  const signature = c.req.header('X-Body-Signature') || '';

  if (!(await verifySignature(body, signature))) {
    return c.json({ error: 'Invalid signature' }, 401);
  }

  const payload: WebhookPayload = JSON.parse(new TextDecoder().decode(body));

  // Respond immediately
  switch (payload.event_type) {
    case 'record_change':
      console.log(`New data from ${payload.provider_slug}: ${payload.data_types?.join(', ')}`);
      // Process async
      break;

    case 'provider_integration_created':
      console.log(`User ${payload.user_id} connected ${payload.provider_slug}`);
      break;

    case 'provider_integration_deleted':
      console.log(`User ${payload.user_id} disconnected ${payload.provider_slug}`);
      break;
  }

  return c.text('OK', 200);
});

export default app;
```

### PHP

```php
<?php

$webhookSignatureKey = 'your_webhook_signature_key';

// 1. Read raw body
$body = file_get_contents('php://input');

// 2. Verify signature
$signature = $_SERVER['HTTP_X_BODY_SIGNATURE'] ?? '';
$expected = hash_hmac('sha256', $body, $webhookSignatureKey);

if (!hash_equals($expected, $signature)) {
    http_response_code(401);
    echo json_encode(['error' => 'Invalid signature']);
    exit;
}

// 3. Parse payload
$payload = json_decode($body, true);

// 4. Respond immediately
http_response_code(200);

// Flush output to release the connection, then process
if (function_exists('fastcgi_finish_request')) {
    fastcgi_finish_request();
}

// 5. Process event
switch ($payload['event_type']) {
    case 'record_change':
        $userId = $payload['user_id'];
        $provider = $payload['provider_slug'];
        $dataTypes = implode(', ', $payload['data_types']);
        error_log("New data from {$provider} for user {$userId}: {$dataTypes}");

        // Queue for processing
        enqueueDataFetch($userId, $provider, $payload['data_types']);
        break;

    case 'provider_integration_created':
        $userId = $payload['user_id'];
        $provider = $payload['provider_slug'];
        error_log("User {$userId} connected {$provider}");

        triggerBackfill($userId, $provider);
        break;

    case 'provider_integration_deleted':
        $userId = $payload['user_id'];
        $provider = $payload['provider_slug'];
        error_log("User {$userId} disconnected {$provider}");

        cleanupProviderData($userId, $provider);
        break;

    default:
        error_log("Unknown event type: {$payload['event_type']}");
}
```

---

## Webhook Configuration via Admin Console

1. Log in to the [Spike Admin Console](https://admin.spikeapi.com/)
2. Navigate to your application settings
3. Configure the following webhook URLs:

| Setting | Description |
|---------|-------------|
| **Webhook URL** | Endpoint for `record_change` and provider integration events |
| **Lab Reports Webhook URL** | Endpoint for lab report processing results |
| **Nutrition Records Webhook URL** | Endpoint for nutrition analysis results |
| **Webhook Signature Key** | Secret key for HMAC-SHA256 signature verification (minimum 16 characters) |

4. Test your webhook by connecting a test provider integration
5. Monitor webhook delivery status in the admin console dashboard

### Testing Webhooks Locally

For local development, use a tunneling service to expose your local server:

```bash
# Using ngrok
ngrok http 3000

# Then configure the webhook URL in admin console:
# https://your-subdomain.ngrok.io/webhooks/spike
```

### Monitoring

The admin console provides:
- Delivery status for recent webhook events
- Retry attempt tracking
- Failed delivery alerts
- Event log with payloads
