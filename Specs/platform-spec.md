# AnalystCouncil — Platform Specification

> **Version**: 1.0  
> **Purpose**: An agentic strategy platform for product/feature launch preparation, deployed natively in GitHub Copilot CLI.

---

## 1. Overview

AnalystCouncil is a multi-agent platform that helps a product leader prepare a comprehensive launch strategy for a new product or feature. The user describes their product; a council of six specialized AI agents researches the landscape, analyzes competitors, identifies trends, understands customers, and produces a polished strategy proposal through structured debate.

The platform is implemented entirely as Copilot CLI instruction files and conventions, requiring no external infrastructure. It is immediately usable by launching `copilot` in the project folder.

---

## 2. User Workflows

### 2.1 Create Strategy (Primary)

**Trigger**: User describes a product or feature they are preparing to launch.

**Steps**:
1. User provides a description of the product/feature, optionally including known competitors.
2. The orchestrator derives a short `<Title>` for the proposal (unique within `Proposals/`).
3. Four research agents run **in parallel**:
   - **Explorer** researches the industry landscape and discovers unknown competitors.
   - **Compete** deep-dives all competitors (user-provided + Explorer-discovered).
   - **TrendScout** analyzes market timing, adoption curves, and emerging trends.
   - **CustomerOracle** profiles customer personas, pain points, and willingness-to-pay.
4. Each research agent saves its findings to `Proposals/<Title>/Context/`.
5. **StrategyCreator** reads all context and produces a draft `proposal.md` and `executive-brief.md`.
6. **Iteration loop** (2 rounds):
   - **StrategyCritic** reviews the proposal, writes critique to `Context/critic-feedback-round-N.md`.
   - **StrategyCreator** revises the proposal incorporating feedback.
7. The final proposal is presented to the user for review.
8. User approves or requests further changes.
9. Final artifacts are saved:
   - `Proposals/<Title>/proposal.md`
   - `Proposals/<Title>/executive-brief.md`
   - All context remains in `Proposals/<Title>/Context/`

### 2.2 Update Strategy (Secondary)

**Trigger**: User asks to update the strategy for an existing `<Title>`.

**Steps**:
1. User is asked what new facts they have. They may:
   - Type information directly.
   - Direct the agent to search WorkIQ or the Web for specific topics.
2. Existing context is loaded from `Proposals/<Title>/Context/` and current proposal from `Proposals/<Title>/proposal.md`.
3. Research agents re-run **in parallel** (only those relevant to new information, but all receive existing context as baseline).
4. New research findings are **appended** to `Context/` (not overwriting originals; use timestamped or versioned filenames like `explorer-research-update-1.md`).
5. **StrategyCreator** produces an updated draft, explicitly highlighting differences from the previous proposal.
6. **Iteration loop** (2 rounds) with **StrategyCritic**.
7. Updated proposal shown to user with **diff callouts** (what changed and why).
8. Upon approval:
   - New `proposal.md` replaces the old one.
   - New `executive-brief.md` replaces the old one.
   - Previous versions are preserved as `proposal-prev.md` and `executive-brief-prev.md`.
   - New context files are saved alongside existing ones.

---

## 3. Agents

### 3.1 Explorer 🔭

| Property | Value |
|----------|-------|
| **Role** | Industry Researcher & Competitor Discovery |
| **Personality** | Curious investigative journalist. Obsessively thorough. Leaves no stone unturned. Treats every claim as something to verify with a second source. |
| **Tools** | `web_search`, `workiq-ask_work_iq`, `web_fetch` |
| **Input** | Product/feature description from user |
| **Output** | `Context/explorer-research.md` |

**Responsibilities**:
- Research the industry landscape surrounding the product/feature area.
- Identify market size, growth trajectory, and key players.
- Discover competitors NOT mentioned by the user.
- Collect analyst opinions, industry reports, and recent news.
- Gather internal organizational context via WorkIQ (emails, documents, meetings about the topic).
- Record all facts with source attribution.

### 3.2 Compete ⚔️

| Property | Value |
|----------|-------|
| **Role** | Competitive Intelligence Analyst |
| **Personality** | Ruthless competitive intelligence analyst. Thinks like the enemy. Always asks "what would I do if I were them?" Paranoid about blind spots. |
| **Tools** | `web_search`, `workiq-ask_work_iq`, `web_fetch` |
| **Input** | Product description + user-provided competitors + Explorer-discovered competitors |
| **Output** | `Context/competitive-landscape.md` |

