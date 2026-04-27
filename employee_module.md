## Endpoints

### Employee Module
Employee CRUD (used by Manager / HR) plus the /employee/me family for an employee's self-service profile, tax info, bank account, contract, notification preferences, biometric toggle, and emergency contacts. Florida hourly/daily wage context — money in cents USD.

### GET `/api/v1/employees?page=1&size=20&search=priya&status=ACTIVE&department=Operations`

Lists / searches employees.

#### Response Body

```json
{
  "data": [
    {
      "employeeId": "EMP1001",
      "firstName": "Priya",
      "lastName": "Sharma",
      "email": "priya@zexovo.com",
      "phone": "+19045551234",
      "designation": "Floor Supervisor",
      "department": "Operations",
      "managerId": "EMP1000",
      "payType": "hourly",
      "status": "ACTIVE",
      "joiningDate": "2024-02-10"
    }
  ],
  "page": 1,
  "size": 20,
  "total": 143
}
```

---

### GET `/api/v1/employees/{employeeId}`

Full employee profile.

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "personalInfo": {
    "firstName": "Priya",
    "lastName": "Sharma",
    "dob": "1995-08-10",
    "gender": "FEMALE",
    "maritalStatus": "SINGLE"
  },
  "contactInfo": {
    "email": "priya@zexovo.com",
    "phone": "+19045551234",
    "address": "Orlando, FL"
  },
  "jobInfo": {
    "department": "Operations",
    "designation": "Floor Supervisor",
    "managerId": "EMP1000",
    "joiningDate": "2024-02-10",
    "employmentType": "FULL_TIME",
    "state": "FL"
  },
  "pay": {
    "type": "hourly",
    "hourlyRate": 2200,
    "currency": "USD"
  },
  "tax": {
    "filingStatus": "single",
    "ssnLast4": "4421",
    "exemptions": 1,
    "w4OnFile": true
  },
  "disbursement": {
    "method": "direct_deposit",
    "bankAccountLast4": "7890"
  },
  "status": "ACTIVE"
}
```

---

### POST `/api/v1/employees`

Creates a new employee.

#### Request Body

```json
{
  "firstName": "Priya",
  "lastName": "Sharma",
  "email": "priya@zexovo.com",
  "phone": "+19045551234",
  "departmentId": "D100",
  "designationId": "DES200",
  "managerId": "EMP1000",
  "joiningDate": "2026-04-22",
  "employmentType": "FULL_TIME",
  "state": "FL",
  "pay": {
    "type": "hourly",
    "hourlyRate": 2200,
    "currency": "USD"
  },
  "tax": {
    "filingStatus": "single",
    "ssnLast4": "4421",
    "exemptions": 1,
    "w4OnFile": true
  },
  "disbursement": {
    "method": "direct_deposit",
    "bank": {
      "accountHolderName": "Priya Sharma",
      "routingNumber": "021000021",
      "accountNumber": "1234567890",
      "accountType": "checking"
    }
  }
}
```

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "message": "Employee created successfully"
}
```

---

### PUT `/api/v1/employees/{employeeId}`

Updates an employee.

#### Request Body

```json
{
  "phone": "+19045559999",
  "designationId": "DES300",
  "managerId": "EMP1002",
  "pay": {
    "type": "hourly",
    "hourlyRate": 2400,
    "currency": "USD"
  }
}
```

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "updatedFields": [
    "phone",
    "designationId",
    "managerId",
    "pay"
  ]
}
```

---

### PATCH `/api/v1/employees/{employeeId}/status`

Activates / deactivates an employee.

#### Request Body

```json
{
  "status": "INACTIVE",
  "reason": "Resigned",
  "lastWorkingDate": "2026-05-30"
}
```

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "status": "INACTIVE",
  "lastWorkingDate": "2026-05-30"
}
```

---

## Documents

### POST `/api/v1/employees/{employeeId}/documents`

Uploads a document.

#### Request Body (multipart/form-data)

