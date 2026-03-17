# MVP0 Tracking UX/UI Design

## Purpose

This document translates the tracking philosophy in [first-class-lifting-tracking-experience.md](/Users/sam/loadstar/docs/first-class-lifting-tracking-experience.md) into a concrete MVP0 product surface.

MVP0 ships only the tracking experience. Social profiles, feeds, comments, messaging, and workout sharing are explicitly deferred to MVP1.

The design goal is simple:

- a serious lifter can log a real session between sets with almost no typing
- the app feels faster than notes, spreadsheets, and incumbent lifting trackers
- the interface looks focused, athletic, and intentional rather than playful or generic

## MVP0 Scope

### In Scope

- Account onboarding only as needed to persist data across devices later
- Home/start-workout surface
- Active workout logging flow
- Exercise search and ranked suggestions
- Inline custom exercise creation
- One-tap repeat set
- Quick reps/load adjustment
- Bodyweight, weighted, and assisted logging
- Previous session recall
- Suggested next exercises
- Edit, undo, delete, reorder, and swap exercise actions
- Session history and session detail
- Exercise history and recent performance views
- Settings required for units and basic profile preferences

### Explicitly Out of Scope for MVP0

- social graph
- public profiles
- posts, likes, comments, messaging
- coach-athlete collaboration
- workout feed or workout sharing
- advanced program builder
- media-heavy content creation

## Product Positioning

The product should feel like a premium training instrument.

It should not resemble:

- a calorie tracker
- a generic wellness app
- a spreadsheet with rounded corners
- a template-driven bodybuilding app with rigid forms

It should resemble:

- a fast field notebook for strength training
- a performance dashboard tuned for repeated mid-workout use
- a serious tool that rewards consistency and progression

## UX Principles

### 1. One Primary Action Per Moment

Every screen should make the next action obvious:

- home: start or resume workout
- workout: log next set
- exercise picker: choose the likely exercise
- set editor: accept or nudge reps/load
- history: recall last performance fast

### 2. Tap First, Keyboard Second

Default flows should rely on:

- large chips
- segmented controls
- plus/minus steppers
- recent-value shortcuts
- one-tap repeat

The keyboard is a fallback for unusual cases.

### 3. Suggestions Must Be Visible but Never Controlling

Suggested exercises, prefilled loads, and "looks like your usual push session" cues should help the user move faster without making the app feel presumptuous.

### 4. Dense Information, Low Cognitive Load

The app should surface:

- what you did last time
- what you just did
- what probably comes next

It should not force the user to parse dense tables or tiny text while resting.

### 5. Sweat-Tested Controls

Controls should assume:

- one-handed use
- poor lighting
- partial attention
- quick glances
- thumb reach priority

## Information Architecture

MVP0 should use five primary tabs.

1. Today
2. History
3. Exercises
4. Insights
5. Settings

### Today

Primary destination. Shows:

- resume current workout if one exists
- start empty session
- repeat similar recent session
- suggested routine based on history
- recent workouts

### History

Chronological workout log with filters:

- date
- duration
- session type label if inferred
- top exercises

### Exercises

Searchable exercise directory for the user, combining:

- canonical exercises
- custom exercises
- recent exercises
- favorites later if needed

### Insights

MVP0 should keep this lightweight:

- streak or consistency summary
- recent volume trends
- recently improved lifts

This tab is secondary and should not consume core product energy.

### Settings

- units
- default plate increments
- bodyweight display preferences
- account and sync status

## Core Screens

## 1. Home / Today

### Job

Get the user into the workout with no ceremony.

### Content Hierarchy

1. `Resume Workout` card when applicable
2. `Start Workout` primary button
3. `Repeat Recent Session` carousel
4. `Suggested For Today` session suggestions
5. recent activity summary

### Interaction Notes

- If a workout is in progress, `Resume Workout` dominates the screen.
- Starting empty should be one tap.
- Suggested sessions should show only enough detail to trigger recognition:
  - session nickname or inferred label
  - top exercises
  - last completed date

