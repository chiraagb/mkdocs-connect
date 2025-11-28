# List Templates with search by name and filter by type

# GET /api/v1/drafting/contract/template/

## Method

`GET`

## Auth

Requires **Bearer Token** (IsAuthenticated).

## Query Params (optional)

| Param    | Description                                 |
| -------- | ------------------------------------------- |
| `type`   | Filter by template type.                    |
| `search` | Case-insensitive search in template `name`. |

## Success Response – 200 OK

```json
{
  "templates": [
    {
      "id": "string",
      "name": "string",
      "description": "string",
      "type": "string",
      "file_name": "string",
      "file_url": "string"
    }
  ]
}
```

## Error Responses

### 400 – No Organization

```json
{ "error": "User organization not found." }
```

### 500 – Server / Mongo Error

```json
{ "error": "Failed to fetch templates." }
```
