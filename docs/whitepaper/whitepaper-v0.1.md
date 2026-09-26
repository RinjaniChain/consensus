# RinjaniChain Whitepaper

**Version 0.1 · 26 September 2026 · Research draft**

## Abstract

RinjaniChain is a proposed permissionless Layer 1 blockchain for payments and atomic asset exchange, with an eventual Rust implementation. Its research objective is to combine fast deterministic finality, meaningful participation on modest hardware, resistance to concentrated control, and a cryptographic architecture designed for post-quantum adversaries.

These objectives create a difficult resource and security problem. Large post-quantum authorization payloads consume bandwidth before execution or agreement begins. Short block intervals leave less time for geographically distributed participants. Committees and execution proofs may reduce some participants' work while introducing selection risk, additional cryptographic assumptions, or dependence on specialized operators.

This whitepaper presents the requirements, analytical findings, and candidate design space developed in the first research phase. It does not specify a selected consensus algorithm or claim a working network. Payments and atomic exchange are the agreed starting scope; admission rules, fault thresholds, timing guarantees, proof duties, and economic parameters remain open. The next milestone is an explicit threat model and a comparison of architectures under the same financial and participation requirements.

## 1. Purpose and evidence status

A financial blockchain must establish more than an agreed sequence of messages. Participants need to know whether an authorized operation succeeded, whether the result is final, and whether they can verify and recover the relevant state without depending indefinitely on a particular service provider.

RinjaniChain approaches these questions as a requirements problem before committing to a protocol. The project direction includes permissionless participation, approximately one-second timing, deterministic finality, low participation costs, decentralization, and post-quantum security. These are objectives, not demonstrated properties of an implementation.

The research distinguishes four kinds of statements:

| Status | Meaning in this whitepaper |
| --- | --- |
| Agreed scope | Payments and atomic asset exchange belong in the initial financial scope. |
| Research direction | A desired property whose precise guarantee and feasibility still need definition. |
| Source finding or analysis | An observation tied to inspected source snapshots, or a calculation under stated assumptions. |
| Proposal or open question | A possible requirement, architecture, parameter, or tradeoff awaiting explicit agreement. |

Phase 1 produced reviewed documentation, including a consolidated requirements draft. Technical review and merging those documents do not approve the unresolved protocol choices. This whitepaper synthesizes that work; it adds no accepted consensus decision and makes no new implementation, audit, or benchmark claim. The detailed requirement identifiers and open questions remain in the [consolidated draft](../requirements/phase-1-requirements-draft.md).

## 2. Financial scope and observable outcomes

### Payments and atomic exchange

The baseline covers payments and atomic exchange of assets. A working exchange model uses two parties and two assets on the same chain: the agreed exchange effects should occur together or not occur. This model is a research fixture; precise consent, cancellation, expiry, fee, replay, and conflict rules remain to be agreed. In particular, “both or neither” cannot silently decide whether a failed request may incur a fee or consume a replay-protection value.

Payment correctness requires explicit authorization, asset rules, balance arithmetic, and duplicate handling. Exchange adds mutual consent and consistency across all affected balances. A consensus protocol can agree on an invalid financial result if the validity contract is incomplete. Financial semantics and agreement therefore need separate specifications with an explicit connection between them.

The workload study includes independent payments, issued-asset transfers, batches, atomic exchanges, shared-state contention, and specialized proof claims. General-purpose contracts and cross-chain operations have no agreed baseline commitment. On-chain settlement describes the chain's successful finalized result; it does not establish off-chain redemption or the status of externally backed assets. [S1]

### What a user can observe

A submitted request may be locally admitted, included, executed, finalized as an inclusion, and confirmed as a successful finalized outcome. These are distinct observations; their exact ordering depends on the eventual execution architecture.

A wallet acknowledgment does not establish inclusion. Finalized inclusion does not alone establish successful execution. A client-facing result should identify the claim it establishes and the evidence used to verify it. Retries must retain a stable relationship to the original request so that a timeout cannot be mistaken for cancellation or lead to unintended duplicate effects.

Performance reporting should retain rejected, expired, failed, dropped, pending, and unknown outcomes. Fast percentiles computed only over successful requests cannot establish reliable service for the original request cohort. [S1, S3, S4]

## 3. Safety, finality, and progress

The intended finality property is a durable, verifiable commitment: within a stated security envelope, honest observers should not finalize conflicting histories. A selected protocol will need to define the finality predicate, observer verification procedure, and evidence connecting finalized history to valid financial outcomes.

