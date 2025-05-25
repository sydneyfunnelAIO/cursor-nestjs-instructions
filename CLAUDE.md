# NestJS Project Guidelines

## Architecture Principles
- Follow modular, feature-based organization under `src/modules/`
- Implement layered separation: Controller → Service → Repository/DataSource
- Controllers should only handle HTTP concerns, delegate all logic to services (Separation of Concerns)
- Place shared utilities in `shared/` and pure helper functions in `utils/`
- Integrate all external APIs under `data-sources/`
- Apply Single Responsibility Principle to all providers and services
- Design providers to be thread-safe for concurrent access
- Each module should be self-contained with its own DTOs, entities, and tests
- Use barrel exports (index.ts) for cleaner imports

## Folder Structure
```
src/
├── modules/              # Feature-based modules (auth, user, product, etc.)
│   └── user/
│       ├── dto/         # Data Transfer Objects
│       ├── entities/    # Database entities/schemas
│       ├── user.controller.ts
│       ├── user.service.ts
│       ├── user.module.ts
│       └── user.service.spec.ts
├── common/              # Global resources
│   ├── decorators/      # Custom decorators
│   ├── filters/         # Exception filters
│   ├── guards/          # Auth guards
│   ├── interceptors/    # Response interceptors
│   ├── middlewares/     # Custom middlewares
│   └── pipes/           # Validation pipes
├── config/              # Configuration files
│   ├── database.config.ts
│   ├── app.config.ts
│   └── validation.ts
├── data-sources/        # External API integrations
│   ├── openai/
│   │   ├── openai.service.ts
│   │   └── openai.module.ts
│   └── database/
│       └── database.module.ts
├── shared/              # Shared modules
│   ├── logger/
│   └── cache/
├── utils/               # Pure utility functions
├── types/               # TypeScript type definitions
├── app.module.ts        # Root module
└── main.ts              # Application entry point

test/                    # Test files
├── unit/               # Unit tests
├── integration/        # Integration tests
└── e2e/                # End-to-end tests
```

## Code Standards
- Use strict TypeScript types - NEVER use `any`
- Write meaningful variable and function names
- Document all functions with JSDoc
- Add inline comments for complex logic
- Keep functions small and single-purpose
- Always use DTOs with class-validator for input validation
- Mark all providers with @Injectable() decorator
- Use dependency injection instead of direct instantiation
- Follow NestJS naming conventions:
  - Controllers: `*.controller.ts`
  - Services: `*.service.ts`
  - Modules: `*.module.ts`
  - DTOs: `*-*.dto.ts` (e.g., create-user.dto.ts)
  - Entities: `*.entity.ts`
  - Interfaces: `*.interface.ts`
  - Tests: `*.spec.ts` (unit) or `*.e2e-spec.ts` (e2e)

## Testing Requirements (TDD Approach)

### Test-Driven Development Workflow
1. **Red**: Write a failing test first
2. **Green**: Write minimal code to make the test pass
3. **Refactor**: Improve code while keeping tests green

### Testing Guidelines
- Write tests for every function BEFORE implementation
- Use Jest and @nestjs/testing
- Run tests before build/start (`npm run test:watch` during development)
- Aim for minimum 80% code coverage

### Test Types
- **Unit Tests**: Services, utilities, pure functions
  ```typescript
  // user.service.spec.ts
  describe('UserService', () => {
    let service: UserService;
    let repository: MockType<Repository<User>>;

    beforeEach(async () => {
      const module = await Test.createTestingModule({
        providers: [
          UserService,
          { provide: getRepositoryToken(User), useFactory: repositoryMockFactory },
        ],
      }).compile();
    });
  });
  ```

- **Integration Tests**: Controllers with mocked services
  ```typescript
  // user.controller.spec.ts
  describe('UserController', () => {
    let controller: UserController;
    let service: UserService;

    beforeEach(async () => {
      const module = await Test.createTestingModule({
        controllers: [UserController],
        providers: [
          {
            provide: UserService,
            useValue: {
              findAll: jest.fn(),
              findOne: jest.fn(),
            },
          },
        ],
      }).compile();
    });
  });
  ```

- **E2E Tests**: Full application flow
  ```typescript
  // user.e2e-spec.ts
  describe('UserController (e2e)', () => {
    let app: INestApplication;

    beforeEach(async () => {
      const moduleFixture = await Test.createTestingModule({
        imports: [AppModule],
      }).compile();

      app = moduleFixture.createNestApplication();
      await app.init();
    });

    it('/users (GET)', () => {
      return request(app.getHttpServer())
        .get('/users')
        .expect(200)
        .expect('Content-Type', /json/);
    });
  });
  ```

