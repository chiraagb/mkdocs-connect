# Contracts

## Overview

The Contracts API provides endpoints for managing contracts within an organization, including uploading files, processing contract data, retrieving contract details, and deleting contracts.

## Base URL

All endpoints are prefixed with `compliances/`

## Authentication

All endpoints require authentication. Include your authentication token in the request headers:

```
Authorization: Bearer
```

## Endpoints

### 1. Get All Contracts

Retrieve a paginated list of all contracts for the authenticated user's organization.

**Endpoint:** `GET /contracts/`

**Parameters:**

- `page` (optional): Page number (default: 1)
- `page_size` (optional): Number of contracts per page (default: 15)

**Response:**

```json
{
  "contracts": [
    {
      "_id": "contract_id",
      "created_at": "2023-08-13T19:00:00Z",
      "updated_at": "2023-08-13T19:00:00Z"
      // ... other contract fields
    }
  ],
  "total_count": 100,
  "total_pages": 7,
  "current_page": 1,
  "page_size": 15
}
```

**Status Codes:**

- `200 OK`: Successfully retrieved contracts
- `401 Unauthorized`: Authentication required

---

### 2. Get Contract Details

Retrieve detailed information for a specific contract including AI insights, compliance status, and violations.

**Endpoint:** `GET /contracts/detail/{contract_id}/`

**Parameters:**

- `contract_id` (path): MongoDB ObjectId of the contract

**Response:**

```json
{
  "contract": {
    "_id": "contract_id",
    "ai_insights": {...},
    "is_compliant": true,
    "violations": [],
    "created_at": "2023-08-13T19:00:00Z",
    "updated_at": "2023-08-13T19:00:00Z",
    // ... other contract fields
  }
}
```

**Status Codes:**

- `200 OK`: Contract found and returned
- `404 Not Found`: Contract not found
- `401 Unauthorized`: Authentication required

---

### 3. Upload Contract Files

Upload contract files for processing.

**Endpoint:** `POST /contracts/files/`

**Content-Type:** `multipart/form-data`

**Parameters:**

- `files` (form-data): Array of contract files to upload

**Response:**

```json
{
  "message": "Contract processing has started.",
  "task_id": "uuid-task-id"
}
```

**Status Codes:**

- `202 Accepted`: File upload initiated successfully
- `400 Bad Request`: No files were uploaded
- `401 Unauthorized`: Authentication required

---

### 4. Submit Contract JSON

Submit contract data in JSON format for processing.

**Endpoint:** `POST /contracts/json/`

**Content-Type:** `application/json`

**Request Body:**

```json
{
  "contract_json": {
    // Contract data in JSON format
    "title": "Contract Title",
    "parties": [...],
    "terms": {...},
    // ... other contract fields
  }
}
```

**Response:**

```json
{
  "message": "Contract processing has started.",
  "task_id": "uuid-task-id"
}
```

**Status Codes:**

- `202 Accepted`: JSON processing initiated successfully
- `400 Bad Request`: No contract_json provided or invalid input_type
- `401 Unauthorized`: Authentication required

---

### 5. Update Contract Details

Update specific fields of an existing contract.

**Endpoint:** `PATCH /contracts/json/{mongo_id}/`

**Content-Type:** `application/json`

**Parameters:**

- `mongo_id` (path): MongoDB ObjectId of the contract to update

**Request Body:**

```json
{
  "customer": {
    "name": "Updated Customer Name",
    "type": "Company"
  },
  "title": "Updated Contract Title"
  // ... other fields to update
}
```

**Response:**

```json
{
  "_id": "mongo_id",
  "message": "Contract updated successfully.",
  "updated_contract": {
    "_id": "mongo_id",
    "updated_at": "2023-08-13T19:00:00Z"
    // ... complete updated contract data
  }
}
```

**Status Codes:**

- `200 OK`: Contract updated successfully
- `400 Bad Request`: Contract ID required or invalid request body
- `404 Not Found`: Contract not found
- `401 Unauthorized`: Authentication required

---

### 6. Check Contract Processing Status

Check the status of a contract processing task.

**Endpoint:** `GET /contract-upload-status/{task_id}/`

**Parameters:**

- `task_id` (path): UUID of the processing task

**Response:**

```json
{
  "state": "SUCCESS",
  "meta": {
    // Task metadata
  },
  "message": "Contract processed successfully."
}
```

**Possible States:**

- `PENDING`: Task is waiting to be processed
- `PROCESSING`: Task is currently being processed
- `SUCCESS`: Task completed successfully
- `FAILED`: Task failed to complete

**Status Codes:**

- `200 OK`: Status retrieved successfully
- `401 Unauthorized`: Authentication required

---

### 7. Delete Contracts

Delete multiple contracts from both the database and Azure Storage.

**Endpoint:** `POST /contracts/delete/`

**Content-Type:** `application/json`

**Request Body:**

```json
{
  "contract_ids": ["contract_id_1", "contract_id_2", "contract_id_3"]
}
```

**Response:**

```json
{
  "message": "3 contract(s) deleted from the database. 2 corresponding file(s) deleted from Azure Storage."
}
```

**Status Codes:**

- `200 OK`: Contracts deleted successfully
- `400 Bad Request`: No contract IDs provided
- `404 Not Found`: No matching contracts found to delete
- `401 Unauthorized`: Authentication required

---

## Error Responses

All endpoints may return the following error responses:

**401 Unauthorized:**

```json
{
  "detail": "Authentication credentials were not provided."
}
```

**400 Bad Request:**

```json
{
  "error": "Detailed error message"
}
```

**404 Not Found:**

```json
{
  "error": "Resource not found"
}
```

**500 Internal Server Error:**

```json
{
  "error": "An internal server error occurred"
}
```
