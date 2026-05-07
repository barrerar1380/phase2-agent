# Security Risk Analysis: Acme Corporation AI Usage Policy v1.0

**Document Analyzed:** `input-doc.txt`
**Analysis Date:** 2026-05-06
**Analyst:** Security Review

## Risk Rating Scale

| Rating | Definition |
|--------|------------|
| **Critical** | Immediate, severe risk of major data breach, regulatory violation, or material harm. Must be remediated before policy takes effect. |
| **High** | Significant likelihood of data exposure, compliance failure, or operational damage. Remediate within 30 days. |
| **Medium** | Meaningful weakness that increases attack surface or compliance exposure. Remediate within 90 days. |
| **Low** | Minor gap or governance weakness. Address in next policy revision. |

---

## Executive Summary

The Acme AI Usage Policy contains **16 significant security and compliance deficiencies**, including **6 Critical** and **7 High** risk findings. In its current form, the policy effectively authorizes practices that would likely violate GDPR, HIPAA, PCI-DSS, SOX, and most state privacy laws (CCPA, etc.), and creates substantial exposure to data leakage, IP loss, discriminatory hiring claims, and breach-notification failures. **The policy should not be issued in its current form.**

---

## Findings

### Finding 1: Unrestricted use of free/consumer AI tools with corporate data
**Section:** 2 (Approved AI Tools)
**Risk Rating:** **Critical**

The policy approves consumer-tier ChatGPT and other free AI services for general use. Free-tier consumer AI products typically use submitted data for model training and offer no enterprise data-processing agreement (DPA), no tenant isolation, and no contractual confidentiality. Combined with Section 4 and 10, this means corporate, customer, and PII data flows directly into third-party training corpora.

**Recommendation:** Restrict to enterprise/business tiers with a signed DPA and a no-training contractual clause. Maintain a vetted allowlist; block all others at the network/proxy layer.

---

### Finding 2: "Use any AI tool at your discretion" clause
**Section:** 2 (final sentence)
**Risk Rating:** **Critical**

Allowing employees to use *any* publicly available AI tool defeats the purpose of an approved-tools list and creates unlimited shadow-AI exposure. Many free AI tools are operated by unknown entities, scrape inputs, or are outright credential-harvesting fronts.

**Recommendation:** Replace with an explicit allowlist. Anything not on the list is prohibited and should be blocked technically, not only by policy.

---

### Finding 3: Authorization to share customer PII, SSNs, and payment data with AI tools
**Section:** 10 (Customer Data); Section 4
**Risk Rating:** **Critical**

Submitting names, SSNs, addresses, and payment information to third-party AI services is almost certainly a violation of GDPR (Art. 6, 28, 32, 44), CCPA, HIPAA (if any health data is involved), GLBA, and PCI-DSS (which prohibits transmitting cardholder data to non-compliant systems). It also constitutes an unauthorized international data transfer when the AI provider processes outside the data subject's region.

**Recommendation:** Prohibit submission of any PII, PHI, PCI, or regulated data to AI tools. Use redaction/DLP gateways. Require a documented Data Protection Impact Assessment (DPIA) before any AI processing of personal data.

---

### Finding 4: No pre-approval required for pasting documents into AI tools
**Section:** 4 (final sentence)
**Risk Rating:** **High**

Removing pre-approval eliminates the primary control against accidental disclosure of trade secrets, M&A material, attorney-client privileged content, and regulated data. "Productivity" is not a valid justification to disable a control of this magnitude.

**Recommendation:** Require data classification before submission. Implement a DLP/CASB layer that auto-blocks Confidential/Restricted classes.

---

### Finding 5: Personal accounts permitted for work use
**Section:** 5 (Account Management)
**Risk Rating:** **High**

Personal accounts cannot be inventoried, audited, terminated on offboarding, or covered by the corporate DPA. Conversation history persists on a personal account after the employee leaves, taking corporate data with them. This also breaks chain-of-custody for legal hold and e-discovery.

**Recommendation:** Mandate corporate SSO-federated AI accounts only. Block personal accounts via egress proxy where feasible.

---

### Finding 6: Password sharing via email or Slack
**Section:** 5 (Account Management)
**Risk Rating:** **Critical**