## 2. Active Workout

This is the core screen of the product.

### Layout Structure

- top bar: elapsed time, session title, finish action
- sticky next-action area
- scrollable list of exercise blocks
- bottom anchored `Add Exercise` and quick action surface

### Sticky Next-Action Area

Should include:

- likely next exercise chips
- current rest timer if active
- a compact "last logged set" summary

### Exercise Block Anatomy

Each block shows:

- exercise name
- last session reference line
- current session sets as large row cards
- primary CTA: `Repeat Set`
- secondary actions: edit, note, set type, more

### Set Row Design

A set row should make these fields scannable:

- set index
- load presentation
- reps
- set type if present
- completion state

Recommended row example:

- `3   185 lb   x 6   Working`
- `2   BW + 25   x 5`
- `1   BW assisted 40   x 10`

### Reps / Load Editing

Tap a set or `Repeat Set` to open a bottom sheet with:

- large reps control
- large load control
- fast increment buttons
- direct keypad option
- bodyweight mode picker when applicable
- save, undo, delete

### Bodyweight Interaction

For bodyweight-family exercises, the editor should expose:

- segmented control: `BW` `Weighted` `Assisted`
- reps control first
- external load control second

Presentation examples:

- `BW x 8`
- `BW + 25 x 6`
- `BW assisted 40 x 10`

### Add Exercise Flow

Tapping `Add Exercise` opens a command-bar style panel with sections:

- Suggested next
- Recent
- Usual for this workout type
- Search results
- Create custom exercise

### Forgiveness Controls

Active workout must support:

- undo toast after adding a set
- inline set edit
- delete set
- reorder sets
- move back to a previous exercise later in the session
- swap exercise if the wrong one was selected

## 3. Add Exercise / Search

### Job

Make the likely exercise visible before the user finishes typing.

### UX Rules

- search starts focused
- results update instantly
- recent and suggested items remain visible when query is short
- custom exercise creation is always present if no exact match exists

### Result Card Contents

- exercise name
- equipment type
- small recent-history hint such as `last: 185 x 6`
- indicator if it is custom

## 4. Create Custom Exercise

This should be a compact modal or sheet, not a full-screen flow.

### Fields

- name
- equipment type
- bodyweight yes/no
- optional muscle tags

### Rules

- no mandatory taxonomy ceremony
- immediate availability in current workout
- identical logging behavior to standard exercises

## 5. Session Complete

### Job

Close the workout cleanly and reinforce value.

### Contents

- session duration
- exercises completed
- set count
- total volume where relevant
- notable progress callout
- optional short notes

### Actions

- done
- edit session metadata
- duplicate structure for next time

No social sharing actions appear in MVP0.

## 6. History

### List View

Each workout card should show:

- date
- inferred session label if available
- duration
- key exercises
- total sets

### Session Detail

Should mirror the active workout structure but read-only by default, with edit access.

## 7. Exercise Detail

### Purpose

Give lifters quick progression context without opening spreadsheets.

### Sections

- latest session summary
- recent performance list
- best recent sets
- recent volume trend
- all-time history link later if needed

MVP0 should keep charts simple and secondary.

## Visual Design Direction

## Brand Personality

- disciplined
- tactile
- confident
- high-performance
- minimal but not sterile

## Color System

Avoid generic wellness palettes and avoid purple-heavy startup styling.

Recommended foundation:

- background: warm near-black or graphite
- primary surface: charcoal / gunmetal
- accent: oxidized lime, training red, or electric amber used sparingly
- success: muted green
- warning: burnt amber
- destructive: iron red

Suggested token direction:

- `bg-primary`: `#111315`
- `bg-surface`: `#181C1F`
- `bg-elevated`: `#21272B`
- `text-primary`: `#F3F4F2`
- `text-secondary`: `#A8B0B5`
- `accent-primary`: `#C7FF4A`
- `accent-secondary`: `#FF6B3D`
- `border-subtle`: `rgba(255,255,255,0.08)`

## Typography

