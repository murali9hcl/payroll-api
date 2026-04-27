## HR Endpoints

### GET `/api/v1/manager/dashboard`

Manager dashboard summary API.

#### Response Body

```json
{
  "greeting": "Good Morning",
  "manager": {
    "id": 87,
    "name": "Sarah Jenkins"
  },
  "teamSize": 14,
  "today": {
    "clockedIn": 11,
    "onBreak": 2,
    "absent": 1,
    "onLeave": 0,
    "flagged": 1
  },
  "pendingApprovals": {
    "timeOff": 3,
    "overtime": 1,
    "clockInRequests": 2,
    "timesheets": 4,
    "total": 10
  },
  "recentAlerts": [
    {
      "id": "alrt_882",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "type": "off_radius",
      "message": "Outside work radius for more than 1 hour",
      "createdAt": "2026-04-26T13:48:00+05:30"
    }
  ]
}
````

---

### GET `/api/v1/manager/team?page=1&size=20&status=active&search=priya`

Manager team members listing API.

#### Response Body

```json
{
  "data": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "designation": "Warehouse Associate",
      "department": "Operations",
      "status": "ACTIVE",
      "todayStatus": "clocked_in",
      "avatarUrl": "https://cdn.zexovo.com/avatars/101.png"
    }
  ],
  "page": 1,
  "size": 20,
  "total": 14
}
```

---

### GET `/api/v1/manager/team/EMP1001`

Manager team employee profile API.

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "name": "Priya Sharma",
  "designation": "Warehouse Associate",
  "department": "Operations",
  "status": "ACTIVE",
  "joiningDate": "2024-02-10",
  "todayStatus": "clocked_in",
  "currentShift": {
    "id": "shf_2031",
    "startTime": "09:00",
    "endTime": "17:00"
  },
  "weekHours": 39.72,
  "monthlyAttendance": 96.4
}
```

---

### GET `/api/v1/manager/team/attendance/today`

Live team attendance snapshot API.

#### Response Body

```json
{
  "date": "2026-04-26",
  "summary": {
    "total": 14,
    "clockedIn": 11,
    "onBreak": 2,
    "absent": 1,
    "onLeave": 0,
    "flagged": 1
  },
  "members": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "status": "clocked_in",
      "clockInTime": "2026-04-26T09:02:00+05:30",
      "elapsed": "7h 43m",
      "withinRadius": true,
      "flagged": false
    }
  ]
}
```

---

### GET `/api/v1/manager/alerts?status=open&from=2026-04-01&to=2026-04-30`

Manager presence alerts listing API.

#### Response Body

```json
{
  "alerts": [
    {
      "id": "alrt_882",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "type": "off_radius",
      "severity": "warning",
      "message": "Outside work radius for more than 1 hour",
      "occurredAt": "2026-04-26T13:48:00+05:30",
      "status": "open"
    }
  ]
}
```

---

### GET `/api/v1/manager/approvals/time-off?status=pending`

Pending leave approval requests API.

#### Response Body

```json
{
  "requests": [
    {
      "id": "to_5512",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "type": "vacation",
      "startDate": "2026-05-10",
      "endDate": "2026-05-12",
      "days": 3,
      "reason": "Family event",
      "status": "pending",
      "submittedAt": "2026-04-26T10:14:00+05:30",
      "balanceAfter": 9
    }
  ]
}
```

---

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

---

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

---

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
