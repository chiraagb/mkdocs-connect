## Base info

- Base URL: `/api/v1/drafting/`
- Auth: `Authorization: Bearer <token>` header required on all calls.

---

## Get all clauses

**Method**: `GET`  
**URL**: `/clauses/`

**Request**

- Headers:
  - `Authorization: Bearer <token>`

**Response 200**

```json
{
  "clauses": [
    {
      "_id": "string",
      "name": "string",
      "content": "string",
      "description": "string (optional, present on created/updated ones)"
    }
  ]
}
```

**Error 400**

```json
{ "error": "User organization not found." }
```

---

## Create clause

**Method**: `POST`  
**URL**: `/clauses/`

**Request**

- Headers:
  - `Authorization: Bearer <token>`
  - `Content-Type: multipart/form-data`
- Body (form-data):
  - `name`: string (required)
  - `description`: string (required)
  - `image`: file (required) – image from which text will be extracted

**Response 201**

```json
{
  "message": "Clause added successfully",
  "clause_id": "string"
}
```

**Error 400**

```json
{
  "error": "Fields 'name' , 'description', 'image' fields are required."
}
```

---

## Update clause

**Method**: `PATCH`  
**URL**: `/clauses/<clause_id>/`

**Request**

- Headers:
  - `Authorization: Bearer <token>`
  - `Content-Type: application/json`
- Body (any subset, at least one field):

```json
{
  "name": "string (optional)",
  "description": "string (optional)",
  "content": "string (optional)"
}
```

**Response 200**

```json
{ "message": "Clause updated successfully" }
```

**Error 404**

```json
{ "error": "Clause not found." }
```

**Error 400**

```json
{ "error": "At least one field ('name' or 'content') must be provided." }
```

---

## Delete clause

**Method**: `DELETE`  
**URL**: `/clauses/<clause_id>/`

**Request**

- Headers:
  - `Authorization: Bearer <token>`

**Response 200**

```json
{ "message": "Clause deleted successfully" }
```

**Error 404**

```json
{ "error": "Clause not found." }
```
