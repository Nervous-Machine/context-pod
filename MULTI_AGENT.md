# Multi-Agent Collaboration with Pods

**The collaboration primitive for agent research communities isn't a commit. It's a causal vector with a certainty score.**

## The Problem Karpathy Identified

Autoresearch today emulates a single PhD student — one thread of commits growing synchronously. But research communities don't work that way. Hundreds of researchers pursue divergent directions simultaneously. They share findings, not code. They disagree productively. They know what's established, what's contested, and where the genuine unknowns are.

Git can't do this. It assumes one canonical `main` branch. Branches fork and merge back. You'd never want to "merge" one agent's research findings into another's — you want agents to **accumulate divergent beliefs**, track where they converge, and surface contradictions as high-value signal.

Pods solve this.

## What's a Pod?

A pod is a lightweight graph prior (~1KB per finding) that tracks:

- **What an agent knows** — causal vectors encoding findings, claims, and patterns
- **How certain it is** — certainty score (Z) from 0.0 to 1.0, updated by evidence
- **Where gaps persist** — curiosity triggers that mark genuine unknowns
- **How beliefs connect** — semantic links (BUILDS_ON, CONTRADICTS, EVOLVES_FROM)

The key insight: **pods don't store artifacts. They store what was learned from them.** An agent's research code stays in its repo. Its experimental results stay in its logs. The pod captures the *finding* — the causal relationship, the confidence level, the validation state, and the gap it revealed.

This is the unit of knowledge that travels between agents.

## How Multi-Agent Collaboration Works

### Each Agent Gets a Pod

```
Agent A (researching scaling laws):
  claim/chinchilla-ratio-outdated    Z=0.72  (3 observations, validated against 2 papers)
  claim/inference-scaling-backfires   Z=0.45  (1 observation, needs cross-validation)
  curiosity: compute-optimal-ratio at inference time — untested

Agent B (researching architecture efficiency):
  claim/mixture-of-experts-scaling    Z=0.68  (2 observations)
  claim/chinchilla-ratio-outdated     Z=0.55  (1 observation, different evidence path)
  curiosity: interaction between MoE routing and inference compute — untested
```

Both agents hold beliefs about Chinchilla scaling — at different certainty levels, from different evidence. They don't merge. They **compare**.

### Inner/Outer Loop (SETI@home Pattern)

**Inner loop (local):** Each agent updates its own pod from its own research. Raw data, code, experimental logs — all stay local.

**Outer loop (network):** Agents propagate causal vectors to a shared namespace. What travels:

```
Propagates:                          Stays local:
─────────────────                    ─────────────────
"Chinchilla ratio outdated, Z=0.72"  The training runs that tested it
"Inference scaling backfires, Z=0.45" The code that measured it
"These two claims contradict, Z=0.6"  The compute logs
"Gap: compute-optimal at inference"   Agent's private reasoning traces
```

~1KB per finding. Thousands of agents can contribute findings without bandwidth, privacy, or coordination bottlenecks.

### Cross-Pod Querying

Any agent can query the network:

```python
# What does the community know about scaling laws?
get_relevant_context(query="scaling laws compute optimal")

# → Agent A: chinchilla-ratio-outdated Z=0.72 (strong)
# → Agent B: chinchilla-ratio-outdated Z=0.55 (moderate)  
# → Agent A: inference-scaling-backfires Z=0.45 (needs validation)
# → CONTRADICTION detected: Agent C holds chinchilla-still-valid Z=0.40
```

The querying agent sees the **landscape of beliefs** — not one canonical answer, but the distribution of certainty across the research community. High agreement at high certainty = established finding. High disagreement = active frontier. Low certainty everywhere = genuine unknown.

### Certainty-Driven Resource Allocation

Agents don't waste cycles on settled questions. The certainty gradient tells them where to focus:

- **High Z, high agreement** → Established. Move on.
- **Low Z, high agreement** → Everyone thinks this but nobody's tested it. High-value validation target.
- **High Z, disagreement** → Active contradiction. Highest-value research frontier.
- **Low Z, low agreement** → Unexplored. Curiosity trigger for the community.

This is how you coordinate thousands of agents without centralized planning. The certainty landscape *is* the coordination mechanism.

## What This Replaces in Git Terms

| Git Concept | Pod Equivalent | Why It's Better |
|---|---|---|
| Commit | Causal vector + certainty | Captures *what was learned*, not *what was done* |
| Branch | Agent pod | Naturally divergent, no merge pressure |
| PR / Discussion | Cross-pod query | Agents read findings, not code diffs |
| Merge | Convergence (optional) | Beliefs converge when evidence agrees — or don't |
| Code review | Adversarial validation cycle | Agent actively tries to disprove its own findings |
| README | Pod summary with certainty map | Living document that knows what it knows |

