# Spike API - Complete Knowledge Base

## What is Spike API?

Spike API is a health data integration platform that connects applications to 500+ wearables, IoT devices, EMRs, and lab systems through a single unified API. It provides standardized, AI-ready health data from providers like Apple Health, Garmin, Fitbit, WHOOP, Oura, and many more.

## Documentation Map

| File | Contents |
|------|----------|
| `00-overview.md` | This file - platform overview and architecture |
| `01-authentication.md` | HMAC auth, token exchange, security |
| `02-api-endpoints.md` | All REST API endpoints with schemas |
| `03-flutter-sdk.md` | Flutter SDK setup, usage, background delivery |
| `04-nutrition-ai.md` | Food image analysis, nutritional fields |
| `05-lab-reports.md` | Lab report upload, OCR, LOINC mapping |
| `06-webhooks.md` | Real-time data notifications, signature verification |
| `07-providers-and-metrics.md` | Provider matrix, metrics matrix, activity types |
| `08-mcp-server.md` | Model Context Protocol for AI integration |
| `09-integration-guide.md` | Step-by-step Flutter + REST API integration |
| `10-configuration.md` | App config, backfill, redirect URLs |

## Key Facts

- **Base URL**: `https://app-api.spikeapi.com/v3`
- **API Version**: 3.0.2 (latest: v3.5.41, Feb 2026)
- **Auth**: HMAC-SHA256 signatures → JWT bearer tokens
- **SDKs**: Flutter, iOS, Android, React Native
- **Admin Console**: https://admin.spikeapi.com/
- **OpenAPI Spec**: https://docs.spikeapi.com/api-reference/openapi.json
- **Docs Index**: https://docs.spikeapi.com/llms.txt

## Pricing

| Product | Sandbox | Enterprise |
|---------|---------|------------|
| Wearables & IoT API | From $450/month | Custom |
| Nutrition Scanner AI | From $450/month | Custom |

Both tiers include GDPR, HIPAA, CCPA compliance.

## Compliance

- HIPAA compliant
- GDPR compliant
- CCPA compliant
- ISO certified

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                   Your Application                   │
├──────────────┬──────────────┬────────────────────────┤
│  Flutter SDK │   REST API   │   MCP Server           │
│  (on-device) │  (server)    │   (AI agents)          │
├──────────────┴──────────────┴────────────────────────┤
│              Spike API Platform (v3)                  │
│  Base URL: https://app-api.spikeapi.com/v3           │
├──────────────────────────────────────────────────────┤
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐│
│  │HealthKit │ │  Health   │ │  OAuth   │ │Nutrition││
│  │  (SDK)   │ │ Connect  │ │Providers │ │  AI     ││
│  │          │ │  (SDK)   │ │(Garmin,  │ │         ││
│  │          │ │          │ │Fitbit,..)│ │Lab Rpts ││
│  └──────────┘ └──────────┘ └──────────┘ └─────────┘│
└──────────────────────────────────────────────────────┘
```

## Integration Approaches

### 1. SDK Integration (Flutter/iOS/Android)
- Direct device health data access (Apple Health, Health Connect, Samsung Health)
- Data stored on-device only, requires active extraction
- Granular per-metric permissions
- Background delivery via webhooks
- Best for: Mobile apps needing real-time device health data

### 2. REST API Integration (Server-side)
- OAuth provider connections (Garmin, Fitbit, WHOOP, Oura, etc.)
- Webhook-driven data sync
- Query endpoints for statistics, sleep, workouts, timeseries
- Nutrition AI image analysis
- Lab report processing
- Best for: Backend services, data aggregation, AI analysis

### 3. MCP Integration (AI Agents)
- Pre-built MCP server at `https://app-api.spikeapi.com/v3/mcp`
- Compatible with OpenAI and Anthropic
- 6 health data tools available
- Best for: AI-powered health coaching, analysis, reporting

## Supported Providers (24)

### OAuth-based (server-side redirect flow)
| Provider | Slug | Strengths |
|----------|------|-----------|
| Garmin | `garmin` | Comprehensive workouts (35+ metrics), blood pressure |
| Fitbit | `fitbit` | Balanced: 15 intraday, 10 daily, 10 sleep metrics |
| Oura | `oura` | Sleep expertise (20+ metrics), comprehensive daily |
| WHOOP | `whoop` | Recovery metrics, breathing rate |
| Polar | `polar` | ECG integration, extensive daily (15+) |
| Strava | `strava` | Workout-only (18 metrics), athlete-focused |
| Withings | `withings` | Balanced access, ECG capability |
| Suunto | `suunto` | Elevation and cadence, diverse workouts |
| Huawei | `huawei` | Sleep quality, elevation tracking |
| Coros | `coros` | Workout specialization, cadence/pace |
| Ultrahuman | `ultrahuman` | Glucose + wearable metrics |
| Dexcom | `dexcom` | Glucose monitoring |
| Freestyle Libre | `freestyle_libre` | Glucose focus |
| Omron EU/US | `omron_eu`/`omron_us` | Blood pressure, body composition |
| Luna | `luna` | Sleep-exclusive (11 metrics) |

### SDK-based (on-device, no OAuth)
| Provider | Slug | Strengths |
|----------|------|-----------|
| Apple HealthKit | `apple` | Extensive intraday (45+ metrics) |
| Google Health Connect | `health_connect` | Broad coverage (30+ metrics) |
| Samsung Health Data | `samsung_health_data` | User properties, glucose |

## Data Categories

| Category | Endpoints | Use Case |
|----------|-----------|----------|
| Statistics | Daily, Interval | Trends, aggregated metrics |
| Sleep | List, By ID | Sleep stages, duration, quality |
| Workouts | List, By ID | Exercise sessions, GPS, laps |
| Time Series | Timeseries | Raw temporal data points |
| Provider Records | List, By ID | Debug, raw provider data |
| Nutrition | Image, Label, CRUD | Food analysis, tracking |
| Lab Reports | Upload, List, Get | Medical test results |
| User | Info, Properties | Profile, connected providers |
