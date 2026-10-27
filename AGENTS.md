# Drasi Server - Agent Guide

Drasi Server wraps DrasiLib (from the drasi-core repo) to turn the Drasi continuous-query engine into an operable service: sources feed data changes into always-running Graph queries, and reactions fire on result changes. Operators use it to detect and react to data changes via a REST API, YAML config, and a web UI, without writing engine code. This repo is only the API surface, configuration, and plugin orchestration - all query evaluation lives in the external drasi-lib/drasi-core crates. Those crates are published from the drasi-core repo.

## Commit & PR rituals (CI-enforced)

- Sign every commit: `git commit -s` - DCO `Signed-off-by` trailers are required.
- PRs linking an issue (`#N`) the author is NOT assigned to are auto-closed by automation; PRs with no linked issue get labeled `needs-issue` (write/admin authors exempt from both).
- Before pushing, run `make fmt-check`, `make clippy`, and `make test` - these make-target names are the contract with the shared CI workflows in `drasi-project/.github`.
- PRs touching any `*.md` file trigger an AI workflow that validates YAML config snippets in docs and posts an advisory PR comment (same-repo PRs only) - fix what it flags.
- `.github/workflows/*.lock.yml` files are compiled artifacts: edit the sibling `.md` and run `gh aw compile`; never hand-edit a `.lock.yml`.

## Non-obvious build & config traps

- Use `make build` / `make run` when the web UI matters: the build embeds `ui/dist` at compile time and silently creates it EMPTY if missing, so bare `cargo build --release` ships a binary with no UI.
- `[patch.crates-io]` overrides for local drasi-core never reach runtime-loaded plugins - rebuild those with `make build-local-plugins`; plugin-gated tests run via `make test-all`.
- New server-level, config-only fields (settings, identityProviders, top-level bootstrapProviders) must also be wired into `PreservedServerSettings` and `save()` in `src/persistence.rs`, or the first API-triggered save silently erases them from the user's config file.
- Sample configs under `config/` and `examples/` are CI-validated only if registered in the path list in `tests/example_configs_validation_test.rs` - unregistered files go silently unvalidated.
