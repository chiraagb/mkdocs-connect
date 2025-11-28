# Delete Templates API

## POST `/api/v1/drafting/contract/template/delete/`

## Auth

Requires **Bearer Token**.

## Body

```json
{
  "template_ids": ["string", "string"]
}
```

## Success Response — 200 OK

```json
{
  "message": "X template(s) deleted from the database. Y corresponding file(s) deleted from Azure Storage."
}
```

## Error Responses

### 400 — Missing org or IDs

```json
{ "error": "User organization not found." }
```

```json
{ "error": "No template IDs provided." }
```

### 404 — IDs not found

```json
{ "error": "No matching templates found to delete." }
```

### 500 — Unexpected errors

(Not explicitly returned except for Azure/Mongo logs; API returns 200 unless DB deletion count is zero.)
