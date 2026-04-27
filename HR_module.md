## HR Endpoints

### GET `/api/v1/hr/dashboard`

HR dashboard summary API.

#### Response Body

```json
{
  "headcount": 312,
  "newHiresThisMonth": 6,
  "attritionRateMonthly": 1.2,
  "presenceToday": {
    "clockedIn": 248,
    "absent": 12,
    "onLeave": 9,
    "flagged": 3
  },
  "openPositions": 14,
  "payrollStatus": {
    "currentPeriod": "2026-04",
    "status": "in_progress",
    "scheduledRunDate": "2026-04-30"
  }
}
```

---

### GET `/api/v1/hr/departments`

Department listing API.

#### Response Body

```json
{
  "departments": [
    {
      "id": "D100",
      "name": "Engineering",
      "headcount": 84,
      "managerId": "EMP1000"
    }
  ]
}
```

---

### GET `/api/v1/hr/payroll/runs?year=2026&status=approved`

Payroll runs listing API.

#### Response Body

```json
{
  "runs": [
    {
      "id": "pr_2026_04",
      "period": "2026-04",
      "status": "approved",
      "employeesIncluded": 312,
      "grossTotal": 2652000000,
      "netTotal": 2096640000,
      "scheduledPayDate": "2026-04-30"
    }
  ]
}
```
### GET `/api/v1/hr/reports/attendance?from=2026-04-01&to=2026-04-30`

HR attendance analytics report API.

#### Response Body

```json
{
  "from": "2026-04-01",
  "to": "2026-04-30",
  "summary": {
    "averageAttendance": 95.2,
    "averageOnSitePercentage": 88.4,
    "flaggedDays": 14,
    "absences": 22
  },
  "byDepartment": [
    {
      "departmentId": "D100",
      "departmentName": "Engineering",
      "attendance": 96.1
    }
  ]
}
```
### GET `/api/v1/hr/audit-logs?page=1&size=20`

HR audit logs API.

#### Response Body

```json
{
  "data": [
    {
      "id": "log_8821",
      "actorId": "EMP_HR_1",
      "actorName": "Anita Rao",
      "action": "PAYROLL_RUN_APPROVED",
      "entityType": "payroll_run",
      "entityId": "pr_2026_04",
      "occurredAt": "2026-04-29T18:00:00+05:30"
    }
  ],
  "page": 1,
  "size": 20,
  "total": 1421
}
```

## HR Module (High Priority Additions)

### POST `/api/v1/hr/departments`

Creates a new department.

#### Request Body

```json id="hr001"
{
  "name": "Operations",
  "managerId": "EMP1500",
  "code": "OPS"
}
```

#### Response Body

```json id="hr002"
{
  "id": "D200",
  "name": "Operations"
}
```

---

### PATCH `/api/v1/hr/departments/{id}`

Updates a department.

#### Request Body

```json id="hr003"
{
  "managerId": "EMP1600"
}
```

#### Response Body

```json id="hr004"
{
  "id": "D200",
  "updated": true
}
```

---

### DELETE `/api/v1/hr/departments/{id}`

Deletes a department.

#### Response Body

```json id="hr005"
{
  "id": "D200",
  "deleted": true
}
```

---

### GET `/api/v1/hr/designations`

Returns all designations.

#### Response Body

```json id="hr006"
{
  "designations": [
    {
      "id": "DES200",
      "title": "Software Engineer",
      "departmentId": "D100",
      "level": "L3"
    }
  ]
}
```

---

### POST `/api/v1/hr/designations`

Creates a designation.

#### Request Body

```json id="hr007"
{
  "title": "Senior Software Engineer",
  "departmentId": "D100",
  "level": "L4"
}
```

#### Response Body

```json id="hr008"
{
  "id": "DES210",
  "title": "Senior Software Engineer"
}
```

---

### PATCH `/api/v1/hr/designations/{id}`

Updates a designation.

#### Request Body

```json id="hr009"
{
  "level": "L5"
}
```

#### Response Body

```json id="hr010"
{
  "id": "DES210",
  "updated": true
}
```

---

### DELETE `/api/v1/hr/designations/{id}`

Deletes a designation.

#### Response Body

```json id="hr011"
{
  "id": "DES210",
  "deleted": true
}
```

---

### POST `/api/v1/hr/employees/bulk-import`

Imports employees from CSV.

#### Content Type

`multipart/form-data`

#### Multipart Fields

| Field  | Type   | Required | Description                    |
| ------ | ------ | -------- | ------------------------------ |
| file   | file   | Yes      | CSV file with required columns |
| dryRun | string | No       | `"true"` validates only        |

#### Response Body

```json id="hr012"
{
  "totalRows": 50,
  "created": 47,
  "skipped": 2,
  "failed": 1,
  "errors": [
    {
      "row": 14,
      "code": "DUPLICATE_EMAIL",
      "message": "rahul@company.com already exists"
    }
  ]
}
```

---

### POST `/api/v1/hr/employees/{employeeId}/terminate`

Terminates an employee.

#### Request Body

```json id="hr013"
{
  "lastWorkingDate": "2026-05-30",
  "reason": "Resignation",
  "noticePeriodWaived": false,
  "settleFinalPay": true
}
```

#### Response Body

```json id="hr014"
{
  "employeeId": "EMP1001",
  "status": "TERMINATED",
  "finalSettlementId": "fs_4421"
}
```

