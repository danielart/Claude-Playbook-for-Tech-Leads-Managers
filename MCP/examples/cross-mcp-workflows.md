# Cross-MCP Workflows — Combining Integrations

> The most powerful MCP usage comes from combining multiple integrations in a single conversation. These workflows show how to chain MCPs for leadership tasks that cut across tools.

---

## Setup for Cross-MCP Workflows

To run these workflows, you'll need the following configured in your `.claude/mcp.json`. These are the official HTTP/SSE servers:

```json
{
  "mcpServers": {
    "datadog": {
      "type": "http",
      "url": "https://mcp-server.datadoghq.com/"
    },
    "amplitude": {
      "type": "http",
      "url": "https://mcp.amplitude.com/mcp"
    },
    "miro": {
      "type": "http",
      "url": "https://mcp.miro.com"
    },
    "excalidraw": {
      "type": "http",
      "url": "https://mcp.excalidraw.com"
    }
  }
}
```

> [!NOTE]
> Authenticate each server via the browser popup (OAuth) when prompted by Claude Code.

## Incident Post-Mortem (Datadog + Amplitude)

### Full Incident Analysis

```
We had an incident yesterday from 14:00 to 14:45 UTC
affecting the checkout service.

Step 1 — Technical Impact (Datadog):
- Pull the error rate timeline for the checkout service
- Show latency percentiles (p50, p95, p99) during the window
- Identify which monitors triggered and when
- Check if downstream services were also affected
- Pull any deployment events near the incident start

Step 2 — User Impact (Amplitude):
- How many users attempted checkout during this window?
- What was the checkout success rate during vs. normal?
- Did users retry or abandon?
- Was there a spike in support/help events?
- Compare revenue impact: expected vs actual transactions

Step 3 — Combined Report:
Create a post-mortem data section with:
- Timeline of events
- Technical metrics summary
- User impact quantification
- Business impact estimate

Format as markdown suitable for our post-mortem template.
```

---

## Architecture Consistency Audit (Miro + Excalidraw + GitHub)

### Documentation vs Reality

```
I need to verify our architecture documentation is accurate.

Step 1 — Extract planned architecture (Miro):
Analyze the Miro board "[Board ID]" and list all services,
their dependencies, and the expected data flow.

Step 2 — Check actual code (GitHub):
For each service listed in Miro:
- Does the repository exist?
- Do the API endpoints match what Miro shows?
- Are the dependencies in the code consistent with the diagram?

Step 3 — Generate updated diagram (Excalidraw):
Create an Excalidraw diagram showing:
- Services that match (green)
- Services that exist but differ from the plan (yellow)
- Planned services that don't exist yet (red dashed)
- Existing services not in the plan (blue)

Step 4 — Discrepancy Report:
Create a markdown report listing every discrepancy,
categorized by severity, with suggested actions.
```

---

## Quarterly Health Check (Datadog + Amplitude + GitHub)

### Full Stack Health Report

```
Generate a quarterly health check for Q1:

Step 1 — Technical Health (Datadog):
- Uptime per service (target: 99.9%)
- Average error rate trend (weekly over the quarter)
- Latency improvements or degradation
- Number of on-call pages, mean time to resolve

Step 2 — Product Health (Amplitude):
- DAU/MAU ratio trend over the quarter
- Feature adoption for newly shipped features
- User retention cohort analysis (week 1, week 4, week 12)
- Top 5 user journeys and their completion rates

Step 3 — Engineering Health (GitHub):
- PR cycle time (open → merge)
- Review turnaround time
- Deployment frequency
- Hotfix frequency (indicator of quality)

Step 4 — Executive Summary:
Combine all three into a single-page executive summary with:
- 3 key wins
- 3 key concerns
- Recommended actions for Q2

Format for presentation to engineering leadership.
```

---

## Sprint Planning Support (Miro + Atlassian)

### Pre-Planning Analysis

```
We have sprint planning tomorrow. Help me prepare.

Step 1 — Roadmap context (Miro):
Review the Miro board "[Board ID]" and identify:
- Which roadmap items are coming up next
- What dependencies exist between them
- Which teams are involved

Step 2 — Current state (Atlassian):
Check Jira for:
- Carry-over tickets from last sprint
- Bugs that need prioritization
- Tech debt tickets that have been deferred 3+ sprints
- Team capacity based on PTO calendar

Step 3 — Sprint Proposal:
Based on dependencies, capacity, and priorities, suggest:
- Sprint goal (one sentence)
- Must-do tickets (critical path)
- Should-do tickets (important but flexible)
- Nice-to-have tickets (if capacity allows)
- Risk flags

Format as a sprint planning summary I can present to the team.
```

---

## Deployment Readiness (Datadog + GitHub + Amplitude)

### Release Confidence Check

```
We're about to release v3.0 to production. Run a readiness check:

Step 1 — Code quality (GitHub):
- Any open critical/high priority issues?
- All PRs merged and reviewed?
- What's changed since the last release? (summary of commits)
- Any reverted changes?

Step 2 — Baseline metrics (Datadog):
- Current error rates for affected services
- Current latency baselines
- All monitors in OK state?
- Any warnings or known issues?

Step 3 — User baselines (Amplitude):
- Current DAU and session metrics
- Current feature usage rates for impacted areas
- Any ongoing experiments that could conflict?

Step 4 — Go/No-Go Assessment:
Based on all data, provide:
- ✅ Green flags (safe to proceed)
- ⚠️ Yellow flags (proceed with caution)
- 🔴 Red flags (do not deploy until resolved)
- Recommended monitoring plan for the first 2 hours post-deploy
```

---

## Onboarding New Lead (All MCPs)

### Team Context Package

```
A new tech lead is joining my team next week. Help me create
a comprehensive context package.

Step 1 — Architecture overview (Excalidraw):
Generate a clean architecture diagram showing:
- All services our team owns
- Key dependencies
- Data stores
- External integrations

Step 2 — Current health (Datadog):
Summarize the last 30 days for our services:
- Overall reliability
- Most common issues
- Current monitoring coverage

Step 3 — Product context (Amplitude):
Key metrics for our team's features:
- Usage volume
- Trends (growing, stable, declining)
- Key user segments

Step 4 — Strategic context (Miro):
From the roadmap board, summarize:
- Current quarter priorities
- Upcoming major initiatives
- Dependencies with other teams

Step 5 — Compile:
Create a single onboarding document with all of the above,
formatted as a README-style guide with links to deeper context.
```

---

## Pro Tips

> [!TIP]
> **Start with the MCP that answers "what happened" and then move to "so what."** Datadog tells you what broke, Amplitude tells you who was affected, and GitHub tells you what changed. This narrative structure makes your reports compelling.

> [!TIP]
> **Save your multi-MCP prompts as skills.** If you find yourself running the same cross-MCP workflow regularly (like the weekly health check), convert it into a Claude skill with the specific prompts embedded.

> [!IMPORTANT]
> **Token awareness matters even more with multi-MCP workflows.** Each MCP call consumes tokens from its respective service. A cross-MCP workflow can be expensive — use them for high-value decisions, not routine checks that could be dashboarded.
