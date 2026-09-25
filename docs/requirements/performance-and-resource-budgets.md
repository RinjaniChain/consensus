# Proposed performance and resource budgets

Status: **Draft for review — all new numerical budgets and acceptance methods are proposals. No production target or architecture is approved.** Version 0.1.

Document path: `docs/requirements/performance-and-resource-budgets.md`.

Related work: [issue #4](https://github.com/RinjaniChain/consensus/issues/4), [Phase 1 roadmap](../../README.md#phase-1--scope-and-measurable-requirements), [financial workloads](financial-workloads-and-scope.md), [participant roles and device profiles](participant-roles-and-device-profiles.md), [consensus guarantees](consensus-guarantees-and-decentralization.md), and [cryptography implementation review](../research/crypto-zk-implementation-review.md).

Payments and atomic asset exchange remain the agreed starting scope. W4 same-chain exchange is the existing draft's working model; its detailed semantics remain proposed. General programmable finance and cross-chain exchange are not approved. Publication, review, merge, or issue closure approves neither the numerical proposals nor the mechanisms discussed here.

## 1. Purpose and evidence boundary

This document proposes measurable budget envelopes and a future measurement plan. Its purpose is to expose combinations of workload, participant duties, network conditions, and resource ceilings that merit further analysis, including combinations already incompatible by arithmetic.

The approximately one-second ambition remains an initial project target whose exact meaning needs owner agreement. It is not simultaneously a slot duration, block interval, inclusion deadline, proof deadline, and settlement guarantee.

Evidence labels used throughout:

| Label | Meaning |
| --- | --- |
| Existing agreement | Payments and atomic exchange from the beginning, as recorded in the workload draft. Project goals and research process remain those in the README. |
| Supplied finding | An observation attributed to an existing supplied document or its primary-source reference. No additional source inspection is claimed here. |
| Analytical estimate | Arithmetic under explicitly named assumptions. It is not measured performance. |
| Synthetic fixture | A proposed workload, device, network, or fault case for later analysis. It is not a deployment forecast. |
| Proposed criterion | A candidate acceptance rule requiring review and explicit owner agreement before becoming a production requirement. |
| Unknown | Missing mechanism, evidence, or parameter that prevents a feasibility conclusion. Unknown costs must not be treated as zero. |

No protocol implementation, executable experiment, benchmark, new source inspection, or security certification was performed. The supplied evidence contains no measured RinjaniChain throughput, cryptographic timing, or end-to-end finality results. The numerical ranges below are original engineering proposals, not literature-derived performance claims.

## 2. Definitions and measurement endpoints

Use monotonic elapsed time at a named observer where possible. Cross-node measurements must report clock-offset uncertainty separately from any clock bounds required by the protocol. An RPC response is service-reported evidence unless the client verifies the claimed outcome.

| ID | Metric and units | Start and end events |
| --- | --- | --- |
| TIME-01 | Nominal slot duration, seconds | Scheduled opportunity boundary to the next boundary, only if slots are adopted. Report actual boundary error separately. |
| TIME-02 | Successful block interval, seconds | Consecutive locally validated block acceptances at the same observer. Also report consecutive finalized-record acceptance intervals separately. |
| TIME-03 | Admission latency, milliseconds | First client submission to explicit local admission or rejection at a named ingress node. |
| TIME-04 | Inclusion latency, seconds | First submission to observer verification of inclusion in an identified proposal/history position; also report admission-to-inclusion. Inclusion may remain unfinalized. |
| TIME-05 | Execution latency, milliseconds per transaction and block | All required ordered inputs and prior state available to verified execution result. Report waiting for those inputs separately. |
| TIME-06 | Proof latency, seconds | All required witness data and authenticated artifacts available to completed proof; separately, proof receipt to completed verification. Report witness acquisition and aggregation waiting separately. Applicable only to a specified proof statement. |
| TIME-07 | Record finality latency, seconds | First complete proposal available at the designated proposer to the observer accepting its finality evidence. This includes dissemination but excludes pre-proposal transaction waiting. |
| TIME-08 | Transaction finality latency, seconds | First submission to verified finalized inclusion; separately, first submission to verified finalized successful effects. Also report admission-to-each-outcome. |
| TIME-09 | Recovery latency, seconds or minutes | Declared restoration of progress conditions to next finalization, and separately to restored steady service and drained backlog. |

The outcome distinctions follow the [workload draft](financial-workloads-and-scope.md#3-user-visible-transaction-outcomes) and the guarantee draft's DEF-09–DEF-12. Finalized failure is not successful payment or exchange. W4 success requires both exchange legs under the eventual agreed semantics; counting one leg as a successful exchange is invalid.

Report p50, p95, p99, maximum observed latency, completed sample count, and pending-age distribution. A percentile computed only over successful completions must be labeled as such and accompanied by the full submitted cohort's outcomes. Requests still pending at the observation cutoff are right-censored, not zero-latency or silently discarded. Preserve original submission times across retries and re-admission.

Throughput means distinct economic requests per second at each stage: offered, admitted, included, finalized, and finalized-successful. Report retransmissions and duplicate requests separately. For W4 also report successful transfer legs per second; for optional W3 report batches and legs. Raw transactions per second cannot compare workloads with different authorization, execution, or batching costs.

Use decimal bytes, MB, GB, Mbit/s and seconds for traffic and rates; use binary MiB/GiB for device memory/storage capacities. One byte equals eight bits. Report both elapsed wall time and aggregate CPU core-seconds for computation.

## 3. Traceable assumptions and comparison fixtures

### 3.1 Assumption register

These assumptions instantiate measurement cases, not protocol choices. They supplement A-F through A-I in the [guarantee draft](consensus-guarantees-and-decentralization.md#3-explicit-assumption-register).

| ID | Working assumption | Traceability and unresolved consequence |
| --- | --- | --- |
| B-A01 | Baseline profiles are W1 payments and W4 atomic exchange, run separately. W2 is an additional transfer sensitivity case. | Agreed financial scope and workload draft. W4 consent, expiry, cancellation, fees, and conflict semantics remain unresolved. |
| B-A02 | Main byte illustrations use 256-byte bodies, inline public keys, and separate ML-DSA-65/87 variants. | Supplied workload and implementation reviews. These are comparison variants, not signature adoption. Full body ranges and encoding overhead remain required. |
| B-A03 | Combined execution/verification and separated production with local execution are reference cost models. | Role draft, section 4. Proof-backed validity and executor attestations remain separate alternatives with additional obligations. |
| B-A04 | N is the number of active voting identities; M is the number assigned to a duty if committees are studied; q is the number of signatures carried in a particular certificate fixture. | N, M, q, voting weight, and independent operator count are distinct. No quorum threshold, committee, equal-weight rule, or admission mechanism is selected. |
| B-A05 | N takes values 16, 64, 256, and 1,024 for scale sensitivity. Optional M takes 16, 64, or 256 subject to M ≤ N. | New synthetic fixtures spanning materially different message costs. They are not minimum decentralization requirements or acceptable capture risks. |
| B-A06 | A successful block interval of one second is used only as an arithmetic reference. Timing sweeps use 0.5, 1, and 2 seconds. | Initial timing ambition and TIME-01/TIME-02 distinction. Misses, repeated rounds, and transitions can reduce realized throughput. |
| B-A07 | Data and control traffic share the declared link unless a separate path is explicitly modeled. | Role data duties and A-N/A-V. Dedicated links cannot be silently added to make a device fit. |
| B-A08 | Baseline normal-operation cases have no injected Byzantine behavior or outages. | A measurement baseline, not a security assumption or fault-tolerance claim. Separate fault cases are mandatory. |
| B-A09 | Voting resumes after recovery only when history, membership, and durable signing constraints are authenticated. | G-08 and F-06/F-07 in the guarantee draft. Resource compliance cannot waive recovery safety. |

### 3.2 Workloads

All workload cases inherit the exact authorization, state-access, and outcome definitions of [W1–W6](financial-workloads-and-scope.md#5-candidate-workload-profiles).

- **B-W1:** pure W1, one authorization; 128–512 body bytes, with 256 bytes for central arithmetic.
- **B-W4:** pure W4, two independent authorizations; 256–2,048 body bytes. Independent pairs and contended pairs are separate cases.
- **B-WM:** new synthetic mixture of 70% W1 and 30% W4 by distinct submitted request count. This ensures exchange remains visible in the baseline; the proportions are not a demand forecast.
- **B-W2:** W2 with one/two authorizations and 0%/100% new recipient-state creation. Do not inherit B-W1 capacity without re-evaluation.
- **B-WX:** optional W3, W5, and W6 sensitivity cases retain their existing proposed status. No baseline throughput promise applies to them.

Run each baseline with 10,000 and 1,000,000 logical participants, applicable 32/256/4,096-byte state values, warm/cold state access, and 0%/50%/100% designated hot-state contention. Contended or deliberately invalid cases report useful successful effects and failures; they do not inherit the no-conflict success criterion below.

### 3.3 Network and geography

These are imposed network fixtures, not observations of Internet performance. Geography must eventually be described by actual node locations, provider relationships, and a complete measured peer latency matrix; synthetic regions do not demonstrate geographic decentralization.

| ID | Pairwise network fixture | Conditions and purpose |
| --- | --- | --- |
| B-N1 | RTT 50/150/300 ms; within-region RTT 50 ms and cross-region RTT 150 or 300 ms | Three logical regions, identities distributed as evenly as possible. Baseline loss 0%; bounded added one-way jitter 0–20 ms. Compare a uniform matrix with the regional matrix. |
| B-N2 | RTT 100/300/800 ms | Three logical regions, including a slower access region; one-way jitter 0–100 ms. Intermittent/mobile and degraded-service comparison. |
| B-N3 | B-N1 or B-N2 plus independent packet-loss cases 1% and 5% | Record transport, retransmission policy, queue discipline, and resulting application delivery. Percentage loss alone does not specify burst loss. |
| B-N4 | Complete partition or regional/provider disconnection lasting 10 seconds, 60 seconds, or 10 minutes | New outage-duration fixtures spanning brief disruption through recovery load. No normal timing target applies while progress conditions are absent. |

For analytical calculations only, one-way propagation is half the stated symmetric RTT. Future evidence must replace that simplification with measured directional behavior where available. Report node upload/download caps, peer degree, topology, bottleneck sharing, message priority, transport overhead, and handshake traffic. Repeat selected cases with asymmetric delays and correlated loss; their distributions remain an explicit measurement-plan input.

## 4. Candidate budget envelopes

An envelope is a conjunction: its rate, latency, resource limits, role duties, and conditions must hold together. Passing each component in unrelated runs does not establish feasibility. Range endpoints are alternatives for owner review, not permission to select a favorable threshold after seeing results.

| ID | Roles and device | Workload and scale | Proposed sustained finalized-successful rate | Proposed normal latency ceilings |
| --- | --- | --- | --- | --- |
| B-E1: weak stationary reference | H1 full verifier with local execution; voting assessed additionally with all control traffic. Producer duty is a separate peak-cost case. | B-W1, B-W4, B-WM separately; N=16 and 64; B-N1; no committee assumed. | W1: 10–25 requests/s; W4: 5–12 exchanges/s; mixed: 8–20 requests/s. | Successful block interval p95 ≤ 1–2 s; inclusion p95 ≤ 2–5 s; finalized success p95 ≤ 4–10 s and p99 ≤ 8–20 s. |
| B-E2: capacity reference | H4, initially 4 cores/8 GiB RAM; repeat at 8/16 and 16/32 core/GiB combinations. Combined producer/voter/executor and full verifier reported separately. | B-W1, B-W4, B-WM separately; N=64 and 256; B-N1; no committee assumed. | W1: 100–500 requests/s; W4: 50–250 exchanges/s; mixed: 75–375 requests/s. | Successful block interval p95 ≤ 1–2 s; inclusion p95 ≤ 2–5 s; finalized success p95 ≤ 4–10 s and p99 ≤ 8–20 s. |
| B-E3: intermittent verification | H3 light verifier, with claim and bootstrap trust explicitly specified. No voting or full-body download promise. | Track chains carrying B-E1 and B-E2 workloads; N=16/64/256; B-N2; 10-minute/1-hour/24-hour offline intervals. | No chain TPS target assigned to the client. Query one verified outcome/minute while connected as a synthetic service demand. | Once current authenticated history is available, outcome retrieval and verification p95 ≤ 2–10 s, p99 ≤ 5–30 s. Catch-up is reported separately. |
| B-E4: scale stress | H1 and H4 sensitivity, including peak assigned duty if committees are evaluated. | N=1,024; optional M sweep; B-N1/B-N2; all baseline workloads. | No success floor proposed before message accounting. Determine which B-E1/B-E2 points are analytically excluded. | No inherited latency promise; record the same endpoint distributions and failed duties. |

**Rationale and uncertainty:** B-E1 starts well below the known body-only bandwidth contradiction at 1,000 W1 requests/s. B-E2 explores one to two orders of magnitude more load while exposing replication and control-traffic constraints. The W4 ranges are lower because the fixture has two authorizations and additional state work, not because measured exchange execution is twice as expensive. The mixed ranges are independent proposals, not a linear capacity interpolation.

The latency ranges allow several communication and validation steps around the one-second block reference. No supplied consensus mechanism establishes that these deadlines are achievable, especially at 300 ms RTT, high certificate fan-out, or during repeated rounds. The equal B-E1/B-E2 latency proposals make hardware and scale tradeoffs visible rather than presuming that larger machines deliver faster financial settlement.

### 4.1 Timing alternatives requiring agreement

| Candidate interpretation | Measurable proposal | Consequence |
| --- | --- | --- |
| B-T1: one-second schedule | Nominal slot = 1 s if slots are selected; compare 0.5/2 s fixtures. | Says nothing about missed opportunities, inclusion, or finality. Requires a clock and scheduling model. |
| B-T2: one-second normal block cadence | TIME-02 p50 ≤ 1 s and p95 ≤ 2 s under the selected normal envelope. | Compatible with longer transaction settlement; invalid/missing proposals remain visible. |
| B-T3: one-second finalized outcome | TIME-08 finalized-success p95 ≤ 1 s under a separately narrowed envelope. | A substantially stronger alternative with no supplied feasibility evidence. Requires ingress, queueing, execution, dissemination, and finality to fit together. |

Use B-T2 as a proposed reference for analysis, not an accepted interpretation. No one-second claim applies during arbitrary delay or partition. TIME-07 record finality is separately proposed at p95 ≤ 2–6 s and p99 ≤ 5–12 s for B-E1/B-E2; this does not replace TIME-08's longer submission-based budget.

## 5. Resource and service budget table

Every row inherits its named envelope's workload, N/M cases, role, device, network, and normal conditions. Unless stated otherwise, resource ceilings apply while sustaining the selected throughput point and its latency ceilings. For each future evaluation, select one concrete value from each proposed range before collecting results.

| ID / metric | Units and endpoints | Proposed target or range | Acceptance method and unresolved feasibility |
| --- | --- | --- | --- |
| B-M01: sustained service | Distinct successful requests/s at verified finalized effects, with all preceding stage counts | B-E1/B-E2 rates in section 4; B-E3 query demand separately | Count over a 60-minute steady window after proposed 10-minute warmup. Require no increasing eligible backlog over the final 30 minutes. For prevalidated independent baseline requests, propose ≥99% finalized-success by the declared latency deadline; also report empirical p95/p99 and every other outcome. This percentage is a proposal, not an accepted failure rate. |
| B-M02: burst service | Offered requests/s, queue bytes, drain seconds from end of burst | 5× chosen baseline for 10 s; return to baseline; propose drain ≤120 s | Apply existing workload burst shape. Track admissions, bounded queue occupancy, drops, outcomes, and time back within steady latency ceilings. Burst acceptance may use explicit rejection, but cannot count rejected requests as served. Owner must choose required admission fraction. |
| B-M03: bandwidth | Receive/transmit Mbit/s at physical or virtual interface, separately per node; application payload bytes by category | B-E1: sustained ≤6 down/1.2 up; B-E2: ≤60 each direction; B-E3: ≤1.2 down/0.3 up while connected | Proposed 60% sustained use of H1/H3/H4 link caps reserves headroom for variation/recovery. Report maximum rolling 1 s and 60 s rates and queues; physical caps remain 10/2, 2/0.5, and 100/100 Mbit/s respectively. Capture all protocol and co-located-role traffic without double counting. |
| B-M04: memory | Process-tree resident memory and system pressure, MiB/GiB, from startup through steady state and recovery | B-E1 ≤1 GiB; B-E2 ≤4/8/16 GiB on 8/16/32 GiB hosts; B-E3 ≤512 MiB and separate ≤1 GiB variant | Peak, not mean, must fit selected ceiling; disclose file cache, mapped artifacts, swap, child processes, and accelerator memory. Half-RAM H4 budgets reserve OS/cache space; they are not measured requirements. |
| B-M05: computation | CPU core-seconds/s; per-role and per-operation times from input readiness to completion | Normal sustained CPU ≤60% of allocated cores; propose ≤80% during a declared recovery interval | B-E1 uses two available cores; B-E2 uses selected H4 count; B-E3 measures foreground/background separately. Require latency compliance and no accumulating compute queue. Core counts cannot substitute for specified CPU models and thermal behavior. |
| B-M06: execution | Milliseconds/block and transaction; inputs-ready to checked result | B-E1/B-E2 p95 ≤200–500 ms/block at 1 s reference cadence | Candidate allocation reserves part of cadence for networking and agreement; no execution timing evidence exists. Measure authorization, state I/O, arithmetic, persistence, and waits separately. Concurrent work must not be counted as sequential without a dependency model. |
| B-M07: proof production/verification | Seconds/proof, bytes/proof, peak GiB, core-seconds/proof; TIME-06 endpoints | Optional W6 sweep: generation budget 0.5/1/2/5 s; verification allocation ≤100–250 ms/block if required on a 1 s path | These are screening deadlines, not feasibility claims. Pin statement, leaf count, artifacts, hardware, witness access, and privacy boundary. General state-transition proofs have no established candidate artifact and remain an evidence gap. |
| B-M08: persistent storage | GiB occupied at role data-directory boundary; logical bytes and physical bytes written/s and retained/day | B-E1 ≤64 GiB; B-E2 ≤256 GiB; B-E3 ≤8 GiB, each with proposed ≥20% free reserve | Account state, retained bodies, receipts, certificates, indexes, artifacts, temporary compaction/snapshot space, and signing metadata. Candidate live-state growth allowance: B-E1 0.1–0.5 GiB/day; B-E2 1–5 GiB/day. These are planning limits requiring a declared new-state workload; report history separately. |
| B-M09: retention runway | Days from declared initial occupancy to 80% capacity, using net retained growth | Propose ≥30 days before maintenance for full verifiers; ≥90 days for B-E3 | Specify pruning/compaction policy and retained-history window; account for peak temporary space. An archive has no fixed-capacity pass claim: report raw/physical GB/day and one-year capacity projection under the exact workload. |
| B-M10: durable I/O | Physical MB/s, operations/s, fsync latency ms; request issuance to durable completion | Proposed durable-update p99 ≤10–50 ms; sustained device load ≤60% of independently characterized capacity | Applies to local-execution and signing-state duties in B-E1/B-E2. Storage model, access distribution, and failure semantics must be fixed. No disk capability is established by H1/H4 capacity alone. |
| B-M11: bootstrap | Minutes, downloaded bytes, CPU time, peak RAM/disk; trusted anchor available to independently validated current state | Synthetic 1 GiB B-E1 snapshot case: 30–120 min; 16 GiB B-E2 case: 60–240 min | Snapshot sizes are new fixtures, not asserted state sizes. Include membership/history evidence and concurrent chain growth. Compare authenticated snapshot with full replay; passing a snapshot case does not establish replay feasibility or approve checkpoint trust. |
| B-M12: rejoin/catch-up | Seconds/minutes and bytes; connectivity restored to verified current claim, or safe voting readiness | B-E1/B-E2: 1-hour offline case ≤5–30 min; B-E3: 24-hour offline case ≤1–10 min for its scoped claim | Include continuing live traffic and all missed authentication evidence. Report other offline fixtures without inheriting these bounds. Full-state catch-up and light-claim catch-up are different services. |
| B-M13: mobile transfer/energy | Decimal GB/month; Wh/day above idle baseline, plus total device energy | B-E3 compare 1/5/10 GB per month and 0.5/1/2 Wh/day | Inherit H3 fixtures, using a declared 30-day activity schedule including catch-up, queries, and handshakes. Record radio/OS/thermal conditions. No energy estimate or feasibility finding is available. |
| B-M14: availability service | Retrieval seconds and bytes; required-data request to verified complete response | B-E1/B-E2 live proposal data p95 ≤0.5–1 s; recent recovery object ≤1 MiB p95 ≤2–10 s | Declare object size, requesting role, retention age, serving load, supplier count, and authentication. Count service upload in B-M03. Successful retrieval samples do not prove adversarial data availability. |
| B-M15: transitions and key changes | Bytes/event, CPU/event, peak memory, and seconds from authorized activation to usable authenticated membership | Fixture changes affecting 1%/10% of identities per minute; no accepted churn rate | Evaluate B-E1/B-E2 latency/resource ceilings during changes and report any violation. Include full roster/key bootstrap, revocation/rotation, and overlapping evidence. Mechanism and safe transition rules remain unknown. |

For TIME-03 admission, propose p95 ≤100–500 ms and p99 ≤1 s for normal B-E1/B-E2 load. These are local service targets; they cannot establish global eligibility or inclusion. TIME-04, TIME-07, TIME-08 and B-E3 outcome latencies use section 4 ceilings and the common cohort method.

The CPU, memory, storage reserve, warmup, duration, and percentile proposals are planning choices. They offer explicit rejection conditions while leaving their representativeness open. A later result must identify the exact selected threshold; compliance with an unspecified range is not a meaningful pass.

## 6. Complete accounting model

### 6.1 Transaction payload lower bounds

The supplied workload draft attributes the following sizes to [FIPS 204, Table 2](https://csrc.nist.gov/pubs/fips/204/final) and its pinned implementation review. This document uses those supplied size facts without additional inspection.

| Authorization fixture | Signature bytes | Inline key bytes | W1 subtotal, 256-byte body | W4 subtotal, 256-byte body |
| --- | --- | --- | --- | --- |
| ML-DSA-65 | 3,309 | 1,952 | 5,517 bytes | 10,778 bytes |
| ML-DSA-87 | 4,627 | 2,592 | 7,475 bytes | 14,694 bytes |

These exclude scheme discriminators, framing, witnesses, block metadata, receipts, certificates, retransmission, and transport. They are lower-bound payload fixtures, not encoded transaction specifications. At the same request rate, the ML-DSA-87 W1 subtotal is approximately 35.5% larger than the ML-DSA-65 subtotal. This is a byte comparison, not a security-policy recommendation or verification-speed comparison.

For rate λ and mean subtotal b, one full copy requires `8 × λ × b` bit/s. B-WM's 256-byte-body mean subtotals are 7,095.3 and 9,640.7 bytes respectively.

| Analytical case | One-copy transaction payload | Consequence before other costs |
| --- | --- | --- |
| B-E1, 25 W1/s | 1.1034 or 1.495 Mbit/s | Fits the proposed 6 Mbit/s download budget. One full upload copy of the larger variant already exceeds the proposed 1.2 Mbit/s upload budget. |
| B-E1, 12 W4/s | 1.034688 or 1.410624 Mbit/s | Download arithmetic alone does not establish proposer or forwarding feasibility. |
| B-E2, 500 W1/s | 22.068 or 29.9 Mbit/s | Two full outgoing copies of the larger variant use 59.8 Mbit/s, leaving almost none of the proposed 60 Mbit/s sustained budget for other traffic. |
| B-E2, 250 W4/s | 21.556 or 29.388 Mbit/s | Similar replication pressure despite half the request rate. |
| B-E2, 375 mixed requests/s | 21.2859 or 28.9221 Mbit/s | Actual authorization/body distribution must remain visible. |

These exclusions do not establish a system capacity limit. They show why proposer duty, full verification, forwarding, and light verification cannot share an unexplained hardware claim.

### 6.2 Traffic ledger

For every node and role, account for application bytes in the categories below, then transport/packet overhead and retransmissions at the wire boundary. Sum unique interface measurements for co-located roles; do not count the same packet twice. Cluster totals sum node egress once; receive totals are a separate view.

| Category | Required terms |
| --- | --- |
| Client ingress and gossip | Valid/invalid/duplicate transactions, inventories, requests, peer fan-out, admission responses, and client status queries. |
| Proposals | Body, header, proposer authentication, execution result commitments, witnesses, and each retransmitted or conflicting proposal. |
| Voting and rounds | Vote payload/authentication, recipient count, phases, failed rounds, timeout/view-change evidence, and duplicate handling. |
| Certificates | Signer identification, each signature or justified alternative construction, keys if carried, statement framing, dissemination, and historical retention. |
| Availability | Required body/state distribution, retrieval requests/responses, replication or coding expansion if proposed, retention service, and any sampling evidence. |
| Membership and identity | Rosters, keys, activation/rotation evidence, authentication history, bootstrap, and reconnect handshakes. |
| Optional proofs | Public inputs, proof bytes, verifier artifacts, witness movement, aggregate formation, and retries. Private witness movement must respect the declared trust boundary. |
| Recovery | Missing blocks/state, snapshots, retained history, crash recovery, resynchronization, and backlog processing while new traffic continues. |

No compression, signature aggregation, erasure coding, or proof-based reduction receives a byte credit without a specified construction, security assumptions, and applicable data duties.

### 6.3 Votes and certificate sensitivity

For a synthetic individually signed certificate:

`certificate_bytes = framing + signer_identification + q × (signature_bytes + optional_key_bytes)`.

A registered-key variant may omit per-certificate keys only by explicitly adding authenticated key registration, lookup, rotation, bootstrap, and historical retention costs. The transaction baseline still uses inline keys.

For illustration, q=64 individual signatures require 211,776 bytes with ML-DSA-65 or 296,128 bytes with ML-DSA-87, before framing and signer identification. Carrying all corresponding keys raises these subtotals to 336,704 or 462,016 bytes. At one certificate per second, one copy of the latter requires 3.696128 Mbit/s. That exceeds H1's 2 Mbit/s physical upload cap before any transaction relay.

Here q=64 is a byte fixture, not an accepted quorum. Sweep q over 16/64/256/1,024 where applicable to the candidate's actual certificate rule. Signature-only subtotals for q=1,024 become 3,388,416 or 4,738,048 bytes. Whether every role must receive every certificate is part of the unresolved role/protocol contract.

In an all-to-all vote-exchange fixture, one phase generates `N × (N−1)` directed vote transmissions: 240, 4,032, 65,280, and 1,047,552 at the N values above. A collector fixture instead receives N votes and disseminates a result, shifting ingress, CPU, availability, and denial-of-service pressure to the collector. These are topology calculations, not claims about a selected consensus protocol.

Changing a full set to a committee can reduce active message work but introduces selection/capture and corruption-timing assumptions. Neither N nor M proves independent control; the guarantee draft's D-01–D-09 remain applicable.

### 6.4 Computation, timing, and queue stability

For local verification, a first-order work estimate is:

`CPU core-seconds/s = transaction authorization work + vote/certificate work + execution/state work + hashing/encoding + transport + optional proof work + recovery work`.

Use measured operation costs only when exact implementations, CPU models, artifacts, and adversarial input cases are available. For sensitivity before measurement, vary each unknown cost independently by factors 0.5/1/2/4 around a clearly labeled hypothetical reference. Do not publish the reference as observed performance.

For a declared sequence of d dependent communication steps, a lower-bound timing model is `d × one-way propagation + serialization on the critical links + critical-path processing`. At symmetric 300 ms RTT, four such propagation steps consume 600 ms before serialization, processing, queueing, or ingress. This does not assert that any candidate requires four steps; d must come from its actual dependency graph.

Block payload per successful interval is approximately `rate × interval × mean encoded transaction bytes`, plus block-level overhead. Doubling the interval doubles accumulated payload at fixed rate, which can amortize fixed certificate work but increases batching delay and peak dissemination demand. Increasing payload without spare bandwidth increases serialization time; more cores do not remove that delay.

For a simple burst queue with baseline λ, service μ, and a 5λ burst lasting 10 s, extra backlog is `max(0, 5λ−μ) × 10` requests. After the burst, drain time is backlog divided by `μ−λ` when μ>λ. At μ=1.5λ, this gives 70 s; at μ=λ, the backlog does not drain. These synthetic estimates ignore conflicts, byte variation, and priority changes. They explain why B-M02 requires service headroom, a lower admitted rate, or visible rejection.

### 6.5 Storage and synchronization lower bounds

At continuous W1 rate λ, raw transaction history growth is `86,400 × λ × b` bytes/day before other records or storage amplification. At 25 W1/s this is approximately 11.917 or 16.146 GB/day; at 500 W1/s it is approximately 238.334 or 322.92 GB/day. Thus H1's 64 GiB and H4's 256 GiB cannot support indefinite archival retention at these rates. A live-state budget is not a history budget.

Physical growth must additionally include receipts, certificates, indexes, retained versions, compaction/write amplification, and temporary snapshots. Pruning reduces local retention but introduces recovery and supplier obligations; it does not demonstrate global historical availability.

For recovery size S and usable transfer rate u, download time is at least `8S/u`. At the proposed sustained link budgets, 1 GiB at 6 Mbit/s takes approximately 23.9 minutes and 16 GiB at 60 Mbit/s takes approximately 38.2 minutes before validation or additional evidence. These lower bounds motivate B-M11's wider candidate ranges, not a claim that either bootstrap succeeds.

During catch-up, let g be the incoming live-data rate in compatible bytes/s and c be the effective download-and-validation service rate. Backlog S drains no faster than `S/(c−g)` when c>g; when c≤g, catching the live tip is not sustainable under this model. Snapshot trust, history authentication, state construction, and safe signing readiness remain separate requirements.

## 7. Degraded and failure scenarios

Normal B-E1/B-E2 latency targets do not apply automatically to these cases. Continue measuring all resource and outcome metrics; label the condition and its relationship to the candidate's eventual fault/network envelope.

| ID / fixture | Required observation and proposed criterion | Security and recovery boundary |
| --- | --- | --- |
| B-F01: missed or invalid proposal | Inject one and then three consecutive failed opportunities where the candidate has such opportunities. Measure longest no-finality interval, repeated traffic, and time to next finalization. Proposed post-restoration target: ≤5–30 s. | Replacement must use the same validity/finality rules. Slots, backups, or timeouts are not selected by this fixture. |
| B-F02: delay/loss/reordering | Apply B-N2/B-N3 and separately duplication/reordering. Report latency degradation factor, expired work, queue peaks, and retransmission bytes. | No automatic finite finality promise when the chosen delivery assumptions fail. Authentication and validity checks remain required. |
| B-F03: partition and reconnect | Apply B-N4 with disclosed membership/control split. After required connectivity and participation return, propose next finalization ≤5–30 s and stable service ≤1–10 min, subject to backlog accounting. | No progress promise in every component. Any conflicting finalized histories are a safety incident, not successful recovery. Bounds await a protocol recovery argument. |
| B-F04: invalid/adversarial ingress | Apply existing 1%/10% malformed, bad-signature, and unauthorized cases separately, then offered-load sweep through 1.2× reference capacity. | Propose bounded memory and no bypassed checks; report valid-traffic latency, CPU, rejection stage, and resource isolation. These percentages do not bound adversarial capability. |
| B-F05: unavailable data/proof/executor | Withhold required body, witness, proof, or result separately. Measure detection, retained work, supplier switching, and stalled roles. | No finalized-success claim without required evidence. No automatic trusted fallback. Recovery deadlines remain unresolved until alternatives and trust are defined. |
| B-F06: regional/provider or collector loss | Remove each designated dependency group separately; record lost duty share, control traffic redistribution, and remaining capacity. | Identity counts are insufficient; report correlated operators/services and whether the candidate remains inside its progress envelope. |
| B-F07: restart and stale signer | Restart during signing, persistence, or catch-up; separately consider lost signing state and duplicate devices. Report time/bytes to safe readiness. | Resource targets cannot authorize signing from stale state. No claim of safe recovery without the mechanism required by G-08. |
| B-F08: key/membership migration | Apply B-M15 and an additional hypothetical dual-algorithm coexistence case. Count both evidence paths and old-history verification. | No migration policy or extra algorithm is adopted. Historical authentication and downgrade rules remain open. |
| B-F09: mobile suspension | Apply H3 offline intervals and background suspension; report stale results, missed duty if duty was explicitly assigned, energy, and catch-up. | Light-client feasibility does not establish continuous voting feasibility; OS scheduling and availability assumptions must remain explicit. |

Finite executions with no safety violation cannot establish safety. The proposed performance plan complements, and cannot replace, the safety, validity, and liveness arguments required by G-01–G-09.

## 8. Viable alternatives and their consequences

| Alternative | Budget advantage to investigate | Costs, failure, decentralization, and PQ implications |
| --- | --- | --- |
| Lower rate with full local execution | Reduces data and execution burden without changing the validity trust boundary. | Sacrifices capacity; eligibility and overload policy still affect access. Weak upload and certificates may remain limiting. |
| Longer block interval with comparable sustained rate | Amortizes fixed per-block evidence. | Larger proposal bursts, additional waiting, slower failure detection depending on mechanism, and more memory. Does not reduce transaction history growth. |
| Full-set versus committee duties | Committees may reduce active messages and average work. | Capture/selection risks, peak assigned-duty requirements, churn and roster traffic, and PQ randomness/selection gaps must be quantified. No committee size or acceptable risk accepted. |
| Gossip/tree/collector dissemination | Different replication paths can redistribute producer upload and total traffic. | Relay or collector concentration, targeted denial of service, latency depth, and failover traffic must be counted. A topology change is not free bandwidth. |
| Local execution versus specialized proofs versus general validity proofs | Specialized proofs can support their actual claims; general proofs might reduce repeated execution if developed and justified. | Supplied W6 circuits do not certify W4 atomic exchange or arbitrary transitions. Prover latency, concentration, artifact trust, privacy, PQ soundness, and data availability remain separate. |
| Executor attestations | May reduce local computation. | Adds an explicit honesty/control assumption; performance gains cannot be reported as equivalent independent validation. |
| Inline versus registered validator keys | Registered keys can reduce repeated control-message bytes. | Requires authenticated registration, transitions, cache misses, key rotation, and bootstrap. Does not alter the supplied inline-key transaction baseline. |
| Pruning versus local archive | Pruning can fit bounded storage. | Changes historical service and recovery dependencies. Archive concentration and supplier exit become measurable concerns. |
| Mobile light verification versus mobile voting | Scoped claims can require less data and uptime. | Distinct capabilities; headers or inclusion evidence do not automatically verify successful execution or availability. Large PQ certificates may still dominate catch-up. |

The supplied cryptography review does not establish compact PQ vote aggregation, a randomness beacon, a complete PQ leader-selection construction, or whole-system PQ security. Signature, proof, hashing, transport, migration, and historical-key assumptions must retain separate accounting. Its recursion configuration value is not evidence of a particular quantum security level or proving deadline.

Resource limits also affect effective control. If weak nodes can receive data but cannot relay, produce, recover, or independently verify it within duties, low nominal voting cost can coexist with concentrated production and service dependence. Report assigned versus completed duties at fixed qualifying resource as required by IA-01; faster hardware must not be mistaken for an approved basis of consensus influence.

## 9. Future measurement and acceptance plan

This section specifies later evidence; it does not authorize executable work.

1. **Freeze a reviewable case.** Identify the exact workload, semantic rules, observer claims, device model, software/artifact versions, N/M/q, control grouping, topology, network matrix, storage device, data duties, and cryptographic variants. Instantiate the guarantee draft's assumptions. Unresolved critical parameters make the case incomplete.
2. **Select thresholds before evaluation.** Choose one point from the proposed throughput/latency/resource ranges and state whether the case evaluates production, voting, full verification, light verification, archival service, or combined roles. Include all co-located duties.
3. **Apply analytical exclusion first.** Compare mandatory payload, certificate, storage, and synchronization lower bounds with budgets. Mark an excluded pairing as incompatible under its assumptions; do not compensate by silently changing role or trust.
4. **Specify future observation windows.** Proposed normal windows are 10-minute warmup plus 60-minute observation, repeated three times with disclosed workloads/seeds and initial state. This is a reproducibility proposal, not sufficient evidence of long-term reliability. Extend low-volume runs or label tail percentiles inconclusive; propose at least 10,000 completed observations for a p99 evaluation.
5. **Record a complete cohort and resource ledger.** Collect stage timestamps, verified outcome, original request identity, eligibility changes, pending age, all traffic categories, CPU, memory, durable I/O, state/history growth, and duty assignment/completion. Record instrumentation overhead and clock uncertainty.
6. **Evaluate normal and degraded cases separately.** Keep baseline, contention, malformed traffic, loss, outages, transitions, and recovery identifiable before combining them. Report every attempted case and failed threshold. Avoid averaging away the weakest region or overloaded proposer.
7. **Report uncertainty and limitations.** Give per-run and per-observer distributions, quantile-estimation method, sample sizes, temporal dependence, and measurement error. An aggregate p99 must not conceal a consistently slower region or role. Unknown costs and untested failure modes remain explicit.
8. **Issue a scoped finding.** Use `analytically excluded`, `not yet evaluated`, `measurement incomplete`, or `meets selected proposed envelope under stated conditions`. No result alone approves an architecture, establishes universal safety, or proves decentralization.

For documentation acceptance now, each B-M metric has units, endpoints, workload/environment inheritance, and a proposed acceptance method. For later technical acceptance, the required mechanisms and evidence must exist; this draft supplies neither benchmark results nor a feasible selected protocol.

## 10. Unresolved owner decisions and evidence gaps

All records below remain open. The existing scope agreement is carried forward unchanged.

| ID | Decision requiring explicit agreement | Evidence or consequence |
| --- | --- | --- |
| B-D01 | Which event does the approximately one-second ambition target: schedule, block cadence, or finalized outcome? | Select among B-T1–B-T3 or revise them; a protocol dependency/timing argument remains necessary. |
| B-D02 | Which workload rate, latency, success fraction, and burst-admission targets should become requirements? | Choose concrete points, not ranges interpreted after measurement; W1 and W4 must both remain covered. |
| B-D03 | Which roles must fit H1/H2/H3, and is independent mobile verification launch-critical? | Resolve ROLE-07–ROLE-09 and the bandwidth/retention contradictions before promising weak-device voting or production. |
| B-D04 | Which geography, link, loss, clock, and fault envelopes are mandatory? | Synthetic RTTs are not geographic evidence. Phase 2 must establish safety/progress assumptions and thresholds. |
| B-D05 | Which validator scales and full-set/committee comparisons merit continued analysis? | N/M fixtures do not select participation or acceptable capture risk. Control and quorum rules remain missing. |
| B-D06 | What admission, queue, fairness, expiry, and overload behavior is acceptable? | Needed to interpret inclusion bounds, burst rejection, and contended exchange outcomes. |
| B-D07 | What retention, state-growth, offline horizon, bootstrap trust, and archive-service requirements apply? | Determines whether storage/runway/catch-up targets are compatible with independent recovery. |
| B-D08 | Which signature, key-distribution, PQ security, and migration policies should candidates satisfy? | Complete costs and dependency-specific security evidence are missing; ML-DSA variants are comparison inputs only. |
| B-D09 | Are separate executors/provers or alternative validity mechanisms permissible? | Must state trust, proof statements, data duties, supplier diversity, and failure behavior. No general validity construction is supplied. |
| B-D10 | Which resource reserves, device representativeness, test duration, and statistical acceptance rules should be adopted? | Current percentages and durations are planning proposals with no empirical calibration. |
| B-D11 | Which priority wins when targets conflict? | Compare lower throughput, longer timing, stronger devices, different roles, or explicit additional assumptions. Preserve payments plus exchange unless the owner changes scope. |

Critical missing evidence includes operation timings on exact hardware, full encodings, protocol message/round structure, certificate rules, state and storage amplification, realistic latency/provider distributions, recovery authentication, proof sizes/costs, and representative financial demand. External protocol performance comparisons would require additional primary evidence and matched assumptions; none are asserted here.

## 11. Completion mapping to issue #4

| Scope or acceptance item | Coverage in this document | Remaining status |
| --- | --- | --- |
| Separate slots, block intervals, inclusion, execution/proofs, and finality | TIME-01–TIME-09; B-T1–B-T3; sections 4–5 | Definitions and thresholds proposed; timing interpretation open. |
| Sustained/burst throughput and latency percentiles | B-E1–B-E4; B-M01/B-M02; complete-cohort reporting and burst analysis | No measured capacity or accepted service target. |
| Bandwidth, memory, computation, storage, and synchronization | B-M03–B-M15; accounting and lower bounds in section 6 | Unknown implementation costs remain visible. |
| Targets tied to workloads, roles, scale, geography, and network | Sections 3–5 establish explicit inherited case tuples | Geography is synthetic; real deployment representativeness unresolved. |
| Votes, proposals, certificates, PQ, availability, and recovery included | Section 6 traffic ledger, certificate sensitivity, sections 7–8 | No compact certificate or proof-based availability shortcut assumed. |
| Compare envelopes and sensitivity to count, block size, delay, and crypto/proofs | B-E1–B-E4; sections 6 and 8 | Analytical exclusions and alternatives, not benchmark comparisons. |
| Every metric has units, endpoints, workload, environment, acceptance method | Sections 2–5 and common plan in section 9 | Future cases must instantiate unresolved mechanisms and select concrete thresholds. |
| Distinguish normal and degraded/failure operation | B-N fixtures, B-F01–B-F09, section 9 | Normal deadlines do not silently extend to partitions or faults beyond assumptions. |
| Identify one-second ambition as initial and unresolved | Sections 1 and 4.1; B-D01 | No interpretation accepted. |
| Numerical proposals include rationale, uncertainty, and feasibility questions | Sections 4–6 and B-D01–B-D11 | No new numerical production target approved. |
| Separate evidence, estimates, and unknowns | Section 1 labels, source-attributed sizes, explicit synthetic arithmetic | No implementation, experiment, fabricated evidence, or new research claimed. |

The documentation deliverable is complete for human review. Subsequent Phase 1 prioritization can use the exposed conflicts; architecture selection and numerical acceptance remain separate owner decisions.

## References and provenance

- [Project README](../../README.md): goals, initial timing ambition, research process, and candidate-only architecture status.
- [Financial workloads and scope](financial-workloads-and-scope.md): recorded scope agreement, W1–W6 definitions, synthetic workload overlays, authorization sizes, and outcome boundaries.
- [Participant roles and device profiles](participant-roles-and-device-profiles.md): role/data contracts, H1–H4 fixtures, and weak-device bandwidth constraints.
- [Consensus guarantees and decentralization criteria](consensus-guarantees-and-decentralization.md): definitions, assumption register, conditional guarantees, recovery obligations, IA-01, and control/dependency measurements.
- [Cryptography and specialized proof implementation review](../research/crypto-zk-implementation-review.md): supplied findings with commit-pinned primary-source provenance; no new inspection is claimed here.
- NIST, [FIPS 204: Module-Lattice-Based Digital Signature Standard](https://csrc.nist.gov/pubs/fips/204/final), Table 2: signature/public-key sizes as attributed in the supplied documents; not performance evidence.
- [Issue #4](https://github.com/RinjaniChain/consensus/issues/4): scope and completion criteria.
