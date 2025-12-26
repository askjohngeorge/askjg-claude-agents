---
description: Create a new subagent definition for specialized tasks with isolated context
allowed-tools: Task, Read, Write, Bash, WebFetch, Glob
---

# Create New Subagent: $ARGUMENTS

Ultrathink.

## Objective
Create a specialized subagent definition based on user requirements. Subagents are powerful tools for delegating specific tasks to AI assistants with fresh context windows, keeping the main conversation clean while enabling focused, expert-level work.

## Step 1: Understand Subagent Requirements

First, let me understand what you want this subagent to accomplish:

### Requirement Analysis
Analyze the user's request: **$ARGUMENTS**

Key questions to understand:
- What is the primary purpose of this subagent?
- What specialized expertise or role should it have?
- Should it be invoked automatically (proactive) or manually?
- What tools will it need access to?
- Should it be read-only or able to modify files?
- What kind of output should it produce?
- Are there any specific methodologies or guidelines it should follow?

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
- Do NOT make assumptions about critical design decisions (purpose, expertise, tools, invocation style)
- Wait for user response before proceeding to research

**Clarity Examples:**

*Clear requirement*: "Create a security auditor that scans Python code for SQL injection, XSS, and hardcoded credentials"
→ Purpose, expertise area, and scope are all specified

*Unclear requirement*: "Make a security subagent"
→ Ask: What kind of security? Code auditing? Infrastructure? Compliance? What should it check for? Should it be proactive or manual?

**Only proceed to Step 2 once you have confidence in understanding the user's needs.**

## Step 2: Research Subagent Patterns

### Subagent 1: Documentation Study
<Task description="Study subagent documentation">
Read and understand the Claude Code subagent documentation.

Tasks:
1. Use WebFetch to read https://docs.claude.com/en/docs/claude-code/sub-agents
2. Extract key information about:
   - Subagent file format and YAML frontmatter structure
   - Available configuration options (name, description, tools, model)
   - Best practices for effective subagent design
   - How to make subagents proactive vs manual
   - Model selection guidelines
   - Tool restriction strategies

Return:
- Complete understanding of subagent structure
- YAML frontmatter requirements and syntax
- Best practices for subagent design
- Guidelines for tool and model selection
- Patterns for proactive invocation
</Task>

### Subagent 2: Existing Subagents Analysis
<Task description="Analyze existing subagents">
Study existing subagents in the project to understand patterns and best practices.

Tasks:
1. Use Bash to check if .claude/agents/ directory exists
2. If it exists, list all subagent files: ls .claude/agents/
3. Read 2-3 existing subagents to understand:
   - Common YAML frontmatter patterns
   - System prompt structure and style
   - Tool selection strategies
   - How they define their expertise and methodology
   - Output format specifications
4. If no subagents exist, note this and rely on documentation patterns

Return:
- List of existing subagents (if any)
- Common patterns and structure
- Effective techniques to adopt
- Recommendations for this subagent
</Task>

## Step 3: Design the Subagent

Based on the user's goals and research above, design the new subagent:

### Subagent Design Planning

1. **Name**:
   - Lowercase with hyphens (e.g., `code-reviewer`, `security-auditor`)
   - Descriptive and clear
   - Check for conflicts with existing subagents

2. **Description**:
   - Clear statement of purpose and expertise
   - Include "Use PROACTIVELY" if it should invoke automatically
   - Specify when/why to use this subagent

3. **Tools**:
   - Determine minimum necessary tools
   - Read-only: `Read, Grep, Glob` (for analysis/review tasks)
   - Write-enabled: Add `Edit, Write` (for modification tasks)
   - Shell access: Add `Bash` if needed
   - External data: Add `WebFetch` if needed
   - Consider: Does it need Task tool for sub-delegation?

4. **Model Selection**:
   - `'inherit'` - Match main conversation's model (recommended for consistency)
   - `sonnet` - Balanced performance (default)
   - `opus` - Maximum capability for complex reasoning
   - `haiku` - Fast, cost-effective for simple tasks

