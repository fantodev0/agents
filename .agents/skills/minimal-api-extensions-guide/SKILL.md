---
name: minimal-api-extensions-guide
description: Implements backend APIs using Minimal APIs with extension methods in C# .NET. Use this skill when setting up API endpoints, route grouping, and mapping HTTP requests to private static delegate methods.
---

# Minimal API Implementation Guide

This skill instructs agents on how to generate C# API code using a clean, extension-method-based Minimal API architecture.

## ⚠️ Core Agent Instructions (CRITICAL)

When generating API endpoints, you **MUST** follow these architectural rules:

1. **No Controllers:** Do NOT use `ControllerBase` or MVC controllers.
2. **Endpoint Mapping Pattern:** API routes MUST be defined in a `static class WebApplicationExtensions` using a `MapEndpoints` (or domain-specific) extension method on `WebApplication`.
3. **Route Grouping:** Use `app.MapGroup("/api/[domain]")` to group related endpoints.
4. **Private Static Delegates:** Map endpoints to `private static async Task<IResult>` methods within the same extension class. Do NOT write inline lambda functions for the route handlers.
5. **Dependency Injection:** Inject required services (like repositories, loggers, or domain services) directly into the private static delegate method signatures.

---

## Step-by-Step Implementation

### 1. The API Extension Class

Create a static extension class to define the routes and map them to private static handler methods.
**Example:**

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Routing;
using Serilog;
// Add domain-specific using statements
// using Birthday.DTOs;
// using Birthday.Repository;
// using Birthday.Services;

namespace Birthday.Extensions;

public static class InvitationsEndpoints
{
    // 1. The Extension Method
    public static WebApplication MapInvitationsEndpoints(this WebApplication app)
    {
        // 2. Route Grouping
        var group = app.MapGroup("/api/invitations");

        // 3. Mapping to Private Static Methods
        group.MapPost("/", CreateInvitation)
            .WithName("Create Invitation")
            .WithDescription("Endpoint for creating a new invitation and person");

        group.MapGet("/{id:guid}", GetInvitation)
            .WithName("Get Invitation")
            .WithDescription("Endpoint for retrieving an invitation by ID");

        return app;
    }

    // 4. Private Static Delegate (POST)
    private static async Task<IResult> CreateInvitation(
        CreateInvitationRequest request,
        Serilog.ILogger logger,
        IInvitationRepository invitationRepository,
        IPersonRepository personRepository,
        IEmailService emailService)
    {
        logger.Information("Creating invitation for {Email}", request.Email);

        // Execute business logic using injected services...

        return Results.Ok();
    }

    // 5. Private Static Delegate (GET)
    private static async Task<IResult> GetInvitation(
        Guid id,
        Serilog.ILogger logger,
        IInvitationRepository invitationRepository)
    {
        logger.Information("Fetching invitation {Id}", id);

        // Execute business logic using injected services...

        return Results.Ok();
    }
}

```

### 2. Registering in Program.cs

Keep the startup configuration clean by calling the extension method directly on the WebApplication instance.
**Example:**

```csharp
using Birthday.Extensions;
using Microsoft.AspNetCore.Builder;

var builder = WebApplication.CreateBuilder(args);

// Register services, repositories, Serilog, etc. here
// builder.Services.AddScoped<IInvitationRepository, InvitationRepository>();

var app = builder.Build();

// Map endpoints using the extension method
app.MapInvitationsEndpoints();

app.Run();
```