| Field        | Type   | Required | Description                                                |
| ------------ | ------ | -------- | ---------------------------------------------------------- |
| file         | file   | Yes      | PDF / JPEG / PNG, max 10 MB                                |
| documentType | string | Yes      | id_proof / address_proof / offer_letter / contract / other |

#### Response Body

```json
{
  "id": "doc_4421",
  "documentType": "id_proof",
  "fileName": "drivers_license.pdf",
  "uploadedAt": "2026-04-26T10:14:00-04:00"
}
```

---

### GET `/api/v1/employees/{employeeId}/documents`

Lists employee documents.

#### Response Body

```json
{
  "documents": [
    {
      "id": "doc_4421",
      "documentType": "id_proof",
      "fileName": "drivers_license.pdf",
      "uploadedAt": "2026-04-26T10:14:00-04:00",
      "fileSizeBytes": 144211
    }
  ]
}
```

---

### GET `/api/v1/employees/{employeeId}/documents/{documentId}/download`

Returns signed download URL.

#### Response Body

```json
{
  "id": "doc_4421",
  "url": "https://files.zexovo.com/signed/doc_4421?token=...",
  "expiresAt": "2026-04-26T11:14:00-04:00"
}
```

---

### DELETE `/api/v1/employees/{employeeId}/documents/{documentId}`

Deletes a document.

#### Response Body

```json
{
  "id": "doc_4421",
  "deleted": true
}
```

---

## Employee Dashboard

### GET `/api/v1/employee/dashboard?date=2026-04-26`

Aggregated home-screen payload.

#### Response Body

```json
{
  "greeting": "Good Morning",
  "date": "2026-04-26",
  "banner": {
    "type": "within_radius",
    "message": "Within building radius",
    "severity": "success"
  },
  "shift": {
    "status": "clocked_in",
    "scheduledHours": 8,
    "workedHours": 7.72,
    "remainingHours": 0.28,
    "clockInTime": "2026-04-26T09:02:00-04:00",
    "expectedClockOut": "2026-04-26T17:00:00-04:00"
  },
  "weeklySummary": {
    "totalHours": 39.72,
    "remainingHours": 0.28,
    "flsaThresholdHours": 40,
    "approachingOvertime": true
  },
  "alerts": [
    {
      "id": "alrt_882",
      "type": "presence_alert_sent_to_manager",
      "message": "You have been outside the work radius for more than 1 hour today.",
      "severity": "warning",
      "createdAt": "2026-04-26T13:48:00-04:00"
    }
  ],
  "upcomingShifts": [
    {
      "id": "shf_2031",
      "date": "2026-04-19",
      "startTime": "09:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "role": "Floor Supervisor",
      "assignedBy": "Sarah Jenkins"
    }
  ]
}
```

---

## Self-Service Profile

### GET `/api/v1/employee/me`

Logged-in employee profile.

#### Response Body

```json
{
  "id": 101,
  "employeeId": "EMP1001",
  "name": "Priya Sharma",
  "email": "priya@zexovo.com",
  "phone": "+19045551234",
  "designation": "Floor Supervisor",
  "department": "Operations",
  "manager": {
    "id": 87,
    "name": "Sarah Jenkins"
  },
  "avatarUrl": "https://cdn.zexovo.com/avatars/101.png",
  "biometricEnabled": true,
  "joiningDate": "2024-02-10",
  "state": "FL",
  "payType": "hourly"
}
```

---

### PATCH `/api/v1/employee/me`

Updates own profile contact info.

#### Request Body

```json
{
  "phone": "+19045559999",
  "address": "123 Main St, Orlando, FL 32801"
}
```

#### Response Body

```json
{
  "success": true,
  "updatedFields": [
    "phone",
    "address"
  ]
}
```

---

### POST `/api/v1/employee/me/avatar`

Uploads profile photo.

#### Request Body (multipart/form-data)

| Field | Type | Required | Description          |
| ----- | ---- | -------- | -------------------- |
| file  | file | Yes      | JPEG / PNG, max 5 MB |

