# Sullivan Creative Co. — Cowork

Custom Claude Code skills, prompts, and planning tools for Sullivan Creative Co.

## Contents

- **[SULLIVAN-README.md](SULLIVAN-README.md)** — Sullivan Creative skills and workflows
- **[GSD-README.md](GSD-README.md)** — Get Shit Done framework documentation
- **[SETUP_GUIDE.md](SETUP_GUIDE.md)** — Setup instructions

## Personal skill library (`skills/`, submodule)

`skills/` is a **git submodule** pointing at `SullivanCreativeCo/claude-skills`, which is
Keegan's personal Claude Code skill library. That repo is also cloned at `~/.claude/skills`,
which is where Claude Code actually loads skills from. This repo does not vendor a copy;
there is one source of truth.

```bash
git clone --recurse-submodules git@github.com:SullivanCreativeCo/cowork.git

# already cloned without it
git submodule update --init --recursive

# after editing a skill (edit in ~/.claude/skills, commit and push there first)
cd skills && git pull origin main
cd .. && git add skills && git commit -m "Bump skills"
```

A submodule pins to a specific commit, so `skills/` in this repo lags `~/.claude/skills`
until you bump it. That is the trade for not keeping two divergent copies.

## Get Shit Done (GSD)

This repo includes the [GSD framework](https://github.com/gsd-build/get-shit-done) for spec-driven development and project planning with Claude Code.

## Sullivan Creative Skills

- Engagement Scanner
- Content workflows
- Custom prompts
