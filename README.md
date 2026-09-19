# Greenhaven server releases

Public download host for the **Greenhaven world server**. Nothing here is source - the development repository is private. Every release carries the three files a host needs.

## Assets in every release

| Asset | What it is |
| --- | --- |
| `greenhaven-server-x86_64-unknown-linux-musl` | The world server: one fully static Linux x86_64 binary (musl). Runs on any base image - no glibc matching, nothing to install. |
| `greenhaven-server-x86_64-unknown-linux-musl.sha256` | SHA-256 of that binary, one lowercase line `<hash>  <name>`. Verify before running. |
| `greenhaven.json` | The version-1 world map the server boots with. Required: a missing or invalid map stops the boot. |

## Download

Per-tag base URL (example tag `v0.1.9`):

```
https://github.com/Otterdays/greenhaven-server/releases/download/v0.1.9/greenhaven-server-x86_64-unknown-linux-musl
https://github.com/Otterdays/greenhaven-server/releases/download/v0.1.9/greenhaven-server-x86_64-unknown-linux-musl.sha256
https://github.com/Otterdays/greenhaven-server/releases/download/v0.1.9/greenhaven.json
```

## Verify and run

```sh
BASE=https://github.com/Otterdays/greenhaven-server/releases/download/v0.1.9
curl -fsSLO "$BASE/greenhaven-server-x86_64-unknown-linux-musl"
curl -fsSLO "$BASE/greenhaven-server-x86_64-unknown-linux-musl.sha256"
curl -fsSLO "$BASE/greenhaven.json"
sha256sum -c greenhaven-server-x86_64-unknown-linux-musl.sha256
chmod +x greenhaven-server-x86_64-unknown-linux-musl
mkdir -p assets/maps && mv greenhaven.json assets/maps/greenhaven.json
RPG_BIND=0.0.0.0:6000 RPG_MAP=assets/maps/greenhaven.json \
  RPG_DB=postgres://user:pass@host:5432/greenhaven RPG_WORLD_ID=world_1 \
  ./greenhaven-server-x86_64-unknown-linux-musl
```

It prints `world listening on ws://...` once it is up. Exactly one TCP port is used: the WebSocket `/ws` and `GET /health` share it. `/debug/world` stays off on a non-loopback bind unless `RPG_DEBUG=1`. Stop is `^C`; the server also flushes on SIGTERM. Player data lives in the shared PostgreSQL database named by `RPG_DB`, not on local disk - multiple world servers can point at the same database, each with its own `RPG_WORLD_ID`.

## Used by the Pelican egg

A Pelican/Wings egg fetches these three assets from one base URL (`RELEASE_BASE`), verifies the checksum, and installs them as `greenhaven-server` plus `assets/maps/greenhaven.json`. Nothing is compiled on the node.

## Notes

- Tags are `vX.Y.Z`, matching the server crate version. Latest: `v0.1.9`.
- A new version is a new tag. An uploaded asset is never overwritten.
- Artifacts only. Source, issues and design docs live in the private development repository.
- The Pelican egg JSON (`greenhaven-egg.json`) is also hosted here for panel import.
