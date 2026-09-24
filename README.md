# Contribution #1: Add a svg icon for the crash cymbal

**Contribution Number:** 1  
**Student:** Kafilat Sarki-Uamr  
**Issue:** [GitHub Issue Link](https://github.com/Babali42/DrumBeatRepo/issues/511)  
**Status:** Phase I Complete

---

## Why I Chose This Issue

I chose this issue because I have an interest in UI/UX design and frontend development.

---

## Understanding the Issue

### Problem Description

The crash symbol object doesn't have a relevant icon which reflects the object's purpose

### Expected Behavior

There should be a relevant icon for the crash selection

### Current Behavior

Currently, the icon is a wavelength, not too relevant to a crash cymbal

### Affected Components

- `frontend/src/app/ui/pipes/drum-image.pipe.ts` — maps a MIDI drum note to an image filename
- `frontend/src/app/ui/pipes/drum-image.pipe.spec.ts` — unit test for the pipe
- `frontend/src/assets/images/drums/` — SVG icon assets for each drum piece

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. Run the app and load a drum pattern that includes a crash cymbal (MIDI notes 49 or 57).
2. Look at the icon rendered for that drum in the UI.
3. Observed result: the crash cymbal falls through to the `default.svg` icon (a generic wavelength graphic) because `drumImages` in `drum-image.pipe.ts` has no entry for notes 49/57.

### Reproduction Evidence

- **Commit showing the fix:** [b59aaf3](https://github.com/shanker-codepath/DrumBeatRepo/commit/b59aaf31b18ac289dae77653635de0e3b691eaa1) — "Added crash cymbal image"
- **Branch:** [shanker-codepath:cymbal-image](https://github.com/shanker-codepath/DrumBeatRepo/tree/cymbal-image)
- **Compare view:** [main...cymbal-image](https://github.com/Babali42/DrumBeatRepo/compare/main...shanker-codepath:DrumBeatRepo:cymbal-image)
- **My findings:** Before the fix, `drum-image.pipe.spec.ts` explicitly tested that a crash cymbal note resolved to `assets/images/drums/default.svg`, confirming the fallback-icon behavior was expected/known, not a rendering bug.

---

## Solution Approach

### Analysis

The `drumImages` lookup map in `drum-image.pipe.ts` only had entries for `kick` (36), `snare` (38), and `hihats` (42, 46). Any MIDI note without an entry — including crash cymbal notes 49 and 57 — falls through to `'default'` via the `?? 'default'` fallback in `getDrumPath`, so the crash cymbal renders the generic default icon instead of a cymbal-specific one.

### Proposed Solution

Add a `crash` entry to the `drumImages` map for MIDI notes 49 and 57, and add corresponding crash cymbal SVG assets to `frontend/src/assets/images/drums/`.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** The crash cymbal has no dedicated icon; it silently reuses the default icon because it's missing from the pipe's note-to-image map.

**Match:** The other drum pieces (kick, snare, hihats) already follow the pattern of a MIDI-note-to-name entry in `drumImages`, paired with an SVG in `frontend/src/assets/images/drums/`. The new crash icon follows that same pattern.

**Plan:**
1. Add `49: 'crash'` and `57: 'crash'` to `drumImages` in `drum-image.pipe.ts`.
2. Add crash cymbal SVG assets — a light-theme version (`crash-light.svg`, black fill) and a dark-theme version (`crash-dark.svg`, white fill).
3. Update `drum-image.pipe.spec.ts` so the existing crash cymbal test expects `assets/images/drums/crash.svg` instead of `default.svg`.

**Implement:** [b59aaf3](https://github.com/shanker-codepath/DrumBeatRepo/commit/b59aaf31b18ac289dae77653635de0e3b691eaa1) on the [cymbal-image](https://github.com/shanker-codepath/DrumBeatRepo/tree/cymbal-image) branch.

**Review:** DrumBeatRepo has no `CONTRIBUTING.md` or `.github/PULL_REQUEST_TEMPLATE.md`. The root `README.md` has "Contributing" and "Contribution Workflow" sections instead: fork + branch from `main`, make changes, pass all tests, open a PR. From `frontend/package.json` and recent merged PRs, the de facto conventions are:
- **Lint:** `npm run lint` (ESLint, `eslint.config.mjs`, max 30 warnings)
- **Tests:** `npm test` (Karma) or `npm run test-vitest` (Vitest) — the project appears to be mid-migration between the two
- **Commit style:** informal Conventional Commits, e.g. `fix: improve mobile sequencer scrolling`, `docs: add X as a contributor`
- **PR description:** a short prose summary of the fix followed by a "Changes Made" bullet list (CodeRabbit auto-adds a release-notes summary on top of that)

My commit ("Added crash cymbal image") doesn't follow the `type: summary` convention — worth renaming to something like `fix: add crash cymbal icon` before opening the PR. I haven't run `lint` or the test suite myself yet since I'm working from the diff rather than a local checkout.

**Evaluate:** Run the updated unit test suite (`drum-image.pipe.spec.ts`) and visually confirm the crash cymbal icon renders correctly in both light and dark mode for notes 49 and 57.

> **Note:** `DrumImagePipe` resolves the base path to `assets/images/drums/crash.svg`; a separate `IconDarkModePipe` (chained in the template as `track.midiNote | drumImage | iconDarkMode`) then rewrites `.svg` to `-light.svg` or `-dark.svg` based on the current theme. That's why the two assets are named `crash-light.svg`/`crash-dark.svg` rather than `crash.svg` — this matches the existing pattern used by `kick`, `snare`, `hihats`, and `default`.

---

## Testing Strategy

### Unit Tests

- [x] `drum-image.pipe.spec.ts`: crash cymbal (MIDI note for `CRASH_CYMBAL_1`) resolves to `assets/images/drums/crash.svg` (updated from the old expectation of `default.svg`)

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results — e.g. loading a pattern with crash notes 49/57 in the running app and confirming the icon renders]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:**
  - `frontend/src/app/ui/pipes/drum-image.pipe.ts` (added `49: 'crash'`, `57: 'crash'` to the map)
  - `frontend/src/app/ui/pipes/drum-image.pipe.spec.ts` (updated expected icon path)
- **Files added:**
  - `frontend/src/assets/images/drums/crash-light.svg` (black fill, `#000000`)
  - `frontend/src/assets/images/drums/crash-dark.svg` (white fill, `#FFFFFF`)
- **Key commits:** [b59aaf3](https://github.com/shanker-codepath/DrumBeatRepo/commit/b59aaf31b18ac289dae77653635de0e3b691eaa1) — "Added crash cymbal image"
- **Approach decisions:** Followed the existing pattern in `drumImages` (MIDI note → icon name) rather than introducing a new lookup mechanism. Provided both light and dark SVG variants because the template chains `drumImage` with `iconDarkMode`, which rewrites the resolved path to `-light.svg`/`-dark.svg` based on the active theme — the same pattern already used for `kick`, `snare`, `hihats`, and `default`.

---

## Pull Request

**PR Link:** Not yet opened — no PR exists from the `cymbal-image` branch as of this writing.

**PR Description (draft):**

> Resolved [#511](https://github.com/Babali42/DrumBeatRepo/issues/511): the crash cymbal had no dedicated icon and fell back to the generic default (wavelength) icon.
>
> **Changes Made**
> - Added `49: 'crash'` and `57: 'crash'` mappings to `drumImages` in `drum-image.pipe.ts`
> - Added `crash-light.svg` and `crash-dark.svg` icon assets under `frontend/src/assets/images/drums/`
> - Updated `drum-image.pipe.spec.ts` so the crash cymbal test expects `assets/images/drums/crash.svg` instead of `default.svg`

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [DrumBeatRepo root README — Contributing / Contribution Workflow sections](https://github.com/Babali42/DrumBeatRepo#contributing) (no separate `CONTRIBUTING.md` exists)
- [Issue #511](https://github.com/Babali42/DrumBeatRepo/issues/511)
- [Tutorial or Stack Overflow post that helped]
