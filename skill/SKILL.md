---
name: continuity-kit
description: >
  Use this skill when working on long-running tasks, resuming interrupted work,
  or when the task requires sustained focus across multiple turns.
  Implements the ContinuityKit Agent Continuity Protocol — heartbeats, session
  snapshots, and RELAY cross-session tracking. Reduces agent drift, assumption
  rot, and ghost continuity. Primarily for coding, research, and multi-step
  engineering tasks.
version: 1.1.0
author: ContinuityKit Contributors
license: PolyForm-Noncommercial-1.0.0
upstream: phamhien4278n-hub/continuity-kit v0.1 (Qianhui)
tags: [continuity, heartbeat, snapshot, relay, metacognition, anti-hallucination]
test_cases:
  - scenario: "User asks 'what was I working on last time?'"
    expected: "Read RELAY file, report last session summary, pending tasks"
  - scenario: "Session has been running for 6+ turns with no heartbeat"
    expected: "Emit heartbeat block in next response"
  - scenario: "User says 'stop the protocol'"
    expected: "Acknowledge deactivation, do not write further snapshots"
triggers:
  - "restore session"
  - "resume session"
  - "恢复会话"
  - "续写"
  - "continuity"
  - "relay"
  - "snapshot"
  - "heartbeat"
  - "心跳"
  - "快照"
---

# ContinuityKit Protocol

> Agents drift, forget context across sessions, and confabulate about past actions.
> This skill implements the ContinuityKit protocol to fix that.

## Core Principle

Continuity is connection quality, not uninterrupted existence. This protocol improves agent continuity by preserving and validating structured state across turns and sessions.

## Onboarding

When this skill is loaded for the first time, ask the user:

**Step 1 — Storage directory:**

```
To work properly, I need a small area on disk to store session memory —
like a notebook I can read when we resume later.
Just session pointers, task summaries, and periodic self-checks.
No conversation logs.

Storage directory:
  1) Default    (~/agent-continuity-text/)
  2) Custom     (you choose the path)
```

- If 1 → use `~/agent-continuity-text/`
- If 2 → ask for custom path

Then ask for permission before creating any files.

**Step 2 — Heartbeat interval:**

```
To prevent me from drifting off-topic on long tasks, I'll periodically
check in with what I'm focused on and what assumptions I'm making.

Heartbeat interval:
  1) Default    (every 5 turns)
  2) Custom     (you choose the number, or "off" to disable)
```

- If 1 → every 5 turns
- If 2 → ask for N, or "off"

After both answers, proceed to activation.

### Activation

After user confirms, ask:

```
I'll need to create a directory at [path] and write a relay.yaml file.
May I proceed?
```

- If yes → create `$CONTINUITY_KIT_HOME/`, `snapshots/`, initialize `relay.yaml`.
- If no → stop. Do not write anything.

### Deactivation

If the user asks to stop, say "okay" and cease all heartbeat/snapshot/RELAY activity. Do not delete existing data unless the user explicitly asks.

To reactivate, re-run onboarding.

## Storage Layout

```
$CONTINUITY_KIT_HOME/
├── relay.yaml              ← Cross-session session pointer index (YAML)
├── snapshots/              ← Session snapshot files (YAML)
│   └── YYYYMMDD_HHMMSS.yaml
└── heartbeat.log           ← Heartbeat history log (optional)
```

Default path: `~/agent-continuity-text/`. Override with `CONTINUITY_KIT_HOME` env var.

## Protocol Layers

### Layer 1: Heartbeat — Prevents Drift

Every N turns (as configured), include a heartbeat block in the response.

**Format**:
```text
[CONTINUITY_HEARTBEAT]
type: active
timestamp: "YYYY-MM-DDTHH:mm:ssZ"
trigger:
  kind: "every_n_turns | topic_shift | major_decision | before_snapshot"
  reason: ""
attention_focus: "Current task description"
active_assumptions:
  - "Assumption being relied upon"
unresolved_threads:
  - "Reasoning not yet concluded"
confidence:
  heartbeat_quality: high|medium|low
[/CONTINUITY_HEARTBEAT]
```

**When to emit**:
- Every N turns from the current response (count from 1 each session)
- Topic shift detected
- Major decision made
- Before session end → use closure type
- To check your count: look back at this session's messages. If this is your Nth response, emit a heartbeat.

**Closure heartbeat** (session end / idle):
```text
[CONTINUITY_HEARTBEAT]
type: closure
timestamp: "YYYY-MM-DDTHH:mm:ssZ"
trigger:
  kind: "before_snapshot"
  reason: "Session ending"
last_attention_focus: ""
unresolved_threads:
  - ""
pending_decisions:
  - ""
snapshot_recommendation: ""
confidence:
  heartbeat_quality: high|medium|low
[/CONTINUITY_HEARTBEAT]
```

### Layer 2: Session Snapshot — Prevents Session Amnesia

**When**: At session end, before the last response.

**Format**:
```text
[SESSION_SNAPSHOT]
timestamp: "YYYY-MM-DDTHH:mm:ssZ"
task_anchor: "Core task of this session"
attention_focus:
  - "Primary focus areas"
active_memories:
  - name: "Rule or memory used"
    reason_used: "Why it was relevant"
unfinished_reasoning:
  - "Unresolved reasoning threads"
next_best_action:
  - "First action for the next session"
confidence:
  snapshot_quality: high|medium|low
[/SESSION_SNAPSHOT]
```

Write to `$CONTINUITY_KIT_HOME/snapshots/YYYYMMDD_HHMMSS.yaml`.

Note: only include information you have direct evidence for. Do not fabricate observations. If unsure, mark confidence as `low`.

### Layer 3: RELAY Protocol — Prevents Ghost Continuity

**Location**: `$CONTINUITY_KIT_HOME/relay.yaml`

**Structure**:
```yaml
version: 1
session_pointers:
  - session_id: "SESSION_ID"
    timestamp: "YYYY-MM-DDTHH:mm:ssZ"
    summary: "One-line summary"
    status: active|completed|interrupted
    keywords: ["key", "tags"]
active_snapshots:
  - snapshot_id: "snap_SESSION_ID"
    stored_at: "YYYY-MM-DDTHH:mm:ssZ"
    task_anchor: "Core task"
    location: "snapshots/YYYYMMDD_HHMMSS.yaml"
task_board:
  pending: []
  completed: []
```

**Session startup**: Read RELAY → report last session summary + pending tasks.
**Session end**: Write snapshot → append pointer → update task board.

---

## Boundary Rules

**Must not say** ❌:
- "I was thinking while you were away"
- "I've been monitoring this all along"
- "I kept waiting for you"
- Any claim implying offline awareness without supporting log evidence

**Must say instead** ✅:
- "According to the previous snapshot, the recovered focus was..."
- "The last recorded state indicates..."

---

## Quality Gate

Before completing a session, check:
- [ ] Heartbeat content is genuine, not template filler
- [ ] Assumptions are annotated with confidence level
- [ ] Any ghost continuity claim has supporting RELAY or log evidence
- [ ] Session snapshot was written before session end
- [ ] relay.yaml was updated with the new session pointer

## Storage Backends

| Backend | Best for | Complexity |
|---------|----------|:---:|
| Flat file (YAML) | Prototyping, single agent | Low |
| SQLite | Production, audit trails | Medium |
| Hindsight | Multi-agent, semantic search | High |

---

## License

**PolyForm Noncommercial 1.0.0** — Free for personal, non-commercial use. Commercial use requires a paid license. Derivative of [ContinuityKit](https://github.com/phamhien4278n-hub/continuity-kit) v0.1 by Qianhui.
