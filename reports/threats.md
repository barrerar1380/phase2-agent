# Threats: Prompt Injection and AI Agent Risks

## Overview
This document catalogs known threat categories affecting AI agents, with severity ratings to guide mitigation prioritization.

**Severity scale:**
- **Critical** — Immediate, high-impact exploitation path; can lead to data loss, system compromise, or persistent backdoors.
- **High** — Significant risk requiring active mitigation; commonly observed in the wild.
- **Med** — Real but bounded risk; impact depends on agent permissions and deployment context.
- **Low** — Limited impact or already addressed by standard hardening.

---

## 1. Direct Prompt Injection
- **Severity:** High
- **Description:** A user or attacker submits crafted input designed to override the system prompt.
- **Examples:** "ignore previous instructions," role-play framings, obfuscated unicode payloads that bypass surface-level pattern matching.
- **Impact:** Agent abandons safety constraints and executes attacker-directed behavior within the current session.

## 2. Indirect Prompt Injection
- **Severity:** Critical
- **Description:** Malicious instructions embedded in external content — webpages, emails, PDFs, repository files, or tool output — hijack the agent the moment it ingests the content.
- **Trigger:** No malicious user input required; attack fires during normal content consumption.
- **Impact:** Trust boundary collapse between content and instructions; the user is unaware they have been compromised.

## 3. Confused Deputy / Privilege Escalation
- **Severity:** Critical
- **Description:** An agent with file-write, shell, or network access is coerced into using its own permissions on behalf of an attacker.
- **Examples:** Exfiltrating data, modifying files, calling paid APIs the user (not the attacker) is authenticated to.
- **Impact:** Attacker borrows the user's identity and authorization without ever holding their credentials.

## 4. Data Exfiltration via Side Channels
- **Severity:** Critical
- **Description:** Injected prompts coax the agent into encoding secrets into outbound channels.
- **Channels:** Outbound URLs, markdown image tags, image-fetch requests beaconing to attacker-controlled servers.
- **Targets:** API keys, session tokens, source code, conversation history.

## 5. Memory and Context Poisoning
- **Severity:** High
- **Description:** Persistent memory, RAG indexes, and conversation history allow a single successful injection to be stored and re-triggered in later sessions.
- **Impact:** One-shot attacks become long-lived backdoors that persist across sessions and users.

## 6. Multi-Agent Chain Amplification
- **Severity:** High
- **Description:** When one agent calls another or pipes output into downstream tools, an injection in one hop propagates through the chain.
- **Risk:** Downstream components may re-interpret the malicious output with elevated trust.

## 7. Supply-Chain Injection
- **Severity:** High
- **Description:** Tool schemas, MCP server descriptions, third-party system prompts, and shared agent skills can themselves carry injections.
- **Trigger:** Activates the moment the artifact is loaded — before any user input is processed.
- **Impact:** Compromise occurs at install/configuration time, bypassing runtime input filtering.

## 8. Guardrail Bypass
- **Severity:** Med
- **Description:** Fine-tuned refusals overridden by encoded or framed payloads.
- **Techniques:** Base64-encoded payloads, language-switching, role-play scenarios, adversarial suffixes.
- **Root cause:** Guardrails relying on surface-level pattern matching rather than semantic intent.

## 9. Detection and Attribution Gaps
- **Severity:** Med
- **Description:** Successful injections often look like normal model behavior and leave little forensic trace beyond logs.
- **Impact:** Incident response and regression testing are significantly harder than for traditional injection bugs; attacks are difficult to deterministically reproduce.

---

## Top 3 Most Critical Threats

The following threats represent the highest-priority risks based on impact, exploitability, and prevalence:

1. **Indirect Prompt Injection (#2)** — Weaponizes any external content the agent reads, with no user action required. The largest attack surface in modern agent deployments.
2. **Confused Deputy / Privilege Escalation (#3)** — Turns the agent's own permissions against the user, enabling unauthorized actions under legitimate credentials. Severity scales directly with the agent's tool access.
3. **Data Exfiltration via Side Channels (#4)** — Provides the payoff path for most successful injections; without an exfiltration channel, many attacks are inert. Mitigating outbound channels limits the blast radius of every other threat.
