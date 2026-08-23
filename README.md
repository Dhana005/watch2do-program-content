# Watch2Do Program Content

Source of truth for curated content behind Watch2Do's multi-day Programs
(the Dashboard → Week → Day → Exercise flow) — real exercise/pose/session
data and reference images, owned here instead of hotlinked from a third
party.

## Structure

```
exercises/{exerciseId}/
  data.json    # name, level, equipment, muscles, instructions, sets/reps/rest
  0.jpg        # reference photo, position A
  1.jpg        # reference photo, position B (app animates between the two)

yoga/{poseId}/
  data.json    # name, level, equipment, focus area, instructions, hold/rest

meditation/{sessionId}/
  data.json    # name, level, duration, focus, step-by-step guidance script

programs/{programId}/
  manifest.json   # { programId, title, days: { "1": [id, ...], ... } }
```

Each of `exercises/`, `yoga/`, and `meditation/` is its own shared library —
the same entry can be referenced by multiple days without duplicating its
data. A program's manifest only lists ids; which library a given id lives in
follows the program (`prog-weight`/`prog-fitness` → `exercises/`,
`prog-yoga` → `yoga/`, `prog-meditation` → `meditation/`).

`yoga/` and `meditation/` reuse the same field shape as `exercises/` (it
already covers name/level/instructions/hold-or-duration/rest well enough),
just without photos — poses and sessions have no source photo library, so
those entries omit `images` rather than link a fake or broken one. In the
app, `Exercise.statLabels` relabels the sets/reps/rest row for meditation
specifically (e.g. "Rounds/Duration/Pause") since a guided session isn't
rep-based — see `src/data/exerciseLibrary.ts`. A meditation session's three
duration tiers (5/10/15 min) are each their own id, since the guidance
script genuinely gets longer and deeper, not just repeated.

## Sourcing

- `exercises/*/data.json` and `0.jpg`/`1.jpg` are adapted from
  [yuhonas/free-exercise-db](https://github.com/yuhonas/free-exercise-db)
  (The Unlicense — public domain, no attribution required). See
  `LICENSE-NOTES.md`.
- `yoga/` and `meditation/` are original content (standard, well-documented
  asanas/techniques, written for this project) — no source dataset or
  photos exist for these.

## Coverage

- `exercises/`: 103 real bodyweight exercises.
- `yoga/`: 36 real yoga poses across 6 themes (Standing, Seated &
  Hip-Opening, Core & Balance, Backbends & Chest Openers, Twists & Forward
  Folds, Restorative).
- `meditation/`: 18 guided sessions — 6 techniques (breath awareness, body
  scan, loving-kindness, thought observation, progressive muscle relaxation,
  gratitude reflection) × 3 duration tiers each.
- `programs/prog-weight/` (30-Day Fat Loss Challenge): all 26 workout days
  covered, 6 exercises each (156 slots). Days 1-20 use exercises unique to
  that stretch of the program; days 22-30 (Weeks 5-6) intentionally reuse
  exercises from earlier weeks in fresh day-by-day combinations rather than
  forcing artificial variety past what the source dataset can support with
  real, well-documented, no-equipment-appropriate moves — no exercise
  repeats within any single day or week, but the same move can appear in
  more than one week, same as real fitness programs.
- `programs/prog-fitness/` (6-Week Strength Builder): all 36 workout days
  covered, 6 exercises each (216 slots), reusing the same 103-exercise pool
  as prog-weight in a repeating 6-day theme split (Legs & Hips, Push, Core,
  Posterior Chain & Glutes, Full-Body Conditioning, Core & Shoulders).
- `programs/prog-yoga/` (4-Week Yoga Foundations): all 24 practice days
  covered, 6 poses each (144 slots) — the same 6 poses per theme every week,
  by design (repetition builds familiarity in a foundations program).
- `programs/prog-meditation/` (21-Day Mindfulness Challenge): all 18 session
  days covered, one guided session per day — the 6 techniques repeat across
  the 3 weeks with longer, deeper guidance each time (5 → 10 → 15 min), real
  progression rather than a literal repeat.

## Consumed by

[Dhana005/Watch2Do](https://github.com/Dhana005/Watch2Do) — `src/data/exerciseLibrary.ts`
is a generated, bundled copy of this repo's content (so the app works
offline and without an extra network round-trip on every program screen);
only the `exercises/*/0.jpg`/`1.jpg` reference photos are fetched from this
repo's raw URLs live. This repo is the source of truth — when curating a
new program, author/derive the data here first, generate the matching copy
in `exerciseLibrary.ts`, and keep both in sync going forward.
