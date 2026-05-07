# Lessons Learned: Hands-On Evaluation of an AI Agent for Security Work

**To:** Director of Technology
**From:** Security Engineering Manager
**Re:** `phase2-agent` — practical evaluation of LLM agents on real security-engineering tasks
**Date:** 2026-05-06

---

## Purpose

This memo summarizes what I learned by running an AI agent through a structured set of security-engineering tasks. The goal was not to ship a product. It was to develop a defensible, first-hand opinion on **where AI agents create leverage for our security function, where they still need a human in the loop, and what we would need to put around them before we let them operate against production data.** The artifacts produced during the exercise live in this repository and are referenced below.

---

## What was covered

The work spans two complementary tracks. Both are the kind of output a working security team produces routinely.

### Track 1 — Applied: AI policy review
- **Input:** A deliberately weak fictional corporate AI usage policy (`input-doc.txt`), seeded with realistic mistakes — free-tier consumer AI for corporate data, password sharing over Slack, AI-driven HR decisions without documentation, employee self-deletion of incident evidence, API keys in shared drives, and so on.
- **Output:** `analysis.md` — a 17-finding security risk analysis (7 Critical, 6 High, 3 Medium, 1 Low) with a defined risk scale, executive summary, per-finding writeups citing the relevant regulatory regimes (GDPR, HIPAA, PCI-DSS, EU AI Act, NYC Local Law 144, ISO 27001, SOC 2), a summary table, and a prioritized pre-issuance remediation list.

### Track 2 — Conceptual: Agent threat modeling
- `security-notes.md` — foundational notes on prompt-injection risk in tool-using agents (trust-boundary collapse, indirect injection, confused-deputy escalation, side-channel exfiltration, memory poisoning, supply-chain risk, detection gaps).
- `owasp-notes.md` — alignment to the OWASP Top 10 for LLM Applications 2025 (LLM01 Prompt Injection, LLM02 Sensitive Information Disclosure, LLM03 Supply Chain).
- `reports/threats.md` — nine-category threat catalog with Critical/High/Med/Low severity bands and a stated top-three priority list (indirect injection, confused-deputy, side-channel exfiltration).
- `reports/mitigations.md` — defensive controls mapped to those threats: least-privilege tooling, human-in-the-loop, input provenance, egress controls, secret hygiene, memory sanitization, multi-agent boundary enforcement, defense in depth, adversarial testing.
- `reports/summary.md` — executive rollup that explicitly aligns the severity bands to CVSS v3.1/v4.0 qualitative ranges so individual findings translate into vulnerability-tracking workflows downstream.
- `reports/incident-response-checklist.md` — a 10-step IR runbook for a confirmed or suspected agent compromise (halt and isolate, preserve evidence, scope, rotate credentials, reverse tool actions, identify injection vector, sanitize persistent state, notify, patch, post-incident review).

The two tracks intentionally complement each other. Policy review is where AI **governance** lives in practice; the threat catalog is where the technical **risk** lives. A useful security function needs both.

---

## What the AI agent did autonomously

In the course of the exercise, the agent — given short, plain-English direction — produced the following without further hand-holding:

1. **Adversarial document reading.** Took a multi-section policy document and identified weaknesses at the clause level rather than at the document level. Each weakness became a discrete, rated finding with a concrete remediation, not a vague "this is bad" comment.
2. **Severity rating discipline.** Applied a consistent Critical / High / Medium / Low scale across 17 findings and 9 threat categories, and explicitly aligned those bands to CVSS qualitative ranges so the work feeds existing vulnerability-management tooling rather than living in a silo.
3. **Regulatory mapping.** Cited the specific frameworks and clauses a regulator or plaintiff would actually invoke: GDPR Art. 6/28/32/44, HIPAA, PCI-DSS, GLBA, EU AI Act high-risk classification, NYC Local Law 144, Illinois AI Video Interview Act, Title VII / EEOC, ISO 27001 A.7.2.2, SOC 2 CC2.2 / CC6.1, NIST 800-53 IA-5.
4. **Concrete remediations.** For every finding, produced an actionable control (e.g., "store API keys in a managed secret manager," "require corporate SSO-federated AI accounts only," "mandatory immediate reporting aligned to GDPR 72-hour, HIPAA 60-day").
5. **Threat catalog construction.** Built a nine-category prompt-injection threat model with severity, description, examples, and impact for each category, plus a defended top-three priority list.
6. **Mitigation mapping.** Translated the threats into a controls catalog organized around durable principles (least privilege, HITL, egress control, memory sanitization, defense in depth) rather than tool-specific recipes that would age out quickly.
7. **Operational runbook authoring.** Produced an IR checklist that respects the realities of LLM agents specifically — preserve traces *before* you cut access, treat anything the agent could read as leaked, sanitize persistent memory, add the exploit payload to a regression corpus.
8. **Executive-grade prose.** Tight executive summaries, scannable tables, a top-priority list at the end of each section. The deliverables read like work product, not chat output.
9. **Cross-document consistency.** The severity scale used in `analysis.md` is the same one used in `reports/threats.md`; the mitigations in `reports/mitigations.md` map cleanly back to threats in `reports/threats.md`; the IR checklist references the same vector taxonomy. The agent maintained coherence across a multi-file deliverable set without being told to.

