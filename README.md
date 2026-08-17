# Omni-Animate Prompt Builder

A single self-contained HTML file that builds shot-by-shot prompts for image-to-video
models. Drop in reference images, pick a starting point, adjust the shot list, copy the
prompt. No build step, no server, no dependencies — open `index.html` in a browser.

## Using it

Open `index.html`. That's the whole install.

Three steps:

1. **References** — drag in the images you'll feed the model. Tag each one
   (character, environment, product, style, lighting, start frame) and name it. Palette,
   warmth, tone, contrast and saturation are read off the pixels automatically. Optionally
   add an API key and let a vision model name each image and list what's in the scene.
2. **Preset** — 30 starting points across 10 collapsible groups. Each one arrives fully
   written: shot list, framing, lens, camera move, lighting, timing, and the scene
   description for every beat, with your references woven into the text.
3. **Shots** — a storyboard grid. Click any shot to edit its text or settings. The prompt
   sits at the bottom and updates as you type.

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

## Optional: automatic image description

Without a key the tool still reads palette and tone from each image locally; you name the
images yourself. With a key, a vision model names each one and lists the scene elements.

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