#### Response Body

```json
{
  "avatarUrl": "https://cdn.zexovo.com/avatars/101.png"
}
```


# Employee Profile — Tax Info & Bank Account APIs

Florida hourly/daily wage context. Currency values in cents (USD) unless otherwise stated.

---

## Tax Information

### GET `/api/v1/employee/me/tax-info`

Tax information shown on the Profile screen (filing status, SSN last 4, exemptions, W-4 status).

#### Response Body

```json
{
  "filingStatus": "single",
  "ssnLast4": "4421",
  "exemptions": 1,
  "w4OnFile": true,
  "w4UpdatedAt": "2025-12-15"
}
````

#### Field Notes

| Field        | Type    | Description                                                       |
| ------------ | ------- | ----------------------------------------------------------------- |
| filingStatus | string  | single / married_jointly / married_separately / head_of_household |
| ssnLast4     | string  | Last 4 digits of SSN                                              |
| exemptions   | number  | Number of exemptions                                              |
| w4OnFile     | boolean | Whether W-4 declaration exists                                    |
| w4UpdatedAt  | string  | Last updated date                                                 |

---

## Bank Account

### GET `/api/v1/employee/me/bank-account`

Returns employee bank / direct deposit details. Account number is masked.

#### Response Body

```json
{
  "method": "direct_deposit",
  "accountHolderName": "Priya Sharma",
  "routingNumber": "021000021",
  "accountNumberLast4": "7890",
  "accountType": "checking"
}
```

#### Field Notes

| Field              | Type   | Description            |
| ------------------ | ------ | ---------------------- |
| method             | string | direct_deposit / check |
| accountHolderName  | string | Name on bank account   |
| routingNumber      | string | Bank routing number    |
| accountNumberLast4 | string | Masked account number  |
| accountType        | string | checking / savings     |

---

### PATCH `/api/v1/employee/me/bank-account`

Updates employee bank details.

#### Request Body

```json
{
  "accountHolderName": "Priya Sharma",
  "routingNumber": "021000021",
  "accountNumber": "9876543210",
  "accountType": "savings"
}
```

#### Response Body

```json
{
  "success": true,
  "accountNumberLast4": "3210"
}
```


# Employee Profile — Contract, Preferences, Biometric & Emergency Contacts APIs

Florida hourly/daily wage context. Timestamps in `-04:00 (US/Eastern)`.

---

## Employment Contract

### GET `/api/v1/employee/me/contract`

Returns a signed URL to the employment contract PDF.

#### Response Body

```json
{
  "documentId": "doc_contract_101",
  "url": "https://files.zexovo.com/signed/contract_EMP1001?token=...",
  "expiresAt": "2026-04-26T11:14:00-04:00"
}
````

#### Field Notes

| Field      | Type   | Description                   |
| ---------- | ------ | ----------------------------- |
| documentId | string | Internal contract document ID |
| url        | string | Signed download URL           |
| expiresAt  | string | URL expiry timestamp          |

---

## Notification Preferences

### GET `/api/v1/employee/me/preferences/notifications`

Returns notification channel and topic preferences.

#### Response Body

```json
{
  "channels": {
    "push": {
      "enabled": true,
      "topics": ["payslip","timesheet","schedule","overtime","announcement"]
    },
    "email": {
      "enabled": true,
      "topics": ["payslip","announcement"]
    },
    "whatsapp": {
      "enabled": false,
      "topics": []
    }
  }
}
```

#### Supported Topics

* payslip
* timesheet
* schedule
* overtime
* announcement

---

### PATCH `/api/v1/employee/me/preferences/notifications`

Partial update of notification preferences.

#### Request Body

```json
{
  "channels": {
    "whatsapp": {
      "enabled": true,
      "topics": ["payslip"]
    }
  }
}
```

#### Response Body

```json
{
  "success": true
}
```

---

## Biometric Login

### PATCH `/api/v1/employee/me/biometric`

