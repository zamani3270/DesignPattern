# Assign Topping To Product Variants API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to assign a topping to product variants.

The Assign Topping To Product Variants API updates the variant assignments for a topping. Existing assignments are removed first, then new assignments are applied based on request mode (`allProductVariants`, explicit `productVariants`, or `productId`). The API appends an outbox event (`topping.assigned`) and returns assigned variant data. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/toppings/{id}/assign` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpPost("{id:long}/assign")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}/assign`
- **Path parameters:** `vendorId` (long), `id` (long).
- **Note:** `vendorId` and `id` from route are applied as source of truth and override request body values.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | number | Yes | Topping id in payload. Route `id` is applied as source of truth. |
| `vendorId` | number | Yes | Vendor id in payload. Route `vendorId` is applied as source of truth. |
| `productVariants` | array<number> | No | Explicit product variant ids to assign when `allProductVariants` is false. |
| `allProductVariants` | boolean | Yes | If true, assign topping to all variants of the vendor. |
| `productId` | number | No | Assign topping to variants under this product when `allProductVariants` is false and `productVariants` is empty. |

**Assignment mode priority (from handler):**
1. If `allProductVariants = true`: use all vendor variants.
2. Else if `productVariants` has values: use those variant ids.
3. Else if `productId` has value: use variants under that product.
4. Else: assign no variants (existing assignments are still removed).

**Business rules:**
- Topping must exist for `(vendorId, id)`; otherwise request fails with not found.
- Existing topping-variant mappings are cleared before new assignment is applied.
- Outbox event `topping.assigned` is emitted with assigned variant SKU payload.

---

## Sample Request

```http
POST /vendors/100/toppings/12345/assign
Content-Type: application/json
```

```json
{
  "id": 12345,
  "vendorId": 100,
  "productVariants": [901, 902, 903],
  "allProductVariants": false,
  "productId": null
}
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/toppings/12345/assign" \
  -H "Content-Type: application/json" \
  -d '{"id":12345,"vendorId":100,"productVariants":[901,902,903],"allProductVariants":false,"productId":null}'
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
  "productVariants": [
    "VAR-901",
    "VAR-902",
    "VAR-903"
  ]
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** Topping id not found for vendor.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while assigning topping to product variants.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping assignment applied successfully. |
| **400** | Validation failure. |
| **404** | Topping not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
