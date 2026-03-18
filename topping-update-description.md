# Update Topping API - Description

This document is for **backoffice developers** integrating with the Menu Management API to update a topping record for a vendor.

The Update Topping API receives an existing topping id and creates an updated LQA version of that topping using the provided fields. It appends an outbox event (`topping.lqa.added`) and returns the updated topping payload. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `PUT` | `/vendors/{vendorId}/toppings/{id}` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpPut("{id:long}")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}`
- **Path parameters:** `vendorId` (long), `id` (long).
- **Note:** `vendorId` from route is used by the handler and overrides `vendorId` sent in request body.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier in payload. Route `vendorId` is applied as source of truth. |
| `name` | string | Yes | Topping name. |
| `toppingCategorySignature` | string | Yes | Topping category signature for the updated version. |
| `price` | number | Yes | Topping price amount (Toman). |
| `taxIncluded` | boolean | Yes | Whether the price is tax-inclusive. |
| `stock` | number | Yes | Current stock amount. |
| `tax` | decimal | Yes | Tax value for the topping. |

**Business rules:**
- The base topping must exist for `(id, vendorId)`; otherwise request fails with not found.
- Update flow creates a new LQA topping version from existing signature/sku/vendor and request fields.
- Outbox event `topping.lqa.added` is emitted transactionally.

---

## Sample Request

```http
PUT /vendors/100/toppings/12345
Content-Type: application/json
```

```json
{
  "vendorId": 100,
  "name": "Extra Cheese Premium",
  "toppingCategorySignature": "TCAT-100-00098766",
  "price": 30000,
  "taxIncluded": true,
  "stock": 90,
  "tax": 0.09
}
```

### cURL example

```bash
curl -X PUT "{baseUrl}/vendors/100/toppings/12345" \
  -H "Content-Type: application/json" \
  -d '{"vendorId":100,"name":"Extra Cheese Premium","toppingCategorySignature":"TCAT-100-00098766","price":30000,"taxIncluded":true,"stock":90,"tax":0.09}'
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
  "id": 12401,
  "name": "Extra Cheese Premium",
  "vendorId": 100,
  "signature": "TOP-100-0012345",
  "price": 30000,
  "tax": 0.09,
  "stock": 90,
  "status": "Pending",
  "approvedBy": null,
  "approvedAt": null,
  "rejectedBy": null,
  "rejectedAt": null,
  "rejectionReason": null
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** Topping not found for provided `id` and `vendorId`.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while updating topping.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping update accepted and returned. |
| **400** | Validation failure. |
| **404** | Topping not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
