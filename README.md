# Thegram Cloud

Thegram is shared, structured memory for people and AI assistants. This plugin pairs the Thegram remote MCP connector with one memory skill. Ask Claude or another supported host to find an earlier decision, retrieve a project's current constraints, or save an explicitly authorized outcome with its sources. Plans, notes, ideas, tasks and knowledge stay in structured `.gram` files rather than disconnected conversation transcripts.

Version 4.5.0 keeps the `thegram-cloud` plugin identity, the `thegram` MCP server key, and `https://vault.thegram.one/mcp`. It uses the 13-tool surface (`vault_guide`, `vault_search`, `vault_read`, `vault_browse`, `vault_changes`, `vault_comments`, `vault_create`, `vault_edit`, `vault_comment`, `vault_feedback`, `vault_move`, `vault_delete`, `vault_restore`). The skill is one short file with no references: one method reads every vault and shared GramHub dataset (scope first, `vault_search` with a folder, tags copied from a hit, `group_by:"종류"` for the kinds, `order` for newest or oldest, open before quoting), and writes only what the person asks to save. Record shapes and write rules come from `vault_guide` and the tool schemas, so the skill carries no recipe for a particular dataset.

## Install on Claude

On claude.ai or the Claude desktop app, open **Customize > Plugins > Add > Upload plugin** to install a ZIP privately. Download the latest Claude ZIP from https://thegram.one/download/latest/thegram-claude.zip. Open the plugin's **Connectors** tab, add the Thegram connector if needed, and connect it using your own Thegram account. Installing the plugin alone does not authorize access to your data. Your plan or organization policy may control whether you can add connectors.

For Claude Code, this repository is also a marketplace:

```text
/plugin install thegram-cloud --marketplace thegram-one/thegram-plugin
```

The skill is available as `/thegram-cloud:thegram-cloud` on surfaces that expose namespaced plugin skills. Describe your task normally to let Claude select it instead. The plugin uses remote MCP and OAuth; it does not need a running Thegram desktop app or an API key pasted into the package.

## Install on Codex

This repository is also a Codex marketplace (`.agents/plugins/marketplace.json`). In the Codex CLI:

```sh
codex plugin marketplace add thegram-one/thegram-plugin
codex plugin add thegram-cloud@thegram-cloud
codex mcp login thegram
```

The plugin brings the skill (`thegram-cloud:thegram-cloud`) and the remote `thegram` MCP server; `codex mcp login thegram` signs in with your Thegram account through OAuth in the browser. Installing the plugin alone does not authorize access to your data. In the Codex app, add the same marketplace from the Plugins directory. Update with `codex plugin marketplace upgrade thegram-cloud`.

To use an API key instead of OAuth, see **Coding agents**.

## Coding agents

Coding agents use the same skill and the same endpoint; only the sign-in differs. In https://vault.thegram.one/ open **Settings → Agents → API keys**, create a named key for the vault it needs with only the permissions required, and copy the secret once into a secret environment variable `THEGRAM_API_KEY`. Then add the server with a bearer header:

```sh
claude mcp add --transport http thegram https://vault.thegram.one/mcp --header "Authorization: Bearer $THEGRAM_API_KEY"
codex mcp add thegram --url https://vault.thegram.one/mcp --bearer-token-env-var THEGRAM_API_KEY
opencode mcp add thegram --url https://vault.thegram.one/mcp --header Authorization="Bearer $THEGRAM_API_KEY"
```

The key carries its own access scope, so a vault it was not granted is denied with `AGENT_SCOPE_DENIED`. Never paste the key into a conversation, a repository or a package. `--header` takes the value as a command argument, so run those two commands yourself in a shell whose history and logs are private; Codex reads the key from the environment and needs no argument. The bundled skill is the same file for every host.

With Codex, install the plugin as above for the skill, then run the `codex mcp add` line. A user-level server named `thegram` replaces the plugin's own server of that name, so there is one `thegram` server that sends the bearer key and no OAuth sign-in. Codex does not send an `Authorization` header declared in a plugin's `mcp.json`, so the key is never bundled in the package.

## Public directory submission

The Claude directory reads this repository root through `.claude-plugin/plugin.json`; public submission is a GitHub repository submission, not a private ZIP upload. The MCP server also needs its own connector listing, which can be paired with the plugin. Keep the existing submission and its repository/path when updating a version.

- Plugin format and account upload: https://claude.com/docs/plugins/build
- Directory submission: https://claude.com/docs/directory/publish
- Plugin submission and updates: https://claude.com/docs/plugins/submit
- Connector submission: https://claude.com/docs/connectors/building/submission

Local validation is not a portal security scan, host installation, OAuth test or directory approval. The existing submission's actual review state is authoritative.

## Storage and access

Cloud and separately connected Drive mounts are visible only when allowed by the current connection. Use the exact refs returned by `vault_browse`; reuse the established document instead of inventing a path, copying between mounts or redirecting a failed save. Local-only Mac folders are not visible to the cloud server.

