---
name: safe-update
description: Update a MintServers game server (Minecraft version or server software, a modpack, or a game version setting) with a backup first and a check that it starts afterwards. Use when the user asks to update, upgrade, change version, switch to Paper/Fabric/Forge, or update a modpack.
---

# Update a MintServers server safely

Work through the MintServers connector tools (`gameserver_*`). If they are missing, tell the user to run `/mcp` and sign in to MintServers first.

## 1. Know what is installed now

- `gameserver_get` for the game and status.
- Minecraft: `gameserver_minecraft_software` for the installed software and version.
- Modpack servers: `gameserver_modpack_status`, and `gameserver_modpack_versions` for what is available.
- Other games: `gameserver_settings_get` and look for version variables (`VERSION`, `BRANCH`, `BETA`...).

Tell the user the current version and the target version, and point out risks: a newer Minecraft version can break plugins and mods, and worlds cannot be downgraded.

## 2. Back up first

- `gameserver_backup_list` to check there is room for one more backup. If the limit is reached, ask the user which backup may be deleted, or whether to lock this one instead.
- `gameserver_backup_create`, then poll `gameserver_backup_list` until it shows as completed. Do not continue while it is still running.

## 3. Update with the right tool

| What changes | Tool |
|---|---|
| Minecraft version or software (Paper, Purpur, Fabric, Forge, NeoForge...) | `gameserver_minecraft_software_types`, `gameserver_minecraft_versions`, `gameserver_minecraft_builds`, then `gameserver_minecraft_install` |
| Modpack version | `gameserver_modpack_install`, then poll `gameserver_modpack_status` |
| Version variable of another game | `gameserver_settings_update`, then `gameserver_reinstall` (ask first: it needs `"confirm": true`) |

Prefer the modpack tool over uploading server packs by hand.

## 4. Start and verify

- `gameserver_power` start, then poll `gameserver_console_read` until the server reports it is ready.
- Look for plugin or mod load errors even if it started.
- Report the new version and anything that failed to load.

## 5. If it went wrong

Offer to roll back with `gameserver_backup_restore` to the backup from step 2. Only send `"confirm": true` after the user agrees in this chat.
