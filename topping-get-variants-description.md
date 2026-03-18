# Get Topping Variants API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to fetch product variants assigned to a topping.

The Get Topping Variants API retrieves a topping by vendor and id, then returns the list of assigned product variants for that topping signature. On success it returns **200 OK** with an array of `ToppingVariantResponse` objects.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/toppings/{id}/variants` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpGet("{id:long}/variants")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}/variants`
- **Path parameters:** `vendorId` (long), `id` (long).

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier. |
| `id` | number | Yes | Topping identifier. |

**Business rules:**
- Topping must exist for `(vendorId, id)`; otherwise request fails with not found.
- Variants are loaded by topping signature from the resolved topping record.

---

## Sample Request

```http
GET /vendors/100/toppings/12345/variants
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/toppings/12345/variants" \
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
    "sku": "VAR-901",
    "name": "Large Pizza"
  },
  {
    "sku": "VAR-902",
    "name": "Medium Pizza"
  }
]
```

### Error - 404 / 400 / 500

- **404:** Topping id not found for vendor.
- **400:** Validation error (for example invalid route parameter binding).
- **500:** Unexpected server error.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping variants fetched successfully (possibly empty list). |
| **404** | Topping not found. |
| **400** | Validation failure. |
| **500** | Server error. |
