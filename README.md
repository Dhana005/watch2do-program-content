# Watch2Do Program Content

Source of truth for curated content behind Watch2Do's multi-day Programs
(the Dashboard → Week → Day → Exercise flow) — real exercise/pose/session/
lesson data and reference images, owned here instead of hotlinked from a
third party.

## Structure

```
exercises/{exerciseId}/
  data.json    # name, level, equipment, muscles, instructions, sets/reps/rest
  0.jpg        # reference photo, position A
  1.jpg        # reference photo, position B (app animates between the two)

yoga/{poseId}/
  data.json       # same shape as exercises/
  0.jpg, 1.jpg,   # AI-generated (gpt-image-1) — no real pose-photo dataset
  2.jpg           # exists. All 36 poses regenerated 2026-08 with three
                  # frames each (setup / midway / full pose), an explicit
                  # per-pose prompt built from that pose's own real
                  # instructions text, and a consistent model/studio style
                  # prompt across all of them. Quality tier varies per pose
                  # (high/medium/low, split across a few generation runs to
                  # manage cost) — accuracy came from the detailed prompts,
                  # not the quality tier, so this is intentional, not a
                  # quality regression; spot-checked at each tier.

meditation/{sessionId}/
  data.json    # same shape, statLabels relabels sets/reps/rest for display,
               # videoId added 2026-08 (see below)
  0.jpg, 1.jpg # AI-generated, shared across a technique's 3 duration-tier ids
               # (prog-meditation's 18 sessions only — the 9 real breathing
               # techniques curated 2026-08 for prog-meditation-alt have a
               # real videoId and no images at all, since they never had a
               # fake-photo phase to begin with — see Sourcing)

learning/{lessonId}/
  data.json    # same shape, no images — instructions hold real phrases to
               # practice; `icon` ('💬') is shown in place of a photo where
               # no video exists (see Sourcing). `videoId` added 2026-08: a
               # real, verified YouTube video matched to that exact
               # lesson's topic (e.g. Oxford Online English for
               # weather-talk) — a program named "Conversational" needs
               # real spoken audio, not a static emoji.

coding/{lessonId}/
  data.json    # same shape, no images or video yet — instructions hold a
               # real, correct Python snippet; `icon` ('💻') shown in place
               # of a photo (see Sourcing)

cooking/{recipeId}/
  data.json    # same shape, no images — instructions hold real cooking
               # technique guidance; `videoId` is a real, verified recipe
               # video reused from the `cooking` entry in
               # scripts/seed-videos.mjs (already-audited real durations),
               # not fresh per-recipe research. `icon` ('🍱') is the
               # program-day.tsx fallback.

sleep/{routineId}/
  data.json    # same shape, no images — instructions hold real,
               # evidence-based sleep-hygiene guidance (consistent
               # schedule, wind-down routine, room temperature/darkness,
               # caffeine timing, morning light for circadian reset);
               # `videoId` reused from the `sleep` entry in
               # scripts/seed-videos.mjs (a small real pool of guided
               # sleep meditation / bedtime yoga videos, cycled across
               # nights). `icon` ('😴') is the fallback.

programs/{programId}/
  manifest.json   # { programId, title, days: { "1": [id, ...], ... } }
```

Each of `exercises/`, `yoga/`, `meditation/`, `learning/`, and `coding/` is
its own shared library — the same entry can be referenced by multiple days
without duplicating its data. A program's manifest only lists ids; which
library a given id lives in follows the program (`prog-weight`/`prog-fitness`
→ `exercises/`, `prog-yoga` → `yoga/`, `prog-meditation` → `meditation/`,
`prog-learning` → `learning/`, `prog-coding` → `coding/`).

All five reuse the same field shape (it already covers name/level/
instructions/duration/rest well enough) rather than forking a schema per
content type. `Exercise.statLabels` (in the app) relabels the sets/reps/rest
row when those don't literally apply — "Rounds/Duration/Pause" for a
meditation session, "Phrases/Practice Time/Review" for a language lesson,
"Concepts/Practice Time/Review" for a coding lesson. A meditation session's
three duration tiers (5/10/15 min) are each their own id, since the guidance
script genuinely gets longer and deeper, not just repeated.

## Sourcing

- `exercises/*/data.json` and `0.jpg`/`1.jpg` are adapted from
  [yuhonas/free-exercise-db](https://github.com/yuhonas/free-exercise-db)
  (The Unlicense — public domain, no attribution required). See
  `LICENSE-NOTES.md`.
- `yoga/` and `meditation/` data is original content (standard,
  well-documented asanas/techniques, written for this project); their
  photos are AI-generated (OpenAI `gpt-image-1`). yoga/ was regenerated
  2026-08 with an explicit per-pose prompt built from each pose's own real
  `instructions` text (the original pass had gotten several complex poses
  — Eagle Pose, Half Moon Pose — visibly wrong, not just low-res); quality
  tier varies per pose (high/medium/low across a few runs, to manage
  cost) since the fix was the detailed prompt, not the quality tier —
  confirmed by spot-checking complex poses at each tier before committing
  to it. meditation/ is still the original "low quality" pass.
- `learning/`'s 36 lessons each carry a real, verified `videoId` (found via
  YouTube Data API search, matched to that exact lesson's topic and
  checked embeddable) as of 2026-08 — a program literally named
  "Conversational" needs real spoken audio and a real teacher, not a
  static emoji. `coding/` doesn't have an equivalent yet.
- `learning/` and `coding/` are original content too (standard ESL phrases;
  real, correct Python code examples), and — aside from learning/'s new
  videos above — have **no photos** — a lesson has no physical position to
  demonstrate the way a pose or exercise does, so generating two similar
  stock photos and animating between them would be decoration pretending
  to be a demo, not a real reference. These use a single `icon` emoji (💬
  for learning, 💻 for coding) as the program-day.tsx list thumbnail
  instead; the app's `ExerciseAnimation` component shows that in place of
  a photo whenever there's no video for that specific lesson.

