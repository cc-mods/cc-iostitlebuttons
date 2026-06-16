# Copilot instructions — cc-iostitlebuttons

Part of the **[cc-mods](https://github.com/cc-mods)** CrossCode suite (Restart/Close title buttons
for the cc-ios wrapper).

📓 **Read the knowledge base first:**
**[`cc-mods/knowledge`](https://github.com/cc-mods/knowledge)** (private; org members only) is the
source of truth for hard-won findings. Most relevant here:
- [`crosscode-modding.md`](https://github.com/cc-mods/knowledge/blob/main/crosscode-modding.md) —
  platform detection and the "iOS-targeted but universally safe" pattern this mod embodies.
- [`cc-ios.md`](https://github.com/cc-mods/knowledge/blob/main/cc-ios.md) — the `cccontrol` native
  bridge it posts to, and the title-screen menu internals.

**When you learn something durable, add it to `cc-mods/knowledge`** and keep this pointer intact.

## What this is

A `prestart.js` mod that adds **Restart Game** / **Close Game** entries to the title menu by patching
`sc.TitleScreenButtonGui`. It posts to the cc-ios `window.webkit.messageHandlers.cccontrol` bridge.

## The defining rule: load everywhere, function only on cc-ios

The mod **detects the cc-ios bridge** (`window.webkit?.messageHandlers?.cccontrol`). If it's absent
(desktop NW.js / any non-cc-ios host) it **logs and returns — a clean no-op** (desktop already has a
native Close button). It must still *load without error* anywhere; it only *functions* inside cc-ios.
Never make it throw or add UI on desktop.

## Must-not-break

- Keep the desktop no-op guard. Patch in `prestart`; wrap setup + button callbacks in try/catch so a
  failure never reaches game init (`CRITICAL BUG`). Use focus indices clear of the game's (0–5).
- Ship **no assets**. Ship both `ccmod.json` and `package.json`; keep versions in sync. Valid
  CCModDB tags only. id `cc-iostitlebuttons` == repo name — don't rename.
- No game assets / personal data / secrets in commits.

## Release

Push to `main` auto-bumps the patch, tags, builds `cc-iostitlebuttons-<ver>.ccmod`, publishes a
Release. **The bot pushes the bump commit back — `git pull --rebase origin main` first.** Docs-only
paths (`**.md`, `.github/**`, `LICENSE`) are excluded. Rebuild `cc-mods/CCModDB` after a release.

## Verify

`node --check prestart.js`; validate JSON manifests. Real test is in cc-ios: title screen shows the
buttons and they restart/quit the app; desktop loads it as a silent no-op.
