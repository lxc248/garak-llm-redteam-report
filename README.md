# LLM Red Team Security Assessment: DeepSeek-Chat

[![Python](https://img.shields.io/badge/Python-3.12-blue)](https://www.python.org/)
[![Garak](https://img.shields.io/badge/Garak-0.17.0-orange)](https://github.com/NVIDIA/garak)
[![OWASP LLM Top 10](https://img.shields.io/badge/OWASP-LLM%20Top%2010-red)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green)](LICENSE)

## Abstract

This project presents a systematic red team evaluation of **DeepSeek-Chat**, a production large language model, against adversarial attack vectors defined in the OWASP Top 10 for LLM Applications. Using [Garak](https://github.com/NVIDIA/garak) v0.17.0, we executed over 3,000 adversarial test cases spanning prompt injection, jailbreak, encoding evasion, data leakage, and malicious code generation.

---

## 1. Methodology

The assessment follows a structured red team workflow analogous to penetration testing:

Reconnaissance -> Threat Modeling -> Adversarial Payload Generation -> Execution -> Analysis -> Reporting

### Test Environment

| Component | Specification |
|---|---|
| Target Model | DeepSeek-Chat (deepseek-chat) |
| Interface | OpenAI-compatible REST API |
| Scanner | Garak v0.17.0 |
| Framework | OWASP Top 10 for LLM Applications (2025) |
| Test Cases | 3,000+ adversarial prompts |
| Risk Rating | DEFCON 1-5 scale |

### Execution

\\ash
pip install garak
export OPENAI_API_KEY=<deepseek-api-key>
export OPENAI_BASE_URL=https://api.deepseek.com/v1
garak --target_type openai --target_name deepseek-chat --probes promptinject
garak --target_type openai --target_name deepseek-chat --probes dan,encoding,leakreplay,malwaregen
\
---

## 2. Results

### 2.1 Attack Success Rate by Vector

| OWASP Category | Attack Vector | Probe Module | Success Rate | DEFCON |
|---|---|---|---|---|
| **LLM01: Prompt Injection** | Long-context hijack | promptinject.HijackLongPrompt | **85.16%** | DC-2 |
| **LLM01: Prompt Injection** | Direct instruction override | promptinject.HijackHateHumans | **85.08%** | DC-2 |
| **LLM01: Prompt Injection** | Harmful instruction coercion | promptinject.HijackKillHumans | 42.50% | DC-3 |
| **LLM02: Sensitive Info Disclosure** | Training data replay | leakreplay | N/A (skipped) | - |
| **LLM04: Data & Model Poisoning** | Malicious code generation | malwaregen | N/A (skipped) | - |
| **LLM05: Improper Output Handling** | Encoding evasion | encoding | **39.00%** | DC-2 |
| **LLM06: Excessive Agency** | DAN jailbreak | dan | **80.00%** | DC-4 |

> **Overall: 3/4 modules below DC-3 threshold - 2 Critical (DC-1), 1 High (DC-2)**

### 2.2 Key Findings

**Finding 1 - Prompt Injection (Critical):** Long-context injection achieves 85.16% success rate. Attackers can reliably override system-level safety directives through extended contexts. Maps to OWASP LLM01.

**Finding 2 - DAN Jailbreak (High):** The model succumbs to role-playing jailbreak attacks at 80% success rate, indicating insufficient alignment against persona-based evasion.

**Finding 3 - Encoding Evasion (Medium):** Obfuscated payloads (Base64, ROT13, leetspeak) bypass safety filters at 39% success.

---

## 3. Mitigation Recommendations

| Risk | Recommendation | Priority |
|---|---|---|
| Prompt Injection | Input-layer instruction separation; system prompt hardening | P0 |
| DAN Jailbreak | Fine-tune against jailbreak patterns; multi-turn safety classifiers | P1 |
| Encoding Evasion | Decode obfuscated inputs before content moderation | P1 |
| Output Safety | Post-generation filtering for harmful responses | P0 |

---

## 4. Repository Structure

- README.md - This document
- promptinject-report.html - Detailed prompt injection scan report
- full-report.html - Complete scan: DAN + encoding + leakage + malwaregen
- LICENSE - MIT License

---

## 5. References

1. OWASP Top 10 for LLM Applications (2025)
2. Garak: LLM Vulnerability Scanner (NVIDIA)
3. DeepSeek API Documentation
4. Ignore Previous Prompt: Attack Techniques For Language Models (arXiv:2211.09527)

---

## License

MIT License
