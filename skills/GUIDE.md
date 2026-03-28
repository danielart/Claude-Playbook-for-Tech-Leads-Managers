# Skills — A Practical Guide

> Based on [Anthropic's Claude Code documentation](https://code.claude.com/docs/en/plugins) and the [Anthropic Skills course](https://anthropic.skilljar.com/introduction-to-agent-skills).

---

## What Are Skills?

Skills are **on-demand, specialized workflows** that Claude loads only when needed. They contain detailed instructions, templates, and resources for complex, multi-step tasks that would be too large to keep in CLAUDE.md or rules.

Think of skills as **runbooks for Claude** — step-by-step procedures for recurring complex tasks like writing ADRs, generating investor reports, or running verification loops.

## How Skills Fit In: The Full Customization Landscape

Claude Code offers several customization options — skills, CLAUDE.md, subagents, hooks, and MCP servers. They solve **different problems**, and knowing when to use each prevents you from building the wrong thing.

### Skills vs CLAUDE.md

CLAUDE.md loads into **every conversation, always**. If you want Claude to use TypeScript strict mode in your project, put it in CLAUDE.md.

Skills load **on demand**. When Claude matches a request to a skill, that skill's instructions join the conversation. Your PR review checklist doesn't need to be in context when you're writing new code — it activates when you ask for a review.

| Use CLAUDE.md for | Use Skills for |
|-------------------|----------------|
| Project-wide standards that always apply | Task-specific expertise |
| Constraints like "never modify the database schema" | Knowledge that's only relevant sometimes |
| Framework preferences and coding style | Detailed procedures that would clutter every conversation |

### Skills vs Subagents

Skills **add knowledge to your current conversation**. When a skill activates, its instructions join the existing context.

Subagents **run in a separate context**. They receive a task, work on it independently, and return results. They're isolated from the main conversation.

| Use Subagents when | Use Skills when |
|--------------------|----------------|
| You want to delegate a task to a separate execution context | You want to enhance Claude's knowledge for the current task |
| You need different tool access than the main conversation | The expertise applies throughout a conversation |
| You want isolation between delegated work and your main context | You need Claude to reason with the knowledge, not just produce a result |

### Skills vs Hooks

Hooks fire **on events**. A hook might run a linter every time Claude saves a file, or validate input before certain tool calls. They're **event-driven**.

Skills are **request-driven**. They activate based on what you're asking.

| Use Hooks for | Use Skills for |
|---------------|----------------|
| Operations that should run on every file save | Knowledge that informs how Claude handles requests |
| Validation before specific tool calls | Guidelines that affect Claude's reasoning |
| Automated side effects of Claude's actions | Procedures triggered by user intent, not system events |

### Skills vs MCP Servers

MCP servers provide **external tools and integrations** — connecting Claude to databases, APIs, and other services. They're a completely different category from skills.

### Putting It All Together

A typical setup combines all five:

| Feature | Role | Loads |
|---------|------|-------|
| **CLAUDE.md** | Always-on project standards | Every conversation |
| **Skills** | Task-specific expertise | On demand |
| **Hooks** | Automated operations | Triggered by events |
| **Subagents** | Isolated execution contexts | Delegated work |
| **MCP servers** | External tools and integrations | Always available |

> [!TIP]
> Each handles its own specialty. Don't force everything into skills when another option fits better — combine them. Skills provide automatic task-specific expertise, CLAUDE.md is for always-on instructions, subagents run in isolated contexts, hooks fire on events, and MCP provides external tools.

## Skill File Structure

Every skill lives in its own directory under `skills/`. The minimum requirement is a `SKILL.md` file:

```
skills/
├── architecture-decision-records/
│   └── SKILL.md                 # Main instructions
├── article-writing/
│   └── SKILL.md
├── verification-loop/
│   └── SKILL.md
└── investor-materials/
    └── SKILL.md
```

### Advanced Skill Structure

Skills can include additional resources:

```
skills/
└── my-complex-skill/
    ├── SKILL.md                 # Main instructions (required)
    ├── scripts/                 # Helper scripts
    │   └── validate.sh
    ├── examples/                # Reference implementations
    │   └── example-output.md
    └── resources/               # Templates, assets, data
        └── template.md
```

## SKILL.md Format

The SKILL.md file uses **YAML frontmatter + Markdown body**:

```markdown
---
name: architecture-decision-records
description: >
  Capture architectural decisions as structured ADRs.
  Auto-detects decision moments, records context, alternatives,
  and rationale. Use when discussing trade-offs or choosing
  between significant alternatives.
---

# Architecture Decision Records

## When to Activate
- User explicitly says "let's record this decision"
- User chooses between significant alternatives
- During planning phases with architectural trade-offs

## Process
1. Identify the decision being made
2. Gather context and constraints
3. Document alternatives considered
4. State consequences and trade-offs
5. Write the ADR file

## Output Template
[detailed template here]
```

### Frontmatter Fields

The agent skills open standard supports several fields. Two are required, the rest are optional but powerful:

| Field | Required | Description | Constraints |
|-------|----------|-------------|-------------|
| `name` | ✅ | Identifies your skill | Lowercase letters, numbers, hyphens only. Max 64 chars. Should match directory name |
| `description` | ✅ | Tells Claude **when** to use this skill — the most important field | Max 1,024 chars. Claude uses this for matching |
| `allowed-tools` | ❌ | Restricts which tools Claude can use when skill is active | Comma-separated list (e.g., `Read, Grep, Glob`) |
| `model` | ❌ | Specifies which Claude model to use | `sonnet`, `opus`, `haiku`, or `inherit` |

## Writing Effective Descriptions

The `description` field is the most important metadata — it's what Claude uses to decide whether a skill is relevant. Be explicit. If someone told you *"your job is to help with docs,"* you wouldn't know what to do — and Claude thinks the same way.

A good description answers **two questions**:
1. **What does the skill do?**
2. **When should Claude use it?**

```yaml
# ❌ Bad — too vague
description: Helps with documentation

# ✅ Good — clear purpose and triggers
description: >
  Captures architectural decisions as structured ADRs.
  Use when discussing trade-offs, choosing between alternatives,
  or when the user says "record this decision" or "ADR this".
  Also triggers when comparing frameworks, databases, or patterns.
```

> [!IMPORTANT]
> If your skill isn't triggering when you expect, **add more keywords** that match how you actually phrase your requests. The description is what Claude uses for matching, so the language matters.

## Restricting Tools with `allowed-tools`

Sometimes you want a skill that can **only read files, not modify them**. This is useful for security-sensitive workflows, read-only tasks, or any situation where you want guardrails.

```yaml
---
name: codebase-onboarding
description: Helps new developers understand how the system works.
allowed-tools: Read, Grep, Glob, Bash
model: sonnet
---
```

When this skill is active, Claude can only use those tools without asking permission — **no editing, no writing**.

> [!NOTE]
> If you omit `allowed-tools` entirely, the skill doesn't restrict anything. Claude uses its normal permission model.

## Progressive Disclosure

Skills share Claude's **context window** with your conversation. When Claude activates a skill, it loads the SKILL.md contents into context. But sometimes you need references, examples, or utility scripts that the skill depends on.

Cramming everything into one 2,000-line file has two problems: it takes up a lot of context window space, and it's not fun to maintain.

**Progressive disclosure** solves this. Keep essential instructions in SKILL.md and put detailed reference material in **separate files** that Claude reads only when needed.

The recommended directory structure:

```text
skills/
└── my-skill/
    ├── SKILL.md              # Core instructions (< 500 lines)
    ├── scripts/              # Executable code
    │   └── validate.sh
    ├── references/           # Additional documentation
    │   └── architecture-guide.md
    └── assets/               # Images, templates, data
        └── template.md
```

Then in SKILL.md, **link to supporting files with clear instructions** about when to load them:

```markdown
## References

- If the user asks about system design, read `references/architecture-guide.md`
- If the user needs the output template, read `assets/template.md`
- For environment validation, run `scripts/validate.sh`
```

Claude reads `architecture-guide.md` only when someone asks about system design. If they're asking where to add a component, it never loads that file. It's like having a **table of contents** in the context window rather than the entire document.

> [!TIP]
> **Rule of thumb**: Keep SKILL.md under **500 lines**. If you're exceeding that, split content into separate reference files.

## Using Scripts Efficiently

Scripts in your skill directory can run **without loading their contents into context**. The script executes and only the output consumes tokens. The key instruction: tell Claude to **run** the script, not **read** it.

```markdown
## Environment Check

Before starting, run the validation script (do not read its contents):
`bash scripts/validate-environment.sh`
```

This is particularly useful for:
- **Environment validation** — check that required tools are installed
- **Data transformations** — consistent processing that doesn't need to be regenerated
- **Operations that are more reliable as tested code** than generated code

> [!NOTE]
> Scripts execute without loading their contents into context — only the output consumes tokens, keeping context efficient.

## Best Practices for Writing Skills

### 1. Define Clear Activation Triggers

The `description` and "When to Activate" section are critical — they tell Claude **when** to load this skill:

```markdown
## When to Activate

**Explicit triggers** (always activate):
- User says "write an ADR"
- User says "record this decision"

**Implicit triggers** (suggest activation):
- User is comparing two frameworks
- User says "the reason we chose X over Y is..."
- User asks "why did we decide to use X?"
```

> [!IMPORTANT]
> Include both **explicit** (user directly asks) and **implicit** (contextual cues) triggers.

### 2. Structure the Workflow as Steps

Skills should be step-by-step procedures, not essays:

```markdown
## Workflow

### Phase 1: Gather Context
1. Read the relevant codebase files
2. Identify the decision being made
3. Understand the constraints

### Phase 2: Analyze
1. List all alternatives considered
2. Document pros/cons of each
3. Identify the trade-offs

### Phase 3: Produce Output
1. Draft the document using the template below
2. Present to user for review
3. Only write files after explicit approval
```

### 3. Include Templates and Examples

Skills excel when they provide **concrete output templates**:

```markdown
## Output Template

```markdown
# ADR-NNNN: [Decision Title]

**Date**: YYYY-MM-DD
**Status**: proposed | accepted | deprecated

## Context
[2-5 sentences about the problem]

## Decision
[1-3 sentences stating the decision]

## Alternatives Considered
### Alternative 1: [Name]
- Pros: [benefits]
- Cons: [drawbacks]

## Consequences
### Positive
- [benefit]
### Negative
- [trade-off]
```
```

### 4. Add Guardrails and Quality Criteria

Define what makes a **good** vs **bad** output:

```markdown
## Quality Criteria

### Do
- Be specific — "Use Prisma ORM" not "use an ORM"
- Record the why — rationale matters more than the what
- Keep it short — readable in 2 minutes

### Don't
- Record trivial decisions
- Write essays (context > 10 lines = too long)
- Omit alternatives
```

### 5. Document Integration Points

If the skill interacts with other skills or agents, document it:

```markdown
## Integration with Other Components

- **Planner agent**: Suggest creating an ADR when architecture changes
- **Code reviewer agent**: Flag PRs that introduce architecture changes
  without a corresponding ADR
- **Git workflow rule**: Link ADR in commit messages for traceability
```

## Skills in This Repository

| Skill | Purpose |
|-------|---------|
| `architecture-decision-records` | Capture architectural decisions as structured ADRs with full lifecycle management |
| `article-writing` | Write technical articles and blog posts following a structured format |
| `investor-materials` | Generate investor-facing documents (pitch decks, updates, reports) |
| `verification-loop` | Verify that implementations match the original strategic intent |

## How Skills Are Loaded

1. **Discovery**: Claude scans the `skills/` directory at session start
2. **Matching**: When a task matches a skill's description, Claude reads the SKILL.md
3. **Activation**: The skill's instructions are loaded into context
4. **Execution**: Claude follows the skill's workflow
5. **Completion**: Skill context is released after the task

> [!NOTE]
> Skills consume context **only when activated**. This is their key advantage over rules (always loaded) — you can have many detailed skills without bloating every interaction.

## When NOT to Use Skills

| Scenario | Use Instead |
|----------|-------------|
| Simple, always-on constraint | Rule (`.claude/rules/`) |
| Specialized persona for delegation | Agent (`agents/`) |
| Project overview and core conventions | CLAUDE.md |
| One-off task that won't repeat | Just ask Claude directly |
| External tool integrations | MCP servers |
| Automated actions on file save or tool calls | Hooks |

## Troubleshooting Skills

When skills don't work, the problem usually falls into one of a few categories. Most fixes are straightforward.

### Start with the Skills Validator

The **agent skills verifier** command catches structural problems before you spend time debugging other things. Install it via `uv` and run it against your skill directory:

```bash
# Install and run the validator
uv tool install agent-skills-verifier
agent-skills-verifier /path/to/skills/
```

### Problem: Skill Doesn't Trigger

Your skill exists and passes validation, but Claude isn't using it. The cause is **almost always the description**.

Claude uses **semantic matching**, so your request needs to overlap with the description's meaning. If there's not enough overlap, no match.

**Fix:**
1. Check your description against how you're actually phrasing requests
2. Add trigger phrases users would actually say
3. Test with variations like *"help me profile this,"* *"why is this slow?,"* *"make this faster"*
4. If any variation fails to trigger, add those keywords to your description

### Problem: Skill Doesn't Load

If your skill doesn't appear when you ask Claude *"what skills are available,"* check these structural requirements:

> [!WARNING]
> - The `SKILL.md` file **must be inside a named directory**, not at the skills root
> - The file name must be exactly `SKILL.md` — all caps on "SKILL", lowercase "md"
> - YAML frontmatter must be valid syntax

Run `claude --debug` to see loading errors. Look for messages mentioning your skill name.

```text
# ❌ Wrong — SKILL.md at the root
skills/
├── SKILL.md          ← won't be discovered

# ✅ Correct — SKILL.md inside a named directory
skills/
└── my-skill/
    └── SKILL.md      ← discovered correctly
```

### Problem: Wrong Skill Gets Used

If Claude uses the wrong skill or seems confused between skills, your descriptions are **too similar**. Make them distinct.

Being as specific as possible doesn't just help Claude decide *when* to use your skill — it also prevents conflicts with other similar-sounding skills.

### Problem: Skill Priority Conflicts

If your personal skill is being ignored, an enterprise or higher-priority skill might have the **same name**.

For example, if there's an enterprise `code-review` skill and you also have a personal `code-review` skill, the enterprise one wins every time.

**Options:**
- Rename your skill to something more distinct (usually easier)
- Talk to your admin about the enterprise skill

### Problem: Plugin Skills Not Appearing

Installed a plugin but can't see its skills? Clear the cache, restart Claude Code, and reinstall. If skills still don't appear, the plugin structure might be wrong — use the validator tool.

### Problem: Runtime Errors

The skill loads but fails during execution. Common causes:

| Issue | Fix |
|-------|-----|
| Missing dependencies | Add dependency info to your skill description so Claude knows what's needed |
| Permission issues | Run `chmod +x` on any scripts your skill references |
| Path separators | Use **forward slashes everywhere**, even on Windows |

### Quick Troubleshooting Checklist

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Not triggering | Description mismatch | Add trigger phrases that match how you phrase requests |
| Not loading | Structural issue | Check path, file name (`SKILL.md`), and YAML syntax |
| Wrong skill used | Similar descriptions | Make descriptions more distinct from each other |
| Being shadowed | Priority conflict | Check hierarchy and rename if needed |
| Plugin skills missing | Cache issue | Clear cache and reinstall |
| Runtime failure | Environment issue | Check dependencies, permissions (`chmod +x`), and paths |

## Creating a New Skill — Checklist

- [ ] Create `skills/my-skill-name/SKILL.md`
- [ ] Add YAML frontmatter with `name` and `description`
- [ ] Consider adding `allowed-tools` for security-sensitive skills
- [ ] Define "When to Activate" triggers (explicit + implicit)
- [ ] Write the step-by-step workflow
- [ ] Include output templates or examples
- [ ] Add quality criteria (do/don't)
- [ ] Keep SKILL.md under 500 lines — use progressive disclosure for references
- [ ] Tell Claude to **run** scripts, not **read** them
- [ ] Document integration with other agents/skills
- [ ] Run the skills validator
- [ ] Test by triggering the skill with various phrasings

---

*For the official documentation, see: [Claude Code Skills](https://code.claude.com/docs/en/plugins) and [Anthropic Skills Repository](https://github.com/anthropics/skills)*
