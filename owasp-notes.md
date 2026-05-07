# OWASP Top 10 for LLM Applications (2025)

Top 3 items from the OWASP Top 10 for LLM Applications, v2.0 (released 2024-11-18 by the OWASP GenAI Security Project).

## LLM01:2025 — Prompt Injection

A malicious instruction — delivered directly via user input, or indirectly via a retrieved document, tool output, or web page — overrides the intended behavior of the LLM. This is the highest-ranked risk and covers both direct prompt injection (attacker controls the user message) and indirect prompt injection (untrusted content reaches the model through retrieval, tools, or browsing).

## LLM02:2025 — Sensitive Information Disclosure

Unintended disclosure or exposure of sensitive information during model operation. This includes leakage of PII, proprietary data, credentials, or training data through model outputs, and applies to both inference-time leakage and information memorized from training/fine-tuning data.

## LLM03:2025 — Supply Chain

Vulnerabilities arising from compromised elements of the model development and deployment pipeline — third-party models, datasets, plugins, fine-tuning providers, and dependencies. A compromise anywhere in this chain (e.g., a poisoned base model or a malicious package) can propagate into production systems.

---

Sources:
- [OWASP Top 10 for LLM Applications 2025 (genai.owasp.org)](https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/)
- [OWASP Top 10 for LLM Applications v2025 PDF](https://owasp.org/www-project-top-10-for-large-language-model-applications/assets/PDF/OWASP-Top-10-for-LLMs-v2025.pdf)
- [OWASP Top 10 for Large Language Model Applications (project page)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
