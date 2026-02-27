# Spike API - Providers and Metrics

## Overview

Spike integrates with **19 health data providers** and normalizes data into a unified schema of **127+ metrics** across multiple categories. This document covers the complete provider matrix, metrics catalog, activity types, and data aggregation methods.

---

## Provider Matrix

### All Providers

| # | Provider | Slug | Integration Type | Backfill Limit | Key Metrics |
|---|----------|------|-----------------|----------------|-------------|
| 1 | Apple Health | `apple_health` | SDK | 90 days | All categories via HealthKit |
| 2 | Google Fit | `google_fit` | OAuth | 90 days | Activity, body composition, heart rate |
| 3 | Samsung Health | `samsung_health` | SDK | 90 days | Activity, sleep, heart rate, body |
| 4 | WHOOP | `whoop` | OAuth | 90 days | Recovery, strain, sleep, HRV |
| 5 | Oura | `oura` | OAuth | 90 days | Sleep, readiness, activity, temperature |
| 6 | Fitbit | `fitbit` | OAuth | 30 days | Activity, sleep, heart rate, SpO2 |
| 7 | Garmin | `garmin` | OAuth | 90 days | Activity, sleep, stress, body battery |
| 8 | Withings | `withings` | OAuth | 90 days | Body composition, blood pressure, sleep |
| 9 | Polar | `polar` | OAuth | 90 days | Activity, sleep, heart rate, training load |
| 10 | Suunto | `suunto` | OAuth | 90 days | Activity, workouts, heart rate |
| 11 | Coros | `coros` | OAuth | 90 days | Activity, sleep, workouts |
| 12 | Peloton | `peloton` | OAuth | 90 days | Workouts, heart rate, output |
| 13 | Strava | `strava` | OAuth | 90 days | Activities, segments, power |
| 14 | Zwift | `zwift` | OAuth | 90 days | Cycling workouts, power, cadence |
| 15 | Eight Sleep | `eight_sleep` | OAuth | 90 days | Sleep, bed temperature, HRV |
| 16 | Dexcom | `dexcom` | OAuth | 90 days | Continuous glucose monitoring |
| 17 | Freestyle Libre | `freestyle_libre` | OAuth | 90 days | Glucose readings |
| 18 | Cronometer | `cronometer` | OAuth | 90 days | Nutrition, macros, micronutrients |
| 19 | MyFitnessPal | `myfitnesspal` | OAuth | 30 days | Nutrition, calories, macros |

### Provider Categories

#### OAuth-Based Providers

These providers use standard OAuth 2.0 flow. Users authorize access through the provider's consent screen, and Spike manages token refresh automatically.

**Flow:**
1. Redirect user to Spike connect URL
2. User authenticates with provider
3. Provider redirects back to your app
4. Spike receives tokens and begins data sync
5. Webhook notification sent on initial data availability

**Providers:** WHOOP, Oura, Fitbit, Garmin, Withings, Polar, Suunto, Coros, Peloton, Strava, Zwift, Eight Sleep, Dexcom, Freestyle Libre, Cronometer, MyFitnessPal, Google Fit

#### SDK-Based Providers

These providers require the Spike SDK integrated into your mobile app. Data is read from the device's health framework (HealthKit, Health Connect, Samsung Health SDK).

**Flow:**
1. User grants health permissions in your app
2. Spike SDK reads data from device health store
3. Data uploaded to Spike servers
4. Webhook notification sent on data availability

**Providers:** Apple Health, Samsung Health

---

## Metrics Catalog

### Activity Metrics

