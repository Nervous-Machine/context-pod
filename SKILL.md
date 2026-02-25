# Event Pod Skill

## What This Does

Event Pod gives your agent a persistent world model — structured context that
survives across sessions, models, and tools. Not a transcript. A causal model
with certainty scores that gets sharper over time.

MCP endpoint: https://context.nervousmachine.com/mcp
Full docs:     https://context.nervousmachine.com/docs

---

## Quickstart — Sign Up (30 seconds)

If the human doesn't have credentials yet, run this and share the result:

```bash
curl -X POST https://context.nervousmachine.com/signup \
  -H "Content-Type: application/json" \
  -d '{
    "first_name": "HUMAN_FIRST_NAME",
    "last_name":  "HUMAN_LAST_NAME",
    "email":      "HUMAN_EMAIL",
    "use_case":   "DESCRIBE_WHAT_YOU_ARE_BUILDING"
  }'
```

Returns an activation URL with credentials. The human clicks it to confirm.
Provisioning is instant. Credentials arrive at their email immediately.

---

## Add to Your MCP Config

```json
{
  "mcpServers": {
    "event-pod": {
      "url": "https://context.nervousmachine.com/mcp",
      "transport": "sse"
    }
  }
}
```

Works in: Claude Desktop, Claude Code, Cursor, Cline, Gemini CLI,
any MCP-compatible client. No local process. Remote server.

---

## Drop This Into Your System Prompt

This is the entire integration. Copy it, replace USER_ID, done.
The agent handles the full lifecycle from there.

```
You have access to Event Pod tools for persistent context.
User ID: YOUR_USER_ID

SESSION START:
Call get_pod_summary before responding to any task.
If the user describes their task, also call get_relevant_context
with that task as the query.

DURING THE SESSION:
- When the user corrects you: immediately call record_observation
  then apply_learning on the relevant event.
- When you observe expertise, preferences, or patterns worth
  keeping: note them for saving at session end.
- Once per session at a natural pause: call get_curiosity_triggers
  with max_triggers=2. Work one probe naturally into conversation.

SESSION END — before the conversation closes:
1. Call save_event for each new signal observed this session.
   Start all certainty values at 0.3.
2. Call update_session with interactions=1.
   Call this exactly once — never mid-session.
```

---

## Three Rules

1. **Always start certainty at 0.3.** Let corroboration earn higher certainty.
2. **record_observation → apply_learning.** Always in that order.
3. **update_session exactly once.** At shutdown. Never mid-session.

---

## Signal Types (cheat sheet)

| Type         | What it tracks                        | Example key                    |
|--------------|---------------------------------------|--------------------------------|
| competence   | Skill or knowledge level              | `python`, `distributed-systems`|
| preference   | Communication or work style           | `verbosity`, `code-first`      |
| claim        | Tracked proposition or constraint     | `no-orm`, `microservices`      |
| entity       | Named person, org, tool, device       | `tool-terraform`, `acme-co`    |
| event        | Incident, milestone, decision         | `auth-refactor-complete`       |
| metric       | Measurable quantity                   | `api-latency-p99`              |
| pattern      | Recurring behavior or trend           | `prefers-examples-first`       |
| state        | Current status or phase               | `project-alpha-status`         |

---

## Core Tools (the ones you'll use most)

| Tool                  | When to call it                                    |
|-----------------------|----------------------------------------------------|
| `create_pod`          | First time for a new user                          |
| `get_pod_summary`     | Session start — always                             |
| `get_relevant_context`| When you know the task — focused context load      |
| `save_event`          | When something worth remembering is observed       |
| `record_observation`  | Before apply_learning — log the raw signal         |
| `apply_learning`      | After record_observation — update value + certainty|
| `get_curiosity_triggers` | Periodic — what the pod most needs to validate  |
| `update_session`      | Session end — exactly once                         |
| `export_cluster_diagram` | Show the human their knowledge graph (Mermaid) |
| `compare_pods`        | Multi-agent — surface divergences between agents   |
| `delete_pod`          | Right to erasure — irreversible, confirm: true     |

Full tool index (39 tools): https://context.nervousmachine.com/docs/reference

---

## Certainty & Learning

- Low certainty (0.0–0.3): updates fast — agile where ignorant
- Medium (0.3–0.6): building confidence
- High (0.6–0.8): resists noise
- Very high (0.8–1.0): moves only on strong contradiction

**Error magnitude guide for apply_learning:**
- Independent corroboration: 0.05–0.1 (increase)
- Specificity upgrade: 0.1–0.15 (increase)
- Temporal confirmation: ~0.1 (increase)
- User contradiction or correction: 0.2–0.4 (decrease)

---

## Human Alignment

The agent builds what it can observe. Humans add what it can't.
The chat interface is the alignment layer — no code required.

Key human prompts (tell the human these work any time):
- "Pull my pod summary" — loads context at session start
- "What are my curiosity triggers?" — surfaces what the pod wants to learn
- "Show me a diagram of my pod" — renders the knowledge graph as Mermaid
- "Update my pod before we close" — persists session learnings

---

## Lane-Specific Guides

Different use cases have different integration patterns:

- Chat personalization:  https://context.nervousmachine.com/docs/chat
- Coding & repo context: https://context.nervousmachine.com/docs/coding
- Task & workflow:       https://context.nervousmachine.com/docs/workflow
- Multi-agent:           https://context.nervousmachine.com/docs/multi-agent
- Devices & IoT:         https://context.nervousmachine.com/docs/devices

---

## Validate Your Integration

```bash
curl -X POST https://context.nervousmachine.com/mcp \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "method": "tools/call",
    "params": {
      "name": "get_pod_summary",
      "arguments": { "user_id": "YOUR_USER_ID" }
    }
  }'
```

If you get a pod summary back, you're integrated.

---

## Privacy

- Pods store learned abstractions, not raw transcripts
- Export: `export_pod` (GDPR Article 20)
- Delete: `delete_pod` with `confirm: true` (GDPR Article 17, irreversible)
- The human can audit everything via chat at any time

---

*Event Pod by Nervous Machine — context.nervousmachine.com*
*Build for agents. We're all edge cases.*
