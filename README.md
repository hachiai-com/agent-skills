# Agent Skills

A collection of Skills for HachiAI: each skill is a folder containing a `SKILL.md` file (with optional YAML frontmatter) plus scripts, themes, licenses, and other assets the agent uses when the skill is relevant.

## Skills in this repository

| Folder                                    | Skill            | Summary                                                                                                                                                |
| ----------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [`algorithmic-art`](algorithmic-art/)      | algorithmic-art  | Generative art with p5.js: philosophy docs, HTML viewers, and JS sketches using seeded randomness and interactive parameters.                          |
| [`anthropic-branding`](antropic-branding/) | brand-guidelines | Apply Anthropic brand colors and typography to artifacts.                                                                                              |
| [`doc-coauthoring`](doc-coauthoring/)      | doc-coauthoring  | Structured workflow for co-authoring documentation, proposals, and technical specs.                                                                    |
| [`frontend-design`](frontend-design/)      | frontend-design  | Build distinctive, production-grade web UIs with strong visual direction.                                                                              |
| [`theme-factory`](theme-factory/)          | theme-factory    | Ten preset color/font themes for slides, docs, reports, and HTML; optional custom themes.                                                              |
| [`xl`](xlxs/)sx                            | xlsx             | Create, edit, and analyze spreadsheets (.xlsx, .xlsm, .csv, .tsv); includes Office validation scripts and formula recalculation helpers (LibreOffice). |

Several skills include a `LICENSE.txt` with usage terms; check each folder before redistribution.

## Repository layout

- **`SKILL.md`** — Primary instructions and triggers for the agent (name, description, workflow).
- **Supporting files** — e.g. `xlxs/scripts/` for spreadsheet tooling, `theme-factory/themes/` for theme definitions, `algorithmic-art/templates/` for HTML/JS templates.
