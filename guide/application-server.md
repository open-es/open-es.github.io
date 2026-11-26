---
layout: guide
lang: en
ref: application-server
title: Application server
description: "Serving OES bases without a window: the application server and its folder."
section: running
order: 10
permalink: /guide/application-server/
---

`appcluster` serves the bases listed in its **server folder**, without a window. For each base it
starts its own application server - an `appserver` process - holds the one port every client comes
in by, and carries each client to a server of its base. Firebird and PostgreSQL bases can run side by
side. A fault in one base's server ends that process alone: the cluster starts it again, and the other
bases do not notice.

Started by hand, it logs to its console and runs until Ctrl+C. It can also run as a Windows service or
under systemd. `appcluster-tray` shows its servers and steers them.

## The server folder

```
<server folder>/          by default: "server" beside appcluster
  server.conf             Host and Port; one group per base
  server.key              this installation's key, made at the first start
  cluster.token           the control socket's key, while the cluster runs
  <Id>/                   one folder per base: its journal and infobase.conf
```

```ini
Port=7373

[trade]
Kind=firebird
Path=C:\bases\trade

[stock]
Kind=postgresql
Server=localhost
Port=5432
Database=stock
User=oes
Processes=2
```

| Key | Meaning |
|---|---|
| `Host` (top level) | The address clients connect to; `127.0.0.1` if absent. Any address beyond this machine needs a certificate (below) |
| `Port` (top level) | The port clients connect to; 7373 if absent |
| `[name]` | One base. The name is what clients ask for |
| `Kind` | `firebird` or `postgresql` |
| `Path` | Firebird: the base's folder |
| `Server`, `Port`, `Database`, `User`, `Password` | PostgreSQL: the connection |
| `IbUser`, `IbPassword` | The base's own user the server logs in as |
| `Processes` | How many servers serve this base side by side; 1 if absent |
| `Id` | Assigned at the first start and written back; leave it alone |

## Passwords

Passwords are **sealed** with the installation's key (AES-256-GCM); a password written as plain
text is refused. Seal one from the keyboard:

```bash
appserver --set-password=stock/Password
appserver --set-password=stock/IbPassword
```

Keep `server.key` with `server.conf`: a sealed value does not open without it.

## Options

| Option | Meaning |
|---|---|
| `--dir=<folder>` | Another server folder |
| `--locale=<code>` | The language of console messages |
| `--install` / `--uninstall` | Make it a Windows service, or remove the service (as an administrator) |
| `--service` | Run as a service: under the Windows service manager, or from a systemd unit |

## Rules

- **The studio creates a base** and its tables. The servers open existing bases and do not
  create them.
- **Servers and file-base programs do not share a base.** Servers share a base with each other, and
  programs that open a file base share it with each other, but a server is refused a base a
  file-base program holds, and the reverse.
- **A server that falls is started again**, a second later and then later still after short lives.
  Its sessions are lost with it; a client logs in again.
- **Several servers on one base** share its sessions, record locks and job claims through the base,
  so a scheduled job runs once across them.
- `backend.conf` beside the program sets the process's limits (`Connections` by default, and
  `MemoryLimit` in megabytes), and a base's `infobase.conf` sets its own `Connections`.
- **TLS.** With a certificate in `backend.conf` (`Certificate`, `PrivateKey`) the port speaks TLS;
  without one it listens on this machine only, and an address beyond it is refused at the start.

## Clients

Clients connect to `oes://<host>[:port]/<base>` (`oess://` over TLS) - over WebSocket, or over HTTP
with JSON. The protocol is described in
[client-protocol.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/client-protocol.md),
the cluster in
[application-cluster.md](https://github.com/open-enterprise-solutions/enterprise/blob/develop/docs/public/application-cluster.md).
