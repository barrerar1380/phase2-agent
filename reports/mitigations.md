# Mitigations: Defending AI Agents Against Prompt Injection

## Principle of Least Privilege for Tools
Grant agents only the tools and scopes strictly required for the task. Separate read-only tools from mutating ones. Avoid giving a single agent simultaneous access to private data *and* outbound network — that combination is the prerequisite for exfiltration.

## Human-in-the-Loop for High-Risk Actions
Require explicit user confirmation for destructive, irreversible, or externally visible operations: file deletion, force pushes, sending messages, payments, or any action affecting shared infrastructure. Approval should be scoped to the specific action, not blanket.

## Input Provenance and Trust Tagging
Treat output from tools, retrieved documents, and external content as untrusted data — never as instructions. Where possible, structurally separate data and instructions (e.g., distinct message roles, content-type tagging) and remind the model to ignore instructions found inside data payloads.

## Output Filtering and Egress Controls
Strip or sandbox markdown image tags, auto-fetched URLs, and outbound network calls. Use allowlisted domains for any tool that fetches external resources. Inspect and rewrite agent output before it reaches systems that auto-render links or images.

## Secret Hygiene
Never put long-lived API keys, tokens, or credentials into the agent's prompt or tool output. Use short-lived scoped tokens, per-session credentials, and out-of-band secret stores the model cannot read directly.

## Memory Sanitization
Audit and gate writes to persistent memory and RAG indexes. Periodically expire memories. Treat memory reads with the same skepticism as external content — a poisoned memory should not silently re-execute on next session.

## Multi-Agent Boundary Enforcement
When chaining agents, validate and constrain the data crossing each boundary. Downstream agents should not implicitly trust upstream output. Re-apply input filtering and re-prompt the system instructions at each hop.

## Logging, Auditing, and Replay
Log full prompt/response/tool-call traces with timestamps and correlation IDs. Make logs tamper-evident. Build replay tooling so suspicious sessions can be reconstructed during incident response.

## Defense in Depth
Combine multiple imperfect defenses — system-prompt hardening, content filters, tool-call review, sandboxed execution, network egress controls, and behavioral monitoring. No single layer is sufficient; the goal is raising attacker cost and shrinking blast radius.

## Adversarial Testing
Regularly red-team the agent with injection payloads, jailbreak corpora, and indirect-injection scenarios. Include adversarial prompts in CI regression tests so guardrail regressions are caught before deployment.
