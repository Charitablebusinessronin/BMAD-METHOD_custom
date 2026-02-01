# Neo4j memory schema for BMAD

Conventions for **memory types**, **metadata**, and **relation types** when using the **user-neo4j-memory** MCP with BMAD. Use these so all agents and workflows share a consistent, relationship-aware graph.

---

## 1. Memory types

Use these `memoryType` values in **memory_manage** (and filter on them in **memory_search** with `memoryTypes`).

| memoryType | When to use | Typical observations |
|------------|-------------|------------------------|
| **Event** | Something that happened (e.g. “Notion PRD fetched”, “Sprint started”) | Short description; optional link to artifact or Notion URL |
| **Outcome** | Result of a step (e.g. “Epics EPIC-001..003 created”, “Story STORY-1-2 accepted”) | What was produced; paths or IDs |
| **Insight** | Learned fact or decision (e.g. “Tech stack: Next.js + Neo4j”, “Auth: OIDC”) | One insight per observation if needed |
| **Constraint** | Non‑negotiable requirement or limit | Source (e.g. stakeholder, security) and rule |
| **Handoff** | Explicit handoff between agents (e.g. John → Winston) | From-agent, to-agent, artifact or summary |
| **Artifact** | Reference to a file or Notion page | Path, URL, or ID; brief description |

**Notion-to-planning** already uses Event / Outcome / Insight; keep that alignment and add Handoff, Constraint, Artifact as needed.

---

## 2. Metadata (recommended fields)

Include in **memory_manage** `metadata` when relevant (all optional; add your own):

| Field | Type | Purpose |
|-------|------|--------|
| **project** | string | Project or repo name |
| **epicId** | string | e.g. `EPIC-001` |
| **storyId** | string | e.g. `STORY-1-2` |
| **agent** | string | BMAD agent name (e.g. Jay, John, Winston, Sarah Boone) |
| **workflow** | string | e.g. `planning`, `execution`, `support` |
| **path** | string | File path (e.g. `_bmad-output/planning-artifacts/epics/EPIC-001.md`) |
| **notionUrl** | string | Notion page URL if memory is tied to a Notion source |
| **language** | string | e.g. `en` (recommended by MCP for semantic search) |

---

## 3. Relation types

Use these `relationType` values in **relation_manage** to link memories.

| relationType | Direction | Meaning |
|--------------|-----------|--------|
| **HANDOFF_TO** | Step A → Step B | Agent/workflow step A handed off to B |
| **INFORMED_BY** | Later → Earlier | Current outcome was informed by a prior memory |
| **DEPENDS_ON** | Task/outcome → Prerequisite | This memory depends on another (e.g. story depends on epic) |
| **CONTAINS** | Parent → Child | Parent artifact or epic contains child (e.g. epic contains stories) |
| **INFLUENCES** | Memory A → Memory B | A influences B (decisions, constraints) |
| **REFERENCES** | Memory → Artifact/Notion | Memory points to an external artifact or Notion page |

**Strength** (0.0–1.0): use default `0.5` or set higher for critical links. **Source**: `agent` | `user` | `system` as appropriate.

---

## 4. Example: planning chain

1. **Jay** finishes analysis → **memory_manage** create:
   - memoryType: `Outcome`
   - name: e.g. `analysis-{project}-{date}`
   - observations: summary of requirements/discovery
   - metadata: `agent: "Jay"`, `workflow: "planning"`, `project`, `language: "en"`

2. **John** consumes Jay’s output → **memory_search** for recent Jay outcomes; then **memory_manage** create:
   - memoryType: `Outcome`
   - name: e.g. `prd-{project}-{date}`
   - metadata: `agent: "John"`, `workflow: "planning"`, `project`
   - **relation_manage** create: fromId = John’s memory, toId = Jay’s memory, relationType: `INFORMED_BY`

3. **Winston** consumes John’s PRD → same pattern; **relation_manage** John → Winston with `HANDOFF_TO` or Winston → John with `INFORMED_BY`.

4. Later **Bob** (execution) can **memory_search** for `workflow: "planning"` or by `project` and get the chain via `includeGraphContext: true`.

---

## 5. Example: notion-to-planning

After generating epics and stories from a Notion PRD:

- **memory_manage** (create):
  - memoryType: `Event` – “Notion PRD fetched”, notionUrl in metadata
  - memoryType: `Outcome` – “Epics EPIC-001..003 created”, path to index in metadata
  - memoryType: `Insight` – key decisions extracted from PRD
- **relation_manage**: link Outcome → Event (`INFORMED_BY`), Insight → Event (`REFERENCES` or `INFORMED_BY`).

This matches the **notion-to-planning** skill and extends cleanly to execution (Bob, Brooks/Barry, Troy) using the same types and relations.

---

## 6. Summary

- **Memory types:** Event, Outcome, Insight, Constraint, Handoff, Artifact  
- **Metadata:** project, epicId, storyId, agent, workflow, path, notionUrl, language  
- **Relations:** HANDOFF_TO, INFORMED_BY, DEPENDS_ON, CONTAINS, INFLUENCES, REFERENCES  

Use **memory_search** with `includeGraphContext: true` and optional `memoryTypes` so agents get both relevant memories and their graph context for relationship-aware behavior.
