## Endpoints

### POST `/api/v1/auth/login`

Authenticates a user with either email or Manager Assigned ID + password. Returns JWT tokens.

#### Request Body

```json id="a1k9pd"
{
  "identifier": "sarah@zexovo.com",
  "password": "******"
}
```

`identifier` accepts a work email (`user@company.com`) or Manager Assigned ID (`MGR-1042`).

#### Response Body

```json id="f3m2qe"
{
  "token": "jwt_token",
  "refreshToken": "refresh_token",
  "expiresIn": 3600,
  "user": {
    "id": 101,
    "name": "Priya Sharma",
    "role": "employee",
    "actorType": "employee"
  }
}
```

#### Negative Response

```json id="l8v2rt"
{
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Wrong username or password."
  }
}
```

```json id="u5x7cn"
{
  "error": {
    "code": "ACCOUNT_LOCKED",
    "message": "Account locked after multiple failed attempts.",
    "details": {
      "unlockAfter": "2026-04-26T11:30:00-04:00"
    }
  }
}
```

---

### POST `/api/v1/auth/register`

Self-registration via OTP (Employee mobile flow). Issues an OTP session that must be verified separately.

#### Request Body

```json id="n4p8wy"
{
  "firstName": "Priya",
  "lastName": "Sharma",
  "email": "priya@zexovo.com",
  "phone": "+19045551234",
  "employeeCode": "EMP1001",
  "password": "******"
}
```

#### Response Body

```json id="g6r1tm"
{
  "registrationId": "reg_1",
  "otpSessionId": "otps_9001",
  "expiresInSeconds": 300
}
```

---

### POST `/api/v1/auth/otp/request`

Sends an OTP to a phone number for login or registration confirmation.

#### Request Body

```json id="c2j7zs"
{
  "phone": "+19045551234",
  "purpose": "login"
}
```

`purpose`: `login | register | reset_password`

#### Response Body

```json id="w9e4bn"
{
  "otpSessionId": "otps_8821",
  "expiresInSeconds": 300
}
```

---

### POST `/api/v1/auth/otp/verify`

Verifies an OTP and returns JWT for login or registration flows.

#### Request Body

```json id="d5q3lx"
{
  "otpSessionId": "otps_8821",
  "otp": "482917"
}
```

#### Response Body

```json id="m1s6hk"
{
  "token": "jwt_token",
  "refreshToken": "refresh_token",
  "user": {
    "id": 101,
    "name": "Priya Sharma",
    "actorType": "employee"
  }
}
```

#### Negative Response

```json id="r4v8py"
{
  "error": {
    "code": "OTP_INVALID",
    "message": "Incorrect OTP."
  }
}
```

```json id="t7n5qa"
{
  "error": {
    "code": "OTP_EXPIRED",
    "message": "OTP expired. Request a new one."
  }
}
```

---

### POST `/api/v1/auth/biometric/register`

Stores a public-key credential after device Face ID / Fingerprint enrollment.

#### Request Body

```json id="k8z2mc"
{
  "deviceId": "ios-9F2A-...-B81C",
  "publicKey": "base64_encoded_pubkey",
  "biometricType": "face_id"
}
```

`biometricType`: `face_id | fingerprint`

#### Response Body

```json id="p2u6fe"
{
  "credentialId": "bio_4421",
  "registered": true
}
```

---

### POST `/api/v1/auth/biometric/login`

Logs in using a signed challenge from the device.

#### Request Body

```json id="x6w3dv"
{
  "credentialId": "bio_4421",
  "signedChallenge": "base64_signed_challenge"
}
```

#### Response Body

```json id="e1h7kr"
{
  "token": "jwt_token",
  "refreshToken": "refresh_token",
  "user": {
    "id": 101,
    "name": "Priya Sharma",
    "actorType": "employee"
  }
}
```

#### Negative Response

```json id="q5m9ts"
{
  "error": {
    "code": "BIOMETRIC_NOT_REGISTERED",
    "message": "No biometric credential found for this device."
  }
}
```

---

### POST `/api/v1/auth/refresh`

Exchanges a refresh token for a new access token.

#### Request Body

