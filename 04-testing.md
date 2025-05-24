# Testing Guidelines

## General
- Every function must have a test
- Tests should run before build/start
- Use Jest and @nestjs/testing

## Types
- Unit tests for utilities and services
- Integration tests for REST, GraphQL, WebSocket

## Example
```ts
describe('AuthService', () => {
  it('should validate user', async () => {
    // test logic
  });
});
```
