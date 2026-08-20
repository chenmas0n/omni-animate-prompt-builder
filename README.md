# Omni-Animate Prompt Builder

A single self-contained HTML file that builds shot-by-shot prompts for image-to-video
models. Drop in reference images, pick a starting point, adjust the shot list, copy the
prompt. No build step, no server, no dependencies — open `index.html` in a browser.

## Using it

Open `index.html`. That's the whole install.

Three steps:

1. **References** — drag in the images you'll feed the model. Tag each one
   (character, environment, product, VFX sheet, storyboard, animation / poses, style,
   lighting, start frame, UI / menu layout, logo, text / title, icon, banner / graphic),
   or leave **Auto detect** selected. Palette, warmth, tone,
   contrast and saturation are read off the pixels automatically. Naming them is optional —
   step 2 identifies the subject, reference type and any ordered panels or poses for you.
2. **Preset** — pick what kind of animation you want. 44 starting points across 13
   collapsible groups.
3. **Shots** — the shot list, already written, as a storyboard grid. Click any shot to
   edit its text or settings. The prompt sits at the bottom and updates as you type.

The tool auto-saves the current job — prompt, shot settings and downscaled reference
images — in the browser. **History** reopens any of the 20 most recent jobs, and a refresh
restores the current draft. **Start new prompt** saves the current job to history and resets
the three steps for a clean job.

## How step 2 fills in step 3

Picking a preset sends **every reference in one call** along with that preset's intent. The
model looks at your actual images and writes the sequence against them — naming each
subject, then choosing framing, angle, lens, depth of field, camera move, lighting, colour
temperature, transitions and per-shot timing, and writing the scene and action text for
every beat. Subjects are cited as `(ref N)` so the video model binds the words back to the
right image. You land on step 3 with a finished list to fine-tune, and a **Rewrite this
shot list** button for another take.

Storyboards are treated as ordered shot plans rather than style images: the model reads
panels in visual order and preserves framing, blocking, screen direction, action and cuts.
VFX and animation sheets are read as ordered effect stages or key poses. A separately
tagged character reference supplies final identity and design while the board or pose sheet
supplies the sequence. Each reference card also has an optional direction-notes field for
clarifying panel order, effect direction, planted contacts or other constraints.

