# First-Class Lifting Tracking Experience

## Scope

This document defines the product behavior and feature set for a social app with a best-in-class lifting tracker at its core. It focuses on what the tracking experience should feel like to the lifter, what flows must exist, and what product decisions make logging faster, smarter, and more satisfying than typical weight-training apps.

It intentionally does not cover backend architecture, storage models, sync strategy, or implementation details. Those can be designed after the product surface is clear.

## Product Goal

Create the lifting equivalent of Strava, with social identity and workout sharing as the overall product frame and tracking as a truly first-class feature rather than a chore. A lifter should want to use the app in the middle of a workout because it helps them move faster, remember what they did last time, and stay locked in.

The product should feel:

- Fast enough to use between sets without breaking focus.
- Smart enough to reduce repetitive input.
- Flexible enough for real-world lifting behavior.
- Personal enough to adapt to the user's routines.
- Serious enough that experienced lifters trust it.
- Social-native enough that sharing progress, identity, and follow behavior feel like natural extensions rather than a second product bolted on later.

## Product Boundary

Loadstar is not a trainer, coaching product, class-based training product, or prescriptive workout app.

It should not:

- tell users what workout to do
- recommend exercises as programming advice
- behave like Peloton, a digital coach, or a guided training product

It should:

- help users log what they actually did
- help users recall what they previously did
- make workout history easy to revisit, share, and present socially

Any intelligence in the tracking flow should be framed as recall, autofill, ordering, and convenience based on the user's own history, not as advice about what they should do next in the gym.

## App Shell And Navigation

The product should present as a modern consumer social app from day one, even while MVP0 is primarily a tracking release.

Recommended bottom navigation for the main app shell:

- `Home`
- `Explore`
- `Lift`
- `History`
- `Profile`

Rationale:

- `Home` is the app front door and the future social/feed entry point.
- `Explore` is the future discovery surface for people, workout posts, and related community entities.
- `Lift` is the center-tab primary action and the main workout entry point.
- `History` is the user's private archive of previous lifts and progression.
- `Profile` is the identity surface that later expands into full social presentation.

Important information architecture rule:

- `History` should sit to the right of `Lift`, adjacent to `Profile`, because it is conceptually part of the user's personal archive rather than a discovery surface.

MVP0 interpretation:

- `Home`, `Lift`, `History`, and `Profile` should feel real in MVP0.
- `Home` and `Explore` can ship as explicit `Coming Soon` surfaces in MVP0 if needed, as long as the shell establishes the correct future product shape.

## Core Product Thesis

Most lifting apps feel tedious because they treat logging as form entry. The user is forced to navigate large exercise lists, re-enter repeated numbers, and conform to rigid workout templates that break as soon as the workout changes.

This product should treat logging as assisted sequence capture:

- The user records what actually happened, one set at a time.
- The app remembers what the user did recently.
- The default values are almost always correct or close.
- Repeating prior actions is easier than typing new ones.
- Customization is available without making the main flow complex.

The ideal outcome is that a user can log an entire session with a small number of taps and only occasional edits.

This is important because "Strava for lifters" is not just a feature roadmap. It is a product posture:

- tracking must be first-class
- identity and progress must feel worth returning to
- the app should feel like a modern consumer social product, not a niche spreadsheet for the gym

## Design Principles

### 1. Set-First, Not Exercise-Plan-First

The fundamental unit of tracking is the completed set, not a predeclared exercise prescription like `4 x 12`.

Why:

- Real sessions drift.
- Rep counts vary across sets.
- Warmups, drop sets, top sets, backoff sets, rest-pause sets, and failure sets do not fit cleanly into simple templates.
- Users should log what they did, not what they intended.

Implications:

- Users add sets individually.
- Repeating the previous set should be the fastest action in the product.
- Exercise summaries should be generated from logged sets, not required up front.

### 2. Defaults Should Do Most of the Work

The app should aggressively prefill likely values:

- Same exercise as earlier in the session.
- Same weight/reps as the prior set.
- Same first working set as the previous relevant session.
- Same exercise order as similar past sessions.

Typing should be the fallback, not the primary action.

### 3. Logging Must Preserve Workout Momentum

Every interaction should respect the fact that the user is between sets, possibly fatigued, sweaty, distracted, or in a crowded gym.

This means:

- Few taps.
- Large hit targets.
- Clear next action.
- Strong offline/instant responsiveness.
- Minimal context switching.

### 4. History Should Power Logging, Not Programming

Many users do not follow a perfectly static plan. The app should help them log and recall their own behavior without turning that history into workout prescription.

The product should not require routines, templates, or plans. It should work well for lifters who simply open the app and record what they did.

### 5. Advanced Lifters Must Not Feel Constrained

