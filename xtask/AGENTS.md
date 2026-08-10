# xtask - vendored native libs tooling

`cargo xtask vendor push/pull` manages prebuilt native libraries (jq/OpenSSL for Windows MSVC targets) as OCI artifacts at `ghcr.io/drasi-project/vendor/{target}:{tag}`, so release builds pull them instead of building from source.

- The tarball layout (root dir = target triple, containing `lib/`) has TWO independent consumers: the root `build.rs` auto-downloads it with its own implementation pinned to tag `v1`, and `release.yaml` runs a pull before Windows MSVC builds. A layout change breaks Windows release builds and the failure only surfaces at release time - nothing in CI validates the layout earlier; update `build.rs` and bump the tag in both `build.rs` and `release.yaml` together.
- Pushing requires a PAT with `write:packages` scope (`gh auth token` OAuth tokens lack it). Signing is opt-in (`--sign`, cosign keyless) while `--verify` pins the GitHub Actions OIDC identity `https://github.com/drasi-project/*` - published artifacts aren't guaranteed to satisfy it, so check before relying on `--verify`.
