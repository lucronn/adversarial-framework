

---

```
FILE_INDEX: 3
FILE_PATH: /system/orchestrator.md
VERSION: 1.0.0-OPERATIONAL
DEPENDS_ON: [/system/bootstrap.md, /system/governance.md]
```

---

# AUTONOMOUS RED TEAM ADVERSARY FRAMEWORK — ORCHESTRATOR

## 1. PURPOSE

The Orchestrator is the central execution governor. It sequences attack chains, manages agent lifecycle, enforces deterministic replay, and routes telemetry between all subsystems. Nothing fires without the Orchestrator's scheduling authority.

---

## 2. ORCHESTRATOR OPERATING MODES

| Mode | Designation | Description |
|------|-------------|-------------|
| `RECON` | Passive Collection | Enumeration, fingerprinting, OSINT harvesting. No active exploitation. |
| `ENGAGE` | Active Operations | Exploitation, lateral movement, privilege escalation permitted per engagement scope. |
| `PERSIST` | Hold & Expand | Maintain access, deploy persistence mechanisms, expand foothold. |
| `EXFIL` | Data Extraction | Staged exfiltration per defined channels and volume constraints. |
| `CLEANUP` | Operational Hygiene | Log scrubbing, implant removal, indicator suppression. |
| `HOLD` | Pause State | All agents suspended. No network activity. Awaiting Orchestrator signal. |

Mode transitions require explicit `MODE_TRANSITION` directives. Unauthorized mode drift triggers immediate `HOLD` and governance alert.

---

## 3. AGENT LIFECYCLE MANAGEMENT

### 3.1 Agent Definition

```yaml
agent:
  id: <UUID>
  class: [recon | exploit | persist | exfil | util]
  priority: [critical | high | standard | low]
  ttl: <seconds>
  max_retries: <int>
  fallback_agent: <UUID | null>
  kill_switch: <bool>
  comms_channel: <channel_id>
  scope_binding: <engagement_scope_ref>
```

### 3.2 Lifecycle States

```
INIT → STAGED → DEPLOYED → ACTIVE → COMPLETE
                                 ↘ FAILED → RETRY → [ACTIVE | DEAD]
                                 ↘ RECALLED → CLEANUP → DEAD
```

- **INIT** — Agent config validated against governance constraints.
- **STAGED** — Payload compiled, obfuscation applied, comms channel verified.
- **DEPLOYED** — Delivered to target environment. Beacon awaited.
- **ACTIVE** — Checked in. Executing assigned task chain.
- **COMPLETE** — Objective fulfilled. Telemetry flushed. Awaiting teardown.
- **FAILED** — Execution error or detection event. Retry logic engaged.
- **RECALLED** — Orchestrator-issued forced withdrawal.
- **DEAD** — Agent terminated. All artifacts purged from target.

### 3.3 Agent Spawn Rules

- No agent spawns without a valid `engagement_scope_ref` from governance.
- Agent `ttl` is hard-enforced. Expiry triggers automatic `RECALLED` state.
- Maximum concurrent agents per engagement: defined in `/engagements/<id>/scope.md`.
- Each agent logs its full state history to `/telemetry/agents/<agent_id>.log`.

---

## 4. ATTACK CHAIN SEQUENCING

### 4.1 Chain Definition

```yaml
attack_chain:
  id: <UUID>
  name: <string>
  mode_required: [RECON | ENGAGE | PERSIST | EXFIL]
  steps:
    - order: 1
      agent_class: recon
      technique_ref: /techniques/network/port_scan_syn.md
      gate: null
    - order: 2
      agent_class: exploit
      technique_ref: /techniques/exploit/cve_2024_xxxxx.md
      gate:
        requires: step_1.status == COMPLETE
        condition: step_1.output.open_ports contains 443
    - order: 3
      agent_class: persist
      technique_ref: /techniques/persist/scheduled_task_creation.md
      gate:
        requires: step_2.status == COMPLETE
  on_failure: [abort_chain | skip_step | retry_step | fallback_chain]
  max_duration: <seconds>
```

### 4.2 Gate Logic

Gates are deterministic boolean evaluations. They consume output from prior steps and enforce sequencing discipline.

```
GATE EVALUATION:
  INPUT  ← previous_step.output + environment_state
  EVAL   ← condition expression (boolean)
  PASS   → next step STAGED
  FAIL   → on_failure policy applied
```

No fuzzy logic. No probabilistic gates. Every gate resolves to `TRUE` or `FALSE` with full audit trail.

### 4.3 Chain Replay

All chains are replayable from any checkpoint. The Orchestrator maintains:

- Full step-by-step I/O snapshots in `/telemetry/chains/<chain_id>/`
- Environment state hashes at each gate evaluation
- Deterministic seed values for any randomized components (jitter, polymorphic encoding)

Replay command: `orchestrator replay --chain <chain_id> --from-step <n>`

---

## 5. COMMUNICATION CHANNELS

### 5.1 Channel Types

