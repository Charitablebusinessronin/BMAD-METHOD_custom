# BMAD Fork: Notion + Neo4j

This fork customizes **BMAD-METHOD** with:

1. **Notion as source of truth** – Agent names, personas, and workflows from your Notion workspace  
2. **Neo4j as the memory layer** – Persistent, relationship-aware memory for BMAD agents  
3. **Aligned workflow structure** – Planning (Jay → John → Winston), Execution (Bob → Brooks/Barry → Troy), Support (Allura, Paige, Dr. Quinn, Sarah Boone)

---

## 1. Install in a new folder

You can use this fork as the BMAD layer in **any project folder**. Two ways:

### Option A: Clone into a new project folder

Use the fork as the whole project (e.g. a dedicated BMAD project or new product):

```bash
cd /path/to/your/projects
git clone https://github.com/YOUR_USER/BMAD-METHOD_custom.git my-project-name
cd my-project-name
```

Open `my-project-name` in Cursor. **Project root** = that folder; `_bmad-output` and config paths are relative to it.

### Option B: Copy BMAD into an existing project

To add BMAD (agents, workflows, Notion/Neo4j) into a repo you already have:

1. **Copy** into your project root: **`_bmad/`**, **`.cursor/`**, **`docs/`**
2. **Configure:**  
   - **`_bmad/core/config.yaml`** – Set `user_name`, `communication_language`; `output_folder` defaults to `{project-root}/_bmad-output`.  
   - **`_bmad/bmm/config.yaml`** – Set `project_name`, `user_name`; adjust `planning_artifacts`, `implementation_artifacts`, `project_knowledge` if you use custom paths.
3. **Open** your **project folder** in Cursor (that folder = `{project-root}`). BMAD writes outputs to `_bmad-output/` there.

Detailed steps and troubleshooting: **`docs/INSTALL-NEW-FOLDER.md`**.

---

## 2. Repo structure (after install)

- **`_bmad/core/agents/`** – Core agents (e.g. Sarah Boone → `bmad-master.md`)
- **`_bmad/bmm/agents/`** – BMM agents (Jay, John, Winston, Bob, Brooks, Barry, Dr. Quinn, Allura, Paige) as `.md` files
- **`_bmad/_config/agents/`** – Customization overrides (`*.customize.yaml`)
- **`_bmad/bmm/workflows/`** – Planning, solutioning, implementation workflows
- **`docs/`** – Memory schema, Notion integration, **install-in-new-folder** guide

---

## 3. Notion source of truth

Your agent team and workflows are defined in Notion. Use these pages as the canonical reference:

