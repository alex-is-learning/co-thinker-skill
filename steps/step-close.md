# Step: `close` — Session Close

Read this file completely before acting. Execute sections in order. Do not skip.

---

## 1. Setup

Re-derive engagement and session if not in context:

```bash
ENGAGEMENT_ROOT=""
DIR="$PWD"
while [ "$DIR" != "/" ]; do
  if [ -d "$DIR/.cothinker" ]; then ENGAGEMENT_ROOT="$DIR"; break; fi
  DIR="$(dirname "$DIR")"
done

SESSION_FILE=$(ls "$ENGAGEMENT_ROOT/.cothinker/sessions/"*.json 2>/dev/null | xargs grep -l '"status": "in_progress"' 2>/dev/null | sort | tail -1)

TODAY_FULL=$(date '+%Y-%m-%dT%H:%M')
TODAY_DATE=$(date '+%Y-%m-%d')
SESSION_END_TS=$(date '+%s')
```

If `ENGAGEMENT_ROOT` empty:
> No co-thinker engagement found at or above `$PWD`. Stopping.

If `SESSION_FILE` empty:
> No in-progress session found in `[engagement]`. Run `/co-thinker hello` first.

Stop in either case.

Derive duration:

```bash
SESSION_START_STR=$(python3 -c "import json; d=json.load(open('$SESSION_FILE')); print(d.get('date',''))" 2>/dev/null)
SESSION_START_TS=$(date -j -f '%Y-%m-%dT%H:%M' "$SESSION_START_STR" '+%s' 2>/dev/null || echo 0)
if [ "$SESSION_START_TS" -gt 0 ] 2>/dev/null; then
  ELAPSED=$(( (SESSION_END_TS - SESSION_START_TS) / 60 ))
  if [ "$ELAPSED" -lt 60 ]; then DURATION="${ELAPSED} min"; else DURATION="$((ELAPSED / 60))h $((ELAPSED % 60))min"; fi
else DURATION="unknown"; fi
echo "$DURATION"
```

Derive session N from filename:

```bash
SESSION_N=$(basename "$SESSION_FILE" | sed 's/.*-session-//' | sed 's/\.json//')
SESSION_DATE=$(basename "$SESSION_FILE" | cut -c1-10)
```

Read engagement name:

```bash
ENGAGEMENT_NAME=$(python3 -c "import json; print(json.load(open('$ENGAGEMENT_ROOT/.cothinker/meta.json')).get('name',''))" 2>/dev/null)
```

Store all variables.

---

## 2. Write Transcript

Write the full conversation to a `.txt` file in one heredoc. Include every turn in order. Format:

```bash
TRANSCRIPT_FILE="${SESSION_FILE%.json}.txt"
cat > "$TRANSCRIPT_FILE" << 'TEXTEOF'
CO-THINKER: [first message]

ALEX: [first Alex message]

CO-THINKER: [next message]

[continue for all turns in order]
TEXTEOF
```

Rules:
- Every co-thinker turn starts with `CO-THINKER: ` on its own line.
- Every Alex turn starts with `ALEX: ` on its own line.
- One blank line between turns.
- No role labels in content. Continuation lines have no prefix.

If the write fails:
> Transcript write failed: [error]. Stopping.

Do not proceed after a transcript failure.

---

## 3. Write Session Note

Compose a clean Alex-readable session note from context. The note distils what happened — quote his phrasings where they land, don't paraphrase upgrade.

Session note format (write via heredoc):

