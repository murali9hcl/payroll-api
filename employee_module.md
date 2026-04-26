# Employee Module

## Endpoints

### GET `/api/v1/employees?page=1&size=20&search=rahul&status=ACTIVE&department=ENG`

Employee search API

#### Response Body

```json
{
  "data": [
    {
      "employeeId": "EMP1001",
      "firstName": "Rahul",
      "lastName": "Sharma",
      "email": "rahul@company.com",
      "phone": "9876543210",
      "designation": "Software Engineer",
      "department": "Engineering",
      "managerId": "EMP1000",
      "status": "ACTIVE",
      "joiningDate": "2024-02-10"
    }
  ],
  "page": 1,
  "size": 20,
  "total": 143
}

```

### GET `/api/v1/employees/{employeeId}`

GET Employee details by ID API

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "personalInfo": {
    "firstName": "Rahul",
    "lastName": "Sharma",
    "dob": "1995-08-10",
    "gender": "MALE",
    "maritalStatus": "SINGLE"
  },
  "contactInfo": {
    "email": "rahul@company.com",
    "phone": "9876543210",
    "address": "Bengaluru"
  },
  "jobInfo": {
    "department": "Engineering",
    "designation": "Software Engineer",
    "managerId": "EMP1000",
    "joiningDate": "2024-02-10",
    "employmentType": "FULL_TIME"
  },
  "status": "ACTIVE"
}

```

###  POST /api/v1/employees

Create Employee

#### Request Body

```json
{
  "firstName": "Rahul",
  "lastName": "Sharma",
  "email": "rahul@company.com",
  "phone": "9876543210",
  "departmentId": "D100",
  "designationId": "DES200",
  "managerId": "EMP1000",
  "joiningDate": "2026-04-22",
  "employmentType": "FULL_TIME"
}

```

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "message": "Employee created successfully"
}

```

###  PUT /api/v1/employees/{employeeId}

Update Employee

#### Request Body

```json
{
  "phone": "9999999999",
  "designationId": "DES300",
  "managerId": "EMP1002"
}

```

### PATCH /api/v1/employees/{employeeId}/status

Deactive Employee

```json
{
  "status": "INACTIVE",
  "reason": "Resigned",
  "lastWorkingDate": "2026-05-30"
}
```

### POST `/api/v1/employees/{employeeId}/documents`

Uploads a document for a specific employee.

#### Path Parameters

| Parameter   | Type   | Required | Description |
|------------|--------|----------|-------------|
| employeeId | number | Yes | Unique employee ID |

#### Description

Supported document uploads include identity and onboarding documents.

#### Content Type

`multipart/form-data`

#### Multipart Fields

| Field        | Type   | Required | Description |
|-------------|--------|----------|-------------|
| file        | file   | Yes | Document file to upload |
| documentType | string | Yes | Type of document |


### GET /api/v1/employees/search?q=rah

Employee Search


#### Request Body

```json
{
  "id": 101,
  "name": "Priya Sharma",
  "email": "priya@zexovo.com",
  "role": "Warehouse Associate",
  "department": "Operations",
  "assignedLocation": {
    "id": "wh-4-north",
    "name": "Warehouse 4, Northern Sector",
    "geofenceRadiusMeters": 75
  },
  "manager": {
    "id": 87,
    "name": "Sarah Jenkins"
  },
  "avatarUrl": "https://cdn.zexovo.com/avatars/101.png"
}
```

#### Response Body

```json
{
  "code": "EMPLOYEE_NOT_FOUND",
  "message": "Employee not found"
}
```

GET /api/v1/employee/dashboard

Employee Details for Dashboard


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
    "scheduledHours": 8.00,
    "workedHours": 7.72,
    "remainingHours": 0.28,
    "clockInTime": "2026-04-26T09:02:00+05:30",
    "expectedClockOut": "2026-04-26T17:00:00+05:30"
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
      "createdAt": "2026-04-26T13:48:00+05:30"
    }
  ],
  "upcomingShifts": [
    {
      "id": "shf_2031",
      "date": "2026-04-19",
      "startTime": "09:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "assignedBy": "Sarah Jenkins"
    }
  ]
}
```

