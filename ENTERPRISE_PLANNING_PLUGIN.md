# Enterprise Planning Plugin

## Overview

This document describes the **enterprise-planning** Claude Code plugin v0.1.0, which transforms ambiguous requirements into executable Work Orders compatible with the work_O system.

## Installation

The plugin is installed at:
```
~/.claude/plugins/marketplaces/local/plugins/enterprise-planning/
```

### Registration

```json
{
  "enterprise-planning@local": [{
    "scope": "user",
    "installPath": "/Users/felipe_gonzalez/.claude/plugins/marketplaces/local/plugins/enterprise-planning",
    "version": "0.1.0",
    "installedAt": "2026-01-15T17:00:00.000Z"
  }]
}
```

## Usage

### Basic Command

```bash
/plan-orch "Add user authentication with JWT tokens"
```

### Workflow Phases

The `/plan-orch` command orchestrates a 5-phase interactive workflow:

1. **Requirements Parsing**
   - Extract claims (P0/P1/P2 priority)
   - Identify assumptions (confidence level + impact)
   - Document constraints (hard vs soft)

2. **Strategic Decomposition**
   - Generate 3-5 strategic alternatives
   - Score each on 5 dimensions (1-5 scale):
     - Constraints satisfaction
     - Time-to-value
     - Complexity
     - Auditability
     - Adaptability
   - User selects preferred strategy

3. **Plan Tree Construction**
   - Build vision → strategy → architecture
   - Create work packages → work orders
   - Review tree structure

4. **Work Order Generation**
   - Generate WOs with Definition of Done (DoD)
   - Include 2+ verification commands per WO
   - Define scope boundaries (allow/deny lists)
   - Set iteration limits

5. **Validation**
   - Schema validation (RequirementSet, WorkOrder, PlanTree)
   - Traceability checking (claims → strategies → WOs → DoD)
   - Circular dependency detection
   - Create validated PlanPack

## Output Structure

```
_ctx/plans/PLAN-2026-0001/
├── requirements.yaml          # RequirementSet
├── strategies/
│   └── strategy-evaluation.yaml
├── plan-tree.yaml             # PlanTree
├── work-orders/
│   ├── WO-0001.yaml
│   ├── WO-0002.yaml
│   └── ...
└── dods/
    ├── WO-0001-dod.yaml
    ├── WO-0002-dod.yaml
    └── ...
```

## Components

### Skills (4)

1. **requirements-parsing** - Parse vague requirements into structured RequirementSet YAML
2. **strategic-decomposition** - Generate and score strategic alternatives
3. **work-order-generation** - Generate executable WOs with DoD
4. **plan-validation** - Fail-closed validation of complete plans

### Commands (1)

- **/plan-orch** - Interactive workflow orchestrator

### Agents (1)

- **planner-guide** - Orchestrates interactive planning workflow

### Validation Scripts (3)

- **plan_validator.py** - Schema validation
- **traceability.py** - Traceability checking with circular dependency detection
- **yaml_utils.py** - Safe YAML loading/saving

### Hooks (1)

- **PostToolWrite** - Auto-validates planning YAML files on write (fail-closed)

### Templates (3)

- **requirement-set.yaml** - RequirementSet template
- **work-order.yaml** - WorkOrder template
- **plan-pack.yaml** - ValidationReport template

## YAML Schema Examples

### RequirementSet

```yaml
RequirementSet:
  id: RQ-2026-0001
  claims:
    - id: C-001
      text: "User login with email/password"
      priority: P0
      acceptance_signals:
        - "User can log in successfully"
  assumptions:
    - id: A-001
      text: "Email service available"
      confidence: 0.9
      impact: high
  constraints:
    - id: CN-001
      type: timeline
      description: "Must ship in Q2"
      hard: false
```

### WorkOrder

```yaml
WorkOrder:
  id: WO-0001
  title: "Setup JWT authentication"
  epic_id: E-001
  priority: P0
  objective: "JWT server issues and validates tokens"
  definition_of_done:
    - metric: "Token validation"
      threshold: "< 50ms p95"
      verification: "bash tests/test_jwt_perf.sh"
  scope:
    allow:
      - "src/auth/"
      - "tests/auth/"
    deny:
      - "src/billing/"
  constraints:
    max_iterations: 3
    max_files_changed: 10
  dependencies: []
```

## Code Quality

### Improvements Applied

1. **Error Handling** - Added `load_yaml_safe()` to prevent catastrophic failure
2. **Code Deduplication** - Consolidated `load_yaml()` into yaml_utils.py
3. **Encoding** - Added UTF-8 encoding to all `open()` calls
4. **Documentation** - Fixed path inconsistencies in skill files
5. **Validation** - Hook with `failurePolicy: "fail"` enforces validation

### Files Summary

- **Total files:** 18
- **Lines of code:** ~2,800
- **Components:** 4 skills, 1 command, 1 agent, 3 scripts, 3 templates

## Testing

To test the plugin:

```bash
cd /Users/felipe_gonzalez/enterprise-planning-test
/plan-orch "Add user authentication with JWT tokens"
```

Expected output:
- Validated PlanPack in `_ctx/plans/PLAN-2026-0001/`
- All YAML files validated against schemas
- Traceability chain verified
- No circular dependencies

## Future Enhancements

Deferred for future iterations:
- Test suite (currently 0% coverage)
- LICENSE file (MIT)
- .gitignore for Python artifacts
- Type safety improvements (TypedDict/Pydantic)

## Related Links

- **Plugin location:** `~/.claude/plugins/marketplaces/local/plugins/enterprise-planning/`
- **Installation status:** `INSTALLATION_STATUS.md`
- **work_O repo:** https://github.com/fegome90-cmd/work_O.git
