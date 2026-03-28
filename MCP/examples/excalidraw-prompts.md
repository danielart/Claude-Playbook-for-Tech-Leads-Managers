# Excalidraw MCP — Prompt Examples for Leads

> Practical prompts for generating architecture diagrams, feature maps, and visual documentation using the Excalidraw MCP server. Not everything is Mermaid — sometimes you need something you can easily tweak and share.

---

## Setup

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

---

## Architecture Diagrams

### System Architecture Map

```
Create an Excalidraw diagram showing our microservices architecture:

Services:
- API Gateway (entry point)
- Auth Service
- User Service
- Payment Service
- Notification Service
- Order Service

Connections:
- API Gateway → Auth, User, Order
- Order → Payment, Notification
- Auth → User

Use color coding:
- Green: healthy services
- Blue: core business logic
- Orange: external integrations

Save as a .excalidraw file.
```

### Infrastructure Diagram

```
Create an infrastructure diagram for our AWS setup:

Components:
- CloudFront (CDN)
- ALB (Load Balancer)
- ECS Cluster (3 services)
- RDS PostgreSQL (Primary + Read Replica)
- ElastiCache Redis
- S3 Buckets (assets + backups)
- CloudWatch

Show the data flow from user request to database and back.
Group components by VPC/subnet boundaries.

Export as .excalidraw file — do NOT generate a public link.
```

### Data Flow Diagram

```
Create a data flow diagram for our user registration process:

1. User fills form (Frontend)
2. POST /api/register (API Gateway)
3. Validate input (Auth Service)
4. Create user record (User Service → DB)
5. Send welcome email (Notification Service → Email Provider)
6. Track event (Analytics Service → Amplitude)
7. Return success (API Gateway → Frontend)

Show both the happy path (green arrows) and error paths (red arrows)
at steps 3, 4, and 5.
```

---

## Feature Documentation

### Feature Map

```
Create a visual feature map for our "Premium Subscription" feature:

Components involved:
- Pricing Page (Frontend)
- Stripe Integration (Payment Service)
- Subscription Management (User Service)
- Feature Flags (Config Service)
- Usage Tracking (Analytics)

Show which components are new (green), modified (yellow),
and existing/unchanged (gray).

This is for our sprint planning documentation.
```

### Migration Plan Visualization

```
Create a diagram showing our database migration plan:

Phase 1 (Week 1-2):
- Add new tables alongside old ones
- Dual-write to both schemas

Phase 2 (Week 3-4):
- Switch reads to new schema
- Keep writes to both

Phase 3 (Week 5):
- Stop writes to old schema
- Verify data consistency

Phase 4 (Week 6):
- Drop old tables
- Remove dual-write code

Use a timeline layout with arrows showing the progression.
Color code by risk level (green → yellow → orange → green).
```

---

## Team Communication

### Incident Timeline

```
Create a visual timeline for yesterday's incident:

14:02 - Deployment v2.3.1 starts
14:05 - Deployment complete
14:12 - First error alerts (Datadog)
14:15 - Error rate crosses 5% threshold
14:18 - On-call engineer paged
14:25 - Root cause identified (bad DB migration)
14:30 - Rollback initiated
14:35 - Rollback complete
14:40 - Error rates back to normal
14:45 - All-clear communicated

Use red for the incident window, green for resolution.
This goes in our post-mortem document.
```

### Technical Decision Diagram

```
Create a comparison diagram for our "which message queue to use" decision:

Option A: RabbitMQ
- Mature, well-known
- Complex clustering
- AMQP protocol

Option B: Amazon SQS
- Managed service, no ops
- Limited features
- AWS lock-in

Option C: Apache Kafka
- High throughput
- Complex to operate
- Event sourcing ready

Show as a comparison grid with pros (green), cons (red),
and neutral points (gray). This is for our ADR documentation.
```

### Onboarding Diagram

```
Create a visual onboarding guide for new engineers joining the team:

Day 1: Environment setup (accounts, repos, tools)
Day 2-3: Architecture overview (read ADRs, explore codebase)
Day 4-5: First task (paired with mentor)
Week 2: Independent small feature
Week 3-4: First on-call shadow

Show as a path/journey with milestones.
Include links to relevant docs at each stage (just placeholders).
```

---

## Combining & Iterating

### Iterate on a Diagram

```
Take the architecture diagram we just created and:
1. Add a new "Search Service" connected to Order Service
2. Add an Elasticsearch cluster as its data store
3. Mark the new components with a dashed border to indicate
   "planned, not yet implemented"
4. Add a legend explaining the border styles
```

### Merge Multiple Diagrams

```
Combine our three Excalidraw files into a single overview:
1. infrastructure-diagram.excalidraw (cloud resources)
2. services-architecture.excalidraw (microservices)
3. data-flow.excalidraw (request flow)

Create a high-level overview that references all three,
with each as a "zoomed out" section. Add arrows showing
how they relate to each other.
```

---

## Pro Tips

> [!TIP]
> **Export files, don't share links.** Excalidraw can generate public links, but for internal architecture diagrams, always export as `.excalidraw` files and share through your internal tools (Confluence, GitHub, etc.).

> [!TIP]
> **Iterate, don't restart.** One of the biggest advantages over Mermaid is that you can ask Claude to modify existing diagrams — adding, removing, or reorganizing elements without redoing everything from scratch.

> [!WARNING]
> **Never expose architecture in public links.** If you generate a shareable link, anyone with the URL can see your system architecture. Always use file exports for anything internal.
