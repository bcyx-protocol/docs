Below is a practical roadmap for the **BCYX-SWAP proof-of-concept**, assuming the documentation repo is now the source of truth and the first implementation goal is a **private BTC ↔ strkBTC swap coordination flow**.

The implementation should stay aligned with the paper’s core properties: **batch efficiency**, **trust minimization**, **authority provenance**, and **selective disclosure**. The paper’s architecture also makes the key design choice clear: prove existence with anchored receipts, then prove predicates over committed data with zero knowledge.  

## Roadmap

### Phase 1 — Lock the PoC scope

**Goal:** reduce the problem to one clean swap path.

**Decisions to freeze**

* Asset pair: BTC ↔ strkBTC
* Settlement model: ephemeral coordination, no long-lived BCYX custody
* Finality model: correspondent-chain finalization
* Privacy model: commitments + selective disclosure
* Verification model: Cairo verifier on Starknet
* Proof model: recursive zk-STARK / batch aggregation later, single swap first

**Deliverables**

* one-page PoC spec
* swap state machine
* explicit threat model
* success metrics

**Exit criteria**

* everyone agrees what “swap success” means
* everyone agrees which chain owns final settlement
* no ambiguity about what stays private vs public

---

### Phase 2 — Define the swap state machine

**Goal:** make the swap executable on paper before code.

**States**

* `IntentCreated`
* `Committed`
* `Matched`
* `LockedOnBTC`
* `ProofPending`
* `ProofVerified`
* `ReleasedOnStarknet`
* `Finalized`
* `Refundable`
* `Refunded`
* `Expired`

**Must define**

* who can move between states
* timeout rules
* refund rules
* nullifier behavior
* what data is public at each state

**Deliverables**

* state diagram
* transition table
* error/rollback table

**Exit criteria**

* every state has a deterministic transition
* every failure path ends in refund or safe expiration

---

### Phase 3 — Build the core data model

**Goal:** standardize the objects that flow through BCYX.

**Core objects**

* `SwapIntent`
* `Commitment`
* `Nullifier`
* `SettlementProof`
* `AccumulatorRoot`
* `DisclosurePolicy`
* `CorrespondentChainReceipt`

**Fields to define**

* asset in/out
* amount
* timeout
* recipient policy
* chain identifiers
* batch identifier
* disclosure scope
* proof references

**Deliverables**

* Rust/TypeScript/Cairo-compatible schema draft
* serialization format
* canonical hashing rules

**Exit criteria**

* the same swap intent hashes identically everywhere
* commitments and nullifiers are reproducible

---

### Phase 4 — Implement the commitment layer

**Goal:** hide swap details while keeping them provable.

**Functions**

* create commitment from swap intent
* generate nullifier from spent commitment
* verify commitment inclusion
* verify nullifier uniqueness

**Deliverables**

* commitment library
* nullifier library
* test vectors
* local Merkle/accumulator prototype

**Why this matters**
This is the base privacy layer. The paper’s model is exactly this pattern: store commitments, prove existence through receipts, then prove a hidden predicate over the committed data. 

**Exit criteria**

* commitments can be created and verified locally
* spent commitments cannot be reused
* the public state does not reveal swap details

---

### Phase 5 — Build the shielded coordination pool

**Goal:** create the private matching and routing layer.

**Responsibilities**

* accept private swap intents
* keep them off public order flow
* match compatible intents
* batch pending swaps
* route them to proof generation

**Matching logic**

* asset compatibility
* amount compatibility
* timeout compatibility
* disclosure policy compatibility
* correspondent-chain compatibility

**Deliverables**

* private matching service
* encrypted queue or hidden in-memory pool
* matching rules
* audit logs that reveal only proofs and roots, not hidden order flow

**Exit criteria**

* two swap intents can be matched without public exposure
* matching metadata is not leaked on-chain

---

### Phase 6 — Implement the proof layer

**Goal:** prove that a matched swap is valid without exposing the witness.

**Proof statements to support first**

* commitment exists
* commitment has not been spent
* swap amounts are consistent
* lock condition is satisfied
* timeout condition is valid
* selective disclosure rule is satisfied

**Proof strategy**

* start with a single-swap proof
* keep witness minimal
* keep public inputs fixed and small
* add recursion later

**Deliverables**

* proof circuit / Cairo program draft
* proving script
* verifier input format
* test vectors

**Exit criteria**

