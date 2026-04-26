# Auth  Module

## Endpoints

### POST `/api/v1/auth/login`

Authenticates a user and returns a JWT token with basic user details.

#### Request Body

```json
{
  "email": "user@test.com",
  "password": "******"
}
```
#### Response Body

```json
{
  "token": "jwt_token",
  "user": {
    "id": 101,
    "name": "Murali"
  }
}
```

#### Response Negative 

```json
{
 "code":"INVALID_CREDENTIALS",
 "message":"Wrong username/password"
}
```


### POST /api/v1/auth/refresh
Exchanges a refresh token for a new access token. Required so the mobile app can stay signed in without re-prompting for password.
#### Request Body
```json
{
  "refreshToken": "refresh_token"
}
```

#### Response Body
```json
{
  "token": "new_jwt_token",
  "refreshToken": "new_refresh_token",
  "expiresIn": 3600
}
```
#### Response Negative
```json
{
  "code": "TOKEN_EXPIRED",
  "message": "Refresh token has expired. Please log in again."
}
```

### POST /api/v1/auth/logout
Invalidates the current refresh token on the server.
#### Request Body
```json
{
  "refreshToken": "refresh_token"
}
```
#### Response Body
```json
{
  "success": true
}
```

### POST /api/v1/auth/forgot-password
Triggers a password reset email.
#### Request Body
```json
{
  "email": "priya@zexovo.com"
}
```
#### Response Body
```json
{
  "success": true,
  "message": "If the email is registered, a reset link has been sent."
}
```
### POST /api/v1/auth/reset-password
Completes the password reset flow using the token from the email.
#### Request Body
```json
{
  "resetToken": "reset_token_from_email",
  "newPassword": "******"
}
```
#### Response Body
```json
{
  "success": true,
  "message": "Password reset successfully."
}
```
### POST /api/v1/auth/change-password
Lets a logged-in user change their password.
#### Request Body
```json
{
  "currentPassword": "******",
  "newPassword": "******"
}
```
#### Response Body
```json
{
  "success": true
}
```
