---
description: Bootstrap initial development workflow and project documentation in consultation with senior developer
allowed-tools: Task, Read, Write, Edit, Bash, Glob, Grep
---

# Bootstrap Project: $ARGUMENTS

Ultrathink.

## Objective

Establish the foundation for this Pipecat starter boilerplate project by working interactively with the senior developer to:
1. Define the project's functional requirements and goals
2. Establish development workflow and when to use different subagents
3. Create comprehensive project documentation in `docs/`
4. Update `CLAUDE.md` with project-specific guidance

This command creates a solid foundation for development by ensuring alignment between the project vision and Claude Code's workflow.

## Step 1: Project Context Gathering

First, let's understand the current project state and goals:

**Questions for Senior Developer**:

1. **Project Purpose**:
   - What is the primary purpose of this Pipecat starter boilerplate?
   - What types of projects should developers be able to quickly start with this?

2. **Key Capabilities**:
   - What are the must-have features/patterns this boilerplate should include?
   - What pipecat/pipecat-flows features are most important?
   - What transport types should be supported (Daily, Twilio, etc.)?

3. **Typical Use Cases**:
   - What are the 2-3 most common scenarios this boilerplate will enable?
   - Are there specific industries or application types in mind?

4. **Configuration Philosophy**:
   - How configurable vs. opinionated should the boilerplate be?
   - What should be environment variables vs. code configuration?

5. **Extension Points**:
   - Where should developers most commonly need to extend/customize?
   - What patterns should we document for common extensions?

**Wait for senior developer responses before proceeding to Step 2.**

## Step 2: Analyze Existing Code

Based on the senior developer's input, analyze the `_examples/outbound-example/` folder to understand:

1. **Core Infrastructure** (to keep):
   - **Bot Architecture**: `bots/base_bot.py` and `bots/flow_bot.py`
     - BaseBot: Transport setup, service initialization, pipeline composition
     - FlowBot: FlowManager integration, flow state management
   - **Runner Pattern**: `runner.py`
     - Unified bot execution for different transports
     - Lifecycle management and error handling
   - **Transport Layer**: `transports/`
     - Daily.co integration patterns
     - Twilio WebSocket handling
     - Warm transfer capabilities
   - **Configuration**: `config/config.py`
     - Environment-based configuration
     - API key management
     - Service settings

2. **Client-Specific Logic** (to strip/genericize):
   - **Service Integrations**: `services/`
     - Supabase integration (client-specific database)
     - CalCom integration (client-specific booking)
     - Post-call analysis (client-specific)
   - **Business Flows**: `flows/`
     - Voicemail detection (keep as example)
     - Client-specific prompts and handlers (genericize)
     - Domain-specific transitions (simplify to examples)
   - **Deployment Scripts**:
     - `deploy.sh`, `server_setup.sh` (keep as templates)
     - `cloud_init.yaml` (keep as example)

3. **Valuable Patterns** (to document):
   - **Flow Organization**: nodes.py, handlers.py, transitions.py, prompts.py separation
   - **Error Handling**: Try/catch patterns in transports and bots
   - **Configuration Management**: Environment variable patterns
   - **Testing Strategies**: `testing/` folder structure
   - **Bot Lifecycle**: First participant handling, cleanup patterns
   - **State Management**: Custom parameters and flow state

**Analysis Steps**:
1. Use Glob to list all Python files in `_examples/outbound-example/`
2. Read key files: `bots/base_bot.py`, `bots/flow_bot.py`, `runner.py`, `config/config.py`
3. Scan `flows/` to understand organization pattern
4. Review `transports/` for integration patterns
5. Check `services/` to identify what needs stripping

**Create a summary document** (`docs/analysis-outbound-example.md`) with:
- Components to copy directly
- Components to genericize
- Components to document as examples only
- Patterns to extract and document

**Present findings to senior developer before proceeding.**

## Step 3: Define Development Workflow

Work with senior developer to establish:

1. **Subagent Strategy**:
   - **pipecat-expert**: Already created, set to PROACTIVE
     - Automatically invoked when working with pipecat/pipecat-flows code
     - Reviews implementations, suggests optimizations
     - Maintains `docs/pipecat/` documentation
   - **Additional subagents to consider**:
     - `flow-designer`: Helps design and validate pipecat-flows architecture
     - `config-validator`: Ensures configuration is complete and correct
     - `deployment-helper`: Assists with deployment and infrastructure
     - `testing-assistant`: Helps write and organize tests
   - **Coordination patterns**:
     - Which subagents should be PROACTIVE vs MANUAL?
     - How should they reference each other's documentation?
     - What's the handoff pattern between subagents?

2. **Development Phases**:
   - **Feature Development Cycle**:
     1. Design: Consult pipecat-expert for architecture guidance
     2. Implementation: Write code, pipecat-expert reviews automatically
     3. Documentation: Update relevant docs in `docs/`
     4. Testing: Write/run tests
     5. Review: Final review before commit
   - **Documentation Updates**:
     - Update `docs/pipecat/` when discovering new patterns
     - Update architecture docs when adding components
     - Keep examples folder with working code samples
   - **Testing Expectations**:
     - What level of test coverage?
     - Integration tests for transports?
     - Flow simulation tests?

3. **Code Quality Standards**:
   - **Pipecat Patterns**:
     - Must follow pipecat best practices (verified by pipecat-expert)
     - Async/await patterns correctly implemented
     - Proper error handling and cleanup
   - **Code Organization**:
     - Follow established folder structure
     - Clear separation of concerns (flows, services, config, etc.)
     - Well-documented with docstrings
   - **Anti-patterns to Avoid**:
     - Blocking operations in async code
     - Hardcoded configuration values
     - Overly coupled components
     - Undocumented complex logic

