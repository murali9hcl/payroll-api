# Attendence Module

## Endpoints


### POST /api/v1/attendance/clock-in

Records a clock-in event. Validates the employee is within the geofence and inside their scheduled window. Returns the updated shift state.

#### Request Body

```json
{
  "shiftId": "shf_2031",
  "timestamp": "2026-04-26T09:02:00+05:30",
  "location": {
    "latitude": 17.4435,
    "longitude": 78.3772,
    "accuracyMeters": 8
  },
  "deviceId": "ios-9F2A-...-B81C"
}
```

#### Response Body

```json
{
  "attendanceId": "att_55021",
  "shiftId": "shf_2031",
  "status": "clocked_in",
  "withinRadius": true,
  "clockInTime": "2026-04-26T09:02:00+05:30",
  "geofence": {
    "locationId": "wh-4-north",
    "radiusMeters": 75,
    "distanceMeters": 12
  }
}
```
### POST /api/v1/attendance/clock-out

Records a clock-out event and finalizes the shift totals.

#### Request Body

```json
{
  "attendanceId": "att_55021",
  "timestamp": "2026-04-26T17:18:00+05:30",
  "location": {
    "latitude": 17.4435,
    "longitude": 78.3772,
    "accuracyMeters": 6
  }
}
```

#### Response Body

```json
{
  "attendanceId": "att_55021",
  "status": "clocked_out",
  "clockInTime": "2026-04-26T09:02:00+05:30",
  "clockOutTime": "2026-04-26T17:18:00+05:30",
  "totalDuration": "8h 02m",
  "scheduledHours": 8.00,
  "workedHours": 8.02,
  "onSiteHours": 6.55,
  "offSiteHours": 1.47,
  "onSitePercentage": 82,
  "flagged": false
}
```

### POST /api/v1/attendance/clock-out

Records a clock-out event and finalizes the shift totals.

#### Request Body

```json
{
  "attendanceId": "att_55021",
  "timestamp": "2026-04-26T17:18:00+05:30",
  "location": {
    "latitude": 17.4435,
    "longitude": 78.3772,
    "accuracyMeters": 6
  }
}
```

#### Response Body

```json
{
  "attendanceId": "att_55021",
  "status": "clocked_out",
  "clockInTime": "2026-04-26T09:02:00+05:30",
  "clockOutTime": "2026-04-26T17:18:00+05:30",
  "totalDuration": "8h 02m",
  "scheduledHours": 8.00,
  "workedHours": 8.02,
  "onSiteHours": 6.55,
  "offSiteHours": 1.47,
  "onSitePercentage": 82,
  "flagged": false
}
```
### POST /api/v1/attendance/break/start

Starts a break in the current shift.

### Request Body
```json
{
  "attendanceId": "att_55021",
  "timestamp": "2026-04-26T13:00:00+05:30",
  "type": "lunch"
}
```

### Response Body
```json
{
  "breakId": "brk_771",
  "attendanceId": "att_55021",
  "status": "on_break",
  "startTime": "2026-04-26T13:00:00+05:30"
}
```

### POST /api/v1/attendance/break/end

Ends an active break.

### Request Body
```json
{
  "breakId": "brk_771",
  "timestamp": "2026-04-26T13:30:00+05:30"
}
```

### Response Body
```json
{
  "breakId": "brk_771",
  "status": "completed",
  "startTime": "2026-04-26T13:00:00+05:30",
  "endTime": "2026-04-26T13:30:00+05:30",
  "durationMinutes": 30
}
```

### POST /api/v1/attendance/request-clock-in

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
  "submittedAt": "2026-04-26T08:55:00+05:30"
}
```
### GET /api/v1/attendance/presence-timeline
Returns the chronological presence events for a given day — clock-in, geofence exits/returns, flagged windows, and clock-out.
### Query Parameters
date (required, YYYY-MM-DD)

### Response Body
```json
{
  "date": "2026-04-26",
  "attendanceId": "att_55021",
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
    },
    {
      "id": "evt_2",
      "type": "left_radius",
      "label": "Left radius (Exit Perimeter A)",
      "time": "11:25",
      "flagged": false
    },
    {
      "id": "evt_3",
      "type": "returned",
      "label": "Returned (Office Back Entrance)",
      "time": "12:42",
      "flagged": false
    },
    {
      "id": "evt_4",
      "type": "left_radius",
      "label": "Left radius (Exit Perimeter A)",
      "time": "15:50",
      "flagged": true
    },
    {
      "id": "evt_5",
      "type": "clocked_out",
      "label": "Clocked out (Inside)",
      "time": "17:18",
      "flagged": false
    }
  ]
}
```

### GET /api/v1/attendance/history
Returns the calendar view of attendance for a given month with per-day status.
### Query Parameters
month (required, YYYY-MM), filter (optional: all | on_site | remote, default all)

### Response Body
```json
{
  "month": "2026-04",
  "filter": "all",
  "monthlyAverage": 96.4,
  "maxHoursPerDay": 9,
  "days": [
    {
      "date": "2026-04-01",
      "status": "on_site",
      "workedHours": 8.02,
      "flagged": false
    },
    {
      "date": "2026-04-02",
      "status": "remote",
      "workedHours": 7.50,
      "flagged": false
    },
    {
      "date": "2026-04-15",
      "status": "flagged",
      "workedHours": 7.72,
      "flagged": true
    }
  ]
}
```

### GET /api/v1/attendance/stats
Returns aggregate attendance metrics used in the history insight card.
### Query Parameters
period (weekly | monthly | quarterly), month (required if monthly)
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

