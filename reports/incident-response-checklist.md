# Incident Response Checklist: AI Agent Compromise

A 10-step checklist for security teams responding to a confirmed or suspected compromise of an AI agent — for example, prompt injection that produced unauthorized tool calls, exfiltrated data, or poisoned persistent state. Steps are ordered roughly by urgency; in practice the early containment steps run in parallel.

## 1. Halt the Agent and Cut Tool Access
Stop any running sessions of the affected agent. Revoke the credentials and API keys it was using, disable its tools at the orchestration layer, and block outbound network egress from the host or sandbox. Stopping further damage takes priority over preserving live state.

## 2. Preserve Forensic Evidence
Before changing anything else, snapshot full prompt/response/tool-call traces, conversation history, persistent memory stores, RAG indexes, and any uploaded files. Capture timestamps, correlation IDs, and the exact model/version/system-prompt configuration. Treat logs as tamper-evident — copy them to a write-once location.

## 3. Determine Scope of Compromise
Enumerate which tools the agent had, which data sources it could read, which sessions and users were active, and which downstream systems consumed its output. Scope drives every later step — over-scoping wastes effort, under-scoping leaves backdoors.

## 4. Rotate All Reachable Credentials
Rotate every API key, OAuth token, session credential, signing key, and service account the agent could read or had loaded into context. Assume anything in the agent's prompt, tool output, or memory has leaked — even if no exfiltration is yet visible.

## 5. Audit and Reverse Tool Actions
Walk every tool call made during the suspected window. For reversible actions (file writes, repo commits, branch pushes, message sends, payments, ticket changes, infra calls) revert or compensate. For irreversible actions, notify recipients and downstream owners.

## 6. Identify the Injection Vector
Determine how the compromise entered: direct user input, indirect injection in retrieved content (web page, email, PDF, repo file, tool output), poisoned memory or RAG entry, or supply-chain (a malicious tool schema, MCP server, or shared skill). The vector decides which mitigations need to harden.

## 7. Sanitize Persistent State
Purge poisoned entries from agent memory, RAG indexes, fine-tuning datasets, conversation summaries, and any caches. Rebuild from a known-good snapshot where possible. Validate that purged content is not silently re-imported by a sync job.

## 8. Notify Stakeholders
Inform affected users, internal security/legal/compliance, and any downstream system owners whose data or workflows touched the agent. Apply the organization's breach-notification thresholds — regulated data (PII, PHI, payment, regulated source code) often triggers external timelines.

## 9. Patch the Root Cause
Add detection rules for the specific vector (e.g., outbound URL patterns, image-tag rendering, suspicious tool-call sequences). Tighten the relevant mitigation: stricter least-privilege scopes, allowlisted egress, human-in-the-loop on the abused tool, memory write gating. Deploy and verify.

## 10. Post-Incident Review and Regression Tests
Write a timeline, root-cause, and lessons-learned document. Add the exact injection payload (or a sanitized analog) to the adversarial-testing CI corpus so this pattern fails any future build. Update the threat catalog and mitigations if the incident exposed a category gap.
