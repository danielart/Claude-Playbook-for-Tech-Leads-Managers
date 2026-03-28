# Rules — A Practical Guide

> Based on [Anthropic's Claude Code documentation](https://code.claude.com/docs/en/memory) and the [Anthropic Skills course](https://skilljar.anthropic.com).

---

## What Are Rules?

Rules are **always-on guardrails** that shape Claude's behavior every time it interacts with your project. Unlike skills (which are invoked on demand) or agents (which handle specific tasks), rules are **passive constraints** that Claude follows automatically.

Think of rules as your team's **coding standards document** — but one that's actually enforced automatically on every interaction.

## Rules vs CLAUDE.md vs Skills

Understanding when to use each is critical for keeping context lean and effective:

| Feature | CLAUDE.md | Rules (`.claude/rules/`) | Skills (`skills/`) |
|---------|-----------|--------------------------|---------------------|
| **Loading** | Always, every session | Always or path-scoped | On-demand only |
| **Scope** | Whole project | Can target specific paths | Activated by trigger |
| **Best for** | Core conventions, project overview | Governance, standards, constraints | Complex workflows, multi-step processes |
| **Size** | Keep concise (< 100 lines) | Short and focused per file | Can be detailed |
| **Example** | "Use conventional commits" | "Security rules for auth/" | "Write an ADR with full template" |

> [!TIP]
> **Rule of thumb**: If Claude must follow it on *every* interaction → **CLAUDE.md**. If it's domain-specific → **Rule file**. If it's a multi-step procedure → **Skill**.

## Where Rules Live

Rules can exist at multiple levels, each with different scope:

### 1. Project Rules — `.claude/rules/`
Located in your repo's `.claude/rules/` directory. These are version-controlled and shared with the team.

```
your-project/
├── .claude/
│   ├── CLAUDE.md              # Main project instructions
│   └── rules/
│       ├── code-style.md      # Code style guidelines
│       ├── security.md        # Security requirements
│       └── testing.md         # Testing conventions
```

### 2. Shared Rules — `rules/`
This repository uses a separate `rules/` directory for reusable, shareable rules that can be adopted across multiple projects.

```
rules/
├── common/                    # Battle-tested rules from the community
│   ├── coding-style.md
│   ├── security.md
│   └── performance.md
├── agents.md                  # Agent orchestration patterns
├── development-workflow.md    # Feature implementation process
└── git-workflow.md            # Commit and branching conventions
```

### 3. User-Level Rules — `~/.claude/rules/`
Personal rules that apply to all your projects (not version-controlled).

## Rule File Format

Rules are simple Markdown files. No frontmatter required — just clear, concise instructions:

```markdown
# Security Rules

## Input Validation
- Validate ALL user input before processing
- Use schema-based validation (Zod, Joi, Pydantic)
- Never trust external data

## Authentication
- Always verify tokens on protected routes
- Use secure defaults (httpOnly, sameSite cookies)
- Implement rate limiting on auth endpoints
```

### Writing Effective Rules

> [!IMPORTANT]
> Rules consume context on every interaction. Keep them **short and actionable**.

```markdown
# ❌ Bad — essay-length explanation
Security is important because attackers can exploit vulnerabilities
in web applications through various attack vectors including but
not limited to SQL injection, cross-site scripting, and...
[200 more words]

# ✅ Good — short, actionable directives
## Security
- Validate all inputs with schema validation
- Escape output to prevent XSS
- Use parameterized queries (no string concatenation in SQL)
- Never log secrets or PII
```

## Path-Scoped Rules

One of the most powerful features: rules that **only activate** when Claude is working on specific files or directories.

```markdown
---
globs: ["agents/*.md"]
---

# Agent Writing Rules

When creating or modifying agent files:
- Always include YAML frontmatter with name, description, tools, and model
- Description must include concrete trigger examples
- System prompt must define role, process, and output format
- Keep agent prompts under 200 lines
```

```markdown
---
globs: ["docs/adr/*.md"]
---

# ADR Rules

When working with Architecture Decision Records:
- Follow the ADR-NNNN naming convention
- Always include: Context, Decision, Alternatives, Consequences
- Update the index in docs/adr/README.md
- Set status to "proposed" for new ADRs
```

> [!TIP]
> Path-scoped rules save context — they only load when relevant files are being edited, keeping CLAUDE.md lean.

## Best Practices for Rules

### 1. Use the Imperative Mood
```markdown
# ❌ "You should probably consider validating input"
# ✅ "Validate all user input before processing"
```

### 2. Provide Examples (Sparingly)
```markdown
## Commit Messages

Use conventional commits:
- `feat: add user authentication`
- `fix: resolve null pointer in login flow`
- `docs: update API reference`
```

### 3. Categorize by Domain
One file per concern — don't mix security rules with coding style rules.

### 4. Include the "Why" for Non-Obvious Rules
```markdown
## Immutability (CRITICAL)

ALWAYS create new objects, NEVER mutate existing ones.
Rationale: Prevents hidden side effects, enables safe concurrency.
```

### 5. Use Severity Markers
```markdown
## Error Handling (CRITICAL)
- Never silently swallow errors

## Code Organization (RECOMMENDED)
- Prefer files under 400 lines
```

## Rules in This Repository

| Rule File | Purpose |
|-----------|---------|
| `coding-style.md` | Immutability, file size, error handling |
| `security.md` | Input validation, auth, data protection |
| `git-workflow.md` | Conventional commits, branching strategy |
| `development-workflow.md` | Feature implementation pipeline |
| `performance.md` | Optimization guidelines |
| `patterns.md` | Common design patterns |
| `testing.md` | Testing strategy and conventions |
| `agents.md` | Agent orchestration patterns |
| `hooks.md` | Automation hooks for quality gates |

### `.claude/rules/` (Local Guardrails)

| Rule File | Purpose |
|-----------|---------|
| `leadership-guardrails.md` | Core leadership principles: document the "why", maintainability over speed, security by design |

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Rules too long (>50 lines) | Split into multiple focused files |
| Vague instructions | Use specific, actionable directives |
| Rules contradict each other | Audit for conflicts periodically |
| Language-specific rules in a general repo | Keep rules language-agnostic unless scoped |
| Stale references | Audit regularly — agents and paths change |

---

*For the official documentation, see: [Claude Code Memory & Rules](https://code.claude.com/docs/en/memory)*
