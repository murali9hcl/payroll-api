# Copilot Module

In-app HR Copilot — used by both Employee (mobile) and Manager (web). Same chat backbone, different actorType-aware prompts.

---

## Endpoints

---

## POST /api/v1/copilot/chat

Sends a message to the HR Copilot. Supports session continuity via `sessionId`. If `sessionId` is omitted, a new session is created.

### Request Body

```json
{
  "sessionId": "sess_4441",
  "message": "How many vacation days do I have left?"
}
````

### Response Body

```json
{
  "sessionId": "sess_4441",
  "messageId": "msg_9912",
  "reply": "You have 12 vacation days remaining for 2026.",
  "suggestions": [
    "Request time off",
    "Show my upcoming shifts"
  ],
  "createdAt": "2026-04-26T11:02:00-04:00"
}
```

---

## GET /api/v1/copilot/sessions/{sessionId}

Returns the message history for a session.

### Response Body

```json
{
  "sessionId": "sess_4441",
  "messages": [
    {
      "id": "msg_9911",
      "role": "user",
      "text": "How many vacation days do I have left?",
      "createdAt": "2026-04-26T11:01:50-04:00"
    },
    {
      "id": "msg_9912",
      "role": "assistant",
      "text": "You have 12 vacation days remaining for 2026.",
      "createdAt": "2026-04-26T11:02:00-04:00"
    }
  ]
}
```

---

## GET /api/v1/copilot/sessions

Lists the user's recent Copilot sessions.

### Query Parameters

| Parameter | Type    | Required | Description  |
| --------- | ------- | -------- | ------------ |
| limit     | integer | No       | Default `10` |

### Response Body

```json
{
  "sessions": [
    {
      "id": "sess_4441",
      "title": "Vacation balance question",
      "lastMessageAt": "2026-04-26T11:02:00-04:00"
    }
  ]
}
```

---

## GET /api/v1/copilot/insights

Returns contextual insight cards (HR Architect / financial assistant). Different contexts produce different card sets.

### Query Parameters

| Parameter | Type   | Required | Description                                                             |
| --------- | ------ | -------- | ----------------------------------------------------------------------- |
| context   | string | Yes      | `attendance`, `payroll`, `performance`, `labor_cost`, `team_efficiency` |
| month     | string | No       | Format `YYYY-MM`                                                        |

### Response Body

```json
{
  "context": "attendance",
  "month": "2026-04",
  "insights": [
    {
      "id": "ins_1201",
      "title": "Attendance Consistency",
      "body": "Your on-site consistency improved by 12% this month. You've met the 85% threshold for 8 of the last 10 shifts.",
      "tone": "positive",
      "generatedAt": "2026-04-26T07:00:00-04:00"
    }
  ]
}
```

---

## POST /api/v1/copilot/actions/send-payslip

Used when chat detects a "send my payslip to WhatsApp / email / SMS" intent.

### Request Body

```json
{
  "payslipId": "ps_3301",
  "channel": "whatsapp",
  "recipientPhone": "+19045551234"
}
```

### Notes

* `channel` enum: `whatsapp | email | sms`
* Use `recipientEmail` instead of `recipientPhone` for email.

### Response Body

```json
{
  "success": true,
  "deliveryId": "dlv_4421",
  "channel": "whatsapp",
  "scheduledFor": "2026-04-26T11:02:30-04:00"
}
```

---

## POST /api/v1/copilot/actions/fetch-document

Used when chat is asked to fetch a tax / contract / payslip document inline. Returns a short-lived signed URL.

### Request Body

```json
{
  "documentType": "w2",
  "year": 2025
}
```

### Notes

`documentType` enum:

* `payslip`
* `w2`
* `1095_c`
* `form_16`
* `contract`
* `flsa_policy`
* `geofence_consent`

### Response Body

```json
{
  "documentId": "tax_w2_2025",
  "downloadUrl": "https://files.zexovo.com/signed/tax_w2_2025?token=...",
  "expiresAt": "2026-04-26T11:14:00-04:00"
}
```

### Negative Response

```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "No W-2 issued for 2025."
  }
}
```

```
```
