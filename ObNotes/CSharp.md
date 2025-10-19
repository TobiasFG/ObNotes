# Data Modeling — Entities, Single Value Objects, & Data Transfer Objects

To explain how we model data consider the following example:

> We need to model a user of our system. The user have 5 pieces of information that describe them; an ID, a primary email and an optional alternative email, a display name, a list of favorite colors, and a list of friends.

The first step in the process of modeling our user example (aka. domain data) is to identify the unique types of data we are dealing with. For this we use
## Single Value Objects (SVOs)
SVOs are meant to keep us honest and the first step towards describing our domain data and logic. They wrap primitives so unrelated values are not accidentally mixed, introduce validation, and encapsulate logic at the core.

Taking our user example we can quickly identify the SVOs needed; a `UserId`, an `Email`, a `DisplayName`, and `Color`. 

```csharp
public record Email
{
    public required string Value { get; init; }

    // Constructor keeps validation/normalization close to the data
    public Email(string value)
    {
        Value = Normalize(Validate(value));
    }

    // Validation
    public static string Validate(string value)
    {
        if (string.IsNullOrWhiteSpace(value) || !value.Contains('@'))
            throw new ArgumentException("Invalid email.", nameof(value));
        return value;
    }

    // Small domain helpers live with the value
    public string Domain => Value[(Value.IndexOf('@') + 1)..];
    public string Obfuscated => Value[..1] + "****@" + Domain;

    // Overrides
    public override string ToString() => Value;
}
```

Notice how SVOs
- Validate and normalize once at the type boundary.
- Encapsulate small domain rules (e.g., Domain, Obfuscated).
- Are DDD Value Objects: identified by value, not by reference.

We model other primitives in the same way (e.g., `UserId`, `DisplayName`, `Color`) so each value carries its meaning and rules. 

You might ask:
> *what about the alternative email?*

SVOs describe types, not use cases. “Primary” and “Alternative” are the same type (`Email`). The difference lives in the entity via property names and nullability—not separate SVOs.

With these building blocks in place, we can now describe our User by tying them together using
## Entities
Entities are at the core. They bring value objects together under an identity and host behavior.

These are in code defined as follows:
```csharp
public class User
{
    public required UserId Id { get; init; }
    public required Email PrimaryEmail { get; init; }
    public required Email? AlternativeEmail {get; set; }
    public required DisplayName DisplayName { get; set; }
    public required List<Color> FavoriteColors { get; init; }
    public required List<UserId> Friends { get; init; }

	// Computed
	public Email EmailToContact => AlternativeEmail ?? PrimaryEmail;
	
	// Logic
	public void ChangePrimaryEmail(Email newEmail) => PrimaryEmail = newEmail;

	public void AddFriend(UserId friendId)
	{
	    if (!Friends.Any(f => f.Value == friendId.Value)) Friends.Add(friendId);
	}
}
```

Notice how entities
* Are constructor-less (implicit parameterless works with EF Core).
* Are described using entities or single value objects - they never hold primitive types.
* Always require all of their properties - even when they are nullable.

Our entity is how we think and operate in the domain. But when we cross a boundary—HTTP, messaging, UI—we don’t leak domain types. Instead, we project the entity into a simple shape by using
## Data Transfer Objects (DTOs)
DTOs are our border crossing language. They carry only primitives so they serialize well and don’t leak domain rules. Here’s the user as an API shape:

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
