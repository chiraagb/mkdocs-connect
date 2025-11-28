---

## 1. Text-Based Compliance Rules

### Endpoint

`POST /rules/text/`
`GET /rules/text/`

### Authentication

Required (`IsAuthenticated`)

---

### `POST /rules/text/` — Save or Update Compliance Rules

**Purpose**
Allows an authenticated user to submit or update compliance rules in plain text format.

**Request Body**

```json
{
  "compliance_rules": "All invoices must be submitted within 7 days of contract execution."
}
```

**Responses**

- `200 OK` – Success

```json
{
  "message": "Compliance rules updated successfully."
}
```

- `400 Bad Request` – Missing rules

```json
{
  "error": "Compliance rules are required."
}
```

- `500 Internal Server Error` – Server failure

```json
{
  "error": "Exception message"
}
```

---

### `GET /rules/text/` — Retrieve Saved Compliance Rules

**Purpose**
Fetch the current user's saved compliance rules, if available.

**Responses**

- `200 OK` – Rules found

```json
{
  "compliance_rules": "All invoices must be submitted within 7 days of contract execution."
}
```

- `404 Not Found` – No rules saved

```json
{
  "compliance_rules": ""
}
```

- `500 Internal Server Error` – Server failure

```json
{
  "error": "Exception message"
}
```

---

## 2. File-Based Compliance Rules

### Endpoint

`POST /rules/files/`
`GET /rules/files/`
`PATCH /rules/files/<file_id>/`

### Authentication

Required (`IsAuthenticated`)

---

### `POST /rules/files/` — Upload Compliance Rule Files

**Purpose**
Upload one or more rule documents (PDF, DOCX) and extract their text content for compliance processing.

**Accepted MIME Types**

| MIME Type                                                               | Label |
| ----------------------------------------------------------------------- | ----- |
| application/pdf                                                         | pdf   |
| application/msword                                                      | word  |
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | word  |

**Request Format**
`multipart/form-data`

**Request Example**

```
files: [<file1>, <file2>, ...]
```

**Success Response** (`201 Created`)

```json
{
  "message": "Files uploaded successfully.",
  "files": [
    {
      "id": 101,
      "file_name": "compliance_rules.pdf",
      "file_type": "pdf",
      "file_content": "Extracted content from PDF...",
      "url": "/media/compliance_files/compliance_rules.pdf"
    }
  ]
}
```

**Error Response** (`400 Bad Request`)

```json
{
  "error": "No files were uploaded."
}
```

---

### `GET /rules/files/` — List Uploaded Rule Files

**Purpose**
Paginate through the list of uploaded compliance rule documents for the current user.

**Query Parameters**

| Parameter | Type | Default | Description          |
| --------- | ---- | ------- | -------------------- |
| page      | int  | 1       | Page number          |
| page_size | int  | 15      | Number of items/page |

**Success Response**

```json
{
  "files": [
    {
      "id": 101,
      "file_name": "compliance_rules.pdf",
      "file_type": "pdf",
      "url": "/media/compliance_files/compliance_rules.pdf",
      "created_at": "2025-06-24T13:00:00Z"
    }
  ],
  "total_count": 5,
  "total_pages": 1,
  "current_page": 1,
  "page_size": 15
}
```

---

### `PATCH /rules/files/<file_id>/` — Rename an Uploaded File

**Purpose**
Update the name of a previously uploaded file.

**Request Body**

```json
{
  "new_file_name": "updated_rules.pdf"
}
```

**Success Response**

```json
{
  "message": "File renamed successfully.",
  "id": 101,
  "new_file_name": "updated_rules.pdf"
}
```

**Error Responses**

- Missing name

```json
{
  "error": "New file name is required."
}
```

- File not found

```json
{
  "error": "File not found."
}
```

---

## Summary

| Endpoint                  | Method | Description                  |
| ------------------------- | ------ | ---------------------------- |
| `/rules/text/`            | GET    | Fetch text compliance rules  |
| `/rules/text/`            | POST   | Save or update text rules    |
| `/rules/files/`           | GET    | List uploaded rule files     |
| `/rules/files/`           | POST   | Upload compliance rule files |
| `/rules/files/<file_id>/` | PATCH  | Rename a rule file by ID     |

---
