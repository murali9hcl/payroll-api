# Other Modules — Time Off, Payslip, Overtime, Tax Documents, Notifications, Devices

Employee-facing modules grouped together. Florida hourly/daily wage context — money in cents (USD), timestamps `-04:00` (US/Eastern).

---

# Time Off Module

## Endpoints

---

## GET /api/v1/time-off/balances

Standalone leave balances. Drives the balance card on the leave-request screen.

### Response Body

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

---

## GET /api/v1/time-off/types

Leave types allowed for this employee. Drives the dropdown in the request form.

### Response Body

```json
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

---

## POST /api/v1/time-off/requests

Submits a new time-off request.

### Request Body

```json
{
  "type": "vacation",
  "startDate": "2026-05-10",
  "endDate": "2026-05-12",
  "reason": "Family event",
  "halfDay": false
}
```

### Response Body

```json
{
  "id": "to_5512",
  "type": "vacation",
  "startDate": "2026-05-10",
  "endDate": "2026-05-12",
  "days": 3,
  "status": "pending",
  "submittedAt": "2026-04-26T10:14:00-04:00"
}
```

---

## GET /api/v1/time-off/requests

Lists the employee's time-off requests.

### Query Parameters

| Name   | Required | Type   | Description                                                           |
| ------ | -------- | ------ | --------------------------------------------------------------------- |
| status | No       | string | `pending`, `approved`, `rejected`, `cancelled`, `all` (default `all`) |
| year   | No       | number | Filter by year                                                        |

### Response Body

```json
{
  "requests": [
    {
      "id": "to_5512",
      "type": "vacation",
      "startDate": "2026-05-10",
      "endDate": "2026-05-12",
      "days": 3,
      "status": "pending",
      "submittedAt": "2026-04-26T10:14:00-04:00"
    }
  ]
}
```

---

## PATCH /api/v1/time-off/requests/{id}

Edits a pending request before manager approval.

### Request Body

```json
{
  "startDate": "2026-05-11",
  "endDate": "2026-05-13",
  "reason": "Family event (dates moved)"
}
```

### Response Body

```json
{
  "id": "to_5512",
  "status": "pending",
  "updatedAt": "2026-04-26T12:14:00-04:00"
}
```

## DELETE /api/v1/time-off/requests/{id}

Cancels a pending request.

### Response Body

```json
{
  "id": "to_5512",
  "status": "cancelled"
}
````

## DELETE /api/v1/time-off/requests/{id}

Cancels a pending request.

### Response Body

```json
{
  "id": "to_5512",
  "status": "cancelled"
}
````

# Payslip Module

Florida hourly/daily wage payslips. Currency is USD in cents. Mandatory deductions: Federal Income Tax, Social Security (6.2%), Medicare (1.45%). FUTA + FL SUTA are employer-paid and never appear on the employee payslip.

---

# Endpoints

## GET /api/v1/payslips

Lists the employee's payslips. Drives the Payslips tab in the bottom nav.

### Query Parameters

| Name  | Required | Type   | Description |
|-------|----------|--------|-------------|
| year  | No | number | Filter by year |
| limit | No | number | Default `12` |

### Response Body

```json
{
  "payslips": [
    {
      "id": "ps_3301",
      "period": "2026-04-15_to_2026-04-28",
      "payDate": "2026-04-30",
      "grossPay": 190850,
      "netPay": 157450,
      "currency": "USD",
      "payType": "hourly",
      "status": "issued"
    }
  ]
}
````

### Status Enum

`pending | issued | paid | cancelled`

---

## GET /api/v1/payslips/{id}

Single payslip with full breakdown.

### Response Body — Hourly Variant

```json
{
  "id": "ps_3301",
  "period": "2026-04-15_to_2026-04-28",
  "payDate": "2026-04-30",
  "currency": "USD",
  "payType": "hourly",
  "hours": {
    "regularHours": 80.0,
    "overtimeHours": 4.5
  },
  "rates": {
    "hourlyRate": 2200,
    "overtimeRate": 3300
  },
  "earnings": [
    { "code": "REGULAR", "label": "Regular Pay", "amount": 176000 },
    { "code": "OVERTIME", "label": "Overtime Pay", "amount": 14850 }
  ],
  "grossPay": 190850,
  "deductions": [
    {
      "code": "FEDERAL_INCOME_TAX",
      "label": "Federal Income Tax",
      "amount": 18800
    },
    {
      "code": "SOCIAL_SECURITY",
      "label": "Social Security",
      "amount": 11833,
      "ratePercent": 6.2
    },
    {
      "code": "MEDICARE",
      "label": "Medicare",
      "amount": 2767,
      "ratePercent": 1.45
    }
  ],
  "totalDeductions": 33400,
  "netPay": 157450,
  "netPayInWords": "One thousand five hundred seventy-four dollars and fifty cents",
  "disbursement": {
    "method": "direct_deposit",
    "bankAccountLast4": "7890",
    "status": "scheduled",
    "expectedSettlementDate": "2026-05-01"
  },
  "downloadUrl": "https://api.zexovo.com/api/v1/payslips/ps_3301/download"
}
```

