# Reject Topping Category API - Description

This document is for **backoffice developers** integrating with the Menu Management API to reject a pending topping category record for a vendor.

The Reject Topping Category API marks a pending topping category as **Rejected**, stores the rejection metadata (agent and reason), and publishes an outbox event (`topping.category.rejected`). On success it returns **200 OK** with the updated topping category payload.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `POST` | `/vendors/{vendorId}/topping-categories/{id}/reject` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Path parameter:** `vendorId` (number) is used as the effective vendor identifier.
- **Note:** `vendorId` from route is used by the handler and overrides `vendorId` sent in request body.

---

## Request Body

### Field reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `signature` | string | Yes | Topping category signature used to locate the LQA record. |
| `vendorId` | number | Yes | Vendor identifier in payload. Route `vendorId` is applied as source of truth. |
| `agentUserId` | number | Yes | Backoffice agent user id performing the rejection. |
| `rejectReason` | string | Yes | Human-readable reason for rejection. |

**Business rules:**
- The system searches by `(vendorId from route, signature)`.
- A matching topping category record must exist; otherwise the request fails.
- The record must be in **Pending** status to be rejected.

---

## Sample Request

```http
POST /vendors/100/topping-categories/98765/reject
Content-Type: application/json
```

```json
{
  "signature": "TCAT-100-00098765",
  "vendorId": 100,
  "agentUserId": 5012,
  "rejectReason": "Category name conflicts with vendor taxonomy"
}
```

### cURL example

```bash
curl -X POST "{baseUrl}/vendors/100/topping-categories/98765/reject" \
  -H "Content-Type: application/json" \
  -d '{"signature":"TCAT-100-00098765","vendorId":100,"agentUserId":5012,"rejectReason":"Category name conflicts with vendor taxonomy"}'
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
  "status": "Rejected",
  "approvedBy": null,
  "approvedAt": null,
  "rejectedBy": 5012,
  "rejectedAt": "2026-03-18T10:20:30Z",
  "rejectionReason": "Category name conflicts with vendor taxonomy"
}
```

### Error - 400 / 404 / 409 / 500

- **400:** Validation error (invalid payload).
- **404:** No matching topping category found for `(vendorId, signature)`.
- **409:** Concurrency conflict during transactional update.
- **500:** Unexpected server error while rejecting topping category.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping category rejected successfully. |
| **400** | Validation failure. |
| **404** | Topping category signature not found for vendor. |
| **409** | Concurrency conflict. |
| **500** | Server error. |
