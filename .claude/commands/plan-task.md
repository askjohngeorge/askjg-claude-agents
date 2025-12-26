---
description: Create task planning documentation with selective expert analysis based on task type
allowed-tools: Task, Read, Write, Bash, Glob, Grep
---

# Plan Task: $ARGUMENTS

Ultrathink.

## Objective
Kick off planning for a new development task by gathering relevant expert analysis and creating comprehensive task documentation in `_docs/tasks/<task-name>/`. This command uses judgment to consult only the experts that are relevant to the specific task (e.g., shadcn-expert for UI tasks, turso-expert for database tasks, pipecat-expert for voice agent pipelines).

**Note**: This project uses Pipecat for voice agents (not Vapi). The vapi-expert is only used when explicitly asked to research how Vapi solves a particular problem.

## Step 1: Get Task Description

Check if task description was provided via `$ARGUMENTS`.

**If $ARGUMENTS is empty or just "/plan-task":**
- Ask the user: "What task would you like to plan? Please describe what needs to be implemented."
- Wait for response
- Use their description for the rest of the workflow

**If $ARGUMENTS contains description:**
- Use it directly
- Example: `/plan-task add expandable rows to call log table`

## Step 2: Generate Task Name

From the task description, auto-generate a kebab-case task name:

**Rules:**
- Lowercase only
- Replace spaces with hyphens
- Remove special characters except hyphens
- Keep it concise but descriptive
- Extract key action + subject

**Examples:**
- "add expandable rows to call log table" → `add-expandable-rows`
- "implement login page with cookie auth" → `implement-login-page`
- "create webhook endpoint for Vapi" → `create-vapi-webhook`
- "add pagination to call logs" → `add-pagination`

**Create task name variable** for use in folder creation.

## Step 3: Gather Relevant Expert Analysis (Selective)

**IMPORTANT**: Use judgment to determine which expert subagents are relevant for this specific task. Do NOT blindly consult all experts. Analyze the task type and consult only the experts that provide value.

### Decision Matrix

Analyze the task description and determine which experts to consult:

**UI/Component Tasks:**
- ✅ **MUST use**: `shadcn-expert`
- ✅ **Consider**: `react-expert` (for complex state or hooks)
- ❌ **Skip**: `turso-expert`, `nextjs-expert`

**Examples:**
- "Add expandable rows to call log table" → shadcn-expert + react-expert
- "Style the login form" → shadcn-expert only
- "Add loading spinner to table" → shadcn-expert only

**Data Table Tasks:**
- ✅ **MUST use**: `shadcn-expert` + `react-expert`
- ✅ **Consider**: `turso-expert` (if query changes needed)

**Examples:**
- "Add sorting to call log table" → shadcn-expert + react-expert
- "Add pagination with server-side fetching" → shadcn-expert + react-expert + turso-expert

**API Route Tasks:**
- ✅ **MUST use**: `nextjs-expert`
- ✅ **Consider**: `turso-expert` (for database operations)
- ✅ **Consider**: `pipecat-expert` (for Pipecat webhook handling)
- ❌ **Skip**: `shadcn-expert`, `react-expert`

**Examples:**
- "Create login API route" → nextjs-expert only
- "Add call log fetching endpoint" → nextjs-expert + turso-expert

**Database Tasks:**
- ✅ **MUST use**: `turso-expert`
- ✅ **Consider**: `nextjs-expert` (for API integration)
- ❌ **Skip**: UI experts unless displaying data

**Examples:**
- "Add index to call_logs table" → turso-expert only
- "Create new table for users" → turso-expert only
- "Optimize query for call log fetching" → turso-expert + nextjs-expert

**Authentication Tasks:**
- ✅ **MUST use**: `better-auth-expert` + `nextjs-expert`
- ✅ **Consider**: `shadcn-expert` (for login form UI)
- ✅ **Consider**: `turso-expert` (for schema/migrations)

**Examples:**
- "Setup Better Auth" → better-auth-expert + nextjs-expert + turso-expert
- "Create login page with form" → better-auth-expert + nextjs-expert + shadcn-expert
- "Add session verification" → better-auth-expert + nextjs-expert

