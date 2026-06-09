# Behavior-Focused Tests

Good tests survive refactors because they describe what the system does through a public contract.

## Good test characteristics

- They describe a user-visible or caller-visible outcome.
- They use the real interface whenever practical.
- They assert on outputs, returned values, state exposed by the contract, or observable side effects.
- They read like a specification for a behavior.

```ts
test("checkout confirms an order with a valid cart", async () => {
  const cart = createCart();
  cart.add(validProduct);

  const result = await checkout(cart, paymentMethod);

  expect(result.status).toBe("confirmed");
});
```

## Common weak tests

- Tests that inspect private helpers or internal collaborators
- Tests that assert call order or call counts for code you own
- Tests that query storage directly instead of verifying through the supported interface
- Tests whose names describe how the code works internally rather than the behavior being delivered

```ts
test("checkout calls payment client once", async () => {
  const paymentClient = { charge: vi.fn().mockResolvedValue({ ok: true }) };

  await checkout(cart, paymentClient);

  expect(paymentClient.charge).toHaveBeenCalledTimes(1);
});
```

The example above might be useful at a boundary seam, but it is a poor default for testing your own application behavior.

## Prefer verification through the contract

```ts
test("creating a user makes that user retrievable", async () => {
  const created = await createUser({ name: "Alice" });

  const loaded = await getUser(created.id);

  expect(loaded.name).toBe("Alice");
});
```

If a refactor changes internals but preserves the contract, good tests should still pass.
