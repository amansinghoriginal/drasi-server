# ui - embedded web UI

React/TypeScript single-page app served by the Rust binary at `/ui`, so operators can inspect and manage instances, components, and plugins without curl-ing the REST API. There is deliberately no separate UI deployment - the binary is the only distribution channel.

## Serving model (defined outside this directory)

- Release builds embed `ui/dist` via rust-embed in `src/ui_assets.rs` - run `make build-ui` first; the empty-`ui/dist` trap is covered in root `AGENTS.md`.
- Debug builds read `ui/dist` from disk at runtime - `npm run build` alone refreshes `/ui`, no Rust recompile needed.
- Dev loop: run the Rust server on :8080, then `npm run dev` - Vite serves on :3000 and proxies `/api` and `/health` to the server; the production base path is `/ui/`.

## Contracts & gaps

- `src/api/types.ts` and `client.ts` are hand-written mirrors of the server's Rust DTOs - no codegen exists; update them whenever a Rust API DTO changes.
- Plugin config forms are generated at runtime from the server's plugin schema endpoint via rjsf - never hand-build a form for a specific plugin kind.
- There is no test suite and no working lint config; CI only builds the UI inside the Docker image. Verify changes by building and exercising the UI against a running server.