4. **Git Workflow**:
   - **Commit Strategy**:
     - NEVER commit without explicit user approval
     - Wait for user to say "commit" or "yes, commit"
     - Conventional commits format: `type(scope): description`
   - **Commit Message Format**:
     - Types: feat, fix, docs, refactor, test, chore
     - Scopes: pipecat, flows, config, transports, docs, etc.
     - Example: `feat(flows): Add example voicemail detection node`
   - **Branch Naming**:
     - Format: `{initials}/{type}/{description}`
     - Examples: `jg/feat/add-twilio-transport`, `jg/docs/pipecat-patterns`

## Step 4: Create Documentation Structure

Based on the gathered information, create comprehensive documentation:

### Create `docs/` folder structure:

```bash
mkdir -p docs/{pipecat,architecture,development,integrations}
```

### Create core documentation files:

1. **`docs/README.md`**: Documentation overview and navigation
2. **`docs/architecture/overview.md`**: System architecture and design
3. **`docs/architecture/bot-lifecycle.md`**: Bot lifecycle and orchestration
4. **`docs/architecture/configuration.md`**: Configuration system and patterns
5. **`docs/development/workflow.md`**: Development workflow and process
6. **`docs/development/subagent-usage.md`**: When and how to use subagents
7. **`docs/development/testing.md`**: Testing approach and guidelines
8. **`docs/pipecat/README.md`**: Pipecat-specific documentation (managed by pipecat-expert)
9. **`docs/integrations/README.md`**: Integration guides and patterns

### Documentation Content

Each file should be populated with:
- Information gathered from senior developer
- Analysis of outbound-example
- Patterns and best practices
- Clear examples and guidelines

## Step 5: Create/Update CLAUDE.md

Create a comprehensive `CLAUDE.md` file with:

### Sections to Include:

1. **Project Overview**:
   - Purpose and goals (from Step 1)
   - Technical stack
   - Key capabilities

2. **Development Commands**:
   - Setup instructions
   - Running locally
   - Testing commands
   - Common development tasks

3. **Subagent Coordination**:
   - Available subagents and their roles
   - When each subagent should be invoked (PROACTIVE vs MANUAL)
   - How subagents work together
   - Trust and delegation patterns

4. **Documentation Organization**:
   - Overview of `docs/` structure
   - Which subagent manages which docs folder
   - How to find information

5. **Development Workflow**:
   - Feature development process
   - Code review expectations
   - Testing requirements
   - Documentation updates

6. **Git Workflow**:
   - Branching strategy
   - Commit message format
   - When to create commits
   - PR process

7. **Architecture**:
   - Project structure
   - Key components
   - Data flow
   - Extension points

8. **Pipecat Best Practices**:
   - Key patterns to follow
   - Common pitfalls
   - When to consult pipecat-expert
   - Reference to docs/pipecat/

## Step 6: Define Functional Requirements

Create `docs/requirements.md` documenting:

1. **Core Functionality**:
   - What the boilerplate must support
   - Required features and capabilities
   - Must-have integrations

2. **Optional Functionality**:
   - Nice-to-have features
   - Optional integrations
   - Extension examples

3. **Non-Functional Requirements**:
   - Performance expectations
   - Scalability considerations
   - Maintainability goals
   - Documentation standards

4. **Success Criteria**:
   - What defines a successful use of this boilerplate?
   - Time-to-first-bot metrics
   - Ease of customization
   - Quality of generated code

## Step 7: Summarize and Confirm

Provide the senior developer with:

1. **Summary of Created Documentation**:
   - List all created files
   - Brief description of each
   - Key decisions documented

2. **Next Steps Recommendation**:
   - Suggest immediate next actions
   - Identify any gaps or open questions
   - Propose additional subagents/commands needed

3. **Validation Questions**:
   - Does the documentation capture the vision?
   - Is the workflow clearly defined?
   - Are there any missing pieces?
   - Should any decisions be reconsidered?

## Output Format

At the end of the bootstrap process, provide:

```markdown
## ✅ Bootstrap Complete

**Documentation Created**:
- ✅ `docs/` structure with [X] files
- ✅ `CLAUDE.md` with comprehensive guidance
- ✅ Functional requirements documented
- ✅ Development workflow defined

**Key Decisions**:
- [Decision 1]
- [Decision 2]
- [Decision 3]

**Subagent Strategy**:
- pipecat-expert: [PROACTIVE/MANUAL] - [when to use]
- [Other subagents]: [usage patterns]

**Next Steps**:
1. [Immediate next action]
2. [Follow-up action]
3. [Future consideration]

**Ready for Development**: The project foundation is established and documented.
```

## Important Notes

- **Interactive Process**: This command is designed to be highly interactive. Wait for senior developer input at each key decision point.
- **Iterative**: Be prepared to refine documentation based on feedback.
- **Comprehensive**: Err on the side of more documentation rather than less.
- **Practical**: Include concrete examples and clear guidelines.
- **Maintainable**: Structure documentation so it's easy to update as the project evolves.

## Edge Cases

### EC-1: Documentation Already Exists
- If `docs/` or `CLAUDE.md` exist, ask senior developer if they want to:
  - Overwrite completely
  - Merge with existing
  - Cancel and preserve existing

### EC-2: Unclear Requirements
- If senior developer's responses are vague, ask specific follow-up questions
- Provide examples or options to help clarify
- Don't proceed with assumptions

### EC-3: Conflicting Patterns
- If outbound-example patterns conflict with stated goals, highlight the conflict
- Ask senior developer which approach to prioritize
- Document the decision and rationale

**Begin by asking the senior developer the initial context-gathering questions.**
