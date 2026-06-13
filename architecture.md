# BCYX Architecture and BCYX-SWAP PoC

## Executive Summary

BCYX is a **privacy-first cross-chain coordination and settlement** framework that separates *public verifiability* from *private execution*. It uses commitment trees, accumulator roots, Bitcoin anchoring, and recursive zk-STARK proofs to validate swaps without revealing sensitive data.

The BCYX-SWAP prototype demonstrates private settlement coordination between BTC on the Bitcoin network and a correspondent-chain asset on a chain that can interact with Bitcoin state. The correspondent chain may be a Bitcoin sidechain, Bitcoin Layer 2, UTXO-compatible chain, smart-contract chain with BTC verification adapters, federated BTC environment, or another settlement domain with auditable Bitcoin anchoring.

## Architecture

```mermaid
flowchart LR
    U[Users / Treasuries]
    U --> C[Commitment Layer]
    C --> P[Shielded Coordination Pools]
    P --> E[Swap Coordination Engine]
    E --> Z[Zero-Knowledge Proof Engine]
    Z --> A[Accumulator / Batch Proof Aggregation]
    A --> V[Correspondent-Chain Verification]
    V --> X[Bitcoin + Correspondent-Chain Execution]
```

BCYX’s **commitment layer** encodes swap intents and asset deposits as cryptographic commitments. Commitments can be anchored to Bitcoin for durable timestamping and auditability, while the correspondent chain stores the verification state needed to finalize a swap.

The **Zero-Knowledge Proof Layer** generates proofs that settlement conditions are satisfied without revealing identities, amounts, routes, or private matching logic. Proofs from many swaps are aggregated using accumulators or Merkle trees into a compact batch proof.

Finally, a **Correspondent-Chain Verification** step checks the batch proof and the associated roots. Upon success, execution finalizes on the Bitcoin side and on the correspondent-chain side. Failures or disputes trigger timeout and refund paths.

### Component Responsibilities

| Component | Responsibility |
| --- | --- |
| **Commitment Layer** | Encodes deposit and swap intents as commitments; supports Bitcoin anchoring and correspondent-chain receipts. |
| **Shielded Pools** | Holds batched commitments, hides transaction graphs, and performs private matching. |
| **Swap Coordination Engine** | Tracks paired commitments, manages state transitions, and triggers proof generation. |
| **ZK Proof Engine** | Generates proofs of swap correctness, commitment inclusion, nullifier uniqueness, and policy satisfaction. |
| **Accumulator** | Aggregates commitments, nullifiers, and proofs into compact roots for batch verification. |
| **Verifier Modules** | Verify proofs and roots on a correspondent chain that can interact with Bitcoin network state. |
| **Cross-Chain Executors** | Lock, release, burn, mint, or refund assets according to verified settlement conditions. |

## Security, Threat Model & Privacy

- **Security Assumptions:** BCYX relies on sound proof systems, collision-resistant commitments, correct accumulator updates, and the finality assumptions of Bitcoin plus the selected correspondent chain.
- **Threat Model:** Adversaries may try to double-spend commitments, front-run settlement, forge proofs, or infer private routing. BCYX mitigates this with nullifiers, hidden coordination pools, proof verification, and minimal public inputs.
- **Privacy & Disclosure Model:** Amounts, participants, and routes are hidden by default. Selective disclosure allows specific compliance or policy facts to be proven without exposing the full transaction graph.

## BCYX-SWAP PoC Design

### Data Structures

- **Commitments:** Hashes or Merkle-tree leaves representing private swap intents, BTC locks, or correspondent-chain settlement claims.
- **Nullifiers:** One-time markers derived from commitments to prevent replay or double execution.
- **Swap Objects:** Ephemeral records pairing a BTC-side commitment with a correspondent-chain commitment, including timeout and proof metadata.
- **Receipts:** Chain-native or adapter-produced records proving that a relevant settlement event exists.

### API Endpoints

| Endpoint | Inputs | Outputs | Description |
| --- | --- | --- | --- |
| `POST /commit` | `{user, asset, amount, pubkey}` | `commitmentID` | Create a private settlement commitment. |
| `POST /match` | `{commitmentID_A, commitmentID_B}` | `swapID` | Pair commitments into a candidate swap. |
| `POST /prove` | `{swapID}` | `proofData` | Generate a proof for the swap. |
| `POST /verify` | `{proofData}` | `success/failure` | Submit proof to the correspondent-chain verifier. |
| `POST /settle` | `{swapID}` | `{txId_A, txId_B}` | Finalize release, mint, burn, or reassignment. |
| `POST /refund` | `{swapID}` | `{refundTx}` | Refund assets if proof or timeout conditions fail. |

