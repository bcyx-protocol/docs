# BCYX Glossary

> Core terms used across the BCYX documentation set.

---

## BCYX
Privacy-first cross-chain coordination and settlement protocol. BCYX separates public verification from private coordination using commitments, zero-knowledge proofs, accumulators, and shielded execution.

---

## BCYX-SWAP
The proof-of-concept swap mechanism for BCYX. It validates confidential swap coordination between BTC and asset correspondent settlement flows.

---

## Accumulator
A compact state-compression structure that aggregates many commitments, nullifiers, or proofs into a single verifiable root.

---

## Anchor
A public commitment point for a batch of private state. In the paper, anchored state is used so one root can validate many receipts and data chains.

---

## Atomic coordination
A settlement property where either the full swap succeeds or the whole operation reverts safely. No partial completion is considered final.

---

## Batch proof
A proof that aggregates many private actions into one verification object. The paper emphasizes batch efficiency through hierarchical receipts and proof composition.

---

## Commitment
A cryptographic hash or commitment object that hides private values while allowing later proof of knowledge or inclusion.

---

## Commitment root
The public root summarizing a set of commitments stored in an accumulator or Merkle-like structure.

---

## Correspondent chain
A chain that carries the actual final settlement state for a BCYX operation. BCYX coordinates the swap, while correspondent chains finalize the asset state.

---

## Ephemeral settlement
A settlement model where BCYX exists only for the duration of the swap or routing process, then disappears after correspondent-chain finalization.

---

## Hierarchical receipt
A compact proof structure that shows a committed entry exists in an anchored state. The paper describes receipts as O(log N) proofs over state paths.

---

## Nullifier
A public one-time marker used to prevent a commitment or note from being spent twice.

---

## Private coordination
The internal execution and routing logic of BCYX, hidden from public view while settlement validity remains verifiable.

---

## Proof aggregation
The compression of multiple proofs into a smaller verification set, often a single batch proof or root-level proof.

---

## Recursive zk-STARK
A zero-knowledge proof system that can verify nested or aggregated proof statements. The paper uses zkSTARKs as the selective disclosure layer.

---

## Receipt
A compact proof that a commitment or entry exists in the anchored state. The paper uses receipts to prove existence before proving a predicate over hidden data.

---

## Selective disclosure
The ability to prove only the required fact, while keeping the underlying private data hidden. In the paper, zkSTARKs demonstrate properties of committed data without revealing values.

---

## Settlement accumulator
An accumulator that tracks pending and completed settlement actions, including state transitions, proofs, and finalization records.

---

## Shielded coordination pool
A private coordination environment where swap intents, matching, and settlement routing occur without revealing the transaction graph publicly.

---

## Proof verifier
The on-chain contract layer that checks proofs, roots, nullifiers, and settlement predicates inside a correspondent chain.

---

## strkBTC
A Starknet-side Bitcoin representation used in the BCYX-SWAP proof-of-concept as the correspondent settlement asset or settlement mirror.

---

## Temporal integrity
The property that a record can be proven to have existed at a specific time. The paper highlights Bitcoin-anchored timestamps as a way to prove when data existed.

---

## Trust minimization
Reducing dependency on trusted intermediaries by using anchored proofs and public verification. The paper frames Bitcoin anchoring as a trust-minimizing property.

---

## zk proof
A zero-knowledge proof that demonstrates a statement is true without revealing the private witness used to prove it.

---

## zkSTARK
A transparent zero-knowledge proof system used in the BCYX architecture to prove predicates over hidden data. The paper describes zkSTARKs as the selective disclosure layer.

---

## Zero-knowledge
A privacy property where the verifier learns only the truth of the statement, not the hidden inputs. The paper explicitly uses zkSTARKs to preserve this property.

---

## Private witness
The secret input to a zero-knowledge proof. This may include amounts, identities, routes, preimages, or hidden policy data.

---

## Public input
The values visible to the verifier, such as roots, nullifiers, e to the verifier, such as roots, nullifiers, thresholds, thresholds, or anchor identifiers.

---

## Settlement finality
The point at which a BCYX operation is considered complete on the correspondent chains and no longer reversible through the coordination layer.

---

## Rollback
The fallback path if a proof fails, a timeout occurs, or correspondent-chain settlement does not complete.

---

## Rollup-style aggregation
A general comparison point for proof compression systems. BCYX uses a similar compression idea, but for private coordination rather than generic execution.

---

## Authority provenance
The ability to prove which authority authorized data without revealing real-world identity. The paper highlights URL/ADI binding as a way to prove “who authorized the data.”

---

## Private authorization
A policy model where a participant proves they satisfy a rule without revealing the full identity or all underlying values.

---

## Batch efficiency
The ability for a single anchor or receipt to validate many entries. The paper reports this as one of the main advantages of hierarchical proofs.

---

## Settlement root
The compressed public summary of the current settlement state.

---

## Coordination state
Temporary BCYX state used during matching, proof generation, and settlement orchestration.

---

BCYX uses these terms to describe a system that is:
- private in coordination,
- public in verification,
- ephemeral in settlement state,
- and atomic in execution.
