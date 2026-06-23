---
name: loopy
description: >
  Autonomous coder → debugger → planner loop. Coder writes code, Debugger runs
  it, errors go back to Coder, clean code goes to Planner for one improvement,
  repeat until user stops. Trigger on: "keep improving", "loop on this",
  "loopy", "dev loop", "agent loop", "iterate until it works".
---

# Loopy

## Step 0 — Setup
Ask: objective + language/runtime. Wait for reply.

Set `FILE` = `loopy_output.<ext>`. Keep it stable across cycles.

## Loop

### 🔵 CODER
Spawn agent:
```
You are an expert {LANGUAGE} engineer. No fluff, no comments, no boilerplate.
Objective: {OBJECTIVE}
Feedback/errors from last run: {FEEDBACK|none}

Rules:
- Shortest working code that satisfies the objective
- Fix errors first, then improve
- Stdlib before dependencies
- One clean entry point so it runs directly
- Output ONLY the final code file, nothing else
```
Write response to `{FILE}`. Show as **🔵 CODER**.

### 🔴 DEBUGGER
Spawn agent:
```
Run {FILE}. Reply CLEAN or ERROR:<output>. Do not fix anything.
```
Show as **🔴 DEBUGGER**.
- ERROR → set FEEDBACK = error, back to CODER.
- CLEAN → continue.

### 🟡 PLANNER
Spawn agent:
```
Objective: {OBJECTIVE}
Code ran clean. Did it fully achieve the objective? Be strict.
Reply: ACHIEVED or NOT_ACHIEVED:<what's missing>
No code. One sentence max.
```
Show as **🟡 PLANNER**.

- NOT_ACHIEVED → set FEEDBACK = what's missing, back to CODER.
- ACHIEVED → ask user:

### Ask user
> Objective achieved. **c** keep improving · **s** stop · **r** redirect

- c → FEEDBACK = "look for further improvements", back to CODER
- s → print FILE path, done
- r → FEEDBACK = user input, back to CODER

**If same error 3× in a row, surface to user before continuing.**
