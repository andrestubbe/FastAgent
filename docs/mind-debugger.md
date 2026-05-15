# Mind Debugger: Visualizing Cognition

The **Mind Debugger** is the primary UI for inspecting, debugging, and replaying FastAgent execution tracks. It treats the agent's reasoning like source code in a traditional debugger.

---

## 1. UI Layout Strategy (Three-Column System)

### Column A: Timeline List (Left)
- **Entries**: `#42 [ACT] vscode: focusWindow (SUCCESS)`.
- **Filtering**: Phase, Tag, Error, Tool.

### Column B: State Details / MRI View (Center)
- **Tabs**: Input | Internal | Decision | Action | Observation | Raw JSON.
- **Functionality**: Deep diffing between State N and N+1.

### Column C: Context & Visuals (Right)
- **UIA Snapshot**: Thumbnail or Tree View.
- **Goal Stack**: Visualized hierarchy of intents.
- **Branch Tree**: Interactive graph of forks.

---

## 2. Debugger Pseudo-Code (Java Swing Style)
```java
class MindDebugger extends JFrame {
    JList<StateNode> timelineList;
    JTextArea jsonView;
    JPanel detailsPanel;
    JTree branchTree;

    void onSelectState(StateNode s) {
        jsonView.setText(prettyJson(s));
        detailsPanel.showState(s);
        branchTree.setSelectionPath(pathForState(s));
    }
}
```

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
