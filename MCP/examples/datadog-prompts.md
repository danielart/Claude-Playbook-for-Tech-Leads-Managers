# Datadog MCP — Prompt Examples for Leads

> Practical prompts for analyzing production errors, optimizing alerts, investigating incidents, and creating reports using the Datadog MCP server.

---

## Setup

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

> [!NOTE]
> Replace the URL with your regional Datadog site (e.g., `https://mcp-server.datadoghq.eu/` for EU1). The HTTP transport handles OAuth automatically.

---

## Monitor Analysis

### Review All Monitors for a Service

```
List all monitors related to the "payments" microservice.
For each monitor, tell me:
- Current status (OK, Alert, Warn, No Data)
- How many times it triggered in the last 7 days
- Whether the threshold seems reasonable based on recent data

Flag any monitors that seem too noisy (triggered more than 5 times this week)
or too silent (haven't triggered in months despite production activity).
```

### Identify Noisy Alerts

```
Analyze our Datadog monitors and identify the top 10 noisiest alerts
from the past 30 days. For each one:
- How many times did it trigger?
- What's the average resolution time?
- Is the threshold too sensitive?
- Suggest a more appropriate threshold based on the p95 of the metric

I want to reduce alert fatigue without missing real incidents.
```

### Audit Monitor Coverage

```
Review all monitors for our microservices and identify gaps:
- Which services have no monitors at all?
- Which services only have basic health checks but no
  business logic monitors?
- Are there any critical endpoints without latency monitors?

Compare against our service catalog and flag anything unmonitored.
```

---

## Alert Optimization

### Optimize Alert Thresholds

```
For the monitor "[Monitor Name]", analyze the metric history
for the last 30 days:
- What's the normal baseline (p50)?
- What are the typical spikes (p95, p99)?
- How many false positives did the current threshold cause?

Suggest an optimized threshold that would have caught real
incidents but avoided false alarms. Show me a before/after
comparison.
```

### Consolidate Redundant Monitors

```
Review all our monitors and identify groups that:
- Track the same or very similar metrics
- Have overlapping alert conditions
- Could be consolidated into composite monitors

For each group, suggest a single consolidated monitor
configuration that maintains the same coverage with less noise.
```

---

## Incident Investigation

### Investigate a Production Error

```
We're seeing elevated error rates on the "checkout" service
starting around [timestamp]. Investigate:

1. Which endpoints are affected?
2. What error types are most common (5xx vs 4xx)?
3. Are there correlated changes in latency or throughput?
4. Did anything change around the time errors started?
   (deployments, config changes, upstream issues)
5. Which downstream services are impacted?

Give me a timeline of events and your hypothesis for root cause.
```

### Post-Incident Analysis

```
An incident occurred on [date] from [start time] to [end time]
affecting the [service name]. Pull the following data:

1. Error rate timeline during the incident window
2. Latency percentiles (p50, p95, p99) before, during, and after
3. Number of affected users/requests
4. Any triggered monitors and their alert timestamps
5. Related infrastructure metrics (CPU, memory, connections)

Format this as a data section for our post-mortem document.
```

### Correlate Across Services

```
We suspect a cascading failure pattern in our architecture.
Starting from the "api-gateway" service:

1. Map all downstream service dependencies
2. Check if error rate increases propagate in order
3. Identify the original source of failure
4. Calculate the delay between each service being affected

Present as a timeline showing the cascade pattern.
```

---

## Reporting & Dashboards

### Weekly Error Report

```
Generate a weekly error report for the engineering team covering
the last 7 days:

1. Top 10 errors by frequency across all services
2. New errors that appeared this week (not seen last week)
3. Errors that were resolved (present last week, gone this week)
4. Services with the highest error rate increase week-over-week
5. Overall system health score (based on p99 latency and error rate)

Format as a markdown table I can paste into our team's Confluence page.
```

### SLA Compliance Check

```
Check our SLA compliance for the past month:
- For each customer-facing service, calculate the actual uptime
- Compare against our 99.9% SLA target
- Identify any breaches and their duration
- Flag services that are trending toward breach (< 99.95% currently)

Include a risk assessment for each service.
```

### Deployment Impact Analysis

```
For the last 5 deployments to the "[service]" service:
1. Compare error rates 1 hour before and after each deployment
2. Compare latency percentiles before and after
3. Identify any deployments that degraded performance
4. Highlight deployments that improved metrics

This helps me decide if we need to add canary deployments
or adjust our rollout strategy.
```

---

## Pro Tips

> [!TIP]
> **Batch your queries.** Instead of asking about monitors one at a time, ask Claude to analyze all monitors for a service in a single prompt. This saves tokens and gives Claude more context for better analysis.

> [!TIP]
> **Combine with Amplitude.** After identifying errors in Datadog, use the Amplitude MCP to check the user impact: "Now check in Amplitude how many users were affected by these checkout errors during the same time window."

> [!WARNING]
> **Don't auto-apply changes.** Claude can suggest new thresholds and configurations, but always review them before applying to production monitors. Use the suggestions as a starting point for discussion with your team.