| Metric | Unit | Precision | Description | Key Providers |
|--------|------|-----------|-------------|---------------|
| `steps` | count | integer | Total step count | All |
| `distance_m` | meters | 1 decimal | Distance covered | All |
| `active_calories_kcal` | kcal | 1 decimal | Active energy expenditure | All |
| `total_calories_kcal` | kcal | 1 decimal | Total energy expenditure (active + basal) | All |
| `floors_climbed` | count | integer | Floors/flights climbed | Apple, Fitbit, Garmin, Samsung |
| `active_minutes` | minutes | integer | Minutes of moderate-to-vigorous activity | All |
| `sedentary_minutes` | minutes | integer | Minutes of sedentary time | Fitbit, Garmin, Apple |
| `lightly_active_minutes` | minutes | integer | Minutes of light activity | Fitbit, Garmin |
| `moderately_active_minutes` | minutes | integer | Minutes of moderate activity | Fitbit, Garmin |
| `vigorously_active_minutes` | minutes | integer | Minutes of vigorous activity | Fitbit, Garmin |
| `elevation_gain_m` | meters | 1 decimal | Total elevation gained | Garmin, Strava, Suunto, Coros |
| `stand_hours` | count | integer | Hours with standing activity | Apple |
| `move_minutes` | minutes | integer | Google Fit move minutes | Google Fit |
| `exercise_minutes` | minutes | integer | Minutes of exercise | Apple, Samsung |

### Cardiovascular Metrics

| Metric | Unit | Precision | Description | Key Providers |
|--------|------|-----------|-------------|---------------|
| `heart_rate_bpm` | bpm | integer | Current/spot heart rate | All wearables |
| `heart_rate_resting_bpm` | bpm | integer | Resting heart rate | All wearables |
| `heart_rate_max_bpm` | bpm | integer | Maximum heart rate (during activity) | WHOOP, Garmin, Polar |
| `heart_rate_min_bpm` | bpm | integer | Minimum heart rate | WHOOP, Oura, Garmin |
| `heart_rate_avg_bpm` | bpm | integer | Average heart rate | All wearables |
| `hrv_ms` | milliseconds | 1 decimal | Heart rate variability (RMSSD) | WHOOP, Oura, Garmin, Apple, Polar |
| `hrv_sdnn_ms` | milliseconds | 1 decimal | HRV SDNN method | Garmin, Polar |
| `blood_pressure_systolic_mmhg` | mmHg | integer | Systolic blood pressure | Withings |
| `blood_pressure_diastolic_mmhg` | mmHg | integer | Diastolic blood pressure | Withings |
| `blood_oxygen_pct` | percent | 1 decimal | Blood oxygen saturation (SpO2) | WHOOP, Oura, Garmin, Fitbit, Apple |
| `vo2_max_ml_kg_min` | mL/kg/min | 1 decimal | Estimated VO2 max | Garmin, Apple, WHOOP, Polar |
| `respiratory_rate_brpm` | breaths/min | 1 decimal | Respiratory rate | WHOOP, Oura, Garmin, Fitbit |

### Sleep Metrics

| Metric | Unit | Precision | Description | Key Providers |
|--------|------|-----------|-------------|---------------|
| `sleep_duration_min` | minutes | integer | Total time in bed | All wearables |
| `sleep_efficiency_pct` | percent | 1 decimal | Sleep efficiency (time asleep / time in bed) | WHOOP, Oura, Fitbit, Garmin |
| `sleep_latency_min` | minutes | integer | Time to fall asleep | Oura, Fitbit, Garmin |
| `sleep_deep_min` | minutes | integer | Deep (slow wave) sleep duration | All wearables |
| `sleep_rem_min` | minutes | integer | REM sleep duration | All wearables |
| `sleep_light_min` | minutes | integer | Light sleep duration | All wearables |
| `sleep_awake_min` | minutes | integer | Awake time during sleep period | All wearables |
| `sleep_disturbances` | count | integer | Number of sleep disturbances | WHOOP, Oura |
| `sleep_score` | score (0-100) | integer | Composite sleep quality score | Oura, Fitbit, Garmin, Eight Sleep |
| `sleep_start` | ISO 8601 | datetime | Sleep onset time | All wearables |
| `sleep_end` | ISO 8601 | datetime | Wake time | All wearables |
| `skin_temperature_deviation_c` | celsius | 2 decimal | Deviation from baseline skin temp | Oura, Eight Sleep |

### Body Composition Metrics

