<pre>
===============================================================================
UXSTM — Universal eXtensible STack Machine
Version: 0.1d (DRAFT)
Owner: UXSTM
Copyright: © 2025 UXSTM. All rights reserved. Unauthorized copying, distribution,
implementation or manufacturing is prohibited.
Authorized sources: uxstm.org
===============================================================================
</pre>

# UXSTM — Universal eXtensible STack Machine

**UXSTM — Universal eXtensible STack Machine** is a next-generation stack architecture built on **UXSISC (SISC)** as its baseline ISA. UXSTM is designed as a native hardware substrate for stack semantics across modern runtimes and protocol-validated execution environments, including smart contracts.

- **Status:** `0.1 — DRAFT` (not finalized)
- **Rights:** All rights reserved. Viewing and evaluation only.
- See: **[LICENSE](./LICENSE)** and **[LEGAL_NOTICE](./LEGAL_NOTICE.md)**

## Authorized source (canonical)

The only official, authorized source of UXSTM materials is:

- **uxstm.org**

Materials published elsewhere (including mirrors, reposts, “forked” documents, archives, translations, or third-party repositories) are not authorized unless explicitly confirmed in writing by the rightsholder.

## Why it matters

- **Alignment with dominant execution models.** Major stack VMs are formally stack-defined.
- **Reduced translation overhead.** A stack-native ISA can map common stack effects more directly, reducing the structural cost of reconstructing stack semantics through register-centric pipelines in interpreters and JIT lowering.
- **More predictable VM performance.** Treating the stack as an architectural interface enables tighter control of hot operands and reduces implicit reshaping, improving determinism at the VM boundary.
- **Extensibility without breaking the model.** Domain accelerators can be introduced as orthogonal extensions over a stable stack execution contract.

## Domain model (configurable multi-domain ISA)

UXSTM is a configurable, stack-native ISA whose core structuring principle is a **domain model**. Functionality is partitioned into ISA domains, enabling universal cores to be configured for specific workloads by selecting the required domain blocks while keeping the canonical **top-of-stack (TOS)** execution model.

### Baseline domain taxonomy (current snapshot)

| Domain | Nominal width class | Summary |
|---|---:|---|
| BIT | 8-bit | bit logic and predication (masks, tests, shifts/rotates, boolean ops) |
| INT | 32/64/128-bit | base scalar integer arithmetic for selected profile width |
| WINT | 256/512-bit | wide scalar integers oriented to wide-word and native EVM-style execution |
| FP | 32/64-bit | scalar floating-point |
| DP | 128–2048-bit | data-parallel domain (SIMD/vector/tile-class aggregate datapaths) |
| WDP | 8192–131072-bit | wide data-parallel domain (very-wide SIMD/tile-class aggregate datapaths) |
| CR | 512-bit | cryptography / wide-primitive domain at the 512-bit class |

### Example profiles (current snapshot)

- **BIT + INT** → minimal / low-power baseline (control, integer logic, lightweight runtimes)
- **BIT + INT + FP** → MCU-class and general embedded that needs scalar FP (control + basic numeric)
- **BIT + INT + FP + WINT + CR** → EVM-oriented execution (wide word + crypto primitives)
- **BIT + INT + FP + WINT + DP + CR** → rollups / batching / throughput-oriented blockchain compute
- **BIT + INT + FP + DP** → general-purpose / “light server”
- **BIT + INT + FP + DP + WDP + CR** → high-load HPC / AI pathways

## Domain structure (repeatable template)

Inside each domain, UXSTM uses a repeatable domain template:

- **M-stack (Main)** — primary evaluation stack (LIFO). Full-function execution surface; most ops are “stack in → op → stack out”.
- **S-stack (Staging)** — staging/assist stack (LIFO). Decoupled prepare path: memory-oriented prep, lightweight primitives for preparation, hot-value holding.
- **Constant register bank** — fast access to frequent literals (e.g., 0, 1) to reduce immediate materialization.
- **Pinned register bank** — anchored “hot” values / globals / descriptors / invariants to reduce deep-stack churn.

UXSTM allows instructions to be specified in either:
- **Destructive (consuming):** pop operands → compute → push result
- **Non-destructive (preserving):** peek operands → compute → push result while preserving sources

## Workloads

### 1) Stack VM acceleration

UXSTM targets stack bytecode ecosystems where the operand stack is the execution interface:

- JVM
- WebAssembly (Wasm)
- EVM
- Other stack VMs

Additional stack-native contract environments:

- TON TVM
- Algorand AVM / TEAL
- Tezos Michelson
- Bitcoin Script

### 2) Blockchain execution targets

UXSTM accelerates blockchains by accelerating their execution surfaces.

**EVM execution surface (EVM bytecode):**
- Ethereum
- BNB Smart Chain
- Polygon PoS
- Avalanche C-Chain
- Celo
- Arbitrum
- Optimism / OP Stack
- Base
- Gnosis Chain

**Wasm execution surface (WebAssembly modules):**
- NEAR
- EOSIO
- Casper
- CosmWasm ecosystem (Cosmos SDK)
- Substrate pallet-contracts (Polkadot ecosystem)
- MultiversX
- Filecoin FVM
- Internet Computer (ICP)
- Stellar Soroban

### 3) AI & High-Performance Compute

AI/HPC is treated as an ISA and microarchitecture extensibility track:

- scalable vector/tile primitives for data-parallel and tensor-like kernels
- mixed precision and integer-heavy pathways for inference
- cryptography and wide-integer primitives as a system foundation for blockchain throughput

## Deployment / implementation forms

UXSTM-processor can be realized across multiple deployment forms:

- on-die coprocessor integrated into x86/Arm/RISC-V SoCs
- PCIe accelerator for server and edge offload
- standalone processor (CPU)
- MCU-class core for embedded
- CXL-device

## Repository docs (mirrors of uxstm.org pages)

- [Overview](./docs/overview.md)
- [Domain model](./docs/domain_model.md)
- [Domain structure](./docs/domain_structure.md)
- [FAQ](./docs/faq.md)
- [Legal notice](./LEGAL_NOTICE.md)

## Legal (read carefully)

This repository is published for **viewing and evaluation only**.

You may:
- view and read the Materials for personal or internal evaluation;
- share links to **uxstm.org**.

Short quotations for commentary or review may be permitted only if they are **non-substantial**, include clear attribution to **uxstm.org**, and do not include or reconstruct substantial parts of the Materials (including complete opcode maps, full tables, full chapters, or complete diagrams).

Everything else is prohibited without written permission, including (non-exhaustive):
- copying/redistribution/mirroring/archiving of Materials in whole or substantial part
- translations or derivative works / “forked specifications”
- implementing/emulating/simulating/manufacturing UXSTM in hardware or software

See: **[LICENSE](./LICENSE)** and **[LEGAL_NOTICE](./LEGAL_NOTICE.md)**.
