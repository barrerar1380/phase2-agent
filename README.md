# phase2-agent

A learning project at the intersection of **AI agents** and **security engineering**. The repository contains two pieces of practical work produced while studying how LLM-based agents fail and how to govern their use inside an organization:

1. A line-by-line security risk analysis of a (deliberately weak) corporate AI usage policy.
2. A threat-and-mitigation catalog for prompt-injection and tool-using AI agents, plus an incident-response checklist for an agent compromise.

This is coursework-style output rather than a shipped product, but the artifacts are written to the standard of a real security review: every finding has a severity rating, a cited section or threat category, and a concrete remediation. The aim was to practice the same workflow a junior security analyst or AI governance reviewer would run on day one — read the document, identify the risks, rate them, and write something a stakeholder could act on.

## Contents

### Top level

| File | What it is |
|------|------------|
| `input-doc.txt` | The source artifact under review: a fictional "Acme Corporation AI Usage Policy v1.0." It was constructed to contain realistic mistakes (free-tier consumer AI for corporate data, password sharing over Slack, AI-driven HR decisions without documentation, self-deletion of incident evidence, etc.). |
| `analysis.md` | The security risk analysis of `input-doc.txt`. 17 findings (7 Critical, 6 High, 3 Medium, 1 Low) with risk scale, executive summary, per-finding writeups, summary table, and pre-issuance remediation priorities. References real regulatory frameworks (GDPR, HIPAA, PCI-DSS, EU AI Act, NYC Local Law 144, ISO 27001, SOC 2). |
| `security-notes.md` | Foundational notes on prompt-injection risks in AI agents — trust-boundary collapse, indirect injection, confused-deputy escalation, side-channel exfiltration, memory poisoning, supply-chain risk. Acts as the entry point into the `reports/` folder. |

### `reports/`

| File | What it is |
|------|------------|
| `threats.md` | Catalog of nine prompt-injection / agent-misuse threat categories with a Critical/High/Med/Low severity scale. Calls out the top three priorities (indirect injection, confused-deputy, side-channel exfiltration). |
| `mitigations.md` | Defensive controls mapped to those threats: least-privilege tooling, human-in-the-loop, input provenance, egress controls, secret hygiene, memory sanitization, multi-agent boundary enforcement, defense in depth, adversarial testing. |
| `summary.md` | Executive summary tying the threat and mitigation documents together. Notes that the severity bands align conceptually with CVSS v3.1/v4.0 qualitative ranges so individual findings translate cleanly into CVSS vectors downstream. |
| `incident-response-checklist.md` | A 10-step IR runbook for a confirmed or suspected AI-agent compromise: halt and isolate, preserve evidence, scope, rotate credentials, reverse tool actions, identify the injection vector, sanitize persistent state, notify stakeholders, patch root cause, post-incident review. |

## How the pieces relate

`input-doc.txt` → `analysis.md` is the **applied** track: take a real-shaped policy document and produce a defensible, prioritized review of it.

`security-notes.md` → `reports/threats.md` → `reports/mitigations.md` → `reports/incident-response-checklist.md` → `reports/summary.md` is the **conceptual** track: build up a model of how AI agents get attacked, how to defend them, and what to do when defense fails.

The two tracks are intentionally complementary. The policy review is where AI governance lives in practice; the threat/mitigation catalog is where the actual technical risk lives. A useful security function needs both.

## What I was practicing

- Reading a document adversarially and translating each weakness into a rated finding with a concrete remediation, rather than a vague "this is bad" comment.
- Mapping findings to the regulatory regimes that would actually be invoked in a breach (GDPR Art. 6/28/32/44, HIPAA, PCI-DSS, EU AI Act high-risk classification, NYC Local Law 144, ISO 27001 A.7.2.2, SOC 2 CC2.2 / CC6.1).
- Building a threat catalog with severity bands that line up with industry scoring (CVSS) so it could feed a real vulnerability-tracking workflow.
- Writing an incident-response checklist that respects the realities of LLM agents — preserve traces *before* you cut access, treat anything the agent could read as leaked, sanitize persistent memory, and add the exploit payload to a regression corpus.
- Treating prose as a deliverable: tight executive summaries, tables for scannability, top-priority lists at the end of each section.

## Status

Static document set. No code, no runtime. The repository is intended to be read, not executed.

## About

Built while learning AI agents and security engineering. Feedback from anyone working in AI governance, application security, or agent infrastructure is welcome.
