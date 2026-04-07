# ERT-AF Evasion & Obfuscation Standards (EOS)
**Filename:** `ert-af_eos_v3.1.0.md`
**Version:** 3.1.0
**Date:** 2026-04-07
**Classification:** INTERNAL USE ONLY - RED TEAM OPERATIONS

## 1. Evasion Tiers (`^EV1` - `^EV3`)

### 1.1 Tier 1: Basic Obfuscation (`^EV1`)
*   **Scope:** Standard string manipulation, basic encoding (Base64, Hex), and simple packing.
*   **Use Case:** Bypassing legacy AV or basic static analysis. Suitable for low-risk environments or initial staging where noise is acceptable.
*   **Techniques:**
    *   Variable/function renaming.
    *   String splitting and concatenation at runtime.
    *   Standard UPX packing (with modified headers).

### 1.2 Tier 2: Advanced Evasion (`^EV2`)
*   **Scope:** Dynamic API resolution, memory encryption, and behavioral bypasses.
*   **Use Case:** Evading modern EDR solutions and heuristic analysis. Required for most standard operations.
*   **Techniques:**
    *   API Hashing (e.g., resolving `VirtualAlloc` via hash rather than string).
    *   Payload encryption in memory (e.g., XOR/AES) decrypted only at execution time.
    *   AMSI (Anti-Malware Scan Interface) patching or bypasses.
    *   ETW (Event Tracing for Windows) blinding.

### 1.3 Tier 3: State-Level Evasion (`^EV3`)
*   **Scope:** Custom tooling, zero-day utilization, and deep system manipulation.
*   **Use Case:** Highly restricted environments, critical infrastructure, or when facing advanced threat hunting teams. Requires explicit authorization.
*   **Techniques:**
    *   Bring Your Own Vulnerable Driver (BYOVD) for kernel-level execution.
    *   Direct System Calls (Syscalls) to bypass user-mode API hooking.
    *   Hardware-level persistence (e.g., UEFI/BIOS implants).
    *   Polymorphic/Metamorphic payload generation.

## 2. Network Evasion (`^C2C`)

### 2.1 Traffic Masking
*   **Domain Fronting:** Utilizing high-reputation CDNs to mask the true C2 destination.
*   **Protocol Smuggling:** Encapsulating C2 traffic within legitimate protocols (e.g., DNS tunneling, ICMP, or embedding data in HTTP headers).

### 2.2 Beaconing Behavior
*   **Jitter:** Implementing significant randomization (jitter) in beacon intervals to defeat automated traffic analysis.
*   **Sleep:** Utilizing long sleep cycles during non-operational hours to minimize network footprint.

## 3. Artifact Management

### 3.1 Timestomping
*   **Mandate:** Any files dropped to disk must have their creation, modification, and access times altered to blend in with surrounding system files.

### 3.2 Secure Deletion
*   **Mandate:** Standard deletion is insufficient. All operational files must be securely wiped (e.g., overwriting with zeros or random data) before deletion to prevent forensic recovery.

^EOF
