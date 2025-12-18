# Contract Approval APIs

## Send Contract for Approval

### Endpoint

`POST /api/v1/drafting/contracts/send-approval/`

### Authentication

Required (Bearer Token)

### Description

Send a contract for approval to one or more approvers. Supports **parallel** and **sequential** approval workflows.

### Request Body

```json
{
  "contract_id": "string",
  "approver_emails": [
    {
      "email": "string",
      "order": "number (optional, required for sequential)"
    }
  ],
  "approval_type": "parallel | sequential"
}
```

### Request Fields

| Field           | Type   | Required | Description                |
| --------------- | ------ | -------- | -------------------------- |
| contract_id     | string | Yes      | Contract ObjectId          |
| approver_emails | array  | Yes      | List of approver objects   |
| approval_type   | string | Yes      | `parallel` or `sequential` |

### Success Response

**200 OK**

```json
{
  "message": "Approvals emails sent successfully."
}
```

### Error Responses

| Status | Response                                                                    |
| ------ | --------------------------------------------------------------------------- |
| 400    | `{ "error": "User organization not found." }`                               |
| 400    | `{ "error": "Invalid approval_type. Must be 'parallel' or 'sequential'." }` |
| 400    | `{ "error": "contract_id and approver_emails are required." }`              |
| 400    | `{ "error": "Invalid contract ID." }`                                       |
| 404    | `{ "error": "Contract not found." }`                                        |
| 500    | `{ "error": "Failed to fetch contract." }`                                  |

---

## Accept Contract Approval

### Endpoint

`POST /api/v1/drafting/contracts/{contract_id}/accept-approval/`

### Authentication

Required (Bearer Token)

### Description

Allows an approver to approve a contract. Behavior differs based on approval type.

- **Parallel**: Any approver can approve at any time.
- **Sequential**: Approvers must approve in defined order.

### Path Parameters

| Parameter   | Type   | Required |
| ----------- | ------ | -------- |
| contract_id | string | Yes      |

### Success Response

**200 OK**

```json
{
  "message": "Approval recorded successfully."
}
```

### Error Responses

| Status | Response                                                    |
| ------ | ----------------------------------------------------------- |
| 400    | `{ "error": "User organization not found." }`               |
| 400    | `{ "error": "Invalid contract ID." }`                       |
| 404    | `{ "error": "Contract not found." }`                        |
| 400    | `{ "error": "No approvers found for this contract." }`      |
| 403    | `{ "error": "You are not an approver for this contract." }` |
| 400    | `{ "error": "You have already approved this contract." }`   |
| 400    | `{ "error": "Previous approver must approve before you." }` |
| 500    | `{ "error": "Failed to fetch contract." }`                  |

---

## Approval Object (Stored on Contract)

```json
{
  "approvals": {
    "type": "parallel | sequential",
    "status": "pending | approved",
    "approvers": [
      {
        "email": "string",
        "order": "number",
        "status": "pending | approved",
        "approved_at": "datetime"
      }
    ]
  }
}
```
