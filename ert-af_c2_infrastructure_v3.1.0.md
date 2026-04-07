# ERT-AF C2 Infrastructure & Communications (C2C)
**Filename:** `ert-af_c2_infrastructure_v3.1.0.md`
**Version:** 3.1.0
**Date:** 2026-04-07
**Classification:** INTERNAL USE ONLY - RED TEAM OPERATIONS

## 1. Infrastructure Architecture

### 1.1 Tiered Design
*   **Tier 1: Long-Haul/Low-and-Slow:** Highly resilient, low-bandwidth C2 used strictly for maintaining persistence and issuing critical commands. Traffic must blend with normal background noise (e.g., DNS, infrequent HTTPS).
*   **Tier 2: Operational/Interactive:** Used for active engagement, lateral movement, and data exfiltration. Higher bandwidth, shorter lifespan.
*   **Tier 3: Ephemeral/Staging:** Short-lived infrastructure used solely for initial payload delivery (`^IMP`) and staging. Burned immediately after use.

### 1.2 Redirectors
*   **Mandate:** Direct connections to team servers are strictly prohibited. All traffic must pass through at least one layer of redirectors (e.g., Nginx, Apache, HAProxy).
*   **Filtering:** Redirectors must implement strict filtering based on User-Agent, IP reputation, and expected request URIs to drop automated scanners and blue team probing.

## 2. Communication Protocols

### 2.1 Encryption
*   **Standard:** All C2 traffic must be encrypted in transit using TLS 1.3 (or highest supported).
*   **Payload Encryption:** Data within the transport layer must be additionally encrypted (e.g., AES-256) with keys negotiated per session or pre-shared.

### 2.2 Malleable Profiles
*   **Requirement:** C2 frameworks must utilize malleable profiles to alter network indicators (e.g., HTTP headers, URI structures, response bodies) to mimic legitimate applications or services specific to the target environment.

## 3. Infrastructure Security

### 3.1 Access Control
*   **Authentication:** Multi-factor authentication (MFA) is required for all operator access to team servers.
*   **Network Segmentation:** Team servers must be isolated from the internet, accessible only via secure VPN or SSH tunnels from authorized operator IPs.

^EOF
