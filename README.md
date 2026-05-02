# Wocheneinkauf Site

Static website for Wocheneinkauf App Store metadata.

## Pages

The site is organized as mirrored English and German page trees where English is the default at
the root. The app name in the header is the only link back to the current language's main page.

### English

- `/` - marketing page
- `/privacy/` - English privacy summary
- `/support/` - support page

### German

- `/de/` - German marketing page
- `/datenschutz/` - German privacy notice
- `/de/support/` - German support page

### Legal

- `/impressum/` - German legal notice

## Navigation Rules

- Header navigation links only to subpages in the current language.
- Footer navigation mirrors the current language's subpages and also includes `/impressum/`.
- The main page is not listed in header or footer navigation; use the app name link instead.
- Language links are separate from normal navigation:
  - `/` <-> `/de/`
  - `/privacy/` <-> `/datenschutz/`
  - `/support/` <-> `/de/support/`
- `/impressum/` is German-only and has no language switch.

## GitHub Pages

Publish from the repository root on the default branch:

1. Push this repository to GitHub.
2. Open the repository settings.
3. Go to Pages.
4. Set source to `Deploy from a branch`.
5. Select the default branch and `/ (root)`.

Suggested App Store Connect URLs:

- Marketing URL: `https://<domain>/`
- German Marketing URL: `https://<domain>/de/`
- Privacy Policy URL: `https://<domain>/privacy/`
- German Privacy Policy URL: `https://<domain>/datenschutz/`
- Support URL: `https://<domain>/support/`
- German Support URL: `https://<domain>/de/support/`
