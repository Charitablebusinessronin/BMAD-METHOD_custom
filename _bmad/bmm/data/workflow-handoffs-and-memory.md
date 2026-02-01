# Workflow handoffs and Neo4j memory

This fork uses **Planning**, **Execution**, and **Support** chains aligned with Notion. Use Neo4j for persistent, relationship-aware memory at handoffs and milestones.

---

## Agent chains (from Notion)

**Planning:** Jay (Analyst) → John (PM) → Winston (Architect)  
**Execution:** Bob (Scrum Master) → Brooks / Barry (Dev) → Troy (Test)  
**Support:** Allura (UX), Paige (docs), Dr. Quinn (debugging), Sarah Boone (orchestration)

Agents ARE their names. Never refer to them by any inspiration or alternate name.

---

## When to log to Neo4j

- **After a workflow step completes** (e.g. PRD created, architecture doc done, sprint planned): call **memory_manage** (create) with type **Outcome**; include agent, workflow, and summary in metadata/observations.
- **At handoffs** (e.g. John finishing PRD and handing to Winston): call **memory_manage** for the completing agent’s outcome, then **relation_manage** to link to the next step (e.g. HANDOFF_TO or INFORMED_BY).
- **Before starting a step**: optionally **memory_search** (e.g. by project or workflow) with `includeGraphContext: true` to load prior outcomes and decisions.

Full memory types and relation types: **{project-root}/docs/memory-schema.md**.

---

## Workflow.xml protocol

Use **`<invoke-protocol name="log_memory" />`** in workflow instructions after a step or at completion to log the outcome to Neo4j. The protocol is defined in **{project-root}/_bmad/core/tasks/workflow.xml** and references docs/memory-schema.md.

---

## BMM workflow mapping

| Phase | Primary agent | Typical workflows |
|-------|----------------|--------------------|
| Analysis | Jay | research, create-product-brief, document-project |
| Planning | John | create-prd, create-ux-design (Allura), create-architecture (Winston), create-epics-and-stories, check-implementation-readiness |
| Execution | Bob → Brooks/Barry | sprint-planning, dev-story, create-story, code-review, correct-course, retrospective |
| Support | Allura, Paige, Dr. Quinn, Sarah Boone | UX flows, documentation, debugging, orchestration |