Each agent has its own access scope. Connecting Drive or owning another vault does not automatically expose it to that agent. Manage grants and disconnect agents in **Settings > Connected agents** at https://vault.thegram.one/settings/agents. A writable connection still requires user authorization before saving.

`AGENT_SCOPE_DENIED` is a tool error, not an empty result or a successful save. Stop the denied operation, explain the blocked scope and give the settings URL. Do not retry unchanged, switch credentials or destinations, or claim success. After an authorized scope change, rediscover mounts and use fresh revision/hash before an authorized retry.

## Data and permissions

Memory requests go to `https://vault.thegram.one/mcp`. Requests can contain search questions, selected document and record identifiers, and content and source references the user authorizes the assistant to save. The server returns the authorized vault or separately connected Drive data requested through that connection. OAuth sign-in follows the server's advertised authentication flow.

The plugin itself does not fetch Slack, Gmail or unrelated connected-app data. Reading another source does not authorize copying it into Thegram. Retrieval and drafts stay read-only. Writes stay within the approved purpose and destination, with saved-state readback separate from retrieval-quality checks. Search ranking is not guaranteed; reported keyword fallbacks and semantic-index limitations must be disclosed.

No local MCP command, lifecycle hook or automatic package installation is declared. The repository's Python packaging tools are developer tools, not code that the plugin starts on a user's machine. Thegram AI handles document chat inside Thegram; this plugin no longer delegates document chat through the removed `chat_*` tools.

- Privacy: https://vault.thegram.one/legal/privacy
- Terms: https://vault.thegram.one/legal/terms
- Website: https://thegram.one
- Support: https://github.com/thegram-one/thegram-plugin/issues

## Shared workflow provenance

The cloud skill comes from the validated public `thegram-cloud-4.5.0.zip` release snapshot. `skill-source.lock.json` records that archive's SHA256, version, source path and the exact SHA256 of each imported file. This replaces the obsolete `skills/thegram-memory` commit pin; the release does not pretend that uncommitted workflow edits came from an older source commit.

To import a later validated public release without changing the cloud skill identity:

```sh
python3 -B scripts/sync_skill.py --source /path/to/thegram-cloud-4.5.0.zip
```

The importer validates the archive before writing, replaces only its owned skill tree and updates the lock. Do not edit the generated skill independently. Keep `plugin.json`, `.claude-plugin/plugin.json` and the matching `.claude-plugin/marketplace.json` entry on the same release version. Preserve both remote MCP configurations and the registered name.

## Build and verification

```sh
python3 -B scripts/package_plugin.py build --output /tmp/thegram-cloud.zip
python3 -B scripts/package_plugin.py validate /tmp/thegram-cloud.zip
python3 -B scripts/package_plugin.py build-claude --output /tmp/thegram-claude.zip
claude plugin validate .claude-plugin/plugin.json --profile directory --strict --json
```

Complete all changes and builds before one final integrated E2E. Do not run individual unit, per-feature or partial scenarios during release work. The final run must cover the matching runtime, release packaging, source-pin integrity and actual Claude manifest validation, and retain repeatable reports. A failed run requires a fix, completed builds and a rerun of the complete final E2E.

The archived live harnesses under `tests/e2e/` are not evidence of a new host installation or permission grant. Do not run them against a production account without explicit authorization. Keep test accounts, credentials, private reviewer details and local machine state out of public files.

## Releases and latest downloads

Pushing a `v<version>` tag that matches `plugin.json` runs `.github/workflows/release.yml`. It builds the portable and Claude ZIPs, attaches them under stable names (`thegram-cloud.zip`, `thegram-claude.zip`) together with versioned copies and `SHA256SUMS`, and marks the release as latest. Links never name a version:

- Portable/ChatGPT/agents: https://github.com/thegram-one/thegram-plugin/releases/latest/download/thegram-cloud.zip
- Claude upload: https://github.com/thegram-one/thegram-plugin/releases/latest/download/thegram-claude.zip
- thegram.one aliases: `https://thegram.one/download/latest/thegram-cloud.zip`, `…/thegram-claude.zip`, and `…/download/latest/` for the release page

To release, bump the three manifest versions and the CHANGELOG, merge, then `git tag v<version> <merge-commit> && git push origin v<version>`.

## Review materials and release

The English [reviewer guide](review/reviewer-guide.md) defines five positive and three negative memory scenarios. The [demo script](review/demo-script.md) distinguishes the recordings from future bundled-package runs. Published recordings at https://thegram.one/review/openai/ used a dev-mode custom MCP connection; they do not prove that the 4.5.0 bundled skill ran in Claude. Reviewer credentials belong only in the provider's private review channel, never in this repository or recordings.

Follow [CHANGELOG.md](CHANGELOG.md). A source push lets the directory detect and scan a new version; it does not mean the version is approved or published. Do not withdraw an existing review, change its tracked branch, create another registration or change automatic-publication settings as a workaround. Publishing source and a plugin package does not deploy the server or change OAuth scopes or credentials.

License: `UNLICENSED`, retained from the existing manifest and [LICENSE](LICENSE). This update does not change the license or grant additional rights.
