# Spike API - Nutrition AI Service

## Overview

Spike's Nutrition AI provides AI-powered food image analysis that extracts comprehensive nutritional data from photos of meals, ingredient labels, or manual input. The service returns **29 nutritional fields** with precise measurements and a **Nutri-Score (A-E)** health rating for every analyzed item.

The system supports three input methods:
1. **Image analysis** - Photograph a meal for instant breakdown
2. **Label scanning** - Submit ingredient label images for extraction
3. **Manual entry** - Provide ingredient lists as text

---

## Endpoints

### POST /nutrition_records/image

Analyze a food image for nutritional content.

**Request Body:**

```json
{
  "image_url": "https://example.com/meal.jpg",
  "country_code": "US",
  "language_code": "en",
  "wait_on_process": true,
  "analysis_mode": "precise"
}
```

Alternatively, submit a base64-encoded image:

```json
{
  "image_base64": "<base64-encoded-image-data>",
  "country_code": "US",
  "language_code": "en",
  "wait_on_process": true,
  "analysis_mode": "precise"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `image_url` | string | One of `image_url` or `image_base64` | URL of the food image |
| `image_base64` | string | One of `image_url` or `image_base64` | Base64-encoded image data |
| `country_code` | string | No | ISO 3166-1 alpha-2 country code for localized nutritional data |
| `language_code` | string | No | ISO 639-1 language code for response text |
| `wait_on_process` | boolean | No | `true` = synchronous (wait for result), `false` = async (webhook notification) |
| `analysis_mode` | string | No | `"precise"` (default, highest accuracy) or `"fast"` (quicker, slightly less accurate) |

### POST /nutrition_records/ingredients/label

Analyze an ingredient label image.

```json
{
  "image_url": "https://example.com/label.jpg",
  "country_code": "US",
  "language_code": "en",
  "wait_on_process": true
}
```

### POST /nutrition_records/manual

Submit ingredients as text for analysis.

```json
{
  "ingredients": "2 eggs, 1 slice whole wheat bread, 1 tbsp butter, 100g avocado",
  "country_code": "US",
  "language_code": "en",
  "wait_on_process": true
}
```

### GET /nutrition_records

List all nutrition records for the authenticated user.

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | integer | Max records to return (default: 20) |
| `offset` | integer | Pagination offset |
| `status` | string | Filter by status: `pending`, `processing`, `completed`, `failed`, `updated` |

### GET /nutrition_records/{id}

Retrieve a specific nutrition record by ID.

### PATCH /nutrition_records/{id}

Partially update a nutrition record (e.g., user corrections to quantities or items).

```json
{
  "items": [
    {
      "name": "Grilled Chicken Breast",
      "quantity": 200,
      "unit": "g"
    }
  ]
}
```

### PUT /nutrition_records/{id}

Fully replace a nutrition record.

### DELETE /nutrition_records/{id}

Delete a nutrition record.

---

## Processing Modes

### Synchronous (`wait_on_process: true`)

The API call blocks until analysis is complete and returns the full result in the response. Typical response time: 3-15 seconds depending on image complexity and analysis mode.

```json
{
  "wait_on_process": true
}
```

**Response:** Full nutrition record with all 29 fields populated.

### Asynchronous (`wait_on_process: false`)

The API returns immediately with a record ID and `pending` status. Results are delivered via webhook when processing completes.

```json
{
  "wait_on_process": false
}
```

**Immediate Response:**
```json
{
  "id": "nutr_abc123",
  "status": "pending",
  "created_at": "2026-02-27T10:00:00Z"
}
```

**Webhook Notification:** Sent to the configured nutrition webhook URL when processing completes (see [06-webhooks.md](./06-webhooks.md)).

---

## Analysis Modes

### Precise (Default)

- Highest accuracy nutritional analysis
- Multi-pass AI analysis with cross-validation
- Processing time: 5-15 seconds
- Recommended for: meal logging, dietary tracking, clinical use

### Fast

- Quicker analysis with slightly reduced accuracy
- Single-pass AI analysis
- Processing time: 2-5 seconds
- Recommended for: real-time previews, quick estimates, high-volume scenarios

---

## Image Requirements

| Requirement | Specification |
|-------------|---------------|
| Minimum resolution | 512 x 512 pixels |
| Maximum file size | 10 MB |
| Supported formats | JPEG, PNG, WebP |
| Recommended resolution | 1024 x 1024 or higher |

---

## 29 Nutritional Fields

Every completed nutrition record includes the following fields:

| # | Field | Unit | Description |
|---|-------|------|-------------|
| 1 | `energy_kcal` | kcal | Total energy content |
| 2 | `protein_g` | g | Total protein |
| 3 | `fat_total_g` | g | Total fat |
| 4 | `carbohydrate_g` | g | Total carbohydrates |
| 5 | `fat_saturated_g` | g | Saturated fat |
| 6 | `fat_trans_g` | g | Trans fat |
| 7 | `fat_monounsaturated_g` | g | Monounsaturated fat |
| 8 | `fat_polyunsaturated_g` | g | Polyunsaturated fat |
| 9 | `fiber_g` | g | Dietary fiber |
| 10 | `sugar_g` | g | Total sugars |
| 11 | `cholesterol_mg` | mg | Cholesterol |
| 12 | `sodium_mg` | mg | Sodium |
| 13 | `calcium_mg` | mg | Calcium |
| 14 | `iron_mg` | mg | Iron |
| 15 | `magnesium_mg` | mg | Magnesium |
| 16 | `potassium_mg` | mg | Potassium |
| 17 | `zinc_mg` | mg | Zinc |
| 18 | `phosphorus_mg` | mg | Phosphorus |
| 19 | `vitamin_a_mcg` | mcg | Vitamin A (retinol equivalent) |
| 20 | `vitamin_b6_mg` | mg | Vitamin B6 (pyridoxine) |
| 21 | `vitamin_b12_mcg` | mcg | Vitamin B12 (cobalamin) |
| 22 | `vitamin_c_mg` | mg | Vitamin C (ascorbic acid) |
| 23 | `vitamin_d_mcg` | mcg | Vitamin D |
| 24 | `vitamin_e_mg` | mg | Vitamin E (alpha-tocopherol) |
| 25 | `vitamin_k_mcg` | mcg | Vitamin K |
| 26 | `thiamin_mg` | mg | Thiamin (Vitamin B1) |
| 27 | `riboflavin_mg` | mg | Riboflavin (Vitamin B2) |
| 28 | `niacin_mg` | mg | Niacin (Vitamin B3) |
| 29 | `folate_mcg` | mcg | Folate (Vitamin B9) |

---

## Localization

### Country Code (ISO 3166-1 Alpha-2)

Controls nutritional database and reference daily values. Examples:

| Code | Country | Effect |
|------|---------|--------|
| `US` | United States | USDA database, FDA daily values |
| `GB` | United Kingdom | UK nutritional standards |
| `DE` | Germany | German nutritional references |
| `JP` | Japan | Japanese food composition data |

### Language Code (ISO 639-1)

Controls the language of food item names and descriptions in the response.

| Code | Language |
|------|----------|
| `en` | English |
| `es` | Spanish |
| `fr` | French |
| `de` | German |
| `ja` | Japanese |

---

## Response Schema

### Completed Nutrition Record

```json
{
  "id": "nutr_abc123",
  "status": "completed",
  "created_at": "2026-02-27T10:00:00Z",
  "updated_at": "2026-02-27T10:00:12Z",
  "analysis_mode": "precise",
  "nutri_score": "B",
  "items": [
    {
      "name": "Grilled Chicken Breast",
      "quantity": 150,
      "unit": "g",
      "confidence": 0.92
    },
    {
      "name": "Brown Rice",
      "quantity": 200,
      "unit": "g",
      "confidence": 0.88
    },
    {
      "name": "Steamed Broccoli",
      "quantity": 100,
      "unit": "g",
      "confidence": 0.95
    }
  ],
  "totals": {
    "energy_kcal": 485.0,
    "protein_g": 42.5,
    "fat_total_g": 8.2,
    "carbohydrate_g": 58.3,
    "fat_saturated_g": 1.8,
    "fat_trans_g": 0.0,
    "fat_monounsaturated_g": 2.4,
    "fat_polyunsaturated_g": 1.9,
    "fiber_g": 5.6,
    "sugar_g": 2.1,
    "cholesterol_mg": 95.0,
    "sodium_mg": 320.0,
    "calcium_mg": 85.0,
    "iron_mg": 3.2,
    "magnesium_mg": 92.0,
    "potassium_mg": 680.0,
    "zinc_mg": 3.8,
    "phosphorus_mg": 350.0,
    "vitamin_a_mcg": 120.0,
    "vitamin_b6_mg": 0.8,
    "vitamin_b12_mcg": 0.4,
    "vitamin_c_mg": 65.0,
    "vitamin_d_mcg": 0.2,
    "vitamin_e_mg": 1.5,
    "vitamin_k_mcg": 110.0,
    "thiamin_mg": 0.3,
    "riboflavin_mg": 0.25,
    "niacin_mg": 12.0,
    "folate_mcg": 85.0
  }
}
```

### Status Lifecycle

```
pending  -->  processing  -->  completed
                           -->  failed
