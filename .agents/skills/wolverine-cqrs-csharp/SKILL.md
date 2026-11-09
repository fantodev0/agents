---
name: wolverine-cqrs-csharp
description: Implements CQRS architecture in C# .NET using the Wolverine command bus instead of MediatR. Use this when setting up command/query handlers, message routing, or the transactional outbox pattern in .NET applications.
---

# Wolverine CQRS Implementation Guide

This skill provides instructions for generating C# code that implements the Command Query Responsibility Segregation (CQRS) pattern using Wolverine. Wolverine replaces MediatR and relies heavily on naming conventions and code generation rather than marker interfaces.

## ⚠️ Core Agent Instructions (CRITICAL)

When prompted to generate handlers, commands, or queries using Wolverine, you **MUST** follow these rules:

1. **No Interfaces:** Do NOT use `IRequest`, `IRequest<T>`, or `IRequestHandler<T, R>`. Wolverine does not use them.
2. **Simple Messages:** Define Commands and Queries as simple `record` types.
3. **Handler Naming Convention:** Name the handler class `{MessageName}Handler`.
4. **Method Naming:** The handling method inside the class MUST be named `Handle` or `Consume`.
5. **Static Handlers:** Prefer `public static` classes and methods for handlers to reduce allocations.
6. **Method Injection:** Inject dependencies directly into the `Handle` method's signature, NOT via a class constructor.

---

## Step-by-Step Implementation

### 1. Setup and Configuration

Replace MediatR registration with Wolverine in `Program.cs`.

```csharp
using Wolverine;

var builder = WebApplication.CreateBuilder(args);

// Replaces builder.Services.AddMediatR(...)
builder.Host.UseWolverine(opts =>
{
    // Wolverine configuration goes here
});

var app = builder.Build();
app.Run();
```

### 2. Defining Commands and Queries

Use C# records for clean, immutable message definitions.

```csharp
// Command
public record CreateOrderCommand(Guid CustomerId, decimal TotalAmount);

// Event/Result
public record OrderCreatedEvent(Guid OrderId);

// Query
public record GetOrderQuery(Guid OrderId);
```

### 3. Creating Handlers

Implement handlers using static classes and method injection.

````csharp
using System.Threading.Tasks;

// The class name matches the command name + "Handler"
public static class CreateOrderCommandHandler
{
    // Dependencies like DbContext are injected directly into the method
    public static async Task<OrderCreatedEvent> Handle(
        CreateOrderCommand command,
        AppDbContext dbContext)
    {
        var order = new Order { CustomerId = command.CustomerId, Amount = command.TotalAmount };
        dbContext.Orders.Add(order);
        await dbContext.SaveChangesAsync();

        return new OrderCreatedEvent(order.Id);
    }
}
```

### 4. Dispatching Messages (Minimal APIs)
Inject `IMessageBus` directly into your Minimal API endpoints to send commands, publish events, or invoke queries.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;
using Wolverine;

var builder = WebApplication.CreateBuilder(args);
builder.Host.UseWolverine();
var app = builder.Build();

// --- Command Example (POST) ---
app.MapPost("/api/orders", async (CreateOrderCommand command, IMessageBus bus) =>
{
    // InvokeAsync waits for the handler to finish and expects a specific return type.
    // Use SendAsync(command) for fire-and-forget operations.
    var result = await bus.InvokeAsync<OrderCreatedEvent>(command);

    return Results.Ok(result);
});

// --- Query Example (GET) ---
app.MapGet("/api/orders/{id:guid}", async (Guid id, IMessageBus bus) =>
{
    var query = new GetOrderQuery(id);
    var order = await bus.InvokeAsync<Order?>(query);

    return order is not null ? Results.Ok(order) : Results.NotFound();
});

app.Run();
````

### Common Edge Cases & Wolverine Features

#### Multiple Return Values (Cascading Messages)

Wolverine allows handlers to return multiple messages (e.g., events to be published) using C# Tuples.

```csharp
public static (Order, OrderCreatedEvent) Handle(CreateOrderCommand command)
{
    var order = new Order();
    var evt = new OrderCreatedEvent(order.Id);

    // Wolverine automatically publishes the event and returns the order
    return (order, evt);
}
```

#### Handling Queries

Queries are handled exactly the same way as commands. The only difference is the semantic intent and the return type.

```csharp
public static class GetOrderQueryHandler
{
    public static async Task<Order?> Handle(GetOrderQuery query, AppDbContext dbContext)
    {
        return await dbContext.Orders.FindAsync(query.OrderId);
    }
}
```
