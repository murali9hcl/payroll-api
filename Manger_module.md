# Manager Module

Web-portal endpoints scoped to the manager's direct reports — team views, approvals, scheduling, AI insights, manager-side notifications.

**Florida hourly/daily wage context** — money in cents (USD), timestamps `-04:00` (US/Eastern).

---

# Endpoints

---

## GET /api/v1/manager/dashboard

Aggregated home screen — pay-period totals, today's status, pending approvals, burnout/cost insights, recent alerts.

### Response Body

```json
{
  "greeting": "Good Morning",
  "manager": {
    "id": 87,
    "name": "Sarah Jenkins",
    "managerId": "MGR-1042"
  },
  "teamSize": 14,
  "currentPayPeriod": {
    "start": "2026-04-15",
    "end": "2026-04-28",
    "totalTeamHours": 412.7,
    "totalOvertimeHours": 18.5,
    "estimatedGrossPay": 9876200,
    "currency": "USD"
  },
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
  "approvalRatePercent": 92,
  "burnoutRiskAlerts": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "weekHours": 47.5,
      "consecutiveDaysWorked": 11,
      "riskLevel": "high"
    }
  ],
  "costOptimizationSuggestions": [
    {
      "id": "co_1",
      "title": "Reassign Wed coverage to reduce OT",
      "estimatedSavings": 24000
    }
  ],
  "unreadNotifications": 4,
  "recentAlerts": [
    {
      "id": "alrt_882",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "type": "off_radius",
      "message": "Outside work radius for more than 1 hour",
      "createdAt": "2026-04-26T13:48:00-04:00"
    }
  ]
}
````

# Manager — Team

---

## GET /api/v1/manager/team

Returns the manager's direct reports.

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| page | integer | No | Default `1` |
| size | integer | No | Default `20` |
| status | string | No | `active \| inactive \| all` (default `active`) |
| search | string | No | Search by employee name / ID |

### Response Body

```json
{
  "data": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "designation": "Floor Supervisor",
      "department": "Operations",
      "payType": "hourly",
      "status": "ACTIVE",
      "todayStatus": "clocked_in",
      "avatarUrl": "https://cdn.zexovo.com/avatars/101.png"
    }
  ],
  "page": 1,
  "size": 20,
  "total": 14
}
````

---

## GET /api/v1/manager/team/{employeeId}

Single direct report's profile + current status.

### Response Body

```json
{
  "employeeId": "EMP1001",
  "name": "Priya Sharma",
  "designation": "Floor Supervisor",
  "department": "Operations",
  "payType": "hourly",
  "hourlyRate": 2200,
  "currency": "USD",
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

## GET /api/v1/manager/team/attendance/today

Live snapshot of today's team attendance.

### Response Body

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
      "clockInTime": "2026-04-26T09:02:00-04:00",
      "elapsed": "7h 43m",
      "withinRadius": true,
      "flagged": false
    }
  ]
}
```

---

## GET /api/v1/manager/team/{employeeId}/attendance/history

Per-employee attendance history.

### Query Parameters

| Parameter | Type   | Required | Description                                           |
| --------- | ------ | -------- | ----------------------------------------------------- |
| month     | string | Yes      | Format `YYYY-MM`                                      |
| filter    | string | No       | `all \| on_site \| remote \| flagged` (default `all`) |

### Response Body

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

## GET /api/v1/manager/team/{employeeId}/attendance/presence-timeline

Per-employee daily presence timeline.

### Query Parameters

| Parameter | Type   | Required | Description         |
| --------- | ------ | -------- | ------------------- |
| date      | string | Yes      | Format `YYYY-MM-DD` |

### Response Body

```json
{
  "employeeId": "EMP1001",
  "date": "2026-04-26",
  "summary": {
    "totalDuration": "8h 16m",
    "onSiteHours": 6.55,
    "offSiteHours": 1.22,
    "onSitePercentage": 84
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
# Manager Module (Part 2)

Manager-facing web portal endpoints for presence alerts and approvals.

---

## Manager — Presence Alerts

### GET `/api/v1/manager/alerts`

Lists presence alerts on direct reports (off-radius, no-show, late, FLSA threshold).

#### Query Parameters

| Name   | Type   | Required | Description                                                  |
| ------ | ------ | -------- | ------------------------------------------------------------ |
| status | string | No       | `open \| acknowledged \| dismissed \| all` (default: `open`) |
| from   | string | No       | Start date/time filter                                       |
| to     | string | No       | End date/time filter                                         |

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
      "occurredAt": "2026-04-26T13:48:00-04:00",
      "status": "open"
    }
  ]
}
```