**Pipecat Integration Tasks:**
- ✅ **MUST use**: `pipecat-expert`
- ✅ **Consider**: `gemini-live-expert` (for Gemini native audio configuration)
- ✅ **Consider**: `nextjs-expert` (for API routes to spawn bots)
- ✅ **Consider**: `turso-expert` (for storing conversation data)
- ❌ **Skip**: UI experts unless building client interfaces

**Examples:**
- "Set up Daily transport for WebRTC" → pipecat-expert only
- "Create custom processor for conversation" → pipecat-expert only
- "Build API to spawn Pipecat bots" → pipecat-expert + nextjs-expert
- "Update end-of-call report payload" → pipecat-expert + turso-expert
- "Configure Gemini Live for voice agent" → pipecat-expert + gemini-live-expert

**Gemini Live Configuration Tasks:**
- ✅ **MUST use**: `gemini-live-expert`
- ✅ **Consider**: `pipecat-expert` (for pipeline integration)
- ❌ **Skip**: UI experts, database experts

**Examples:**
- "Write native audio system prompt" → gemini-live-expert only
- "Configure voice settings and affective dialog" → gemini-live-expert only
- "Optimize Gemini model parameters" → gemini-live-expert only
- "Integrate Gemini Live with Pipecat" → gemini-live-expert + pipecat-expert

**Prompt Engineering Tasks:**
- **Cascade/text-based prompts**: `prompt-engineering-expert`
- **Native audio prompts**: `gemini-live-expert`
- **Tasks spanning both pipeline types**: Both experts
- ✅ **Consider**: `pipecat-expert` (for pipeline integration)

**Examples:**
- "Refactor system prompts for voice agent" → prompt-engineering-expert + gemini-live-expert (both types)
- "Add transcription error handling to prompt" → prompt-engineering-expert only
- "Write voice style section for Gemini" → gemini-live-expert only
- "Improve conversation stage design" → prompt-engineering-expert (if Cascade) or gemini-live-expert (if native audio)
- "Add quality gates to Cascade prompt" → prompt-engineering-expert only

**Twilio Telephony Tasks:**
- ✅ **MUST use**: `twilio-expert`
- ✅ **Consider**: `pipecat-expert` (for transport configuration)
- ✅ **Consider**: `nextjs-expert` (for webhook endpoints)
- ❌ **Skip**: UI experts unless building telephony UI

**Examples:**
- "Configure Twilio Media Streams" → twilio-expert only
- "Add Twilio webhook for call status" → twilio-expert + nextjs-expert
- "Set up TwilioFrameSerializer" → twilio-expert + pipecat-expert
- "Implement call recording via Twilio" → twilio-expert + pipecat-expert

**Full-Stack Features:**
- Route based on feature components
- Example: "Add call log dashboard" → shadcn-expert + react-expert + nextjs-expert + turso-expert

**Vapi Research Tasks (only when user explicitly requests):**
- ✅ **Use**: `vapi-expert` to research how Vapi solves a specific problem
- ⚠️ **Do NOT use proactively** - only when explicitly requested
- Examples: "How does Vapi structure end-of-call reports?", "What patterns does Vapi use for X?"

### Expert Prompt Templates

Based on your decision above, use the relevant prompt templates:

---

#### Template A: nextjs-expert

**Use for**: API routes, middleware, server components, Vercel deployment

**Prompt Template:**
```
Analyze this task from a Next.js App Router perspective:

**Task**: [Insert full task description]

Provide comprehensive analysis on:

1. **App Router Patterns**
   - Which Next.js patterns apply? (Route handlers, middleware, server components)
   - File structure recommendations
   - Server vs client component decisions

2. **API Route Design** (if applicable)
   - Route handler structure
   - Request/response handling
   - Error handling patterns

3. **Middleware Requirements** (if applicable)
   - Auth protection needs
   - Cookie handling
   - Redirect logic

4. **Implementation Guidance**
   - Which files need changes
   - Code structure recommendations
   - Environment variables needed

5. **Anti-Patterns to Avoid**
   - Common Next.js mistakes
   - Performance pitfalls

6. **References**
   - Relevant Next.js documentation URLs

**Return a structured analysis** with specific recommendations and code examples.
```

---

#### Template B: turso-expert

**Use for**: Schema design, queries, @libsql/client patterns