Version 0.1 does not assign a Byzantine threshold. The threshold depends on membership, influence weighting, quorum construction, adversarial capabilities, and network assumptions. A familiar threshold from another protocol cannot be transferred without its proof conditions.

The threat model must establish at least:

- What the adversary controls: voting influence, identities, keys, communication, infrastructure, transaction flow, and any specialized services.
- Whether corruption is static or adaptive, how quickly keys can be compromised, and which failures are correlated.
- Which delivery, connectivity, clock, and eventual network-recovery assumptions support progress.
- How membership transitions, durable signing state, crashes, and returning validators preserve the guarantee.
- What cryptographic assumptions apply during operation and throughout historical verification.

Safety and progress are separate obligations. During a partition or unavailable-data event, a candidate may have to stop making progress to preserve its checks. Recovery cannot silently replace the declared guarantee with a trusted service or administrative reversal of finality.

Chain progress is also distinct from transaction service. A chain that keeps finalizing blocks may still omit a particular eligible request indefinitely. Inclusion policy, overload behavior, transaction conflicts, and censorship resistance therefore need their own assumptions and criteria. Eventual, bounded, and best-effort inclusion remain alternatives for explicit evaluation. [S3, S6]

## 4. Permissionless participation and concentrated control

Permissionless participation needs an objective lifecycle: entry, activation, assigned duties, exit, and release from residual obligations. Hardware access alone is insufficient if participation also depends on discretionary admission, an exclusive prover, a hosting service, or unaffordable economic requirements.

Token stake is a candidate economic admission mechanism for resisting inexpensive identity multiplication. It is not yet the selected mechanism. A stake-based candidate must explain resource acquisition, influence allocation, delegation if any, key splitting, withdrawal, historical attacks, and incentives. Version 0.1 specifies no token supply, reward schedule, slashing rule, minimum stake, or governance mechanism.

The project aims to keep protocol-assigned influence from increasing directly with computation at a fixed qualifying resource. This still requires analysis of grinding, proposal races, and unequal completion rates. Faster operators may gain effective influence through better availability or lower costs even when CPU power is absent from the formal allocation rule.

### Committees and proposer rotation

Full participation and sampled duties are both research alternatives. Sampling may reduce active communication, but it introduces capture risk over repeated selections and sensitivity to adaptive corruption. Committee size must be evaluated together with the adversarial resource distribution, selection process, duty duration, and exposure horizon. A count of keys is not a count of independent operators.

A proposer cooldown can limit consecutive production by one identity. It does not establish an equivalent limit on one operator who controls several identities. Operator-level restrictions would require a credible attribution mechanism and an account of its privacy and permissionless-entry consequences. Cooldown rules also need analysis of depleted eligibility, withheld proposals, and recovery under failures.

Randomized selection introduces separate obligations for unpredictability, bias resistance, withholding, grinding, and post-quantum security. Backup proposers need an unambiguous conflict-resolution rule; simultaneous valid proposals cannot simply inherit finality from a scheduling policy.

Decentralization assessment should report effective voting and production control, delegation where applicable, geographic distribution, infrastructure concentration, verification dependence, and service dependence. Any grouping must disclose missing coverage and uncertainty. Identity counts or apparently diverse IP addresses alone do not establish independent control. [S2, S3, S5]

## 5. Logical architecture and participation roles

The following boundaries describe responsibilities to be specified, rather than a selected deployment topology:

| Subsystem | Responsibility and required boundary |
| --- | --- |
| Consensus | Order admissible proposals, manage membership transitions, and establish finality under a declared fault model. |
| Execution | Define and evaluate deterministic financial state transitions and expose success or failure precisely. |
| Networking | Disseminate proposals, evidence, and required data; account for delay, invalid traffic, and peer authentication. |
| Cryptography | Supply versioned signatures, hashes, proof verification, and any selection primitives with explicit security assumptions. |
| Storage | Preserve state, history retained by each role, and signing metadata needed for safe restart. |
| Availability and recovery | Specify which objects remain retrievable, by whom, for how long, and how retrieval is authenticated. |

The protocol must bind ordered data, execution results, state commitments, membership, and any required availability evidence. A hash commits to an object; it does not establish that the object can be retrieved or that its contents are valid.

Distinct roles include voter, proposer, executor, optional prover, full verifier, archive, light verifier, and RPC client. Roles may be combined on one machine, but their costs then accumulate. Each role needs a contract stating what it checks locally, what it verifies through proofs, what it trusts, and what it downloads and retains.

Three validity approaches remain open: local execution, verification of a sufficiently specified execution proof, and reliance on explicitly identified executor attestations. These offer different guarantees. Moving execution away from a voter reduces local work only if the replacement validity obligation is both adequate and affordable.

