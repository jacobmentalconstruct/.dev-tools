# Dev Log

_Last updated: 2026-04-11. Journal mirror lives in
`_docs/_journalDB/app_journal.sqlite3`._

---

## How to use this log

- Append a new dated section for each meaningful work phase.
- Record what changed, why it changed, and any trust boundary that matters.
- Keep summaries concise but complete.
- Use the journal DB for durable machine-queryable memory; use this file for
  fast human scanning.

---

## 2026-03-28 — Initial project authority kit (v2.0.0)

- Built the core `_app-journal` package with 10 MCP tools, Tkinter UI, and
  SQLite-backed journal store.
- Established the tool contract pattern: `FILE_METADATA` + `run(arguments)` +
  `standard_main()` + CLI via `metadata`/`run --input-json`.
- Seeded builder constraint contract, CAS blob store, action ledger, merkle
  snapshots.
- All 31 smoke tests passing at this point.

---

## 2026-04-02 — Intake cleanup: new builder tools

- Cleaned `_Possible_Intake` folder. Evaluated all candidates against existing
  tool surface.
- Packaged 4 new builder tools into `src/tools/`:
  - `module_decomp_planner` — AST-based module decomposition, groups defs by
    section headers, detects dependencies, scores extractability.
  - `tokenizing_patcher` — whitespace-immune hunk-based patcher using
    tokenization. Supports single-file, multi-target, and manifest modes.
  - `domain_boundary_audit` — AST-based domain boundary violation detector.
    Classifies imports into domains, flags functions crossing too many.
  - `scan_blocking_calls` — scans for `subprocess.run`, `time.sleep`,
    `os.system`, `urlopen`, etc. Supports custom `extra_blocking` patterns.
- Added `should_skip_dir()` and `DEFAULT_IGNORED_DIRS` to `src/common.py`.
- Registered all 4 in `tool_manifest.json` and `src/mcp_server.py`.
- Cleared `_Possible_Intake`.

---

## 2026-04-02 — Second intake dump: non-redundant tools extracted

- Received a second batch of loose files from a side branch.
- Found `domain_boundary_audit` and `scan_blocking_calls` as non-redundant
  additions (the first two had already been packaged).
- Cleared intake after extraction.

---

## 2026-04-11 — Three-tier architecture: vendable packages restored

- Identified that installable packages (`_app-journal`, `_manifold-mcp`,
  `_ollama-prompt-lab`) had drifted out of the canonical `.dev-tools` during
  earlier reorganizations.
- Designed and implemented three-tier toolbox architecture:
  - **Tier 1** (`src/tools/`) — builder tools that stay in the toolbox
  - **Tier 2** (`packages/`) — vendable packages installed INTO target projects
  - **Tier 3** (`templates/`) — vendable documents for cross-project reuse
- Copied all 3 packages from a prior project's `.claude/.dev-tools/`
  into `packages/`.
- Audited each for project-specific drift:
  - `_app-journal` — clean, no drift.
  - `_manifold-mcp` — 2 hardcoded absolute paths in example job files. Fixed
    to relative paths. Also upgraded `mcp_server.py` to support both
    Content-Length and NDJSON framing, and fixed `smoke_test.py` protocol
    mismatch.
  - `_ollama-prompt-lab` — clean, no drift.
- All 3 smoke tests passing.
- Created `toolbox_manifest.json` as the zero-context agent index for the
  three-tier layout.
- Updated `README.md`, `VENDORING.md` to reflect new architecture.
- Created `packages/README.md` and `templates/README.md`.
- Created this dev log.

Current state: 16 builder tools active, 3 vendable packages restored,
templates layer ready for content.

---

## 2026-04-11 — Builder Constraint Contract: vendable doc + atomic registry

- Received `_BuilderConstraintCONTRACT` folder via intake. Audited for
  project-specific drift: clean (only generic "sandbox" architectural
  concepts and proper author attribution). Fixed `LISCENSE.md` typo to
  `LICENSE.md`. Fixed duplicate section numbering (two 7.9s merged into
  7.9 + 7.10). Placed in `templates/_BuilderConstraintCONTRACT/`.
- Designed and built `_constraint-registry` vendable package:
  - Decomposed the 1,250-line BCC into 74 Atomic Constraint Units (ACUs)
  - Each ACU tagged with domain, severity (HARD_BLOCK/PUSHBACK/ADVISORY),
    and tier (spirit/letter/gate)
  - 8 pre-built task profiles: ui_implementation, core_implementation,
    refactoring, sourcing_extraction, documentation, cleanup,
    tool_creation, scaffolding
  - `registry_build` tool — builds the SQLite registry from seed data
  - `constraint_query` tool — queries by profile, domain, severity, tier,
    or specific UIDs
  - Full MCP server (NDJSON + Content-Length dual protocol)
  - 12/12 smoke tests passing
- Established clear separation: vendable registry (packages/_constraint-registry)
  vs full working contract (CONTRACT.md at .dev-tools root, never vended).
- Updated toolbox_manifest.json, packages/README.md, DEV_LOG.md.

Current state: 16 builder tools, 4 vendable packages, 1 vendable document
template. All smoke tests passing.

---

## 2026-04-11 — Three new builder tools: introspection quick wins

- Added `sqlite_schema_inspector` — reads any .sqlite3 file and returns
  structured schema: tables, columns, types, PKs, indexes, foreign keys,
  row counts, optional sample rows. Tested against `authority.sqlite3`
  (10 tables, full schema returned).
- Added `import_graph_mapper` — AST-based project import dependency graph.
  Classifies internal vs external imports, computes fan-in/fan-out per
  module, detects circular import chains. Tested against own `src/tools/`
  (19 modules scanned).
- Added `tkinter_widget_tree` — AST-based Tkinter widget hierarchy mapper.
  Extracts widgets, parent-child nesting, geometry manager calls,
  variable bindings, event bindings. Tested against `app_journal_ui.py`
  (12 widgets, tree structure, geometry calls all captured).
- All three registered in `tool_manifest.json` and `src/mcp_server.py`.
- All three follow the standard tool contract: `FILE_METADATA + run() +
  standard_main()`.

Current state: 19 builder tools, 4 vendable packages, 1 vendable document
template.

---

## Template for future entries

Journal entry: pending mirror

- Files changed:
  - list important files or subsystems
- What changed:
  - concise summary
- Why it changed:
  - the design or operational reason
- Validation:
  - tests, smoke checks, or manual verification
- Current read:
  - what is now true
