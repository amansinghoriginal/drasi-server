# solutions - solution templates

Ready-made pipeline templates (sources + queries + reactions, optionally parameterized with `${VAR}` / `${VAR:-default}` variables) served by the server's catalog API, so users can deploy a working end-to-end example without writing config. The filename minus `.yaml` is the template ID - there is no registry or manifest to update.

- The server WRITES user-created templates into `./solutions` at runtime - treat this directory as runtime-writable, not static repo content.
- `tests/iot_template_e2e_test.rs` parses `iot-temperature-monitor.yaml` by hardcoded path and is `#[ignore]`d (plugin-gated) - template breakage surfaces only via `make test-all`, never plain `cargo test` or CI.
- Keep the built-in template table in the root `README.md` in sync when adding or renaming templates.
