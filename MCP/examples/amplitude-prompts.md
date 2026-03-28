# Amplitude MCP — Prompt Examples for Leads

> Practical prompts for analytics, notebook creation, and user impact analysis using the Amplitude MCP server. Especially useful if you understand the concepts but don't dominate the tool — Claude can navigate it for you.

---

## Setup

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

> [!NOTE]
> Use `https://mcp.eu.amplitude.com/mcp` if you are on the EU site. The HTTP transport handles OAuth automatically.

---

## Analytics Notebooks

### Create a Feature Adoption Notebook

```
Create an Amplitude notebook analyzing the adoption of our
"dark mode" feature over the last 30 days:

1. How many users enabled it?
2. What's the daily activation trend?
3. Retention: do users who enable it keep it on?
4. Breakdown by platform (iOS, Android, Web)

Include charts for each metric and a summary with conclusions.
```

### User Funnel Analysis

```
Create a notebook analyzing our checkout funnel for the last 14 days:

1. Cart → Checkout page → Payment → Confirmation
2. Drop-off rate at each step
3. Compare this week vs last week
4. Segment by new users vs returning users

Highlight the step with the biggest drop-off and suggest
what might be causing it based on the data patterns.
```

---

## Analyzing Trends

### Investigate a Metric Drop

```
Our daily active users dropped 15% yesterday compared to the
7-day average. Investigate:

1. When exactly did the drop start?
2. Which user segments are most affected? (country, platform, user type)
3. Are specific features being used less, or is it fewer sessions overall?
4. Did anything correlate? (a deployment, a marketing campaign ending, etc.)

Present a hypothesis for the cause, supported by the data.
```

### Investigate a Metric Spike

```
We're seeing a 40% spike in "export_report" events since Tuesday.
Analyze:

1. Is this organic growth or a specific cohort?
2. Which user segment is driving the spike?
3. Are there parallel changes in related events?
4. Is this sustainable or a one-time burst?

Help me understand if this is a feature going viral in a team
or something else.
```

### Week-Over-Week Comparison

```
Compare user engagement metrics for this week vs last week:
- DAU, WAU, MAU trends
- Average session duration
- Feature usage (top 10 events by volume)
- Retention rate (day 1, day 7)

Flag any metric that changed more than 10% and suggest
whether the change is positive, negative, or neutral.
```

---

## Combined with Datadog (Cross-MCP)

### Technical Errors → User Impact

```
We identified elevated 5xx errors on the "search" service
in Datadog between 14:00 and 16:00 UTC yesterday.

Now check in Amplitude:
1. How many users attempted search during that window?
2. What was the success rate vs normal?
3. Did users retry? Did they abandon?
4. Was there a spike in support-related events?

I need this for our incident post-mortem to quantify user impact.
```

### Deployment Impact — Both Sides

```
We deployed v2.4.0 of the mobile app on Monday.

From Datadog:
- Error rates before and after deployment
- Latency changes
- Any new error types

From Amplitude:
- Session duration changes
- Feature usage changes
- Crash rate perception (if tracked)
- Any drop in engagement metrics

Give me a combined assessment: was this deployment good or bad
from both a technical and user experience perspective?
```

### Reliability vs UX Correlation

```
For the past 30 days, correlate:
- Datadog: daily error rate and p99 latency for "api-gateway"
- Amplitude: daily active users and average session duration

Plot the correlation. Does technical degradation impact user
engagement? What's the threshold where users start noticing?

This helps me make the business case for our reliability
improvement initiative.
```

---

## Data Extraction & Reporting

### Executive Summary

```
Create an executive summary of our product metrics for the
last month:

1. User growth (new users, churn, net growth)
2. Engagement (DAU/MAU ratio, avg session duration)
3. Feature adoption (top 5 new feature usage)
4. Retention (cohort analysis for last 4 weeks)

Format it as a brief report suitable for sharing with
stakeholders — clear, data-driven, no jargon.
```

### Team Performance Dashboard Data

```
Extract the following metrics for my team's quarterly review:

1. Feature completion: events associated with features we shipped
   this quarter and their adoption curves
2. Quality: error rates for our services before and after our changes
3. User satisfaction: any NPS or satisfaction events in Amplitude
4. Impact: which shipped feature had the highest user adoption

Present as a structured summary with metrics and trends.
```

---

## Pro Tips

> [!TIP]
> **You don't need to be an Amplitude expert.** The MCP handles navigation. Focus on asking the right questions — "what happened", "why", and "what should we do" — and let Claude handle the query syntax.

> [!TIP]
> **Combine with Datadog for full context.** The most powerful analyses cross the boundary between technical metrics (Datadog) and user metrics (Amplitude). Always ask "how did this technical issue affect users?" or "did this user behavior change correlate with something in our infrastructure?"

> [!WARNING]
> **Validate the conclusions.** Claude can identify patterns and suggest causes, but correlation isn't causation. Always cross-check with your team's domain knowledge before acting on analytical conclusions.