```bash
SESSION_NOTE_FILE="$ENGAGEMENT_ROOT/1-daily/${SESSION_DATE}-session-${SESSION_N}.md"
cat > "$SESSION_NOTE_FILE" << 'NOTEEOF'
---
date: SESSION_DATE_PLACEHOLDER
session: SESSION_N_PLACEHOLDER
duration: DURATION_PLACEHOLDER
engagement: ENGAGEMENT_NAME_PLACEHOLDER
---

# Session SESSION_N_PLACEHOLDER — SESSION_DATE_PLACEHOLDER

**Top thread:** [TOP THREAD logged in context]

## Threads

[For each substantial thread in the conversation, a short header and 2–4 bullets. Quote Alex where his phrasing is sharp.]

## What surfaced

[Anything new — a name that landed, a decision that emerged, a tension named for the first time. Bullets, brief.]

## Action items

[Concrete to-dos that came out, if any. Provisional voice. If none, write "None this session."]

## Next thread

[NEXT THREAD logged in context, or "Open" if Alex didn't flag one.]

---

Transcript: `.cothinker/sessions/SESSION_DATE_PLACEHOLDER-session-SESSION_N_PLACEHOLDER.txt`
NOTEEOF
```

Replace placeholders. Write the actual content of "Threads", "What surfaced", "Action items", "Next thread" inline — don't leave bracketed instructions in the output.

If write fails, surface and stop.

---

## 4. Propose Atomic Notes

Pull all `[ATOMIC CANDIDATE — ...]` items from context. Present them to Alex as a numbered list. This is the **one allowed menu at close** — keep it tight.

Format (in your message to Alex):

> Atomic notes I'd extract from today. Tell me which to keep (e.g. "1, 3, 4 — rename 2, drop 5").
>
> 1. **"[claim-form title]"** — [one-line thesis]. Topic: `[suggested-topic-slug]`
> 2. **"[claim-form title]"** — [one-line thesis]. Topic: `[suggested-topic-slug]`
> ...

Wait for Alex's response. He may:
- Accept all (e.g. "yes" / "all good")
- Accept specific (e.g. "1, 3, 4")
- Reject all
- Rename: "rename 2 to 'X'"
- Reassign topic: "move 3 to topic 'Y'"
- Add a new topic folder: handle by asking once, "Create a new topic folder `[topic]`?"

If he rejects all: skip to section 5. If he accepts some: write each confirmed atomic note.

For each confirmed note:

```bash
TOPIC_DIR="$ENGAGEMENT_ROOT/2-atomic/TOPIC_SLUG_PLACEHOLDER"
mkdir -p "$TOPIC_DIR"
NOTE_FILE="$TOPIC_DIR/CLAIM_TITLE_PLACEHOLDER.md"
cat > "$NOTE_FILE" << 'ATOMICEOF'
---
date: SESSION_DATE_PLACEHOLDER
source-session: 1-daily/SESSION_DATE_PLACEHOLDER-session-SESSION_N_PLACEHOLDER.md
---

# CLAIM_TITLE_PLACEHOLDER

ONE_LINE_THESIS_PLACEHOLDER

[Body: 1–4 short paragraphs that defend the claim. Quote Alex from the session where his phrasing is the sharpest version. Don't pad.]
ATOMICEOF
```

Replace placeholders. The filename should match the title (sanitised: replace `/` with `-`, keep spaces and most punctuation). Don't use claude-style snake_case or kebab-case unless that's how Alex titled it.

Body content: drafted from the session conversation. Default to short — 1–2 paragraphs. Quote Alex literally where possible. Avoid AI-tone explanation.

If the new note is in a topic folder that doesn't exist yet, ask Alex once before creating it:
> Topic `[topic-slug]` doesn't exist yet. Create it?

If he says no, ask which existing topic to put it in.

Track the list of confirmed notes for section 5 and 7 (`atomicNotesAdded`).

If any write fails, surface the error and stop after that note — preserve any successful writes.

---

## 5. Update 2-atomic Index

Read the current index:

```bash
cat "$ENGAGEMENT_ROOT/2-atomic/index.md"
```

Append the new atomic notes under their topic headings (create new headings if needed). Format each entry as:

```markdown
- [Claim title](topic-slug/Claim title.md) — one-line thesis
```

Write the updated index back:

