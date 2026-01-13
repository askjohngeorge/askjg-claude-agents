---
description: Iterative root cause analysis for Pipecat bot issues using hypothesis-driven debugging
allowed-tools: Task, Read, Write, Edit, Bash, Glob, Grep, AskUserQuestion, TaskOutput
argument-hint: [issue description]
---

# Pipecat Bot Root Cause Analysis: $ARGUMENTS

Ultrathink.

## Objective

Perform iterative hypothesis-driven root cause analysis for a Pipecat bot issue. This command follows a scientific debugging approach:

1. **Analyze** the issue and relevant code
2. **Hypothesize** the root cause
3. **Instrument** with diagnostic logs
4. **Observe** via terminal output when user tests
5. **Verify** hypothesis via logs
6. **Iterate** (if wrong) or **Fix** (if confirmed)
7. **Validate** fix works
8. **Commit** (with user approval)

---

## Phase 1: Environment Setup

### Step 1.1: Stop Existing Bot

Kill any existing process on port 7860:

```bash
lsof -ti:7860 | xargs kill -9 2>/dev/null || true
```

### Step 1.2: Start Bot Server

Start the Pipecat bot in the background:

```bash
cd /Users/jga01/dev/pcc-askjg-gemini-demo/scripts/pipecat && uv run python bot.py
```

Run this as a background task so you can monitor the logs throughout the session.

### Step 1.3: Verify Bot is Running

Wait a few seconds, then check if the bot started successfully by looking for the startup log message:
- "Starting Pipecat bot"
- Server listening on port 7860

**Inform the user**: "Bot server started. Ready for debugging. You can connect via /dashboard/bot-test"

---

## Phase 2: Issue Analysis

### Step 2.1: Understand the Issue

Parse the issue description from `$ARGUMENTS`.

If `$ARGUMENTS` is empty or unclear:
- Use `AskUserQuestion` to ask: "Please describe the Pipecat bot issue you're experiencing. What behavior are you seeing vs. what you expect?"

### Step 2.2: Identify Relevant Code

Based on the issue description, search relevant files:

1. Use `Glob` and `Grep` to find relevant code:
   - `scripts/pipecat/bot.py` - Main bot pipeline
   - `scripts/pipecat/*.py` - Custom processors, observers, reporters
   - `_refs/pipecat/` - Reference Pipecat source code

2. Focus on:
   - Pipeline structure and frame flow
   - Event handlers (`@transport.event_handler`, `@rtvi.event_handler`)
   - Observer methods (`on_push_frame`)
   - Frame types (TTSTextFrame, TranscriptionFrame, etc.)

3. Use the `pipecat-expert` subagent if needed to understand Pipecat internals

**Document findings** for hypothesis formation.

---

## Phase 3: Hypothesis Formation

### Step 3.1: Form Initial Hypothesis

Based on code analysis, form a hypothesis about the root cause.

**Hypothesis Template:**
```
HYPOTHESIS #[N]:
- What I think is happening: [description]
- Why I think this: [evidence from code]
- How to prove/disprove: [specific logs to add]
- Expected log output if TRUE: [what logs would show]
- Expected log output if FALSE: [what logs would show]
```

**Present the hypothesis to the user** and explain what you'll be looking for.

---

## Phase 4: Instrumentation

### Step 4.1: Add Diagnostic Logs

Add `logger.info` or `logger.debug` statements to prove or disprove the hypothesis:

```python
# Example diagnostic logs
from loguru import logger

logger.info(f"[RCA] Frame received: {type(frame).__name__}")
logger.info(f"[RCA] Frame content: {frame}")
logger.info(f"[RCA] Buffer state: {self._buffer}")
logger.info(f"[RCA] Condition check: {condition=}, {result=}")
```

**Guidelines:**
- Prefix all logs with `[RCA]` for easy filtering
- Log BEFORE and AFTER key operations
- Log frame types and their content
- Log buffer/state changes
- Log conditional branch decisions
- Use f-strings with `{var=}` syntax for clear output

### Step 4.2: Save Changes

Save the instrumented files. Do NOT commit yet.

### Step 4.3: Restart Bot

Kill and restart the bot to pick up changes:

```bash
lsof -ti:7860 | xargs kill -9 2>/dev/null || true
sleep 1
cd /Users/jga01/dev/pcc-askjg-gemini-demo/scripts/pipecat && uv run python bot.py
```

**Inform user**: "Diagnostic logs added and bot restarted. Ready for you to test."

---

## Phase 5: User Handover - Test the Issue

### Step 5.1: Hand Over to User

Use `AskUserQuestion` with the following:

**Question**: "Please connect to the bot via /dashboard/bot-test and recreate the issue. When done, tell me what happened."

**Options**:
- "I've recreated the issue"
- "I couldn't recreate it"
- "Something different happened"

**While waiting**: Monitor the background task output for `[RCA]` logs.

---

## Phase 6: Log Analysis

### Step 6.1: Collect Evidence

After user recreates the issue:

1. Check the background task output for `[RCA]` prefixed logs
2. Look for:
   - Frame flow (what frames were processed)
   - State changes (buffers, flags)
   - Missing frames (expected but not seen)
   - Errors or exceptions

### Step 6.2: Analyze Results

Compare actual log output against expected output from hypothesis:

```
ANALYSIS:
- Expected if TRUE: [from hypothesis]
- Actual output: [from logs]
- Conclusion: CONFIRMED / DISCONFIRMED
```

---

## Phase 7: Decision Point

### If Hypothesis DISCONFIRMED:

1. **Reset instrumentation**:
   ```bash
   git checkout -- scripts/pipecat/
   ```

2. **Analyze why it was wrong**:
   - What did the logs reveal?
   - What new information do we have?

3. **Form new hypothesis** and return to **Phase 3**

4. **Inform user**: "Hypothesis #N was incorrect. Based on the logs, I now suspect [new theory]. Let me add new diagnostic logs."

### If Hypothesis CONFIRMED:

1. **Document the root cause**:
   ```
   ROOT CAUSE CONFIRMED:
   - The issue is: [description]
   - It happens because: [explanation]
   - Evidence: [log output that proves it]
   ```

2. **Reset instrumentation**:
   ```bash
   git checkout -- scripts/pipecat/
   ```

3. **Proceed to fix** in Phase 8

---

## Phase 8: Implement Fix

### Step 8.1: Design the Fix

Based on confirmed root cause:

1. Identify the exact code change needed
2. Consider edge cases
3. Ensure fix doesn't break other functionality

**Present fix plan to user** before implementing.

### Step 8.2: Apply the Fix

Make the necessary code changes using `Edit` tool.

**Guidelines:**
- Make minimal, targeted changes
- Don't refactor unrelated code
- Preserve existing patterns

### Step 8.3: Restart and Test

1. Restart the bot:
   ```bash
   lsof -ti:7860 | xargs kill -9 2>/dev/null || true
   sleep 1
   cd /Users/jga01/dev/pcc-askjg-gemini-demo/scripts/pipecat && uv run python bot.py
   ```

2. Use `AskUserQuestion`:
   **Question**: "I've implemented the fix and restarted the bot. Please test it and let me know if it resolves the issue."

   **Options**:
   - "The fix works!"
   - "The issue still exists"
   - "New issue appeared"
   - "Partially fixed"

---

## Phase 9: Final Verification

### If Fix NOT Working:

1. Check bot logs for errors
2. Analyze what's still wrong
3. Either:
   - Iterate on current fix
   - Reset and form new hypothesis if root cause was wrong

### If Fix WORKS:

Proceed to Phase 10.

---

## Phase 10: Commit (with approval)

### Step 10.1: Request Commit Approval

Use `AskUserQuestion`:

**Question**: "The fix has been verified. Would you like me to commit these changes?"

**Options**:
- "Yes, commit it"
- "No, I want to review first"
- "Make some changes first"

### Step 10.2: Create Commit (if approved)

```bash
git add scripts/pipecat/
git commit -m "Fix: [brief description of fix]

Root cause: [one line explanation]
"
```

---

## Iteration Tracking

Track the debugging session:

```
SESSION LOG:
============
Issue: [original description]

Iteration 1:
- Hypothesis: [...]
- Result: [CONFIRMED/DISCONFIRMED]
- Evidence: [...]

[Repeat for each iteration]

Resolution:
- Root cause: [...]
- Fix applied: [...]
- Verified: [YES/NO]
- Committed: [YES/NO]
```

---

## Pipecat-Specific Debugging Tips

### Common Frame Types to Watch
- `TranscriptionFrame` - User speech transcription
- `InterimTranscriptionFrame` - Partial transcription
- `TTSTextFrame` - Text being spoken by bot
- `LLMTextFrame` - LLM output text
- `AudioRawFrame` - Raw audio data

### Common Issues
- **Missing frames**: Check pipeline order, frame filtering
- **State not updating**: Check buffer logic, event handlers
- **RTVI not working**: Check RTVIProcessor/RTVIObserver setup
- **Transcription issues**: Check TranscriptProcessor position in pipeline

### Useful Log Points
- After `transport.input()` - Incoming frames
- Before/after processors - Frame transformations
- In observer `on_push_frame` - What the observer sees
- Event handlers - When events fire

---

## Error Recovery

### Bot Won't Start
- Check for syntax errors: `uv run python -m py_compile scripts/pipecat/bot.py`
- Check for import errors in the logs
- Verify environment variables are set

### Port Still in Use
```bash
lsof -i :7860  # See what's using the port
kill -9 <PID>  # Kill specific process
```

### Git Reset Issues
```bash
git status  # Check state
git stash   # Stash if needed
git checkout -- scripts/pipecat/  # Reset pipecat files only
```

---

## Success Criteria

The RCA session is complete when:
- Root cause has been identified and confirmed with evidence
- Fix has been implemented and verified by user
- Changes have been committed (with user approval)

**Begin by setting up the environment and understanding the issue.**