---

### Response Body — Daily Variant

```json
{
  "id": "ps_3302",
  "period": "2026-04-15_to_2026-04-28",
  "payDate": "2026-04-30",
  "currency": "USD",
  "payType": "daily",
  "days": {
    "regularDays": 10,
    "extraDays": 1
  },
  "rates": {
    "dailyRate": 18000,
    "extraDayRate": 27000
  },
  "earnings": [
    { "code": "REGULAR_DAYS", "label": "Regular Days", "amount": 180000 },
    { "code": "EXTRA_DAYS", "label": "Extra Days", "amount": 27000 }
  ],
  "grossPay": 207000,
  "deductions": [
    {
      "code": "FEDERAL_INCOME_TAX",
      "label": "Federal Income Tax",
      "amount": 20400
    },
    {
      "code": "SOCIAL_SECURITY",
      "label": "Social Security",
      "amount": 12834,
      "ratePercent": 6.2
    },
    {
      "code": "MEDICARE",
      "label": "Medicare",
      "amount": 3002,
      "ratePercent": 1.45
    }
  ],
  "totalDeductions": 36236,
  "netPay": 170764,
  "netPayInWords": "One thousand seven hundred seven dollars and sixty-four cents",
  "disbursement": {
    "method": "check",
    "checkNumber": "0001245",
    "status": "printed"
  },
  "downloadUrl": "https://api.zexovo.com/api/v1/payslips/ps_3302/download"
}
```

---

## GET /api/v1/payslips/{id}/download

Short-lived signed URL for the payslip PDF.

### Response Body

```json
{
  "id": "ps_3301",
  "url": "https://files.zexovo.com/signed/ps_3301?token=...",
  "expiresAt": "2026-04-26T11:14:00-04:00"
}
```

# Overtime Module

Florida FLSA: 40 hours/week. Anything over is overtime at **1.5× hourly rate**.

---

# Endpoints

## GET /api/v1/overtime/status

FLSA / weekly overtime tracker. Drives the **"Approaching 40 hr threshold"** banner.

### Response Body

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
````

---

## POST /api/v1/overtime/extend-shift

Submits a request to extend the current shift into overtime.

### Request Body

```json
{
  "attendanceId": "att_55021",
  "additionalHours": 1.5,
  "reason": "Backlog clearance"
}
```

### Response Body

```json
{
  "requestId": "ot_221",
  "status": "pending_manager_approval",
  "submittedAt": "2026-04-26T16:50:00-04:00"
}
```

---

## GET /api/v1/overtime/requests

Lists the employee's overtime requests.

### Query Parameters

| Name   | Required | Type   | Description                                              |
| ------ | -------- | ------ | -------------------------------------------------------- |
| status | No       | string | `pending`, `approved`, `rejected`, `all` (default `all`) |

### Response Body

```json
{
  "requests": [
    {
      "id": "ot_221",
      "attendanceId": "att_55021",
      "additionalHours": 1.5,
      "reason": "Backlog clearance",
      "status": "approved",
      "submittedAt": "2026-04-26T16:50:00-04:00",
      "decidedAt": "2026-04-26T16:55:00-04:00"
    }
  ]
}
```
# Tax Documents Module

W-2, 1095-C, and (legacy international) Form 16 documents.

---

# Endpoints

## GET /api/v1/tax-documents

### Query Parameters

| Name | Required | Type | Description |
|------|----------|------|-------------|
| year | No | number | Default = current tax year |

### Response Body

