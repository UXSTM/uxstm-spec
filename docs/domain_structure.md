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

# Domain structure

Canonical page:

- https://uxstm.org/domain_structure/

UXSTM is organized around a domain model: each domain is a typed ISA block (e.g., BIT, INT, WINT, FP, DP/WDP for data-parallel, CR) that can be included or omitted to build a processor profile for a target workload. Inside each domain, UXSTM uses a repeatable domain template: two operand stacks plus two ISA-visible register resources. The result is a uniform execution contract for compilers/JITs and a clean mechanism to configure “universal” cores without changing the fundamental programming model.

The domain template is intentionally stack-first: the canonical operand/result interface is the top of stack (TOS), consistent with well-known stack-execution specifications.

## 1. Domain Template: Architectural Elements

A UXSTM domain is defined by the following ISA-visible elements:

- **M-stack (Main)** — primary evaluation stack (LIFO)
- **S-stack (Staging)** — secondary staging/assist stack (LIFO)
- **Constant register bank** — fast access to frequently used literals (e.g., 0, 1, etc.)
- **Pinned register bank** — anchored registers for “hot” values / globals / descriptors

All four exist to support one execution principle: operations remain defined over stack tops; registers are auxiliary mechanisms to reduce friction and traffic, not a replacement for stack semantics.

## 2. M-stack (Main): Full-Function Execution Surface

**Definition.** The M-stack is the domain’s primary evaluation stack. Most domain operations are specified in terms of TOS semantics:

- operands are taken from the top entries of M-stack (unless an instruction explicitly states otherwise)
- results are pushed back onto M-stack

**Scope.** The M-stack supports the complete functional envelope of the domain, including:

- **Memory transfers:** explicit load/store-style transfers that inject/extract values into/from the stack-based flow
- **Core compute:** arithmetic, logical operations, comparisons, and other domain-defined transforms
- **Bit operations:** where the domain includes them

**Why this matters.** Making the M-stack the “full surface” ensures the architecture remains canonically stack-executed and predictable: the compiler/JIT can reason about execution as “stack in → op → stack out”, and the ISA contract does not devolve into a hidden register machine.

## 3. S-stack (Staging): Decoupled Prepare Path

**Definition.** The S-stack is a secondary operand stack intended for staging and servicing, not for replacing the main evaluation flow.

**Intended usage.** The S-stack exists to handle work that is useful to overlap with M-stack compute or expensive to express as deep-stack manipulation on M:

- **Memory-oriented preparation:** preloading, buffering, and preparing operands while the M-stack performs compute
- **Lightweight primitives:** small bit-ops, inc/dec, and similar low-cost transformations that support preparation workflows
- **Hot-value holding:** keeping frequently used values close (in S or pinned regs) to avoid deep-stack rearrangements on M

**Core idea.** S-stack is a second architectural surface. This decoupling is a key lever for reducing stack traffic and enabling higher instruction-level overlap at the core scheduler level.

## 4. Constant Register Bank: Fast Literals

**Definition.** The constant registers are an ISA-visible bank that provides frequent literals without repeatedly encoding immediates.

**Why constant registers exist:**

- Code density / decode simplicity
- Lower stack churn

## 5. Pinned Register Bank: Anchored “Hot” Values and Globals

**Definition.** Pinned registers are an ISA-visible bank of registers intended to hold values that should remain anchored across instruction sequences.

**Primary purposes:**

- Hot operands
- Descriptors / handles / pointers
- Global-like state
- Loop invariants / masks

**Why pinned registers fit a stack-first ISA.** Pinned registers provide stable anchors that reduce rearrangement while keeping the canonical operand interface stack-based.

## 6. Destructive vs Non-destructive Instruction Forms

UXSTM allows instructions to be specified in either of two semantic forms:

### 6.1 Destructive (consuming)

- operands are popped from the designated stack(s)
- the operation is performed
- the result is pushed

### 6.2 Non-destructive (preserving)

- operands are read from TOS without popping (peek semantics)
- the operation is performed
- the result is pushed while preserving the source operands

**Why both forms are valuable:**

- fewer explicit duplication and rearrangement sequences
- direct mapping for VM/JIT idioms that repeatedly reference the same TOS values
- controlled trade-off between instruction count and stack traffic

## Summary

The UXSTM domain template is built around a simple contract: two operand stacks (Main and Staging) plus constant and pinned register banks, while keeping execution defined over stack tops. M-stack provides the full evaluation surface, and S-stack provides a decoupled preparation surface that reduces main-stack traffic and enables explicit overlap of staging with computation.
