# Agents — A Practical Guide

> Based on [Anthropic's Claude Code documentation](https://code.claude.com/docs/en/sub-agents) and the [Anthropic Skills course](https://anthropic.skilljar.com/introduction-to-agent-skills).

---

## What Are Agents?

Agents (also called **sub-agents**) are specialized, autonomous AI personas that Claude can delegate work to. Think of them as **virtual team members** — each with a distinct expertise, toolset, and perspective.

When Claude encounters a task that matches an agent's specialty, it can spawn that agent to handle the work independently, then incorporate the results back into the main conversation.

## Why Use Agents?

| Without Agents | With Agents |
|---------------|-------------|
| One generalist prompt tries to do everything | Specialists handle what they're best at |
| Context pollution — security concerns mixed with architecture | Clean separation of concerns |
| Inconsistent review quality | Repeatable, structured analysis |
| Manual invocation of complex workflows | Automatic delegation based on context |

## Agent File Format

Every agent lives in a `.md` file inside the `agents/` directory. The format is **YAML frontmatter + Markdown body**:

```markdown
---
name: code-reviewer              # Identifier (kebab-case)
description: >                   # When Claude should invoke this agent
  Reviews code for quality and best practices.
  Use when code has been written or modified.
tools: ["Read", "Grep", "Glob"]  # Tools the agent can access
model: sonnet                    # Which model powers it (sonnet, opus, haiku)
---

You are a senior code reviewer. When invoked, analyze code and provide
specific, actionable feedback on quality, security, and best practices.

## Your Process
1. Read the changed files
2. Analyze for quality issues
3. Check security implications
4. Provide structured feedback
```

### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | ✅ | Unique identifier, used for invocation |
| `description` | ✅ | Tells Claude **when** to use this agent — include examples |
| `tools` | ❌ | List of tools available (Read, Write, Bash, Grep, Glob, etc.) |
| `model` | ❌ | Model override: `sonnet` (fast), `opus` (deep), `haiku` (lightweight) |

### The System Prompt (Body)

The Markdown body after the frontmatter is the agent's **system prompt**. This defines:
- **Role**: Who the agent is ("You are a senior architect...")
- **Process**: Step-by-step workflow to follow
- **Output format**: How to structure the response
- **Guardrails**: What to avoid or prioritize

## Best Practices for Writing Agents

### 1. Be Specific About When to Invoke

> [!IMPORTANT]
> The `description` field is how Claude decides whether to use an agent. Vague descriptions = missed invocations.

```yaml
# ❌ Bad — too vague
description: Helps with code

# ✅ Good — clear triggers with examples
description: >
  Reviews code for quality and best practices.
  Use PROACTIVELY when code has been written, modified, or when the user
  asks for a review. Examples: "review my changes", "check this code",
  or after implementing a feature.
```

### 2. Restrict Tools to What's Needed

Agents should only have access to tools they actually need. A code reviewer doesn't need `Write` — it should only read and analyze.

```yaml
# Reviewer: read-only access
tools: ["Read", "Grep", "Glob"]

# Implementer: full access
tools: ["Read", "Write", "Bash", "Grep", "Glob"]
```

### 3. Choose the Right Model

| Model | Best For | Trade-off |
|-------|----------|-----------|
| `opus` | Complex reasoning, architecture, deep analysis | Slower, more expensive |
| `sonnet` | Balanced tasks, code review, general work | Good balance |
| `haiku` | Simple tasks, formatting, quick lookups | Fast but less capable |
| `inherit` | Use whatever the parent session is using | Default behavior |

### 4. Structure the System Prompt

Good agent prompts follow this pattern:

```markdown
You are a [role] specializing in [domain].

## Your Role
- [Responsibility 1]
- [Responsibility 2]

## Process
1. [Step 1 — gather context]
2. [Step 2 — analyze]
3. [Step 3 — produce output]

## Output Format
[Define the expected structure]

## Guardrails
- [What to avoid]
- [What to prioritize]
```

### 5. Keep Agents Focused

Each agent should do **one thing well**. If an agent's prompt exceeds ~200 lines, consider splitting it into two agents.

## Agents in This Repository

| Agent | Model | Purpose |
|-------|-------|---------|
| `architect` | opus | System design, scalability, ADRs |
| `chief-of-staff` | opus | Cross-team alignment, communication |
| `planner` | opus | Implementation planning, task breakdown |
| `code-reviewer` | opus | Code quality, security, best practices |
| `database-reviewer` | opus | Schema design, query optimization |

## When Subagents Shine

Subagents work best when **the exploration is separate from the execution**. If each step depends on what the previous step discovered, keep that work in your main thread. But if you just need an answer and don't care about the journey — delegate it.

### The Decision Rule

> [!TIP]
> Ask yourself one question: **does the intermediate work matter?**
>
> - **No** → you just need the final result → **delegate to a subagent**
> - **Yes** → you need to see and react along the way → **keep it in your main thread**

### ✅ Ideal Use Cases

#### 1. Research & Exploration

Research is the classic subagent use case. Consider investigating how authentication works in an unfamiliar codebase. Your main thread needs to know *where* the JWT is validated, but it doesn't need to see every file searched along the way.

A research subagent can read dozens of files, trace function calls, and explore different code paths. All that exploration stays in the subagent's context. Your main thread receives a clean summary:

> *"JWT validation happens in `middleware/auth.js` line 42, called from the Express router in `routes/api.js`"*

The subagent did the heavy lifting. Your main thread gets exactly what it needs to move forward.

#### 2. Code Reviews

Claude reviews code **more effectively** when the code is presented as being authored by someone else. If you built a feature over many turns with your main thread, asking that same thread to review it produces weak feedback — Claude was involved in creating it and has trouble seeing it with fresh eyes.

A reviewer subagent sees the changes in a **separate context**. It runs `git diff`, reads the modified files, and applies its specialized review criteria without the history of how the code was written. This separation also lets you encode project-specific review standards in the subagent's system prompt, ensuring consistent criteria across the team.

#### 3. Custom System Prompts

Claude Code's default system prompt emphasizes concise, code-focused responses. That works great for coding, but not for everything. Two cases where a custom system prompt makes the subagent genuinely better:

| Use Case | Why It Helps |
|----------|-------------|
| **Copywriting subagent** | Give it instructions about tone, audience, and style. Claude Code's default prompt tends toward concise technical writing — not what you want for a landing page or email campaign. |
| **Styling subagent** | Point it at your design system files. When the subagent runs, those files load into its context automatically — it knows your color variables, spacing conventions, and component patterns before writing any CSS. |

### ❌ When Subagents Hurt

The overhead of launching a subagent — losing visibility into its work and compressing findings into a summary — only makes sense when the subagent does something the main thread can't. Watch out for these **anti-patterns**:

#### Anti-Pattern 1: "Expert" Claims

> [!WARNING]
> Subagents that claim expertise rarely help. Prompts like *"you are a Python expert"* or *"you are a Kubernetes specialist"* add no value because Claude **already has that knowledge**. There's nothing a so-called expert subagent can do that your main thread can't do directly.

The value of a subagent comes from **context separation and custom system prompts**, not from claiming domain expertise.

#### Anti-Pattern 2: Sequential Pipelines

Sequential subagent pipelines create problems. Consider a three-agent flow: one to reproduce a bug, one to debug it, one to fix it.

Pipelines work when tasks are **truly independent**. They **fail** when each step depends on discoveries from the previous step — and bug fixing almost always does. Information gets lost in the handoff between agents.

```
# ❌ Bad — each step depends on the previous one
Agent 1: Reproduce the bug → summary
Agent 2: Debug based on summary → summary  ← information lost!
Agent 3: Fix based on summary → code

# ✅ Good — keep dependent steps in the main thread
Main thread: Reproduce → Debug → Fix (full context preserved)
```

#### Anti-Pattern 3: Test Runners

Test runner subagents tend to **hide information you need**. When tests fail, you want the full output to diagnose issues. A subagent that returns "tests failed" forces you to create additional debug scripts to get details that would have been visible in direct output.

> [!CAUTION]
> Testing has shown that the test runner pattern **performed worse** among all agent configurations. Keep test execution in your main thread.

### Quick Reference

| Scenario | Use Subagent? | Why |
|----------|:---:|-----|
| Researching an unfamiliar codebase | ✅ | Exploration stays contained, main thread gets clean summary |
| Reviewing code you just wrote | ✅ | Fresh context = better, more honest feedback |
| Writing copy with specific tone | ✅ | Custom system prompt beats default coding prompt |
| Styling with a design system | ✅ | Design tokens load into subagent context automatically |
| "Python expert" review | ❌ | Claude already has that knowledge — no added value |
| Multi-step bug reproduction → fix | ❌ | Steps are dependent, information lost in handoffs |
| Running and interpreting tests | ❌ | You need full output for debugging, not a summary |

## How Agents Are Loaded

- Agents are discovered at **session start** from the `agents/` directory
- If you create or modify an agent mid-session, restart or use `/agents` to reload
- Agents can be invoked explicitly (`@agent-name`) or automatically when Claude detects a matching task

## Multi-Agent Patterns

### Parallel Execution
For independent tasks, launch multiple agents simultaneously:
```
Agent 1: Security review of auth module
Agent 2: Performance analysis of database queries
Agent 3: Architecture review of API design
```

> [!TIP]
> Parallel execution is where subagents truly shine — each gets a fresh context, the tasks are independent, and you get multiple perspectives simultaneously.

### Sequential Pipeline

> [!WARNING]
> Use with caution. Sequential pipelines only work when steps are **truly independent**. If each step depends on the previous one's discoveries, information gets lost in the handoff. Keep dependent workflows in your main thread instead.

For genuinely independent sequential steps:
```
1. Planner → creates implementation plan (independent research)
2. Architect → validates architecture (reads the plan, separate analysis)
3. Code Reviewer → reviews the implementation (fresh eyes on the code)
```

---

*For the official documentation, see: [Claude Code Sub-Agents](https://code.claude.com/docs/en/sub-agents)*
