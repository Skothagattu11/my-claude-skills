# Spike API Integration Skill

A comprehensive skill for integrating Spike API health data into any application. Covers Flutter SDK, REST API, Nutrition AI, Lab Reports, Webhooks, MCP, and 500+ wearable/IoT device integrations.

---

## Overview

Spike API is a unified health data platform that connects applications to 500+ wearables, IoT devices, EMRs, and lab systems through a single API. It provides standardized, AI-ready health data with HIPAA, GDPR, and CCPA compliance.

**Base URL:** `https://app-api.spikeapi.com/v3`
**API Version:** 3.x (latest: v3.5.41)
**Admin Console:** https://admin.spikeapi.com/
**OpenAPI Spec:** https://docs.spikeapi.com/api-reference/openapi.json
**Docs:** https://docs.spikeapi.com/llms.txt

---

## When to Use

- Integrating wearable/health device data into an app
- Building Flutter apps with Apple Health, Health Connect, or Samsung Health
- Connecting OAuth providers (Garmin, Fitbit, WHOOP, Oura, Polar, etc.)
- Adding food photo analysis (Nutrition AI) to an app
- Processing lab reports with OCR and LOINC mapping
- Building AI health coaching with MCP server integration
- Setting up webhook-driven health data sync pipelines
- Querying health statistics, sleep, workouts, or time series data

---

## Knowledge Base

Detailed documentation is in the `knowledge/` subfolder:

| File | Contents |
|------|----------|
| `knowledge/00-overview.md` | Platform overview, architecture, 24 providers, pricing, compliance |
| `knowledge/01-authentication.md` | HMAC-SHA256 auth flow, JWT tokens, code examples |
| `knowledge/02-api-endpoints.md` | All 25 REST API endpoints with complete schemas |
| `knowledge/03-flutter-sdk.md` | Flutter SDK setup (iOS/Android), usage, background delivery, Nutrition AI |
| `knowledge/04-nutrition-ai.md` | Food image analysis, 29 nutritional fields, sync/async processing |
| `knowledge/05-lab-reports.md` | Lab report upload, OCR pipeline, LOINC mapping, HIPAA |
| `knowledge/06-webhooks.md` | Event types, HMAC verification, retry logic, handler examples |
| `knowledge/07-providers-and-metrics.md` | Provider matrix, 127+ metrics, 190+ activity types |
| `knowledge/08-mcp-server.md` | MCP server for AI agents (OpenAI, Anthropic, Claude) |
| `knowledge/09-integration-guide.md` | Step-by-step Flutter + REST API integration with code |
| `knowledge/10-configuration.md` | Admin console, credentials, backfill, redirect URLs |

**Usage:** Read the relevant knowledge file(s) before implementing. Start with `00-overview.md` for orientation, then the specific topic file.

---

## Quick Reference

### Authentication (3-Step Flow)

1. **Generate HMAC signature** (server-side, NEVER in client code):
```python
import hmac, hashlib

def sign_user(user_id: str, hmac_key: str) -> str:
    return hmac.new(hmac_key.encode(), user_id.encode(), hashlib.sha256).hexdigest()
```

2. **Exchange for JWT token:**
```
POST https://app-api.spikeapi.com/v3/auth/hmac
Body: { "application_id": 9999, "application_user_id": "user-123", "signature": "..." }
Response: { "access_token": "eyJ..." }
```

3. **Use bearer token:** `Authorization: Bearer <access_token>`

For Flutter SDK, pass the signature to `createConnection()` - SDK handles token exchange.

### Key Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/auth/hmac` | POST | Get JWT token |
| `/providers/{slug}/integration/init_url` | GET | OAuth redirect URL |
| `/providers/{slug}/integration` | DELETE | Disconnect provider |
| `/queries/statistics/daily` | GET | Daily aggregated stats |
| `/queries/statistics/interval` | GET | Hourly UTC stats |
| `/queries/sleeps` | GET | Sleep data with stages |
| `/queries/workouts` | GET | Workout data |
| `/queries/timeseries` | GET | Time series (raw data) |
| `/nutrition_records/image` | POST | Analyze food photo |
| `/nutrition_records/ingredients/label` | POST | Analyze nutrition label |
| `/nutrition_records/manual` | POST | Manual nutrition entry |
| `/nutrition_records` | GET | List nutrition records |
| `/nutrition_records/{id}` | GET/PATCH/PUT/DELETE | CRUD nutrition record |
| `/lab_reports` | POST | Upload lab report |
| `/lab_reports` | GET | List lab reports |
| `/lab_reports/{id}` | GET | Get lab report |
| `/userinfo` | GET | User + connected providers |
| `/userproperties` | GET | Height, weight, etc. |

