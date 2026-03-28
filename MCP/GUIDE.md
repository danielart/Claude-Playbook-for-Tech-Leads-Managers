# MCP Servers — A Practical Guide for Tech Leads

> Based on [Anthropic's Claude Code documentation](https://code.claude.com/docs/en/mcp-servers), official MCP documentation, and **real-world experience** using MCPs in engineering leadership.

---

## What Are MCP Servers?

MCP (Model Context Protocol) servers are **external integrations** that extend Claude's capabilities by connecting it to databases, APIs, SaaS platforms, and other services. They provide Claude with tools to interact with the outside world — reading data from Datadog, creating diagrams in Excalidraw, querying analytics in Amplitude, or navigating boards in Miro.

Think of MCP servers as **bridges** — they give Claude the ability to reach systems it couldn't access otherwise, turning it from a local coding assistant into a connected leadership tool.

## Why MCPs Matter for Tech Leads

As a lead, your daily work isn't just about code — it's about **observability, incident response, cross-team alignment, architecture communication, and data-driven decisions**. MCPs let you delegate the tedious parts of those tasks:

| Without MCPs | With MCPs |
| :--- | :--- |
| Manually browsing Datadog dashboards hunting for error patterns | Claude analyzes monitors, identifies trends, suggests alert optimizations |
| Copy-pasting metrics into spreadsheets for reports | Claude pulls data directly and generates comparative reports |
| Spending hours navigating enormous Miro boards for discrepancies | Claude cross-references diagrams with documentation automatically |
| Struggling with unfamiliar analytics tools | Claude navigates the platform for you and extracts insights |

> [!TIP]
> MCPs are tools, not magic. Use them for **concrete use cases where you have enough knowledge to validate the output** — the same principle as everything with AI. You wouldn't use a chainsaw to sand a splinter, and you definitely wouldn't use one without reading the instructions first.

## How MCP Servers Work

MCP servers run as **background processes** that Claude communicates with via a standardized protocol. When configured, Claude can call tools provided by these servers just like its built-in tools.

```mermaid
┌─────────────┐       MCP Protocol       ┌──────────────────┐
│  Claude Code │ ◄──────────────────────► │  MCP Server      │
│  (Client)    │       (JSON-RPC)         │  (e.g. Datadog)  │
└─────────────┘                           └────────┬─────────┘
                                                   │
                                               ┌───▼───┐
                                               │  API  │
                                               └───────┘
```

### Configuration

MCP servers are configured in `.claude/mcp.json` (project-level) or `~/.claude/mcp.json` (user-level):

```json
{
  "mcpServers": {
    "datadog": {
      "type": "http",
      "url": "https://mcp-server.datadoghq.com/"
    }
  }
}
```

> [!IMPORTANT]
> **Token costs are on your account.** Every MCP call consumes API tokens from the underlying service. Optimize your usage — batch queries when possible and avoid unnecessary polling.

> [!CAUTION]
> **Never commit API keys or tokens to version control.** Use environment variables or secret managers. Project-level `.claude/mcp.json` should be in `.gitignore` if it contains credentials.

## MCP Servers for Engineering Leadership

The following MCPs have proven especially useful for tech lead workflows. All are **official**, maintained by their respective companies, which provides an extra layer of security and reliability.

### Datadog — Observability & Incident Response

**What it does**: Connects Claude to your Datadog dashboards, monitors, and metrics.

**How to set up**:

1. **HTTP Transport (Recommended)**: Add to `.claude/mcp.json`:
   ```json
   {
     "mcpServers": {
       "datadog": {
         "type": "http",
         "url": "https://mcp-server.datadoghq.com/"
       }
     }
   }
   ```
   *Replace the URL with your regional site (e.g., `.eu/` for Europe).*

2. **Regional Endpoints**:
   - **US1**: `https://mcp-server.datadoghq.com/`
   - **US3**: `https://mcp-server.us3.datadoghq.com/`
   - **US5**: `https://mcp-server.us5.datadoghq.com/`
   - **EU1**: `https://mcp-server.datadoghq.eu/`
   - **AP1**: `https://mcp-server.ap1.datadoghq.com/`

**Official link**: [Set up the Datadog MCP Server](https://docs.datadoghq.com/bits_ai/mcp_server/setup/?tab=claudecode)

---

### Amplitude — Product Analytics & Insights

**What it does**: Connects Claude to your Amplitude workspace for analytics queries, notebook creation, and data analysis.

**How to set up**:

1. **HTTP Transport (Recommended)**: Add to `.claude/mcp.json`:
   ```json
   {
     "mcpServers": {
       "amplitude": {
         "type": "http",
         "url": "https://mcp.amplitude.com/mcp"
       }
     }
   }
   ```
   *Note: Use `https://mcp.eu.amplitude.com/mcp` if you are on the EU site.*

**Official link**: [Amplitude MCP](https://amplitude.com/docs/amplitude-ai/amplitude-mcp)

---

### Excalidraw — Architecture & Design

**What it does**: Streams hand-drawn Excalidraw diagrams with smooth viewport camera control and interactive fullscreen editing.

**How to set up**:

1. **Cloud Remote (Highly Recommended)**: Add to `.claude/mcp.json`:
   ```json
   {
     "mcpServers": {
       "excalidraw": {
         "type": "http",
         "url": "https://mcp.excalidraw.com"
       }
     }
   }
   ```

2. **Local Setup (Optional)**: If you need to run it locally for isolation, build from source using `node` and the `--stdio` flag.

**Official link**: [Excalidraw MCP App Server](https://mcp.excalidraw.com)

---

### Miro — Board Analysis & Design

**What it does**: Connects Claude to your Miro boards for reading, analyzing, and creating content.

**How to set up**:

1. **HTTP Transport (Recommended)**: Add to `.claude/mcp.json`:
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
   *Note: Ensure your Miro admin has enabled the MCP server in Admin settings if you are on an Enterprise plan.*

**Official link**: [Miro MCP Server](https://miro.com/marketplace/mcp-server/)

---

### Other MCPs Worth Knowing

| MCP | Use Case for Leads | Status |
| :--- | :--- | :--- |
| **Atlassian (Confluence/Jira)** | Cross-reference documentation, analyze sprint data | Ready |
| **Context7** | Pull up-to-date documentation for any library/framework | Ready |
| **Playwright** | Automate browser-based verification and testing | Ready |
| **GitHub** | PR management, issue tracking, repository analysis | Ready |

## How MCPs Fit In: The Customization Landscape

MCP servers provide **external tools and integrations** — they're a distinct category from agents, skills, rules, and hooks:

| Feature | Role | Loads |
| :--- | :--- | :--- |
| **CLAUDE.md** | Always-on project standards | Every conversation |
| **Skills** | Task-specific expertise | On demand |
| **Hooks** | Automated operations | Triggered by events |
| **Subagents** | Isolated execution contexts | Delegated work |
| **MCP servers** | External tools and integrations | Always available |

### MCPs + Agents = Power Combos

The real strength emerges when you combine MCPs with agents and skills:

- **Planner agent + Datadog MCP**: Plan a migration while checking current error rates and system health
- **Architect agent + Excalidraw MCP**: Design a system and immediately generate architecture diagrams
- **Code reviewer agent + GitHub MCP**: Review PRs with full repository context
- **Chief of Staff agent + Atlassian MCP**: Prepare cross-team alignments with live data from Jira and Confluence

## Best Practices

### 1. Start With Official MCPs

Official MCPs from the service provider are maintained, documented, and more secure. Check the company's documentation before looking for community alternatives.

### 2. Optimize Token Usage

MCP calls consume API tokens. Be strategic:

```text
# ❌ Bad — chatty, multiple small queries
"Check monitor 12345"
"Now check monitor 12346"
"Now check monitor 12347"

# ✅ Good — batched, focused
"Analyze all monitors for the payments microservice and identify
 which ones triggered in the last 24 hours"
```

### 3. Validate Before Trusting

Claude can analyze data, but **you validate the conclusions**. Always:

- Cross-check critical findings manually
- Don't auto-apply suggestions to production monitors without review
- Use MCP output as a starting point, not the final word

### 4. Secure Your Configuration

- Keep API keys in environment variables, not in config files
- Use read-only tokens when possible
- Rotate keys regularly
- Add `.claude/mcp.json` to `.gitignore` if it contains secrets

### 5. Combine MCPs for Broader Context

The most powerful workflows combine multiple MCPs:

| Combination | Use Case |
| :--- | :--- |
| Datadog + Amplitude | Technical errors → user impact analysis |
| Miro + Confluence | Diagram vs documentation discrepancy detection |
| Excalidraw + GitHub | Generate architecture docs from code structure |
| Datadog + GitHub | Correlate deployment events with error spikes |

## When NOT to Use MCPs

| Scenario | Do Instead |
| :--- | :--- |
| You need always-on project standards | Use CLAUDE.md or rules |
| You need task-specific expert workflows | Use skills |
| You need isolated, delegated work | Use agents (subagents) |
| You need automated actions on events | Use hooks |
| The data is too sensitive for API transit | Access it directly and paste relevant excerpts |

## Security Considerations

> [!CAUTION]
> MCP servers run as external processes and communicate with third-party APIs. Treat them with the same security hygiene as any production integration.

- **Audit which MCPs you install** — only use official or well-vetted servers
- **Scope permissions tightly** — use read-only API keys when you only need to analyze, not modify
- **Never expose internal architecture via public links** — export files instead
- **Review what data is being sent** — understand what each MCP tool call transmits
- **Rotate credentials** — treat MCP API keys like any other secret

---

*For the official documentation, see: [Claude Code MCP Servers](https://code.claude.com/docs/en/mcp-servers) and the [MCP Specification](https://modelcontextprotocol.io/)*
