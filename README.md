# BCYX Documentation

> Technical specifications, research papers, architecture notes, and protocol design documents for BCYX.

## Overview

This repository contains the official technical documentation and research material for BCYX — a privacy-first cross-chain coordination and settlement protocol focused on:
- confidential interoperability,
- selective disclosure,
- aggregated proof verification,
- and shielded settlement coordination.

BCYX separates:
- public verification,
from:
- private execution and coordination.

The protocol is designed as a generalized coordination layer for:
- cross-chain settlement,
- confidential swaps,
- treasury routing,
- oracle coordination,
- and programmable interoperability systems.

---

# Repository Structure

```txt
docs/
├── tech-summary.md
├── architecture.md
├── swapping-mechanism.md
├── accumulators.md
├── zk-verification.md
├── selective-disclosure.md
├── interoperability.md
├── settlement-model.md
├── glossary.md
└── references.md
```

---

# Documents

## `tech-summary.md`
High-level protocol overview and conceptual thesis behind BCYX.

Includes:
- protocol vision,
- coordination model,
- privacy assumptions,
- settlement philosophy,
- and long-term architecture goals.

---

## `architecture.md`
Core system architecture and modular protocol stack.

Covers:
- commitment layers,
- coordination pools,
- proof systems,
- settlement verification,
- and execution flow.

---

## `swapping-mechanism.md`
Proof-of-concept private swap coordination mechanism.

Includes:
- atomic coordination model,
- commitment-based swaps,
- proof flow,
- batch settlement logic,
- and cross-chain synchronization.

---

## `accumulators.md`
Accumulator and proof aggregation design.

Covers:
- batch proof systems,
- recursive aggregation,
- compressed verification,
- commitment roots,
- and synchronization models.

---

## `zk-verification.md`
Zero-Knowledge verification layer specification.

Includes:
- proof generation,
- verification assumptions,
- nullifier systems,
- validity checks,
- and execution consistency guarantees.

---

## `selective-disclosure.md`
Programmable disclosure architecture.

Covers:
- privacy scopes,
- verifier permissions,
- regulated visibility,
- and conditional disclosure models.

---

## `interoperability.md`
Cross-chain coordination and settlement interoperability design.

Includes:
- heterogeneous chain coordination,
- settlement synchronization,
- execution environments,
- and modular verification backends.

---

## `settlement-model.md`
Settlement lifecycle and execution semantics.

Covers:
- atomic coordination,
- asynchronous settlement,
- rollback assumptions,
- and execution guarantees.

---

# Design Principles

- Privacy-first
- Proof-native
- Modular verification
- Chain-agnostic coordination
- Aggregated scalability
- Selective disclosure
- Bitcoin-aligned settlement philosophy

---

# Current Focus

The current pilot focuses on validating BCYX as:
- a confidential swap coordination mechanism,
- powered by aggregated proof verification,
- and shielded settlement execution.

---

# Status

Research / Pilot Phase

This repository is under active development and subject to significant architectural changes.

---

# References

- BitVMX
- Recursive Proof Aggregation
- zkSTARKs
- Halo2
- Stateful Merkle Trees
- Accumulate Protocol
- Threshold Cryptography
- Atomic Swap Protocols

---

# Tagline

```txt
Public verification.
Private coordination.
```
