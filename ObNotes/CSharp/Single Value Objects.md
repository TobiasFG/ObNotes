Goal: capture meaning, validation, and tiny rules around single primitives.

## Why
- Prevent mixing unrelated values that share a primitive type.
- Centralize validation/normalization where the data lives.
- Express domain language with explicit types (DDD Value Objects).

## How (outline)
- Creation patterns (constructors vs static factories).
- Validation and normalization strategies.
- Allowing optional/nullable SVOs without losing intent.
- Composing simple helpers on the value (e.g., `Email.Domain`).
- Equality, immutability, and record defaults.

## Examples to build out
- `UserId`, `Email`, `DisplayName`, `Color`.
- Handling parsing failures (exceptions vs result objects).
- Integrating with EF Core or serializers.

Add notes here as decisions solidify.
