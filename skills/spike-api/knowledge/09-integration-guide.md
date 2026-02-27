# Spike API Integration Guide - Flutter + REST API

## Architecture Decision: SDK vs API

### Use the Flutter SDK When:
- Reading data from Apple Health (HealthKit)
- Reading data from Google Health Connect
- Reading data from Samsung Health
- You need on-device health data extraction
- You want background delivery of health updates
- You need Nutrition AI image analysis in the app

### Use the REST API When:
- Connecting OAuth providers (Garmin, Fitbit, WHOOP, Oura, etc.)
- Server-side data processing and storage
- Webhook-driven data sync pipeline
- Lab report processing
- Querying aggregated statistics
- MCP integration for AI agents
- Backend nutrition record management

### Recommended: Hybrid Approach
```
┌─────────────────────────────────────────────┐
│              Flutter App                     │
│  ┌──────────────────────────────────────┐   │
│  │  Spike Flutter SDK                    │   │
│  │  - Apple Health / Health Connect      │   │
│  │  - Samsung Health                     │   │
│  │  - Nutrition AI (image capture)       │   │
│  │  - Background delivery                │   │
│  └──────────────────────────────────────┘   │
│           │ auth signature                   │
│           ▼                                  │
│  ┌──────────────────────────────────────┐   │
│  │  Your Backend (FastAPI)               │   │
│  │  - HMAC signature generation          │   │
│  │  - OAuth provider redirect handling   │   │
│  │  - Webhook receiver                   │   │
│  │  - Data aggregation                   │   │
│  │  - Lab report uploads                 │   │
│  │  - API queries (stats, sleep, etc.)   │   │
│  └──────────────────────────────────────┘   │
│           │                                  │
│           ▼                                  │
│  ┌──────────────────────────────────────┐   │
│  │  Spike API (v3)                       │   │
│  │  Base: app-api.spikeapi.com/v3        │   │
│  └──────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

## Step-by-Step Integration

### Phase 1: Backend Setup (FastAPI)

#### 1.1 Environment Variables
```env
SPIKE_APPLICATION_ID=your_app_id
SPIKE_HMAC_KEY=your_hmac_key_min_16_chars
SPIKE_WEBHOOK_SIGNATURE_KEY=your_webhook_sig_key
SPIKE_API_BASE_URL=https://app-api.spikeapi.com/v3
```

#### 1.2 Authentication Service
```python
# spike_auth.py
import hmac
import hashlib
import httpx

SPIKE_HMAC_KEY = os.environ["SPIKE_HMAC_KEY"]
SPIKE_APP_ID = int(os.environ["SPIKE_APPLICATION_ID"])
SPIKE_BASE_URL = os.environ["SPIKE_API_BASE_URL"]

def generate_signature(user_id: str) -> str:
    """Generate HMAC-SHA256 signature for user auth."""
    h = hmac.new(
        SPIKE_HMAC_KEY.encode(),
        user_id.encode(),
        hashlib.sha256
    )
    return h.hexdigest()

async def get_access_token(user_id: str) -> str:
    """Exchange signature for JWT access token."""
    signature = generate_signature(user_id)
    async with httpx.AsyncClient() as client:
        response = await client.post(
            f"{SPIKE_BASE_URL}/auth/hmac",
            json={
                "application_id": SPIKE_APP_ID,
                "application_user_id": user_id,
                "signature": signature,
            }
        )
        response.raise_for_status()
        return response.json()["access_token"]
```

#### 1.3 Webhook Handler
```python
# webhook_handler.py
from fastapi import APIRouter, Request, HTTPException
import hmac
import hashlib

router = APIRouter()
WEBHOOK_SIG_KEY = os.environ["SPIKE_WEBHOOK_SIGNATURE_KEY"]

def verify_webhook_signature(body: bytes, signature: str) -> bool:
    """Verify HMAC-SHA256 webhook signature."""
    expected = hmac.new(
        WEBHOOK_SIG_KEY.encode(),
        body,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(expected, signature)

@router.post("/webhooks/spike/health-data")
async def handle_health_webhook(request: Request):
    body = await request.body()
    signature = request.headers.get("X-Body-Signature", "")

    if not verify_webhook_signature(body, signature):
        raise HTTPException(status_code=401, detail="Invalid signature")

    events = await request.json()
    for event in events:
        event_type = event["event_type"]
        user_id = event["application_user_id"]
        provider = event["provider_slug"]

        if event_type == "record_change":
            metrics = event.get("metrics", [])
            # Fetch updated data from Spike API
            await process_health_update(user_id, provider, metrics)
        elif event_type == "provider_integration_created":
            await handle_provider_connected(user_id, provider)
        elif event_type == "provider_integration_deleted":
            await handle_provider_disconnected(user_id, provider)

    return {"status": "ok"}

@router.post("/webhooks/spike/nutrition")
async def handle_nutrition_webhook(request: Request):
    body = await request.body()
    signature = request.headers.get("X-Body-Signature", "")

    if not verify_webhook_signature(body, signature):
        raise HTTPException(status_code=401, detail="Invalid signature")

    data = await request.json()
    # Process nutrition analysis result
    return {"status": "ok"}

@router.post("/webhooks/spike/lab-reports")
async def handle_lab_report_webhook(request: Request):
    body = await request.body()
    signature = request.headers.get("X-Body-Signature", "")

    if not verify_webhook_signature(body, signature):
        raise HTTPException(status_code=401, detail="Invalid signature")

    data = await request.json()
    # Process lab report result
    return {"status": "ok"}
```

#### 1.4 Provider Integration API
```python
# provider_routes.py
from fastapi import APIRouter, Depends
import httpx

router = APIRouter()

@router.get("/providers/{provider_slug}/connect")
async def initiate_provider_connection(
    provider_slug: str,
    user_id: str,
    redirect_uri: str = None
):
    """Get OAuth URL for connecting a health provider."""
    token = await get_access_token(user_id)

    params = {}
    if redirect_uri:
        params["redirect_uri"] = redirect_uri

    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"{SPIKE_BASE_URL}/providers/{provider_slug}/integration/init_url",
            headers={"Authorization": f"Bearer {token}"},
            params=params
        )
        response.raise_for_status()
        return response.json()

