# Contributing

Thanks for helping make this a better field guide for the next book club. Most contributions are content: a better question, a fixed fact, a stronger source. Code changes are small because the whole app is one HTML file.

## Ground rules

1. **Stay dependency-free.** No frameworks, bundlers, npm packages or CDN scripts. The file should keep working when opened from disk, offline.
2. **Keep it a single file** unless there is a strong reason to split it. If you do split it, update the README and CLAUDE.md in the same PR.
3. **Spoilers must be flagged.** Any question that reveals late-book events needs the spoiler flag set (see below).
4. **Cite, paraphrase, don't copy.** Link to the original interview or article. Do not paste long quotes, and never reproduce passages from the novel or other copyrighted text.
5. **Don't state unverified claims as fact.** If you are not sure a character, scene or cross-book connection is accurate, say so in the text, as the existing "unverified" entries do.

## Getting started

```bash
git clone <repo-url>
cd book-club-exit-party
open exit-party-deluxe.html
```

Edit the file, reload the browser, and check your change. There is no build or test suite yet.

## Where things live

Everything is in `exit-party-deluxe.html`:

| Section | What it holds |
|---|---|
| `<style>` | All CSS, with theme colors as variables on `:root` (Side B overrides under `body.sideB`) |
| `sources` | Interview sources, keyed by short ID (`LH`, `NPR`, ...) |
| `Q` | The discussion questions, one array per question |
| `cast` | Character files as `[name, sideTag, body, facts]`. A tag of exactly `Side A` or `Side B` places the file in that column; any other tag puts it in the "Displaced" band. `facts` holds sourced details (`age`, `aka`, `look`, `race`, `origin`, `role`, `family`, `note`), a `check` note for claims the sources don't support, and `src` (source keys) |
| `refs` | Reviews cited by character files, keyed like `sources` |
| `motifs` | `[title, body]` |
| `connections` | Mandelverse timeline as `[title, tag, body]`. Title must be `YYYY · Book title`. The tag text picks the node style: "Direct…" is solid, "…echo…" is dashed, "check"/"unverified" is dotted, "Convergence" is highlighted |
| `#plot` template | The plot refresher, near the end of the script |

## Adding a question

Append a new array to `Q`. Fields are positional:

```
[id, level, theme, question, purpose, interpretations, cues, authorNote, sourceIds, followUpIds, spoiler, debate]
```

| Field | Notes |
|---|---|
| `id` | Next unused integer. Never reuse or renumber existing IDs, because follow-ups and users' saved notes depend on them. |
| `level` | Exactly one of `Intro`, `Intermediate`, `Advanced`, `Wildcard` |
| `interpretations` | One string, with options separated by `\|` |
| `sourceIds` | Array of keys from `sources`, or `[]` |
| `followUpIds` | Array of existing question IDs, or `[]`. An ID that doesn't exist will break rendering. |
| `spoiler` | `1` if it reveals late-book events, else `0` |
| `debate` | `1` if it tends to split the room, else `0` |

Counts in the heading, hero and progress bar are derived from the data, so there's nothing else to update. Keep `theme` short (one to three words), because it also labels the question in the running order, follow-up chips and progress-bar tooltips.

Good questions are open-ended, answerable without a literature degree, and have a clear "why ask" for the moderator. Provide at least two or three genuinely different interpretations, so the moderator isn't steering toward one answer.

## Adding character details

Only add a fact to a character's `facts` if a review or interview states it, and list that source in `src` (add it to `refs` if it's a review). Leave a field out rather than guessing. Never infer race or ethnicity from a name, and don't use Goodreads or other reader reviews as sources. If you can't confirm something already in a description, add a short `check` note instead of deleting it.

## Adding a source

Add an entry to `sources` as `ID:['Publisher','https://url','One-line description']`. Use the canonical article URL with no tracking parameters.

## Style

- Match the existing formatting. Content arrays are one item per line, and JS is compact.
- Use the typographic apostrophe (’) in prose, as the existing text does.
- Any user-supplied or data-driven text rendered into HTML must go through the `esc()` helper.
- Keep the page accessible: visible focus states, `aria-label`s on icon-only controls, and `prefers-reduced-motion` respected.

## Before you open a PR

- [ ] Opens and works from `file://` with no console errors
- [ ] Checked at phone width (~375px) and desktop
- [ ] Tried both Side A and Side B themes
- [ ] Search, filters, Random, and follow-up links still work
- [ ] New questions have the correct spoiler and debate flags
- [ ] Facts and sources checked; anything uncertain is labelled as such
- [ ] Documentation updated if you changed structure or behavior

## Pull requests

- Keep each PR focused: one theme, fix or feature.
- Use a short imperative title, such as "Add question on the jacaranda motif".
- Say what you changed and why. For content changes, list your sources.
- Screenshots help for visual changes.

## Reporting problems

Open an issue for factual errors (include the question number, what's wrong, and a source), bugs (browser, device and steps to reproduce), or ideas for new questions or features.

## Conduct

Be kind. Book clubs run on disagreement, so critique ideas generously and people not at all. Harassment or personal attacks are not welcome.
