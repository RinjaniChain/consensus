# RinjaniChain Consensus

Research, requirements, and eventual implementation of the consensus protocol for **RinjaniChain**, a new Layer 1 blockchain intended primarily for financial operations and planned to be written in Rust.

**Current phase: research and specification.** The immediate objective is a rigorous consensus requirements specification, not an implementation. This repository is the canonical workspace for that research and will later host the consensus code.

## Mission and design goals

Develop a protocol that supports financial transactions with fast, deterministic finality while preserving meaningful permissionless participation and decentralization.

The following are goals to investigate and turn into measurable requirements. They are not claims that every goal can be achieved simultaneously.

| Area | Goal | Questions the specification must resolve |
| --- | --- | --- |
| Participation | Permissionless entry and participation. | Admission, activation, exit, validator churn, and the cost of joining. |
| Timing | Predictable, short block times, initially targeting approximately one second. | Whether this means slot duration, normal-case block interval, or finality latency; behavior during missed slots and network disruption. |
| Finality | Fast deterministic finality suitable for financial transactions. | The exact finality guarantee, fault assumptions, latency distribution, and behavior after faults exceed the security threshold. |
| Hardware | Participation from weak computers and potentially mobile devices. | Which roles are feasible on such devices and their bandwidth, memory, storage, energy, and availability requirements. |
| Consensus power | Consensus influence must not depend directly on computational power. | How voting and proposer influence are assigned and how resource advantages still affect participation. |
| Sybil resistance | Prevent inexpensive identity multiplication from capturing consensus. | Whether stake or another economic admission mechanism is appropriate, and what security assumptions it introduces. |
| Concentration resistance | Resist concentration of validation and block production. | How to measure control by operators, stake owners, delegates, pools, and infrastructure providers. |
| Decentralization | Support geographic and infrastructure diversity where realistically achievable. | Latency tradeoffs and how diversity can be assessed without assuming identities reveal independent operators. |
| Proposer rotation | Limit repeated or consecutive production by the same participant. | Whether cooldowns or alternatives address actual control and remain effective against identity splitting. |
| Post-quantum security | Treat post-quantum security as an architectural requirement. | Security and migration assumptions for signatures, identities, randomness, leader selection, proofs, and all other cryptographic dependencies. |
| Throughput | Sustain high throughput appropriate for financial workloads. | Representative workloads, transaction sizes, execution costs, data availability costs, and target throughput under explicit resource budgets. |
| Fault tolerance | Preserve safety and provide liveness under a defined Byzantine fault and network model. | Fault thresholds, adaptive corruption, partitions, recovery, and validator-set transitions. |
| Modularity | Separate consensus, execution, networking, cryptography, and storage. | Responsibilities, interfaces, and the guarantees each subsystem must provide to the others. |

## Candidate ideas, not decisions

The following ideas are research candidates only. Their inclusion does not approve an architecture, algorithm, dependency, or implementation plan:

- Slot-based consensus, with approximately one-second slots.
- Proof of Stake primarily as an economic Sybil-resistance mechanism.
- Validator committees and BFT finality.
- Randomized proposer selection, proposer cooldowns, and backup proposers.
- Separation of block production from finality.
- Separation of expensive execution from lightweight consensus participation.
- Succinct or zero-knowledge proofs for state-transition verification.
- Malachite or another Rust BFT framework as a possible foundation.

Each candidate must be compared with viable alternatives, including simpler designs. Framework selection must follow the requirements and security analysis.

## Questions we must challenge

### Timing, safety, and liveness

Define the network model before promising timing guarantees. Distinguish a scheduled slot from successful block delivery and finalized inclusion. A one-second target must specify its operating conditions and cannot stand in for a guarantee during arbitrary network delays or partitions.

Evaluate normal operation, missed proposals, delayed messages, clock skew, partitions, reconnection, and recovery. Specify which guarantees must survive each case and when progress may stop.

### Participation, stake, and concentration

Low hardware requirements alone do not establish decentralized control. Investigate stake concentration, delegation, pooling, censorship, transaction-ordering incentives, operating costs, and infrastructure dependence.

A validator identity is not necessarily an independent participant. Any proposer cooldown or per-validator limit must be evaluated against one operator splitting stake across identities. Assess the effects on honest small validators, availability, fairness, and liveness before adopting such rules.

### Committees, randomness, and proposer selection

Compare full-validator participation with sampled committees. For committee designs, quantify capture risk per selection and across repeated selections, with explicit adversarial stake and corruption assumptions.

Study randomness bias, withholding, grinding, predictability, denial of service against known proposers, and fallback behavior. Backup proposers require clear rules for conflicting proposals and finality; they are not merely a scheduling optimization.

### Lightweight verification and data availability

Define what lightweight participants actually verify and what they must download, retain, or trust. Separate execution correctness from data availability, censorship resistance, and independent recovery of chain state.

Proof-based execution must account for prover hardware, proof generation latency, verification costs, unavailable or invalid proofs, and the risk of concentrating block production in a few provers. Succinctness and zero knowledge are separate properties; identify which properties the protocol needs.

### Post-quantum architecture

Inventory every cryptographic dependency and its assumptions. Do not infer whole-system post-quantum security from the choice of transaction or validator signature alone.

Evaluate signature and key sizes, verification costs, message amplification, aggregation or threshold requirements, randomness mechanisms, leader-selection primitives, proof systems, key rotation, and algorithm migration. State the adversary model and distinguish established constructions from research proposals.

### Bootstrapping and recovery

Specify how new and returning nodes identify the canonical finalized history and validator set. Examine long-range attacks, equivocation, key compromise, stake withdrawal, outages, and any checkpoint or weak-subjectivity assumptions. Recovery procedures must make their trust and coordination requirements explicit.

## Research and decision method

