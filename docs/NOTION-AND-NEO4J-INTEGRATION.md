# Notion + Neo4j Integration for BMAD

This document defines how your **Notion workspace** and **Neo4j memory** integrate with the BMAD fork: prompts and workflows come from Notion; persistent, relationship-aware context lives in Neo4j.

---

## 1. Notion as source of truth

### Canonical pages

| Page | Use | MCP usage |
|------|-----|-----------|
| [BMAD Method Overview & Agent Guide](https://www.notion.so/BMAD-Method-Overview-Agent-Guide-2a11d9be65b380fd9f45fc483ff6f2eb) | Hub: checklists, tasks, workflows, data, BMAD databases | `notion-fetch` for checklists/tasks/workflows/data links and database refs |
| [BMAD Agent Names & Personas](https://www.notion.so/BMAD-Agent-Names-Personas-2e21d9be65b380778b94dd502b30fca8) | Agent names, personas, “agents ARE their names” | `notion-fetch` with this URL to pull latest copy |
| [BMAD Agent Team](https://www.notion.so/3f0853a758b94c8d99f313ae9d80dc06?v=553ff992bed04b9980cb465fa666cd43&t=2fa1d9be65b38068b3eb00a97b498568) | Roles, FILE NAME mapping, workflows (Planning / Execution / Support) | `notion-fetch` to sync agent→file mapping and workflow chains |

### Pulling prompts into the project

1. **Fetch** – Use Notion MCP `notion-fetch` with the page URL or ID.
2. **Parse** – Extract tables (Agent, Role, FILE NAME) and workflow bullets (Jay → John → Winston, etc.).
3. **Map** – Align to repo paths:
   - Core: `_bmad/core/agents/bmad-master.md` + `_bmad/_config/agents/core-bmad-master.customize.yaml` (Sarah Boone)
   - BMM: `_bmad/bmm/agents/<agent>.md` + `_bmad/_config/agents/bmm-<agent>.customize.yaml` per your “Original File Names” table.
4. **Sync** – Either:
   - **Option A:** Write/update agent YAML files from Notion content (persona, role, principles).
   - **Option B:** Keep agent YAML in repo and use Notion only as reference; periodically re-fetch to check drift.

### Workflow structure (from your Notion)

- **Planning:** Jay (Analyst) → John (PM) → Winston (Architect)  
- **Execution:** Bob (Scrum Master) → Brooks / Barry (Dev) → Troy (Test)  
- **Support:** Allura (UX), Paige (docs), Dr. Quinn (debugging), Sarah Boone (orchestration)

Adapt `_bmad/bmm/workflows/` and any workflow YAML so that:

- Phase/step names and handoffs match this chain.
- Each handoff can log to Neo4j (see memory schema) and optionally load context via `memory_search` / `memory_retrieve`.

---

## 2. Neo4j as the memory layer

### MCP tools (user-neo4j-memory)

| Tool | Purpose |
|------|--------|
| **memory_manage** | Create/update/delete memories (name, memoryType, metadata, observations). |
| **memory_retrieve** | Get memories by ID; updates lastAccessed; returns relations. |
| **memory_search** | Semantic search; optional `includeGraphContext` for related memories. |
| **observation_manage** | Add/delete observations on existing memories. |
| **relation_manage** | Create/delete relations between memories (fromId, toId, relationType, strength, source). |

### When to use Neo4j in BMAD

1. **After a workflow step or handoff**  
   - Call **memory_manage** (create) with type `Event` or `Outcome` or `Insight` (see `memory-schema.md`).  
   - Call **relation_manage** (create) to link this memory to the previous step’s memory (e.g. `HANDOFF_TO`, `INFORMED_BY`).

2. **Before starting a step**  
   - Call **memory_search** (e.g. by project/epic/story or agent name) with `includeGraphContext: true`.  
   - Optionally **memory_retrieve** specific IDs from prior steps to load full context.

3. **When resolving ambiguity**  
   - **memory_search** for past decisions, constraints, or outcomes so the current agent stays aligned.

4. **Notion-to-planning flow**  
   - The **notion-to-planning** skill already logs Event/Outcome/Insight via **memory_manage**.  
   - Use the same memory types and relation types so planning memories connect to execution memories.

### Prerequisites

- Neo4j running (e.g. Docker) and reachable.
- **user-neo4j-memory** MCP configured in Cursor and pointing at that instance.
- All agents/workflows that use memory follow `docs/memory-schema.md` for types and relations.

---

## 3. End-to-end flow

```
Notion (agent definitions, workflows)
    ↓ notion-fetch
BMAD repo (agent YAML, workflow configs)
    ↓ execution
Workflow steps (Jay → John → Winston → Bob → Brooks/Barry → Troy)
    ↓ at each step / handoff
Neo4j (memory_manage, relation_manage)
    ↓ at next step / new session
memory_search / memory_retrieve → context for current agent
```

Result: BMAD runs with **Notion-driven prompts and workflows** and **Neo4j-backed, relationship-aware memory** across planning and execution.

---

## 4. File placement (BMAD conventions)

- **Epics:** `_bmad-output/planning-artifacts/epics/EPIC-*.md`  
- **Stories:** `_bmad-output/implementation-artifacts/stories/STORY-*-*.md`  
- **Index:** `_bmad-output/planning-artifacts/EPICS-AND-STORIES-INDEX.md`  
- **Sprint status:** `_bmad-output/implementation-artifacts/sprint-status.yaml`  

Neo4j memories can store references to these paths (e.g. in `metadata.path` or in observations) so retrieval can point the agent to the right artifact.

---

## 5. References

- Notion Agent Names & Personas: `https://www.notion.so/BMAD-Agent-Names-Personas-2e21d9be65b380778b94dd502b30fca8`  
- Notion BMAD Agent Team: `https://www.notion.so/3f0853a758b94c8d99f313ae9d80dc06?v=553ff992bed04b9980cb465fa666cd43&t=2fa1d9be65b38068b3eb00a97b498568`  
- Memory types and relation types: `docs/memory-schema.md`  
- Fork setup overview: `README-FORK-SETUP.md`
