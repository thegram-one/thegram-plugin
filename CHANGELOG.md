# Changelog

## 4.8.1 — 2026-11-10

- Skill: two rules changed, together with the server's instructions (the same SHA-256 on both sides). A count that comes back `unresolved` lists the words it did not read in `unread`: the skill asks again without them only when they add no condition, and otherwise says that condition cannot be counted. For a shared hub the skill gives the key words as `terms` in the language the hub is written in. It is byte for byte the one in `thegram-plugin-codex` and `thegram-plugin-api` (shared-skill release `thegram-cloud-4.8.1.zip`).
- Needs the server release of 2026-11-10 (thegram-one/thegram-vault #419, #420), which is deployed. Checked on it with whole requests and real tool results: counts in the person's own vaults, small model 9 of 9 (2 of 4 before); two questions about Korean statutes asked in English, small model 6 of 6 (0 of 2 before). A count that names a hub's records by a word the server does not read still stops the small model in about two runs of three, with no number given. No Claude host has run it.

## 4.8.0 — 2026-11-10

- Skill: rewritten as one page of 3.8 KB; it was eighteen rules in 6.5 KB. Its rules are the text the Thegram server sends as its instructions, byte for byte (`thegram-skill` and `thegram-vault` pin the same SHA-256), so Claude reads one contract whether it is given the skill, the instructions or both. The rules route a request to one tool, first match wins: remember with `vault_note` only when asked, in any language; a count with `vault_exact`; public data through `vault_ask {op:"hubs"}`; what can be read or what changed with `vault_ask`; a document with `vault_change` and `vault_add`; and anything else that may depend on what the person said with `vault_recall`, before asking the person for the missing detail. The skill no longer lists the record types, tells the agent to find a document before a note, or sends it to the guide. It is byte for byte the one in `thegram-plugin-codex` and `thegram-plugin-api` (shared-skill release `thegram-cloud-4.8.0.zip`).
- Checked with a model given exactly the server's instructions and tool list (`thegram-skill`, `evals/scenarios.json`, eighteen one-step scenarios): the small model chose the expected call in 107 of 108 runs and the default model in all 72, with the skill loaded or not. No real host has run it.
- Publish after the server release that lists `vault_recall` and `vault_note`, drops the `guide` and `feedback` ops and sends these rules (thegram-one/thegram-vault #416, #417 and the unified-rules change). Merging to `master` is the release for Claude Code installs from this repository.

## 4.7.5 — 2026-11-09

- Skill: a move, delete or restore takes no `request_id` and refuses one; only a create and an edit carry it. The skill said every write has one, which a delete refused. After a lost reply the skill now reads the result before repeating a move or delete. The skill is byte for byte the one in `thegram-plugin-codex` and `thegram-plugin-api` (shared-skill release `thegram-cloud-4.7.5.zip`).
- The releases of this repository carry `thegram-claude.zip` (this plugin's files), `thegram-cloud.zip` (the portable package built in `thegram-plugin-codex`) and `SHA256SUMS`, so `thegram.one/download/latest/…` keeps following the latest release here. Versions of the three plugins move together.

## 4.7.4 — 2026-11-07

- This repository now holds only the Claude plugin: `.claude-plugin/`, `.mcp.json`, `skills/`, `assets/`, `LICENSE`, `NOTICE`, `README.md` and this file. The Claude directory refused 4.7.3 for the packaging scripts, tests and release tooling at the repository root (`LOCAL_CODE_FILE_TYPE`), and warned that the root `plugin.json` is an Agent Plugins manifest Claude does not read.
- The Codex and ChatGPT package moved to https://github.com/thegram-one/thegram-plugin-codex, together with `plugin.json`, `mcp.json`, the Codex marketplace, the review materials, `scripts/`, `tests/` and the release workflow. The API-key plugin for coding agents and services is https://github.com/thegram-one/thegram-plugin-api.
- No change to the bundled skill, the `thegram-cloud` identity, the MCP URL, OAuth configuration, assets or listing metadata. Versions of the three plugins move together.
- Notes for 4.7.3 and earlier, when one repository served every host, are kept in the CHANGELOG of `thegram-plugin-codex`.
