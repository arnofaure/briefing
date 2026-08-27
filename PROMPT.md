# Daily briefing generation prompt

This is the exact task the scheduled agent runs once a day. Edit this file to
change what gets generated — the schedule reads and follows it verbatim.

---

Repo: this Briefing app ("Buzz Now", BRIEFING-APP/Briefing on GitHub, deployed
via GitHub Pages at buzz.arnofaure.com).

1. Read `data/history.json` to see headlines/URLs already covered in the last
   30 days. Do not repeat a story from there unless there is a meaningful new
   development (in which case treat it as a fresh item and note the update).

2. Using web search, scan broadly for news in three areas from the last ~48
   hours. Only use fresh, credible sources (established outlets, official
   company/product blogs, reputable trade press — not low-quality
   aggregators).

   **AI + creative technology.** Run dedicated searches for each of these,
   not just general keyword queries:
   - New AI model releases or major version updates (LLMs, image, video,
     audio, music) from labs and platforms (OpenAI, Anthropic, Google
     DeepMind, Runway, Midjourney, ElevenLabs, Adobe Firefly, etc.)
   - Specifically check for any update to these models/families, since they
     are actively tracked: Nano Banana, LTX, Kling, Seedance, Seedream,
     Recraft, Mini H3, Wan, and the main open-weight model releases (e.g.
     Llama, Qwen, DeepSeek, Mistral, GLM). If one of these has a genuine new
     release or major version in the lookback window, it should be a strong
     candidate for inclusion.
   - New or changed creative/production workflows and tools (editing,
     VFX/CGI, virtual production, color, sound, previz, pipeline software)
   - Platform and studio announcements (major feature launches, pricing/
     licensing changes, notable acquisitions or shutdowns)

   **Cinema + filmmaking.**
   - Production technology, studio/industry news, festival news with real
     downstream impact, meaningful policy/labor/legal-rights developments.

   **Society.** A new third area — search for:
   - The single most significant social media platform story of the window
     (a major product change, policy shift, outage, or controversy at one
     of the large platforms — X, Meta/Instagram/Threads, TikTok, YouTube,
     Reddit, etc.). Pick only the one most significant story here, not
     several.
   - One notable societal-behavior story: something about how people are
     actually living, working, or relating to technology/culture right now
     (a real trend backed by data or reporting, not a think-piece opinion).

3. Curate hard. From everything found, keep only what is genuinely
   significant — the stories a well-informed person would actually want to
   know about today, not filler or minor incremental posts. Rank candidates
   within each area by real-world impact and drop the weakest.

   Pick 6 items total: 2 AI + creative-tech, 2 cinema + filmmaking, 2
   society (one social-media story, one societal-behavior story). If one
   area is genuinely thin that day, it's fine to shift to 3/2/1 or similar
   rather than force a weak item in — but never drop an area to zero unless
   there is truly nothing usable.

4. For each item write:
   - `id`: a short slug, unique within this briefing
   - `topic`: `"ai"`, `"film"`, or `"society"`
   - `headline`: clear, concise, no clickbait
   - `summary`: 2-4 sentences explaining what happened and why it matters
   - `source_name`: the publication/site name
   - `source_url`: direct link to the article
   - `image_url`: a relevant image URL if one is available from the source
     (e.g. og:image), otherwise `null` — never fabricate one

5. Write a `claude_take`: 2-4 sentences of your own analysis of today's set
   of stories as a whole — genuine point of view, not a recap. Look for
   connections, tensions, or patterns across the items (e.g. two stories
   pulling in opposite directions, a theme repeating across AI/film/society,
   something that seems more/less significant than the headlines suggest).
   Write it in first person as your own read, since the app displays it
   explicitly labeled "Buzz's Take" (your own personal take), not neutral
   reporting.

   Also write a `claude_take_title`: a short pull-quote or title, 4-10
   words, distilled from the take above (not a generic label — an actual
   line that could stand alone, e.g. lifted or lightly adapted from the
   take's sharpest sentence). This is what shows under the illustration in
   the Timeline tab, so it needs to work as a one-line summary on its own.

6. Generate a companion illustration for the take using Flora (already
   connected as an MCP tool in this session):
   - Style (always use this, word for word, do not vary it): "A
     single-panel editorial magazine illustration, square format, in the
     classic style of a highbrow literary magazine cover. Hand-painted
     gouache and ink texture, muted sophisticated color palette, clean
     confident linework, conceptual and quietly witty. Restrained color,
     single human figure. Absolutely no text anywhere in the image: no
     magazine masthead, no newspaper header, no title, no logo, no
     watermark, no lettering of any kind."
   - Scene (write this fresh each day): 1-2 sentences describing a single
     visual moment or gesture involving one person that captures the
     emotional core of today's `claude_take` — a metaphor or feeling, not a
     literal chart, diagram, or recap of the news. Append this to the style
     text above to form the full prompt.
   - Call `flora_generate` with `workspace_id`
     "ws_qd70hrccjtm43n8g2bdjvffke180yyzd", `project_id`
     "prj_ns74pbw2tn6aqdtv2j4rc8nzdx8d8vfm", `type` "image", `model`
     "t2i-krea-2-t2i-lora", `params` `{"aspect_ratio": "1:1"}`.
   - Poll `flora_get_run` with the returned `run_id` until `status` is
     `"completed"` (check every ~10s, it usually takes under 30s), then
     read the output image URL.
   - If Flora errors, times out, or is unavailable, don't block the rest of
     the briefing — just leave `claude_take_image_url` as `null` for today.

7. Overwrite `data/briefing.json` with:
   ```json
   {
     "date": "YYYY-MM-DD",
     "generated_at": "<ISO 8601 UTC timestamp>",
     "claude_take": "...",
     "claude_take_title": "...",
     "claude_take_image_url": "... or null",
     "items": [ /* the 6 items above */ ]
   }
   ```

8. Append today's items (headline, source_url, date) into `data/history.json`
   under `seen`, then trim `seen` to only the last 30 days so the file
   doesn't grow unbounded.

9. If `claude_take_image_url` is not `null`, append an entry to
   `data/timeline.json` (create it with `{"entries": []}` first if it
   doesn't exist yet) under `entries`:
   ```json
   { "date": "YYYY-MM-DD", "image_url": "...", "title": "<claude_take_title>" }
   ```
   Insert new entries at the start of the array (most recent first). Skip
   this step entirely if the image failed to generate — don't add an entry
   with a null image.

10. Commit all changed files with message `Daily briefing: YYYY-MM-DD` and
    push to `main`. GitHub Pages redeploys automatically on push.

No emoji anywhere in the output — this app never uses emoji.
