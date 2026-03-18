# Assign Topping To Product Variant API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to assign a topping to a specific product variant.

The Assign Topping To Product Variant API assigns one topping to one product variant identified by product id and variant SKU. The API validates variant and topping existence, persists the assignment transactionally, appends an outbox event (`product.variant.topping.assigned`), and returns assignment details. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/products/{productId}/variants/{sku}/topping/{toppingId}/assign` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/products/{productId:long}/variants")]`
- **Action route:** `[HttpPost("{sku}/topping/{toppingId:long}/assign")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/products/{productId}/variants/{sku}/topping/{toppingId}/assign`
- **Path parameters:** `vendorId` (long), `productId` (long), `sku` (string), `toppingId` (long).
- **Request body:** none.

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier used for topping lookup. |
| `productId` | number | Yes | Product identifier used with SKU to find variant. |
| `sku` | string | Yes | Product variant SKU. |
| `toppingId` | number | Yes | Topping identifier to assign. |

**Business rules:**
- Product variant must exist for `(productId, sku)`; otherwise request fails with not found.
- Topping must exist for `(vendorId, toppingId)`; otherwise request fails with not found.
- Assignment is saved transactionally and outbox event `product.variant.topping.assigned` is emitted.

---

## Sample Request

```http
POST /vendors/100/products/501/variants/VAR-901/topping/12345/assign
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/products/501/variants/VAR-901/topping/12345/assign" \
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
  "sku": "VAR-901",
  "toppingId": 12345,
  "assignedAt": "2026-03-18T12:15:00Z"
}
```

### Error - 404 / 400 / 409 / 500

- **404:** Product variant or topping not found.
- **400:** Validation error (for example invalid route parameter binding).
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while assigning topping to product variant.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping assigned to product variant successfully. |
| **404** | Product variant or topping not found. |
| **400** | Validation failure. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
