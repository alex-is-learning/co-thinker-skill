# Step: `hello` — Session Entry

Read this file completely before acting. Execute sections in order. Do not skip.

---

## 1. Find the Engagement (CWD walk-up)

```bash
ENGAGEMENT_ROOT=""
DIR="$PWD"
while [ "$DIR" != "/" ]; do
  if [ -d "$DIR/.cothinker" ]; then
    ENGAGEMENT_ROOT="$DIR"
    break
  fi
  DIR="$(dirname "$DIR")"
done
echo "$ENGAGEMENT_ROOT"
```

If empty, tell Alex:

> No co-thinker engagement found at or above `$PWD`. Run `/co-thinker init` from the project folder you want to co-think on, then come back.

Stop.

Store `ENGAGEMENT_ROOT` for the rest of the session.

---

## 2. Derive Session State

```bash
SESSION_N=$(ls "$ENGAGEMENT_ROOT/.cothinker/sessions/"*.json 2>/dev/null | wc -l | tr -d ' ')
TODAY_FULL=$(date '+%Y-%m-%dT%H:%M')
TODAY_DATE=$(date '+%Y-%m-%d')
SESSION_FILE="$ENGAGEMENT_ROOT/.cothinker/sessions/${TODAY_DATE}-session-${SESSION_N}.json"
```

Store `SESSION_N`, `TODAY_FULL`, `TODAY_DATE`, `SESSION_FILE`.

---

## 3. Early Exit Detection

Check whether any previous session JSON has status "in_progress":

```bash
INCOMPLETE=$(ls "$ENGAGEMENT_ROOT/.cothinker/sessions/"*.json 2>/dev/null | xargs grep -l '"status": "in_progress"' 2>/dev/null | sort | tail -1)
echo "$INCOMPLETE"
```

**If empty:** continue to section 4.

**If non-empty:** tell Alex once, briefly:

> Looks like the last session — [extract date from filename] — didn't close cleanly. No transcript was saved for it. We can start fresh.

Mark the incomplete session abandoned:

```bash
INCOMPLETE_FILE="$INCOMPLETE"
INCOMPLETE_N=$(basename "$INCOMPLETE_FILE" | sed 's/.*-session-//' | sed 's/\.json//')
INCOMPLETE_DATE=$(basename "$INCOMPLETE_FILE" | cut -c1-10)
ENGAGEMENT_NAME=$(python3 -c "import json; print(json.load(open('$ENGAGEMENT_ROOT/.cothinker/meta.json')).get('name',''))" 2>/dev/null)
cat > "$INCOMPLETE_FILE" << 'ABANEOF'
{
  "id": "co-thinker-session-INCOMPLETE_N_PLACEHOLDER",
  "date": "INCOMPLETE_DATE_PLACEHOLDER",
  "sessionN": INCOMPLETE_N_PLACEHOLDER,
  "engagementName": "ENGAGEMENT_NAME_PLACEHOLDER",
  "status": "abandoned"
}
ABANEOF
```

Replace placeholders before executing. If this write fails, note it and continue — don't stop.

---

## 4. Read Engagement State

Read in this order — don't read everything in `2-atomic/`, just the index and the most recent session note.

### Meta

```bash
python3 -c "import json; d=json.load(open('$ENGAGEMENT_ROOT/.cothinker/meta.json')); print(json.dumps(d, indent=2))"
```

Hold the meta in context. Note `name`, `stuckThing`, `telos`, `sprintShape`.

### Current state

```bash
cat "$ENGAGEMENT_ROOT/Current state.md"
```

Hold the most recent few entries in context (the top of the file).

### Most recent session note

```bash
ls -t "$ENGAGEMENT_ROOT/1-daily/"*.md 2>/dev/null | head -1
```

If a file exists, read it:

```bash
cat "$LATEST_SESSION_NOTE"
```

Hold the top-of-next-session thread (usually flagged in the "Next thread" section at the bottom) in context.

### Atomic notes index

```bash
cat "$ENGAGEMENT_ROOT/2-atomic/index.md"
```

Hold a sense of what's been distilled so far. Don't read individual atomic notes unless a topic comes up that needs it.

### Plan

```bash
cat "$ENGAGEMENT_ROOT/Plan & to-dos.md"
```

Hold the current state of the plan. Useful if Alex picks up a thread that touches it.

---

## 5. Open the Session

Tell Alex — tight, one-liner where-we-left-off plus "what's on top?". Do not produce a multi-option menu.

If there are previous sessions:
> Where we left off: [one line pulled from the most recent session note's "Next thread" or from Current state.md]. What's on top right now?

If this is session 0 (first session after init):
> First session for `[ENGAGEMENT_NAME]`. The stuck thing is: [stuckThing]. The telos: [telos]. What do you want to start with — a brain-dump on what's currently swirling, or do you want me to ask?

Wait for Alex.

---

## 6. Write In-Progress Session JSON

```bash
ENGAGEMENT_NAME=$(python3 -c "import json; print(json.load(open('$ENGAGEMENT_ROOT/.cothinker/meta.json')).get('name',''))")
cat > "$SESSION_FILE" << 'SESSEOF'
{
  "id": "co-thinker-session-SESSION_N_PLACEHOLDER",
  "date": "TODAY_FULL_PLACEHOLDER",
  "sessionN": SESSION_N_PLACEHOLDER,
  "engagementName": "ENGAGEMENT_NAME_PLACEHOLDER",
  "status": "in_progress"
}
SESSEOF
```

Replace all `_PLACEHOLDER` values before executing.

If the write fails, surface and stop:
> Failed to create session file: [error]. Stopping.

Store `SESSION_FILE`, `SESSION_N`, `TODAY_FULL`, `ENGAGEMENT_NAME`, `ENGAGEMENT_ROOT`.

---

## 7. Begin Session

Load `./steps/step-session.md`. All in-session behaviour follows from there.
