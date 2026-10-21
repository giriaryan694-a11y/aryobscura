<div align="center">

# AryObscura

**Client-side script obfuscation & deobfuscation suite**

Encode scripts into unreadable-but-executable forms. Peel layers off obfuscated ones.

**Nothing leaves your browser — single HTML file, zero network calls, no telemetry.**

### 🌐 Live: [giriaryan694-a11y.github.io/aryobscura](https://giriaryan694-a11y.github.io/aryobscura/)

</div>

---

## Modes

### ⬦ Obfuscate
Pick **platform → language → method**, paste or upload your script, get an obfuscated output that **runs exactly like the original**.

### ◎ Analysis & Deobfuscate
Paste an obfuscated script — AryObscura fingerprints the encoding scheme, peels up to **8 layers** automatically, shows the layer chain, and reconstructs the readable source with a language guess.

## Supported targets

### Windows
| Target | Methods |
|---|---|
| **PowerShell** (`.ps1`) | Base64 → IEX · Char-code array → IEX · Double-layer Base64 |
| **Batch** (`.bat`/`.cmd`) | certutil drop → execute → wipe |
| **VBScript** (`.vbs`) | Chr() concatenation → Execute · XOR keystream + Base64 → Execute |
| **VBA Macro** (Office) | Chr() chain → hidden Shell (AutoOpen) · Base64 → certutil drop |
| **LNK Shortcut** | Generator script → `.lnk` with `-enc` (UTF-16LE) target · char-splat variant |
| **HTA** (`.hta`) | Base64 → document.write · Char-code stream |

### Linux
| Target | Methods |
|---|---|
| **Bash / Zsh** (`.sh`) | Base64 eval · Hex printf eval · Variable-indirection Base64 · Double-layer Base64 |
| **POSIX Shell** (`/bin/sh`) | POSIX-safe Base64 eval (dash, ash, busybox) |
| **Desktop Entry** (`.desktop`) | Exec wrapper (decode-on-run) |

### Universal
| Target | Methods |
|---|---|
| **Python** (`.py`) | exec(b64decode) · bytes.fromhex · Hex-wrapped double layer · ROT-13 |
| **JavaScript** (`.js`) | eval(atob) · String.fromCharCode stream · Double-layer Base64 |
| **PHP** (`.php`) | eval(base64_decode) · ROT-13 + Base64 |
| **Perl** (`.pl`) | eval pack H* · MIME::Base64 |
| **Java** (`.java`) | Base64 → Runtime.exec · char[] literal (Java 8+) |
| **Ruby** (`.rb`) | eval(Base64.decode64) · eval(pack H*) |
| **Lua** (`.lua`) | loadstring + string.char (5.1/LuaJIT) · self-contained Base64 decoder |
| **HTML** (`.html`) | Base64 → document.write · Char-code stream |

## What the analysis engine detects

Base64 eval chains (shell), `exec(b64decode)` / `bytes.fromhex` / ROT-13 (Python), `FromBase64String` + IEX (PowerShell), `-enc` EncodedCommand (UTF-16LE), `atob` / `fromCharCode` (JavaScript), `pack H*` / `decode_base64` (Perl), `base64_decode` / `str_rot13` (PHP), `Chr()` chains / XOR keystream (VBScript), Chr→Shell / certutil drops (VBA), `loadstring` patterns (Lua), `Base64.decode64` / `pack H*` (Ruby), Java `Base64.getDecoder` / `char[]`, certutil echo drops (Batch), raw Base64 blobs, and nested combinations up to 8 layers.

## Verification

Every method was tested before shipping:

- **Shell methods executed for real** — obfuscated bash/sh/`.desktop` scripts ran and reproduced the original output
- **Other runtimes simulated** — each generated wrapper's actual decode logic was transpiled and executed (PowerShell expression chains, VBS/Lua decoders with real language semantics, UTF-16LE `-enc` chains), plus full round-trip through the deobfuscation engine
- **Padding bugs caught & fixed** — the VBS and Lua Base64 decoders mishandled `=` padding; both now pass sweeps across all length mod-3 cases
- Outputs stay **fully executable** — obfuscation wraps payloads, it never alters them

## Notes

- Binary formats (`.lnk`, `.scr`, `.exe`) can't be text-obfuscated directly — the LNK target emits a generator script that builds the shortcut with an encoded target
- VBA / Java / LNK methods take a **command line** as input (their runtimes don't exec arbitrary source text); each method card states this
- Variable names and XOR keys are **randomized on every generation** — no two outputs are identical

## Use responsibly

AryObscura is built for **education, code protection, CTFs, and authorized security testing**. Only use it on code you own or are explicitly authorized to test.

---

<div align="center">

**Made By Aryan Giri** · giriaryan694-a11y

</div>
