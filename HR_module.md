# HR Module

Org-wide HR + payroll administration.

**Payroll Context**
- Florida hourly/daily wage payroll
- Currency: USD (cents)
- Employee deductions:
  - Federal Income Tax
  - Social Security (6.2%)
  - Medicare (1.45%)
- Employer-paid:
  - FUTA
  - FL SUTA
- Florida has no state income tax
- Timestamps: `-04:00 (US/Eastern)`

---

## Endpoints

---

## GET /api/v1/hr/dashboard

Org-wide metrics for the HR home screen.

### Response Body

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
    "currentPeriod": "2026-04-15_to_2026-04-28",
    "status": "in_progress",
    "scheduledRunDate": "2026-04-30"
  },
  "complianceAlerts": [
    {
      "id": "comp_1",
      "type": "flsa_breach",
      "message": "2 employees crossed 40 hr threshold without OT approval",
      "severity": "warning"
    }
  ]
}
````

---

# HR — Departments

---

## GET /api/v1/hr/departments

### Response Body

```json
{
  "departments": [
    {
      "id": "D100",
      "name": "Operations",
      "headcount": 84,
      "managerId": "EMP1000"
    }
  ]
}
```

---

## POST /api/v1/hr/departments

### Request Body

```json
{
  "name": "Operations",
  "managerId": "EMP1500",
  "code": "OPS"
}
```

### Response Body

```json
{
  "id": "D200",
  "name": "Operations"
}
```

---

## PATCH /api/v1/hr/departments/{id}

### Request Body

```json
{
  "managerId": "EMP1600"
}
```

### Response Body

```json
{
  "id": "D200",
  "updated": true
}
```

---

## DELETE /api/v1/hr/departments/{id}

### Response Body

```json
{
  "id": "D200",
  "deleted": true
}
```


# HR — Designations

---

## GET /api/v1/hr/designations

### Response Body

```json
{
  "designations": [
    {
      "id": "DES200",
      "title": "Floor Supervisor",
      "departmentId": "D100",
      "level": "L3"
    }
  ]
}
````

---

## POST /api/v1/hr/designations

### Request Body

```json
{
  "title": "Senior Floor Supervisor",
  "departmentId": "D100",
  "level": "L4"
}
```

### Response Body

```json
{
  "id": "DES210",
  "title": "Senior Floor Supervisor"
}
```

---

## PATCH /api/v1/hr/designations/{id}

### Request Body

```json
{
  "level": "L5"
}
```

### Response Body

```json
{
  "id": "DES210",
  "updated": true
}
```

---

## DELETE /api/v1/hr/designations/{id}

### Response Body

```json
{
  "id": "DES210",
  "deleted": true
}
```

---

# HR — Employee Operations

---

## POST /api/v1/hr/employees/bulk-import

Bulk-creates employees from a CSV upload.

### Content Type

`multipart/form-data`

### Multipart Fields

| Field  | Type   | Required | Description                           |
| ------ | ------ | -------- | ------------------------------------- |
| file   | file   | Yes      | CSV with required columns             |
| dryRun | string | No       | `"true"` to validate without creating |

### Response Body

```json
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

## POST /api/v1/hr/employees/{employeeId}/terminate

### Request Body

```json
{
  "lastWorkingDate": "2026-05-30",
  "reason": "Resignation",
  "noticePeriodWaived": false,
  "settleFinalPay": true
}
```

### Response Body

```json
{
  "employeeId": "EMP1001",
  "status": "TERMINATED",
  "finalSettlementId": "fs_4421"
}
```


## POST /api/v1/hr/employees/{employeeId}/rehire

### Request Body

