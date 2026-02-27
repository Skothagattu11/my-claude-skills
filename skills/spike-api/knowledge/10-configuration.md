# Spike API - App Configuration

## Overview

All Spike API application settings are managed through the **Spike Admin Console**. This document covers credential management, webhook configuration, security settings, backfill limits, and operational best practices.

**Admin Console URL:** [https://admin.spikeapi.com/](https://admin.spikeapi.com/)

---

## Credentials

### Application ID

A unique identifier for your Spike application. This is used in API requests and SDK initialization.

- **Format:** Alphanumeric string
- **Where to find:** Admin Console > Application Settings
- **Usage:** Passed as `app_id` in API initialization and SDK configuration

### HMAC Key

Used for generating HMAC-based authentication tokens (JWT) for API requests.

- **Minimum length:** 16 characters
- **Format:** Alphanumeric + special characters
- **Where to find:** Admin Console > Application Settings > Security
- **Usage:** Server-side JWT generation for API authentication

**Important:** The HMAC Key is shown only once when generated. Store it securely immediately. If lost, generate a new one (this will invalidate all existing tokens).

### Webhook Signature Key

Used for verifying the authenticity of incoming webhook payloads via HMAC-SHA256 (see [06-webhooks.md](./06-webhooks.md)).

- **Minimum length:** 16 characters
- **Format:** Alphanumeric + special characters
- **Where to find:** Admin Console > Webhooks > Signature Key
- **Usage:** Server-side signature verification of webhook payloads

---

## Redirect URL Configuration

### Default Redirect URL

The URL where users are redirected after completing the provider OAuth flow. Supports placeholders for dynamic routing.

**Format:**
```
https://yourdomain.com/callback?user_id={user_id}&provider={provider_slug}&status={status}
```

**Available Placeholders:**

| Placeholder | Description | Example Value |
|-------------|-------------|---------------|
| `{user_id}` | The Spike user ID | `user_xyz789` |
| `{provider_slug}` | The connected provider slug | `whoop` |
| `{status}` | Connection result | `success` or `error` |
| `{error_code}` | Error code (when status=error) | `access_denied` |
| `{customer_id}` | Your application's user ID | `cust_abc123` |

**Example configurations:**

```
# Web application
https://app.example.com/integrations/callback?user={user_id}&provider={provider_slug}&status={status}

# Mobile deep link (iOS/Android)
myapp://integrations/callback?user={user_id}&provider={provider_slug}&status={status}

# Flutter app with custom scheme
holisticos://callback?user={user_id}&provider={provider_slug}&status={status}
```

### Allowed Redirect Domains

A security whitelist of domains that are permitted as redirect targets. Any redirect URL must match one of the allowed domains.

**Configuration:**
- Add each allowed domain in the Admin Console
- Subdomains can be wildcarded: `*.example.com`
- Include both production and development domains

**Example whitelist:**

| Domain | Purpose |
|--------|---------|
| `app.example.com` | Production web app |
| `staging.example.com` | Staging environment |
| `localhost:3000` | Local development |
| `localhost:8080` | Alternative local dev |
| `myapp://` | Mobile deep link scheme |

**Security note:** Only add domains you control. Remove unused domains promptly.

---

## Webhook Endpoints

Configure separate webhook URLs for different event categories:

### Main Webhook URL

Receives health data and provider integration events.

| Setting | Description |
|---------|-------------|
| **URL** | HTTPS endpoint for `record_change`, `provider_integration_created`, `provider_integration_deleted` events |
| **Method** | POST |
| **Timeout** | Must respond within 30 seconds |
| **Retry** | 10 attempts with exponential backoff |

**Example:** `https://api.example.com/webhooks/spike/health-data`

### Lab Reports Webhook URL

Receives lab report processing results.

| Setting | Description |
|---------|-------------|
| **URL** | HTTPS endpoint for `lab_report_completed`, `lab_report_failed` events |
| **Method** | POST |
| **Timeout** | Must respond within 30 seconds |
| **Retry** | 10 attempts with exponential backoff |

**Example:** `https://api.example.com/webhooks/spike/lab-reports`

### Nutrition Records Webhook URL

Receives nutrition analysis results.

| Setting | Description |
|---------|-------------|
| **URL** | HTTPS endpoint for `nutrition_record_completed`, `nutrition_record_failed` events |
| **Method** | POST |
| **Timeout** | Must respond within 30 seconds |
| **Retry** | 10 attempts with exponential backoff |

**Example:** `https://api.example.com/webhooks/spike/nutrition`

**Notes:**
- All webhook URLs must use HTTPS in production
- HTTP is allowed for `localhost` during development
- You can use the same URL for all three categories (differentiate by `event_type` in the payload)
- See [06-webhooks.md](./06-webhooks.md) for detailed webhook documentation

---

## Max Backfill Configuration

Controls how much historical data is fetched when a user first connects a provider.

### Global Setting

| Setting | Range | Default | Description |
|---------|-------|---------|-------------|
| **Max Backfill Days** | 1-90 | 90 | Maximum number of days of historical data to fetch |

**Configuration:** Admin Console > Application Settings > Data > Max Backfill

### Behavior

- When a user connects a new provider, Spike fetches historical data up to the configured number of days back
- The configured value applies globally to all providers
- The actual backfill may be less than configured if the provider has a lower limit

### Provider-Specific Backfill Limits

Some providers impose their own maximum backfill regardless of your configuration:

| Provider | Provider Max Backfill | Your Config (max) |
|----------|-----------------------|-------------------|
| Apple Health | 90 days | Up to 90 |
| Google Fit | 90 days | Up to 90 |
| Samsung Health | 90 days | Up to 90 |
| WHOOP | 90 days | Up to 90 |
| Oura | 90 days | Up to 90 |
| **Fitbit** | **30 days** | Up to 30 |
| Garmin | 90 days | Up to 90 |
| Withings | 90 days | Up to 90 |
| Polar | 90 days | Up to 90 |
| Suunto | 90 days | Up to 90 |
| Coros | 90 days | Up to 90 |
| Peloton | 90 days | Up to 90 |
| Strava | 90 days | Up to 90 |
| Eight Sleep | 90 days | Up to 90 |
| Dexcom | 90 days | Up to 90 |
| Freestyle Libre | 90 days | Up to 90 |
| Cronometer | 90 days | Up to 90 |
| **MyFitnessPal** | **30 days** | Up to 30 |

**Example:** If your Max Backfill is set to 60 days:
- WHOOP connection: fetches 60 days of history
- Fitbit connection: fetches 30 days (provider limit)
- Oura connection: fetches 60 days of history

### Recommendations

| Use Case | Recommended Backfill | Rationale |
|----------|---------------------|-----------|
| **Health coaching app** | 90 days | Maximum context for trend analysis |
| **Fitness tracking** | 30-60 days | Recent data is most relevant |
| **Quick onboarding** | 7-14 days | Fast initial data load |
| **Clinical/research** | 90 days | Complete historical picture |

---

## Best Practices

### Security

1. **Rotate HMAC keys periodically** - Generate new HMAC keys every 90 days. Update your server-side code first, then rotate in the admin console.

2. **Store secrets securely** - Never commit HMAC keys or Webhook Signature Keys to version control. Use environment variables or a secrets manager (AWS Secrets Manager, HashiCorp Vault, etc.).

3. **Restrict redirect domains** - Only whitelist domains you actively use. Remove staging/development domains before production launch.

4. **Verify all webhooks** - Always validate the HMAC-SHA256 signature on incoming webhooks. Never process unverified payloads.

5. **Use HTTPS everywhere** - All webhook URLs and redirect URLs should use HTTPS in production.

6. **Implement IP allowlisting** - If your infrastructure supports it, allowlist Spike's webhook source IPs (available in admin console).

### Monitoring

1. **Monitor webhook delivery** - Check the admin console dashboard regularly for failed webhook deliveries.

2. **Set up health checks** - Ensure your webhook endpoints have uptime monitoring.

3. **Track API usage** - Monitor your API call volume and response times in the admin console.

4. **Alert on failures** - Set up alerts for:
   - Webhook delivery failures exceeding threshold
   - Unusual API error rates
   - Provider disconnections

5. **Log webhook events** - Log all incoming webhook payloads (after verification) for debugging and audit purposes.

### Data Management

1. **Choose appropriate backfill** - Set backfill based on your use case. Larger backfill means more initial data transfer and processing time.

2. **Handle provider disconnections** - When receiving `provider_integration_deleted` webhook, decide whether to retain or purge the provider's data.

3. **Deduplicate data** - If users connect multiple providers that report similar metrics, implement deduplication logic (see merge strategies in [07-providers-and-metrics.md](./07-providers-and-metrics.md)).

4. **Respect data retention** - Establish and enforce data retention policies appropriate for your jurisdiction (GDPR, HIPAA, etc.).

### Development Workflow

1. **Use separate apps for environments** - Create separate Spike applications for development, staging, and production.

2. **Test webhooks locally** - Use ngrok or similar tunneling services during development:
   ```bash
   ngrok http 3000
   # Configure the ngrok URL as your webhook URL in the dev app
   ```

3. **Validate integration** - After configuring:
   - Connect a test provider account
   - Verify webhook delivery
   - Confirm data appears correctly via API queries
   - Test the full disconnect/reconnect flow

4. **Document your configuration** - Keep a record of your Spike configuration settings in your team's internal documentation (not in version control).

---

## Configuration Checklist

Use this checklist when setting up a new Spike application:

- [ ] Create application in Admin Console
- [ ] Record Application ID
- [ ] Generate and securely store HMAC Key (min 16 chars)
- [ ] Generate and securely store Webhook Signature Key (min 16 chars)
- [ ] Configure Default Redirect URL with appropriate placeholders
- [ ] Add all required domains to Allowed Redirect Domains
- [ ] Configure Main Webhook URL (HTTPS)
- [ ] Configure Lab Reports Webhook URL (HTTPS) if using lab reports
- [ ] Configure Nutrition Records Webhook URL (HTTPS) if using nutrition AI
- [ ] Set Max Backfill Days based on use case
- [ ] Enable desired providers
- [ ] Test with a development provider account
- [ ] Verify webhook signature validation in your code
- [ ] Verify redirect flow works end-to-end
- [ ] Set up monitoring and alerting