Use a type system with stronger character than standard SaaS defaults.

Recommended direction:

- condensed or athletic display face for headings and large numeric stats
- highly legible grotesk or neo-grotesk for UI labels and controls
- tabular numerals for set rows, timers, and performance values

Typography goals:

- weights and reps should be visually authoritative
- timers and active controls should read instantly
- small metadata should stay quiet

## Shape and Density

- medium corner radius, not overly soft
- strong card separation through depth and border contrast
- dense enough for serious logging, but with oversized tap targets

## Motion

Motion should reinforce workout rhythm.

- set log confirmation: quick snap-in
- rest timer start: subtle pulse, not flashy
- suggested next exercise chips: soft slide/fade
- completed set row: micro flash to confirm capture

Animations should feel crisp and industrial, not whimsical.

## Responsive Behavior

### Mobile

Primary target. The app should be optimized for single-hand operation.

### Tablet

Can use a dual-pane history or exercise detail layout, but the active workout should still preserve large central controls.

### Desktop Web

Useful for reviewing history, editing, and analytics. Active workout can remain centered and phone-like rather than expanding into a dense table.

## Key User Flows

## Flow 1: Start Empty and Log First Set

1. user lands on Today
2. taps `Start Workout`
3. active workout opens immediately
4. taps `Add Exercise`
5. selects suggested or searched exercise
6. first-set defaults are prefilled from most recent relevant session
7. user accepts or nudges values
8. set appears instantly and rest timer optionally starts

## Flow 2: Repeat Set

1. user returns to the same exercise block
2. taps `Repeat Set`
3. identical set is logged immediately or opens with tiny confirmation affordance depending on final implementation
4. user optionally edits reps or load inline

## Flow 3: Bodyweight Exercise

1. user adds pull-up
2. sheet defaults to `BW`
3. user logs `BW x 8`
4. on next set, user switches to `Weighted`
5. enters `+25`
6. history remains unified under pull-up family

## Flow 4: Missing Exercise

1. user searches
2. no suitable match
3. taps `Create Custom Exercise`
4. enters minimal metadata
5. custom exercise becomes available immediately
6. user logs set without leaving workout context

## Flow 5: Review Prior Performance

1. user opens an exercise block
2. sees compact line such as `Last: 185 x 6, 185 x 5, 175 x 8`
3. taps reference line if deeper detail is needed
4. bottom sheet or exercise detail shows recent sessions and best set

## AI Mockup Prompt Pack

These prompts are intended for image-generation or UI-concept tools. They are written to generate high-fidelity mobile-product mockups with a consistent visual language.

### Global Prompt Addendum

Append this guidance to every prompt unless a tool requires shorter text:

`Design a premium mobile fitness app UI for serious lifters. Tracking-first, no social feed, no likes, no comments. Dark graphite background, high-contrast typography, crisp athletic interface, industrial but refined, large thumb-friendly controls, tabular numerals, strong card hierarchy, premium product-shot lighting, realistic iPhone Pro frame, highly legible text, believable product design, not a generic Dribbble shot, not playful, not neon cyberpunk, not purple-heavy, not glassmorphism.`

### Prompt 1: Today / Start Workout

`Create an iPhone app mockup for a lifting tracker home screen called Loadstar. Show a "Today" tab with a dominant Resume Workout or Start Workout card, a row of suggested recent sessions, and a compact recent activity summary. Visual style is premium athletic software, dark graphite surfaces, off-white text, lime accent used sparingly, condensed display type for key headings, clean neo-grotesk UI labels, bold hierarchy, large rounded-rectangle cards, subtle depth, realistic spacing, designed for one-handed gym use between sets. No social feed, no avatars grid, no community elements.`

### Prompt 2: Active Workout Core Screen

