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

The `<style>` block runs from line 2 to the `</style>` line, one line per commented section (tokens, base, hero, toolbar, questions, reference tabs, presenter, responsive, reduced motion, print). Markup follows, then `<script>`. Inside the script, the content data comes first (`sources` through `connections`), then the logic.

**Data (top of script)**
- `sources`: `{ID: [publisher, url, blurb]}`
- `Q`: array of positional tuples:
  `[id, level, theme, question, purpose, interpretations, cues, authorNote, sourceIds[], followUpIds[], spoiler(0/1), debate(0/1)]`
  - `interpretations` is a single `|`-delimited string. It renders as the lettered "Possible readings" list.
  - Level is one of `Intro | Intermediate | Advanced | Wildcard`. `LV` holds that order and `MV` maps each level to its movement numeral and title (I · The arrival … IV · The afterparty).
  - IDs are not in level order (e.g. #22 is Advanced, #23 is Wildcard). `ordered()` groups by level, relying on a stable sort.
  - `theme` doubles as the short label in the running-order rail, follow-up chips and progress-tick tooltips, so keep it short.
- `cast`: `[name, sideTag, body, facts]`. A tag of exactly `Side A` or `Side B` puts the file in that column. Any other tag (e.g. `Displaced from A`) puts it in the "Displaced" band, with the tag shown on the file.
  - `facts` is an object rendered as the file's detail list by `facts()`. Keys: `age`, `aka`, `look`, `race`, `origin`, `role`, `family`, `note`, `check`, `src`. `age`, `look` and `race` always show, reading "Not stated in our sources" when missing. Other keys show only when set.
  - Every fact must come from a review or interview listed in `src` (keys from `sources` or `refs`). Never fill a field from a guess, a name, or a Goodreads/reader review. No source states any character's race or ethnicity, so `race` stays empty unless one does.
  - `check` flags a claim in `body` that our sources don't support or that they contradict. It renders in the caution color as "Check your copy". Prefer flagging over deleting the organizer's text.
- `refs`: `{ID: [publication, url]}` for reviews cited by character files. These are not author interviews, so they stay out of `sources` (which feeds the Interviews tab).
- `motifs`: `[title, body]`.
- `connections`: `[title, tag, body]`. `title` must be `YYYY · Book title`, because the year is split off into the timeline column. `tag` text sets the node style via `kind()`: contains "converg" → convergence, "check"/"unverified" → dotted, "direct" → solid, otherwise → dashed echo. The legend promises these styles, so keep tags honest.

**State**
- `state = {stars, done, notes, side, initialized}`, persisted to `localStorage['exit-party-session']` in try/catch. `save()` returns false when storage fails, and the live-notes status line shows that. The page must still work when storage is unavailable.
- Ephemeral UI state lives in module globals: `onlyStars`, `onlyRemaining`, `expand`, `opened` (a Set of open question IDs), `tab`, `level` (the level filter), `current` (the question the scrollspy marks).
- First run seeds `state.stars` from `must = [2,8,9,14,18,21]`.

**Rendering**
- `visible()` filters `Q` by `level`, search term (over tuple indices 1–7), stars, unasked, spoilers and debate.
- `render()` rebuilds `#list` (movement headers + `card()`s), `#rail` (running order), `#progress` (one tick per question), `#count`, `#tally` and the `#fcount` filter badge, all via `innerHTML`. Any open `<details>` is lost unless tracked in `opened`. Keep that in mind when adding stateful elements.
- `spy()` re-attaches an IntersectionObserver after each render. `setCur()` highlights the current question in the rail and ticks.
- Handlers are inline (`toggle(...)`, `jump(...)`, `note(...)`, `rememberOpen(id,open,el)`, `present(...)`, `clearFilters()`), so those functions must stay global. `toggle()` restores keyboard focus to the same button after the re-render.
- Reference tabs render once at load (cast groups, motifs, timeline, interview cards). Interview cards derive "Cited in" chips from `Q[i][8]`.
- `switchTab()` toggles `.hidden` on `main section`s, updates `aria-selected`/roving `tabindex`, and hides `#filters` outside the Questions tab.
- The sticky toolbar's height is mirrored into the `--stick` CSS variable by a ResizeObserver. Scroll margins and the rail's sticky offset use it.
- Presenter mode (`present`, `paintStage`, `step`, `closeStage`) is a full-screen dialog over the currently visible questions. ←/→ step, Esc closes, Tab is trapped.
- Side A/B is `body.sideB`, which overrides the color tokens *and* the radii (`--r`, `--r2`, `--pill`), so Side B is literally more rigid. The hero SVG is colored entirely by CSS classes. Its left half (`.ha-a`) is a Los Angeles skyline (palms, City Hall, downtown towers) and its right half (`.ha-b`) is Paris (Notre-Dame, the Eiffel Tower, Sacré-Cœur, Montparnasse). The active side's city lights up; petals only show on A, cameras and the scan beam only on B. Each half has its own coordinates label. On phones the SVG is pinned to the hero's bottom at 150% width so both landmark clusters stay visible. `flip()` uses a View Transition with a circular reveal from the switch, falling back to an instant swap without support or with reduced motion.

## Gotchas

- **Escaping:** all text interpolated into HTML goes through `esc()`. `sourceLinks()` does *not* escape URLs. Source URLs are trusted and hard-coded, so keep it that way.
- **Follow-up IDs** are resolved with `byId()`. Missing IDs are skipped rather than throwing, but don't rely on that: a bad ID silently drops a link.
- **Counts are derived.** The heading, hero stats and progress all use `Q.length`, `cast.length`, etc. Nothing is hard-coded.
- **Never renumber or reuse question IDs.** Saved stars, done flags and notes in users' browsers are keyed by ID.
- **Print:** `beforeprint`/`afterprint` force every note open and then restore `opened`. The print CSS disables animations, because the `.notes` reveal animation otherwise prints as blank space. Empty live-note boxes are hidden with `:placeholder-shown`.
- **Phones (≤760px):** `.q-body` becomes `display:contents` so the question number sits inline with the meta row. Check that if you restructure the card.
- `rememberOpen()` ignores toggle events while `expand` is on or from detached elements. Without that, stale async `toggle` events re-open notes after Expand all or after printing.
- Preserve accessibility behavior: focus-visible outlines, `aria-label`s/`aria-pressed`, tab roles, the skip link, and `prefers-reduced-motion` handling in both CSS and JS (`reduced()` gates smooth scrolling and the view transition).

## Conventions

- Match the existing compact style. Minified-ish CSS and dense JS are intentional, so don't reformat the whole file in an unrelated change.
- Typographic apostrophes (’) in prose.
- Small, focused diffs. Mention which question IDs or sections changed.
- `docs/screenshots/` holds the README images. When the design changes visibly, retake them: desktop at 1280×800 @2x and phone at 390×844 @3x, saved as JPEGs about 1600px (desktop) or 585px (phone) wide.

## Verifying changes

There is no automated test suite. After editing:
1. Open the file in a browser and check the console for errors.
2. Exercise search (and the `/` shortcut), the Filters panel (level, spoilers, debate, expand all), Starred, Unasked, Random, follow-up chips, the rail and progress ticks, Mark discussed, presenter mode (←/→/Esc) and Reset session.
3. Check all four tabs, both Side A and Side B themes, a ~375px viewport and the print preview.
4. Confirm state persists across a reload.

## Related docs

- [README.md](README.md): overview and usage
- [CONTRIBUTING.md](CONTRIBUTING.md): contribution workflow and the question schema in contributor-friendly form. Keep it in sync with the schema above.
