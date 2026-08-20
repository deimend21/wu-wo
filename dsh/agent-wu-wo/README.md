# wu-wo — dsh Agent Preset (模式预设)

Selectable preset that locks every reasoning block into the egoless
(无我) style: objective openers only, zero personal pronouns, one concrete
action per sentence.

## Install

There are two harness versions in the wild; pick the path that matches yours.

### Option A — native agent preset (recommended, harness 0.1.x / `deepseek-harness`)

Harnesses that ship `@deepseek-ai/dsh-agent-presets` (the `deepseek-harness`
fork family, dsh `0.1.x`) have a native **agent preset** roster: a preset is a
directory with an `agent.cordis.yml` composition under
`$DSH_HOME/.agent-presets/`. Install `wu-wo` there:

```sh
git clone https://github.com/deimend21/wu-wo.git
mkdir -p ~/.dsh/.agent-presets/wu-wo
cp wu-wo/dsh/agent-wu-wo/agent.cordis.yml ~/.dsh/.agent-presets/wu-wo/
cp wu-wo/dsh/agent-wu-wo/preset.yml ~/.dsh/.agent-presets/wu-wo/
```

`agent.cordis.yml` is the full `standard` coding-agent composition (adapted
from `deepseek-harness`, MIT) with the wu-wo persona swapped into the
`persona` row — tools, plan mode, compaction and delegation behave exactly
like `standard`; only the system prompt changes.

Then start dsh and pick **wu-wo** in the agent picker, or pin it as the
session default in `~/.dsh/settings.yaml`:

```yaml
agent-presets:
  default: wu-wo
```

> A preset is discoverable immediately — the roster re-reads its roots on
> every session start, no restart needed. Do NOT write this preset's persona
> into `cordis.patch.yml`: inserting a second `dsh-system-prompt` /
> `dsh-agent-loop` instance fails on these harnesses because
> `systemPrompt` / `agentLoop` are already provided by the base bundle.

### Option B — legacy profile patch (upstream harness)

On upstream DeepSeek Harness (no agent-presets roster), copy the patch into
your profile's user layer instead:

```sh
git clone https://github.com/deimend21/wu-wo.git
cd wu-wo
mkdir -p ~/.dsh/profiles/web
cp dsh/agent-wu-wo/cordis.patch.yml ~/.dsh/profiles/web/cordis.patch.yml
```

> `cordis.patch.yml` is the user-level layer of the dsh config stack
> (bundle → profile → user patch). See
> [Configuration](https://deepseekdocs.com/en/docs/user-guide/configuration).
> Adjust the profile name (`web`, `tui`, ...) to match the one you run.

The patch registers a `wu-wo` agent — select it per session in the UI, or
pin the persona as the deployment-wide default via the `persona` field of
`~/.dsh/settings.yaml` (see the full example in the commit history / the
main README). Then start dsh and choose the `wu-wo` agent:

```sh
npx @deepseek-ai/dsh web        # requires Node.js ^22.19.0 or >=24.0.0
```

## What it does

- Bans `let me` / `I` / `we` / `my` / 我 / 我们 from the reasoning trace.
- Forces every thought block to open with `Need to ...` / `Goal is ...` /
  `Current state is ...`.
- Keeps the final reply in the user's own language and tone — the rule only
  governs internal reasoning.