```json
{
  "rejoiningDate": "2026-07-01",
  "designationId": "DES200"
}
````

### Response Body

```json
{
  "employeeId": "EMP1001",
  "status": "ACTIVE"
}
```

---

## POST /api/v1/hr/employees/{employeeId}/transfer

### Request Body

```json
{
  "departmentId": "D200",
  "managerId": "EMP1500",
  "locationId": "wh-2-south",
  "effectiveDate": "2026-05-01"
}
```

### Response Body

```json
{
  "employeeId": "EMP1001",
  "transferId": "trf_99"
}
```

---

# HR — Onboarding

---

## POST /api/v1/hr/onboarding

Starts an onboarding workflow for a new hire.

### Request Body

```json
{
  "employeeId": "EMP1001",
  "templateId": "onb_default",
  "startDate": "2026-04-22"
}
```

### Response Body

```json
{
  "id": "onb_run_1",
  "employeeId": "EMP1001",
  "tasks": [
    {
      "id": "tsk_1",
      "label": "Submit ID proof",
      "status": "pending"
    },
    {
      "id": "tsk_2",
      "label": "Sign offer letter",
      "status": "pending"
    },
    {
      "id": "tsk_3",
      "label": "Sign FLSA policy",
      "status": "pending"
    },
    {
      "id": "tsk_4",
      "label": "Submit W-4",
      "status": "pending"
    },
    {
      "id": "tsk_5",
      "label": "Submit direct deposit form",
      "status": "pending"
    }
  ]
}
```

---

## GET /api/v1/hr/onboarding/{id}

### Response Body

```json
{
  "id": "onb_run_1",
  "employeeId": "EMP1001",
  "progress": 33,
  "tasks": [
    {
      "id": "tsk_1",
      "label": "Submit ID proof",
      "status": "completed",
      "completedAt": "2026-04-22T10:00:00-04:00"
    },
    {
      "id": "tsk_2",
      "label": "Sign offer letter",
      "status": "pending"
    }
  ]
}
```

## PATCH /api/v1/hr/onboarding/{id}/tasks/{taskId}

### Request Body

```json
{
  "status": "completed"
}
````

### Response Body

```json id="0wt9n8"
{
  "id": "tsk_2",
  "status": "completed"
}
```

---

# HR — Holidays

---

## GET /api/v1/hr/holidays

### Query Parameters

| Parameter | Required | Description          |
| --------- | -------- | -------------------- |
| year      | No       | Default current year |
| state     | No       | Example: `FL`        |

### Response Body

```json id="xg10uq"
{
  "year": 2026,
  "holidays": [
    {
      "id": "hol_1",
      "date": "2026-07-04",
      "name": "Independence Day",
      "type": "federal",
      "states": ["all"]
    },
    {
      "id": "hol_2",
      "date": "2026-11-26",
      "name": "Thanksgiving",
      "type": "federal",
      "states": ["all"]
    }
  ]
}
```

**type enum:** `federal | state | company`

---

## POST /api/v1/hr/holidays

### Request Body

```json id="r0h3ok"
{
  "date": "2026-12-25",
  "name": "Christmas Day",
  "type": "federal",
  "states": ["all"]
}
```

### Response Body

```json id="i5kv2q"
{
  "id": "hol_3",
  "name": "Christmas Day"
}
```

---

## PATCH /api/v1/hr/holidays/{id}

### Request Body

```json id="td3g1n"
{
  "name": "Christmas Day (observed)"
}
```

### Response Body

```json id="f8e7y4"
{
  "id": "hol_3",
  "updated": true
}
```

---

## DELETE /api/v1/hr/holidays/{id}

### Response Body

```json id="aym7x6"
{
  "id": "hol_3",
  "deleted": true
}
```

---

# HR — Leave Policies

---

## GET /api/v1/hr/leave-policies

### Response Body

```json id="m0q5se"
{
  "policies": [
    {
      "id": "lp_1",
      "name": "Standard Full-Time",
      "appliesTo": "FULL_TIME",
      "types": [
        {
          "type": "vacation",
          "annualDays": 18,
          "accrualMethod": "monthly",
          "carryForwardMax": 6
        },
        {
          "type": "sick",
          "annualDays": 10,
          "accrualMethod": "upfront",
          "carryForwardMax": 0
        }
      ]
    }
  ]
}
```


## POST /api/v1/hr/leave-policies

### Request Body

