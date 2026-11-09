# Changelog

## 4.7.5 — 2026-11-09

- Skill: a move, delete or restore takes no `request_id` and refuses one; only a create and an edit carry it. The skill said every write has one, which a delete refused. After a lost reply the skill now reads the result before repeating a move or delete. The skill is byte for byte the one in `thegram-plugin-codex` and `thegram-plugin-api` (shared-skill release `thegram-cloud-4.7.5.zip`).
- The releases of this repository carry `thegram-claude.zip` (this plugin's files), `thegram-cloud.zip` (the portable package built in `thegram-plugin-codex`) and `SHA256SUMS`, so `thegram.one/download/latest/…` keeps following the latest release here. Versions of the three plugins move together.

## 4.7.4 — 2026-11-07

- This repository now holds only the Claude plugin: `.claude-plugin/`, `.mcp.json`, `skills/`, `assets/`, `LICENSE`, `NOTICE`, `README.md` and this file. The Claude directory refused 4.7.3 for the packaging scripts, tests and release tooling at the repository root (`LOCAL_CODE_FILE_TYPE`), and warned that the root `plugin.json` is an Agent Plugins manifest Claude does not read.
- The Codex and ChatGPT package moved to https://github.com/thegram-one/thegram-plugin-codex, together with `plugin.json`, `mcp.json`, the Codex marketplace, the review materials, `scripts/`, `tests/` and the release workflow. The API-key plugin for coding agents and services is https://github.com/thegram-one/thegram-plugin-api.
- No change to the bundled skill, the `thegram-cloud` identity, the MCP URL, OAuth configuration, assets or listing metadata. Versions of the three plugins move together.
- Notes for 4.7.3 and earlier, when one repository served every host, are kept in the CHANGELOG of `thegram-plugin-codex`.