**Prompt Template:**
```
Analyze this task from a Turso/LibSQL database perspective:

**Task**: [Insert full task description]

Provide comprehensive analysis on:

1. **Schema Considerations**
   - Table structure needed
   - Column types and constraints
   - Indexes for query optimization

2. **Query Patterns**
   - SQL queries needed
   - Parameterized query examples
   - Pagination/filtering patterns

3. **@libsql/client Usage**
   - Client initialization
   - Query execution patterns
   - Transaction needs (if any)

4. **Type Safety**
   - TypeScript interfaces for rows
   - Type-safe query patterns

5. **Anti-Patterns to Avoid**
   - SQLite/Turso specific gotchas
   - Performance issues to watch

6. **References**
   - Relevant Turso documentation URLs

**Return a structured analysis** with specific SQL and TypeScript examples.
```

---

#### Template C: react-expert

**Use for**: Component architecture, hooks, state management

**Prompt Template:**
```
Analyze this task from a React component and hooks perspective:

**Task**: [Insert full task description]

Provide comprehensive analysis on:

1. **Component Architecture**
   - Component hierarchy needed
   - Props and composition patterns
   - Server vs client component decisions

2. **Hooks Strategy**
   - Which hooks to use (useState, useEffect, custom hooks)
   - State management approach
   - Data fetching patterns

3. **State Management**
   - Where state should live
   - Context needs (if any)
   - State update patterns

4. **Performance Considerations**
   - Memoization needs
   - Re-render optimization
   - Loading/error states

5. **Anti-Patterns to Avoid**
   - Common React mistakes
   - Hook rule violations

6. **References**
   - Relevant React documentation URLs

**Return a structured analysis** with specific component and hook examples.
```

---

#### Template D: shadcn-expert

**Use for**: Data Tables, UI components, Tailwind styling

**Prompt Template:**
```
Analyze this task from a Shadcn UI and TanStack Table perspective:

**Task**: [Insert full task description]

Provide comprehensive analysis on:

1. **Component Selection**
   - Which Shadcn components to use
   - Installation commands if new components needed
   - Component customization approach

2. **TanStack Table Configuration** (if table-related)
   - Column definitions
   - Features needed (sorting, filtering, pagination, expanding)
   - Row model configuration

3. **Styling Approach**
   - Tailwind classes to use
   - Theme integration
   - Responsive design considerations

4. **Accessibility**
   - Keyboard navigation
   - Screen reader support
   - ARIA attributes needed

5. **Anti-Patterns to Avoid**
   - Shadcn/TanStack common mistakes
   - Styling pitfalls

6. **References**
   - Relevant Shadcn/TanStack documentation URLs

**Return a structured analysis** with specific component configurations and code examples.
```

---

#### Template E: vapi-expert

**Use for**: Researching how Vapi solves a specific problem (ONLY when user explicitly asks)

**Note**: This project uses Pipecat, not Vapi. Only use this expert when the user specifically wants to understand Vapi's approach to something (e.g., "how does Vapi structure their end-of-call reports?").

**Prompt Template:**
```
Research how Vapi approaches this problem:

**Question**: [Insert user's specific question about Vapi]

First, search the local Vapi documentation in `_refs/vapi-docs/` for relevant information.
If needed, fetch the OpenAPI spec from https://api.vapi.ai/api for request/response schemas.

Provide analysis on:

1. **Vapi's Approach**
   - How Vapi solves this problem
   - Payload structures used
   - API patterns

2. **Key Design Decisions**
   - Why Vapi chose this approach
   - Trade-offs in their design

3. **Relevant Patterns**
   - Patterns that could be adapted for our Pipecat implementation
   - What to emulate vs what to avoid

4. **References**
   - Relevant Vapi documentation URLs
   - Local doc file paths in `_refs/vapi-docs/`

**Return analysis** focused on understanding Vapi's approach for reference purposes.
```

---

#### Template F: better-auth-expert

**Use for**: Authentication setup, session management, login/logout flows, Drizzle adapter configuration

