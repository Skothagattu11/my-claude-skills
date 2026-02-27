# Spike API - Lab Reports

## Overview

Spike's Lab Reports service provides AI-powered extraction and standardization of medical lab results from uploaded documents. The service performs OCR on lab report images or PDFs, maps extracted biomarkers to the **LOINC (Logical Observation Identifiers Names and Codes)** standard, and applies **HIPAA-compliant de-identification** of patient information.

Key capabilities:
- Upload base64-encoded lab documents (PDF, JPEG, PNG, GIF, WebP)
- AI-powered OCR with intelligent field extraction
- Automatic LOINC standard code mapping for interoperability
- Normal range flagging (high/low/normal)
- HIPAA-compliant patient information de-identification
- Sync and async processing modes

---

## Document Requirements

| Requirement | Specification |
|-------------|---------------|
| Supported formats | PDF (preferred), JPEG, PNG, GIF, WebP |
| Maximum file size | 10 MB |
| Recommended resolution | 300+ DPI for optimal OCR accuracy |
| Encoding | Base64-encoded in request body |
| Best format | PDF - preserves text layers for higher accuracy |

**Tips for best results:**
- Use PDF format whenever possible (native text extraction is more reliable than OCR)
- Scan at 300 DPI or higher
- Ensure documents are not rotated or skewed
- Avoid cropped or partial lab reports
- Multi-page documents are supported

---

## Processing Pipeline

The lab report processing follows a 5-stage pipeline:

```
1. Validation
   ├── Format check (PDF/JPEG/PNG/GIF/WebP)
   ├── Size check (< 10MB)
   └── Base64 decode verification

2. OCR (Optical Character Recognition)
   ├── Text layer extraction (PDF)
   └── Image-based OCR (raster formats)

3. AI Analysis
   ├── Section identification (CBC, metabolic panel, lipid panel, etc.)
   ├── Biomarker extraction (name, value, unit, reference range)
   └── Confidence scoring per result

4. LOINC Mapping
   ├── Biomarker name normalization
   ├── LOINC code assignment (e.g., "Glucose" → 2345-7)
   └── Standard unit mapping

5. HIPAA De-identification
   ├── Patient name removal
   ├── Date of birth redaction
   ├── Address/phone/SSN removal
   └── Medical record number anonymization
```

**Processing time:** 10-60 seconds depending on document length and complexity.

---

## Endpoints

### POST /lab_reports

Upload a lab report document for analysis.

**Request Body:**

