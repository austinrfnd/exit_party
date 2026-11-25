# Exit Party: Moderator's Field Guide

A single-page discussion dashboard for leading a book club conversation about *Exit Party* by Emily St. John Mandel. It is built for a live room: a moderator opens it on a laptop or phone and works through the questions.

No build step, no server, no dependencies. It is one HTML file.

> **Spoiler warning.** The Plot & cast and Mandelverse tabs contain full-novel spoilers.

## Features

- **26 discussion questions** in four movements (I · The arrival, II · Crossing over, III · Beyond the veil, IV · The afterparty), each with:
  - why to ask it,
  - possible readings,
  - discussion cues,
  - author perspective with links to interviews,
  - linked follow-up questions ("the thread"),
  - a live notes box.
- **Moderator tools:** search (press `/`), a Filters panel (level, spoilers, debate picks, expand all notes), starred questions, "unasked only", and a random-question picker that favors questions not yet discussed.
- **Running order:** on wide screens, a sticky sidebar lists every question by movement, marks discussed and starred ones, and follows your scroll position.
- **Presenter mode:** show one question full screen for the room, step with ← / →, mark it discussed, and press Esc to return.
- **Session tracking:** a progress bar with one segment per question, color-coded by movement, plus "mark discussed" and per-question notes.
- **Reference tabs:** plot refresher, character files split by side, recurring motifs, a Mandelverse timeline whose legend distinguishes direct links, thematic echoes and unverified claims, and interview cards showing which questions cite each source.
- **Side A / Side B:** two visual worlds, not just two palettes. Side A is warm dusk with drifting jacaranda petals. Side B is cold and rigid, with lit tower windows and a scanning beam. Switching plays a circular reveal.
- Responsive down to phone width, keyboard and screen-reader friendly, reduced-motion aware, and a print stylesheet that expands every note into a paper field guide.

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
