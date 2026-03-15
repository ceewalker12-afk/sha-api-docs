# User Management API

**Base Path:** `/api/users`
**Target:** SaaS companies, enterprise applications

---

## Overview

Complete user lifecycle management with role-based access control, activity tracking, and audit logging. All user operations are admin-only and automatically logged.

**Key Features:**
- Full CRUD user management
- Role-based access control (6 roles)
- Soft-delete (deactivate, not destroy)
- Per-user activity summaries
- Anomaly detection on user behaviour
- Audit log on every action

---

## Endpoints

### List Users
```
GET /api/users/
```
**Required Role:** `admin`

**Response `200`:**
```json
{
  "success": true,
  "users": [
    {
      "id": 1,
      "username": "jane_admin",
      "full_name": "Jane Wanjiku",
      "email": "jane@sha.go.ke",
      "role": "admin",
      "is_active": 1,
      "last_login": "2024-06-01T08:00:00Z",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ],
  "count": 1
}
```

---

### Get Single User
```
GET /api/users/<id>
```
**Required Role:** `admin`

Returns user profile. Sensitive fields (`password_hash`, `mfa_secret`) are automatically stripped.

---

### Create User
```
POST /api/users/
```
**Required Role:** `admin`

**Request Body:**
```json
{
  "username": "chp_nairobi_01",
  "password": "SecurePass123",
  "full_name": "Peter Otieno",
  "email": "peter@sha.go.ke",
  "role": "chp",
  "assigned_area": "Westlands Ward"
}
```

**Required Fields:** `username`, `password`, `full_name`, `email`, `role`

**Response `201`:**
```json
{
  "success": true,
  "message": "User created successfully",
  "user_id": 42
}
```

---

### Update User
```
PUT /api/users/<id>
```
**Required Role:** `admin`

**Updatable Fields:** `full_name`, `email`, `phone`, `role`, `is_active`, `hospital_id`, `assigned_area`

---

### Deactivate User
```
DELETE /api/users/<id>
```
**Required Role:** `admin`

Soft-delete — sets `is_active = 0`. User data is preserved for audit purposes.

---

### User Activity Summary
```
GET /api/users/<id>/activity
```
**Required Role:** `admin`

Returns action history and anomaly flags for the user.

---

### Available Roles
```
GET /api/users/roles
```

**Response `200`:**
```json
{
  "roles": [
    { "id": "admin", "name": "Administrator", "description": "Full system access" },
    { "id": "auditor", "name": "Auditor", "description": "View and verify documents, access audit logs" },
    { "id": "user", "name": "Standard User", "description": "Create and manage documents" },
    { "id": "hospital", "name": "Hospital Staff", "description": "Hospital-specific access" },
    { "id": "doctor", "name": "Doctor", "description": "Record patient treatments" },
    { "id": "patient", "name": "Patient", "description": "Patient portal access" }
  ]
}
```

---

## Use Cases
- Enterprise employee account management
- SaaS multi-tenant user provisioning
- Healthcare staff access control
- Government system user administration