The app can simplify the common case without dumbing down the product.

It should support:

- Weighted bodyweight work.
- Warmups and working sets.
- Unilateral movements.
- Machines with ambiguous naming.
- Custom exercises.
- Notes and nuance when needed.

## User Jobs To Be Done

### Primary Job

"Help me log my workout accurately with almost no friction."

### Secondary Jobs

- "Show me what I did last time so I can progress."
- "Let me adapt mid-workout without fighting the app."
- "Make bodyweight and weighted variants easy to track."
- "Let me track niche exercises without waiting for the app to support them."
- "Make completed logging feel satisfying and useful, not administrative."

## Success Criteria

The tracking experience is succeeding when:

- Most sets can be logged in one tap or one tap plus a minor adjustment.
- Users rarely need to search deeply for exercises.
- Users frequently reuse recalled defaults from their own history.
- Session completion time in the app is short relative to workout duration.
- Returning users trust the app as the source of truth for prior performance.
- Power users choose it over notes apps, spreadsheets, or incumbent trackers.

## Core Tracking Model

### Session

A workout session is a time-bounded collection of logged sets, notes, and metadata.

### Exercise Block

A session is visually grouped by exercise, but the exercise block is a presentation layer over a set stream. Users are not declaring a rigid set count up front.

### Set

Each set should support, at minimum:

- Exercise
- Reps
- Load
- Completion state
- Optional set type
- Optional note

Suggested optional set types:

- Warmup
- Working
- Top set
- Backoff
- Drop set
- Failure

These should not be required in the main flow.

## Core Experience Requirements

### 1. Instant Session Start

Starting a workout should be lightweight.

Preferred entry points:

- Start empty session.
- Resume in-progress session.
- Repeat a similar past session.

The user should not have to fully configure the workout before lifting.

Navigation implication:

- tapping the `Lift` tab with no active session should open the session-start surface
- tapping the `Lift` tab with an active session should return directly to that in-progress workout

### 2. Add Exercise Must Be Smart

Exercise search should feel more like a predictive command bar than a static catalog.

Ranking signals for autocomplete should include:

- Exact text match.
- Exercises done recently.
- Exercises done frequently.
- Exercises commonly paired with already-logged exercises in this session.
- Exercises from the most similar past session.

Suggested sections in the add-exercise UI:

- Recent
- In this session
- From similar past sessions
- Search results
- Create custom exercise

The main point is that the user should almost always see the likely exercise before fully typing it.

### 3. Repeating A Set Should Be The Fastest Action

Once an exercise exists in the session, the default action should be to add another set with inherited values.

Expected behavior:

- Tap `Repeat Set` to duplicate previous reps/load instantly.
- Then optionally adjust reps or load.
- The repeated set should appear immediately in the exercise block.

This is the highest-leverage interaction in the product.

### 4. Inheritance Rules Must Feel Obvious

Default value inheritance should follow a clear hierarchy:

For a new set on an exercise in the current session:

- Inherit from the previous set on that exercise.

For the first set of an exercise in a new session:

- Inherit from the first relevant set of the most recent prior session where that exercise was performed.

If there are multiple useful anchors, the app can surface alternatives, but the default should remain simple and predictable.

Possible future refinement:

- Let advanced users choose whether first-set inheritance prefers:
  - Last session first set
  - Last working set
  - Best set from last session

### 5. Weight And Reps Editing Must Be Frictionless

Editing reps and load should not feel like spreadsheet entry.

Recommended interaction model:

- Large tappable reps and load chips/cards.
- Single tap opens quick adjust controls.
- `+` and `-` buttons for common increments.
- Numeric keypad for direct entry when needed.
- One-tap standard plate jumps for barbell lifts.
- One-tap small jumps for dumbbells/machines.

The common action is not "type a number from scratch." The common action is "accept or nudge."

### 6. Bodyweight Tracking Must Feel Native

Bodyweight movements should not force users into awkward zero-load logging.

Exercises should support an `is_bodyweight` concept at the product level. For these exercises:

- The default presentation should emphasize reps first.
- The load UI should communicate bodyweight clearly.
- Weighted and assisted versions should remain part of the same exercise family.
- Users should be able to log:
  - Bodyweight only
  - Weighted sets
  - Assisted sets

Examples:

- Pull-up: `BW x 8`
- Weighted pull-up: `BW + 25 x 6`
- Assisted pull-up: `BW assisted 40 x 10`

The UI should make this feel intentional, not like a hack around a zero value or a negative number.

Recommended product model:

- Exercise-level flag: `is_bodyweight = true`
- Set-level load mode:
  - `bodyweight`
  - `weighted`
  - `assisted`
- Optional external load value on the set

Recommended interaction:

- Use a small mode picker or segmented control for `BW`, `Weighted`, and `Assisted`.
- Do not force users to represent assistance as negative weight.
- Keep pull-up, weighted pull-up, and assisted pull-up history unified under the same base exercise unless the movement itself is meaningfully different.

This matters because assisted work should feel legitimate and first-class, not like a lesser version of the movement.

### 7. Custom Exercises Must Be First-Class

If an exercise is missing, the user must be able to create it immediately without leaving the workout flow.

Requirements:

- Create custom exercise inline from search.
- Minimal required fields.
- Immediate usability in the session.
- Remember it permanently for that user.

Recommended initial metadata:

- Name
- Equipment type
- Bodyweight flag
- Optional muscle group tags

Important principle:

- Custom should not mean second-class.
- Custom exercises should support all normal logging behavior, history, quick access, and progression views.

Longer-term product behavior:

- Detect frequently repeated custom names across users.
- Normalize and promote common customs into canonical exercises.
- Preserve user history and mappings when that happens.

## Smart Recall Features

These are the features that can make the tracker feel truly special rather than merely usable, without turning it into a trainer.

### 1. Fast Recall Of Recent Or Similar Exercises

After the user logs several exercises, the app should make it easy to pull in exercises from the current session or from similar past sessions.

Signals:

- recent sessions
- similarity to previous workouts
- exercises already logged in the current session
- exercises commonly co-occurring in the user's own history

The UX should be assistive, not prescriptive:

- surface quick-add shortcuts based on the user's own history
- allow one-tap accept
- never imply that the app is telling the user what workout to do

### 2. Detect Session Similarity Without Formal Split Setup

Users should not need to declare "today is push day" for the app to organize history intelligently.

Instead, infer session similarity from:

- exercise composition
- exercise order
- historical recurrence patterns
- day/time habits

This should help the product describe and organize prior workouts, not prescribe a new one.

### 3. Pre-Fill From Prior History

As a user logs, the app can prepare quick-add shortcuts and prefilled defaults based on similar prior sessions.

This should surface as:

- recent exercises
- exercises already used in the current session
- quick-add buttons with prefilled starting weights/reps
- a lightweight "repeat last version of this session" option

It should not require the user to manage explicit templates.

### 4. Smart Rest And Repeat Cues

Optional but high value:

- Rest timer auto-start after a set is logged.
- Suggested repeat button remains prominent during rest.
- Reminder of previous set during countdown.

This keeps the app oriented around the real cadence of lifting.

### 5. In-Progress Session Persistence Should Feel Like An Active Recording

The mental model should be closer to Strava recording than to filling out a form.

This means:

- once a workout is started, it is treated as an active session until explicitly finished or discarded
- if the user backgrounds the app mid-lift and later reopens it, they should land back in the in-progress workout
- every meaningful workout action should persist locally immediately
- the user should not be asked to manually recover a workout after ordinary app closes or reopens
- recovery prompts should be reserved for exceptional states such as corruption or rare sync conflicts

Product consequence:

- `Lift` is not just a place to start a workout
- `Lift` is the durable home of the current live session, and reopening the app should feel like resuming an active recording

### 5. Contextual Performance Recall

Before a set is confirmed, the app should remind the user what happened last time in a compact way:

- Last session: `55 x 10, 55 x 9, 55 x 8`
- Best recent set
- Target to beat

This is a major motivator and reduces mental bookkeeping.

## Detailed UX Rules

### Session Screen Priorities

The active workout screen should optimize for four actions only:

- Log the next set.
- Repeat the previous set.
- Adjust reps/load quickly.
- Add or switch exercises quickly.

Anything else should be visually secondary.

The active workout screen should also be the default landing surface when an in-progress workout exists, whether the user reached it from app launch, the `Lift` tab, or a resume affordance elsewhere in the app.

### Exercise Block Design

Each exercise block should make these items immediately visible:

- Exercise name
- Last session reference
- Logged sets in this session
- Primary action to add the next set

Useful actions within the block:

- Repeat last set
- Edit a specific set
- Mark warmup or special set type
- Add note
- Finish or collapse exercise

### Keyboard Use Should Be Optional

If a user wants direct numeric entry, it should be available. But the default flow should work mostly through taps.

### Mistakes Should Be Cheap

The user should be able to:

- Undo a just-logged set quickly.
- Edit reps/load inline.
- Reorder or delete sets when needed.
- Swap an exercise name if they selected the wrong movement.

Logging should feel forgiving.

### Low-Cognitive-Load Visual Language

The UI should clearly distinguish:

- What came from history
- What was logged
- What changed from last time
- What action is primary right now

The user should not need to interpret dense tables during a workout.

## Feature Set By Priority

### Must-Have For V1

