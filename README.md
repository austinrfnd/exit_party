# Exit Party: Moderator's Field Guide

A single-page discussion dashboard for leading a book club conversation about *Exit Party* by Emily St. John Mandel. It is built for a live room: a moderator opens it on a laptop or phone and works through the questions.

No build step, no server, no dependencies. It is one HTML file.

> **Spoiler warning.** The Plot & cast and Mandelverse tabs contain full-novel spoilers.

## Features

- **26 discussion questions** in four tiers (Intro, Intermediate, Advanced, Wildcard), each with:
  - why to ask it,
  - possible interpretations,
  - discussion cues,
  - author perspective with links to interviews,
  - linked follow-up questions,
  - a live notes box.
- **Moderator tools:** search, difficulty filter, starred questions, "unasked only", a random-question picker that favors questions not yet discussed, and expand/collapse for all notes.
- **Spoiler and debate filters:** hide spoiler-heavy questions, or show only the ones that tend to split the room.
- **Session tracking:** a progress bar, "mark discussed", and notes per question.
- **Reference tabs:** plot refresher, character files for both Americas, recurring motifs, a Mandelverse connections timeline, and an interview source list.
- **Side A / Side B theme toggle**, plus responsive layout, reduced-motion support and a print stylesheet.

## Quick start

Open the file in any modern browser:

```bash
open exit-party-deluxe.html
```

Or serve the folder locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/exit-party-deluxe.html
```

## Privacy and data

There is no backend and nothing is sent anywhere. Stars, discussed status, live notes and the Side A/B choice are saved in your browser's `localStorage` under the key `exit-party-session`. They are per-browser and per-device. Clearing site data erases them.

## Project layout

```
exit-party-deluxe.html   # the whole app: markup, CSS, content data and JS
README.md                # this file
CONTRIBUTING.md          # how to contribute
CLAUDE.md                # orientation for AI coding assistants
```

## Contributing

Contributions are welcome, especially new questions, corrections and better sources. See [CONTRIBUTING.md](CONTRIBUTING.md).

## Content and sources

Plot and character summaries are discussion refreshers assembled from published interviews and the organizer's notes. They are **not** verified chapter by chapter against the novel. Some items, such as certain Mandelverse cameos, are explicitly marked unverified in the app. Please check details against your copy before correcting or adding anything.

Interview links point to the original publishers. Summaries are paraphrased, not quoted at length. Please keep it that way.

## Disclaimer

This is an unofficial fan project for book club discussion. It is not affiliated with or endorsed by the author or publisher.

## License

Released under the [MIT License](LICENSE).