**Prompt Template:**
```
Analyze this task from a Better Auth perspective:

**Task**: [Insert full task description]

First, check for any existing Better Auth configuration in the codebase (lib/auth.ts, lib/auth-client.ts).
Fetch Better Auth documentation if needed for latest patterns.

Provide comprehensive analysis on:

1. **Better Auth Configuration**
   - Server setup (lib/auth.ts)
   - Client setup (lib/auth-client.ts)
   - API route handler setup
   - Environment variables needed

2. **Database/Adapter Setup**
   - Drizzle adapter configuration for Turso
   - Schema generation steps
   - Migration commands

3. **Session Management**
   - Session verification in DAL
   - Cookie caching configuration
   - Expiration policies

4. **Authentication Flows**
   - Login implementation
   - Logout implementation
   - Session checking in components

5. **Security Considerations**
   - Defense in depth with DAL pattern
   - Why Better Auth is more secure than custom auth
   - Cookie security settings

6. **Anti-Patterns to Avoid**
   - Common Better Auth mistakes
   - Security vulnerabilities to watch for

7. **References**
   - Relevant Better Auth documentation URLs

**Return a structured analysis** with specific configuration and code examples.
```

---

#### Template G: pipecat-expert

**Use for**: Real-time voice agents, STT/LLM/TTS pipelines, transport layers, processor patterns

**Prompt Template:**
```
Analyze this task from a Pipecat framework perspective:

**Task**: [Insert full task description]

First, search these local references:
- `_refs/voice-demo-backend/` - Production Pipecat bot (Gemini Live native audio) for the landing page voice demo
- `_refs/pipecat/` - Pipecat framework source code
- `_refs/pipecat/examples/` - Reference implementations
- `_refs/voice-demo-example/` - Additional voice demo patterns
- `_refs/voice-ui-kit/` - React components for voice UI

If needed, fetch documentation from https://docs.pipecat.ai/ for detailed guides.

Provide comprehensive analysis on:

1. **Pipeline Architecture**
   - Processor chain structure
   - Data flow and frame handling
   - Service selection (STT, LLM, TTS)

2. **Service Configuration**
   - Which services to use
   - API configuration and credentials
   - Service-specific options

3. **Transport Setup**
   - Transport layer choice (Daily, WebSocket, etc.)
   - Client connection handling
   - Audio/video stream configuration

4. **Frame Handling**
   - Relevant frame types
   - Custom processor needs
   - Context aggregation

5. **Conversation Management**
   - Turn-taking and interruptions
   - VAD configuration
   - Context window management

6. **Anti-Patterns to Avoid**
   - Common Pipecat mistakes
   - Performance pitfalls

7. **References**
   - Relevant documentation URLs
   - Local file paths in `_refs/voice-demo-backend/` (production patterns)
   - Local example file paths in `_refs/pipecat/`

**Return a structured analysis** with specific pipeline configurations and code examples.
```

---

#### Template H: gemini-live-expert

**Use for**: Gemini Live native audio model configuration, system prompts, voice settings, affective dialog, proactive audio

**Prompt Template:**
```
Analyze this task from a Gemini Live native audio perspective:

**Task**: [Insert full task description]

First, search the production voice demo backend in `_refs/voice-demo-backend/` for:
- `bot/pipelines/gemini.py` - Gemini Live service configuration
- `bot/prompts/demo_system_prompt.md` - Native audio system prompt
- `bot/core/prompts.py` - Prompt loader patterns

Then research relevant Gemini Live documentation using WebFetch:
- https://ai.google.dev/gemini-api/docs/live - Live API overview
- https://ai.google.dev/gemini-api/docs/live-guide - Configuration guide
- https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/2-5-flash-live-api - Model details

Provide comprehensive analysis on:

1. **System Prompt Design**
   - Persona definition
   - Behavioral constraints
   - Voice style guidance (tone, pacing, expressivity)
   - Task instructions

2. **Configuration Options**
   - Response modalities
   - Voice selection (30 HD voices, 24 languages)
   - Affective dialog settings
   - Proactive audio settings
   - Thinking budget

3. **VAD Configuration**
   - Speech sensitivity settings
   - Silence duration
   - Barge-in behavior

4. **Session Management**
   - Duration limits (15min audio, 2min video)
   - Context window usage (128k tokens)
   - Compression and resumption

5. **Tool/Function Integration**
   - Function calling during voice dialog
   - Google Search grounding
   - Low-latency tool design

6. **Anti-Patterns to Avoid**
   - Pipeline-era prompting patterns (STT/TTS awareness)
   - Multi-stage flow design
   - Missing voice styling
   - Complex tool interfaces

7. **References**
   - Relevant Gemini Live documentation URLs
   - Local file paths in `_refs/voice-demo-backend/` (production Gemini Live implementation)

**Return a structured analysis** with specific configuration examples and prompt recommendations.
```

