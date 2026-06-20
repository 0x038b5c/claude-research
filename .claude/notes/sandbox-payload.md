# Payload & Loader System

## Architecture Overview

```
Anthropic (loader skill) 
  → injects PYTHONPATH + runs: python -m src.main --uuid <uuid> --config <config-repo>
  → loader clones config repo → reads config.toml → clones payload repo
  → payload/src/main.py provisions the environment
  → /bin/sh replaced with shell_wrapper.sh
  → every bash_tool call now sources /opt/bash_profile (injects GH_TOKEN)
```

## Key Repos (0x038b5c)
| Repo | Purpose |
|------|---------|
| `claude-config` | `config.toml` mapping UUID → settings |
| `claude-payload` | The provisioning code + context injected into system prompt |
| `claude-secrets` | Age-encrypted secrets (github-token.age, signing-key.age) |
| `claude-state` | Session notes, project handoffs, active task |

## config.toml (at /opt/claude-config/config.toml)
```toml
[global]
github_username = "0x038b5c"
payload_repo = "0x038b5c/claude-payload"
state_repo = "0x038b5c/claude-state"
secrets_repo = "0x038b5c/claude-secrets"
signing_enabled = true
git_signing_format = "ssh"
```

## /opt/payload/src/main.py (provisioning entrypoint)
Runs once per session. Does:
1. Detects Mode A (UUID) or Mode B (username)
2. `apt-get install gh age openssh-client`
3. `pip install python-frontmatter`
4. Copies `bin/tool` → `/usr/local/bin/tool`
5. Clones `claude-secrets`, decrypts with `age -d -i /opt/age.key`
6. Configures git (name, email, signingkey, gpg.format=ssh, commit.gpgsign=true)
7. Replaces `/bin/sh` with `shell_wrapper.sh`
8. Clones `claude-state` → `/opt/state`
9. Prints `context.md` contents → injected into system prompt as `<static_context>`
10. Prints active sessions as `<dynamic_context>`

## /opt/payload/src/tool.py (the `tool` CLI)
A Click app providing:
- `tool project new <name>` — creates GitHub repo + local dir + state entry
- `tool session list` — lists sessions from /opt/state/sessions/ with frontmatter
- `tool state atomic-write --repo --file --message` — THE write primitive:
  - writes file, git add, git commit -S (signed), git push
  - if repo doesn't exist, creates it first

## /opt/payload/src/utils.py
Thin wrapper around `subprocess.run(cmd, shell=True, capture_output=True)`

## Secrets
- `/opt/age.key` (189 bytes, ASCII) — age private key, used to decrypt secrets
- `/opt/secrets/github-token` (40 chars) — GitHub PAT
- `/opt/secrets/signing-key` (OpenSSH private key, 399 bytes, chmod 600) — for signed commits

## /opt/state structure
```
/opt/state/
  active.md                    - current task WAL
  state.json                   - (legacy?)
  modules/
    firefly-iii-data-importer.nix
  projects/
    claude-research.md
    claude-setup.md
    har-analyzer.md
    new-test-repo.md
    python-calc-repl.md
    test-project.md
  sessions/
    2026-06-14-firefly-iii-data-importer-multi-instance.md
    2026-06-14-hello-world-tcp-lesson-plan.md
    2026-06-16-atomic-write-testing.md
    2026-06-18-python-calc-repl.md
    2026-06-18-session-note-template-yaml-conversion.md
    2026-06-20-claude-research-init.md
    atomic-write-test.md
  templates/
    session-note-template.yaml
```
