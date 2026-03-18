# Get Topping Category By Id API - Description

This document is for **backoffice developers** integrating with the Menu Management API to fetch a single topping category by id for a vendor.

The Get Topping Category By Id API returns one topping category record scoped by vendor and category id. It can also accept an optional `name` query filter. On success it returns **200 OK** with a `ToppingCategoryResponse` payload.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/topping-categories/{id}` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Path parameters:** `vendorId` (number) and `id` (number) identify the requested topping category.
- **Query parameter:** `name` (optional string) filter.

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier. |
| `id` | number | Yes | Topping category identifier. |

### Query parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | No | Optional name filter. |

---

## Sample Request

```http
GET /vendors/100/topping-categories/98765?name=Sauces
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/topping-categories/98765?name=Sauces" \
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
{
  "id": 98765,
  "signature": "TCAT-100-00098765",
  "name": "Pizza Toppings",
  "vendorId": 100,
  "isRequired": false,
  "maxSelect": 3,
  "status": "Approved",
  "approvedBy": 3001,
  "approvedAt": "2026-03-18T07:45:00Z",
  "rejectedBy": null,
  "rejectedAt": null,
  "rejectionReason": null
}
```

### Error - 404 / 400 / 500

- **404:** Topping category not found for the provided id/vendor scope.
- **400:** Validation error (for example invalid route/query parameter binding).
- **500:** Unexpected server error.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping category fetched successfully. |
| **404** | Topping category not found. |
| **400** | Validation failure. |
| **500** | Server error. |