For every major design decision:

1. Define the problem and the intended measurable outcome.
2. State the security, economic, cryptographic, and network assumptions.
3. Present viable alternatives and explain their tradeoffs.
4. Analyze attack vectors, failure modes, and recovery behavior.
5. Analyze consequences for participation and decentralization.
6. Analyze performance, hardware, bandwidth, and network consequences.
7. Evaluate post-quantum implications across all dependencies.
8. Compare relevant existing protocols where useful, using primary sources and noting differences in assumptions.
9. Distinguish established techniques, adaptations, and novel or experimental ideas; identify evidence gaps.
10. Record a decision as accepted only after explicit agreement with the project owner.

Disagreement is part of the process. An attractive idea should be rejected or revised when its assumptions, security properties, or operational costs do not support the project goals.

Research notes should separate facts supported by sources, analytical conclusions, hypotheses, and unresolved questions. Quantitative claims must include their workload, environment, assumptions, and evidence.

## Roadmap

### Phase 1 — Scope and measurable requirements

- Define financial workloads and distinguish validator, proposer, executor, prover, archival node, and light-client roles where relevant.
- Define safety, liveness, finality, permissionless participation, and decentralization in operational terms.
- Establish candidate budgets for block interval, finality latency, throughput, bandwidth, storage, memory, and computation.
- Identify conflicting goals, priorities, and acceptable tradeoffs.

**Deliverable:** a versioned requirements draft with open questions and acceptance criteria. Numerical targets beyond the initial approximately one-second timing ambition require explicit agreement.

Drafts for review:

- [Financial workloads and consensus scope](docs/requirements/financial-workloads-and-scope.md) — issue #1; candidate profiles, subsystem boundaries, and open scope decisions. Not yet approved.
- [Cryptography and specialized proof implementation review](docs/research/crypto-zk-implementation-review.md) — concrete ML-DSA, Poseidon2, and recursive-proof candidates; observed parameters, reuse constraints, and limits of the proof statements. No dependencies selected.
- [Participant roles and candidate device profiles](docs/requirements/participant-roles-and-device-profiles.md) — issue #2; verification duties, trust boundaries, device fixtures, and participation tradeoffs. Proposed requirements, not device-support guarantees.

- [Phase 1: Consensus guarantees, assumptions, and decentralization criteria](docs/requirements/consensus-guarantees-and-decentralization.md) — issue #3; draft for review, with unresolved decisions explicitly marked.

- [Phase 1: Proposed performance and resource budgets](docs/requirements/performance-and-resource-budgets.md) — issue #4; draft for review, with unresolved decisions explicitly marked.

- [Phase 1: Requirement tradeoffs and proposed priorities](docs/requirements/tradeoffs-and-priorities.md) — issue #5; draft for review, with unresolved decisions explicitly marked.

### Phase 2 — Threat model and comparative research

- Define adversarial capabilities, fault thresholds, network conditions, and economic assumptions.
- Compare relevant protocol families, including BFT-based Proof of Stake, chain-based approaches, and committee-based designs.
- Investigate proposer concentration, committee capture, censorship, long-range attacks, randomness manipulation, and denial of service.
- Build a post-quantum dependency assessment and identify constructions that need further research.

**Deliverable:** a threat model and evidence-backed comparison of candidate approaches, including rejected alternatives and unresolved risks.

### Phase 3 — Candidate architecture and feasibility

- Develop candidate architectures with explicit subsystem boundaries and trust assumptions.
- Analyze timing, message complexity, validator-set changes, proposer rotation, data availability, and recovery.
- Estimate resource costs and identify likely centralization pressures.
- Assess whether existing Rust consensus frameworks can satisfy the agreed requirements.
- Propose targeted simulations or benchmarks where analysis alone is insufficient; executable experiments require explicit authorization.

**Deliverable:** architecture proposals and a feasibility assessment. No candidate becomes the selected protocol by default.

### Phase 4 — Agreed consensus requirements specification

- Resolve or explicitly defer open questions with their consequences documented.
- Assign stable requirement identifiers and distinguish mandatory properties from targets and optional capabilities.
- Define verifiable acceptance criteria and trace each requirement to its rationale, assumptions, and agreed decisions.
- Document remaining research risks and the evidence required before protocol design or implementation proceeds.

**Deliverable:** an explicitly approved consensus requirements specification that can guide subsequent protocol design.

### Phase 5 — Protocol design and later implementation

After approval of the requirements, specify protocol state machines, message formats, safety and liveness arguments, and a validation plan. Rust implementation begins only when explicitly requested. This repository will then hold the consensus code alongside its specification and decision history.

## Repository workflow

This README is the initial research roadmap. Smaller, bounded work items can later become GitHub issues, linked to the relevant roadmap phase and requirement or research question.

Each research issue should state its problem, scope, assumptions, alternatives to investigate, expected deliverable, and completion criteria. Closing an issue does not by itself approve a protocol decision.

As work grows, documents can be organized under:

| Proposed location | Purpose |
| --- | --- |
| `docs/requirements/` | Requirements, definitions, measurable targets, and acceptance criteria. |
| `docs/research/` | Comparative studies, source references, cryptographic assessments, and feasibility analyses. |
| `docs/threat-model/` | Adversary models, attack scenarios, and security assumptions. |
| `docs/decisions/` | Proposed, accepted, rejected, or superseded decisions with rationale and explicit approval references. |
| `docs/specification/` | Protocol specifications after the requirements phase. |

These locations are a proposed documentation structure, not an instruction to implement the protocol now. Accepted decisions must retain their rationale and approval reference; later revisions must preserve the decision history.

## Current decision status

The project direction, research-first workflow, and need for explicit agreement on protocol decisions are established by the project brief. **No specific consensus architecture has been selected.**