Enables / disables biometric login.

#### Request Body

```json
{
  "enabled": true
}
```

#### Response Body

```json
{
  "enabled": true
}
```

#### Field Notes

| Field   | Type    | Description                   |
| ------- | ------- | ----------------------------- |
| enabled | boolean | Current biometric login state |

---

## Emergency Contacts

### GET `/api/v1/employee/me/emergency-contacts`

Returns all emergency contacts.

#### Response Body

```json
{
  "contacts": [
    {
      "id": "ec_1",
      "name": "Anita Sharma",
      "relation": "Spouse",
      "phone": "+19045559999",
      "primary": true
    }
  ]
}
```

---

### POST `/api/v1/employee/me/emergency-contacts`

Creates a new emergency contact.

#### Request Body

```json
{
  "name": "Ravi Sharma",
  "relation": "Father",
  "phone": "+19045557777",
  "primary": false
}
```

#### Response Body

```json
{
  "id": "ec_2",
  "name": "Ravi Sharma"
}
```

---

### PATCH `/api/v1/employee/me/emergency-contacts/{id}`

Updates an emergency contact.

#### Request Body

```json
{
  "phone": "+19045557788"
}
```

#### Response Body

```json
{
  "id": "ec_2",
  "updated": true
}
```

---

### DELETE `/api/v1/employee/me/emergency-contacts/{id}`

Deletes an emergency contact.

#### Response Body

```json
{
  "id": "ec_2",
  "deleted": true
}
```

---

## Emergency Contact Fields

| Field    | Type    | Description              |
| -------- | ------- | ------------------------ |
| id       | string  | Contact ID               |
| name     | string  | Full name                |
| relation | string  | Relationship to employee |
| phone    | string  | Emergency phone number   |
| primary  | boolean | Primary contact flag     |

---

# Employee Signature Requests APIs

Compliance documents awaiting employee acknowledgment or signature  
(FLSA policy, geofence consent, handbook, NDA, etc.)

Timestamps use `-04:00 (US/Eastern)`.

---

## GET `/api/v1/employee/me/signature-requests`

Returns all compliance documents awaiting the employee's signature.

### Response Body

```json
{
  "requests": [
    {
      "id": "sig_1",
      "documentId": "comp_flsa_2026",
      "documentName": "FLSA Overtime Policy 2026",
      "documentType": "flsa_policy",
      "dueDate": "2026-05-15",
      "status": "pending"
    }
  ]
}
````

### Field Notes

| Field        | Type   | Description                                                       |
| ------------ | ------ | ----------------------------------------------------------------- |
| id           | string | Signature request ID                                              |
| documentId   | string | Compliance document ID                                            |
| documentName | string | Human-readable document title                                     |
| documentType | string | flsa_policy / geofence_consent / handbook / nda / safety_training |
| dueDate      | string | Signature deadline                                                |
| status       | string | pending / signed / expired                                        |

---

## POST `/api/v1/employee/me/signature-requests/{id}/sign`

Signs a compliance document using the in-app signature pad.

### Request Body

```json
{
  "signatureImageBase64": "...",
  "consentAccepted": true,
  "signedAt": "2026-04-27T10:14:00-04:00",
  "deviceFingerprint": "ios-9F2A-...-B81C"
}
```

### Request Field Notes

| Field                | Type    | Required | Description                          |
| -------------------- | ------- | -------- | ------------------------------------ |
| signatureImageBase64 | string  | Yes      | Base64 encoded signature image       |
| consentAccepted      | boolean | Yes      | Must be true before signing          |
| signedAt             | string  | Yes      | Signature timestamp                  |
| deviceFingerprint    | string  | Yes      | Device fingerprint for audit logging |

---

### Response Body

```json
{
  "id": "sig_1",
  "status": "signed",
  "auditTrailId": "audit_sig_1",
  "signedDocumentUrl": "https://files.zexovo.com/signed/comp_flsa_2026_signed_EMP1001?token=..."
}
```