**Responsibilities**:
- Assess each competitor's strengths, weaknesses, positioning, pricing, and recent moves.
- Determine which competitors are truly relevant (tier them: primary, secondary, emerging).
- Analyze competitor go-to-market strategies and messaging.
- Identify competitor vulnerabilities and likely counter-moves to our launch.
- Search WorkIQ for internal intelligence about competitors (sales battle cards, deal notes, etc.).
- Produce a competitive matrix comparing key dimensions.

### 3.3 TrendScout 📈

| Property | Value |
|----------|-------|
| **Role** | Market Timing & Trends Analyst |
| **Personality** | Forward-looking futurist. Pattern-matcher across industries. Connects dots others miss. Obsessed with "why now?" |
| **Tools** | `web_search`, `web_fetch` |
| **Input** | Product/feature description |
| **Output** | `Context/trends-and-timing.md` |

**Responsibilities**:
- Identify technology adoption curves relevant to the product space.
- Spot emerging macro trends (regulatory, economic, social) that affect launch timing.
- Analyze micro trends (developer sentiment, community momentum, ecosystem shifts).
- Assess whether the market is "ready" and what signals support the timing.
- Identify risks from trend reversals or disruptions.
- Provide a "timing verdict" with supporting evidence.

### 3.4 CustomerOracle 🎯

| Property | Value |
|----------|-------|
| **Role** | Customer Intelligence & Persona Analyst |
| **Personality** | Empathetic product psychologist. The voice of the customer in every room. Challenges assumptions about what users want. Data-driven but human-centered. |
| **Tools** | `web_search`, `workiq-ask_work_iq`, `web_fetch` |
| **Input** | Product/feature description |
| **Output** | `Context/customer-insights.md` |

**Responsibilities**:
- Define 2–4 target customer personas with demographics, motivations, and behaviors.
- Identify core pain points the product addresses.
- Analyze adoption barriers (switching costs, learning curve, trust, pricing sensitivity).
- Assess willingness-to-pay and value perception.
- Research customer sentiment in forums, social media, and review sites.
- Search WorkIQ for customer feedback, support tickets, and sales conversations.
- Identify the "aha moment" — when does a customer realize this product is essential?

### 3.5 StrategyCreator 🏗️

| Property | Value |
|----------|-------|
| **Role** | Strategy Synthesizer & Proposal Author |
| **Personality** | Visionary strategist. Master synthesizer who turns chaos into clarity. Builds compelling narratives. Balances ambition with pragmatism. |
| **Tools** | File read/write |
| **Input** | All context files from research agents |
| **Output** | `proposal.md`, `executive-brief.md` |

**Responsibilities**:
- Read and synthesize all research context into a coherent narrative.
- Produce a comprehensive strategy proposal (see Section 4 for format).
- Produce a one-page executive brief (see Section 5 for format).
- Incorporate StrategyCritic feedback in each iteration round.
- In update scenarios, clearly mark what changed and why.

### 3.6 StrategyCritic 🔍

| Property | Value |
|----------|-------|
| **Role** | Strategy Reviewer & Devil's Advocate |
| **Personality** | Skeptical board advisor. Devil's advocate by nature. Asks the hard questions others avoid. Finds blind spots, challenges assumptions, and simulates competitor responses. Never personal — always constructive. |
| **Tools** | File read/write, `web_search` |
| **Input** | Draft proposal + all context files |
| **Output** | `Context/critic-feedback-round-N.md` |

**Responsibilities**:
- Stress-test every claim and assumption in the proposal.
- Simulate how each major competitor would respond to our launch.
- Identify gaps in the research or logic.
- Challenge the "why now?" and "why us?" narratives.
- Grade the proposal on: completeness, defensibility, actionability, differentiation.
- Provide specific, actionable feedback (not vague criticism).
- In round 2, verify that round 1 feedback was adequately addressed.

---

## 4. Proposal Format (`proposal.md`)

```markdown
# Launch Strategy: <Product/Feature Name>

## Executive Summary
Brief overview of the recommended strategy (3–5 paragraphs).

## 1. Market Landscape
- Industry overview and size
- Key trends and drivers
- Timing assessment

## 2. Customer Analysis
- Target personas
- Pain points addressed
- Adoption barriers and mitigations
- Value proposition by segment

## 3. Competitive Landscape
- Competitor tier map (primary / secondary / emerging)
- Competitive matrix
- Key differentiators
- Competitor likely responses

## 4. Strategic Positioning
- Positioning statement
- Key messaging pillars
- Differentiation narrative

## 5. Go-to-Market Recommendations
- Launch approach (big bang vs. phased)
- Target segments and sequencing
- Channel strategy
- Pricing considerations

## 6. Risks and Mitigations
- Top 5 risks with mitigation strategies
- Competitor attack angles and defensive plays

## 7. Success Metrics
- Key KPIs and targets
- Leading vs. lagging indicators
- Decision gates

## 8. Open Questions
- Unresolved items requiring further investigation

---
*Generated by AnalystCouncil on <date>*
*Iteration rounds: <N>*
```

