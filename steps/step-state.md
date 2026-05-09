# Step: `state` — Regenerate Current state.md

Read-only side-step. Regenerates `Current state.md` from session notes in `1-daily/` without ending a session. Useful if state has bloated or gone stale.

---

## 1. Find the Engagement

```bash
ENGAGEMENT_ROOT=""
DIR="$PWD"
while [ "$DIR" != "/" ]; do
  if [ -d "$DIR/.cothinker" ]; then ENGAGEMENT_ROOT="$DIR"; break; fi
  DIR="$(dirname "$DIR")"
done
```

If empty:
> No co-thinker engagement found at or above `$PWD`. Stopping.

Stop.

---

## 2. Read Inputs

```bash
ENGAGEMENT_NAME=$(python3 -c "import json; print(json.load(open('$ENGAGEMENT_ROOT/.cothinker/meta.json')).get('name',''))")
TODAY_FULL=$(date '+%Y-%m-%dT%H:%M')
ls -t "$ENGAGEMENT_ROOT/1-daily/"*.md 2>/dev/null
```

For each session note (newest first), read it and extract:
- Date and session number (from frontmatter or filename)
- Top thread (from "**Top thread:**" line)
- 2–3 brief bullets summarising what surfaced

Read meta.json fully for the engagement summary footer.

---

## 3. Rebuild Current state.md

Same format as `step-close.md` section 6. Full overwrite, newest at top, glanceable.

```bash
cat > "$ENGAGEMENT_ROOT/Current state.md" << 'STATEEOF'
# Current state — ENGAGEMENT_NAME_PLACEHOLDER

Last updated: TODAY_FULL_PLACEHOLDER. Generated from session notes in `1-daily/`. Newest at top.

---

## Session N — DATE

**Top:** [top thread]

- [bullet 1]
- [bullet 2]

→ [Session note](1-daily/DATE-session-N.md)

---

[etc., all sessions]

---

## Engagement

**The stuck thing:** [from meta.json]
**Telos:** [from meta.json]
**Sprint shape:** [from meta.json]
**Started:** [created date]
STATEEOF
```

Replace placeholders.

If write fails, surface and stop.

---

## 4. Close

> State regenerated from [N] session notes. Last entry: [most recent session date].
