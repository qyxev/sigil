<div align="center">

# Sigil

**Enterprise-grade desktop drive initiation, monitoring, and recovery.**  
*Initiate a volume, track changes securely, and recover failing drives without ever formatting.*

[![Rust](https://img.shields.io/badge/Rust-Workspace-000000?style=for-the-badge&logo=rust&logoColor=white)](https://www.rust-lang.org/)
[![Tauri 2](https://img.shields.io/badge/Tauri_2-Desktop_App-24C8D8?style=for-the-badge&logo=tauri&logoColor=white)](https://v2.tauri.app/)
[![Python](https://img.shields.io/badge/Python-Stdlib_Toolkit-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Status](https://img.shields.io/badge/All_Systems-Passing-2EA44F?style=for-the-badge&logo=checkmarx&logoColor=white)](#status)

</div>

---

## 📖 Overview & Core Principles

Built after the **E: rescue**—a dying FAT32 drive that was successfully imaged, repaired across 33/33 checks, hash-verified, and written back without formatting—**Sigil** is engineered around field-tested reliability principles:

* 🛡️ **Never `chkdsk /F`, never format:** Metadata is rebuilt from a verified image; writes occur exclusively through a secured approval gate.
* 🔍 **The image is the source of truth:** Live media is treated as hostile and read-only by default, re-verified against the source image during acceptance.
* ⚡ **Short reads are evidence, not EOF:** Dying media returning `0xFF` or nothing is surfaced as divergence rather than a truncated file.
* 🔐 **Read-back verification:** Every single write operation is rigorously SHA-256 read-back verified.
* 📂 **Artifact protection:** Volume labels are treated strictly (preventing stray empty `AFK` files), and deleted names overwritten with `0xE5` preserve their structure via `_` placeholders.

---

## 🛠️ Tech Stack & Workspace Layout

Sigil is organized as a high-performance Rust workspace complemented by a Tauri desktop interface and a standalone Python recovery toolkit.

| Module | Technology | Description & Role |
| :--- | :--- | :--- |
| **`sigil-core`** | Rust | Device management, hashing, partition geometry, raw imaging, and audit logging. |
| **`sigil-fs`** | Rust | FAT32 parsing, 33-check integrity validation, and metadata repair builders. |
| **`sigil-journal`** | Rust | `.dskx` marker container management, baseline manifests, HMAC chaining, and tree scanning. |
| **`sigil-engine`** | Rust | Cryptographic approval token lifecycle and recovery pipeline write gating. |
| **`sigil-app`** | Tauri 2 (HTML/CSS/JS) | Desktop application frontend and UI wiring (Drives / Initiate / Journal / Recovery / Audit). |
| **`python/sigil_recovery`** | Python (Stdlib) | Standalone recovery toolkit and command-line interface. |

```text
sigil/
├─ Cargo.toml               # Rust workspace (5 members, pinned deps)
├─ crates/
│  ├─ sigil-core/           # Devices, hashing, partition geometry, imaging, audit log
│  ├─ sigil-fs/             # FAT32 parse, 33-check validator, metadata repair builders
│  ├─ sigil-journal/        # .dskx marker, baseline manifest, HMAC chain, tree scan
│  ├─ sigil-engine/         # Approval tokens + the recovery pipeline (the write gate)
│  └─ sigil-app/            # Tauri 2 desktop app (static HTML/CSS/JS, no npm required)
├─ ui/                      # Embedded frontend (Drives / Initiate / Journal / Recovery / Audit)
├─ python/sigil_recovery/   # Standalone recovery toolkit (stdlib only)
├─ python/tests/            # 42 tests, synthetic FAT32 fixture
├─ docs/                    # Format spec + threat model
└─ scripts/make_icon.py     # Regenerates the app icon
