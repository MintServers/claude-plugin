# MintServers for Claude

Manage your [MintServers](https://mintservers.com) game servers by talking to Claude. Start, stop and restart servers, read the console, edit config files, take and restore backups, update Minecraft or a modpack, set up schedules, manage databases, ports and subdomains, and upload files from your computer.

The plugin connects Claude to the MintServers connector and adds three guided workflows. It works in Claude Code, Claude Cowork and claude.ai.

## Install

### Claude Code

```bash
claude plugin marketplace add MintServers/claude-plugin
```

```bash
claude plugin install mintservers@mintservers
```

Or inside a session: `/plugin marketplace add MintServers/claude-plugin`, then `/plugin install mintservers@mintservers`.

Then run `/mcp`, pick **mintservers** and sign in with your MintServers account.

### claude.ai and Cowork

**Customize > Plugins > Add marketplace**, enter `https://github.com/MintServers/claude-plugin`, and install **MintServers**. Then open the plugin's **Connectors** tab and connect it to sign in.

## Sign-in and access

There is no API key. You sign in with your MintServers account (OAuth) and choose **view only** or **full control**. Claude gets your own permissions, never more, and your plan limits apply. Deleting files, restoring or deleting a backup, reinstalling, and removing ports, subdomains or schedules always wait for your yes in the chat. Every change shows in your server's Activity tab with the app's name.

You can see and disconnect connected apps from the **AI Assistants** tab of any server in the MintServers panel.

## Skills

| Skill | What it does |
|---|---|
| `/mintservers:diagnose` | Finds why a server crashed, will not start or lags, from the console and logs, and fixes it with your OK |
| `/mintservers:safe-update` | Updates Minecraft, the server software or a modpack with a backup first, then checks it starts |
| `/mintservers:upload` | Uploads local files or folders (plugins, mods, worlds, configs) to your server. Needs a shell, so Claude Code or Cowork |

Claude also uses these on its own when your request matches. You can always just ask in your own words, for example:

- "Restart my survival server and tell me when it is back up"
- "Why did my server crash? Check the console"
- "Make a backup before I update to the latest Paper"
- "Add a schedule that restarts the server every day at 5 am"

## Data and network use

- The plugin connects to one MCP server, `https://mintservers.com/mcp`, run by MintServers. Your requests to Claude about your servers, and the results (console lines, file contents, settings), pass between Claude and MintServers through it.
- The `upload` skill sends the files you choose from your computer to the MintServers game node hosting your server, using a signed link from that server that expires after 30 minutes.
- The plugin itself collects nothing and sends nothing anywhere else.

See the [MintServers privacy policy](https://mintservers.com/p/privacy) and [terms of service](https://mintservers.com/p/terms-of-service).

## Support

Questions or problems: [mintservers.com/support](https://mintservers.com/support).

## License

[MIT](LICENSE)