```json
{
  "name": "Hourly Workers",
  "appliesTo": "HOURLY",
  "types": [
    {
      "type": "sick",
      "annualDays": 6,
      "accrualMethod": "upfront",
      "carryForwardMax": 0
    }
  ]
}
````

### Response Body

```json id="j2r7fd"
{
  "id": "lp_2",
  "name": "Hourly Workers"
}
```

---

## PATCH /api/v1/hr/leave-policies/{id}

### Request Body

```json id="s4mv0p"
{
  "types": [
    {
      "type": "sick",
      "annualDays": 8,
      "accrualMethod": "upfront",
      "carryForwardMax": 0
    }
  ]
}
```

### Response Body

```json id="c9u6et"
{
  "id": "lp_2",
  "updated": true
}
```

---

## POST /api/v1/hr/leave-policies/{id}/assign

### Request Body

```json id="v0wx7n"
{
  "employeeIds": [
    "EMP1001",
    "EMP1002"
  ]
}
```

### Response Body

```json id="m1k5za"
{
  "assigned": 2
}
```

---

# HR — Pay Components & Salary

---

## GET /api/v1/hr/pay-components

Configured earnings + deductions.

**Florida defaults**

* REGULAR
* OVERTIME
* FEDERAL_INCOME_TAX
* SOCIAL_SECURITY (6.2%)
* MEDICARE (1.45%)

### Response Body

```json id="f6q3yo"
{
  "components": [
    {
      "id": "pc_regular",
      "code": "REGULAR",
      "name": "Regular Pay",
      "kind": "earning",
      "type": "calculated",
      "taxable": true
    },
    {
      "id": "pc_overtime",
      "code": "OVERTIME",
      "name": "Overtime Pay",
      "kind": "earning",
      "type": "calculated",
      "taxable": true,
      "multiplier": 1.5
    },
    {
      "id": "pc_fed_income",
      "code": "FEDERAL_INCOME_TAX",
      "name": "Federal Income Tax",
      "kind": "deduction",
      "type": "tax_table"
    },
    {
      "id": "pc_ss",
      "code": "SOCIAL_SECURITY",
      "name": "Social Security",
      "kind": "deduction",
      "type": "percentage",
      "percentageOf": "grossPay",
      "percentage": 6.2
    },
    {
      "id": "pc_medicare",
      "code": "MEDICARE",
      "name": "Medicare",
      "kind": "deduction",
      "type": "percentage",
      "percentageOf": "grossPay",
      "percentage": 1.45
    }
  ]
}
```

**kind enum:** `earning | deduction`
**type enum:** `fixed | percentage | calculated | tax_table`

---

## POST /api/v1/hr/pay-components

### Request Body

```json id="p2dn9w"
{
  "code": "BONUS",
  "name": "Performance Bonus",
  "kind": "earning",
  "type": "fixed",
  "amount": 50000,
  "taxable": true
}
```

### Response Body

```json id="z8cf6r"
{
  "id": "pc_bonus",
  "code": "BONUS"
}
```

## PATCH /api/v1/hr/pay-components/{id}

### Request Body

```json
{
  "amount": 75000
}
````

### Response Body

```json id="v7m2xd"
{
  "id": "pc_bonus",
  "updated": true
}
```

---

## GET /api/v1/hr/employees/{employeeId}/salary

### Response Body — Hourly

```json id="a3k8pl"
{
  "employeeId": "EMP1001",
  "currency": "USD",
  "payType": "hourly",
  "hourlyRate": 2200,
  "overtimeMultiplier": 1.5,
  "effectiveFrom": "2026-04-01"
}
```

### Response Body — Daily

```json id="x4r9mu"
{
  "employeeId": "EMP1099",
  "currency": "USD",
  "payType": "daily",
  "dailyRate": 18000,
  "extraDayRate": 27000,
  "effectiveFrom": "2026-04-01"
}
```

---

## PUT /api/v1/hr/employees/{employeeId}/salary

Creates a new effective record (history is preserved).

### Request Body

```json id="n6q1st"
{
  "payType": "hourly",
  "hourlyRate": 2400,
  "overtimeMultiplier": 1.5,
  "effectiveFrom": "2026-05-01"
}
```

### Response Body

```json id="b0z7yk"
{
  "revisionId": "sal_rev_55",
  "effectiveFrom": "2026-05-01"
}
```

---

# HR — Payroll Runs

**Lifecycle:** `draft → processing → awaiting_approval → approved → locked → paid`

(Can be `cancelled` at any stage before `locked`)

---

## GET /api/v1/hr/payroll/runs

### Query Parameters

| Parameter | Required | Description      |
| --------- | -------- | ---------------- |
| year      | No       | Filter by year   |
| status    | No       | Filter by status |

### Response Body

```json id="f8r2ow"
{
  "runs": [
    {
      "id": "pr_2026_04_p1",
      "period": "2026-04-15_to_2026-04-28",
      "status": "in_progress",
      "employeesIncluded": 312,
      "grossTotal": 48297700,
      "netTotal": 38421500,
      "currency": "USD",
      "scheduledPayDate": "2026-04-30"
    }
  ]
}
```

