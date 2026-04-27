# Schedule Module

Employee-facing schedule views.

> Manager-facing scheduling APIs are documented in `Manager_module.md`.

---

# Endpoints

---

## GET /api/v1/schedule/upcoming

Returns upcoming shifts for the employee home screen.

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| limit | integer | No | Number of upcoming shifts to return. Default: `5` |
| from | string | No | Start date filter. Default: today (`YYYY-MM-DD`) |

### Response Body

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
      "role": "Floor Supervisor",
      "shiftType": "regular",
      "assignedBy": "Sarah Jenkins"
    },
    {
      "id": "shf_2032",
      "date": "2026-04-20",
      "dayLabel": "WED",
      "startTime": "08:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "role": "Floor Supervisor",
      "shiftType": "regular",
      "assignedBy": "Sarah Jenkins"
    },
    {
      "id": "shf_2033",
      "date": "2026-04-21",
      "dayLabel": "THU",
      "startTime": "08:00",
      "endTime": "17:00",
      "location": "Warehouse 4, Northern Sector",
      "role": "Floor Supervisor",
      "shiftType": "overtime_extension",
      "assignedBy": "Sarah Jenkins"
    }
  ]
}
````

### Enum

`shiftType`

* `regular`
* `overtime_extension`
* `swap`
* `covering`

---

## GET /api/v1/schedule/calendar

Returns shifts within a date range for the schedule calendar view.

### Query Parameters

| Name | Type   | Required | Description               |
| ---- | ------ | -------- | ------------------------- |
| from | string | Yes      | Start date (`YYYY-MM-DD`) |
| to   | string | Yes      | End date (`YYYY-MM-DD`)   |

### Response Body

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
      "role": "Floor Supervisor",
      "shiftType": "regular",
      "assignedBy": "Sarah Jenkins",
      "status": "scheduled"
    }
  ]
}
```

### Enum

`status`

* `scheduled`
* `completed`
* `missed`
* `cancelled`

---

```
```
