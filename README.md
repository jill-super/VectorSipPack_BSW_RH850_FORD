![Docs CI](https://github.com/<org>/BSW_RH850_FORD/actions/workflows/docs-ci.yml/badge.svg)
![License](https://img.shields.io/badge/license-MIT-green)
![AUTOSAR](https://img.shields.io/badge/standard-AUTOSAR%204-green)
![Language](https://img.shields.io/badge/language-C-blue)
![MCU](https://img.shields.io/badge/MCU-Renesas%20RH850-red)
![SIP](https://img.shields.io/badge/SIP-18.00.15-blue)
![Delivery](https://img.shields.io/badge/delivery-CBD1601056_D05-lightgrey)

# BSW_RH850_FORD

Automotive Basic Software (BSW) for the **Renesas RH850** microcontroller platform, tailored for **Ford** automotive requirements — delivered as the **Vector MICROSAR SIP CBD1601056 D05** (SIP 18.00.15, Nexteer / MSR_Ford_SLP1, derivative `R7F701373AEABG`).

> **Documentation site:** run it locally with `cd docs && npm install && npm run dev`
> (Astro Starlight). It is published automatically to
> `https://<org>.github.io/BSW_RH850_FORD/` on every push to `main` that touches
> `docs/` — see [`docs/README.md`](docs/README.md).

<details>
<summary><strong>Table of contents</strong></summary>

- [Overview](#overview)
- [AUTOSAR layers & module map](#autosar-layers--module-map)
- [Vector vs. custom](#vector-vs-custom)
- [Repository structure](#repository-structure)
- [Installation & build](#installation--build)
- [Documentation](#documentation)
- [License](#license)
- [Disclaimer](#disclaimer)

</details>

## Overview

Core AUTOSAR services, ECU abstraction, MCAL integration, CAN network management, diagnostics, memory and cryptographic services for integration into automotive ECU projects, plus DaVinci Configurator tooling and the full Vector delivery documentation set (`Doc/`).

<details>
<summary><strong>Features</strong></summary>

- **Communication:** CAN driver (RH850 MCAN), CAN Interface, CAN State Manager, CAN / generic Network Management, Communication Manager, AUTOSAR COM, PDU Router, CAN Transport Protocol, TJA1043 transceiver.
- **Diagnostics & supervision:** Development Error Tracer, Diagnostic Event Manager, Diagnostic Communication Manager, Watchdog Manager / Interface.
- **Memory:** NVRAM Manager → Memory Abstraction → Flash EEPROM Emulation (`Fee_30_SmallSector`).
- **Crypto:** Crypto Service Manager dispatch; software crypto (`Cry_30_LibCv`); RH850 ICUM hardware driver; Ford `CryFord` package; cryptovision `SecMod` library.
- **System:** AUTOSAR OS, ECU / Basic Software Mode Managers, CRC and Vector standard libraries, shared compiler/platform/memory-mapping headers.
- **Measurement & calibration:** XCP + XCP-on-CAN with application hooks.
- **Tooling:** DaVinci Configurator, RTE/XSLT generators, MSSV plugins, RteAnalyzer, A2L/McData converters, MCAL integration helper, SIP modification checker.

</details>

## AUTOSAR layers & module map

| Module | Path | AUTOSAR layer | Origin |
|--------|------|---------------|--------|
| SwcSecAccessFord | `SWC/SecAccess/` | Application Software | Vector (Ford-customised) |
| Xcp, CanXcp, Vsg | `BSW/Xcp`, `BSW/CanXcp`, `BSW/Vsg` | Complex Device Drivers | Vector |
| CryFord | `BSW/CryFord/` | CDD (Ford crypto access) | Vector (Ford-customised) |
| Os, BswM, EcuM, WdgM, Crc, VStdLib | `BSW/Os`, `BSW/BswM`, … | Services / System | Vector |
| Det, Dem, Dcm | `BSW/Det`, `BSW/Dem`, `BSW/Dcm` | Services / Diagnostics | Vector |
| NvM | `BSW/NvM/` | Services / Memory | Vector |
| Csm, Cry_30_LibCv, Cry_30_Rh850Icum | `BSW/Csm`, `BSW/Cry`, … | Services / Crypto | Vector |
| SecMod (cv act lib) | `BSW/SecMod/` | Crypto (third-party lib) | Third-party (cryptovision) via Vector |
| CanIf, CanSM, PduR, WdgIf, CanTrcv_30_Tja1043 | `BSW/CanIf`, … | ECU Abstraction | Vector |
| MemIf, Fee_30_SmallSector | `BSW/MemIf`, `BSW/Fee_30_SmallSector` | ECU Abstraction / Memory | Vector |
| _Common (Std_Types, Compiler, MemMap, ComStack) | `BSW/_Common/` | Common foundation | Vector |
| Can (RH850 MCAN) | `BSW/Can/` | MCAL / CAN driver | Vector |
| MCAL integration wrapper | `BSW/Mcal_Rh850P1xC/`, `ThirdParty/` | MCAL (Renesas + Vector glue) | Third-party (Renesas) via Vector |
| Com, ComM, Nm, CanNm, CanTp | `BSW/Com`, … | Communication | Vector |
| Config tooling & descriptions | `DaVinciConfigurator/`, `Generators/`, `BSWMD/`, `Misc/` | Tools | Vector |

Full per-module reference (purpose, API, files, dependencies, delivery docs): [`docs/`](docs/) site → *AUTOSAR layers* / *Modules*.

## Vector vs. custom

- **Effectively the whole functional delivery is Vector-provided** (MICROSAR; every `BSW/*` header carries a Vector copyright notice). Detection: `Vector Informatik GmbH` / `MICROSAR` / `DaVinci` / `GenData` markers + root `SipLicense.lic` (CBD1601056 D05).
- **Ford-customised:** `BSW/CryFord/`, `SWC/SecAccess/`.
- **Third-party via Vector:** `BSW/SecMod/` (cryptovision cv act), `ThirdParty/Mcal_Rh850P1xC/` (Renesas MCAL + Vector integration).
- **Custom (MIT):** this repository's documentation — root `README.md`, `docs/` site — and the root `LICENSE` third-party notice. No custom BSW source in this snapshot.

See the docs site: *Start here → Vector vs. custom*.

## Repository structure

```text
BSW/<Module>/            # MICROSAR sources + mak/ build fragments (Vector)
BSW/<Module>/mak/        # *_cfg.mak, *_check.mak, *_defs.mak, *_rules.mak
BSWMD/<Module>/          # BSW module descriptions (*.arxml) for DaVinci
SWC/SecAccess/           # Ford security-access SWC (Vector, Ford-customised)
ThirdParty/              # Renesas MCAL + VectorIntegration glue
Generators/              # RTE/XSLT, A2L, MSSV plugins, Wdg/McData tooling
DaVinciConfigurator/     # Configurator installation
Doc/                     # Delivery PDFs: TechnicalReferences, ApplicationNotes,
                         #   UserManuals, DeliveryInformation, ReleaseNotes, SafetyManual
Misc/                    # RteAnalyzer, SipModificationChecker, Wdg xsltproc
docs/                    # Docs website (Astro Starlight) — see docs/README.md
LICENSE                  # MIT + third-party notice (Vector/3rd-party stay proprietary)
SipLicense.lic           # Vector SIP license (CBD1601056 D05, SIP 18.00.15)
```

## Installation & build

<details>
<summary><strong>Firmware (Windows-centric Vector flow)</strong></summary>

**Prerequisites:** Renesas RH850 toolchain (automotive GHS/LLVM/GCC), GNU Make, Windows for the generator tools, a valid Vector SIP license.

1. Configure in **DaVinci Configurator** using `BSWMD/*/*.arxml` + your ECU description → generates `*_Cfg` sources.
2. Integrate the MCAL: run `ThirdParty/Mcal_Rh850P1xC/VectorIntegration/Script_MCAL_Prepare.bat` (uses `MIPconfig.xml`, applies `Patches/`).
3. Build with your project-level global makefile that includes each `BSW/<Module>/mak/` fragment (no root makefile is shipped here).
4. Validate with `Misc/SipModificationChecker/SipModificationChecker.bat`.

Details: docs site → *Start here → Build system*.

</details>

<details>
<summary><strong>Docs site (any OS)</strong></summary>

Requires Node.js ≥ 22.12.0 (Astro v7 requirement).

```bash
cd docs
npm install
npm run dev      # local preview, usually http://localhost:4321
npm run build    # production build -> docs/dist/ (same check CI runs)
```

For a project page (`https://<org>.github.io/<repo>/`):

```bash
ASTRO_BASE=/<repo> npm run build
```

</details>

## Documentation

- **Docs site source:** [`docs/`](docs/) (Astro Starlight) — overviews per AUTOSAR layer, one page per module (purpose, origin badge, key files, API excerpt, usage, dependencies, config, delivery-doc links), the [Vector SIP hub](docs/src/content/docs/sip/index.mdx), and converted summaries of **all ~65 delivery PDFs** under `general/converted/`.
- **Originals stay authoritative:** each converted page links its source PDF in `Doc/` and states extraction limits (no `.doc`/`.docx` files exist in the repo — only PDFs, `.txt` licenses, and HTML/XML reports, which are referenced as-is).
- **Finding things:** start at the site landing page, or `docs/src/content/docs/{asw,cdd,services,ecu-abstraction,mcal,communication,memory,diagnostics,crypto,system,tools}/index.mdx`.

## License

This repository uses the **MIT License** for its own documentation content — see [`LICENSE`](LICENSE). It additionally contains **third-party proprietary software** (Vector MICROSAR, cryptovision, Renesas) that keeps its own license terms and is usable only with a valid Vector SIP license (`SipLicense.lic`).

## Disclaimer

Some files are provided “as is” and without warranty. Beta disclaimer applies (`BetaDisclaimer.txt`). See module headers and delivery documentation for more information.

---

*Conventions: every docs page carries an origin badge (Vector / Ford-customised / third-party / custom); code references are relative to the repo root; converted PDFs are summaries — the PDF in `Doc/` is authoritative.*
