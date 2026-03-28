# The Claude Playbook for Tech Leads & Managers

This repository provides a standardized set of patterns, agents, and automated workflows specifically designed for engineering leadership. It focuses on automating technical debt assessment, standardizing high-quality reviews, and professional architectural planning.

## Overview

As a Tech Lead or Engineering Manager, your focus is on the health of the organization and the quality of strategic decisions. This playbook enables you to leverage Claude to:
- **Automate Reviews**: Use specialized agents to ensure code and database changes meet peak standards.
- **Record Decisions**: Standardize the use of ADRs (Architecture Decision Records) to capture the "why" behind every change.
- **Streamline Communication**: Utilize a 'Chief of Staff' agent to triage communication and prioritize action items.
- **Maintain Governance**: Enforce guardrails and common rules across diverse project environments.

## Repository Structure

- `agents/`: Specialized AI agents for architecture, review, and management.
- `rules/common/`: Shared governance rules (coding style, security, git workflow).
- `MCP/`: Guide and prompt examples for MCP server integrations (Datadog, Amplitude, Excalidraw, Miro).
- `skills/`: Comprehensive workflows for strategic tasks (ADRs, article writing, investor materials).
- `.claude/rules/`: Local guardrails for Claude Code behavior.
- `.github/`: Industry-standard PR and Issue templates.
- `scripts/hooks/`: Automation hooks for quality assurance.

## Getting Started

1. **Configure Claude**: Ensure `.claude/rules/` are loaded in your environment.
2. **Review Agents**: Read through `agents/` to understand when to invoke specialists.
3. **Use Skills**: Call upon skills in `skills/` for complex workflows like writing ADRs or management reporting.

## Core Principles

1. **Maintainability over Speed**: Build for the long term.
2. **Strategic Documentation**: Every major decision is recorded.
3. **Security-First**: Baseline focus on trust and data integrity.
4. **Generalist Flexibility**: Tools are language-agnostic for broad engineering support.
5. **Cost-efficiency**: Ensure to optimize the token usage by using the correct tools, asking human before alucinating, and using the correct models.
6. **Context optimization**: Always provide the necessary context to Claude, including relevant documentation, code snippets, and project information. Use subagents to provide just necessary context when the result is what only matters and not the way it was achieved.

## Acknowledgments

This project is derived from [everything-claude-code](https://github.com/affaan-m/everything-claude-code) by **Affaan Mustafa**, licensed under the [MIT License](https://github.com/affaan-m/everything-claude-code/blob/main/LICENSE). Several agents, rules, hooks, and structural patterns were adapted from that project and customized for Engineering Leadership use cases.