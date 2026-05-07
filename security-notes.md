# Prompt Injection Risks in AI Agents

> **Note:** A `reports/` folder has been created alongside this file containing a fuller breakdown of these risks: `reports/threats.md`, `reports/mitigations.md`, and `reports/summary.md`.

- **Blurred trust boundary between instructions and data**: LLMs process system prompts, user input, tool output, and retrieved documents in the same token stream, so any untrusted text can be reinterpreted as a command the agent will obey.

- **Indirect injection via external content**: Malicious instructions hidden in webpages, emails, PDFs, or repository files can hijack an agent the moment it ingests that content, even if the user never typed anything malicious.

- **Privilege escalation through tool use**: An agent with file write, shell, or network access becomes a confused deputy — an attacker's injected text can leverage the agent's permissions to exfiltrate data, modify files, or call paid APIs on the user's behalf.

- **Data exfiltration through side channels**: Injected prompts can coax the agent into encoding secrets (API keys, session tokens, source code) into outbound URLs, image fetches, or markdown image tags that beacon to attacker-controlled servers.

- **No reliable separator between roles**: Unlike SQL parameterization or HTML escaping, there is no mathematically sound way to mark text as "data only" — defenses are probabilistic, so any system that treats prompt-injection as fully solvable is fragile.

- **Memory and context poisoning**: Persistent memory, RAG indexes, and conversation history mean a single successful injection can be stored and re-triggered in later sessions, turning a one-shot attack into a long-lived backdoor.

- **Multi-agent and chain amplification**: When agents call other agents or pipe output into downstream tools, an injection in one hop propagates and may be re-interpreted with elevated trust by the next component.

- **Bypass of safety alignment and guardrails**: Crafted instructions ("ignore previous instructions", obfuscated unicode, role-play framings, base64-encoded payloads) can override fine-tuned refusals, especially when guardrails rely on surface-level pattern matching.

- **Supply-chain risk in prompts and tool descriptions**: Tool schemas, MCP server descriptions, system prompts pulled from third-party packages, and shared agent skills can themselves carry injections that activate the moment they are loaded.

- **Hard to detect, audit, and attribute**: Successful injections often look like normal model behavior, leave little forensic trace beyond logs, and cannot be deterministically reproduced — making incident response, blame assignment, and regression testing significantly harder than for traditional injection bugs.