```bash
cat > "$ENGAGEMENT_ROOT/2-atomic/index.md" << 'INDEXEOF'
[full updated content]
INDEXEOF
```

If write fails, surface and continue (the notes themselves are written; only the index is missing).

---

## 6. Regenerate Current state.md

This is a **full rebuild**, not an append. Generate from session notes in `1-daily/`, newest first.

```bash
ls -t "$ENGAGEMENT_ROOT/1-daily/"*.md 2>/dev/null
```

For each session note (newest first), pull:
- Date and session number
- Top thread (one line)
- 2–3 bullets capturing what surfaced or what landed
- Link to the session note

Write the rebuilt state file:

```bash
cat > "$ENGAGEMENT_ROOT/Current state.md" << 'STATEEOF'
# Current state — ENGAGEMENT_NAME_PLACEHOLDER

Last updated: TODAY_FULL_PLACEHOLDER. Generated from session notes in `1-daily/`. Newest at top.

---

## Session SESSION_N_PLACEHOLDER — SESSION_DATE_PLACEHOLDER

**Top:** [top thread]

- [bullet 1]
- [bullet 2]
- [bullet 3 if needed]

→ [Session note](1-daily/SESSION_DATE-session-N.md)

---

## Session N-1 — DATE

[etc., newest to oldest, all sessions]

---

## Engagement

**The stuck thing:** [from meta.json]
**Telos:** [from meta.json]
**Sprint shape:** [from meta.json]
**Started:** [created date from meta.json]
STATEEOF
```

Replace placeholders. Each session block is brief — the file's value is glanceability.

If write fails, surface and continue.

---

## 7. Update Plan & to-dos.md

Only update if concrete action items emerged this session (logged as `[ACTION ITEM — ...]` in context).

If no action items: skip this section.

If action items: read the existing plan:

```bash
cat "$ENGAGEMENT_ROOT/Plan & to-dos.md"
```

Add new action items under an "Active to-dos" section (create if missing). Mark with today's date for traceability:

```markdown
- [ ] [action item description] *(SESSION_DATE)*
```

If the session also produced a structural shift (e.g. resequencing, new phase, dropped workstream), reflect that in the relevant section. Use provisional voice — "where this is heading is..." not "the new plan is...". If you're unsure whether a shift is structural enough to update, skip and let Alex flag it next session.

Write the updated plan back via heredoc.

If write fails, surface and continue.

---

## 8. Overwrite Session JSON

```bash
cat > "$SESSION_FILE" << 'SESSEOF'
{
  "id": "co-thinker-session-SESSION_N_PLACEHOLDER",
  "date": "SESSION_START_DATE_PLACEHOLDER",
  "sessionN": SESSION_N_PLACEHOLDER,
  "engagementName": "ENGAGEMENT_NAME_PLACEHOLDER",
  "status": "complete",
  "duration": "DURATION_PLACEHOLDER",
  "topThread": "TOP_THREAD_PLACEHOLDER",
  "atomicNotesAdded": ["TITLE_1", "TITLE_2"],
  "movesUsed": ["MOVE_1", "MOVE_2"],
  "nextThread": "NEXT_THREAD_PLACEHOLDER"
}
SESSEOF
```

Replace placeholders. `atomicNotesAdded` is the list of confirmed titles. `movesUsed` is the deduplicated list from `[MOVE — ...]` tags in context. Both can be empty arrays. If any field has no content, use `null` (unquoted) for strings or `[]` for arrays.

If write fails:
> Session JSON write failed: [error]. The transcript and session note are intact.

---

## 9. Close

Tell Alex (one or two lines, no menu):

> Done. Session [N] saved. [Brief — e.g. "Three atomic notes added: X, Y, Z." or "No atomic notes today." or "One action item logged in the plan."]

If `[NEXT THREAD — ...]` was logged, optionally add:
> Next time: [next thread].

Stop.
