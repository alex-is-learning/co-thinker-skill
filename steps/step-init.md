# Step: `init` — Bootstrap a New Engagement

Read this file completely before acting. Execute sections in order. Do not skip.

---

## 1. Check for Existing Engagement

```bash
[ -d "$PWD/.cothinker" ] && echo "EXISTS" || echo "OK"
```

If `EXISTS`:
> An engagement is already initialized here. Run `/co-thinker hello` to start a session, or `/co-thinker map` to see what's been mapped.

Stop.

---

## 2. Three Framing Questions

Ask one at a time, conversationally. Keep your responses tight — receive what Alex offers, don't escalate his sketches into "official" framings.

**Q1.** What's the stuck thing? One or two sentences.

Wait. Receive. Reflect back briefly only if it helps clarify. Store as `STUCK_THING`.

**Q2.** What would 'unstuck' look like — what's the desired outcome here? Doesn't have to be precise. The telos.

Wait. Receive. Store as `TELOS`.

**Q3.** Sprint shape? E.g. "two weeks of daily 1hr sessions", "ad-hoc when I get stuck", "one big session a week for a month". What feels right?

Wait. Receive. Store as `SPRINT_SHAPE`.

Then ask one more for naming:

**Q4.** A short name for this engagement — for filenames and so I can refer to it later. (E.g. "offer-shapes", "ediya-launch-prep", "career-pivot".)

Wait. Receive. Store as `ENGAGEMENT_NAME`. Slug it for filenames if needed (lowercase, hyphens) — store as `ENGAGEMENT_SLUG`.

---

## 3. Create Folder Structure

```bash
mkdir -p "$PWD/.cothinker/sessions"
mkdir -p "$PWD/1-daily"
mkdir -p "$PWD/2-atomic"
```

If any of these fail, surface the error and stop:
> Failed to create folder structure: [error]. Stopping.

---

## 4. Write Engagement Meta

```bash
TODAY_FULL=$(date '+%Y-%m-%dT%H:%M')
cat > "$PWD/.cothinker/meta.json" << 'METAEOF'
{
  "name": "ENGAGEMENT_NAME_PLACEHOLDER",
  "created": "TODAY_FULL_PLACEHOLDER",
  "stuckThing": "STUCK_THING_PLACEHOLDER",
  "telos": "TELOS_PLACEHOLDER",
  "sprintShape": "SPRINT_SHAPE_PLACEHOLDER"
}
METAEOF
```

Replace all `_PLACEHOLDER` values before executing. Escape any double quotes in Alex's answers (use single quotes inside if needed). If a write fails, surface and stop.

---

## 5. Write Templates

Read each template file from `~/.claude/skills/co-thinker/templates/` and write the engagement-specific version into the engagement root, substituting `{{NAME}}`, `{{CREATED}}`, `{{STUCK_THING}}`, `{{TELOS}}`, `{{SPRINT_SHAPE}}` as needed.

### `CLAUDE.md`

Read `~/.claude/skills/co-thinker/templates/engagement-CLAUDE.md`. Substitute placeholders. Write to `$PWD/CLAUDE.md`.

### `Current state.md`

Read `~/.claude/skills/co-thinker/templates/current-state.md`. Substitute placeholders. Write to `$PWD/Current state.md`.

### `Plan & to-dos.md`

Read `~/.claude/skills/co-thinker/templates/plan-and-todos.md`. Substitute placeholders. Write to `$PWD/Plan & to-dos.md`.

### `2-atomic/index.md`

Read `~/.claude/skills/co-thinker/templates/atomic-index.md`. Substitute placeholders. Write to `$PWD/2-atomic/index.md`.

If any write fails, surface the error and stop.

---

## 6. Close

Tell Alex (single line, no menu):

> Engagement `[ENGAGEMENT_NAME]` initialized at `[$PWD]`. Run `/co-thinker hello` when you want to start the first session.

Stop.
