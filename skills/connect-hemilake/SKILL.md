---
name: connect-hemilake
description: Connect an AI assistant (Claude Code, Codex, or another MCP client) to a user's Hemilake over remote MCP with OAuth. Use when the user asks to "connect me to my Hemilake", "add Hemilake to Codex", "add Hemilake to Claude Code", "set up Hemilake MCP", or link an assistant to their Hemilake lake.
---

# Connect an AI assistant to Hemilake

Follow these steps to connect the user's Hemilake over remote MCP with OAuth. Never ask the user for a device key, API key, or token.

## 1. Get the lake's MCP address

Take `<MCP_URL>` from the user's prompt. It ends in `/mcp`.

If the prompt did not include an MCP address, ask the user for it. Tell them where to find it:

- In their Hemilake invitation email.
- On their Hemilake console's **Connect** page.

Do not guess a hostname. Wait until you have the exact `<MCP_URL>`.

## 2. Detect your client and add the server

Identify which client you are running in and follow the matching branch, substituting `<MCP_URL>`.

### Claude Code

1. Run:

   ```sh
   claude mcp add --transport http hemilake <MCP_URL>
   ```

2. A running Claude Code session does not see a server added with `claude mcp add` until it restarts.
3. Tell the user to:
   - Exit and start a new Claude Code session in the same directory.
   - Run `/mcp` in that new session (only the user can run `/mcp`), select `hemilake`, and start sign-in.
   - Complete the sign-in in the browser window that opens: confirm who they are if not already signed in, verify with their passkey, and approve the connection.
4. Wait until the user confirms sign-in is done.

### Codex

1. Before running the command, tell the user that it opens a browser window for sign-in (confirm who they are if not already signed in, verify with their passkey, and approve the connection) and ask them to confirm they are ready.
2. Run:

   ```sh
   codex mcp add hemilake --url <MCP_URL>
   ```

   This adds `hemilake` and starts the OAuth browser sign-in automatically. Wait for the user to finish sign-in in the browser and for the command to exit.
3. If sign-in was skipped or cancelled, ask the user to confirm and run `codex mcp login hemilake`.
4. A running Codex session picks up the newly added server without restarting. If `connect_status` is still not available after sign-in finishes, ask the user to restart Codex.

### Any other MCP client

Tell the user to add a remote MCP server in their client with:

- Name: `hemilake`
- MCP address: `<MCP_URL>`
- OAuth client id and secret: leave empty (the client registers itself dynamically)

Tell them a browser window will open for sign-in (who they are if not already signed in, their passkey, then Approve) and wait until they confirm it finished.

## 3. Verify and report

Call the `connect_status` tool from the `hemilake` server.

If `connect_status` answers, report the installation kind (`kind`), the MCP address (`addresses.mcp`), and confirm that Hemilake is connected.

If verification fails, tell the user what failed and what to do next:

- **Not signed in yet** (or `hemilake` is missing from `/mcp`): in Claude Code, restart the session if not restarted yet, then run `/mcp` and select `hemilake` to sign in. In Codex, run `codex mcp login hemilake`.
- **401 Unauthorized**: sign-in did not finish, expired, or was revoked. Start sign-in again (`/mcp` in Claude Code, `codex mcp login hemilake` in Codex). If it keeps returning 401, check that OAuth sign-in is enabled on the lake (`HEMI_OAUTH_ENABLED` is not `0`).
- **Redirect refused** (`invalid_redirect_uri`): the client sent a callback URL the lake does not accept. Hosted lakes accept loopback callbacks and the allowed web app callbacks. Use a supported client with a loopback callback, or open the lake's Connect page for another setup method.
- **Unreachable or connection refused**: check that the lake is running and reachable from this machine. A `loopback` lake answers on its own computer only; a `tailnet` lake requires Tailscale to be connected.
