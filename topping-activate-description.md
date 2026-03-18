# Activate Topping API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to activate a topping for a vendor.

The Activate Topping API activates a topping by vendor and topping id. If the topping is already active, the API returns the current topping response without applying changes. If activation is needed, it updates the record, appends an outbox event (`topping.activated`), and returns the updated topping payload. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/toppings/{id}/activate` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpPost("{id:long}/activate")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}/activate`
- **Path parameters:** `vendorId` (long), `id` (long).
- **Note:** `vendorId` and `id` from route are applied as source of truth and override request body values.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | number | Yes | Topping id in payload. Route `id` is applied as source of truth. |
| `vendorId` | number | Yes | Vendor id in payload. Route `vendorId` is applied as source of truth. |

**Business rules:**
- Topping must exist for `(id, vendorId)`; otherwise request fails with not found.
- If `isActive` is already true, current topping response is returned immediately.
- If inactive, topping is activated and saved transactionally.
- Outbox event `topping.activated` is emitted when activation is applied.

---

## Sample Request

```http
POST /vendors/100/toppings/12345/activate
Content-Type: application/json
```

```json
{
  "id": 12345,
  "vendorId": 100
}
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/toppings/12345/activate" \
  -H "Content-Type: application/json" \
  -d '{"id":12345,"vendorId":100}'
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
  "signature": "TOP-100-0012345",
  "price": 30000,
  "stock": 90,
  "status": "Active",
  "approvedBy": 7001,
  "approvedAt": "2026-03-18T11:30:00Z",
  "rejectedBy": null,
  "rejectedAt": null,
  "rejectionReason": null,
  "isActive": true
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** Topping id not found for vendor.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while activating topping.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping activated successfully (or already active). |
| **400** | Validation failure. |
| **404** | Topping not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
