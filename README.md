# DEVSENSE PHP Tools for Grok Build

PHP workspace intelligence for [Grok Build](https://x.ai/build), powered by the [`devsense-php-mcp`](https://www.npmjs.com/package/devsense-php-mcp) npm package.

## Architecture

This is a **local MCP integration**. Grok Build launches `devsense-php-mcp` as a child process over stdio. The MCP server and PHP language server run on the developer's machine and access the local workspace.

The plugin intentionally contains no DEVSENSE credentials, OAuth client secrets, token storage, or separate authentication implementation. If DEVSENSE account sign-in and premium MCP entitlements are introduced, they belong in `devsense-php-mcp` itself so the same login works across Grok, Codex, Claude, Cursor, and other MCP clients.

A future sign-in should authorize the local MCP process with `devsense.com`; it should not move PHP analysis or source code to a DEVSENSE-hosted server.

## Requirements

- Grok Build with plugin and MCP support.
- Node.js and npm available on `PATH`.
- A PHP project opened as the Grok Build working directory.
- Network access on first launch so `npx` can download `devsense-php-mcp` (unless already cached).

## Install

The bundled `.mcp.json` launches:

```bash
npx -y devsense-php-mcp --transport stdio --root .
```

Start Grok Build from the PHP project directory so `--root .` resolves to the intended workspace. For a fixed project-specific setup, use an absolute path instead.

For local development:

```bash
grok --plugin-dir /path/to/devsense-php-grok-plugin
```

Alternatively, configure the MCP server directly:

```bash
grok mcp add devsense-php -- npx -y devsense-php-mcp --transport stdio --root .
```

Check the connection:

```bash
grok mcp list
grok mcp doctor devsense-php
```

In Grok Build, open `/mcps` and ensure the server is enabled and trusted.

## Authentication roadmap

No sign-in is required by this plugin today unless the installed MCP package itself implements it.

If account-backed premium features are added later:

1. The user signs in once through a browser flow initiated by the local CLI (for example, `npx -y devsense-php-mcp login`).
2. `devsense-php-mcp` stores credentials in the operating system's secure credential store, not in the repository, plugin manifest, MCP configuration, or shell history.
3. On subsequent launches, the local process refreshes or validates its authorization with `devsense.com` when needed.
4. The local MCP server enforces feature entitlements. Grok does not receive or manage DEVSENSE access tokens.
5. If the user signs out or revokes access, the local process removes its credentials and premium access is disabled.

The exact login command and UX will be documented here once implemented in the npm package. Do not invent or run a login command that the installed package does not yet support.

## What it provides

Grok discovers the tools exposed by the installed `devsense-php-mcp` version at connection time. The bundled skill guides the agent to use available PHP Tools capabilities for semantic symbol search, occurrence lookup, type-aware analysis, and code changes.

Tool availability can vary by package version; the plugin does not hard-code a duplicate MCP tool schema.

## Privacy and security

- The MCP server runs locally as a child process under the current operating-system user.
- PHP workspace analysis remains local.
- `npx` may download the npm package and its dependencies on first run.
- The plugin does not send PHP source code to a DEVSENSE-hosted service.
- If future sign-in is implemented, account and entitlement requests may contact `devsense.com`; that is separate from source-code analysis.
- Authentication tokens must be managed by the npm CLI, stored securely, and never placed in `.mcp.json`, project files, agent prompts, or logs.
- Review the npm package and its dependencies before trusting or installing it.
