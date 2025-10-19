#Entity #Entities

Goal: tie value objects together under identity, host domain behavior, and stay persistence-friendly.

## Why
- Represent domain concepts with lifecycle/identity (DDD Entities).
- Keep invariants and behaviors close to the data they affect.
- Cooperate with EF Core while preserving domain rules.

## How (outline)
- Identity definitions (SVO IDs vs primitive IDs).
- Property conventions (`required`, `init`, when to allow setters).
- Encapsulating collections (private lists + read-only views?).
- Domain behaviors (methods vs domain services; guard clauses).
- EF Core considerations (constructors, proxies, change tracking).

## Examples to build out
- `User` aggregate variants (with/without friends list).
- Computed properties vs projection helpers.
- Handling domain events or invariants (e.g., email uniqueness checks).

Capture decisions and patterns here as they emerge.