## Coverage

- `exercises/`: 103 real bodyweight exercises.
- `yoga/`: 36 real yoga poses across 6 themes (Standing, Seated &
  Hip-Opening, Core & Balance, Backbends & Chest Openers, Twists & Forward
  Folds, Restorative).
- `meditation/`: 18 guided sessions — 6 techniques (breath awareness, body
  scan, loving-kindness, thought observation, progressive muscle relaxation,
  gratitude reflection) × 3 duration tiers each — plus 9 real breathing
  techniques (box breathing, 4-7-8, diaphragmatic, alternate nostril,
  extended exhale, coherent, ujjayi, breath counting, lion's breath) added
  2026-08 for prog-meditation-alt, each with a real videoId from the start.
- `learning/`: 36 English conversation lessons across 6 topic categories
  (Everyday Essentials, Food & Dining, Travel & Directions, Work &
  Technology, Social & Emotions, Health & Hobbies), each with 4 real key
  phrases to practice.
- `coding/`: 48 Python lessons across 8 topic categories (Fundamentals,
  Control Flow, Data Structures, Functions, Working with Data,
  Object-Oriented Python, Tools & Libraries, Real Projects), each with a
  real, runnable code example.
- `cooking/`: 11 real one-pot/meal-prep techniques, each with a real
  matched YouTube video from the already-audited `cooking` video pool.
- `sleep/`: 18 real, evidence-based sleep-hygiene routines (consistency,
  wind-down, environment, relaxation technique, caffeine/screen timing,
  circadian light exposure), reusing a small real video pool (guided sleep
  meditation + bedtime yoga) from the already-audited `sleep` video pool.
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
- `programs/prog-yoga-alt/` (Power Yoga Intensive): all 12 practice days
  covered, 6 poses each (72 slots), reusing the same 36-pose `yoga/` library
  as prog-yoga — no new poses or images. Days mix poses across prog-yoga's
  6 themes into faster-paced, cross-category "power flow" combos (e.g.
  Standing Strength: Mountain, Warrior I/II, Triangle, Chair, Half Moon)
  instead of one theme per day, repeating the same 6-day split across its
  2 weeks. Added 2026-08 (previously fell back to the generic single-video
  screen, the only yoga-goal program still uncurated).
- `programs/prog-meditation/` (21-Day Mindfulness Challenge): all 18 session
  days covered, one guided session per day — the 6 techniques repeat across
  the 3 weeks with longer, deeper guidance each time (5 → 10 → 15 min), real
  progression rather than a literal repeat.
- `programs/prog-meditation-alt/` (Guided Breathing Series): all 9 workout
  days covered, one real breathing technique per day, no repeats — added
  2026-08 (previously fell back to the generic single-video screen, the
  only meditation-goal program still uncurated).
- `programs/prog-learning/` (Conversational English Bootcamp): all 36 lesson
  days covered, one lesson per day, one topic category per week (e.g. all of
  "Food & Dining" in week 2) — a linear curriculum, not a repeating split.
- `programs/prog-coding/` (Python Basics Bootcamp): all 48 lesson days
  covered, one lesson per day, one topic category per week, same linear
  structure as prog-learning.
- `programs/prog-weight-alt/` (HIIT Cardio Blast Series): all 9 workout days
  covered, 6 exercises each, reusing prog-weight's exercise pool in
  explosive/cardio combos (jump squats, sprints, plyo push-ups) instead of
  the strength-focused split.
- `programs/prog-fitness-alt/` (Bodyweight Basics): all 12 workout days
  covered, 6 exercises each, same pool as prog-fitness in a gentler,
  beginner-focused split.
- `programs/prog-cooking/` (Meal Prep Mastery Series): all 5 days covered,
  one real one-pot/meal-prep technique per day.
- `programs/prog-cooking-alt/` (One-Pot Dinners): all 6 workout days
  covered, one real one-pot technique per day (pasta, sheet-pan, stir-fry,
  soup/stew, grain bowl, freezer-friendly) — distinct combos from
  prog-cooking, same `cooking/` library.
- `programs/prog-sleep/` (Better Sleep in 7 Nights): all 6 workout nights
  covered, one real sleep-hygiene routine per night.
- `programs/prog-sleep-alt/` (Deep Sleep Reset): all 12 workout nights
  covered, a deeper 12-routine sequence from the same `sleep/` library
  (progressive muscle relaxation, journaling, circadian light exposure,
  etc. — routines prog-sleep's shorter program doesn't reach).

Both weight-alt/fitness-alt and cooking/cooking-alt and sleep/sleep-alt
were added 2026-08, closing out the "-alt programs fall back to the
generic video-details screen" gap for those three goals.

## Consumed by

[Dhana005/Watch2Do](https://github.com/Dhana005/Watch2Do) — `src/data/programContent.ts`
(renamed from `exerciseLibrary.ts` 2026-08 — it covers yoga/meditation/
learning/coding too, not just exercises) is a generated, bundled copy of
this repo's data fields (so the app works offline and without an extra
network round-trip on every program screen); only real media is fetched
live from elsewhere: `exercises/`/`yoga/`/`meditation/`'s `0.jpg`/`1.jpg`
reference photos from this repo's raw URLs, and any `videoId` (meditation,
learning) from YouTube directly via the app's `VideoEmbed` component —
`coding/` has neither yet. This repo is the source of truth — when
curating a new program, author/derive the data here first, generate the
matching copy in `programContent.ts`, and keep both in sync going forward.
