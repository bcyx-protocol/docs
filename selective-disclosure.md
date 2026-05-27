# BCYX Selective Disclosure

> Proof-based privacy for swaps, settlement, oracle attestations, and compliance-sensitive coordination.

---

## Overview

BCYX uses selective disclosure to prove that a condition is true without revealing the underlying private data. This is the layer that turns BCYX from a private coordination system into a verifiable privacy system.

The paper underpinning BCYX introduces the core idea clearly: a receipt proves that a commitment exists, and a zkSTARK proves a predicate over that committed value without revealing the value itself. It also shows that the URL/ADI structure can bind a statement to an authority without exposing real-world identity. 
In BCYX, selective disclosure is used for:
- confidential swap validation,
- private settlement predicates,
- proof of eligibility,
- policy-constrained coordination,
- and oracle-style attestations.

---

## What selective disclosure means in BCYX

Selective disclosure means a verifier learns only the minimum statement needed.

Examples:
- “This swap is valid.”
- “This participant satisfies policy X.”
- “This note is unspent.”
- “This transaction occurred before deadline T.”
- “This treasury movement is authorized.”

The verifier does **not** learn:
- balances,
- counterparty identity,
- routing path,
- internal execution state,
- or the full underlying commitment preimage.

The paper’s model is: commit privately, prove existence publicly, then prove a property over the commitment using zkSTARKs. BCYX keeps that structure, but extends it into a broader coordination layer.

---

## Core privacy model

BCYX selective disclosure is built from three ideas:

1. **Commitment**
   A private value is committed as a hash or similar commitment.

2. **Receipt / inclusion proof**
   The system proves the commitment exists in the canonical anchored state.

3. **Predicate proof**
   A zk proof shows the committed value satisfies a rule, without exposing the value.

This is the architecture described in the paper’s “commitment-based privacy” section: store a commitment, prove it exists, then prove a property over the hidden witness.

---

## What gets hidden

BCYX aims to hide:

- exact value
- sender identity
- receiver identity
- counterparties
- execution routing
- treasury allocation
- swap path
- internal matching logic
- private oracle inputs

What stays visible:

- proof validity
- root hashes
- nullifiers
- policy outcomes
- verification status
- optional compliance disclosures

This mirrors the paper’s claim that selective disclosure should let systems verify “what” is true without learning the hidden inputs, while preserving authority provenance.

---

## Selective disclosure primitives

BCYX uses the following primitives:

### Commitment
A private value is committed before being proved.

### Receipt
A compact membership proof shows the commitment is anchored.

### Predicate proof
A zkSTARK proves a condition over the hidden value.

### Authority binding
If the data comes from an authority, the authority is bound through the structure that produced the commitment.

### Nullifier
A public one-time marker prevents double use of the same hidden note or execution object.

The paper’s architecture explicitly combines receipt verification with STARK predicate proofs, and its examples show threshold, range, membership, and multi-authority checks.

---

## Disclosure modes

BCYX can support several disclosure modes:

### 1. Full private mode
Nothing sensitive is revealed except proof validity.

### 2. Threshold mode
Only a threshold result is revealed.

Example:
- “amount > 1 BTC”
- “score >= 700”
- “swap before deadline”

### 3. Membership mode
Only membership in an approved set is revealed.

Example:
- approved treasury
- whitelisted counterparty
- sanctioned-free jurisdiction

### 4. Authority mode
The verifier learns which authority signed or authorized the data, while not learning the real identity if pseudonymous.

The paper highlights this as “authority provenance”: verifiers can learn who authorized a datum without identity disclosure.

### 5. Conditional disclosure
Some metadata may be revealed only under policy or by request.

Example:
- disclose to a regulator,
- hide from the public,
- reveal only a timestamp or jurisdictional tag.

---

## Architecture flow

```mermaid
flowchart TD

A[Private Value or Event]
--> B[Commitment Creation]

B --> C[Anchor / Receipt Proof]

C --> D[Private Predicate Definition]

D --> E[zk Proof Generation]

E --> F[Public Verification]

F --> G{Predicate Satisfied?}

G -- Yes --> H[Selective Disclosure Result]
G -- No --> I[Reject]
````

---

## How it works in BCYX-SWAP

For a BTC ↔ strkBTC PoC, selective disclosure is what allows the swap to be validated without exposing the full coordination graph.

### Example flow

1. BTC is locked into a commitment-based settlement condition.
2. The swap intent is recorded privately.
3. A receipt proves the commitment exists in the anchored state.
4. A zk proof proves the swap satisfies the agreed rules.
5. The verifier learns only that the swap is valid.
6. The strkBTC side is released or minted.
7. The hidden details remain private.

This is consistent with the paper’s combined verification model: the verifier sees the receipt, the anchor, and the public threshold or predicate, but not the private witness.  

---

## Supported predicate types

BCYX can start with a small set of useful predicates:

* amount greater than threshold
* amount within range
* commitment exists in accumulator
* note not previously spent
* authority belongs to approved domain
* timestamp before or after a deadline
* counterparty belongs to approved set
* swap completed under valid policy

The paper’s examples directly include threshold, range, membership, and multi-authority proofs, which are good starting points for BCYX’s selective-disclosure policy engine.  

---

## Selective disclosure in oracle and settlement use cases

The original paper frames this as useful for:

* private credit scoring,
* front-running-resistant price feeds,
* age verification,
* and multi-authority credentials.  

BCYX generalizes the same pattern to:

* private swaps,
* confidential treasury routing,
* settlement authorization,
* oracle attestations,
* and institutional capital flows.

That makes selective disclosure a general-purpose coordination primitive, not just an oracle feature.

---

## Verifier contract behavior

The Starknet verifier should only accept:

* valid receipt
* valid anchor root
* valid predicate proof
* valid nullifier state
* valid settlement root

It should reject:

* reused nullifiers
* stale roots
* malformed predicates
* inconsistent commitments
* proofs that do not match the public state

The paper’s Cairo implementation already models this structure with receipt verification plus private oracle predicates.  

---

## Security assumptions

Selective disclosure depends on standard assumptions:

* collision-resistant commitments
* Merkle / receipt soundness
* STARK soundness
* STARK zero-knowledge
* correct authority binding
* correct public input construction

The paper states explicitly that its security is not based on novel cryptography, but on composition of standard primitives. BCYX inherits that approach.  

---

## Limitations

Selective disclosure does not solve everything.

It does not automatically provide:

* data recovery,
* data availability,
* user-intuitive UX,
* or trustless anchoring by itself.

The paper notes limitations around data availability, anchor trust, and commitment scheme choice. BCYX should treat these as design constraints, not afterthoughts. 

---

## PoC implementation notes

For the first BCYX proof-of-concept, selective disclosure should be implemented with:

* a commitment format
* a receipt verification path
* a predicate library
* a nullifier registry
* a Cairo verifier
* a small set of policy predicates
* a minimal swap flow between BTC and strkBTC

Recommended initial predicates:

* `amount > threshold`
* `note is unspent`
* `lock happened before deadline`
* `authority is approved`
* `swap belongs to valid batch`

This is enough to demonstrate the real value of selective disclosure without overbuilding the first prototype.

---

## Summary

Selective disclosure is the privacy layer that lets BCYX prove correctness without exposing private state.

It is the difference between:

* “show me your whole transaction”
  and
* “show me the one fact I need to verify.”

That is what makes BCYX suitable for:

* confidential swaps,
* private settlement,
* compliant privacy,
* and scalable cross-chain coordination.
