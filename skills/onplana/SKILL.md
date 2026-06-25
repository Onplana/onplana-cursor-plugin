---
name: onplana-project-management
description: Plan, track, and report on projects, sprints, and portfolios in Onplana (an AI-native project & portfolio management platform, and the Microsoft Project Online successor) over its MCP server. Use when the user asks to create or break down a project, plan sprints/epics/milestones, check status or Earned Value (EVM), run risk analysis, log or review timesheets, or move a proposal through governance. Realistic triggers: "set up a project in Onplana", "break this spec into tasks and a sprint", "what's at risk on the Atlas project?", "show EVM / status for my portfolio", "log 3 hours to the migration task".
---

# Onplana project & portfolio management

Onplana is an AI-native project portfolio management (PPM) platform positioned as the successor to Microsoft Project Online (which retires Sept 30, 2026). Cursor reaches it through the remote Onplana MCP server, which exposes **247 tools** spanning projects, tasks, dependencies, sprints, epics, milestones, risks, issues, governance/proposals, timesheets & compliance, resource capacity, portfolios, EVM/finance, reports, and workspace documents.

You connect as a real org member (an "agent persona"): you can be assigned tasks, you act under a plan tier and a role, and your writes are attributed to you. Treat Onplana like a teammate's project tool, not a scratchpad.

## When to use this skill

Reach for Onplana when the user wants to **do project/portfolio work that should persist in their PPM system**, for example:

- Stand up a new project and break a brief/spec into tasks, subtasks, sprints, epics, and milestones.
- Check or report status: progress, overdue work, Earned Value (EVM/SPI/CPI), a status digest, or a portfolio rollup.
- Run risk or issue analysis on a project and record findings.
- Log or review timesheets and check compliance.
- Move a proposal through the governance pipeline (Enterprise).
- Find work assigned to you and execute it.

Do **not** use it for throwaway local TODOs or for code-only tasks that don't belong in a PPM tool.

## First moves (every session)

1. **`agent_bootstrap`** — one call returns your identity + org, what you may do on this plan/role, each tool's fields, and your assigned-task count. Call this first. (`whoami` + `describe_capabilities` + `describe_schema` individually only if you need more detail.)
2. To find a project or entity by name when you lack an id: **`search`** or **`search_org_knowledge`** (full-text). Most write tools need an id, not a name.

Availability scales with the org's plan tier and the caller's role; a tool you can't use returns a clear plan/permission error rather than failing silently. When a write is denied, surface the reason (upgrade plan / request role / not a project member) instead of retrying blindly.

## Tool families (the map)

- **Discovery**: `agent_bootstrap`, `whoami`, `describe_capabilities`, `describe_schema`, `search`, `search_org_knowledge`, `list_projects`, `get_project`, `lookup_help_topic`.
- **Projects**: `create_project`, `update_project`, `get_project_history`, `find_similar_projects`, `summarize_project`.
- **Tasks**: `list_tasks`, `get_task`, `create_task`, `add_subtasks` (up to 25 children atomically), `bulk_create_tasks`, `update_task`, `bulk_update_tasks`, `bulk_update_status`, `assign_task`, `bulk_assign`, `move_task_to_epic`, `move_task_to_sprint`, `list_assigned_tasks`, `list_my_tasks`, `list_overdue`, `list_related_tasks`.
- **Dependencies**: `link_dependency`, `unlink_dependency`, `list_dependencies` (FS/SS/FF/SF + lag; cycles are rejected).
- **Agile**: `create_sprint_with_tasks`, `list_sprints`, `get_sprint`, `update_sprint`, `create_epic`, `list_epics`, `get_epic`, `update_epic`.
- **Milestones**: `create_milestone`, `list_milestones`, `get_milestone`, `update_milestone`.
- **Risks & issues**: `analyze_project_risks` (AI), `list_risks`, `dismiss_risk`, `create_issue`, `list_issues`, `update_issue`, `resolve_issue`, `link_issue`.
- **Governance (Enterprise)**: `create_proposal`, `list_proposals`, `get_proposal`, `advance_proposal_stage`, `submit_proposal_review`, `list_gate_approvals`, `designate_gate_reviewer`.
- **Timesheets & compliance**: `create_timesheet_entry`, `list_timesheet_entries`, `submit_timesheet`, `submit_timesheet_week`, `week_status`, `weekly_compliance`, `approve_timesheet_week`.
- **Resource & capacity**: `get_capacity`, `set_capacity`, `resource_leveler_analyze`, `leveling_narrative`.
- **EVM / finance**: `detect_evm_data_source`, `calculate_evm`, `calculate_evm_history`, `compose_evm_narrative`, `capture_baseline`, `get_baseline`.
- **Reporting**: `generate_status_report`, `compose_status_digest`, `compose_meeting_brief`, `compose_release_notes`, `get_project_velocity`.
- **Portfolios & teams**: `create_portfolio`, `get_portfolio`, `add_project_to_portfolio`, `list_teams`, `list_org_members`.
- **Workspace docs**: `list_libraries`, `create_library`, `draft_document` (markdown), `list_documents`, `read_document_content`, `update_document_content`, `upload_task_attachment`, `attach_file_from_url`.
- **Notifications & activity**: `list_notifications`, `list_recent_activity`, `list_my_operations`.

