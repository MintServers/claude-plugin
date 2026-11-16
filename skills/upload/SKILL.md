---
name: upload
description: Upload files or folders from this computer to a MintServers game server, such as a plugin jar, mods, a world folder or config files. Use when the user asks to upload, deploy, push or copy local files to their server. Needs a shell with curl, so it works in Claude Code and Cowork, not in chat.
---

# Upload local files to a MintServers server

Work through the MintServers connector tools (`gameserver_*`). If they are missing, tell the user to run `/mcp` and sign in to MintServers first.

If you have no shell (for example in claude.ai chat), stop here and tell the user to upload through the File Manager in the MintServers panel instead.

## 1. Plan the upload

- Find the server: `gameserver_list`, then confirm with the user if more than one could match.
- Find the target folder with `gameserver_files_list` (for example `/plugins`, `/mods`, `/world`, `/config`).
- If files with the same names already exist, tell the user they will be replaced and ask before you continue.
- For a world or a folder that the running game writes to, stop the server first with `gameserver_power`, and offer `gameserver_backup_create` before overwriting a world.

## 2. Prepare the files

- A single file: upload it as it is.
- A folder or many files: zip them locally first (`zip -r upload.zip <folder>`). Upload the zip, extract it on the server, then delete the zip.
- Get the exact size in bytes: `wc -c < <file>`.

## 3. Get an upload link

Call `gameserver_upload_link` with the server and `size`. It checks the disk limit and returns `chunk_url`, `proxy_chunk_url`, `max_chunk_bytes` and a `how_to`. The link is valid for 30 minutes. Do not print it in full back to the user, because anyone holding it can write files to their server until it expires.

Follow the `how_to` text the tool returns if it differs from the steps below.

## 4. Send the file in chunks

Use 8 MB chunks. Pick a random `upload_id` of letters and digits. Each chunk is a POST of the raw bytes:

```bash
FILE="path/to/file.jar"
NAME="$(basename "$FILE")"
DIR="/plugins"
URL="<chunk_url from step 3>"
ID="up$(date +%s)$RANDOM"
TOTAL=$(wc -c < "$FILE" | tr -d ' ')
CHUNK=$((8 * 1024 * 1024))
OFFSET=0
while [ "$OFFSET" -lt "$TOTAL" ]; do
  dd if="$FILE" bs=1048576 skip=$((OFFSET / 1048576)) count=8 2>/dev/null \
    | curl -sS -X POST -H "Content-Type: application/octet-stream" --data-binary @- \
      "$URL&upload_id=$ID&directory=$DIR&name=$NAME&offset=$OFFSET&total=$TOTAL"
  echo
  OFFSET=$((OFFSET + CHUNK))
done
```

- Each answer is `{"size": <bytes received>, "complete": <bool>}`. The file is saved when `complete` is true.
- A `409` answer `{"size": n}` means the server already has `n` bytes: continue from offset `n`.
- If the node cannot be reached directly, use `proxy_chunk_url` the same way.
- To cancel, send `DELETE` to `<chunk_url>&upload_id=<id>&directory=<folder>`, or call `gameserver_upload_cancel`.

## 5. Finish

- Check the file is there with `gameserver_files_list`.
- For a zip: `gameserver_archive_extract`, then poll `gameserver_archive_extract_status` until it is finished. Then delete the zip with `gameserver_files_delete` (this needs `"confirm": true`, so ask the user first).
- Mods and plugins load on start: offer to restart with `gameserver_power`, then check `gameserver_console_read` for load errors.