---

### PATCH `/api/v1/manager/alerts/{alertId}/acknowledge`

Marks an alert as acknowledged.

#### Response Body

```json
{
  "id": "alrt_882",
  "status": "acknowledged",
  "acknowledgedAt": "2026-04-26T13:55:00-04:00"
}
```

---

### PATCH `/api/v1/manager/alerts/{alertId}/dismiss`

Dismisses an alert.

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

## Manager — Approvals: Time Off

### GET `/api/v1/manager/approvals/time-off`

Returns time-off requests awaiting approval.

#### Query Parameters

| Name   | Type   | Required | Description        |
| ------ | ------ | -------- | ------------------ |
| status | string | No       | Default: `pending` |

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
      "submittedAt": "2026-04-26T10:14:00-04:00",
      "balanceAfter": 9
    }
  ]
}
```

---

### POST `/api/v1/manager/approvals/time-off/{requestId}/approve`

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
  "decidedAt": "2026-04-26T11:00:00-04:00"
}
```

---

### POST `/api/v1/manager/approvals/time-off/{requestId}/reject`

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
  "decidedAt": "2026-04-26T11:00:00-04:00"
}
```

---

## Manager — Approvals: Overtime

### GET `/api/v1/manager/approvals/overtime`

Returns overtime approval requests.

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
      "submittedAt": "2026-04-26T16:50:00-04:00"
    }
  ]
}
```

---

### POST `/api/v1/manager/approvals/overtime/{requestId}/approve`

Approves overtime request.

#### Request Body

```json
{
  "comment": "OK — capped at 1.5h"
}
```

#### Response Body

```json
{
  "id": "ot_221",
  "status": "approved"
}
```

---

### POST `/api/v1/manager/approvals/overtime/{requestId}/reject`

Rejects overtime request.

#### Request Body

```json
{
  "comment": "Already at FLSA threshold"
}
```

#### Response Body

```json
{
  "id": "ot_221",
  "status": "rejected"
}
```
# Manager Module (Part 3)

Manager-facing approval workflows for clock-in requests and timesheets.

---

## Manager — Approvals: Clock-In Requests

### GET `/api/v1/manager/approvals/clock-in-requests`

Returns employee off-schedule clock-in requests awaiting approval.

#### Response Body

```json
{
  "requests": [
    {
      "id": "req_4421",
      "employeeId": "EMP1001",
      "employeeName": "Priya Sharma",
      "date": "2026-04-26",
      "reason": "Asked to cover shift",
      "expectedHours": 6,
      "submittedAt": "2026-04-26T08:55:00-04:00",
      "status": "pending"
    }
  ]
}
```

---

### POST `/api/v1/manager/approvals/clock-in-requests/{requestId}/approve`

Approves a clock-in request.

#### Response Body

```json
{
  "id": "req_4421",
  "status": "approved"
}
```

---

### POST `/api/v1/manager/approvals/clock-in-requests/{requestId}/reject`

Rejects a clock-in request.

#### Request Body

```json
{
  "comment": "Already covered"
}
```

#### Response Body

```json
{
  "id": "req_4421",
  "status": "rejected"
}
```

---

## Manager — Approvals: Timesheets

### GET `/api/v1/manager/approvals/timesheets`

Lists weekly timesheets pending approval. Includes gross pay, overtime risk, and AI anomaly flags.

#### Query Parameters

