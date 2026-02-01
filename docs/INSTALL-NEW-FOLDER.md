# Install BMAD fork in a new folder

This guide explains how to use this BMAD fork (Notion + Neo4j) in **any project folder**—either as a new project or by adding BMAD into an existing repo.

---

## How “project root” works

- **Project root** = the folder you open in Cursor (the workspace root).
- All BMAD paths use `{project-root}`: e.g. `output_folder: "{project-root}/_bmad-output"`.
- So the **same fork** can be installed in many folders; each folder is one “project” with its own `_bmad-output/` and config.

---

## Option A: Clone into a new project folder

Use the fork as the entire project (e.g. a new product or BMAD-only repo).

### Steps

1. **Clone** the repo into a new folder (use that folder as project root):

   ```bash
   cd /path/to/your/projects
   git clone https://github.com/YOUR_USER/BMAD-METHOD_custom.git my-project-name
   cd my-project-name
   ```

2. **Open** `my-project-name` in Cursor (File → Open Folder → `my-project-name`). That folder is `{project-root}`.

3. **Configure** (optional):
   - **`_bmad/core/config.yaml`** – `user_name`, `communication_language`.
   - **`_bmad/bmm/config.yaml`** – `project_name`, `user_name`; change `planning_artifacts` / `implementation_artifacts` / `project_knowledge` only if you use non-default paths.

4. **Run** – Invoke Sarah Boone (`/sarah`) or any agent; outputs go to `_bmad-output/` in that folder.

---

## Option B: Copy BMAD into an existing project

Add BMAD (agents, workflows, Notion/Neo4j) to a repo you already have, without replacing the repo.

### What to copy

Copy these **into your existing project root** (the repo root you open in Cursor):

| Copy this   | Purpose |
|------------|---------|
| **`_bmad/`**   | Agents, workflows, config, tasks. Required. |
| **`.cursor/`** | Commands and rules so Cursor sees BMAD agents and the Notion/Neo4j rule. |
| **`docs/`**    | Memory schema, Notion integration, this install guide. |

Do **not** copy the rest of the BMAD repo (e.g. other top-level files) unless you want them. Your existing `.gitignore` and project files stay as they are.

### Steps

1. **Copy** the three items above from this fork into your project root.  
   - If you already have a `docs/` folder, merge the BMAD `docs/` contents into it (e.g. add `memory-schema.md`, `NOTION-AND-NEO4J-INTEGRATION.md`, `INSTALL-NEW-FOLDER.md`, `NOTION-METHOD-OVERVIEW.md`).  
   - If you already have `.cursor/`, merge BMAD’s `.cursor/commands/` and `.cursor/rules/` into it.

2. **Configure** for this project:
   - **`_bmad/core/config.yaml`**  
     - `user_name` – Your name (or team default).  
     - `communication_language` – e.g. `English`.  
     - `output_folder` – Default `{project-root}/_bmad-output` is fine; BMAD will create `_bmad-output/` in the folder you open in Cursor.
   - **`_bmad/bmm/config.yaml`**  
     - `project_name` – This project’s name.  
     - `user_name` – Same as core.  
     - `planning_artifacts` – Default `{project-root}/_bmad-output/planning-artifacts`.  
     - `implementation_artifacts` – Default `{project-root}/_bmad-output/implementation-artifacts`.  
     - `project_knowledge` – Optional; default `{project-root}/docs` is fine.

3. **Open** your **project folder** in Cursor (the repo root). That folder = `{project-root}`. BMAD will write to `_bmad-output/` there.

4. **Optional:** Add `_bmad-output/` to `.gitignore` if you don’t want to commit BMAD outputs.

---

## Multiple folders / multiple projects

- **Different folder = different project.**  
  Clone (or copy) the fork into as many folders as you like; each folder is independent.
- **Same Neo4j:** If you use one Neo4j instance and one **user-neo4j-memory** MCP, memories are shared across projects (use `metadata.project` or similar in the memory schema to distinguish projects).
- **Same Notion:** Notion pages are shared; only config (e.g. `user_name`, `project_name`) is per folder.

---

## Troubleshooting

- **Agents or workflows not found** – Ensure the folder you opened in Cursor is the one that contains `_bmad/` (project root). Paths are resolved from there.
- **Outputs in the wrong place** – Check `_bmad/core/config.yaml` and `_bmad/bmm/config.yaml`; `output_folder` and artifact paths should use `{project-root}` so they stay inside the opened folder.
- **Cursor doesn’t show BMAD commands** – Ensure `.cursor/commands/` and `.cursor/rules/` were copied (or merged) into the project’s `.cursor/` directory.
- **Neo4j / Notion not used** – Enable the **user-neo4j-memory** and **user-Notion** MCPs in Cursor and point them at your instances; see `README-FORK-SETUP.md` and `docs/NOTION-AND-NEO4J-INTEGRATION.md`.

---

## Summary

| Goal                         | Action |
|-----------------------------|--------|
| New project from scratch    | Clone fork into new folder → open folder in Cursor → configure configs. |
| Add BMAD to existing repo   | Copy `_bmad/`, `.cursor/`, `docs/` into repo root → configure configs → open repo in Cursor. |
| Use in another folder later | Clone or copy again into that folder; each folder is its own project. |

Config is per folder; Notion and Neo4j (if configured) are shared across projects that use the same MCPs and servers.
