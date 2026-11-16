---
name: diagnose
description: Find out why a MintServers game server crashed, will not start, lags or kicks players, then fix it with the customer's OK. Use when the user says their server is down, crashing, stuck on starting, laggy, or showing errors.
---

# Diagnose a MintServers game server

Work through the MintServers connector tools (`gameserver_*`). If they are missing, tell the user to run `/mcp` and sign in to MintServers first.

## 1. Find the server

- If the user did not name a server, call `gameserver_list` and ask which one when there is more than one.
- Call `gameserver_get` for its game, status (running, offline, starting, suspended, installing) and resource use.
- A suspended, installing or transferring server cannot be fixed from here: say so and stop.

## 2. Gather evidence before guessing

- `gameserver_console_read` for the latest output. Look for the last error, stack trace or "Exception" before the stop.
- Read the game's own logs with `gameserver_files_list` and `gameserver_file_read`:
  - Minecraft: `crash-reports/` (newest file) and `logs/latest.log`
  - Other games: the log folder the console mentions
- `gameserver_settings_get` when the error points at a version, a port or a game setting.
- Large logs are paged: read the end first, then go back only if the cause is not there.

## 3. Explain the cause

Tell the user in plain words:

- what failed, quoting the one or two log lines that prove it
- the likely cause (a broken mod or plugin, a wrong version, a bad config value, a full disk, an EULA not accepted, a port clash)
- how sure you are

## 4. Fix it, safely

- Propose the fix and wait for the user's yes before changing anything.
- Before you edit or delete files, mods or worlds, create a backup with `gameserver_backup_create` and tell the user its name.
- Never send `"confirm": true` to a destructive tool (delete files, restore or delete a backup, reinstall, remove a port, delete a subdomain or schedule) until the user has agreed to that exact action in this chat.
- The startup command is managed by MintServers and is not shown. Never ask for it or suggest JVM or startup flags; fix files, mods, settings or the installed software instead. If it truly has to change, send the user to MintServers support.
- RAM is "32 GB+" on every plan and scales on fair use. Never quote another RAM figure or suggest a bigger plan: find the mod, plugin or setting using the memory, and send the user to support if the server still needs more.
- If the server keeps going to sleep (Automatic Hibernation), suggest a 24-hour idle time under Settings > Automatic Hibernation. Only explain how to turn it off if the user explicitly asks.

## 5. Check it worked

- Start or restart with `gameserver_power`, then poll `gameserver_console_read` until the server reports it is ready or fails again.
- If it fails again, go back to step 2 with the new output. After two failed attempts, summarise what you tried and suggest contacting MintServers support with that summary.
