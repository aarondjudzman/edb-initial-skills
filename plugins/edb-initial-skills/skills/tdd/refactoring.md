# Refactoring After Green

Once the current slice is passing, look for the smallest cleanup that improves clarity without changing behavior.

## Common refactor targets

- Duplication that became obvious across neighboring paths
- Names that no longer reflect the role of the code
- Long methods that want extraction behind the same public contract
- Overly shallow modules that should absorb more complexity
- Primitive data passed around where a value object would make the contract clearer

## Refactoring rules

- Stay green while refactoring.
- Keep tests on the public contract.
- Rerun focused validation after each meaningful cleanup.
- Stop when the design is clearer; do not turn refactoring into a second implementation project.