completed  -->  updated  (after PATCH/PUT)
```

| Status | Description |
|--------|-------------|
| `pending` | Record created, waiting for processing to begin |
| `processing` | AI analysis is in progress |
| `completed` | Analysis finished successfully |
| `failed` | Analysis could not be completed (bad image, unrecognizable food, etc.) |
| `updated` | Record was modified by user after completion (via PATCH or PUT) |

---

## Webhook Payload (Async Results)

When `wait_on_process: false`, the completed result is sent to the configured nutrition webhook URL:

```json
{
  "event_type": "nutrition_record_completed",
  "record_id": "nutr_abc123",
  "user_id": "user_xyz789",
  "status": "completed",
  "timestamp": "2026-02-27T10:00:12Z",
  "data": {
    "nutri_score": "B",
    "items": [ ... ],
    "totals": { ... }
  }
}
```

---

## Error Handling

| HTTP Code | Condition | Response |
|-----------|-----------|----------|
| 400 | Bad image format, unsupported file type | `{"error": "invalid_format", "message": "Image must be JPEG, PNG, or WebP"}` |
| 413 | File exceeds 10MB limit | `{"error": "payload_too_large", "message": "Image must be under 10MB"}` |
| 422 | Invalid image URL or unreachable | `{"error": "invalid_url", "message": "Could not fetch image from provided URL"}` |
| 422 | Image below minimum resolution | `{"error": "resolution_too_low", "message": "Image must be at least 512x512 pixels"}` |
| 429 | Rate limit exceeded | `{"error": "rate_limit_exceeded", "message": "Too many requests"}` |

---

## Photography Best Practices

For optimal AI analysis accuracy:

1. **Lighting** - Use natural or bright, even lighting. Avoid harsh shadows or dim environments.
2. **Angle** - Shoot from 30-45 degrees above the plate (not straight down, not from the side).
3. **Distance** - Frame the entire plate/meal with minimal background. Food should fill 60-80% of the frame.
4. **Focus** - Ensure all food items are in sharp focus. Avoid motion blur.
5. **Separation** - When possible, keep food items slightly separated for better identification.
6. **No filters** - Submit unedited photos. Instagram filters or color adjustments reduce accuracy.
7. **Scale reference** - Including a standard item (fork, plate) helps with portion estimation.
8. **Single meal** - Photograph one meal/plate at a time for best results.

---

## Flutter SDK Methods

### analyzeNutrition()

Submit a food image for synchronous analysis.

```dart
final result = await spikeApi.analyzeNutrition(
  imageFile: File('/path/to/meal.jpg'),
  countryCode: 'US',
  languageCode: 'en',
  analysisMode: AnalysisMode.precise,
);

