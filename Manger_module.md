## Manager Endpoints

### GET `/api/v1/manager/dashboard`

Aggregated home screen for a manager — pending approval counts, today's team status, recent presence alerts.

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
      "employeeId": 101,
      "employeeName": "Priya Sharma",
      "type": "off_radius",
      "message": "Outside work radius for more than 1 hour",
      "createdAt": "2026-04-26T13:48:00+05:30"
    }
  ]
}
````

### GET `/api/v1/manager/team?page=1&size=20&status=active&search=priya`

Returns the manager's direct reports.

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

Returns a single direct report's profile + current status.

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

Live snapshot of today's team attendance.

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

### GET `/api/v1/manager/team/EMP1001/attendance/history?month=2026-04&filter=all`

Per-employee attendance history.

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "month": "2026-04",
  "monthlyAverage": 96.4,
  "days": [
    {
      "date": "2026-04-01",
      "status": "on_site",
      "workedHours": 8.02,
      "flagged": false
    }
  ]
}
```

---

### GET `/api/v1/manager/team/EMP1001/attendance/presence-timeline?date=2026-04-26`

Per-employee daily presence timeline.

#### Response Body

```json
{
  "employeeId": "EMP1001",
  "date": "2026-04-26",
  "summary": {
    "totalDuration": "8h 00m",
    "onSiteHours": 6.55,
    "offSiteHours": 1.47,
    "onSitePercentage": 82
  },
  "events": [
    {
      "id": "evt_1",
      "type": "clocked_in",
      "label": "Clocked in (Inside)",
      "time": "09:02",
      "flagged": false
    }
  ]
}
```

---

### GET `/api/v1/manager/alerts?status=open&from=2026-04-01&to=2026-04-30`

Lists presence alerts raised on direct reports.

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

### PATCH `/api/v1/manager/alerts/alrt_882/acknowledge`

Marks an alert as seen.

#### Response Body

```json
{
  "id": "alrt_882",
  "status": "acknowledged",
  "acknowledgedAt": "2026-04-26T13:55:00+05:30"
}
```

---

### PATCH `/api/v1/manager/alerts/alrt_882/dismiss`

Closes an alert with a reason.

#### Request Body

```json
{
  "reason": "Spoke to employee — picking up parts from yard"
}
```

#### Response Body

```json
{
  "id": "alrt_882",
  "status": "dismissed"
}
```

---

### GET `/api/v1/manager/approvals/time-off?status=pending`

Lists pending time-off requests from direct reports.

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

### POST `/api/v1/manager/approvals/time-off/to_5512/approve`

Approves a time-off request.

#### Request Body

```json
{
  "comment": "Approved"
}
```

#### Response Body

```json
{
  "id": "to_5512",
  "status": "approved",
  "decidedAt": "2026-04-26T11:00:00+05:30"
}
```

---

### POST `/api/v1/manager/approvals/time-off/to_5512/reject`

Rejects a time-off request.

#### Request Body

```json
{
  "comment": "Coverage gap on those dates — please reschedule."
}
```

#### Response Body

```json
{
  "id": "to_5512",
  "status": "rejected",
  "decidedAt": "2026-04-26T11:00:00+05:30"
}
```

---

### GET `/api/v1/manager/approvals/overtime`

Lists pending overtime extension requests.

#### Response Body

```json
{
  "requests": [
    {
      "id": "ot_221",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "attendanceId": "att_55021",
      "additionalHours": 1.5,
      "reason": "Backlog clearance",
      "currentWeekHours": 39.72,
      "projectedWeekHours": 41.22,
      "submittedAt": "2026-04-26T16:50:00+05:30"
    }
  ]
}
```

---

### GET `/api/v1/manager/approvals/timesheets?weekStart=2026-04-20&status=pending`

Lists weekly timesheets pending manager approval.

#### Response Body

```json
{
  "weekStart": "2026-04-20",
  "weekEnd": "2026-04-26",
  "timesheets": [
    {
      "id": "ts_771",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "totalHours": 39.72,
      "regularHours": 39.72,
      "overtimeHours": 0,
      "flaggedDays": 1,
      "status": "pending"
    }
  ]
}
```

---

### GET `/api/v1/manager/schedule/team?from=2026-04-20&to=2026-04-26`

Returns the team schedule grid for a date range.

#### Response Body

```json
{
  "from": "2026-04-20",
  "to": "2026-04-26",
  "team": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "shifts": [
        {
          "id": "shf_2031",
          "date": "2026-04-19",
          "startTime": "09:00",
          "endTime": "17:00",
          "locationId": "wh-4-north"
        }
      ]
    }
  ]
}
```
## Manager Approval Actions

### POST `/api/v1/manager/approvals/overtime/{requestId}/approve`

Approves an overtime request.

#### Request Body

```json id="2v1t3a"
{
  "comment": "OK — capped at 1.5h"
}
```

#### Response Body

```json id="91x4cm"
{
  "id": "ot_221",
  "status": "approved"
}
```

---

### POST `/api/v1/manager/approvals/overtime/{requestId}/reject`

Rejects an overtime request.

#### Request Body

```json id="83p7fa"
{
  "comment": "Already at FLSA threshold"
}
```

#### Response Body

```json id="g4d71x"
{
  "id": "ot_221",
  "status": "rejected"
}
```

---

### GET `/api/v1/manager/approvals/clock-in-requests`

Lists pending clock-in requests.

#### Response Body

```json id="b6r2ke"
{
  "requests": [
    {
      "id": "req_4421",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "date": "2026-04-26",
      "reason": "Asked to cover shift",
      "expectedHours": 6,
      "submittedAt": "2026-04-26T08:55:00+05:30",
      "status": "pending"
    }
  ]
}
```

---

### POST `/api/v1/manager/approvals/clock-in-requests/{requestId}/approve`

Approves a clock-in request.

#### Response Body

```json id="s8q5yu"
{
  "id": "req_4421",
  "status": "approved"
}
```

---

### POST `/api/v1/manager/approvals/clock-in-requests/{requestId}/reject`

Rejects a clock-in request.

#### Request Body

```json id="x5n8je"
{
  "comment": "Already covered"
}
```

#### Response Body

```json id="w3k1zc"
{
  "id": "req_4421",
  "status": "rejected"
}
```

---

### GET `/api/v1/manager/approvals/timesheets/{timesheetId}`

Returns detailed timesheet breakdown.

#### Response Body

```json id="l6f0pt"
{
  "id": "ts_771",
  "employeeId": "EMP1001",
  "weekStart": "2026-04-20",
  "weekEnd": "2026-04-26",
  "days": [
    {
      "date": "2026-04-20",
      "scheduled": 8,
      "worked": 8.02,
      "flagged": false
    }
  ],
  "totals": {
    "scheduled": 40,
    "worked": 39.72,
    "overtime": 0
  }
}
```

---

### POST `/api/v1/manager/approvals/timesheets/{timesheetId}/approve`

Approves a timesheet.

#### Response Body

```json id="u2m9va"
{
  "id": "ts_771",
  "status": "approved"
}
```

---

### POST `/api/v1/manager/approvals/timesheets/{timesheetId}/reject`

Rejects a timesheet.

#### Request Body

```json id="r1h8kd"
{
  "comment": "Day 4 hours look off — please re-submit"
}
```

#### Response Body

```json id="c7y4ne"
{
  "id": "ts_771",
  "status": "rejected"
}
```

---

### POST `/api/v1/manager/approvals/timesheets/bulk-approve`

Approves multiple timesheets in one request.

#### Request Body

```json id="n9p6lw"
{
  "timesheetIds": [
    "ts_771",
    "ts_772",
    "ts_773"
  ]
}
```

#### Response Body

```json id="d5t2qa"
{
  "approved": 3,
  "failed": 0
}
```
