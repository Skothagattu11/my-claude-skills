# Spike API Authentication

## Overview

Spike uses HMAC-SHA256 signature-based authentication. No pre-registration required - users are valid after their first provider integration.

## Three-Step Flow

### Step 1: Generate HMAC Signature (Server-Side)

Sign the user ID with your HMAC key from the admin console.

**Python:**
```python
import hmac
import hashlib

def sign_user(user_id: str) -> str:
    hmac_key = b"HMAC_KEY_FROM_ADMIN_CONSOLE"
    h = hmac.new(hmac_key, user_id.encode(), hashlib.sha256)
    return h.hexdigest()
```

**JavaScript/Node.js:**
```javascript
const crypto = require("crypto");

function signUser(userId) {
  const hmacKey = "HMAC_KEY_FROM_ADMIN_CONSOLE";
  const hmac = crypto.createHmac("sha256", hmacKey);
  hmac.update(userId);
  return hmac.digest("hex");
}
```

**Dart (Flutter - for reference, should be done server-side):**
```dart
import 'dart:convert';
import 'package:crypto/crypto.dart';

String signUser(String userId, String hmacKey) {
  final key = utf8.encode(hmacKey);
  final bytes = utf8.encode(userId);
  final hmacSha256 = Hmac(sha256, key);
  final digest = hmacSha256.convert(bytes);
  return digest.toString();
}
```

### Step 2: Exchange Signature for JWT Token

**Endpoint:** `POST https://app-api.spikeapi.com/v3/auth/hmac`

**Request:**
```json
{
    "application_id": 9999,
    "application_user_id": "my_user_123",
    "signature": "HMAC_SIGNATURE_FROM_STEP_1"
}
```

**Response:**
```json
{
    "access_token": "eyJhbGciOiJIUzI1NiIs..."
}
```

**User ID Rules:**
- 1-128 characters
- Alphanumeric
- Special chars allowed: `-`, `_`, `.`
- Must be consistent throughout user's lifecycle

### Step 3: Use Bearer Token

```
Authorization: Bearer <access_token>
```

## Security Best Practices

1. HMAC key must stay server-side (NEVER in client code)
2. Generate signatures on your backend, pass to Flutter SDK
3. Store JWT tokens securely on client
4. Rotate HMAC keys regularly via admin console
5. Use HTTPS for all API communication

## SDK Authentication

For Flutter SDK, the signature is passed to `createConnection()` - the SDK handles token exchange internally:

```dart
final connection = await SpikeSDKV3.createConnection(
    applicationId: 1000,         // Your app ID from admin console
    signature: signatureFromBackend, // HMAC signature generated server-side
    endUserId: "user-123",       // Your user's unique ID
);
```

## Credentials Summary

| Credential | Source | Purpose |
|-----------|--------|---------|
| Application ID | Admin console | Identifies your app |
| HMAC Key | Admin console | Signs user IDs for auth |
| Webhook Signature Key | Admin console | Verifies webhook authenticity |
| JWT Access Token | Auth endpoint | Bearer token for API calls |
