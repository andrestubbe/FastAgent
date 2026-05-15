# FastAgent Architecture

FastAgent is not a framework; it is an Agent Runtime Model. The core cycle is defined by: **State → Plan → Act → Observe → Adjust**.

---

## Architecture Overview (ASCII)

```text
                ┌──────────────────────────┐
                │      FastAgentCore       │
                │  (State Machine + Loop)  │
                └─────────────┬────────────┘
                              │
     ┌────────────────────────┼────────────────────────┐
     │                        │                        │
┌──────────────┐       ┌──────────────┐        ┌──────────────┐
│  FastBrain   │       │  FastTools   │        │  FastMemory   │
│ (LLM/Planner)│       │ (UIA/CLI/API)│        │ (Short/Long)  │
└──────┬───────┘       └──────┬───────┘        └──────┬───────┘
       │                       │                        │
       │                       │                        │
       ▼                       ▼                        ▼
┌──────────────┐       ┌──────────────┐        ┌──────────────┐
│  FastVision  │       │  FastUIA     │        │  CREAM Engine │
│ (Perception) │       │ (Actuation)  │        │ (Timeline)    │
└──────────────┘       └──────────────┘        └──────────────┘
```

---

## State Model

The agent maintains a strictly typed `AgentState`:
- **TaskState**: Current goals, steps, and execution progress.
- **MemoryState**: Short-term context and long-term learned facts.
- **WorldState**: UI hierarchy, file system state, processes, and screenshots.
- **ErrorState**: Failure modes, deviations, and recovery attempts.

---

## Execution Loop

The runtime operates through a deterministic closed-loop system:

```java
while (!state.task().isDone()) {
    // 1. Construct deterministic task graph
    plan = planner.plan(state);
    
    // 2. Actuation: Execute through runtime boundaries
    step = plan.next();
    observation = executor.execute(step);
    
    // 3. Verification: Observe state change & Commit memory
    state = monitor.update(state, observation);
}
```

---

## CREAM Integration

CREAM (Context Reconstruction Engine & Activity Model) provides the temporal foundation:
- **Timeline**: A linear trace of all agent actions.
- **World-State-Snapshots**: Periodic captures of the OS environment.
- **Deterministic Replays**: The ability to reconstruct any run from history.
- **Temporal Reconstruction**: Understanding the past to improve future planning.

FastAgent leverages CREAM for **Memory**, **Debugging**, and **Observability**.
