---
name: obsidian-plugin-review
description: Review an Obsidian Community Plugin according to Obsidian's submission requirements and developer policies.
license: MIT
metadata:
  author: moraym
  version: "1.0.0"
---

# Agent skill: Obsidian plugin review

Use this checklist to review an Obsidian community plugin for compliance with Obsidian’s submission requirements, developer policies, and automated validation. Treat it as the source of truth for “will this pass review?” and “is this policy-compliant?”.

---

## 1. Official references

- **Developer policies:** https://docs.obsidian.md/Developer+policies  
- **Submission requirements:** https://docs.obsidian.md/Plugins/Releasing/Submission+requirements+for+plugins  
- **Plugin guidelines:** https://docs.obsidian.md/Plugins/Releasing/Plugin+guidelines  
- **Automated validation (canonical):** https://github.com/obsidianmd/obsidian-releases/blob/master/.github/workflows/validate-plugin-entry.yml  
- **PR template (submission checklist):** https://raw.githubusercontent.com/obsidianmd/obsidian-releases/refs/heads/master/.github/PULL_REQUEST_TEMPLATE/plugin.md  

---

## 2. Automated validation (from `validate-plugin-entry.yml`)

These checks run when a PR edits `community-plugins.json`. **Errors** block submission; **warnings** do not but should be fixed when possible.

### 2.1 PR and repo

| Check | Type | Rule |
|-------|------|------|
| Changed files | Error | PR must change **only** `community-plugins.json`. No other files. |
| Maintainer edit | Warning | “Maintainers of this repo should be allowed to edit this pull request.” |
| PR body template | Error | PR body must contain **every** of these exact phrases (from the plugin PR template): |
| | | • `I have tested the plugin on` |
| | | • `My GitHub release contains all required files (as individual files, not just in the source.zip / source.tar.gz)` |
| | | • `GitHub release name matches the exact version number specified in my manifest.json` |
| | | • `The \`id\` in my \`manifest.json\` matches the \`id\` in the \`community-plugins.json\` file.` |
| | | • `My README.md describes the plugin's purpose and provides clear usage instructions.` |
| | | • `I have read the developer policies at https://docs.obsidian.md/Developer+policies, and have assessed my plugin` |
| | | • `I have read the tips in https://docs.obsidian.md/Plugins/Releasing/Plugin+guidelines and have self-reviewed my plugin to avoid these common pitfalls` |
| | | • `I have added a license in the LICENSE file.` |
| | | • `My project respects and is compatible with the original license of any code from other plugins that I'm using.` |
| | | • `I have given proper attribution to these other projects in my \`README.md\`.` |
| Repo format | Error | `repo` must be exactly `owner/repo` (e.g. `moraym/omdb-fetcher`). |
| Repo exists | Error | Repo must exist and be accessible. |
| Submitter | Error | PR author must be the repo `owner` or a **public** member of that org. |
| Issues enabled | Warning | Repo should have issues enabled. |

### 2.2 `community-plugins.json` entry (the new plugin object)

**Required keys only:** `id`, `name`, `description`, `author`, `repo`.  
**Invalid key:** Any key not in that list → Error.

| Field | Check | Type | Rule |
|-------|--------|------|------|
| **id** | No “obsidian” | Error | `id` must not contain the word `obsidian` (e.g. not `obsidian-omdb-fetcher`). |
| **id** | No “plugin” suffix | Error | `id` must not end with `plugin`. |
| **id** | Format | Error | Only `[a-z0-9-_]+` (lowercase letters, digits, hyphen, underscore). |
| **id** | Unique | Error | No other plugin in the list may have the same `id`. |
| **id** | Not removed | Error | `id` must not match an entry in `community-plugins-removed.json`. |
| **name** | No “Obsidian” | Error | Must not contain `Obsidian`. |
| **name** | No “Plugin” | Error | Must not end with `Plugin`. |
| **name** | No “Obsi”/“dian” | Error | Must not start with `Obsi` or end with `dian`. |
| **name** | Unique | Error | No other plugin may have the same `name`. |
| **name** | Removed | Warning | If name was used by a removed plugin, recommend a different name. |
| **author** | Not email | Warning | Prefer name; discourage using a raw email in `author`. |
| **description** | No “Obsidian” | Error | Must not contain `Obsidian`. |
| **description** | No “this plugin” etc. | Warning | Avoid “this plugin”, “this is a plugin”, “this plugin allows”. |
| **description** | Ending | Error | Must end with one of: `.` `?` `!` `)`. |
| **description** | Length | Error | Must be ≤ 250 characters. |

### 2.3 Repo `manifest.json` (at repo root)

**Required keys:** `id`, `name`, `description`, `author`, `version`, `minAppVersion`, `isDesktopOnly`.  
**Allowed extra keys only:** `authorUrl`, `fundingUrl`, `helpUrl`.  
Any other key → Error.

| Check | Type | Rule |
|-------|------|------|
| Present & parseable | Error | `manifest.json` must exist at repo root and be valid JSON. |
| ID match | Error | `manifest.id` must equal the `id` in the PR’s `community-plugins.json` entry. |
| Name match | Error | `manifest.name` must equal the `name` in the PR. |
| Description match | Error | `manifest.description` must equal the `description` in the PR. |
| **authorUrl** | Error | Must not be `https://obsidian.md`. Must not be the plugin repo URL (e.g. `github.com/owner/repo`). |
| **fundingUrl** | Error | Must not be `https://obsidian.md/pricing`. Must not be empty string; if no funding link, omit the key. |
| **version** | Error | Only numbers and dots: `/^[0-9.]+$/` (e.g. `1.0.0`; no `v` prefix). |
| GitHub release | Error | A GitHub release whose **tag** equals `manifest.version` must exist. |
| Release assets | Error | That release must have **individual** assets: `main.js`, `manifest.json`. (`styles.css` optional.) |

