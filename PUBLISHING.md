# Publishing DEVSENSE PHP Tools for Grok

This repository is a **Grok Build plugin source repository**. It is not itself the xAI marketplace index.

## Local execution model

The plugin launches `devsense-php-mcp` locally over stdio. This is intentional: the PHP language server and workspace access remain on the developer's machine.

Do not turn this plugin into a hosted proxy merely to add account sign-in. If DEVSENSE later introduces account-backed premium MCP features, implement sign-in, token refresh, secure credential storage, and entitlement enforcement in `devsense-php-mcp`. That keeps one identity and licensing implementation shared by Grok, Codex, Claude, Cursor, and other MCP clients.

A future CLI login could use a browser-based OAuth device authorization flow or authorization-code flow with PKCE. Store refresh credentials in the OS credential manager. Never put secrets in `.mcp.json`, repository files, agent prompts, or stdout. The local MCP server should continue to perform PHP analysis locally and contact `devsense.com` only for identity/entitlement operations as needed.

## A. Prepare the plugin repository

1. Create a public GitHub repository, for example `DEVSENSE/devsense-php-grok-plugin`.
2. Copy the contents of this directory to the repository root.
3. Test the plugin from a clean Grok Build installation and a representative PHP project.
4. Commit and push the tested version.
5. Record the full 40-character commit SHA.

Example:

```bash
git init
git add .
git commit -m "Initial DEVSENSE PHP Tools plugin for Grok"
git branch -M main
git remote add origin git@github.com:DEVSENSE/devsense-php-grok-plugin.git
git push -u origin main
git rev-parse HEAD
```

Adjust the repository URL to the actual DEVSENSE-owned public repository.

## B. Submit to the official xAI marketplace

The Grok Build marketplace is maintained at:

https://github.com/xai-org/plugin-marketplace

1. Fork the marketplace repository and create a branch.
2. Edit `.grok-plugin/marketplace.json`.
3. Add an entry to its `plugins` array. Use the actual public repository URL and full commit SHA:

```json
{
  "name": "devsense-php",
  "description": "Local PHP workspace intelligence for Grok Build. Search PHP symbols, inspect semantic usages, and work with PHP code using the DEVSENSE PHP Tools language server.",
  "category": "development",
  "source": {
    "source": "url",
    "url": "https://github.com/DEVSENSE/devsense-php-grok-plugin.git",
    "sha": "REPLACE_WITH_FULL_40_CHARACTER_COMMIT_SHA"
  },
  "homepage": "https://github.com/DEVSENSE/devsense-php-grok-plugin",
  "keywords": ["php", "php tools", "php intellisense", "php language server", "php mcp", "devsense"],
  "domains": ["devsense.com", "npmjs.com"]
}
```

4. Validate the JSON and follow the marketplace repository's current contribution instructions.
5. Open a pull request describing the plugin, local execution model, test steps, and exact source commit.
6. Respond to review feedback.

Contribution instructions:
https://github.com/xai-org/plugin-marketplace/blob/main/CONTRIBUTING.md

## C. User installation

After marketplace approval, users install the plugin through Grok Build. Verify the exact marketplace alias and CLI syntax against the current Grok Build CLI documentation before publishing user-facing instructions.

## D. Grok.com remote connectors are a separate target

A local stdio process is not itself a public remote MCP endpoint. If Grok.com custom connectors are a target, they need a publicly reachable MCP endpoint with a supported remote transport.

That is a separate deployment option, not a requirement for this Grok Build plugin. Do not expose a developer's local filesystem or language-server process to the public internet. A remote design would need explicit user consent, authentication, workspace isolation, authorization, and a secure local-to-remote routing model.

xAI connector documentation:
https://docs.x.ai/grok/connectors
