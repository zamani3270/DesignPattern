# Get Topping Categories API - Description

This document is for **backoffice developers** integrating with the Menu Management API to fetch topping categories for a vendor.

The Get Topping Categories API returns the list of topping categories for a given vendor. On success it returns **200 OK** with an array of `ToppingCategoryResponse` objects. If no records exist, the API returns an empty array.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/topping-categories` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Path parameter:** `vendorId` (number) identifies the vendor whose topping categories are requested.

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
GET /vendors/100/topping-categories
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/topping-categories" \
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
    "rejectionReason": null
  },
  {
    "id": 98766,
    "signature": "TCAT-100-00098766",
    "name": "Sauces",
    "vendorId": 100,
    "isRequired": true,
    "maxSelect": 1,
    "status": "Approved",
    "approvedBy": 3001,
    "approvedAt": "2026-03-18T07:45:00Z",
    "rejectedBy": null,
    "rejectedAt": null,
    "rejectionReason": null
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
| **200** | Topping categories fetched successfully (possibly empty list). |
| **400** | Validation failure. |
| **500** | Server error. |
