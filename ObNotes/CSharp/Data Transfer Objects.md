Goal: define the shapes we expose at application/interface boundaries while keeping domain types internal.

## Why
- Provide transport-friendly models (APIs, messages, UI bindings).
- Avoid leaking domain concerns or validation rules into external contracts.
- Enable versioning and backward compatibility strategies.

## How (outline)
- Shape conventions (primitives only, flat vs nested, naming).
- Mapping strategies (manual mappers, partial methods, source generators).
- Handling nullable fields and defaults at the boundary.
- Validation at the edge vs inside the domain.
- Versioning considerations (add-only changes, deprecation notes).

## Examples to build out
- Request/response DTOs for `User` operations.
- Mapping snippets between `User` entity and DTOs.
- Error/validation response patterns.

Add details here as standards settle.
