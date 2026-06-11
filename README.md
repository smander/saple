# SAPLE — Symbolic Algebraic Process Logic Engine

A general-purpose **formal verification framework**. SAPLE models concurrent systems as process equations over a shared state environment and verifies arbitrary properties — safety, liveness, mutual exclusion, custom vulnerability patterns — through structural pattern matching, symbolic execution, and SMT-based constraint solving (Z3).

The specification language is **SPL** (Symbolic Process Language). The CLI binary is `saple`.

- **Project site:** https://saple.cloud
- **Documentation:** https://saple.cloud/docs/
- **Latest release:** [v0.1.0](https://github.com/smander/SAPLE/releases/tag/v0.1.0)

---

## Download

Prebuilt binaries are available below. Both bundle Z3 4.16.0 statically — no extra install needed.

### Latest release (recommended)

| Platform | File | Direct link |
|---|---|---|
| **Windows (x64)** | `saple-windows-x64.zip` | [Releases page](https://github.com/smander/SAPLE/releases/latest/download/saple-windows-x64.zip) |
| **macOS (universal-2)** | `saple-macos.dmg`       | [Releases page](https://github.com/smander/SAPLE/releases/latest/download/saple-macos.dmg) |

### From this repository

The current build is also tracked here for convenience:

- [`downloads/saple-windows-x64.zip`](downloads/saple-windows-x64.zip) (17 MB)
- [`downloads/saple-macos.dmg`](downloads/saple-macos.dmg) (20 MB)

---

## Install

### Windows (x64)

1. Download `saple-windows-x64.zip` and extract it.
2. Add the folder containing `saple.exe` to your `PATH`.
3. Open a new Command Prompt or PowerShell:

```cmd
saple --version
```

### macOS

Mount the DMG and either run the bundled installer or copy the binary manually:

```bash
hdiutil attach saple-macos.dmg
sudo install -m 0755 /Volumes/SAPLE/saple /usr/local/bin/saple
hdiutil detach /Volumes/SAPLE
saple --version
```

The macOS binary is unsigned. On first run Gatekeeper will block it; open **System Settings → Privacy & Security** and click **Open Anyway**, then re-run.

---

## Your first analysis

The dining philosophers — five agents, five forks, three properties to verify:

```bash
saple analyze dining_philosophers.sbl --verbose
```

Properties checked:

- `noDeadlock` — some philosopher can always act
- `mutualExclusion` — adjacent philosophers never eat simultaneously
- `progress` — at least one philosopher can eventually eat

---

## What SAPLE does

- **Process algebra.** Schemas, transitions, agents, and behaviour equations. Sequential, choice, parallel, and guarded composition are first-class.
- **Any property.** Safety, liveness, mutual exclusion, deadlock freedom, or your own pattern. Nothing is hardcoded.
- **SMT solving (Z3).** Constraint satisfiability rules out false positives. Symbolic execution checks property violations against concrete or symbolic state.
- **Rule extraction.** Findings become reusable, cross-validated rules — exportable as JSON for downstream tooling or digital-twin integration.

### Supported input representations

Translate from other formats into SPL and verify them the same way:

| Frontend | Flag |
|---|---|
| x86-64 assembly | `--arch x86-64` |
| ARM64 / AArch64 assembly | `--arch arm64` |
| Clang / LLVM IR (C, C++, Rust) | `--arch clang` |
| VHDL hardware description | `--arch vhdl` |
| Blockchain smart contracts | `--arch blockchain` |

---

## CLI commands

```bash
saple parse     file.sbl                # Parse and print AST
saple analyze   file.sbl                # Verify properties + match patterns
saple model     file.sbl --mode forward # Symbolic state-space exploration
saple match     file.sbl --pattern p.sbl
saple test      file.sbl --coverage state
saple translate input.s --arch x86-64 -o out.sbl
```

See the [full CLI reference](https://saple.cloud/docs/getting-started/cli) on the docs site.

---

## License

[MIT](LICENSE) © Mostovyi
