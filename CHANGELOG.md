# Changelog

All notable changes to SAPLE are documented here.

## v0.1.0 — 2026-06-11

First public binary release.

### Highlights

- CLI binary `saple` for macOS (universal-2) and Windows (x64).
- Z3 4.16.0 statically linked into both binaries; no external solver install required.
- Five frontend translators: x86-64, ARM64, Clang IR, VHDL, blockchain.
- Six CLI subcommands: `parse`, `analyze`, `model`, `match`, `test`, `translate`.
- SPL language with schemas, transitions, agents, systems, properties, and vulnerability patterns.

### Known limitations

- Binaries are unsigned. macOS users hit Gatekeeper on first launch; see install notes.
- The `--version` string still reports the legacy name (`sable 0.1.0`); will be fixed in the next release.
