# BRACE Resources

The agent-security ecosystem, mapped to BRACE. BRACE **composes with — it does not replace** the work below: use the standards to know what can go wrong, the reference implementations to see one way to build a control, and the open-source building blocks to actually build it.

Each entry notes the BRACE concern it most relates to: **Build-time · Run-time · Agent · Config · Ecosystem · Observability · Governance**.

> Curated and verified mid-2026. **Listing is not endorsement**, and this space moves fast — verify status before relying on anything. The canonical, always-current version of this list lives at <https://braceframework.org/resources/>. Know something we should add? [Suggest a resource](https://github.com/brace-ai-security/brace/issues/new?template=get-listed.yml).

---

## Standards & threat models

Know what can go wrong, and the governance you must answer to. BRACE is the technical control layer beneath these.

### Agentic threat catalogs

| Resource | What it is | BRACE |
|---|---|---|
| [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) | The flagship ranked risk list (ASI01–ASI10) for autonomous agents — the closest external analog to BRACE's scope. | Agent · Run-time · Ecosystem |
| [OWASP Top 10 for LLM Applications (2025)](https://genai.owasp.org/llm-top-10/) | The ten most critical LLM-app risks; the industry baseline. | Run-time · Build-time |
| [OWASP Multi-Agentic System Threat Modeling Guide](https://genai.owasp.org/resource/multi-agentic-system-threat-modeling-guide-v1-0/) | Applies the agentic threat taxonomy to multi-agent systems. | Ecosystem · Agent |
| [MITRE ATLAS](https://atlas.mitre.org/) | ATT&CK-style knowledge base of real adversary techniques against AI systems, now including agentic techniques. | Run-time · Ecosystem |
| [CSA MAESTRO](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro) | Seven-layer threat-modeling method for agentic systems. | Governance · Ecosystem |
| [OWASP AIVSS](https://aivss.owasp.org/) | An AI Vulnerability Scoring System extending CVSS with agentic amplifiers. *Pre-1.0.* | Agent · Run-time |

### Governance frameworks & regulation

| Resource | What it is | BRACE |
|---|---|---|
| [NIST AI RMF (AI 100-1)](https://www.nist.gov/itl/ai-risk-management-framework) | The de facto U.S. governance baseline — Govern, Map, Measure, Manage. | Governance · Config |
| [NIST Generative AI Profile (AI 600-1)](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf) | GenAI-specific companion to the AI RMF — 12 risk categories. | Run-time · Governance |
| [ISO/IEC 42001:2023](https://www.iso.org/standard/42001) | The first certifiable AI management-system standard. *Paywalled.* | Governance · Config |
| [ISO/IEC 23894:2023](https://www.iso.org/standard/77304.html) | AI-specific risk-management guidance. *Paywalled.* | Governance |
| [EU AI Act (Reg. 2024/1689)](https://artificialintelligenceact.eu/) | The first comprehensive AI regulation. *(Cite EUR-Lex as authoritative.)* | Governance · Agent |
| [Google SAIF 2.0](https://saif.google/) | A practitioner security framework whose 2.0 release adds agent security. *Vendor-authored.* | Agent · Build-time · Run-time |

### Government & multi-stakeholder guidance

| Resource | What it is | BRACE |
|---|---|---|
| [Five Eyes — Careful Adoption of Agentic AI Services](https://www.cisa.gov/news-events/news/cisa-us-and-international-partners-release-guide-secure-adoption-agentic-ai) | Joint CISA/NSA/UK/CA/AU/NZ guidance (2026) — privilege, design/config, behavioral, structural, accountability: almost one-to-one with BRACE's five concerns. | All concerns |
| [NIST CAISI — AI Agent Standards Initiative](https://www.nist.gov/caisi/ai-agent-standards-initiative) | The first U.S. program dedicated to agentic-AI standards. *Mostly drafts; track it.* | Agent · Ecosystem |
| [Coalition for Secure AI (CoSAI)](https://www.coalitionforsecureai.org/) | An OASIS open project shipping agentic secure-design patterns and an agent identity/governance framework. | Agent · Ecosystem · Build-time |

## Reference implementations & conformance specs

Concrete blueprints — one way to actually build a control. BRACE defines what to enforce and in what order; these show how.

| Resource | What it is | BRACE |
|---|---|---|
| [Microsoft Agent Governance Toolkit](https://github.com/microsoft/agent-governance-toolkit) | RFC-2119 conformance specs + SDKs (MIT). Its [MCP Security Gateway](https://braceframework.org/guides/ecosystem/) spec implements the Ecosystem boundary; siblings cover hypervisor execution control (Build-time), AgentMesh identity (Agent), audit-compliance (Ecosystem), and SRE/kill-switch governance. | Ecosystem · Build-time · Agent · Config |
| [Anthropic — Trustworthy Agents framework](https://www.anthropic.com/research/trustworthy-agents) | A four-layer shared-responsibility model (Model / Harness / Tools / Environment); its harness-as-governed-layer treatment mirrors BRACE's prompt-and-harness-as-artifact. | Config · Build-time · Run-time |

## Managed platforms

Commercial runtimes that ship some controls out of the box. For per-control vendor coverage, see [VENDOR-MATRIX.md](VENDOR-MATRIX.md).

| Resource | What it is | BRACE |
|---|---|---|
| [Amazon Bedrock AgentCore](https://aws.amazon.com/bedrock/agentcore/) | Serverless agent runtime on Firecracker microVMs, with Cedar authz, managed memory, OAuth identity, an MCP/API gateway, and OTel observability. | Build-time · Agent · Run-time · Ecosystem |
| [Microsoft Entra Agent ID](https://learn.microsoft.com/en-us/entra/agent-id/what-is-microsoft-entra-agent-id) | A purpose-built agent identity type with parent-child relationships and no standalone credentials. | Agent |
| [Microsoft Foundry Agent Service](https://learn.microsoft.com/en-us/azure/foundry/agents/overview) | Per-agent Entra identity, an AI Gateway enforcing tool-level controls, plus Purview/Defender governance. | Agent · Ecosystem · Run-time |
| [Google Gemini Enterprise Agent Platform](https://cloud.google.com/products/gemini-enterprise-agent-platform) | Consolidated agent platform with an isolated Agent Sandbox and Apigee as an MCP bridge. *Heavy rebrand churn.* | Build-time · Ecosystem |
| [Anthropic Managed Agents](https://www.anthropic.com/research/trustworthy-agents) | Enterprise agent governance with IdP connectors and Workload Identity Federation. | Agent · Config |
| [OpenAI AgentKit](https://openai.com/index/introducing-agentkit/) | A Connector Registry for agent→tool connections, plus open-source [Guardrails](https://github.com/openai/openai-guardrails-python). *Agent Builder sunsets 2026-11-30.* | Ecosystem · Run-time |

## Open-source building blocks

The primitives you assemble BRACE-level controls from.

### Identity & attribution → Agent

| Resource | What it is |
|---|---|
| [SPIFFE / SPIRE](https://spiffe.io/) | CNCF-graduated workload identity — short-lived cryptographic SVIDs, no long-lived secrets. |
| [Agent identity standards (IETF / NIST / OAP)](https://datatracker.ietf.org/doc/draft-klrc-aiagent-auth/) | The 2025–2026 frontier of agent auth: an IETF draft, NIST's concept paper, and the Open Agent Passport spec. *Emerging; none ratified.* |

### Authorization → Config / Agent / Ecosystem

| Resource | What it is |
|---|---|
| [Cedar](https://github.com/cedar-policy/cedar) | AWS's formally-verified authorization language/engine; the policy layer behind AgentCore Policy. |
| [OpenFGA](https://github.com/openfga/openfga) | CNCF relationship-based (Zanzibar-style) authorization — fits agent delegation chains. |
| [Open Policy Agent (OPA)](https://github.com/open-policy-agent/opa) | CNCF-graduated general-purpose policy engine (Rego). |

### Observability → the three BRACE observability requirements

| Resource | What it is |
|---|---|
| [OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/) | Standard `gen_ai.*` / agent span conventions; directly underpins all three BRACE observability requirements. |
| [Langfuse](https://github.com/langfuse/langfuse) | Open-source LLM/agent tracing, evals, prompt management; ingests OTel GenAI. |
| [Arize Phoenix](https://github.com/Arize-ai/phoenix) | Self-hostable, vendor-neutral tracing + evals on OpenTelemetry; strong span-tree view for sub-agent provenance. |
| [OpenLLMetry (Traceloop)](https://github.com/traceloop/openllmetry) | OpenTelemetry-native, non-intrusive instrumentation for LLM apps. |
| [Helicone](https://github.com/Helicone/helicone) | Open-source LLM observability plus a Rust AI gateway. |

### Sandboxing & isolation → Build-time

| Resource | What it is |
|---|---|
| [Firecracker](https://github.com/firecracker-microvm/firecracker) | AWS's Rust microVM monitor — hardware-enforced isolation, ~125ms boot. |
| [gVisor](https://github.com/google/gvisor) | Google's user-space application kernel — stronger than namespaces, lighter than a VM. |
| [E2B](https://github.com/e2b-dev/E2B) | Open-source infrastructure to run AI-generated code in Firecracker-backed microVM sandboxes. |
| [Daytona](https://github.com/daytonaio/daytona) | Elastic infrastructure for running AI-generated code with sub-90ms cold starts. *AGPL-3.0.* |
| [Modal Sandboxes](https://modal.com/docs/guide/sandbox) | gVisor-isolated containers with deny-by-default inbound networking. *Hosted; SDK open source.* |

### Guardrails & runtime defense → Run-time

| Resource | What it is |
|---|---|
| [NeMo Guardrails](https://github.com/NVIDIA-NeMo/Guardrails) | NVIDIA's programmable input/output rails — content safety, jailbreak detection, topic control, PII. |
| [Guardrails AI](https://github.com/guardrails-ai/guardrails) | Composable input/output validators via the Guardrails Hub. |
| [Llama Guard / Prompt Guard](https://github.com/meta-llama/PurpleLlama) | Meta's classifier models for content safety and injection/jailbreak detection. |
| [LLM Guard](https://github.com/protectai/llm-guard) | Input/output scanners to detect, redact, and sanitize prompts/responses. |

### Red-team & testing → verify your Run-time defenses

| Resource | What it is |
|---|---|
| [garak](https://github.com/NVIDIA/garak) | "Nessus for LLMs" — 50+ probes for injection, jailbreaks, and data leakage. |
| [PyRIT](https://github.com/microsoft/PyRIT) | Microsoft's red-team orchestration framework for multi-turn adversarial attacks. |
| [ModelScan](https://github.com/protectai/modelscan) | Scans serialized model files for unsafe code / serialization attacks before load. |

### MCP & tool supply-chain security → Ecosystem

| Resource | What it is |
|---|---|
| [Snyk Agent Scan (mcp-scan)](https://github.com/snyk/agent-scan) | The flagship MCP scanner (ex-Invariant Labs) — tool poisoning, injection, tool-shadowing, rug pulls. |
| [Cisco MCP Scanner](https://github.com/cisco-ai-defense/mcp-scanner) | Discovers MCP tools and scans descriptions/schemas with YARA rules. |
| [Agentic Radar](https://github.com/splx-ai/agentic-radar) | Static scanner for agentic workflows — maps tools, detects MCP servers, surfaces vulnerabilities. |

### Memory, signing & incident knowledge

| Resource | What it is |
|---|---|
| [Mem0](https://github.com/mem0ai/mem0) | An agent memory layer with per-user keys and a request audit log — useful toward memory provenance. |
| [Sigstore / cosign](https://github.com/sigstore/cosign) | Keyless artifact/container signing with a transparency log — provenance for signed agent builds. |
| [SLSA](https://slsa.dev/) | Supply-chain Levels for Software Artifacts — incrementally adoptable build-integrity levels. |
| [AI Incident Database (AIID)](https://incidentdatabase.ai/) | A long-running index of real-world AI harms — the threat priors behind "what actually goes wrong." |
| [AI Vulnerability Database (AVID)](https://avidml.org/) | An open knowledge base of GPAI/agent failure modes. |

## Commercial security products

Notable commercial offerings, clearly flagged. Several pioneered techniques now common across the field.

| Resource | What it is | BRACE |
|---|---|---|
| [Lakera Guard](https://www.lakera.ai/) · [Gandalf](https://gandalf.lakera.ai/) | Runtime prompt-injection/threat detection (now part of Check Point). **Gandalf** is a free prompt-injection game worth trying. | Run-time |
| [Cisco AI Defense](https://www.cisco.com/site/us/en/products/security/ai-defense/index.html) | End-to-end model/app/agent/MCP protection (ex–Robust Intelligence); its [MCP Scanner](https://github.com/cisco-ai-defense/mcp-scanner) is open source. | Run-time · Ecosystem |

---

*Part of BRACE, a security framework for autonomous AI agents. CC BY 4.0.*
