# GenerateDraftView

## Endpoint

POST `api/v1/drafting/contract/generate-draft/`

## Auth

Requires authentication (`IsAuthenticated`).

## Content-Type

`application/json`

## Request Body

```json
{
  "template_id": "69291e28f5e5c1fd74b48165",
  "variables": {
    "landlord_name": "John Doe",
    "tenant_name": "Acme Pvt Ltd",
    "property_address": "Bangalore, India",
    "lease_term": "11 months",
    "monthly_rent": "50000",
    "currency": "USD",
    "security_deposit": "150000",
    "start_date": "2025-12-01",
    "special_terms": "No subletting"
  }
}
```

## Success Response — 201 Created

```json
{
  "message": "Draft contract generated successfully.",
  "contract_id": "string", // stringified MongoDB _id of the created contract draft
  "html_content": "string" // rendered HTML of the contract
}
```

## Error Responses

### 400 Bad Request

- Missing organization:

```json
{ "error": "User organization not found." }
```

- Missing `template_id`:

```json
{ "error": "template_id is required." }
```

- `variables` not an object/dict:

```json
{ "error": "variables must be an object/dict." }
```

- Invalid `template_id` (not a valid ObjectId):

```json
{ "error": "Invalid template_id." }
```

- Template has no `html_content`:

```json
{ "error": "Template does not have html_content." }
```

- Rendering left placeholders (missing variables) — includes `missing` list:

```json
{
  "error": "Missing variables for template rendering.",
  "missing": ["var1", "var2"]
}
```

- Template rendering failed (Jinja TemplateError) — includes `details`:

```json
{ "error": "Template rendering failed.", "details": "error details string" }
```

### 404 Not Found

- Template not found:

```json
{ "error": "Template not found." }
```

### 500 Internal Server Error

- Failed to save contract draft:

```json
{ "error": "Failed to save contract draft." }
```

- Generic rendering failure:

```json
{ "error": "Template rendering failed." }
```