```json
{
  "year": 2025,
  "documents": [
    {
      "id": "tax_w2_2025",
      "name": "W-2 — 2025",
      "type": "w2",
      "issuedOn": "2026-01-31",
      "fileSizeBytes": 184231
    },
    {
      "id": "tax_1095c_2025",
      "name": "1095-C — 2025",
      "type": "1095_c",
      "issuedOn": "2026-01-31",
      "fileSizeBytes": 91204
    }
  ]
}
````

### Type Enum

`w2 | 1095_c | 1099 | form_16`

---

## GET /api/v1/tax-documents/{id}/download

Signed URL.

### Response Body

```json id="a6w2ke"
{
  "id": "tax_w2_2025",
  "url": "https://files.zexovo.com/signed/tax_w2_2025?token=...",
  "expiresAt": "2026-04-26T11:14:00-04:00"
}
```

---

# Notifications Module (Employee)

Drives the in-app banners and bell-icon list. Manager notifications live in `Manager_module.md`.

---

# Endpoints

## GET /api/v1/notifications

### Query Parameters

| Name       | Required | Type    | Description                 |
| ---------- | -------- | ------- | --------------------------- |
| unreadOnly | No       | boolean | Default `false`             |
| type       | No       | string  | Filter by notification type |
| limit      | No       | number  | Default `20`                |

### Response Body

```json id="y4g7vn"
{
  "unreadCount": 4,
  "notifications": [
    {
      "id": "ntf_8821",
      "type": "pay_change",
      "title": "Pay change effective May 1",
      "body": "Your hourly rate has been updated to $22.00.",
      "severity": "info",
      "read": false,
      "createdAt": "2026-04-26T09:00:00-04:00"
    },
    {
      "id": "ntf_8822",
      "type": "timesheet_approved",
      "title": "Timesheet approved",
      "body": "Your week 04/20–04/26 timesheet was approved.",
      "severity": "success",
      "read": false,
      "createdAt": "2026-04-27T11:14:00-04:00"
    },
    {
      "id": "ntf_8823",
      "type": "payslip_ready",
      "title": "Payslip ready",
      "body": "Your 04/15–04/28 payslip is available.",
      "severity": "info",
      "read": false,
      "createdAt": "2026-04-30T07:00:00-04:00"
    },
    {
      "id": "ntf_8824",
      "type": "overtime_warning",
      "title": "Approaching 40 hr threshold",
      "body": "0.28 hours remaining this week.",
      "severity": "warning",
      "read": false,
      "createdAt": "2026-04-26T15:30:00-04:00"
    },
    {
      "id": "ntf_8825",
      "type": "schedule_change",
      "title": "Schedule updated",
      "body": "Wed 04/29 shift moved to 10:00–18:00.",
      "severity": "info",
      "read": true,
      "createdAt": "2026-04-26T10:00:00-04:00"
    },
    {
      "id": "ntf_8826",
      "type": "geofence_breach",
      "title": "Off-site detected",
      "body": "You've left the work radius.",
      "severity": "warning",
      "read": true,
      "createdAt": "2026-04-26T13:48:00-04:00"
    },
    {
      "id": "ntf_8827",
      "type": "announcement",
      "title": "Q1 Town Hall",
      "body": "Friday at 4 PM.",
      "severity": "info",
      "read": true,
      "createdAt": "2026-04-26T09:00:00-04:00"
    }
  ]
}
```

### Type Enum

`pay_change | timesheet_approved | payslip_ready | overtime_warning | schedule_change | geofence_breach | announcement`

### Severity Enum

`info | success | warning | error`

---

## PATCH /api/v1/notifications/{id}/read

### Response Body

```json id="c4n5qb"
{
  "id": "ntf_8821",
  "read": true
}
```

---

## POST /api/v1/notifications/mark-all-read

### Response Body

```json id="m8w4tf"
{
  "markedCount": 4
}
```

---

# Devices Module

Push-notification device registration. Required for any push (timesheet approved, payslip ready, schedule change, geofence breach, etc.).

---

# Endpoints

## POST /api/v1/devices/register

Registers a mobile device.

### Request Body

```json id="v8h3er"
{
  "deviceId": "ios-9F2A-...-B81C",
  "platform": "ios",
  "pushToken": "fcm_or_apns_token",
  "appVersion": "1.4.2"
}
```

### Platform Enum

`ios | android | web`

### Response Body

```json id="g1p9lo"
{
  "success": true,
  "deviceId": "ios-9F2A-...-B81C"
}
```

---

## DELETE /api/v1/devices/{deviceId}

Unregisters a device. Called on logout.

### Response Body

```json id="r2x7ua"
{
  "success": true
}
```