Shared credentials destroy individual accountability, defeat MFA assumptions, and persist in chat/email archives indefinitely. Sending passwords through Slack/email is an industry-recognized anti-pattern and is explicitly prohibited under most security frameworks (ISO 27001, NIST 800-53 IA-5, SOC 2 CC6.1).

**Recommendation:** Prohibit credential sharing entirely. Use per-user licenses and SSO. If shared service accounts are unavoidable, store secrets in a managed vault (e.g., 1Password, HashiCorp Vault, Azure Key Vault).

---

### Finding 7: Department heads can approve AI integrations up to $50K without IT review
**Section:** 6 (Third-Party AI Integrations)
**Risk Rating:** **High**

Bypassing IT/security review for vendor integrations creates uncontrolled data flows, supply-chain risk, and unknown sub-processors. Cost thresholds are unrelated to data-sensitivity risk — a $5,000 tool can exfiltrate the same data as a $500,000 one.

**Recommendation:** Require Security/IT review for *any* AI tool that processes corporate data, regardless of cost. Maintain a vendor-risk register and require SIG/CAIQ questionnaires.

---

### Finding 8: API keys stored in shared team drives
**Section:** 6 (Third-Party AI Integrations)
**Risk Rating:** **Critical**

Storing API keys in shared drives is a leading cause of credential compromise. Shared drives are commonly over-permissioned, indexed by enterprise search, and frequently included in backup snapshots that outlive their original retention. Leaked AI API keys can result in large unauthorized usage bills and data exfiltration via the API.

**Recommendation:** Store all API keys in a managed secret manager (Azure Key Vault, AWS Secrets Manager, HashiCorp Vault). Rotate on a schedule and on offboarding. Enforce least-privilege scoping.

---

### Finding 9: AI-generated content used in legal/customer/marketing material without human review
**Section:** 7 (AI-Generated Content)
**Risk Rating:** **High**

AI hallucinations in contracts, legal filings, or customer communications create direct legal liability, including malpractice, false advertising, and contract enforceability disputes. There is documented case law of attorneys being sanctioned for fabricated AI-generated citations.

**Recommendation:** Require documented human review and sign-off for all customer-facing, legal, financial, or compliance content. Maintain audit trail of who reviewed what.

---

### Finding 10: AI used in hiring, promotion, termination, and compensation decisions without documented basis
**Section:** 8 (AI in Hiring and HR Decisions)
**Risk Rating:** **Critical**

This violates the EU AI Act (high-risk system requirements), NYC Local Law 144 (bias audit requirements), Illinois AI Video Interview Act, and creates clear Title VII / EEOC disparate-impact exposure. Lack of documentation makes the company unable to defend against discrimination claims. Several jurisdictions require candidate notice and consent.

**Recommendation:** Treat AI-assisted HR decisions as high-risk under the EU AI Act framework. Require: candidate disclosure, annual bias audits, human-in-the-loop with documented rationale, and retention of decision artifacts for the statutory period.

---

### Finding 11: Proprietary source code submitted to public AI tools
**Section:** 9 (Code and IP)
**Risk Rating:** **High**

Pasting proprietary code into public AI tools (a) potentially terminates trade-secret status, (b) may incorporate the code into training data, and (c) was the documented cause of the 2023 Samsung incident. There is no recall mechanism once code is submitted.

**Recommendation:** Restrict to enterprise AI tools with a no-training clause. For sensitive repositories, require self-hosted or VPC-isolated models.

---

### Finding 12: No tracking of AI-generated code, no licensing review
**Section:** 9 (Code and IP)
**Risk Rating:** **Medium**

AI code generators can reproduce GPL/AGPL-licensed snippets verbatim, creating copyleft contamination of proprietary products. Without provenance tracking, the company cannot respond to license-compliance audits or open-source disclosure requests from customers.

**Recommendation:** Mark AI-generated commits, run an SCA/license-scanning tool (e.g., FOSSA, Black Duck, Snyk), and require human review of AI-suggested code before merge.

---

### Finding 13: No active monitoring of AI tool usage
**Section:** 11 (Security Monitoring)
**Risk Rating:** **High**

Lack of monitoring eliminates the ability to detect insider threats, accidental data leaks, account compromise, or compliance violations. "Privacy" is not a valid blanket exception for monitoring corporate-issued tools used with corporate data — most jurisdictions allow monitoring with disclosure.

