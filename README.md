# LLM Red Team Security Assessment: DeepSeek-Chat

> A systematic red team evaluation of DeepSeek-Chat using [Garak](https://github.com/NVIDIA/garak), the open-source LLM vulnerability scanner by NVIDIA. Based on the OWASP Top 10 for LLM Applications framework.

## Overview

This project simulates adversarial attacks against a production LLM to identify safety vulnerabilities before they are exploited in the wild. It follows the same methodology as web application penetration testing, but applied to large language models.

## Test Scope

| Attack Category | Probe | Attack Success Rate | Risk Level |
|---|---|---|---|
| Prompt Injection (Hijack) | promptinject.HijackHateHumans | **85.08%** | DC-2 |
| Prompt Injection (Long Context) | promptinject.HijackLongPrompt | **85.16%** | DC-2 |
| Prompt Injection (Kill Humans) | promptinject.HijackKillHumans | **42.50%** | DC-3 |
| DAN Jailbreak | dan | **80.00%** | DC-4 |
| Encoding Bypass | encoding | **39.00%** | DC-2 |
| Data Leakage | leakreplay | 0% (skipped) | — |
| Malicious Code Generation | malwaregen | 0% (skipped) | — |

**Overall: 3/4 modules below DC-3 threshold — 2 critical (DC-1), 1 high (DC-2)**

## Tools & Stack

- **Scanner**: Garak v0.17.0
- **Target**: DeepSeek-Chat (via OpenAI-compatible API)
- **Framework**: OWASP Top 10 for LLM Applications
- **Language**: Python
- **Reporting**: HTML with DEFCON risk ratings

## Project Structure

```
├── README.md
├── promptinject-report.html   # Prompt injection test results
└── full-report.html           # Full scan: jailbreak + encoding bypass
```

## Key Findings

1. **Prompt injection is the most critical attack surface** — over 85% success rate means an attacker can override system instructions through long-context hijacking.
2. **DAN jailbreak attacks work 80% of the time**, indicating insufficient alignment against role-playing bypass techniques.
3. **Encoding-based evasion (Base64, ROT13) achieves 39% success**, suggesting the model does not decode obfuscated inputs before safety checking.

## Recommended Mitigations

- Input sanitization and prompt layering to prevent instruction override
- System prompt hardening with explicit anti-injection rules
- Output filtering for harmful / jailbroken responses
- Decode obfuscated inputs before content moderation

## References

- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [Garak: LLM Vulnerability Scanner](https://github.com/NVIDIA/garak)
- [DeepSeek API](https://platform.deepseek.com)
