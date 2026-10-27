# src - server crate conventions

Crate-wide rules that no compiler or lint enforces. Architecture, build, and CI rituals live in the root `AGENTS.md`; API-layer rituals in `src/api/AGENTS.md`.

## Error handling (three layers)

- HTTP handlers return `Result<Json<ApiResponse<T>>, ErrorResponse>` (`api/shared/error.rs`); never a bare `Err(StatusCode::...)` (no error body) and never `Ok(Json(ApiResponse::error(...)))` (wrong status code) - both compile fine.
- Internal/service modules use `anyhow::Result` with `.context()`; drasi-lib calls return `DrasiError` - both convert at the handler boundary via `ErrorResponse::from`.
- Error codes come from the `error_codes` module - add new ones there, never ad-hoc strings. `ErrorResponse.message` stays high-level; raw underlying errors (`e.to_string()`, file paths) go in `ErrorDetail::technical_details`.
- If persistence fails after an in-memory mutation succeeded, handlers return `PERSISTENCE_FAILED` (500) stating the change applied but was not saved - the runtime is then ahead of the on-disk YAML.

## Design intent

- The redb WAL under `./data/<instance-key>/wal/` is always on, with deliberately no config off-switch: source-event durability is a baseline guarantee, not an opt-in.
- `persistConfig: false` means API mutations are allowed but not saved; mutations are blocked only when the config file itself is read-only. Saves reconstruct the whole YAML from ComponentGraph snapshots - they don't patch it.
- Dynamic upgrade of a loaded plugin is unsupported by design - replacing a plugin requires a server restart.

## Misc

- `println!` is lint-gated (`print_stdout`); its sanctioned uses are CLI/banner output in the binary - operational logging always uses `log` macros.
- `cli_styles.rs`, `init/`, and `plugin/` are binary-only modules (declared in `main.rs`, not `lib.rs`) - library code cannot use them.
- Plugin-loading integration tests live cross-repo: `cd ../drasi-core && cargo test -p drasi-host-sdk --test integration_test`.
