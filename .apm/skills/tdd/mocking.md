# Mocking Guidance

Mock at system boundaries, not inside the code you own.

## Good places to mock

- External APIs and third-party services
- Time, randomness, and nondeterministic platform behavior
- Infrastructure seams such as file systems, queues, or process boundaries when a real test harness is impractical

## Bad default targets

- Internal helpers
- Neighboring modules in the same domain layer
- Classes and functions you control and can exercise through a public interface

## Design seams that are easy to test

Prefer dependency injection over constructing dependencies deep inside the function under test.

```ts
function processPayment(order, paymentGateway) {
  return paymentGateway.charge(order.total);
}
```

This is easier to test than building the gateway inside the function.

Prefer small, explicit adapters over generic transport wrappers.

```ts
const billingApi = {
  createCharge: (payload) => http.post("/charges", payload),
  fetchCharge: (id) => http.get(`/charges/${id}`),
};
```

Small boundary functions produce simpler mocks, clearer intent, and more focused tests.
