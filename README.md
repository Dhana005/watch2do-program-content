# Watch2Do Program Content

Source of truth for curated content behind Watch2Do's multi-day Programs
(the Dashboard → Week → Day → Exercise flow) — real exercise/session data
and reference images, owned here instead of hotlinked from a third party.

## Structure

```
exercises/{exerciseId}/
  data.json    # name, level, equipment, muscles, instructions, sets/reps/rest
  0.jpg        # reference photo, position A
  1.jpg        # reference photo, position B (app animates between the two)

programs/{programId}/
  manifest.json   # { programId, title, days: { "1": [exerciseId, ...], ... } }
```

`exercises/` is a shared library — the same exercise can be referenced by
multiple days or multiple programs' manifests without duplicating its data.

Not every program is exercise-based (meditation, coding, cooking, etc. will
need their own session schema) — this repo is meant to hold all of those
eventually, `exercises/` + `programs/*/manifest.json` is just the first one.

## Sourcing

`exercises/*/data.json` and `0.jpg`/`1.jpg` are adapted from
[yuhonas/free-exercise-db](https://github.com/yuhonas/free-exercise-db)
(The Unlicense — public domain, no attribution required). See
`LICENSE-NOTES.md`.

## Coverage

- `exercises/`: 103 real exercises.
- `programs/prog-weight/` (30-Day Fat Loss Challenge): all 26 workout days
  covered, 6 exercises each (156 slots). Days 1-20 use exercises unique to
  that stretch of the program; days 22-30 (Weeks 5-6) intentionally reuse
  exercises from earlier weeks in fresh day-by-day combinations rather than
  forcing artificial variety past what the source dataset can support with
  real, well-documented, no-equipment-appropriate moves — no exercise
  repeats within any single day or week, but the same move can appear in
  more than one week, same as real fitness programs.

## Consumed by

[Dhana005/Watch2Do](https://github.com/Dhana005/Watch2Do) —
`src/data/exerciseLibrary.ts` reads this repo's raw content URLs directly.