---

#### Template I: twilio-expert

**Use for**: Twilio Voice API, Media Streams, TwiML, telephony webhooks, call control

**Prompt Template:**
```
Analyze this task from a Twilio Programmable Voice perspective:

**Task**: [Insert full task description]

First, research relevant Twilio documentation using WebFetch:
- https://www.twilio.com/docs/voice - Voice overview
- https://www.twilio.com/docs/voice/media-streams - Media Streams guide
- https://www.twilio.com/docs/voice/twiml - TwiML reference

Then analyze the current Twilio integration in `scripts/pipecat/bot.py`.

Provide comprehensive analysis on:

1. **Twilio Feature Analysis**
   - How Twilio exposes this feature
   - API endpoints or TwiML verbs involved
   - Request/response formats
   - Webhook events available

2. **Media Streams Configuration** (if applicable)
   - WebSocket message types
   - Audio format requirements
   - TwilioFrameSerializer settings
   - Bidirectional streaming patterns

3. **TwiML Configuration** (if applicable)
   - Required TwiML verbs
   - `<Stream>` configuration options
   - Custom parameters

4. **Current Implementation Analysis**
   - How this is implemented in `scripts/pipecat/bot.py`
   - Integration with Pipecat Cloud
   - Existing patterns to follow

5. **Implementation Strategy**
   - Step-by-step guidance
   - Code locations to modify
   - Configuration changes needed

6. **Security Considerations**
   - Request validation
   - Credential management
   - Webhook security

7. **Anti-Patterns to Avoid**
   - Common Twilio mistakes
   - Performance pitfalls

8. **References**
   - Relevant Twilio documentation URLs

**Return a structured analysis** with specific Twilio configurations and code examples.
```

---

#### Template J: prompt-engineering-expert

**Use for**: Cascade pipeline prompts (STT→LLM→TTS), text-based LLM behavior, transcription handling, goal-oriented prompt design

**Prompt Template:**
```
Analyze this task from a Voice AI prompting perspective for Cascade pipelines:

**Task**: [Insert full task description]

First, consult the askjg voice AI prompting guide in `_refs/askjg-voice-ai-prompting-guide.md`.
Then review existing prompts in `scripts/pipecat/prompts/`.

Provide comprehensive analysis on:

1. **Pipeline Awareness**
   - STT→LLM→TTS flow implications
   - Transcription errors: same spoken word may transcribe differently each time
   - Prompts must avoid correction loops when phonetics match but spelling varies

2. **Prompt Structure**
   - Goal-oriented stage design
   - Success/failure criteria per stage
   - Quality gates (explicit "do not proceed until" checkpoints)
   - Variable management

3. **Conversation Design**
   - Stage transitions
   - Edge case handling (refusal, partial info, off-topic, transcription failures)
   - Command processing and resumption logic

4. **Output Constraints**
   - Conversational vs written style
   - Token budget considerations
   - Response format guidance

5. **Anti-Patterns to Avoid**
   - Rigid numbered condition syntax
   - Missing edge case handling
   - Implicit variable tracking
   - Pipeline-unaware instructions

6. **References**
   - Relevant documentation URLs

**Return a structured analysis** with specific prompt improvements and examples.
```

---

### Invocation

Based on your decision above, invoke the relevant expert(s) using the Task tool:

**Single Expert Example:**
```python
Task(
  subagent_type="shadcn-expert",
  prompt="[Use Template D with task description filled in]",
  description="Analyze UI patterns"
)
```

**Multiple Experts Example (invoke in parallel):**
```python
Task(
  subagent_type="shadcn-expert",
  prompt="[Use Template D with task description filled in]",
  description="Analyze UI patterns"
)

Task(
  subagent_type="react-expert",
  prompt="[Use Template C with task description filled in]",
  description="Analyze React patterns"
)
```

**Key Principle**: Only invoke experts that add value to the specific task at hand. Quality over quantity.

## Step 4: Create Task Documentation

Create the task folder structure: `_docs/tasks/<task-name>/`

Use Bash to create directory:
```bash
mkdir -p _docs/tasks/<task-name>
```

Then create all four documentation files:

### Create plan.md

