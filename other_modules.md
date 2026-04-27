
# Payslip, OffTime and OverTime Modules

## Endpoints

### GET `/api/v1/time-off/balances`

Standalone leave balances endpoint. Useful for balance cards in UI.

#### Response Body

```json
{
  "year": 2026,
  "balances": [
    {
      "type": "vacation",
      "totalDays": 18,
      "usedDays": 6,
      "remainingDays": 12
    },
    {
      "type": "sick",
      "totalDays": 10,
      "usedDays": 4,
      "remainingDays": 6
    },
    {
      "type": "personal",
      "totalDays": 4,
      "usedDays": 2,
      "remainingDays": 2
    }
  ]
}
````

### GET `/api/v1/time-off/types`

Returns the leave types allowed for this employee. Drives the dropdown in the request form.

#### Response Body

```json id="x4g2or"
{
  "types": [
    {
      "id": "vacation",
      "label": "Vacation",
      "halfDayAllowed": true
    },
    {
      "id": "sick",
      "label": "Sick Leave",
      "halfDayAllowed": true
    },
    {
      "id": "personal",
      "label": "Personal Day",
      "halfDayAllowed": false
    },
    {
      "id": "bereavement",
      "label": "Bereavement",
      "halfDayAllowed": false
    }
  ]
}
```

### PATCH `/api/v1/time-off/requests/to_5512`

Edits a pending request before manager approval.

#### Request Body

```json id="l26r5z"
{
  "startDate": "2026-05-11",
  "endDate": "2026-05-13",
  "reason": "Family event (dates moved)"
}
```

#### Response Body

```json id="q3u6zt"
{
  "id": "to_5512",
  "status": "pending",
  "updatedAt": "2026-04-26T12:14:00+05:30"
}
```

### POST /api/v1/time-off/requests

#### Request Body

```json
{
  "type": "vacation",
  "startDate": "2026-05-10",
  "endDate": "2026-05-12",
  "reason": "Family event",
  "halfDay": false
}
```

#### Response Body

```json
{
  "id": "to_5512",
  "type": "vacation",
  "startDate": "2026-05-10",
  "endDate": "2026-05-12",
  "days": 3,
  "status": "pending",
  "submittedAt": "2026-04-26T10:14:00+05:30"
}
```
### GET /api/v1/time-off/requests
Lists the employee's time-off requests.
### Query Parameters
status (optional: pending | approved | rejected | all, default all), year (optional)

#### Response Body
```json
{
  "balances": {
    "vacationDaysRemaining": 12,
    "sickDaysRemaining": 6,
    "personalDaysRemaining": 2
  },
  "requests": [
    {
      "id": "to_5512",
      "type": "vacation",
      "startDate": "2026-05-10",
      "endDate": "2026-05-12",
      "days": 3,
      "status": "pending",
      "submittedAt": "2026-04-26T10:14:00+05:30"
    }
  ]
}
```

### DELETE /api/v1/time-off/requests/{id}
Cancels a pending time-off request.

#### Response Body
```json
{
  "id": "to_5512",
  "status": "cancelled"
}
```

### GET `/api/v1/payslips?year=2026&limit=12`

Lists the employee's payslips. Drives the Payslips tab in the bottom nav.

#### Response Body

```json
{
  "payslips": [
    {
      "id": "ps_3301",
      "period": "2026-04",
      "payDate": "2026-04-30",
      "grossPay": 8500000,
      "netPay": 6720000,
      "currency": "INR",
      "status": "issued"
    }
  ]
}
````

---

### GET `/api/v1/payslips/ps_3301`

Returns the breakdown for a single payslip (earnings, deductions, totals).

#### Response Body

```json
{
  "id": "ps_3301",
  "period": "2026-04",
  "payDate": "2026-04-30",
  "currency": "INR",
  "earnings": [
    { "label": "Basic", "amount": 5000000 },
    { "label": "HRA", "amount": 2000000 },
    { "label": "Overtime", "amount": 1500000 }
  ],
  "deductions": [
    { "label": "PF", "amount": 600000 },
    { "label": "TDS", "amount": 1180000 }
  ],
  "grossPay": 8500000,
  "totalDeductions": 1780000,
  "netPay": 6720000,
  "downloadUrl": "https://api.zexovo.com/api/v1/payslips/ps_3301/download"
}
```

---

### GET `/api/v1/payslips/ps_3301/download`

Returns a short-lived signed URL for the payslip PDF.

#### Response Body

```json
{
  "id": "ps_3301",
  "url": "https://files.zexovo.com/signed/ps_3301?token=...",
  "expiresAt": "2026-04-26T11:14:00+05:30"
}
```

---

### GET `/api/v1/overtime/status`

Returns the FLSA / weekly overtime tracker. Drives the "Approaching 40 hrs FLSA threshold" banner.

#### Response Body

```json
{
  "weekStart": "2026-04-20",
  "weekEnd": "2026-04-26",
  "hoursWorked": 39.72,
  "flsaThresholdHours": 40,
  "remainingHours": 0.28,
  "approachingThreshold": true,
  "overtimeAllowed": true
}
```

---

### POST `/api/v1/overtime/extend-shift`

Submits a request to extend the current shift into overtime.

#### Request Body

```json
{
  "attendanceId": "att_55021",
  "additionalHours": 1.5,
  "reason": "Backlog clearance"
}
```

#### Response Body

```json
{
  "requestId": "ot_221",
  "status": "pending_manager_approval",
  "submittedAt": "2026-04-26T16:50:00+05:30"
}
```

### GET `/api/v1/overtime/requests?status=all`

Lists the employee's overtime requests with statuses.

#### Response Body

```json
{
  "requests": [
    {
      "id": "ot_221",
      "attendanceId": "att_55021",
      "additionalHours": 1.5,
      "reason": "Backlog clearance",
      "status": "approved",
      "submittedAt": "2026-04-26T16:50:00+05:30",
      "decidedAt": "2026-04-26T16:55:00+05:30"
    }
  ]
}
```

