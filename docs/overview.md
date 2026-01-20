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

# Overview

Canonical page:

- https://uxstm.org/overview/

UXSTM — Universal eXtensible STack Machine is a next-generation stack architecture built on UXSISC (SISC) as its baseline ISA. UXSTM is designed as a native hardware substrate for stack semantics across modern runtimes and protocol-validated execution environments, including smart contracts.

UXSISC — Universal eXtensible Stack Instruction Set Computer defines a stack-native ISA and a processor class aimed at eliminating structural overhead that occurs when stack execution models are forced onto register-centric architectures. In SISC, the stack is established as a first-class architectural interface rather than a secondary abstraction reconstructed by compilers or JIT translators.

UXSTM targets three priority domains: acceleration of mainstream stack VMs (primarily JVM, WebAssembly, and EVM), acceleration of blockchain execution (including native EVM execution), and scalable AI/HPC through ISA- and microarchitecture-level extensibility.

UXSTM-processor can be realized across multiple deployment forms: as an on-die coprocessor integrated into x86/Arm/RISC-V SoCs, as a PCIe accelerator for server and edge offload, as a standalone processor (CPU), as an MCU-class core for embedded, and as a CXL-device.

## Why it matters

- Alignment with dominant execution models. Major stack VMs are formally stack-defined.
- Reduced translation overhead. A stack-native ISA can map common stack effects more directly, reducing the structural cost of reconstructing stack semantics through register-centric pipelines in interpreters and JIT lowering.
- More predictable VM performance. Treating the stack as an architectural interface enables tighter control of hot operands and reduces implicit reshaping, improving determinism at the VM boundary.
- Extensibility without breaking the model. Domain accelerators can be introduced as orthogonal extensions over a stable stack execution contract.

## Workloads

### 1) Stack VM acceleration

UXSTM targets stack bytecode ecosystems where the operand stack is the execution interface:

- JVM
- WebAssembly (Wasm)
- EVM
- Other stack VM

Additional stack-native contract environments:

- TON TVM
- Algorand AVM / TEAL
- Tezos Michelson
- Bitcoin Script

### 2) Blockchain execution targets

UXSTM accelerates blockchains by accelerating their execution surfaces:

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