### Supported Providers (19)

**OAuth-based** (server redirect flow):
`garmin`, `fitbit`, `whoop`, `oura`, `polar`, `strava`, `withings`, `suunto`, `huawei`, `coros`, `ultrahuman`, `dexcom`, `freestyle_libre`, `omron_eu`, `omron_us`, `luna`

**SDK-based** (on-device, no OAuth):
`apple` (HealthKit), `health_connect` (Google), `samsung_health_data`

### Flutter SDK Setup

```yaml
# pubspec.yaml
dependencies:
  spike_flutter_sdk: ^4.3.34
```

**iOS:** HealthKit capability + Info.plist permissions
**Android:** Health Connect permissions + FlutterFragmentActivity + Maven repo

```dart
// 1. Connect
final connection = await SpikeSDKV3.createConnection(
    applicationId: 1000,
    signature: signatureFromBackend,  // HMAC generated server-side
    endUserId: "user-123",
);

// 2. Request permissions (MUST call on every app restart)
await connection.requestHealthPermissions(
    statisticTypes: [StatisticsType.steps, StatisticsType.caloriesBurnedTotal],
);

// 3. Query data (max 90 days on Android)
final stats = await connection.getStatistics(
    ofTypes: [StatisticsType.steps],
    from: DateTime.now().subtract(Duration(days: 7)),
    to: DateTime.now(),
    interval: StatisticsInterval.day,
);
```

### Nutrition AI

```dart
// Analyze food photo
final result = await connection.analyzeNutrition(
    imageBase64: base64EncodedImage,
    config: NutritionalAnalysisConfig(
        analysisMode: NutritionalAnalysisMode.precise,
        includeNutriScore: true,
        includeIngredients: true,
    ),
);
print('${result.dishName}: ${result.nutritionalFields?["energyKcal"]} kcal');
```

### Webhooks

3 event types: `record_change`, `provider_integration_created`, `provider_integration_deleted`

Verify with HMAC-SHA256 on `X-Body-Signature` header. Must respond HTTP 200 within 30 seconds. Retries: 10 attempts with exponential backoff.

### MCP Server (AI Integration)

```
URL: https://app-api.spikeapi.com/v3/mcp
Auth: Bearer <JWT_TOKEN>
Tools: query_statistics_hourly, query_statistics_daily, query_sleep,
       query_workout, get_user_info, get_user_properties
```

Works with OpenAI, Anthropic Claude, and Claude Desktop.

---

## Architecture Pattern

```
┌─────────────────────────────────────────────┐
│              Your Application                │
├──────────────┬──────────────────────────────┤
│  Flutter SDK │   Backend (FastAPI/Express)   │
│  - HealthKit │   - HMAC signature gen       │
│  - Health    │   - OAuth provider redirect  │
│    Connect   │   - Webhook receiver         │
│  - Samsung   │   - Data aggregation         │
│  - Nutrition │   - Lab report uploads       │
│    AI        │   - API queries              │
├──────────────┴──────────────────────────────┤
│         Spike API (v3) + MCP Server          │
├──────────────────────────────────────────────┤
│  500+ Providers: Garmin, Fitbit, WHOOP,      │
│  Oura, Apple Health, Health Connect, etc.    │
└──────────────────────────────────────────────┘
```

---

## Critical Rules

1. **HMAC key MUST stay server-side** - Never embed in client/Flutter code
2. **Call `requestHealthPermissions()` on EVERY app restart** - Required by SDK
3. **`enableBackgroundDelivery()` OVERWRITES** previous config - Include ALL types in one call
4. **Android Health Connect max 90-day query range** - Paginate for longer
5. **Webhooks must respond HTTP 200 within 30 seconds** - Process data async
6. **SDK providers (Apple/HC/Samsung) need manual backfill** - Query historical data explicitly
7. **Application user ID must be consistent** - Same ID throughout user lifecycle
8. **Samsung Health requires Samsung Partnership Agreement** for production
9. **iOS background delivery requires device to be unlocked**
10. **Provider backfill is async** - Data may arrive hours after integration

