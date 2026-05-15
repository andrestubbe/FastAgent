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

## 2. State Navigator API (Logical Structure)
Thinking in terms of a `TrackStore` (File, DB, or Append-Log).

```typescript
interface TrackStore {
  getById(id: string): StateNode | null;
  getNext(id: string): StateNode | null;
  getPrev(id: string): StateNode | null;
  getChildren(id: string): StateNode[];
  getBranch(branchId: string): StateNode[];
  query(filter: (s: StateNode) => boolean): StateNode[];
}
```

### Primitive Navigation
- **jumpTo(id)**: Direct debug jump.
- **replayFrom(id)**: Deterministic reproduction from a specific point.
- **timeline(sessionId)**: Complete session overview.

---

## 3. State Forking Mechanism (Alternative Futures)
Forking is simply creating a new `branchId` with a specific `parentId`.

```typescript
function forkState(baseId: string, newBranchId: string): StateNode {
  const base = store.getById(baseId);
  if (!base) throw new Error("Base state not found");

  const forkRoot: StateNode = {
    ...base,
    id: generateId(),
    parentId: base.id,
    branchId: newBranchId,
    stepIndex: base.stepIndex + 1,
    meta: {
      ...base.meta,
      forkedFrom: base.id
    }
  };

  store.save(forkRoot);
  return forkRoot;
}
```

### LLM Determinism
For the fork to remain deterministic, the **deterministicSeed** must be preserved along with the same prompt and tool parameters.