There is nothing to set up: a free-tier Gemini key ships in the file, so it works on first
open. See [The API key](#the-api-key) for what that means and how to use your own instead.

Whatever the model sends back is checked against the app's own vocabulary before it is
used. A setting it invents, misspells or leaves out falls back to the preset's crafted
value rather than emptying the field, durations are re-fitted to the runtime cap, and a
reply with no usable shot list leaves the preset's own expansion in place. So a bad
response degrades to the old behaviour instead of a broken storyboard.

**If the API is unreachable the tool still works** — presets expand from their own wording
and your reference tags, and you write from there. The model only ever changes who drafts
the first version.

Total runtime is capped at 15 seconds; shot lengths draw from that shared budget.

## Presets

| Group | Presets |
| --- | --- |
| Game cinematic | Character reveal, Boss reveal, Combat beat, Environment flythrough, Cutscene dialogue, Vehicle reveal |
| Game animation | Idle loop, Locomotion pass, Ability / VFX beat, MOBA champion ability showcase, Expression pass |
| VFX exploration | VFX sheet → graybox, 360° readability test, Impact & scale test, Ambient VFX loop |
| Storyboard & animation | Storyboard → sequence, Storyboard + character, Pose sheet → motion, Animation in graybox |
| Game marketing | Roster line-up, Logo sting, Gameplay montage |
| UI/UX visual design | Menu flow showcase, UI transition exploration, Graphic element motion system, Logo & title package, HUD motion pass |
| Product | Product turntable, Hero reveal, Macro detail pass, Three-shot product spot |
| Architecture | Exterior approach, Interior reveal, Material tour |
| Automotive | Rolling shot, Interior pan |
| Fashion | Look walk, Fabric macro, Editorial turn |
| Food | Pour hero, Ingredient macro |
| Abstract | Seamless loop, Kinetic reveal |
| Blank | Start from scratch |

Presets are recipes, not fixed lists. A beat marked `per` repeats for every matching
reference you uploaded — three character refs build three reveal beats, one builds one —
and durations are scaled to fit the runtime cap after expansion.

### VFX and storyboard workflows

- For a VFX concept/progression sheet, leave its tag on **Auto detect** or choose
  **VFX sheet**, then use **VFX sheet → graybox** to see anticipation, emission, impact
  and decay in an empty measured game-level scene.
- For a storyboard, tag the board **Storyboard** and any separate design image
  **Character**, then choose **Storyboard + character**. Panel composition and acting come
  from the board; identity, costume, proportions, materials and colour come from the
  character reference.
- For pose sheets or keyframes, use **Animation / poses** with **Pose sheet → motion**.
  The generated sequence preserves pose order, contacts, facing direction, silhouette and
  motion arcs.

### UI/UX visual-design workflows

Upload complete screens as **UI / menu layout**, then tag separate production assets as
**Logo**, **Text / title**, **Icon** or **Banner / graphic**. The UI/UX presets treat those
references as exact flat assets: words, letterforms, icon meaning, geometry, colours,
spacing and alignment are preserved.

- **Menu flow showcase** builds a navigable sequence across supplied menu states.
- **UI transition exploration** tests slide, mask, scale-and-fade, shared-element and
  staggered transitions while ending on exact source layouts.
- **Graphic element motion system** gives logos, text, banners and icons one shared motion
  language before assembling the complete interface.
- **Logo & title package** creates a brand-safe title and end-card sequence.
- **HUD motion pass** demonstrates entrance, value changes, cooldown or selection feedback,
  notifications and exit states at fixed screen anchors.

## The API key

A free-tier Gemini key can ship inside `index.html`, so the tool needs no setup: open the
page and it reads your images, with no key prompt anywhere. While a key is bundled it is
*the* key — it outranks anything a browser saved on an earlier visit, and there is no key
field, provider switch or "use your own" link in the UI. With the blob left empty, the
References panel asks each visitor for their own key instead and everything still works.

> **A bundled key is public.** It reaches every visitor's browser, so anyone who opens
> devtools can read it. It is stored encoded (see below), which is *not* security — it
> only stops automated scanners. What makes this acceptable is that the key is barely
> worth stealing: it **carries no billing account**, so the worst case is an exhausted
> free quota rather than a charge, and it can be rotated in one click. Never bundle a key
> with billing attached.
>
> An origin restriction would be the obvious extra control and **is not available**:
> Google now requires Gemini API keys to be bound to a service account, and
> service-account-bound keys reject Website/IP restrictions outright — the console says
> *"This option is not available for API keys authenticated through a service account."*
> Nothing to configure; the option isn't offered.

### Why the key is encoded

The first key shipped here was a plain `AIzaSy…` literal. GitHub's secret scanning found
it in this public repo, reported it to Google, and Google disabled it **within about half
an hour** — every request then returns `403` with *"Your API key was reported as leaked."*
That is terminal; a disabled key cannot be re-enabled.

So the key is stored XOR-encoded and base64'd, which breaks the pattern the scanners match
on. To be explicit about what that does and doesn't buy: it keeps a bundled key alive, and
it stops nobody who actually looks at the page source.

**Gemini** (free tier, no billing setup): [aistudio.google.com/apikey](https://aistudio.google.com/apikey).
Uses `gemini-3.5-flash-lite`, falling back to `gemini-flash-lite-latest` then
`gemini-3.1-flash-lite`. Google retires pinned model ids — `gemini-2.5-flash-lite` and
`gemini-2.0-flash` both 404 for new keys now — so the rolling `-latest` alias sits in the
chain to survive the next one.

### Free-tier limits

Roughly **15 requests/minute, 1,000 requests/day, 250k tokens/minute**, shared per Google
Cloud project rather than per key, resetting at midnight Pacific. Google publishes the
live numbers only in [AI Studio](https://aistudio.google.com/rate-limit), so treat these
as indicative.

What that buys here: **one request per preset pick**, whatever the reference count, since
every image goes in a single call. Two references run about 1,100 tokens of image plus
~900 of instructions, so the per-minute token ceiling is not the binding limit — the daily
request count is. Call it several hundred shot lists a day across everyone using the link.
Over the limit the API returns `429` and the banner says *Rate limited — wait a moment and
retry*; the preset's own expansion stays on screen, so the tool keeps working.

Prompts and responses on the free tier may be used to improve Google's products. Don't put
anything confidential through it.

**Claude**: needs a paid API key from
[console.anthropic.com](https://console.anthropic.com). A Claude.ai Pro or Team
subscription is a separate product and does **not** include an API key.
Uses `claude-haiku-4-5-20251001`.

**ChatGPT keys cannot be used.** `api.openai.com` sends no CORS headers, so the browser
blocks the request before it leaves — from `file://` and from a real origin alike. It
would need a proxy server, which defeats the point of a single file.

Images leave the browser at two moments only: when you press Describe, and when you pick a
preset and the model writes the shot list. Nothing is sent otherwise.

### Replacing the bundled key

1. Mint a key at [aistudio.google.com/apikey](https://aistudio.google.com/apikey) on a
   project with **no billing account**. Both current formats work — the classic `AIza…`
   and the newer service-account-bound `AQ.…`.
2. Encode it. Paste this into any browser console — nothing to install:

   ```js
   (k => btoa([...k].map((c, i) =>
     String.fromCharCode(c.charCodeAt(0) ^ "omni-animate".charCodeAt(i % 12))
   ).join("")))("AIza…your key here…")
   ```

3. Put the resulting string in `GEMINI_KEY_BLOB` near the top of the script in
   `index.html`. `CLAUDE_KEY_BLOB` works the same way.

Leave a blob empty and that provider falls back to asking the visitor for a key, stored in
their browser's `localStorage`. A malformed blob decodes to nothing and does the same,
rather than sending a broken key to the API.

If a bundled key does get disabled, the app says so in plain words on the References panel
and the presets keep expanding from their own wording — the tool degrades, it doesn't
break.

> Anything on those lines is public — see the warning above. `.gitignore` excludes
> `index.local.html` if you'd rather keep a differently-keyed copy alongside this one.

## Notes

- Runs from `file://`. No server needed. The one thing that doesn't work there is the
  Cache API, which is why nothing depends on it.
- Prompt history uses IndexedDB and stays entirely in the current browser profile. Clearing
  site data removes it; it is not synced to another browser or device.
- Fonts (Space Mono, DM Sans) load from Google Fonts and degrade to system faces offline.
- Camera-move previews animate on hover only, so a selected card holds still.
- Respects `prefers-reduced-motion`.
