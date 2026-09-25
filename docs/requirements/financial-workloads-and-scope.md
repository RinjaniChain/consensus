# Financial workloads and consensus scope

Status: **Draft for review — payments and atomic asset exchange are the agreed starting scope; detailed semantics and numerical targets remain proposals.**

Version: 0.1. Related work: [issue #1](https://github.com/RinjaniChain/consensus/issues/1). This document supplies inputs to issues [#2](https://github.com/RinjaniChain/consensus/issues/2), [#3](https://github.com/RinjaniChain/consensus/issues/3), and [#4](https://github.com/RinjaniChain/consensus/issues/4); it does not replace their role, guarantee, or resource-budget specifications.

## 1. Problem and evidence status

“Financial operations” is not a workload specification. Independent payments, a payroll batch, and transactions contending for one liquidity pool have different validation, state-access, and ordering costs. A single transactions-per-second number would hide those differences.

The project brief establishes the financial focus, permissionless participation, post-quantum requirement, decentralization ambitions, and approximately one-second timing ambition. It does not establish a transaction model, asset standard, virtual machine, signature scheme, block capacity, or settlement deadline.

In this document:

- **Proposal** means a candidate requirement or scope choice awaiting agreement.
- **Fixture** means a synthetic input for later analysis, not a production limit, demand forecast, or measured result.
- **Derived estimate** means arithmetic under named assumptions, not a benchmark.
- **External evidence** is linked to a primary source. No source is evidence that RinjaniChain already achieves a property.

No code or experiments are included. These are requirement proposals using established workload-description techniques, not a novel consensus construction.

## 2. Scope alternatives

| Option | Initial scope | Benefits to investigate | Costs and limitations |
| --- | --- | --- | --- |
| A: bounded transfers | Native-value and issued-asset transfers, explicit authorization, bounded batches | Smaller semantic surface; easier cost accounting and correctness review | Does not establish feasibility for shared-state DeFi; issuance and asset administration still need definitions |
| B: transfers and atomic exchange | A plus bounded same-chain multi-asset exchanges | Tests financial atomicity and ordering dependencies early | Additional authorization, conflict handling, and cancellation/deadline semantics |
| C: general programmable finance | Transfers plus user-defined financial programs | Broad application flexibility | Execution metering, code/state growth, adversarial computation, and highly variable workloads become first-order inputs |

**Agreed direction:** payments and atomic asset exchange are included from the beginning. Use B as the working baseline, with W1/W2 and W4 assessed separately rather than collapsing them into one throughput figure. The W4 model is same-chain exchange; cross-chain atomicity is not inferred from this agreement. W3 batch semantics remain proposed; W5 remains a stress/extension profile. General programmable finance is not approved merely because atomic exchange is included.

Regardless of the baseline, propose keeping invalid traffic, contention, bursts, replay attempts, and recovery in scope for analysis. They are operating conditions, not optional financial features.

Propose deferring cross-chain settlement, fiat redemption, oracle correctness, private transactions, an exchange matching engine, and a specific VM/asset standard from this first workload baseline. Deferral does not reject these features: it prevents their external dependencies from becoming implicit consensus guarantees. If any is launch-critical, add a dedicated profile and assumptions before setting capacity targets.

## 3. User-visible transaction outcomes

The following are observation points, not a mandated linear protocol pipeline. Execution may precede inclusion, follow ordering, or be represented by a proof; that architecture remains open.

| Observation | Meaning proposed for requirements | What it does not establish |
| --- | --- | --- |
| Submitted | A client sends an identified request to a named ingress endpoint | Receipt by any other node |
| Locally admitted | An identified node accepts the request under its current admission policy and state | Global agreement, eventual inclusion, or successful execution |
| Included | A proposal/history contains the request at an identified position | Finality or execution success |
| Executed | The specified execution rules produce a result against a specified prior state | That this result belongs to finalized canonical history |
| Finalized inclusion | Inclusion is covered by the protocol's finality rule under its explicit security assumptions | A successful business operation |
| Finalized successful on-chain outcome | Successful effects are bound to finalized history and verifiable under the chosen execution-validity and data-access model | Off-chain payment, asset backing, bridge completion, or legal settlement |

**Proposal:** use “settled” in this requirements set only for a finalized successful on-chain outcome, with its assumptions stated. A finalized failure is an immutable failed attempt, not a completed payment. Ethereum's EIP-658 is an existing example of explicitly distinguishing success and failure in transaction receipts; its receipt format and VM are not proposed for adoption here. [S2]

Latency definitions for issue #4:

- Submission-to-admission, submission-to-inclusion, and submission-to-finalized-success must be reported separately.
- Also report admission-to-finality to isolate ingress delays; name the observing node/client and its verification method.
- Track rejected, expired, dropped, failed, and still-pending attempts separately. Report success-conditioned latency together with these counts, not alone.
- A client timeout means an unknown outcome unless there is verifiable rejection or expiry. Retrying must not silently mean a second payment; replay protection and application-level payment identifiers need explicit semantics.
- The approximately one-second ambition is not yet a one-second successful-settlement requirement. Timing under overload, partitions, and recovery remains an input to #3/#4.

## 4. Workload description conventions

Every profile below is a **synthetic proposal**, not a selected product feature. Model logical state items rather than physical database operations; an account-based representation is used only as illustrative accounting. A UTXO or other model must map the same economic operation to its own inputs, outputs, authorization, and state-access costs before comparison.

For encoded transaction size, use:

`B_tx = B_body + sum(signature_bytes) + B_inline_keys + B_witness + B_encoding_overhead`

`B_body` includes operation fields, identifiers, replay/domain information, and any fee/deadline fields. It excludes signatures, inline keys, and witnesses. Body ranges below are provisional byte fixtures, not claims about an eventual encoding. If a chosen encoding does not fit them, revise the fixtures visibly. Receipts, block metadata, finality certificates, and replicated networking are additional costs.

For each profile, distinguish existing-state updates from new-state creation, warm from cold access, and unique state items from repeated accesses. Fees, replay bookkeeping, asset policies, state proofs, and storage indexing can add reads/writes beyond the economic minimum listed below.

## 5. Candidate workload profiles

| ID / operation | Body and authorization fixtures | State and execution assumptions | Arrival and contention fixtures |
| --- | --- | --- | --- |
| W1: independent payments | One transfer; 128–512 body bytes; one independent authorization | Illustrative account model: read and write two balances; authorization, replay checks, and bounded arithmetic; no external calls | Independent funded senders; uniform recipients initially; steady load plus shared overlays below |
| W2: issued-asset transfers | One transfer; 256–1,024 body bytes; one authorization, with a separate two-authorization variant | Two balance reads/writes plus asset-policy reads; policy size/cost unresolved; test 0% and 100% new recipient-state creation separately | Uniform traffic and concentrated merchant/exchange recipients; hot-state overlay required |
| W3: batch distribution | One signed request containing n payments; n in {1, 10, 100}; body fixture 128–512 + n × (64–128) bytes; one authorization | One sender and n distinct recipients; at least n+1 balance reads/writes in the illustrative model; operation cost scales with n; all-or-nothing versus partial success unresolved | Periodic batches overlaid on W1; repeated use of one distributor; count both transactions and payment legs |
| W4: same-chain atomic exchange | Two assets, two parties, two transfer legs; 256–2,048 body bytes; two independent authorizations | At least four party/asset balance reads/writes, plus relevant policy checks; both legs succeed or neither does, excluding explicitly defined fees/replay effects | Independent pairs versus repeated access to one asset/participant; dependent requests and expiry boundaries |
| W5: shared-state programmable finance | One bounded call sequence; 256–4,096 body bytes; one or two transaction authorizations; any additional authorization must be counted | Synthetic sweeps of 10/100/1,000 logical reads and 2/20/200 writes; compute demand and witness sizes unresolved; no assumed VM, gas conversion, oracle, or proof system | 0%/50%/100% of requests touch one designated writable state item; order-sensitive successes and failures |

State counts describe economic effects and need not predict implementation I/O. W3's independent-authority variant requires separate authorizations per payer; it must not inherit the single-payer signature count. W4 describes atomic effects, not a complete exchange protocol: consent, replay, pricing, cancellation, and settlement rules remain open.

Execution cost must initially be expressed as a vector: authorization checks, logical reads/writes and bytes, arithmetic/program operations, allocation/state growth, and optional proof generation/verification. Wall-clock cost, target device, and exact operation mix remain unresolved inputs to #2/#4. “Bounded” does not mean inexpensive.

## 6. Shared traffic and failure overlays

These fixture values deliberately explore different shapes. They are not target TPS or acceptable failure rates.

| Dimension | Proposed fixture | What must be reported |
| --- | --- | --- |
| Offered load | Absolute rate unresolved; sweep 0.25, 0.5, 0.8, 1.0, and 1.2 times a declared reference capacity C once available | How C was obtained for that exact profile and environment; request, byte, and signature arrival rates; never use a transfer-only C for all workloads |
| Arrivals | Fixed spacing and independently randomized inter-arrival times; burst at 5 times the declared baseline rate for 10 seconds | Queue growth, admission drops, time to drain, pending requests, and latency distribution |
| Working set | 10,000 and 1,000,000 logical participants; state values of 32, 256, and 4,096 bytes where semantically applicable | State footprint, cache assumptions, bytes read/written, new-state growth; larger sets remain an open extension |
| Contention | 0%, 50%, or 100% of requests access one shared writable logical item | Dependency depth, success/failure counts, ordering effects, and sustained successful effects per second |
| Traffic mix | Pure W1–W5 runs; provisional mixed run: 70% W1, 20% W2, 10% W3 by submitted transaction count, with W3 n=10 | Exact percentages and transaction/payment-leg/byte mix; no implicit inclusion of unsupported features |
| Invalid input | 0%, 1%, and 10% malformed, bad-signature, or unauthorized requests, each tested separately; saturation behavior additionally unresolved | Rejection stage, consumed bandwidth/CPU, valid-traffic impact, and admission limits |
| Stateful failures | Conflicting spends, stale preconditions, duplicate requests, insufficient balances, and expiry races | Which operations are rejected before inclusion or fail after it; deterministic results and fee/replay consequences |
| Read traffic | 0, 1, and 10 client status/state queries per submitted transaction | RPC load separately from consensus messages; trusted responses versus verified responses and proof bytes |
| Network disruption | Delays, loss, partitions, proposer outages, and reconnecting clients; concrete network envelopes deferred to #3/#4 | Safety observations, progress conditions, backlog recovery, and ambiguous client outcomes |

“0% contention” concerns the selected economic state; shared fee counters or metadata may still serialize execution. Future capacity reporting must expose such hidden shared state. Invalid-load percentages do not bound an adversary's capability or replace admission/DoS analysis.

## 7. Mapping use cases to requirements

All statements in this section are proposed review criteria, not accepted guarantees or quantified service levels.

| Profile | Latency and finality need | Capacity reporting | Correctness and failure criteria |
| --- | --- | --- | --- |
| W1 | Prompt verifiable successful payment outcome; tail latency and pending outcomes visible | Submitted/admitted/finalized-success transactions per second and bytes per second | No unauthorized value movement or duplicate application; balances conserved subject to explicit fees/issuance rules |
| W2 | W1 plus explicit policy/version under which transfer settled | Per-asset and total successful transfers, policy-check load, and state growth | Authorization and asset restrictions applied consistently; asset backing remains external |
| W3 | Batch completion latency separately from individual-leg reporting | Batches per second, payment legs per second, bytes and state writes per batch | Agreed batch atomicity; failed-leg behavior and retries unambiguous |
| W4 | Finalized outcome covering both legs; deadline interpretation explicit | Exchanges and transfer legs per second; conflicting/expired attempts separate | No one-sided successful exchange; matched consent, replay protection, and specified cancellation behavior |
| W5 | Finalized execution result, not just finalized ordering; stale-precondition failures visible | Successful operations plus execution/read/write/witness/proof costs and failure fraction | Determinism, bounded resources, invariant preservation, and explicit ordering-sensitive outcomes |

**Proposed common reporting rule:** specify the experiment duration, warmup, state size, offered load, node count, topology, hardware, cryptography, and success definition with every future performance claim. No finality percentile, TPS target, or device capability is accepted here.

## 8. Subsystem responsibilities and contracts

This is a responsibility map, not a choice of processes, crates, consensus family, or validator roles. Each boundary must eventually have a versioned, deterministic contract where applicable.

| Subsystem / concern | Responsibility proposed for analysis | Required boundary or unresolved choice |
| --- | --- | --- |
| Consensus | Agreement on ordered history and finality under explicit fault/network assumptions | Define the validity predicate and how execution results, validator changes, and availability evidence bind to finalized history; must not silently equate ordering with valid settlement |
| Execution | Transaction semantics, authorization policy, replay/fee handling, deterministic state transitions and outcomes | Supplies results and state commitments through a specified validity mechanism; direct execution, re-execution, and proof verification remain alternatives |
| Networking | Propagation, peer connectivity, ingress transport, congestion and resource controls | Delivers messages/data; delivery or local admission alone cannot assert global agreement; network assumptions feed liveness |
| Cryptography | Signature/hash/proof operations, key formats, domain separation, randomness primitives as needed | Algorithms and security assumptions exposed explicitly; PQ coverage includes transaction and validator paths, not just one signature |
| Storage | Durable blocks, state, metadata, retrieval, and crash recovery | Atomic persistence and recovery semantics must agree with finalized state; pruning, snapshots, and trust on bootstrap remain unresolved |
| Data availability | Required participants can obtain data needed for verification and recovery under a defined model | Cross-cutting obligation spanning networking, storage, and consensus validity; a state root or correctness proof alone is not the availability contract |
| Client/API and applications | Submission, outcome interpretation, business identifiers, application invariants | Must name the verification/trust model for reported finality and success; application semantics cannot be inferred from a hash alone |

A future design separating ordering and execution must explain how invalid execution results are excluded from the claimed settlement guarantee. A design using proof-producing operators must specify what happens when proofs or input data are delayed, unavailable, or invalid. Neither option is selected by this document.

## 9. Security, decentralization, and post-quantum consequences

| Pressure / failure mode | Consequence to investigate | Alternatives or requirement questions |
| --- | --- | --- |
| Unauthenticated or expensive invalid traffic | Verification and admission can exhaust weak participants before useful work reaches consensus | Cheap structural checks, explicit resource limits, and admission pricing; assess permissionless access and attack costs rather than assuming stake solves ingress abuse |
| Hot shared state and order-sensitive execution | Resource-rich producers may have scheduling advantages; finalized order can still be economically unfavorable | Bounded operations, explicit order semantics, or application-level auctions; no fairness guarantee is inferred from finality |
| Large batches and growing state | Low transaction counts can conceal high bandwidth, execution, and storage costs | Multi-dimensional accounting; compare small batches, bounded fan-out, pruning, and independent history access |
| Heavy execution or specialized provers | Lightweight voting could coexist with concentrated production/verification services | Compare local execution, re-execution, and proofs including latency, trust, availability, and PQ assumptions |
| Oracle, issuer, bridge, or custodian dependence | Consensus agreement does not establish external truth or redeemability | Keep those guarantees outside the baseline or introduce explicit dependency-specific profiles and failure models |
| PQ authorization overhead | Larger encoded requests and repeated verification affect bandwidth, storage, and weak-device participation | Evaluate schemes and key-distribution models explicitly; do not assume classical signature aggregation/threshold tools remain available |

For a concrete **size illustration only**, FIPS 204 Table 2 specifies an ML-DSA-44 signature of 2,420 bytes and public key of 1,312 bytes. [S1] With a 256-byte body, one signature, an already available authenticated key, no witness, and omitted encoding overhead, W1 would carry at least 2,676 bytes. Including that key raises the subtotal to 3,988 bytes. At an illustrative 1,000 such requests per second, these are 2.676 MB/s or 3.988 MB/s of transaction payload alone (decimal units), before replication, votes, certificates, receipts, transport, or recovery traffic.

Those figures are derived arithmetic, not an algorithm selection, throughput target, minimum network requirement, or verification benchmark. Cached-key variants require specified key registration, authenticated lookup, rotation, and cold-start costs. Independent multi-signature variants multiply authorization costs unless a separately justified construction changes that model. Proofs, randomness, leader selection, and network authentication need their own PQ assessment in later work.

## 10. Open decisions for owner review

| ID | Question | Proposed starting point, not approval | Consequence / follow-up |
| --- | --- | --- | --- |
| SCOPE-01 | Which scope option is the initial baseline? | Agreed: payments and atomic asset exchange from the beginning; B is the working baseline | #4 must cover transfer and atomic-exchange workloads; detailed semantics remain open |
| SCOPE-02 | Are issued assets required initially, and with which administrative powers? | Include a generic transfer profile; defer issuance/freeze/redemption rules | W2 needs explicit semantics before protocol validity rules can be derived |
| SCOPE-03 | Does atomic exchange require general programmable contracts, and is cross-chain exchange required? | Atomic exchange is in the agreed starting scope; use same-chain W4 for this draft; general contracts and cross-chain support remain unapproved | Define exchange consent, cancellation, expiry, and conflict behavior before deriving validity rules |
| SCOPE-04 | What does a failed batch mean? | Compare atomic failure with explicit per-leg outcomes | Determines W3 correctness, retry behavior, and reporting |
| SCOPE-05 | What user-visible settlement latency and failure rate are acceptable, under which conditions? | Do not equate the one-second timing ambition with a settlement deadline | Quantitative agreement belongs to #3/#4 |
| SCOPE-06 | Which devices must verify successful settlement independently? | Separate direct verification from service-reported success | Role and trust definitions belong to #2 |
| SCOPE-07 | Which external assets, privacy features, or integrations are indispensable? | Defer external guarantees from the first baseline | Each accepted dependency needs its own assumptions and workload |
| SCOPE-08 | Are the synthetic profile sizes and mixes representative enough to begin budget analysis? | Use the proposed fixtures with explicit sensitivity analysis | Revise after product input; no fixture becomes a production limit automatically |

Approval record: on 2026-09-25, in the working task for issue #1, the project owner selected **payments and atomic asset exchange from the beginning** in response to the baseline-scope question. This records the scope preference only, not approval of this entire draft, an exchange mechanism, batch semantics, cryptographic primitives, or numerical targets. Unanswered items remain proposals. The analytical artifact is ready for review; the review gate remains open.

## 11. Review checklist and handoff

- Profile parameters: sections 4–6 specify candidate sizes, authorization, state access, arrivals, contention, and explicit unresolved execution budgets.
- Use-case requirements: sections 3 and 7 map latency, finality, capacity, and correctness without unapproved targets.
- Subsystem boundaries: section 8 identifies responsibilities and required contracts without selecting an architecture.
- Scope agreement: sections 2 and 10 expose alternatives, recommendations, and outstanding decisions.
- Issue #2 should refine device/role and independent-verification assumptions; #3 should formalize guarantees; #4 should resolve quantitative budgets; #5 should prioritize the exposed tradeoffs; #6 should consolidate reviewed results.

## Sources

- **[S1]** NIST, [FIPS 204: Module-Lattice-Based Digital Signature Standard](https://csrc.nist.gov/pubs/fips/204/final), Table 2 ([full text](https://tsapps.nist.gov/publication/get_pdf.cfm?pub_id=958463)). Used only for the concrete signature/key-size illustration, not evidence of system-wide PQ security or performance.
- **[S2]** Nick Johnson, [EIP-658: Embedding transaction status code in receipts](https://eips.ethereum.org/EIPS/eip-658). Used as an established example of recording execution success/failure separately from transaction inclusion, not as a consensus recommendation.

Sources consulted on 2026-09-25. All workload mixes, ranges, recommendations, and arithmetic beyond the cited facts are proposals or analysis in this document.