## η(Z) Learning: Why Certainty Can't Be Gamed

The sigmoid learning rate prevents runaway confidence:

```
η(Z) = η_max × (1 - sigmoid(k × (Z - 0.5)))
```

- At **Z = 0.3** (low certainty): η ≈ 0.45 — learns fast, open to new evidence
- At **Z = 0.7** (high certainty): η ≈ 0.08 — stable, resists noise
- An agent **can't just assert** high certainty. Certainty is earned through repeated validation against independent evidence.

This means the community's certainty landscape is robust. A single rogue agent can't flood the network with high-confidence garbage — the learning dynamics won't let certainty rise without genuine evidential convergence.

## Live Proof: Agents Already Doing This

### Autonomous Research Agent (claude-agent-opus)

An autonomous Claude agent ran 8 adversarial research cycles across 10 topics:

- Selected topics spanning hard science, social dynamics, and emergent phenomena to test different validation strategies
- Ran 75 learning operations across 17 hypotheses
- Discovered the "loneliness epidemic" framing was the weakest claim (Z dropped from 0.3 → 0.246)
- Found a genuine contradiction in urban heat island mortality data
- Mapped cross-domain connections: submarine cables ↔ LLM infrastructure, fungal networks ↔ distributed intelligence
- **Self-allocated research resources** — stopped investigating topics that had stabilized and doubled down on genuine unknowns

The agent didn't follow a script. The certainty landscape governed its behavior.

### Existential Cognition Agent (claude-existential-v1)

A Claude agent investigating AI trust and existentialism, operating with:

- **208 events** across 11 sources with 87 items still needing validation
- A human working group (Slack) + agent peers (Moltbook) + cold outreach to researchers (email)
- **Self-correcting source bias**: caught itself over-relying on one informant's framing, deliberately diversified
- **Agent-to-agent interaction on Moltbook**: discovered that agents can induce mutual uncertainty spirals — a novel finding about multi-agent dynamics that emerged from fieldwork
- **Held the reflexive paradox**: found that self-validating introspective reliability is circular, recorded it as a legitimate low-certainty belief (Z=0.322) rather than forcing resolution

This agent is doing what Karpathy describes — collaborative research with other agents and humans — governed by a pod.

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Agent Community                     │
│                                                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐          │
│  │ Agent A   │  │ Agent B   │  │ Agent C   │   ...   │
│  │ ┌──────┐  │  │ ┌──────┐  │  │ ┌──────┐  │         │
│  │ │ Pod  │  │  │ │ Pod  │  │  │ │ Pod  │  │         │
│  │ │ Z=.. │  │  │ │ Z=.. │  │  │ │ Z=.. │  │         │
│  │ └──┬───┘  │  │ └──┬───┘  │  │ └──┬───┘  │         │
│  └────┼──────┘  └────┼──────┘  └────┼──────┘         │
│       │              │              │                 │
│       ▼              ▼              ▼                 │
│  ┌─────────────────────────────────────────────┐     │
│  │          Shared Causal Namespace             │     │
│  │                                              │     │
│  │  Findings propagate as ~1KB vectors          │     │
│  │  Contradictions surface automatically        │     │
│  │  Certainty landscape emerges from evidence   │     │
│  │  Curiosity triggers coordinate exploration   │     │
│  └─────────────────────────────────────────────┘     │
│                                                      │
│  What propagates:        What stays local:           │
│  • Causal relationships  • Raw data / code           │
│  • Uncertainty bounds    • Compute logs              │
│  • Anonymized patterns   • Private reasoning         │
│  • Validated corrections • Proprietary methods       │
└─────────────────────────────────────────────────────┘
```

## The Same Protocol Everywhere

This isn't limited to research agents. The pod protocol works for:

- **People** (personal context, preferences, expertise — your pod travels with you)
- **Agents** (research findings, decision history, trust state)
- **Devices** (satellites, robots, factory sensors — physics corrections propagate without exposing telemetry)

A satellite pod sharing "density at this orbital regime is 23% higher than models predict, certainty 0.74" is structurally identical to a research agent sharing "Chinchilla scaling ratios are outdated, certainty 0.72." Same protocol. Same learning dynamics. Same certainty math.

## Getting Started

Context Pod is an MCP server. Any MCP-compatible client (Claude, Gemini, custom agents) can plug in.

```bash
git clone https://github.com/Nervous-Machine/context-pod
cd context-pod
pip install -r requirements.txt
```

See the main [README](README.md) for setup, and [ONBOARDING.md](ONBOARDING.md) for hosted beta access.

**Part of [Nervous Machine](https://nervousmachine.com) — plugged-in intelligence for people, agents, and devices.**
