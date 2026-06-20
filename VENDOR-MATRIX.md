# BRACE Vendor Coverage Matrix (Condensed)

**Snapshot:** Q3 2026

This is a condensed, public reference. It maps each BRACE control to what AWS, Google Cloud, Microsoft Azure, OpenAI's platform, Anthropic's platform, and the open-source ecosystem have shipped. For the full per-vendor detail and the framework itself, see the BRACE paper, *A Unified Security Framework for Autonomous AI Agents* (2026), and the repository at https://github.com/brace-ai-security/brace.

> **Staleness warning.** Agent-platform offerings move fast. Expect a large fraction of these cells to be stale within six months. Treat this as a starting point for a deployment-specific audit, not a substitute for one. Verify before relying on it.

## How to read the signals

- A named capability or **✓** — the vendor has a directly-applicable offering.
- **partial** — coverage exists but at insufficient granularity for BRACE's autonomous-agent regime.
- **(none)** — no vendor-side coverage; the customer must roll their own.
- **(managed)** — the layer is fully managed by the vendor and not customer-inspectable.
- **(manual)** / **(custom)** — achievable, but only via manual operation or customer-assembled glue.

## At-a-glance matrix

| # | Control | AWS | Google Cloud | Microsoft Azure | OpenAI Platform | Anthropic Platform | Open Source |
|---|---|---|---|---|---|---|---|
| 1 | Architecture | ✓ | ✓ | ✓ | partial | partial | ✓ |
| 2 | Fine-grained API access | ✓ | ✓ | ✓ | partial | partial | ✓ |
| 3 | Container security | ✓ | ✓ | ✓ | (managed) | (managed) | ✓ |
| 4 | Harness security | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| 5 | Data security | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| 6 | Memory security | partial | partial | partial | partial | partial | ✓ |
| 7 | Behavioral security | ✓ | ✓ | ✓ | partial | partial | ✓ |
| T1 | Hierarchical identity | partial | partial | partial | (none) | (none) | partial |
| T2 | Context-size logging | (none) | (none) | (none) | partial | partial | partial |
| T3 | Sub-agent tracking | (none) | (none) | (none) | (none) | (none) | partial |
| 8 | Kill switch | ✓ (custom) | ✓ (custom) | ✓ (custom) | (manual) | (manual) | ✓ |
| 9 | Audit trail | ✓ | ✓ | ✓ | partial | partial | ✓ |

## Key takeaway

The cross-cutting traceability layer (T1–T3) is where every vendor is weakest. This is the single most important finding from the matrix: **the controls BRACE adds beyond the reviewed literature are precisely the ones no vendor has shipped yet.** Customers wanting BRACE-level traceability must build it themselves on open-source primitives (SPIFFE + OpenTelemetry GenAI + Langfuse).

## Per-control recap

Each control has a one-line statement of what BRACE requires and the one cross-cutting gap that spans most or all vendors. The full per-vendor cell prose lives in the paper.

**1. Architecture (build-time)**
- *Requires:* environment separation, blast-radius isolation, and a network topology an agent in one environment cannot escape under any failure mode.
- *Gap:* cross-cloud agent deployments have no native isolation story and no vendor-neutral "this agent type may only run in this region" primitive.

**2. Fine-grained API access (build-time)**
- *Requires:* capability-scoped tokens (`read:invoices`, not `invoices:*`), with per-agent-type capability sets enforced at issuance and at use.
- *Gap:* vendors address authorization *policies* but not *issuance* with frozen, per-agent-type capability scopes; that glue is customer-side.

**3. Container security (build-time)**
- *Requires:* signed images (hash referenced in the agent-type-id), a minimal toolset, and ID-propagating tokens minted inside the container.
- *Gap:* "ID-propagating tokens" whose claims reference the container image hash are not a turnkey feature anywhere; configurable but customer-engineered.

**4. Harness security (build-time)**
- *Requires:* a role-scoped harness, the system prompt as a versioned diff-reviewed artifact, built-in-tool allowlists, and iteration/token-budget limits.
- *Gap:* no vendor treats the *system prompt* as a first-class versioned artifact; all treat it as a configuration string.

**5. Data security (run-time)**
- *Requires:* treat all external data as untrusted; sanitize, redact, and validate at the boundary; isolate untrusted-data channels from instruction channels.
- *Gap:* direct prompt injection is handled reasonably; *indirect* injection (instructions embedded in retrieved docs, tool outputs, uploads) is largely on the customer.

**6. Memory security (run-time)**
- *Requires:* multi-level memory scoping, validated writes, and provenance for every memory entry.
- *Gap:* no vendor offers per-entry provenance (which agent wrote it, at what context size, under what task, with what input). BRACE-novel; no vendor coverage.

**7. Behavioral security (run-time)**
- *Requires:* baseline normal behavior early in production, alert on deviation, and route higher-severity events to human review.
- *Gap:* no vendor sells behavioral monitoring at the fidelity of OpenAI's own internal monitoring (99.9% coverage); the published template is not productized for customers.

**T1. Hierarchical identity (cross-cutting)**
- *Requires:* a five-level identity hierarchy (org → team → agent-type-id → agent-instance-id → sub-agent-id) propagated through every log and downstream call.
- *Gap:* no vendor implements all five levels as one primitive; Microsoft Entra Agent ID (parent-child relationships) comes closest.

