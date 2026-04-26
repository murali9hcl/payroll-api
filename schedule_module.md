# Attendence Module

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

