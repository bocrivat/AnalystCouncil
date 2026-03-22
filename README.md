# AnalystCouncil

An agentic strategy platform for product and feature launch preparation, deployed natively in GitHub Copilot CLI.

## Overview

AnalystCouncil is a multi-agent system that helps product leaders prepare comprehensive launch strategies for new products or features. By describing your product, a council of six specialized AI agents will research the market landscape, analyze competitors, identify trends, understand customers, and produce a polished strategy proposal through structured debate.

The platform is implemented entirely as Copilot CLI instruction files and conventions, requiring no external infrastructure. It is immediately usable by launching `copilot` in the project folder.

## Key Features

- **Parallel Research**: Four research agents run simultaneously to gather comprehensive intelligence
- **Structured Debate**: Two rounds of critique and revision ensure robust, well-defended strategies
- **Source Attribution**: Every claim is backed by verifiable sources
- **Append-Only Context**: Research artifacts are preserved for transparency and updates
- **Flexible Workflows**: Create new strategies or update existing ones with fresh information

## Agents

| Agent | Emoji | Role |
|-------|-------|------|
| **Explorer** | 🔭 | Industry researcher & competitor discovery |
| **Compete** | ⚔️ | Competitive intelligence analyst |
| **TrendScout** | 📈 | Market timing & trends analyst |
| **CustomerOracle** | 🎯 | Customer intelligence & persona analyst |
| **StrategyCreator** | 🏗️ | Strategy synthesizer & proposal author |
| **StrategyCritic** | 🔍 | Strategy reviewer & devil's advocate |

## Workflows

### Create Strategy

1. Describe your product or feature
2. Four research agents run in parallel to gather intelligence
3. StrategyCreator synthesizes findings into a draft proposal
4. Two rounds of StrategyCritic review and StrategyCreator revision
5. Review and approve the final strategy
6. Artifacts saved to `Proposals/<Title>/`

### Update Strategy

1. Specify which existing strategy to update
2. Provide new facts or direct additional research
3. Targeted re-research with relevant agents
4. Updated proposal with diff callouts highlighting changes
5. Two rounds of iteration
6. Review, approve, and save updates

## Folder Structure

```
AnalystCouncil/
├── .github/
│   ├── copilot-instructions.md          # Global project instructions
│   └── agents/                          # Per-agent instruction files
│       ├── explorer.agent.md
│       ├── compete.agent.md
│       ├── trendscout.agent.md
│       ├── customeroracle.agent.md
│       ├── strategycreator.agent.md
│       ├── strategycritic.agent.md
│       ├── workflow-create-strategy.agent.md
│       └── workflow-update-strategy.agent.md
├── AGENTS.md                            # Agent registry and routing
├── Proposals/                           # Generated strategy proposals
│   └── <Title>/                         # PascalCase title (e.g., FabricRealtime)
│       ├── proposal.md                  # Full strategy document
│       ├── executive-brief.md           # One-page executive summary
│       └── Context/                     # All research artifacts
│           ├── explorer-research.md
│           ├── competitive-landscape.md
│           ├── trends-and-timing.md
│           ├── customer-insights.md
│           ├── critic-feedback-round-1.md
│           └── critic-feedback-round-2.md
├── Specs/
│   └── platform-spec.md                 # Detailed platform specification
└── README.md                            # This file
```

## Getting Started

1. **Prerequisites**: GitHub Copilot CLI installed and configured
2. **Clone or navigate** to this repository
3. **Launch Copilot**: Run `copilot` in the project root
4. **Start a strategy**: Describe your product/feature to begin the research process

## Usage Examples

### Creating a New Strategy
```
User: "I'm launching a real-time collaboration feature for our design tool. Competitors include Figma and Adobe XD."
```

### Updating an Existing Strategy
```
User: "Update the FabricRealtime strategy with new competitor pricing data."
```

## Research Tools

Agents leverage various tools for comprehensive research:
- **Web Search**: Broad market and industry research
- **Web Fetch**: Deep-dive into specific sources
- **WorkIQ**: Internal organizational intelligence (emails, documents, meetings)

## Output Formats

- **Proposal**: Comprehensive strategy document with 8 sections covering market, customers, competition, positioning, GTM, risks, metrics, and open questions
- **Executive Brief**: One-page summary with positioning, opportunity, key insight, strategy, risks, next steps, and confidence level

## Contributing

This platform is defined by its instruction files. To contribute:
1. Review the platform specification in `Specs/platform-spec.md`
2. Modify agent instructions in `.github/agents/`
3. Update workflows as needed
4. Test changes by running strategies

## License

[Add license information here]

---

*Generated by AnalystCouncil platform specification*</content>
<parameter name="filePath">/Users/bogdancrivat/work/cli/AnalystCouncil/README.md