The API is conceptual. Actual implementations may use events, messages, contract calls, Bitcoin transactions, or correspondent-chain adapters.

### Correspondent-Chain Eligibility

A correspondent chain is eligible for BCYX-SWAP when it can do at least one of the following:

- verify BTC lock, release, or anchor data through a light client, oracle, relay, threshold signer, or bridge adapter,
- store proof verification state and emit finalization receipts,
- represent BTC-backed settlement value through a sidechain, Layer 2, wrapped asset, vault, or federation,
- enforce timeout, refund, or dispute rules compatible with Bitcoin-side settlement,
- or provide auditable anchors back to the Bitcoin network.

### Proof Flow

1. **Commit Phase:** Users create BTC-side and correspondent-chain commitments.
2. **Matching & Coordination:** The shielded pool matches compatible commitments without revealing counterparties.
3. **Proof Generation:** The prover shows that the commitments exist, the swap rules are satisfied, and nullifiers are unused.
4. **Batch Aggregation:** Multiple swaps can be compressed into one root and one aggregate proof.
5. **Verification:** The correspondent-chain verifier validates the proof and emits a settlement event.
6. **Execution:** Bitcoin-side and correspondent-chain-side executors finalize or refund according to the verified outcome.

### Accumulator Updates and Nullifiers

- **Accumulator Algorithm:** Maintain Merkle or sparse-Merkle roots over commitments, nullifiers, proofs, and settlement receipts.
- **Nullifier Handling:** When a commitment is spent, its nullifier is recorded. Reuse of the same private object fails.

### Atomic Swap Protocol

1. **Lock-in:** BTC is locked under a Taproot-compatible script, multisig condition, or PoC escrow rule. The correspondent-chain asset is also locked, reserved, or represented.
2. **Proof Trigger:** The coordinator prepares the proof when both sides have compatible commitments and receipts.
3. **Verify & Execute:** The correspondent-chain verifier accepts the proof, and both settlement sides execute the final state transition.
4. **Timeout/Dispute:** If proof or execution fails, timeouts unlock refund paths.

### Testnet Deployment & Tooling

- **Bitcoin Environment:** Bitcoin Core regtest, Signet, or another controlled BTC-network environment for lock and refund testing.
- **Correspondent-Chain Environment:** A selected sidechain, Layer 2, smart-contract chain, UTXO-compatible chain, or simulated settlement VM with proof verification support.
- **Tooling:** Rust or TypeScript coordination services, STARK prover tooling, adapter SDKs, and reproducible test scripts.

### Benchmarks & Metrics

- proof size for single-swap and batched proofs,
- verifier cost on the correspondent chain,
- proof generation latency,
- batch size versus settlement latency,
- refund execution time,
- and adapter reliability for BTC-state verification.

## Implementation Milestones

| Weeks | Milestone & Outputs |
| --- | --- |
| **W1-2** | Finalize correspondent-chain target criteria, swap schemas, and local dev environments. |
| **W3-4** | Implement commitments, nullifiers, BTC lock adapter, and correspondent-chain receipt format. |
| **W5-6** | Prototype single-swap proof generation and local verification. |
| **W7-8** | Add accumulator roots, batch proof aggregation, and replay tests. |
| **W9-10** | Integrate Bitcoin-side and correspondent-chain-side settlement adapters end to end. |
| **W11** | Benchmark proof size, verification cost, latency, and refund behavior. |
| **W12** | Publish demo, documentation, test vectors, and final technical report. |

## Appendix: References

- Accumulate Network - modular blockchain anchoring and hierarchical receipts.
- Ben-Sasson et al. - scalable transparent computational integrity and zk-STARK foundations.
- BitVMX and related Bitcoin verification research - context for Bitcoin-side computation and anchoring.
- Bitcoin Taproot and timelock primitives - BTC-side lock and refund design.
- Relevant correspondent-chain documentation selected during implementation.

These sources inform BCYX’s design: Bitcoin anchoring, commitment receipts, recursive proof composition, and atomic settlement with timeout recovery.
