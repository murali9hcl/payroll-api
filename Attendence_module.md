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


