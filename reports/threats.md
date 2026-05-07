# Threats: Prompt Injection and AI Agent Risks

## Direct Prompt Injection
A user or attacker submits crafted input designed to override the system prompt. Examples include "ignore previous instructions," role-play framings, and obfuscated unicode payloads that bypass surface-level pattern matching.

## Indirect Prompt Injection
Malicious instructions embedded in external content — webpages, emails, PDFs, repository files, or tool output — hijack the agent the moment it ingests the content, even when the user never typed anything malicious.

## Confused Deputy / Privilege Escalation
An agent with file-write, shell, or network access can be coerced into using its own permissions on behalf of an attacker — exfiltrating data, modifying files, or calling paid APIs that the user (not the attacker) is authenticated to.

## Data Exfiltration via Side Channels
Injected prompts coax the agent into encoding secrets (API keys, session tokens, source code) into outbound URLs, markdown image tags, or image-fetch requests that beacon to attacker-controlled servers.

## Memory and Context Poisoning
Persistent memory, RAG indexes, and conversation history allow a single successful injection to be stored and re-triggered in later sessions, turning a one-shot attack into a long-lived backdoor.

## Multi-Agent Chain Amplification
When one agent calls another or pipes output into downstream tools, an injection in one hop propagates and may be re-interpreted with elevated trust by the next component in the chain.

## Supply-Chain Injection
Tool schemas, MCP server descriptions, third-party system prompts, and shared agent skills can themselves carry injections that activate the moment they are loaded — even before any user input is processed.

## Guardrail Bypass
Fine-tuned refusals can be overridden by base64-encoded payloads, language-switching, role-play scenarios, and adversarial suffixes — particularly when guardrails rely on surface-level pattern matching rather than semantic intent.

## Detection and Attribution Gaps
Successful injections often look like normal model behavior, leave little forensic trace beyond logs, and cannot be deterministically reproduced — making incident response and regression testing significantly harder than traditional injection bugs.
