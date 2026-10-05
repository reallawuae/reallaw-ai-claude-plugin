# realLaw AI for Claude

Research published UAE legislation and legal updates with [realLaw AI](https://reallaw.ai). This plugin combines the UAE Legislation Research skill with a connection to the realLaw AI MCP server. It helps Claude find relevant documents, read their text, and provide answers with links to the retrieved sources.

## What you can do

- Find a law by its title, number, year, or citation.
- Search a legal topic and narrow results by jurisdiction, authority, document type, status, field of law, and release date.
- Retrieve documents and summarize or compare their content with citations.
- Review legislation news and digests for a chosen period.
- Check whether your linked realLaw AI account currently has access.

The research workflow searches the English corpus and can explain retrieved evidence in your language. It reads each selected document completely before making claims about its content. Short search snippets identify candidates; they are not treated as complete evidence.

## Requirements

- A Claude environment that supports plugins and remote MCP connections.
- A realLaw AI account connected through OAuth.
- An eligible active Professional or Team subscription for legislation searches and document retrieval. Checking account access requires sign-in but does not require a paid subscription.

Installing the plugin does not create a realLaw AI account or grant access to paid features. Sign in to the account you intend to use; changing your website login does not change an existing OAuth connection.

## Try the plugin

The initial Claude release is in preparation. Live OAuth sign-in and research workflows in Claude have not yet been verified. The configuration points to the existing realLaw AI server; installation alone does not establish a working connection.

### Claude Code

From a local checkout of this repository, start a session with:

```sh
claude --plugin-dir .
```

Use `/mcp` to inspect the `reallaw-ai` connection and authenticate. Invoke the skill explicitly with:

```text
/reallaw-ai:uae-legislation-research
```

You can also ask a relevant research question in normal language. The skill is available for Claude to select when it matches your request.

### Claude chat and Cowork

For a private preview, ZIP the plugin files with `.claude-plugin/plugin.json` at the archive root. Include `.mcp.json`, `skills/`, README, and the license; omit `.git/` and local credentials. In Claude, use **Customize > Plugins > Add > Upload plugin**, then connect realLaw AI from the plugin's **Connectors** tab. Your organization may require an owner to enable the connector first.

These loading methods follow [Anthropic's plugin guide](https://claude.com/docs/plugins/build).

### Example requests

- “Use realLaw AI to find Federal Decree-Law No. 47 of 2022 and summarize the edition available in the corpus.”
- “Find in-force federal UAE laws governing private-sector employment termination and summarize up to two relevant laws.”
- “Show recent UAE legislation digests and corporate tax updates for the last 14 days.”
- “Check whether my linked realLaw AI account currently has access.”

## Tools

| Tool | Purpose |
| --- | --- |
| `get_access_status` | Check current access for the linked account |
| `search_legislation_keywords` | Find English legal phrases used in the corpus |
| `list_legislation_filters` | Resolve supported filter keys |
| `search_legislation` | Search documents by topic and filters |
| `find_law_document_by_reference` | Resolve a title, number, year, or citation |
| `get_law_document` | Retrieve document text in bounded fragments |
| `get_legislation_news_digests` | Find legal updates and recent digests |

## Scope and limitations

Results reflect the documents available in the realLaw AI corpus. The plugin does not independently certify a document's legal status or invent missing publication details. It preserves the source links returned by the server.

The tools do not amend or publish legislation, change subscriptions, or execute payments. Requests solely about other countries' legislation are outside this plugin's scope. If a document cannot be retrieved completely, the research workflow reports that limitation instead of presenting a summary based on partial text.

## Privacy and connection

The plugin connects to `https://api.reallaw.ai/mcp`. Claude sends the tool arguments needed for a request, such as search phrases, document references, filters, dates, and pagination information. realLaw AI authenticates the linked account, checks access, and returns the requested document content or metadata.

Tool invocations generate internal operational logs, including relevant request parameters and technical diagnostics. The integration does not intentionally request or reconstruct your complete conversation history. Avoid including confidential or personal information that is unnecessary for the research request.

See the [realLaw AI Privacy Policy](https://reallaw.ai/privacy-policy) for data handling, retention, and privacy contacts. Claude's processing is also governed by Anthropic's policies. Disabling the connection does not automatically delete information already retained by either service.

This repository contains Markdown instructions and JSON connection settings. It includes no local MCP server, installation scripts, hooks, or bundled credentials. OAuth authentication happens through the service; do not paste passwords, access tokens, or client secrets into a conversation or the repository.

## Troubleshooting and support

- **Tools unavailable or sign-in fails:** inspect the connection in `/mcp` or the plugin's Connectors tab. If authentication cannot complete, contact support; the preview still needs live Claude verification.
- **Access denied:** confirm the connected realLaw AI account and its subscription. After an account or plan change, ask Claude to check access again.
- **Unexpected account:** disconnect and reconnect with the intended account.
- **Incomplete document:** narrow the request or retry retrieval. A partial fragment is not a verified summary of the complete document.

For account, connection, and privacy assistance: [support@reallaw.ai](mailto:support@reallaw.ai).

## License

The plugin files in this repository are available under the [MIT License](LICENSE), including the research skill and connection configuration.

This license applies to this repository's files. It does not license the separately hosted realLaw AI backend or documents returned by the service, grant access to an account, or waive subscription requirements. Use of the service and its content remains subject to the applicable realLaw AI terms and access permissions.

## Package validation

From the repository root:

```sh
claude plugin validate . --strict
```

This checks the package locally. Anthropic's developer portal runs additional directory checks, and live sign-in and tool use need separate verification. See the [plugin submission checklist](https://claude.com/docs/plugins/pre-submission-checklist).
