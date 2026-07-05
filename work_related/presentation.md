
You are a presentation designer. Create 5 slides for a 5-minute talk using the structure in Section 12. Use the concrete examples from Section 9 as the demo slide. (em portugues por favor)

# André's Agentic & Prompt Workflow — Presentation Reference Document

> **Optimized for LLM consumption.** Each section is self-contained. Sections reference each other explicitly. No assumed prior knowledge about the platform or the author.

---

## 1. WHO AND WHERE

**Role**: Platform Operations Engineer at HP DataOS — a multi-cloud data platform running on AWS, Kubernetes (EKS), Databricks E2, and Apache Airflow.

**Scope of daily work**: Jira tickets from 30+ product teams, AWS infrastructure-as-code (Terragrunt/Terraform), Kubernetes GitOps (Flux CD) with Karpenter, Databricks access and Unity Catalog governance, CI/CD pipelines (Azure DevOps / Codeway), Airflow Jobs and documentation.

**Primary workspace**: A multi-root VS Code workspace with ~50 cloned repos, all open simultaneously. GitHub Copilot Agent mode is the primary interface.

---

## 2. THE CORE PROBLEM BEING SOLVED

**Before agentic workflow:**
- Tickets arrived as raw, unstructured Jira text — often vague, missing context, or mixing multiple asks
- Institutional knowledge lived only in engineers' heads
- Each similar problem required re-investigation from scratch
- Documentation was always out of date
- Estimated throughput: ~2 tickets/week

**After agentic workflow:**
- Tickets are immediately structured and triaged by AI
- When relevant completed task produces a reusable runbook
- Agents carry domain context across sessions
- Estimated throughput: ~5–6 tickets/week (self-reported, journal entry 15/06/26)

---

## 3. THE CENTRAL KNOWLEDGE REPOSITORY: `cc-dataos`

`cc-dataos` is the cross-cutting hub that makes all other agentic work possible. It is not a product repo — it is the **AI brain of the platform**.

### What it contains

| Directory | Purpose |
|-----------|---------|
| `.github/agents/` | 22 specialized agent definitions (`.agent.md` files) |
| `.github/skills/` | 5+ reusable skill prompts (validated patterns) |
| `.github/prompts/` | Slash-command prompts (`/task-analyze`, `/task-wrap-up`) |
| `.github/workflows/` | GitHub Actions — daily agent compliance audit |
| `.ai-workflow/` | Personal execution, journaling, and synthesis prompt system |
| `.ai-taskanator/` | Master prompt — central brain for all incoming tasks |
| `canonical-specs/` | Authoritative platform specifications |
| `copilot-instructions.md` | ~600-line shared instruction file loaded by all agents |
| `journal/journal.md` | Running work journal — every task, dated, with reuse annotations |
| `prompts/` | Curated prompt templates for architecture, documentation, change impact |
| `readmes-backups/` | Mirror of README files from all DataOS repos |
| `common-responses/` | Pre-written response templates for common support patterns |
| `ticket-patterns/` | Classified Jira ticket patterns for routing and prediction |

### Key design principle

`copilot-instructions.md` is a single source of truth for platform conventions, loaded by all 22 agents. Every agent inherits the same understanding of the stack, networking, security model, and deployment patterns without repetition.

---

## 4. THE AGENT PORTFOLIO (22 AGENTS)

Agents are defined as `.agent.md` files with strict frontmatter: `name`, `description` (contains "Use when:" triggers), `tools` (explicit list), and `argument-hint`. Every agent has a single responsibility.

### Domain agents (do the actual work)