**T2. Context-size logging (cross-cutting)**
- *Requires:* per-action context-size emission in audit logs (tokens in the working window at each action), enabling context-conditioned anomaly baselines.
- *Gap:* every cloud and platform vendor lacks this at the audit-log layer; the open-source stack (OTel GenAI + Langfuse) is the only path. BRACE-novel; essentially no vendor coverage.

**T3. Sub-agent tracking (cross-cutting)**
- *Requires:* parent-child relationships and the parent-passed system prompt captured in audit logs; distributed tracing extended to prompt-as-data propagation.
- *Gap:* the weakest-covered control of all. No major vendor logs *the prompt a parent passed to its sub-agent*; only custom OTel instrumentation does.

**8. Kill switch (run-time)**
- *Requires:* a *tested* mechanism to stop an agent mid-execution within a risk-appropriate wall-clock budget, propagating recursively to sub-agents, leaving work in a safe state.
- *Gap:* a tested kill switch with recursive sub-agent propagation is operational work no vendor sells; every deployment must build and test it.

**9. Audit trail (cross-cutting / post-execution)**
- *Requires:* every action by every instance captured with full hierarchical identity, context size, parent-child relationships, tool calls, decisions, and outcomes — the full execution graph, with risk-matched retention.
- *Gap:* vendor trails capture API-level events but miss agent-internal state (context size, sub-agent prompt provenance, memory writes); customers must layer their own instrumentation.

## Cross-cutting observations

1. **The traceability layer (T1–T3) is the weakest vendor coverage.** Every cloud and platform vendor lacks at least one of: five-level hierarchical identity, context-size logging, sub-agent prompt provenance — the controls BRACE identifies as absent from any reviewed framework.
2. **AWS Bedrock AgentCore is currently the most BRACE-complete commercial platform** (harness, container isolation via Firecracker, Cedar-based authorization, managed memory scoping). Its weaknesses are everyone's: T1–T3 and the tested kill switch.
3. **Microsoft Entra Agent ID is the strongest identity-specific offering** — the only vendor product documenting parent-child agent identity relationships.
4. **Open source is competitive across every layer except T1.** Langfuse + Mem0 + LangGraph + OTel GenAI + SPIFFE reproduce most of the stack with significant operational investment; hierarchical workload identity at multi-tenant SaaS scale is where it genuinely lags.
5. **No vendor sells behavioral monitoring at published fidelity.** The closest commercial alternatives operate at lower coverage and rely on customer-defined behavioral baselines.

## Reference implementations & open conformance specs

The matrix above tracks managed-platform coverage. Separately, open, conformance-testable *implementations* of individual BRACE controls are starting to appear. These are useful as concrete blueprints: BRACE defines what to enforce and in what order; a reference spec shows one way to build it. They realize BRACE controls — they do not replace the framework.

**Microsoft Agent Governance Toolkit** (MIT-licensed; `microsoft/agent-governance-toolkit`) ships RFC-2119 conformance specs with multi-language SDKs. The most directly BRACE-relevant is the **MCP Security Gateway 1.0** — a policy-enforcing interception layer between an agent and its MCP tool servers (the Ecosystem boundary). It maps onto BRACE as follows:

| BRACE control | MCP Security Gateway mechanism |
|---|---|
| Ecosystem — MCP/tool supply chain | Security scanner: tool poisoning, **rug-pull fingerprinting** (SHA-256 of description + schema, re-checked on re-registration), cross-server typosquatting (Levenshtein ≤ 2), hidden-instruction / description-injection detection; CVE feed via OSV |
| 5 — Data security (run-time) | Response scanning (block / sanitize / log) for prompt injection, credential and PII leaks, exfiltration URLs |
| 4 — Harness security (build-time) | Allow / deny / sensitive tool lists with approval workflow; sliding-window rate limiting |
| T1 / Agent identity & A2A boundary | HMAC-SHA256 message signing with replay protection; session auth (TTL, concurrency); auth-method enforcement (oauth2 / mTLS / api-key / bearer) |
| Configuration — drift detection | Schema-drift detection (eight drift types, severity-classified; baseline vs. current fingerprint) |
| 8 — Kill switch / 9 — Audit trail | Audit entry per decision; circuit breaker; **fail-closed** semantics (deny on error, never silently permit) |

Sibling specs in the same toolkit implement other BRACE controls — *Audit-Compliance* (Control 9), *AgentMesh Identity & Trust* (T1 / Agent identity), *Agent Hypervisor Execution Control* (Controls 1 and 3, sandboxing), and *Agent OS Policy Engine* (Control 4, authorization).

Pinned for durability to commit `013c7fb` (2026-05-17): [MCP-SECURITY-GATEWAY-1.0.md](https://github.com/microsoft/agent-governance-toolkit/blob/013c7fb44ae589c21488b2746fef9ee44c4f1416/docs/specs/MCP-SECURITY-GATEWAY-1.0.md).

> A toolkit can implement the controls; BRACE is the cross-vendor organization, the adoption order, and the sign-off gate — plus the agent-granularity instrumentation (T1–T3) that the matrix above shows no implementation yet covers in full.

---

*Part of BRACE, a security framework for autonomous AI agents. CC BY 4.0.*