The headline observation: **for structured-knowledge work where the answer is "apply a known framework rigorously and write it up well," the agent operates at the level of a competent junior analyst on day one — and produces it in minutes, not days.**

---

## What required human input or external tools

This is the part most worth being honest about. The agent did not produce this work in a vacuum.

1. **The source artifact had to be supplied.** The agent did not discover that Acme Corp had a weak policy. A human chose what to review, scoped it, and dropped the file in the working directory. Nothing in this exercise simulates a security function that finds its own work — that is still a human responsibility.
2. **Framing and scope came from the human.** Decisions like "produce a risk analysis with Critical/High/Med/Low," "create a `reports/` folder containing threats, mitigations, and an executive summary," "write an IR checklist for an agent compromise," and "tie severity to CVSS bands" were human judgment calls. Without that direction, the agent produces something more generic.
3. **External knowledge sources.** The OWASP Top 10 for LLM Applications 2025 references in `owasp-notes.md` rely on web-fetch / web-search tooling against the canonical OWASP and FIRST.org sources. Live regulatory references (GDPR article numbers, NYC Local Law 144, EU AI Act phased enforcement timeline through 2027) need to be verified against authoritative sources before publication; the agent's recall is good but not citable on its own.
4. **Quality control on facts.** Agents are fluent enough that a wrong citation reads as confidently as a right one. We treated every named statute, framework section, and standard ID as something a human reviewer must spot-check before the document leaves our team. This is a non-negotiable cost of using the tool.
5. **Judgment on what is "Critical."** The severity bands are reasonable, but the choice to rate password-sharing-over-Slack as Critical rather than High is exactly the sort of call a human reviewer needs to ratify. The agent will defend its rating articulately even if the rating is wrong; that is a feature for productivity and a hazard for governance.
6. **No execution, no live systems.** This entire repository is a static document set. The agent never touched production data, never called a live API, never consumed real customer PII. Everything we learned about its written output applies; nothing we learned tells us how it would behave with tools, network access, and persistent memory in front of it. The threat catalog in `reports/threats.md` is precisely a list of what we would need to control for before we make that leap.
7. **Iterative shaping by the human.** The artifact set evolved: severity scales were added, an executive summary was retrofitted, an IR checklist was added later, OWASP notes were added later still. That iterative shaping is human work. The agent executes each step well; it does not on its own decide that the deliverable set is incomplete.

---

## What I would tell our team based on this

1. **AI agents are a force multiplier on structured security writing.** Policy review, threat catalogs, mitigation libraries, IR runbooks, and audit-prep documentation are now hours-of-work tasks instead of days-of-work tasks. We should plan around that capacity gain.
2. **The bottleneck shifts to review, not authorship.** Our team's scarce resource becomes the senior reviewer who can spot a wrong citation, an over-rated severity, or a missing regulatory regime. We should staff for that.
3. **Governance has to precede deployment, not follow it.** Every mitigation in `reports/mitigations.md` — least privilege on tools, human-in-the-loop on destructive actions, egress controls, secret hygiene, memory sanitization, adversarial testing in CI — is something we would need *in place* before we let an agent operate against real systems. The threat catalog is, in effect, a pre-flight checklist for any internal AI deployment.
4. **The policy in `input-doc.txt` is a useful caricature, but the real ones we will review look mostly like it.** The mistakes are realistic. Free-tier consumer AI being used with corporate data is the modal failure mode in industry today, not an edge case. Our own AI usage policy — and any vendor policy we evaluate — should be measured against the same 17-point checklist.
5. **Incident response for AI agents is its own discipline.** Standard IR playbooks do not cover persistent memory poisoning, prompt-injection vectors, or the fact that anything in the agent's context window may have leaked. The 10-step checklist in `reports/incident-response-checklist.md` is a reasonable starting baseline; we should adapt it for our environment before we need it, not during the incident.

---

## Recommended next steps

1. Apply the same review methodology to **our own** internal AI usage policy and any third-party AI tooling currently in use.
2. Use the threat catalog and mitigations as the gating criteria for any internal proposal to deploy an AI agent against production data, code, or customer information.
3. Pilot the incident-response checklist in a tabletop exercise before the first real incident — agent compromise has enough novel mechanics that practicing it cold is expensive.
4. Stand up a short list of vetted enterprise AI tools (with DPAs and no-training clauses) and make the allowlist enforceable at the proxy / CASB layer, not just in policy text.
5. Budget for ongoing adversarial-testing capacity. Prompt injection is not a problem that gets solved; it is a problem that gets continuously contained.

---

## Bottom line

The exercise made the trade clear. **AI agents materially raise the floor on what a security function can produce per analyst-hour, in exchange for a permanent obligation to review their output and a new category of operational risk we have to govern before we deploy them.** The artifacts in this repository are evidence of both halves of that trade — what the agent can do, and what it can do *to us* if we deploy it without controls.