| Agent | Domain |
|-------|--------|
| Terragrunt IaC Engineer | Terragrunt HCL, team scaffolding, env management |
| Terraform Module Developer | Module creation, versioning, breaking changes |
| Databricks Workspace Engineer | Jobs, cluster policies, SQL warehouses |
| Databricks Security Debugger | 7-layer permission tracing — access denied errors |
| Unity Catalog Governance Agent | Catalogs, schemas, grants, storage credentials |
| Kubernetes & Helm Engineer | Helm charts, Flux CD, GitOps, stack definitions |
| Airflow DAG Engineer | DAG authoring, debugging, dev→prod promotion |
| Airflow Access Administrator | User lifecycle, RBAC, Airflow permissions |
| Airflow Incident Triage Agent | Incident classification, severity, handoff |
| Codeway CI/CD Engineer | Pipeline definitions, triggers, filter_stages |
| AWS Platform Specialist | AWS ownership mapping, integration tracing, architecture |
| AWS Change Orchestrator | Execute AWS handoff packets across domain agents |

### Meta agents (work about work)

| Agent | Purpose |
|-------|---------|
| Workflow Orchestrator | Route multi-step tasks; coordinate cross-domain execution |
| Platform Understanding Agent | Build architecture overviews, explain the platform |
| Agent Manager | Create and improve `.agent.md` files |
| Agent Reviewer | Audit agents for drift, staleness, redundancy |
| Repo Cartographer | Document repos with AI-readable structure |
| Code Change Analyst | Git history analysis, code truth reports |
| Doc Consistency Reviewer | Validate documentation against canonical specs |
| Doc Improvement Orchestrator | Full documentation improvement pipeline |
| Doc Writer | Apply doc fixes to MkDocs sites |
| Task Retrospective Scribe | Post-task knowledge capture → runbook authoring |

---

## 5. THE TASK EXECUTION WORKFLOW

### Entry point: `/task-analyze`

Every task starts with `/task-analyze`. The operator pastes a raw Jira ticket, Teams message, or task description. The **master-prompt** (`.ai-taskanator/master-prompt.md`) processes it:

1. **Choose Response Mode**: Route / Clarify / Investigate / Plan / Communicate / Document
2. **Agent Routing**: Identify which specialist agent(s) own the domain — before any execution
3. **Structured Output**: Facts vs. assumptions vs. unknowns made explicit; execution plan if applicable

**Example input**: A Jira ticket with two lines of vague text and a screenshot of an error.
**Example output**: Task type detected, ownership traced to a specific repo and team, execution plan with ordered safe steps, draft Jira comment ready to paste.

### Execution phase (sequential prompt system)

For complex tasks, the `.ai-workflow/execution/` sequence is used:

```
00-execution-controller.md   → session rules (safety, assumptions, output quality bar)
01-task-interpretation.md    → real ask, scope, systems, owners
02-clarification-check.md    → expose missing info and dependencies
03-execution-plan.md         → ordered steps with validation gates
04-delivery-artifacts.md     → PR branch, Jira comment, stakeholder message
```

### Exit point: `/task-wrap-up`

Every completed task ends with `/task-wrap-up`. The **Task Retrospective Scribe** agent runs a two-phase process:

**Phase 1 — Runbook**:
- Structured interview: problem, systems, root cause, exact steps, verification, gotchas
- Drafts a runbook → shows it to the operator → waits for explicit approval → writes to `dataos-ops-internal-docs`
- Registers it in `mkdocs.yml`

**Phase 2 — Journal Entry**:
- Writes a dated entry to `journal/journal.md`
- Annotates for reuse: blog post, PDI evidence, team presentation, YouTube idea, resume highlight

### The feedback loop

```
Jira Ticket
    ↓
/task-analyze (master-prompt + agent routing)
    ↓
Specialist Agent executes (Terragrunt / K8s / Databricks / etc.)
    ↓
Human reviews and approves changes (human-in-the-loop gate)
    ↓
/task-wrap-up (runbook → internal docs + journal entry)
    ↓
Next similar task is cheaper to solve
    ↓ (over time)
Agents have better context → humans need fewer interactions
```

---

## 6. THE JOURNALING SYSTEM