Weak stationary computers and mobile devices are candidate participation environments. Intermittent mobile light verification and continuous independent voting are different claims. Mobile feasibility must include energy, thermal behavior, background execution, connectivity gaps, recovery, and the cost of current authentication evidence. No device profile is currently certified for voting or proving. [S2, S6]

## 6. Post-quantum cryptographic direction

Post-quantum security is a system-wide research requirement. Transaction signatures alone cannot establish it. The inventory includes validator identity and votes, finality evidence, hashes and commitments, randomness and leader selection, proof systems, transport authentication and confidentiality where required, distribution of verifier artifacts, and migration of historical verification.

The source review found concrete Rust implementation candidates. These findings are pinned to independent snapshots in [S7]; they are not an adopted, mutually compatible release set or evidence about a deployed RinjaniChain network.

### Signatures, identities, and communication cost

The inspected authorization wrapper supports ML-DSA-65 and ML-DSA-87 and carries a public key alongside the signature. It checks the key-derived account identifier against the claimed signer. A short account identifier therefore does not eliminate public-key transmission from that transaction format.

| Candidate | Signature bytes | Public-key bytes | Combined bytes per authorization |
| --- | ---: | ---: | ---: |
| ML-DSA-65 | 3,309 | 1,952 | 5,261 |
| ML-DSA-87 | 4,627 | 2,592 | 7,219 |

These standardized size figures are used in the existing resource analysis, not as verification benchmarks or a parameter-selection decision. Scheme tags, framing, and other protocol fields add further cost. Validator key registration is a separate encoding alternative to analyze; it must not silently replace the inspected transaction format. [S1, S7]

Adoption still requires signing-domain separation, key lifecycle and compromise handling, malformed-input checks, implementation review, and an explicit security-level policy. No compact post-quantum quorum-certificate or signature-aggregation construction has been established by the inspected evidence.

### Hashing and proof parameters

The inspected hash implementation uses Poseidon2 over the Goldilocks field: width 12, rate 8, capacity 4, eight external rounds, 22 internal rounds, and four output field elements serialized into 32 bytes. Compatibility depends on exact constants, byte-to-field encoding, padding, domains, and serialization, including canonical field decoding. Matching an algorithm name is insufficient.

The recursive proof engine is Plonky2-derived, using Goldilocks and extension degree two. The reviewed configuration declares `security_bits = 100`, rate bits 3, cap height 4, 28 query rounds, and 16 proof-of-work bits. This last parameter concerns proof generation/transcript grinding; it is not consensus mining.

Neither the declared value 100 nor the hash output length establishes a quantum security level. Parameters, transcript assumptions, recursive composition, and implementation behavior require dedicated assessment. Proof-system security does not inherit the signature scheme's security category. [S7]

### Transport, selection, and migration

The inspected networking sources contain ML-DSA-87 peer identity support and both ML-KEM-768 and classical X25519 transport paths. Source availability does not establish the active negotiation policy, authentication, or downgrade resistance of a consuming application.

The review did not establish a randomness beacon, post-quantum verifiable proposer selection, or a complete BFT certificate design. These remain explicit research gaps. Migration must also address old signatures, old validator sets, account recovery, and historical commitments; replacing future signatures alone is not a historical-security policy. [S3, S7]

## 7. What specialized proofs can contribute

The reviewed circuits offer reusable components for specialized claims, with materially different statements and privacy boundaries:

| Component | Observed scope | Boundary that must remain explicit |
| --- | --- | --- |
| Transfer-membership leaf | Address-secret relations, transfer-tree membership, nullifiers, exit accounts, and block-header data | The reviewed leaf configuration is not zero knowledge. |
| Private batch | ZK recursive aggregation | Witness handling and the private proving boundary still matter. |
| Public batch | Non-ZK aggregation of already private proofs | Public inputs remain exposed. |
| Ownership proof | Knowledge associated with a derived address and a bound claim account | Does not establish inclusion, canonical history, or single-use eligibility. |

Runtime checks around the proofs authenticate referenced history and track nullifier use. Those checks are part of the financial construction; proof verification alone does not replace them.

The inspected amount representation also has application-specific constraints: a quantized `u32` amount and a conversion scale of `10^10` base units. Reuse needs an explicit treatment of remainders, precision, range, fees, overflow, and conservation. RinjaniChain has adopted neither those units nor those limits.

These components are possible starting points for specialized functionality. They are not evidence of a general state-transition proof, atomic-exchange protocol, data-availability guarantee, or compact finality certificate. A general execution-proof architecture would require additional statements, circuits, authenticated artifacts, and integration rules.