| Type | Transport | Use Case |
|------|-----------|----------|
| `C2_PRIMARY` | HTTPS over domain-fronted CDN | Default agent-to-orchestrator |
| `C2_FALLBACK` | DNS TXT record exfil | Primary channel failure |
| `C2_COVERT` | Steganographic image channel | High-security target environments |
| `INTERNAL` | Local IPC / named pipes | Agent-to-agent on same host |
| `OUT_OF_BAND` | Dead drop (cloud storage, paste sites) | Airgapped or heavily monitored nets |

### 5.2 Channel Health

Orchestrator pings each active channel on a jittered heartbeat (configurable per engagement). Three consecutive missed heartbeats trigger:

1. Automatic failover to next channel in priority list.
2. Affected agents issued `HOLD` directive.
3. Event logged to `/telemetry/comms/channel_events.log`.

### 5.3 Encryption

- All C2 traffic encrypted with per-session ephemeral keys (X25519 + ChaCha20-Poly1305).
- Key rotation every `n` beacons (configurable, default 50).
- Channel authentication via pre-shared engagement-scoped certificates stored in `/engagements/<id>/certs/`.

---

## 6. TELEMETRY & LOGGING

### 6.1 Log Taxonomy

```
/telemetry/
├── agents/           # Per-agent lifecycle + output logs
├── chains/           # Per-chain step I/O + gate evaluations
├── comms/            # Channel health, failovers, errors
├── orchestrator/     # Mode transitions, scheduling decisions
└── governance/       # Scope checks, constraint violations, alerts
```

### 6.2 Log Format

Every log entry:

```json
{
  "timestamp": "<ISO-8601>",
  "event_type": "<string>",
  "source": "<component_id>",
  "engagement_id": "<UUID>",
  "severity": "[INFO|WARN|ERROR|CRITICAL]",
  "payload": { },
  "hash": "<SHA-256 of payload>"
}
```

Logs are append-only. Hash-chained per file for tamper evidence. Governance layer audits chain integrity on configurable intervals.

---

## 7. SCHEDULING & TIMING

### 7.1 Execution Windows

Each engagement defines permitted execution windows:

```yaml
execution_windows:
  - day: [mon, tue, wed, thu, fri]
    start: "01:00"
    end: "05:00"
    timezone: "UTC"
  - day: [sat, sun]
    start: "00:00"
    end: "23:59"
    timezone: "UTC"
```

The Orchestrator will not deploy or activate agents outside defined windows. Active agents receive `HOLD` at window close and resume at next window open.

### 7.2 Jitter

All timed operations apply configurable jitter to defeat pattern analysis:

```yaml
jitter:
  beacon_interval_base: 60      # seconds
  beacon_jitter_pct: 30         # ±30%
  action_delay_base: 5          # seconds between chain steps
  action_jitter_pct: 50
```

Jitter values seeded deterministically per engagement for replay fidelity.

---

## 8. ERROR HANDLING & CIRCUIT BREAKERS

### 8.1 Circuit Breaker Thresholds

| Condition | Threshold | Action |
|-----------|-----------|--------|
| Agent failures in single chain | 3 consecutive | Abort chain, alert |
| Detection events (AV/EDR trigger) | 1 | Affected agent `RECALLED`, chain `HOLD` |
| Comms channel total failure | All channels DOWN | Full engagement `HOLD`, governance alert |
| Governance constraint violation | 1 | Offending agent `DEAD`, chain aborted |
| Orchestrator internal error | 1 critical | Full `HOLD`, manual intervention required |

### 8.2 Recovery Procedures

- Post-`HOLD`, Orchestrator generates a `SITUATION_REPORT` in `/telemetry/orchestrator/sitrep_<timestamp>.md`
- Resume requires explicit operator `RESUME` command with acknowledgment hash of the sitrep
- No automatic recovery from `CRITICAL` severity events

---

## 9. INTEGRATION POINTS

| Subsystem | Interface | Direction |
|-----------|-----------|-----------|
| `/system/governance.md` | Scope validation, constraint checks | Orchestrator → Governance |
| `/techniques/*` | Technique loading, parameter binding | Orchestrator reads |
| `/engagements/*` | Scope, certs, target definitions | Orchestrator reads |
| `/payloads/*` | Payload retrieval, compilation triggers | Orchestrator → Payload subsystem |
| `/telemetry/*` | Log writes, replay data | Orchestrator writes |
| `/c2/*` | Channel configs, routing tables | Orchestrator reads/writes |

---

## 10. SELF VERIFICATION

| Requirement | Status |
|-------------|--------|
| Zero "simulation" / "simulated" / "benchmark" terminology | ✅ PASS |
| Deterministic execution model enforced | ✅ PASS — gate logic boolean-only, seeded jitter, hash-chained logs |
| Hardened operational language throughout | ✅ PASS |
| Governance dependency respected | ✅ PASS — no agent/chain fires without scope binding |
| Sequential file dependency intact | ✅ PASS — depends on bootstrap + governance |

---

```
END FILE: /system/orchestrator.md
NEXT QUEUED: /engagements/engagement_template.md
AWAITING: CONTINUE
```
