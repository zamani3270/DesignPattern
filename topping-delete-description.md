# Delete Topping API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to delete a topping for a vendor.

The Delete Topping API performs a logical delete on the topping record for the given vendor and id, appends an outbox event (`topping.deleted`), and commits the change transactionally. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `DELETE` | `/vendors/{vendorId}/toppings/{id}` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpDelete("{id:long}")]`
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

**Business rules:**
- Topping must exist for `(id, vendorId)`; otherwise request fails with not found.
- Delete operation is logical (`topping.Delete()`), then persisted.
- Outbox event `topping.deleted` is emitted in the same transaction.

---

## Sample Request

```http
DELETE /vendors/100/toppings/12345
```

### cURL example

```bash
curl -X DELETE "{baseUrl}/vendors/100/toppings/12345" \
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
{}
```

### Error - 404 / 400 / 409 / 500

- **404:** Topping id not found for vendor.
- **400:** Validation error (for example invalid route parameter binding).
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while deleting topping.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping deleted successfully. |
| **404** | Topping not found. |
| **400** | Validation failure. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
