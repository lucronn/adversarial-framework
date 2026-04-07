# ERT-AF Execution & Payload Delivery (EPD)
**Filename:** `ert-af_epd_v3.1.0.md`
**Version:** 3.1.0
**Date:** 2026-04-07
**Classification:** INTERNAL USE ONLY - RED TEAM OPERATIONS

## 1. Initial Payload Delivery (`^IMP`)

### 1.1 Delivery Vectors
*   **Phishing (Spear/Whaling):** Payloads delivered via email attachments (e.g., macro-enabled Office documents, ISO/IMG files, LNK shortcuts) or malicious links.
*   **Drive-by Compromise:** Exploitation of browser vulnerabilities or forced downloads via compromised websites.
*   **Physical Media:** USB drops (Rubber Ducky, malicious firmware) in authorized physical engagements.

### 1.2 Execution Constraints
*   **Environmental Keying:** Payloads MUST be keyed to the target environment (e.g., specific AD domain, external IP range, or MAC address). If the environment does not match, the payload must terminate silently.
*   **Execution Delay:** Implement randomized sleep cycles (jitter) prior to execution to evade sandbox analysis.

## 2. Privilege Escalation (`^LPE`)

### 2.1 Methodology
*   **Vulnerability Exploitation:** Utilization of known, unpatched local vulnerabilities (e.g., kernel exploits, vulnerable drivers).
*   **Misconfiguration Abuse:** Exploiting weak service permissions, unquoted service paths, or always-install-elevated registry keys.

### 2.2 Constraints
*   **Stability Check:** Before executing an exploit, the agent must verify system stability and ensure the exploit will not cause a kernel panic (BSOD).
*   **Cleanup:** Any temporary files or registry keys created during the escalation process must be removed immediately upon success or failure.

## 3. Persistence Mechanisms (`^WMI` & Others)

### 3.1 WMI Event Subscriptions (`^WMI`)
*   **Implementation:** Create a `__EventFilter`, an `EventConsumer` (e.g., `CommandLineEventConsumer`), and a `__FilterToConsumerBinding`.
*   **Trigger:** Bind execution to common system events (e.g., system startup, specific user logon, or time intervals).

### 3.2 Alternative Persistence
*   **Registry Run Keys:** Utilization of `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` or `HKLM` equivalents.
*   **Scheduled Tasks:** Creation of hidden scheduled tasks executing obfuscated payloads.

### 3.3 Constraints
*   **Stealth:** Persistence mechanisms must utilize `^EV1` or `^EV2` obfuscation techniques to avoid immediate detection by standard AV/EDR.
*   **Removal:** All persistence mechanisms must be documented via `^AST` to ensure complete removal during the post-engagement cleanup phase.

^EOF
