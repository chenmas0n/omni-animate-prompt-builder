# Omni-Animate Prompt Builder

A single self-contained HTML file that builds shot-by-shot prompts for image-to-video
models. Drop in reference images, pick a starting point, adjust the shot list, copy the
prompt. No build step, no server, no dependencies — open `index.html` in a browser.

## Using it

Open `index.html`. That's the whole install.

Three steps:

1. **References** — drag in the images you'll feed the model. Tag each one
   (character, environment, product, style, lighting, start frame). Palette, warmth, tone,
   contrast and saturation are read off the pixels automatically. Add an API key here and
   step 2 does the rest; naming the images yourself is optional.
2. **Preset** — pick what kind of animation you want. 30 starting points across 10
   collapsible groups.
3. **Shots** — the shot list, already written, as a storyboard grid. Click any shot to
   edit its text or settings. The prompt sits at the bottom and updates as you type.

## How step 2 fills in step 3

With an API key, picking a preset sends **every reference in one call** along with that
preset's intent. The model looks at your actual images and writes the sequence against
them — naming each subject, then choosing framing, angle, lens, depth of field, camera
move, lighting, colour temperature, transitions and per-shot timing, and writing the scene
and action text for every beat. Subjects are cited as `(ref N)` so the video model binds
the words back to the right image. You land on step 3 with a finished list to fine-tune,
and a **Rewrite this shot list** button for another take.

Whatever the model sends back is checked against the app's own vocabulary before it is
used. A setting it invents, misspells or leaves out falls back to the preset's crafted
value rather than emptying the field, durations are re-fitted to the runtime cap, and a
reply with no usable shot list leaves the preset's own expansion in place. So a bad
response degrades to the old behaviour instead of a broken storyboard.

**Without a key the tool works exactly as it did before** — presets expand from their own
wording and your reference tags, and you write from there. The key only ever changes who
drafts the first version.

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

Without a key the tool still reads palette and tone from each image locally, and presets
expand from their own wording; you name the images and write the shots yourself. With a
key, the model names each reference and writes the shot list from them when you pick a
preset. It is the same key either way, stored in this browser only.

**Gemini** (recommended, free): get a key at
[aistudio.google.com/apikey](https://aistudio.google.com/apikey). No billing setup.
Uses `gemini-flash-lite-latest`, falling back to `gemini-2.0-flash`.

**Claude**: needs a paid API key from
[console.anthropic.com](https://console.anthropic.com). A Claude.ai Pro or Team
subscription is a separate product and does **not** include an API key.
Uses `claude-haiku-4-5-20251001`.

**ChatGPT keys cannot be used.** `api.openai.com` sends no CORS headers, so the browser
blocks the request before it leaves — from `file://` and from a real origin alike. It
would need a proxy server, which defeats the point of a single file.

Keys are kept in that browser's `localStorage`, one per provider. Images are sent to the
provider only when you press Describe, and nothing is sent otherwise.

### Baking a key into the file

Near the top of the script in `index.html`:

```js
const DEFAULT_GEMINI_KEY = "";
const DEFAULT_CLAUDE_KEY = "";
```

Fill one in and it pre-populates on every load. A key entered in the app overrides the
constant; **Forget key** falls back to it.

> A key on those lines is plain text in the file. Blank it before sharing the file or
> committing — whoever holds the file can spend against your account, and providers
> revoke keys found in repositories. `.gitignore` excludes `index.local.html` if you'd
> rather keep a keyed copy alongside the clean one.

## Notes

- Runs from `file://`. No server needed. The one thing that doesn't work there is the
  Cache API, which is why nothing depends on it.
- Fonts (Space Mono, DM Sans) load from Google Fonts and degrade to system faces offline.
- Camera-move previews animate on hover only, so a selected card holds still.
- Respects `prefers-reduced-motion`.
