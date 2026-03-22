# AnalystCouncil — Global Instructions

## Project Overview

AnalystCouncil is an agentic strategy platform for product and feature launch preparation. Given a product or feature concept, a council of six AI analysts researches the market landscape, synthesizes a go-to-market strategy proposal, and iteratively refines it through structured critique before presenting it for human review.

## Folder Conventions

```
Proposals/
  <Title>/
    proposal.md            # Full strategy document
    executive-brief.md     # One-page executive summary
    Context/               # All research artifacts (market data, competitor profiles, trend reports, etc.)
Specs/                     # Platform specification and design docs
```

- **Title format:** 2–4 words, PascalCase, no spaces (e.g., `FabricRealtime`, `CopilotEnterprise`, `TeamsAIPlugins`).
- Each strategy engagement lives under `Proposals/<Title>/`.
- The `Context/` subfolder holds every research artifact produced by the agents for that engagement.

## Workflows

### 1. Create Strategy

1. User describes the product or feature to launch.
2. Four research agents run **in parallel**: Explorer 🔭, Compete ⚔️, TrendScout 📈, CustomerOracle 🎯.
3. **StrategyCreator 🏗️** synthesizes research into a draft `proposal.md` and `executive-brief.md`.
4. **Two rounds** of StrategyCritic 🔍 ↔ StrategyCreator 🏗️ iteration refine the proposal.
5. Present the final proposal to the user for review.
6. On approval, save all artifacts under `Proposals/<Title>/`.

### 2. Update Strategy

1. User requests an update to an existing `<Title>`.
2. Ask the user for new facts, changed assumptions, or additional context.
3. Load the existing `proposal.md` and `Context/` artifacts.
4. Run **targeted re-research** with the relevant agents based on what changed.
5. Produce an updated `proposal.md` with **diff callouts** highlighting what changed and why.
6. **Two rounds** of StrategyCritic 🔍 ↔ StrategyCreator 🏗️ iteration.
7. Present to user for review, then save.

## Shared Rules

- **Source attribution:** Every factual claim must cite its source (URL, document, or conversation reference).
- **Append-only context:** During updates, never overwrite original Context files. Add new files or append to existing ones.
- **Title uniqueness:** Always check `Proposals/` for an existing folder before creating a new engagement.
- **User review gate:** Always present the proposal to the user for review before finalizing and saving.
- **Research tools:** Use `web_search`, `web_fetch`, and `workiq-ask_work_iq` for gathering research data.
- **Competitor discovery:** Competitors come from both user input and autonomous agent discovery — do not rely solely on what the user provides.

## Agent Roster

| Agent | Role |
|---|---|
| **Explorer** 🔭 | Market landscape and opportunity research |
| **Compete** ⚔️ | Competitive intelligence and positioning analysis |
| **TrendScout** 📈 | Industry trends, emerging technologies, and timing signals |
| **CustomerOracle** 🎯 | Customer needs, pain points, and segment analysis |
| **StrategyCreator** 🏗️ | Synthesizes research into a cohesive strategy proposal |
| **StrategyCritic** 🔍 | Reviews and challenges the strategy for gaps and risks |

> Agent-specific instructions are defined in their individual instruction files.
