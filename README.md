# SAPLE — Symbolic Algebraic Process Logic Engine

A general-purpose **formal verification framework**. SAPLE models concurrent systems as process equations over a shared state environment and verifies arbitrary properties — safety, liveness, mutual exclusion, custom vulnerability patterns — through structural pattern matching, symbolic execution, and SMT-based constraint solving (Z3).

The specification language is **SPL** (Symbolic Process Language). The CLI binary is `saple`.

- **Project site:** https://saple.cloud
- **Documentation:** https://saple.cloud/docs/
- **Latest release:** [v0.1.0](https://github.com/smander/saple/releases/tag/v0.1.0)

---

## Download

Prebuilt binaries are available below. Both bundle Z3 4.16.0 statically — no extra install needed.

### Latest release (recommended)

| Platform | File | Direct link |
|---|---|---|
| **Windows (x64)** | `saple-windows-x64.zip` | [Releases page](https://github.com/smander/saple/releases/latest/download/saple-windows-x64.zip) |
| **macOS (universal-2)** | `saple-macos.dmg`       | [Releases page](https://github.com/smander/saple/releases/latest/download/saple-macos.dmg) |
| **Linux (Ubuntu / Debian)** | `saple-linux-x64.deb`   | [Releases page](https://github.com/smander/saple/releases/latest/download/saple-linux-x64.deb) |
| **Linux (CentOS / RHEL / Fedora)** | `saple-linux-x64.rpm` | [Releases page](https://github.com/smander/saple/releases/latest/download/saple-linux-x64.rpm) |

### From this repository

The current build is also tracked here for convenience:

- [`downloads/saple-windows-x64.zip`](downloads/saple-windows-x64.zip) (20 MB)
- [`downloads/saple-macos.dmg`](downloads/saple-macos.dmg) (25 MB)
- [`downloads/saple-linux-x64.deb`](downloads/saple-linux-x64.deb) (13 MB)
- [`downloads/saple-linux-x64.rpm`](downloads/saple-linux-x64.rpm) (14 MB)

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

### Linux (Ubuntu / Debian)

```bash
sudo apt install ./saple-linux-x64.deb
saple --version
```

### Linux (CentOS / RHEL / Fedora)

```bash
sudo rpm -i saple-linux-x64.rpm
saple --version
```

The Linux binary is fully static (musl libc, Z3 4.16.0 bundled) — no system dependencies.

The macOS binary is unsigned. On first run Gatekeeper will block it; open **System Settings → Privacy & Security** and click **Open Anyway**, then re-run.

---

## Your first analysis

The dining philosophers — five agents, five forks, three properties to verify:

```bash
saple analyze dining_philosophers.spl --verbose
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

Two front ends turn machine code into SPL models and check them the same way:

| Front end | Input | Flag |
|---|---|---|
| [Intel x86-64 Model](https://saple.cloud/docs/frontends/x86-64) | a static Intel-syntax assembly listing; every path checked on bit-vector semantics | `--arch x86-64` |
| [AArch64 Model](https://saple.cloud/docs/frontends/aarch64) | a trace-v1 recording of one run; each match graded VERIFIED, OBSERVED, UNKNOWN or REFUTED | `--arch aarch64` |

---

## CLI commands

```bash
saple parse     file.spl                # Parse and print AST
saple analyze   file.spl                # Verify properties + match patterns
saple translate listing.txt --arch x86-64 -o out.spl   # x86-64 listing to SPL
saple translate run.json --arch aarch64 -o out.spl     # AArch64 trace to SPL
```

See the [full CLI reference](https://saple.cloud/docs/getting-started/cli) on the docs site.

---

## License

[MIT](LICENSE) © Mostovyi