---

## Credentials Needed

| Credential | Source | Purpose |
|-----------|--------|---------|
| Application ID | Admin console | Identifies your app |
| HMAC Key | Admin console | Signs user IDs (min 16 chars) |
| Webhook Signature Key | Admin console | Verifies webhook authenticity |

All from https://admin.spikeapi.com/

---

## Health Metrics (127+ Available)

**Categories:** Heart rate, HRV, blood pressure, SpO2, sleep stages/duration/quality, steps, distance, calories, elevation, speed, body composition, temperature, glucose, ECG, VO2max, breathing rate, and more.

**See:** `knowledge/07-providers-and-metrics.md` for the complete matrix.

---

## Compliance

- HIPAA compliant (lab reports auto-de-identify patient data)
- GDPR compliant
- CCPA compliant
- ISO certified

---

## Examples

### Backend: Get Daily Steps for a User (Python/FastAPI)
```python
import httpx, hmac, hashlib, os
from datetime import datetime, timedelta

BASE_URL = "https://app-api.spikeapi.com/v3"

async def get_daily_steps(user_id: str, days: int = 7):
    # 1. Generate signature
    sig = hmac.new(
        os.environ["SPIKE_HMAC_KEY"].encode(),
        user_id.encode(), hashlib.sha256
    ).hexdigest()

    # 2. Get token
    async with httpx.AsyncClient() as client:
        auth = await client.post(f"{BASE_URL}/auth/hmac", json={
            "application_id": int(os.environ["SPIKE_APP_ID"]),
            "application_user_id": user_id,
            "signature": sig,
        })
        token = auth.json()["access_token"]

        # 3. Query stats
        stats = await client.get(
            f"{BASE_URL}/queries/statistics/daily",
            headers={"Authorization": f"Bearer {token}"},
            params={
                "from_date": (datetime.now() - timedelta(days=days)).strftime("%Y-%m-%d"),
                "to_date": datetime.now().strftime("%Y-%m-%d"),
                "types": ["steps"],
            }
        )
        return stats.json()
```

### Flutter: Full Health Data Flow
```dart
// 1. Get signature from your backend
final sig = await yourBackendApi.getSpikeSignature(userId);

// 2. Connect SDK
final conn = await SpikeSDKV3.createConnection(
    applicationId: appId, signature: sig, endUserId: userId);

// 3. Request permissions
await conn.requestHealthPermissions(
    statisticTypes: [StatisticsType.steps, StatisticsType.caloriesBurnedTotal],
    includeBackgroundDelivery: true);

// 4. Enable background delivery (include ALL types)
await conn.enableBackgroundDelivery(
    statisticTypes: [StatisticsType.steps, StatisticsType.caloriesBurnedTotal]);

// 5. Query data
final stats = await conn.getStatistics(
    ofTypes: [StatisticsType.steps],
    from: DateTime.now().subtract(Duration(days: 7)),
    to: DateTime.now(),
    interval: StatisticsInterval.day);

for (final s in stats) {
  print('${s.start}: ${s.value} ${s.unit}');
}
```

### Webhook Handler (Python/FastAPI)
```python
from fastapi import APIRouter, Request, HTTPException
import hmac, hashlib

router = APIRouter()

@router.post("/webhooks/spike")
async def handle_spike_webhook(request: Request):
    body = await request.body()
    sig = request.headers.get("X-Body-Signature", "")

    expected = hmac.new(
        os.environ["SPIKE_WEBHOOK_KEY"].encode(),
        body, hashlib.sha256
    ).hexdigest()

    if not hmac.compare_digest(expected, sig):
        raise HTTPException(401, "Invalid signature")

    for event in await request.json():
        if event["event_type"] == "record_change":
            # New health data available - fetch it
            pass
        elif event["event_type"] == "provider_integration_created":
            # User connected a new provider
            pass

    return {"status": "ok"}
```
