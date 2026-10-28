# Pathfinder

**Adaptive Infrastructure Security Research Engine**

Pathfinder is an open-source platform for intelligent internal infrastructure assessment. It combines network discovery, topology reconstruction, configuration analysis, attack-path reasoning, and evidence-driven validation into an adaptive investigation workflow.

Rather than treating infrastructure as a collection of isolated hosts, Pathfinder models the environment as a graph of interconnected assets, network boundaries, services, policies, and trust relationships.

The objective is simple: discover what is reachable, understand how systems interact, identify where security assumptions fail, and validate findings against observable evidence.

---

<p align="center">
  <strong>DISCOVERY · TOPOLOGY · REASONING · VALIDATION</strong>
</p>

<p align="center">
  <code>Open Source</code> &nbsp; <code>Python</code> &nbsp; <code>FastAPI</code> &nbsp; <code>Network Analysis</code> &nbsp; <code>LLM-Assisted Investigation</code>
</p>

---

## 01 / Research Direction

Conventional infrastructure scanners primarily operate through predefined checks against individual assets. This approach can identify known weaknesses, but it often lacks an understanding of the relationships that determine real infrastructure risk.

Pathfinder explores a graph-oriented, adaptive alternative.

- **Asset-centric discovery** — identify reachable hosts, interfaces, services, and network segments.
- **Topology reconstruction** — represent observed connectivity, routing relationships, and trust boundaries.
- **Configuration intelligence** — derive network behavior and potential policy violations from device configurations.
- **Adaptive investigation** — prioritize subsequent checks based on accumulated evidence and unresolved hypotheses.
- **Attack-path reasoning** — analyze chains of relationships that could expose critical infrastructure.
- **Evidence-backed validation** — distinguish inferred risks from findings supported by reproducible technical evidence.

Pathfinder is designed to evolve its understanding of an environment as new evidence becomes available.

## 02 / System Model

The core abstraction is an evolving infrastructure graph.

\[
G = (V, E, A)
\]

Where:

- \(V\) represents infrastructure entities, including hosts, interfaces, services, subnets, and security controls.
- \(E\) represents observed or modeled relationships, including connectivity, routing, trust, and policy dependencies.
- \(A\) represents evidence-backed attributes associated with each entity and relationship.

The graph is not merely a visualization. It provides the foundation for asset correlation, investigation prioritization, reachability analysis, and attack-path reasoning.

Each relationship should retain its provenance: whether it was observed on the network, derived from configuration, imported from an external inventory, or inferred by an analysis engine.

## 03 / Investigation Pipeline

```text
┌─────────────────────────────────────────────────────┐
│                 ASSESSMENT SCOPE                     │
│          Authorized targets and exclusions          │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│               DISCOVERY ENGINE                      │
│       Hosts · Services · Interfaces · Routes        │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│              INFRASTRUCTURE GRAPH                   │
│       Assets · Relationships · Trust Boundaries     │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│               ANALYSIS ENGINES                      │
│   Configurations · Reachability · Policy · Paths    │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│             INVESTIGATION PLANNER                   │
│      Hypotheses · Priorities · Approved Actions     │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│              EVIDENCE VALIDATION                    │
│       Verification · Confidence · Contradictions     │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────┐
│             FINDINGS AND REMEDIATION                │
│       Evidence · Impact · Recommendations           │
└─────────────────────────────────────────────────────┘
                         │
                         └──── New evidence feeds
                               the next iteration
```

The investigation planner determines which approved checks are most useful next. The underlying analysis engines and validation tools remain responsible for producing technical evidence.

## 04 / Operational Modes

### Live infrastructure assessment

Assess an authorized internal network from a defined vantage point.

- Discover reachable assets and exposed services.
- Correlate interfaces, addresses, and observed network relationships.
- Investigate management-plane exposure and segmentation weaknesses.
- Identify potential paths between security zones.
- Reassess priorities as the infrastructure graph changes.

### Configuration analysis

Analyze uploaded network-device configurations independently of live discovery.

- Parse and normalize supported vendor configurations.
- Evaluate routing, VLANs, ACLs, management access, and network policies.
- Identify potentially unintended reachability and configuration weaknesses.
- Preserve configuration provenance, rule references, and supporting evidence.
- Compare modeled behavior with live observations when sufficient data is available.

