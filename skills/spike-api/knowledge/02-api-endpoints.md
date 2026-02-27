# Spike API - Complete Endpoint Reference

> **Base URL**: `https://app-api.spikeapi.com/v3`
> **Auth**: Bearer JWT via `Authorization: Bearer <token>` header (see `01-authentication.md`)
> **Content-Type**: `application/json` for all request/response bodies
> **API Version**: v3

---

## Table of Contents

1. [Error Response Format](#error-response-format)
2. [Authentication](#1-authentication)
3. [Provider Integration](#2-provider-integration)
4. [Health Data Queries](#3-health-data-queries)
   - [Daily Statistics](#31-daily-statistics)
   - [Interval Statistics](#32-interval-statistics)
   - [Time Series](#33-time-series)
   - [Sleep Records](#34-sleep-records)
   - [Workouts](#35-workouts)
   - [Provider Records](#36-provider-records)
5. [Nutrition AI](#4-nutrition-ai)
6. [Lab Reports](#5-lab-reports)
7. [User](#6-user)
8. [Reference Tables](#7-reference-tables)
   - [Provider Slugs](#71-provider-slugs)
   - [Metric Types](#72-metric-types)
   - [Statistics Types](#73-statistics-types)
   - [Sleep Stages](#74-sleep-stages)
   - [Workout/Activity Types](#75-workoutactivity-types)
   - [Merge Strategies](#76-merge-strategies)
   - [Nutritional Fields](#77-nutritional-fields)

---

## Error Response Format

All errors follow **RFC 9457 Problem Details** format:

```json
{
  "title": "Bad Request",
  "status": 400,
  "detail": "Descriptive error message explaining what went wrong",
  "errors": [
    {
      "location": "body.field_name",
      "message": "Specific validation message",
      "value": "the_invalid_value"
    }
  ]
}
```

### HTTP Status Codes

| Code | Meaning | Common Causes |
|------|---------|---------------|
| `400` | Bad Request | Invalid parameters, malformed JSON, missing required fields |
| `401` | Unauthorized | Missing/expired/invalid JWT token |
| `403` | Forbidden | Insufficient permissions, wrong application context |
| `413` | Payload Too Large | File upload exceeds size limit (images, lab reports) |
| `422` | Unprocessable Entity | Valid JSON but semantically invalid (e.g., end_date before start_date) |
| `500` | Internal Server Error | Server-side failure, retry with exponential backoff |

### Error Handling Best Practices

- On `401`: Re-authenticate via `/auth/hmac` and retry
- On `429` (if rate-limited): Respect `Retry-After` header
- On `500`: Retry with exponential backoff (max 3 attempts)
- Always check the `errors` array for field-level validation details

---

## 1. Authentication

### POST /auth/hmac

Exchange an HMAC-SHA256 signature for a JWT access token.

**Description**: This is the entry point for all API access. Generate an HMAC signature server-side using your HMAC key and the user's ID, then exchange it here for a JWT bearer token. See `01-authentication.md` for the full HMAC signing flow.

**Request Body**:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `application_id` | `integer` | Yes | Your application ID from the Spike admin console |
| `application_user_id` | `string` | Yes | Unique user identifier (1-128 chars, alphanumeric + `-`, `_`, `.`) |
| `signature` | `string` | Yes | HMAC-SHA256 hex digest of `application_user_id` signed with your HMAC key |

**Request Example**:
```json
{
  "application_id": 9999,
  "application_user_id": "user-abc-123",
  "signature": "a1b2c3d4e5f6789012345678abcdef0123456789abcdef0123456789abcdef01"
}
```

**Response** (`200 OK`):
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyLWFiYy0xMjMiLCJhcHBfaWQiOjk5OTksImlhdCI6MTcwOTEwMDAwMH0.signature_here"
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Missing or malformed fields |
| `401` | Invalid HMAC signature or application_id |

**Notes**:
- The HMAC key must be kept server-side only. Never include it in client code.
- JWT tokens have a limited lifetime. Re-authenticate when you receive `401` on subsequent calls.
- The `application_user_id` does not need to be pre-registered; the user is created on first authentication.

---

## 2. Provider Integration

### GET /providers/{provider_slug}/integration/init_url

Get the OAuth redirect URL to initiate a provider connection.

**Description**: Returns a URL that the user should be redirected to in order to authorize access to their health data from the specified provider. After the user completes authorization, they are redirected back to your configured redirect URL.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `provider_slug` | `string` | Yes | Provider identifier (see [Provider Slugs](#71-provider-slugs)) |

**Response** (`200 OK`):
```json
{
  "init_url": "https://connect.provider.com/oauth/authorize?client_id=xxx&redirect_uri=xxx&scope=xxx&state=xxx"
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid provider slug |
| `401` | Invalid or missing JWT token |

**Notes**:
- SDK-only providers (`apple`, `health_connect`, `samsung_health_data`) do NOT use this endpoint. They are connected via the Flutter/iOS/Android SDKs.
- Configure your redirect URL in the Spike admin console before using this endpoint.
- The `state` parameter in the URL is managed by Spike for CSRF protection.
- After successful OAuth, the provider appears in the user's `userinfo` response.

**Example Flow**:
```
1. GET /providers/whoop/integration/init_url
2. Redirect user to returned init_url
3. User authorizes on WHOOP's site
4. WHOOP redirects back to your configured URL
5. Spike begins syncing data automatically
6. Receive webhooks as data arrives
```

---

### DELETE /providers/{provider_slug}/integration

Disconnect a provider integration for the authenticated user.

**Description**: Removes the OAuth connection between the user and the specified provider. All future data syncs will stop. Historical data that has already been synced remains available.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `provider_slug` | `string` | Yes | Provider identifier to disconnect |

**Response** (`200 OK`):
```json
{
  "status": "ok"
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid provider slug |
| `401` | Invalid or missing JWT token |
| `404` | No active integration found for this provider |

**Notes**:
- This revokes the OAuth tokens on Spike's side. It does not revoke access on the provider's side (the user should do that in the provider's app if desired).
- Re-integration requires the user to go through the OAuth flow again via `init_url`.

---

## 3. Health Data Queries

All query endpoints require a valid JWT bearer token and return health data for the authenticated user.

### 3.1 Daily Statistics

#### GET /queries/statistics/daily

Retrieve daily aggregated health statistics in the user's local timezone.

**Description**: Returns one data point per day for each requested metric. Values are aggregated across all connected providers for the given date range. Times are aligned to the user's configured timezone (midnight to midnight).

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | `string` (ISO 8601 date) | Yes | Start date inclusive, format `YYYY-MM-DD` |
| `end_date` | `string` (ISO 8601 date) | Yes | End date inclusive, format `YYYY-MM-DD` |
| `types` | `string` (comma-separated) | Yes | Statistics types to retrieve (see [Statistics Types](#73-statistics-types)) |
| `provider_slug` | `string` | No | Filter by specific provider. Omit for cross-provider aggregation |

**Request Example**:
```
GET /queries/statistics/daily?start_date=2026-02-01&end_date=2026-02-07&types=steps,caloriesBurnedTotal,heartRateAvg
```

**Response** (`200 OK`):
```json
{
  "data": [
    {
      "date": "2026-02-01",
      "values": {
        "steps": 8432,
        "caloriesBurnedTotal": 2150.5,
        "heartRateAvg": 68.2
      }
    },
    {
      "date": "2026-02-02",
      "values": {
        "steps": 12045,
        "caloriesBurnedTotal": 2480.1,
        "heartRateAvg": 72.0
      }
    }
  ]
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid date format, end_date before start_date, invalid types |
| `401` | Invalid or missing JWT token |
| `422` | Date range exceeds maximum allowed (typically 90 days) |

**Notes**:
- Dates are in the user's timezone. If no timezone is configured, UTC is used.
- Missing data for a day results in `null` values for that metric, not omission of the day.
- Maximum date range is typically 90 days per request. For longer ranges, paginate.
- If `provider_slug` is omitted, Spike intelligently merges data from all connected providers.

---

### 3.2 Interval Statistics

#### GET /queries/statistics/interval

Retrieve interval-based aggregated statistics in 1-hour UTC intervals.

**Description**: Returns statistics broken down into 1-hour intervals in UTC. Useful for building intraday charts and identifying patterns within a day. Unlike daily statistics which use user timezone, interval statistics always use UTC.

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | `string` (ISO 8601 datetime) | Yes | Start datetime in UTC, format `YYYY-MM-DDTHH:mm:ssZ` |
| `end_date` | `string` (ISO 8601 datetime) | Yes | End datetime in UTC, format `YYYY-MM-DDTHH:mm:ssZ` |
| `types` | `string` (comma-separated) | Yes | Statistics types to retrieve (see [Statistics Types](#73-statistics-types)) |
| `provider_slug` | `string` | No | Filter by specific provider |

**Request Example**:
```
GET /queries/statistics/interval?start_date=2026-02-01T00:00:00Z&end_date=2026-02-01T23:59:59Z&types=steps,heartRateAvg,caloriesBurnedTotal
```

**Response** (`200 OK`):
```json
{
  "data": [
    {
      "start_time": "2026-02-01T00:00:00Z",
      "end_time": "2026-02-01T01:00:00Z",
      "values": {
        "steps": 0,
        "heartRateAvg": 58.3,
        "caloriesBurnedTotal": 65.2
      }
    },
    {
      "start_time": "2026-02-01T01:00:00Z",
      "end_time": "2026-02-01T02:00:00Z",
      "values": {
        "steps": 0,
        "heartRateAvg": 55.1,
        "caloriesBurnedTotal": 62.8
      }
    },
    {
      "start_time": "2026-02-01T08:00:00Z",
      "end_time": "2026-02-01T09:00:00Z",
      "values": {
        "steps": 1250,
        "heartRateAvg": 78.4,
        "caloriesBurnedTotal": 185.0
      }
    }
  ]
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid datetime format, end_date before start_date, invalid types |
| `401` | Invalid or missing JWT token |
| `422` | Date range exceeds maximum allowed |

**Notes**:
- All times are in UTC. Convert to user's local timezone on the client side.
- Intervals are fixed at 1 hour. There is no option to change granularity at this endpoint.
- Hours with no data may be omitted from the response or returned with `null` values.
- This endpoint is ideal for building energy curve visualizations (mapping to HolisticOS energy zones).

---

### 3.3 Time Series

#### GET /queries/timeseries

Retrieve raw time series data points for specific metrics.

**Description**: Returns individual timestamped data points for the requested metric types. Supports merge strategies for combining data from multiple providers. This is the most granular data access endpoint.

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | `string` (ISO 8601 datetime) | Yes | Start datetime, format `YYYY-MM-DDTHH:mm:ssZ` |
| `end_date` | `string` (ISO 8601 datetime) | Yes | End datetime, format `YYYY-MM-DDTHH:mm:ssZ` |
| `types` | `string` (comma-separated) | Yes | Metric types to retrieve (see [Metric Types](#72-metric-types)) |
| `merge_strategy` | `string` | No | How to merge data from multiple providers (see [Merge Strategies](#76-merge-strategies)). Default: `concat` |
| `provider_slug` | `string` | No | Filter by specific provider |

**Request Example**:
```
GET /queries/timeseries?start_date=2026-02-01T06:00:00Z&end_date=2026-02-01T22:00:00Z&types=heartrate,steps&merge_strategy=prefer_newest
```

**Response** (`200 OK`):
```json
{
  "data": {
    "heartrate": [
      {
        "timestamp": "2026-02-01T06:05:00Z",
        "value": 62.0,
        "provider": "whoop",
        "unit": "bpm"
      },
      {
        "timestamp": "2026-02-01T06:10:00Z",
        "value": 64.0,
        "provider": "whoop",
        "unit": "bpm"
      },
      {
        "timestamp": "2026-02-01T08:30:00Z",
        "value": 85.0,
        "provider": "apple",
        "unit": "bpm"
      }
    ],
    "steps": [
      {
        "timestamp": "2026-02-01T07:00:00Z",
        "value": 450,
        "provider": "apple",
        "unit": "count"
      },
      {
        "timestamp": "2026-02-01T08:00:00Z",
        "value": 1200,
        "provider": "apple",
        "unit": "count"
      }
    ]
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid datetime format, invalid types, invalid merge_strategy |
| `401` | Invalid or missing JWT token |
| `422` | Date range too large, too many metric types requested |

**Notes**:
- Time series data can be very large. Keep date ranges narrow (prefer 1-3 days max).
- The `merge_strategy` parameter controls how overlapping data from multiple providers is handled. See [Merge Strategies](#76-merge-strategies).
- Data points are sorted by timestamp ascending.
- The `provider` field on each data point indicates which source device provided it.
- The `unit` field indicates the measurement unit for the value.

---

### 3.4 Sleep Records

#### GET /queries/sleeps

List sleep records for the authenticated user.

**Description**: Returns a paginated list of sleep sessions within the specified date range. Each sleep record contains summary metrics and optionally stage-level breakdown.

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | `string` (ISO 8601 date) | Yes | Start date inclusive, format `YYYY-MM-DD` |
| `end_date` | `string` (ISO 8601 date) | Yes | End date inclusive, format `YYYY-MM-DD` |
| `provider_slug` | `string` | No | Filter by specific provider |
| `limit` | `integer` | No | Max records to return (default: 50, max: 100) |
| `offset` | `integer` | No | Number of records to skip (default: 0) |

**Request Example**:
```
GET /queries/sleeps?start_date=2026-02-01&end_date=2026-02-07&limit=10
```

**Response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "sleep_abc123",
      "provider": "oura",
      "start_time": "2026-02-01T22:30:00Z",
      "end_time": "2026-02-02T06:45:00Z",
      "timezone_offset": "-08:00",
      "duration_seconds": 29700,
      "metrics": {
        "sleep_duration": 29700,
        "sleep_efficiency": 0.92,
        "sleep_latency": 480,
        "awake_duration": 1200,
        "light_duration": 12600,
        "deep_duration": 7200,
        "rem_duration": 8700,
        "respiratory_rate_avg": 14.5,
        "heart_rate_avg": 54.2,
        "heart_rate_min": 48,
        "hrv_avg": 62.5,
        "temperature_deviation": -0.2,
        "sleep_score": 85
      },
      "stages": [
        {
          "stage": "awake",
          "start_time": "2026-02-01T22:30:00Z",
          "end_time": "2026-02-01T22:38:00Z",
          "duration_seconds": 480
        },
        {
          "stage": "light",
          "start_time": "2026-02-01T22:38:00Z",
          "end_time": "2026-02-01T23:15:00Z",
          "duration_seconds": 2220
        },
        {
          "stage": "deep",
          "start_time": "2026-02-01T23:15:00Z",
          "end_time": "2026-02-02T00:05:00Z",
          "duration_seconds": 3000
        },
        {
          "stage": "rem",
          "start_time": "2026-02-02T00:05:00Z",
          "end_time": "2026-02-02T01:00:00Z",
          "duration_seconds": 3300
        }
      ]
    }
  ],
  "pagination": {
    "total": 7,
    "limit": 10,
    "offset": 0,
    "has_more": false
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid date format, invalid provider slug |
| `401` | Invalid or missing JWT token |
| `422` | Date range exceeds maximum |

**Notes**:
- Sleep records span midnight boundaries. A sleep that starts on Feb 1 and ends on Feb 2 appears when querying either date.
- The `stages` array contains the full sleep stage breakdown. See [Sleep Stages](#74-sleep-stages) for all possible stage values.
- Naps are included as separate sleep records with shorter durations.
- Different providers report different subsets of metrics. Missing metrics are `null`.

---

#### GET /queries/sleeps/{sleep_id}

Get a specific sleep record by ID.

**Description**: Returns the full details of a single sleep record including all metrics and stage breakdown.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `sleep_id` | `string` | Yes | The unique identifier of the sleep record |

**Response** (`200 OK`):

Same schema as a single item from the `GET /queries/sleeps` response `data` array.

```json
{
  "data": {
    "id": "sleep_abc123",
    "provider": "oura",
    "start_time": "2026-02-01T22:30:00Z",
    "end_time": "2026-02-02T06:45:00Z",
    "timezone_offset": "-08:00",
    "duration_seconds": 29700,
    "metrics": { ... },
    "stages": [ ... ]
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |
| `404` | Sleep record not found |

---

### 3.5 Workouts

#### GET /queries/workouts

List workout records for the authenticated user.

**Description**: Returns a paginated list of workout/exercise sessions within the specified date range. Includes summary metrics, and optionally GPS data, laps, and heart rate zones.

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | `string` (ISO 8601 date) | Yes | Start date inclusive, format `YYYY-MM-DD` |
| `end_date` | `string` (ISO 8601 date) | Yes | End date inclusive, format `YYYY-MM-DD` |
| `provider_slug` | `string` | No | Filter by specific provider |
| `activity_type` | `string` | No | Filter by activity type (see [Workout/Activity Types](#75-workoutactivity-types)) |
| `limit` | `integer` | No | Max records to return (default: 50, max: 100) |
| `offset` | `integer` | No | Number of records to skip (default: 0) |

**Request Example**:
```
GET /queries/workouts?start_date=2026-02-01&end_date=2026-02-07&activity_type=running
```

**Response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "workout_xyz789",
      "provider": "garmin",
      "activity_type": "running",
      "activity_name": "Morning Run",
      "start_time": "2026-02-03T07:00:00Z",
      "end_time": "2026-02-03T07:45:00Z",
      "timezone_offset": "-08:00",
      "duration_seconds": 2700,
      "metrics": {
        "distance_meters": 5200.0,
        "calories_burned": 420.5,
        "heart_rate_avg": 152,
        "heart_rate_max": 178,
        "heart_rate_min": 110,
        "pace_avg_min_per_km": 5.19,
        "speed_avg_kmh": 11.56,
        "speed_max_kmh": 14.2,
        "cadence_avg": 172,
        "elevation_gain_meters": 45.0,
        "elevation_loss_meters": 42.0,
        "steps": 4800,
        "power_avg_watts": null,
        "vo2max_estimate": 48.5,
        "training_effect_aerobic": 3.2,
        "training_effect_anaerobic": 1.8,
        "respiration_rate_avg": 28.5,
        "stress_avg": null
      },
      "heart_rate_zones": [
        { "zone": 1, "min_bpm": 93, "max_bpm": 112, "duration_seconds": 120 },
        { "zone": 2, "min_bpm": 112, "max_bpm": 131, "duration_seconds": 360 },
        { "zone": 3, "min_bpm": 131, "max_bpm": 150, "duration_seconds": 900 },
        { "zone": 4, "min_bpm": 150, "max_bpm": 168, "duration_seconds": 960 },
        { "zone": 5, "min_bpm": 168, "max_bpm": 187, "duration_seconds": 360 }
      ],
      "laps": [
        {
          "lap_number": 1,
          "start_time": "2026-02-03T07:00:00Z",
          "duration_seconds": 300,
          "distance_meters": 1000,
          "pace_avg_min_per_km": 5.0,
          "heart_rate_avg": 145
        }
      ],
      "gps": {
        "has_gps": true,
        "polyline": "encoded_polyline_string_here"
      },
      "source": {
        "device_name": "Garmin Forerunner 965",
        "device_manufacturer": "Garmin"
      }
    }
  ],
  "pagination": {
    "total": 5,
    "limit": 50,
    "offset": 0,
    "has_more": false
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid date format, invalid activity_type |
| `401` | Invalid or missing JWT token |
| `422` | Date range exceeds maximum |

**Notes**:
- Not all providers report all metrics. Unsupported metrics are `null`.
- GPS data may be returned as an encoded polyline string. Decode it client-side.
- Heart rate zones are provider-defined and may vary between providers.
- The `activity_type` field is normalized across providers. See [Workout/Activity Types](#75-workoutactivity-types).

---

#### GET /queries/workouts/{workout_id}

Get a specific workout record by ID.

**Description**: Returns full details of a single workout including all metrics, laps, GPS, and heart rate zones.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `workout_id` | `string` | Yes | The unique identifier of the workout record |

**Response** (`200 OK`):

Same schema as a single item from the `GET /queries/workouts` response `data` array.

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |
| `404` | Workout record not found |

---

### 3.6 Provider Records

#### GET /queries/provider_records

List raw provider records for debugging and data inspection.

**Description**: Returns the raw, unprocessed records as received from providers. Useful for debugging data discrepancies, verifying provider-specific fields, or accessing data that is not yet mapped to Spike's normalized schema.

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | `string` (ISO 8601 date) | Yes | Start date inclusive, format `YYYY-MM-DD` |
| `end_date` | `string` (ISO 8601 date) | Yes | End date inclusive, format `YYYY-MM-DD` |
| `provider_slug` | `string` | No | Filter by specific provider |
| `record_type` | `string` | No | Filter by record type (e.g., `sleep`, `workout`, `daily`) |
| `limit` | `integer` | No | Max records to return (default: 50, max: 100) |
| `offset` | `integer` | No | Number of records to skip (default: 0) |

**Request Example**:
```
GET /queries/provider_records?start_date=2026-02-01&end_date=2026-02-03&provider_slug=whoop&record_type=sleep
```

**Response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "record_raw_001",
      "provider": "whoop",
      "record_type": "sleep",
      "timestamp": "2026-02-01T22:00:00Z",
      "raw_data": {
        "id": 12345678,
        "user_id": 98765,
        "start": "2026-02-01T22:00:00.000Z",
        "end": "2026-02-02T06:30:00.000Z",
        "score": {
          "stage_summary": {
            "total_in_bed_time_milli": 30600000,
            "total_awake_time_milli": 1800000,
            "total_light_sleep_time_milli": 12000000,
            "total_slow_wave_sleep_time_milli": 7200000,
            "total_rem_sleep_time_milli": 9600000
          },
          "sleep_needed": {
            "baseline_milli": 28800000,
            "need_from_sleep_debt_milli": 3600000
          },
          "respiratory_rate": 14.8,
          "sleep_performance_percentage": 88.0,
          "sleep_consistency_percentage": 92.0,
          "sleep_efficiency_percentage": 94.1
        },
        "nap": false
      }
    }
  ],
  "pagination": {
    "total": 2,
    "limit": 50,
    "offset": 0,
    "has_more": false
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid parameters |
| `401` | Invalid or missing JWT token |

**Notes**:
- The `raw_data` structure varies entirely by provider. There is no guaranteed schema.
- Use this endpoint for debugging only. Prefer normalized endpoints (`sleeps`, `workouts`, `statistics`) for production features.
- Raw records may contain fields not yet mapped by Spike's normalization layer.

---

#### GET /queries/provider_records/{record_id}

Get a specific raw provider record by ID.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `record_id` | `string` | Yes | The unique identifier of the provider record |

**Response** (`200 OK`):

Same schema as a single item from the `GET /queries/provider_records` response `data` array.

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |
| `404` | Record not found |

---

## 4. Nutrition AI

Nutrition endpoints provide AI-powered food recognition, nutrition label scanning, and manual nutrition record management.

### POST /nutrition_records/image

Analyze a food image using AI to extract nutritional information.

**Description**: Upload a photo of food and receive AI-generated nutritional analysis including identified food items, estimated serving sizes, and per-item macronutrient/micronutrient breakdown. Supports all common image formats.

**Request**: `multipart/form-data`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `image` | `file` | Yes | Food image file (JPEG, PNG, WebP). Max size: 10 MB |
| `meal_type` | `string` | No | Meal classification: `breakfast`, `lunch`, `dinner`, `snack`. If omitted, AI will infer |
| `timestamp` | `string` (ISO 8601) | No | When the food was consumed. Defaults to current time |
| `notes` | `string` | No | Additional context for the AI (e.g., "dressing on the side") |

**Request Example** (cURL):
```bash
curl -X POST "https://app-api.spikeapi.com/v3/nutrition_records/image" \
  -H "Authorization: Bearer <token>" \
  -F "image=@lunch_photo.jpg" \
  -F "meal_type=lunch" \
  -F "timestamp=2026-02-01T12:30:00Z"
```

**Response** (`200 OK`):
```json
{
  "data": {
    "id": "nutr_img_001",
    "type": "image",
    "meal_type": "lunch",
    "timestamp": "2026-02-01T12:30:00Z",
    "items": [
      {
        "name": "Grilled Chicken Breast",
        "serving_size": 150,
        "serving_unit": "g",
        "confidence": 0.92,
        "nutrients": {
          "calories": 248,
          "total_fat_g": 5.4,
          "saturated_fat_g": 1.5,
          "trans_fat_g": 0.0,
          "cholesterol_mg": 117,
          "sodium_mg": 104,
          "total_carbohydrates_g": 0.0,
          "dietary_fiber_g": 0.0,
          "total_sugars_g": 0.0,
          "added_sugars_g": 0.0,
          "protein_g": 46.5,
          "vitamin_a_mcg": 9.0,
          "vitamin_c_mg": 0.0,
          "vitamin_d_mcg": 0.2,
          "calcium_mg": 16.5,
          "iron_mg": 1.35,
          "potassium_mg": 400,
          "vitamin_b6_mg": 0.8,
          "vitamin_b12_mcg": 0.5,
          "magnesium_mg": 37.5,
          "zinc_mg": 1.5,
          "phosphorus_mg": 300,
          "thiamin_mg": 0.1,
          "riboflavin_mg": 0.2,
          "niacin_mg": 14.5,
          "folate_mcg": 6.0,
          "selenium_mcg": 35.0,
          "copper_mg": 0.06,
          "manganese_mg": 0.03
        }
      },
      {
        "name": "Brown Rice",
        "serving_size": 200,
        "serving_unit": "g",
        "confidence": 0.88,
        "nutrients": {
          "calories": 232,
          "total_fat_g": 1.8,
          "saturated_fat_g": 0.4,
          "trans_fat_g": 0.0,
          "cholesterol_mg": 0,
          "sodium_mg": 2,
          "total_carbohydrates_g": 48.5,
          "dietary_fiber_g": 3.2,
          "total_sugars_g": 0.7,
          "added_sugars_g": 0.0,
          "protein_g": 4.9,
          "vitamin_a_mcg": 0.0,
          "vitamin_c_mg": 0.0,
          "vitamin_d_mcg": 0.0,
          "calcium_mg": 20,
          "iron_mg": 1.0,
          "potassium_mg": 154,
          "vitamin_b6_mg": 0.3,
          "vitamin_b12_mcg": 0.0,
          "magnesium_mg": 86,
          "zinc_mg": 1.2,
          "phosphorus_mg": 208,
          "thiamin_mg": 0.2,
          "riboflavin_mg": 0.05,
          "niacin_mg": 2.6,
          "folate_mcg": 18,
          "selenium_mcg": 19.0,
          "copper_mg": 0.2,
          "manganese_mg": 1.8
        }
      }
    ],
    "totals": {
      "calories": 480,
      "total_fat_g": 7.2,
      "protein_g": 51.4,
      "total_carbohydrates_g": 48.5
    }
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid image format, missing image |
| `401` | Invalid or missing JWT token |
| `413` | Image file exceeds 10 MB limit |
| `422` | Image could not be processed (blurry, no food detected) |

**Notes**:
- The `confidence` score (0-1) indicates how certain the AI is about each food identification.
- The `totals` object provides a quick summary across all identified items.
- For best results, take photos in good lighting with food clearly visible.
- The AI can identify multiple food items in a single image.
- See [Nutritional Fields](#77-nutritional-fields) for the complete list of 29 nutrient fields.

---

### POST /nutrition_records/ingredients/label

Analyze a nutrition label image.

**Description**: Upload a photo of a nutrition facts label (FDA/EU format) and receive structured nutritional data extracted via OCR. More accurate than food image analysis for packaged foods.

**Request**: `multipart/form-data`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `image` | `file` | Yes | Photo of nutrition label (JPEG, PNG, WebP). Max size: 10 MB |
| `serving_count` | `number` | No | Number of servings consumed. Default: 1 |
| `meal_type` | `string` | No | `breakfast`, `lunch`, `dinner`, `snack` |
| `timestamp` | `string` (ISO 8601) | No | When consumed. Defaults to current time |

**Request Example** (cURL):
```bash
curl -X POST "https://app-api.spikeapi.com/v3/nutrition_records/ingredients/label" \
  -H "Authorization: Bearer <token>" \
  -F "image=@label_photo.jpg" \
  -F "serving_count=2" \
  -F "meal_type=snack"
```

**Response** (`200 OK`):
```json
{
  "data": {
    "id": "nutr_label_001",
    "type": "label",
    "meal_type": "snack",
    "timestamp": "2026-02-01T15:00:00Z",
    "product_name": "Organic Protein Bar",
    "serving_size": 40,
    "serving_unit": "g",
    "serving_count": 2,
    "nutrients_per_serving": {
      "calories": 180,
      "total_fat_g": 7.0,
      "saturated_fat_g": 2.5,
      "trans_fat_g": 0.0,
      "cholesterol_mg": 5,
      "sodium_mg": 150,
      "total_carbohydrates_g": 22,
      "dietary_fiber_g": 3,
      "total_sugars_g": 8,
      "added_sugars_g": 5,
      "protein_g": 10,
      "vitamin_d_mcg": 0,
      "calcium_mg": 40,
      "iron_mg": 2,
      "potassium_mg": 120
    },
    "nutrients_total": {
      "calories": 360,
      "total_fat_g": 14.0,
      "saturated_fat_g": 5.0,
      "trans_fat_g": 0.0,
      "cholesterol_mg": 10,
      "sodium_mg": 300,
      "total_carbohydrates_g": 44,
      "dietary_fiber_g": 6,
      "total_sugars_g": 16,
      "added_sugars_g": 10,
      "protein_g": 20
    }
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid image format, missing image |
| `401` | Invalid or missing JWT token |
| `413` | Image file exceeds size limit |
| `422` | Label could not be read (blurry, non-standard format) |

**Notes**:
- `nutrients_per_serving` contains values as printed on the label.
- `nutrients_total` is `nutrients_per_serving * serving_count`.
- OCR works best with clear, flat photos of standard FDA or EU nutrition labels.

---

### POST /nutrition_records/manual

Upload a manual nutrition record.

**Description**: Create a nutrition record by manually specifying food items and their nutritional values. Useful when AI image analysis is not applicable or when importing data from other systems.

**Request Body**:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `meal_type` | `string` | Yes | `breakfast`, `lunch`, `dinner`, `snack` |
| `timestamp` | `string` (ISO 8601) | Yes | When consumed |
| `items` | `array` | Yes | Array of food items |
| `items[].name` | `string` | Yes | Food item name |
| `items[].serving_size` | `number` | Yes | Serving size amount |
| `items[].serving_unit` | `string` | Yes | Unit of measurement (`g`, `ml`, `oz`, `cup`, `tbsp`, `tsp`, `piece`) |
| `items[].nutrients` | `object` | Yes | Nutritional values (see [Nutritional Fields](#77-nutritional-fields)) |
| `notes` | `string` | No | Additional notes |

**Request Example**:
```json
{
  "meal_type": "breakfast",
  "timestamp": "2026-02-01T07:30:00Z",
  "items": [
    {
      "name": "Oatmeal with Berries",
      "serving_size": 300,
      "serving_unit": "g",
      "nutrients": {
        "calories": 350,
        "total_fat_g": 8.0,
        "protein_g": 12.0,
        "total_carbohydrates_g": 58.0,
        "dietary_fiber_g": 8.0,
        "total_sugars_g": 15.0
      }
    }
  ],
  "notes": "Steel-cut oats with blueberries and almond butter"
}
```

**Response** (`201 Created`):
```json
{
  "data": {
    "id": "nutr_manual_001",
    "type": "manual",
    "meal_type": "breakfast",
    "timestamp": "2026-02-01T07:30:00Z",
    "items": [ ... ],
    "notes": "Steel-cut oats with blueberries and almond butter"
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Missing required fields, invalid nutrient values |
| `401` | Invalid or missing JWT token |
| `422` | Semantically invalid data (negative calories, etc.) |

---

### GET /nutrition_records

List nutrition records by time range.

**Description**: Returns all nutrition records (image, label, and manual) within the specified time range, sorted by timestamp descending.

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | `string` (ISO 8601 datetime) | Yes | Start datetime, format `YYYY-MM-DDTHH:mm:ssZ` |
| `end_date` | `string` (ISO 8601 datetime) | Yes | End datetime, format `YYYY-MM-DDTHH:mm:ssZ` |
| `meal_type` | `string` | No | Filter by meal type: `breakfast`, `lunch`, `dinner`, `snack` |
| `type` | `string` | No | Filter by record type: `image`, `label`, `manual` |
| `limit` | `integer` | No | Max records (default: 50, max: 100) |
| `offset` | `integer` | No | Offset for pagination (default: 0) |

**Request Example**:
```
GET /nutrition_records?start_date=2026-02-01T00:00:00Z&end_date=2026-02-01T23:59:59Z&meal_type=lunch
```

**Response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "nutr_img_001",
      "type": "image",
      "meal_type": "lunch",
      "timestamp": "2026-02-01T12:30:00Z",
      "items": [ ... ],
      "totals": {
        "calories": 480,
        "total_fat_g": 7.2,
        "protein_g": 51.4,
        "total_carbohydrates_g": 48.5
      }
    }
  ],
  "pagination": {
    "total": 1,
    "limit": 50,
    "offset": 0,
    "has_more": false
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid date format, invalid filter values |
| `401` | Invalid or missing JWT token |

---

### GET /nutrition_records/{id}

Get a specific nutrition record by ID.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | `string` | Yes | Nutrition record identifier |

**Response** (`200 OK`):

Full nutrition record object (same schema as items in `GET /nutrition_records` response).

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |
| `404` | Record not found |

---

### PATCH /nutrition_records/{id}

Modify the serving size of a nutrition record.

**Description**: Adjusts the serving size/count for an existing nutrition record. Nutritional values are automatically recalculated proportionally.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | `string` | Yes | Nutrition record identifier |

**Request Body**:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `serving_count` | `number` | No | New number of servings |
| `items` | `array` | No | Updated items with modified serving sizes |
| `items[].name` | `string` | Yes (if items provided) | Food item name to modify |
| `items[].serving_size` | `number` | Yes (if items provided) | New serving size |

**Request Example**:
```json
{
  "items": [
    {
      "name": "Grilled Chicken Breast",
      "serving_size": 200
    }
  ]
}
```

**Response** (`200 OK`):

Updated nutrition record with recalculated nutritional values.

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid fields |
| `401` | Invalid or missing JWT token |
| `404` | Record not found |
| `422` | Invalid serving values |

---

### PUT /nutrition_records/{id}

Replace a nutrition record entirely.

**Description**: Completely replaces an existing nutrition record with new data. All fields must be provided.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | `string` | Yes | Nutrition record identifier |

**Request Body**: Same schema as `POST /nutrition_records/manual` request body.

**Response** (`200 OK`):

Replaced nutrition record.

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Missing required fields |
| `401` | Invalid or missing JWT token |
| `404` | Record not found |
| `422` | Invalid data |

---

### DELETE /nutrition_records/{id}

Delete a nutrition record.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | `string` | Yes | Nutrition record identifier |

**Response** (`200 OK`):
```json
{
  "status": "ok"
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |
| `404` | Record not found |

---

## 5. Lab Reports

### POST /lab_reports

Upload a lab report for AI processing.

**Description**: Upload a medical lab report (blood work, urinalysis, etc.) as a PDF or image. Spike's AI extracts biomarker values, maps them to LOINC codes, and provides reference range context.

**Request**: `multipart/form-data`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `file` | `file` | Yes | Lab report file (PDF, JPEG, PNG). Max size: 20 MB |
| `report_date` | `string` (ISO 8601 date) | No | Date the lab work was performed. AI will attempt to extract if omitted |
| `lab_name` | `string` | No | Name of the lab/facility |
| `notes` | `string` | No | Additional context |

**Request Example** (cURL):
```bash
curl -X POST "https://app-api.spikeapi.com/v3/lab_reports" \
  -H "Authorization: Bearer <token>" \
  -F "file=@bloodwork_results.pdf" \
  -F "report_date=2026-01-15" \
  -F "lab_name=Quest Diagnostics"
```

**Response** (`200 OK`):
```json
{
  "data": {
    "id": "lab_001",
    "status": "processed",
    "report_date": "2026-01-15",
    "lab_name": "Quest Diagnostics",
    "uploaded_at": "2026-02-01T10:00:00Z",
    "biomarkers": [
      {
        "name": "Hemoglobin A1c",
        "loinc_code": "4548-4",
        "value": 5.4,
        "unit": "%",
        "reference_range": {
          "low": 4.0,
          "high": 5.6
        },
        "status": "normal"
      },
      {
        "name": "Total Cholesterol",
        "loinc_code": "2093-3",
        "value": 215,
        "unit": "mg/dL",
        "reference_range": {
          "low": 0,
          "high": 200
        },
        "status": "high"
      },
      {
        "name": "Vitamin D, 25-OH",
        "loinc_code": "1989-3",
        "value": 32,
        "unit": "ng/mL",
        "reference_range": {
          "low": 30,
          "high": 100
        },
        "status": "normal"
      }
    ],
    "summary": {
      "total_biomarkers": 3,
      "normal": 2,
      "high": 1,
      "low": 0,
      "critical": 0
    }
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `400` | Invalid file format, missing file |
| `401` | Invalid or missing JWT token |
| `413` | File exceeds 20 MB limit |
| `422` | File could not be processed (corrupted, unreadable, not a lab report) |

**Notes**:
- Processing may take 10-30 seconds for complex reports. The response is synchronous.
- LOINC codes are internationally standardized lab test identifiers.
- The `status` field is one of: `normal`, `low`, `high`, `critical`.
- Lab reports are HIPAA-compliant and encrypted at rest.

---

### GET /lab_reports

List uploaded lab reports.

**Query Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | `integer` | No | Max records (default: 50, max: 100) |
| `offset` | `integer` | No | Pagination offset (default: 0) |

**Response** (`200 OK`):
```json
{
  "data": [
    {
      "id": "lab_001",
      "status": "processed",
      "report_date": "2026-01-15",
      "lab_name": "Quest Diagnostics",
      "uploaded_at": "2026-02-01T10:00:00Z",
      "summary": {
        "total_biomarkers": 15,
        "normal": 12,
        "high": 2,
        "low": 1,
        "critical": 0
      }
    }
  ],
  "pagination": {
    "total": 3,
    "limit": 50,
    "offset": 0,
    "has_more": false
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |

---

### GET /lab_reports/{id}

Get a specific lab report with full biomarker details.

**Path Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `id` | `string` | Yes | Lab report identifier |

**Response** (`200 OK`):

Full lab report object including all biomarkers (same schema as `POST /lab_reports` response).

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |
| `404` | Lab report not found |

---

## 6. User

### GET /userinfo

Get user information including connected providers.

**Description**: Returns the authenticated user's profile information and a list of all currently connected health data providers.

**Query Parameters**: None.

**Response** (`200 OK`):
```json
{
  "data": {
    "user_id": "user-abc-123",
    "application_id": 9999,
    "created_at": "2026-01-01T00:00:00Z",
    "providers": [
      {
        "slug": "whoop",
        "name": "WHOOP",
        "connected_at": "2026-01-15T10:00:00Z",
        "status": "active",
        "last_sync": "2026-02-01T08:00:00Z"
      },
      {
        "slug": "oura",
        "name": "Oura",
        "connected_at": "2026-01-20T14:00:00Z",
        "status": "active",
        "last_sync": "2026-02-01T07:30:00Z"
      },
      {
        "slug": "apple",
        "name": "Apple Health",
        "connected_at": "2026-01-10T08:00:00Z",
        "status": "active",
        "last_sync": "2026-02-01T09:00:00Z"
      }
    ]
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |

**Notes**:
- The `status` field can be `active`, `expired` (OAuth token expired, needs re-auth), or `error`.
- SDK-based providers (`apple`, `health_connect`, `samsung_health_data`) appear here once the SDK has successfully extracted data.

---

### GET /userproperties

Get user physical properties.

**Description**: Returns the user's physical properties as reported by connected providers (height, weight, date of birth, biological sex, etc.). These properties are extracted from provider profiles and health records.

**Query Parameters**: None.

**Response** (`200 OK`):
```json
{
  "data": {
    "height_cm": 178.0,
    "weight_kg": 75.5,
    "date_of_birth": "1990-05-15",
    "biological_sex": "male",
    "blood_type": "O+",
    "skin_type": null,
    "wheelchair_use": false,
    "max_heart_rate": 186,
    "resting_heart_rate": 58,
    "vo2_max": 48.5,
    "sources": {
      "height_cm": "apple",
      "weight_kg": "withings",
      "date_of_birth": "garmin",
      "biological_sex": "apple",
      "blood_type": "apple",
      "max_heart_rate": "garmin",
      "resting_heart_rate": "whoop",
      "vo2_max": "garmin"
    }
  }
}
```

**Error Responses**:

| Status | Cause |
|--------|-------|
| `401` | Invalid or missing JWT token |

**Notes**:
- Properties are aggregated from all connected providers. The `sources` object indicates which provider supplied each value.
- Not all properties are available from all providers. Unavailable properties are `null`.
- Weight and height may update over time as new measurements are synced.

---

## 7. Reference Tables

### 7.1 Provider Slugs

Complete list of provider identifiers used in API calls:

| Slug | Provider | Integration Type | Primary Data |
|------|----------|-----------------|--------------|
| `strava` | Strava | OAuth | Workouts, activities |
| `omron_eu` | Omron (Europe) | OAuth | Blood pressure, body composition |
| `omron_us` | Omron (US) | OAuth | Blood pressure, body composition |
| `health_connect` | Google Health Connect | SDK | Comprehensive health data |
| `fitbit` | Fitbit | OAuth | Activity, sleep, heart rate |
| `garmin` | Garmin | OAuth | Workouts, daily metrics, sleep |
| `huawei` | Huawei Health | OAuth | Activity, sleep, heart rate |
| `dexcom` | Dexcom | OAuth | Continuous glucose monitoring |
| `whoop` | WHOOP | OAuth | Recovery, strain, sleep |
| `withings` | Withings | OAuth | Weight, blood pressure, sleep |
| `suunto` | Suunto | OAuth | Workouts, GPS activities |
| `apple` | Apple HealthKit | SDK | Comprehensive health data (iOS) |
| `freestyle_libre` | Freestyle Libre | OAuth | Continuous glucose monitoring |
| `oura` | Oura Ring | OAuth | Sleep, readiness, activity |
| `polar` | Polar | OAuth | Heart rate, workouts, sleep |
| `coros` | COROS | OAuth | Workouts, GPS, training load |
| `ultrahuman` | Ultrahuman | OAuth | Glucose, metabolic health |
| `samsung_health_data` | Samsung Health Data | SDK | Comprehensive health data (Android) |
| `luna` | Luna | OAuth | Sleep tracking |

---

### 7.2 Metric Types

Used with the `GET /queries/timeseries` endpoint in the `types` parameter. These represent individual time series data streams.

#### Heart & Cardiovascular
| Metric | Unit | Description |
|--------|------|-------------|
| `heartrate` | bpm | Heart rate |
| `heartrate_resting` | bpm | Resting heart rate |
| `heartrate_max` | bpm | Maximum heart rate |
| `heartrate_min` | bpm | Minimum heart rate |
| `heartrate_variability` | ms | Heart rate variability (HRV) |
| `heartrate_variability_rmssd` | ms | HRV RMSSD measurement |
| `heartrate_variability_sdnn` | ms | HRV SDNN measurement |
| `blood_pressure_systolic` | mmHg | Systolic blood pressure |
| `blood_pressure_diastolic` | mmHg | Diastolic blood pressure |
| `blood_oxygen` | % | Blood oxygen saturation (SpO2) |
| `ecg` | mV | Electrocardiogram readings |
| `vo2max` | mL/kg/min | Maximum oxygen uptake |

#### Activity & Movement
| Metric | Unit | Description |
|--------|------|-------------|
| `steps` | count | Step count |
| `distance` | meters | Distance traveled |
| `calories_burned` | kcal | Calories burned |
| `calories_active` | kcal | Active calories (exercise) |
| `calories_basal` | kcal | Basal metabolic calories |
| `floors_climbed` | count | Floors/flights climbed |
| `elevation` | meters | Elevation gained |
| `speed` | m/s | Speed/pace |
| `cadence` | steps/min | Step or pedal cadence |
| `power` | watts | Power output |
| `active_minutes` | minutes | Active zone minutes |
| `active_minutes_moderate` | minutes | Moderate intensity minutes |
| `active_minutes_vigorous` | minutes | Vigorous intensity minutes |
| `move_minutes` | minutes | Total movement minutes |

#### Sleep
| Metric | Unit | Description |
|--------|------|-------------|
| `sleep_duration` | seconds | Total sleep duration |
| `sleep_efficiency` | ratio (0-1) | Sleep efficiency score |
| `sleep_latency` | seconds | Time to fall asleep |
| `sleep_deep` | seconds | Deep sleep duration |
| `sleep_light` | seconds | Light sleep duration |
| `sleep_rem` | seconds | REM sleep duration |
| `sleep_awake` | seconds | Awake time during sleep |
| `sleep_score` | score | Overall sleep quality score |
| `sleep_respiratory_rate` | breaths/min | Breathing rate during sleep |
| `sleep_restfulness` | score | Sleep restfulness metric |

#### Body & Composition
| Metric | Unit | Description |
|--------|------|-------------|
| `weight` | kg | Body weight |
| `body_fat` | % | Body fat percentage |
| `body_mass_index` | kg/m2 | BMI |
| `lean_body_mass` | kg | Lean body mass |
| `bone_mass` | kg | Bone mass |
| `muscle_mass` | kg | Muscle mass |
| `water_percentage` | % | Body water percentage |
| `waist_circumference` | cm | Waist circumference |
| `height` | cm | Height |
| `basal_metabolic_rate` | kcal/day | Basal metabolic rate |

#### Respiratory
| Metric | Unit | Description |
|--------|------|-------------|
| `respiratory_rate` | breaths/min | Breathing rate |
| `forced_vital_capacity` | L | FVC |
| `forced_expiratory_volume` | L | FEV1 |
| `peak_expiratory_flow` | L/min | Peak flow rate |
| `oxygen_saturation` | % | SpO2 |

#### Glucose & Metabolic
| Metric | Unit | Description |
|--------|------|-------------|
| `glucose` | mg/dL | Blood glucose level |
| `glucose_interstitial` | mg/dL | Interstitial glucose (CGM) |
| `glucose_fasting` | mg/dL | Fasting glucose |
| `insulin` | mU/L | Insulin level |
| `ketones` | mmol/L | Blood ketone level |

#### Temperature
| Metric | Unit | Description |
|--------|------|-------------|
| `temperature_body` | Celsius | Body temperature |
| `temperature_skin` | Celsius | Skin temperature |
| `temperature_basal` | Celsius | Basal body temperature |
| `temperature_wrist` | Celsius | Wrist temperature (deviation) |

#### Stress & Recovery
| Metric | Unit | Description |
|--------|------|-------------|
| `stress` | score | Stress level score |
| `stress_body_battery` | score | Body battery / energy level |
| `recovery_score` | score | Recovery readiness score |
| `strain` | score | Exertion/strain score |
| `readiness_score` | score | Readiness score (Oura-style) |
| `resilience` | score | Resilience metric |

#### Hydration & Nutrition
| Metric | Unit | Description |
|--------|------|-------------|
| `hydration` | mL | Water intake |
| `caffeine` | mg | Caffeine intake |
| `alcohol` | standard_drinks | Alcohol consumption |

#### Reproductive Health
| Metric | Unit | Description |
|--------|------|-------------|
| `menstrual_flow` | enum | Menstrual flow level |
| `cervical_mucus` | enum | Cervical mucus quality |
| `ovulation_test` | enum | Ovulation test result |
| `sexual_activity` | boolean | Sexual activity logged |

#### Mindfulness
| Metric | Unit | Description |
|--------|------|-------------|
| `mindfulness_duration` | seconds | Meditation/mindfulness time |
| `mindfulness_sessions` | count | Number of sessions |

---

### 7.3 Statistics Types

Used with `GET /queries/statistics/daily` and `GET /queries/statistics/interval` in the `types` parameter. These are pre-aggregated values.

#### Activity Statistics
| Type | Unit | Description |
|------|------|-------------|
| `steps` | count | Total steps |
| `distanceTotal` | meters | Total distance traveled |
| `caloriesBurnedTotal` | kcal | Total calories burned |
| `caloriesActiveTotal` | kcal | Total active calories |
| `caloriesBasalTotal` | kcal | Total basal calories |
| `floorsClimbed` | count | Floors climbed |
| `elevationGain` | meters | Total elevation gained |
| `activeMinutesTotal` | minutes | Total active minutes |
| `activeMinutesModerate` | minutes | Moderate activity minutes |
| `activeMinutesVigorous` | minutes | Vigorous activity minutes |
| `moveMinutes` | minutes | Total movement minutes |
| `sedentaryMinutes` | minutes | Sedentary minutes |
| `standHours` | hours | Standing hours |

#### Heart Rate Statistics
| Type | Unit | Description |
|------|------|-------------|
| `heartRateAvg` | bpm | Average heart rate |
| `heartRateMax` | bpm | Maximum heart rate |
| `heartRateMin` | bpm | Minimum heart rate |
| `heartRateResting` | bpm | Resting heart rate |
| `hrvAvg` | ms | Average HRV |
| `hrvMax` | ms | Maximum HRV |

#### Sleep Statistics
| Type | Unit | Description |
|------|------|-------------|
| `sleepDurationTotal` | seconds | Total sleep duration |
| `sleepDeepDuration` | seconds | Deep sleep duration |
| `sleepLightDuration` | seconds | Light sleep duration |
| `sleepRemDuration` | seconds | REM sleep duration |
| `sleepAwakeDuration` | seconds | Awake during sleep |
| `sleepEfficiency` | ratio | Sleep efficiency (0-1) |
| `sleepLatency` | seconds | Time to fall asleep |
| `sleepScore` | score | Sleep quality score |
| `sleepRespiratoryRate` | breaths/min | Sleep breathing rate |

#### Body Statistics
| Type | Unit | Description |
|------|------|-------------|
| `weightLatest` | kg | Latest body weight |
| `bodyFatLatest` | % | Latest body fat % |
| `bmiLatest` | kg/m2 | Latest BMI |

#### Respiratory Statistics
| Type | Unit | Description |
|------|------|-------------|
| `respiratoryRateAvg` | breaths/min | Average breathing rate |
| `bloodOxygenAvg` | % | Average SpO2 |
| `bloodOxygenMin` | % | Minimum SpO2 |

#### Glucose Statistics
| Type | Unit | Description |
|------|------|-------------|
| `glucoseAvg` | mg/dL | Average glucose |
| `glucoseMax` | mg/dL | Maximum glucose |
| `glucoseMin` | mg/dL | Minimum glucose |
| `glucoseStdDev` | mg/dL | Glucose standard deviation |

#### Recovery & Stress Statistics
| Type | Unit | Description |
|------|------|-------------|
| `stressAvg` | score | Average stress |
| `recoveryScore` | score | Recovery score |
| `strainScore` | score | Strain score |
| `readinessScore` | score | Readiness score |
| `bodyBattery` | score | Body battery / energy |

#### Temperature Statistics
| Type | Unit | Description |
|------|------|-------------|
| `temperatureBodyAvg` | Celsius | Average body temperature |
| `temperatureSkinAvg` | Celsius | Average skin temperature |
| `temperatureDeviation` | Celsius | Temperature deviation from baseline |

---

### 7.4 Sleep Stages

Possible values for the `stage` field in sleep record stage breakdowns:

| Stage | Description |
|-------|-------------|
| `awake` | Fully awake period during sleep session (before falling asleep or after final wake) |
| `sleeping` | Generic sleeping state (used when provider does not differentiate stages) |
| `out_of_bed` | User got out of bed during the night |
| `light` | Light sleep (NREM Stage 1 + Stage 2) |
| `deep` | Deep sleep (NREM Stage 3, slow-wave sleep) |
| `rem` | REM (Rapid Eye Movement) sleep - dreaming stage |
| `awake_in_bed` | Awake but still in bed (restless periods mid-sleep) |
| `nap` | Nap session (short daytime sleep, typically <90 minutes) |
| `unknown` | Sleep stage could not be determined |

**Notes**:
- Not all providers support all stages. Basic providers may only report `sleeping` and `awake`.
- Oura and WHOOP provide the most detailed stage breakdowns (`light`, `deep`, `rem`, `awake_in_bed`).
- The `nap` stage is typically a top-level classification, not used within the stages array.
- Stage durations should sum to approximately the total sleep `duration_seconds`.

---

### 7.5 Workout/Activity Types

Normalized activity type identifiers used in workout records. All providers' activity types are mapped to this standardized list.

#### Running & Walking
| Type | Description |
|------|-------------|
| `running` | Outdoor running |
| `treadmill_running` | Treadmill running |
| `trail_running` | Trail/off-road running |
| `walking` | Walking |
| `hiking` | Hiking |
| `race_running` | Competitive race |
| `ultra_running` | Ultra-marathon running |
| `speed_walking` | Speed walking / race walking |
| `jogging` | Jogging |
| `stair_climbing` | Stair climbing exercise |

#### Cycling
| Type | Description |
|------|-------------|
| `cycling` | Outdoor cycling |
| `indoor_cycling` | Stationary bike / spin |
| `mountain_biking` | Mountain biking |
| `gravel_cycling` | Gravel cycling |
| `ebike_cycling` | Electric bike cycling |
| `handcycling` | Handcycling |
| `bmx` | BMX |
| `cyclocross` | Cyclocross |

#### Swimming
| Type | Description |
|------|-------------|
| `swimming` | General swimming |
| `pool_swimming` | Pool swimming |
| `open_water_swimming` | Open water swimming |

#### Strength & Gym
| Type | Description |
|------|-------------|
| `strength_training` | Weight/resistance training |
| `functional_training` | Functional fitness |
| `crossfit` | CrossFit-style workout |
| `hiit` | High-intensity interval training |
| `circuit_training` | Circuit training |
| `core_training` | Core/ab exercises |
| `flexibility` | Stretching/flexibility |
| `calisthenics` | Bodyweight exercises |
| `kettlebell` | Kettlebell training |
| `barbell` | Barbell training |

#### Cardio & Fitness
| Type | Description |
|------|-------------|
| `elliptical` | Elliptical machine |
| `rowing` | Rowing / indoor rowing |
| `stair_stepper` | Stair stepper machine |
| `jump_rope` | Jump rope / skipping |
| `aerobics` | Aerobics class |
| `dance` | Dance workout |
| `zumba` | Zumba |

#### Mind & Body
| Type | Description |
|------|-------------|
| `yoga` | Yoga (all styles) |
| `pilates` | Pilates |
| `meditation` | Meditation / mindfulness |
| `tai_chi` | Tai Chi |
| `breathwork` | Breathing exercises |
| `barre` | Barre workout |

#### Water Sports
| Type | Description |
|------|-------------|
| `surfing` | Surfing |
| `kayaking` | Kayaking |
| `canoeing` | Canoeing |
| `paddle_boarding` | Stand-up paddle boarding |
| `sailing` | Sailing |
| `water_polo` | Water polo |
| `windsurfing` | Windsurfing |
| `kitesurfing` | Kitesurfing |
| `diving` | Scuba diving |
| `snorkeling` | Snorkeling |
| `wakeboarding` | Wakeboarding |
| `water_skiing` | Water skiing |
| `rafting` | Whitewater rafting |
| `dragon_boating` | Dragon boating |

#### Winter Sports
| Type | Description |
|------|-------------|
| `skiing` | Downhill skiing |
| `cross_country_skiing` | Cross-country skiing |
| `snowboarding` | Snowboarding |
| `ice_skating` | Ice skating |
| `ice_hockey` | Ice hockey |
| `snowshoeing` | Snowshoeing |
| `backcountry_skiing` | Backcountry skiing |
| `ski_touring` | Ski touring |
| `curling` | Curling |

#### Racquet Sports
| Type | Description |
|------|-------------|
| `tennis` | Tennis |
| `badminton` | Badminton |
| `squash` | Squash |
| `racquetball` | Racquetball |
| `table_tennis` | Table tennis / ping pong |
| `pickleball` | Pickleball |
| `padel` | Padel |

#### Team Sports
| Type | Description |
|------|-------------|
| `soccer` | Soccer / football |
| `basketball` | Basketball |
| `american_football` | American football |
| `volleyball` | Volleyball |
| `baseball` | Baseball |
| `softball` | Softball |
| `rugby` | Rugby |
| `cricket` | Cricket |
| `handball` | Handball |
| `field_hockey` | Field hockey |
| `lacrosse` | Lacrosse |
| `ultimate_frisbee` | Ultimate frisbee |

#### Combat Sports
| Type | Description |
|------|-------------|
| `boxing` | Boxing |
| `kickboxing` | Kickboxing |
| `martial_arts` | Martial arts (general) |
| `mma` | Mixed martial arts |
| `wrestling` | Wrestling |
| `fencing` | Fencing |
| `judo` | Judo |
| `karate` | Karate |
| `taekwondo` | Taekwondo |
| `jiu_jitsu` | Jiu-jitsu (BJJ) |
| `muay_thai` | Muay Thai |

#### Outdoor & Adventure
| Type | Description |
|------|-------------|
| `rock_climbing` | Rock climbing |
| `bouldering` | Bouldering |
| `indoor_climbing` | Indoor climbing |
| `mountaineering` | Mountaineering |
| `horseback_riding` | Horseback riding |
| `skateboarding` | Skateboarding |
| `roller_skating` | Roller skating / inline skating |
| `parkour` | Parkour / freerunning |
| `orienteering` | Orienteering |
| `fishing` | Fishing |
| `hunting` | Hunting |
| `archery` | Archery |

#### Multi-Sport & Endurance
| Type | Description |
|------|-------------|
| `triathlon` | Triathlon |
| `duathlon` | Duathlon |
| `multisport` | Multi-sport activity |
| `transition` | Triathlon transition |
| `obstacle_course` | Obstacle course racing (e.g., Spartan) |

#### Golf & Precision
| Type | Description |
|------|-------------|
| `golf` | Golf |
| `bowling` | Bowling |
| `darts` | Darts |
| `shooting` | Shooting sports |
| `billiards` | Billiards / pool |

#### Wheelchair & Adaptive
| Type | Description |
|------|-------------|
| `wheelchair` | Wheelchair exercise |
| `wheelchair_running` | Wheelchair racing |

#### Other
| Type | Description |
|------|-------------|
| `other` | Uncategorized activity |
| `workout` | Generic workout (provider did not specify type) |
| `cooldown` | Cooldown / recovery session |
| `warmup` | Warm-up session |
| `recovery` | Recovery activity |
| `physical_therapy` | Physical therapy exercises |
| `foam_rolling` | Foam rolling / self-massage |
| `sauna` | Sauna session |
| `cold_plunge` | Cold water immersion |
| `hot_tub` | Hot tub session |
| `gardening` | Gardening |
| `housework` | Household chores |
| `snow_shoveling` | Snow shoveling |

---

### 7.6 Merge Strategies

Used with `GET /queries/timeseries` when the user has multiple providers reporting the same metric type. The `merge_strategy` parameter controls how overlapping data points are combined.

| Strategy | Description | Best For |
|----------|-------------|----------|
| `concat` | Returns all data points from all providers, unmodified. May contain duplicates at overlapping timestamps. | Debugging, seeing all raw data |
| `prefer_newest` | When multiple providers have data at the same timestamp, keeps the most recently synced value. | General use, latest data wins |
| `prefer_oldest` | When multiple providers have data at the same timestamp, keeps the earliest synced value. | Prioritizing first-recorded data |
| `prefer_provider` | Prefers data from the provider specified in `provider_slug`. Falls back to other providers for gaps. Requires `provider_slug` parameter. | When you trust one provider more |
| `average` | Averages values from multiple providers at overlapping timestamps. | Smoothing multi-source data |
| `max` | Takes the maximum value across providers at overlapping timestamps. | Heart rate peaks, step counts |
| `min` | Takes the minimum value across providers at overlapping timestamps. | Resting metrics, minimum values |
| `sum` | Sums values across providers at overlapping timestamps. Use with caution to avoid double-counting. | Rarely appropriate - use carefully |

**Notes**:
- Default strategy is `concat` if not specified.
- For most production use cases, `prefer_newest` or `prefer_provider` gives the best results.
- The `sum` strategy should be used with extreme caution as it can easily double-count metrics like steps if both a watch and phone are tracking simultaneously.

---

### 7.7 Nutritional Fields

The 29 standardized nutritional fields returned by Nutrition AI endpoints. All nutrition records (image, label, manual) use this schema.

| Field | Type | Unit | Description |
|-------|------|------|-------------|
| `calories` | `number` | kcal | Total energy |
| `total_fat_g` | `number` | g | Total fat |
| `saturated_fat_g` | `number` | g | Saturated fat |
| `trans_fat_g` | `number` | g | Trans fat |
| `cholesterol_mg` | `number` | mg | Cholesterol |
| `sodium_mg` | `number` | mg | Sodium |
| `total_carbohydrates_g` | `number` | g | Total carbohydrates |
| `dietary_fiber_g` | `number` | g | Dietary fiber |
| `total_sugars_g` | `number` | g | Total sugars |
| `added_sugars_g` | `number` | g | Added sugars |
| `protein_g` | `number` | g | Protein |
| `vitamin_a_mcg` | `number` | mcg | Vitamin A |
| `vitamin_c_mg` | `number` | mg | Vitamin C |
| `vitamin_d_mcg` | `number` | mcg | Vitamin D |
| `calcium_mg` | `number` | mg | Calcium |
| `iron_mg` | `number` | mg | Iron |
| `potassium_mg` | `number` | mg | Potassium |
| `vitamin_b6_mg` | `number` | mg | Vitamin B6 |
| `vitamin_b12_mcg` | `number` | mcg | Vitamin B12 |
| `magnesium_mg` | `number` | mg | Magnesium |
| `zinc_mg` | `number` | mg | Zinc |
| `phosphorus_mg` | `number` | mg | Phosphorus |
| `thiamin_mg` | `number` | mg | Thiamin (B1) |
| `riboflavin_mg` | `number` | mg | Riboflavin (B2) |
| `niacin_mg` | `number` | mg | Niacin (B3) |
| `folate_mcg` | `number` | mcg | Folate (B9) |
| `selenium_mcg` | `number` | mcg | Selenium |
| `copper_mg` | `number` | mg | Copper |
| `manganese_mg` | `number` | mg | Manganese |

**Notes**:
- Not all fields are populated for every food item. Missing values are `null`.
- For image-based analysis, macronutrients (`calories`, `protein_g`, `total_fat_g`, `total_carbohydrates_g`) are always estimated. Micronutrients may be `null` for less common foods.
- For label-based analysis, only nutrients printed on the label are populated.
- For manual records, you only need to provide the fields you know.
- All values are per the specified `serving_size` and `serving_unit`.

---

## Quick Reference: All Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/auth/hmac` | Exchange HMAC signature for JWT token |
| `GET` | `/providers/{slug}/integration/init_url` | Get OAuth redirect URL |
| `DELETE` | `/providers/{slug}/integration` | Disconnect provider |
| `GET` | `/queries/statistics/daily` | Daily aggregated stats (user timezone) |
| `GET` | `/queries/statistics/interval` | Interval stats (1h UTC intervals) |
| `GET` | `/queries/timeseries` | Raw time series data points |
| `GET` | `/queries/sleeps` | List sleep records |
| `GET` | `/queries/sleeps/{sleep_id}` | Get sleep record by ID |
| `GET` | `/queries/workouts` | List workout records |
| `GET` | `/queries/workouts/{workout_id}` | Get workout by ID |
| `GET` | `/queries/provider_records` | List raw provider records |
| `GET` | `/queries/provider_records/{record_id}` | Get raw provider record by ID |
| `POST` | `/nutrition_records/image` | Analyze food image (AI) |
| `POST` | `/nutrition_records/ingredients/label` | Analyze nutrition label (OCR) |
| `POST` | `/nutrition_records/manual` | Create manual nutrition record |
| `GET` | `/nutrition_records` | List nutrition records |
| `GET` | `/nutrition_records/{id}` | Get nutrition record by ID |
| `PATCH` | `/nutrition_records/{id}` | Modify serving size |
| `PUT` | `/nutrition_records/{id}` | Replace nutrition record |
| `DELETE` | `/nutrition_records/{id}` | Delete nutrition record |
| `POST` | `/lab_reports` | Upload lab report |
| `GET` | `/lab_reports` | List lab reports |
| `GET` | `/lab_reports/{id}` | Get lab report by ID |
| `GET` | `/userinfo` | User info + connected providers |
| `GET` | `/userproperties` | User physical properties |
