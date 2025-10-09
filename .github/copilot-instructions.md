<!-- Copilot instructions for the `docs` mkdocs site -->
# Copilot instructions — docs repository

This repository is a documentation site built with MkDocs (Material theme). The goal of these instructions is to help an AI coding agent be immediately productive when editing, building and deploying docs.

Summary (big picture)
- This project uses MkDocs with `mkdocs-material` (see `mkdocs.yml`).
- Content lives under the `docs/` directory and is organized by course folders (e.g. `DSM5 | PDMII/`).
- GitHub Actions publish the site to GitHub Pages using `mkdocs gh-deploy` (see `.github/workflows/ci.yml` and `.github/workflows/ci_test.yml`).

What you can edit
- Markdown files under `docs/` (headings, code blocks, images). Keep file names and folder structure stable: navigation is inferred from files and folders.
- `mkdocs.yml` for site config (theme, extensions, navigation order).

How to build & preview locally
- Reproduce CI locally with these commands (Ubuntu / Python 3):

```bash
python3 -m pip install --user mkdocs-material
mkdocs serve
```

That runs a local dev server on http://127.0.0.1:8000. To deploy the compiled site (same as CI):

```bash
python3 -m pip install --user mkdocs-material
mkdocs gh-deploy --force
```

CI & deployment notes
- CI workflows use `actions/setup-python` and run `pip install mkdocs-material` then `mkdocs gh-deploy --force`.
- Changes under `docs/**` trigger the `Build GitHub Pages` workflow which builds and deploys.

Repository conventions and useful patterns
- Keep image assets next to the markdown that references them (see `docs/DSM5 | PDMII/image-*.png`). Use relative paths in markdown.
- Many folder names include a pipe character (`|`) and spaces (e.g. `DSM5 | PDMII`). Preserve these names; navigation and links assume them.
- Use standard Markdown; the project enables these extensions in `mkdocs.yml`: `attr_list`, `pymdownx.*` (snippets, superfences, inlinehilite, highlight).
- Do not rename `index.md` at root — it is the site entry point.

Examples from the codebase
- Edit `docs/DSM5 | PDMII/5. Navegação.md` to update navigation content.
- To change theme behavior, edit `mkdocs.yml` (font, palette, features like `content.code.copy`).
- CI workflow files: `.github/workflows/ci.yml` and `.github/workflows/ci_test.yml` show how deploys are performed.

When making PRs
- Small documentation edits: single-file PRs are fine. Large reorganizations: update `mkdocs.yml` navigation if necessary and include a preview link or description of the new structure.
- Avoid changing folder names with `|` or spaces unless necessary — they are part of the current organization and may break links.

What not to do
- Don’t remove the `mkdocs.yml` extensions without ensuring equivalence — pages rely on `pymdownx` features (fenced code, snippets).
- Don’t assume a frontend codebase or JS build; this repository is documentation-only (no package.json, no node build steps present).

If you need more context
- Refer to `mkdocs.yml`, `docs/` and `.github/workflows/*.yml` for the canonical build/deploy flow.
- If you must change CI behavior, mirror the existing workflow pattern and test `mkdocs serve` locally before pushing.

If anything here is unclear or you need a different level of detail (e.g., explicit navigation ordering in `mkdocs.yml`), ask and I will iterate.
