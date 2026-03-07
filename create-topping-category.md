# Create Topping Category API Documentation

## Endpoint

`POST /vendors/{vendorId}/topping-categories`

Creates a new topping category for a vendor, persists it inside a transaction, and publishes a corresponding outbox event.

---

## Controller Action

```csharp
[HttpPost]
public async Task<IActionResult> CreateToppingCategory(
    [FromRoute] long vendorId,
    [FromBody] CreateToppingCategoryRequestDto body,
    CancellationToken cancellationToken)
{
    var command = new CreateToppingCategoryCommand(vendorId, body);
    var result = await mediator.Send(command, cancellationToken);

    return CreatedAtAction(nameof(GetToppingCategory), new { vendorId, id = result.Id }, result);
}
```

### Behavior
- Reads `vendorId` from route.
- Reads payload into `CreateToppingCategoryRequestDto`.
- Sends `CreateToppingCategoryCommand` through MediatR.
- Returns `201 Created` with a `Location` header pointing to `GetToppingCategory`.

---

## Request Contract

```csharp
public sealed record CreateToppingCategoryRequestDto(
   string Name,
   long VendorId,
   bool IsRequired,
   byte MaxSelectionAllowed
);
```

### Fields
- `Name` (`string`): Category name.
- `VendorId` (`long`): Vendor identifier included in body.
- `IsRequired` (`bool`): Whether selecting from this category is mandatory.
- `MaxSelectionAllowed` (`byte`): Maximum number of selectable toppings.

> Note: The handler uses route `vendorId` from the command (`command.VendorId`) when creating the entity and event payload.

---

## Command

```csharp
public sealed record CreateToppingCategoryCommand(
    long VendorId,
    CreateToppingCategoryRequestDto Request
) : IRequest<ToppingCategoryResponse>;
```

This command bundles route-level context (`VendorId`) and request body (`Request`) into one MediatR request.

---

## Handler Workflow

```csharp
public sealed class CreateToppingCategoryHandler : IRequestHandler<CreateToppingCategoryCommand, ToppingCategoryResponse>
```

### Dependencies
- `IToppingCategoryRepository toppingCategoryRepository`
- `IOutboxWriter outbox`
- `IUnitOfWork unitOfWork`
- `ILogger<CreateToppingCategoryHandler> logger`

### Execution Steps
1. Generate `correlationId` and start a stopwatch for observability.
2. Verify vendor existence:
   - Calls `toppingCategoryRepository.IsVendorExist(command.VendorId)`.
   - Throws `NotFoundException("Vendor", command.VendorId)` if vendor does not exist.
3. Create domain entity:
   - `new Domain.ToppingCategories.ToppingCategory(...)`
4. Run transactional unit of work:
   - Add category (`AddAsync`)
   - Save changes (`SaveChangesAsync`)
   - Append outbox event (`AddOutboxEvent`)
5. Log success after outbox append.
6. Return mapped `ToppingCategoryResponse`.
7. On any exception:
   - Log structured error with correlation ID and elapsed time.
   - Throw `ApplicationException("Unexpected error while creating topping category.", ex)`.

---

## Transaction and Outbox

Inside `ExecuteTransactionalAsync`, persistence and outbox write happen in the same transactional scope.

### Outbox Event
- **Type:** `topping.category.created`
- **Payload fields:**
  - `topping_category_id`
  - `name`
  - `vendor_id`
  - `is_required`
  - `max_selection_allowed`
  - `status`
  - `correlation_id`
  - `created_at` (`DateTime.Now`)

This supports reliable asynchronous integration/event publication through outbox processing.

---

## Response Mapping

The handler maps the domain entity to `ToppingCategoryResponse` using `GenerateResponse(...)`, including:
- Identifiers (`Id`, `Signature`)
- Core settings (`Name`, `VendorId`, `IsRequired`, `MaxSelect`)
- Workflow/status fields (`Status`, approval/rejection metadata)

Returned by the controller as `201 Created`.

---

## Error Scenarios

1. **Vendor not found**
   - Throws `NotFoundException("Vendor", vendorId)`.
2. **Unexpected failure**
   - Logs error with correlation ID and duration.
   - Rethrows wrapped `ApplicationException`.

---

## Logging and Traceability

- Success log:
  - Event type
  - Correlation ID
  - Created category ID
- Error log:
  - Exception details
  - Correlation ID
  - Elapsed execution time

These logs help correlate API calls with outbox events and downstream processing.

---

## Example Request

```http
POST /vendors/123/topping-categories
Content-Type: application/json

{
  "name": "Sauces",
  "vendorId": 123,
  "isRequired": true,
  "maxSelectionAllowed": 2
}
```

## Example Success Response

```http
HTTP/1.1 201 Created
Location: /vendors/123/topping-categories/{newId}
Content-Type: application/json

{
  "id": 456,
  "signature": "....",
  "name": "Sauces",
  "vendorId": 123,
  "isRequired": true,
  "maxSelect": 2,
  "status": "Pending",
  "approvedBy": null,
  "approvedAt": null,
  "rejectedBy": null,
  "rejectedAt": null,
  "rejectionReason": null
}
```