- Start session instantly.
- Add exercises with ranked autocomplete.
- Create custom exercises inline.
- Log sets one by one.
- One-tap repeat set.
- Inherit reps/load from prior set.
- Inherit first set defaults from prior session for that exercise.
- Native bodyweight exercise support.
- Quick reps/load adjustment UI.
- Show previous session reference for each exercise.
- Edit/delete/undo sets.
- Fast quick-add from recent or similar session history.

### Strong V1.5 Features

- Inferred session-type labels for organization and recall.
- Repeat a similar past workout.
- Rest timer integrated into logging flow.
- Lightweight set tags like warmup/backoff/failure.
- Target-to-beat recall based on recent performance.
- Smarter ordering of quick-add exercise shortcuts based on user history.

### Later Enhancements

- Superset/circuit grouping.
- Exercise aliases and normalization across naming variants.
- Voice logging or watch interactions.
- Camera/computer-vision assist for plates or reps.
- Auto-detection from gym hardware or wearables.

## Important Product Decisions

### Decision 1: Do Not Require Planned Set Counts

Recommendation:

- Do not make users enter planned sets as the primary model.

Why:

- It creates friction before the workout.
- It poorly reflects actual training behavior.
- It makes deviations feel like failure instead of reality.

If planned sets exist later, they should be optional scaffolding over a set-first tracker.

### Decision 2: Keep Exercise Metadata Lightweight

Recommendation:

- Avoid bloated exercise setup.

Only require enough metadata to make tracking work well:

- Name
- Equipment type
- Bodyweight flag
- Optional tags

Too much structure at exercise creation time will hurt the core use case.

### Decision 3: Optimize For One-Handed, Mid-Workout Use

Recommendation:

- Treat the workout screen like a high-frequency action surface, not a database form.

This means:

- Large controls
- Minimal modal depth
- Few required fields
- Clear next-step emphasis

### Decision 4: Treat `Lift` As A Persistent Mode, Not A Disposable Screen

Recommendation:

- the center tab should always represent the current workout state

Why:

- it matches the user's mental model of "I am in a workout" rather than "I opened a logging page"
- it aligns with the product posture of tracking as an active session, similar to recording in Strava
- it makes mid-workout app closes and reopens feel safe and predictable

Expected behavior:

- no active session: `Lift` opens session start options
- active session: `Lift` opens directly back into the live workout

### Decision 5: Recall Should Be Personalized, Not Prescriptive

Recommendation:

- Derive intelligence from the user's own session history first.

Users will trust history-based recall that reflects their actual habits more than anything that feels like generic fitness-program advice.

## Edge Cases The Product Must Handle Well

- Rep drop-off across sets.
- Weight changes between sets.
- Warmups before working sets.
- Dropping weight for backoff sets.
- Switching exercise order mid-workout.
- Doing fewer or more sets than usual.
- Logging unilateral exercises.
- Machines with different brand-specific names.
- Bodyweight movements with added or assisted load.
- Niche/custom exercises.
- Revisiting an exercise later in the same session.

If these cases feel clumsy, lifters will quickly conclude the app is not built by people who understand training.

## Product Risks

### Risk 1: Over-Automation Feels Creepy Or Wrong

Mitigation:

- Keep history-based shortcuts clearly framed as convenience, not advice.
- Make manual override immediate.
- Favor high-confidence, obvious defaults over aggressive auto-logging.

### Risk 2: Power Features Pollute The Main Flow

Mitigation:

- Keep the default workflow extremely simple.
- Tuck advanced options behind secondary actions.
- Only surface complexity when it is contextually relevant.

### Risk 3: Exercise Taxonomy Becomes A Mess

Mitigation:

- Let users create custom exercises easily.
- Invest later in aliasing, normalization, and promotion to canonical exercises.
- Do not block logging because taxonomy is incomplete.

## What Makes This Feel Special

The app becomes special if the user experiences the following pattern repeatedly:

1. They start a session quickly.
2. The app remembers what they recently did.
3. Logging the next set takes one tap most of the time.
4. The app remembers prior performance without making them dig for it.
5. Custom or unusual cases do not break the flow.
6. The app adapts to their training habits over time.

That combination makes the tracker feel like a serious memory and recording tool rather than an obligation.

## Recommended Product Statement

The product should be built around this standard:

"A serious lifter should be able to log a real workout, with all its messiness and variation, faster than they could type it into notes."

If a feature helps that, it belongs near the core. If it adds setup, ceremony, or form-filling, it should be treated with suspicion.

## Suggested Next Doc

After aligning on the product behavior in this document, the next design document should cover:

- Data model for sessions, exercises, and sets
- Suggestion and ranking architecture
- Custom exercise normalization strategy
- Offline and sync behavior
- Feed/social integration points for sharing workouts
