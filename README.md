# Omni-Animate Prompt Builder

A single self-contained HTML file that builds shot-by-shot prompts for image-to-video
models. Drop in reference images, pick a starting point, adjust the shot list, copy the
prompt. No build step, no server, no dependencies — open `index.html` in a browser.

## Using it

Open `index.html`. That's the whole install.

Three steps:

1. **References** — drag in the images you'll feed the model. Tag each one
   (character, environment, product, style, lighting, start frame). Palette, warmth, tone,
   contrast and saturation are read off the pixels automatically. Naming them is optional —
   step 2 does it for you.
2. **Preset** — pick what kind of animation you want. 30 starting points across 10
   collapsible groups.
3. **Shots** — the shot list, already written, as a storyboard grid. Click any shot to
   edit its text or settings. The prompt sits at the bottom and updates as you type.

## How step 2 fills in step 3

Picking a preset sends **every reference in one call** along with that preset's intent. The
model looks at your actual images and writes the sequence against them — naming each
subject, then choosing framing, angle, lens, depth of field, camera move, lighting, colour
temperature, transitions and per-shot timing, and writing the scene and action text for
every beat. Subjects are cited as `(ref N)` so the video model binds the words back to the
right image. You land on step 3 with a finished list to fine-tune, and a **Rewrite this
shot list** button for another take.

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
| Game animation | Idle loop, Locomotion pass, Ability / VFX beat, Expression pass |
| Game marketing | Roster line-up, Logo sting, Gameplay montage |
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

## The API key

**A free-tier Gemini key ships in `index.html`, so there is nothing to set up.** Open the
page and it reads your images. The key panel stays folded away behind *Use my own API key
instead* on the References step.

> **That key is public and disposable.** It is plain text in a file served to every
> visitor, readable in devtools by anyone who opens the page. It carries no billing
> account, so the worst case is an exhausted daily quota, not a bill. It is restricted to
> this site's origin. If it gets burned or revoked, issue another at
> [aistudio.google.com/apikey](https://aistudio.google.com/apikey) and replace the
> constant — nothing else depends on it. **Never put a key with billing attached there.**

Paste your own key to spend your own quota instead; a typed key always wins over the
built-in one, and *Go back to the built-in key* restores it.

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

A typed key is kept in that browser's `localStorage`, one per provider. Images leave the
browser at two moments only: when you press Describe, and when you pick a preset and the
model writes the shot list. Nothing is sent otherwise.

### Changing the built-in key

Near the top of the script in `index.html`:

```js
const DEFAULT_GEMINI_KEY = "AIza…";
const DEFAULT_CLAUDE_KEY = "";
```

Fill one in and it is used on every load; blank it and the app asks for a key instead.
A key entered in the app overrides the constant.

> Anything on those lines is public — see the warning above. `.gitignore` excludes
> `index.local.html` if you'd rather keep a differently-keyed copy alongside this one.

## Notes

- Runs from `file://`. No server needed. The one thing that doesn't work there is the
  Cache API, which is why nothing depends on it.
- Fonts (Space Mono, DM Sans) load from Google Fonts and degrade to system faces offline.
- Camera-move previews animate on hover only, so a selected card holds still.
- Respects `prefers-reduced-motion`.
