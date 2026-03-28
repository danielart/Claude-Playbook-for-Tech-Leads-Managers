# Incident Post-Mortem: [Incident Name]

**Post-mortem Published Date**: [YYYY-MM-DD]

## 1. Incident Summary
Provide a high-level overview of what happened, the duration of the issue, and the primary symptoms.

- **Duration**: [Start Date] to [End Date]
- **Issue**: [Brief description of the technical error]
- **Primary Symptom**: [e.g., Error codes, UI blocking, service downtime]
- **Overall Impact**: [Summary of business/user loss]

## 2. Timeline
**Timezone**: [e.g., CEST/UTC] | **Format**: Military Time

### [Month D, Yr] (First day)
- **[HH:MM]** - Incident declared as [Priority Level, e.g., P1/P2] by [Name].
- **[HH:MM]** - Evidence identified (e.g., Charts, Logs) confirming the drop in [Metric].
- **[HH:MM]** - Technical root cause identified: [Specific error message or log].
- **[HH:MM]** - Mitigation strategy discussed/approved.

### [Month D, Yr] (Next day, and one entry per different day)
- **[HH:MM]** - Recovery: [Action taken to stop the bleeding].
- **[HH:MM]** - Confirmation of service normalization.

## 3. Root Cause(s)
A detailed technical explanation of why the failure occurred.

- **Primary Cause**: [The specific failure point, e.g., third-party API failure, code bug, configuration error].
- **Trigger**: [What event triggered the failure? e.g., browser update, traffic spike].

## 4. Impact

### Customer Impact
- **Severity**: [High/Medium/Low]
- **Description**: [Which user segment was affected and what was their experience?]

### Business Impact
- **Total Loss**: [Currency/Value]
- **Calculation Analysis**: [Number of impacted users] × [Expected Conversion Rate] × [Average Order Value].

## 5. Resolution
- **Immediate Fix**: [Steps taken to resolve the incident quickly].
- **Verification**: [How was the fix verified in Production? e.g., manual tests, monitoring dashboards].

## 6. Remediation and Preventive Measure(s)
Actions to ensure this specific incident (or type of incident) doesn't happen again.

- **Observability**: [Improvements to monitoring/alerts to detect the issue faster next time].
- **Additional Protection**: [Infrastructure changes, e.g., Rate limiting, Fallback mechanisms].
- **Tracking/Logging**: [New events or logs added to provide visibility into "silent" errors].

## 7. Closing the Incident
- [ ] Link this post-mortem in the relevant Runbook.
- [ ] Create Jira tickets for long-term remediation tasks.
- [ ] Close the incident using the Incident Bot.

## Appendix
- **Runbook(s)**: [Link to service runbook]
- **Related Post-Mortem(s)**: [Links to past incidents]
