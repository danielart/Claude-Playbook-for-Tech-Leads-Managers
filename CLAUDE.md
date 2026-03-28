# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the **Claude Playbook for Tech Leads & Managers**. It is a collection of curated agents, rules, and templates specifically designed to support technical leadership roles (Tech Leads, Engineering Managers, Staff Engineers, etc.).

## Repository Structure

- **agents/** - Specialized leadership agents for delegation (Architect, Chief of Staff, Planner, Code Reviewer).
- **MCP/** - Guide and prompt examples for MCP server integrations (Datadog, Amplitude, Excalidraw, Miro).
- **rules/** - Always-follow guidelines for technical excellence (Security, Git Workflow, Patterns, Performance).
- **templates/** - Structured templates for critical engineering leadership processes:
  - `PR_TEMPLATE.md`: Standardized Pull Request summaries.
  - `ADR_TEMPLATE.md`: Architecture Decision Records for logging design choices.
  - `POST_MORTEM_TEMPLATE.md`: Incident review and root cause analysis.

## Leadership Workflow

1. **Strategic Planning**: Use the **planner** and **architect** agents to define vision and project structure.
2. **Reviewing & Mentorship**: Use the **code-reviewer** and **chief-of-staff** agents to ensure cross-team alignment and quality.
3. **Continuous Learning**: Record every significant structural choice as an **ADR** in `docs/adr/`.
4. **Resiliency**: Document all significant failures using the **Post-Mortem** template.

## Contributing

Follow the established formats:
- **Agents**: Markdown with YAML frontmatter (name, description, tools, model).
- **Rules**: Concise guidelines (Context, Rules, Patterns).
- **Templates**: Standard GFM (GitHub Flavored Markdown).

File naming convention: lowercase with hyphens (e.g., `technical-lead.md`, `post-mortem-guide.md`).