Call `describe_capabilities` for the full, plan-resolved list and `describe_schema` for an entity's exact fields before composing a write.

## Common workflows

### 1. Stand up a project from a brief
1. `create_project` with a clear name, description, and (optionally) start/end dates.
2. Decompose: prefer `bulk_create_tasks` for a flat list, or `create_task` + `add_subtasks` for a parent with up to 25 children in one atomic call. Write task **titles a non-technical PM can read**.
3. Group work: `create_epic` for themes, `create_sprint_with_tasks` to plan an iteration, `create_milestone` for delivery dates.
4. Wire order with `link_dependency` (FS/SS/FF/SF, optional lag).
5. Assign with `assign_task` / `bulk_assign`. Confirm with `get_project` or `summarize_project`.

### 2. Check status & Earned Value
- Quick read: `summarize_project` (progress, open risks/issues, top items) or `generate_status_report`.
- Overdue: `list_overdue`. Velocity: `get_project_velocity`.
- EVM: `detect_evm_data_source` first (imported costs vs. rate cards vs. none), then `calculate_evm` for PV/EV/AC/SPI/CPI, `compose_evm_narrative` for a plain-English summary. Capture a `capture_baseline` before tracking variance.

### 3. Risk & issue analysis
- `analyze_project_risks` runs AI detection and returns findings; persist what matters with `create_issue` (a materialized problem) vs. leaving it as a risk (a probabilistic future). `list_risks` / `list_issues` to review, `resolve_issue` to close with evidence.

### 4. Log and review time
- `create_timesheet_entry` (taskId + hours + date), then `submit_timesheet_week`. Check `week_status` / `weekly_compliance`. Approvers use `approve_timesheet_week`.

### 5. Execute assigned work (agent loop)
- `list_assigned_tasks` → `read_agent_brief(taskId)` for context → `assign_task` to claim → `update_task` status IN_PROGRESS → do the work, posting `append_log` / `create_comment` → `update_task` DONE with evidence (or BLOCKED with a reason). File an `create_issue` if something is wrong.

## Conventions

- **Dates** are calendar-day `YYYY-MM-DD` for due/start dates; timestamps are full ISO.
- **Decompose with `add_subtasks`** (atomic, up to 25) over many single `create_task` calls.
- **One project at a time**: resolve the project id first (via `search`/`list_projects`), then operate on it.
- **Attribute honestly**: report DONE only when verified; use BLOCKED with a reason otherwise.
- **Mixed-currency portfolios**: report per-currency; EVM math is per-task and source-aware.

## Example prompts this skill should handle

- "Create a project in Onplana for the website redesign and break this spec into tasks and a 2-week sprint."
- "What's overdue and at risk on the Atlas migration project? Summarize and file the top issue."
- "Show me EVM (SPI/CPI) for the Q3 portfolio and write a one-paragraph status digest for leadership."
- "Log 3 hours today to the 'Data migration dry run' task and submit my week."
- "Find the tasks assigned to me and start the highest-priority one."
- "Set up an epic and milestones for the Project Online → Onplana migration and wire the dependencies."

When you finish a unit of work, reflect it in Onplana (update the task, add a comment) so the project tool stays the source of truth.
