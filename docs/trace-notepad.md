# FastAgent Execution Trace — Notepad Demo

**Task**: "Open Notepad, write 'Hello Andre', save it to Desktop."

---

## STEP 1 — PLAN
**Planner Output**:
```json
[
  { "action": "open_app", "target": "notepad" },
  { "action": "type", "text": "Hello Andre" },
  { "action": "save_file", "path": "~/Desktop/hello.txt" }
]
```

---

## STEP 2 — EXECUTE: open_app(notepad)
**Tool**: `uia.launch`
**Observation**:
- Process started: `notepad.exe`
- Window visible: `true`

**Result**: `WorldState` updated.

---

## STEP 3 — EXECUTE: type("Hello Andre")
**Tool**: `uia.type`
**Observation**:
- Text inserted into buffer.
- Caret position updated.

**Result**: `WorldState` updated.

---

## STEP 4 — EXECUTE: save_file("~/Desktop/hello.txt")
**Tools**:
1. `uia.hotkey("Ctrl+S")`
2. `uia.type("~/Desktop/hello.txt")`
3. `uia.press("Enter")`

**Observation**:
- File created on disk.
- File path verified.

**Result**: `WorldState` updated.

---

## STEP 5 — DONE
**TaskState**:
- Status: `success`
- Duration: `2.4s`

**CREAM Snapshot**:
- 3 UI screenshots archived.
- 3 tool calls logged.
- 3 observations verified.
- **Deterministic Replay Ready.**
