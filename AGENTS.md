# Wocheneinkauf Site Agent Notes

Keep this file specific to the static website. Follow the workspace `../AGENTS.md` for shared workflow, GitHub, git, security, and response rules.

## Design System

- Read `../DESIGN.md` before changing layout, visual styling, marketing sections, screenshots, colors, typography, spacing, radii, shadows, or component patterns.
- Treat `../DESIGN.md` as the shared design contract with the iOS app. The site may be more editorial and spacious, but it should keep the same mint-teal brand accent, rounded system typography, restrained surfaces, and phone-first product signal.
- If a requested site change intentionally changes the design direction, update `../DESIGN.md` in the same task.
- Reuse `assets/styles.css` and existing page structure. Avoid one-off inline styles unless the local page already uses that pattern and the change is tightly scoped.

## Site Scope

- This repository is a static GitHub Pages site published from the repository root.
- The page hierarchy is mirrored by language:
  - English: `/`, `/privacy/`, `/support/`
  - German: `/de/`, `/datenschutz/`, `/de/support/`
  - Legal-only: `/impressum/`
- The app name in the header is the only link to the current language's main page. Do not add `Home`, `Startseite`, or equivalent main-page links to header or footer navigation.
- Header navigation should only contain subpages from the current language: English pages use `Privacy` and `Support`; German pages use `Datenschutz` and `Support`.
- Footer navigation should mirror the current language's subpages and include `Impressum`: English pages use `Privacy`, `Support`, `Impressum`; German pages use `Datenschutz`, `Support`, `Impressum`.
- Keep language switches visually and structurally separate from normal navigation using the shared language switch pattern. Pair pages as `/` <-> `/de/`, `/privacy/` <-> `/datenschutz/`, and `/support/` <-> `/de/support/`.
- `/impressum/` is German-only legal content and should not include a language switch.
- Keep `docs/app-feature-inventory.md` aligned with the current iOS app when app features, tabs, settings, data flows, privacy-relevant behavior, or user-facing workflows change.
- Preserve German copy where present. Do not translate, soften, or rewrite legal and privacy text unless the task explicitly asks for that.
- Do not add build tooling, package managers, or generated assets unless the task requires them.

## Verification

- For content-only changes, inspect the changed files and check links or metadata touched by the change.
- For rendered HTML or CSS changes, validate the affected page in a browser. The site is static, so opening the changed page directly is acceptable unless hosted behavior matters.
- Check the relevant desktop and mobile widths when layout, navigation, or responsive styling changes.