### Cross-source correlation

Combine live observations, configuration-derived behavior, and imported inventory data.

When sources disagree, Pathfinder should preserve the contradiction as an investigation target rather than silently choosing one interpretation.

A configuration-derived path is not automatically a confirmed live attack path. Similarly, a failed connectivity test does not independently establish that segmentation is effective.

## 05 / Evidence and Confidence

Pathfinder is intended to maintain a distinction between a suspected weakness and a verified finding.

| State | Interpretation |
|---|---|
| Inferred | Suggested by incomplete observations or analysis |
| Model-supported | Supported by configuration or deterministic modeling |
| Partially verified | Some required conditions have been observed |
| Confirmed | Required validation criteria are satisfied |
| Inconclusive | Available evidence cannot establish the claim |
| Contradicted | Evidence conflicts with the original hypothesis |
| Remediated | A subsequent assessment supports the remediation |

Each finding should retain the affected asset or flow, detection logic, evidence references, collection timestamp, source vantage point, validation method, confidence, impact, and remediation guidance.

The intended outcome is fewer unsupported claims, more reproducible findings, and a clear explanation of why each conclusion was reached.

## 06 / AI-Assisted Investigation

Pathfinder can use an LLM such as DeepSeek as an optional reasoning and planning layer.

The model may:

- Generate investigation hypotheses from structured observations.
- Rank candidate checks using impact, uncertainty, and information value.
- Recommend approved validators for unresolved questions.
- Correlate related findings across assets and network boundaries.
- Explain technical evidence and produce remediation summaries.

The model does not replace deterministic analysis or validation.

Tool execution should be mediated by a policy-enforcing controller that checks scope, permissions, rate limits, and action constraints before dispatch. Model output alone must never authorize an operation.

## 07 / Proposed Architecture

| Layer | Technology | Responsibility |
|---|---|---|
| API | FastAPI | Assessment orchestration and access control |
| Interface | React, TypeScript | Asset exploration and investigation workflows |
| Discovery | Nmap | Scoped host and service discovery |
| Network modeling | Batfish | Configuration analysis and reachability modeling |
| Graph analysis | NetworkX | Asset relationships and path analysis |
| Persistence | PostgreSQL | Assessments, findings, evidence metadata |
| Background jobs | Redis and workers | Asynchronous analysis and collection |
| Reasoning | DeepSeek API | Optional hypothesis generation and planning |

This stack is a proposed implementation direction, not a statement that every integration is already operational.

The initial implementation should favor a modular Python architecture with isolated workers and explicit interfaces between discovery, analysis, verification, and reporting. Additional services can be introduced when operational requirements justify them.

## 08 / Security and Execution Boundaries

Infrastructure assessment requires strict control over what the system can observe and execute.

Pathfinder's design principles include:

- Explicit target scopes and exclusions.
- Read-only collection by default wherever practical.
- Bounded concurrency, timeouts, and rate limits.
- Separate approval for higher-impact validation.
- Immutable evidence artifacts and auditable execution history.
- Restricted worker permissions and controlled network egress.
- Secure handling of credentials and sensitive configuration files.
- Clear reporting of visibility gaps and unsupported platforms.

Pathfinder is intended for authorized security testing of owned or explicitly permitted infrastructure.

## 09 / Development Status

Pathfinder is an evolving open-source project. The architecture and capabilities described here define the intended direction; individual features depend on implementation progress.

Initial development priorities:

1. Assessment scope enforcement and evidence schema.
2. Configuration ingestion and deterministic security rules.
3. Network discovery and asset normalization.
4. Infrastructure graph construction.
5. Reachability and segmentation analysis.
6. Evidence correlation and finding confidence.
7. Controlled AI-assisted investigation.
8. Reporting, remediation tracking, and regression testing.

## 10 / Contributing

Contributions are welcome across network protocols, configuration parsers, graph algorithms, analysis rules, validation adapters, testing infrastructure, and visualization.

For substantial changes, open an issue or discussion first to establish scope, expected behavior, test coverage, and security implications.

Security-sensitive changes should include reproducible test cases and explicit execution-boundary considerations.

---

## Pathfinder

*Infrastructure security through adaptive investigation and evidence.*

