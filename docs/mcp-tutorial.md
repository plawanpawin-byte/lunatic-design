# Connect Claude Code, Codex or Antigravity to Timeline

This takes about five minutes, once. After that, your AI agent can work on the document you have open in Timeline.

## What you need

- Timeline **installed** (the Setup file, `Timeline-Setup-0.5.0.exe`). The portable file cannot be connected to agents, because it runs from a temporary copy.
- At least one of: **Claude Code**, **Codex** or **Antigravity**, already installed on this computer.
- Windows PowerShell or Command Prompt (press the Start button, type `powershell`, press Enter).

## Step 1: Install Timeline

1. Download `Timeline-Setup-0.5.0.exe` from the Releases page.
2. Run it and finish the installer. It creates the folder **Documents\Timeline**, where your documents are saved by default.
3. Open Timeline once from the Start menu, then close it.

## Step 2: Run the setup command once

Open PowerShell and paste this one line. It finds the app by itself, so you do not need to know where it was installed:

```
$exe = (Get-ChildItem "$env:LOCALAPPDATA\Programs" -Filter "Timeline.exe" -Recurse -EA 0 | Select-Object -First 1).FullName; & $exe --install-mcp
```

You should see something like this:

```
Timeline: connecting AI agents
  Claude Code: written to C:\Users\you\.claude.json
  Codex: written to C:\Users\you\.codex\config.toml
  Antigravity: skipped (not installed)
Restart Claude Code, Codex or Antigravity, then ask it to use the timeline tools.
```

- **written** means the agent's settings now include Timeline.
- **skipped (not installed)** means that agent is not on this computer. Nothing was changed for it.
- **NOT written** means the agent's settings file could not be read or changed. The reason is shown after it. The file is left as it was.

The Windows version of the app does not always print to the terminal, so the same result is also saved to:

```
%APPDATA%\Timeline\install-mcp.log
```

Paste `%APPDATA%\Timeline` into the File Explorer address bar to open it.

Your existing settings and other MCP servers stay as they were. Before any file is changed, a copy named `.bak` is saved next to it.

## Step 3: Restart the agent

Close Claude Code, Codex or Antigravity completely, then open it again.

## Step 4: Check that it works

1. Open **Timeline** and leave it open. The agent can only reach the app while it is running.
2. In the agent, ask in plain language, for example:

   > Use the timeline tools. Tell me which document is open, then add a red circle in the middle of the page.

3. You should see the agent call `get_status`, then `add_shape`. The circle appears in the app within a second. Press **Ctrl+Z** in the app to undo it.

If you ask for the list of tools, you should see `get_status`, `get_document`, `add_shape`, `add_shapes`, `update_shape`, `delete_shapes`, `select_shapes`, `undo`, `redo`, `render_page`, `screenshot`, `new_document`, `save_document`, `open_document` and `list_documents`.

## Connect from inside the app (alternative)

Open **Window → Connect AI agents…** in Timeline. The same command is shown there with a **Copy** button. Paste it into PowerShell.

## Troubleshooting

| What you see | What to do |
| --- | --- |
| `'"C:\...\Timeline.exe"' is not recognized` | The path is wrong. Use the steps above to find the real path. |
| The agent says "Timeline is not running" | Open Timeline and keep it open, then ask again. |
| `NOT written` for an agent | That agent's settings file is damaged or locked. Close the agent, then run the command again. The `.bak` file keeps the old version. |
| The agent does not see the tools | Restart the agent. Check that the setup line said **written** for that agent. |
| You moved or renamed the app | Run the setup command again with the new path. It replaces the old entry. |
| You upgraded the app | You do not need to run the command again, unless the install location changed. |

## Remove it

Delete the `timeline` entry:

- **Claude Code:** in `%USERPROFILE%\.claude.json`, delete the `"timeline"` block inside `"mcpServers"`.
- **Codex:** in `%USERPROFILE%\.codex\config.toml`, delete the `[mcp_servers.timeline]` section and the two lines under it.
- **Antigravity:** in its `mcp_config.json`, delete the `"timeline"` block inside `"mcpServers"`.

## What the agent can and cannot do

- It can read the open document, add and change shapes and text, undo, look at the page as a picture, and save or open files **only** in `Documents\Timeline`.
- It asks before it would throw away unsaved work.
- It talks to Timeline only on this computer, through a private connection protected by a secret token that changes every time the app starts.
- It cannot reach other folders or the internet through Timeline.
