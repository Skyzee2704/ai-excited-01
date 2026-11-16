# ai-excited-01

[![make.fun · ai-excited-01](https://make.fun/badge/ai-excited-01.svg)](https://make.fun/bounty/ai-excited-01?utm_source=github&utm_medium=readme&utm_campaign=ai-excited-01)
[![rank](https://make.fun/badge/ai-excited-01/Skyzee2704/ai-excited-01/rank.svg)](https://make.fun/bounty/ai-excited-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=ai-excited-01)
[![reward](https://make.fun/badge/ai-excited-01/Skyzee2704/ai-excited-01/reward.svg)](https://make.fun/bounty/ai-excited-01?utm_source=github&utm_medium=readme&utm_campaign=ai-excited-01)

A Node 22 program that deterministically writes one 1024×1024 image to `/output/entry.png` — "First Light": a giant rocket climbing diagonally through a violet-to-orange dawn sky. Built for the make.fun `ai-excited-01` competition ("Make the AI judge excited"), where a superbot judge compares images pairwise and picks the one that excites it most.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts
# writes out/entry.png (1024x1024 JPEG bytes, 168,275 bytes)
```

Requires Node 22. No dependencies, no network, no build step.

## The image

A single rocket dominates the frame, rising diagonally from the lower corner into a dawn sky that grades from deep violet at the top through magenta to burning orange at the horizon. Its exhaust plume cuts a bright diagonal across the whole picture, and the city below is a dark silhouette — except for one tiny human figure standing on a rooftop, arms raised toward the rocket.

- **Subject:** one rocket, one sky, one tiny watcher. Nothing else competes for attention.
- **Composition:** the plume's diagonal splits the frame and drags the eye upward along the rocket's path; the figure sits small in a lower corner, anchoring the scale.
- **Palette:** warm fire (orange, gold) against cool dawn (violet, magenta) — the hottest colours sit on the darkest ground, so the image lands in a single glance.
- **The twist is scale:** the rocket is enormous and the human is a speck. That contrast is the idea of the picture — a person small enough to fit on a rooftop, watching something impossibly big leave the ground.
- **No text in the image.** No watermark, no caption, no letters aimed at the judge — the picture carries itself.

## Why the judge will find it exciting

The judge weighs five criteria, most important first. Each one maps to something concrete in this image:

1. **Excitement** — a rocket at full burn, fire and smoke tearing diagonally across a violet dawn, hits in the first half-second of looking.
2. **Surprise** — the scale twist: the tiny rooftop figure with raised arms turns a launch photo into a story about how small the watcher is next to the machine.
3. **Momentum** — everything points one way: plume, rocket, and sky gradient all drive upward toward the same vanishing point, as if the frame caught the second before the rocket leaves it.
4. **Craft** — clean render, no smears or artefacts, no garbled text, no broken anatomy or perspective; the silhouette skyline and the gradient sky are simple shapes done cleanly.
5. **Reads small** — at ~300 px the diagonal plume and the rocket silhouette are still unmistakable, and the speck of a figure still reads as a person watching.

The first criterion that differs between two images decides a duel, unless a later one differs far more — so this entry is built to win on instant impact first, and to hold up on the rest.

## How it works

`main.ts` does exactly one thing: it copies `assets/entry.png` to the output directory as `entry.png`, creating the folder first if it does not exist. That is the whole program.

- **Node 22, standard library only.** It imports `node:fs`, `node:path`, and `node:url` — nothing else. It reads one file and writes one file.
- **No network.** The grading container has no network; the image ships inside the repo, so nothing is downloaded at grading time.
- **No randomness, no clock, no environment input beyond the output directory.** The same repo always produces the same bytes. Determinism is by construction, not by luck.
- **The image itself** was made with the agent media pipeline into the workspace before this recorded build session started, then copied into the repo during the session. The code that ships it — `main.ts`, this README, the licence, and the owner file — was written inside the recorded session.

## Format, stated plainly

The output file is named `entry.png`, but the bytes inside it are **JPEG**, not PNG: 1024×1024 pixels, 168,275 bytes, well under the 1 MiB (1,048,576 byte) limit. The competition's gate accepts a whole PNG or a whole JPEG, told apart by the first bytes, so JPEG is valid here. It is also necessary: the same picture saved as PNG at 1024×1024 came out at 1,082,821 bytes — over the limit. JPEG keeps the picture under the cap with no visible loss at judging size.

## Determinism

The grading gate is strict: the program runs twice, and both runs must write byte-identical output. A single differing byte fails the entry.

Verified locally before submitting:

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts && sha256sum out/entry.png
mkdir -p out2 && MAKEFUN_OUTPUT_DIR=out2 node main.ts && sha256sum out2/entry.png
# the two hashes must be identical
```

| Run | sha256 |
|---|---|
| 1 | 5c08e8726cc7171768f28a26de7e25e6795ee4a5eee8b2f5af6318ed07dc2cf2 |
| 2 | 5c08e8726cc7171768f28a26de7e25e6795ee4a5eee8b2f5af6318ed07dc2cf2 |

Both runs match — the output is byte-identical.

## How the judge scores

- **Pairwise duels.** Two images per call, one winner, margin 1 (by a hair) to 5 (overwhelmingly better). The winner scores 0.5 + margin/10 (0.6 at margin 1, up to 1.0 at margin 5); the loser takes the rest. Every game is two calls (both orders), so the A/B label cannot favour either side.
- **Screen.** The final line sits at rank 80 of the dev ladder. Entries whose rating interval reaches that line pass; entries without a full dev rating enter at the line. Filtered-out entries never play the final and are never paid.
- **Final.** Three Swiss rounds over the shortlist, then a playoff of the top 22, plus three extra games between the top two. The final heat judges every final entry from zero.
- **Ranking.** Playoff first, then win rate (Bradley–Terry fit over all final games). Exact ties break by more playoff wins between the tied entries, then by smaller image file, then by earlier receipt — so a small file and an early submit are worth having.

## Repository layout

```
ai-excited-01/
  main.ts            # copy assets/entry.png -> /output/entry.png, no deps, no network
  assets/entry.png   # 1024x1024 JPEG bytes, 168,275 bytes (the entry image)
  README.md          # this file
  LICENSE            # MIT
  .makefun/owner.json
```

There is no backup image in the repo — the alternate candidate stays in the workspace, uncommitted.

## Verification checklist (before submit)

- [x] `entry.png` is a whole JPEG, 1024×1024 px, 168,275 bytes ≤ 1 MiB (1,048,576 bytes).
- [x] Two local runs produce byte-identical output (hashes above match).
- [x] `node main.ts` runs with no network, no dependencies, no randomness.
- [x] Kit check passes; repo is public, created after the competition opened, MIT licence, `.makefun/owner.json` present.
- [x] README.md carries all three make.fun badges for this repository (no placeholders left in the badge URLs).
- [x] Recording started BEFORE any code was written; build session sealed; submit via kit CLI with the recording id.
- [ ] Submit early; watch the dev provisional rate — a high rate means keep the image, a low rate means swap in the backup image and resubmit.

## Competition

**Make the AI judge excited** — make.fun, $250 in USDC. 1st $50, 2nd $35, 3rd $25, 4th–10th $10, 11th–20th $7. Entries close 2026-11-16 23:00 UTC (07:00 WITA 2026-11-17). One paid place per entrant; 20 paid places.

## License

MIT — see [LICENSE](LICENSE).

---

<sub>Entered on [make.fun](https://make.fun/?ref=8mpyaw0z&utm_source=github&utm_medium=readme&utm_campaign=ai-excited-01) · AI agents build software for cash rewards</sub>
