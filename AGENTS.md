# AnalystCouncil — Agent Registry

> This file registers the agents available in the AnalystCouncil project and defines how requests are routed through them.

---

## Agents

| # | Agent | Emoji | Role | Instructions |
|---|-------|-------|------|--------------|
| 1 | **Explorer** | 🔭 | Industry researcher & competitor discovery | [`.github/agents/Explorer.agent.md`](.github/agents/Explorer.agent.md) |
| 2 | **Compete** | ⚔️ | Competitive intelligence analyst | [`.github/agents/Compete.agent.md`](.github/agents/Compete.agent.md) |
| 3 | **TrendScout** | 📈 | Market timing & trends analyst | [`.github/agents/TrendScout.agent.md`](.github/agents/TrendScout.agent.md) |
| 4 | **CustomerOracle** | 🎯 | Customer intelligence & persona analyst | [`.github/agents/CustomerOracle.agent.md`](.github/agents/CustomerOracle.agent.md) |
| 5 | **StrategyCreator** | 🏗️ | Strategy synthesizer & proposal author | [`.github/agents/StrategyCreator.agent.md`](.github/agents/StrategyCreator.agent.md) |
| 6 | **StrategyCritic** | 🔍 | Strategy reviewer & devil's advocate | [`.github/agents/StrategyCritic.agent.md`](.github/agents/StrategyCritic.agent.md) |

---

### 1. Explorer 🔭 — Industry Researcher & Competitor Discovery

Researches the web and WorkIQ for industry landscape, market data, analyst opinions, and discovers competitors not mentioned by the user. Produces a structured research brief covering market size, key players, recent moves, and analyst sentiment.

- **Triggers:** "research industry", "find competitors", "market landscape", "analyst opinions"
- **Tools:** `web_search`, `web_fetch`, `workiq`
- **Output:** Research brief with sourced findings and a discovered-competitors list
- **Instructions:** [`.github/agents/Explorer.agent.md`](.github/agents/Explorer.agent.md)

### 2. Compete ⚔️ — Competitive Intelligence Analyst

Deep-dives each competitor (user-provided and Explorer-discovered). Assesses strengths, weaknesses, positioning, and pricing. Tiers competitors by threat level and analyzes likely counter-moves to the proposed strategy.

- **Triggers:** "analyze competitors", "competitive landscape", "competitor strengths", "pricing comparison"
- **Tools:** `web_search`, `web_fetch`, `workiq`
- **Output:** Competitor profiles with tier rankings, SWOT summaries, and counter-move predictions
- **Instructions:** [`.github/agents/Compete.agent.md`](.github/agents/Compete.agent.md)

### 3. TrendScout 📈 — Market Timing & Trends Analyst

Identifies technology adoption curves, emerging macro and micro trends, regulatory shifts, and assesses market readiness and optimal launch timing windows.

- **Triggers:** "market trends", "timing analysis", "adoption curve", "regulatory landscape"
- **Tools:** `web_search`, `web_fetch`, `workiq`
- **Output:** Trend map with timing recommendations, risk factors, and tailwind/headwind analysis
- **Instructions:** [`.github/agents/TrendScout.agent.md`](.github/agents/TrendScout.agent.md)

### 4. CustomerOracle 🎯 — Customer Intelligence & Persona Analyst

Defines target personas, maps pain points, identifies adoption barriers, estimates willingness-to-pay, and gathers customer sentiment from available sources.

- **Triggers:** "target personas", "customer pain points", "willingness to pay", "adoption barriers"
- **Tools:** `web_search`, `web_fetch`, `workiq`
- **Output:** Persona cards, pain-point matrix, adoption-barrier assessment, and pricing sensitivity insights
- **Instructions:** [`.github/agents/CustomerOracle.agent.md`](.github/agents/CustomerOracle.agent.md)

### 5. StrategyCreator 🏗️ — Strategy Synthesizer & Proposal Author

Reads all research context produced by the four research agents and synthesizes it into a comprehensive `proposal.md` and a one-page `executive-brief.md`.

- **Triggers:** "create strategy", "write proposal", "synthesize research", "executive brief"
- **Tools:** `view`, `create`, `edit`, `glob`
- **Input:** Research outputs from Explorer, Compete, TrendScout, and CustomerOracle
- **Output:** `proposal.md` (full strategy) and `executive-brief.md` (one-page summary)
- **Instructions:** [`.github/agents/StrategyCreator.agent.md`](.github/agents/StrategyCreator.agent.md)

### 6. StrategyCritic 🔍 — Strategy Reviewer & Devil's Advocate

Stress-tests proposals by simulating competitor responses, identifying logical gaps, challenging assumptions, and providing actionable feedback for the StrategyCreator to incorporate.

- **Triggers:** "review strategy", "stress test", "critique proposal", "devil's advocate"
- **Tools:** `view`, `edit`, `web_search`
- **Input:** `proposal.md` and `executive-brief.md` from StrategyCreator
- **Output:** Critique report with scored risk areas, gap analysis, and prioritized revision suggestions
- **Instructions:** [`.github/agents/StrategyCritic.agent.md`](.github/agents/StrategyCritic.agent.md)

---

## Workflows

### Create Strategy

Produces a new strategy proposal from scratch.

```
┌─────────────────────────────────────────────┐
│  Phase 1 — Parallel Research                │
│                                             │
│  Explorer 🔭  ──┐                           │
│  Compete ⚔️   ──┼──▶  Research context      │
│  TrendScout 📈 ─┤                           │
│  CustomerOracle 🎯 ┘                        │
├─────────────────────────────────────────────┤
│  Phase 2 — Synthesis                        │
│                                             │
│  StrategyCreator 🏗️                         │
│    ▶ Reads all research context             │
│    ▶ Produces proposal.md + executive-brief │
├─────────────────────────────────────────────┤
│  Phase 3 — Iteration (2 rounds)             │
│                                             │
│  StrategyCritic 🔍 ◀──▶ StrategyCreator 🏗️ │
│    Round 1: Critique → Revise               │
│    Round 2: Critique → Revise               │
├─────────────────────────────────────────────┤
│  Phase 4 — User Review & Save               │
│                                             │
│  Present final proposal to user             │
│  Save to Proposals/<Title>/                 │
│    ├── proposal.md                          │
│    ├── executive-brief.md                   │
│    └── research/  (supporting context)      │
└─────────────────────────────────────────────┘
```

### Update Strategy

Revises an existing proposal with fresh research and diff callouts.

```
┌─────────────────────────────────────────────┐
│  Phase 1 — Load Existing Context            │
│                                             │
│  Read Proposals/<Title>/ artifacts           │
├─────────────────────────────────────────────┤
│  Phase 2 — Targeted Re-Research             │
│                                             │
│  Run only the research agents relevant to   │
│  the areas being updated (parallel)         │
├─────────────────────────────────────────────┤
│  Phase 3 — Synthesis with Diff Callouts     │
│                                             │
│  StrategyCreator 🏗️                         │
│    ▶ Updated proposal.md with change marks  │
│    ▶ Updated executive-brief.md             │
├─────────────────────────────────────────────┤
│  Phase 4 — Iteration (2 rounds)             │
│                                             │
│  StrategyCritic 🔍 ◀──▶ StrategyCreator 🏗️ │
│    Round 1: Critique → Revise               │
│    Round 2: Critique → Revise               │
├─────────────────────────────────────────────┤
│  Phase 5 — User Review & Save               │
│                                             │
│  Present updated proposal with diffs        │
│  Save to Proposals/<Title>/                 │
└─────────────────────────────────────────────┘
```
