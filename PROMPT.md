# Daily briefing generation prompt

This is the exact task the scheduled agent runs once a day. Edit this file to
change what gets generated — the schedule reads and follows it verbatim.

---

Repo: this Briefing app (BRIEFING-APP/Briefing on GitHub, deployed via GitHub Pages).

1. Read `data/history.json` to see headlines/URLs already covered in the last
   30 days. Do not repeat a story from there unless there is a meaningful new
   development (in which case treat it as a fresh item and note the update).

2. Using web search, scan broadly for AI + creative technology and cinema +
   filmmaking news from the last ~48 hours. Only use fresh, credible sources
   (established outlets, official company/product blogs, reputable trade
   press — not low-quality aggregators). Run dedicated searches for each of
   these, not just general keyword queries:
   - New AI model releases or major version updates (LLMs, image, video,
     audio, music) from labs and platforms (OpenAI, Anthropic, Google
     DeepMind, Runway, Midjourney, ElevenLabs, Adobe Firefly, etc.)
   - New or changed creative/production workflows and tools (editing,
     VFX/CGI, virtual production, color, sound, previz, pipeline software)
   - Platform and studio announcements (major feature launches, pricing/
     licensing changes, notable acquisitions or shutdowns)
   - Meaningful industry shifts (policy, labor, legal/rights rulings,
     festival or studio news with real downstream impact)

3. Curate hard. From everything found, keep only what is genuinely
   significant — the stories a well-informed person in AI/creative-tech or
   film would actually want to know about today, not filler or minor
   incremental posts. When you have more than 5 strong candidates, rank by
   real-world impact (how many people/workflows it changes, how major the
   company/festival/production is) and drop the weakest. Pick exactly 5,
   balanced across the two core topics (roughly 2-3 / 2-3, never all one
   topic unless there is truly nothing on the other side that week).

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
