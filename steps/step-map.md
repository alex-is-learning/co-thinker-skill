# Step: `map` — Read-Only View of Atomic Notes

Read-only. Prints `2-atomic/index.md` plus per-topic counts. No writes.

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

## 2. Read Index and Counts

```bash
cat "$ENGAGEMENT_ROOT/2-atomic/index.md"
```

Count atomic notes per topic folder:

```bash
for d in "$ENGAGEMENT_ROOT/2-atomic/"*/; do
  if [ -d "$d" ]; then
    topic=$(basename "$d")
    count=$(ls "$d"*.md 2>/dev/null | wc -l | tr -d ' ')
    echo "$topic: $count"
  fi
done
```

---

## 3. Display

Show the index content followed by a brief count summary:

> **2-atomic/ — atomic notes index**
>
> [contents of 2-atomic/index.md]
>
> ---
>
> **Counts by topic:**
> - `topic-1`: 5 notes
> - `topic-2`: 3 notes
> - ...
>
> **Total:** N notes across M topics.

If no topics exist yet (engagement is fresh), say:
> No atomic notes yet. They get extracted at `/co-thinker close`.

Stop.
