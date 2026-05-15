# Mind Architecture: The Timeline Track

FastAgent represents an agent's reasoning as a persistent, immutable graph of **State Nodes**. This architecture enables deterministic replays, forking, and deep introspection.

---

## 1. State Node Schema (JSON)
Each node in the timeline track contains the full context of the agent at that specific moment.

```json
{
  "id": "state-00042",
  "parentId": "state-00041",
  "sessionId": "session-2025-05-15-abc",
  "timestamp": "2025-05-15T14:23:11.123Z",
  "phase": "PLAN|ACT|OBSERVE|ADJUST",
  "stepIndex": 42,
  "branchId": "main", 
  "deterministicSeed": 1337,

  "input": {
    "userIntent": "open vscode and load project X",
    "perception": {
      "uiaSnapshotId": "uia-1234",
      "focusedApp": "Code.exe",
      "windowTitle": "FastAgent.java - Visual Studio Code"
    }
  },

  "internal": {
    "goals": ["Locate project X", "Open main file"],
    "plan": ["Find VS Code window", "Open matching entry"],
    "variables": { "projectName": "FastAgent", "retryCount": 1 }
  },

  "decision": {
    "tool": "uia.findWindow",
    "arguments": { "titleContains": "Visual Studio Code" },
    "reasoningSummary": "VS Code already open, reuse existing window."
  },

  "action": {
    "type": "UIA_ACTION",
    "name": "focusWindow",
    "status": "SUCCESS",
    "durationMs": 37
  },

  "observation": {
    "uiaSnapshotAfterId": "uia-1235",
    "resultSummary": "VS Code focused, project list visible."
  }
}
```

---

## 2. State Navigator API
The `TrackStore` allows for non-linear navigation through the agent's history.

```java
public interface TrackStore {
    /** Returns a specific state node by ID */
    StateNode getById(String id);
    
    /** Returns the entire timeline for a branch, sorted by step index */
    List<StateNode> getBranch(String branchId);
    
    /** Creates a new branch from an existing state (Forking) */
    StateNode fork(String baseStateId, String newBranchId);
}
```

---

## 3. State Forking (Alternative Futures)
Forking allows the developer (or the agent itself) to explore alternative execution paths without corrupting the original timeline.

1.  **Select Base**: Pick `state-00042`.
2.  **Create Branch**: Generate a new `branchId` (e.g., `optimization-test`).
3.  **Execute**: The agent resumes from the base state but commits new nodes to the new branch.
4.  **Compare**: Use the **Mind Debugger** to compare the success rates of different branches.