---

### POST `/api/v1/hr/employees/{employeeId}/rehire`

Rehires an employee.

#### Request Body

```json id="hr015"
{
  "rejoiningDate": "2026-07-01",
  "designationId": "DES200"
}
```

#### Response Body

```json id="hr016"
{
  "employeeId": "EMP1001",
  "status": "ACTIVE"
}
```

---

### POST `/api/v1/hr/employees/{employeeId}/transfer`

Transfers an employee.

#### Request Body

```json id="hr017"
{
  "departmentId": "D200",
  "managerId": "EMP1500",
  "effectiveDate": "2026-05-01"
}
```

#### Response Body

```json id="hr018"
{
  "employeeId": "EMP1001",
  "transferId": "trf_99"
}
```

---

### POST `/api/v1/hr/onboarding`

Starts onboarding workflow.

#### Request Body

```json id="hr019"
{
  "employeeId": "EMP1001",
  "templateId": "onb_default",
  "startDate": "2026-04-22"
}
```

#### Response Body

```json id="hr020"
{
  "id": "onb_run_1",
  "employeeId": "EMP1001",
  "tasks": [
    {
      "id": "tsk_1",
      "label": "Submit ID proof",
      "status": "pending"
    }
  ]
}
```

---

### GET `/api/v1/hr/onboarding/{id}`

Returns onboarding progress.

#### Response Body

```json id="hr021"
{
  "id": "onb_run_1",
  "employeeId": "EMP1001",
  "progress": 33,
  "tasks": [
    {
      "id": "tsk_1",
      "label": "Submit ID proof",
      "status": "completed",
      "completedAt": "2026-04-22T10:00:00+05:30"
    }
  ]
}
```

---

### PATCH `/api/v1/hr/onboarding/{id}/tasks/{taskId}`

Updates onboarding task status.

#### Request Body

```json id="hr022"
{
  "status": "completed"
}
```

#### Response Body

```json id="hr023"
{
  "id": "tsk_2",
  "status": "completed"
}
```

---

### GET `/api/v1/hr/holidays`

Returns holiday list.

#### Query Parameters

`year` (optional)

#### Response Body

```json id="hr024"
{
  "year": 2026,
  "holidays": [
    {
      "id": "hol_1",
      "date": "2026-01-26",
      "name": "Republic Day",
      "type": "national"
    }
  ]
}
```

---

### POST `/api/v1/hr/holidays`

Creates a holiday.

#### Request Body

```json id="hr025"
{
  "date": "2026-08-15",
  "name": "Independence Day",
  "type": "national"
}
```

#### Response Body

```json id="hr026"
{
  "id": "hol_2",
  "name": "Independence Day"
}
```

---

### PATCH `/api/v1/hr/holidays/{id}`

Updates holiday.

#### Request Body

```json id="hr027"
{
  "name": "Independence Day (observed)"
}
```

#### Response Body

```json id="hr028"
{
  "id": "hol_2",
  "updated": true
}
```

---

### DELETE `/api/v1/hr/holidays/{id}`

Deletes holiday.

#### Response Body

```json id="hr029"
{
  "id": "hol_2",
  "deleted": true
}
```

---

### GET `/api/v1/hr/payroll/runs/{id}`

Returns payroll run details.

#### Response Body

```json id="hr030"
{
  "id": "pr_2026_04",
  "period": "2026-04",
  "status": "awaiting_approval",
  "summary": {
    "employees": 312,
    "grossTotal": 2652000000,
    "deductionsTotal": 555360000,
    "netTotal": 2096640000,
    "taxTotal": 367920000
  },
  "lines": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "gross": 8500000,
      "deductions": 1780000,
      "net": 6720000
    }
  ]
}
```

---

### POST `/api/v1/hr/payroll/runs/{id}/approve`

Approves payroll run.

#### Response Body

```json id="hr031"
{
  "id": "pr_2026_04",
  "status": "approved",
  "approvedAt": "2026-04-29T18:00:00+05:30"
}
```

---

### POST `/api/v1/hr/payroll/runs/{id}/publish`

Publishes payroll and payslips.

#### Response Body

```json id="hr032"
{
  "id": "pr_2026_04",
  "status": "paid",
  "payslipsPublished": 312,
  "bankFileUrl": "https://files.zexovo.com/signed/bank_pr_2026_04?token=..."
}
```

---

### GET `/api/v1/hr/announcements`

Returns announcements.

#### Response Body

```json id="hr033"
{
  "announcements": [
    {
      "id": "ann_1",
      "title": "Q1 Town Hall",
      "body": "Join us on April 30 at 4 PM IST.",
      "audience": "all",
      "publishedAt": "2026-04-26T09:00:00+05:30"
    }
  ]
}
```

---

### POST `/api/v1/hr/announcements`

Creates announcement.

#### Request Body

```json id="hr034"
{
  "title": "Office closure on Friday",
  "body": "All offices closed for maintenance.",
  "audience": "department:D100",
  "publishAt": "2026-04-29T09:00:00+05:30"
}
```

#### Response Body

```json id="hr035"
{
  "id": "ann_2",
  "status": "scheduled"
}
```

---

### DELETE `/api/v1/hr/announcements/{id}`

Deletes announcement.

#### Response Body

```json id="hr036"
{
  "id": "ann_2",
  "deleted": true
}
```

