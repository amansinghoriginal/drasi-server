# examples - runnable demos & config catalog

Operator-facing demos wiring sources -> queries -> reactions, from a static YAML catalog (`configs/`) to full apps; each example's own README covers its specifics - this file covers only cross-cutting traps.

- getting-started CI exercises a parallel MIRROR at `tests/integration/getting-started/` with its own config and scripts - keep both in sync when changing the walkthrough.
- `sse-cli` is excluded from the cargo workspace (it declares its own `[workspace]`); build it with `cd examples/sse-cli && cargo build`. `basic_setup.rs` IS a workspace example target and must keep compiling against the `drasi_server` library API.
- All app demos set `autoInstallPlugins: true` and pull plugins from the OCI registry on first start - when developing against a locally patched drasi-core, the plugin-ABI trap in root `AGENTS.md` applies: use `make build-local-plugins`.
- App demos serve the embedded web UI - the empty-`ui/dist` trap in root `AGENTS.md` applies; build via `make build` or `make build-ui`.
- New or renamed config files must be registered for CI validation - see the root `AGENTS.md`.
