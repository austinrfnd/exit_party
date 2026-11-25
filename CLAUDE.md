# CLAUDE.md

Guidance for AI coding assistants working in this repo.

## What this is

A static, single-file discussion dashboard for a book club reading *Exit Party* (Emily St. John Mandel). Everything is in `exit-party-deluxe.html`: CSS, content data and vanilla JS. No build, no package manager, no tests, no backend.

## Hard constraints

- **No dependencies and no build tooling.** No npm, bundlers, frameworks or CDN scripts. The file must work offline from `file://`.
- **Keep it one file** unless the user asks otherwise.
- **Do not start a dev server or watcher** unless asked. To check changes, open the file in a browser or use a throwaway `python3 -m http.server` the user knows about.
- **This is not a Launch/Studioshare app.** There is no Dockerfile, database or `.launch/` directory. Don't add any.
- **Content accuracy matters.** Some entries (plot, cast, Mandelverse links) are explicitly *unverified*. Never present a guess about the novel as fact, and never invent quotes, scenes or citations. If unsure, say so in the text or ask.
- **Copyright:** paraphrase interviews, link to sources, no long quotes, no passages from the novel.

## Architecture

Line 2 is the `<style>` block. Line 4 onward is the `<script>`.

**Data (top of script)**
- `sources`: `{ID: [publisher, url, blurb]}`
- `Q`: array of positional tuples:
  `[id, level, theme, question, purpose, interpretations, cues, authorNote, sourceIds[], followUpIds[], spoiler(0/1), debate(0/1)]`
  - `interpretations` is a single `|`-delimited string.
  - Level is one of `Intro | Intermediate | Advanced | Wildcard`.
  - IDs are not in level order (e.g. #22 is Advanced, #23 is Wildcard). `render()` groups by level, relying on a stable sort.
- `cast`, `motifs`, `connections`: arrays fed to `tile()`.

**State**
- `state = {stars, done, notes, side, initialized}`, persisted to `localStorage['exit-party-session']` in try/catch. It must still work when storage is unavailable.
- Ephemeral UI state lives in module globals: `onlyStars`, `onlyRemaining`, `expand`, `opened` (a Set of open question IDs), `tab`.
- First run seeds `state.stars` from `must = [2,8,9,14,18,21]`.

**Rendering**
- `visible()` filters `Q` by level, search term (over tuple indices 1–7), stars, unasked, spoilers and debate.
- `render()` rebuilds `#list` via `innerHTML`, which means any open `<details>` is lost unless tracked in `opened`. Keep that in mind when adding stateful elements.
- Handlers are inline (`onclick="toggle(...)"`, `jump(...)`, `note(...)`, `rememberOpen(...)`), so those functions must stay global.
- Reference tabs render once at load via `tile()`.
- `switchTab()` toggles `.hidden` on `main section`s and hides `#filters` outside the Questions tab.
- Side A/B is `body.sideB`, which overrides CSS variables. The SVG hero is recolored with `hue-rotate`.

## Gotchas

- **Escaping:** all text interpolated into HTML goes through `esc()`. `sourceLinks()` does *not* escape URLs. Source URLs are trusted and hard-coded, so keep it that way.
- **Follow-up IDs** are resolved with `Q.find(x=>x[0]===n)[3]`. A missing ID throws and blanks the list.
- **Hard-coded count:** the "26 prompts" text in the section heading is static, so update it when adding or removing questions. The progress counter itself uses `Q.length`.
- **Never renumber or reuse question IDs.** Saved stars, done flags and notes in users' browsers are keyed by ID.
- The `.hero h1 span` rule uses `!important`. Don't strip it without checking the hero.
- Preserve accessibility behavior: focus-visible outlines, `aria-label`s, and `prefers-reduced-motion` handling in both CSS and the `jump()` animation.
- The print stylesheet expands all `<details>`. Check it if you change the question markup.

## Conventions

- Match the existing compact style. Minified-ish CSS and dense JS are intentional, so don't reformat the whole file in an unrelated change.
- Typographic apostrophes (’) in prose.
- Small, focused diffs. Mention which question IDs or sections changed.

## Verifying changes

There is no automated test suite. After editing:
1. Open the file in a browser and check the console for errors.
2. Exercise search, level filter, Starred, Unasked, spoiler and debate toggles, Random, Expand notes, follow-up links and Reset session.
3. Check all four tabs, both Side A and Side B themes, and a ~375px viewport.
4. Confirm state persists across a reload.

## Related docs

- [README.md](README.md): overview and usage
- [CONTRIBUTING.md](CONTRIBUTING.md): contribution workflow and the question schema in contributor-friendly form. Keep it in sync with the schema above.