`journal/journal.md` is a running, dated log of all work. It is not a to-do list — it is an evidence archive.

### Each entry contains
- What was done (facts, root cause, fix, outcome)
- What made it non-trivial or interesting
- Reuse annotations: blog / PDI / presentation / YouTube / resume

### Why it matters for agentic work

- The journal is fed into weekly and monthly synthesis prompts (`synthesis/` directory) to extract career-ready artifacts
- It is the primary input for PDI reviews and retrospectives
- It demonstrates compounding productivity — the system gets smarter over time because knowledge accumulates

### Example entry (from journal.md, 22/06/26)

> Fixed `MalformedPolicyDocumentException` blocking 4 AWS Secrets Manager secrets. Root cause: 3 deleted IAM principals hardcoded in KMS CMK key policy. Diagnosed by diffing live AWS KMS policy against HCL. Key lesson: ADO "Re-run failed jobs" uses original commit SHA — needed no-op trailing-newline commits to trigger Codeway filter_path. Runbook written to internal docs.

---

## 7. THE DOCUMENTATION PIPELINE

Documentation is treated as a continuous byproduct of operations, not a separate project.

### Flow

```
Task completed → /task-wrap-up → runbook in dataos-ops-internal-docs
                                        ↓
Pattern repeats across multiple tasks → Doc Writer consolidates
                                        ↓
Doc Improvement Orchestrator runs periodic sweeps → keeps all docs current
                                        ↓
Doc Consistency Reviewer validates claims against implementation
```

### Automated compliance

`daily-agent-audit.yml` runs every day via GitHub Actions:
- Validates all 22 `.agent.md` files against minimum specification requirements
- Creates GitHub issues automatically when agents drift out of compliance

---

## 8. KEY PATTERNS AND PRINCIPLES

### Prompts as infrastructure
Prompts are version-controlled, numbered, and treated with the same discipline as code. They have a reading order, a controller file, and clear inputs/outputs.

### Agent routing before execution
The master-prompt always identifies which specialist agent owns the domain *before* any execution begins. This prevents the AI from attempting domain-specialist work generically.

### Human-in-the-loop gates
Every destructive or production-affecting change requires explicit human approval. The agent drafts; the human reviews; the agent writes only after approval. This is enforced in the agent specifications, not just assumed.

### Separation of concerns
Each agent has exactly one responsibility. No agent does what another agent should do. The Workflow Orchestrator coordinates but does not execute domain work.

### Single source of truth
`copilot-instructions.md` defines all platform conventions once. All agents inherit it. No drift between agents on fundamental facts about the platform.

### Compounding knowledge
The system is designed so that every task makes the next similar task cheaper. Runbooks reduce future investigation time. Journal entries surface patterns. Agents improve as context accumulates.

---

## 9. REAL EXAMPLES FROM PRODUCTION

### Example 1: KMS stale principal cleanup (22/06/26)
- **Input**: Vague Jira ticket about 4 secrets not being created since June 14
- **Diagnosis**: `/task-analyze` traced ownership to the KMS CMK key policy in `dataos-dev-terragrunt`; diffed live AWS policy vs HCL; found 3 deleted IAM principals
- **Fix**: PR #2701, removed stale principals, added 4 live-only principals
- **Lesson captured**: Codeway "Re-run failed jobs" uses original SHA; need no-op commits to retrigger `filter_path`
- **Output**: Runbook written to internal docs

### Example 2: Databricks access debug — IP access list (15/06/26)
- **Input**: Teams message — HPIP service can't reach Databricks workspace
- **Diagnosis**: Databricks Security Debugger ran 7-layer trace; root cause was missing NAT Gateway IPs in IP access list
- **Fix**: PRs #5160 and #5164; also found ITG was missing the HPIP block entirely
- **Rule extracted**: "When an HPIP service can't reach Databricks, check the IP access list first — identity and UC permissions are secondary"

