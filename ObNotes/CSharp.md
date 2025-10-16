# C# Models — Entities, Single Value Objects, & Data Transfer Objects

To explain how we model data consider the following example:

> We need to model a user of our system. The user have 5 pieces of information that describe them; an ID, a primary email and an optional alternative email, a display name, a list of favorite colors, and a list of friends.

We usually store this user somewhere, for example a database, and step one will often be to model this using entities.
## Entities
are at the core and in their simplest form responsible for describing our domain.

These are in code defined as follows:
```csharp
public class User
{
    public required UserId Id { get; init; }
    public required Email PrimaryEmail { get; init; }
    public required Email? AlternativeEmail {get; set; }
    public required DisplayName DisplayName { get; set; }
    public required List<Color> FavoriteColors { get; init; }
    public required List<User> Friends { get; init; }
}
```

Notice how entities
* Are constructor less.
* Are described using entities or single value objects - they never hold primitive types.
* Always require all of their properties - even when they are nullable.





- Single Value Objects (SVOs) wrap primitives to enforce meaning and validation.
- DTOs are for communication across boundaries (APIs, queues) and use primitives only.


1) SVOs — wrap primitives, validate once, prevent mixing
```csharp
public record UserId
{
    public required Guid Value { get; init; }
    public static UserId New() => new() { Value = Guid.NewGuid() };
}

public record Email
{
    public required string Value { get; init; }
    public static Email Create(string value)
    {
        if (string.IsNullOrWhiteSpace(value) || !value.Contains('@'))
            throw new ArgumentException("Invalid email.", nameof(value));
        return new Email { Value = value.Trim().ToLowerInvariant() };
    }
    public override string ToString() => Value;
}

public record DisplayName
{
    public required string Value { get; init; }
    public static DisplayName Create(string value)
    {
        if (string.IsNullOrWhiteSpace(value))
            throw new ArgumentException("Display name required.", nameof(value));
        return new DisplayName { Value = value.Trim() };
    }
    public override string ToString() => Value;
}
```

2) Entity — our domain model


3) DTO — shape for the outside world
```csharp
public class UserDto
{
    public required Guid Id { get; init; }
    public required string Email { get; init; }
    public required string DisplayName { get; init; }
}
```

How it fits together
- Domain uses Entities and SVOs exclusively (no raw primitives on Entities).
- Boundaries use DTOs (primitives only). Map explicitly to/from Entities/SVOs.
- Defaults: `required` + `init`; allow `set` on Entities when we intend mutation.