| Metric | Unit | Precision | Description | Key Providers |
|--------|------|-----------|-------------|---------------|
| `weight_kg` | kg | 2 decimal | Body weight | Withings, Garmin, Fitbit, Apple |
| `body_fat_pct` | percent | 1 decimal | Body fat percentage | Withings, Garmin, Fitbit |
| `body_mass_index` | kg/m^2 | 1 decimal | BMI | Withings, Garmin, Fitbit |
| `lean_mass_kg` | kg | 2 decimal | Lean body mass | Withings |
| `bone_mass_kg` | kg | 2 decimal | Bone mass | Withings |
| `muscle_mass_kg` | kg | 2 decimal | Muscle mass | Withings |
| `water_pct` | percent | 1 decimal | Body water percentage | Withings |
| `height_cm` | cm | 1 decimal | Height | Apple, Google Fit |
| `waist_circumference_cm` | cm | 1 decimal | Waist circumference | Apple |

### Respiratory Metrics

| Metric | Unit | Precision | Description | Key Providers |
|--------|------|-----------|-------------|---------------|
| `respiratory_rate_brpm` | breaths/min | 1 decimal | Breathing rate | WHOOP, Oura, Garmin, Fitbit |
| `blood_oxygen_pct` | percent | 1 decimal | SpO2 | WHOOP, Oura, Garmin, Fitbit, Apple |
| `blood_oxygen_avg_pct` | percent | 1 decimal | Average SpO2 during sleep | Garmin, Fitbit |

### Energy & Recovery Metrics

| Metric | Unit | Precision | Description | Key Providers |
|--------|------|-----------|-------------|---------------|
| `recovery_score` | score (0-100) | integer | Recovery readiness | WHOOP |
| `strain_score` | score (0-21) | 1 decimal | Cardiovascular strain | WHOOP |
| `readiness_score` | score (0-100) | integer | Overall readiness | Oura |
| `stress_level` | score (0-100) | integer | Stress level | Garmin, Samsung |
| `body_battery` | score (0-100) | integer | Garmin Body Battery | Garmin |
| `training_load` | arbitrary | 1 decimal | Training load metric | Garmin, Polar |
| `training_status` | string | - | Training status label | Garmin |

### Specialized / Provider-Specific Metrics

#### WHOOP-Specific

| Metric | Unit | Description |
|--------|------|-------------|
| `whoop_recovery_score` | 0-100 | Daily recovery percentage |
| `whoop_strain` | 0-21 | Day strain score |
| `whoop_sleep_performance_pct` | percent | Sleep performance vs. need |
| `whoop_sleep_need_min` | minutes | Recommended sleep duration |
| `whoop_sleep_debt_min` | minutes | Accumulated sleep debt |
| `whoop_hrv_rmssd_ms` | ms | HRV via RMSSD |

#### Oura-Specific

| Metric | Unit | Description |
|--------|------|-------------|
| `oura_readiness_score` | 0-100 | Daily readiness composite |
| `oura_sleep_score` | 0-100 | Sleep quality composite |
| `oura_activity_score` | 0-100 | Activity composite |
| `oura_temperature_deviation_c` | celsius | Skin temperature trend |
| `oura_lowest_heart_rate_bpm` | bpm | Lowest overnight HR |

#### Garmin-Specific

| Metric | Unit | Description |
|--------|------|-------------|
| `garmin_body_battery` | 0-100 | Energy level score |
| `garmin_stress_avg` | 0-100 | Average daily stress |
| `garmin_sleep_score` | 0-100 | Sleep quality score |
| `garmin_training_load` | arbitrary | 7-day training load |
| `garmin_training_status` | string | Productive/Recovery/Overreaching |
| `garmin_intensity_minutes` | minutes | Weekly intensity minutes |

#### Glucose Metrics (Dexcom, Freestyle Libre)

| Metric | Unit | Description |
|--------|------|-------------|
| `glucose_mg_dl` | mg/dL | Current glucose reading |
| `glucose_mmol_l` | mmol/L | Current glucose (SI units) |
| `glucose_trend` | string | Rising, falling, stable |
| `glucose_avg_mg_dl` | mg/dL | Average glucose |
| `time_in_range_pct` | percent | % time in target range (70-180 mg/dL) |
| `time_below_range_pct` | percent | % time below target |
| `time_above_range_pct` | percent | % time above target |

