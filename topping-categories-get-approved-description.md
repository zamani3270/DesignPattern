# Get Approved Topping Categories API - Description

This document is for **backoffice developers** integrating with the Menu Management API to fetch approved topping categories for a vendor.

The Get Approved Topping Categories API returns only topping categories that are already approved for the given vendor. On success it returns **200 OK** with an array of `ApprovedToppingCategoryResponse` objects. If no approved records exist, the API returns an empty array.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/topping-categories/approved` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Path parameter:** `vendorId` (number) identifies the vendor whose approved topping categories are requested.

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier. |

---

## Sample Request

```http
GET /vendors/100/topping-categories/approved
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/topping-categories/approved" \
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

- **400:** Validation error (for example invalid route parameter binding).
- **500:** Unexpected server error.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Approved topping categories fetched successfully (possibly empty list). |
| **400** | Validation failure. |
| **500** | Server error. |
