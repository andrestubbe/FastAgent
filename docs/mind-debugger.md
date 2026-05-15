# Mind Debugger: Visualizing Cognition

The **Mind Debugger** is the primary UI for inspecting, debugging, and replaying FastAgent execution tracks. It treats the agent's reasoning like source code in a traditional debugger.

---

## 1. UI Layout Strategy

### Left: Timeline List
- A vertical list of all `State Nodes`.
- Each entry shows: `#Index [Phase] ActionName (Status)`.
- Color-coded: Success (Green), Failure (Red), Replan (Yellow).

### Center: State Details (MRI View)
Tabs for deep introspection:
- **Perception**: The UIA snapshot and focused windows at the time of decision.
- **Reasoning**: The raw LLM thought process and goal stack.
- **Plan**: The proposed task graph for this specific step.
- **Action/Obs**: The exact tool call and the resulting system change.

### Right: Context & Replay
- **World Viewer**: A thumbnail or tree view of the UI state (powered by FastUIA/FastVision).
- **Branch Tree**: A visual graph showing the main timeline and any experimental forks.
- **Replay Controls**: [Jump to State] [Replay from here] [Fork Branch].

---

## 2. Key Features

### 1. The "MRI" Diff View
Compare `State N` with `State N+1`.
- What changed in the `WorldState`?
- Which variable in the `InternalState` was updated?
- Why did the `Observation` trigger a `Replan`?

### 2. Anomaly Detection
The debugger automatically highlights states where:
- The `Observation` did not match the expected `Plan` result.
- The `durationMs` exceeded the threshold.
- The `ErrorState` was triggered.

### 3. Replay & Fork
If an agent fails at Step 89, the developer can:
1.  **Jump** to Step 88.
2.  **Inspect** the variables.
3.  **Fork** a new branch with a modified goal or tool parameter.
4.  **Execute** to see if the new path succeeds.
