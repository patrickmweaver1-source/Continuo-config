# Selector changelog

Keep a one-line note per change so you (and Claude) know what was touched and when.
Bump `version` in selectors.json with every entry.

## v2 — initial
Baseline selectors for chatgpt, claude, gemini, grok, copilot, deepseek, cocounsel.
Best-guess DOM selectors; expect first-contact tuning per site on live use.

## v3 — interface profiles + control fields
Added per-site `interface` (markup/channel/ctx_hint) moved out of extension code so
injection formatting is tunable without a rebuild. Added per-site `active` toggle,
top-level `min_extension_version` and `notice`. Extension now schema-validates fetched
configs and rejects malformed ones (keeps last good).