```json
{
  "document_base64": "<base64-encoded-document>",
  "document_type": "pdf",
  "wait_on_process": true
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `document_base64` | string | Yes | Base64-encoded document content |
| `document_type` | string | Yes | File format: `pdf`, `jpeg`, `png`, `gif`, `webp` |
| `wait_on_process` | boolean | No | `true` = synchronous, `false` = async with webhook notification |

**Synchronous Response (wait_on_process: true):**

```json
{
  "id": "lab_abc123",
  "status": "completed",
  "created_at": "2026-02-27T10:00:00Z",
  "processed_at": "2026-02-27T10:00:35Z",
  "sections": [
    {
      "name": "Complete Blood Count (CBC)",
      "results": [
        {
          "biomarker": "White Blood Cells",
          "value": 6.8,
          "unit": "10^3/uL",
          "reference_range": {
            "low": 4.5,
            "high": 11.0
          },
          "flag": "normal",
          "loinc_code": "6690-2",
          "loinc_name": "Leukocytes [#/volume] in Blood by Automated count",
          "confidence": 0.97
        },
        {
          "biomarker": "Red Blood Cells",
          "value": 4.9,
          "unit": "10^6/uL",
          "reference_range": {
            "low": 4.5,
            "high": 5.5
          },
          "flag": "normal",
          "loinc_code": "789-8",
          "loinc_name": "Erythrocytes [#/volume] in Blood by Automated count",
          "confidence": 0.95
        },
        {
          "biomarker": "Hemoglobin",
          "value": 14.2,
          "unit": "g/dL",
          "reference_range": {
            "low": 13.5,
            "high": 17.5
          },
          "flag": "normal",
          "loinc_code": "718-7",
          "loinc_name": "Hemoglobin [Mass/volume] in Blood",
          "confidence": 0.98
        }
      ]
    },
    {
      "name": "Comprehensive Metabolic Panel",
      "results": [
        {
          "biomarker": "Glucose",
          "value": 105,
          "unit": "mg/dL",
          "reference_range": {
            "low": 70,
            "high": 100
          },
          "flag": "high",
          "loinc_code": "2345-7",
          "loinc_name": "Glucose [Mass/volume] in Serum or Plasma",
          "confidence": 0.96
        },
        {
          "biomarker": "Creatinine",
          "value": 0.9,
          "unit": "mg/dL",
          "reference_range": {
            "low": 0.6,
            "high": 1.2
          },
          "flag": "normal",
          "loinc_code": "2160-0",
          "loinc_name": "Creatinine [Mass/volume] in Serum or Plasma",
          "confidence": 0.94
        }
      ]
    }
  ],
  "patient_info": {
    "de_identified": true,
    "note": "Patient identifying information has been removed per HIPAA compliance"
  },
  "metadata": {
    "lab_name": "Quest Diagnostics",
    "report_date": "2026-02-25",
    "page_count": 3,
    "total_results": 24,
    "flagged_results": 2
  }
}
```

**Asynchronous Response (wait_on_process: false):**

```json
{
  "id": "lab_abc123",
  "status": "pending",
  "created_at": "2026-02-27T10:00:00Z"
}
```

### GET /lab_reports

List all lab reports for the authenticated user.

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | integer | Max records to return (default: 20) |
| `offset` | integer | Pagination offset |
| `status` | string | Filter by status: `pending`, `processing`, `completed`, `failed` |

**Response:**

```json
{
  "data": [
    {
      "id": "lab_abc123",
      "status": "completed",
      "created_at": "2026-02-27T10:00:00Z",
      "processed_at": "2026-02-27T10:00:35Z",
      "metadata": {
        "lab_name": "Quest Diagnostics",
        "report_date": "2026-02-25",
        "total_results": 24,
        "flagged_results": 2
      }
    }
  ],
  "total": 5,
  "limit": 20,
  "offset": 0
}
```

### GET /lab_reports/{id}

Retrieve a specific lab report by ID with full results.

---

## Response Schema

### Lab Report Object

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Unique report identifier |
| `status` | string | `pending`, `processing`, `completed`, `failed` |
| `created_at` | string (ISO 8601) | Upload timestamp |
| `processed_at` | string (ISO 8601) | Completion timestamp |
| `sections` | array | Array of lab sections |
| `patient_info` | object | De-identification status |
| `metadata` | object | Report metadata |

### Section Object

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Section name (e.g., "Complete Blood Count (CBC)") |
| `results` | array | Array of biomarker results |

### Result Object

| Field | Type | Description |
|-------|------|-------------|
| `biomarker` | string | Name of the biomarker as it appears on the report |
| `value` | number | Measured value |
| `unit` | string | Unit of measurement |
| `reference_range` | object | Contains `low` and `high` normal boundaries |
| `flag` | string | `"normal"`, `"high"`, `"low"`, or `"critical"` |
| `loinc_code` | string | LOINC standard code |
| `loinc_name` | string | Official LOINC name |
| `confidence` | number | AI confidence score (0.0 - 1.0) |

### Review Flags

| Flag | Description |
|------|-------------|
| `normal` | Value within reference range |
| `high` | Value above reference range upper limit |
| `low` | Value below reference range lower limit |
| `critical` | Value significantly outside range (requires immediate attention) |

---

## Sync vs Async Processing

### Synchronous (`wait_on_process: true`)

- Request blocks until processing completes
- Full report returned in the response body
- Timeout: up to 60 seconds for complex multi-page reports
- Best for: real-time user-facing uploads where the user is waiting

### Asynchronous (`wait_on_process: false`)

- Request returns immediately with `pending` status and report ID
- Results delivered via configured lab reports webhook URL
- Best for: batch processing, background uploads, mobile apps with poor connectivity

---

## Webhook Notifications

When async processing completes, a webhook is sent to the configured lab reports webhook URL:

```json
{
  "event_type": "lab_report_completed",
  "report_id": "lab_abc123",
  "user_id": "user_xyz789",
  "status": "completed",
  "timestamp": "2026-02-27T10:00:35Z",
  "data": {
    "sections": [ ... ],
    "metadata": {
      "lab_name": "Quest Diagnostics",
      "report_date": "2026-02-25",
      "total_results": 24,
      "flagged_results": 2
    }
  }
}
```

Failed processing webhook:

```json
{
  "event_type": "lab_report_failed",
  "report_id": "lab_abc123",
  "user_id": "user_xyz789",
  "status": "failed",
  "timestamp": "2026-02-27T10:00:15Z",
  "error": {
    "code": "ocr_failed",
    "message": "Unable to extract text from document. Please upload a higher resolution scan."
  }
}
```

---

## Patient Info De-identification (HIPAA Compliance)

All lab reports are automatically processed to remove Protected Health Information (PHI):

| PHI Category | Action |
|--------------|--------|
| Patient name | Removed |
| Date of birth | Removed |
| Address | Removed |
| Phone number | Removed |
| Social Security Number | Removed |
| Medical Record Number | Anonymized |
| Insurance information | Removed |

The `patient_info` field in the response always contains:
```json
{
  "de_identified": true,
  "note": "Patient identifying information has been removed per HIPAA compliance"
}
```

---

## Code Examples

### Upload a Lab Report (Flutter)

```dart
import 'dart:convert';
import 'dart:io';

class LabReportService {
  final SpikeApiClient _spikeApi;

  LabReportService(this._spikeApi);

