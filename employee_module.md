## Endpoints

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
