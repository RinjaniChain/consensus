# Phase 1 requirements draft

Status: **Draft for explicit owner review. Technical requirements, priorities, architectures, and production targets remain proposed unless individually identified as agreed.**

Version: **0.1** · Date: **2026-09-25**

Document path: `docs/requirements/phase-1-requirements-draft.md`

Tracking: [issue #6](https://github.com/RinjaniChain/consensus/issues/6) · [Phase 1 roadmap](../../README.md#phase-1--scope-and-measurable-requirements)

This is the initial requirements baseline for research, not the final Phase 4 specification. It consolidates the five supplied Phase 1 workstreams, identifies conflicts and missing evidence, and provides a review checklist. **Payments and atomic asset exchange remain the agreed starting scope.** No consensus architecture has been selected.

Publication or merge approves publication of this draft. It does not approve its proposed requirements or resolve owner questions. Human review of this consolidated version is pending; the corresponding issue completion criterion cannot yet be marked complete.

## 1. Baseline, evidence, and change control

### 1.1 Incorporated inputs

| Input | Supplied version and issue | Incorporated content | Remaining incompleteness |
| --- | --- | --- | --- |
| [Financial workloads and scope](financial-workloads-and-scope.md) | 0.2; [#1](https://github.com/RinjaniChain/consensus/issues/1) | W1–W6, transaction outcomes, subsystem boundaries, scope approval | Exchange and asset semantics, optional features, representative demand |
| [Participant roles and device profiles](participant-roles-and-device-profiles.md) | 0.1; [#2](https://github.com/RinjaniChain/consensus/issues/2) | Role duties, verification boundaries, H1–H4, lifecycle and resource questions | Required device capabilities, role architecture, operational feasibility |
| [Consensus guarantees and decentralization criteria](consensus-guarantees-and-decentralization.md) | 0.1; [#3](https://github.com/RinjaniChain/consensus/issues/3) | DEF, A, G, F, D, PQ records and IA-01 | Fault thresholds, network model, admission, proofs of guarantees, recovery policy |
| [Proposed performance and resource budgets](performance-and-resource-budgets.md) | 0.1; [#4](https://github.com/RinjaniChain/consensus/issues/4) | TIME, B-E, B-M, B-N and B-F records; resource accounting | Selected thresholds, exact mechanisms, representative environments and measurements |
| [Requirement tradeoffs and proposed priorities](tradeoffs-and-priorities.md) | 0.1; [#5](https://github.com/RinjaniChain/consensus/issues/5) | TP-T conflicts, TP-K packages, TP-P priorities and TP-R relaxations | Owner ranking, acceptable compromises, feasible package |
| [Cryptography and specialized proof implementation review](../research/crypto-zk-implementation-review.md) | Dated 2026-09-25 | Supplied implementation observations, pinned provenance, proof and licensing boundaries | Compatible dependency set, security assessment, integration and resource evidence |

All five workstreams have supplied documentation. Their unresolved technical questions remain incomplete research; incorporation does not mean that their proposals have been accepted or demonstrated feasible. Only issue #6's full issue body is supplied here; coverage of the other issues uses their supplied documents and completion mappings.

### 1.2 Evidence classes

- **Recorded agreement:** an explicit owner decision documented in a supplied input.
- **Project direction:** a goal or process constraint in the README, not evidence of an achieved guarantee.
- **Supplied finding:** an observation reported by a supplied document, retaining that document's source and version limitations.
- **Analysis:** reasoning or arithmetic under named assumptions.
- **Proposal:** a requirement, priority, mechanism, or acceptance method awaiting agreement.
- **Synthetic fixture:** a hypothetical workload, device, network, or failure case; neither a forecast nor an experiment.

This consolidation performs no new source inspection, external research, implementation, or experiment. No throughput, security certification, device-support result, or external protocol superiority is established.

### 1.3 Decision status and version rules

| Status | Meaning | Current record |
| --- | --- | --- |
| Agreed | Explicit owner agreement with a traceable reference and bounded scope | AGR-01 below |
| Proposed | Available for review; neither owner silence nor publication changes its status | All P1-R requirements, numerical envelopes, priority ordering, and architecture alternatives |
| Deferred | Explicit decision to postpone an item, recording consequences and revisit conditions | No additional explicit owner deferral is recorded. Proposed deferrals below remain proposals. |
| Rejected | Explicit decision against an item, with rationale and approval reference | No architecture or requirement rejection is recorded. An analytically incompatible fixture pairing is not a rejected architecture. |

**AGR-01 — agreed financial starting scope:** payments and atomic asset exchange from the beginning. Approval reference: [workload draft, section 10](financial-workloads-and-scope.md#10-open-decisions-for-owner-review), recording the owner's 2026-09-25 response to the baseline-scope question. This does not approve a particular exchange mechanism, general programmable finance, cross-chain exchange, signatures, batches, or targets.

The README separately establishes the research-first process and directions including deterministic finality, permissionlessness, decentralization, PQ architecture, and influence not directly assigned by computational power. This document does not manufacture additional explicit owner decisions from those goals.

P1-R, P1-Q, and P1-C identifiers are stable consolidation identifiers. Existing source identifiers remain valid and are not renumbered. A later revision should record changed identifiers, rationale, affected dependencies, and any explicit decision reference. Acceptance of one record does not automatically accept its implementation, source proposals, or unresolved numerical parameters. Review should identify both the document version and the reviewed PR head.

## 2. Scope and definitions

### 2.1 Financial scope

W1 payments and W4 atomic exchange are the core analysis profiles. W4's same-chain, two-party, two-asset model is the supplied working model, not approval of all its semantics. Issued-asset policy in W2 remains unresolved. W3 batches, W5 programmable finance, and W6 specialized proof-backed claims remain additional proposed profiles; they cannot replace W1/W4 coverage.

Propose keeping cross-chain settlement, external asset backing, oracle correctness, private transactions, a matching engine, and a specific VM outside the initial guaranteed service until separately specified. These are proposed scope boundaries, not recorded owner deferrals. Atomic exchange does not itself require approval of general programmability.

Every candidate exchange description must resolve consent, matching of terms, asset authority, amounts, cancellation, expiry, conflicts, fees, and replay effects. Its success criterion must specify which effects are atomic. A payment retry after a timeout must not silently create another economic payment.

### 2.2 Common terms

These definitions consolidate the supplied DEF and TIME records and remain proposed terminology.

| Term | Operational meaning |
| --- | --- |
| Safety | Correct verifiers do not accept conflicting finalized histories within the declared fault, cryptographic, bootstrap, and transition assumptions. |
| Deterministic finality | Once accepted as final, history remains a prefix of later accepted finalized history while its assumptions hold; no additional confirmation depth is required. Selection and cryptographic failure risks remain explicit. |
| Validity | Finalized records satisfy the selected deterministic predicate, including authorized financial effects and membership changes. Agreement alone does not establish validity. |
| Liveness | Finalized history eventually advances from an admissible state under declared participation, network, and validity conditions. This alone does not guarantee inclusion of a particular transaction. |
| Finalized successful outcome | Successful effects are verifiably bound to finalized history. Finalized failure or inclusion alone is insufficient. Here, settlement refers only to that successful on-chain outcome. |
| Inclusion service | An eventual, bounded, or best-effort service for eligible transactions under explicit dissemination, capacity, scheduling, and expiry rules. The service class is unresolved. |
| Permissionless participation | Entry into a named role follows objective published rules without discretionary incumbent approval; economic and operational barriers must still be disclosed. |
| Independent verification | A participant checks a named claim from authenticated evidence under disclosed assumptions. Delegation and RPC-reported success are different capabilities. |
| Effective control | Distribution of voting, production, transaction selection, and essential services across control and failure domains. Keys do not establish independent operators. |
| Data availability | Designated roles can retrieve the data needed for verification and recovery within a declared retrieval and retention envelope. A commitment or correctness proof alone is insufficient. |
| Slot, block interval, finality latency | Respectively: a scheduled opportunity interval if slots exist; elapsed time between named block acceptance events; elapsed time from a declared event to verified finality. These are separate metrics. |
| Resource envelope | A simultaneous conjunction of workload, role duties, scale, network/fault conditions, and resource/service limits. Passing its components in unrelated cases does not satisfy it. |

## 3. Assumptions that candidates must instantiate

No unresolved assumption receives a favorable default. These records summarize [A-F through A-I](consensus-guarantees-and-decentralization.md#3-explicit-assumption-register).

| ID | Required declaration | Current boundary |
| --- | --- | --- |
| P1-A01: financial semantics | Eligibility, authorization, value conservation, exchange atomicity, fees, replay, expiry, and conflict rules | Scope agreed; detailed semantics open |
| P1-A02: adversary and control | Fault unit, safety/progress/censorship thresholds, crashes, equivocation, adaptive corruption, historical keys, collusion and correlated failures | No threshold, weight rule, stake mechanism, or independence assumption selected |
| P1-A03: network and clocks | Connectivity, delivery model, bandwidth, delay/loss, eclipse conditions, skew/drift, and restoration interval | Synthetic network cases exist; no mandatory synchrony or clock model selected |
| P1-A04: validity and data | Local execution, proofs or attestations; exact predicate; required data, suppliers, retrieval and retention | No validity architecture or general-purpose proof construction selected |
| P1-A05: lifecycle and recovery | Admission, activation, duties, exit, membership/key changes, bootstrap anchors, offline horizon and durable signing state | No committee, withdrawal rule, checkpoint policy, or rollback authority selected |
| P1-A06: cryptography | Quantum capabilities/horizon; per-dependency properties and parameters; compromise, migration and historical verification | Signature, hash, proof, selection and transport policies remain separate open choices |
| P1-A07: measurement case | W profile, H device, role combination, N/M/q, topology, encoding, artifacts, observer, duration and offered load | Fixture values are proposals; missing costs are unknown, not zero |
| P1-A08: control attribution | Operator/provider grouping, missing coverage, uncertainty, geography and privacy limitations | Identity count cannot substitute for evidenced control groups |

N denotes active voting identities, M an optional assigned subset, and q signatures carried in a certificate fixture. None denotes independent operators or an approved quorum threshold.

## 4. Consolidated requirement register

**Every P1-R record below is proposed.** AGR-01 supplies the agreed scope underlying P1-R01; its detailed acceptance method remains proposed. Criteria describe evidence required later, not achieved protocol behavior. The listed P1-Q records are concrete owner questions or research blockers carried into section 9.

### 4.1 Workloads, outcomes, and role contracts

| ID | Draft requirement and rationale | Assumptions / dependencies | Proposed acceptance criterion or blocking question |
| --- | --- | --- | --- |
| P1-R01 | Retain payments and atomic exchange in the baseline; avoid transfer-only conclusions about financial feasibility. | AGR-01; P1-A01; W1/W4 | Every baseline comparison covers W1 and W4 separately, with authorization, state-access, bytes, contention and outcome counts. Resolve detailed semantics through P1-Q01. |
| P1-R02 | Specify valid payment/exchange outcomes and unambiguous retry behavior. Financial correctness must be distinct from agreement. | P1-A01/A04; G-03; W1/W4 | A deterministic semantic account covers authorization, conservation, both-or-neither exchange effects, consent, cancellation, expiry, conflicts and fee/replay exceptions. Synthetic duplicate, timeout and one-sided-effect traces have explicit outcomes. P1-Q01 blocks final criteria. |
| P1-R03 | Distinguish submission, local admission, inclusion, execution, finalized inclusion and finalized success. | DEF-07/DEF-09; TIME-03–TIME-08 | Each advertised response names its event and verification method. Reports include rejected, expired, failed, dropped, pending and unknown outcomes; retries retain original request identity/time. |
| P1-R04 | Give each role a verification, data, retention, trust and cost contract. Cheap client access must not imply independent voting. | ROLE-01/04/06; P1-A04/A07 | Cover voter, proposer, executor, optional prover, full verifier, archive, light verifier and RPC client. Identify locally checked claims, proof-checked claims and trusted assertions, including co-located duties. Role/device mandate remains P1-Q02. |
| P1-R05 | Separate consensus, execution, networking, cryptography, storage and availability responsibilities. | Workload section 8; P1-R02/R04 | Document contracts binding order, results, state, membership and availability evidence to finality. Identify responsibility for missing/invalid results and crash persistence; no subsystem may infer another's guarantee from a hash alone. |

### 4.2 Guarantees, participation, and recovery

| ID | Draft requirement and rationale | Assumptions / dependencies | Proposed acceptance criterion or blocking question |
| --- | --- | --- | --- |
| P1-R06 | Preserve finalized-history safety and permanence within an explicit envelope. | G-01/G-02; P1-A02/A03/A05/A06 | State finality predicate and observer procedure; provide a safety argument including restart and membership changes. Any admissible conflicting-finality trace defeats the claim. Arbitrary-delay safety remains a proposed scope of the guarantee, pending P1-Q03. |
| P1-R07 | Define progress separately from transaction service. Empty progress must not mask indefinite omission. | G-04/G-05; P1-A01–A05 | Name restoration conditions and eventual-progress argument. Choose eventual, bounded or best-effort inclusion; define eligibility/dissemination, conflict/expiry transitions and overload behavior. P1-Q03/Q04 block service bounds. |
| P1-R08 | Make entry, active duties, exit and release objectively specified for every claimed permissionless role. | G-07; ROLE-02; P1-A02/A05 | Publish lifecycle, costs, queues, rejection reasons, residual duties and release conditions. Assess discretionary/exclusive-provider gates. Admission resource and accountability remain P1-Q05. |
| P1-R09 | Protocol-assigned voting and proposing influence must not increase directly with computation at fixed qualifying resource. | IA-01; P1-A02/A08 | Define allocation functions and comparison interval; analyze extra computation, grinding and identity splitting. Separately report assigned versus completed duties and resulting concentration. A rule merely omitting CPU as an input is insufficient. P1-Q05/Q07 remain open. |
| P1-R10 | Authenticate bootstrap, rejoin, transitions and signing recovery before resuming duties. | G-08; F-05–F-07; P1-A05/A06 | Compare genesis replay, authenticated snapshots and checkpoints with explicit trust. Cover stale/lost signing state, duplicate devices, historical-key compromise, supplier loss and long offline periods. P1-Q06 blocks accepted trust and offline limits. |
| P1-R11 | Define availability independently of execution correctness. | G-06; DEF-17; ROLE-04; P1-A04 | Map each role to required objects, authenticated retrieval, retention and recovery suppliers. Specify missing-body/proof/result behavior and claims still supportable. A validity proof cannot substitute for this contract. P1-Q06/Q08 remain open. |
| P1-R12 | Report effective control and dependencies, with uncertainty. Nominal identity diversity is insufficient. | D-01–D-09; P1-A08 | Cover voting, production, admission resource/delegation if applicable, geography, infrastructure, verification and service dependence. Report coverage, top shares and HHI only under disclosed groupings; do not substitute one attack threshold for another. Limits remain P1-Q07. |

### 4.3 Resources, PQ security, and decision integrity

| ID | Draft requirement and rationale | Assumptions / dependencies | Proposed acceptance criterion or blocking question |
| --- | --- | --- | --- |
| P1-R13 | Attach timing and throughput targets to exact outcomes and operating envelopes. | TIME-01–TIME-09; P1-A07; P1-R03/R07 | Select endpoints and concrete thresholds before evaluation. Report complete-cohort outcomes, p50/p95/p99, maximum, pending ages, counts and observer uncertainty, including per-region results. Timing meaning and production values remain P1-Q09. |
| P1-R14 | Account for simultaneous per-role bandwidth, CPU, memory, durable I/O, storage, energy and peak duties. | B-M01–B-M15; H1–H4; P1-A04/A07 | Include proposals, votes, certificates, identities, transport, availability, invalid traffic and recovery. A mandatory lower bound above budget excludes the pairing; unknown work leaves feasibility unresolved. Device and budget selections remain P1-Q02/Q09. |
| P1-R15 | Make retention, bootstrap and catch-up compatible with continuing live demand. | B-M08–B-M13; P1-R10/R11/R14 | State retained-data policy, initial occupancy, temporary space, growth, maintenance runway and supplier duties. Under the simple backlog model, catch-up requires service rate greater than live growth; measure safe readiness separately from download completion. P1-Q06 remains open. |
| P1-R16 | Preserve declared checks during faults and resource exhaustion; report loss of progress honestly. | F-01–F-10; B-F01–B-F09; P1-A02–A06 | Analyze misses, delay/reordering, partitions, reconnection, correlated service loss, invalid ingress, unavailable data/proofs, stale signers and transitions. Distinguish conditions inside/outside the guarantee; no unapproved trusted fallback or revocation of finality counts as normal success. |
| P1-R17 | Maintain complete PQ dependency coverage and migration obligations. | G-09; PQ-01–PQ-07; P1-A06 | Each dependency has a justified claim, explicit inapplicability or visible gap: authorization/identity, votes/certificates, hashes, randomness/selection, proofs, transport/distribution and historical migration. No whole-system claim from one signature path. P1-Q10 blocks security objectives. |
| P1-R18 | Bound proof and dependency reuse claims to supplied evidence. | Supplied cryptography review; W6; P1-R02/R11/R17 | Distinguish ownership, transfer membership, private aggregation and public aggregation. Record artifacts, public inputs, canonical-history/nullifier checks, privacy boundaries and amount conversion. Do not infer general execution validity, exchange semantics, compact vote certificates or availability. Adoption requires separate compatibility, security and licensing review. |
| P1-R19 | Compare simpler and more complex alternatives under matched assumptions. | TP-K01–TP-K04; TP-M01; all applicable P1-A records | Compare local-execution references, timing/capacity variants and conditional lighter-duty approaches without dropping exchange. Identify every changed trust, participation or network condition before claiming improvement. Priorities remain P1-Q11. |
| P1-R20 | Preserve explicit approval and evidence status through revisions. | README process; AGR-01; TP-M11 | Every accepted/deferred/rejected record has an explicit decision reference, scope and rationale. All requirements have a criterion or blocking question. Closure/publication is not an architecture decision. Human review remains P1-Q12. |

## 5. Candidate resource baseline

The [budget draft](performance-and-resource-budgets.md) retains the detailed numerical proposal records. This section consolidates them without turning ranges into production requirements. Every endpoint below is a candidate, and no candidate is demonstrated jointly feasible.

| Envelope | Proposed workload/role conditions | Candidate service and resource values | Main blocker |
| --- | --- | --- | --- |
| B-E1 | H1 local full verification; voting and producer duties additionally accounted; N=16/64, B-N1 | W1 10–25 requests/s; W4 5–12 exchanges/s; mixed 8–20 requests/s. Sustained 6 Mbit/s down, 1.2 up; process memory 1 GiB; 64 GiB available storage. | Upload, control evidence, execution and retention duties may exclude some combinations. |
| B-E2 | H4 local verification/combined-role cases; N=64/256, B-N1 | W1 100–500 requests/s; W4 50–250 exchanges/s; mixed 75–375 requests/s. Sustained 60 Mbit/s each direction; process memory 4/8/16 GiB on corresponding host variants; 256 GiB storage. | Replication, certificates, state I/O and recovery may consume remaining capacity. |
| B-E3 | H3 intermittent light verifier; B-N2; named claim and bootstrap policy | One verified outcome/minute while connected; retrieval/verification p95 2–10 s once current history is available. Sustained 1.2 Mbit/s down, 0.3 up; 512 MiB/1 GiB memory variants; 8 GiB storage. | Claim verification, certificate history, supplier dependence, background operation and energy unknown. No voting guarantee. |
| B-E4 | N=1,024 sensitivity; optional M only if sampling studied | No inherited throughput floor or latency promise. Account peak assigned duty and all control traffic. | Message structure, quorum evidence, selection risk and actual control distribution unresolved. |

B-E1/B-E2 additionally propose successful block interval p95 ceilings of 1–2 s, inclusion p95 of 2–5 s, finalized-success p95 of 4–10 s and p99 of 8–20 s. Each case must select particular values prospectively. The approximately one-second ambition remains unresolved among slot schedule, block cadence and finalized outcome; none is silently selected here.

Other candidate resource criteria remain traceable to B-M records: sustained CPU at 60% of allocated cores; execution p95 200–500 ms/block at the one-second reference; at least 20% free storage; explicit history/state growth and retention runway; durable I/O; authenticated bootstrap; rejoin while live traffic continues; mobile traffic/energy; data retrieval; and membership/key updates. These proposals must be evaluated together. Optional proof-screening deadlines are not general validity-proof feasibility evidence.

Network fixtures include 50/150/300 ms RTT cases for B-N1, 100/300/800 ms for B-N2, separate loss/jitter cases, and outage durations of 10 seconds, 60 seconds and 10 minutes. These are imposed cases, not evidence of actual geographic reach or fault tolerance.

Future reporting should use a fully specified tuple: workload and semantic version; role combination; device; membership/control scale; cryptographic artifacts and encoding; network/fault case; observer; selected thresholds; and observation window. The proposed warmup, duration, repetition and sample-count rules in budget section 9 are planning choices requiring review. They are not evidence of long-term reliability and authorize no executable work.

## 6. Consistency and unsupported-claim review

The following findings result from reviewing the supplied text. They are documentation and analytical findings, not experimental results.

| ID | Finding | Required disposition and traceability |
| --- | --- | --- |
| P1-C01 | The workload draft's illustrative 70% W1/20% W2/10% W3 mixture contains no exchange; B-WM uses 70% W1/30% W4. | Keep them as distinct named fixtures. Propose B-WM plus separate W1/W4 cases for scope coverage; do not silently make either mixture representative demand. P1-R01, P1-Q01. |
| P1-C02 | H1's synthetic 10 Mbit/s download cannot carry one full copy of the illustrative 1,000 W1/s payload: 44.136 or 59.8 Mbit/s under the supplied two authorization variants. | This pairing is analytically incompatible; it is not a measured capacity ceiling. Compare lower rate, changed roles, device envelope or justified encoding. P1-R14, P1-Q02/Q09. |
| P1-C03 | Lower voting CPU does not solve producer upload or certificate traffic. The supplied q=64 inline-key certificate example requires 3.696128 Mbit/s for one copy/s in its larger variant, above H1's 2 Mbit/s physical upload. | Keep q and key carriage as fixtures, not quorum/codec decisions. Account topology, registered-key alternatives and peak duty without assuming compact aggregation. P1-R14/R17. |
| P1-C04 | Finite disk capacities conflict with indefinite full-history retention at the supplied rates. | Separate current state, retained history, archive service and temporary storage. Pruning changes recovery dependencies and needs an explicit contract. P1-R10/R11/R15. |
| P1-C05 | Short cadence, low settlement latency and broad geographic participation are different obligations. | Require endpoint and critical-path definitions; slow regions cannot be removed from reports without changing the stated envelope. P1-R06/R13, P1-Q03/Q09. |
| P1-C06 | B-M01 proposes at least 99% successful completion by a declared deadline alongside p95/p99 ranges; the particular cohort and deadline still need selection. | Specify the cohort, deadline, observation cutoff and treatment of pending/failed requests before evaluation. Do not add success-conditioned percentiles to infer cohort-wide success. P1-R03/R13, P1-Q09. |
| P1-C07 | Light verification, local execution, proof verification and executor trust are not interchangeable low-cost duties. | Every resource reduction must identify moved work and added trust. Specialized proofs do not fill the general validity gap. P1-R04/R05/R18. |
| P1-C08 | Identity-level cooldown and committee scale can create misleading decentralization claims. | Analyze fixed-resource splitting, eligibility depletion, capture horizons and correlated operators; no cooldown, committee or risk limit is accepted. P1-R09/R12, P1-Q05/Q07. |
| P1-C09 | Concrete signature paths and declared proof parameters do not establish complete PQ security. | Keep all PQ dependencies and migration gaps visible. A recursion configuration value is not an independently justified quantum security level. P1-R17/R18, P1-Q10. |
| P1-C10 | Proposed gates, reference packages and relaxation order could be mistaken for accepted priorities when consolidated. | Preserve proposed status. There are no additional accepted priorities, deferred features or rejected architectures in this baseline. P1-R19/R20, P1-Q11. |

The byte examples are derived from supplied workload assumptions and signature/key sizes attributed to [NIST FIPS 204, Table 2](https://csrc.nist.gov/pubs/fips/204/final). They are neither verification benchmarks nor a signature-family decision. The [supplied implementation review](../research/crypto-zk-implementation-review.md) provides the pinned provenance for wrapper, proof and transport findings; no new inspection is claimed.

## 7. Alternatives and proposed priorities

A viable alternative here means a coherent subject for research, not a proven deployable design.

| Choice | Alternatives to retain | Security, failure, decentralization, performance, network and PQ implications |
| --- | --- | --- |
| Requirement package | TP-K01 conservative independent verification; TP-K02 cadence emphasis; TP-K03 higher capacity on stronger devices; TP-K04 conditional lighter duties | All retain payments/exchange. Lower load may preserve local checking but does not solve admission or certificates. Stronger devices can raise participation barriers. Lighter duties introduce separate selection, validity or provider assumptions. |
| Validity and roles | Combined roles; separate production with local execution; specified proofs; executor attestations | Offloading can reduce local work while concentrating production or adding honesty/soundness assumptions. Withholding requires explicit stop/fallback behavior. Proofs add artifact, privacy and PQ obligations; all alternatives still need data availability. |
| Participation | Full participation versus sampled duties; direct participation versus optional hosting/delegation | Sampling may reduce active communication but needs capture and corruption analysis, peak-duty budgets and PQ selection. Hosted participation may reduce user costs while concentrating signing and service outages. |
| Timing and throughput | Lower rate; longer intervals; narrower service envelope; stronger devices | Longer intervals amortize fixed evidence but increase waiting/bursts and do not reduce transaction history growth at fixed rate. Narrowing geography or weakening devices' roles changes participation claims and needs owner agreement. |
| Rotation | No cooldown reference; resource-weighted selection if admission supports it; explicit cooldown/fallback rules | Per-key limits invite splitting analysis; operator limits need credible attribution. Eligibility depletion can harm progress; predictable duties invite targeting. Randomization adds bias/grinding and PQ dependencies. |
| Recovery and retention | Replay; authenticated snapshots; explicit checkpoints; bounded local retention with external archives | Compare historical-key exposure, anchor trust, download/revalidation work and supplier loss. Migration must authenticate old history, not merely change future signatures. |
| Authentication costs | Supplied inline-key transaction variants; separately modeled registered validator keys; other justified constructions | Registered keys move work to authenticated enrollment, rotation and bootstrap. No compact certificate or algorithm security equivalence is assumed. |

Propose the TP-P ordering for discussion: valid finalized financial outcomes and accurate guarantees; defensible permissionless influence and PQ coverage; independent verification/recovery and control visibility; conditional progress and useful inclusion service; normal timing and capacity; optional capabilities and complexity. This ordering is **not accepted**.

Propose comparing lower throughput, longer timing and reduced optional breadth before raising hardware requirements, narrowing geographic coverage or adding trust. Those comparisons cannot remove atomic exchange. No relaxation is automatic, and a safety/validity failure cannot be compensated by a higher throughput score.

## 8. PQ and evidence-gap checklist

| Dependency | Supplied evidence boundary | Required next evidence |
| --- | --- | --- |
| Transaction, validator, peer and recovery identities | Concrete transaction and peer paths are reported; no complete identity policy selected | Threat horizon, domain separation, key binding, rotation, compromise recovery and role-specific costs |
| Votes and certificates | No compact PQ aggregate/threshold finality construction established | Candidate evidence format, security argument, distribution, verification and retention costs |
| Hashes and commitments | Concrete field/hash parameters reported without a whole-system security assignment | Required properties, cryptanalysis, encoding/domain compatibility and migration |
| Randomness and leader selection | No suitable complete construction established by supplied review | Bias, withholding, grinding, predictability, adaptive targeting and PQ assumptions |
| Proofs | Specialized ownership/transfer statements and different privacy layers reported | Exact soundness/privacy claims, artifacts, recursion assumptions, public-input binding and resource evidence; new statements if general validity is pursued |
| Transport and distribution | Alternative code paths reported, not active deployment/negotiation policy | Authentication binding, downgrade resistance, key exchange, software/artifact authenticity and handshake costs |
| Historical trust and migration | No approved migration policy | Coexistence, activation, old evidence after compromise, long-offline clients and any external anchor authority |

Unresolved entries prevent an unqualified whole-system PQ claim. Supplied configuration values and algorithm labels cannot fill those entries. Dependency adoption additionally requires a compatible pinned version/feature/artifact set and a licensing decision; documentation here imports no code.

## 9. Owner decisions and Phase 2 research register

All P1-Q records remain open. The owner resolves product priorities and acceptable assumptions; technical research must separately justify feasibility. Proposed research responsibilities below are handoff functions, not assigned people.

| ID | Explicit owner decision needed | Phase 2 research output / risk addressed | Traceability |
| --- | --- | --- | --- |
| P1-Q01 | Which exchange, asset, batch and retry semantics belong in the initial service? | Financial analysis: deterministic W1/W4 invariants, conflict/expiry traces and representative demand; avoid unauthorized or one-sided effects | SCOPE-02–04/07/08; P1-R01–R03; P1-C01 |
| P1-Q02 | Which weak stationary/mobile roles are required, and which device envelopes are representative? | Role/resource analysis: duties, availability, peak cost and supplier dependence; avoid conflating client access with independent influence | ROLE-07–09; B-D03; TP-D02; P1-R04/R14 |
| P1-Q03 | Which fault units, corruption models, safety envelope, synchrony and clock conditions must candidates meet? | Threat-model analysis: separate safety/progress/censorship thresholds and transition assumptions; avoid unconditional timing or static-set claims | DEC-01–03; B-D04; P1-R06/R07/R16 |
| P1-Q04 | Is transaction service eventual, bounded or best effort, and what ordering/overload behavior is acceptable? | Service analysis: eligibility/dissemination rules, omission scenarios and W4 expiry/conflict handling | DEC-04; B-D06; TP-D04; P1-R03/R07 |
| P1-Q05 | Which objective admission/influence/exit alternatives should proceed, including delegation and any rotation goal? | Economic/control analysis: acquisition, collusion, splitting/grinding, custody, exit and eligible-set depletion. No supplied non-stake mechanism is established; stake is also unselected. | DEC-06; TP-D05/06; P1-R08/R09 |
| P1-Q06 | What bootstrap trust, retention, offline horizon and incident-recovery authority are acceptable? | Recovery analysis: historical keys, conflicting anchors, stale signers, archive loss, replay/snapshot costs and migration trust | DEC-07; B-D07; TP-D08; P1-R10/R11/R15 |
| P1-Q07 | Is sampling acceptable; what risk horizon, control measures and eventual concentration limits apply? | Selection/control analysis: capture-to-failure mapping, per-selection/horizon bounds, adaptive corruption, provider correlation and attribution uncertainty | DEC-02/08; B-D05; TP-D07/11; P1-R09/R12 |
| P1-Q08 | Which validity/data contracts and additional provider assumptions may candidate designs use? | Execution/availability analysis: matched local execution, proofs and attestation alternatives; withholding, supplier exit and independent reconstruction | DEC-10; B-D09; TP-D08; P1-R05/R11/R18 |
| P1-Q09 | What does one second measure, and which joint rates, deadlines, success fractions, resource limits and network envelopes matter? | Quantitative analysis: exact encodings/message graphs, critical-path and lower-bound exclusions, workload/device representativeness and future measurement design | DEC-11; B-D01/02/04/10; TP-D03/04; P1-R13–R15 |
| P1-Q10 | What quantum horizon, security objectives and historical migration obligations are mandatory? | Cryptographic analysis: complete dependency assessment, justified parameters, compatibility and licensing gaps; no family chosen by payload alone | DEC-09; B-D08; TP-D09; P1-R17/R18 |
| P1-Q11 | Which priority ordering, reference package and relaxation sequence should guide further research? | Comparative analysis: matched assumption packages and documented capability/trust changes; avoid premature framework or architecture selection | B-D11; TP-D01/10; P1-R19 |
| P1-Q12 | Has the owner reviewed this version, and which individual records, if any, are explicitly accepted, deferred or rejected? | Review record: version/head, disposition, rationale and exact approval reference; unresolved technical choices remain open | Issue #6 final completion criterion; P1-R20 |

Phase 2 should first make P1-Q03/Q05/Q06/Q10 assumptions explicit, alongside financial clarification in P1-Q01. It can then compare candidate protocol families using primary evidence under matched assumptions and map results back to P1-R records. The supplied evidence is insufficient for substantive external protocol rankings; those comparisons remain future research.

Exact software timings, full message formats, deployed network/provider distributions, storage amplification, proof sizes/costs and representative demand remain evidence gaps. Later experiments require explicit authorization; identifying an evidence need does not authorize execution or implementation. Framework selection belongs after requirements and security analysis, not in this consolidation.

## 10. Review checklist and disposition procedure

### Documentation checks completed against supplied inputs

- [x] Incorporate all five Phase 1 workstreams and identify remaining incomplete research.
- [x] Preserve the explicit payments-plus-atomic-exchange agreement and its approval reference.
- [x] Give every consolidated requirement rationale, assumptions/dependencies, proposed status and a criterion or concrete blocker.
- [x] Retain role distinctions, conditional guarantees, resource accounting, failure cases and full PQ coverage.
- [x] Identify inconsistent fixture interpretations, analytical exclusions, missing evidence and premature-commitment risks.
- [x] Trace open owner decisions and Phase 2 research to requirement identifiers.
- [x] Distinguish proposal publication from architecture acceptance; record no unsupported accepted, deferred or rejected decisions.

These checks establish documentation coverage, not verified protocol properties.

### Human review gate — pending

- [ ] Owner reviews version 0.1 at the identified PR head.
- [ ] Review confirms whether the agreed scope is faithfully carried forward.
- [ ] Each proposed requirement needing a disposition is marked accept, revise, defer, reject or still open, with rationale and an explicit reference for decisions.
- [ ] Numerical targets, priority ordering, role promises and additional trust assumptions receive separate explicit decisions if any are to become mandatory.
- [ ] Remaining blockers have a research destination; deferrals state consequences and revisit conditions.
- [ ] Review record states whether issue #6's documentation/review scope is complete while preserving unresolved architecture choices.

A review approval without explicit technical dispositions leaves those records proposed. Issue closure must not be used as a substitute approval reference for them. If human review has not occurred, retain that unmet completion criterion visibly even though the draft is ready to publish.

## 11. Completion mapping to issue #6

| Issue scope or completion criterion | Coverage | Current status |
| --- | --- | --- |
| Consolidate workloads, roles, guarantees, budgets and priorities | Sections 1–7; P1-R01–R19; links to all five supporting issues/documents | Addressed for review; underlying technical decisions remain open |
| Assign draft identifiers with rationale, assumptions, status, dependencies and criteria | Sections 3–4; P1-A/P1-R; linked P1-Q blockers | Addressed; no proposed requirement is presented as achieved |
| Maintain open questions and Phase 2 research needs | Sections 8–9; P1-Q01–P1-Q12 | Addressed; research responsibilities proposed, no personal assignments invented |
| Check conflicts, unsupported claims, PQ omissions and architectural commitment | P1-C01–P1-C10; alternatives and PQ checklist | Addressed against supplied material only |
| Versioned draft and review checklist under `docs/requirements/` | Version 0.1 at the stated path; section 10 | Artifact prepared for publication |
| All Phase 1 workstreams incorporated or explicitly incomplete | Section 1.1 | Documentation incorporated; unresolved research expressly listed |
| Each requirement has a criterion or blocking question | P1-R register and P1-Q register | Addressed |
| Agreed, proposed, deferred and rejected distinguishable with approval references | Section 1.3; AGR-01; P1-R20 | Addressed; no new accepted/deferred/rejected decisions asserted |
| Remaining risks and Phase 2 questions traceable | Sections 6, 8 and 9 | Addressed |
| Owner has reviewed the Phase 1 deliverable; closure does not approve unresolved choices | P1-Q12 and section 10 | **Pending human review. Not claimed complete.** |

This draft supplies a reviewable Phase 1 baseline. Phase 4 must still resolve or explicitly defer open questions, distinguish mandatory properties from targets, and obtain explicit approval of the resulting requirements specification.
