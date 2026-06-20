# BRACE Self-Assessment and Vendor-Evaluation Question Set

BRACE is a vendor-neutral control framework for securing autonomous AI
agents — software that takes actions on its own, calls tools, and runs
without a human approving each step. The name encodes the five concerns it
organizes: **B**uild-time, **R**un-time, **A**gent, **C**onfiguration,
**E**cosystem. The framework defines nine controls plus a small set of
observability (logging and tracing) requirements. This document turns those
into questions you can score.

Part of the BRACE Project. License: CC BY 4.0.

## How to use this

There are two ways to run it. Both use the same questions.

- **Score your own deployment.** Go through each question and mark how your
  current setup answers it. You are auditing what you actually run, not what
  you intend to build.
- **Ask a vendor.** Reframe each question as "Does your platform do this?"
  and put it to the team selling you an agent platform or product. Their
  answers, and what they can show you, tell you how much of the work you
  will inherit.

### Scoring

Score every item:

- **Yes = 2** — the control is in place and you can show the evidence.
- **Partial = 1** — it is partly there, or there for some agents but not all.
- **No = 0** — it is not in place, or you cannot show it.

A "yes" you cannot back up with evidence is a "no." If someone claims a
control but cannot produce the log, config, or drill record that proves it,
score it 0.

### Tiers

Each question is tagged with an adoption tier. The tiers are the
recommended order of adoption.

- **T1 (Tier 1)** — prevent damage and establish attribution. These are the
  go/no-go items. **Any Tier-1 item you cannot answer Yes is a blocker.**
- **T2 (Tier 2)** — harden the substrate the agent runs on.
- **T3 (Tier 3)** — active detection of misbehavior.

Each control also has two halves: an **agent-scoped** half (specific to one
agent type) and an **ecosystem-scoped** half (the shared substrate every
agent runs on). Where it matters, the question says which half it asks about.

---

## Build-time controls

These cover the environment, credentials, container image, and harness — the
parts fixed before an agent ever runs. (The "harness" is the wrapper that
gives the model its tools, system prompt, and limits.)

### Control 1 — Architecture (environment isolation, network egress)

- **[C1 / T2]** Is the agent's environment isolated from production at the
  infrastructure layer (separate account, VPC, or cluster), not just
  network-segmented inside one environment?
- **[C1 / T2]** Does the agent have no network route to endpoints it is not
  authorized to call — that is, is outbound traffic (egress) restricted to a
  named allowlist with no vendor exceptions?
- **[C1 / T2]** Is multi-tenant isolation enforced by an infrastructure
  primitive, not by application code?

### Control 2 — Capability-scoped API access (least-privilege tokens)

- **[C2 / T1]** Does the agent's token grant specific capabilities (for
  example `read:invoices`) rather than wildcard or whole-service scope (for
  example `invoices:*`)?
- **[C2 / T1]** Can the agent's credentials be revoked on their own, without
  having to stop the process — a working breakglass?
- **[C2 / T2]** Is each agent type's capability set reviewed on a set cadence,
  with stale capabilities removed?

### Control 3 — Container (signed, minimal, kernel-isolated)

- **[C3 / T2]** Is the container image signed and verified at runtime before
  it is allowed to run?
- **[C3 / T2]** Is the image minimal — unused binaries such as `curl`,
  `bash`, `dig`, and `nc` are absent?
- **[C3 / T2]** Does the runtime use kernel isolation (such as gVisor,
  Firecracker, or Kata) when it processes untrusted input?

### Control 4 — Harness (tool/argument/limit scoping, destructive-verb interception)

- **[C4 / T1]** Are destructive actions (delete, drop, force-push, payment
  changes, external sends) intercepted by default — denied unless explicitly
  pre-authorized (default-deny)?
- **[C4 / T1]** Does the harness expose only the tools this agent type needs
  (a role-scoped allowlist), rather than every tool by default?
- **[C4 / T1]** Is the system prompt versioned in source control and not
  editable from a web UI?
- **[C4 / T2]** Does the harness enforce hard limits on iterations,
  wall-clock time, tokens, and per-run dollar cost, with a record when a
  limit is hit?

---

## Run-time controls

These cover what happens while the agent is running: the data it ingests,
the memory it carries, and how its behavior is watched.

### Control 5 — Data (untrusted-input validation at the boundary)

- **[C5 / T2]** Is external data validated at the infrastructure boundary,
  before it reaches the model — not left to the model to judge?
- **[C5 / T2]** Are untrusted-data channels kept structurally separate from
  instruction channels, so retrieved content cannot pose as a command?

### Control 6 — Memory (per-instance/per-type scoping, per-entry provenance)

- **[C6 / T3]** Is memory scoped per agent instance and per agent type, with
  cross-type reads denied by default?
- **[C6 / T3]** For any memory entry, can you query its provenance — which
  agent wrote it, when, at what context size, and under what task?

### Control 7 — Behavioral (security-anomaly monitoring)

