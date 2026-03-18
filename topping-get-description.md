# Get Topping API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to fetch a single topping by vendor and topping id.

The Get Topping API returns one topping record scoped by vendor and topping id. On success it returns **200 OK** with a `ToppingResponse` payload.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/toppings/{id}` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpGet("{id:long}")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}`
- **Path parameters:** `vendorId` (long), `id` (long).

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier. |
| `id` | number | Yes | Topping identifier. |

---

## Sample Request

```http
GET /vendors/100/toppings/12345
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/toppings/12345" \
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
  "id": 12345,
  "name": "Extra Cheese Premium",
  "vendorId": 100,
  "signature": "TCAT-100-00098766",
  "price": 30000,
  "tax": 0.09,
  "stock": 90,
  "status": "Active",
  "approvedBy": 7001,
  "approvedAt": "2026-03-18T11:30:00Z",
  "rejectedBy": null,
  "rejectedAt": null,
  "rejectionReason": null
}
```

### Error - 404 / 400 / 500

- **404:** Topping not found for the provided `(id, vendorId)`.
- **400:** Validation error (for example invalid route parameter binding).
- **500:** Unexpected server error.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping fetched successfully. |
| **404** | Topping not found. |
| **400** | Validation failure. |
| **500** | Server error. |

---

## Implementation note

- Current handler maps `ToppingResponse.signature` from `topping.ToppingCategorySignature.Value` in `PrepareResponse`.
