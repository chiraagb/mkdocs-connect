# Template Upload - Included details required for frontend Integration

## API Overview

| Purpose             | Endpoint                                                      | Method |
| ------------------- | ------------------------------------------------------------- | ------ |
| Upload a template   | `/api/v1/drafting/contract/template/upload/`                  | `POST` |
| Track upload status | `/api/v1/drafting/contract/template/upload/status/{task_id}/` | `GET`  |

---

## 1. Upload Template (Async)

### Endpoint

`POST /api/v1/drafting/contract/template/upload/`

### Purpose

Upload a contract template file and start asynchronous processing.

### What Happens Internally

- File is validated - pdf only
- Duplicate file check is performed
- Background task starts:

  - Variable extraction
  - HTML processing
  - File upload to storage
  - Database insertion

### Required Inputs

- Template metadata:

  - Name
  - Type (e.g., Sale Agreement, Lease Agreement, etc.)
  - Description

- File: Only one file per request is supported

### Success Response (Upload Started)

- HTTP **202 Accepted**
- Returns:

  - `task_id`

👉 **Important:**
This does **not** mean the template is ready.
Frontend **must track status** using the task ID.

---

### Duplicate Template Handling

If the same file is uploaded again:

| Status       | Meaning                     |
| ------------ | --------------------------- |
| 409 Conflict | Duplicate template detected |

Response includes:

- Existing template ID
- Existing file URL

**Frontend UX Recommendation**

- Show warning: “This template already exists”

---

## 2. Upload Status Tracking

### Endpoint

`GET /api/v1/drafting/contract/template/upload/status/{task_id}/`

### Purpose

Track the progress of an ongoing template upload.

### When to Use

- Immediately after upload starts
- Poll every 2 seconds

---

### Task States

| State        | Meaning             | Frontend Action       |
| ------------ | ------------------- | --------------------- |
| `PENDING`    | Task queued         | Show loading          |
| `STARTED`    | Processing started  | Show progress         |
| `PROCESSING` | Uploading & parsing | Update progress bar   |
| `SUCCESS`    | Upload completed    | Refresh template list |
| `FAILED`     | Error occurred      | Show error message    |

---

### Status Response Structure

The response includes:

- `task_id`
- `state`
- `meta` (varies by state)

#### Meta Object (Common Fields)

- `progress` (percentage)
- `message` (human-readable status)

#### On Success

Meta may also include:

- Template ID
- File URL

---

### Frontend UX Recommendations

Show a progress bar using `progress`

Display user-friendly messages from `meta.message`

Stop polling once state is `SUCCESS` or `FAILED`

On success:

- Redirect to template list
- Or auto-refresh list

---

## Recommended Frontend Flow

1. User fills template form
2. User uploads file
3. API returns `task_id`
4. Frontend starts polling status API
5. Show progress updates
6. On success: Refresh template list
7. On failure: Show error

---
