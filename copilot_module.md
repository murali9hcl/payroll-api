## Copilot Endpoints

### POST `/api/v1/copilot/chat`

Sends a message to the in-app HR copilot. Supports session continuity via `sessionId`. Drives the **Chat with Copilot** CTA.

#### Request Body

```json
{
  "sessionId": "sess_4441",
  "message": "How many vacation days do I have left?"
}
````

#### Response Body

```json
{
  "sessionId": "sess_4441",
  "messageId": "msg_9912",
  "reply": "You have 12 vacation days remaining for 2026.",
  "suggestions": [
    "Request time off",
    "Show my upcoming shifts"
  ],
  "createdAt": "2026-04-26T11:02:00+05:30"
}
```

---

### GET `/api/v1/copilot/sessions/sess_4441`

Returns the message history for a copilot session.

#### Response Body

```json
{
  "sessionId": "sess_4441",
  "messages": [
    {
      "id": "msg_9911",
      "role": "user",
      "text": "How many vacation days do I have left?",
      "createdAt": "2026-04-26T11:01:50+05:30"
    },
    {
      "id": "msg_9912",
      "role": "assistant",
      "text": "You have 12 vacation days remaining for 2026.",
      "createdAt": "2026-04-26T11:02:00+05:30"
    }
  ]
}
```

---

### GET `/api/v1/copilot/sessions?limit=10`

Lists the employee's recent copilot sessions.

#### Response Body

```json
{
  "sessions": [
    {
      "id": "sess_4441",
      "title": "Vacation balance question",
      "lastMessageAt": "2026-04-26T11:02:00+05:30"
    }
  ]
}
```

---

### GET `/api/v1/copilot/insights?context=attendance&month=2026-04`

Returns the **HR Architect Insight** cards rendered on the attendance history screen.

#### Response Body

```json
{
  "context": "attendance",
  "month": "2026-04",
  "insights": [
    {
      "id": "ins_1201",
      "title": "Attendance Consistency",
      "body": "Your on-site consistency improved by 12% this month. You've met the 85% threshold for 8 of the last 10 shifts. Keep it up to maintain your Platinum attendance status.",
      "tone": "positive",
      "generatedAt": "2026-04-26T07:00:00+05:30"
    }
  ]
}
```
