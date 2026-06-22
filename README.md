# co-thinker

A Claude Code skill that turns Claude into a **second-person co-thinker** — a thinking partner you talk *with*, not a chatbot you query. The premise: you articulate better in dialogue than in your own head, so the job of the skill is to be the second nervous system in the room — receive, mirror, name things tentatively, and write down the good stuff so it compounds across sessions.

It captures raw brain-dumps in dialogue, distills them into evergreen atomic notes, and keeps a glanceable picture of where a project stands — one engagement (project) at a time.

> Note: the persona is written in the first person for its author ("Alex"). It works as-is, but if you adopt it, search-replace the name and tune the *Persona* / *Voice anchors* sections in `workflow.md` to your own voice.

## What it does

- **Dialogue over interrogation.** Tight 1–2 sentence turns, no menus mid-flow, no AI-tone paraphrase. It quotes you in your own words.
- **A toolkit of moves for when you're stuck** — externalising an invisible belief, naming the shape of a problem, breaking false binaries, finding the upstream crux, mapping the whole system before operating on one part. Deployed sparingly, only at clear openings.
- **One-way distillation.** Raw dated session notes (`1-daily/`) flow into evergreen, claim-form atomic notes (`2-atomic/`) — never the reverse. One thesis per note.
- **Progressive clarity across sessions.** A generated `Current state.md` and a living `Plan & to-dos.md` mean each session picks up where the last left off.

## Install

Clone it and symlink (or copy) it into your Claude Code skills directory:

```bash
git clone https://github.com/alex-is-learning/co-thinker-skill.git
ln -s "$(pwd)/co-thinker-skill" ~/.claude/skills/co-thinker
```

Restart Claude Code (or start a new session) and `/co-thinker` will be available.

## Usage

Run these from inside the project folder you want to think about:

| Command | What it does |
|---|---|
| `/co-thinker init` | One-time setup in a project folder — creates the `.cothinker/` marker, folder structure, and templates |
| `/co-thinker hello` | Start a session — finds your engagement by walking up from the current directory, reads the state, opens with where you left off |
| *(no command, mid-session)* | Just talk — the conversational worksheet |
| `/co-thinker close` | End the session — writes the transcript and session note, proposes atomic notes for you to confirm, regenerates the state |
| `/co-thinker state` | Regenerate `Current state.md` from past sessions without running a session |
| `/co-thinker map` | Read-only view of your atomic-notes index |

Typical loop: `init` once per project, then `hello` → think → `close` each session.

## How it's structured

Each project ("engagement") is anchored to a folder containing a `.cothinker/` marker. The skill walks up from your current directory to find it, so you can run it from any subfolder.

```
<your-project>/
├── .cothinker/            ← marker; holds session metadata + transcripts
├── CLAUDE.md              ← engagement-specific operator manual
├── Current state.md       ← generated, newest at top
├── Plan & to-dos.md       ← living source of truth for sequencing
├── 1-daily/               ← raw dated session notes
└── 2-atomic/              ← evergreen atomic notes, grouped by topic
```

It's designed to live inside an Obsidian vault (the `.cothinker/` dot-folder is auto-ignored), but it's just markdown — any editor works.

Nothing is written mid-session; all material is held in context and written at `close`, and atomic notes are always proposed for your confirmation before anything is saved.

## Files

- `SKILL.md` — skill manifest (name + description Claude Code reads)
- `workflow.md` — the persona, hard constraints, conventions, and step dispatch
- `steps/` — one file per command (`init`, `hello`, `close`, `state`, `map`, `session`)
- `templates/` — starting files written into a new engagement
