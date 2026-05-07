# Security Report Summary

## Executive Summary
This report set provides an end-to-end view of prompt-injection risk in tool-using AI agents: `threats.md` enumerates nine attack categories with severity ratings (three Critical, four High, two Med) and identifies indirect injection, confused-deputy privilege escalation, and side-channel data exfiltration as the top-priority risks; `mitigations.md` maps defensive controls — least-privilege tooling, human-in-the-loop approval, egress filtering, memory sanitization, and continuous adversarial testing — onto those threats; and this summary ties both documents to the operational posture in `security-notes.md`. The severity model used here aligns conceptually with **CVSS** (Common Vulnerability Scoring System, the FIRST.org industry standard for vulnerability scoring), where our Critical/High/Med/Low bands roughly correspond to the CVSS v3.1/v4.0 qualitative ranges (Critical ≥ 9.0, High 7.0–8.9, Medium 4.0–6.9, Low 0.1–3.9). Our top-rated threats reflect the same factor weighting CVSS uses — high impact on Confidentiality, Integrity, and Availability; low Attack Complexity; and, for indirect injection, no required user interaction — making this catalog directly translatable into CVSS vectors when individual vulnerabilities are filed downstream.

## Overview
This report set documents the prompt-injection and agent-misuse threat surface for AI agents that have access to tools, persistent memory, and external content. It accompanies the higher-level notes in `security-notes.md` and is organized into two companion documents: `threats.md` and `mitigations.md`.

## Key Findings

1. **The trust boundary problem is structural.** LLMs process system prompts, user input, tool output, and retrieved documents in the same token stream. Unlike SQL parameterization or HTML escaping, there is no mathematically sound way to mark text as "data only." Defenses are probabilistic.

2. **Tool access is the multiplier.** Without tools, prompt injection is a misbehavior problem. With tools — file write, shell, network, paid APIs — injection becomes a confused-deputy attack with real-world blast radius.

3. **External content is the most common vector.** Indirect injection through webpages, emails, PDFs, MCP server descriptions, and shared skills is harder to defend than direct user injection because the user themselves never sees the malicious payload.

4. **Persistence amplifies impact.** Memory, RAG indexes, and multi-agent chains let a single successful injection survive across sessions and propagate across components — turning one-shot exploits into long-lived backdoors.

## Recommended Posture
- Adopt least-privilege tool design and require human confirmation for high-risk actions.
- Enforce egress controls and secret hygiene to neutralize the most common exfiltration paths.
- Treat all tool output and retrieved content as untrusted; never as instructions.
- Build defense in depth — no single filter, prompt, or guardrail is sufficient.
- Make adversarial testing a continuous practice, not a one-time audit.

## Companion Documents
- [`threats.md`](./threats.md) — catalog of injection and agent-misuse threat patterns.
- [`mitigations.md`](./mitigations.md) — defensive controls and operational practices.
- `../security-notes.md` — broader notes on prompt-injection risk in AI agents.