### Testing Best Practices
- Mock external dependencies using Jest mocks
- Use TestingModule from @nestjs/testing for DI testing
- Test happy path, error cases, and edge conditions
- Use descriptive test names: `should return user when valid id provided`
- Group related tests with `describe` blocks
- Use `beforeEach` for common setup
- Keep tests independent and isolated
- Use factories for test data generation
- Test one thing per test case
- Always clean up resources in `afterEach` or `afterAll`
- Use `it.todo()` for planned but unimplemented tests
- Test async operations properly with `async/await` or `done()`
- Validate error messages and error types
- Use `jest.spyOn()` to verify method calls
- Test database transactions with rollback

### Testing Tools
- **Jest**: Main testing framework
- **@nestjs/testing**: NestJS testing utilities
- **supertest**: HTTP assertions for e2e tests
- **jest-mock-extended**: Enhanced mocking capabilities
- **faker**: Generate realistic test data

### Testing Commands
```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:cov": "jest --coverage",
    "test:debug": "node --inspect-brk -r tsconfig-paths/register -r ts-node/register node_modules/.bin/jest --runInBand",
    "test:e2e": "jest --config ./test/jest-e2e.json"
  }
}
```

## Security Best Practices
- Sanitize all inputs using class-validator
- Validate all DTOs
- Use guards and interceptors for authentication/authorization
- Never leak internal errors to clients
- Enable CSP headers
- For GraphQL: Secure resolvers with guards, prevent overfetching
- For WebSocket: Use token-based handshake, validate all payloads

## Performance Optimization
- Implement caching with Redis or in-memory cache
- Use @CacheInterceptor where appropriate
- Optimize async operations
- Implement pagination and batching for large datasets
- Avoid memory leaks in WebSocket connections
- Keep long-running operations outside request cycle
- Use lazy loading for modules and dependencies
- Profile with clinic.js for performance bottlenecks
- Implement database connection pooling
- Use indexes on frequently queried fields

## API-Specific Guidelines

### REST
- Use guards and interceptors
- Handle errors gracefully
- Follow RESTful conventions
- Use proper HTTP status codes
- Implement versioning (e.g., /api/v1/)
- Use @ApiTags() for Swagger documentation

### GraphQL
- Define clear schemas in `.graphql` files
- Use code-first or schema-first approach consistently
- Implement DataLoader for N+1 query prevention
- Use DTOs and resolvers
- Apply guards to resolvers
- Limit field exposure and query depth
- Write integration tests for resolvers
- Implement proper error handling with GraphQL errors

### WebSocket
- Use @WebSocketGateway
- Implement token-based authentication in handshake
- Validate all incoming events
- Use rooms for broadcasting to specific clients
- Implement heartbeat/ping-pong for connection health
- Handle reconnection logic
- Write e2e tests with socket clients

## Development Workflow (TDD)
1. Create feature module under `src/modules/`
2. **Write failing test first** (Red phase)
   - Start with unit test for the service
   - Define expected behavior
   - Run test to see it fail
3. **Implement minimal code** (Green phase)
   - Write just enough code to pass the test
   - Don't add extra functionality
4. **Refactor** (Refactor phase)
   - Improve code quality
   - Ensure tests still pass
5. Repeat for each feature:
   - Service unit tests → Service implementation
   - Controller integration tests → Controller implementation
   - E2E tests → Full feature validation
6. Add DTOs with validation (test DTO validation separately)
7. Apply appropriate guards/interceptors (with tests)
8. Run `npm run test:cov` to ensure coverage > 80%
9. Profile performance if needed

### TDD Example Flow
```bash
# 1. Create module
nest g module users

# 2. Create service test
nest g service users --no-spec
# Manually create users.service.spec.ts with failing tests

# 3. Run tests (see them fail)
npm run test:watch users.service

# 4. Implement service to pass tests

# 5. Create controller test
nest g controller users --no-spec
# Manually create users.controller.spec.ts

# 6. Implement controller to pass tests

# 7. Create e2e test
# Create test/users.e2e-spec.ts

# 8. Run all tests
npm run test
npm run test:e2e
```

## Dependency Injection Best Practices
- Use @Injectable() decorator for all providers
- Prefer constructor injection over property injection
- Use custom providers (useValue, useFactory, useClass) when needed
- Implement lazy loading for heavy dependencies
- Export only necessary providers from modules
- Use @Global() decorator sparingly (only for truly global services)

## Module Best Practices
- Each module should have a single, well-defined purpose
- Import only required modules (avoid circular dependencies)
- Use forRoot() and forRootAsync() patterns for configurable modules
- Implement dynamic modules for reusable, configurable features
- Keep module imports hierarchical and organized

## Advanced Patterns
- Use interceptors for response transformation and logging
- Implement exception filters for consistent error handling
- Apply pipes for data transformation and validation
- Use guards for authentication and authorization
- Implement custom decorators for reusable logic
- Consider microservices architecture for large-scale applications
- Use Repository pattern for database operations
- Implement CQRS pattern for complex domains
- Use Event Sourcing for audit trails
- Apply Circuit Breaker pattern for external services