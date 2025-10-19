## Story Setup
Imagine the `User` entity leaving the safety of our domain and stepping into an API response. Inside the boundary it is rich with value objects (`Email`, `DisplayName`, `Color`) and behaviors. Once the request crosses HTTP, we need a contract that is flat, predictable, and speaks the language of consumers who do not know our domain types. That contract is the `UserDto`—a shaped snapshot of the entity, tailored for transport and versioning rather than domain rules.

## Purpose
DTOs define how our domain data travels across boundaries in both directions. Whether we expose information (API response, event payload) or consume it (request, downstream service), the DTO is the contract that decouples us from the outside world. Drawing from DDD layering and patterns such as CQRS or vertical slices, a single entity might ship with one DTO in simple scenarios or many specialized DTOs when different consumers demand distinct shapes. The DTO stays behavior-free, uses primitives so it can serialize cleanly, and evolves independently from the rich domain model it mirrors.

## Design Rules
- **Primitives only** — use fundamental types (`string`, `Guid`, `int`, `decimal`, `DateTime`, etc.) and lists/dictionaries of those; never expose entities or SVOs.
- **Explicit naming** — reflect the contract's intent (`PrimaryEmail`, `FriendIds`); avoid leaking internal terminology that only makes sense inside the domain.
- **Required vs optional** — default to `required`; allow null or optional fields only when the contract truly permits absence.
- **Flat first** — favor shallow structures; only nest when the consumer or protocol demands grouping.
- **No behavior** — keep DTOs as pure data bags; validation and business logic stay at the boundary or inside the domain.
- **Evolve deliberately** — if the contract needs new shape or semantics, introduce a new DTO (or versioned endpoint) rather than overloading an existing one with optional fields; keep each DTO clear and intentional.

## Mapping Guidance
Mapping code lives at the boundary—typically in application or interface layers—so the domain remains free of transport concerns. Keep mappings explicit: take a `User` entity plus its SVOs and construct the DTO field by field, and do the reverse when consuming requests. Let the mapping fail fast if input data is invalid so SVO constructors and validators can do their job, then translate those failures into edge-level error responses. Whether you hand-write the mapping, use helper extensions, or adopt a tool, favor approaches that keep the mapping visible and easy to adjust when the contract evolves.

## Validation & Errors
Incoming DTOs are untrusted input. Validate them at the boundary first (shape, required fields, primitive parsing) before handing them to the domain. Let SVO constructors and entity logic enforce deeper rules, and propagate their failures back as clear error responses that match the contract. When returning data, keep DTOs free of domain validation details; instead, use dedicated error DTOs or problem-detail responses so consumers understand what to fix without exposing internals.

## Versioning & Compatibility
Boundary contracts change slower than code. When a consumer needs fresh data or semantics, consider the blast radius: can you add a new DTO version (for example `UserDtoV2`), expose a versioned endpoint, or publish a new event schema without breaking existing clients? Document what each contract guarantees, and retire old shapes intentionally rather than mutating them in place. If different workflows pull the same entity in different directions, give each its own DTO so shared contracts stay small and stable.

## Common Pitfalls
- **Leaking domain types** — returning SVOs or entities defeats the purpose; consumers should not know internal structures.
- **One DTO to rule them all** — forcing every consumer through a bloated contract creates confusion; split contracts when shapes diverge.
- **Optional everything** — sprinkling nullable fields everywhere invites fragile handling; mark fields optional only when absence is valid.
- **Silent schema changes** — mutating a field's meaning without versioning breaks clients; evolve contracts intentionally and communicate.
- **Hidden mappings** — reflection-based or opaque mapping frameworks make review difficult; keep mappings explicit and testable.

## Next Steps
Once DTO conventions feel settled, revisit [[Single Value Objects (SVOs)]] to see how raw data becomes meaning, and [[Entities]] to recall how behavior stays in the domain. As mapping rules and validation patterns stabilize, capture concrete examples here (or link to them) so future readers can see the conventions in action.
