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

# FAQ

Canonical page:

- https://uxstm.org/faq/

## What is UXSTM?

UXSTM (Universal eXtensible STack Machine) is a family of stack-native execution architectures where the operand stack(s) are treated as first-class architectural interfaces, not as a compiler artifact. The design goal is to map stack-semantic workloads (VM bytecodes, interpreters/JIT pipelines, compositional execution: JVM, EVM, WASM and etc.) to hardware with fewer structural mismatches in operand access and instruction encoding. The approach emphasizes modularity via domains and profiles rather than a single monolithic ISA.

## Why choose a stack architecture at all?

A large fraction of stack-machine operations can be encoded as zero-address (zero-operand) instructions, because operands are implicitly taken from the top of the operand stack and results are pushed back. This reduces or eliminates explicit operand specifiers (e.g., register indices) in the common case, improving code density and, as a direct consequence, lowering instruction-fetch bandwidth pressure.

Also a hardware stack ISA can execute stack-VM code (JVM, EVM, WASM and etc.) either directly or through a shallow, semantics-preserving translation—i.e., without the substantial “stack-to-register reconstruction” work that register ISAs often impose (temporary register assignment, frequent shuffles, and spill/reload traffic introduced to emulate operand-stack semantics).

## Is a stack ISA inherently “simpler”?

Semantically, many operations share a uniform contract: consume operands from the operand stack; produce a result onto the operand stack. This is a major reason stack machines are common in virtual machines.

## Is UXSTM just “a classic stack computer”?

No: UXSTM’s key thesis is not merely “use a stack,” but make stack semantics a modular, composable architectural substrate (domains/profiles) suitable for modern runtime patterns – AI, stack VM (JVM, WASM, etc.), blockchain (EVM, WASM), HPC, etc.

## Why does UXSTM emphasize JVM/EVM/WASM workloads?

Because these ecosystems are defined by an execution model – operand stack for JVM/WASM; 256-bit operand stack for EVM. A stack-native ISA can reduce impedance mismatch in operand transport and instruction representation, especially for interpreter/JIT lowering paths.

## Why can UXSTM realistically accelerate the JVM?

Because JVM bytecode is specified around an operand stack, and UXSTM makes stack effects a native hardware contract. A stack-native ISA can reduce semantic friction in interpreter/baseline paths by making push/pop/dup-like behavior structurally cheap.

## Why can UXSTM realistically accelerate WebAssembly (WASM)?

Because WebAssembly is formally a stack machine, so its “pop arguments / push results” semantics map directly onto stack-native execution.

## Why can UXSTM realistically accelerate the EVM?

Because UXSTM provides a native 256-bit operand stack (WINT) that matches the EVM’s execution model, so EVM bytecode can run with minimal structural overhead. With a 256-bit-wide WINT-stack, UXSTM can execute EVM-style wide-integer and stack-effect-heavy code paths without relying on multi-limb emulation patterns that general-purpose CPUs often require for 256-bit arithmetic.

## Which VMs can be accelerated by UXSTM?

UXSTM is “stack-native” for VMs whose execution model is an implicit operand stack, so most VM ops map directly to hardware push/pop and stack-ALU primitives with little semantic reshaping. Representative targets include WASM (Rust/C/C++/AssemblyScript), JVM (Java/Kotlin/Scala), EVM (Solidity/Vyper bytecode), and .NET CIL/CLI (C#/F#). The key advantage is reduced stack→register lowering (fewer synthetic moves/spills) and a shorter path from bytecode semantics to native execution.

## Which blockchains can be accelerated by UXSTM?

UXSTM can accelerate blockchains via minimal translation whenever their contract runtime is already a stack VM, so the execution model maps directly to a stack CPU (stack ops stay stack-shaped rather than being lowered into a register machine).

### EVM execution surface (EVM bytecode)

- Ethereum
- BNB Smart Chain
- Polygon PoS
- Avalanche C-Chain
- Celo
- Arbitrum
- Optimism / OP Stack chains
- Base
- Gnosis Chain

For Ethereum/EVM specifically, the effect is amplified by native 256-bit big-int in UXSTM, because the EVM is a stack machine with 256-bit words.

### WASM execution surface (WebAssembly modules)

- NEAR
- EOSIO
- Casper
- CosmWasm-based Cosmos chains (ecosystem)
- Substrate pallet-contracts chains (Polkadot ecosystem)
- MultiversX
- Filecoin (FVM)
- Internet Computer (ICP)
- Stellar Soroban

## What UXSTM deployment/implementation forms are possible?

UXSTM can be realized in multiple form factors depending on the target constraints (latency, power, integration depth, and software stack). It can be built as a standalone general-purpose CPU (primary host processor), or as a tightly coupled coprocessor inside a host CPU (x86/ARM/RISC-V) sharing coherent memory and interrupts for low-latency offload. It can also be delivered as a discrete accelerator (PCIe/CXL) optimized for throughput and domain-specific kernels, or as an MCU-class core for embedded control where deterministic latency and code density matter.

## What are typical UXSTM usage scenarios?

UXSTM is highly flexible and scalable, enabling implementations that range from low-power MCU-class cores to high-throughput server and accelerator designs, while preserving the same core “stack-native” execution principles. Typical usage scenarios include general-purpose computing, high-load backends (event-driven web servers, API gateways, RPC/microservices), and VM-centric platforms where minimal translation matters (WASM/JVM/EVM/.NET runtimes and JIT hot paths). It is also well-suited for blockchain workloads (smart-contract execution, validation, off-chain compute) and cryptography-intensive services (hashing, signatures, proofs). For compute-dense domains, UXSTM targets HPC-style kernels (vector/tile compute, reductions) and AI/ML inference pipelines where predictable dataflow and high arithmetic intensity benefit from specialized execution units and wide arithmetic. In deployment, UXSTM can serve either as the primary compute substrate or as an offload engine for throughput-critical kernels (VM dispatch loops, crypto primitives, and latency-sensitive processing chains).

## What does UXSTM mean by a domain-based architecture?

UXSTM uses a domain-based architecture: the ISA is partitioned into well-defined execution domains, each with explicit operand widths, semantics, and dedicated execution units, so translation and scheduling remain mechanically clear across workloads. The baseline domains are BIT (bit/boolean), INT (scalar integer), FP (floating-point), WINT (native 256-bit big-integer aligned with EVM-style arithmetic), DP (data-parallel), WDP (wide data-parallel), and CR (cryptography, providing 128–512-bit-class primitives). This approach scales from minimal embedded configurations to wide-throughput designs by enabling, specializing, or widening specific domains without changing the core programming model.

## What is the UXSTM domain stack programming model?

Each domain exposes a two-stack model: Main (M) and Staging (S) operand stacks, supported by pinned registers (hot values) and constant registers. Architecturally, execution units operate on stack tops only (stack-first contract): operations consume operands from the top of M/S and push results back to the stack, while registers function as fast holding/reload sources rather than turning UXSTM into a register machine. The M/S split enables practical overlap of compute and preparation (e.g., staging data while main executes).
