````md
# Attendance Module

Time tracking for hourly and daily wage employees.

**Florida FLSA Rules**
- Regular hours up to **40/week**
- Anything above is **overtime at 1.5×**

**Currency**
- All amounts in cents (USD)

---

# Endpoints

---

## GET /api/v1/attendance/today

Returns the current day's attendance state. Drives the home screen Clock In / Break In button states.

### Response Body

```json
{
  "date": "2026-04-26",
  "shift": {
    "id": "shf_2031",
    "scheduled": true,
    "startTime": "09:00",
    "endTime": "17:00",
    "status": "in_progress",
    "role": "Floor Supervisor",
    "location": "Warehouse 4, Northern Sector"
  },
  "attendance": {
    "id": "att_55021",
    "status": "clocked_in",
    "clockInTime": "2026-04-26T09:02:00-04:00",
    "elapsed": "7h 43m",
    "remaining": "0h 17m",
    "onBreak": false,
    "breakDurationMinutes": 30
  },
  "geofence": {
    "withinRadius": true,
    "distanceMeters": 12,
    "label": "Within building radius"
  },
  "actions": {
    "canClockIn": false,
    "canClockOut": true,
    "canStartBreak": true,
    "canEndBreak": false,
    "canRequestClockIn": false
  }
}
````

### Enums

* `shift.status`: `not_started | in_progress | completed | missed | none_scheduled`
* `attendance.status`: `not_clocked_in | clocked_in | on_break | clocked_out`

---

## POST /api/v1/attendance/clock-in

Records a clock-in. Validates geofence and scheduled shift window.

### Request Body

```json
{
  "shiftId": "shf_2031",
  "timestamp": "2026-04-26T09:02:00-04:00",
  "location": {
    "latitude": 28.5383,
    "longitude": -81.3792,
    "accuracyMeters": 8
  },
  "deviceId": "ios-9F2A-...-B81C"
}
```

### Response Body

```json
{
  "attendanceId": "att_55021",
  "shiftId": "shf_2031",
  "status": "clocked_in",
  "withinRadius": true,
  "clockInTime": "2026-04-26T09:02:00-04:00",
  "geofence": {
    "locationId": "wh-4-north",
    "radiusMeters": 75,
    "distanceMeters": 12
  }
}
```

### Negative Responses

```json
{
  "error": {
    "code": "GEOFENCE_VIOLATION",
    "message": "You must be within the assigned location radius to clock in.",
    "details": {
      "distanceMeters": 142,
      "allowedRadiusMeters": 75
    }
  }
}
```

```json
{
  "error": {
    "code": "SHIFT_NOT_STARTED",
    "message": "Your shift starts at 09:00. Try again then."
  }
}
```

```json
{
  "error": {
    "code": "ALREADY_CLOCKED_IN",
    "message": "You are already clocked in.",
    "details": {
      "attendanceId": "att_55021"
    }
  }
}
```

---

## POST /api/v1/attendance/clock-out

Records clock-out and finalizes shift totals.

### Request Body

```json
{
  "attendanceId": "att_55021",
  "timestamp": "2026-04-26T17:18:00-04:00",
  "location": {
    "latitude": 28.5383,
    "longitude": -81.3792,
    "accuracyMeters": 6
  }
}
```

### Response Body

```json
{
  "attendanceId": "att_55021",
  "status": "clocked_out",
  "clockInTime": "2026-04-26T09:02:00-04:00",
  "clockOutTime": "2026-04-26T17:18:00-04:00",
  "totalDuration": "8h 16m",
  "breakDurationMinutes": 30,
  "processedDuration": "7h 46m",
  "scheduledHours": 8.00,
  "regularHoursToday": 7.77,
  "overtimeHoursToday": 0,
  "workedHours": 7.77,
  "onSiteHours": 6.55,
  "offSiteHours": 1.22,
  "onSitePercentage": 84,
  "flagged": false,
  "weeklySummary": {
    "totalHours": 39.72,
    "remainingHours": 0.28,
    "flsaThresholdHours": 40,
    "approachingOvertime": true
  }
}
```

---

## POST /api/v1/attendance/break/start

Starts a break.

### Request Body

```json
{
  "attendanceId": "att_55021",
  "timestamp": "2026-04-26T13:00:00-04:00",
  "type": "lunch"
}
```

### Enum

`type`: `lunch | rest | personal`

### Response Body

```json
{
  "breakId": "brk_771",
  "attendanceId": "att_55021",
  "status": "on_break",
  "startTime": "2026-04-26T13:00:00-04:00"
}
```

---

## POST /api/v1/attendance/break/end

Ends active break.

### Request Body

```json
{
  "breakId": "brk_771",
  "timestamp": "2026-04-26T13:30:00-04:00"
}
```

### Response Body

```json
{
  "breakId": "brk_771",
  "status": "completed",
  "startTime": "2026-04-26T13:00:00-04:00",
  "endTime": "2026-04-26T13:30:00-04:00",
  "durationMinutes": 30
}
```

---

## POST /api/v1/attendance/request-clock-in

Requests off-schedule clock-in.

### Request Body

```json
{
  "date": "2026-04-26",
  "reason": "Asked to cover shift",
  "expectedHours": 6
}
```

### Response Body

```json
{
  "requestId": "req_4421",
  "status": "pending_manager_approval",
  "submittedAt": "2026-04-26T08:55:00-04:00"
}
```

---

## GET /api/v1/attendance/request-clock-in

Lists employee requests.

### Query Parameters

* `status` (optional): `pending | approved | rejected | all`

### Response Body

```json
{
  "requests": [
    {
      "id": "req_4421",
      "date": "2026-04-26",
      "reason": "Asked to cover shift",
      "expectedHours": 6,
      "status": "pending_manager_approval",
      "submittedAt": "2026-04-26T08:55:00-04:00"
    }
  ]
}
```

---

## DELETE /api/v1/attendance/request-clock-in/{requestId}

Cancels pending request.

### Response Body

```json
{
  "id": "req_4421",
  "status": "cancelled"
}
```

---

## GET /api/v1/attendance/presence-timeline

Returns chronological presence events for a day.

### Query Parameters

* `date` (required): `YYYY-MM-DD`

### Response Body

```json
{
  "date": "2026-04-26",
  "attendanceId": "att_55021",
  "summary": {
    "totalDuration": "8h 16m",
    "onSiteHours": 6.55,
    "offSiteHours": 1.22,
    "onSitePercentage": 84
  },
  "events": [
    { "id": "evt_1", "type": "clocked_in", "label": "Clocked in (Inside)", "time": "09:02", "flagged": false },
    { "id": "evt_2", "type": "left_radius", "label": "Left radius", "time": "11:25", "flagged": false },
    { "id": "evt_3", "type": "returned", "label": "Returned", "time": "12:42", "flagged": false },
    { "id": "evt_4", "type": "left_radius", "label": "Left radius", "time": "15:50", "flagged": true },
    { "id": "evt_5", "type": "clocked_out", "label": "Clocked out", "time": "17:18", "flagged": false }
  ]
}
```

---

## GET /api/v1/attendance/history

Returns monthly attendance calendar.

### Query Parameters

* `month` (required): `YYYY-MM`
* `filter` (optional): `all | on_site | remote | flagged`

### Response Body

```json
{
  "month": "2026-04",
  "filter": "all",
  "monthlyAverage": 96.4,
  "maxHoursPerDay": 9,
  "days": [
    { "date": "2026-04-01", "status": "on_site", "workedHours": 8.02 },
    { "date": "2026-04-02", "status": "remote", "workedHours": 7.50 },
    { "date": "2026-04-15", "status": "flagged", "workedHours": 7.72 },
    { "date": "2026-04-21", "status": "leave", "workedHours": 0 },
    { "date": "2026-04-25", "status": "weekoff", "workedHours": 0 }
  ]
}
```

---

## GET /api/v1/attendance/stats

Attendance insights.

### Query Parameters

* `period`: `weekly | monthly | quarterly`
* `month` (required if monthly)
* `weekStart` (required if weekly)

### Response Body

```json
{
  "period": "monthly",
  "month": "2026-04",
  "averageAttendance": 96.4,
  "consistencyScore": 92,
  "trendDirection": "up",
  "improvementVsPreviousMonth": 12,
  "onTimeRate": 98.1,
  "flaggedDays": 1
}
```

```
```
