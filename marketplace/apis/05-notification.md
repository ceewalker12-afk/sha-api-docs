# Notification API

**Base Path:** `/api/notifications`
**Target:** Marketing platforms, customer service, alert systems

---

## Overview

Multi-channel notification delivery via SMS (Africa's Talking + Twilio fallback) and email. Supports single-channel and multi-channel sends in one call.

**Key Features:**
- SMS via Africa's Talking (primary) and Twilio (fallback)
- Email via SMTP
- Multi-channel single-call delivery
- Priority levels (`normal`, `high`, `urgent`)
- Delivery tracking

---

## Endpoints

### Send SMS
```
POST /api/notifications/sms
```
**Required Role:** `admin`, `user`

**Request Body:**
```json
{
  "phone": "0712345678",
  "message": "Your SHA claim #56 has been approved.",
  "priority": "normal"
}
```

**Priority values:** `normal`, `high`, `urgent`

**Response `200`:**
```json
{
  "success": true,
  "channel": "sms",
  "recipient": "0712345678",
  "status": "sent"
}
```

---

### Send Email
```
POST /api/notifications/email
```
**Required Role:** `admin`, `user`

**Request Body:**
```json
{
  "to": "jane@example.com",
  "subject": "Claim Approved",
  "body": "Your claim has been approved and payment is being processed.",
  "html": false
}
```

**Required Fields:** `to`, `subject`, `body`

---

### Send Multi-Channel Notification
```
POST /api/notifications/send
```
**Required Role:** `admin`, `user`

Sends to multiple channels in a single call.

**Request Body:**
```json
{
  "recipient": {
    "phone": "0712345678",
    "email": "jane@example.com"
  },
  "message": "Your registration is complete.",
  "channels": ["sms", "email"]
}
```

---

## Use Cases
- Claim status updates to members
- Hospital payment notifications
- CHP activity reminders
- System alert broadcasts
- OTP delivery for MFA
