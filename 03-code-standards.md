# Code Standards

## General
- Use strict TypeScript types
- Avoid use of `any`
- Use meaningful variable/function names

## Structure
- Document all functions with JSDoc
- Add inline comments for complex logic
- Avoid logic in controllers — delegate to services

## Example
```ts
/**
 * Generates access token
 */
async function generateToken(userId: string): Promise<string> {
  // token creation logic
}
```
