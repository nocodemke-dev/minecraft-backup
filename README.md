# minecraft-backup

Back up a Minecraft server on Linux without copying world files blindly while the server autosaves. This Bash Minecraft backup script coordinates server saves through screen, tmux, or RCON, then writes tar archives or restic backups.

## Features

- Back up one or more world directories; repeat `-i` for each world.
- Send save commands through screen, tmux, RCON, or the RCON CLI in an itzg/docker-minecraft-server container.
- Write compressed tar archives or use a restic repository.
- Remove old backups with the default thinning policy or delete the oldest backups sequentially.
- Print backup status in Minecraft chat with `-c`, without server plugins.

## Installation

Use Linux with Bash and install the tools for your chosen backend. Tar backups require `tar` and a compression program; the default is `gzip`. RCON also requires `xxd`. Install `restic` if you plan to use that backend, or `screen` or `tmux` if you use those connection methods.

From an extracted source checkout, make the script executable:

```bash
chmod +x backup.sh
./backup.sh -h
```

The release download below is a template for this standalone project, not the donor's release. Replace `OWNER` with its GitHub owner and confirm the published asset name before use.

```bash
curl -fL -o minecraft-backup-v0.1.0.tar.gz https://github.com/OWNER/minecraft-backup/releases/download/v0.1.0/minecraft-backup-v0.1.0.tar.gz
```

## Usage

Enable RCON on your Minecraft server, then run:

```bash
./backup.sh -c -i /home/user/server/world -o /mnt/storage/backups -s localhost:25575:secret -w rcon
```

Replace the world path, backup directory, and RCON credentials with your own values. This creates a backup with gzip compression, reports status in chat, and uses the default thinning policy for old backups. Keep RCON access restricted to trusted hosts.

For a server in a screen session named `minecraft`:

```bash
./backup.sh -c -i /home/user/server/world -o /mnt/storage/backups -s minecraft
```

For restic instead of tar:

```bash
export RESTIC_PASSWORD="restic-pass-secret"
./backup.sh -c -i /home/user/server/world -r /mnt/storage/backups-restic -s localhost:25575:secret -w rcon
```

Use `-d sequential` to delete the oldest backups first, or `-d none` to disable automatic deletion. Run `./backup.sh -h` for the available options.

To schedule hourly backups, edit your crontab with `crontab -e` and add an entry with absolute paths:

```cron
00 * * * * /path/to/backup.sh -c -i /home/user/server/world -o /mnt/storage/backups -s minecraft
```

**Live backups have a consistency limit.** The script disables automatic world saves, requests saves and a disk flush, then re-enables autosaving after the backup. Event-driven player saves can still occur, so player inventories and world contents may be out of sync. Stop the server before backing up when that risk is unacceptable.

**Test your restores.** Extract a gzip-compressed tar backup into a separate directory before replacing any live world files:

```bash
mkdir restored-world
cd restored-world
 tar -xzvf /path/to/backups/2019-04-09_02-15-01.tar.gz
```

Inspect the extracted directory structure; world files may be nested under parent directories. Stop the server before moving the restored world into its configured location. For restic backups, use `restic restore`.

## License

MIT. This project is based on [nicolaschan/minecraft-backup](https://github.com/nicolaschan/minecraft-backup). Retain the upstream copyright notice and MIT license text when distributing copies or substantial portions of the code.

## Related projects

These repositories were supplied as other projects from the same author. Their implementations were not included in the source material, so the notes below describe their names rather than verified capabilities.

- [boostlyapi/autonomous-agentic-rag](https://github.com/boostlyapi/autonomous-agentic-rag): Named for autonomous agentic retrieval-augmented generation.
- [Sriharsh-A/goscan](https://github.com/Sriharsh-A/goscan): A repository named `goscan`; consult its README for the scanning scope.
- [zameera9502/coinhive-stratum-mining-proxy](https://github.com/zameera9502/coinhive-stratum-mining-proxy): Named for a Coinhive-to-Stratum mining proxy.
