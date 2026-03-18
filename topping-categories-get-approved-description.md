# Get Approved Topping Categories API - Description

This document is for **backoffice developers** integrating with the Menu Management API to fetch approved topping categories for a vendor.

The Get Approved Topping Categories API returns only topping categories that are already approved for the given vendor. It also supports an optional name filter. On success it returns **200 OK** with an array of `ApprovedToppingCategoryResponse` objects. If no approved records exist, the API returns an empty array.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/topping-categories/approved` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Path parameter:** `vendorId` (number) identifies the vendor whose approved topping categories are requested.
- **Query parameter:** `name` (optional string) filters approved categories by name.

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
GET /vendors/100/topping-categories/approved?name=sauce
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/topping-categories/approved?name=sauce" \
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
    "id": 98766,
    "name": "Sauces",
    "vendorId": 100,
    "isRequired": true,
    "maxSelect": 1,
    "status": "Approved",
    "approvedBy": 3001,
    "approvedAt": "2026-03-18T07:45:00Z"
  },
  {
    "id": 98767,
    "name": "Cheese Add-ons",
    "vendorId": 100,
    "isRequired": false,
    "maxSelect": 2,
    "status": "Approved",
    "approvedBy": 3002,
    "approvedAt": "2026-03-18T08:00:00Z"
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
| **200** | Approved topping categories fetched successfully (possibly empty list). |
| **400** | Validation failure. |
| **500** | Server error. |
