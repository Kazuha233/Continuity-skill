# ContinuityKit Protocol Specification

Version: 1.0.0 | Last updated: 2026-07-11

## 1. Principle

Continuity is connection quality, not uninterrupted existence.

This protocol improves agent continuity by preserving and validating structured state across turns and sessions. It does not claim subjective experience, offline awareness, or uninterrupted consciousness.

## 2. Runtime Model

```
pre_llm_call  →  LLM Generation  →  post_llm_validation
     │                                  │
     ▼                                  ▼
 Inject snapshot                    Validate heartbeat
 & heartbeat trigger                 & boundary rules
```

## 3. Event Types

| Event | Description |
|-------|-------------|
| `heartbeat` | Periodic self-calibration |
| `session_snapshot` | End-of-session state dump |
| `relay_pointer_appended` | New session added to index |
| `relay_startup` | Session start with RELAY recovery |
| `validation_failed` | Boundary rule violation detected |
| `memory_promoted` | Heartbeat content promoted to long-term |

## 4. Heartbeat Policy

```yaml
heartbeat_policy:
  enabled_scope: "active_conversation"
  default_interval: 5  # turns
  triggers:
    every_n_turns: true
    after_topic_shift: true
    after_major_decision: true
    before_end_snapshot: true
    idle_closure:
      enabled: true
      trigger: "next_model_call_after_idle_gt_20min"
```

See `skill/continuity-kit.skill.md` for full protocol details and structured block formats.
