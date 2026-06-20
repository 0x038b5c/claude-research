# Network Configuration

## Interface & Routing
- Single network interface, IP in `192.0.2.x` range (from /proc/net/tcp: local `020200C0` = 192.2.2.x in little-endian)
- Default gateway routes to Anthropic infrastructure

## Active Connections (at inventory time)
- 3x ESTABLISHED to `10.79.104.10:443` — likely the Anthropic API relay / bash_tool WebSocket upstream
- Several TIME_WAIT connections to same host (prior tool calls)
- One ESTABLISHED to `192.0.2.1:41076` — likely internal VM host communication

## process_api Network Behavior
- `--block-local-connections` flag: loopback (127.0.0.1) connections are blocked
- This means: can't `curl localhost:PORT` to talk to process_api itself
- Outbound goes through egress proxy with domain allowlist

## Egress Proxy Allowlist (from system prompt)
```
api.anthropic.com, api.github.com, archive.ubuntu.com, codeload.github.com,
crates.io, files.pythonhosted.org, github.com, index.crates.io, npmjs.com,
npmjs.org, pypi.org, pythonhosted.org, raw.githubusercontent.com,
registry.npmjs.org, registry.yarnpkg.com, security.ubuntu.com,
static.crates.io, www.npmjs.com, www.npmjs.org, yarnpkg.com
```
Notably ABSENT: no general web access, no S3, no CDNs, no arbitrary domains.

## DNS
Standard resolv.conf, resolves through VM network.

## Implications
- GitHub operations: fully supported (gh, git, api.github.com)
- Package installs: apt (ubuntu repos), pip (pypi), npm (registry.npmjs.org), cargo (crates.io) — all work
- No arbitrary web fetching from bash_tool (only Claude's web_search/web_fetch tools can do that)