Proof-based participation also creates an economic and availability dependency: who can generate proofs, at what cost, within which deadline, and with what fallback if production fails? Smaller verification work must be evaluated against prover concentration and proof withholding. No one-second proving result or mobile proving capability has been demonstrated. [S7]

## 8. Timing, throughput, and resource feasibility

### Defining the one-second ambition

A one-second slot schedule, a one-second successful block interval, and one-second finalized success are different targets. Version 0.1 retains the ambition without choosing its meaning. Missed proposals, agreement rounds, execution, proof generation if required, data retrieval, and durable storage all affect the path to the chosen observable outcome.

The budget draft proposes exploratory ranges, including successful block-interval p95 ceilings of 1–2 seconds and finalized-success p95 ceilings of 4–10 seconds for particular envelopes. These are unaccepted planning alternatives, not service commitments or measured performance. A later evaluation must select a concrete endpoint and threshold before collecting results. [S4]

### A bandwidth exclusion already visible

Consider the existing synthetic fixture: a payment with a 256-byte body, one authorization, and an inline public key. Its size is at least:

- ML-DSA-65 variant: `256 + 3,309 + 1,952 = 5,517 bytes`.
- ML-DSA-87 variant: `256 + 4,627 + 2,592 = 7,475 bytes`.

At 1,000 such payments per second, one incoming copy requires 44.136 or 59.8 Mbit/s, respectively. This excludes network framing, replication, votes, certificates, recovery, and execution. It already exceeds the H1 weak-device fixture's 10 Mbit/s download link. The pairing is analytically incompatible under these assumptions; this calculation is neither a chain throughput measurement nor a universal device limit.

The corresponding two-authorization exchange fixture has lower bounds of 10,778 or 14,694 bytes with the same 256-byte body. Payment-only results therefore cannot establish the capacity of the agreed financial baseline. [S1, S4]

### Candidate evaluation envelopes

| Envelope | Proposed role and equipment fixture | Illustrative offered-load range |
| --- | --- | --- |
| B-E1 | H1 local full verification; 2 cores, 4 GiB host RAM, 1 GiB process budget, 64 GiB available storage; 10/2 Mbit/s physical down/up | 10–25 payments/s or 5–12 exchanges/s; mixed cases separately specified. |
| B-E2 | H4 combined-role variants; 4/8/16 cores and 8/16/32 GiB host RAM, 256 GiB available storage, 100 Mbit/s links | 100–500 payments/s or 50–250 exchanges/s. |
| B-E3 | H3 intermittent mobile light verifier with a specifically named verification claim | One verified outcome per connected minute; not a voting or chain-throughput target. |

These are proposed experiments, not certified capacity tiers. Role combinations, membership size, network case, cryptographic format, and thresholds must be fixed for any comparison. In particular, producer upload, replication, and certificate traffic may exclude a case whose transaction download fits.

Resource accounting must include simultaneous CPU, peak memory, durable I/O, retention growth, temporary storage, invalid traffic, energy, and live recovery. Average assigned duty does not establish feasibility during a peak proposer or committee duty. Reports should preserve geographic variation, complete request cohorts, pending ages, and observation uncertainty. No benchmark has established that any proposed envelope jointly satisfies its timing and resource criteria. [S2, S4, S6]

## 9. Availability, bootstrap, and recovery

A participant needs an authenticated route from its initial trust anchor to the current finalized history and membership. Genesis replay, authenticated snapshots, and checkpoints are alternatives with different costs and trust assumptions. None is selected in this version.

Long offline periods, historical-key compromise, validator exits, missing archives, stale signing state, and duplicate signer instances must be part of this analysis. A recovered machine is not safe to resume signing merely because it has downloaded recent blocks.

Finite local storage requires explicit retention and supplier duties. Pruning can reduce local cost while increasing recovery dependence on others. A state commitment or validity proof does not guarantee access to the state needed for future execution or independent recovery.

Catch-up also competes with live work. In a simple backlog model, recovery capacity must exceed ongoing data growth to close the gap. Readiness should be measured as verified current state or safe participation in the specified role, rather than downloaded bytes alone. [S2–S4]

## 10. Alternatives and evidence required

No protocol family or Rust framework has been selected. BFT-based stake systems, chain-based approaches, full participation, and sampled duties remain subjects for the next comparative phase. Malachite is a possible framework to assess after the required protocol behavior is defined; its candidacy does not establish post-quantum compatibility or performance.

The current work proposes four comparison packages:

