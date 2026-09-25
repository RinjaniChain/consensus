# Requirement tradeoffs and proposed priorities

Status: **Draft for human review — no new priority ordering, architecture, or production target is approved.** Version 0.1.

Document path: `docs/requirements/tradeoffs-and-priorities.md`.

Related work: [issue #5](https://github.com/RinjaniChain/consensus/issues/5), [Phase 1 roadmap](../../README.md#phase-1--scope-and-measurable-requirements), [workloads](financial-workloads-and-scope.md), [roles and devices](participant-roles-and-device-profiles.md), [guarantees](consensus-guarantees-and-decentralization.md), and [resource budgets](performance-and-resource-budgets.md).

## 1. Purpose and decision status

This document identifies incompatible or competing requirements, compares requirement packages, and proposes an order for resolving conflicts. Packages describe desired capabilities and constraints; they do not select a consensus protocol.

**Payments and atomic asset exchange remain the agreed starting scope.** The approval record is in the [workload draft, section 10](financial-workloads-and-scope.md#10-open-decisions-for-owner-review): on 2026-09-25, the owner selected both from the beginning. Same-chain W4 is the working exchange model, while consent, cancellation, expiry, fee treatment, and conflict semantics remain proposed. General programmable finance and cross-chain exchange remain unapproved. A simpler package in this document still includes payments and atomic exchange.

No accepted priority ordering is recorded in the supplied documents. The approximately one-second ambition has no approved observation endpoint or operating envelope. Publication, review, merge, and issue closure approve neither this ordering nor any mechanism or numerical target.

### Evidence labels

- **Recorded agreement:** the scope decision above, with its existing approval record.
- **Project constraint or goal:** an instruction or direction in the [README](../../README.md), distinguished below from an implemented guarantee.
- **Supplied finding:** evidence reported in an existing supplied document; not independently re-inspected here.
- **Analysis/proposal:** the conflict assessments, packages, priorities, and criteria introduced here.
- **Synthetic fixture:** an illustrative workload or scenario, not an experiment, forecast, or benchmark.

No implementation, executable experiment, new source inspection, or external protocol comparison was performed. Unavailable evidence remains an explicit limitation.

## 2. Definitions and common assumptions

The operational definitions DEF-01–DEF-18 in the [guarantee draft](consensus-guarantees-and-decentralization.md#2-glossary-and-observable-boundaries) remain proposed definitions. This document uses them consistently without converting them into approved requirements.

| Term | Meaning in this analysis |
| --- | --- |
| Requirement package | A proposed combination of scope, roles, service expectations, resource ceilings, and trust boundaries. A package is not a feasibility finding. |
| Non-negotiable | Either an existing task/project constraint or a proposed acceptance gate explicitly labeled as such. A preferred target cannot silently acquire this status. |
| Target | A desired measurable outcome under named conditions, potentially relaxable by explicit agreement. |
| Relaxation | A documented change to a target, operating envelope, role obligation, or assumption. Narrowing the population covered by a claim is a relaxation even if its headline number stays unchanged. |
| Effective control | Control of voting, production, transaction selection, or essential services, grouped by operator/dependency where evidence permits. Distinct keys do not establish independent control. |
| Independent verification | Checking a named claim from authenticated evidence under disclosed bootstrap, data, cryptographic, and protocol assumptions. RPC access and delegation are separate capabilities. |
| Fairness | A specified distribution of assigned opportunities, completed duties, or transaction service among comparable eligible participants. These are different properties; equal opportunity per key is not necessarily fair allocation per owner or resource. |
| Successful settlement | A verified finalized successful on-chain outcome. Finalized inclusion or failure alone is insufficient; W4 requires the agreed atomic effects. |
| Viable alternative | A coherent option for continued requirements analysis, with explicit missing conditions. It does not mean a working protocol or demonstrated deployment. |

The following assumptions apply to all matrix rows unless narrowed explicitly:

| ID | Assumption and unresolved boundary |
| --- | --- |
| TP-A01 | Compare W1 payments and W4 exchanges separately and in the proposed B-WM mixture. Neither an exchange-free benchmark nor optional W3/W5/W6 can replace the baseline. |
| TP-A02 | H1–H4, B-N1–B-N4, B-E1–B-E4, and all numerical budget ranges remain synthetic proposals. Their representativeness and production values are unresolved. |
| TP-A03 | Admission, weight, fault thresholds, corruption timing, synchrony, clocks, membership transitions, and bootstrap policy remain unselected. Each future candidate must instantiate A-F through A-I in the guarantee draft. |
| TP-A04 | Compare alternatives at matched financial semantics, workload, role claims, fault assumptions, and network envelope. If any changes, identify it before claiming an improvement. |
| TP-A05 | Count transaction, control, availability, storage, and recovery costs together, including peak duty and co-located roles. Unknown costs are not zero. |
| TP-A06 | Cryptographic variants are cost/security comparison inputs, not adopted algorithms. No compact PQ vote aggregation, general execution-validity proof, or complete PQ selection mechanism is established by the supplied material. |
| TP-A07 | Operator independence and correlated failure exposure are uncertain. Report grouping evidence and sensitivity; do not assume identity count, stake ownership, hosting, and signing control coincide. |

## 3. Constraints, proposed gates, and preferences

| ID / status | Property | Implication for prioritization |
| --- | --- | --- |
| TP-C01 — recorded agreement | Payments plus atomic asset exchange from the beginning | Lower ambition may reduce capacity or optional features, but cannot silently remove exchange. |
| TP-C02 — existing process constraint | Research/documentation now; explicit owner agreement for architecture and production requirements | No package, committee, admission mechanism, framework, or experiment is authorized by publication. |
| TP-C03 — existing project directions | Deterministic finality, permissionless participation, PQ architecture, and consensus influence not directly assigned by computational power | Candidates must explain compatibility. Precise fault models, supported roles, security levels, and acceptance thresholds remain open; feasibility is not established. |
| TP-G01 — proposed acceptance gate | Preserve finalized-history safety and valid financial outcomes within the declared envelope | Reject a candidate with an admissible conflicting-finality or invalid-settlement counterexample. Additional speed cannot compensate for that failure. Includes proposed G-01–G-03. |
| TP-G02 — proposed acceptance gate | Missing evidence cannot silently become trusted success | Required data, verification, recovery, and signing-state checks must remain enforced under overload or provider loss. Progress may stop according to the declared model. |
| TP-G03 — proposed acceptance gate | Claims include complete assumptions, dependency costs, and PQ gaps | A smaller resource number achieved by offloading work must identify the receiving role and added trust. Unresolved critical security dependencies prevent a positive security claim. |
| TP-P01 — unresolved preferences | Timing endpoint, throughput, weak/mobile roles, geographic envelope, retention, inclusion service, concentration limits, and operational simplicity | These require explicit ranking and concrete criteria. No numerical value in an earlier draft is already mandatory. |

TP-G01–TP-G03 are proposed non-negotiable technical gates for later candidate acceptance. They are not additional owner decisions. Safety under arbitrary network delay, exact inclusion guarantees, acceptable bootstrap trust, and acceptable selection risk require separate agreement.

## 4. Tradeoff matrix

Every row is original analysis under TP-A01–TP-A07. Consequences are conditional on the named mechanism; no candidate is adopted.

| ID / conflict | Additional assumptions and affected roles | Consequences across security, decentralization, performance, network, and PQ | Viable alternatives and measurable comparison |
| --- | --- | --- | --- |
| TP-T01: short timing versus geographic participation | Proposal/validation paths contain dependent delivery and processing steps. Affects distant voters, low-upload proposers, full verifiers, and payment/exchange clients. | Short deadlines can increase missed duties or favor well-connected operators. Raising timeout frequency adds traffic and verification work; it does not establish faster finality. Excluding slow regions improves a reported latency only by narrowing participation. PQ authentication adds serialization and verification obligations. | Compare longer normal latency with broad participation; short cadence with separately longer settlement; or a narrow service envelope explicitly labeled as such. Report per-region TIME-02/TIME-08, assigned/completed duties, critical-path bytes and steps, and recovery latency. A narrow envelope needs a separate geographic-policy decision. |
| TP-T02: weak/mobile availability versus continuous duties | Intermittent devices may suspend or lose connectivity; inactivity can be correlated. Affects voters, mobile verifiers, key custodians, and users considering delegation. | Low average CPU does not establish deadline completion, safe restart, or continuous availability. Hosted signing can transfer control to providers. Scheduled duties add assignment and recovery requirements; PQ certificates and membership history may dominate rejoin cost. | Compare always-on weak stationary voting, intermittent independent light verification, and conditional scheduled mobile voting. Keep RPC access separate. Measure missed duties, peak RAM/energy/traffic, safe readiness, catch-up bytes, and provider dependence. Continuous mobile voting requires separate feasibility evidence. |
| TP-T03: throughput versus bandwidth, storage, and independent verification | Roles downloading every body incur at least one-copy payload cost; local execution and retained history add work. Affects producers, voters, full verifiers, archives, and new entrants. | Higher offered rate can overwhelm upload, verification, or recovery before download alone fails. Pruning shifts historical availability duties. Larger batches can hide economic work behind low transaction counts. Larger PQ authorization payloads persist in replicated traffic and history. | Compare lower W1/W4 rates with local verification; longer intervals to amortize fixed control costs; stronger producer devices while retaining independently verified outcomes; and explicit pruning with recoverable authenticated data. Report complete traffic ledger, successful effects, backlog, storage runway, and catch-up margin. Longer intervals do not reduce per-transaction history growth. |
| TP-T04: economic Sybil resistance versus stake/delegation concentration | An admission resource must resist cheap multiplication. If stake is proposed, ownership, borrowed control, custody, and delegation differ. Affects small applicants, capital owners, operators, delegates, and users exposed to censorship. | Stake weighting can make splitting neutral only under a specified allocation rule; it does not distribute concentrated ownership. Minimum holdings, fixed operating costs, lockups, or penalties can encourage pooling. Delegation may improve access while concentrating signing. Network/uptime advantages affect realized duties even when compute does not assign weight. PQ keys and transition evidence add entry costs. | Compare stake-based direct participation with optional delegation under separately disclosed control assumptions; another objectively checkable non-computational scarce-resource rule only once its issuance/control model is specified; and lower operational requirements that reduce pressure to delegate. A permissioned roster is a contrast that fails the permissionless direction, not an equivalent solution. Measure lifecycle costs, ownership/operator shares, switching friction, and fixed-resource splitting sensitivity. No non-stake mechanism is established by supplied evidence. |
| TP-T05: proposer rotation versus splitting, fairness, and liveness | Consecutive production is undesirable, but keys are cheap and operator attribution is incomplete. Affects producers, small validators, users awaiting inclusion, and any attribution authority. | A per-key cooldown can be evaded through splitting and may disqualify available honest producers during outages. Per-operator limits require credible attribution and may introduce gatekeeping. Predictable eligibility can expose targets to denial of service. Randomized alternatives require bias, grinding, and PQ selection analysis. | Compare no cooldown with reported run lengths; resource-weighted assignment without a consecutive cap if the admission model supports it; and cooldown rules with explicit fallback and splitting analysis. Compare assigned versus completed share, maximum observed runs, inclusion service, and time to progress under eligible-set depletion. Do not equate producer rotation with fair transaction ordering. |
| TP-T06: committee scalability versus capture | If sampling is used, define population, weights, selection, corruption timing, duty duration, and failure threshold. Affects assigned voters, non-assigned applicants, light verifiers, and randomness providers. | Smaller active sets may reduce messages while increasing dependence on selection and concentrated control. Adaptive targeting or biased selection can invalidate simple capture estimates. Average duty relief does not reduce peak duty costs. Full participation still requires fault analysis and may have large PQ certificates; committees add PQ randomness/selection obligations. | Compare full participation at lower rate or longer timing, broader duty sets, and sampled duties with explicit risk limits. Measure peak and total bytes, verification counts, duty access, per-selection and horizon capture bounds, and regional/provider failure exposure. No acceptable risk or committee size is selected. |
| TP-T07: role separation versus specialized-operator dependence | Distinguish production separation with local execution from proof verification or executor attestations. Affects voters, producers, executors, provers, data suppliers, and users. | Local execution keeps verification duties local but may exclude weak devices. Proof-based validity adds statement, artifact, soundness, proving-deadline, and witness obligations. Attestations add an honesty assumption. Specialized providers can concentrate censorship and outage risk even with distributed voters. Neither proofs nor signatures establish data availability. | Compare combined roles; separate production with local voter execution; and explicitly conditional proof/attestation alternatives. Measure critical-path proving/execution, provider switching, independent recovery, invalid/withheld-result behavior, and peak producer resources. Supplied specialized circuits cannot satisfy general W1/W4 transition validity. |
| TP-T08: PQ coverage versus communication and verification budgets | Authentication, commitments, selection, proofs, transport, migration, and historical trust are separate dependencies. Affects every role, especially low-bandwidth voters and returning clients. | Larger authentication evidence can constrain cadence, fan-out, certificate distribution, and storage. Registered keys shift costs to authenticated enrollment and history. Switching algorithms can temporarily add evidence paths. Reducing checks or security objectives to fit a target changes the security claim. | Compare lower traffic/cadence, authenticated registered validator keys, different justified PQ constructions, and explicit migration/coexistence policies. Keep the supplied transaction inline-key baseline unchanged in comparisons unless a new transaction design is declared. Measure bytes and checks by role, rotation/bootstrap peaks, and dependency coverage. Neither compact aggregation nor security equivalence between variants is assumed. |
| TP-T09: low local storage versus independent bootstrap and recovery | Pruning or snapshots change which data remains locally available. Historical keys and long offline periods need a canonical-history policy. Affects full verifiers, archives, returning signers, and light clients. | Cheap steady operation may conceal expensive or supplier-dependent entry. Archive loss can prevent replay despite valid commitments. Trusted anchors alter the verification boundary; PQ migration must address old evidence as well as new signatures. | Compare bounded local retention with multiple authenticated suppliers, longer local history, and explicit checkpoint/snapshot policies. Measure retrieval coverage, supplier loss, storage growth, offline horizon, and catch-up while live traffic continues. No trusted checkpoint or archive requirement is adopted. |
| TP-T10: low latency/high utilization versus fair service under overload | Finite service capacity, bursts, invalid traffic, hot state, and W4 expiry affect eligibility. Affects ingress operators, producers, ordinary payers, and contending exchange parties. | High utilization leaves little recovery headroom. Cheap admission can enable resource exhaustion; pricing or quotas can exclude users. Aggregate latency can conceal selective omission. PQ checks can amplify expensive invalid traffic. | Compare lower admitted load with explicit rejection, reserved service headroom, and bounded queues with published eligibility/scheduling policies. Measure complete-cohort outcomes, eligible waiting time, invalid-traffic cost, queue/drain behavior, and exchange expiry/conflict failures. No inclusion or fair-ordering mechanism is selected. |

## 5. Analytical checks that constrain the choices

These are arithmetic or conditional reasoning, not benchmark findings.

**TP-X01: transaction data cannot be removed by reducing voting CPU.** The supplied workload/budget drafts use 256-byte bodies and inline keys, giving W1 payload subtotals of 5,517 or 7,475 bytes and W4 subtotals of 10,778 or 14,694 bytes. Signature/key sizes are attributed to [FIPS 204, Table 2](https://csrc.nist.gov/pubs/fips/204/final); the wrapper assumptions come from the [supplied implementation review](../research/crypto-zk-implementation-review.md). No signature family is selected.

For rate λ and mean payload b, one-copy payload traffic is `8 × λ × b` bit/s. At the existing illustrative 1,000 W1/s, that is 44.136 or 59.8 Mbit/s, already above H1's synthetic 10 Mbit/s download limit. A package must lower rate, change duties, change the device envelope, or justify a different encoding/construction. It cannot claim that low-cost voting alone resolves the contradiction.

**TP-X02: cadence and throughput affect different costs.** With fixed per-record evidence c bytes and interval τ seconds, that evidence contributes approximately `c/τ` bytes/s per delivered copy. Increasing τ may reduce this component but leaves transaction traffic `λb` unchanged and increases accumulated proposal payload and waiting. This simplification excludes retries and variable certificates; use the actual candidate message graph before deriving a limit.

**TP-X03: local storage and recovery must be jointly feasible.** Raw retained payload grows as `86,400 × λ × b` bytes/day before receipts, certificates, indexes, and state. For recovery backlog S, effective service c, and continuing live growth g in compatible units, the simple catch-up bound is `S/(c−g)` when c exceeds g. If c does not exceed g, that model cannot reach the live tip. Pruning and snapshots may change S or duties, but their trust and availability requirements must be exposed.

**TP-X04: committee risk needs a horizon.** Let pᵢ denote a justified bound on capture at selection i under the actual sampling and adversary model. The probability of at least one capture over K selections is at most `min(1, sum(pᵢ))` by the union bound; independence is not required for this bound. The expression `1−(1−p)^K` requires independent, identically distributed capture events and must not be assumed for reused memberships or adaptive corruption. No pᵢ, K, threshold, or acceptable risk is supplied. Capture also needs mapping to the particular safety, progress, or censorship failure it enables.

**TP-X05: rotation should be compared at fixed resource.** In a synthetic fixture, one controller replaces one eligible identity with several identities while keeping total qualifying resource fixed. Compare assigned opportunity, completed production, and eligibility after a cooldown. An advantage caused only by splitting is a failure of a proposed split-neutral rule; finite observed runs alone do not prove or disprove assignment fairness. Resource-weighted assignment and operator-level limits require different assumptions and cannot be treated as interchangeable fixes.

## 6. Candidate requirement packages

All packages preserve payments and atomic exchange, explicit financial validity, and the need for PQ dependency analysis. None resolves admission, fault thresholds, finality rules, or architecture. They are coherent research alternatives, with feasibility open.

| Package | Proposed requirements emphasis | Deliberate tradeoff and alternatives | Main acceptance blocker |
| --- | --- | --- | --- |
| TP-K01: conservative independent verification | Bounded payment/exchange semantics; local execution as a reference; assess weak stationary verification/voting under a declared broad network envelope. Treat intermittent mobile light verification separately. | Relax throughput and normal timing first. Compare combined roles with separate production/local execution. Optional programmable workloads and general proofs are not baseline obligations. | Unknown execution/control costs, admission model, geographic representativeness, and bootstrap policy. Lower load alone does not establish affordable voting or production. |
| TP-K02: cadence-oriented service | Give the initial timing ambition a precise normal-operation endpoint while keeping settlement and disruption recovery separate. Retain full W1/W4 outcome checks. | Reduce payload or offered rate to preserve short paths; require explicit owner agreement before narrowing geography or raising device requirements. Local verification remains a comparison case. | No protocol dependency graph or evidence establishes the desired cadence. Deadlines may exclude distant or weaker operators. |
| TP-K03: higher-capacity local verification | Explore higher W1/W4 service rates with complete local verification on declared stronger devices; retain independent non-voting and light-client roles. | Accept a higher operational barrier only if approved. Compare stronger devices, lower rate, and longer intervals before introducing new validity trust. | Upload, archive/recovery growth, concentration, and unsupported weak-device duties. H4 is a fixture, not an approved operator tier. |
| TP-K04: distributed light-duty research | Seek lower individual voting/verification work through separately evaluated sampled duties or alternative validity mechanisms. These are independent axes, not a mandatory combination. | Potentially retain broader participant access at higher aggregate load, while accepting extra selection, provider, proof, or attestor obligations only through explicit decisions. | Capture risk, peak duty, general proof availability, PQ selection, supplier dependence, and failure recovery are unresolved. Specialized W6 proofs do not close these gaps. |

**Proposed research preference:** begin with TP-K01 as the reference package and compare TP-K02/TP-K03 at matched assumptions. Keep TP-K04 conditional until its additional security and resource obligations are specified. This favors an interpretable cost baseline; it does not adopt local execution, exclude later role separation, or establish a winning protocol.

No package is declared globally superior. A candidate dominates another only if it is no worse on agreed dimensions under equivalent assumptions and strictly better on at least one. Moving from independent verification to service trust, or excluding a region, invalidates a simple performance-only dominance claim.

## 7. Proposed priority ordering and relaxation rules

This ordering is a proposal for owner agreement. It is not a weighted score: capacity cannot offset failure of an agreed safety gate, and uncertainty cannot be scored as success.

| Rank / ID | Proposed priority | Conflict resolution |
| --- | --- | --- |
| 1 — TP-P10 | Accurate guarantees and valid finalized financial outcomes | Resolve safety, validity, atomicity, evidence, and recovery contracts before accepting throughput or latency claims. Preserve the approved starting scope. |
| 2 — TP-P20 | Defensible permissionless influence and PQ dependency coverage | Require explicit admission/control and cryptographic models. Do not trade these project directions away to meet a timing fixture. Detailed thresholds remain open. |
| 3 — TP-P30 | Independent verification, recoverability, and observable control distribution | Specify which roles can verify and recover, at what cost and with which suppliers. Evaluate hardware advantages in completed duties separately from assigned influence under IA-01. |
| 4 — TP-P40 | Conditional progress and useful transaction service | Define when progress resumes, how eligible requests receive service, and how overload or exchange conflicts affect outcomes. Safety-first does not make an indefinitely stalled design acceptable within its promised progress envelope. |
| 5 — TP-P50 | Normal timing and sustainable W1/W4 throughput | Choose measurable endpoints and jointly feasible resource envelopes. Timing versus capacity within this tier depends on owner product priorities. |
| 6 — TP-P60 | Optional capabilities and mechanism complexity | Defer unapproved workloads and avoid additional mechanisms unless their benefits justify their evidence, operational, and security obligations. |

The relative importance of broad geographic participation, weak-device voting, low capital barriers, and short latency is not fully ordered here; these dimensions need owner choices in section 9.

### Conditional relaxation ladder

| ID | Candidate relaxation and trigger | Consequence and required boundary |
| --- | --- | --- |
| TP-R01 | Lower offered/admitted throughput when complete bandwidth, compute, storage, or catch-up accounting fails | Longer queues or more explicit rejection unless demand is lower. Preserve payments and exchange; report service outcomes and inclusion policy. Proposed first resource adjustment. |
| TP-R02 | Lengthen cadence or normal finality targets when critical-path or fixed-evidence costs do not fit | More waiting and potentially larger proposal bursts; transaction byte rate and retained history do not automatically fall. Revise exchange expiry assumptions explicitly. |
| TP-R03 | Reduce optional workload breadth or bound operation size more tightly | Less application flexibility. Removing atomic exchange is outside the authorized baseline; bounding its semantics requires product agreement. |
| TP-R04 | Change retention or bootstrap policy when storage/replay costs fail | Greater external data or anchor dependence. Requires explicit recovery, supplier, historical-key, and trust decisions; cannot be advertised as unchanged independence. |
| TP-R05 | Raise device requirements or narrow which roles fit weak/mobile devices | May improve deadline completion while reducing independent influence or access. Requires owner approval of the capability loss and revised concentration analysis. |
| TP-R06 | Narrow geographic/network service coverage | Potentially shorter measured latency with exclusion or degraded service elsewhere. Requires an explicit participation-policy decision and per-region reporting. Never silently discard slow observations. |
| TP-R07 | Add committees, delegation, specialized proofs, or attestors | May redistribute costs, but changes selection, control, or validity assumptions. This is an architectural decision, not an automatic target relaxation. Requires its own evidence and approval. |

TP-R01–TP-R03 are proposed first comparisons, not automatic changes. TP-R04–TP-R07 require explicit reconsideration of trust or participation. Missing data, failed authentication, conflicting finality, or one-sided exchange cannot be treated as successful service to satisfy a budget. PQ security objectives cannot be reduced without a separately recorded change to the security policy.

Every proposed relaxation should record the failed criterion, matched-case evidence, changed requirement, affected stakeholders, alternatives considered, new failure modes, and owner decision. Existing numerical draft ranges must be resolved before evaluation, not selected after observing a favorable result.

## 8. Proposed measurable review criteria

These criteria specify future evidence and documentation checks. They authorize no executable work and introduce no numerical production thresholds.

| ID | Criterion and evidence | Rejection or unresolved condition |
| --- | --- | --- |
| TP-M01 | Each comparison fixes workload, role claims, network/fault envelope, cryptographic variant, device, membership/control scale, and bootstrap assumptions | Unmatched assumptions prevent a comparative performance conclusion. |
| TP-M02 | W1 and W4 report submitted/admitted/finalized-success rates, outcome counts, p50/p95/p99, pending ages, and per-region results using TIME definitions | Inclusion-only, transfer-leg-only, or survivor-only reporting cannot establish successful exchange service. |
| TP-M03 | A complete per-role ledger fits selected simultaneous bandwidth, CPU, memory, storage, energy, and peak-duty budgets; recovery service exceeds live demand in the declared catch-up model | Any mandatory lower bound above budget excludes that pairing. Unknown costs leave feasibility unresolved. |
| TP-M04 | Entry, activation, exit, and release have objective rules; fixed-resource compute/splitting analysis covers both voting and proposal assignment | Discretionary gates or unexplained splitting/grinding advantages conflict with the proposed permissionless/influence criteria. Economic security remains separately unresolved. |
| TP-M05 | Report voting, production, delegation where applicable, provider dependence, and independent-verification coverage using D-01–D-09 | Key counts alone cannot establish decentralized control. Report grouping uncertainty, top shares, and HHI only under disclosed groupings. |
| TP-M06 | Rotation analysis specifies fairness unit, assignment expectation, consecutive-run behavior, and progress when eligible producers fail | A cooldown without a splitting and eligible-set-depletion analysis is incomplete. No acceptable run length is set here. |
| TP-M07 | Committee candidates provide a selection/adversary model, capture-to-failure mapping, per-selection/horizon risk, and peak duty costs | No independence assumption without justification; no committee safety claim without an agreed risk policy. |
| TP-M08 | Each separated service has a validity/data contract and analysis of invalid results, withholding, supplier exit, and switching | A trusted fallback or unavailable general proof cannot count as equivalent independent verification. |
| TP-M09 | PQ-01–PQ-07 each have evidence, explicit inapplicability, or a visible gap, including historical authentication and migration costs | One signature family or a proof configuration value cannot establish system-wide PQ security. |
| TP-M10 | Partition, regional/provider loss, stale signer, missing data/proof, overload, and membership/key transition cases preserve declared checks and identify progress conditions | Recovery by revoking finality or weakening validity is an incident/assumption change, not a normal budget pass. |
| TP-M11 | Every proposed priority or relaxation carries status and an explicit approval reference if accepted | Publication, issue closure, and unresolved owner silence cannot serve as architecture approval. |

For synthetic review, consider a region missing proposal deadlines, an operator splitting identities around a cooldown, a captured sampled set, an unavailable prover, and a returning node unable to authenticate old membership. These are reasoning fixtures only. A satisfactory specification must explain each case; no scenario has been executed.

## 9. Unresolved owner decisions and evidence gaps

All records below are open. Suggested starting points are recommendations only.

| ID | Owner question | Proposed starting point and consequence |
| --- | --- | --- |
| TP-D01 | Should TP-G01–TP-G03 become mandatory candidate acceptance gates, and should TP-P10–TP-P60 guide prioritization? | Adopt the ordering for requirements review only, with separately stated exceptions. No supplied approval exists. |
| TP-D02 | Which roles must be independently feasible on weak stationary and mobile devices? | Investigate H1 verification/voting and H3 independent light verification separately; keep scheduled/continuous mobile voting open. Align ROLE-07–ROLE-09 and B-D03. |
| TP-D03 | What event does the one-second ambition mean, and how does it rank against geographic coverage? | Compare normal block cadence with separately reported settlement; do not approve B-T2 by inference. Resolve B-D01/B-D04 and DEC-03/DEC-11. |
| TP-D04 | Which W1/W4 capacity, latency, inclusion-service, and rejection outcomes are product-critical? | Set concrete joint criteria after choosing semantics and roles. Decide timing versus capacity within TP-P50; resolve B-D02/B-D06 and DEC-04. |
| TP-D05 | Which admission-resource alternatives and delegation models should proceed to threat analysis? | Require objective admission, control acquisition, splitting, custody, and exit analysis before selecting stake or another mechanism. Align DEC-06. |
| TP-D06 | Is limiting consecutive production itself required, or is the goal distributed control and transaction service? | Compare no cooldown against candidate rotation rules using control-level fairness and liveness. Do not assume per-key rotation solves concentration. |
| TP-D07 | Is sampled participation acceptable, and what risk horizon and corruption model would govern it? | Retain full participation as a reference; no numerical risk or committee size proposed for approval here. Align DEC-02 and B-D05. |
| TP-D08 | Which extra validity, provider, bootstrap, or archive assumptions are acceptable? | Compare local execution and independent recovery first; record any attestor, prover, snapshot, or checkpoint dependence explicitly. Align DEC-07/DEC-10 and B-D07/B-D09. |
| TP-D09 | What quantum threat horizon, dependency security objectives, and migration obligations are mandatory? | Require dependency-specific assessment rather than selecting a signature family by byte size. Align DEC-09 and B-D08. |
| TP-D10 | Which package should be the next reference, and which relaxations are permissible first? | Recommend TP-K01 and initial comparison of TP-R01–TP-R03. Raising hardware requirements or reducing geographic coverage needs explicit agreement. |
| TP-D11 | What concentration/coverage limits and measurement uncertainty are acceptable? | Use power and dependency views together; do not adopt numerical limits until control attribution and coverage are defined. Align DEC-08. |

Critical evidence gaps are representative financial demand, exact encodings, execution and cryptographic timings, message/round structure, actual network/provider distributions, admission security, selection and corruption models, storage amplification, recovery authentication, and proof costs/statements. The supplied material cannot establish a feasible package, capture probability, device-support guarantee, or externally benchmarked protocol advantage. Those conclusions remain provisional until the corresponding evidence exists.

## 10. Completion mapping to issue #5

| Scope or acceptance item | Coverage | Status |
| --- | --- | --- |
| Short timing versus geography and weak-device availability | TP-T01/TP-T02; TP-X02; TP-R02/TP-R05/TP-R06 | Assumptions, affected roles, alternatives, and criteria supplied; coverage priorities open. |
| Throughput versus bandwidth, storage, and independent verification | TP-T03/TP-T09; TP-X01–TP-X03; TP-M03 | Analytical incompatibilities and viable adjustments identified; no capacity claim. |
| Economic Sybil resistance versus stake/delegation concentration | TP-T04; TP-M04/TP-M05; TP-D05 | Conditional stake analysis and alternative requirements supplied; no admission mechanism selected. |
| Proposer rotation versus splitting, fairness, and liveness | TP-T05; TP-X05; TP-M06 | Per-key/control distinctions and failure alternatives supplied; cooldown unapproved. |
| Committees versus capture; separation versus specialist dependence | TP-T06/TP-T07; TP-X04; TP-M07/TP-M08 | Selection-risk accounting and service-trust consequences supplied; mechanisms unresolved. |
| PQ overhead versus communication and verification | TP-T08 and PQ consequences throughout the matrix; TP-M09 | Full dependency accounting required; no signature/proof family selected. |
| Viable packages including simpler alternatives | TP-K01–TP-K04 | All preserve payments and atomic exchange; feasibility remains conditional. |
| Proposed priorities and conditional target relaxation | Sections 3 and 7 | Existing constraints, proposed gates, and unresolved preferences separated. |
| Each material conflict has assumptions, affected stakeholders, and an alternative | TP-A01–TP-A07 plus every TP-T row | Documentation coverage complete for review. |
| Security, decentralization, performance, network, and PQ consequences | Matrix, analytical checks, package blockers, and TP-M criteria | Consequences stated without fabricated evidence or protocol comparisons. |
| Accepted priorities link to explicit approval, otherwise remain proposals | Sections 1, 3, 7, and 9; TP-M11 | No new priority accepted. Only the existing scope approval is carried forward. |

The issue's research/documentation scope is addressed for human review. The next consolidation should carry each unresolved TP-D record forward without converting review or publication into agreement.

## References and provenance

- [Project README](../../README.md): project directions, candidate-only architecture status, and approval process.
- [Financial workloads and scope](financial-workloads-and-scope.md): recorded owner scope decision, W1/W4 baseline, outcome definitions, and authorization fixtures.
- [Participant roles and device profiles](participant-roles-and-device-profiles.md): verification boundaries, H1–H4 synthetic envelopes, availability and role-separation alternatives.
- [Consensus guarantees and decentralization criteria](consensus-guarantees-and-decentralization.md): definitions, assumptions, G records, IA-01, D metrics, and PQ inventory.
- [Proposed performance and resource budgets](performance-and-resource-budgets.md): synthetic budget/network cases, traffic ledger, analytical bounds, and unresolved numerical targets.
- [Cryptography and specialized proof implementation review](../research/crypto-zk-implementation-review.md): supplied findings and commit-pinned primary-source provenance; no new inspection claimed.
- NIST, [FIPS 204](https://csrc.nist.gov/pubs/fips/204/final), Table 2: signature/key sizes as attributed in the supplied documents, not system performance evidence.
- [Issue #5](https://github.com/RinjaniChain/consensus/issues/5): scope and completion criteria.

All new tradeoff assessments, package definitions, priority records, relaxation rules, and review criteria are original analysis and proposals. No implementation, experiment, benchmark, architecture approval, or whole-system security claim is produced by this document.
