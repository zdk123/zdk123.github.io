# Personal site — "Window"

A single-page personal website for GitHub Pages (`zdk123.github.io`). No build step, no dependencies: `index.html` is the whole site.

## What it is
The top of the page is a window looking out on a backyard: a stylized SVG drawing of the owner's real yard (dark board fence, arborvitae, turf, stepping-stone path, patio with fire pit and hammock, string lights, row houses and utility poles behind). It is drawn from reference photos, which are kept out of the repo for privacy. Don't add details that would identify the location. The bottom is a windowsill with a few lines of first-person text. The scene follows the real conditions at the house:
- The idea: a window into the owner's yard in Brooklyn as it is right now. Every visitor sees the same thing, whatever their own time zone. Nothing about the visitor's location is used.
- **Time**: the current time in `HOME.tz` (America/New_York), via `homeNow()`.
- **Phase**: from real sunrise/sunset at `HOME` (central Brooklyn, deliberately rounded). Dawn is sunrise ±45 min, dusk is sunset −30/+45 min. This sets the sky, the room colours and the ink colour. String lights come on at dusk and night.
- **Season** (from the month in Brooklyn): Dec–Feb winter, Mar–May spring, Jun–Aug summer, Sep–Nov autumn.
- **Weather**: the latest observation from NWS stations near the house (`HOME.stations`: KJFK, then KLGA), ignored if over 2 h old; Open-Meteo for Brooklyn as the last resort (a model, which can miss light rain). Fetched after first paint, 2.5 s timeout per request, cached in localStorage for 15 min. If everything fails: snow in winter, rain in spring, clear otherwise.
- The opening sentence ("It's dark here, and it's raining on the yard.") is generated from phase, season and weather.
- The page re-checks every 30 seconds; weather every 15 minutes.

- **Clutter**: only the hoop stays put. Each day (the date in Brooklyn) seeds `dailyClutter()`: the sit-and-spin (most days), the ring frisbee (some days) and a few balls (big and small basketball, soccer ball, baseballs) in random spots, the same all day; fewer in winter. Summer adds the hose and a sprinkler, spraying on most dry days in daylight. In winter balls sit in the snow with a cap of it; on snowing days they're half buried, baseballs disappear, and the snowman appears. Every random roll happens regardless of hour, so nothing moves when the light changes.

To preview other conditions, use URL overrides: `?hour=21&weather=snow&season=winter&day=x` (weather: clear | cloudy | rain | snow; day: any string, a different one gives a different day's clutter).

## Where things live (all in index.html)
- `<style>`: layout and animations. Colours come from CSS variables (`--sky`, `--room`, `--ink`, …), which the script sets.
- The sill paragraphs (bio, "Things I'm working on", contact) are plain HTML in `.sill`.
- `PHASES` / `SEASONS`: the colour palettes.
- `buildScene()`: draws the yard with a small perspective projection `P(X, Y, Z)` (metres; X left/right, Y up, Z distance from the window). Season changes the trees, hammock and snow cover; `ball()` and `dailyClutter()` draw the day's toys.
- `buildGlow()`: the light layer (lit windows, string lights, fire pit, stars, moon) plus a colour wash for dawn/dusk/night.
- `fit()`: sets the shared viewBox so wide screens see more width and tall screens see full height.
- `render()`: works out phase, season and weather, then updates the variables, the opener line and the rain or snow particles.

## Writing
- Each post is a self-contained page at `posts/<slug>/index.html` (its own styles, images inlined as data URIs). The byline reads "Zachary D. Kurtz" and links back to `/`.
- `feed.xml` (RSS) lists the posts, newest first. The homepage fetches it and shows the first `<item>` as "My latest writing: …" in the sill (hidden if the fetch fails).
- To publish a post: add its folder, then add an `<item>` at the top of `feed.xml`. Nothing else to edit.

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
- Keep it static: no frameworks, no bundler. The homepage is a single file; posts are separate static pages.
- External resources: the Newsreader font from Google Fonts, and the weather requests (api.weather.gov, Open-Meteo).
- Keep the quiet, plain tone of the copy: first person, no pitch.