```json id="y4r2nb"
{
  "refreshToken": "refresh_token"
}
```

#### Response Body

```json id="h8v1kc"
{
  "token": "new_jwt_token",
  "refreshToken": "new_refresh_token",
  "expiresIn": 3600
}
```

#### Negative Response

```json id="z7p3gd"
{
  "error": {
    "code": "TOKEN_EXPIRED",
    "message": "Refresh token has expired. Please log in again."
  }
}
```

---

### POST `/api/v1/auth/logout`

Invalidates the current refresh token.

#### Request Body

```json id="b3x6lf"
{
  "refreshToken": "refresh_token"
}
```

#### Response Body

```json id="n9u4ra"
{
  "success": true
}
```

---

### POST `/api/v1/auth/forgot-password`

Email-link based password reset (used for HR / non-mobile flows).

#### Request Body

```json id="j1c8te"
{
  "email": "priya@zexovo.com"
}
```

#### Response Body

```json id="v6m2po"
{
  "success": true,
  "message": "If the email is registered, a reset link has been sent."
}
```

---

### POST `/api/v1/auth/reset-password`

Completes email-link password reset.

#### Request Body

```json id="f4r7ya"
{
  "resetToken": "reset_token_from_email",
  "newPassword": "******"
}
```

#### Response Body

```json id="k2n5qb"
{
  "success": true
}
```

---

### POST `/api/v1/auth/forgot-password/otp`

OTP-based password reset for Employee mobile flow.

#### Request Body

```json id="u8x3jd"
{
  "phone": "+19045551234"
}
```

#### Response Body

```json id="m7v1eg"
{
  "otpSessionId": "otps_8822",
  "expiresInSeconds": 300
}
```

---

### POST `/api/v1/auth/reset-password/otp`

Confirms password reset using OTP + new password.

#### Request Body

```json id="c9p2wf"
{
  "otpSessionId": "otps_8822",
  "otp": "104592",
  "newPassword": "******"
}
```

#### Response Body

```json id="s6k4lt"
{
  "success": true
}
```

---

### POST `/api/v1/auth/manager/forgot-password/sms`

Manager recovery via code sent to registered mobile.

#### Request Body

```json id="r2n8qx"
{
  "managerId": "MGR-1042"
}
```

#### Response Body

```json id="d1f6hb"
{
  "recoverySessionId": "rec_771",
  "phoneMasked": "+1***-***-1234",
  "expiresInSeconds": 600
}
```

---

### POST `/api/v1/auth/manager/reset-password/sms`

Completes manager SMS recovery.

#### Request Body

```json id="w5m3ce"
{
  "recoverySessionId": "rec_771",
  "recoveryCode": "284910",
  "newPassword": "******"
}
```

#### Response Body

```json id="x1v7np"
{
  "success": true
}
```

---

### POST `/api/v1/auth/change-password`

Logged-in user changes their password.

#### Request Body

```json id="g3r8ta"
{
  "currentPassword": "******",
  "newPassword": "******"
}
```

#### Response Body

```json id="q2w6me"
{
  "success": true
}
```

---

## Shared Error Response

Every endpoint across all modules returns errors in this envelope.

```json id="z4k9uh"
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable description.",
    "details": {}
  }
}
```

### Common Error Codes

* `INVALID_CREDENTIALS`
* `ACCOUNT_LOCKED`
* `OTP_INVALID`
* `OTP_EXPIRED`
* `BIOMETRIC_NOT_REGISTERED`
* `TOKEN_EXPIRED`
* `SHIFT_NOT_STARTED`
* `ALREADY_CLOCKED_IN`
* `NOT_CLOCKED_IN`
* `GEOFENCE_VIOLATION`
* `FLSA_LIMIT_REACHED`
* `INSUFFICIENT_BALANCE`
* `EMPLOYEE_NOT_FOUND`
* `APPROVAL_ALREADY_DECIDED`
* `PAYROLL_RUN_LOCKED`
* `OVERLAPPING_SHIFT`
* `NOT_DIRECT_REPORT`
* `FORBIDDEN`
* `NOT_FOUND`
* `VALIDATION_ERROR`