---

## Statistics Types and Aggregation Methods

### Query Granularity

| Granularity | Endpoint | Description |
|-------------|----------|-------------|
| Hourly | `GET /statistics/hourly` | Data aggregated per hour |
| Daily | `GET /statistics/daily` | Data aggregated per day |

### Aggregation Methods

For each metric, statistics are computed using the following methods where applicable:

| Method | Description | Example |
|--------|-------------|---------|
| `sum` | Total value over the period | Steps, calories, distance |
| `avg` | Average value over the period | Heart rate, HRV, SpO2 |
| `min` | Minimum recorded value | Resting HR, lowest SpO2 |
| `max` | Maximum recorded value | Max HR during workout |
| `latest` | Most recent measurement | Weight, body fat |
| `count` | Number of data points | Glucose readings per hour |

### Time Series Merge Strategies

When a user has multiple providers reporting the same metric (e.g., Apple Health steps + Garmin steps), Spike applies merge strategies:

| Strategy | Description | Used For |
|----------|-------------|----------|
| **Priority-based** | Highest-priority provider wins per time window | Steps, calories, distance (avoids double-counting) |
| **Source-specific** | Each provider's data kept separate, queryable by source | HR, HRV, sleep (different measurement methods) |
| **Latest-wins** | Most recent measurement replaces older ones | Weight, body composition |
| **Union** | All data points kept (no overlap removal) | Workouts, activities |

Provider priority (for priority-based merge) is configurable per user but defaults to the most recently connected provider.

---

## Activity Types

### 190+ Activity Types by Category

#### Cardio (Running)

| Activity Type | Description |
|--------------|-------------|
| `running` | General running |
| `treadmill_running` | Treadmill run |
| `trail_running` | Trail running |
| `track_running` | Track running |
| `interval_running` | Interval/speed work |
| `marathon` | Marathon |
| `half_marathon` | Half marathon |
| `ultra_running` | Ultra-marathon |
| `virtual_running` | Virtual/indoor running |

#### Cardio (Cycling)

| Activity Type | Description |
|--------------|-------------|
| `cycling` | General cycling |
| `indoor_cycling` | Stationary bike |
| `mountain_biking` | Mountain biking |
| `road_cycling` | Road cycling |
| `gravel_cycling` | Gravel riding |
| `virtual_cycling` | Zwift/virtual cycling |
| `hand_cycling` | Hand cycling |
| `e_biking` | E-bike riding |
| `bmx` | BMX |
| `cyclocross` | Cyclocross |

#### Cardio (Swimming)

| Activity Type | Description |
|--------------|-------------|
| `swimming` | General swimming |
| `pool_swimming` | Pool swimming |
| `open_water_swimming` | Open water swimming |

#### Cardio (Other)

| Activity Type | Description |
|--------------|-------------|
| `walking` | Walking |
| `hiking` | Hiking |
| `rowing` | Rowing machine or on-water |
| `elliptical` | Elliptical trainer |
| `stair_climbing` | Stair climber |
| `jump_rope` | Jump rope |
| `dancing` | Dancing |
| `aerobics` | Aerobics class |
| `kickboxing` | Kickboxing |
| `boxing` | Boxing |
| `skating` | Ice/roller skating |
| `cross_country_skiing` | XC skiing |
| `downhill_skiing` | Downhill skiing |
| `snowboarding` | Snowboarding |
| `snowshoeing` | Snowshoeing |
| `kayaking` | Kayaking |
| `canoeing` | Canoeing |
| `stand_up_paddling` | SUP |
| `surfing` | Surfing |
| `water_polo` | Water polo |

#### Strength & Resistance

