# agent-guard

**Runtime guardrails for AI coding agents.** Hooks for [Claude Code](https://code.claude.com/docs/en/hooks), [Cursor](https://cursor.com/docs/agent/hooks), and [Codex](https://learn.chatgpt.com/docs/hooks) that block dangerous actions before they run.

> npm name `agent-guard` is taken (unrelated cost-protection tool), so this package is **`@kurtfrancisco/agent-guard`**.

Free MIT core · Pro **launch price $11** one-time (will rise later)

## What it blocks (free core — 5 guards)

| Guard | Examples |
| --- | --- |
| **destructive** | `rm -rf /`, `rm -rf ~`, `dd of=/dev/sda`, `mkfs`, wipefs on disks |
| **secrets** | Writing private keys / AWS keys / `.env` credential values into files |
| **exfil** | `curl | sh`, `wget | bash`, posting `env`/tokens to URLs, scp of `.env` |
| **forcepush** | `git push --force` to `main` / `master` / `production` |
| **scope** | Edit/Write outside the project root |

Deterministic. No LLM calls. **Zero runtime dependencies** (Node ≥ 18).

## Install

```bash
npm i -D @kurtfrancisco/agent-guard
npx agent-guard init --agent all
```

Or one-shot:

```bash
npx @kurtfrancisco/agent-guard init --agent claude
```

`init` writes the correct hook config for each agent:

- Claude Code → `.claude/settings.json` (`PreToolUse`)
- Cursor → `.cursor/hooks.json` (`beforeShellExecution` / `preToolUse`)
- Codex → `.codex/hooks.json` (`PreToolUse`) — trust via `/hooks` after install

## CLI

```bash
agent-guard init [--agent claude|cursor|codex|all] [--profile solo|strict|ci]
agent-guard check --command 'rm -rf /'
agent-guard check --file .env
agent-guard report          # Pro: HTML audit report
agent-guard license        # verify SKS2 key
agent-guard profiles
```

## Profiles (Pro)

| Profile | Intent |
| --- | --- |
| `solo` | Free-friendly defaults (also the free-tier behavior) |
| `strict` | Extra guards + audit log |
| `ci` | Fail-closed for CI / policy enforcement |

```bash
export AGENT_GUARD_LICENSE_KEY='SKS2-....'
agent-guard init --agent all --profile strict
```

## Pro (launch price $11)

**$11 one-time** at launch (will rise later).

**[Buy agent-guard Pro: $11 one-time →](https://buy.stripe.com/9B614p8XLe7E7Oj0md5AQ03)**
Payment is handled by Stripe. Your license key arrives by email; set `AGENT_GUARD_LICENSE_KEY`.

Includes:

- Extra guards (`sudo`, privileged Docker, world-writable `chmod`, symlink escape, download-then-exec, `git config` hooksPath)
- Profiles `strict` / `ci`
- Tamper-evident JSONL audit log (hash-chained)
- HTML report (`agent-guard report`)
- Multi-agent support in one installer

License scheme matches skill-shipper Pro (Ed25519 `SKS2-` keys, product id `agent-guard-pro`). Offline verify. Dev override: `AGENT_GUARD_LICENSE_DEV=1`.

## Honest limits

- Under Claude Code `--dangerously-skip-permissions`, some hooks may run asynchronously — do not treat hooks as a hard sandbox.
- Agents can still be told to disable hooks; this is a **guardrail**, not DRM or a seccomp jail.
- Obfuscation coverage is strong for common bypasses (quoting, env defaults, `sh -c`, base64, subshells) but not infinite.

## Competitors

Inspired by the demand shown by tools like **nah** and **Railguard**. agent-guard focuses on ready-made policy profiles, multi-agent hooks, an audit report teams can show a lead, and thorough bypass tests.

## License

MIT © Kurt Francisco
