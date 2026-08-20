# oh-wu-wo — Egoless Chain-of-Thought Prompt

A tiny, strict system prompt that forces an AI model's internal reasoning into an
**impersonal, objective, process-oriented style** — no "let me think", no "I", no
"we", no "my". The prompt originated from a bug where an assistant kept leaking
"Let me ..." into its own reasoning traces, and works by locking the very first
sentence of every reasoning block to a fixed impersonal opener.

> The Chinese title 无我 (wú wǒ) means **"no-self"** — reasoning without ego.

---

## What it enforces

1. **First sentence rule (mandatory, highest priority)** — every chain of thought
   must open with an objective, impersonal statement:
   `Need to ...` / `Current state is ...` / `Goal is ...` / `Problem is ...` / `Steps: ...`
2. **No personal pronouns at all** — `I, me, we, my, our, Let me, We need`, 我, 我们 etc.
   are banned from the reasoning trace.
3. **One concrete action per sentence** — short, decision-level summaries only.
4. **Classify every task first** — `build` (produce, verify) · `fix` (read, locate,
   minimal change, verify) · `weak` (classify first, then build or fix).
5. **Think tags** — reasoning lives only inside the thinking block; never leaks
   into the final reply.
6. **Scope** — the rule shapes internal reasoning only; final replies follow the
   user's language and tone.

---

## Files

| File               | Purpose                                                        |
| ------------------ | -------------------------------------------------------------- |
| `wu-wo-prompt.md`  | The canonical prompt, drop-in for any chat or agent context.   |
| `AGENTS.md`        | Identical copy for coding agents that auto-load `AGENTS.md`.   |

---

## Quick start

```sh
git clone https://github.com/deimend21/wu-wo.git
```

## Using it in DeepSeek Harness (dsh)

[DeepSeek Harness](https://deepseekdocs.com) is DeepSeek's agent harness. Its
single entry point is the `dsh` CLI (zero-install via `npx`; requires Node.js
`^22.19.0` or `>=24.0.0`):

```sh
npx @deepseek-ai/dsh web      # web UI
npx @deepseek-ai/dsh --profile headless "run the tests"
```

### Recommended — install it as a selectable Agent preset

The repo ships a ready-made dsh **Agent preset** under
[`dsh/agent-wu-wo/`](dsh/agent-wu-wo/README.md) in two formats — pick the one
that matches your harness:

**Native agent preset** (harness 0.1.x / `deepseek-harness`, the
`@deepseek-ai/dsh-agent-presets` roster): copy the composition into the
user preset root, then pick **wu-wo** in the UI:

```sh
git clone https://github.com/deimend21/wu-wo.git
mkdir -p ~/.dsh/.agent-presets/wu-wo
cp wu-wo/dsh/agent-wu-wo/agent.cordis.yml ~/.dsh/.agent-presets/wu-wo/
cp wu-wo/dsh/agent-wu-wo/preset.yml ~/.dsh/.agent-presets/wu-wo/
npx @deepseek-ai/dsh web        # pick the "wu-wo" agent in the UI
```

Pin it as the default for every new session in `~/.dsh/settings.yaml`:

```yaml
agent-presets:
  default: wu-wo
```

**Legacy profile patch** (upstream harness): copy `cordis.patch.yml` into the
profile's user layer instead — see the
[preset README](dsh/agent-wu-wo/README.md) for both full walkthroughs.

### Alternative — workspace `AGENTS.md`

dsh's `workspaceContext` loader auto-loads `AGENTS.md` / `CLAUDE.md` from your
workspace root into the model's context:

```sh
curl -O https://raw.githubusercontent.com/deimend21/wu-wo/main/AGENTS.md
npx @deepseek-ai/dsh --profile headless "refactor src/server.ts"
```

### Alternative — one-off prompt paste

```sh
npx @deepseek-ai/dsh --profile headless "$(cat wu-wo-prompt.md)"
```

---

## Using it elsewhere

The prompt is tool-agnostic. It works with any model or agent that exposes a
system prompt / instructions file:

- **opencode / Claude Code / Cursor** — write it to `AGENTS.md` / `CLAUDE.md`.
- **Raw chat models (ChatGPT, Claude, DeepSeek, Qwen...)** — paste as a system message.
- **Any harness with a `persona` field** — same as Option 3.

## License

MIT