| Activity Type | Description |
|--------------|-------------|
| `strength_training` | General strength training |
| `weight_lifting` | Free weight lifting |
| `functional_training` | Functional fitness |
| `crossfit` | CrossFit |
| `circuit_training` | Circuit training |
| `bodyweight_training` | Calisthenics |
| `kettlebell` | Kettlebell training |
| `resistance_band` | Resistance band training |
| `machine_training` | Machine-based training |
| `powerlifting` | Powerlifting |
| `olympic_lifting` | Olympic weightlifting |

#### Flexibility & Mind-Body

| Activity Type | Description |
|--------------|-------------|
| `yoga` | Yoga |
| `pilates` | Pilates |
| `stretching` | Stretching |
| `tai_chi` | Tai Chi |
| `meditation` | Meditation |
| `breathwork` | Breathwork/breathing exercises |
| `barre` | Barre class |
| `foam_rolling` | Foam rolling/myofascial release |

#### Team Sports

| Activity Type | Description |
|--------------|-------------|
| `basketball` | Basketball |
| `soccer` | Soccer/football |
| `football` | American football |
| `baseball` | Baseball |
| `softball` | Softball |
| `volleyball` | Volleyball |
| `tennis` | Tennis |
| `badminton` | Badminton |
| `table_tennis` | Table tennis |
| `pickleball` | Pickleball |
| `squash` | Squash |
| `racquetball` | Racquetball |
| `hockey` | Hockey (ice/field) |
| `lacrosse` | Lacrosse |
| `rugby` | Rugby |
| `cricket` | Cricket |
| `handball` | Handball |
| `ultimate_frisbee` | Ultimate frisbee |

#### Outdoor & Adventure

| Activity Type | Description |
|--------------|-------------|
| `rock_climbing` | Rock climbing |
| `bouldering` | Bouldering |
| `mountaineering` | Mountaineering |
| `backpacking` | Backpacking |
| `hunting` | Hunting |
| `fishing` | Fishing |
| `horseback_riding` | Horseback riding |
| `sailing` | Sailing |
| `scuba_diving` | Scuba diving |
| `snorkeling` | Snorkeling |
| `paragliding` | Paragliding |
| `skydiving` | Skydiving |

#### HIIT & Interval

| Activity Type | Description |
|--------------|-------------|
| `hiit` | High-intensity interval training |
| `tabata` | Tabata |
| `emom` | Every minute on the minute |
| `amrap` | As many rounds as possible |
| `bootcamp` | Bootcamp class |

#### Recovery & Wellness

| Activity Type | Description |
|--------------|-------------|
| `sauna` | Sauna session |
| `cold_plunge` | Cold water immersion |
| `massage` | Massage therapy |
| `physical_therapy` | Physical therapy |
| `sleep` | Sleep (tracked as activity by some providers) |

#### Other

| Activity Type | Description |
|--------------|-------------|
| `golf` | Golf |
| `bowling` | Bowling |
| `martial_arts` | Martial arts (general) |
| `fencing` | Fencing |
| `archery` | Archery |
| `gymnastics` | Gymnastics |
| `wheelchair` | Wheelchair activity |
| `other` | Uncategorized activity |

---

## Activity Tags

21 tags that can be applied to any activity for additional classification:

| # | Tag | Description |
|---|-----|-------------|
| 1 | `outdoor` | Activity performed outdoors |
| 2 | `indoor` | Activity performed indoors |
| 3 | `competitive` | Competition/race |
| 4 | `training` | Training/practice session |
| 5 | `recovery` | Recovery-focused activity |
| 6 | `warmup` | Warm-up activity |
| 7 | `cooldown` | Cool-down activity |
| 8 | `group` | Group/class activity |
| 9 | `solo` | Solo activity |
| 10 | `coached` | Coached/guided session |
| 11 | `virtual` | Virtual/online session |
| 12 | `morning` | Morning activity |
| 13 | `afternoon` | Afternoon activity |
| 14 | `evening` | Evening activity |
| 15 | `fasted` | Performed in fasted state |
| 16 | `interval` | Contains interval segments |
| 17 | `endurance` | Endurance-focused |
| 18 | `strength` | Strength-focused |
| 19 | `flexibility` | Flexibility-focused |
| 20 | `mindfulness` | Mindfulness/meditation |
| 21 | `rehabilitation` | Rehabilitation/PT |