  /// Upload a lab report PDF for analysis
  Future<LabReport> uploadLabReport(File document) async {
    // Read and encode the file
    final bytes = await document.readAsBytes();
    final base64Doc = base64Encode(bytes);

    // Determine document type from extension
    final extension = document.path.split('.').last.toLowerCase();
    final docType = switch (extension) {
      'pdf' => 'pdf',
      'jpg' || 'jpeg' => 'jpeg',
      'png' => 'png',
      'gif' => 'gif',
      'webp' => 'webp',
      _ => throw Exception('Unsupported format: $extension'),
    };

    // Validate file size
    if (bytes.length > 10 * 1024 * 1024) {
      throw Exception('Document exceeds 10MB limit');
    }

    // Submit for synchronous analysis
    final result = await _spikeApi.submitLabReport(
      documentBase64: base64Doc,
      documentType: docType,
      waitOnProcess: true,
    );

    return result;
  }

  /// Display flagged results
  void printFlaggedResults(LabReport report) {
    print('Lab Report: ${report.metadata.labName}');
    print('Date: ${report.metadata.reportDate}');
    print('Total results: ${report.metadata.totalResults}');
    print('Flagged: ${report.metadata.flaggedResults}');
    print('---');

    for (final section in report.sections) {
      final flagged = section.results.where((r) => r.flag != 'normal');
      if (flagged.isNotEmpty) {
        print('\n${section.name}:');
        for (final result in flagged) {
          print('  ${result.flag.toUpperCase()}: ${result.biomarker} = '
              '${result.value} ${result.unit} '
              '(range: ${result.referenceRange.low}-${result.referenceRange.high})');
          print('    LOINC: ${result.loincCode} - ${result.loincName}');
        }
      }
    }
  }
}
```

### Upload a Lab Report (Python)

```python
import base64
import httpx

SPIKE_API_URL = "https://app-api.spikeapi.com/v3"
AUTH_TOKEN = "your_jwt_token"

async def upload_lab_report(file_path: str, wait: bool = True) -> dict:
    """Upload a lab report document for AI analysis."""

    # Read and encode
    with open(file_path, "rb") as f:
        document_base64 = base64.b64encode(f.read()).decode("utf-8")

    # Determine type
    ext = file_path.rsplit(".", 1)[-1].lower()
    doc_type_map = {
        "pdf": "pdf",
        "jpg": "jpeg",
        "jpeg": "jpeg",
        "png": "png",
        "gif": "gif",
        "webp": "webp",
    }
    doc_type = doc_type_map.get(ext)
    if not doc_type:
        raise ValueError(f"Unsupported format: {ext}")

    async with httpx.AsyncClient() as client:
        response = await client.post(
            f"{SPIKE_API_URL}/lab_reports",
            headers={
                "Authorization": f"Bearer {AUTH_TOKEN}",
                "Content-Type": "application/json",
            },
            json={
                "document_base64": document_base64,
                "document_type": doc_type,
                "wait_on_process": wait,
            },
            timeout=90.0,  # Up to 60s processing + network
        )
        response.raise_for_status()
        return response.json()


async def get_flagged_results(report_id: str) -> list:
    """Retrieve only flagged (abnormal) results from a lab report."""
    async with httpx.AsyncClient() as client:
        response = await client.get(
            f"{SPIKE_API_URL}/lab_reports/{report_id}",
            headers={"Authorization": f"Bearer {AUTH_TOKEN}"},
        )
        response.raise_for_status()
        report = response.json()

    flagged = []
    for section in report["sections"]:
        for result in section["results"]:
            if result["flag"] != "normal":
                flagged.append({
                    "section": section["name"],
                    "biomarker": result["biomarker"],
                    "value": result["value"],
                    "unit": result["unit"],
                    "flag": result["flag"],
                    "range": f"{result['reference_range']['low']}-{result['reference_range']['high']}",
                    "loinc_code": result["loinc_code"],
                })

    return flagged
```

### Async Upload with Webhook (Python)

```python
async def upload_lab_report_async(file_path: str) -> str:
    """Upload a lab report for async processing. Returns report ID."""
    result = await upload_lab_report(file_path, wait=False)
    report_id = result["id"]
    print(f"Lab report submitted: {report_id} (status: {result['status']})")
    print("Results will be delivered via webhook.")
    return report_id
```

### Polling for Results (Flutter)

```dart
/// Poll for lab report results (alternative to webhooks)
Future<LabReport> waitForLabReport(String reportId, {
  Duration timeout = const Duration(minutes: 2),
  Duration interval = const Duration(seconds: 5),
}) async {
  final deadline = DateTime.now().add(timeout);

  while (DateTime.now().isBefore(deadline)) {
    final report = await _spikeApi.getLabReport(reportId);

    if (report.status == 'completed') {
      return report;
    } else if (report.status == 'failed') {
      throw Exception('Lab report processing failed');
    }

    await Future.delayed(interval);
  }

  throw TimeoutException('Lab report processing timed out');
}
```
