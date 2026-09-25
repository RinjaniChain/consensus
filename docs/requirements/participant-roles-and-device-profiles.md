# Participant roles and candidate device profiles

Status: **Draft for review; no device support, role separation, voting rule, or numerical resource target is approved.** Version 0.1.

Related work: [issue #2](https://github.com/RinjaniChain/consensus/issues/2). Inputs: [financial workloads](financial-workloads-and-scope.md) and the [cryptography/proof review](../research/crypto-zk-implementation-review.md). The agreed starting financial scope is payments and atomic asset exchange. W1–W6 below refer to the workload draft; optional workloads remain optional.

## 1. Problem and terminology

“Participation on a weak computer or phone” can mean three different things:

1. **Consensus influence:** independently selecting and signing votes that contribute to agreement, subject to the agreed admission and fault model.
2. **Independent verification:** checking a claim using authenticated data and explicitly stated trust assumptions, without necessarily influencing consensus.
3. **Client access:** submitting transactions and displaying responses from a service, potentially without independently checking them.

These properties must be reported separately. Delegating stake does not establish independent validation; running an RPC wallet does not establish either voting or independent verification. This is a proposed classification, not a selected staking or delegation design.

The roles below are logical responsibilities. One process/operator may perform several roles; different processes may perform one role. Role separation is an architectural option, not an approved requirement. “Independent” always has a boundary: a node still needs cryptographic, bootstrap, network, and consensus assumptions.

## 2. Role and trust matrix

Every row is a candidate responsibility contract. Where the protocol has not selected a mechanism, the obligation is explicit and its mechanism remains unresolved.

| Role | Duties and what it verifies | Data/retention obligation | Dependencies and trust boundary |
| --- | --- | --- | --- |
| Validator / voter | Authenticate proposals/votes and validator-set changes; enforce voting/locking rules and the chosen block-validity predicate; protect signing history against conflicting signatures | Keys, durable anti-equivocation state, validator-set history needed for authentication, finalized anchors, and data/evidence required by the validity predicate | Admission and voting weight unresolved. If it does not execute transactions, it needs a specified alternative validity mechanism; trusting a proposer or executor is an additional assumption |
| Proposer / block producer | Select/order candidate transactions, construct a valid proposal, and supply the data required for validation and availability | Candidate transaction pool, proposal bodies, execution state or authenticated execution evidence, retransmission data | May depend on an executor/prover if roles separate; transaction selection can affect censorship and ordering even if voting power is decentralized |
| Executor | Apply deterministic transaction rules, authorization, fees/replay rules, and state transitions; produce results/commitments | Current authenticated state, transaction bodies, runtime/version information, and recovery data | Its result must be independently reproduced or covered by an accepted validity mechanism; an executor signature alone is not execution correctness |
| Prover / proof aggregator | Construct proofs for an explicitly defined statement; aggregate only compatible proofs and bind public inputs/version | Witnesses, circuit/proving artifacts, input data, temporary workspace, and retained artifacts for reproducibility | Specialized ownership/transfer circuits are available research inputs, not general state-transition proofs. Non-ZK leaf workflows may require a private boundary; see the implementation review |
| Full verifying node | Check canonical history/finality and the agreed block-validity rules; maintain authenticated current state; serve/check data according to policy | Current state plus a defined recent-history window; either replay data or authenticated bootstrap artifacts | Need not vote. Pruning old data changes historical service and bootstrap dependencies; “full” must name execution-based versus proof-based verification |
| Archival node | Retain and serve historical blocks/state artifacts with a defined retention scope; independently validate if advertised as a verifying archive | Historical bodies, receipts/proofs, and state versions as declared; indexes measured separately | Storage does not confer consensus power. Historical completeness and availability are service properties, not implied by a Merkle root |
| Light verifier / light client | Verify a precisely scoped claim: authenticated finalized headers, transaction inclusion, execution result, or state membership as actually supported | Trusted starting anchor, validator updates or other authentication evidence, headers/certificates, requested proofs and local wallet state | Header/inclusion verification alone does not prove successful execution or data availability. Supplier independence and bootstrap trust must be stated |
| RPC-only client | Submit requests, manage transaction identifiers/retries, display service-reported outcomes | Keys/wallet history as applicable; no requirement to download consensus history | Trusts the service for the claims it does not verify. It must not be advertised as independent consensus participation |

## 3. Availability, admission, exit, and cost ownership

| Role | Admission and exit questions | Availability and failure behavior to specify | Dominant resource categories |
| --- | --- | --- | --- |
| Voter | Permissionless economic admission, activation delay, assignment, withdrawal, and accountability unresolved | Online during assigned duties; safe abstention on missing data; reconnect/catch-up and safe key recovery before resuming votes; correlated absence affects progress | Signature verification/signing, proposal verification, peer traffic, durable metadata; execution costs if combined |
| Proposer | Eligibility may coincide with voting or be separate; cooldown/backup selection unresolved | Meet proposal/data deadlines under declared conditions; replacement after failure without conflicting finality | Ingress/egress bandwidth, selection/execution, state I/O, optional proving |
| Executor | Local component or independent service; no economic admission selected | Results must arrive within a defined budget; handle invalid, late, or withheld results | CPU, state working set, storage latency, state growth, transaction reads/writes |
| Prover | Open production versus selected service unresolved; no exclusive proving right assumed | Deadline, artifact availability, recovery after interruption, and fallback on missing proofs unresolved | Peak RAM, CPU/accelerator use, witness I/O, proof size; verify and prove budgets separate |
| Full node | Peer access and bootstrap should be specified without assuming validator stake | Can go offline without withholding a consensus vote if not a voter; revalidate/catch up on return | Sustained download, state memory/storage, verification, replay and snapshot costs |
| Archive | Storage/service participation need not be voting admission | Retention guarantees, discovery, replication and loss recovery unresolved | Long-term capacity, write rate, retrieval bandwidth and indexes |
| Light verifier | No voting admission assumed; access/provider censorship remains relevant | Intermittent operation; detect stale or unverifiable claims and fail closed rather than label them final | Header/certificate/proof bytes, verification, bootstrap/catch-up, battery |
| RPC-only client | Endpoint service policies may restrict access | Tolerate timeout/unknown outcome; avoid duplicate economic actions on retries | Request/response traffic, local signing, battery; provider bears remote validation cost |

Punishment for equivocation, penalties for downtime, and withdrawal delays are not selected here. Treating all offline behavior as malicious would be a separate economic/security decision, particularly for intermittently connected devices. Service/provider exit must not silently make historical verification impossible.

## 4. Architecture alternatives

| Alternative | Verification boundary | Potential benefit | Failure and concentration consequences |
| --- | --- | --- | --- |
| Combined voter/producer/executor | Voting participants check execution locally | Fewer external execution dependencies; straightforward attribution of costs | Highest per-voter execution/data burden; high throughput may exclude weaker devices; shared client faults remain correlated |
| Separate production, local voter execution | Producers build; voters independently execute and validate | Producer scheduling/ingress need not run everywhere | Voters still need bodies/state and execution capacity; separation alone does not make voting lightweight |
| Proof-backed validity for voters | Voters verify a statement binding the accepted transition to prior state, transactions, and result | Potentially smaller verification cost than executing everything | Requires a general validity construction not established by the inspected specialized circuits; proving deadlines and operator concentration may become critical |
| Voters accept executor attestations | Selected actors attest to validity | Can reduce local work | Introduces an executor fault/trust model; their honest quorum cannot be inferred from the consensus quorum unless explicitly justified |
| Committee participation | Only assigned participants perform particular duties | Potentially less frequent work or communication per participant | Sampling/capture, corruption timing, duty predictability, key management, and recovery need analysis; low average work is not low peak duty cost |
| Delegation / hosted voting | Another operator signs or validates on the participant's behalf | Easier economic access and operational availability | Delegators are not independent validators; keys, voting influence, data access, and outages may concentrate |

**Working recommendation, not a decision:** use combined and separated-production/local-execution cases as reference cost models. Evaluate proof-backed verification as an additional candidate with explicit missing work. Treat intermittently active phones first as a light-verification research target; do not promise that this establishes their feasibility as always-eligible voters. Conditional scheduled voting on mobile remains an open alternative, not a rejected feature.

## 5. Candidate device and network fixtures

The following are deliberately synthetic envelopes for future analysis. They are not measured capabilities, buying recommendations, minimum requirements, production SLOs, or approved targets. CPU architecture/model, instruction support, software version, and sustained thermal behavior must be recorded before benchmarking; core count alone is insufficient.

| ID | Proposed resource envelope | Network and availability fixture | Purpose and unresolved feasibility |
| --- | --- | --- | --- |
| H1: weak stationary computer | Two CPU cores available; 4 GiB total RAM with a 1 GiB process-memory test budget; 64 GiB available persistent storage; no assumed accelerator | 10 Mbit/s down, 2 Mbit/s up; peer RTT cases 50/150/300 ms; always-on power with explicit outage cases | Candidate full verification and/or voting at an as-yet unresolved workload rate; no throughput guarantee |
| H2: connected mobile | Two cores available to the workload; 512 MiB and 1 GiB process-memory cases; 8 GiB local-storage allowance; sustained CPU power unresolved | 10 Mbit/s down, 2 Mbit/s up; RTT 50/150/300 ms; foreground, Wi-Fi, charging | Evaluate verification and signing; heavier roles require demonstrated duty completion and safe scheduling |
| H3: intermittent mobile | H2 memory/storage cases; battery operation with 0.5/1/2 Wh per day energy budgets as fixtures, not promises | 2 Mbit/s down, 0.5 Mbit/s up; RTT 100/300/800 ms; 10-minute, 1-hour, and 24-hour offline intervals; 1/5/10 GB monthly transfer allowances | Light verification, outcome retrieval, and catch-up; continuous voting eligibility is not assumed |
| H4: resource sensitivity reference | 4/8/16 cores, 8/16/32 GiB RAM, 256 GiB available storage; accelerator type/support unresolved | 100 Mbit/s up/down, same RTT cases as H1 | Find where execution/proving becomes feasible and quantify the gap from weak-device goals; not an approved privileged operator tier |

Bandwidth is decimal bit/s; memory/storage use binary GiB/MiB except explicitly decimal monthly GB allowances. Define baseline idle energy and incremental protocol energy separately. Storage fixtures are available capacity, not total device capacity; history/state growth may exhaust them. Throughput and retention duration must be reported together.

Apply packet-loss cases {0%, 1%, 5%}, jitter, constrained upload, and peer reconnection separately before combining them. Report topology, jitter model, peer count, correlated regional/provider outages, and warm/cold caches; do not turn independent per-node availability percentages into an assumed independent fault model.

Android documents restrictions on background jobs and networking for restricted applications, with manufacturer-dependent details. This supports testing background suspension explicitly, not a universal claim that a phone can or cannot validate. [R1] Other operating systems require their own documented/tested scheduling assumptions.

## 6. Lower bounds and post-quantum accounting

The workload draft gives W1 payload subtotals of 5,517 bytes with ML-DSA-65 and 7,475 bytes with ML-DSA-87 for a 256-byte body and inline public key. These exclude framing and network replication. At an illustrative 1,000 W1 transactions/s, a participant downloading each body receives at least 44.136 or 59.8 Mbit/s respectively. This arithmetic already exceeds H1's proposed 10 Mbit/s download envelope before votes, proofs, execution, and recovery are considered. It does not establish any system throughput limit: it identifies an incompatible pairing of workload and device assumptions. [R2]

Consequently, low voting CPU cost alone is insufficient. Issue #4 must choose a viable combination of workload rate, data duties, and device budgets, or explicitly change the role/verification model. Compression, batching, sampling, and proof aggregation may change costs only under a specified construction and threat model.

For future measurements record independently:

- Transaction, vote, proposal, identity-update, and certificate authentication counts and bytes. Do not assume every message uses the transaction signature wrapper or inline key.
- ML-DSA variant, signing mode/context, key cache/registration assumptions, malformed-input rejection, and verification time on each device profile.
- Validator-set distribution and changes, key rotation, cold bootstrap, and repeated reconnect authentication.
- Proof generation versus verification, artifact sizes, peak RAM, witness access, public inputs, and the particular circuit statement. The specialized W6 proof profile does not certify W4 atomic exchange or all W5 execution.
- Hash/field encoding compatibility and canonical decoding; network-authentication, key-exchange, randomness, and leader-selection assumptions separately from transaction signatures.

No compact PQ threshold/aggregate certificate is assumed. A large validator set with individual signatures requires explicit certificate-size and verification accounting. Consensus power must not be assigned by compute capacity, but faster networking/execution can still concentrate practical production opportunities.

## 7. Failure and decentralization review

| Scenario | Required investigation | Observable evidence for later validation |
| --- | --- | --- |
| Voter receives a header but no body/proof | Define exactly what may be signed and when it must abstain | No accepted vote bypasses the chosen validity/availability predicate; progress loss reported separately |
| Mobile sleeps or loses connectivity during duties | Safe abstention, duty reassignment, recovery, and any economic consequences | Missed-duty rate and recovery latency under declared scheduling; no conflicting signatures after restart |
| Restored signing device has stale state or copied keys | Anti-equivocation persistence, fencing, key rotation, and recovery procedure | Crash/restart and duplicate-signer scenarios; no claimed safety from a seed backup alone |
| Light verifier is isolated by one provider | Freshness, authenticated validator updates, alternate peers, and bootstrap trust | Distinguish unverified/stale responses from accepted finality; document what cannot be detected during isolation |
| Executor/prover withholds results or supplies invalid data | Alternative providers, fallback, bounded validation cost, and stop conditions | Time to detect/recover and effect on liveness; no implicit trusted fallback that weakens validity |
| Proofs are valid but state data is unavailable | State reconstruction and availability obligations independent of proof soundness | Identify which roles can recover state and what data/retention assumptions they require |
| Many low-cost identities share one operator/cloud | Measure effective control instead of counting keys | Operator/provider concentration estimates with uncertainty; distinguish public evidence from unverifiable independence claims |
| Local devices depend on one RPC, archive, or prover | Quantify hidden service concentration and switching costs | Number of independent suppliers, data portability, recovery cost, and censorship/outage behavior |

Geographic diversity increases the range of latency and outage conditions the protocol must tolerate. No geographic quota, real-world identity oracle, or provider classification mechanism is selected. A committee or cooldown must not treat different keys as proven independent operators.

## 8. Proposed requirement records and owner questions

All records below are **proposed**. Review/merge of this document does not change that status.

| ID | Proposed requirement / question | Acceptance evidence or remaining decision |
| --- | --- | --- |
| ROLE-01 | Every advertised role names its verification claims and trust boundary | A role-to-claim matrix identifies what is checked locally, by proof, or by trusted service |
| ROLE-02 | Voting eligibility and availability obligations are explicit | Specify admission, activation, duties, exit, offline handling, and accountability in #3; economic choices require agreement |
| ROLE-03 | Weak-device claims include network, memory, storage, energy, and uptime | Reproducible future results against a selected H profile and W workload, including peak costs; budgets agreed in #4 |
| ROLE-04 | Proof-backed participation distinguishes statement scope and data availability | A documented validity/availability contract; no claim of general execution validity from specialized W6 circuits |
| ROLE-05 | Bootstrap and rejoining cannot silently replace independent verification with service trust | Name initial anchors, state acquisition, verification steps, and assumptions for long offline periods |
| ROLE-06 | Direct influence, independent verification, delegation, and RPC access remain separate metrics | Participation reporting distinguishes each category and its operator dependencies |
| ROLE-07 | Decide which mobile capability is a launch requirement | Owner choice: client access, independent light verification, scheduled voting, or continuous voting; no choice inferred here |
| ROLE-08 | Decide whether heavy execution/proving may be separate from voting | Compare section 4 under matched W1/W4 loads; explicitly approve any added trust assumptions |
| ROLE-09 | Decide the first representative device envelope | H1–H4 are fixtures only; validate representativeness before setting mandatory minimums |

Approval record: no new role architecture or device budget approved. Payments and atomic exchange remain the previously agreed scope. No protocol implementation, benchmark, or device-support claim is produced by this issue.

## 9. Completion mapping and next inputs

- Sections 2–3 cover duties, data, availability, admission/exit, resource categories, and trust for every candidate role.
- Sections 1 and 4 distinguish lightweight verification, actual consensus influence, and service access, including combined/separated alternatives.
- Sections 5–7 expose resource limits, availability conflicts, PQ costs, and concentration pressures; numerical fixtures remain proposals.
- Section 8 records unresolved owner choices. Issue #3 should formalize guarantees and eligibility assumptions; #4 should establish measurable budgets; #5 should prioritize the tradeoffs.

## References

- **[R1]** Android Developers, [System restrictions on background tasks](https://developer.android.com/develop/background-work/background-tasks/bg-work-restrictions), consulted 2026-09-25. Supports the need to model OS restrictions; it is not a blockchain performance measurement.
- **[R2]** [Financial workload draft, implementation-aligned authorization costs](financial-workloads-and-scope.md#implementation-aligned-authorization-costs), derived from FIPS 204 sizes and the pinned source references in the [implementation review](../research/crypto-zk-implementation-review.md). The bit-rate calculation here is arithmetic, not a benchmark.

All role contracts, numerical device envelopes, failure fixtures, and priorities in this document are proposed analysis unless explicitly identified as existing project agreement or attributed external evidence.
