# Personal site — "Window"

A single-page personal website for GitHub Pages (`zdk123.github.io`). No build step, no dependencies: `index.html` is the whole site.

## What it is
The top of the page is a window looking out on a backyard: a stylized SVG drawing of the owner's real yard (dark board fence, arborvitae, turf, stepping-stone path, patio with fire pit and hammock, string lights, row houses and utility poles behind). It is drawn from reference photos, which are kept out of the repo for privacy. Don't add details that would identify the location. The bottom is a windowsill with a few lines of first-person text. The scene follows the visitor's local clock:
- **Phase**: from real sunrise/sunset, computed for a rough location guessed from the visitor's time zone (`TZ_PLACES`, falling back to the UTC offset). Dawn is sunrise ±45 min, dusk is sunset −30/+45 min. This sets the sky, the room colours and the ink colour. String lights come on at dusk and night.
- **Season** (from the month, shifted six months for southern-hemisphere visitors): Dec–Feb winter, Mar–May spring, Jun–Aug summer, Sep–Nov autumn.
- **Weather**: live from Open-Meteo (no key) for the same rough location, fetched after first paint with a 2.5 s timeout and cached in localStorage for 30 min. If blocked or slow, it falls back to snow in winter, rain in spring, clear otherwise.
- The idea: the visitor sees the owner's yard as if it were where *they* are, at their time of day, season and weather.
- The opening sentence is generated from phase, season and weather.
- The page re-checks every 30 seconds.

To preview other conditions, use URL overrides: `?hour=21&weather=snow&season=winter` (weather: clear | cloudy | rain | snow).

## Where things live (all in index.html)
- `<style>`: layout and animations. Colours come from CSS variables (`--sky`, `--room`, `--ink`, …), which the script sets.
- The sill paragraphs (bio, "Things I'm working on", contact) are plain HTML in `.sill`.
- `PHASES` / `SEASONS`: the colour palettes.
- `buildScene()`: draws the yard with a small perspective projection `P(X, Y, Z)` (metres; X left/right, Y up, Z distance from the window). Season changes the trees, hammock, snow cover and snowman.
- `buildGlow()`: the light layer (lit windows, string lights, fire pit, stars, moon) plus a colour wash for dawn/dusk/night.
- `fit()`: sets the shared viewBox so wide screens see more width and tall screens see full height.
- `render()`: works out phase, season and weather, then updates the variables, the opener line and the rain or snow particles.

## Content
- Name in `<title>` and the meta/OG tags: Zachary D. Kurtz.
- Email is assembled in script (`$("email").href = ...`) so it is never whole in the page source. Keep it that way.
- Social links: GitHub, LinkedIn, X, in the last paragraph.

## Deploy
1. Create the GitHub repo `zdk123.github.io` (public): `gh repo create zdk123.github.io --public`
2. Commit `index.html` (and optionally this file) to `main` at the repo root, then push.
3. Pages serves a `<user>.github.io` repo from `main` automatically. If it doesn't, enable it with: `gh api repos/zdk123/zdk123.github.io/pages -X POST -f "source[branch]=main" -f "source[path]=/"`
4. The site is live at https://zdk123.github.io within about a minute.

## Constraints
- Keep it a single static file: no frameworks, no bundler.
- External resources: the Newsreader font from Google Fonts, and the Open-Meteo weather request.
- Keep the quiet, plain tone of the copy: first person, no pitch.
