# Create Topping API - Description

This document is for **backoffice developers** integrating with the Menu Management API to create a topping record for a vendor.

The Create Topping API submits a new topping and returns the created entity details. The API validates vendor existence, validates topping category existence, persists the topping in a transaction, and appends an outbox event (`topping.created`). On success it returns **201 Created** with the topping payload and a `Location` header pointing to GetOne.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/toppings` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Path parameter:** `vendorId` (number) is used as the effective vendor identifier.
- **Note:** `vendorId` from route is used by the handler and overrides `vendorId` sent in request body.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier in payload. Route `vendorId` is applied as source of truth. |
| `sku` | number | Yes | SKU identifier for the topping. |
| `name` | string | Yes | Topping name. |
| `toppingCategoryId` | number | Yes | Target topping category id. Must exist. |
| `price` | number | Yes | Topping price amount (Toman). |
| `taxIncluded` | boolean | Yes | Whether the price is tax-inclusive. |
| `stock` | number | Yes | Current stock amount. |
| `tax` | decimal | Yes | Tax value for the topping. |

**Business rules:**
- Vendor must exist; otherwise request fails with not found.
- Topping category must exist; otherwise request fails with not found.
- Record is created transactionally and an outbox event (`topping.created`) is emitted.

---

## Sample Request

```http
POST /vendors/100/toppings
Content-Type: application/json
```

```json
{
  "vendorId": 100,
  "sku": 200001,
  "name": "Extra Cheese",
  "toppingCategoryId": 98766,
  "price": 25000,
  "taxIncluded": true,
  "stock": 120,
  "tax": 0.09
}
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/toppings" \
  -H "Content-Type: application/json" \
  -d '{"vendorId":100,"sku":200001,"name":"Extra Cheese","toppingCategoryId":98766,"price":25000,"taxIncluded":true,"stock":120,"tax":0.09}'
```

---

## Responses

### Success - 201 Created

```http
HTTP/1.1 201 Created
Location: /vendors/100/toppings/12345
Content-Type: application/json
```

```json
{
  "id": 12345,
  "name": "Extra Cheese",
  "vendorId": 100,
  "signature": "TOP-100-0012345",
  "price": 25000,
  "tax": 0.09,
  "stock": 120,
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
- **404:** Vendor or topping category not found.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while creating topping.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **201** | Topping created successfully. |
| **400** | Validation failure. |
| **404** | Vendor or topping category not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
