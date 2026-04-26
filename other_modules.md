# Time Off Module

## Endpoints


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