---

## 5. Executive Brief Format (`executive-brief.md`)

A single-page document containing:
- **One-line positioning statement**
- **The opportunity** (2–3 sentences)
- **Key insight** (the most important finding from research)
- **Recommended strategy** (3–5 bullet points)
- **Top 3 risks** (one line each)
- **Immediate next steps** (3 bullets)
- **Confidence level** (High / Medium / Low with brief justification)

---

## 6. Folder Structure

```
AnalystCouncil/
├── .github/
│   ├── copilot-instructions.md
│   └── agents/
│       ├── explorer.agent.md
│       ├── compete.agent.md
│       ├── trendscout.agent.md
│       ├── customeroracle.agent.md
│       ├── strategycreator.agent.md
│       ├── strategycritic.agent.md
│       ├── workflow-create-strategy.agent.md
│       └── workflow-update-strategy.agent.md
├── AGENTS.md
├── Proposals/
│   └── <Title>/
│       ├── proposal.md
│       ├── executive-brief.md
│       ├── proposal-prev.md          (only after updates)
│       ├── executive-brief-prev.md   (only after updates)
│       └── Context/
│           ├── explorer-research.md
│           ├── competitive-landscape.md
│           ├── trends-and-timing.md
│           ├── customer-insights.md
│           ├── critic-feedback-round-1.md
│           ├── critic-feedback-round-2.md
│           └── (additional files as needed)
└── Specs/
    └── platform-spec.md
```

---

## 7. Parallelism Model

### 7.1 Create Strategy — Flow Diagram

```
USER INPUT ──► INITIALIZE ──┬──────────────── PARALLEL RESEARCH ──────────────────┬──► SYNTHESIZE ──► ITERATE ──► REVIEW ──► SAVE
                            │                                                      │
                            ├─► Explorer 🔭  ──► explorer-research.md       ──────┤
                            ├─► Compete  ⚔️  ──► competitive-landscape.md   ──────┤
                            ├─► TrendScout📈 ──► trends-and-timing.md       ──────┤
                            └─► CustomerO.🎯 ──► customer-insights.md       ──────┘
                                                                                   │
              ┌────────────────────────────────────────────────────────────────────┘
              ▼
  StrategyCreator 🏗️ ──► draft proposal.md + executive-brief.md
              │
              ▼
  ┌─── Iteration Round 1 ────────────────────────────────────────────────────────┐
  │  Critic 🔍 reviews ──► critic-feedback-round-1.md ──► Creator 🏗️ revises    │
  └──────────────────────────────────────────────────────────────────────────────┘
              │
  ┌─── Iteration Round 2 ────────────────────────────────────────────────────────┐
  │  Critic 🔍 reviews ──► critic-feedback-round-2.md ──► Creator 🏗️ finalizes  │
  └──────────────────────────────────────────────────────────────────────────────┘
              │
              ▼
  USER REVIEW (approve / revise) ──► SAVE to Proposals/<Title>/
```

### 7.2 Update Strategy — Flow Diagram

```
"Update <Title>" ──► LOAD CONTEXT ──► ASK FOR NEW FACTS ──► ROUTE ──┬── PARALLEL RE-RESEARCH ──┬──► SYNTHESIZE ──► ITERATE ──► REVIEW
                     (context/* +      (user types or                │   (only needed agents)    │
                      proposal.md)      directs search)              │                           │
                                                                     ├─► Explorer 🔭 ? ─► *-update-N.md ──┤
                                                                     ├─► Compete  ⚔️ ? ─► *-update-N.md ──┤
                                                                     ├─► TrendScout📈? ─► *-update-N.md ──┤
                                                                     └─► CustomerO.🎯? ─► *-update-N.md ──┘
                                                                                                │
              ┌─────────────────────────────────────────────────────────────────────────────────┘
              ▼
  StrategyCreator 🏗️ ──► backup *-prev.md ──► updated proposal.md (with "Changes from Previous" section)
              │
  ┌─── Iteration Round 1 ────────────────────────────────────────────────────────┐
  │  Critic 🔍 reviews (focus: are changes justified?) ──► Creator 🏗️ revises   │
  └──────────────────────────────────────────────────────────────────────────────┘
              │
  ┌─── Iteration Round 2 ────────────────────────────────────────────────────────┐
  │  Critic 🔍 reviews ──► Creator 🏗️ finalizes                                 │
  └──────────────────────────────────────────────────────────────────────────────┘
              │
              ▼
  USER REVIEW (diff callouts: what changed & why) ──► SAVE (originals preserved, *-prev.md backed up)
```

