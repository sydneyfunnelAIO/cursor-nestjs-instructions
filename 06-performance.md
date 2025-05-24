# Performance Optimization

## General
- Use caching (Redis, memory)
- Optimize async operations
- Use pagination, batching
- Avoid memory leaks in WS

## Tools
- Use `@CacheInterceptor`
- Profile with `clinic.js`

Keep long-running operations outside of request cycle.
