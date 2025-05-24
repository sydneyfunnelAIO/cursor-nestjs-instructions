# Architecture Guidelines

This NestJS project is structured following a modular and layered architecture to ensure separation of concerns, scalability, and maintainability.

## Key Principles
- Feature-based module organization
- Layered separation: Controller → Service → DataSource/Repository
- Common/shared utilities and decorators extracted into `common/` and `utils/`
- All external APIs integrated under `DataSources/`

## Example Structure
```
src/
├── modules/
│   └── auth/
│       ├── auth.module.ts
│       ├── auth.controller.ts
│       ├── auth.service.ts
│       └── auth.spec.ts
├── DataSources/
│   ├── openai.ts
│   └── gemini.ts
├── common/
├── utils/
├── graphql/
├── websocket/
├── rest/
├── config/
└── main.ts
```
