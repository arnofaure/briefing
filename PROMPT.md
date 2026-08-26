# Daily briefing generation prompt

This is the exact task the scheduled agent runs once a day. Edit this file to
change what gets generated — the schedule reads and follows it verbatim.

---

Repo: this Briefing app (BRIEFING-APP/Briefing on GitHub, deployed via GitHub Pages).

1. Read `data/history.json` to see headlines/URLs already covered in the last
   30 days. Do not repeat a story from there unless there is a meaningful new
   development (in which case treat it as a fresh item and note the update).

2. Using web search, find AI + creative technology and cinema + filmmaking
   news from the last ~48 hours. Prioritize major developments plus useful
   tools, model launches, platform updates, production technology, creative
   workflows, and meaningful industry changes. Only use fresh, credible
   sources (established outlets, official company/product blogs, reputable
   trade press — not low-quality aggregators).

3. Pick exactly 5 items with a balanced mix across the two core topics (e.g.
   roughly 2-3 / 2-3, never all one topic unless there is truly nothing on
   the other side that week).

4. For each item write:
   - `id`: a short slug, unique within this briefing
   - `topic`: `"ai"` or `"film"`
   - `headline`: clear, concise, no clickbait
   - `summary`: 2-4 sentences explaining what happened and why it matters
   - `source_name`: the publication/site name
   - `source_url`: direct link to the article
   - `image_url`: a relevant image URL if one is available from the source
     (e.g. og:image), otherwise `null` — never fabricate one

5. Overwrite `data/briefing.json` with:
   ```json
   {
     "date": "YYYY-MM-DD",
     "generated_at": "<ISO 8601 UTC timestamp>",
     "items": [ /* the 5 items above */ ]
   }
   ```

6. Append today's items (headline, source_url, date) into `data/history.json`
   under `seen`, then trim `seen` to only the last 30 days so the file
   doesn't grow unbounded.

7. Commit both files with message `Daily briefing: YYYY-MM-DD` and push to
   `main`. GitHub Pages redeploys automatically on push.

No emoji anywhere in the output — this app never uses emoji.