print('Nutri-Score: ${result.nutriScore}');
print('Calories: ${result.totals.energyKcal} kcal');
print('Protein: ${result.totals.proteinG} g');
```

### submitNutritionForAnalysis()

Submit a food image for asynchronous analysis (webhook notification).

```dart
final record = await spikeApi.submitNutritionForAnalysis(
  imageUrl: 'https://example.com/meal.jpg',
  countryCode: 'US',
  languageCode: 'en',
);

print('Record ID: ${record.id}');
print('Status: ${record.status}'); // "pending"
// Result will be delivered via webhook
```

### getNutritionRecords()

Retrieve a list of nutrition records.

```dart
final records = await spikeApi.getNutritionRecords(
  limit: 20,
  offset: 0,
  status: NutritionStatus.completed,
);

for (final record in records) {
  print('${record.id}: ${record.totals.energyKcal} kcal - Score: ${record.nutriScore}');
}
```

### getNutritionRecord()

Retrieve a single nutrition record by ID.

```dart
final record = await spikeApi.getNutritionRecord('nutr_abc123');

if (record.status == NutritionStatus.completed) {
  for (final item in record.items) {
    print('${item.name}: ${item.quantity}${item.unit} (confidence: ${item.confidence})');
  }
}
```

### updateNutritionRecord()

Update a nutrition record (e.g., user corrections).

```dart
final updated = await spikeApi.updateNutritionRecord(
  recordId: 'nutr_abc123',
  items: [
    NutritionItem(name: 'Grilled Chicken Breast', quantity: 200, unit: 'g'),
    NutritionItem(name: 'Brown Rice', quantity: 150, unit: 'g'),
  ],
);
```

### deleteNutritionRecord()

Delete a nutrition record.

```dart
await spikeApi.deleteNutritionRecord('nutr_abc123');
```

---

## Complete Code Examples

### Full Meal Logging Flow (Flutter)

```dart
import 'dart:io';
import 'package:image_picker/image_picker.dart';

class MealLoggingService {
  final SpikeApiClient _spikeApi;

  MealLoggingService(this._spikeApi);