### 2.4 License

| Check | Type | Rule |
|-------|------|------|
| License in repo | Error | Repo must have a detectable license (e.g. `LICENSE` file or GitHub “License” from API). |

---

## 3. Submission requirements (summary)

- **fundingUrl:** Only for links to financial support (e.g. Buy Me a Coffee, GitHub Sponsors). Omit if none; do not point to Obsidian.  
- **minAppVersion:** Set to a version that supports the APIs you use (e.g. current stable such as `1.11.7`).  
- **Descriptions:** Short and simple; end with `.` or `?` or `!` or `)`.  
- **Node/Electron:** Only allowed on desktop; use `isDesktopOnly: true` if the plugin relies on them.  
- **Command IDs:** Do **not** include the plugin ID inside the command ID (e.g. prefer `fetch-movie-data` over `omdb-fetcher:fetch-movie-data`).  
- **Sample code:** Remove all sample/boilerplate code before submission.  

---

## 4. Developer policies (summary)

- **Policies / Not allowed:** No hidden telemetry; no remote code execution; no auto-updating plugin code outside normal releases; no collecting vault contents or personal data without consent; no deceptive patterns or spam.  
- **Disclosures:** Clearly disclose external services, data sent, and risks (e.g. in README and settings).  
- **Copyright and licensing:** Include a LICENSE file; respect and attribute third-party code and licenses.  
- **Network:** Only when essential; explicit opt-in for analytics or third-party services; document in README.  
- **Scope:** Read/write only what’s needed inside the vault; do not access files outside the vault.  
- **Cleanup:** Register and clean up DOM, app, and interval listeners (e.g. `registerEvent`, `registerDomEvent`, `registerInterval`) so the plugin unloads cleanly.  

---

## 5. Plugin guidelines (code & UX)

- **Structure:** Split code into modules; keep `main.ts` minimal (lifecycle, commands, settings tab).  
- **Manifest:** Required: `id`, `name`, `version`, `minAppVersion`, `description`, `isDesktopOnly`; optional: `author`, `authorUrl`, `fundingUrl`, `helpUrl`. Never change `id` after release.  
- **Releases:** GitHub release tag = `manifest.version` exactly (no `v`). Attach `main.js`, `manifest.json`, and `styles.css` (if used) as separate assets.  
- **README:** Purpose and clear usage instructions.  
- **Security/compliance:** Local-first; no telemetry by default; no remote code execution; document external services.  
- **UX/copy:** Sentence case; arrow notation for navigation (e.g. **Settings → Community plugins**).  
- **Commands:** Stable IDs; appropriate callback type; avoid default hotkeys.  
- **Performance:** Light startup; defer heavy work; debounce/throttle where appropriate.  
- **Mobile:** If `isDesktopOnly` is false, avoid Node/Electron-only APIs and test on mobile where feasible.  

---

## 6. Review procedure for an agent

1. **Repo and manifest**  
   - Read `manifest.json`: id, name, description, author, version, minAppVersion, isDesktopOnly, authorUrl, fundingUrl, helpUrl.  
   - Confirm no extra manifest keys; required keys present.  
   - Check id: no “obsidian”, no “plugin” suffix, only `[a-z0-9-_]+`.  
   - Check name: no “Obsidian”, no “Plugin”, no “Obsi”/“dian”.  
   - Check description: no “Obsidian”, ends with `.?! )`, length ≤ 250.  
   - Check authorUrl/fundingUrl rules (see 2.3).  
   - Check version format and that a GitHub release with that tag exists with `main.js` and `manifest.json`.

2. **License**  
   - Confirm a LICENSE file (or repo license) exists and matches the project (correct copyright holder, not sample boilerplate).

3. **README**  
   - Purpose and usage clear; no required template phrases missing if simulating a submission PR.

4. **Code and behavior**  
   - `main.ts` minimal; logic in other modules.  
   - No sample/boilerplate left in.  
   - Command IDs don’t embed plugin id.  
   - Settings: defaults and persistence.  
   - Network/telemetry: documented and opt-in where applicable.  
   - Listeners registered with `register*` and cleaned up on unload.  
   - No remote code execution or auto-update of plugin code.

5. **Report**  
   - List **errors** (would fail automated validation or submission).  
   - List **warnings** (would trigger validation warnings or guideline recommendations).  
   - List **recommendations** (best practice, UX, performance, maintainability).  

---

## 7. Quick checklist (copy-paste)

- [ ] `manifest.json`: id (no obsidian/plugin, `[a-z0-9-_]+`), name (no Obsidian/Plugin), description (no Obsidian, ends .?! ) , ≤250 chars), version (`x.y.z`), minAppVersion, isDesktopOnly, author; only allowed extras authorUrl, fundingUrl, helpUrl.
- [ ] authorUrl not obsidian.md, not plugin repo URL; fundingUrl not obsidian.md/pricing, not empty.
- [ ] GitHub release tag = manifest.version; assets include main.js, manifest.json (and styles.css if used).
- [ ] LICENSE in repo; correct copyright.
- [ ] README: purpose and usage; no “Obsidian” in description.
- [ ] No plugin ID in command IDs; no sample code left.
- [ ] Network/telemetry disclosed and opt-in; register* for listeners; no remote code execution.
