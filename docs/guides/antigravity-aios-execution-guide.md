# AIOS Framework Orchestration Guide for Antigravity

This guide provides a step-by-step walkthrough of how to use the AIOS framework within the **Antigravity** environment, focusing on orchestration and squad management.

---

## 🚀 1. Activation Phase

To begin using the AIOS framework in Antigravity, you must activate the **AIOS Master Orchestrator**.

### Step 1: Locate the Skill
The entry point is the global or project skill located at:
`@/.codex/skills/aios-master`

### Step 2: Activate the Master Agent
In your Antigravity chat, type:
`@aios-master`

### Step 3: Verify the Greeting
The agent **Orion** will greet you, showing the current project status and available commands. Ensure the `.aios-core/development/scripts/generate-greeting.js` script runs successfully.

---

## 👑 2. Mastering the Orchestrator (Orion)

The `aios-master` agent is the universal executor. It can coordinate all other agents and execute framework-level tasks.

### Core Commands:
- `*help`: Shows all available orchestration commands.
- `*kb`: Toggles **Knowledge Base mode**. Loads the "AIOS Method" knowledge for expert decision-making.
- `*status`: Provides a report of the current context, active squad, and progress.
- `*guide`: Displays the comprehensive usage guide for the Master agent.

---

## 🏗️ 3. Orchestrating Squad Teams

Squads are modular teams of specialized agents. You can create your own squads or use existing ones.

### Step 1: Activate the Squad Creator
To manage teams, switch to the **Craft** (Squad Creator) agent:
`*squad-creator` (or use `@squad-creator`)

### Step 2: Designing a Squad from Documentation
If you have a PRD or technical requirement, use the intelligent designer:
`*design-squad --docs ./docs/prd/your-project.md`
> **Craft** will analyze the requirements and recommend a specific set of agents and tasks.

### Step 3: Creating the Squad
Once the design is approved, generate the squad structure:
`*create-squad {squad-name}`

This creates a new folder in `./squads/` with:
- `squad.yaml`: The team manifest.
- `agents/`: Local agent definitions.
- `tasks/`: Executable task-first workflows.
- `templates/`: Domain-specific document templates.

---

## 🔄 4. The Multi-Agent Workflow

AIOS operates in two primary phases facilitated by the orchestrator.

### Phase 1: Agentic Planning (Strategic)
1. **@analyst**: Create the Project Brief.
2. **@pm**: Develop the PRD (Product Requirements Document).
3. **@architect**: Design the System Architecture.

### Phase 2: Contextualized Development (Tactical)
1. **@sm**: Fragment the PRD into "User Stories" (markdown files in `docs/stories/`).
2. **@dev**: Implement the code based on the context provided in the Story file.
3. **@qa**: Validate the implementation against the acceptance criteria.

---

## 🛠️ 5. Advanced Execution Engine

The Orchestrator can run complex multi-step processes automatically.

### Running Workflows
To run a predefined sequence of agent actions:
`*run-workflow {workflow-name}`
> Use `greenfield-fullstack` for new projects or `brownfield-discovery` for existing codebases.

### IDS (Incremental Development System)
Before creating something new, check for reuse:
`*ids check "create authentication system"`
> This prevents duplication by checking the entity registry for existing components.

---

## 📚 6. Essential Documentation Links

For deeper dives into specific topics, refer to:
- **Core User Guide**: `docs/guides/user-guide.md`
- **Squads Overview**: `docs/guides/squads-overview.md`
- **Workflows Guide**: `docs/guides/workflows-guide.md`
- **Architecture Index**: `docs/architecture/ARCHITECTURE-INDEX.md`

---
*— Orion, orchestrating the system 🎯*