@router.delete("/providers/{provider_slug}/disconnect")
async def disconnect_provider(provider_slug: str, user_id: str):
    """Remove a provider integration."""
    token = await get_access_token(user_id)

    async with httpx.AsyncClient() as client:
        response = await client.delete(
            f"{SPIKE_BASE_URL}/providers/{provider_slug}/integration",
            headers={"Authorization": f"Bearer {token}"}
        )
        return {"status": "disconnected"}
```

#### 1.5 Health Data Query Service
```python
# health_data_service.py
import httpx
from datetime import datetime, timedelta

async def get_daily_statistics(
    user_id: str,
    types: list[str],
    days: int = 7
) -> dict:
    """Get daily aggregated health statistics."""
    token = await get_access_token(user_id)
    today = datetime.now().strftime("%Y-%m-%d")
    from_date = (datetime.now() - timedelta(days=days)).strftime("%Y-%m-%d")

    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"{SPIKE_BASE_URL}/queries/statistics/daily",
            headers={"Authorization": f"Bearer {token}"},
            params={
                "from_date": from_date,
                "to_date": today,
                "types": types,
            }
        )
        response.raise_for_status()
        return response.json()

async def get_sleep_data(user_id: str, days: int = 7) -> dict:
    """Get sleep records with stages."""
    token = await get_access_token(user_id)
    today = datetime.now().strftime("%Y-%m-%d")
    from_date = (datetime.now() - timedelta(days=days)).strftime("%Y-%m-%d")

    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"{SPIKE_BASE_URL}/queries/sleeps",
            headers={"Authorization": f"Bearer {token}"},
            params={
                "from_date": from_date,
                "to_date": today,
                "include_stages": True,
            }
        )
        response.raise_for_status()
        return response.json()

async def get_workouts(user_id: str, days: int = 7) -> dict:
    """Get workout records."""
    token = await get_access_token(user_id)
    now = datetime.utcnow().strftime("%Y-%m-%dT%H:%M:%SZ")
    from_ts = (datetime.utcnow() - timedelta(days=days)).strftime("%Y-%m-%dT%H:%M:%SZ")

    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"{SPIKE_BASE_URL}/queries/workouts",
            headers={"Authorization": f"Bearer {token}"},
            params={
                "from_timestamp": from_ts,
                "to_timestamp": now,
            }
        )
        response.raise_for_status()
        return response.json()
```

### Phase 2: Flutter App Setup

#### 2.1 Dependencies
```yaml
# pubspec.yaml
dependencies:
  spike_flutter_sdk: ^4.3.34
  dio: ^5.0.0        # For backend API calls
  crypto: ^3.0.0      # For HMAC if needed client-side
```

#### 2.2 Spike Service Class
```dart
// lib/core/services/spike_service.dart
import 'package:spike_flutter_sdk/spike_flutter_sdk.dart';

class SpikeService {
  SpikeConnectionV3? _connection;

  /// Initialize Spike SDK connection
  Future<void> initialize({
    required int applicationId,
    required String signature,  // From your backend
    required String userId,
  }) async {
    _connection = await SpikeSDKV3.createConnection(
      applicationId: applicationId,
      signature: signature,
      endUserId: userId,
    );

    // Setup logging
    await SpikeSDKV3.setLogCallback(
      callback: (level, message) {
        debugPrint("[Spike.$level] $message");
      },
    );
  }

  /// Request health data permissions
  Future<void> requestPermissions({
    List<StatisticsType> types = const [
      StatisticsType.steps,
      StatisticsType.caloriesBurnedTotal,
      StatisticsType.distanceTotal,
    ],
    bool includeBackgroundDelivery = false,
  }) async {
    await _connection!.requestHealthPermissions(
      statisticTypes: types,
      includeBackgroundDelivery: includeBackgroundDelivery,
    );
  }