**status enum:**
`draft | processing | awaiting_approval | approved | locked | paid | cancelled`

---

## POST /api/v1/hr/payroll/runs

Creates a new payroll run.

### Request Body

```json id="h5u0ca"
{
  "period": "2026-04-15_to_2026-04-28",
  "payDate": "2026-04-30",
  "includeEmployeeFilter": {
    "status": "ACTIVE",
    "state": "FL"
  }
}
```

### Response Body

```json id="r1e6vp"
{
  "id": "pr_2026_04_p1",
  "status": "draft"
}
```

---

## POST /api/v1/hr/payroll/runs/{id}/process

Calculates payroll (regular + overtime + federal tax + FICA).

### Response Body

```json id="q8k3lx"
{
  "id": "pr_2026_04_p1",
  "status": "processing",
  "estimatedCompletionAt": "2026-04-30T11:30:00-04:00"
}
```

---

## GET /api/v1/hr/payroll/runs/{id}

Calculated breakdown. `lines[]` carry the Florida hourly/daily fields.

### Response Body

```json id="u2m7fd"
{
  "id": "pr_2026_04_p1",
  "period": "2026-04-15_to_2026-04-28",
  "status": "awaiting_approval",
  "currency": "USD",
  "summary": {
    "employees": 312,
    "regularPayTotal": 41250000,
    "overtimePayTotal": 7047700,
    "grossTotal": 48297700,
    "deductionsTotal": 9876200,
    "federalIncomeTaxTotal": 5435000,
    "socialSecurityTotal": 2994460,
    "medicareTotal": 700317,
    "netTotal": 38421500
  },
  "lines": [
    {
      "employeeId": "EMP1001",
      "name": "Priya Sharma",
      "payType": "hourly",
      "regularHours": 80.0,
      "overtimeHours": 4.5,
      "regularPay": 176000,
      "overtimePay": 14850,
      "grossPay": 190850,
      "deductions": {
        "federalIncomeTax": 18800,
        "socialSecurity": 11833,
        "medicare": 2767,
        "total": 33400
      },
      "netPay": 157450,
      "disbursementMethod": "direct_deposit"
    },
    {
      "employeeId": "EMP1099",
      "name": "John Doe",
      "payType": "daily",
      "regularDays": 10,
      "extraDays": 1,
      "regularPay": 180000,
      "overtimePay": 27000,
      "grossPay": 207000,
      "deductions": {
        "federalIncomeTax": 20400,
        "socialSecurity": 12834,
        "medicare": 3002,
        "total": 36236
      },
      "netPay": 170764,
      "disbursementMethod": "check"
    }
  ]
}
```

---

## POST /api/v1/hr/payroll/runs/{id}/approve

Approves a processed run.

### Response Body

```json id="t9w4nh"
{
  "id": "pr_2026_04_p1",
  "status": "approved",
  "approvedAt": "2026-04-29T18:00:00-04:00"
}
```

## POST /api/v1/hr/payroll/runs/{id}/lock

Locks the payroll period. No more timesheet edits are accepted after lock.  
Explicit lock confirmation required before disbursement.

### Request Body

```json
{
  "confirmed": true,
  "confirmationCode": "LOCK-PR-2026-04-P1"
}
````

### Response Body

```json
{
  "id": "pr_2026_04_p1",
  "status": "locked",
  "lockedAt": "2026-04-29T18:30:00-04:00",
  "lockedBy": "MGR-1042"
}
```

---

## POST /api/v1/hr/payroll/runs/{id}/publish

Publishes payslips, generates ACH file for direct-deposit employees, and generates check register for check employees.

### Response Body

```json
{
  "id": "pr_2026_04_p1",
  "status": "paid",
  "payslipsPublished": 312,
  "directDeposit": {
    "employeeCount": 245,
    "totalAmount": 38421500,
    "achFileUrl": "https://files.zexovo.com/signed/ach_pr_2026_04_p1?token=...",
    "achTraceNumber": "021000021000123",
    "submittedAt": "2026-04-29T18:30:00-04:00",
    "expectedSettlementDate": "2026-05-01"
  },
  "check": {
    "employeeCount": 67,
    "totalAmount": 9876200,
    "checkRegisterUrl": "https://files.zexovo.com/signed/check_register_pr_2026_04_p1?token=..."
  }
}
```

---

## POST /api/v1/hr/payroll/runs/{id}/cancel

Cancels a payroll run in `draft`, `processing`, or `awaiting_approval` state.
Locked or paid runs cannot be cancelled.

### Response Body

```json
{
  "id": "pr_2026_04_p1",
  "status": "cancelled"
}
```

## GET /api/v1/hr/payroll/runs/{id}/ach-file

Re-fetches the ACH file in NACHA format.

### Response Body

```json
{
  "url": "https://files.zexovo.com/signed/ach_pr_2026_04_p1?token=...",
  "expiresAt": "2026-04-29T19:30:00-04:00",
  "format": "NACHA",
  "totalAmount": 38421500,
  "employeeCount": 245
}
````

