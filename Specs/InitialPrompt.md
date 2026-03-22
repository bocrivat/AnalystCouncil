I want to build an agentic platform that allows me to prepare for launching a new product or feature. The agents and workflows need to be deployed in this current folder in such a way as to be directly usable in GitHub Copilot CLI.
My ultimate goal is to:
- state a problem
- you perform the research workflow
- you recommend me a solution
Firstly, I want you to generate a spec for this platform. Requirements for this platform are described below. The full spec should be saved in the Specs folder. After this, I will ask you to implement the spec.
**Main target user workflow, as operated by me, is to create a strategy for launching a product or feature**
- I will describe the product or feature I am preparing to launch
- you will launch a collection of agents to perform multiple tasks in parallel, review each other's work, and generate a proposed strategy and result
- you will show me the plan for the proposal, and review it with me, before generating the final form
- you will save the proposal under the Proposals folder, in a folder name with the format <Title>, where <Title> is a very short representation of my problem
- the proposal should be saved as proposal.md
- the <Title> Should be unique in the Proposals folder
- all the memory (partial results, summary of information retrieved) for this task will be saved in Proposals/<Title>/Context (using whatever file names you want)

**Structure of the platform**
The platform should have a number of agents, you are to suggest the agents
Amongst them, I want at least:
- one Explorer agent to explore the web and WorkIQ, and collect facts and industry positions about the area of interest (explorer). Explorer should also discover new competitors (not mentioned below)
- one Compete agent to explore Web, WorkIQ and understand the competitive position of my core competitors. This agent should also decide which competitors are relevant.
- One agent to look at the results from the ones above and to generate a proposal (StrategyCreator)
- One agent to critically compare the proposal and analyze attack angles from the competitors (StrategyCritic)
- StrategyCreator and StrategyCritic should work with each other through a small number of iterations (start with 2) to produce the final proposal.
- All relevant facts need to be recorded and remembered during analysis, then saved somehow in the Context folder
- Each agent should have a specific personality, appropriate for the responsibilities assigned to them above
- Each agent should remember the context
- agents should be saved in *.agent.md files, under the .github/agents folder

**Workflows**
The platform should include agentic workflows (what each agent or combination needs to do at any user workflow)
The platform should also define which of these agentic workflows should be executed in parallel
All the work happens in current folder, so feel free to use the .GitHub, .GitHub/copilot-instructions.md, AGENTS.md etc convention and treat this folder as a GitHub Repo (I will create the repo later)
