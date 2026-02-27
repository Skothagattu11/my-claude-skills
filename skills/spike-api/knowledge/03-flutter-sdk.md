# Spike API - Flutter SDK

## Overview

The Spike Flutter SDK provides direct on-device access to Apple HealthKit (iOS), Google Health Connect (Android), and Samsung Health Data (Android). It handles permissions, data extraction, background delivery, and nutrition image analysis entirely on-device, with data synced to Spike servers for unified API access.

| Property | Value |
|----------|-------|
| **SDK Version** | 4.3.184 |
| **pubspec Constraint** | `^4.3.34` |
| **Package** | [spike_flutter_sdk on pub.dev](https://pub.dev/packages/spike_flutter_sdk) |
| **iOS Minimum** | iOS 13.0+ |
| **Android Minimum** | Android 9.0+ (API 28) |
| **Data Sources** | Apple HealthKit, Google Health Connect, Samsung Health Data |

---

## Installation

### pubspec.yaml

```yaml
dependencies:
  spike_flutter_sdk: ^4.3.34
```

Then run:

```bash
flutter pub get
```

---

## iOS Configuration

### 1. Install CocoaPods Dependencies

From the `ios/` directory of your Flutter project:

```bash
cd ios
pod install
# Or if updating:
pod update
```

### 2. Enable HealthKit Capability

In Xcode:

1. Open `ios/Runner.xcworkspace`
2. Select the **Runner** target
3. Go to **Signing & Capabilities**
4. Click **+ Capability**
5. Add **HealthKit**
6. Ensure both **HealthKit** checkboxes are enabled (including Background Delivery if needed)

### 3. Info.plist Entries

Add the following keys to `ios/Runner/Info.plist`:

```xml
<key>NSHealthShareUsageDescription</key>
<string>This app reads your health data to provide personalized energy insights.</string>

<key>NSHealthUpdateUsageDescription</key>
<string>This app writes health data to track your wellness progress.</string>
```

These descriptions are shown to the user in the HealthKit permission dialog. Customize the strings to match your app's purpose.

---

## Android Configuration

### 1. Maven Repository

Add the Spike Maven repository to your Android Gradle configuration. In `android/build.gradle` (project-level), add:

```groovy
allprojects {
    repositories {
        google()
        mavenCentral()
        maven {
            url "https://gitlab.com/api/v4/projects/43396247/packages/maven"
        }
    }
}
```

### 2. SDK Versions (local.properties)

Set the following in `android/local.properties` or `android/app/build.gradle`:

```properties
minSdkVersion=28
compileSdkVersion=34
```

In `android/app/build.gradle`:

```groovy
android {
    compileSdk 34

    defaultConfig {
        minSdk 28
        targetSdk 34
    }
}
```

### 3. Health Connect Permissions

Add all 26 health READ permissions in `android/app/src/main/AndroidManifest.xml` inside the `<manifest>` tag (before `<application>`):

```xml
<!-- Health Connect Permissions (26 total) -->
<uses-permission android:name="android.permission.health.READ_STEPS" />
<uses-permission android:name="android.permission.health.READ_DISTANCE" />
<uses-permission android:name="android.permission.health.READ_TOTAL_CALORIES_BURNED" />
<uses-permission android:name="android.permission.health.READ_ACTIVE_CALORIES_BURNED" />
<uses-permission android:name="android.permission.health.READ_BASAL_METABOLIC_RATE" />
<uses-permission android:name="android.permission.health.READ_HEART_RATE" />
<uses-permission android:name="android.permission.health.READ_RESTING_HEART_RATE" />
<uses-permission android:name="android.permission.health.READ_HEART_RATE_VARIABILITY" />
<uses-permission android:name="android.permission.health.READ_OXYGEN_SATURATION" />
<uses-permission android:name="android.permission.health.READ_BODY_TEMPERATURE" />
<uses-permission android:name="android.permission.health.READ_BLOOD_PRESSURE" />
<uses-permission android:name="android.permission.health.READ_BLOOD_GLUCOSE" />
<uses-permission android:name="android.permission.health.READ_RESPIRATORY_RATE" />
<uses-permission android:name="android.permission.health.READ_SLEEP" />
<uses-permission android:name="android.permission.health.READ_WEIGHT" />
<uses-permission android:name="android.permission.health.READ_HEIGHT" />
<uses-permission android:name="android.permission.health.READ_BODY_FAT" />
<uses-permission android:name="android.permission.health.READ_LEAN_BODY_MASS" />
<uses-permission android:name="android.permission.health.READ_BONE_MASS" />
<uses-permission android:name="android.permission.health.READ_POWER" />
<uses-permission android:name="android.permission.health.READ_SPEED" />
<uses-permission android:name="android.permission.health.READ_ELEVATION_GAINED" />
<uses-permission android:name="android.permission.health.READ_FLOORS_CLIMBED" />
<uses-permission android:name="android.permission.health.READ_EXERCISE" />
<uses-permission android:name="android.permission.health.READ_NUTRITION" />
<uses-permission android:name="android.permission.health.READ_HYDRATION" />
```

### 4. Intent Filter for Permissions Rationale

Add the following intent filter inside the `<activity>` tag for your main activity in `AndroidManifest.xml`. This is required by Health Connect to show your app's permissions rationale screen:

```xml
<intent-filter>
    <action android:name="androidx.health.ACTION_SHOW_PERMISSIONS_RATIONALE" />
</intent-filter>
```

### 5. Activity Alias for ViewPermissionUsageActivity

Add an activity alias at the `<application>` level in `AndroidManifest.xml`. This enables the Health Connect permissions UI to link back to your app:

```xml
<activity-alias
    android:name="ViewPermissionUsageActivity"
    android:exported="true"
    android:targetActivity=".MainActivity"
    android:permission="android.permission.START_VIEW_PERMISSION_USAGE">
    <intent-filter>
        <action android:name="android.intent.action.VIEW_PERMISSION_USAGE" />
        <category android:name="android.intent.category.HEALTH_PERMISSIONS" />
    </intent-filter>
</activity-alias>
```

### 6. MainActivity Must Extend FlutterFragmentActivity

Your `MainActivity.kt` must extend `FlutterFragmentActivity` (not `FlutterActivity`). This is required for Health Connect's permission flow:

```kotlin
// android/app/src/main/kotlin/.../MainActivity.kt
package com.example.yourapp

import io.flutter.embedding.android.FlutterFragmentActivity

class MainActivity: FlutterFragmentActivity()
```

If your `MainActivity` currently extends `FlutterActivity`, change it to `FlutterFragmentActivity`.

---

## Usage Guide - 3 Steps

### Step 1: Create Connection

Establish a connection to the Spike SDK. This authenticates your app and user with Spike servers.

```dart
import 'package:spike_flutter_sdk/spike_flutter_sdk.dart';

final connection = await SpikeSDKV3.createConnection(
    applicationId: 1000,              // Your app ID from Spike admin console
    signature: "hmac_signature",       // HMAC-SHA256 signature (generated server-side)
    endUserId: "user-id",             // Your user's unique identifier
);
```

**Important**: The `signature` must be generated on your backend using your HMAC key. Never embed the HMAC key in client code. See `01-authentication.md` for HMAC signature generation.

### Step 2: Request Health Permissions

Request permissions to read health data from the device. The SDK handles the platform-specific permission dialogs (HealthKit on iOS, Health Connect on Android).

```dart
await connection.requestHealthPermissions(
    statisticTypes: [
        StatisticsType.steps,
        StatisticsType.caloriesBurnedActive,
        StatisticsType.distanceTotal,
    ],
    includeEnhancedPermissions: false,  // Set true for workout/sleep records
);
```

**CRITICAL**: You must call `requestHealthPermissions()` on each app restart. The SDK needs to re-validate permissions every time the app launches. This is not optional -- skipping this call will result in data retrieval failures.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `statisticTypes` | `List<StatisticsType>` | Which health metrics to request |
| `includeEnhancedPermissions` | `bool` | If `true`, also requests permissions for detailed records (workouts, sleep stages, etc.) |
| `includeBackgroundDelivery` | `bool` | If `true`, requests background delivery permissions (see Background Delivery section) |

### Step 3: Retrieve Data

Two methods for data retrieval:

#### getStatistics() - Aggregated Data

Returns aggregated health statistics (daily totals, hourly breakdowns).

```dart
final statistics = await connection.getStatistics(
    statisticTypes: [StatisticsType.steps],
    interval: StatisticsInterval.day,
    from: DateTime.now().subtract(Duration(days: 7)),
    to: DateTime.now(),
);

for (final stat in statistics) {
    print("${stat.date}: ${stat.value} ${stat.unit}");
}
```

**Android limitation**: `getStatistics()` can retrieve a maximum of 90 days of data on Android (Health Connect restriction). iOS has no such limit.

#### getRecords() - Detailed Records

Returns detailed health records with per-metric granularity (workout sessions, sleep stages, heart rate samples, etc.).

```dart
final records = await connection.getRecords(
    metricTypes: [MetricType.heartrate],
    from: DateTime.now().subtract(Duration(days: 1)),
    to: DateTime.now(),
);

for (final record in records) {
    print("${record.startDate} - ${record.endDate}: ${record.value}");
}
```

---

## Enums Reference

### StatisticsType

Used with `requestHealthPermissions()` and `getStatistics()` for aggregated health data.

| Value | Description |
|-------|-------------|
| `StatisticsType.steps` | Step count |
| `StatisticsType.distanceTotal` | Total distance (all activities) |
| `StatisticsType.distanceWalking` | Walking distance |
| `StatisticsType.distanceCycling` | Cycling distance |
| `StatisticsType.distanceRunning` | Running distance |
| `StatisticsType.caloriesBurnedTotal` | Total calories burned (active + basal) |
| `StatisticsType.caloriesBurnedBasal` | Basal metabolic calories |
| `StatisticsType.caloriesBurnedActive` | Active calories burned during activity |

### StatisticsInterval

Controls the time granularity of `getStatistics()` results.

| Value | Description |
|-------|-------------|
| `StatisticsInterval.hour` | Hourly aggregation |
| `StatisticsInterval.day` | Daily aggregation |

### MetricType

Used with `getRecords()` for detailed per-metric data. Over 30 types available:

| Value | Category | Description |
|-------|----------|-------------|
| `MetricType.heartrate` | Heart | Heart rate (bpm) |
| `MetricType.heartrateMin` | Heart | Minimum heart rate |
| `MetricType.heartrateMax` | Heart | Maximum heart rate |
| `MetricType.heartrateAvg` | Heart | Average heart rate |
| `MetricType.heartrateResting` | Heart | Resting heart rate |
| `MetricType.heartrateVariability` | Heart | Heart rate variability (HRV) |
| `MetricType.elevation` | Activity | Elevation gained |
| `MetricType.elevationMin` | Activity | Minimum elevation |
| `MetricType.elevationMax` | Activity | Maximum elevation |
| `MetricType.elevationGain` | Activity | Total elevation gain |
| `MetricType.elevationLoss` | Activity | Total elevation loss |
| `MetricType.calories` | Energy | Calories burned |
| `MetricType.caloriesActive` | Energy | Active calories |
| `MetricType.caloriesBasal` | Energy | Basal calories |
| `MetricType.distance` | Activity | Distance covered |
| `MetricType.distanceWalking` | Activity | Walking distance |
| `MetricType.distanceRunning` | Activity | Running distance |
| `MetricType.distanceCycling` | Activity | Cycling distance |
| `MetricType.speed` | Activity | Speed |
| `MetricType.speedMin` | Activity | Minimum speed |
| `MetricType.speedMax` | Activity | Maximum speed |
| `MetricType.speedAvg` | Activity | Average speed |
| `MetricType.spo2` | Vitals | Blood oxygen saturation |
| `MetricType.spo2Min` | Vitals | Minimum SpO2 |
| `MetricType.spo2Max` | Vitals | Maximum SpO2 |
| `MetricType.spo2Avg` | Vitals | Average SpO2 |
| `MetricType.temperature` | Vitals | Body temperature |
| `MetricType.temperatureMin` | Vitals | Minimum temperature |
| `MetricType.temperatureMax` | Vitals | Maximum temperature |
| `MetricType.temperatureAvg` | Vitals | Average temperature |
| `MetricType.respiratoryRate` | Vitals | Respiratory rate |
| `MetricType.bloodPressureSystolic` | Vitals | Systolic blood pressure |
| `MetricType.bloodPressureDiastolic` | Vitals | Diastolic blood pressure |
| `MetricType.bloodGlucose` | Vitals | Blood glucose level |
| `MetricType.power` | Activity | Power output (watts) |
| `MetricType.cadence` | Activity | Cadence (steps/min or rpm) |
| `MetricType.floors` | Activity | Floors climbed |

### Provider

Identifies the data source provider.

| Value | Description |
|-------|-------------|
| `Provider.apple` | Apple HealthKit (iOS, SDK-based) |
| `Provider.healthConnect` | Google Health Connect (Android, SDK-based) |
| `Provider.samsungHealthData` | Samsung Health Data (Android, SDK-based) |
| `Provider.garmin` | Garmin (OAuth) |
| `Provider.fitbit` | Fitbit (OAuth) |
| `Provider.oura` | Oura Ring (OAuth) |
| `Provider.whoop` | WHOOP (OAuth) |
| `Provider.polar` | Polar (OAuth) |
| `Provider.strava` | Strava (OAuth) |
| `Provider.withings` | Withings (OAuth) |
| `Provider.suunto` | Suunto (OAuth) |
| `Provider.huawei` | Huawei Health (OAuth) |
| `Provider.coros` | COROS (OAuth) |
| `Provider.ultrahuman` | Ultrahuman (OAuth) |
| `Provider.dexcom` | Dexcom (OAuth) |
| `Provider.freestyleLibre` | Freestyle Libre (OAuth) |
| `Provider.omronEu` | Omron EU (OAuth) |
| `Provider.omronUs` | Omron US (OAuth) |
| `Provider.luna` | Luna (OAuth) |

### ActivityTag

Labels for workout/activity types returned in records.

| Value | Description |
|-------|-------------|
| `ActivityTag.walking` | Walking |
| `ActivityTag.running` | Running |
| `ActivityTag.cycling` | Cycling |
| `ActivityTag.swimming` | Swimming |
| `ActivityTag.yoga` | Yoga |
| `ActivityTag.hiking` | Hiking |
| `ActivityTag.strengthTraining` | Strength/weight training |
| `ActivityTag.hiit` | High-intensity interval training |
| `ActivityTag.pilates` | Pilates |
| `ActivityTag.rowing` | Rowing |
| `ActivityTag.elliptical` | Elliptical trainer |
| `ActivityTag.stairClimbing` | Stair climbing |
| `ActivityTag.crossFit` | CrossFit |
| `ActivityTag.dance` | Dance |
| `ActivityTag.martialArts` | Martial arts |
| `ActivityTag.skiing` | Skiing |
| `ActivityTag.snowboarding` | Snowboarding |
| `ActivityTag.surfing` | Surfing |
| `ActivityTag.tennis` | Tennis |
| `ActivityTag.basketball` | Basketball |
| `ActivityTag.soccer` | Soccer/Football |
| `ActivityTag.golf` | Golf |
| `ActivityTag.other` | Other/unclassified activity |

---

## Background Delivery

Background delivery allows your app to receive health data updates even when the app is not in the foreground. The data is synced to Spike servers automatically.

### iOS Background Delivery

**Frequency**: iOS limits background delivery to **once per hour** maximum.

**Setup:**

1. In Xcode, ensure the **HealthKit Background Delivery** capability is enabled under **Signing & Capabilities > HealthKit**.

2. In your `AppDelegate.swift`, call `Spike.configure()` to register background handlers:

```swift
// ios/Runner/AppDelegate.swift
import UIKit
import Flutter
import spike_flutter_sdk  // Import the Spike SDK

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
    override func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        // Configure Spike background delivery BEFORE GeneratedPluginRegistrant
        Spike.configure()

        GeneratedPluginRegistrant.register(with: self)
        return super.application(application, didFinishLaunchingWithOptions: launchOptions)
    }
}
```

### Android Background Delivery

Add the background health data permission in `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.health.READ_HEALTH_DATA_IN_BACKGROUND" />
```

### Flutter: Enable Background Delivery

In your Flutter code, request permissions with background delivery enabled, then call `enableBackgroundDelivery()`:

```dart
// Step 1: Request permissions with background delivery flag
await connection.requestHealthPermissions(
    statisticTypes: [
        StatisticsType.steps,
        StatisticsType.caloriesBurnedActive,
    ],
    includeEnhancedPermissions: false,
    includeBackgroundDelivery: true,  // Request background permission
);

// Step 2: Enable background delivery for specific types
await connection.enableBackgroundDelivery(
    statisticTypes: [
        StatisticsType.steps,
        StatisticsType.caloriesBurnedActive,
    ],
);
```

**CRITICAL**: Each call to `enableBackgroundDelivery()` completely OVERWRITES the previous background delivery configuration. If you first enable background delivery for `steps`, then later call it again for `caloriesBurnedActive` only, you will LOSE background delivery for `steps`. Always pass the complete list of all types you want delivered in the background in a single call.

```dart
// WRONG - second call overwrites first
await connection.enableBackgroundDelivery(statisticTypes: [StatisticsType.steps]);
await connection.enableBackgroundDelivery(statisticTypes: [StatisticsType.caloriesBurnedActive]);
// Result: only caloriesBurnedActive has background delivery

// CORRECT - single call with all types
await connection.enableBackgroundDelivery(
    statisticTypes: [
        StatisticsType.steps,
        StatisticsType.caloriesBurnedActive,
    ],
);
// Result: both types have background delivery
```

---

## Samsung Health Data

Samsung Health Data integration is available on Android only. It provides access to Samsung Health metrics beyond what Google Health Connect offers.

### Requirements

| Requirement | Value |
|-------------|-------|
| **Platform** | Android only |
| **Minimum API** | Android 10 (API 29+) |
| **Samsung Health App** | Version 6.30.2 or later |
| **SDK Provider** | Samsung Health Data SDK |

### Integration Flow

#### 1. Check Availability

```dart
final isAvailable = await connection.checkSamsungHealthDataAvailability();
if (isAvailable) {
    // Samsung Health Data is available on this device
}
```

#### 2. Enable Integration

```dart
await connection.enableSamsungHealthDataIntegration();
```

#### 3. Request Samsung Health Permissions

```dart
await connection.requestSamsungHealthDataPermissions();
```

This opens the Samsung Health permission dialog. The user must grant access to the requested data types.

### Developer Mode Setup (for Testing)

During development, Samsung Health Data SDK requires developer mode to be enabled on the device:

1. Open Samsung Health app on the device
2. Go to **Settings** (gear icon)
3. Scroll to the bottom and tap the version number multiple times until "Developer mode enabled" appears
4. Return to settings -- a new **Developer options** menu item appears
5. Enable **Developer mode** toggle

Without developer mode enabled, Samsung Health Data API calls will fail during testing.

### Production: Samsung Partnership Agreement

For production deployment, Samsung Health Data integration requires a **Samsung Partnership Agreement**. Without this agreement, the SDK will not work on end-user devices outside developer mode.

Contact Samsung Developer Relations to establish a partnership agreement before releasing your app with Samsung Health Data features.

---

## Nutrition AI

The Spike SDK includes on-device nutrition analysis powered by AI. It can analyze food images to identify dishes, estimate nutritional content, and track meals.

### Methods

#### analyzeNutrition() - Synchronous Analysis

Analyzes a food image and returns nutritional data immediately.

```dart
final result = await connection.analyzeNutrition(
    imageData: imageBytes,           // Uint8List of image data
    config: NutritionalAnalysisConfig(
        analysisMode: AnalysisMode.auto,
        countryCode: "US",
        languageCode: "en",
        includeNutriScore: true,
        includeDishDescription: true,
        includeIngredients: true,
        includeNutritionFields: [
            NutritionalField.calories,
            NutritionalField.protein,
            NutritionalField.carbs,
            NutritionalField.fat,
        ],
    ),
);
```

#### submitNutritionForAnalysis() - Asynchronous Analysis

Submits a food image for background analysis. Returns a `recordId` that can be polled for results.

```dart
final recordId = await connection.submitNutritionForAnalysis(
    imageData: imageBytes,
    config: NutritionalAnalysisConfig(
        analysisMode: AnalysisMode.auto,
        countryCode: "US",
        languageCode: "en",
    ),
);

// Poll for results later
final record = await connection.getNutritionRecord(recordId: recordId);
```

#### getNutritionRecords() - List by Date Range

```dart
final records = await connection.getNutritionRecords(
    from: DateTime.now().subtract(Duration(days: 7)),
    to: DateTime.now(),
);

for (final record in records) {
    print("${record.dishDescription}: ${record.calories} kcal");
}
```

#### getNutritionRecord() - Get by ID

```dart
final record = await connection.getNutritionRecord(
    recordId: "record-uuid-here",
);
```

#### updateNutritionRecordServingSize() - Adjust Portions

```dart
await connection.updateNutritionRecordServingSize(
    recordId: "record-uuid-here",
    servingSize: 1.5,  // Multiplier (1.5 = 150% of detected portion)
);
```

#### deleteNutritionRecord() - Remove Record

```dart
await connection.deleteNutritionRecord(
    recordId: "record-uuid-here",
);
```

### NutritionalAnalysisConfig

| Field | Type | Description |
|-------|------|-------------|
| `analysisMode` | `AnalysisMode` | `auto`, `foodLabel`, or `foodImage` |
| `countryCode` | `String` | ISO 3166-1 alpha-2 country code (e.g., `"US"`, `"GB"`) |
| `languageCode` | `String` | ISO 639-1 language code (e.g., `"en"`, `"es"`) |
| `includeNutriScore` | `bool` | Include Nutri-Score rating (A-E) |
| `includeDishDescription` | `bool` | Include AI-generated dish description |
| `includeIngredients` | `bool` | Include detected ingredient list |
| `includeNutritionFields` | `List<NutritionalField>` | Which nutritional fields to return |

### NutritionRecord

Key fields returned in a `NutritionRecord`:

| Field | Type | Description |
|-------|------|-------------|
| `recordId` | `String` | Unique record identifier |
| `status` | `NutritionRecordStatus` | Processing status |
| `dishDescription` | `String?` | AI-generated description of the dish |
| `ingredients` | `List<String>?` | Detected ingredients |
| `nutriScore` | `String?` | Nutri-Score rating (A-E) |
| `servingSize` | `double` | Serving size multiplier |
| `nutritionalFields` | `Map<NutritionalField, double>` | Nutritional values |
| `imageUrl` | `String?` | URL to the analyzed image |
| `createdAt` | `DateTime` | Timestamp of analysis |

### NutritionalField Enum (29 values)

| Value | Unit | Description |
|-------|------|-------------|
| `NutritionalField.calories` | kcal | Total calories |
| `NutritionalField.protein` | g | Protein |
| `NutritionalField.carbs` | g | Total carbohydrates |
| `NutritionalField.fat` | g | Total fat |
| `NutritionalField.saturatedFat` | g | Saturated fat |
| `NutritionalField.unsaturatedFat` | g | Unsaturated fat |
| `NutritionalField.transFat` | g | Trans fat |
| `NutritionalField.monounsaturatedFat` | g | Monounsaturated fat |
| `NutritionalField.polyunsaturatedFat` | g | Polyunsaturated fat |
| `NutritionalField.cholesterol` | mg | Cholesterol |
| `NutritionalField.sodium` | mg | Sodium |
| `NutritionalField.potassium` | mg | Potassium |
| `NutritionalField.fiber` | g | Dietary fiber |
| `NutritionalField.sugar` | g | Total sugars |
| `NutritionalField.addedSugar` | g | Added sugars |
| `NutritionalField.vitaminA` | mcg | Vitamin A |
| `NutritionalField.vitaminC` | mg | Vitamin C |
| `NutritionalField.vitaminD` | mcg | Vitamin D |
| `NutritionalField.vitaminE` | mg | Vitamin E |
| `NutritionalField.vitaminK` | mcg | Vitamin K |
| `NutritionalField.vitaminB6` | mg | Vitamin B6 |
| `NutritionalField.vitaminB12` | mcg | Vitamin B12 |
| `NutritionalField.calcium` | mg | Calcium |
| `NutritionalField.iron` | mg | Iron |
| `NutritionalField.magnesium` | mg | Magnesium |
| `NutritionalField.zinc` | mg | Zinc |
| `NutritionalField.caffeine` | mg | Caffeine |
| `NutritionalField.water` | ml | Water content |
| `NutritionalField.alcohol` | g | Alcohol content |

### NutritionRecordStatus Enum

| Value | Description |
|-------|-------------|
| `NutritionRecordStatus.pending` | Analysis submitted, not yet processed |
| `NutritionRecordStatus.processing` | Analysis in progress |
| `NutritionRecordStatus.completed` | Analysis complete, results available |
| `NutritionRecordStatus.failed` | Analysis failed |

### Image Requirements

| Constraint | Value |
|------------|-------|
| **Minimum Resolution** | 512 x 512 pixels |
| **Maximum File Size** | 10 MB |
| **Supported Formats** | JPEG, PNG, WebP |

Images below 512x512 will be rejected. For best results, use well-lit images with the food clearly visible and centered in the frame.

---

## Logging

Enable SDK logging for debugging during development:

```dart
await SpikeSDKV3.setLogCallback(
    callback: (level, message) {
        log("[SpikeSDK.${level.toJson()}] $message");
    },
);
```

Log levels include `debug`, `info`, `warning`, and `error`. The callback receives every SDK log message, which is useful for diagnosing permission issues, data retrieval failures, and connection problems.

**Recommendation**: Enable logging during development and disable or filter in production to avoid excessive log output.

---

## Troubleshooting

### Permission Dialog Not Appearing (Android)

**Symptom**: `requestHealthPermissions()` completes without showing the Health Connect permission dialog.

**Fix**: Remove `android:launchMode="singleTop"` from the `<activity>` tag of your `MainActivity` in `AndroidManifest.xml`. This launch mode can interfere with the Health Connect permission activity lifecycle.

```xml
<!-- Remove this attribute if present -->
<activity
    android:name=".MainActivity"
    android:launchMode="singleTop"  <!-- REMOVE THIS LINE -->
    ...
```

### Build Failure: Manifest Merge Conflict

**Symptom**: Build fails with `Manifest merger failed` or label conflict errors.

**Fix**: Add `tools:replace="android:label"` to your `<application>` tag in `AndroidManifest.xml`:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <application
        android:label="YourApp"
        tools:replace="android:label"
        ...>
```

### Firebase BOM Compatibility

**Symptom**: Dependency resolution failures when using Firebase alongside the Spike SDK.

**Supported**: Firebase BOM versions up to **33.x.x** are compatible with the Spike Flutter SDK.

If you encounter dependency conflicts, check your Firebase BOM version in `android/app/build.gradle`:

```groovy
dependencies {
    implementation platform('com.google.firebase:firebase-bom:33.7.0')  // Compatible
    // Versions 34.x.x and above may cause conflicts
}
```

### Data Not Returning After Permission Grant

**Symptom**: `getStatistics()` or `getRecords()` returns empty results even after permissions are granted.

**Causes and fixes**:

1. **Missing `requestHealthPermissions()` on restart**: You must call `requestHealthPermissions()` every time the app starts, not just once. The SDK re-validates permissions on each call.

2. **No data in Health Connect/HealthKit**: The user may not have any data for the requested time range. Check with the native Health app to confirm data exists.

3. **Android 90-day limit**: `getStatistics()` on Android can only retrieve the last 90 days of data. Request a shorter date range.

### Samsung Health Data Not Working

**Symptom**: `checkSamsungHealthDataAvailability()` returns `false` or permission calls fail.

**Checklist**:

1. Confirm device is a Samsung device running Android 10+ (API 29+)
2. Confirm Samsung Health app is installed and updated to version 6.30.2+
3. For testing: enable Developer mode in Samsung Health settings
4. For production: ensure Samsung Partnership Agreement is in place
