# Auth Module

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