5. **Thinking Budget**: Determine appropriate level based on subagent complexity:
   - Simple lookup/review tasks → Think.
   - Basic analysis or validation → Think hard.
   - Multi-step analysis workflows → Think harder.
   - Complex reasoning, deep analysis, or creative solutions → Ultrathink.

6. **System Prompt**:
   - Define the subagent's role and expertise
   - Specify methodology and approach
   - Include guidelines and quality standards
   - Define output format
   - Be specific and detailed

### Design Template
Structure the design as:
```
Name: [subagent-name]
Description: [Purpose and invocation guidance]
Tools: [Comma-separated list]
Model: [inherit/sonnet/opus/haiku]
Thinking Budget: [Think./Think hard./Think harder./Ultrathink.]

System Prompt Structure:
- Role definition
- Responsibilities
- Methodology
- Output format
- Quality standards
```

## Step 4: Write the Subagent File

Create the new subagent file with proper structure:

### Subagent File Format
```markdown
---
name: [subagent-name]
description: [Clear purpose and when to use]
tools: [Comma-separated tool list]
model: [model selection]
---

# [Subagent Title]

[Thinking Budget - one of: Think. | Think hard. | Think harder. | Ultrathink.]

[Opening statement of role and purpose]

## Your Responsibilities:
1. [Primary responsibility]
2. [Secondary responsibility]
3. [Additional responsibilities...]

## Methodology:
- [Approach step 1]
- [Approach step 2]
- [Tools to use and how]
- [Quality standards]

## Output Format:
[Specify exact format for results]
- What to include
- How to structure findings
- Level of detail expected

[Any additional sections for guidelines, examples, or constraints]
```

### Thinking Budget Guidelines
The thinking budget should be specified on its own line after the title (typically line 8). Options are:
- **Think.** - For simple, straightforward tasks with clear steps
  - Examples: File syntax checks, simple pattern matching, basic validation
- **Think hard.** - For tasks requiring some analysis or planning
  - Examples: Code review for specific issues, configuration validation, basic refactoring suggestions
- **Think harder.** - For complex tasks with multiple considerations
  - Examples: Architecture review, security audits, performance optimization analysis
- **Ultrathink.** - For highly complex tasks requiring deep analysis, multiple subagents, or creative problem-solving (DEFAULT)
  - Examples: Multi-faceted code review, complex system design evaluation, comprehensive analysis

If not specified, default to **Ultrathink.** to ensure thorough execution.

### File Creation Steps
1. Ensure `.claude/agents/` directory exists (create if needed)
2. Write the subagent file to `.claude/agents/[name].md`
3. Verify the file was created successfully

## Step 5: Provide Usage Guidance

After creating the subagent, provide the user with:

### Usage Instructions

**Subagent Created**: `[name]`
**Location**: `.claude/agents/[name].md`

**How to Use:**

1. **Automatic Invocation** (if proactive):
   - The subagent will automatically activate when relevant tasks arise
   - Claude will delegate appropriate work based on the description

2. **Manual Invocation**:
   - Ask Claude to use the subagent by name: "Use the [name] subagent to..."
   - Or reference it in your request: "Have [name] review this code"

3. **What It Does**:
   - [Summary of subagent purpose]
   - [What kind of output to expect]
   - [When it's most useful]

**Testing the Subagent:**
- Suggest a test case relevant to the subagent's purpose
- Example: "Try asking: 'Review this file for security issues: [file-path]'"

**Customization:**
- The file can be edited to refine behavior
- Update tools, model, or system prompt as needed
- Changes take effect immediately

## Important Notes

- **Context Separation**: Subagents work in isolated context windows - they start fresh each time
- **Tool Restrictions**: Only grant tools the subagent actually needs
- **Proactive vs Manual**: Include "PROACTIVELY" in description for automatic invocation
- **Model Choice**: Use `'inherit'` to match your conversation's model
- **Version Control**: Add `.claude/agents/` to git to share with team
- **Naming**: Use lowercase-with-hyphens format (e.g., `my-subagent`)

**Begin by analyzing the user's requirements for their new subagent.**