### Example 3: New agent created — Databricks Security Debugger (08/05/26)
- **Input**: Recurring Databricks access issues taking too long to diagnose
- **Action**: Used `/create-agent` → Agent Manager built the `.agent.md` with 7-layer permission chain methodology
- **Impact**: Now used as the standard entry point for all Databricks access tickets

### Example 4: Jira ticket pipeline automation
- Built scripts to fetch, classify, and correlate Jira tickets with git commits
- Created ticket pattern library for routing and prediction
- Common response templates for PR comments, ticket closure, follow-ups, and self-service access

---

## 10. PRODUCTIVITY METRICS (SELF-REPORTED)

| Before | After |
|--------|-------|
| ~2 tickets/week resolved | ~5–6 tickets/week resolved |
| Re-investigate every similar problem | Runbooks reduce repeat investigation |
| Docs always lagging | Docs updated as byproduct of every task |
| Agents built on-demand | 22 agents covering all domains |
| Journal: none | Journal: daily entries, reuse-annotated |

---

## 11. WHAT THIS WORKFLOW IS NOT

- **Not fully automated**: Human-in-the-loop is mandatory for all changes. The AI drafts, proposes, and executes after approval — never unilaterally.
- **Not a replacement for engineering judgment**: The system surfaces options and traces ownership; the engineer decides.
- **Not expensive to maintain**: The entire system runs inside VS Code with GitHub Copilot. No external services, no infrastructure cost.
- **Not a one-time setup**: The agent portfolio grows incrementally. Each new recurring problem type that takes too long to diagnose becomes a candidate for a new agent.

---

## 12. TIPS FOR PRESENTING THIS TO OTHER LLMs

If you are using this document as input to another LLM to generate slides, a talk outline, or a script, here are the most important frames:

1. **The core narrative**: "I turned my daily ops work into a compounding knowledge system. Each task teaches the next task."
2. **The most concrete proof point**: 2→5-6 tickets/week throughput increase.
3. **The most relatable concept**: "Prompts as infrastructure" — version-controlled, numbered, with clear reading order.
4. **The most counterintuitive insight**: Human-in-the-loop is a feature, not a limitation. The agent drafts; I approve; knowledge is captured.
5. **The most important file**: `copilot-instructions.md` — the single source of truth that all agents inherit.
6. **The most important workflow**: `/task-analyze` → specialist agent → `/task-wrap-up` → runbook.
7. **What to skip for a 5-minute talk**: The full agent portfolio (just mention count and categories), the synthesis layer, the daily audit workflow.

### Recommended 5-minute talk structure

```
[0:00–0:30] Hook: "I went from 2 to 6 tickets a week. Here's how."
[0:30–1:30] The problem: unstructured tickets, lost knowledge, manual everything
[1:30–2:30] The system: cc-dataos as AI brain, copilot-instructions, agent routing
[2:30–3:30] Live demo or walkthrough: /task-analyze → specialist agent → /task-wrap-up
[3:30–4:30] The compounding effect: journal, runbooks, knowledge that accumulates
[4:30–5:00] Key takeaway: "Prompts as infrastructure. Every task teaches the next."
```

---

## APPENDIX: KEY FILE PATHS

| File | Purpose |
|------|---------|
| `cc-dataos/copilot-instructions.md` | Platform conventions — shared by all agents |
| `cc-dataos/.ai-taskanator/master-prompt.md` | Central brain for task triage |
| `cc-dataos/.github/prompts/task-analyze.prompt.md` | `/task-analyze` slash command |
| `cc-dataos/.github/prompts/task-wrap-up.prompt.md` | `/task-wrap-up` slash command |
| `cc-dataos/.ai-workflow/README.md` | Personal execution and journal system documentation |
| `cc-dataos/.github/agents/` | All 22 agent definitions |
| `cc-dataos/journal/journal.md` | Running work journal |
| `cc-dataos/.github/workflows/daily-agent-audit.yml` | Automated agent compliance check |