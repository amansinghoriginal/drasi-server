# drasi-server VS Code extension

Companion extension providing YAML intellisense, code lenses, and a management UI against a running Drasi Server. It versions and ships independently of the server crate (own `package.json`; published as a marketplace extension).

- `src/drasi-client.ts` and `src/sdk/` are a hand-written client for the server's `/api/v1` - no codegen; sync them manually when the Rust API surface changes.
- YAML intellisense fetches the connected server's `/api/v1/openapi.json` and matches schemas by DTO name suffix (`src/schema-provider.ts`); the server-side naming contract is owned by `src/api/AGENTS.md`.
- Bundles are produced by esbuild (`esbuild.js`); `tsc` and eslint only type-check/lint - they neither gate nor produce the build.
- `npm test` (or `make vscode-test` at repo root) downloads and launches a real VS Code via Electron - needs network and a display. NO CI workflow runs these tests; run them manually before merging extension changes.
- Drasi config detection YAML-parses for a top-level `apiVersion` starting with `drasi.io/` (`src/drasi-yaml.ts`) - code lenses, diagnostics, schema association, and test fixtures all depend on that field.
