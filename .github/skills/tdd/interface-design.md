# Interface Design for Testability

TDD works best when the interface naturally exposes behavior.

## Prefer seams that make behavior obvious

1. Accept dependencies instead of constructing them internally.

```ts
function processOrder(order, paymentGateway) {}
```

2. Return results or expose state changes through the contract instead of hiding everything in side effects.

```ts
function calculateDiscount(cart) {
  return { amount: 25, reason: "volume" };
}
```

3. Keep the public surface area small.

- Fewer methods mean fewer behaviors to reason about.
- Simpler parameters reduce test setup noise.
- Clear contracts produce clearer assertions.

4. Push complexity behind stable interfaces.

If the test setup feels noisy or the assertions need intimate knowledge of the implementation, the interface probably wants another pass.
