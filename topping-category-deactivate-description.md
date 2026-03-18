# Deactivate Topping Category API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to deactivate a topping category for a vendor.

The Deactivate Topping Category API deactivates a topping category by vendor and category id. If the topping category is already inactive, the API returns the current category response without applying changes. If deactivation is needed, it updates the record, appends an outbox event (`topping.category.deactivated`), and returns the updated topping category payload. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/topping-categories/{id}/deactivate` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/topping-categories")]`
- **Action route:** `[HttpPost("{id:long}/deactivate")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/topping-categories/{id}/deactivate`
- **Path parameters:** `vendorId` (long), `id` (long).
- **Note:** `vendorId` and `id` from route are applied as source of truth and override request body values.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor id in payload. Route `vendorId` is applied as source of truth. |
| `id` | number | Yes | Topping category id in payload. Route `id` is applied as source of truth. |

**Business rules:**
- Topping category must exist for `(vendorId, id)`; otherwise request fails with not found.
- If `isActive` is already false, current topping category response is returned immediately.
- If active, category is deactivated and saved transactionally.
- Outbox event `topping.category.deactivated` is emitted when deactivation is applied.

---

## Sample Request

```http
POST /vendors/100/topping-categories/98765/deactivate
Content-Type: application/json
```

```json
{
  "vendorId": 100,
  "id": 98765
}
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/topping-categories/98765/deactivate" \
  -H "Content-Type: application/json" \
  -d '{"vendorId":100,"id":98765}'
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
  "approvedBy": 7001,
  "approvedAt": "2026-03-18T11:30:00Z",
  "rejectedBy": null,
  "rejectedAt": null,
  "rejectionReason": null,
  "isActive": false
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** Topping category id not found for vendor.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while deactivating topping category.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping category deactivated successfully (or already inactive). |
| **400** | Validation failure. |
| **404** | Topping category not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
