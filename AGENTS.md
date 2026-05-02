# Wocheneinkauf Site Agent Notes

Keep this file specific to the static website. Follow the workspace `../AGENTS.md` for shared workflow, GitHub, git, security, and response rules.

## Design System

- Read `../DESIGN.md` before changing layout, visual styling, marketing sections, screenshots, colors, typography, spacing, radii, shadows, or component patterns.
- Treat `../DESIGN.md` as the shared design contract with the iOS app. The site may be more editorial and spacious, but it should keep the same mint-teal brand accent, rounded system typography, restrained surfaces, and phone-first product signal.
- If a requested site change intentionally changes the design direction, update `../DESIGN.md` in the same task.
- Reuse `assets/styles.css` and existing page structure. Avoid one-off inline styles unless the local page already uses that pattern and the change is tightly scoped.

## Site Scope

- This repository is a static GitHub Pages site published from the repository root.
- Main pages are `/`, `/privacy/`, `/datenschutz/`, `/support/`, and `/impressum/`.
- Keep `docs/app-feature-inventory.md` aligned with the current iOS app when app features, tabs, settings, data flows, privacy-relevant behavior, or user-facing workflows change.
- Preserve German copy where present. Do not translate, soften, or rewrite legal and privacy text unless the task explicitly asks for that.
- Do not add build tooling, package managers, or generated assets unless the task requires them.

## Verification

- For content-only changes, inspect the changed files and check links or metadata touched by the change.
- For rendered HTML or CSS changes, validate the affected page in a browser. The site is static, so opening the changed page directly is acceptable unless hosted behavior matters.
- Check the relevant desktop and mobile widths when layout, navigation, or responsive styling changes.
