# Changelog

## 4.7.4 — 2026-11-07

- This repository now holds only the Claude plugin: `.claude-plugin/`, `.mcp.json`, `skills/`, `assets/`, `LICENSE`, `NOTICE`, `README.md` and this file. The Claude directory refused 4.7.3 for the packaging scripts, tests and release tooling at the repository root (`LOCAL_CODE_FILE_TYPE`), and warned that the root `plugin.json` is an Agent Plugins manifest Claude does not read.
- The Codex and ChatGPT package moved to https://github.com/thegram-one/thegram-plugin-codex, together with `plugin.json`, `mcp.json`, the Codex marketplace, the review materials, `scripts/`, `tests/` and the release workflow. The API-key plugin for coding agents and services is https://github.com/thegram-one/thegram-plugin-api.
- No change to the bundled skill, the `thegram-cloud` identity, the MCP URL, OAuth configuration, assets or listing metadata. Versions of the three plugins move together.
- Notes for 4.7.3 and earlier, when one repository served every host, are kept in the CHANGELOG of `thegram-plugin-codex`.