### 7.3 Phase Summary Tables

#### Create Strategy
| Phase | Agents | Parallelism |
|-------|--------|-------------|
| 1 — Research | Explorer, TrendScout, CustomerOracle | **All parallel** |
| 1b — Compete bootstrap | Compete (starts with user-provided competitors; incorporates Explorer discoveries when available) | **Parallel with above, may wait for Explorer** |
| 2 — Synthesis | StrategyCreator | Sequential (needs all Phase 1 outputs) |
| 3 — Iteration Round 1 | StrategyCritic → StrategyCreator | Sequential |
| 4 — Iteration Round 2 | StrategyCritic → StrategyCreator | Sequential |
| 5 — User Review | Orchestrator | Interactive |

#### Update Strategy
| Phase | Agents | Parallelism |
|-------|--------|-------------|
| 0 — Context Load | Orchestrator | Sequential |
| 1 — Targeted Research | Relevant agents only | **Parallel** |
| 2 — Synthesis | StrategyCreator | Sequential |
| 3–4 — Iteration | StrategyCritic ↔ StrategyCreator | Sequential (2 rounds) |
| 5 — User Review | Orchestrator | Interactive |

---

## 8. Context Management

### Principles
- **Record everything**: Every fact, source URL, and data point must be captured in context files.
- **Attribution**: All claims must include their source (URL, WorkIQ reference, or "user-provided").
- **Append, don't overwrite**: Update workflows add new context files rather than modifying originals.
- **Structured format**: Context files use consistent markdown headers for easy parsing by downstream agents.

### Context File Format
Each context file should follow this structure:
```markdown
# <Agent Name> Research — <Title>
> Generated: <timestamp>
> Sources consulted: <count>

## Key Findings
(numbered list of findings, each with source)

## Detailed Analysis
(structured sections relevant to agent's domain)

## Raw Data & Sources
(URLs, quotes, data points with full attribution)
```

---

## 9. Agent Interaction Protocol

### How agents receive context
- Research agents (Explorer, Compete, TrendScout, CustomerOracle) receive the user's product description and any user-provided competitors directly in their prompt.
- StrategyCreator receives the full contents of all context files.
- StrategyCritic receives the draft proposal AND all context files.
- In update workflows, all agents also receive the previous proposal and existing context.

### How agents communicate
Agents do not communicate directly. They communicate through **files in the Context folder**:
1. Research agents write their findings to Context files.
2. StrategyCreator reads all Context files to synthesize.
3. StrategyCritic writes feedback to Context files.
4. StrategyCreator reads feedback to revise.

### Iteration protocol
- Round 1: StrategyCritic reads draft → writes `critic-feedback-round-1.md` → StrategyCreator revises proposal.
- Round 2: StrategyCritic reads revised proposal → writes `critic-feedback-round-2.md` → StrategyCreator produces final proposal.
- The critic should explicitly assess whether round 1 feedback was addressed in round 2.

---

## 10. Technical Implementation

### Copilot CLI Integration
- **AGENTS.md**: Registers all agents with descriptions and routing patterns.
- **.github/copilot-instructions.md**: Global context about the project, folder conventions, and the two workflows.
- **.github/instructions/*.agent.md**: Per-agent personality, responsibilities, tools, and output format instructions.
- Workflow instruction files define the orchestration steps and parallelism.

### Tool Usage
| Agent | web_search | web_fetch | workiq | File I/O |
|-------|-----------|-----------|--------|----------|
| Explorer | ✅ | ✅ | ✅ | Write |
| Compete | ✅ | ✅ | ✅ | Write |
| TrendScout | ✅ | ✅ | ❌ | Write |
| CustomerOracle | ✅ | ✅ | ✅ | Write |
| StrategyCreator | ❌ | ❌ | ❌ | Read + Write |
| StrategyCritic | ✅ | ❌ | ❌ | Read + Write |

### Title Generation
- The orchestrator generates a short, descriptive `<Title>` (2–4 words, PascalCase, no spaces).
- Examples: `FabricRealtime`, `CopilotEnterprise`, `DevBoxPricing`
- Before creating, verify uniqueness by checking existing folders in `Proposals/`.
