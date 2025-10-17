# C# Models — Entities, Single Value Objects, & Data Transfer Objects

To explain how we model data consider the following example:

> We need to model a user of our system. The user have 5 pieces of information that describe them; an ID, a primary email and an optional alternative email, a display name, a list of favorite colors, and a list of friends.

The first step in the process of modeling our example (domain data) is to identify the basic data types we are deling with. For this we use S
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

## Single Value Objects (SVOs)
keep us honest. They wrap single primitives so we validate once and don’t accidentally mix unrelated values that share a type (an email is not just a string; an ID is not just a Guid). For our user we’ll use an `UserId`, an `Email`, a `DisplayName`, and a simple `Color` type for favorites.

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

public record Color
{
    public required string Value { get; init; }
}
```

## DTOs
are our border crossing language. They carry only primitives so they serialize well and don’t leak domain rules. Here’s the user as an API shape:

```csharp
public class UserDto
{
    public required Guid Id { get; init; }
    public required string PrimaryEmail { get; init; }
    public string? AlternativeEmail { get; init; }
    public required string DisplayName { get; init; }
    public required List<string> FavoriteColors { get; init; }
    public required List<Guid> FriendIds { get; init; }
}
```

## Putting it together
Entities describe the heart of our data and use SVOs to capture meaning and validation. At the edges, DTOs use primitives only. We map between them explicitly. Defaults: prefer `required` and `init`; allow `set` on entities when we intend to operate on them (e.g., with EF Core) and then persist.
