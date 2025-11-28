# Lease Extraction API Documentation

## Overview

The Lease Extraction API is designed to process commercial lease PDF documents and extract key clauses. The API uses a POST request to submit a PDF file or URL for processing and a GET request to retrieve the extraction status and results. The extracted data can be used for commercial risk analysis.

This API is hosted at <span style="color:#e91e63;">https://connectstagingbackend.aviaralabs.com/api/v1/aviara-connect/</span>

## Endpoints

### 1. Submit Extraction

**Endpoint**: `/extractor/`  
**Method**: POST  
**Description**: Submits a PDF file for lease clause extraction. The request enqueues a Celery task for asynchronous processing and returns a `job_id` for tracking.

**Request**:

- **Content-Type**: `multipart/form-data`
- **Parameters**:
  - `pdf` (file): A PDF file containing the lease document. Must be one of `application/pdf`, `application/x-pdf`, or `application/octet-stream`.
  - `pages_per_batch` (integer, optional): Number of pages to process per batch. Default is 30.
  - `max_page_chars` (integer, optional): Maximum characters per page for processing. Default is 3000.
- **Constraints**:
  - Supported file types: PDF only.

**Response**:

- **Status**: `202 Accepted`
- **Body**:

```json
{
  "job_id": "985b898f-3d1c-45fd-a810-79db39382d15",
  "status": "queued"
}
```

- **Error Responses**:
  - `400 Bad Request`: Invalid input (e.g., missing `pdf`).
  - `413 Request Entity Too Large`: PDF exceeds size limit (e.g. above `200mb`)
  - `415 Unsupported Media Type`: File type is not PDF.
  - `500 Internal Server Error`: Unexpected server error.

**Example**:

```bash
curl -X POST https://connectstagingbackend.aviaralabs.com/api/v1/aviara-connect/extractor/ \
  -F "pdf=@lease_document.pdf" \
```

---

### 2. Extraction Status

**Endpoint**: `/extractor/<job_id>/`  
**Method**: GET  
**Description**: Retrieves the status and results of a lease extraction task identified by `job_id`.

**Request**:

- **Path Parameter**:
  - `job_id` (string): The unique identifier of the extraction task (UUID format).
- **Query Parameters**: None.

**Response**:

- **Status**: `200 OK` (or `202 Accepted` for `PENDING` state)
- **Body**:

  - Common fields:
    ```json
    {
      "job_id": "985b898f-3d1c-45fd-a810-79db39382d15",
      "status": "PENDING|STARTED|PROGRESS|RETRY|SUCCESS|FAILURE"
    }
    ```
  - For `SUCCESS`:

    ```json
    {
      "job_id": "985b898f-3d1c-45fd-a810-79db39382d15",
      "status": "SUCCESS",
      "result": {
        "Financial Clauses": { ... },
        "Build-Out & Premises Clauses": { ... },
        "Operational Clauses": { ... },
        "Legal & Liability Clauses": { ... },
        "Flexibility & Exit Clauses": { ... },
        "Operational Clauses": { ... },
      }
    }
    ```

  - For `PROGRESS` or `STARTED`:
    ```json
    {
      "job_id": "985b898f-3d1c-45fd-a810-79db39382d15",
      "status": "PROGRESS",
      "progress": 0.5,
      "current_batch": 2,
      "total_batches": 4,
      "pages_loaded": 60,
      "nonempty_pages": 50,
      "message": "Processing batch 2 of 4",
      "events": ["Started processing", "Batch 1 completed"]
    }
    ```
  - For `FAILURE`:
    ```json
    {
      "job_id": "985b898f-3d1c-45fd-a810-79db39382d15",
      "status": "FAILURE",
      "error": "Error message"
    }
    ```

- **Error Responses**:
  - `404 Not Found`: Invalid or expired `job_id`.
  - `500 Internal Server Error`: Unexpected server error.

**Example**:

```bash
curl -X GET https://connectstagingbackend.aviaralabs.com/api/v1/aviara-connect/extractor/985b898f-3d1c-45fd-a810-79db39382d15/
```

---

## Authentication

- **Current**: No authentication required (`permissions.AllowAny`).