`Create a high-fidelity mobile app mockup of the core workout logging screen for a serious lifting app. Show an in-progress session with multiple exercise blocks such as Bench Press, Incline Dumbbell Press, and Pull-Up. Each block includes a last-session reference line, large set rows with load and reps, and a bold one-tap "Repeat Set" action. Include a sticky strip for suggested next exercises and a small rest timer. Interface should feel extremely fast, focused, and built for use mid-workout with sweaty hands. Use dark graphite surfaces, crisp off-white text, muted grey metadata, lime accent for primary actions, tabular numerals, sharp premium styling, realistic iPhone hardware frame.`

### Prompt 3: Set Edit Bottom Sheet

`Design a premium iOS bottom-sheet interaction for editing a logged lifting set. The sheet should have oversized controls for reps and load, plus/minus steppers, a direct keypad option, and a clean save action. Make the numbers visually dominant and easy to adjust with a thumb. Show the background dimmed behind an active workout screen. Style should be industrial, athletic, polished, and serious, with dark UI, high contrast, restrained accent color, and no generic fintech or wellness styling.`

### Prompt 4: Add Exercise Search

`Create a mobile app search panel for adding an exercise during a workout. The panel behaves like a predictive command bar and includes sections labeled Suggested Next, Recent, Usual For This Workout, Search Results, and Create Custom Exercise. The user has typed part of an exercise name, and the likely result appears immediately. Show concise metadata like last used weight or custom badge. The design should feel fast and intelligent, with strong typography, dark surfaces, subtle separators, premium athletic aesthetic, and no unnecessary illustrations.`

### Prompt 5: Bodyweight Logging

`Create an iPhone mockup of a workout set editor for a bodyweight exercise such as Pull-Up. Show a segmented control with BW, Weighted, and Assisted modes. Reps are the primary control, with external load shown second. Include examples like BW x 8 and BW + 25 x 6 in the interface. The design should make bodyweight logging feel native and intentional, not hacked around numeric zero values. Use premium dark UI, crisp typography, serious lifting aesthetic, lime or amber accent, and realistic product-level polish.`

### Prompt 6: Custom Exercise Creation

`Design a compact mobile modal for creating a custom exercise inside a workout flow. Include only minimal fields: name, equipment type, bodyweight toggle, optional muscle tags. The design should communicate speed and low ceremony, with the new exercise available instantly. Use a serious, premium, dark athletic design language with large touch targets, clean text fields, restrained accent color, and realistic iOS styling.`

### Prompt 7: Session Complete Summary

`Create a polished mobile session-complete screen for a lifting tracker. Show workout duration, exercises completed, total sets, total volume, and one notable progress highlight. The design should feel rewarding but restrained, like a premium performance tool rather than a gamified app. Dark graphite background, powerful typography, clean stat cards, a single primary Done button, no social share actions, no confetti, no childish celebration.`

### Prompt 8: Exercise Detail / Progress Recall

`Create a mobile exercise detail screen for Bench Press in a serious lifting tracker. Show latest session performance, best recent set, a compact trend chart or list, and recent workout entries. Use dominant numeric hierarchy, tabular numerals, dark premium UI, subtle chart styling, and a focused performance-oriented aesthetic. The screen should look useful to an experienced lifter, not like a beginner fitness app.`

## Negative Prompt Guidance

Use these exclusions when the tool supports negative prompting:

- social feed
- comments
- influencers
- bodybuilding poster aesthetic
- cartoon icons
- bright gradients
- pastel wellness colors
- purple startup UI
- glassmorphism
- futuristic sci-fi HUD
- cluttered dashboard
- tiny text
- generic smartwatch fitness screen

## Open UX Questions

- Should `Repeat Set` log instantly or open a micro-confirmation state when accidental taps are common?
- Should active workout default to manual finish, or auto-suggest finishing after inactivity?
- Should set-type tags stay hidden by default until a user uses them once?
- Should exercise history open as a bottom sheet or a dedicated screen from the workout flow?

## Recommendation

For MVP0, optimize the product around one screen above all others: the active workout screen.

If tradeoffs are required, prioritize:

1. faster set logging
2. better next-exercise suggestions
3. clearer previous-session recall
4. smoother bodyweight and custom exercise handling

Everything else is secondary.
