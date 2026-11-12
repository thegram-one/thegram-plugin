# Thegram Cloud for Claude

Thegram is the AI knowledge base for enterprise AX: it turns scattered documents and decisions into linked goals, decisions, requirements, evidence and sources that people, Thegram AI and Claude read as one record, with every change kept as history. This plugin pairs the Thegram remote MCP connector with one skill. Ask Claude to find an earlier decision, list a project's open risks, count records exactly, or remember something you tell it. The skill recalls what you told it earlier before it acts, cites the records it relied on, and writes only what you ask it to save.

Version 4.13.0 keeps the `thegram-cloud` identity, the `thegram` MCP server key, seven tools and `https://vault.thegram.one/mcp`; OAuth settings are unchanged. The shared skill carries the server's short instructions (under 4,000 characters): the tool schemas and descriptions give each op's arguments, and an error names the argument to fix, so Claude calls the tools right without long guidance. Authorized AI-authored Decision/Risk knowledge keeps its provenance without blanket human confirmation or an execution grant; Task/Event registration still needs a listed Inbox proposal capability and the person's registration.

## Add and connect

In claude.ai or the Claude desktop app, open **Customize > Plugins > Add > Upload plugin** and choose the plugin ZIP from https://thegram.one/download/latest/thegram-claude.zip. Open the plugin's **Connectors** tab, add the Thegram connector if needed, and connect it using your own Thegram account. Installing the plugin alone does not authorize access to your data. Your plan or organization policy may control whether you can add connectors.

In Claude Code, this repository is also a marketplace:

```text
/plugin install thegram-cloud --marketplace thegram-one/thegram-plugin
```

The plugin keeps the identifier `thegram-cloud`. Its skill is available as `/thegram-cloud:thegram-cloud` on surfaces that expose namespaced plugin skills. Describe your task normally to let Claude select it instead. The plugin uses only remote MCP and OAuth, so it does not need a running Thegram desktop app or a local server.

## Storage and access

Cloud and separately connected Drive mounts are visible only when allowed by the current connection. Use the exact refs the tools return; reuse the established document instead of inventing a path, copying between mounts or redirecting a failed save. Local-only Mac folders are not visible to the cloud server.

Each agent has its own access scope. Connecting Drive or owning another vault does not automatically expose it to Claude. Manage grants and disconnect agents in **Settings > Connected agents** at https://vault.thegram.one/settings/agents. A writable connection still requires your authorization before saving.

`AGENT_SCOPE_DENIED` is a tool error, not an empty result or a successful save. Claude stops the denied operation, explains the blocked scope and gives the settings URL; it does not retry unchanged, switch credentials or destinations, or claim success.

## Data and permissions

MCP requests go to `https://vault.thegram.one/mcp`. Requests can contain your search questions, selected document and record identifiers, and the content and source references you explicitly authorize Claude to save. The server returns only the Thegram vault or separately connected Drive destinations allowed by your current connection. Access does not authorize copying unrelated conversations or connected-app data into Thegram.

Retrieval and drafts stay read-only. Writes require your authorization and stay within the established purpose and destination. A save is reported with the ref and the sync state the server returned, and a queued write is not reported as saved. This repository holds only the plugin files: the manifest, the MCP configuration, one skill, two images, the license and this documentation. It includes no local executable, script, lifecycle hook, automatic package installation or embedded credential.

## Other hosts

Thegram keeps one plugin repository per host, so each carries only what its host loads.

- Codex and ChatGPT: https://github.com/thegram-one/thegram-plugin-codex
- Coding agents and services that sign in with a scoped API key: https://github.com/thegram-one/thegram-plugin-api

## Distribution

The Claude directory reads this repository root through `.claude-plugin/plugin.json`. The ZIP for account upload on claude.ai and the desktop app is this folder, archived; plugins added to that account can sync to Cowork and Claude Code under Anthropic's supported account flow. Local validation is not directory approval. Follow [CHANGELOG.md](CHANGELOG.md).

- Website: https://thegram.one
- Documentation: https://docs.thegram.one
- Privacy: https://vault.thegram.one/legal/privacy
- Terms: https://vault.thegram.one/legal/terms
- Support: https://github.com/thegram-one/thegram-plugin/issues
- Plugin format and upload: https://claude.com/docs/plugins/build
- Directory submission: https://claude.com/docs/directory/publish

License: [Apache-2.0](LICENSE). [NOTICE](NOTICE) reserves the Thegram name and logo; the Thegram service and apps are provided under their own [terms](https://vault.thegram.one/legal/terms).
