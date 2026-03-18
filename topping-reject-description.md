# Reject Topping API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to reject a pending (LQA) topping for a vendor.

The Reject Topping API rejects the pending topping version for the given topping id and vendor, stores rejection metadata (agent and reason), appends an outbox event (`topping.rejected`), and returns the updated topping payload. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/toppings/{id}/reject` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpPost("{id:long}/reject")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}/reject`
- **Path parameters:** `vendorId` (long), `id` (long).
- **Important mapping note:** current action method maps `vendorId` from route but does **not** map route `id` into method parameters; request processing uses `body.id`.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | number | Yes | Topping id used in command payload. |
| `vendorId` | number | Yes | Vendor id in payload. Route `vendorId` is applied as source of truth. |
| `agentUserId` | number | Yes | User id performing rejection. |
| `rejectReason` | string | Yes | Human-readable rejection reason. |

**Business rules:**
- System loads LQA records by `(vendorId, id)`.
- At least one record must exist; otherwise request fails with not found.
- A **Pending** LQA topping record is selected and rejected.
- Rejected record is saved transactionally and outbox event `topping.rejected` is emitted.

---

## Sample Request

```http
POST /vendors/100/toppings/12345/reject
Content-Type: application/json
```

```json
{
  "id": 12345,
  "vendorId": 100,
  "agentUserId": 7002,
  "rejectReason": "Price policy mismatch"
}
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/toppings/12345/reject" \
  -H "Content-Type: application/json" \
  -d '{"id":12345,"vendorId":100,"agentUserId":7002,"rejectReason":"Price policy mismatch"}'
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
  "tax": 0.09,
  "stock": 90,
  "status": "Rejected",
  "approvedBy": null,
  "approvedAt": null,
  "rejectedBy": 7002,
  "rejectedAt": "2026-03-18T11:55:00Z",
  "rejectionReason": "Price policy mismatch"
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** Topping id not found for vendor.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while rejecting topping.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping rejected successfully. |
| **400** | Validation failure. |
| **404** | Topping not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
