---
name: jsend-csharp
description: Implement or refactor C#/.NET/ASP.NET Core APIs to return JSend-compliant JSON response envelopes with success, fail, and error shapes while preserving appropriate HTTP status codes.
---

# JSend for C#/.NET APIs

Use this skill when adding, reviewing, or refactoring JSend responses in C#, especially ASP.NET Core controllers, Minimal APIs, validation handling, or exception middleware.

## JSend contract

JSend is an application-level JSON envelope. Use it in addition to HTTP status codes, not instead of them.

| Status    | Meaning                                                                | Required keys       | Optional keys  |
| --------- | ---------------------------------------------------------------------- | ------------------- | -------------- |
| `success` | Request succeeded                                                      | `status`, `data`    | none           |
| `fail`    | Client submitted invalid data or an API precondition was not satisfied | `status`, `data`    | none           |
| `error`   | Server/processing error, usually unexpected exception                  | `status`, `message` | `code`, `data` |

Canonical examples:

```json
{ "status": "success", "data": { "post": { "id": 1 } } }
{ "status": "success", "data": null }
{ "status": "fail", "data": { "title": "A title is required" } }
{ "status": "error", "message": "Unable to communicate with database" }
```

## Implementation guidance

- Preserve normal HTTP semantics:
  - `success`: usually `200`, `201`, or `204`-like operations represented as `200` with `{ "data": null }` when a body is returned.
  - `fail`: usually `400`, `404`, `409`, or `422`, depending on the failed client condition.
  - `error`: usually `500` or another 5xx status.
- For `success` and `fail`, always serialize the `data` key, even when its value is `null`.
- For `error`, always serialize `message`; serialize `code` and `data` only when provided.
- Do not expose stack traces, exception messages, SQL details, secrets, or internal identifiers in production error responses. Put diagnostic detail in logs; include safe correlation/request IDs if useful.
- Validation failures should use `fail`, with `data` as an object/dictionary keyed by field/property names where possible.
- Unexpected exceptions should use `error`, preferably generated in centralized middleware or exception handlers.
- Avoid mixing JSend with unrelated envelope conventions such as `isSuccess`, `errors`, `result`, or `problemDetails` in the response body unless the project explicitly requires compatibility.

## Reusable response types

Prefer small reusable helpers over hand-writing anonymous objects in every endpoint. With `System.Text.Json`, use explicit property names so naming-policy changes do not break JSend keys.

```csharp
using System.Text.Json.Serialization;

namespace Api.Responses;

public static class SendResponse
{
    public static Success<T> Success<T>(T? data) => new("success", data);
    public static Fail<T> Fail<T>(T? data) => new("fail", data);
    public static Error Error(string message, int? code = null, object? data = null) => new("error", message, code, data);
}

public sealed record Success<T>(
    [property: JsonPropertyName("status")]
    string Status,
    [property: JsonPropertyName("data")]
    [property: JsonIgnore(Condition = JsonIgnoreCondition.Never)]
    T? Data);

public sealed record Fail<T>(
    [property: JsonPropertyName("status")]
    string Status,
    [property: JsonPropertyName("data")]
    [property: JsonIgnore(Condition = JsonIgnoreCondition.Never)]
    T? Data);

public sealed record Error(
    [property: JsonPropertyName("status")]
    string Status,
    [property: JsonPropertyName("message")]
    string Message,
    [property: JsonPropertyName("code")]
    [property: JsonIgnore(Condition = JsonIgnoreCondition.WhenWritingNull)]
    int? Code,
    [property: JsonPropertyName("data")]
    [property: JsonIgnore(Condition = JsonIgnoreCondition.WhenWritingNull)]
    object? Data);
```

If the target project has global `DefaultIgnoreCondition = WhenWritingNull`, confirm `success.data` is still emitted for null data. Add tests for this.

## ASP.NET Core usage patterns

Controllers:

```csharp
[HttpGet("{id:int}")]
public async Task<IActionResult> GetPost(int id)
{
    var post = await posts.GetAsync(id);
    if (post is null)
        return NotFound(SendResponse.Fail(new { id = "Post was not found" }));

    return Ok(SendResponse.Success(new { post }));
}

[HttpDelete("{id:int}")]
public async Task<IActionResult> DeletePost(int id)
{
    await posts.DeleteAsync(id);
    return Ok(SendResponse.Success<object>(null));
}
```

Minimal APIs:

```csharp
app.MapGet("/posts/{id:int}", async (int id, IPostStore posts) =>
{
    var post = await posts.GetAsync(id);
    return post is null
        ? Results.NotFound(SendResponse.Fail(new { id = "Post was not found" }))
        : Results.Ok(SendResponse.Success(new { post }));
});
```

Validation response factory for controller projects:

```csharp
builder.Services.Configure<ApiBehaviorOptions>(options =>
{
    options.InvalidModelStateResponseFactory = context =>
    {
        var errors = context.ModelState
            .Where(x => x.Value?.Errors.Count > 0)
            .ToDictionary(
                x => x.Key,
                x => x.Value!.Errors.Select(e => e.ErrorMessage).ToArray());

        return new BadRequestObjectResult(SendResponse.Fail(errors));
    };
});
```

Centralized exception handling:

```csharp
app.UseExceptionHandler(errorApp =>
{
    errorApp.Run(async context =>
    {
        context.SendResponse.StatusCode = StatusCodes.Status500InternalServerError;
        context.SendResponse.ContentType = "application/json";
        await context.SendResponse.WriteAsJsonAsync(
            SendResponse.Error("An unexpected error occurred."));
    });
});
```

## Tests to add or update

For each helper or endpoint, verify:

- Success body has exactly `status: "success"` and a `data` key, including `data: null` for no payload.
- Fail body has `status: "fail"` and validation/precondition details under `data`.
- Error body has `status: "error"` and `message`; optional `code`/`data` behavior matches the implementation.
- HTTP status codes remain appropriate for clients and infrastructure.
- Production error responses do not leak sensitive exception details.
