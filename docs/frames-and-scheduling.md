# Frames & Intent Scheduling: The Execution Kernel

FastAgent does not follow a stochastic prompt-loop. Instead, it operates on a **Deterministic Frame Schedule**, borrowing architectural patterns from Game Engines and Operating System Schedulers.

---

## 1. Deterministic Frames (The Kernel Heartbeat)
Execution is divided into discrete, atomic units called **Frames**. Each frame ensures that the agent state transition is observable, repeatable, and valid.

### Frame Phases
1.  **INGEST**:
    *   Capture UI hierarchy via FastUIA.
    *   Capture screen context via FastVision/FastOCR.
    *   Collect any external system events (file changes, process triggers).
2.  **INTERNAL**:
    *   **Memory Commit**: Archive perception into the short-term state.
    *   **Goal Resolution**: Update the **Intent Graph** based on the new context.
    *   **Policy Resolve**: Select the next best action from the available tools.
3.  **ACTUATION**:
    *   Execute the selected tool through the **Execution Boundary**.
    *   Capture immediate system feedback (success/fail).
4.  **COMMIT**:
    *   Finalize the state transition.
    *   Archive the frame metadata to the **CREAM Timeline**.

---

## 2. Hierarchical Intent Graph
Traditional agents use a flat list of tasks. FastAgent uses a **Dynamic Intent Graph** that evolves recursively.

### The Lifecycle of an Intent
- **INTENT**: High-level user goal (e.g., "Install Maven").
- **SUB-INTENTS**:
    - **Research**: Check if Maven is already installed.
    - **Plan**: Download and configure environment variables.
    - **Verify**: Run `mvn -version` to confirm success.

### Self-Correction Logic
If the `Verify` sub-intent fails, the Kernel does not just "retry." It modifies the Graph:
1.  `Verify` fails → Error detected.
2.  `Recovery` intent is injected before the original `Verify`.
3.  `Recovery` executes (e.g., "Check PATH manually").
4.  `Verify` is re-attempted.

---

## 3. Time-Travel Debugging
Because each **Frame** is a complete snapshot of the system at that moment, FastAgent allows for **Temporal Debugging**:
- **Rewind**: Step back to Frame #452 to see why the agent decided to delete a file.
- **Rollback**: Undo a frame's action if the validation phase detects an anomaly.
- **Simulate**: Fork the current state into a virtual frame to test an action without real-world side effects.
