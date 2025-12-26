---
description: Iterative root cause analysis for frontend issues using hypothesis-driven debugging with Puppeteer
allowed-tools: Task, Read, Write, Edit, Bash, Glob, Grep, AskUserQuestion, mcp__puppeteer__puppeteer_connect_active_tab, mcp__puppeteer__puppeteer_navigate, mcp__puppeteer__puppeteer_screenshot, mcp__puppeteer__puppeteer_click, mcp__puppeteer__puppeteer_fill, mcp__puppeteer__puppeteer_evaluate
argument-hint: [issue description]
---

# Frontend Root Cause Analysis: $ARGUMENTS

Ultrathink.

## Objective

Perform iterative hypothesis-driven root cause analysis for a frontend issue. This command follows a scientific debugging approach:

1. **Analyze** the issue and relevant code
2. **Hypothesize** the root cause
3. **Instrument** with diagnostic logs
4. **Observe** user recreating the issue
5. **Verify** hypothesis via logs/browser state
6. **Iterate** (if wrong) or **Fix** (if confirmed)
7. **Validate** fix works
8. **Commit** (with user approval)

---

## Phase 1: Environment Setup

### Step 1.1: Start Dev Server

Check if dev server is running, if not start it:

```bash
# Check if already running
lsof -i :3000 || pnpm dev &
```

Wait a few seconds for the server to be ready.

### Step 1.2: Launch Chrome with Remote Debugging

First, check if Chrome debugging port is already in use:

```bash
lsof -i :9222
```

**If port 9222 is NOT in use**, launch Chrome with remote debugging:

```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --remote-debugging-port=9222 \
  --user-data-dir=/tmp/chrome-debug-profile \
  "http://localhost:3000" &
```

**If port 9222 IS in use**, a debug Chrome is already running - skip to Step 1.3.

**Verify Chrome started successfully:**

```bash
sleep 3 && lsof -i :9222
```

You should see Chrome (Google) listening on port 9222. If not, check Error Recovery section.

### Step 1.3: Connect Puppeteer to Chrome

1. Use `mcp__puppeteer__puppeteer_connect_active_tab` to connect to the running Chrome instance
2. **Take an initial screenshot immediately** to confirm connection:
   ```
   mcp__puppeteer__puppeteer_screenshot(name="initial-state", width=1400, height=900)
   ```
3. Navigate to the relevant page if needed using `mcp__puppeteer__puppeteer_navigate`

**Inform the user**: "Dev server started and Chrome connected. Ready for debugging."

---

## Phase 2: Issue Analysis

### Step 2.1: Understand the Issue

Parse the issue description from `$ARGUMENTS`.

If `$ARGUMENTS` is empty or unclear:
- Use `AskUserQuestion` to ask: "Please describe the frontend issue you're experiencing. What behavior are you seeing vs. what you expect?"

### Step 2.2: Identify Relevant Code

Based on the issue description:

1. Use `Glob` and `Grep` to find relevant files:
   - Components related to the issue
   - API routes if data-related
   - State management if state-related

2. Read the most relevant files to understand current implementation

3. Look for:
   - State flow
   - Event handlers
   - Data transformations
   - Conditional rendering logic
   - API calls

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

Add `console.log` statements to prove or disprove the hypothesis:

```javascript
// Example diagnostic logs
console.log('[RCA] Component mounted with props:', props);
console.log('[RCA] State before update:', state);
console.log('[RCA] API response:', data);
console.log('[RCA] Condition check:', { condition, result });
```

**Guidelines:**
- Prefix all logs with `[RCA]` for easy filtering
- Log BEFORE and AFTER key operations
- Log function inputs and outputs
- Log conditional branch decisions
- Keep logs focused on the hypothesis

### Step 4.2: Save Changes

Save the instrumented files. Do NOT commit yet.

**Inform user**: "Diagnostic logs added. Ready for you to recreate the issue."

---

## Phase 5: User Handover - Recreate Issue

### Step 5.1: Hand Over to User

Use `AskUserQuestion` with the following:

**Question**: "Please recreate the issue now. When you're done, tell me what happened and I'll check the logs."