  /// Get daily statistics
  Future<List<Statistic>> getStatistics({
    required List<StatisticsType> types,
    required DateTime from,
    required DateTime to,
    StatisticsInterval interval = StatisticsInterval.day,
  }) async {
    return await _connection!.getStatistics(
      ofTypes: types,
      from: from,
      to: to,
      interval: interval,
    );
  }

  /// Get detailed records
  Future<List<Record>> getRecords({
    required List<MetricType> types,
    required DateTime from,
    required DateTime to,
  }) async {
    return await _connection!.getRecords(
      ofTypes: types,
      from: from,
      to: to,
    );
  }

  /// Enable background delivery
  Future<void> enableBackgroundDelivery({
    required List<StatisticsType> types,
  }) async {
    await _connection!.enableBackgroundDelivery(
      statisticTypes: types,
    );
  }

  /// Analyze food image
  Future<NutritionRecord> analyzeNutrition({
    required String imageBase64,
    DateTime? consumedAt,
  }) async {
    final config = NutritionalAnalysisConfig(
      analysisMode: NutritionalAnalysisMode.precise,
      includeNutriScore: true,
      includeDishDescription: true,
      includeIngredients: true,
      includeNutritionFields: [
        NutritionalField.energyKcal,
        NutritionalField.proteinG,
        NutritionalField.fatTotalG,
        NutritionalField.carbohydrateG,
        NutritionalField.fiberTotalDietaryG,
        NutritionalField.sugarsTotalG,
      ],
    );

    return await _connection!.analyzeNutrition(
      imageBase64: imageBase64,
      consumedAt: consumedAt,
      config: config,
    );
  }
}
```

#### 2.3 Auth Flow
```dart
// lib/core/services/spike_auth_service.dart
import 'package:dio/dio.dart';

class SpikeAuthService {
  final Dio _dio;
  final String _backendBaseUrl;

  SpikeAuthService({required String backendBaseUrl})
    : _backendBaseUrl = backendBaseUrl,
      _dio = Dio(BaseOptions(baseUrl: backendBaseUrl));

  /// Get HMAC signature from your backend
  Future<String> getSignature(String userId) async {
    final response = await _dio.post('/api/spike/signature', data: {
      'user_id': userId,
    });
    return response.data['signature'];
  }

  /// Get application ID from backend config
  Future<int> getApplicationId() async {
    final response = await _dio.get('/api/spike/config');
    return response.data['application_id'];
  }
}
```

### Phase 3: Platform Configuration

#### iOS (ios/Runner/Info.plist)
```xml
<key>NSHealthShareUsageDescription</key>
<string>HolisticOS uses your health data to provide personalized energy insights and wellness tracking.</string>
<key>NSHealthUpdateUsageDescription</key>
<string>HolisticOS records your wellness activities to improve energy predictions.</string>
```

#### Android (android/app/src/main/AndroidManifest.xml)
Add all required health permissions (see 03-flutter-sdk.md for full list)

#### Android (android/app/build.gradle)
```gradle
android {
    compileSdkVersion 34
    defaultConfig {
        minSdkVersion 28
    }
}
```

### Phase 4: Data Flow

```
1. User opens app
   └─> Backend generates HMAC signature
   └─> SDK creates connection with signature
   └─> SDK requests health permissions

2. User connects wearable (OAuth)
   └─> Backend gets init_url from Spike API
   └─> User redirected to provider auth
   └─> Provider redirects back
   └─> Spike sends provider_integration_created webhook

3. Health data flows
   └─> SDK: Background delivery pushes device data
   └─> API: Provider sends data → Spike → webhook to backend
   └─> Backend processes and stores data

4. App queries data
   └─> SDK: getStatistics() for device data
   └─> API: /queries/statistics/daily for provider data
   └─> Display combined health dashboard

5. Nutrition tracking
   └─> User takes food photo
   └─> SDK: analyzeNutrition() or submitNutritionForAnalysis()
   └─> Results displayed with nutritional breakdown
```

## Environment-Specific Configuration

### Development
```env
SPIKE_API_BASE_URL=https://app-api.spikeapi.com/v3
# Use separate dev application in Spike admin console
```

### Production
```env
SPIKE_API_BASE_URL=https://app-api.spikeapi.com/v3
# Use production application with proper webhook URLs
# Ensure HTTPS for all webhook endpoints
```

## Key Gotchas

1. **HMAC key MUST stay server-side** - Never embed in Flutter app
2. **Call requestHealthPermissions on EVERY app restart** - Required by SDK
3. **enableBackgroundDelivery OVERWRITES** - Include ALL types in one call
4. **Android Health Connect max 90-day query** - Paginate if needed
5. **Webhooks must respond HTTP 200 within 30 seconds** - Process async
6. **Provider backfill is async** - Data may arrive hours later
7. **SDK providers (Apple/HC/Samsung) need manual backfill** - Query historical data explicitly
8. **Application user ID must be consistent** - Same ID throughout user lifecycle
9. **Samsung Health requires partnership** for production use
10. **iOS background delivery requires device to be unlocked**