---

## GET /api/v1/hr/payroll/runs/{id}/check-register

Returns detailed check register for employees paid by check.

### Response Body

```json
{
  "url": "https://files.zexovo.com/signed/check_register_pr_2026_04_p1?token=...",
  "expiresAt": "2026-04-29T19:30:00-04:00",
  "totalAmount": 9876200,
  "employeeCount": 67,
  "checks": [
    {
      "employeeId": "EMP1099",
      "employeeName": "John Doe",
      "amount": 170764,
      "memo": "Payroll 04/15-04/28",
      "mailingAddress": "123 Main St, Orlando, FL 32801"
    }
  ]
}
```

---

## POST /api/v1/hr/payroll/runs/{id}/checks/print

Marks checks as printed and assigns sequential check numbers.

### Request Body

```json
{
  "startCheckNumber": 1245,
  "printedAt": "2026-04-30T10:00:00-04:00"
}
```

### Response Body

```json
{
  "checksPrinted": 67,
  "lastCheckNumber": 1311
}
```

# HR — Tax Configuration (Florida / Federal)

## GET /api/v1/hr/tax/slabs

Returns federal income tax slabs by W-4 filing status, plus FICA rates.  
Florida has no state income tax (`stateIncomeTax: false`).

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| year | number | No | Default current year |
| filingStatus | string | No | `single` \| `married_jointly` \| `married_separately` \| `head_of_household` |

### Response Body

```json
{
  "year": 2026,
  "country": "US",
  "state": "FL",
  "stateIncomeTax": false,
  "federal": {
    "filingStatus": "single",
    "slabs": [
      { "minIncome": 0, "maxIncome": 1160000, "ratePercent": 10 },
      { "minIncome": 1160001, "maxIncome": 4710500, "ratePercent": 12 },
      { "minIncome": 4710501, "maxIncome": 10367500, "ratePercent": 22 },
      { "minIncome": 10367501, "maxIncome": 19792500, "ratePercent": 24 },
      { "minIncome": 19792501, "maxIncome": 25195000, "ratePercent": 32 },
      { "minIncome": 25195001, "maxIncome": 62635000, "ratePercent": 35 },
      { "minIncome": 62635001, "maxIncome": null, "ratePercent": 37 }
    ]
  },
  "fica": {
    "socialSecurity": {
      "ratePercent": 6.2,
      "wageBase": 17640000
    },
    "medicare": {
      "ratePercent": 1.45,
      "wageBase": null
    }
  },
  "employerPaid": [
    { "code": "FUTA", "ratePercent": 0.6, "wageBase": 700000 },
    { "code": "FL_SUTA", "ratePercent": 2.7, "wageBase": 700000 }
  ]
}
````

---

## PUT /api/v1/hr/tax/slabs

Replaces tax slab configuration for a given year and filing status.

### Request Body

```json
{
  "year": 2026,
  "filingStatus": "single",
  "slabs": [
    {
      "minIncome": 0,
      "maxIncome": 1160000,
      "ratePercent": 10
    }
  ]
}
```

### Response Body

```json
{
  "year": 2026,
  "filingStatus": "single",
  "updated": true
}
```


## POST /api/v1/hr/tax/employees/{employeeId}/declarations

Stores the employee's W-4 tax declaration.

### Request Body

```json
{
  "year": 2026,
  "filingStatus": "single",
  "exemptions": 1,
  "additionalWithholding": 0,
  "ssnLast4": "4421"
}
````

### Response Body

```json
{
  "id": "dec_4421",
  "status": "submitted"
}
```

---

# HR — Bulk Documents

## POST /api/v1/hr/documents/bulk-upload