- **[C7 / T3]** Is security monitoring run separately from quality
  monitoring — different baselines, thresholds, and on-call — so a security
  anomaly is not buried in quality noise?
- **[C7 / T3]** Do alerts cover evasion and abuse patterns (for example
  base64 obfuscation, self-replication, or data-exfiltration attempts),
  with baselines stratified by context-size bin rather than one flat
  threshold?

---

## Closure controls

These let you stop an agent and reconstruct what it did.

### Control 8 — Kill switch (tested, recursive, safe-state)

- **[C8 / T1]** Has the kill switch been tested recently (within the last 90
  days), with a drill record to show for it?
- **[C8 / T1]** Does killing a parent agent propagate recursively to its
  running sub-agents?
- **[C8 / T1]** Does a kill leave in-progress work in a safe state — no
  half-written transactions, no orphaned external side effects?

### Control 9 — Audit trail (full execution graph)

- **[C9 / T1]** Does the audit log capture every action — identity fields,
  context size, tool calls, decisions, and outcomes — not just the final
  output?
- **[C9 / T2]** Is audit retention defined per agent risk class, with the
  retention decision itself logged?

---

## Observability requirements

Three logging and tracing requirements that make every other control
auditable.

### T1 — Required identity fields

- **[Obs-T1 / T1]** Does every action emit all six required identity fields:
  accountable party, operational owner, tenant, agent-type-id,
  agent-instance-id, and trace context?
- **[Obs-T1 / T1]** Is the **agent-type-id** a content hash computed over the
  container image digest, harness, system prompt, model id, and config — so
  any change to what the agent is produces a new id?

### T2 — Context-size logging

- **[Obs-T2 / T2]** Does every action log the context size at the moment the
  action fired? (Context size correlates with degraded behavior, so it
  belongs on every record.)

### T3 — Sub-agent and parent-prompt provenance

- **[Obs-T3 / T3]** Is the parent-child relationship propagated in the trace
  context, so a sub-agent's lineage back to its parent is queryable?
- **[Obs-T3 / T3]** Is the parent-generated system prompt captured as audit
  data on the sub-agent's record?

---

## Reading your score

There are 24 items above, for a maximum of 48 points. Two readings matter:
the total, and the Tier-1 gate.

**The Tier-1 gate comes first.** Find every item tagged **T1** and check
that each scored **2 (Yes)**. The Tier-1 items are:

- C2 — capability-scoped tokens (and independent revocation)
- C4 — destructive-verb interception, tool allowlist, versioned prompt
- C8 — tested, recursive, safe-state kill switch
- C9 — full audit trail
- Obs-T1 — six required identity fields, content-hash agent-type-id

**Any unmet Tier-1 item is a blocker, regardless of your total score.** A
deployment that scores well overall but cannot stop a runaway agent (C8) or
cannot attribute its actions (Obs-T1) is not ready. Fix Tier-1 gaps before
anything else.

Once the Tier-1 gate is clear, read the total as a rough measure of how far
the substrate is hardened:

- **40–48** — strong. Build-time and run-time substrate is largely covered.
  Close the remaining partials.
- **28–39** — solid foundation, real gaps. Tier-1 is likely met; Tier-2 and
  Tier-3 hardening is incomplete. Prioritize the lowest-scored controls.
- **14–27** — early. You have started but the substrate is thin. Confirm the
  Tier-1 gate, then work Tier-2 in order.
- **0–13** — high exposure. Treat any autonomous deployment as provisional
  until at least the Tier-1 items are in place.

The total is a guide, not a grade. A high total with one unmet Tier-1 item
is still a no-go.

---

## Using this to evaluate a vendor

Ask the vendor the same questions, phrased as "Does your platform do this,
and can you show me?" Score their answers the same way: Yes = 2, Partial =
1, No = 0, and a claim with no evidence is a 0.

What a good answer looks like:

- **It points to a concrete artifact, not a slogan.** For C4, a good answer
  shows the destructive-verb list and a sample denied-action record, not
  "our platform is secure by design."
- **It says what you own versus what they own.** A vendor who answers
  "Partial" and explains which half you have to build is being honest about
  the work you inherit.
- **It distinguishes the two halves.** Many platforms cover the
  ecosystem-scoped half (shared isolation, egress, audit plumbing) but leave
  the agent-scoped half (per-agent-type tool allowlists, capability tokens,
  prompt review) to you. Ask which half each "Yes" refers to.
- **It cannot answer Tier-1 with a maybe.** If a vendor cannot demonstrate a
  tested kill switch (C8), capability-scoped tokens (C2), or the six identity
  fields on every action (Obs-T1), treat that as a coverage gap, not a
  detail to settle later.

A vendor who answers "Partial" across many items is signaling work the buyer
will inherit. A vendor who cannot answer at all is signaling a coverage gap.

---

*Part of BRACE, a security framework for autonomous AI agents. CC BY 4.0.*