| Purpose | Notion link |
|--------|-------------|
| **Method overview & agent guide** | [BMAD Method Overview & Agent Guide](https://www.notion.so/BMAD-Method-Overview-Agent-Guide-2a11d9be65b380fd9f45fc483ff6f2eb?source=copy_link) |
| **Agent names & personas** | [BMAD Agent Names & Personas](https://www.notion.so/BMAD-Agent-Names-Personas-2e21d9be65b380778b94dd502b30fca8?source=copy_link) |
| **BMAD Agent Team (roles, workflows)** | [BMAD Agent Team](https://www.notion.so/3f0853a758b94c8d99f313ae9d80dc06?v=553ff992bed04b9980cb465fa666cd43&t=2fa1d9be65b38068b3eb00a97b498568) |

**Agent → file mapping** (from your Notion table):

| Agent | Role | File (in repo) |
|-------|------|----------------|
| Sarah Boone | Master Orchestrator | `_bmad/core/agents/bmad-master.md` + `_bmad/_config/agents/core-bmad-master.customize.yaml` |
| Jay | Analyst | `_bmad/bmm/agents/analyst.md` + `_bmad/_config/agents/bmm-analyst.customize.yaml` |
| John | Product Manager | `_bmad/bmm/agents/pm.md` + `_bmad/_config/agents/bmm-pm.customize.yaml` |
| Winston | Architect | `_bmad/bmm/agents/architect.md` + `_bmad/_config/agents/bmm-architect.customize.yaml` |
| Bob | Scrum Master | `_bmad/bmm/agents/sm.md` + `_bmad/_config/agents/bmm-sm.customize.yaml` |
| Brooks | Full Stack Developer | `_bmad/bmm/agents/dev.md` + `_bmad/_config/agents/bmm-dev.customize.yaml` |
| Barry | Quick Flow Solo Dev | `_bmad/bmm/agents/quick-flow-solo-dev.md` + `_bmad/_config/agents/bmm-quick-flow-solo-dev.customize.yaml` |
| Dr. Quinn | Problem Solver | `_bmad/bmm/agents/quinn.md` + `_bmad/_config/agents/bmm-quinn.customize.yaml` |
| Allura | UX Designer | `_bmad/bmm/agents/ux-designer.md` + `_bmad/_config/agents/bmm-ux-designer.customize.yaml` |
| Paige | Technical Writer | `_bmad/bmm/agents/tech-writer/` + `_bmad/_config/agents/bmm-tech-writer.customize.yaml` |

**Workflows (from Notion):**

- **Planning:** Jay → John → Winston  
- **Execution:** Bob → Brooks/Barry → Troy  
- **Support:** Allura (UX), Paige (docs), Dr. Quinn (debugging), Sarah Boone (orchestration)

Prompts and persona text can be **pulled from Notion** via the Notion MCP (`notion-fetch`). See `docs/NOTION-AND-NEO4J-INTEGRATION.md` for the full integration pattern.

---

## 4. Neo4j memory layer

BMAD uses a **running Neo4j container** and the **user-neo4j-memory** MCP server to:

- **Store** structured memories (e.g. Event / Outcome / Insight) during workflows  
- **Retrieve** and **search** memories (with optional graph context) for context in later sessions  
- **Manage relations** between memories (e.g. handoffs, dependencies, influences)

Ensure:

1. Neo4j is running (e.g. Docker on the configured port).  
2. The **user-neo4j-memory** MCP is enabled in Cursor and points at that instance.  
3. Agents and workflows use the **memory schema** in `docs/memory-schema.md` when calling `memory_manage`, `memory_retrieve`, `memory_search`, and `relation_manage`.

The **notion-to-planning** skill already logs to Neo4j (Event → Outcome → Insight) via `memory_manage`. The same pattern is extended so that all BMAD workflows can read/write and link memories for persistent, relationship-aware context.

---

## 5. Next steps

1. **Install** in a new folder (clone or copy per section 1).  
2. **Fetch** your Notion pages with the Notion MCP to pull latest agent/workflow copy into the repo or into Neo4j metadata.  
3. **Adapt** `_bmad/bmm/workflows/` and agent YAMLs to match your operating model (Planning / Execution / Support).  
4. **Wire** workflow steps to Neo4j: log at handoffs and milestones; search/retrieve before starting a step.  
5. **Use** `docs/NOTION-AND-NEO4J-INTEGRATION.md` and `docs/memory-schema.md` as the single reference for Notion URLs, agent mapping, and Neo4j conventions.

---

## Quick reference

- **Install in new folder:** `docs/INSTALL-NEW-FOLDER.md`  
- **Notion Method Overview & Agent Guide:**  
  `https://www.notion.so/BMAD-Method-Overview-Agent-Guide-2a11d9be65b380fd9f45fc483ff6f2eb`  
- **Notion Agent Names & Personas:**  
  `https://www.notion.so/BMAD-Agent-Names-Personas-2e21d9be65b380778b94dd502b30fca8`  
- **Notion BMAD Agent Team:**  
  `https://www.notion.so/3f0853a758b94c8d99f313ae9d80dc06?v=553ff992bed04b9980cb465fa666cd43&t=2fa1d9be65b38068b3eb00a97b498568`  
- **Neo4j memory schema:** `docs/memory-schema.md`  
- **Full integration design:** `docs/NOTION-AND-NEO4J-INTEGRATION.md`
