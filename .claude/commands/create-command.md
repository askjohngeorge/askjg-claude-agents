---
description: Create a new Claude command by analyzing requirements, documentation, and existing examples
allowed-tools: Task, Read, WebFetch, Write, LS, Glob
---

# Create New Command: $ARGUMENTS

Ultrathink.

## Step 1: Understand User Goals

First, let me understand what you want this command to accomplish:

### Goal Analysis
Analyze the user's request: **$ARGUMENTS**

Key questions to understand:
- What is the primary purpose of this command?
- What tools will it need to use?
- Is it read-only or does it modify files?
- What kind of workflow should it follow?
- What output should it produce?
- Are there any specific constraints or requirements?
- What level of thinking is appropriate? (simple task vs complex multi-step workflow)

### Requirements Clarity Check

**IMPORTANT**: Before proceeding to research and implementation:

1. **Analyze $ARGUMENTS**: What information did the user provide?
2. **Assess clarity**: Do you have clear, confident answers to the key questions above?
3. **Decision point**:
   - ✅ **Requirements are clear** → Proceed to Step 2
   - ❌ **Requirements are vague or ambiguous** → STOP and ask clarifying questions

**If requirements are unclear:**
- List the specific aspects that need clarification
- Ask targeted questions to fill gaps
- Do NOT make assumptions about critical design decisions (purpose, tools, workflow)
- Wait for user response before proceeding to research

**Clarity Examples:**

*Clear requirement*: "Create a command that runs pytest with coverage, generates HTML report, and opens it in browser"
→ Purpose, tools, and workflow are all specified

*Unclear requirement*: "Make a test command"
→ Ask: What kind of tests? What framework? What should the output be? Should it run automatically or need arguments?

**Only proceed to Step 2 once you have confidence in understanding the user's needs.**

## Step 2: Research Command Creation

### Subagent 1: Documentation Study
<Task description="Study slash command documentation">
Read the official Claude Code documentation on creating slash commands.

Tasks:
1. Run `date +%Y-%m-%d` to get the current date for ensuring documentation currency
2. Use WebFetch to read https://docs.anthropic.com/en/docs/claude-code/slash-commands
   - Be aware that Claude Code documentation is regularly updated
   - Note any version information or last-updated dates on the page
3. Extract key information about:
   - File format and frontmatter structure
   - How to specify allowed-tools
   - Variable usage (like $ARGUMENTS)
   - Shell command execution syntax with `command`
   - Best practices and guidelines
   - Any limitations or special considerations
   - Any new features or recent changes to the command system

Return:
- Current date used for documentation context
- Complete understanding of command structure
- List of available variables
- Syntax requirements
- Best practices for command design
- Any temporal notes about feature availability or recent changes
</Task>

### Subagent 2: Example Analysis
<Task description="Analyze existing commands">
Study existing commands in .claude/commands/ to understand patterns and best practices.

Tasks:
1. Use LS to list all commands in .claude/commands/
2. Read at least 3-4 different commands to understand:
   - Common patterns and structures
   - Thinking budget usage (Think, Think hard, Think harder, Ultrathink)
   - How they use tools effectively
   - How they handle user arguments
   - Workflow organization
   - Output formatting
   - Error handling approaches

Focus on commands that might be similar to what the user wants.
Pay special attention to the thinking budget line (typically line 8) and when different levels are used.

Return:
- Common patterns observed
- Thinking budget patterns (which commands use which level and why)
- Effective techniques to adopt
- Structure recommendations
- Tool usage patterns
</Task>

## Step 3: Design the Command

Based on the user's goals and the research above, design the new command:

### Command Structure Planning
1. **Purpose**: [Clear statement of what the command does]
2. **Thinking Budget**: Determine appropriate level based on complexity:
   - Simple lookup/read tasks → Think.
   - Basic modifications or searches → Think hard.
   - Multi-step workflows → Think harder.
   - Complex analysis, multiple subagents, or creative solutions → Ultrathink.
3. **Workflow**: [Step-by-step process the command will follow]
4. **Tools needed**: [List of tools and why each is needed]
5. **Output format**: [What the user will see]
6. **Edge cases**: [How to handle potential issues]

## Step 4: Write the Command

Create the new command file with:
- Proper frontmatter (description, allowed-tools)
- Thinking budget specification (after title, before objective)
- Clear workflow steps
- Effective use of $ARGUMENTS variable
- Any needed shell commands with `syntax`
- Proper subagent tasks if complex work is needed
- Clear instructions for Claude to follow

### Command Template Structure
```markdown
---
description: [Concise description of what the command does]
allowed-tools: [Comma-separated list of tools needed]
---

# [Command Title]: $ARGUMENTS

[Thinking Budget - one of: Think. | Think hard. | Think harder. | Ultrathink.]

## Objective
[Clear statement of the command's purpose and what it will accomplish]

## Step 1: [First major step]
[Instructions for this step]

## Step 2: [Second major step]
[Instructions for this step]

[Additional steps as needed]

## Output
[Description of what will be provided to the user]
```

### Thinking Budget Guidelines
The thinking budget should be specified on its own line after the title (typically line 8). Options are:
- **Think.** - For simple, straightforward tasks with clear steps
- **Think hard.** - For tasks requiring some analysis or planning
- **Think harder.** - For complex tasks with multiple considerations
- **Ultrathink.** - For highly complex tasks requiring deep analysis, multiple subagents, or creative problem-solving (DEFAULT)

If not specified, default to **Ultrathink.** to ensure thorough execution.

## Step 5: Save and Test

1. Save the command to `.claude/commands/[command-name].md`
2. Provide usage instructions to the user
3. Suggest a test case to verify it works correctly

## Final Notes
- Command names should be kebab-case (e.g., `create-command.md`)
- Descriptions should be concise but clear
- Only include tools that are actually needed
- Consider if the command should be read-only or allow modifications
- Think about reusability and flexibility

**Begin by analyzing the user's requirements for their new command.**