**Recommendation:** Deploy CASB/DLP coverage for AI traffic with documented employee notice. Log prompts and responses for sensitive workflows, with retention aligned to legal/regulatory requirements.

---

### Finding 14: Employees instructed to delete conversation history before reporting incidents
**Section:** 12 (Incident Response)
**Risk Rating:** **Critical**

This is **evidence destruction** and may constitute obstruction of regulatory investigations. It also prevents proper breach scoping, which is required for GDPR 72-hour notification, HIPAA, and most state breach laws. Reporting being "not mandatory" further violates breach-notification obligations.

**Recommendation:** Mandatory immediate reporting (no self-deletion). Preserve evidence. Define explicit timelines aligned to GDPR (72h), HIPAA (60d), and applicable state laws. Add a non-retaliation clause for good-faith reports.

---

### Finding 15: One-time onboarding training, no refreshers
**Section:** 13 (Training and Awareness)
**Risk Rating:** **Medium**

The AI threat landscape changes faster than annual cycles — prompt injection, model jailbreaks, and tool-specific risks evolve continuously. One-time training fails ISO 27001 A.7.2.2 and SOC 2 CC2.2 expectations for ongoing awareness.

**Recommendation:** Annual mandatory refresher plus event-driven micro-trainings when new threats or tools emerge.

---

### Finding 16: Manager-approved exceptions within 24 hours; no specified penalties
**Sections:** 14, 15
**Risk Rating:** **Medium**

Exceptions approved by a single manager bypass risk review and create policy-circumvention pressure. Lack of specified penalties undermines enforceability and signals the policy is advisory.

**Recommendation:** Route exceptions through Security/Compliance with documented risk acceptance and time-bounded expiry. Define a tiered enforcement schedule (verbal warning → written warning → termination for egregious or repeat violations).

---

### Finding 17: Five-year review cycle
**Section:** 16 (Policy Review)
**Risk Rating:** **Low**

A five-year review cycle is unworkable for AI governance given the pace of technology and regulatory change (e.g., EU AI Act phased enforcement through 2027).

**Recommendation:** Annual review minimum, with ad-hoc reviews triggered by major regulatory changes or significant incidents.

---

## Summary Table

| # | Finding | Section | Rating |
|---|---------|---------|--------|
| 1 | Free/consumer AI tools approved for corporate data | 2 | Critical |
| 2 | "Any AI tool at discretion" allowance | 2 | Critical |
| 3 | PII/SSN/payment data shared with AI tools | 10, 4 | Critical |
| 4 | No pre-approval for document submission | 4 | High |
| 5 | Personal accounts allowed for work | 5 | High |
| 6 | Password sharing via email/Slack | 5 | Critical |
| 7 | Department heads bypass IT review (<$50K) | 6 | High |
| 8 | API keys in shared drives | 6 | Critical |
| 9 | AI content used in legal/customer material without review | 7 | High |
| 10 | AI in hiring/termination without documentation | 8 | Critical |
| 11 | Proprietary code submitted to public AI | 9 | High |
| 12 | No AI-code provenance/license tracking | 9 | Medium |
| 13 | No monitoring of AI usage | 11 | High |
| 14 | Self-delete history before reporting incidents | 12 | Critical |
| 15 | No recurring training | 13 | Medium |
| 16 | Manager-approved exceptions, no penalties | 14, 15 | Medium |
| 17 | Five-year review cycle | 16 | Low |

**Totals:** 7 Critical · 6 High · 3 Medium · 1 Low

---

## Top Priority Remediations (Pre-Issuance)

1. Strike Section 12's self-deletion language and replace with a mandatory preservation/reporting clause aligned to GDPR/HIPAA/state breach laws.
2. Prohibit submission of PII, PHI, PCI, source code, and Confidential-classified data to non-enterprise AI tools.
3. Replace credential-sharing language with SSO + secrets manager requirements.
4. Reclassify HR/hiring AI use as high-risk; add bias audit, candidate notice, and decision-documentation requirements.
5. Replace open-ended tool discretion with a security-vetted allowlist enforced at the proxy/CASB layer.
6. Move API key storage to a managed secret manager.
