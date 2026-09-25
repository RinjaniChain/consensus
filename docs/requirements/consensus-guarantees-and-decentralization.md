# Consensus guarantees and decentralization criteria

Status: **Draft for review — proposed definitions and requirements, not approved protocol guarantees.** Version 0.1.

Document path: `docs/requirements/consensus-guarantees-and-decentralization.md`.

Related work: [issue #3](https://github.com/RinjaniChain/consensus/issues/3), [project roadmap](../../README.md#phase-1--scope-and-measurable-requirements), [financial workloads and scope](financial-workloads-and-scope.md), [participant roles and device profiles](participant-roles-and-device-profiles.md), and [cryptography implementation review](../research/crypto-zk-implementation-review.md).

Payments and atomic asset exchange are the agreed starting scope. Same-chain W4 is the existing draft's working exchange model; detailed semantics remain proposed. General programmable finance and cross-chain exchange remain unapproved. Publication, review, merge, or issue closure does not approve the architecture choices recorded here.

## 1. Purpose, evidence, and decision boundary

This document defines what a future consensus design must mean when it claims safety, deterministic finality, progress, permissionless participation, or decentralization. It supplies a glossary, conditional guarantees matrix, proposed measurement criteria, and unresolved choices for owner agreement. It does not select a consensus family, fault threshold, network model, admission mechanism, signature family, committee, cooldown, proof system, or numerical production target.

Evidence labels used here:

- **Existing agreement:** the payments and atomic-exchange starting scope recorded in the workload draft. Project goals and the research-first process remain as stated in the README.
- **Supplied source finding:** an observation reported by an existing supplied document. This document performs no additional source inspection and does not independently validate that observation.
- **Proposal / analysis:** definitions, obligations, tradeoffs, and acceptance criteria introduced here. They require review and, where architectural, explicit owner agreement.
- **Synthetic fixture:** a hypothetical trace for checking the precision of a requirement. It is neither an experiment nor evidence of a working protocol.

The supplied material contains no selected protocol with a safety proof, liveness proof, or validated performance envelope. Consequently, all guarantee records below are requirements to evaluate, not claims already achieved. Detailed adversarial capabilities and thresholds belong to Phase 2; resource budgets belong to issue #4.

## 2. Glossary and observable boundaries

Definitions are proposed for consistent use across subsequent documents. An observer is a named node or client with a specified verification method; a service response alone is not independently verified evidence.

| ID / term | Operational definition | Observable violation or measurement limitation |
| --- | --- | --- |
| DEF-01: history and conflict | A history is a sequence of protocol-authorized records, including ordered transactions, bound results where applicable, and membership/version changes. Two histories conflict when neither is a prefix of the other under the same chain identity and rules. | Two different records at the same authenticated history position witness conflict. Different local tips of one prefix-compatible history do not. Encoding and position rules remain to be specified. |
| DEF-02: correct participant | A participant follows the selected rules throughout the relevant interval and retains the state required by those rules. | Logs and signed messages can expose deviations, but public silence cannot establish honesty, intent, or correct local execution. Correctness is an assumption in a proof, not a publicly certified identity attribute. |
| DEF-03: safety | No two correct verifiers accept conflicting finalized histories within the declared fault, cryptographic, bootstrap, and transition assumptions. | Authenticated conflicting finality evidence accepted by correct verifiers is a counterexample. A fault outside the envelope defeats applicability of the guarantee; it does not make the conflict harmless. Finite observation without conflict is not a proof. |
| DEF-04: deterministic finality | Once a correct verifier accepts history H as finalized, every subsequently accepted finalized history extends H while the stated assumptions hold. No additional confirmation depth is required to make H irreversible under those assumptions. | Acceptance of a conflicting history violates the claim. Cryptographic failure bounds and any committee-selection risk must be stated separately; the word deterministic does not remove those assumptions. |
| DEF-05: validity | Finalized records satisfy a specified deterministic predicate covering authorization, execution results, membership changes, and whatever availability obligations are chosen. | A finalized record failing that predicate is a validity failure even when every verifier agrees on it. Independent detection depends on access to required data and the selected verification mechanism. |
| DEF-06: liveness | From an admissible state, the protocol eventually extends finalized history when its stated fault and operating conditions hold for the required interval. | An indefinitely stalled eligible execution contradicts eventual liveness. A finite stall alone cannot refute an unbounded eventual claim; bounded-progress claims require a declared deadline and conditions. Empty finalized records may satisfy chain progress without transaction inclusion. |
| DEF-07: transaction eligibility and inclusion | Eligibility is a published predicate covering syntax, authorization, fees/resources, replay status, dependencies, and any expiry rules. Inclusion means the transaction occurs at an identified position; finalized inclusion means that position is covered by accepted finality. | An inclusion proof can establish presence, not successful execution. Exclusion cannot be judged without evidence of eligibility, dissemination, load conditions, and the promised service interval. Local admission does not establish these globally. |
| DEF-08: censorship resistance | Under stated eligibility, dissemination, capacity, and adversarial-control assumptions, an eligible transaction cannot be excluded indefinitely, or beyond an explicitly proposed bound if one is adopted. | Conditional omission can violate a service bound. Intentional censorship is generally indistinguishable from loss, overload, or selective connectivity without additional evidence; report observations rather than inferred intent. |
| DEF-09: finalized successful outcome | A successful execution result and its effects are verifiably bound to finalized history under the chosen validity model. | Finalized failure is not a completed payment. For W4, one successful asset leg without the other violates the proposed atomic-effects property; fee/replay exceptions require explicit semantics. Off-chain settlement is outside this definition. |
| DEF-10: slot duration | If slots are adopted, the interval between scheduled proposal opportunities according to the specified clock rules. | A missed proposal does not lengthen the nominal slot. Without slots this metric is not applicable. Schedule compliance requires a clock-error model. |
| DEF-11: block interval | Elapsed time between consecutive accepted blocks observed at a named node, stating whether acceptance means receipt, validation, or finalization. | Local receive times differ between observers. A short interval does not establish short finality or settlement latency. Blockless designs must define an analogous record interval. |
| DEF-12: finality latency | Elapsed time from a declared start event to the observer's acceptance of finality for the corresponding record. Transaction reporting separately names submission-to-finalized-inclusion and submission-to-finalized-success. | Missing start events, clock uncertainty, pending attempts, and observer trust can invalidate comparisons. Report timeouts and failures alongside completed latencies. |
| DEF-13: permissionless admission | Any applicant satisfying published, objectively checkable rules can enter a specified role without discretionary approval by an incumbent or exclusive service provider. | Documented eligible applicants blocked by discretionary gates contradict the claim. A finite participation sample cannot prove universal access; economic, network, software, and operational prerequisites must be disclosed. |
| DEF-14: permissionless exit | A participant can stop a role and complete its defined release procedure without discretionary incumbent approval, subject to explicitly declared residual duties and conditions. | Stopping a process is distinct from removal from active duties or release of any locked resources. Unspecified, discretionary, or unbounded release conditions prevent a meaningful exit claim. |
| DEF-15: independent verification | A participant validates a named claim from authenticated evidence, with explicit bootstrap, cryptographic, data-access, and protocol assumptions, rather than accepting a supplier's assertion. | Dependence on an unverified RPC answer for that claim contradicts independence. Verification of inclusion alone does not establish validity, freshness, or availability. |
| DEF-16: decentralization / effective control | A measured distribution of decision power, production opportunities, verification capability, and essential services across independently controlled failure domains. | No single scalar establishes decentralization. Keys, machines, addresses, or declared organizations are not proof of independent control; uncertain groupings must remain visible. |
| DEF-17: data availability | The designated roles can obtain the data necessary for their verification and recovery duties within a stated retrieval and retention envelope. | Failure of required retrieval within that envelope violates the contract. A commitment or validity proof alone does not establish retrievability. Global absence cannot be inferred from one failed request. |
| DEF-18: bootstrap and recovery | Bootstrap establishes an authenticated starting history, rules, membership, and state; recovery restores those properties after interruption before resuming the relevant duties. | Acceptance of unauthenticated anchors or unsafe signing from stale state violates the proposed contract. Public chain data alone may not reveal whether a node used a trusted checkpoint or service. |

A validator/voter, proposer, executor, prover, full verifier, archive, light verifier, and RPC-only client retain the distinct responsibilities in the [role matrix](participant-roles-and-device-profiles.md#2-role-and-trust-matrix). Permissionlessness and independence must name the role; low-cost RPC access is not evidence of distributed voting control.

## 3. Explicit assumption register

Each candidate must instantiate the following records or mark one inapplicable with a reason. An unresolved assumption is a missing condition, not an implicit favorable default.

| ID / category | Required declaration | Current status and consequence |
| --- | --- | --- |
| A-F: faults | Unit of adversarial control; tolerated control for safety and progress separately; crashes, omission, equivocation, adaptive corruption, key compromise, collusion, and correlated software failures; time interval over which control is counted. | Thresholds and static/adaptive models unselected. Validator count, weight, and independent operators cannot be substituted for one another. |
| A-N: network | Peer connectivity, authenticated delivery, delay/loss/throughput envelope, adversarial scheduling, eclipse resistance, and when any delivery bound begins to hold. | Synchrony model unselected. Candidate analyses must state whether safety uses delivery bounds and when progress becomes conditional on them. |
| A-C: clocks | Need for wall clocks or monotonic timers, skew/drift bounds, clock-source trust, restart behavior, and deadline interpretation. | Slots and clock bounds unselected. Measurement clock error must be distinguished from protocol clock assumptions. |
| A-E: economics | Admission and influence rule, adversarial resource budget, control acquisition/borrowing, delegation, incentives, withdrawal, and any accountability mechanism. | Stake, rewards, slashing, lockups, and cooldowns unselected. A behavior assumption cannot be justified merely by calling it economically irrational. |
| A-K: cryptography | Required properties and failure bounds for authentication, hashes, randomness, selection, commitments, proofs, and transport; quantum capabilities; key compromise and erasure assumptions. | No system-wide security level or primitive family selected. Each construction needs separate justification. |
| A-V: validity and data | Exact validity predicate, execution determinism, verification mechanism, required data, retention, retrieval, and service dependencies. | Direct execution and alternative verification remain candidates. No general-purpose validity proof is established by supplied evidence. |
| A-B: bootstrap and durable state | Initial trust anchor, canonical-history rule, authenticated software/rules, historical-key treatment, state provenance, anti-equivocation persistence, and offline horizon. | Trust and recovery procedures unresolved; genesis replay, checkpoints, and authenticated snapshots require comparison. |
| A-T: transitions | Authority to change membership/rules, activation point, old/new history authentication, fault accounting across changes, churn envelope, and key-rotation semantics. | No membership or upgrade mechanism selected. Static-set reasoning cannot silently cover changing sets. |
| A-I: inclusion service | Eligibility policy, dissemination requirement, sustainable offered load, competing traffic, scheduling/fairness rule, expiry/conflict treatment, and observer evidence. | No inclusion deadline, fee policy, or scheduling guarantee selected. |

Availability percentages in device fixtures do not establish independent failures. Likewise, a candidate's safety threshold need not equal its progress threshold or the control needed to censor one transaction.

## 4. Guarantees and assumptions matrix

All G records are proposed requirements. Their evidence obligations are for future specification and explicitly authorized validation, not work claimed as completed here.

| ID / proposed guarantee | Conditional statement | Assumptions requiring resolution | Acceptance evidence / counterexample |
| --- | --- | --- | --- |
| G-01: finalized-history safety | Correct verifiers never finalize conflicting histories within the declared envelope, including across membership changes. | A-F, A-K, A-B, A-T; A-N/A-C wherever the candidate depends on them. | A safety argument covering message reordering, restart, and transitions; any admissible conflicting-finality trace rejects the claim. Network-dependent exclusions must be explicit. |
| G-02: finality permanence | Once finalized, a history remains a prefix of subsequent accepted finality; routine reconnection cannot revoke it. | G-01 assumptions plus declared persistence and verification rules. | An exact finality predicate, evidence format, and observer procedure. Replacing finalized history requires naming a guarantee breach and any external recovery authority; it is not ordinary finality. |
| G-03: valid financial outcomes | Finalized successful payments obey agreed authorization/value rules; atomic exchanges apply both legs or neither under agreed fee/replay semantics. | A-V, A-K, A-B, A-T; financial semantics still unresolved. | Validity arguments that bind results to history. Agreement on an invalid result fails this record even if G-01 holds. |
| G-04: chain progress | Finalized history eventually extends after the declared operating conditions become and remain satisfied. | A-F, A-N, A-C if used, A-V, A-T; A-E if relying on incentives. | A liveness argument naming the required participation and delivery interval. If a finite recovery/progress bound is proposed, state its start event and parameter dependence. |
| G-05: transaction service | Eligible, sufficiently disseminated transactions receive the selected inclusion service under declared load and censorship conditions. | A-I plus G-04 assumptions. | Separate eligibility, dissemination, admission, inclusion, execution, and finality evidence. Conflicts or expiry end eligibility only according to published rules; they must not become arbitrary excuses for exclusion. |
| G-06: verifiable data and outcomes | Each advertised role can obtain and verify the evidence needed for its claimed outcome and recovery duties. | A-V, A-B, A-N, A-K; supplier-fault assumptions explicit. | Role-to-data contracts, retrieval/retention envelope, and failure signaling. Unverified or stale results are never presented as independently verified current outcomes. |
| G-07: open participation and exit | Eligible applicants can activate and participants can exit under objective, published conditions without discretionary gatekeepers. | A-E, A-I where applicable, A-N, A-T, A-B. | Complete role lifecycle, costs, queues, release conditions, and evidence of eligibility. Timely activation/release claims require operating conditions and bounds. |
| G-08: safe recovery and transitions | Returning/new nodes authenticate history and state; signing resumes only after the required recovery checks. Membership/key changes preserve the stated guarantees. | A-B, A-T, A-F, A-K, A-V, A-N for retrieval/progress. | A transition and recovery argument, including stale backups, historical keys, and unavailable suppliers. A conflicting trusted anchor cannot be silently resolved by an RPC majority. |
| G-09: scoped PQ security | Every dependency relevant to an advertised PQ claim has a stated quantum threat model, justified parameters, and migration path. | A-K, A-B, A-T and implementation assumptions. | Complete dependency/claim inventory with unresolved entries visible. One PQ signature path cannot satisfy this record by itself. |

**Unconditional versus conditional alternatives.** An unconditional claim such as “always final in one second” leaves delivery, faults, observation, and validity unspecified and cannot be accepted from the supplied evidence. The proposed reporting form is a safety statement over an explicit envelope, an eventual-progress statement over its operating conditions, and separately a bounded service target over a narrower measurable envelope. A candidate may seek stronger guarantees, but must supply the supporting argument rather than omit conditions.

For finality, compare irreversible acceptance under stated assumptions with confirmation-depth-based confidence. The latter can be examined as an alternative, but cannot satisfy DEF-04 merely by using the same label. If a sampled participant set is proposed, report selection/capture risk separately over both individual selections and the intended operational horizon; no sampling model or acceptable risk is selected here.

## 5. Timing, inclusion, and measurement proposals

The approximately one-second ambition in the [README](../../README.md) is not an accepted slot, block, inclusion, or settlement deadline. Issue #4 must attach any proposed numerical target to one observation pair and an operating envelope.

| ID | Proposed measurable criterion | Required reporting and limitation |
| --- | --- | --- |
| M-01: safety evidence | No admissible conflicting-finality trace in the specification analysis; a safety argument covers all declared conditions. | Archive authenticated counterexamples if found. A conflict-free observation period cannot establish universal safety. |
| M-02: progress | Time from restoration of declared progress conditions to next finalization; longest no-finality interval. | Record how restoration is known, participation, network envelope, backlog, and transitions. If only eventual progress is promised, no finite timeout alone proves its violation. |
| M-03: latency | Distributions for admission, inclusion, finalized inclusion, and finalized success, plus inter-finalization intervals. | Name observer, start events, verification method, clock uncertainty, window, and workload. Include rejected, expired, failed, dropped, pending, and timed-out attempts; avoid survivor-only reporting. |
| M-04: inclusion service | Eligible transaction waiting time, unresolved eligible population, and omissions relative to the selected scheduling rule. | Record dissemination evidence, fee/resource class, offered load, state conflicts, proposer exposure, and uncertainty about remote receipt. Compare equivalent eligibility classes; do not infer censorship intent from a slow transaction alone. |
| M-05: participation lifecycle | Application-to-activation, stop-to-removal, removal-to-resource-release, bootstrap/catch-up time, and bytes. | Separate protocol delay, queueing, connectivity, service rejection, and discretionary gates; include incomplete attempts and financial/operational costs. |
| M-06: role feasibility | Peak and sustained bandwidth, verification work, memory, storage growth, energy, and duty completion for each claimed role. | Use declared workload and device profiles; include votes, certificates, membership updates, retransmission, and recovery. Low average duty frequency does not establish low peak cost. |
| M-07: resilience | Time and data required to recover after each failure class in section 6. | Report unavailable services and any additional trust. Progress restored by weakening validity or replacing finalized history must be labeled accordingly. |

An inclusion promise must choose among eventual service under sustained eligibility, bounded waiting under a capacity envelope, or best-effort selection with no inclusion guarantee. The first two need a scheduling argument; chain progress alone supplies neither. Under conflicting spends or order-sensitive exchanges, define which requests remain eligible after each finalized state change. Proposed reporting should retain original submission time and eligibility changes so repeated eviction or re-admission cannot hide waiting time.

Separate transaction inclusion from successful exchange completion: an expired or conflicting exchange may be included with a finalized failure if that behavior is adopted. Its finalization is not evidence that both assets exchanged.

## 6. Failure and recovery requirements

The following are requirements-level cases and synthetic review fixtures. No trace has been executed.

| ID / case | Required safety and validity behavior | Progress, trust, and measurable consequences |
| --- | --- | --- |
| F-01: missed proposal | Absence must not authorize an invalid or conflicting finalization. Replacement proposals must obey the same finality rules. | Define how another opportunity arises and how absence is distinguished from lateness. Slots, backup proposers, and timeout mechanisms remain alternatives. Report skipped opportunities and time to finalization. |
| F-02: delayed, duplicated, or reordered messages | Authentication, context/version binding, replay handling, and durable vote rules must preserve G-01/G-03 within the declared envelope. | State whether delayed messages can still advance progress. Report retransmission and queue costs; do not assume every delay is a fault by the sender. |
| F-03: partition or eclipse | State exactly which safety claims remain applicable. Proposal: preserve finalized-history safety during arbitrary delay while other safety assumptions hold; feasibility is unproven until a candidate supplies an argument. | Some or all components may stop progressing. Do not promise progress in every isolated component. Name the conditions for progress on a connected component and distinguish stale knowledge from conflicting finality. |
| F-04: reconnection | Compare authenticated finalized anchors. Reconcile prefix-compatible histories without revoking finality. Conflicting accepted finality evidence triggers a reported safety incident. | Define catch-up and backlog handling. Choosing the longest branch, most peers, or a favored RPC is not an agreed incident-recovery rule. External coordination, if needed, must be explicit. |
| F-05: membership or key change | Authenticate the authorizing history and exact activation point. Prevent divergent acceptance of old/new authority; account for cumulative corruption across transitions. | Define who may act during transition, churn limits if any, and behavior when departing members stop early. Compare transition methods without choosing overlap, epochs, or committees. |
| F-06: new-node bootstrap | Authenticate chain identity, rules, current authority, finalized state, and any snapshot against the selected anchor policy. | Compare replay from a trusted genesis configuration, recent checkpoints, and authenticated snapshots. Replay still requires canonical-history and historical-key assumptions; a snapshot needs provenance, completeness, and availability rules. Measure download, verification, supplier dependence, and trust refresh. |
| F-07: returning or restored signer | Authenticate changes missed while offline and restore durable signing constraints before voting. A key backup alone does not establish safe signing state. | Define maximum unaided offline horizon if one exists, recovery with lost local state, duplicate-signing-device handling, and any required checkpoint. Account for former-member key compromise and possible long-range histories if the chosen design permits them. |
| F-08: missing body, execution result, or proof | Do not label an outcome valid when required evidence is unavailable or invalid. Define whether any narrower header/inclusion claim remains verifiable. | Specify abstention, provider substitution, retention, and recovery behavior. A fallback that trusts an executor changes assumptions and needs approval. |
| F-09: overload or service loss | Resource exhaustion must not silently bypass authentication, validity, or finality checks. | Specify admission/backpressure and recovery reporting. Measure valid-traffic impact and provider switching; unlimited adversarial ingress is not covered by finite workload fixtures. |
| F-10: faults exceed the envelope | The guarantee no longer applies; report any observed conflicting finality or invalid state without redefining it as normal operation. | Compare explicit stop-and-investigate, authenticated recovery coordination, and separately defined recovery mechanisms. No rollback authority or assurance of automatic detection/recovery is accepted here. |

Synthetic review trace A: two correct observers accept different records at the same finalized position under the same rules and assumptions. This is a G-01 counterexample even if both later converge.

Synthetic review trace B: finalized history grows while one continuously eligible and sufficiently disseminated payment remains absent. G-04 may hold while G-05 fails, depending on the agreed service condition.

Synthetic review trace C: a returning node receives two apparently valid histories signed by different historical memberships. Signature verification alone does not resolve canonicality; G-08 requires the chosen bootstrap and historical-key policy.

Synthetic review trace D: both observers finalize the same exchange result, but only one asset leg succeeds. Agreement may satisfy G-01 while the proposed G-03 atomicity obligation fails.

## 7. Permissionlessness and effective-control criteria

Permissionlessness is evaluated separately for voting, proposing, full verification, archival service, light verification, and client ingress. A lifecycle specification must identify eligibility evidence, resource costs, discovery/bootstrap access, activation order, active duties, exit, and residual data or economic obligations. It must also state whether applicants can use alternative software or service providers through documented interfaces.

Economic barriers include any capital requirement, lock duration, fees, delegation terms, and exposure to loss. Operational barriers include hardware, upload capacity, uptime, key custody, specialized proving, data retention, and recovery. These are dimensions to measure, not selected admission rules. A publicly available but practically exclusive provider can undermine access even without a formal allowlist.

Compare objective economic admission with other explicitly specified Sybil-resistance mechanisms; compare direct participation with optional delegation or hosting. A permissioned roster is a useful contrast because its admission authority is visible, but it does not meet DEF-13. No alternative becomes viable merely by naming it: each needs an influence-allocation rule and adversarial-control analysis consistent with the project's goal that power not depend directly on computation.

For concentration, define the observation window, total population or weight, grouping evidence, missing coverage, and measurement uncertainty. For a known partition into control groups with shares s_i, use largest-group/top-k shares and HHI = sum(s_i²). The reciprocal 1/HHI is an effective-number descriptor, not a count of proven independent operators. If a candidate later establishes an attack threshold q, define K(q) as the smallest number of control groups whose combined share reaches that threshold. Do not supply q or equate thresholds for halting, safety violation, and censorship before the threat model establishes them.

| ID / dimension | Proposed observations | Limits and interpretation |
| --- | --- | --- |
| D-01: identities and operators | Active keys, duty-performing keys, evidenced operator groups, and concentration under disclosed grouping assumptions. | Key count is exact only for the defined roster; operator independence is uncertain. One operator splitting keys must not improve the control-group metric. Report unknown control separately and sensitivity to plausible common control. |
| D-02: stake or other admission resource | If adopted: distribution of qualifying resource by owner, custodian, and delegate, including locked/borrowed control where observable. Otherwise mark stake metrics inapplicable and measure the chosen resource. | Beneficial ownership, borrowing, and off-chain arrangements may be hidden. Resource ownership is not necessarily signing control. |
| D-03: voting power | Configured voting shares, observed signed participation, missed duties, and ability to block or violate a property under the candidate's rules. | Use protocol-authorized weights, not inferred machine counts. Low observed activity does not eliminate latent control. |
| D-04: block production | Share of valid proposals and finalized production by key and inferred operator; consecutive-production runs; selected versus completed opportunities. | Report failed opportunities and assignment rules. Cooldowns at key level may be evaded through identity splitting; production share alone does not measure voting or transaction-selection control. |
| D-05: delegation | Owner-to-operator relationships, delegated voting share, custody, redelegation delay, and switching cost if delegation exists. | Delegators are not independent signing operators. Public delegation records may miss contractual control or pooled services. |
| D-06: geography | Distribution of voting/production and critical services across declared or evidenced locations, plus correlated regional-outage exposure. | Network addresses do not prove physical location, jurisdiction, or independence. Coverage and privacy implications must be reported; no geographic identity oracle or quota is selected. |
| D-07: infrastructure | Share depending on hosting providers, networks, data centers, client software, RPCs, archives, executors, provers, and key-custody services. | Dependencies overlap; do not sum them as disjoint shares. Failover is useful only if keys, data, and capacity can actually move independently. Hidden resellers and shared control remain uncertainty. |
| D-08: verifiable participation | Number and share of sampled participants that can authenticate history, validate their advertised claims, obtain data, and recover without one exclusive supplier. | Future evidence must distinguish local checking, proof verification, delegation, and RPC reliance. Self-reporting and observed signatures alone cannot establish independent verification. |
| D-09: admission/exit access | Eligible entry/exit attempts, costs, waiting-time distributions, rejection reasons, service dependencies, and unresolved attempts. | Samples expose barriers but do not prove universal access. Avoid publishing sensitive operator details merely to improve apparent measurement precision. |

**Proposed criterion:** every decentralization claim includes both a power-distribution view and a dependency/verification view, with unknown coverage visible. Identity-count growth alone cannot satisfy it. No minimum operator count, maximum concentration, or acceptable provider exposure is selected.

Full participation and sampled duties should be compared for peak communication, selection risk, and correlated control. Per-key rotation and per-operator rotation should be distinguished: the latter requires control attribution that may itself introduce identity or gatekeeping assumptions. Simpler unrestricted selection remains a comparison case; no cooldown is accepted.

## 8. Post-quantum scope and migration

PQ coverage is an architectural goal, not a property established for this draft. The quantum adversary's capabilities, attack horizon, required security levels, and distinction between future forgery and long-term confidentiality remain owner/threat-model decisions.

| ID / dependency | Required coverage and failure question | Resource and migration implications |
| --- | --- | --- |
| PQ-01: identities and authorization | Transaction, validator, peer, administrative, and recovery identities; address/key binding; authentication, replay protection, role/chain domain separation, and compromise recovery. | Count key/signature bytes and verification costs by message role. Define rotation authorization and treatment of abandoned or compromised keys. |
| PQ-02: votes and finality evidence | Authentication of proposals, votes, membership changes, and any aggregate/threshold certificates. | No compact PQ aggregation is assumed. Certificate dissemination, verification, historical retention, and weak-device catch-up need budgets. |
| PQ-03: hashes and commitments | Account identifiers, transaction/history/state commitments, Merkle paths, nullifiers, encodings, and any proof transcript hashes. | Assess required preimage/collision properties and parameters separately. Output length alone is not a system security claim; migration may affect addresses, commitments, and historic verification. |
| PQ-04: randomness and selection | Any beacon, commitment/reveal process, VRF-like mechanism, or randomized assignment, including bias, withholding, grinding, predictability, and adaptive targeting. | Absence of such a mechanism may make a dependency inapplicable, but a candidate must explain selection instead. No suitable construction is established by the supplied review. |
| PQ-05: proofs | Soundness, knowledge/privacy claims where needed, transcript assumptions, recursion, verifier identity, public-input binding, and artifact authentication. | Separate generation from verification and account for proof bytes, witness access, memory, and deadlines. Specialized claims do not establish general execution validity, exchange atomicity, or data availability. |
| PQ-06: transport and distribution | Peer authentication, session-key establishment, downgrade resistance, software/artifact distribution, and trusted bootstrap channels. | Transport availability and confidentiality claims must name active negotiation policy; a PQ transaction signature does not configure these paths. |
| PQ-07: migration and historical trust | Algorithm/version authorization, coexistence rules, activation, downgrade rejection, historical evidence, long-offline clients, and compromised legacy keys. | Compare single-family deployment with explicit upgrade support against hybrid/coexisting constructions. Additional algorithms increase bytes and validation complexity; combination rules require their own argument. Neither approach is selected. |

The [supplied implementation review](../research/crypto-zk-implementation-review.md) reports concrete signature variants, specialized proof statements, a declared recursion security parameter, and separate transport paths. Those observations establish candidates and limitations, not whole-system PQ security, active deployed policy, general-purpose validity proofs, or compact finality certificates. Its pinned sources remain the provenance for those findings; no new code inspection is claimed here.

Proposed acceptance for G-09 is complete accounting of these dependencies, including explicit inapplicability and evidence gaps. Migration must explain how new verifiers authenticate old history if an old algorithm becomes forgeable; merely changing future signatures does not answer that bootstrap question. Any recent trusted anchor or external coordination needed for that case must be disclosed.

## 9. Consequences and viable comparisons

| Choice to compare | Security / failure consequence | Decentralization, performance, network, and PQ consequence |
| --- | --- | --- |
| Broad safety envelope versus narrow timing envelope | Makes progress conditions explicit without treating a slow response as a safety failure. Stronger safety claims still need proof. | Wider geographic participation and weaker links must be reflected in latency distributions; short targets may exclude participants even when power is not assigned by computation. |
| Local execution versus proof-backed validity versus executor attestations | Changes what makes a finalized result valid and which actors or constructions must remain sound/honest. | Local work, prover concentration, or attestor control must be measured separately. Proof alternatives introduce PQ and artifact dependencies not resolved by supplied specialized circuits. |
| Full participation versus sampled duties | Sampling adds selection/capture and corruption-timing assumptions; full participation still needs a fault/control model. | Compare total and peak messages, certificate bytes, duty costs, and entry opportunities. Large signature payloads cannot be assumed away through aggregation. |
| Genesis-based verification versus trusted checkpoints/snapshots | Changes bootstrap trust, historical-key exposure, and recovery assumptions; neither label alone specifies canonicality. | Compare replay cost, retention, supplier diversity, offline horizons, and migration of old authentication evidence. |
| Direct participation versus delegated/hosted service | Delegation can separate ownership from signing and transaction selection. | Compare total costs and switching friction with concentration of keys, cloud, RPC, and data services; low client cost is not low independent-validator cost. |
| Eventual inclusion versus bounded inclusion versus best effort | Changes the censorship claim and its falsifiability. Bounds need capacity, dissemination, eligibility, and scheduling conditions. | Reservation, pricing, or scheduling choices can create entry barriers. Their network/verification costs must include adversarial and PQ-authenticated traffic. |

These are analytical comparison dimensions, not performance findings. The [role draft's bandwidth analysis](participant-roles-and-device-profiles.md#6-lower-bounds-and-post-quantum-accounting) already identifies an incompatible pairing of a synthetic weak-device envelope and illustrative workload rate. It supports keeping data duties explicit, not selecting a throughput ceiling or a lightweight architecture.

## 10. Unresolved owner decisions and evidence gaps

Every item below is **open**. The owner decides scope and priorities; subsequent research must supply the technical arguments needed before a guarantee or mechanism can be accepted.

| ID | Choice requiring agreement | Required next input and consequence |
| --- | --- | --- |
| DEC-01 | Adopt these definitions, including irreversible finality under explicit assumptions, or revise them? | Resolve whether safety during arbitrary delay is mandatory and how any exceptional recovery is described. No safety proof supplied. |
| DEC-02 | Which fault units, thresholds, corruption timing, and economic assumptions are acceptable? | Phase 2 threat model; determine safety, progress, censorship, and transition envelopes separately. |
| DEC-03 | Which synchrony, clock, and post-disruption progress model should candidates meet? | Phase 2 analysis plus issue #4 envelopes; no one-second guarantee follows before this choice. |
| DEC-04 | Which transaction service and ordering/fairness obligations are required? | Eligibility, fees, dissemination, overload, expiry, and W4 cancellation/conflict semantics. Finality alone provides no fair-ordering promise. |
| DEC-05 | Which roles must support permissionless independent operation, on which devices? | Reconcile ROLE-07–ROLE-09 with lifecycle costs and concentration measures; mobile client access and voting remain distinct. |
| DEC-06 | Which admission, influence, exit, and accountability alternatives merit further evaluation? | Compare Sybil resistance and barriers without defaulting to stake, delegation, penalties, or withdrawal delays. |
| DEC-07 | What bootstrap trust, offline horizon, and incident-recovery authority are acceptable? | Compare genesis/checkpoint/snapshot policies, historical-key compromise, and state supplier loss. No trusted checkpoint or rollback authority adopted. |
| DEC-08 | Which concentration measures, uncertainty reporting, and eventual acceptance limits are mandatory? | Agree measurement coverage, control-group inference, privacy constraints, and sensitivity analysis before proposing numerical limits. |
| DEC-09 | What quantum threat horizon, security objectives, and migration obligations are required? | Complete dependency-specific assessment; randomness, selection, certificates, historic verification, and proof soundness remain evidence gaps. |
| DEC-10 | Which execution-validity and availability contracts should candidate designs satisfy? | Resolve roles and data duties for payments/W4; specialized proof reuse does not fill the general validity gap. |
| DEC-11 | Which event, workload, and envelope should the timing ambition target? | Issue #4 defines candidate latency/resource budgets; owner explicitly agrees any production values. |

No new owner approval is recorded. The only previously documented explicit scope decision carried forward is payments and atomic asset exchange from the beginning. Existing drafts and their publication do not settle these open items.

## 11. Completion mapping and handoff

| Issue #3 scope / acceptance item | Substantive coverage | Remaining decision or evidence |
| --- | --- | --- |
| Safety and finality as conflicting finalized histories | DEF-01–DEF-05; G-01–G-03; synthetic traces A/D. | Candidate rules and safety/validity arguments. |
| Liveness, inclusion, censorship, admission, and operating conditions | DEF-06–DEF-09; A-I; G-04/G-05; M-02–M-04. | Select eventual/bounded/best-effort service and eligibility/scheduling rules. |
| Fault, network, clock, economic, and cryptographic assumptions | A-F through A-I and per-guarantee dependencies. | Phase 2 selects and justifies models; none silently adopted here. |
| Misses, delays, partitions, reconnection, membership changes, bootstrap | F-01–F-10 and G-08. | Mechanisms, recovery trust, and transition arguments remain open. |
| Permissionless admission/exit and barriers | DEF-13/DEF-14; G-07; M-05; section 7. | Role-specific lifecycle and cost thresholds. |
| Stake, voting, production, operators, delegation, geography, infrastructure | D-01–D-09, concentration formulas, coverage limits. | Stake/delegation apply only if selected; independent control cannot be proven from identities. |
| PQ identities, signatures, randomness, selection, proofs, migration | PQ-01–PQ-07 and G-09. | Dependency evidence, parameters, and migration policy. |
| Precise terms with violations or measurement limits | Section 2 and measurement qualifications in sections 4–8. | Refine definitions against future candidate state machines. |
| Timing separated from safety and network-dependent progress | DEF-10–DEF-12, G-01/G-04/G-05, section 5. | No production numerical target accepted. |
| Explicit bootstrap/recovery trust and PQ open requirements | A-B, G-08/G-09, F-06/F-07, DEC-07/DEC-09. | Owner choices and missing technical evidence remain visible. |
| Conflicting interpretations and choices requiring agreement | Sections 4, 7, 9, and DEC-01–DEC-11. | Publication does not approve proposals. |

The documentation scope is addressed for review; architectural acceptance remains open. Issue #4 should use the distinct observation events and assumption register when proposing budgets. Phase 2 should instantiate adversarial and network conditions, while later architecture work must demonstrate how its mechanisms satisfy or explicitly revise each G record.

## References and provenance

- [Project README](../../README.md): research process, goals, candidate-only status, and timing ambition.
- [Financial workloads and scope](financial-workloads-and-scope.md): recorded scope approval, W1–W6, outcome distinctions, proposed exchange semantics, and source-attributed authorization costs.
- [Participant roles and device profiles](participant-roles-and-device-profiles.md): role contracts, trust boundaries, synthetic device envelopes, and derived bandwidth illustration.
- [Cryptography and specialized proof implementation review](../research/crypto-zk-implementation-review.md): supplied source-inspection findings, commit-pinned primary-source provenance, and limitations of cryptographic/proof candidates.
- [Issue #3](https://github.com/RinjaniChain/consensus/issues/3): deliverable scope and acceptance criteria.

All new definitions, guarantee records, metrics, scenario traces, and comparisons in this document are original requirements analysis. No new external research, protocol implementation, executable experiment, benchmark, or security certification is claimed.
