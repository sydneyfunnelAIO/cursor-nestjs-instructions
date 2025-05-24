# Security Guidelines

Apply security best practices across all API layers:

## General
- Sanitize all inputs
- Use `class-validator`
- Validate DTOs
- Handle sensitive data securely
- Enable CSP in headers

## REST
- Use guards and interceptors
- Avoid leaking internal errors

## GraphQL
- Secure resolvers with guards
- Prevent overfetching

## WebSocket
- Token-based handshake
- Validate incoming payloads
