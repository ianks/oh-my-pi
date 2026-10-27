# Amity's minimal fork of oh-my-pi

Carries only what Amity (shopify-playground/amity, `rig` runtime) needs to build oh-my-pi's crates on **stable Rust**:

1. `pi-ast`: tree-sitter `wasm` is an opt-in feature (upstream PR can1357/oh-my-pi#17153).
2. vendored `brush-core`: `pre_exec(setsid)` fallback when `process_setsid` is unavailable (upstream PR #17154).

Branch `amity/stable-toolchain` = upstream `main` at some commit + one commit with both patches.
(`xutf` is not forked: Amity's `crates/xutf-shim` replaces it via `[patch.crates-io]`.)

## Rebase procedure (bump the pinned rev)
1. `git fetch upstream` (`can1357/oh-my-pi`); `git rebase upstream/main` on `amity/stable-toolchain`
   (drop a patch once its upstream PR has landed). Push to this fork.
2. In Amity: replace the old rev with the new SHA in the root `Cargo.toml` (every `pi-*` dep AND every
   `[patch.crates-io]` entry), run `cargo update -p pi-edit` (refreshes Cargo.lock), then update
   `cargoLock.outputHashes` in `deploy/v2/nix/package.nix` (set to `lib.fakeHash`, `nix build`, copy the reported hash).
3. Gates: `cargo check -p amity --all-targets`, `scripts/check-msrv.sh`, the Nix build, THIRD-PARTY-NOTICES.md if
   new licences appeared. Bump deliberately; never track upstream.
