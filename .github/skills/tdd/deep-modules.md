# Deep Modules

A deep module presents a small, clear interface while hiding meaningful internal complexity.

## Why it matters in TDD

Behavior-focused tests become easier to write when the public contract is small and stable.

## Signals of a deep module

- Few entry points
- Simple parameters and return values
- Internal logic hidden behind the contract
- Tests written against outcomes rather than internal coordination

## Signals of a shallow module

- A large public API with little real behavior behind it
- Thin pass-through layers that mostly forward calls
- Tests that have to know internal choreography to prove anything meaningful

When adding a slice through TDD, ask:

- Can this interface expose less?
- Can more complexity move behind the module boundary?
- Can callers depend on one stable contract instead of several fragile ones?
