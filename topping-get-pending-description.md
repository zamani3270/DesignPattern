# Get Pending Toppings API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to fetch pending toppings for a vendor.

The Get Pending Toppings API returns only pending toppings for the given vendor. It also supports an optional name filter. On success it returns **200 OK** with an array of `ToppingResponse` objects. If no records match, the API returns an empty array.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/toppings/pending` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpGet("pending")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/pending`
- **Path parameter:** `vendorId` (long).
- **Query parameter:** `name` (optional string) for filtering pending toppings by name.

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
GET /vendors/100/toppings/pending?name=cheese
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/toppings/pending?name=cheese" \
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
    "id": 12346,
    "name": "Spicy Sauce",
    "vendorId": 100,
    "signature": "TCAT-100-00098767",
    "price": 15000,
    "stock": 70,
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
| **200** | Pending toppings fetched successfully (possibly empty list). |
| **400** | Validation failure. |
| **500** | Server error. |

---

## Implementation note

- Current handler maps `ToppingResponse.signature` from `topping.ToppingCategorySignature.Value` in `PrepareResponse`.