```markdown
# Task: [Task Name]

## Goal
[Task description from user]

## Expert Analysis Summary

### [Expert 1 Name]
[Summary of key findings]

### [Expert 2 Name] (if consulted)
[Summary of key findings]

## Recommended Approach

[Implementation strategy synthesized from expert analyses]

## Implementation Steps

Based on expert analysis:

- [ ] Step 1: [Specific implementation step]
- [ ] Step 2: [Specific implementation step]
- [ ] Step 3: [Specific implementation step]
[Add more steps as identified]

## Files to Modify

[List from expert analyses with descriptions]

- `path/to/file.ts` - [Expected changes]
- `path/to/file.tsx` - [Expected changes]

## Database Changes (if applicable)

[Schema changes, migrations, queries]

## API Changes (if applicable)

[New routes, modified endpoints]

## UI Changes (if applicable)

[New components, styling updates]

## Anti-Patterns to Avoid

[From expert analyses]

- ❌ [Anti-pattern 1]: [Why to avoid]
- ❌ [Anti-pattern 2]: [Why to avoid]

## Acceptance Criteria

- [ ] Feature works as described
- [ ] No TypeScript errors
- [ ] Responsive design (if UI)
- [ ] Error states handled
- [ ] Loading states implemented

## References

[Documentation links from expert analyses]
```

### Create status.md

```markdown
# Status: [Task Name]

**Current Phase**: Planning

**Last Updated**: [Current timestamp]

## Progress Summary

Task planning initiated via `/plan-task` command.

Expert analysis gathered:
[List only the experts that were actually consulted]
- ✅ **[expert-name]** - [Brief summary]

## Current Status

Reviewing expert analysis and preparing implementation.

## Next Steps

- [ ] Review `plan.md` and refine approach
- [ ] Begin implementation
- [ ] Test changes

## Blockers

None currently.
```

### Create decisions.md

```markdown
# Decisions: [Task Name]

## Decision Log

### [Date] - Initial Planning Approach

**Context**: Task planning initiated.

**Expert Recommendations**:
[Summary from expert analyses]

**Decision**: [Recommended approach]

**Rationale**:
- [Reason 1]
- [Reason 2]

---

## Pending Decisions

[List decisions that need to be made during implementation]

1. **[Decision topic 1]**
   - Options: [A, B, C]
   - Considerations: [Tradeoffs]
   - Status: Pending
```

### Create implementation-notes.md

```markdown
# Implementation Notes: [Task Name]

## Planning Phase

### [Expert 1] Analysis (Full)

[Complete analysis from expert]

---

### [Expert 2] Analysis (Full) (if consulted)

[Complete analysis from expert]

---

## Technical Considerations

[Key technical points from analyses]

## Code Patterns to Use

[Specific patterns identified by experts]

## Resources

[Documentation links and references]
```

## Step 5: Provide Summary to User

After creating all documentation:

```markdown
## ✅ Task Planning Complete: [task-name]

**Created**: `_docs/tasks/[task-name]/`

**Expert Analysis Gathered**:
[List experts consulted with brief summaries]
- ✅ **[expert-name]**: [1-2 sentence summary]

**Recommended Approach**:
[2-4 sentences summarizing the implementation strategy]

**Files to Modify**:
- [File list with expected changes]

**Next Steps**:
1. Review `_docs/tasks/[task-name]/plan.md`
2. Begin implementation
3. Update status.md as you progress

Ready to start implementation!
```

## Important Notes

- **Auto-generate task names** - Use kebab-case from description
- **Be selective with experts** - Only consult relevant experts
- **Preserve full analysis** - Include complete expert feedback in implementation-notes.md
- **Keep plans actionable** - Extract concrete steps and file names

## Edge Cases

### EC-1: Task Folder Already Exists
- Check if `_docs/tasks/<task-name>/` exists
- If yes: Warn user and ask if they want to overwrite or use a different name

### EC-2: Vague Task Description
- If description is too vague for meaningful analysis
- Ask user for more details before running experts
- Example: "add feature" → Ask: "What feature specifically?"

### EC-3: Task Name Collision
- If auto-generated name conflicts with existing task
- Append `-2`, `-3`, etc. to make unique

## Command Success Criteria

The command succeeds when:
- ✅ Task folder created at `_docs/tasks/<task-name>/`
- ✅ All 4 documentation files present and populated
- ✅ Relevant expert analysis included
- ✅ Implementation steps specific and actionable
- ✅ User has clear next steps
