# Cryptography and specialized proof implementation review

Status: **Research input, not dependency approval.** Inspected on 2026-09-25.

Purpose: align the [financial workload draft](../requirements/financial-workloads-and-scope.md) with concrete reusable Rust implementations rather than generic PQ/ZK assumptions. No upstream code has been copied into this repository. No foreign build scripts, tests, or benchmarks were executed.

## Evidence and version boundaries

The observations below come from these independent source snapshots. Source paths are relative to the corresponding snapshot. The snapshots are not asserted to constitute a mutually compatible release set or the software running on a live network. Before adoption, resolve the consuming application's exact lockfile, package versions, features, circuit artifacts, and verifier configuration; reviewing the latest library branch does not establish those properties for a deployed binary.

| ID | Source role and revision | Evidence paths |
| --- | --- | --- |
| E1 | [Chain integration](https://github.com/Quantus-Network/chain/tree/482c5b9e02bec0adc70797eade0a67f60baf2619) at `482c5b9e02bec0adc70797eade0a67f60baf2619` | [runtime/src/lib.rs](https://github.com/Quantus-Network/chain/blob/482c5b9e02bec0adc70797eade0a67f60baf2619/runtime/src/lib.rs); [primitives/dilithium-crypto/src/types.rs](https://github.com/Quantus-Network/chain/blob/482c5b9e02bec0adc70797eade0a67f60baf2619/primitives/dilithium-crypto/src/types.rs); [primitives/dilithium-crypto/src/scheme_macro.rs](https://github.com/Quantus-Network/chain/blob/482c5b9e02bec0adc70797eade0a67f60baf2619/primitives/dilithium-crypto/src/scheme_macro.rs); [primitives/dilithium-crypto/src/traits.rs](https://github.com/Quantus-Network/chain/blob/482c5b9e02bec0adc70797eade0a67f60baf2619/primitives/dilithium-crypto/src/traits.rs); [pallets/wormhole/src/lib.rs](https://github.com/Quantus-Network/chain/blob/482c5b9e02bec0adc70797eade0a67f60baf2619/pallets/wormhole/src/lib.rs); [Cargo.toml](https://github.com/Quantus-Network/chain/blob/482c5b9e02bec0adc70797eade0a67f60baf2619/Cargo.toml) |
| E2 | [Signature library](https://github.com/Quantus-Network/qp-rusty-crystals/tree/3b1464f751d536aba023a53df2a4f4533ab94f62) at `3b1464f751d536aba023a53df2a4f4533ab94f62` | [dilithium/src/ml_dsa_65.rs](https://github.com/Quantus-Network/qp-rusty-crystals/blob/3b1464f751d536aba023a53df2a4f4533ab94f62/dilithium/src/ml_dsa_65.rs); [dilithium/src/ml_dsa_87.rs](https://github.com/Quantus-Network/qp-rusty-crystals/blob/3b1464f751d536aba023a53df2a4f4533ab94f62/dilithium/src/ml_dsa_87.rs); [dilithium/src/params.rs](https://github.com/Quantus-Network/qp-rusty-crystals/blob/3b1464f751d536aba023a53df2a4f4533ab94f62/dilithium/src/params.rs); [dilithium/Cargo.toml](https://github.com/Quantus-Network/qp-rusty-crystals/blob/3b1464f751d536aba023a53df2a4f4533ab94f62/dilithium/Cargo.toml); [dilithium/LICENSE](https://github.com/Quantus-Network/qp-rusty-crystals/blob/3b1464f751d536aba023a53df2a4f4533ab94f62/dilithium/LICENSE) |
| E3 | [Hash implementation](https://github.com/Quantus-Network/qp-poseidon/tree/0117e2ca7f46a07d86a43385b5079ac6aaadaadc) at `0117e2ca7f46a07d86a43385b5079ac6aaadaadc` | [src/lib.rs](https://github.com/Quantus-Network/qp-poseidon/blob/0117e2ca7f46a07d86a43385b5079ac6aaadaadc/src/lib.rs); [src/poseidon2.rs](https://github.com/Quantus-Network/qp-poseidon/blob/0117e2ca7f46a07d86a43385b5079ac6aaadaadc/src/poseidon2.rs); [src/serialization.rs](https://github.com/Quantus-Network/qp-poseidon/blob/0117e2ca7f46a07d86a43385b5079ac6aaadaadc/src/serialization.rs); [Cargo.toml](https://github.com/Quantus-Network/qp-poseidon/blob/0117e2ca7f46a07d86a43385b5079ac6aaadaadc/Cargo.toml) |
| E4 | [Specialized circuits](https://github.com/Quantus-Network/qp-zk-circuits/tree/e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99) at `e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99` | [common/src/circuit.rs](https://github.com/Quantus-Network/qp-zk-circuits/blob/e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99/common/src/circuit.rs); [wormhole/circuit/src/circuit.rs](https://github.com/Quantus-Network/qp-zk-circuits/blob/e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99/wormhole/circuit/src/circuit.rs); [wormhole/circuit/src/zk_merkle_proof.rs](https://github.com/Quantus-Network/qp-zk-circuits/blob/e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99/wormhole/circuit/src/zk_merkle_proof.rs); [ownership/circuit/src/circuit.rs](https://github.com/Quantus-Network/qp-zk-circuits/blob/e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99/ownership/circuit/src/circuit.rs); [wormhole/aggregator/src/](https://github.com/Quantus-Network/qp-zk-circuits/tree/e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99/wormhole/aggregator/src); [LICENSE](https://github.com/Quantus-Network/qp-zk-circuits/blob/e77ba5c70da7dc7b3d3da82c84e0fb8e20b40c99/LICENSE) |
| E5 | [Recursive proof engine](https://github.com/Quantus-Network/qp-plonky2/tree/6d2ccc298368e11df34a9211b72424a655bcd799) at `6d2ccc298368e11df34a9211b72424a655bcd799` | [core/src/circuit_config.rs](https://github.com/Quantus-Network/qp-plonky2/blob/6d2ccc298368e11df34a9211b72424a655bcd799/core/src/circuit_config.rs); [plonky2/src/](https://github.com/Quantus-Network/qp-plonky2/tree/6d2ccc298368e11df34a9211b72424a655bcd799/plonky2/src); [Cargo.toml](https://github.com/Quantus-Network/qp-plonky2/blob/6d2ccc298368e11df34a9211b72424a655bcd799/Cargo.toml) |
| E6 | [Peer identity](https://github.com/Quantus-Network/qp-libp2p-identity/tree/b9a7f46426efa2cf9b2ba20b95851ca18f361c95) at `b9a7f46426efa2cf9b2ba20b95851ca18f361c95` | [src/keypair.rs](https://github.com/Quantus-Network/qp-libp2p-identity/blob/b9a7f46426efa2cf9b2ba20b95851ca18f361c95/src/keypair.rs) |
| E7 | [Transport](https://github.com/Quantus-Network/qp-libp2p-noise/tree/901f09f30b32f910395270bba3a566191dc2f61f) at `901f09f30b32f910395270bba3a566191dc2f61f` | [src/protocol.rs](https://github.com/Quantus-Network/qp-libp2p-noise/blob/901f09f30b32f910395270bba3a566191dc2f61f/src/protocol.rs) |

Each source-role link identifies the repository at the inspected commit; each evidence link pins the same commit. These references establish traceable provenance without implying dependency adoption or original authorship. Applicable notices must accompany any later dependency import.

## 1. Transaction authorization and identity

**Observed in E1:** the runtime signature type has ML-DSA-65 and ML-DSA-87 alternatives. Each wraps raw signature bytes together with the public key. Verification checks the derived account identifier against the claimed signer before verifying the signature. The account is a 32-byte hash of the public key. Thus a short account identifier does not remove the key from the authorization payload.

**Observed in E2 / standardized size reference:** the candidate signature family is ML-DSA, not a reason to assume Falcon or elliptic-curve signatures. FIPS 204 specifies 3,309-byte signatures and 1,952-byte public keys for ML-DSA-65, and 4,627-byte signatures and 2,592-byte public keys for ML-DSA-87. [N1]

**Consequence:** model both transaction variants, include inline keys and scheme/framing overhead, and evaluate security-level policy separately. Availability of both variants does not decide which RinjaniChain should accept or which to use for validator identities/votes. Key derivation, signing context, chain/role domain separation, rotation, malformed encodings, and implementation side channels still require review. Test-vector support or code comments are not an independent security certification.

**Reuse alternatives:** adapt the inspected implementation subject to its license and integration requirements; use another implementation of the same standard with compatibility tests; or revisit the signature family through an explicit decision. Standardized parameters provide a stronger starting point than inventing signatures, but do not prove a specific implementation secure.

## 2. Hashing and field encoding

**Observed in E3:** Poseidon2 over the Goldilocks field, with width 12, rate 8, capacity 4, four output field elements serialized to 32 bytes, eight external rounds, and 22 internal rounds. The byte-input path uses an injective encoding, and digest decoding rejects non-canonical field limbs. The implementation also exposes a 64-byte squeeze operation; this is not by itself evidence of increased cryptographic security.

**Consequence:** matching the algorithm name is insufficient for compatibility. Pin round constants, field representation, byte-to-field encoding, padding, domain separation, output serialization, and the distinction between hashing bytes and hashing already decoded field elements. Native and circuit hashes need common known-answer vectors before integration. Account identifiers, commitments, nullifiers, and Merkle nodes need explicit usage domains.

**PQ boundary:** a 32-byte output is not a blanket 256-bit security guarantee, and a larger squeeze does not automatically increase sponge capacity or security. Required preimage/collision strength and the selected permutation's cryptanalysis must be assessed independently; this review assigns no quantum security level to this hash profile.

## 3. What the proof stack actually provides

**Observed in E4/E5:** a Plonky2-derived recursive stack over Goldilocks with extension degree 2 and FRI configuration. The standard recursion configuration declares `security_bits = 100`, rate bits 3, cap height 4, 28 query rounds, and 16 proof-of-work bits. The last value concerns proof generation/transcript grinding, not blockchain mining or a choice of consensus admission mechanism.

The specialized configuration functions distinguish:

| Layer | Observed configuration | Integration consequence |
| --- | --- | --- |
| Transfer-membership leaf | Standard recursion, `zero_knowledge = false` | Treat the leaf proof and witness workflow as privacy-sensitive; a public or outsourced leaf prover/aggregator is not automatically safe |
| Private batch | ZK recursion with 135 wires and 60 routed wires; comments describe row blinding | Model a private proving boundary, witness access, resource use, and leakage from public inputs |
| Public batch | Standard recursion, `zero_knowledge = false` | Aggregates already private proofs and exposes their public inputs; does not hide those inputs |
| Ownership | ZK recursion | Proves knowledge for a derived address and binds a claim account; does not prove deposit inclusion or single-use eligibility |

The leaf transfer circuit connects address-secret relations, transfer-tree membership, nullifier construction, exit accounts, and block-header data. The ownership circuit is deliberately narrower: it has no inclusion proof, block header, or nullifier. In E1, the runtime checks referenced block data and stateful nullifier use around proof verification. These are separate obligations; the cryptographic proof is not the entire financial protocol.

**Financial encoding constraint:** E4's `wormhole/circuit/src/zk_merkle_proof.rs` represents deposit amounts as quantized `u32` values. E3's conversion helper divides base-unit amounts by `10^10`; E1 uses the corresponding scale factor for payouts. This is application-specific, not a universal financial amount representation. Reuse analysis must cover precision, maximum amounts, rounding/remainders, fees, overflow, and conservation across conversion boundaries. RinjaniChain has not adopted those units or precision limits.

**Not established by this evidence:** a general-purpose state-transition proof, signature aggregation for validator votes, atomic-exchange semantics, availability of transaction/state data, consensus safety/finality, or production performance on mobile devices. Reusing the proof engine would still require new statements and circuits for those applications. Verifier artifact identity, public-input schemas, proof-size limits, replay handling, and chain binding must be versioned and authenticated.

**Security interpretation:** preserve the distinction between declared configuration values and independently justified soundness/zero-knowledge guarantees. The value 100 is not a demonstrated quantum security level. Hash-based commitments and FRI are relevant candidates for a PQ architecture, but the complete parameters, Fiat–Shamir/transcript assumptions, recursive composition, and implementation need dedicated assessment. Do not describe all layers as zero knowledge or inherit the signature scheme's security category for the proof system.

**Resource interpretation:** existing profiling code and comments can guide future measurements but are not measurements for RinjaniChain. Keep proof size, end-to-end proving time, peak memory, and verification costs unresolved until reproduced on specified hardware with pinned artifacts. Do not claim one-second proof production or lightweight proving from source inspection alone.

## 4. Transport is a separate dependency

E6 includes ML-DSA-87 peer identity support. E7 contains an ML-KEM-768 path and an X25519 classical path. This establishes available code paths, not the active negotiation policy of a deployed node. Selection, authentication, downgrade resistance, feature flags, handshake costs, and peer identity binding need inspection in the consuming network stack before making end-to-end PQ claims. Reusing transaction signatures does not configure the network automatically.

No randomness beacon, PQ verifiable leader-selection mechanism, or BFT quorum-certificate construction is established by this review. Those remain consensus research questions.

## 5. Reuse feasibility and license boundaries

| Component | Declared license in inspected snapshot | Research consequence |
| --- | --- | --- |
| Signature crate E2 | GPL-3.0 | Adoption requires a licensing decision; do not present it as an MIT dependency merely because this repository currently has an MIT license |
| Specialized circuits E4 | MIT | Retain required notices if code is incorporated; adapt circuit statements and runtime checks together |
| Recursive engine E5 | MIT OR Apache-2.0 | Select and track applicable terms and transitive dependencies when pinning a reuse set |
| Hash crate E3 | MIT-0 | Track the selected version, implementation parameters, and dependency licenses |

This is an inventory of source declarations, not a legal compatibility determination. The current change is original research documentation, not a vendored fork. The license inventory is incomplete for a full application and must be extended before importing code. Repository branding can be absent from architectural prose; required notices and provenance cannot be stripped from reused code.

**Assessment:** reuse is technically plausible at the signature/hash/proof-engine and specialized-gadget levels. It is not yet demonstrated as a compatible, audited dependency set for this project. The practical next step is a pinned dependency/feature/artifact comparison and license review, followed by explicitly authorized compatibility and resource measurements. The current work does not authorize implementation or select a proof-based consensus architecture.

## Reference

**[N1]** NIST, [FIPS 204](https://csrc.nist.gov/pubs/fips/204/final), Table 2. Used for ML-DSA parameter sizes only. The code observations are tied to E1–E7 above and were not inferred from marketing descriptions.
