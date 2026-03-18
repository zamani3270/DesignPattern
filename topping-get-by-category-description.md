# Get Toppings By Category API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to fetch toppings by category for a vendor.

The Get Toppings By Category API returns toppings for the given vendor and category signature. It also supports an optional name filter. On success it returns **200 OK** with an array of `ToppingResponse` objects. If no records match, the API returns an empty array.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/toppings/category/{signature}` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpGet("category/{signature}")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/category/{signature}`
- **Path parameters:** `vendorId` (long), `signature` (string).
- **Query parameter:** `name` (optional string) for filtering toppings by name.

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier. |
| `signature` | string | Yes | Topping category signature used to filter toppings. |

### Query parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | No | Optional text filter passed to repository query. |

---

## Sample Request

```http
GET /vendors/100/toppings/category/TCAT-100-00098766?name=cheese
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/toppings/category/TCAT-100-00098766?name=cheese" \
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
    "id": 12345,
    "name": "Extra Cheese Premium",
    "vendorId": 100,
    "signature": "TCAT-100-00098766",
    "price": 30000,
    "stock": 90,
    "status": "Approved",
    "approvedBy": 7001,
    "approvedAt": "2026-03-18T11:30:00Z",
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
| **200** | Toppings fetched successfully (possibly empty list). |
| **400** | Validation failure. |
| **500** | Server error. |

---

## Implementation note

- Current handler maps `ToppingResponse.signature` from `topping.ToppingCategorySignature.Value` in `PrepareResponse`.
