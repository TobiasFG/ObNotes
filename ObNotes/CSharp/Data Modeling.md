To explain how we model data, consider this example:

> We need to model a user of our system. The user has five pieces of information that describe them: an ID, a primary email and an optional alternative email, a display name, a list of favorite colors, and a list of friends.

## Single Value Objects (SVOs)
The first step is to identify the unique types of data we are dealing with. SVOs keep us honest by wrapping primitives so unrelated values are not mixed, introduce validation, and encapsulate logic at the core.

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

    // Normalization
    private static string Normalize(string value) => value.Trim().ToLowerInvariant();

    // Small domain helpers live with the value
    public string Domain => Value[(Value.IndexOf('@') + 1)..];
    public string Obfuscated => Value[..1] + "****@" + Domain;

    // Overrides
    public override string ToString() => Value;
}
```

- Validate and normalize once at the type boundary.
- Encapsulate small domain rules (Domain, Obfuscated).
- DDD Value Objects: identified by value, not reference.

``Primary`` and ``Alternative`` emails share the same SVO; the difference lives in the entity via property names and nullability.

We model other primitives the same way (e.g., `UserId`, `DisplayName`, `Color`) so each value carries meaning and rules.

Learn more: [[Single Value Objects]]

## Entities
With SVOs in place, we compose a `User` entity that ties them together under an identity and hosts behavior.

```csharp
public class User
{
    public required UserId Id { get; init; }
    public required Email PrimaryEmail { get; init; }
    public required Email? AlternativeEmail { get; set; }
    public required DisplayName DisplayName { get; set; }
    public required List<Color> FavoriteColors { get; init; }
    public required List<UserId> Friends { get; init; }

    // Computed
    public Email EmailToContact => AlternativeEmail ?? PrimaryEmail;

    // Logic
    public void ChangePrimaryEmail(Email newEmail) => PrimaryEmail = newEmail;

    public void AddFavoriteColor(Color color)
    {
        if (!FavoriteColors.Any(c => c.Value == color.Value)) FavoriteColors.Add(color);
    }

    public void AddFriend(UserId friendId)
    {
        if (!Friends.Any(f => f.Value == friendId.Value)) Friends.Add(friendId);
    }
}
```

- Constructor-less (implicit parameterless works with EF Core).
- Never hold primitives; only Entities, SVOs, or lists of those.
- Require all properties (even nullable ones) to reduce guesswork.

Learn more: [[Entities]]

## Data Transfer Objects (DTOs)
Our entity is how we think and operate in the domain. Crossing a boundary—HTTP, messaging, UI—we project it into a simple shape that carries only primitives.

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

- Primitives only; easy to serialize.
- Keep domain validation/logic out of the DTO.
- Map explicitly to Entities/SVOs at the boundary.

Learn more: [[Data Transfer Objects]]

Together, we shape meaning with SVOs, compose entities from those types to model behavior, and translate at the edges with DTOs that carry primitives only. This keeps business logic at the core (DDD: Value Objects and Entities) and boundaries thin. Defaults: prefer `required` and `init`; allow `set` on entities when we intend to operate on them and then persist.
