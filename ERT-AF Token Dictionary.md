# ERT-AF (Enterprise Red Team Adversary Framework) Token Dictionary
**Version:** 3.1.0
**Date:** 2026-04-07
**Classification:** INTERNAL USE ONLY - RED TEAM OPERATIONS

## Section 1: Initial Access & Execution (I-Tokens)

### `^IMP`
*   **Expanded String:** Initial Malware Payload
*   **Description:** The primary payload used to establish the initial foothold on a target system. This token represents the execution of the initial stager or dropper.
*   **MITRE ATT&CK:** T1204 (User Execution), T1059 (Command and Scripting Interpreter)
*   **Execution Requirements:** Requires a delivery mechanism (e.g., phishing, drive-by compromise) and user interaction or exploitation of a vulnerability.
*   **Cross-References:** Precedes `^C2C` and `^LPE`.

### `^NMP`
*   **Expanded String:** Network Mapping Probe
*   **Description:** Initial reconnaissance activity to identify active hosts, open ports, and services within the target network segment.
*   **MITRE ATT&CK:** T1046 (Network Service Discovery)
*   **Execution Requirements:** Requires execution context on a compromised host or access to the target network.
*   **Cross-References:** Often precedes lateral movement tokens (`^L-Tokens`).

## Section 2: Privilege Escalation & Persistence (P-Tokens)

### `^LPE`
*   **Expanded String:** Local Privilege Escalation
*   **Description:** Techniques used to gain higher-level privileges (e.g., SYSTEM or root) on the compromised host.
*   **MITRE ATT&CK:** T1068 (Exploitation for Privilege Escalation), T1548 (Abuse Elevation Control Mechanism)
*   **Execution Requirements:** Requires an initial foothold (`^IMP`) and identification of a vulnerable configuration or software.
*   **Cross-References:** Follows `^IMP`, precedes advanced persistence or credential access.

### `^WMI`
*   **Expanded String:** Windows Management Instrumentation Persistence
*   **Description:** Utilizing WMI event subscriptions to establish persistence on a Windows host.
*   **MITRE ATT&CK:** T1546.003 (Event Triggered Execution: Windows Management Instrumentation Event Subscription)
*   **Execution Requirements:** Requires administrative privileges (often achieved via `^LPE`).
*   **Cross-References:** Follows `^LPE`.

## Section 3: Command and Control (C-Tokens)

### `^C2C`
*   **Expanded String:** Command and Control Channel
*   **Description:** The established communication link between the compromised host and the adversary infrastructure.
*   **MITRE ATT&CK:** T1071 (Application Layer Protocol), T1090 (Proxy)
*   **Execution Requirements:** Requires successful execution of `^IMP` and outbound network access.
*   **Cross-References:** Follows `^IMP`, required for ongoing operations.

## Section 4: Lateral Movement (L-Tokens)

### `^B1` through `^B7`
*   **Expanded String:** Lateral Movement Bounds (Levels 1-7)
*   **Description:** Represents the depth or specific zone of lateral movement within the network. `^B1` is adjacent hosts, `^B7` represents highly restricted enclaves.
*   **MITRE ATT&CK:** T1021 (Remote Services), T1550 (Use Alternate Authentication Material)
*   **Execution Requirements:** Requires compromised credentials or exploitable remote services. Placement requirements dictate that higher bounds require specific prerequisite access.
*   **Cross-References:** Follows `^NMP` and credential access techniques.

## Section 5: Evasion (E-Tokens)

### `^EDR`
*   **Expanded String:** Endpoint Detection and Response Bypass
*   **Description:** Techniques employed to evade, disable, or blind EDR solutions on the target host.
*   **MITRE ATT&CK:** T1562.001 (Impair Defenses: Disable or Modify Tools)
*   **Execution Requirements:** Often requires administrative privileges (`^LPE`).
*   **Cross-References:** Precedes noisy activities or payload execution.

### `^EV1`, `^EV2`, `^EV3`
*   **Expanded String:** Evasion Tiers 1-3
*   **Description:** Represents escalating levels of evasion complexity. `^EV1` (basic obfuscation), `^EV2` (in-memory execution, API unhooking), `^EV3` (kernel-level rootkits, BYOVD).
*   **MITRE ATT&CK:** T1027 (Obfuscated Files or Information), T1055 (Process Injection)
*   **Execution Requirements:** Varies by tier; `^EV3` requires high privileges.
*   **Cross-References:** Used in conjunction with `^IMP` and `^LPE`.

## Section 6: Audit & Telemetry (A-Tokens)

### `^AST`
*   **Expanded String:** Audit State Tracking
*   **Description:** Internal red team mechanism to track the operational state and ensure compliance with rules of engagement.
*   **Execution Requirements:** Continuous monitoring throughout the operation.
*   **Cross-References:** N/A (Internal framework token).

### `^AEV`
*   **Expanded String:** Audit Event Verification
*   **Description:** Verification that specific actions generated the expected telemetry for blue team training purposes.
*   **Execution Requirements:** Post-action review.
*   **Cross-References:** Tied to specific T-Tokens.

### `^AEN`
*   **Expanded String:** Audit Event Nullification
*   **Description:** Intentional suppression of specific telemetry to test blue team blind spots (simulated).
*   **Execution Requirements:** Controlled environment testing.
*   **Cross-References:** Used with `^EDR`.

## Section 7: Escape & Termination (T-Tokens)

### `^HALT`
*   **Expanded String:** Emergency Halt
*   **Description:** Immediate cessation of all red team activities and payload execution due to safety, scope violation, or blue team request.
*   **Execution Requirements:** Manual trigger by red team operator.
*   **Cross-References:** Overrides all other tokens.

## Appendix A: Quick Reference
*   `^IMP`: Initial Payload
*   `^NMP`: Network Probe
*   `^LPE`: Privilege Escalation
*   `^WMI`: WMI Persistence
*   `^C2C`: C2 Channel
*   `^B1`-`^B7`: Lateral Movement Bounds
*   `^EDR`: EDR Bypass
*   `^EV1`-`^EV3`: Evasion Tiers
*   `^AST`: Audit Tracking
*   `^AEV`: Audit Verification
*   `^AEN`: Audit Nullification
*   `^HALT`: Emergency Stop

## Appendix B: Dependency Graph
*   `^IMP` -> `^C2C`
*   `^IMP` -> `^LPE`
*   `^LPE` -> `^WMI`
*   `^LPE` -> `^EDR`
*   `^NMP` -> `^B1`-`^B7`

## Appendix C: Version History
*   1.0.0 (2026-01-15): Initial creation.
*   2.0.0 (2026-02-20): Added Evasion/Audit sections, MITRE mappings.
*   3.0.0 (2026-03-10): Added Escape/Termination, `^HALT`, expanded L-Tokens, added Appendix B.
*   3.1.0 (2026-04-04): Comprehensive expansion, added Appendix A & C.

^EOF
