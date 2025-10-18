# C# Models — Entities, Single Value Objects, & Data Transfer Objects

To explain how we model data consider the following example:

> We need to model a user of our system. The user have 5 pieces of information that describe them; an ID, a primary email and an optional alternative email, a display name, a list of favorite colors, and a list of friends.

The first step in the process of modeling our user example (aka. domain data) is to identify the unique types of data we are deling with. For this we use Single Value Objects.
## Single Value Objects (SVOs)
SVOs are meant keep us honest and the first step towards describing or domain data and logic. They wrap primitives so unrelated values are not accidentally mixed, introduce validation, and encapsulate logic at the core. 

Taking our user example we can quickly identify the SVOs needed; a `UserId`, an `Email`, a `DisplayName`, and `Color`. 

```csharp
public record Email
{
    public required string Value { get; init; }
	
	// Constructor
    public Email(string value)
    {
	    Validate(value);
        return new DisplayName { Value = value.Trim() };
    }
	
	// Validation
	public static Validate(string value)
	{
		if (string.IsNullOrWhiteSpace(value))
            throw new ArgumentException("Email must not be empty");
	}
	
	// Overrides
    public override string ToString() => Value;
	
	// Logic
	...
}
```

Notice how SVOs
...

You might ask:
> *what about the alternative email?*

SVOs are meant to describe *types* and <u>not</u> use cases - ...
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

...
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
