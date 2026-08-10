# src/api - REST layer

Versioned REST API over DrasiLib instances. Business logic lives in `shared/` so future API versions can reuse it; version modules (`v1/`) hold thin annotated wrappers, route tables, and the OpenAPI doc. Keep that split - don't put logic in `v1/` handlers. (Known deviation: plugin endpoints keep logic and routes in `v1/plugin_handlers.rs`, delegating to the server-level orchestrator.)

## The endpoint ritual (silent failure)

Adding or changing a component endpoint touches: `shared/handlers/` (logic), `v1/handlers/` (thin `#[utoipa::path]` wrapper), `v1/routes.rs` (registration - usually twice: the `/instances/:instanceId` router AND the default-instance convenience router), and `v1/openapi.rs` `paths()` (plus `components()` for new DTOs). The `openapi.rs` entry is NOT compiler-checked - omitting it compiles cleanly but silently drops the endpoint from the OpenAPI spec, Swagger UI, and downstream consumers. Each path is also specified independently twice - a full string in the annotation, nested fragments in `routes.rs` (`/api/v1` prefix added in `server.rs`) - with no test enforcing parity; extend `tests/openapi_test.rs` when adding endpoints.

## Contracts

- DTO type names are a cross-artifact API: the separately-shipped VS Code extension (`dev-tools/vscode/drasi-server/`) matches OpenAPI schemas by name suffix (`*SourceConfig`, `*ReactionConfig`, `QueryConfig`) - renaming a DTO silently breaks its YAML intellisense.
- New DTOs need `utoipa::ToSchema` plus registration in `openapi.rs` `components()`; plugin config schemas are injected at runtime, not listed there.
- Config request bodies use the `ConfigBody<T>` extractor (JSON/YAML content negotiation), never `axum::Json<T>` - except data-plane endpoints (e.g. `push_source_data`), which stay on `axum::Json` to avoid silent YAML->JSON coercion.