* one swap proof can be generated locally
* the verifier accepts valid proofs and rejects invalid ones

---

### Phase 7 — Add batch proofs and accumulators

**Goal:** compress multiple swap proofs into one verification path.

This is where BCYX starts to resemble the paper’s batch-efficiency model: hierarchical receipts, compact anchors, and compressed verification.  

**What to batch**

* multiple commitments
* multiple nullifiers
* multiple swap proofs
* multiple disclosure predicates

**Accumulator jobs**

* commit root update
* nullifier root update
* proof root aggregation
* settlement root aggregation

**Deliverables**

* accumulator module
* batch proof aggregator
* batch test fixtures
* proof compression benchmarks

**Exit criteria**

* 2–10 swaps can be verified as one batch
* verification cost is lower than per-swap verification

---

### Phase 8 — Implement Starknet verification

**Goal:** make Starknet the public verification boundary.

**Contract responsibilities**

* store verified roots
* verify receipt/proof objects
* reject reused nullifiers
* emit settlement events
* record finalization status

**Minimal contract set**

* `BCYXVerifier`
* `BCYXSettlementRegistry`
* `BCYXNullifierRegistry`

**Deliverables**

* Cairo contract skeletons
* verifier integration
* event schema
* testnet deployment plan

**Exit criteria**

* Starknet can validate a proof and record the outcome
* the contract state matches the off-chain accumulator state

---

### Phase 9 — Connect correspondent chains

**Goal:** ensure the swap actually settles on BTC and strkBTC side domains.

**BTC side**

* lock BTC under the agreed condition
* prove lock inclusion
* support refund/timeout path

**strkBTC side**

* mint or release after proof verification
* support burn/release on exit
* mirror the final swap state

**Deliverables**

* BTC-side lock adapter
* strkBTC-side settlement adapter
* correspondent-chain receipt format
* rollback/refund scripts

**Exit criteria**

* the swap is final only when both correspondent sides agree
* refund path works if one side fails

---

### Phase 10 — Selective disclosure rules

**Goal:** add policy-aware visibility without breaking privacy.

**First disclosure rules**

* threshold proof
* whitelist membership proof
* deadline compliance proof
* approved-authority proof
* “swap valid” proof without revealing details

This follows the paper’s pattern: prove a predicate over the committed value without exposing the value itself. 

**Deliverables**

* policy DSL or config format
* proof templates for each predicate
* disclosure test cases

**Exit criteria**

* users can prove policy compliance without full data exposure

---

### Phase 11 — Testing and benchmarks

**Goal:** prove the PoC is real, not just conceptual.

**Test categories**

* unit tests
* integration tests
* end-to-end tests
* failure path tests
* timeout/refund tests
* replay/nullifier tests
* batch proof tests

**Benchmarks**

* proof generation time
* verifier time
* batch size vs cost
* swap latency
* storage growth
* refund time

**Deliverables**

* benchmark report
* reproducible scripts
* public test vectors

**Exit criteria**

* results are reproducible
* performance numbers are documented
* known limitations are explicit

---

### Phase 12 — Security review and hardening

**Goal:** make the PoC credible enough for grant follow-up or pilot extension.

**Review items**

* commitment binding
* nullifier uniqueness
* accumulator correctness
* proof soundness
* correspondent-chain assumptions
* refund safety
* privacy leakage from metadata

**Deliverables**

* threat model doc
* security checklist
* audit notes
* hardening backlog

**Exit criteria**

* the PoC has a clear trust model
* open risks are documented, not hidden

---

## Suggested build order

If you want the shortest path to something demoable, build in this order:

1. swap state machine
2. commitment + nullifier layer
3. private matching pool
4. single-swap proof
5. Starknet verifier
6. BTC/strkBTC settlement adapters
7. batch proofs and accumulators
8. selective disclosure rules
9. refund/rollback
10. benchmarks and docs

That order gets you to a working PoC fastest while keeping the architecture aligned with the paper’s two-layer pattern of anchored receipts plus zero-knowledge predicate proofs. 

## Milestone target

A realistic first milestone is a demo where:

* one BTC swap intent is committed,
* one strkBTC counterpart is matched,
* one proof is generated,
* Starknet verifies it,
* and both sides settle or refund safely.

That is enough to prove the BCYX-SWAP concept before expanding into batching, selective disclosure variants, or broader correspondent-chain support.
