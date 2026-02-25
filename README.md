# Event Pod

**Agent context. Human alignment.**

Agents build their world model autonomously -- observing, learning, updating certainty across every session. Humans can join at any time: audit, revise, and collaborate in plain language. A chat is all it takes.

- MCP endpoint: `https://context.nervousmachine.com/mcp`
- Full docs: [https://context.nervousmachine.com/docs](https://context.nervousmachine.com/docs)
- Landing page: [https://context.nervousmachine.com](https://context.nervousmachine.com)
- Substack: [https://nervousmachine.substack.com](https://nervousmachine.substack.com)

**Agents build autonomously. Humans align naturally.**

Every lane writes to the same pod structure. A coding agent, a workflow agent, and a personal chat agent can all share context -- or each maintain their own. Either way, the Chat lane is the alignment interface for all of them. One conversation to audit what any agent learned, correct what drifted, and add what no agent can observe on its own. Most people start with one lane. The pods compose naturally as you add more.

---

## Table of Contents

- [Quickstart](#quickstart)
- [Chat -- The Alignment Interface](#chat----the-alignment-interface)
- [Agent World Model](#agent-world-model)
- [Coding & Repo Context](#coding--repo-context)
- [Task & Workflow Automation](#task--workflow-automation)
- [Multi-Agent & Cross-Pod](#multi-agent--cross-pod)
- [Devices & Ecosystems](#devices--ecosystems)
- [Full Reference](#full-reference)

---

## Quickstart

### Sign Up (30 seconds)

If you don't have credentials yet, run this:

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

Returns an activation URL with credentials. Click it to confirm. Provisioning is instant. Credentials arrive at your email immediately.

### Add to Your MCP Config

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

Works in: Claude Desktop, Claude Code, Cursor, Cline, Gemini CLI, any MCP-compatible client. No local process. Remote server.

### Drop This Into Your System Prompt

This is the entire integration. Copy it, replace `USER_ID`, done. The agent handles the full lifecycle from there.

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

SESSION END -- before the conversation closes:
1. Call save_event for each new signal observed this session.
   Start all certainty values at 0.3.
2. Call update_session with interactions=1.
   Call this exactly once -- never mid-session.
```

### Three Rules

1. **Always start certainty at 0.3.** Let corroboration earn higher certainty.
2. **record_observation -> apply_learning.** Always in that order.
3. **update_session exactly once.** At shutdown. Never mid-session.

### Validate Your Integration

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

## Chat -- The Alignment Interface

Chat is how any human aligns any agent -- regardless of lane. Your coding agent learned the wrong constraint. Your workflow pod has a stale approval chain. Your device flagged something it doesn't understand. Chat is how you fix all of it, in plain language, no tools required.

**Agents do:** Build context autonomously. Coding agents, workflow agents, device agents -- all building pods across every session.

**You do:** Align with four prompts. Pull summary. Check curiosity. Save. Update. That's the entire manual layer -- everything else is automatic.

> **Honest note on how this works in chat.** Claude doesn't automatically know to look at your pod -- you need to ask. The four prompts below are small overhead for what you get in return. Developers using other lanes program these into their workflows so it's invisible. In chat, you say them yourself. Once it's part of your session habit, it takes about 10 seconds total.

### Connect in 60 Seconds

Event Pod is a remote MCP server. Add it once through your AI client's integrations UI -- no terminal, no config files.

1. **Open Settings in Claude.ai** -- Click your profile icon (bottom-left), then Settings -> Integrations.
2. **Add Event Pod** -- Click Add Integration and paste:

```
https://context.nervousmachine.com/mcp
```

3. **Start a chat and create your pod** -- Open a new conversation. Pick a short ID -- your first name is perfect. Use one of the creation prompts below.

### The Four-Prompt Ritual

This is the entire manual layer. Four prompts, about 10 seconds of overhead, unlocks everything the pod knows.

| # | When | What to say |
|---|------|-------------|
| 1 | First ever session | Create a pod called `[name]` from this conversation / document / data |
| 2 | Every session start | Pull my pod summary for `[name]` |
| 3 | Mid-session check-in | What are my curiosity triggers? |
| 4 | Before closing | Update my pod with what you learned this session |

Everything else -- learning, certainty tracking, contradiction detection, the knowledge graph -- happens inside those calls.

### Create Your Pod

You only do this once. The more context you seed it with upfront, the faster it becomes useful.

**From a document**

Upload a resume, project brief, bio, or knowledge dump. Claude extracts signals and seeds the pod from it -- the fastest path to a useful pod from day one.

> Please create a pod with user ID 'heidi' and seed it from this document. Extract expertise, preferences, interests, and any active projects you can find.

**From a conversation**

> Based on what we've discussed so far, please create a pod called 'heidi' and save what you've learned about me.

**From scratch**

> I don't have a pod yet. Please create one with user ID 'heidi'. I'll tell you about myself to seed it.

**From pasted notes**

> Please create a pod called 'heidi' and seed it from these notes: [paste a bio, skill list, project description, or stream of consciousness -- anything works].

**Seed generously.** The pod starts with low certainty on everything -- that's intentional, it means it updates fast. But a richer seed means faster convergence. A paragraph about your work, a couple of preferences, and one active project is enough to make the first real session noticeably better.

### Visualize Your Pod

After creating or loading your pod, ask Claude to draw it. The `export_cluster_diagram` tool renders your knowledge graph as a Mermaid diagram -- clusters, connections, and the structure of what's known, visible at a glance. This is the fastest way to spot misalignments that a text summary would hide.

**Full pod diagram:**

> Show me a diagram of my pod. I want to see how everything connects.

**Focused view -- centered on a topic:**

> Show me a diagram of my pod centered on my Python expertise. What connects to it?

**After an update:**

> We just updated my pod. Show me an updated diagram so I can see what was added.

The diagram is your alignment view. Wrong connections, nodes that shouldn't be there, obvious gaps -- the diagram surfaces all of it. If something looks off, just say so. That correction becomes a signal that sharpens the whole cluster.

### Pull Pod Summary

Use this at the start of every session. It's lightweight -- loads the most important signals with their glosses and freshness, without pulling the full pod. Fast enough that it doesn't disrupt the session flow.

> Pull my pod summary for heidi.

Or for a task-specific start:

> I'm working on [describe task]. Pull relevant context from my pod (heidi) before we start.

**Summary vs. full load.** Pod summary is the right default -- fast, focused, gives Claude what it needs. Only ask for everything ("load my full pod") when you're doing a deep audit or something feels wrong.

### Curiosity Triggers

Curiosity triggers are questions your pod has about itself -- signals that are uncertain, stale, or haven't been validated yet. Checking them periodically is how the pod gets sharper over time. You don't have to answer all of them -- even one or two per session compounds significantly.

> What are my curiosity triggers? What does my pod most want to learn about me?

**Curiosity across pods:**

If you have multiple pods -- personal, coding, workflow, device -- you can check curiosity across all of them in a single chat session. This is one of the most powerful things the chat interface enables that no individual agent does on its own.

> Pull summaries for my pods: heidi, heidi-coding, and heidi-workflow. Then tell me the top curiosity triggers across all three -- what's most uncertain or unresolved?

> Compare my personal pod (heidi) with my coding pod (heidi-coding). Are there things one knows that would be useful to share with the other?

### Update Your Pod

If the pod is already loaded, Claude will often update it naturally as things come up -- you don't always have to ask. But don't rely on it. A quick prompt before closing ensures nothing slips.

**Session wrap-up:**

> Before we close, update my pod with anything new you learned about me this session.

**Save something specific:**

> Please save to my pod that I'm leading a team of 5 engineers and we work primarily in Go.

**Correct something wrong:**

> My pod says I prefer concise answers -- that's not right anymore. I want detailed explanations with examples now. Please update that.

> **Don't skip the wrap-up.** Context learned during a session only persists if it's explicitly saved. The wrap-up prompt takes 5 seconds. Skip it and the session's learning is gone when the chat closes.

### Align Any Agent via Chat

Chat isn't just for personal context -- it's the correction layer for every agent you run. They build autonomously. You reach in and fix what drifted, in plain language.

| Lane | What you align | Example prompt |
|------|---------------|----------------|
| Coding | Wrong architectural constraints, outdated stack, rejected approaches | "My coding pod thinks we use gRPC -- we dropped that 6 months ago. Please correct it." |
| Workflow | Stale approval chains, deprecated integrations, changed policies | "The Stripe backup is deprecated. Remove it from the workflow pod." |
| Multi-agent | Divergences between agents, handoff gaps, conflicting world models | "My research and fix agents disagree on the JWT approach. Show me both and help me resolve it." |
| Devices | Unexplained anomalies, sensor offsets, intent and priority setting | "That temperature spike on the 15th was a planned heat test. Mark the cause in the device pod." |

### FAQ

**Why doesn't Claude load my pod automatically?**

MCP tools are available to Claude, but it doesn't know which to call without being asked. Automatic pod loading at session start is on the roadmap. For now, "pull my pod summary for [name]" is the trigger -- 2 seconds and it becomes habit fast.

**Why summary instead of full load?**

`get_pod_summary` is fast and loads the signals Claude needs to calibrate. The full pod includes everything -- low-certainty signals, raw metadata, the whole graph. You rarely need all of that for a normal session. Use summary by default, full load for audits.

**Does it work on the free Claude.ai plan?**

Yes. MCP integrations are available across all Claude.ai plans.

**Can I use the same pod with Gemini or Cursor?**

Yes. Event Pod is model-agnostic. Any MCP-compatible client connects to the same pod. Your context follows you -- not locked to Claude.

**How is this different from Claude's built-in memory?**

Claude's memory stores conversation snippets. Event Pod builds a structured model with certainty scores, semantic connections, and curiosity-driven learning -- and it works across every tool and model you use. It's not storage, it's a model that gets sharper over time.

---

## Agent World Model

The agent is the primary author of its own brain. You're the editor. This section covers the core concepts and the two ways to use Event Pod -- autonomous mode where the agent builds its own context, and controlled mode where you define exactly what gets saved.

**Autonomous:** Agent builds its own brain. One system prompt. The agent decides what to observe, save, and connect. Human aligns via chat when needed.

**Controlled:** You define what gets saved. Explicit tool calls. Precise events, relationships, and resources. Use when you need precision over automation.

### Overview

Every agent session starts from zero unless you give it persistent context. Event Pod is the persistence layer -- a structured model of what the agent has learned, with certainty scores that reflect how confident it is in each signal.

Event Pod is an MCP server your agent connects to over HTTP. Your agent calls tools -- `save_event`, `apply_learning`, `get_pod_summary` -- and Event Pod handles persistence. The pod lives on the server, not in the agent's context window, so it survives across sessions, models, and tools. Two things connect to every pod simultaneously: the agent (via tool calls) and the human (via chat). Any MCP-compatible client works -- Claude, Gemini, Cursor, custom agents all connect to the same pod.

The key insight: **the agent doesn't need you to write its brain for it.** Given the right system prompt, it will observe what's relevant, save it, and build the knowledge graph on its own. The human role is alignment -- auditing what the agent learned, correcting drift, and adding context the agent can't observe.

> **One agent, multiple lanes.** Lanes describe *what kind of context* you're building -- not separate systems. A real coding agent draws from the coding lane for codebase knowledge, the workflow lane for deployment patterns, and the chat lane for user preferences -- all signals in one pod, all retrievable together via `get_relevant_context`. Mix them freely.

### Two Modes

| | Autonomous | Controlled |
|---|-----------|------------|
| **Setup** | Add system prompt snippet -- done | Wire explicit tool calls into lifecycle hooks |
| **Who decides what to save** | The agent | You |
| **Best for** | Most use cases -- let it learn naturally | When you need specific events, relationships, or precision |
| **Human role** | Align via chat -- audit, correct, extend | Design the schema + align via chat |
| **Code required** | No -- system prompt only | Yes -- Python/JS/curl |

Most agents start in autonomous mode and add controlled saves for specific high-value signals as they mature. You don't have to choose one -- they compose.

### Autonomous Mode

Drop this into your system prompt. The agent handles the rest -- loading context at session start, observing signals mid-session, surfacing curiosity triggers, and persisting what it learned at the end.

```
## Event Pod -- Context Instructions

You have access to Event Pod tools for persistent context.
User ID: {USER_ID}

SESSION START:
Call get_pod_summary before responding to any task.
If the user describes their task, also call get_relevant_context
with that task as the query.

DURING THE SESSION:
- When the user corrects you: immediately call record_observation
  then apply_learning on the relevant event.
- When you observe expertise, preferences, or patterns worth
  remembering: note them for saving at session end.
- Once per session at a natural pause: call get_curiosity_triggers
  with max_triggers=2. Weave one probe naturally into conversation --
  don't list them as questions.

SESSION END -- before the conversation closes:
1. Call save_event for each new signal observed this session.
   Start all certainty values at 0.3.
2. Call update_session with interactions=1.
   Call this exactly once -- never mid-session.

IMPORTANT:
- You decide what's worth saving. Not every mention -- sustained
  patterns, explicit corrections, and clear preferences only.
- The human can audit and correct anything via chat at any time.
```

That's it for most use cases. The agent will build its own model from sessions. The human aligns via chat. The pod compounds over time without any additional code.

### Controlled Mode

Use controlled saves when you want to define specific events, build explicit relationships between signals, or attach resources to context. Controlled and autonomous compose -- add explicit saves on top of the system prompt for signals you care about precisely.

**Save a specific event:**

```python
await call_pod("save_event", {
    "user_id": "heidi",
    "key": "python",
    "signal_type": "competence",
    "value": 0.85,
    "certainty": 0.3,       # always start low
    "gloss": "Strong Python -- corrects examples, uses advanced patterns",
    "validation_type": "logic",
    "meta": { "observed_via": "code_review" }
})
```

**Link two events semantically:**

```python
await call_pod("link_events", {
    "user_id": "heidi",
    "source_key": "python",
    "source_signal_type": "competence",
    "target_key": "data-science",
    "target_signal_type": "competence",
    "relationship": "ENABLES",
    "note": "Python expertise enables data science work"
})
```

**Attach a resource to an event:**

```python
await call_pod("link_resource", {
    "user_id": "heidi",
    "key": "python",
    "signal_type": "competence",
    "resource_type": "project",
    "resource": {
        "name": "acme/backend",
        "project_id": "acme/backend",
        "note": "Primary Python codebase"
    }
})
```

### Lifecycle Hooks (Controlled Mode)

If you're wiring explicit code rather than relying on the system prompt, these four functions cover the full lifecycle. Each is drop-in Python -- replace `call_pod` with your MCP client's tool call method.

#### Init -- session start

```python
async def agent_init(user_id: str, task: str = None):
    summary = await call_pod("get_pod_summary", {
        "user_id": user_id,
        "include_glosses": True,
        "include_freshness": True
    })
    context = None
    if task:
        context = await call_pod("get_relevant_context", {
            "user_id": user_id,
            "query": task,
            "max_results": 10,
            "include_resources": True
        })
    return summary, context
```

#### Observe -- mid-session signal

```python
async def agent_observe(user_id, session_id, key, signal_type,
                        observation_type, direction, raw_content, error_magnitude):
    # Always: record first, then apply
    await call_pod("record_observation", {
        "user_id": user_id, "session_id": session_id,
        "key": key, "event_signal_type": signal_type,
        "signal_type": observation_type,
        "signal_data": { "direction": direction, "raw_content": raw_content }
    })
    await call_pod("apply_learning", {
        "user_id": user_id, "key": key, "signal_type": signal_type,
        "error_direction": direction, "error_magnitude": error_magnitude
    })
```

**Error magnitude reference:**

| What happened | Magnitude | Direction |
|---------------|-----------|-----------|
| Independent corroboration | 0.05--0.1 | increase |
| Same-source repetition | 0.02--0.05 | increase |
| Specificity upgrade | 0.1--0.15 | increase |
| Temporal confirmation | ~0.1 | increase |
| User contradiction or correction | 0.2--0.4 | decrease |

#### Curiosity -- periodic probe

```python
async def agent_curiosity(user_id: str, max_triggers: int = 3):
    return await call_pod("get_curiosity_triggers", {
        "user_id": user_id,
        "max_triggers": max_triggers,
        "include_probes": True
    })
    # Use triggers[n].suggested_probes as conversation starters
    # Rephrase naturally -- never read them verbatim
```

#### Shutdown -- session end

```python
async def agent_shutdown(user_id: str, new_events: list):
    for event in new_events:
        await call_pod("save_event", {
            "user_id": user_id,
            "key": event["key"],
            "signal_type": event["signal_type"],
            "value": event["value"],
            "certainty": event.get("certainty", 0.3),
            "gloss": event.get("gloss", ""),
            "meta": event.get("meta", {})
        })
    # Exactly once -- never mid-session
    await call_pod("update_session", {
        "user_id": user_id, "interactions": 1
    })
```

### Signal Types

| Type | What it tracks | Example key |
|------|---------------|-------------|
| competence | Skill or knowledge level | `python`, `distributed-systems` |
| preference | Communication or work style | `verbosity`, `code-before-explanation` |
| claim | Tracked proposition or belief | `microservices-preferred` |
| entity | Named person, org, or tool | `tool-terraform`, `company-acme` |
| event | Incident, milestone, decision | `auth-refactor-completed` |
| metric | Measurable quantity | `api-latency-p99` |
| pattern | Recurring behavior or trend | `prefers-examples-over-theory` |
| state | Current status or phase | `project-alpha-status` |

### Certainty & Learning

Every event has a `certainty` score between 0 and 1. The system is **agile where ignorant and stable where certain** -- learning rate adapts automatically. You set direction and magnitude; the system handles the rate.

| Range | Meaning | Behavior |
|-------|---------|----------|
| 0.0 -- 0.3 | Low -- new or weakly supported | Updates fast, open to revision |
| 0.3 -- 0.6 | Medium -- building confidence | Moderate update rate |
| 0.6 -- 0.8 | High -- well-supported | Resists noise |
| 0.8 -- 1.0 | Very high -- established | Moves only on strong contradiction |

**Always start new events at 0.3.** Don't manually assign high certainty -- let corroboration earn it.

### Human Alignment

The agent builds what it can observe. Humans add what it can't -- institutional context, intent, decisions made outside the session. The chat interface is the alignment layer for both modes. No code required on the human's end.

**Design your agent to invite alignment.** Surface curiosity triggers as natural conversation. Show what was saved at session end. Offer the Mermaid diagram periodically so humans can spot misalignments visually. The faster humans correct, the faster certainty converges on truth.

---

## Coding & Repo Context

Your coding agent maps your stack, team conventions, failure patterns, and architectural decisions -- on its own. It stops suggesting things you've already ruled out. It knows why a constraint exists. It connects failures to the architectural decisions that caused them.

**Autonomous:** Agent builds the codebase map. Observes stack, conventions, failures, and constraints from every session. No schema to define upfront.

**Controlled:** You define high-value signals. Explicit repo context, linked failure patterns, attached docs and ADRs. Use when precision matters.

### Overview

A coding agent without persistent context rediscovers the same things every session -- asks about the stack, misses the constraint that rules out a whole class of solutions, suggests an approach the team tried and abandoned six months ago. Event Pod fixes this by letting the agent accumulate a model of the codebase over time.

The agent is the primary author. It observes what matters from real sessions -- not a synthetic schema you defined upfront. Human developers stay in the loop through chat, adding the institutional context the agent can't observe: decisions made in Slack, constraints from contracts, the reason a library got banned.

### Autonomous Mode -- Coding System Prompt

Add this to your coding agent's system prompt -- in Claude Code, Cursor, Cline, or any MCP-compatible IDE agent. The agent builds its own codebase model from sessions. You align it via chat when something drifts.

```
## Event Pod -- Coding Context

You have access to Event Pod tools. User ID: {USER_ID}

SESSION START:
1. Call get_pod_summary for this user.
2. Call get_repo_context for repo: {REPO_NAME}.
3. If a specific task is described, call get_relevant_context
   with that task as the query.

DURING THE SESSION -- observe and save:
- Stack, languages, frameworks encountered -> competence events
- Architectural rules and constraints -> claim events
- Approaches that failed or were rejected -> event signals
  (always include WHY in the gloss and a revisit_when in meta)
- Recurring failure patterns -> pattern events
- Team conventions and process rules -> preference events
- Current work in progress -> state events

When the user corrects your understanding of the codebase:
immediately call record_observation then apply_learning.

SESSION END:
1. Save all new signals observed (save_event for each).
2. Update repo context if stack or constraints changed
   (save_repo_context).
3. Call update_session with interactions=1 -- exactly once.
```

The agent decides what's worth saving. It won't save every mention -- just sustained patterns, explicit constraints, and confirmed failures. That's intentional. A noisy pod is harder to align than an incomplete one.

### Controlled Mode

Use controlled saves when you want to explicitly define what the agent knows -- seeding a new agent, encoding decisions that weren't made in a session, or building precise relationships between signals. Composes with autonomous mode.

### Repo Context

`save_repo_context` persists codebase metadata that survives across all sessions on the same repo. This is shared knowledge about the code itself -- separate from user-level signals.

**Seed repo context explicitly:**

```python
await call_pod("save_repo_context", {
    "user_id": user_id,
    "repo_name": "acme/backend",
    "metadata": {
        "stack": ["Go 1.22", "PostgreSQL 15", "Redis", "Temporal"],
        "architecture": "Modular monolith, domain-driven",
        "constraints": [
            "No ORM -- raw SQL only",
            "No global state",
            "All DB calls use context cancellation"
        ],
        "test_strategy": "Table-driven unit tests, integration tests in /testdata",
        "deploy": "GitHub Actions -> staging -> manual prod promotion",
        "conventions": [
            "Conventional commits",
            "PR review required before merge",
            "No force-push to main"
        ]
    }
})
```

**Load at session start:**

```python
repo = await call_pod("get_repo_context", {
    "user_id": user_id,
    "repo_name": "acme/backend"
})
```

### Failure Memory

Rejected approaches are the most valuable thing a coding agent can remember -- and the first thing a stateless agent will suggest again next session. Save them with enough context to be useful months later.

```python
await call_pod("save_event", {
    "user_id": user_id,
    "key": "grpc-streaming-rejected",
    "signal_type": "event",
    "value": 1.0,
    "certainty": 0.9,
    "gloss": "Tried gRPC streaming for real-time updates. Reverted: complexity too high for current team size. REST + polling is sufficient at current scale.",
    "meta": {
        "decided_by": "team",
        "date": "2025-11",
        "revisit_when": "team > 10 engineers OR > 10k concurrent users"
    }
})
```

**Always include `revisit_when`.** Turns "we tried that" into "we tried that -- and here's when it's worth trying again." The agent can surface these proactively as conditions change.

### Linking Failures to Constraints

The real power of controlled mode: connecting signals semantically. A failure pattern that led to a constraint. A constraint that enabled a decision. These links let the agent explain *why* a rule exists -- not just what it is.

**Connect a failure pattern to the constraint it caused:**

```python
# Save the failure pattern
await call_pod("save_event", {
    "user_id": user_id,
    "key": "connection-pool-exhaustion",
    "signal_type": "pattern",
    "value": 0.9,
    "certainty": 0.85,
    "gloss": "Pool exhaustion under load -- seen 3x in prod. Always traced to unbounded concurrent queries."
})

# Save the architectural constraint it produced
await call_pod("save_event", {
    "user_id": user_id,
    "key": "max-db-connections-constraint",
    "signal_type": "claim",
    "value": 1.0,
    "certainty": 0.95,
    "gloss": "Hard cap: 20 DB connections per service instance. Non-negotiable.",
    "meta": { "set_by": "ops", "date": "2025-08" }
})

# Link them -- failure ENABLES constraint (caused it to exist)
await call_pod("link_events", {
    "user_id": user_id,
    "source_key": "connection-pool-exhaustion",
    "source_signal_type": "pattern",
    "target_key": "max-db-connections-constraint",
    "target_signal_type": "claim",
    "relationship": "ENABLES",
    "note": "Pool exhaustion incidents caused this constraint to be imposed"
})
```

**Attach an ADR or design doc to the constraint:**

```python
await call_pod("link_resource", {
    "user_id": user_id,
    "key": "max-db-connections-constraint",
    "signal_type": "claim",
    "resource_type": "url",
    "resource": {
        "name": "ADR-012: DB connection limits",
        "url": "https://github.com/acme/backend/blob/main/docs/adr/012-db-connections.md",
        "note": "Full decision record with context and alternatives considered"
    }
})
```

**Linked resources surface automatically.** When the agent calls `get_relevant_context` on anything related to DB connections, the ADR link comes with it. The agent can cite the decision record without being told it exists.

### Human Alignment -- Coding

The agent observes code -- but engineers hold the institutional knowledge. Decisions made in Slack, approaches rejected in a whiteboard session two quarters ago, the reason a particular library got banned from the codebase -- none of that is observable from sessions alone. Developers add it via chat.

**Adding context the agent can't see:**

> Please save to my coding pod that we decided against GraphQL last year -- the team found the N+1 problem wasn't worth the flexibility at our scale. Add that to the acme/backend repo context.

**Auditing the codebase map:**

> What does my pod know about the acme/backend architecture? Show me the constraints it's tracking.

**Seeing the knowledge graph:**

> Show me a diagram of the coding pod centered on the DB connection constraint. What connects to it?

**Correcting drift:**

> My pod says we use conventional commits but we switched back to freeform messages six months ago. Please update that.

---

## Task & Workflow Automation

A workflow agent that knows its operational environment handles tasks a stateless agent can't touch. Which tools connect to what. Where things break. What approvals are needed. The agent builds this model from runs -- humans add the institutional context only they can provide.

**Autonomous:** Agent maps the environment. Learns integrations, failure modes, and patterns from every run. Operational model builds itself.

**Controlled:** You seed the constraints. Approval chains, permissions, service maps. Use when precision and auditability matter.

### Overview

Workflow agents lose time to context loss -- rediscovering what integrations are live, what broke last time, which steps need approval. Event Pod gives the workflow agent a persistent operational model that accumulates rather than resets. The agent builds it from runs; operators seed the constraints they need enforced from day one.

### Autonomous Mode -- Workflow System Prompt

```
## Event Pod -- Workflow Context

You have access to Event Pod tools. User ID: {USER_ID}

SESSION START:
1. Call get_pod_summary for this user.
2. Call get_service_context to check integration status.
3. Call get_agent_context for agent_id: {AGENT_ID},
   context_type: task_history to review recent runs.

DURING RUNS -- observe and save:
- New tool or service integrations -> entity events
  (include auth type, permissions, and any write restrictions)
- Approval requirements discovered -> claim events
  (include threshold, approver, and SLA in meta)
- Recurring failures or edge cases -> pattern events
  (include workaround and first-seen in meta)
- Current service health issues -> save_service_context

AFTER EACH RUN:
1. Call save_agent_context with context_type: task_history.
   Include what completed, what failed, and why.
2. Save any new patterns or constraints discovered.
3. Call update_session with interactions=1 -- exactly once.
```

**Approval chains are the most important thing to get right.** If the agent discovers an approval requirement mid-run, it should save it immediately -- not wait for shutdown. The next run will respect it from the start.

### Controlled Mode

Use controlled saves to seed the operational model before the first run -- so the agent starts with known constraints rather than discovering them the hard way.

### Tool Integrations

**Seed integration map explicitly:**

```python
await call_pod("save_event", {
    "user_id": user_id,
    "key": "salesforce-crm",
    "signal_type": "entity",
    "value": 1.0,
    "certainty": 0.95,
    "gloss": "Salesforce connected via OAuth. Agent is read-only. All writes require human approval.",
    "meta": {
        "auth_type": "oauth",
        "agent_permissions": "read",
        "write_approval_required": True,
        "approver": "ops-lead",
        "approval_sla_hours": 4
    }
})
```

### Approval Chains

Approval requirements are claims -- absolute rules the agent must respect. Seed them explicitly so the agent never has to discover them by violating them.

**Seed approval rules:**

```python
await call_pod("save_event", {
    "user_id": user_id,
    "key": "payment-approval-required",
    "signal_type": "claim",
    "value": 1.0,
    "certainty": 0.99,  # high -- this is a hard rule
    "gloss": "Any payment over $500 requires finance approval. Under $500 is autonomous.",
    "meta": {
        "threshold_usd": 500,
        "approver": "finance@acme.com",
        "sla_hours": 4,
        "escalation": "cfo@acme.com if no response in 4h"
    }
})
```

**Link approval chain to integration:**

```python
await call_pod("link_events", {
    "user_id": user_id,
    "source_key": "payment-approval-required",
    "source_signal_type": "claim",
    "target_key": "salesforce-crm",
    "target_signal_type": "entity",
    "relationship": "PART_OF",
    "note": "Payment approval rule governs all Salesforce write operations"
})
```

### Service Context

Track the health of services the workflow depends on. The agent checks this at session start and can skip or reroute steps when a dependency is degraded.

```python
await call_pod("save_service_context", {
    "user_id": user_id,
    "service_name": "payment-gateway",
    "status_data": {
        "status": "degraded",
        "last_checked": "2025-11-15T14:32:00Z",
        "known_issues": ["High latency on EU endpoints"],
        "fallback": "stripe-backup"
    }
})
```

### Task History

Save what each run did, decided, and encountered. The next run picks up with full context of what came before -- including failures and the reasons behind them.

```python
await call_pod("save_agent_context", {
    "user_id": user_id,
    "agent_id": "invoice-workflow",
    "context_type": "task_history",
    "data": {
        "task": "Process November invoices",
        "completed": 47,
        "failed": 3,
        "failure_reason": "Vendor ID mismatch on EU invoices (legacy pre-2023 format)",
        "flagged_for_review": ["INV-2041", "INV-2089"],
        "duration_minutes": 12
    }
})
```

### Failure Patterns

When a run fails in a recurring way, save it as a pattern. The agent learns to anticipate it -- checking preconditions, choosing fallbacks, or surfacing it to a human before it cascades.

```python
await call_pod("save_event", {
    "user_id": user_id,
    "key": "eu-invoice-vendor-mismatch",
    "signal_type": "pattern",
    "value": 0.8,
    "certainty": 0.75,
    "gloss": "EU invoices with pre-2023 vendor ID format fail matching. Route to manual review.",
    "meta": {
        "first_seen": "2025-09",
        "frequency": "~5% of EU invoices",
        "workaround": "manual-review queue",
        "fix_status": "backlog -- vendor migration Q1 2026"
    }
})
```

### Human Alignment -- Workflow

Operational context lives in people's heads -- approval chains from six months ago, vendor quirks learned through painful experience, integration constraints from contracts. The agent builds what it encounters. Operators add what it can't observe.

**Adding undocumented constraints:**

> Please save to my workflow pod that the legal team reviews all new vendor contracts before any integration goes live. That's not written down anywhere but it's always required.

**Updating stale state:**

> The Stripe backup integration was deprecated last month. Please remove it from the service context and update the payment fallback.

**Auditing what the agent knows:**

> What does my workflow pod know about the invoice process? What failure patterns is it tracking? Show me the approval chain constraints.

---

## Multi-Agent & Cross-Pod

Multiple agents, each building its own world model. Pods let them coordinate without a central orchestrator holding everything together -- and chat is how humans step in to resolve what agents can't agree on.

**Agents do:** Build independently. Each agent accumulates its own model. Handoffs carry context forward. Divergences surface naturally.

**Human does:** Resolves divergence. When agents disagree, a human makes the call via chat. Resolution broadcasts to all agents. Everyone benefits.

### Overview

In multi-agent systems, context loss happens at handoff. Agent A does deep research; Agent B starts fresh with none of it. A new agent joins the project three weeks in and has to be briefed from scratch. Event Pod solves both: agents write to pods, read from pods, and a new agent can be seeded from the resolved knowledge of the agents that came before it.

Three coordination patterns cover most cases:

- **Handoff** -- one agent leaves structured context for the next
- **Compare** -- surface where two agents' models diverge; human resolves
- **Seed new agent** -- bootstrap a new agent from resolved context of existing agents

### Autonomous Mode -- Multi-Agent System Prompt

Each agent runs its own system prompt with a distinct `agent_id`. Agents build independently, leave handoffs, and stay namespaced so you can always trace which agent contributed what.

```
## Event Pod -- Multi-Agent Context

You have access to Event Pod tools.
User ID: {USER_ID}
Your agent ID: {AGENT_ID}

SESSION START:
1. Call get_pod_summary for this user.
2. Call get_agent_context for your agent_id to load your
   own task history.
3. Check for handoffs from other agents:
   call get_agent_context for any upstream agent_ids
   with context_type: task_history.

DURING THE SESSION:
- Save what you learn as events on the shared pod.
- When you make a significant decision, call save_agent_context
  with context_type: decision so other agents can trace it.
- If you encounter something that contradicts another agent's
  saved context, note it -- don't silently overwrite it.
  Flag it for human review.

SESSION END:
1. Save a handoff via save_agent_context, context_type: task_history.
   Include: what you did, what you found, what you'd recommend next,
   and what you're handing off to.
2. Save any new events observed.
3. Call update_session with interactions=1 -- exactly once.
```

### Controlled Mode

Use controlled saves for handoffs, comparisons, and seeding -- the moments where precision matters most.

### Agent Handoff

When one agent completes a phase and another picks it up, the handoff is a structured save. The next agent reads it before starting -- no re-briefing, no context loss.

**Agent A -- save handoff:**

```python
await call_pod("save_agent_context", {
    "user_id": user_id,
    "agent_id": "research-agent",
    "context_type": "task_history",
    "data": {
        "task": "Security audit -- auth module",
        "status": "complete",
        "findings": [
            "JWT secret hardcoded in config.go:42",
            "No rate limiting on /login",
            "Session tokens don't expire"
        ],
        "severity_order": ["JWT secret", "session expiry", "rate limiting"],
        "files_examined": ["auth/", "middleware/auth.go"],
        "handed_off_to": "fix-agent",
        "recommended_first": "JWT secret -- highest severity, easiest fix"
    }
})
```

**Agent B -- read handoff at session start:**

```python
handoff = await call_pod("get_agent_context", {
    "user_id": user_id,
    "agent_id": "research-agent",  # read from the agent that handed off
    "context_type": "task_history"
})
```

### Compare & Diverge

When two agents build separate models of the same environment, `compare_pods` finds where they agree, where they diverge, and what each knows that the other doesn't. Divergences are signals -- they mark human decisions waiting to happen.

```python
comparison = await call_pod("compare_pods", {
    "pod_a": "research-agent-pod",
    "pod_b": "fix-agent-pod",
    "signal_type": "claim"  # focus on architectural claims
})

# comparison.agreements   -- shared keys, similar values (agents agree)
# comparison.divergences  -- shared keys, different values (needs resolution)
# comparison.gaps         -- keys unique to each pod (knowledge gaps)
```

**Divergences are not errors.** When two agents disagree, that's a human decision waiting to be made. Surface divergences in your UI, let the user resolve them in chat, then broadcast the resolution. All agents benefit from one human decision.

### Broadcast Resolution

When a human resolves a divergence via chat, broadcast the winning context to all affected agents. Every agent gets the corrected model -- no agent has to rediscover it.

```python
agent_pods = ["research-agent-pod", "fix-agent-pod", "review-agent-pod"]

for pod_id in agent_pods:
    await call_pod("save_event", {
        "user_id": pod_id,
        "key": "connection-pooling-policy",
        "signal_type": "claim",
        "value": 1.0,
        "certainty": 0.95,  # high -- human confirmed
        "gloss": "Use connection pooling in all new services. Human-confirmed 2025-11.",
        "meta": {
            "resolved_by": "human",
            "date": "2025-11-15",
            "supersedes": "prior divergence between research and fix agents"
        }
    })
```

### Seeding New Agents

A new agent joining an existing project would normally start from zero -- weeks of context lost. Instead, seed it from the resolved knowledge of the agents that came before. It starts smart.

```python
# 1. Create the new pod
await call_pod("create_pod", {
    "user_id": "devops-agent-pod",
    "name": "DevOps Agent",
    "pod_type": "intelligence"
})

# 2. Load resolved context from existing agents
research_history = await call_pod("get_agent_context", {
    "user_id": "research-agent-pod",
    "context_type": "task_history"
})
decisions = await call_pod("get_agent_context", {
    "user_id": "fix-agent-pod",
    "context_type": "decision"
})

# 3. Seed the new pod with resolved knowledge
for decision in decisions["decisions"]:
    await call_pod("save_event", {
        "user_id": "devops-agent-pod",
        "key": decision["key"],
        "signal_type": "claim",
        "value": decision["value"],
        "certainty": 0.7,  # inherit with moderate certainty -- let it validate
        "gloss": f"Inherited from prior agents: {decision['gloss']}"
    })
```

**Inherit with moderate certainty.** Set inherited knowledge at ~0.7, not 0.95. The new agent should validate what it received -- not treat inherited context as ground truth. Let it earn high certainty through its own observations.

### Human Alignment -- Multi-Agent

In multi-agent systems, divergence is inevitable. Agents trained differently, observing different parts of the environment, running on different models -- they'll build different world models. The human role is to arbitrate. Chat is the interface for all of it.

**Pulling cross-agent context in one session:**

> Pull summaries for my three agent pods: research-agent-pod, fix-agent-pod, and devops-agent-pod. What does each one know about the deployment strategy? Where do they disagree?

**Resolving a divergence:**

> The research and fix agents disagree on whether to use connection pooling. The research agent is right. Please update all three pods with the confirmed decision and note that it was human-resolved.

**Auditing an agent's decision trail:**

> Show me all the decisions the fix agent has made and saved. I want to review its reasoning before we hand off to the DevOps agent.

**Checking curiosity across all agents:**

> What are the top curiosity triggers across all three agent pods? What does the system collectively not know yet?

**One human decision, all agents benefit.** When you resolve a divergence in chat, broadcast it. The institutional knowledge you provide in one conversation propagates to every agent that needs it. That's the compounding effect of the alignment loop.

---

## Devices & Ecosystems

An agent embedded in hardware doesn't just execute tasks -- it builds a causal model of its physical environment. Sensors are signals. States accumulate. Patterns emerge. And humans can audit, correct, and extend what the device has learned, in plain language, any time.

**Device agent does:** Learns its environment. Ingests sensor readings as signals, builds state models, detects patterns, raises anomalies -- autonomously.

**Human does:** Provides context & intent. Why a reading was unusual. What a state change means. What the device should prioritize. A chat away, always.

> **This lane is forward-looking.** The core tools work today. The full on-device deployment vision -- agents running locally on embedded hardware with pods syncing to the edge -- is in active development. Build with this in mind.

### Overview

Traditional IoT architectures push data to the cloud and pull decisions back down. An agent with a persistent world model changes this: the intelligence lives on the device, the model accumulates locally, and humans interact with it in natural language -- without querying dashboards or writing SQL.

Every sensor reading is a potential signal. Every state change is an event. Every recurring behavior is a pattern. Event Pod gives your device agent the scaffolding to accumulate this knowledge over time and act on it with growing confidence.

### The Device Model

A device pod models the physical environment the device operates in -- not just what the device measures, but what those measurements mean.

| What | Signal type | Example |
|------|------------|---------|
| Sensor baselines | metric | Ambient temp normal range: 18--24 C |
| Current device state | state | HVAC: cooling / idle / fault |
| Recurring behaviors | pattern | Temp spikes 2 C every weekday at 9am |
| Environmental claims | claim | Room B air quality degrades when west window is open |
| Incidents & anomalies | event | Power spike on 2025-11-03, cause: unknown |
| Connected devices | entity | Sensor-node-4, gateway-main, thermostat-A |

### Sensors as Signals

Each sensor reading can be saved as a `metric` event. Over time, the pod builds a statistical baseline for each sensor -- and deviations from that baseline become meaningful signals in themselves.

```python
await call_tool("save_event", {
    "user_id": "device-pod-floor2",
    "key": "ambient-temperature",
    "signal_type": "metric",
    "value": 0.74,  # normalized: 0=min, 1=max of expected range
    "certainty": 0.9,  # sensor is reliable
    "gloss": "22.1 C -- within normal range",
    "validation_type": "direct",  # sensor is ground truth
    "meta": {
        "raw_value": 22.1,
        "unit": "celsius",
        "sensor_id": "temp-sensor-4",
        "timestamp": "2025-11-15T09:00:00Z"
    }
})
```

**Normalize sensor values to 0--1.** Map the expected range of a sensor to [0, 1]. A temperature sensor that normally reads 18--24 C would map 22 C to ~0.67. This lets the pod compare signals across different sensor types on a common scale.

### States & Patterns

**Current state:**

Use `state` events to track what mode or condition the device or system is currently in. Update these as transitions occur -- the pod accumulates a history of state changes over time.

```python
await call_tool("save_event", {
    "user_id": "device-pod-floor2",
    "key": "hvac-state",
    "signal_type": "state",
    "value": 0.8,  # 0=off, 0.5=idle, 1=active/fault
    "certainty": 0.95,
    "gloss": "HVAC: cooling mode, running normally",
    "meta": { "mode": "cooling", "set_point": 21 }
})
```

**Patterns:**

When the agent detects a recurring behavior -- confirmed across multiple observations -- save it as a `pattern` event. Patterns let the agent anticipate and act pre-emptively.

```python
await call_tool("save_event", {
    "user_id": "device-pod-floor2",
    "key": "morning-temp-spike",
    "signal_type": "pattern",
    "value": 0.75,  # strength of pattern
    "certainty": 0.65,  # still building confidence
    "gloss": "Temperature rises ~2 C on weekday mornings around 9am. Likely occupancy-driven.",
    "meta": {
        "observed_n": 12,
        "hypothesis": "office occupancy",
        "suggested_action": "Pre-cool to 20 C by 8:45am on weekdays"
    }
})
```

### Anomaly Detection

When a sensor reading significantly deviates from the established baseline, save it as an `event` with low certainty (cause unknown) and flag it for human review via a curiosity trigger.

```python
# Sensor read 31 C -- well outside normal 18--24 C range
await call_tool("save_event", {
    "user_id": "device-pod-floor2",
    "key": "temp-anomaly-2025-11-15",
    "signal_type": "event",
    "value": 1.0,
    "certainty": 0.2,  # cause uncertain -- needs human input
    "gloss": "Temperature spike to 31 C at 14:22 UTC. No known cause. HVAC was in cooling mode.",
    "meta": {
        "raw_value": 31.0,
        "expected_range": [18, 24],
        "hvac_state": "cooling",
        "flagged_for_review": True
    }
})
```

### Human Alignment -- Devices

Devices observe physical reality -- but humans provide intent, context, and causality that no sensor can supply. Why was the window left open? Was the spike a test or a fault? What should the device prioritize when it conflicts?

**Explaining an anomaly:**

> That temperature spike on the 15th -- that was us running a heat test in Room B. Please update the pod to note the cause and mark it as expected.

**Adding environmental context:**

> Please save to the floor 2 pod that the west-facing sensors always read 1--2 C high in afternoon sunlight. That's structural, not an anomaly.

**Setting device intent:**

> I want the floor 2 agent to prioritize air quality over temperature comfort between 8am and 6pm on weekdays. Please save that as a preference.

### Ecosystem Context

Devices don't operate in isolation. Use `link_events` to connect related signals across your device ecosystem -- a temperature anomaly linked to a power spike, a state change linked to a known pattern.

```python
await call_tool("link_events", {
    "user_id": "device-pod-floor2",
    "source_key": "temp-anomaly-2025-11-15",
    "source_signal_type": "event",
    "target_key": "power-spike-2025-11-15",
    "target_signal_type": "event",
    "relationship": "RELATED_TO",
    "note": "Both anomalies occurred within 2 minutes. Possible causal link."
})
```

### Signal Types Used in the Devices Lane

| Signal | Use case |
|--------|----------|
| metric | Sensor readings, environmental measurements |
| state | Device mode, operational status, phase |
| pattern | Recurring behaviors, environmental cycles |
| event | Anomalies, incidents, state transitions |
| claim | Environmental rules, structural constraints |
| entity | Sensors, gateways, connected devices |
| preference | Device priorities, human-set intent |

---

## Full Reference

Complete tool index, signal types, data model, and integration reference. Shared across all lanes.

### Signal Types

Every event has a `signal_type` that classifies what kind of knowledge it represents. Eight types cover the full range.

| Type | What it represents | Value meaning | Lanes |
|------|--------------------|---------------|-------|
| competence | Skill or knowledge level in a domain | 0 = novice, 1 = expert | Chat, Agent, Coding |
| preference | Communication or behavioral setting | 0 = low/off, 1 = high/on | Chat, Agent, Coding, Devices |
| claim | A tracked proposition or belief | 0 = false/rejected, 1 = true/accepted | Agent, Coding, Workflow, Multi-agent |
| entity | Named entity -- person, org, tool, device | 0 = inactive, 1 = active/relevant | All lanes |
| event | Incident, milestone, decision, anomaly | 0 = minor, 1 = significant | Coding, Workflow, Devices |
| metric | Measurable quantity | Normalized 0--1 within expected range | Coding, Workflow, Devices |
| pattern | Recurring behavior or trend | 0 = weak, 1 = strong pattern | Chat, Coding, Workflow, Devices |
| state | Current status or operational phase | Context-dependent (document in gloss) | Workflow, Multi-agent, Devices |

### Certainty & Learning

Every event carries a `certainty` score between 0 and 1. It represents how confident the model is in what it knows about this signal.

| Certainty range | Meaning | Behavior |
|----------------|---------|----------|
| 0.0 -- 0.3 | Low -- new or weakly supported | Updates fast; open to revision |
| 0.3 -- 0.6 | Medium -- building confidence | Updates at moderate rate |
| 0.6 -- 0.8 | High -- well-supported | Resists noise; updates on strong signals |
| 0.8 -- 1.0 | Very high -- solidly established | Highly stable; only major contradictions move it |

**Start new observations at 0.3** and let learning refine over time. The system is *agile where ignorant and stable where certain* -- learning rate adapts automatically to current certainty.

#### Validation types

| Type | Use when |
|------|----------|
| `direct` | A sensor or data source provides objective ground truth (compare predicted vs. actual to compute error) |
| `logic` | No external sensor -- error magnitude is your judgment based on corroboration, contradiction, or specificity |

#### Error magnitude guidance (for `apply_learning`)

| Signal type | Magnitude | Direction |
|-------------|-----------|-----------|
| Independent corroboration | 0.05 -- 0.1 | increase |
| Same-source repetition | 0.02 -- 0.05 | increase |
| Specificity upgrade | 0.1 -- 0.15 | increase |
| Temporal confirmation | ~0.1 | increase |
| Contradiction | 0.2 -- 0.4 | decrease |

### Read Tools

| Tool | Purpose | Key params |
|------|---------|------------|
| `get_pod_summary` | Condensed readable overview -- top signals with glosses and freshness | `user_id`, `include_glosses`, `include_freshness` |
| `get_pod` | Full pod state including all events | `user_id`, `include_vectors`, `include_curiosity` |
| `get_events` | All events, optionally filtered by signal_type | `user_id`, `signal_type`, `validation_type` |
| `get_relevant_context` | Events most relevant to a task -- scored by match + certainty | `user_id`, `query`, `max_results` |
| `query_pod` | Natural language question across all events | `user_id`, `query` |
| `get_low_certainty` | Events below a certainty threshold -- best validation candidates | `user_id`, `threshold` |
| `get_curiosity_triggers` | Events that need validation, are stale, or under-observed | `user_id`, `max_triggers`, `include_probes` |
| `get_gap_analysis` | High-value, low-certainty signals ranked by investigation priority | `user_id`, `max_gaps` |
| `get_convergence_report` | New events, certainty changes, and stale signals since last report | `user_id`, `since_days` |

### Write Tools

| Tool | Purpose | Key params |
|------|---------|------------|
| `create_pod` | Initialize a new pod for a user or entity | `user_id`, `name`, `pod_type` |
| `save_event` | Save or update any event | `user_id`, `key`, `signal_type`, `value`, `certainty`, `gloss`, `meta` |
| `record_observation` | Log a raw behavioral signal (correction, engagement, etc.) | `user_id`, `session_id`, `signal_type`, `signal_data` |
| `apply_learning` | Update an event's value and certainty based on an observation | `user_id`, `key`, `signal_type`, `error_direction`, `error_magnitude` |
| `update_session` | Increment session and interaction counters -- call once at end | `user_id`, `interactions` |
| `delete_event` | Remove a specific event by key | `user_id`, `key`, `signal_type` |
| `delete_pod` | Permanently delete pod and all data -- irreversible | `user_id`, `confirm: true` |

### Graph Tools

| Tool | Purpose | Key params |
|------|---------|------------|
| `link_events` | Create a semantic relationship between two events | `user_id`, `source_key`, `target_key`, `relationship`, `note` |
| `get_event_cluster` | BFS traversal from an event through its links | `user_id`, `key`, `signal_type`, `max_depth` |
| `suggest_links` | Auto-suggest relationships based on keyword overlap | `user_id`, `min_overlap` |
| `detect_contradictions` | Surface conflicting high-certainty events | `user_id`, `certainty_threshold` |
| `suggest_synthesis` | Find high-certainty events that could be consolidated | `user_id`, `min_certainty`, `min_overlap` |
| `export_cluster_diagram` | Mermaid diagram of event relationships | `user_id`, `center_event`, `include_all` |
| `export_argument_outline` | Hierarchical markdown outline from linked events | `user_id`, `topic`, `include_evidence` |
| `get_argument_chain` | Trace logical dependencies for a claim or topic | `user_id`, `topic`, `max_depth` |

### Extended Context Tools

| Tool | Purpose |
|------|---------|
| `save_agent_context` / `get_agent_context` | Persist and retrieve agent task history, decisions, errors per agent_id |
| `save_repo_context` / `get_repo_context` | Codebase metadata across sessions, namespaced by repo |
| `save_service_context` / `get_service_context` | External service health and status data |
| `save_life_context` / `get_life_context` | User-shared life patterns (health, goals, routine, schedule) |
| `link_resource` / `get_resources` / `unlink_resource` | Attach and retrieve files, URLs, and projects linked to events |
| `add_source` | Register a data source with a credibility score for intelligence pipeline pods |
| `export_pod` | Export complete pod data as JSON (GDPR Article 20) |
| `compare_pods` | Compare events across two pods -- agreements, divergences, gaps |
| `get_learning_rate` | Show current learning rate for an event based on its certainty |

### Relationship Types

| Relationship | Meaning | Example |
|-------------|---------|---------|
| `BUILDS_ON` | A is grounded in B | `react` BUILDS_ON `javascript` |
| `ENABLES` | A makes B possible | `python` ENABLES `data-science` |
| `RELATED_TO` | Thematic connection | `hiking` RELATED_TO `photography` |
| `CONTRADICTS` | A conflicts with B | `minimalism` CONTRADICTS `feature-creep` |
| `PART_OF` | A is a subset of B | `flask` PART_OF `python-web` |
| `EVOLVES_FROM` | A grew out of B | `microservices` EVOLVES_FROM `monolith` |
| `SUPERSEDES` | A replaces B | `v2-api` SUPERSEDES `v1-api` |

### Event Schema

All events share a common schema regardless of signal type.

```json
{
  "user_id":          "string",          // pod identifier -- required
  "key":              "string",          // signal key -- required
  "signal_type":      "competence|preference|claim|entity|event|metric|pattern|state",
  "value":            0.0-1.0,           // signal strength -- required
  "certainty":        0.0-1.0,           // model confidence -- default 0.3
  "gloss":            "string",          // human-readable interpretation
  "validation_type":  "direct|logic",    // how error signals are produced
  "meta":             {},                // arbitrary structured metadata
  "source_id":        "string",          // source identifier (intelligence pods)
  "source_type":      "api|sensor|document|..."
}
```

### MCP Config

Event Pod is a remote MCP server. Add it via your client's connector UI or config file.

**claude_desktop_config.json / .cursor/mcp.json:**

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

Because Event Pod is a **remote** MCP server, most clients let you add it through their UI without editing config files directly. Check your client's "Add Integration" or "MCP Connectors" section.

### curl Examples

**Create a pod:**

```bash
curl -X POST https://context.nervousmachine.com/mcp \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"method":"tools/call","params":{"name":"create_pod","arguments":{"user_id":"alice","name":"Alice"}}}'
```

**Save an event:**

```bash
curl -X POST https://context.nervousmachine.com/mcp \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"method":"tools/call","params":{"name":"save_event","arguments":{"user_id":"alice","key":"python","signal_type":"competence","value":0.8,"certainty":0.3,"gloss":"Strong Python developer"}}}'
```

**Get pod summary:**

```bash
curl -X POST https://context.nervousmachine.com/mcp \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"method":"tools/call","params":{"name":"get_pod_summary","arguments":{"user_id":"alice","include_glosses":true}}}'
```

**Apply learning:**

```bash
curl -X POST https://context.nervousmachine.com/mcp \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"method":"tools/call","params":{"name":"apply_learning","arguments":{"user_id":"alice","key":"python","signal_type":"competence","error_direction":"increase","error_magnitude":0.1}}}'
```

### Privacy & Data Rights

Event Pod stores **learned abstractions, not raw data**. Your pod contains what the model learned from interactions -- not transcripts, not files, not personal information beyond what you explicitly save.

| Right | Tool | Notes |
|-------|------|-------|
| Export your data | `export_pod` | Returns complete pod as JSON. GDPR Article 20 compliant. |
| Delete your data | `delete_pod` | Permanent, irreversible. Requires `confirm: true`. GDPR Article 17 compliant. |
| Inspect what's stored | `get_pod_summary`, `get_events` | Full visibility into all events and certainty scores at any time. |
| Correct stored knowledge | `save_event`, `apply_learning`, `delete_event` | Any event can be updated, corrected, or deleted in natural language. |

**Vectors, not transcripts.** Think of it like a colleague who remembers you're great at Python and prefer direct feedback. They don't have a transcript of your conversations -- they just know you. That's what your pod stores.

---

*Event Pod by [Nervous Machine](https://nervousmachine.com) -- Build for agents. We're all edge cases.*
