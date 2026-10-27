# Thegram Cloud for Claude

Thegram is shared, structured memory for people and AI assistants. This plugin pairs the Thegram remote MCP connector with one memory skill. Ask Claude to find an earlier decision, retrieve a project's current constraints, or save an explicitly authorized outcome with its sources. The skill turns a research request into an evidence-driven task plan and maintains one deeply explained document per subject. Each material conclusion is a typed claim with how it is known, version-pinned sources and supporting or contradicting evidence, and section replacements stay protected by the original revision and hash.

## Add and connect

In claude.ai or the Claude desktop app, open **Customize > Plugins > Add > Upload plugin** and choose this ZIP. Open the plugin's **Connectors** tab, add the Thegram connector if needed, and connect it using your own Thegram account. Installing the plugin alone does not authorize access to your data. Your plan or organization policy may control whether you can add connectors.

The plugin keeps the identifier `thegram-cloud`. Its skill is available as `/thegram-cloud:thegram-cloud` on surfaces that expose namespaced plugin skills. Describe your task normally to let Claude select the skill instead. The plugin uses only remote MCP, so it does not need a running Thegram desktop app, a local server, or an API key pasted into a package file.

## Data and permissions

MCP requests go to `https://vault.thegram.one/mcp`. Requests can contain your search questions, selected document and record identifiers, and the content and source references you explicitly authorize Claude to save. The server returns only the Thegram vault or separately connected Drive destinations allowed by your current connection. Access does not authorize copying unrelated conversations or connected-app data into Thegram.

Retrieval and drafts stay read-only. Writes require your authorization and must stay within the established purpose and destination. The skill asks Claude to verify saved state separately from retrieval quality, and it cannot guarantee future search ranking. This ZIP bundles instructions, reference data, images, and connector configuration; it includes no local executable, lifecycle hook, automatic package installation, or embedded credential.

## Distribution

This ZIP is for account upload on claude.ai and the desktop app. Plugins added to that account can sync to Cowork and Claude Code under Anthropic's supported account flow. Public directory submission is separate: Anthropic currently takes a GitHub repository and plugin folder through its developer portal, validates and scans each version, and reviews a new listing before publication. Local validation is not directory approval.

License: `UNLICENSED`, retained from the published plugin manifest. This package does not change the existing license or grant additional rights.

- Website: https://thegram.one
- Privacy: https://vault.thegram.one/legal/privacy
- Terms: https://vault.thegram.one/legal/terms
- Plugin format and upload: https://claude.com/docs/plugins/build
- Directory submission: https://claude.com/docs/directory/publish
