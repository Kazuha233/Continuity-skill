# ContinuityKit Protocol Rules

This file activates the ContinuityKit heartbeat protocol at the system prompt level.
Place this file in your project root to enforce heartbeat compliance.

## Rules

Rule: Every 5 responses in this session, you MUST emit a `[CONTINUITY_HEARTBEAT]` block.
  - Count your responses from 1 at session start.
  - Include the heartbeat block in your response text.
  - Use the format from the continuity-kit skill.
  - If you skip it, the user can detect the gap.

Rule: At session end, write a `[SESSION_SNAPSHOT]` and save it.
Rule: On session start, read the RELAY file and report where you left off.

## Rationale

These rules prevent drift, session amnesia, and ghost continuity.
The .hermes.md level is evaluated every turn — this is not optional advice,
it is a standing instruction that persists for the entire session.
