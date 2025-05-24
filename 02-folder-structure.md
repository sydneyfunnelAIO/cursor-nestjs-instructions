# Folder Structure

## Base Structure
Organize code into self-contained modules under `src/modules`.

- `modules/`: Feature-based Nest modules
- `common/`: Global filters, interceptors, guards, etc.
- `utils/`: Shared pure helper functions
- `DataSources/`: 3rd party integrations
- `graphql/`, `websocket/`, `rest/`: API specific layers
- `config/`: Environment-specific configuration

Keep each function small, modular, and well-documented.