| Name      | Type   | Required | Description          |
| --------- | ------ | -------- | -------------------- |
| weekStart | string | No       | Format: `YYYY-MM-DD` |
| status    | string | No       | Default: `pending`   |

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
      "grossPay": 87384,
      "currency": "USD",
      "overtimeRisk": "low",
      "anomalyFlags": [],
      "flaggedDays": 1,
      "status": "pending"
    }
  ]
}
```

#### Enums

* `overtimeRisk`: `low | medium | high`

#### Possible `anomalyFlags`

* `missing_clock_out`
* `overtime_spike`
* `late_submission`

---

### GET `/api/v1/manager/approvals/timesheets/{timesheetId}`

Returns detailed daily timesheet breakdown.

#### Response Body

```json
{
  "id": "ts_771",
  "employeeId": "EMP1001",
  "weekStart": "2026-04-20",
  "weekEnd": "2026-04-26",
  "currency": "USD",
  "days": [
    {
      "date": "2026-04-20",
      "scheduled": 8,
      "worked": 8.02,
      "regular": 8.0,
      "overtime": 0.02,
      "grossPay": 17644,
      "flagged": false
    }
  ],
  "totals": {
    "scheduled": 40,
    "worked": 39.72,
    "regular": 39.72,
    "overtime": 0,
    "grossPay": 87384
  }
}
```

---

### POST `/api/v1/manager/approvals/timesheets/{timesheetId}/approve`

Approves a timesheet.

#### Response Body

```json
{
  "id": "ts_771",
  "status": "approved"
}
```

---

### POST `/api/v1/manager/approvals/timesheets/{timesheetId}/reject`

Rejects a timesheet.

#### Request Body

```json
{
  "comment": "Day 4 hours look off — please re-submit"
}
```

#### Response Body

```json
{
  "id": "ts_771",
  "status": "rejected"
}
```

---

### POST `/api/v1/manager/approvals/timesheets/bulk-approve`

Approves multiple timesheets in one request.

#### Request Body

```json
{
  "timesheetIds": ["ts_771", "ts_772", "ts_773"]
}
```

#### Response Body

```json
{
  "approved": 3,
  "failed": 0
}
```
# Manager Module (Part 4)

Manager-facing scheduling, AI insights, and notifications.

---

## Manager — Schedule

### GET `/api/v1/manager/schedule/team`

Returns the team schedule grid for a date range.

#### Query Parameters

| Name | Type   | Required | Description          |
| ---- | ------ | -------- | -------------------- |
| from | string | Yes      | Format: `YYYY-MM-DD` |
| to   | string | Yes      | Format: `YYYY-MM-DD` |

#### Response Body

```json id="m1"
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
          "locationId": "wh-4-north",
          "role": "Floor Supervisor",
          "shiftType": "regular"
        }
      ]
    }
  ]
}
```

---

### POST `/api/v1/manager/schedule/shifts`

Assigns a new shift. Includes overtime warnings if applicable.

#### Request Body

```json id="m2"
{
  "employeeId": "EMP1001",
  "date": "2026-04-28",
  "startTime": "09:00",
  "endTime": "17:00",
  "locationId": "wh-4-north",
  "role": "Floor Supervisor",
  "notes": "Floor B coverage"
}
```

#### Response Body

```json id="m3"
{
  "id": "shf_2050",
  "status": "scheduled",
  "warnings": [
    {
      "code": "PROJECTED_OVERTIME",
      "message": "Projected weekly hours 42.5 — overtime applies",
      "projectedWeekHours": 42.5
    }
  ]
}
```

---

### PATCH `/api/v1/manager/schedule/shifts/{shiftId}`

Updates an existing shift.

#### Request Body

```json id="m4"
{
  "startTime": "10:00",
  "endTime": "18:00"
}
```

#### Response Body

```json id="m5"
{
  "id": "shf_2050",
  "status": "scheduled",
  "warnings": []
}
```

---

### DELETE `/api/v1/manager/schedule/shifts/{shiftId}`

Deletes a shift and notifies the employee.

#### Response Body

```json id="m6"
{
  "id": "shf_2050",
  "deleted": true
}
```

---

### POST `/api/v1/manager/schedule/shifts/bulk-assign`

Creates shifts for multiple employees across multiple dates.

#### Request Body

```json id="m7"
{
  "employeeIds": ["EMP1001", "EMP1002", "EMP1003"],
  "dates": ["2026-04-28", "2026-04-29"],
  "startTime": "09:00",
  "endTime": "17:00",
  "locationId": "wh-4-north",
  "role": "Floor Supervisor"
}
```

#### Response Body

```json id="m8"
{
  "createdShifts": 6,
  "warnings": []
}
```

---

### POST `/api/v1/manager/schedule/shifts/{shiftId}/swap`

Swaps a shift with another employee.

#### Request Body

```json id="m9"
{
  "withEmployeeId": "EMP1002"
}
```

#### Response Body

```json id="m10"
{
  "originalShiftId": "shf_2050",
  "newShiftId": "shf_2055",
  "status": "swapped"
}
```

---

### GET `/api/v1/manager/schedule/conflicts`

Returns scheduling conflicts.

#### Query Parameters

| Name | Type   | Required |
| ---- | ------ | -------- |
| from | string | Yes      |
| to   | string | Yes      |

#### Response Body

```json id="m11"
{
  "conflicts": [
    {
      "id": "conf_1",
      "type": "double_booking",
      "employeeId": "EMP1001",
      "shiftIds": ["shf_2050", "shf_2051"],
      "date": "2026-04-28",
      "severity": "high"
    },
    {
      "id": "conf_2",
      "type": "ot_threshold",
      "employeeId": "EMP1004",
      "shiftId": "shf_2055",
      "projectedWeekHours": 44,
      "severity": "medium"
    },
    {
      "id": "conf_3",
      "type": "leave_overlap",
      "employeeId": "EMP1010",
      "shiftId": "shf_2060",
      "leaveRequestId": "to_5512",
      "severity": "high"
    }
  ]
}
```

#### Conflict Types

* `double_booking`
* `ot_threshold`
* `leave_overlap`
* `unavailable`

---

## Manager — AI Insights

### GET `/api/v1/manager/insights/burnout-risk`

Returns burnout risk alerts.

```json id="m12"
{
  "alerts": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "weekHours": 47.5,
      "consecutiveDaysWorked": 11,
      "averageDailyHours": 9.5,
      "riskLevel": "high",
      "factors": ["consecutive_days", "weekly_hours_over_45"]
    }
  ]
}
```

---

### GET `/api/v1/manager/insights/cost-optimization`

Returns labor cost optimization suggestions.

```json id="m13"
{
  "suggestions": [
    {
      "id": "co_1",
      "title": "Reassign Wed coverage to reduce OT",
      "rationale": "EMP1001 will hit 42h on Wed; EMP1004 has slack",
      "estimatedSavings": 24000,
      "currency": "USD",
      "actionType": "shift_swap",
      "shiftId": "shf_2050",
      "swapWith": "EMP1004"
    }
  ]
}
```

---

### GET `/api/v1/manager/insights/timesheets`

Returns anomaly flags for current week's timesheets.

#### Query Parameters

| Name      | Type   | Required |
| --------- | ------ | -------- |
| weekStart | string | No       |

```json id="m14"
{
  "weekStart": "2026-04-20",
  "anomalies": [
    {
      "employeeId": "EMP1004",
      "anomalyType": "missing_clock_out",
      "date": "2026-04-22",
      "severity": "high"
    },
    {
      "employeeId": "EMP1010",
      "anomalyType": "overtime_spike",
      "date": "2026-04-25",
      "severity": "medium",
      "hours": 11.5
    }
  ]
}
```

---

### GET `/api/v1/manager/insights/labor-cost`

Returns labor cost vs budget.

#### Query Parameters

| Name   | Type   | Required |
| ------ | ------ | -------- |
| period | string | Yes      |

```json id="m15"
{
  "period": "2026-04",
  "currency": "USD",
  "actualCost": 9876200,
  "budgetedCost": 10500000,
  "variance": -623800,
  "variancePercent": -5.9,
  "byEmployeeType": [
    { "type": "hourly", "actual": 7621400, "budgeted": 8200000 },
    { "type": "daily", "actual": 2254800, "budgeted": 2300000 }
  ],
  "overtimePercentOfTotal": 6.3
}
```

---

## Manager — Notifications

### GET `/api/v1/manager/notifications`

Returns manager notifications.

#### Query Parameters

| Name       | Type    | Required | Description     |
| ---------- | ------- | -------- | --------------- |
| unreadOnly | boolean | No       | Default `false` |
| limit      | number  | No       | Default `20`    |

#### Response Body

```json id="m16"
{
  "unreadCount": 3,
  "notifications": [
    {
      "id": "mntf_1",
      "type": "timesheet_submitted",
      "title": "Timesheet submitted",
      "body": "Priya Sharma submitted week 04/20–04/26.",
      "severity": "info",
      "read": false
    }
  ]
}
```

#### Notification Types

* `timesheet_submitted`
* `overtime_threshold`
* `compliance_flag`
* `payroll_ready`
* `schedule_conflict`
* `bulk_approval_summary`

---

### PATCH `/api/v1/manager/notifications/{id}/read`

Marks a notification as read.

```json id="m17"
{
  "id": "mntf_1",
  "read": true
}
```

---

### POST `/api/v1/manager/notifications/mark-all-read`

Marks all notifications as read.

```json id="m18"
{
  "markedCount": 3
}
```

