# askjg-claude-agents

Claude Code agents and commands used to build version 2 of [askjg.com](https://askjg.com) — John George's personal business landing page for voice AI consulting and development services.

The site went live on December 24, 2025.

## What's Included

### `.claude/agents/` — Portable Expert Subagents

These are generalized Claude Code subagents that provide specialized expertise. They can be used across different projects:

| Agent | Purpose |
|-------|---------|
| `better-auth-expert` | Authentication setup, session management, Drizzle adapters |
| `gemini-live-expert` | Gemini Live native audio configuration and prompting |
| `nextjs-expert` | Next.js App Router, API routes, middleware, server components |
| `pipecat-expert` | Real-time voice AI pipelines, STT/LLM/TTS integration |
| `prompt-engineering-expert` | Voice AI prompting and system prompt design |
| `react-expert` | Hooks, state management, component architecture |
| `shadcn-expert` | Shadcn UI, TanStack Table, Tailwind styling |
| `turso-expert` | Turso/LibSQL database, schema design, queries |
| `twilio-expert` | Twilio Programmable Voice, Media Streams, TwiML |
| `vapi-expert` | Vapi webhook integration and API patterns |

### `.claude/commands/` — Workflow Templates

These are Claude Code slash commands that define reusable workflows:

| Command | Purpose |
|---------|---------|
| `/bootstrap` | Interactive project setup and customization (see below) |
| `/create-command` | Create new Claude Code commands |
| `/create-subagent` | Create new specialized subagents |
| `/frontend-rca` | Root cause analysis for frontend issues with Puppeteer |
| `/pipecat-rca` | Root cause analysis for Pipecat bot issues |
| `/plan-task` | Create task planning docs with expert analysis |

## Installation

Copy the `.claude/` directory into your project root:

```bash
cp -r .claude/ /path/to/your/project/
```

Or copy just the agents and commands:

```bash
cp -r .claude/agents/ /path/to/your/project/.claude/
cp -r .claude/commands/ /path/to/your/project/.claude/
```

## Customizing for Your Project: The Bootstrap Process

After installing, run `/bootstrap` to customize the setup for your specific project. This is an interactive command that works as a collaborative session with you to tailor the Claude Code environment.

**The bootstrap process will:**

1. **Gather project context** — Ask questions about your project's purpose, key capabilities, and typical use cases

2. **Analyze existing code** — Review your codebase to understand patterns and architecture

3. **Define development workflow** — Establish which subagents to use, when to invoke them (proactive vs manual), and how they should coordinate

4. **Create documentation structure** — Set up a `docs/` folder with architecture, development, and integration guides

5. **Create/update CLAUDE.md** — Generate project-specific guidance including commands, workflow, git conventions, and best practices

6. **Define functional requirements** — Document core functionality, optional features, and success criteria

7. **Establish git workflow** — Set up commit message format, branching strategy, and review expectations

By the end of bootstrap, your project will have a fully customized Claude Code development workflow tailored to your specific needs.

## License

MIT
