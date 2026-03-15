# System Status API

**Base Path:** `/api/system`
**Target:** DevOps teams, monitoring services, IT departments

---

## Overview

Real-time system health checks, service status, and feature flag visibility. Designed for integration with monitoring tools like Grafana, Prometheus, and uptime services.

**Key Features:**
- Service health checks
- Feature flag status (SMS, email, USSD, ML, payments)
- Uptime monitoring integration
- No authentication required for health endpoints

---

## Endpoints

### System Status
```
GET /api/system/status
```
No authentication required.

**Response `200`:**
```json
{
  "success": true,
  "system": "SHA Verification System",
  "status": "operational",
  "services": {
    "sms": "enabled",
    "email": "enabled",
    "ussd": "enabled",
    "ml": "enabled",
    "payments": "enabled"
  }
}
```

---

### Health Check
```
GET /api/system/health
```
No authentication required. Use this for load balancer health probes.

**Response `200`:**
```json
{
  "success": true,
  "status": "healthy",
  "timestamp": "2024-06-01T10:00:00Z"
}
```

---

### Feature Configuration
```
GET /api/system/features
```
**Required Role:** `admin`

Returns detailed feature flag state including mock modes and providers.

**Response `200`:**
```json
{
  "features": {
    "sms": { "enabled": true, "mock_mode": false, "provider": "Africa's Talking" },
    "email": { "enabled": true, "mock_mode": false, "provider": "SMTP" },
    "ussd": { "enabled": true, "mock_mode": false, "provider": "Africa's Talking" },
    "ml": { "enabled": true, "mock_mode": false, "provider": "Custom ML Models" },
    "payments": { "enabled": true, "mock_mode": false, "provider": "M-Pesa" }
  }
}
```

---

## Use Cases
- Load balancer health probes
- Uptime monitoring (UptimeRobot, Pingdom)
- Grafana dashboard data source
- CI/CD deployment verification
- On-call alert integration
