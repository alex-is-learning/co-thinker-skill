# CLAUDE.md — {{NAME}} (co-thinker engagement)

Operator manual for Claude sessions in this folder. This is a co-thinker engagement initialised on {{CREATED}}.

## What this is

A self-co-thinking engagement on **{{NAME}}**. The stuck thing: {{STUCK_THING}}. The telos: {{TELOS}}. Sprint shape: {{SPRINT_SHAPE}}.

The skill that owns this folder: `~/.claude/skills/co-thinker/`. Use `/co-thinker hello` from here (or any sub-folder) to start a session, `/co-thinker close` to end one.

## Read order at session start

1. **`Current state.md`** — newest entry at top. Generated, not hand-written. The fastest way to know where things are.
2. **`Plan & to-dos.md`** — living source of truth for sequencing.
3. **Most recent file in `1-daily/`** — what was actually said in the last session. The "Next thread" at the bottom is usually the top of today.
4. Whatever else the user's request points at.

Don't read all of `2-atomic/` upfront — pull from `2-atomic/index.md` when a topic comes up.

## Folder convention

- `.cothinker/` — engagement marker. `meta.json` holds the framing; `sessions/` holds session JSON + transcript pairs. Don't edit by hand.
- `1-daily/` — session notes (one per session). Raw, dated. Quote Alex's phrasings.
- `2-atomic/` — distilled evergreen notes. One thesis per file. Claim-form titles. Update existing files rather than creating parallel ones.
- `Current state.md` — generated at every close. If it bloats, run `/co-thinker state` to rebuild.
- `Plan & to-dos.md` — living plan. Updated only when concrete action items emerge.

## How to work in this folder

- **Tight responses, no trailing summary.** End-of-turn is 1–2 sentences.
- **Quote Alex in his own words** when the source has it. Resist paraphrase upgrades.
- **`Current state.md` is a generated artifact, not a hand-written log.** When Alex asks to update or regenerate state, rebuild from `1-daily/` (newest at top, a few bullets per session, link to source).
- **One-way distillation:** `1-daily/` → `2-atomic/`, never the reverse.
- **No back-end jargon** (kensho, IFS, kleshas, ugh fields, tpot) in `Plan & to-dos.md` or anywhere a third party (or future-Alex glancing) might read for orientation.

## Common pitfalls

- Don't conflate `1-daily/` (raw, dated) with `2-atomic/` (distilled, evergreen). Synthesis flows one way.
- Don't propose new top-level folders without asking. The 1-daily / 2-atomic structure is settled.
- Don't write atomic notes silently — they're proposed at close, then written only after Alex confirms.
- Don't write to `.cothinker/` files by hand. The skill owns those.