Uploads payslip / W-2 / 1099 batches.
ZIP file must contain PDFs named `<employeeId>.pdf`.

### Content Type

`multipart/form-data`

### Multipart Fields

| Field        | Type   | Required | Description                                       |
| ------------ | ------ | -------- | ------------------------------------------------- |
| file         | file   | Yes      | ZIP of PDFs                                       |
| documentType | string | Yes      | `payslip` | `w2` | `1095_c` | `1099`              |
| period       | string | Yes      | `YYYY-MM` for payslips, `YYYY` for annual filings |

### Response Body

```json
{
  "totalFiles": 312,
  "matched": 310,
  "unmatched": 2,
  "errors": [
    {
      "fileName": "EMP9999.pdf",
      "code": "EMPLOYEE_NOT_FOUND"
    }
  ]
}
```

# HR — Compliance Documents & E-Signature

## GET /api/v1/hr/compliance-documents

Lists company compliance documents such as FLSA policy, geofence consent, handbook, NDA, and safety training.

### Type Enum

`flsa_policy` | `geofence_consent` | `handbook` | `nda` | `safety_training`

### Response Body

```json
{
  "documents": [
    {
      "id": "comp_flsa_2026",
      "name": "FLSA Overtime Policy 2026",
      "type": "flsa_policy",
      "version": "1.2",
      "effectiveDate": "2026-01-01",
      "requiredFor": ["all"],
      "signaturesRequired": true
    },
    {
      "id": "comp_geo_2026",
      "name": "Geofence Consent",
      "type": "geofence_consent",
      "version": "1.0",
      "effectiveDate": "2026-04-01",
      "requiredFor": ["hourly"],
      "signaturesRequired": true
    }
  ]
}
````

---

## POST /api/v1/hr/compliance-documents/{id}/request-signature

Requests signatures from selected employees.

### Request Body

```json
{
  "employeeIds": ["EMP1001", "EMP1002"],
  "dueDate": "2026-05-15"
}
```

### Response Body

```json
{
  "requestsCreated": 2,
  "requestIds": ["sig_1", "sig_2"]
}
```

---

## GET /api/v1/hr/signature-requests/{id}/audit-trail

Returns full audit trail for a signature request.

### Response Body

```json
{
  "id": "sig_1",
  "documentId": "comp_flsa_2026",
  "employeeId": "EMP1001",
  "events": [
    {
      "event": "requested",
      "actor": "MGR-1042",
      "at": "2026-04-26T09:00:00-04:00"
    },
    {
      "event": "viewed",
      "actor": "EMP1001",
      "at": "2026-04-26T18:14:00-04:00",
      "ipAddress": "73.45.21.18"
    },
    {
      "event": "signed",
      "actor": "EMP1001",
      "at": "2026-04-27T10:14:00-04:00",
      "ipAddress": "73.45.21.18",
      "deviceFingerprint": "ios-9F2A-..."
    }
  ]
}
```

# HR — Reports

## GET /api/v1/hr/reports/attendance

Returns attendance analytics for a date range.

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| from | string | Yes | Start date (`YYYY-MM-DD`) |
| to | string | Yes | End date (`YYYY-MM-DD`) |
| departmentId | string | No | Filter by department |
| locationId | string | No | Filter by location |

### Response Body

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
      "departmentName": "Operations",
      "attendance": 96.1
    }
  ]
}
````

---

## GET /api/v1/hr/reports/payroll

Returns payroll summary for a payroll period.

### Query Parameters

| Name   | Type   | Required | Description                                        |
| ------ | ------ | -------- | -------------------------------------------------- |
| period | string | Yes      | `YYYY-MM` or full range `YYYY-MM-DD_to_YYYY-MM-DD` |

### Response Body

```json id="t7z8pl"
{
  "period": "2026-04-15_to_2026-04-28",
  "currency": "USD",
  "totalGross": 48297700,
  "totalNet": 38421500,
  "totalFederalIncomeTax": 5435000,
  "totalSocialSecurity": 2994460,
  "totalMedicare": 700317,
  "byDepartment": [
    {
      "departmentId": "D100",
      "gross": 19200000,
      "net": 15300000
    }
  ],
  "byPayType": [
    {
      "payType": "hourly",
      "employees": 245,
      "gross": 38421500
    },
    {
      "payType": "daily",
      "employees": 67,
      "gross": 9876200
    }
  ]
}
```

---

## GET /api/v1/hr/reports/flsa

Returns FLSA / overtime compliance report.

### Query Parameters

| Name      | Type   | Required | Description                    |
| --------- | ------ | -------- | ------------------------------ |
| weekStart | string | Yes      | Week start date (`YYYY-MM-DD`) |

### Response Body

```json id="y51mwp"
{
  "weekStart": "2026-04-20",
  "totalEmployees": 312,
  "overThreshold": 8,
  "overtimeApprovedCount": 6,
  "overtimeUnapprovedCount": 2,
  "violations": [
    {
      "employeeId": "EMP1099",
      "name": "John Doe",
      "hoursWorked": 41.5,
      "overtimeApproved": false
    }
  ]
}
```

## GET /api/v1/hr/reports/headcount

Returns headcount trend and attrition metrics for a month range.

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| from | string | Yes | Start month (`YYYY-MM`) |
| to | string | Yes | End month (`YYYY-MM`) |

### Response Body

```json
{
  "from": "2026-01",
  "to": "2026-04",
  "trend": [
    {
      "period": "2026-01",
      "headcount": 298,
      "joined": 8,
      "left": 3
    },
    {
      "period": "2026-02",
      "headcount": 302,
      "joined": 6,
      "left": 2
    }
  ],
  "attritionRateAnnualized": 4.6
}
````

