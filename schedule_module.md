# Schedule Shift Module

## Endpoints


### GET /api/v1/schedule/upcoming

Returns upcoming shifts for the home screen.
### Query Parameters
limit (optional, default 5), from (optional, default = today)


#### Response Body

```json
{
  "shifts": [
    {
      "id": "shf_2031",
      "date": "2026-04-19",
      "dayLabel": "TUE",
      "startTime": "09:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "assignedBy": "Sarah Jenkins"
    },
    {
      "id": "shf_2032",
      "date": "2026-04-20",
      "dayLabel": "WED",
      "startTime": "08:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "assignedBy": "Sarah Jenkins"
    },
    {
      "id": "shf_2033",
      "date": "2026-04-21",
      "dayLabel": "THU",
      "startTime": "08:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "assignedBy": "Sarah Jenkins"
    }
  ]
}
```

### GET /api/v1/schedule/calendar
Returns shifts within a date range for the schedule calendar view.
### Query Parameters
from (required, YYYY-MM-DD), to (required, YYYY-MM-DD)

```json
{
  "from": "2026-04-01",
  "to": "2026-04-30",
  "shifts": [
    {
      "id": "shf_2031",
      "date": "2026-04-19",
      "startTime": "09:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "assignedBy": "Sarah Jenkins",
      "status": "scheduled"
    }
  ]
}
```
## Manager Shift Scheduling Write APIs

### POST `/api/v1/manager/schedule/shifts`

Creates a new shift for an employee.

#### Request Body

```json id="k3n8wa"
{
  "employeeId": "EMP1001",
  "date": "2026-04-28",
  "startTime": "09:00",
  "endTime": "17:00",
  "locationId": "wh-4-north",
  "notes": "Floor B coverage"
}
```

#### Response Body

```json id="t6m1qe"
{
  "id": "shf_2050",
  "status": "scheduled"
}
```

---

### PATCH `/api/v1/manager/schedule/shifts/{shiftId}`

Updates an existing shift.

#### Request Body

```json id="v4x7ra"
{
  "startTime": "10:00",
  "endTime": "18:00"
}
```

#### Response Body

```json id="h9p2lu"
{
  "id": "shf_2050",
  "status": "scheduled"
}
```

---

### DELETE `/api/v1/manager/schedule/shifts/{shiftId}`

Deletes a scheduled shift.

#### Response Body

```json id="q8d5zn"
{
  "id": "shf_2050",
  "deleted": true
}
```

---

### POST `/api/v1/manager/schedule/shifts/bulk-assign`

Creates multiple shifts for multiple employees and dates.

#### Request Body

```json id="m2f7ko"
{
  "employeeIds": [
    "EMP1001",
    "EMP1002"
  ],
  "dates": [
    "2026-04-28",
    "2026-04-29"
  ],
  "startTime": "09:00",
  "endTime": "17:00",
  "locationId": "wh-4-north"
}
```

#### Response Body

```json id="j6s4cy"
{
  "createdShifts": 4
}
```
