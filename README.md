# SAPLE — Symbolic Algebraic Process Logic Engine

A general **formal verification framework**. You describe a system (a protocol, a concurrent algorithm, a controller) as process equations over shared state, written in its specification language **SPL**. SAPLE then checks the properties you state and the patterns you look for, with structural matching, symbolic execution and SMT solving (Z3). Through two front ends, it applies the same checks to machine code.

The CLI binary is `saple`.

- **Documentation:** https://saple.cloud/docs/
- **Latest release:** [v0.3.0](https://github.com/smander/saple/releases/tag/v0.3.0) ([changelog](CHANGELOG.md))

---

## Download

Every package bundles Z3 4.16.0 statically, so there is nothing else to install.

| Platform | File |
|---|---|
| **macOS** (Apple silicon and Intel) | [`saple-macos.dmg`](https://github.com/smander/saple/releases/latest/download/saple-macos.dmg) |
| **Windows** (x64) | [`saple-windows-x64.zip`](https://github.com/smander/saple/releases/latest/download/saple-windows-x64.zip) |
| **Linux** (Ubuntu, Debian) | [`saple-linux-x64.deb`](https://github.com/smander/saple/releases/latest/download/saple-linux-x64.deb) |
| **Linux** (Fedora, RHEL, CentOS) | [`saple-linux-x64.rpm`](https://github.com/smander/saple/releases/latest/download/saple-linux-x64.rpm) |

The links always point to the latest release. The same files are tracked in [`downloads/`](downloads/).

---

## Install

### macOS

Mount the DMG, then run the bundled `install.sh` or copy the binary yourself:

```bash
hdiutil attach saple-macos.dmg
sudo install -m 0755 /Volumes/SAPLE/saple /usr/local/bin/saple
hdiutil detach /Volumes/SAPLE
```

The binary is unsigned, so Gatekeeper blocks the first run. Open **System Settings → Privacy & Security**, choose **Open Anyway**, then run it again.

### Linux

```bash
sudo apt install ./saple-linux-x64.deb   # Ubuntu, Debian
sudo rpm -i saple-linux-x64.rpm          # Fedora, RHEL, CentOS
```

The binary is fully static (musl libc, Z3 bundled), so it needs no system packages.

### Windows

Unzip `saple-windows-x64.zip` and add the folder containing `saple.exe` to your `PATH`.

### Check it runs

```bash
saple --version
```

---

## Your first analysis

Save this model as `turnstile.spl`. It describes a coin-operated turnstile and states that every coin should buy one pass:

```spl
// Every coin should buy one pass.
type Gate = { LOCKED, UNLOCKED } ;

schema Turnstile {
    attributes {
        gate   : Gate = LOCKED ;
        coins  : Int  = 0 ;
        passes : Int  = 0 ;
    }
}

transition coin() { guard: coins < 2 ;
    effect: coins += 1 ; gate := UNLOCKED ; }
transition push() { guard: gate == UNLOCKED ;
    effect: passes += 1 ; gate := LOCKED ; }

agent Controller() {
    attributes { }
    process {
        Locked   = [coins < 2]  coin() . Unlocked
                 + [coins == 2] done ;
        Unlocked = push() . Locked
                 + coin() . Unlocked ;
    }
}

system Entrance {
    schema: Turnstile ;
    agents { Controller() ; }
    properties { noLostCoin : coins <= passes + 1 ; }
}
```

Then run:

```bash
saple analyze turnstile.spl
```

SAPLE finds the run that breaks the property: two coins and no pass. The command exits with status 1, and its report includes:

```
  1 finding(s):

  1. [PROPERTY] HIGH — Property 'noLostCoin' violated
     Property: noLostCoin
     Trace: coin → coin
```

The docs walk through larger models: dining philosophers, MESI cache coherence, proof-of-stake consensus, a token contract, and security examples. See [Examples](https://saple.cloud/examples).

---

## What SAPLE does

- **One language for state, behaviour and requirements.** An SPL model is built from these parts:
  - **schemas:** shared typed state, including integers, Booleans, enumerations, collections and bit-vectors;
  - **transitions:** guarded atomic updates of that state;
  - **agents:** process equations over transitions, built with prefix `.`, choice `+`, parallel `||`, guards and recursion;
  - **systems:** the agents that run together and the properties to check;
  - **patterns:** behaviours to look for, each with constraints and a severity.
- **Checking.** SAPLE walks the process equations over the state and reports every property that fails, with the trace that breaks it. It also reports every pattern that matches, and uses Z3 to decide whether the pattern's constraints can hold.
- **Known limitations** are documented on each language page, so you can see exactly what is checked today.

### Front ends for machine code

| Front end | Input | Flag |
|---|---|---|
| [Intel x86-64 Model](https://saple.cloud/docs/frontends/x86-64) | A static Intel-syntax listing of one function. Every path is checked on bit-vector semantics, with data flow along the path and conditional jumps as guards. | `--arch x86-64` |
| [AArch64 Model](https://saple.cloud/docs/frontends/aarch64) | A trace-v1 recording of one run. Each pattern match is graded VERIFIED, OBSERVED, UNKNOWN or REFUTED. | `--arch aarch64` |

The front ends match pattern libraries (the `patterns/` files described in the docs). Those libraries are not part of the binary packages or this repository yet.

---

## CLI commands

```bash
saple parse     model.spl                 # parse and print a declaration summary
saple analyze   model.spl                 # check properties and match patterns
saple translate listing.txt --arch x86-64 -o out.spl    # x86-64 listing to SPL
saple translate run.json --arch aarch64 -o out.spl      # AArch64 trace to SPL
saple compose   ...                       # compose patterns into chain patterns
saple trace     ...                       # inspect and replay captured run traces
```

Every command and flag is in the [CLI reference](https://saple.cloud/docs/getting-started/cli).

---

## License

[MIT](LICENSE) © Mostovyi
