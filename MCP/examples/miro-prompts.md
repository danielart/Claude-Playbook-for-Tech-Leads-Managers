# Miro MCP — Prompt Examples for Leads

> Practical prompts for analyzing Miro boards, cross-referencing with documentation, and detecting discrepancies. Especially powerful for those enormous boards that look like D&D game boards.

---

## Setup

```json
{
  "mcpServers": {
    "miro": {
      "type": "http",
      "url": "https://mcp.miro.com"
    }
  }
}
```

> [!NOTE]
> The HTTP transport handles OAuth automatically. Ensure your Miro admin has enabled the MCP server if you are on an Enterprise plan.

---

## Board Analysis

### Map Board Contents

```
Analyze the Miro board "[Board Name/ID]" and give me a structured summary:

1. How many sections/frames does it contain?
2. For each section, list:
   - Title
   - Number of items (sticky notes, shapes, text blocks)
   - Key themes or topics mentioned
3. Identify any connectors/arrows and what they link
4. Flag any sections that seem incomplete (few items, no connections)

I need to understand the scope of this board before our planning session.
```

### Extract Action Items

```
Go through the Miro board "[Board Name/ID]" and extract all
action items, to-dos, or decisions:

Look for:
- Sticky notes with action-oriented language ("need to", "should", "TODO")
- Items marked with specific colors (red = blocker, yellow = action, green = done)
- Text that mentions team members or deadlines

Organize by priority and ownership if identifiable.
```

### Board Complexity Assessment

```
Analyze the Miro board "[Board Name/ID]" — it's gotten massive
and we need to decide whether to refactor it.

Tell me:
1. Total number of elements
2. How many distinct topics/areas are covered?
3. Are there orphan elements (not connected to anything)?
4. Which sections are most dense/complex?
5. Suggest how to break this into smaller, focused boards

This board has become our "everything board" and it's
hurting team productivity.
```

---

## Cross-Referencing with Documentation

### Miro vs Confluence Discrepancy Check

```
Compare the architecture diagram on Miro board "[Board ID]"
with our architecture documentation in Confluence.

For each service or component shown in Miro:
1. Does it exist in the documentation?
2. Are the connections/dependencies the same?
3. Are there services in Confluence that aren't in Miro?
4. Are there services in Miro that aren't in Confluence?

Create a discrepancy report in markdown table format.
This helps us anticipate blockers from outdated documentation.
```

### Miro vs Code Reality Check

```
The Miro board "[Board ID]" shows our planned microservices
architecture. Compare it against our actual codebase:

1. List every service shown in Miro
2. Check if each service exists in our GitHub repositories
3. Verify the dependencies shown match actual API calls
4. Flag any planned services that haven't been started
5. Flag any existing services not shown in Miro

I want to know the gap between what we planned and what we built.
```

### Roadmap Board vs Sprint Progress

```
The Miro board "[Board ID]" has our product roadmap.
Check it against our Jira project:

1. Which roadmap items are reflected in completed Jira tickets?
2. Which items are in progress?
3. Which items haven't been started?
4. Are there Jira tickets that don't map to any roadmap item?

Calculate the % completion for each roadmap quarter.
```

---

## Anticipating Problems

### Dependency Analysis

```
On the Miro board "[Board ID]", analyze the feature dependency
diagram:

1. Identify circular dependencies
2. Find critical path items (most things depend on them)
3. Flag single points of failure
4. Highlight bottleneck teams (one team blocking many others)

Present as a risk assessment with severity levels:
- 🔴 Critical: circular deps, single points of failure
- 🟡 Warning: bottleneck teams, long chains
- 🟢 OK: independent or well-parallel items
```

### Ambiguity Detection

```
Review the Miro board "[Board ID]" for our upcoming feature
planning and identify ambiguities:

1. Components mentioned but not defined
2. Connections without clear ownership
3. Sticky notes with questions or uncertainty language
4. Areas where multiple teams overlap without clear boundaries
5. Technical decisions that appear unresolved

For each ambiguity, suggest the right person/team to resolve it
and a proposed resolution approach.
```

### Pre-Planning Risk Review

```
Before our quarterly planning, review the Miro board "[Board ID]":

1. Are all dependencies between teams explicitly mapped?
2. Are there hidden assumptions? (things that must be true
   but aren't stated)
3. Do the timelines make sense given the dependency chains?
4. Are there teams that are overcommitted based on the board?

Summarize the top 5 risks I should raise in the planning meeting.
```

---

## Creating Content in Miro

### Sprint Retrospective Board

```
Create a new Miro board structure for our sprint retrospective:

Frame 1: "What went well" (green area)
- Pre-populate with categories: Collaboration, Delivery, Quality

Frame 2: "What didn't go well" (red area)
- Pre-populate with categories: Blockers, Communication, Technical Debt

Frame 3: "Action items" (blue area)
- Columns: Action, Owner, Deadline, Status

Frame 4: "Metrics" (gray area)
- Sprint velocity, bugs found, PR cycle time

Add a team voting area for prioritizing items.
```

### Architecture Review Board

```
Create a Miro board for our architecture review meeting:

Section 1: Current State
- Placeholder for existing architecture diagram

Section 2: Proposed Changes
- Placeholder for the new design

Section 3: Trade-offs
- Pro/Con grid template

Section 4: Decision
- Decision record template
- Voting area for the team

Section 5: Action Items
- Assignee/task/deadline grid
```

---

## Pro Tips

> [!TIP]
> **The biggest value is in cross-referencing.** Miro boards tend to drift from reality over time. Using the MCP to systematically compare boards against code, documentation, or project tracking is where leads save the most time.

> [!TIP]
> **Use it for those monster boards.** When a board has hundreds of elements and looks like a D&D dungeon map, Claude can process it faster than you can scroll through it. Ask targeted questions instead of trying to review it visually.

> [!TIP]
> **Combine with Atlassian MCP.** For the most powerful discrepancy detection, use Miro MCP alongside the Atlassian MCP — let Claude compare boards against Confluence pages and Jira tickets in a single conversation.

> [!WARNING]
> **Board permissions matter.** Make sure the API token has appropriate access. Read-only is sufficient for analysis; only enable write access if you need Claude to create or modify board content.
