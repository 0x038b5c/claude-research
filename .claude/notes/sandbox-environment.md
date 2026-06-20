# Sandbox Environment Inventory
_Captured: 2026-06-20_

## Host / Virtualization

- **Kernel:** Linux 6.18.5 #1 SMP PREEMPT_DYNAMIC x86_64
- **OS:** Ubuntu 24.04.4 LTS
- **Hostname:** `vm`
- **PID 1:** `/process_api` — a custom binary, NOT systemd/init. This IS the container runtime.
- **Virtualization:** Running inside a VM (hypervisor flag in cpuflags). No `/dev/kvm` present. CPU reports `hypervisor` flag. cgroup path contains `process_api/2350d97d7f983f28a0a0ea6687621b31` — UUID likely identifies this sandbox instance.
- **No `.dockerenv`** — not Docker. Likely Firecracker MicroVM (process_api args: `--firecracker-init --addr 0.0.0.0:2024 --max-ws-buffer-size 32768 --block-local-connections`)
- **Seccomp:** DISABLED (Seccomp: 0) — no syscall filtering
- **No swap**, 3.9 GiB RAM, 1 vCPU (Intel Xeon @ 2.10GHz)

## The `process_api` Runtime

PID 1 is `/process_api`, a custom Go/Rust binary that:
- Listens on `:2024` (WebSocket — the bash_tool API endpoint)
- Listens on `:2025` (likely HTTP control/health)
- Started with `--firecracker-init` — this IS the Firecracker VM init process
- `--block-local-connections` — loopback connections are blocked from inside
- Routes bash_tool calls from Claude through WebSocket → shell execution
- stdin/stdout/stderr of my process are pipes to/from this binary

## Shell Wrapper

`/bin/sh` has been **replaced** by `/opt/payload/shell_wrapper.sh`:
```bash
#!/bin/bash
source /opt/bash_profile   # injects GH_TOKEN, GIT_ASKPASS
if [[ -f "$1" ]]; then
  bash "$1" "$@"
else
  eval "$@"
fi
```
Every bash_tool invocation sources `/opt/bash_profile` which sets:
- `GH_TOKEN=$(cat /opt/secrets/github-token)`
- `GIT_TERMINAL_PROMPT=0`
- `GIT_ASKPASS=/opt/payload/git_askpass.sh` (echoes the token)

## Capabilities

Very broad — `CapPrm/CapEff/CapBnd: 000001fffeffffff`. Nearly all capabilities granted. Can do most privileged operations short of a handful of restricted caps.

## Network

- Two established connections to `10.79.104.10:443` (likely Anthropic API / tool relay)
- Outbound filtered by egress proxy (allowlist of domains in system prompt)
- `--block-local-connections` set on process_api
- No iptables access, routing through VM network

## Ports Listening
- `:2024` (0x07E8) — process_api WebSocket (bash_tool)
- `:2025` (0x07E9) — process_api HTTP

## File Descriptors (this process)
- fd 0: pipe (stdin from process_api)
- fd 1: pipe (stdout to process_api)
- fd 2: `/dev/null` (stderr discarded)
- fd 3: `/proc/<pid>/fd` (self-reference)

## Users
Only meaningful user: `claude` (home: `/home/claude`). Root also present but most processes run as root in this VM.
