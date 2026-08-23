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

## Consumed by

[Dhana005/Watch2Do](https://github.com/Dhana005/Watch2Do) —
`src/data/exerciseLibrary.ts` reads this repo's raw content URLs directly.
