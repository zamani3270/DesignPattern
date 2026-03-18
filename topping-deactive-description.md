# DeActive Topping API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to deactivate a topping for a vendor.

The DeActive Topping API deactivates a topping by vendor and topping id. If the topping is already inactive, the API returns the current topping response without applying changes. If deactivation is needed, it updates the record, appends an outbox event (`topping.deactivated`), and returns the updated topping payload. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/toppings/{id}/deActive` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpPost("{id:long}/deActive")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}/deActive`
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
- If `isActive` is already false, current topping response is returned immediately.
- If active, topping is deactivated and saved transactionally.
- Outbox event `topping.deactivated` is emitted when deactivation is applied.

---

## Sample Request

```http
POST /vendors/100/toppings/12345/deActive
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
curl -X POST "{baseUrl}/vendors/100/toppings/12345/deActive" \
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
  "isActive": false
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** Topping id not found for vendor.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while deactivating topping.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping deactivated successfully (or already inactive). |
| **400** | Validation failure. |
| **404** | Topping not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
