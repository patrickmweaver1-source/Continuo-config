# aidentity-config

The live selector configuration for the AIdentity extension. This repo is AIdentity's
**self-healing layer**: when an AI site changes its page structure and AIdentity stops
capturing or the Park buttons stop appearing, you fix it HERE (from your phone, in
30 seconds) and every install updates within ~6 hours. No extension reinstall.

## One-time setup
1. Create a **public** GitHub repo named `aidentity-config`.
2. Upload `selectors.json` into it (this file).
3. On the file's GitHub page, click **Raw**, copy that URL. It looks like:
   `https://raw.githubusercontent.com/<you>/aidentity-config/main/selectors.json`
4. In AIdentity → Settings → **Remote selector config URL**, paste it → Save.

Done. AIdentity now checks this file every ~6 hours (and on install) and adopts it
whenever its `version` is >= the copy it's running.

## How to push a fix (the whole point)
When a site breaks:
1. Edit `selectors.json` here on GitHub (pencil icon — works in mobile Safari).
2. Change the selector(s) for the affected site.
3. **Bump the top-level `"version"` number by 1.** (AIdentity only adopts a remote
   config whose version is >= its current one, so this is what triggers the update.)
4. Commit. Within ~6 hours every install self-heals. To pull it immediately on your
   own machine, toggle AIdentity off/on at chrome://extensions, or re-save the URL in Settings.

## What each field does (per site)
- `hosts`            — domains that count as this AI site (the allowlist; nothing else wakes AIdentity).
- `input`           — the prompt box, where context/persona is injected.
- `userMessage`     — your turns (read for significance/persona; never stored raw).
- `assistantMessage`— the AI's answers (where the ⚑ Park button is attached).
- `messageText`     — inner element to pull clean text from a message node.

Selectors are comma-separated fallbacks: AIdentity tries each until one matches, so you
can list an old and a new selector together during a transition.

## Diagnosing which selector is wrong (tell Claude the symptom)
- Badge green ● but **no ⚑ Park button on answers** → `assistantMessage` is stale.
- **Persona never captures / Yes-No rule box never fires** → `userMessage` is stale.
- **Context/persona won't inject** into the box → `input` is stale.
- **Park saves blank text** → `messageText` is stale.
- **AIdentity doesn't wake at all** on a site → `hosts` is wrong (or the site isn't allowlisted).

Send Claude the site + symptom and you'll get a corrected block to paste here.

## Verifying JSON before you commit
A broken JSON file will be ignored (AIdentity keeps its last good config — it won't brick),
but the fix won't take effect. If unsure, paste the file into jsonlint.com first.

## Current version: 3
## Config fields (v3+)
Top level:
- `version` (number) — bump on every change; installs adopt configs with version >= their current.
- `min_extension_version` (string, optional) — installs older than this see an "update available" nudge (non-blocking).
- `notice` (string, optional) — a short message surfaced in AIdentity's Settings (e.g. "ChatGPT layout changed, fix incoming"). Empty = none.

Per site:
- `active` (bool) — set false to remotely deactivate an already-permitted site without a reinstall. (You cannot ADD a brand-new domain via config — that needs an extension update + permission prompt.)
- `interface` — how AIdentity formats the persona for this site (tunable without a rebuild):
  - `markup`: "xml" | "markdown" | "plain"
  - `channel`: "weak" (persona rides in first user turn; adds imperative framing) | "strong"
  - `ctx_hint`: number (20-4000) — token budget for the injected persona block on this site.

## Safety boundary (do not cross)
aidentity-config carries ONLY passive descriptive data: selectors, hosts, interface hints,
notices. NEVER put logic, regexes, prompt text, injection wording, or user data here.
Anything that would change AIdentity's *behavior* (not its *targeting*) belongs in the
extension code. The extension runs a schema validator on every fetched config and
REJECTS anything malformed, keeping the last good config — but that can't protect against
unsafe *content*, only unsafe *shape*. Keep this file boring.

## Notes
Sites covered: chatgpt, claude, gemini, grok, copilot, deepseek, cocounsel.
