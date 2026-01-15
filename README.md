# Enterprise Planning Test Repository

Test repository for the enterprise-planning Claude Code plugin.

## Purpose

This repository tests the `/plan-orch` command which transforms ambiguous requirements into executable Work Orders.

## Plugin

The enterprise-planning plugin generates PlanPacks compatible with the work_O system:
- Requirements parsing (claims, assumptions, constraints)
- Strategic decomposition (3-5 alternatives)
- Work order generation with DoD
- Fail-closed validation

## Usage

```bash
/plan-orch "Add user authentication with JWT"
```

This will create a validated PlanPack in `_ctx/plans/` with all required YAML files.

