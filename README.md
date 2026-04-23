# Employee Module

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

```json
{
  "code": "EMPLOYEE_NOT_FOUND",
  "message": "Employee not found"
}
```
