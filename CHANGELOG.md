# Changelog

All notable changes to SAPLE are documented here.

## v0.2.1 — 2026-06-21

### Engine kernel

- **Real-typed pattern parameter binding.** The matcher binds `Real`, `Bool`, `Bits(n)`, `Float32`, `Float64` (in addition to `Int`). Substitution emits the corresponding `Formula::*Lit` to Z3.
- **Symbolic effect lifting.** When a pattern parameter is bound `Real` / `Float32` / `Float64`, the analyser lifts each matched transition's effect into SMT constraints: `:=` becomes equation; `if/then/else` becomes ITE-equivalent `And(Implies, Implies)`; sequenced statements conjoin via `Formula::And`.
- **Symbolic Array reads.** `Formula::Select` lowers to Z3 `Array.select` so that `SIG[af[j]]` reaches the solver under QF_ALRA.
- **Float32 / Float64 types.** Lexer recognises `0.1f32`, `0.5e2f64`. Z3 backend emits QF_FP queries.

### SBL libraries

- New directory `nn-verification/lib/` with six composable fragments: `relu.sbl`, `sigmoid_linear_envelope.sbl`, `tanh_linear_envelope.sbl`, `bilinear_havoc_mccormick.sbl`, `ibp_input_region.sbl`, `quantized_lut.sbl`.
- Each fragment ships with a header soundness argument.

### Parser extensions

- `havoc(target)` now accepts arbitrary LValue (e.g. `havoc(sig_out[j])`), not just bare identifiers.
- New quantifier `sum (k : Int) in [lo..hi) . expr` for bounded summation in expression position.
- Chained array subscripts `arr[i][j]` work end-to-end.

### Spec rewrites

- `nn-verification/lstm-encoding/lstm_cell_pruned.sbl` rewritten (616 lines) to compose the σ / tanh / bilinear / IBP library fragments. Envelope coefficients documented in the header.
- `nn-verification/autoencoder-target/autoencoder.sbl` aligned with the `relu.sbl` guarded-choice idiom (header update).

### Packaging

- Linux x86_64 distribution: `saple-linux-x64.deb` (Ubuntu/Debian) and `saple-linux-x64.rpm` (CentOS/RHEL/Fedora). Linux binary is fully static (musl libc + Z3 4.16.0 bundled).
- macOS and Windows artifacts rebuilt with v0.2.0 metadata.

### Validation

- M7 toy gate (spec §5 directional-agreement fallback): SAPLE verdict and the Python+Z3 reference verifier directionally agree on the feed-forward autoencoder.
- M8 regression sweep: all 15 baseline `.sbl` files produce verdicts bit-identical to SAPLE 0.1.0.

### Limitations (see `nn-verification/SAPLE-STATUS.md`)

- No incremental SMT; the pruning loop cost is `H × (single-call cost)`.
- No native IBP pass in Rust — IBP lives in the SBL library.
- No relational abstract domains (DeepPoly-style).
- LSTM pruning validated only at the autoencoder fallback. Per-cell bit-exact LSTM toy validation deferred to v0.3.
- Library soundness is author-attested, not engine-verified.

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
