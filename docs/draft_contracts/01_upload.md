# Template Upload with SSE - Included details required for frontend Integration

This document explains how the frontend should integrate with the **Template Upload API** that supports:

- File upload
- Duplicate detection
- Background processing (Celery)
- Real-time progress updates using **Server-Sent Events (SSE)**

---

## Overview

The upload flow works in **two phases**:

1. **Upload API call**

   - Validates input
   - Performs duplicate check
   - Starts background processing
   - Returns a `task_id`

2. **SSE connection (optional but recommended)**
   - Streams real-time progress
   - Automatically closes on completion or error

> ⚠️ SSE is **read-only** and **one-way** (server → client).

---

## API Endpoints

### Upload Template

**POST**

```

/api/v1/drafting/contract/template/upload/

```

**Headers**

```

Authorization: Bearer <JWT_TOKEN>

```

**Body (multipart/form-data)**

| Field       | Type   | Required |
| ----------- | ------ | -------- |
| name        | string | ✅       |
| type        | string | ✅       |
| description | string | ❌       |
| files       | file   | ✅       |

---

### Success Response (Non-blocking)

```json
{
  "message": "Template upload started",
  "task_id": "d533c282-a5e3-404e-9810-c91dcd8adef9"
}
```

- `task_id` is required to start the SSE connection
- The upload **continues in the background**

---

### Duplicate Template Response

**Status:** `409 Conflict`

```json
{
  "message": "Duplicate template detected.",
  "template_id": "66b5e9c4d2f9a19a3d8a21ab",
  "file_name": "Agreement.pdf",
  "file_url": "https://storage.azure.com/..."
}
```

**Frontend behavior**

- Do NOT open SSE
- Show duplicate warning
- Optionally redirect user to existing template

---

## Server-Sent Events (SSE)

### SSE Endpoint

**GET**

```
/api/v1/drafting/contract/template/upload/sse/{task_id}/
```

**Headers**

```
Authorization: Bearer <JWT_TOKEN>
Accept: text/event-stream
```

---

### SSE Message Format

Each SSE message contains JSON:

```json
{
  "status": "processing",
  "progress": 60,
  "message": "Uploading file to storage"
}
```

---

### Possible `status` values

| Status     | Meaning                      |
| ---------- | ---------------------------- |
| started    | Task accepted                |
| processing | Background work running      |
| completed  | Upload finished successfully |
| error      | Upload failed                |

---

### Example Completion Event

```json
{
  "status": "completed",
  "progress": 100,
  "template_id": "66b5e9c4d2f9a19a3d8a21ab",
  "file_url": "https://storage.azure.com/...",
  "message": "Template uploaded successfully"
}
```

---

## Frontend Integration (React Example)

```ts
const eventSource = new EventSource(
  `/api/v1/drafting/contract/template/upload/sse/${taskId}`,
  {
    withCredentials: true,
  }
);

eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);

  setProgress(data.progress);

  if (data.status === "completed") {
    eventSource.close();
    /** Add your logic **/
  }

  if (data.status === "error") {
    eventSource.close();
    showError(data.message);
  }
};

eventSource.onerror = () => {
  eventSource.close();
};
```

---

## Recommended UX Flow

1. User uploads file
2. Show progress modal (0–10%)
3. Open SSE connection
4. Update progress bar using `progress`
5. Close modal on completion
6. Redirect or refresh template list

---

## Error Handling Guidelines

### Duplicate (409)

- Do not retry
- Inform user
- Provide link to existing template

### SSE Error

- Close connection

---

## Important Notes

### SSE Limitations

- No request body
- No bidirectional communication
- One connection per task

### Browser Support

- Supported in all modern browsers
- Not supported in IE

---

## Debugging Tips (Frontend)

- Use **Browser DevTools → Network → EventStream**
- Avoid Postman for SSE (not supported well)
- Prefer browser console or `curl -N`

---

## Summary

- Upload API is **non-blocking**
- SSE provides **real-time progress**
- Duplicate handling is **synchronous**
- Frontend can safely ignore SSE if progress UI is not needed

---

## FAQ

### Do we need SSE?

No. Upload works without SSE.
SSE is recommended for better UX.

### What if user refreshes page?

- SSE connection closes
- Upload continues in background
- User can refresh template list later

---