**Options**:
- "I've recreated the issue"
- "I couldn't recreate it"
- "Something different happened"

**While waiting**: Use Puppeteer to monitor the page if applicable.

---

## Phase 6: Log Analysis

### Step 6.1: Collect Evidence

After user recreates the issue:

1. Take a screenshot of the current state with `mcp__puppeteer__puppeteer_screenshot`
2. Use `mcp__puppeteer__puppeteer_evaluate` to inspect DOM state, check for errors, or run diagnostic JavaScript
3. Ask the user to check browser DevTools console for `[RCA]` prefixed logs and report findings

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
   git checkout -- .
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
   git checkout -- .
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

### Step 8.3: Hand Over for Verification

Use `AskUserQuestion`:

**Question**: "I've implemented the fix. Please test it and let me know if it resolves the issue."

**Options**:
- "The fix works!"
- "The issue still exists"
- "New issue appeared"
- "Partially fixed"

---

## Phase 9: Final Verification

### If Fix NOT Working:

1. Use Puppeteer to check console for errors
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
git add -A
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

## Error Recovery

### Dev Server Issues
If dev server fails to start:
- Check for port conflicts: `lsof -i :3000`
- Check for missing dependencies: `pnpm install`
- Check for build errors: `pnpm build`

### Chrome Debugging Issues

**"Failed to connect to Chrome debugging port 9222"**
1. Check if Chrome with debugging is running: `lsof -i :9222`
2. If not running, launch Chrome with the command in Step 1.2
3. If Chrome is running but can't connect, kill and restart:
   ```bash
   pkill -f "chrome-debug-profile"
   sleep 2
   /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
     --remote-debugging-port=9222 \
     --user-data-dir=/tmp/chrome-debug-profile \
     "http://localhost:3000" &
   ```

**Chrome opens but debugging doesn't work**
- Ensure you're using `--user-data-dir=/tmp/chrome-debug-profile` (separate profile)
- Regular Chrome windows don't have debugging enabled
- The debug Chrome window has a yellow "controlled by automated software" banner

### Puppeteer Connection Issues
If Puppeteer MCP is not responding:
- The user may need to restart Claude Code to load the MCP server
- Inform user: "Puppeteer MCP may need to be reconnected. Try restarting Claude Code."

### Git Reset Issues
If git reset fails:
- Check for unstaged changes: `git status`
- Stash if needed: `git stash`

---

## Puppeteer Tips for React/Shadcn

### Clicking Shadcn/Radix Components

Standard CSS selectors often fail with Shadcn components. Use `mcp__puppeteer__puppeteer_evaluate` instead:

```javascript
// Click a Shadcn Tab by text content
const tabs = document.querySelectorAll('[role="tablist"] button');
for (const tab of tabs) {
  if (tab.textContent.includes('Messages')) {
    tab.click();
    break;
  }
}
```

```javascript
// Click a button by text
const buttons = document.querySelectorAll('button');
for (const btn of buttons) {
  if (btn.textContent.includes('Submit')) {
    btn.click();
    break;
  }
}
```

### React State Updates

React state changes triggered via Puppeteer may not visually update immediately due to React's synthetic event system. After clicking:

1. Wait briefly: `await new Promise(r => setTimeout(r, 100))`
2. Take a screenshot to verify the actual state
3. If visual doesn't update, the click may have worked but React didn't re-render - check console logs

### Inspecting DOM State

```javascript
// Get current component state from DOM
document.querySelector('[data-state]')?.getAttribute('data-state')

// Check if element is visible
const el = document.querySelector('.my-class');
el ? getComputedStyle(el).display : 'not found'

// Get all console errors
window.__rca_errors = window.__rca_errors || [];
console.error = (...args) => { window.__rca_errors.push(args); };
```

### Taking Effective Screenshots

- Use `width=1400, height=900` for full page context
- Take screenshots AFTER every significant action
- Name screenshots descriptively: `"after-click-submit"`, `"error-state"`, `"form-filled"`

---

## Success Criteria

The RCA session is complete when:
- Root cause has been identified and confirmed with evidence
- Fix has been implemented and verified by user
- Changes have been committed (with user approval)

**Begin by setting up the environment and understanding the issue.**