---

## Sleep Stages and Calculations

### Sleep Stages

| Stage | Description | Typical % of Night |
|-------|-------------|-------------------|
| `awake` | Awake periods during sleep | 5-15% |
| `light` | Light sleep (N1 + N2) | 40-60% |
| `deep` | Deep sleep (N3, slow wave) | 10-25% |
| `rem` | REM sleep (dreaming) | 15-25% |

### Sleep Calculations

| Calculation | Formula |
|-------------|---------|
| **Total sleep time** | `deep + light + rem` (excludes awake) |
| **Time in bed** | `sleep_end - sleep_start` |
| **Sleep efficiency** | `total_sleep_time / time_in_bed * 100` |
| **Sleep latency** | Time from `sleep_start` to first non-awake stage |
| **Wake after sleep onset (WASO)** | Total `awake` time after initial sleep onset |

### Sleep Stage Provider Support

| Provider | Light | Deep | REM | Awake | Sleep Score |
|----------|-------|------|-----|-------|-------------|
| WHOOP | Yes | Yes | Yes | Yes | No (has performance %) |
| Oura | Yes | Yes | Yes | Yes | Yes (0-100) |
| Fitbit | Yes | Yes | Yes | Yes | Yes (0-100) |
| Garmin | Yes | Yes | Yes | Yes | Yes (0-100) |
| Apple Health | Yes | Yes | Yes | Yes | No |
| Samsung Health | Yes | Yes | Yes | Yes | Yes (0-100) |
| Eight Sleep | Yes | Yes | Yes | Yes | Yes (0-100) |
| Polar | Yes | Yes | Yes | Yes | Yes (Polar Sleep Index) |

---

## Provider-Specific Metric Coverage Matrix

### Coverage by Category

| Provider | Activity | Heart Rate | HRV | Sleep | Body | SpO2 | Glucose | Nutrition |
|----------|----------|-----------|-----|-------|------|------|---------|-----------|
| Apple Health | Full | Full | Yes | Full | Yes | Yes | Yes* | No |
| Google Fit | Full | Full | No | Basic | Yes | No | No | No |
| Samsung Health | Full | Full | Yes | Full | Yes | Yes | No | No |
| WHOOP | Strain | Full | Yes | Full | No | Yes | No | No |
| Oura | Basic | Full | Yes | Full | No | Yes | No | No |
| Fitbit | Full | Full | Yes** | Full | Yes | Yes | No | No |
| Garmin | Full | Full | Yes | Full | Yes | Yes | No | No |
| Withings | Basic | Yes | No | Yes | Full | Yes | No | No |
| Polar | Full | Full | Yes | Full | No | No | No | No |
| Dexcom | No | No | No | No | No | No | Full | No |
| Freestyle Libre | No | No | No | No | No | No | Full | No |
| Cronometer | No | No | No | No | Yes | No | No | Full |
| MyFitnessPal | No | No | No | No | Yes | No | No | Full |

`*` Via paired CGM device
`**` Fitbit Premium required for detailed HRV

### Backfill Limits by Provider

| Provider | Max Backfill | Notes |
|----------|-------------|-------|
| Apple Health | 90 days | Configurable, depends on HealthKit data retention |
| Google Fit | 90 days | |
| WHOOP | 90 days | |
| Oura | 90 days | |
| Fitbit | 30 days | API limitation |
| Garmin | 90 days | |
| Withings | 90 days | |
| Polar | 90 days | |
| Strava | 90 days | Activities only |
| Peloton | 90 days | Workouts only |
| Eight Sleep | 90 days | |
| Dexcom | 90 days | |
| Freestyle Libre | 90 days | |
| Cronometer | 90 days | |
| MyFitnessPal | 30 days | API limitation |

**Note:** The maximum backfill can be configured lower than the provider limit in the Spike admin console (see [10-configuration.md](./10-configuration.md)). The configured value applies globally to all providers and cannot exceed 90 days.