| Package | Intended question | Main consequence to examine |
| --- | --- | --- |
| Conservative independent verification | Can a lower-load baseline preserve affordable local checking? | Control traffic and admission still need analysis; lower load alone does not solve concentration. |
| Cadence emphasis | What is required to prioritize short intervals? | Geographic coverage, peak resource demand, and finality latency may differ from cadence. |
| Higher capacity on stronger devices | What capacity becomes plausible with a larger operator budget? | Entry costs and infrastructure dependence may increase. |
| Conditional lighter duties | Can sampling or proof verification lower some participants' costs? | Work moves to other roles or introduces new selection, validity, and provider assumptions. |

All comparisons retain payments and atomic exchange. A candidate cannot claim improvement by silently removing exchange, narrowing geographic coverage, weakening verification, or changing the adversary.

Established research techniques and implementation components are starting points, not evidence that their proposed combination is secure. Cooldown policies, role separation, proof integration, and other adaptations require their own arguments. Comparative rankings and protocol-specific guarantees remain work for Phase 2; this version does not invent them. [S5, S6]

## 11. Development path and open decisions

The next research phase should resolve or explicitly bound the adversary and network model, admission and influence rules, bootstrap trust, and quantum security objectives. Financial semantics must be clarified alongside those foundations.

The principal owner decisions are:

1. Which payment and exchange semantics are mandatory, including consent, expiry, replay, and failure fees?
2. Which roles must be independently feasible on weak stationary and mobile devices?
3. Which faults, network conditions, and recovery assumptions define the security envelope?
4. What inclusion and ordering service is promised under contention and overload?
5. How are admission, voting influence, proposal duties, accountability, and exit assigned?
6. Which historical trust anchors, offline horizons, and retention dependencies are acceptable?
7. What control-concentration and sampling-risk criteria should candidates satisfy?
8. Which validity and availability obligations apply before finality?
9. What does approximately one second measure, and which workload-specific targets take priority?
10. What quantum threat horizon, security objectives, and migration obligations are mandatory?
11. Which tradeoffs and comparison package should guide architecture selection?

These correspond to P1-Q01–P1-Q11; P1-Q12 records explicit disposition of requirements. Documentation approval alone does not answer them.

After comparative research, candidate architectures should specify critical paths, state and message boundaries, membership transitions, and failure behavior. Targeted simulations or benchmarks can then address identified uncertainties when explicitly authorized. A requirements specification must be approved before detailed protocol design; Rust implementation begins only on explicit request.

Dependency adoption has its own gate. The inspected signature crate declares GPL-3.0, while the specialized circuits, recursive engine, and hash implementation declare different licenses. Version compatibility, features, artifacts, provenance, and license obligations must be resolved before importing code. No code has been imported as part of this research. [S7]

## 12. Source map and requirement traceability

The sources below contain the detailed assumptions, proposed criteria, calculations, and unresolved questions behind this synthesis. Source observations refer to the snapshots inspected on 25 September 2026, not current upstream branches. Immutable external implementation links and the standardized signature-size reference are maintained in S7.

| Source | Document | Whitepaper coverage |
| --- | --- | --- |
| S1 | [Financial workloads and consensus scope](../requirements/financial-workloads-and-scope.md) | Financial baseline, outcome semantics, workloads, authorization payloads. |
| S2 | [Participant roles and candidate device profiles](../requirements/participant-roles-and-device-profiles.md) | Role duties, weak-device fixtures, mobile limits, verification trust. |
| S3 | [Consensus guarantees, assumptions, and decentralization](../requirements/consensus-guarantees-and-decentralization.md) | Safety, progress, participation, effective control, recovery, PQ obligations. |
| S4 | [Proposed performance and resource budgets](../requirements/performance-and-resource-budgets.md) | Numerical fixtures, traffic accounting, measurement and recovery criteria. |
| S5 | [Requirement tradeoffs and proposed priorities](../requirements/tradeoffs-and-priorities.md) | Comparison packages and unresolved priorities. |
| S6 | [Phase 1 consolidated requirements](../requirements/phase-1-requirements-draft.md) | P1-R01–P1-R20, consistency findings, P1-Q01–P1-Q12. |
| S7 | [Cryptography and specialized proof implementation review](../research/crypto-zk-implementation-review.md) | Pinned evidence E1–E7, parameters, proof scope, transport and reuse boundaries. |

For review, sections 2–3 map principally to P1-R01–R03 and R06–R07; sections 4–5 to R04–R05 and R08–R12; sections 6–7 to R17–R18; sections 8–9 to R10–R16; and sections 10–11 to R19–R20. Future versions should record explicit decisions and new evidence without presenting the proposals in this version as previously accepted requirements.
