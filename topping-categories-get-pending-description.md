# Get Pending Topping Categories API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to fetch pending topping categories for a vendor.

The Get Pending Topping Categories API returns only pending topping categories for the given vendor. It also supports an optional name filter. On success it returns **200 OK** with an array of `ToppingCategoryResponse` objects. If no records match, the API returns an empty array.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/topping-categories/pending` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/topping-categories")]`
- **Action route:** `[HttpGet("pending")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/topping-categories/pending`
- **Path parameter:** `vendorId` (long).
- **Query parameter:** `name` (optional string) for filtering pending topping categories by name.

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier. |

### Query parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | No | Optional text filter passed to repository query. |

---

## Sample Request

```http
GET /vendors/100/topping-categories/pending?name=sauce
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/topping-categories/pending?name=sauce" \
  -H "Accept: application/json"
```

---

## Responses

### Success - 200 OK

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
[
  {
    "id": 98765,
    "signature": "TCAT-100-00098765",
    "name": "Pizza Toppings",
    "vendorId": 100,
    "isRequired": false,
    "maxSelect": 3,
    "status": "Pending",
    "approvedBy": null,
    "approvedAt": null,
    "rejectedBy": null,
    "rejectedAt": null,
    "rejectionReason": null,
    "isActive": true
  }
]
```

### Error - 400 / 500

- **400:** Validation error (for example invalid route/query parameter binding).
- **500:** Unexpected server error.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Pending topping categories fetched successfully (possibly empty list). |
| **400** | Validation failure. |
| **500** | Server error. |
