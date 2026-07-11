# Heartbeat Example

## Active Heartbeat (mid-session)
```text
[CONTINUITY_HEARTBEAT]
type: active
timestamp: "2026-07-11T14:30:22Z"
trigger:
  kind: "every_n_turns"
  reason: "5-turn interval reached"
attention_focus: "Debugging encoding crash in hook subprocess"
active_assumptions:
  - "pipeline.py and bridge.mjs verified — both output pure ASCII"
  - "0xB2 error is cosmetic — daemon crash, main flow unaffected"
unresolved_threads:
  - "0xB2 exact source not isolated (likely stderr pipe encoding)"
  - "Heartbeat reliability — missed 8 turns this session"
confidence:
  heartbeat_quality: high
[/CONTINUITY_HEARTBEAT]
```

## Closure Heartbeat (session ending / idle)
```text
[CONTINUITY_HEARTBEAT]
type: closure
timestamp: "2026-07-11T15:00:00Z"
trigger:
  kind: "idle_closure"
  reason: "Idle gap exceeded threshold"
last_attention_focus: "ContinuityKit protocol implementation"
unresolved_threads:
  - "Heartbeat turn counter state machine"
  - "Snapshot file format design"
pending_decisions:
  - "Whether to log heartbeat history by default"
snapshot_recommendation: "Write end-of-session snapshot"
confidence:
  heartbeat_quality: high
[/CONTINUITY_HEARTBEAT]
```