  /// Capture and analyze a meal photo
  Future<NutritionRecord> logMeal() async {
    // 1. Capture image
    final picker = ImagePicker();
    final image = await picker.pickImage(
      source: ImageSource.camera,
      maxWidth: 1024,
      maxHeight: 1024,
      imageQuality: 85,
    );

    if (image == null) throw Exception('No image captured');

    // 2. Validate image size
    final file = File(image.path);
    final fileSize = await file.length();
    if (fileSize > 10 * 1024 * 1024) {
      throw Exception('Image exceeds 10MB limit');
    }

    // 3. Submit for analysis (synchronous)
    try {
      final result = await _spikeApi.analyzeNutrition(
        imageFile: file,
        countryCode: 'US',
        languageCode: 'en',
        analysisMode: AnalysisMode.precise,
      );

      // 4. Process results
      print('--- Meal Analysis ---');
      print('Nutri-Score: ${result.nutriScore}');
      print('Items detected: ${result.items.length}');
      for (final item in result.items) {
        print('  ${item.name}: ${item.quantity}${item.unit} '
            '(confidence: ${(item.confidence * 100).toStringAsFixed(0)}%)');
      }
      print('--- Totals ---');
      print('Calories: ${result.totals.energyKcal} kcal');
      print('Protein: ${result.totals.proteinG}g');
      print('Carbs: ${result.totals.carbohydrateG}g');
      print('Fat: ${result.totals.fatTotalG}g');
      print('Fiber: ${result.totals.fiberG}g');

      return result;
    } catch (e) {
      if (e is SpikeApiException) {
        switch (e.code) {
          case 400:
            throw Exception('Invalid image format. Use JPEG, PNG, or WebP.');
          case 413:
            throw Exception('Image too large. Maximum 10MB.');
          case 422:
            throw Exception('Could not analyze image. Please try again.');
          default:
            throw Exception('Analysis failed: ${e.message}');
        }
      }
      rethrow;
    }
  }

  /// Get daily nutrition summary
  Future<DailyNutritionSummary> getDailySummary(DateTime date) async {
    final records = await _spikeApi.getNutritionRecords(
      status: NutritionStatus.completed,
    );

    final todayRecords = records.where((r) =>
      r.createdAt.year == date.year &&
      r.createdAt.month == date.month &&
      r.createdAt.day == date.day
    ).toList();

    double totalCalories = 0;
    double totalProtein = 0;
    double totalCarbs = 0;
    double totalFat = 0;

    for (final record in todayRecords) {
      totalCalories += record.totals.energyKcal;
      totalProtein += record.totals.proteinG;
      totalCarbs += record.totals.carbohydrateG;
      totalFat += record.totals.fatTotalG;
    }

    return DailyNutritionSummary(
      date: date,
      mealCount: todayRecords.length,
      totalCalories: totalCalories,
      totalProtein: totalProtein,
      totalCarbs: totalCarbs,
      totalFat: totalFat,
    );
  }
}
```

### Backend Webhook Handler (Python)

```python
from fastapi import FastAPI, Request, HTTPException
import hmac
import hashlib

app = FastAPI()

WEBHOOK_SIGNATURE_KEY = "your_webhook_signature_key"

@app.post("/webhooks/nutrition")
async def handle_nutrition_webhook(request: Request):
    # Verify signature
    body = await request.body()
    signature = request.headers.get("X-Body-Signature")

    expected = hmac.new(
        WEBHOOK_SIGNATURE_KEY.encode(),
        body,
        hashlib.sha256
    ).hexdigest()

    if signature != expected:
        raise HTTPException(status_code=401, detail="Invalid signature")

    payload = await request.json()

    if payload["event_type"] == "nutrition_record_completed":
        record_id = payload["record_id"]
        user_id = payload["user_id"]
        totals = payload["data"]["totals"]
        nutri_score = payload["data"]["nutri_score"]

        # Store in your database
        await save_nutrition_result(
            user_id=user_id,
            record_id=record_id,
            calories=totals["energy_kcal"],
            protein=totals["protein_g"],
            carbs=totals["carbohydrate_g"],
            fat=totals["fat_total_g"],
            nutri_score=nutri_score,
        )

    return {"status": "ok"}
```

### Ingredient Label Scanning (Flutter)

```dart
Future<NutritionRecord> scanLabel(File labelImage) async {
  final result = await spikeApi.analyzeNutrition(
    imageFile: labelImage,
    endpoint: '/nutrition_records/ingredients/label',
    countryCode: 'US',
    languageCode: 'en',
    analysisMode: AnalysisMode.precise,
  );

  return result;
}
```

### Manual Ingredient Entry (Flutter)

```dart
Future<NutritionRecord> analyzeIngredients(String ingredients) async {
  final result = await spikeApi.submitManualNutrition(
    ingredients: ingredients,  // e.g., "2 eggs, 1 slice toast, 1 tbsp butter"
    countryCode: 'US',
    languageCode: 'en',
  );

  return result;
}
```
