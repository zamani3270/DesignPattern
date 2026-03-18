# Approve Topping API (Chef Users) - Description

This document is for **chef users** integrating with the Menu Management API to approve a pending (LQA) topping for a vendor.

The Approve Topping API approves the pending topping version for the given topping id and vendor. If an active base version exists, it is deleted and replaced by the approved pending version. The API appends an outbox event (`topping.approved`) and returns the approved topping payload. On success it returns **200 OK**.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/toppings/{id}/approve` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpPost("{id:long}/approve")]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings/{id}/approve`
- **Path parameters:** `vendorId` (long), `id` (long).
- **Note:** `vendorId` and `id` from route are applied as source of truth and override request body values.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | number | Yes | Topping id in payload. Route `id` is applied as source of truth. |
| `vendorId` | number | Yes | Vendor id in payload. Route `vendorId` is applied as source of truth. |
| `agentUserId` | number | Yes | Chef/backoffice user id performing approval. |

**Business rules:**
- System loads LQA records by `(vendorId, id)`.
- At least one record must exist; otherwise request fails with not found.
- A **Pending** LQA topping must exist for approval.
- If an **Active** base topping exists, it is deleted during the same transaction.
- Approved pending topping is updated and outbox event `topping.approved` is emitted.

---

## Sample Request

```http
POST /vendors/100/toppings/12345/approve
Content-Type: application/json
```

```json
{
  "id": 12345,
  "vendorId": 100,
  "agentUserId": 7001
}
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/toppings/12345/approve" \
  -H "Content-Type: application/json" \
  -d '{"id":12345,"vendorId":100,"agentUserId":7001}'
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
  "status": "Active",
  "approvedBy": 7001,
  "approvedAt": "2026-03-18T11:30:00Z",
  "rejectedBy": null,
  "rejectedAt": null,
  "rejectionReason": null
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** Topping id not found for vendor, or pending LQA topping not found.
- **409:** Concurrency conflict during transactional save.
- **500:** Unexpected server error while approving topping.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping approved successfully. |
| **400** | Validation failure. |
| **404** | Topping or pending LQA record not found. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
