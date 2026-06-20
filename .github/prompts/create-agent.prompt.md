---
name: Create Agent
description: "Create a new agent from the agency plan. Use when: creating a new agent, adding a new agent to the system, agent creation workflow, new agent definition, agent factory"
argument-hint: "Agent name and purpose, e.g.: 'data-validator — validates inputs before API calls'"
agent: agent-creator
---

You are creating a new agent for this system. Follow the steps below exactly and in order.

## Context

Read and internalize these files before doing anything else:

- [Agency Plan](../../prompts/agency_plan.md) — the product roadmap and planned agent roster
- [Architecture Guidelines](../agents/README_agents.md) — design rules, workflows, and interaction model
- [Capability Registry](../agents/capability_registry.agent.md) — the index of all currently registered capabilities

The agent to create is: **$ARGUMENTS**

---

## Step 1 — Locate the agent in the plan

Read [agency_plan.md](../../prompts/agency_plan.md) and find the entry for `$ARGUMENTS`.

Extract:
- The agent's stated purpose in the product roadmap
- Any explicit inputs and outputs already described in the plan
- The domain it belongs to (e.g., product, data, infrastructure, orchestration)

If the plan does not mention this agent, **stop** and ask the user to clarify purpose and scope before continuing. Do not invent a role.

---

## Step 2 — Check for capability conflicts

Query [capability_registry.agent.md](../agents/capability_registry.agent.md) for any existing capability that overlaps with the new agent's intended scope.

Rules:
- If overlap is **> 30%** with an existing agent → stop, flag the conflict to the Agent Coordinator, and do not proceed until resolved
- If no conflict → continue to Step 3

---

## Step 3 — Map interactions with existing agents

Using [README_agents.md](../agents/README_agents.md) as the interaction map, identify every agent this new agent must communicate with.

For each interaction, document direction, trigger, and payload:

| Direction | Agent | Trigger | Payload sent | Expected response |
|-----------|-------|---------|-------------|-------------------|
| calls →   | Agent X | when condition Y occurs | what data is passed | what is returned |
| receives ← | Agent Z | when event W fires | what arrives | what action is taken |

Also document failure paths: what happens if a called agent does not respond or rejects the request.

---

## Step 4 — Draft the agent using the 9-section template

Write the complete agent definition. All 9 sections are **required** — the Reviewer auto-rejects any agent with a missing or vague section.

1. **Role** — one sentence, no ambiguity
2. **Responsibilities** — bulleted list; each item is a concrete action, not a vague goal
3. **Trigger** — the exact event or condition that activates this agent
4. **Inputs** — what data this agent receives, and from which agent or source
5. **Outputs** — what this agent produces, and where it goes
6. **Decision Logic** — conditional rules in `if X → then Y` format; cover the main branches
7. **Interactions** — the full table from Step 3
8. **Rules** — hard constraints (what this agent must never do)
9. **Evolution Responsibilities** — how this agent contributes to system improvement over time

---

## Step 5 — Model subagent usage

For every agent this new agent calls (from Step 3), write an explicit subagent contract:

```
Subagent: <agent-name>
Called when: <triggering condition>
Input passed: <exact payload or data structure>
Expected output: <what is returned>
On rejection / failure: <fallback behavior>
```

If this agent does not call any subagents, state that explicitly and justify why.

---

## Self-check before finalizing

Before outputting the final agent file, verify every item:

- [ ] All 9 sections are present and non-vague
- [ ] No capability duplicates any entry in the Capability Registry
- [ ] Every interaction from Step 3 appears in the Interactions section
- [ ] Every subagent call has a documented failure path
- [ ] The Role is narrow enough to pass the Coordinator's 30% overlap check

---

## Output

Produce the complete agent file, ready to save as `<agent-name>.agent.md` in `.github/agents/`.

Then list which agents must be notified of this creation, so Knowledge Sync can broadcast the activation.
