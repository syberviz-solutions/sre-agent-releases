# SRE Agent: install packages

Installable packages for **SRE Agent** (log monitor -> incident analysis -> AI fix -> pull request).
This repository only holds release downloads; see **[Releases](https://github.com/syberviz-solutions/sre-agent-releases/releases)**.

## Supported systems

| System | Package |
|---|---|
| Ubuntu 22.04 / 24.04, Debian 12 | `sre-agent_<version>-1_amd64.deb` / `_arm64.deb` |
| RHEL / Rocky / Alma 8 and 9, Amazon Linux 2023 | `sre-agent-<version>-1.x86_64.rpm` / `.aarch64.rpm` |

The package brings its own Python. The server only needs `git` (installed automatically as a dependency).

## Install

```bash
VERSION=1.0.0

# Ubuntu / Debian
wget https://github.com/syberviz-solutions/sre-agent-releases/releases/download/v$VERSION/sre-agent_$VERSION-1_amd64.deb
sudo apt install ./sre-agent_$VERSION-1_amd64.deb

# RHEL / Rocky / Alma / Amazon Linux
sudo dnf install https://github.com/syberviz-solutions/sre-agent-releases/releases/download/v$VERSION/sre-agent-$VERSION-1.x86_64.rpm
```

Verify the download (optional): download `SHA256SUMS` from the same release and run `sha256sum -c --ignore-missing SHA256SUMS`.

## Configure and start

```bash
sudo nano /etc/sre-agent/sre-agent.env   # LLM key, Slack webhook, log source, repo
sudo sre-agent doctor                    # checks the config; fix anything marked FAIL
sudo systemctl start sre-agent
sudo systemctl status sre-agent
```

`SRE_API_KEY` and `APPROVAL_SECRET` are generated for you on first install (unique per server).
Call the API with the header `X-API-Key: <SRE_API_KEY>`.

| What | Where |
|---|---|
| Config | `/etc/sre-agent/sre-agent.env` |
| Data (DB, repo clone, backups) | `/var/lib/sre-agent` |
| Logs | `journalctl -u sre-agent -f` and `/var/log/sre-agent/sre.log` |
| SSH key for reading remote logs | `/var/lib/sre-agent/.ssh/` |

## Upgrade

Install the newer package the same way. Config and data are kept, the database is backed up to
`/var/lib/sre-agent/backups/` first, and a running service is restarted automatically.

## Uninstall

```bash
sudo apt remove sre-agent      # keeps config and data      (dnf remove sre-agent on RHEL)
sudo apt purge sre-agent       # also deletes config, logs and the service user; data in /var/lib/sre-agent stays
```
