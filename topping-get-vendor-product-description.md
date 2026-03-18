# Get Vendor Product Toppings API - Description

This document is for **backoffice and chef users** integrating with the Menu Management API to fetch all toppings for a vendor.

The Get Vendor Product Toppings API returns the topping list for a given vendor. On success it returns **200 OK** with an array of `ToppingResponse` objects. If no toppings exist, the API returns an empty array.

---

## Endpoint

| Method | Path | Content-Type |
|--------|------|--------------|
| `GET` | `/vendors/{vendorId}/toppings` | `application/json` |

- **Base URL:** Use your environment base URL.
- **Controller attributes:** `[ApiController]` and `[Route("vendors/{vendorId:long}/toppings")]`
- **Action route:** `[HttpGet]`
- **Resolved endpoint path:** `/vendors/{vendorId}/toppings`
- **Path parameter:** `vendorId` (long).

---

## Request

This endpoint does not require a request body.

### Path parameter reference

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `vendorId` | number | Yes | Vendor identifier. |

---

## Sample Request

```http
GET /vendors/100/toppings
```

### cURL example

```bash
curl -X GET "{baseUrl}/vendors/100/toppings" \
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
[
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
  },
  {
    "id": 12346,
    "name": "Spicy Sauce",
    "vendorId": 100,
    "signature": "TOP-100-0012346",
    "price": 15000,
    "tax": 0.09,
    "stock": 70,
    "status": "Pending",
    "approvedBy": null,
    "approvedAt": null,
    "rejectedBy": null,
    "rejectedAt": null,
    "rejectionReason": null
  }
]
```

### Error - 400 / 500

- **400:** Validation error (for example invalid route parameter binding).
- **500:** Unexpected server error.

---

## Status codes summary

| Code | Meaning |
|------|---------|
| **200** | Topping list fetched successfully (possibly empty list). |
| **400** | Validation failure. |
| **500** | Server error. |
