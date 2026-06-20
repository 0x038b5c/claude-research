# claude-research

## Purpose
Research into the bash_tool sandbox environment — understanding how the Claude
execution environment works, what it's built on, what capabilities are available,
and what the payload/loader system does.

## Structure
```
claude-research/
    CLAUDE.md
    .claude/
        todo.md
        notes/
            sandbox-environment.md   - VM, process_api, virtualization, capabilities
            sandbox-payload.md       - loader/payload system, tool CLI, repos
            sandbox-tools.md         - installed packages (pip, npm, apt)
            sandbox-network.md       - network config, egress proxy, connections
```

## Key Findings Summary
- We're in a **Firecracker MicroVM** — PID 1 is `/process_api --firecracker-init`
- `/bin/sh` has been replaced by a shell wrapper that injects GH_TOKEN on every invocation
- Massive tool suite: LibreOffice, ffmpeg, Playwright, tesseract, OpenCV, MediaPipe, pandoc, texlive
- Seccomp DISABLED, near-full capabilities — very permissive sandbox
- Egress proxy restricts outbound to package registries + GitHub + Anthropic only
- The `tool` CLI wraps git signed commits as the atomic write primitive