---

## POST /api/v1/hr/reports/{reportType}/export

Triggers an asynchronous report export.

### Request Body

```json
{
  "format": "xlsx",
  "filters": {
    "from": "2026-04-01",
    "to": "2026-04-30"
  }
}
```

### Response Body

```json
{
  "exportId": "exp_771",
  "status": "processing"
}
```

---

## GET /api/v1/hr/reports/exports/{exportId}

Returns export job status and download link when ready.

### Response Body

```json
{
  "exportId": "exp_771",
  "status": "ready",
  "downloadUrl": "https://files.zexovo.com/signed/exp_771?token=...",
  "expiresAt": "2026-04-26T12:00:00-04:00"
}
```


# HR — Announcements

## GET /api/v1/hr/announcements

Returns all published or scheduled announcements.

### Response Body

```json
{
  "announcements": [
    {
      "id": "ann_1",
      "title": "Q1 Town Hall",
      "body": "Join us on April 30 at 4 PM EDT.",
      "audience": "all",
      "publishedAt": "2026-04-26T09:00:00-04:00"
    }
  ]
}
````

---

## POST /api/v1/hr/announcements

Creates or schedules a new announcement.

### Audience Examples

* `all`
* `department:D100`
* `location:wh-4-north`
* `payType:hourly`

### Request Body

```json
{
  "title": "Office closure on Friday",
  "body": "All offices closed for maintenance.",
  "audience": "department:D100",
  "publishAt": "2026-04-29T09:00:00-04:00"
}
```

### Response Body

```json
{
  "id": "ann_2",
  "status": "scheduled"
}
```

---

## DELETE /api/v1/hr/announcements/{id}

Deletes an announcement.

### Response Body

```json
{
  "id": "ann_2",
  "deleted": true
}
```

---

# HR — Audit Logs

## GET /api/v1/hr/audit-logs

Returns paginated audit logs for HR operations.

### Query Parameters

| Name       | Type   | Required | Description                                                            |
| ---------- | ------ | -------- | ---------------------------------------------------------------------- |
| actorId    | string | No       | Filter by actor                                                        |
| entityType | string | No       | `employee` | `payroll_run` | `policy` | `signature_request` | `salary` |
| entityId   | string | No       | Filter by entity ID                                                    |
| from       | string | No       | Start datetime                                                         |
| to         | string | No       | End datetime                                                           |
| page       | number | No       | Page number                                                            |
| size       | number | No       | Page size                                                              |

### Response Body

```json
{
  "data": [
    {
      "id": "log_8821",
      "actorId": "EMP_HR_1",
      "actorName": "Anita Rao",
      "action": "PAYROLL_RUN_LOCKED",
      "entityType": "payroll_run",
      "entityId": "pr_2026_04_p1",
      "before": {
        "status": "approved"
      },
      "after": {
        "status": "locked"
      },
      "occurredAt": "2026-04-29T18:30:00-04:00",
      "ipAddress": "10.4.2.18"
    }
  ],
  "page": 1,
  "size": 50,
  "total": 1421
}
```








```
```
