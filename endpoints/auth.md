# Authentication API

**Base Path:** `/api/auth`

Handles user login, registration, token refresh, and password management using JWT (JSON Web Tokens).

---

## Why JWT?

- Stateless — no session storage needed on the server
- Tokens carry user role and ID, so every service can verify permissions without a database call
- Tokens expire after 24 hours, reducing risk if a token is leaked
- Works seamlessly across desktop (Tkinter) and web (Next.js) clients

---

## Endpoints

### Login

```
POST /api/auth/login
```

Authenticates a user and returns a JWT token.

**Request Body:**
```json
{
  "username": "admin",
  "password": "your_password"
}
```

**Success Response `200`:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "username": "admin",
    "name": "System Administrator",
    "email": "admin@sha.go.ke",
    "role": "admin"
  }
}
```

**Error Response `401`:**
```json
{
  "error": "Invalid credentials"
}
```

---

### Register

```
POST /api/auth/register
```

Creates a new system user account.

**Request Body:**
```json
{
  "username": "chp_nairobi",
  "password": "SecurePass123",
  "full_name": "Jane Wanjiku",
  "role": "chp",
  "email": "jane.wanjiku@sha.go.ke",
  "phone": "0712345678"
}
```

**Available Roles:** `admin`, `doctor`, `chp`, `hospital_admin`, `claims_officer`

**Success Response `201`:**
```json
{
  "message": "User registered successfully",
  "user_id": 42
}
```

---

### Get Current User

```
GET /api/auth/me
```

Returns the profile of the currently authenticated user.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "user": {
    "user_id": 1,
    "username": "admin",
    "role": "admin"
  }
}
```

---

### Refresh Token

```
POST /api/auth/refresh
```

Issues a new token before the current one expires.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

---

### Change Password

```
POST /api/auth/change-password
```

**Headers Required:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "old_password": "OldPass123",
  "new_password": "NewSecurePass456"
}
```

**Success Response `200`:**
```json
{
  "message": "Password changed successfully"
}
```

---

### Logout

```
POST /api/auth/logout
```

Signals logout. The client must discard the token locally — tokens are not server-side invalidated.

**Headers Required:** `Authorization: Bearer <token>`

**Success Response `200`:**
```json
{
  "message": "Logged out successfully"
}
```

---

## Token Structure

A decoded JWT payload looks like this:

```json
{
  "user_id": 1,
  "username": "admin",
  "role": "admin",
  "iat": 1700000000,
  "exp": 1700086400
}
```

| Field | Description |
|-------|-------------|
| `user_id` | Unique user ID in the database |
| `username` | Login username |
| `role` | User role for access control |
| `iat` | Issued at (Unix timestamp) |
| `exp` | Expiry time (24 hours after issue) |

---

## Error Reference

| Error | Status | Cause |
|-------|--------|-------|
| `Token is missing` | 401 | No Authorization header sent |
| `Token has expired` | 401 | Token older than 24 hours |
| `Invalid token` | 401 | Token tampered or malformed |
| `Account is deactivated` | 401 | Admin has disabled the account |
| `Username and password required` | 400 | Missing fields in login body |
