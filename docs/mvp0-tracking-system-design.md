# MVP0 Tracking System Design

## Purpose

This document defines a practical system design for shipping MVP0 of Loadstar as a social app with a tracking-first lifting core.

It is informed by:

- [first-class-lifting-tracking-experience.md](/Users/sam/loadstar/docs/first-class-lifting-tracking-experience.md)
- [social-media-system-design.md](/Users/sam/loadstar/docs/social-media-system-design.md)

Key product decision:

- MVP0 ships the tracking core inside a socially native app shell
- the social system is deferred to MVP1
- the architecture should preserve a clean future integration boundary for workout sharing and profiles without forcing that complexity into MVP0
- MVP0 should still preserve a socially native product foundation in identity, shareable summaries, and profile-ready data
- the product is not a trainer, coaching product, or prescriptive workout app

## Product Shell Assumption

MVP0 should live inside a 5-tab mobile app shell even though the full social system is deferred.

Recommended bottom navigation:

- `Home`
- `Explore`
- `Lift`
- `History`
- `Profile`

Interpretation for MVP0:

- `Lift` is the center-tab primary action and the durable home of any in-progress workout
- `History` is the archive for previous lifts and exercise recall
- `Profile` remains intentionally lightweight in MVP0 but must exist as the identity surface
- `Home` and `Explore` can remain explicit `Coming Soon` surfaces in MVP0 as long as the shell establishes the correct future product shape

Important IA rule:

- `History` belongs to the right of `Lift`, near `Profile`, because it is part of the user's personal record rather than a discovery surface

## System Goals

- make logging feel instant
- work reliably offline in the gym
- preserve user trust with durable workout history
- support fast recall and autofill from the user's own data
- keep the architecture simple enough for an MVP team
- avoid coupling tracking internals to future social features
- keep workout records and summaries structured so they can become compelling profile and feed artifacts later

## Non-Goals for MVP0

- social feed, posts, comments, likes, messaging
- coach collaboration
- advanced ML infrastructure
- wearable ingestion
- computer vision or voice logging
- complex multi-tenant analytics platform

## Product Requirements Mapped to System Requirements

### Product Requirement: One-Tap Logging

System implication:

- local writes must commit immediately on-device
- UI should never wait on network round trips to confirm a set

### Product Requirement: Resume In-Progress Workout On Reopen

System implication:

- the current active session must always be recoverable from local storage on app relaunch
- the `Lift` tab must resolve to the active workout when one exists
- ordinary app backgrounding or closing should not require an explicit recovery flow

### Product Requirement: Previous Session Recall

System implication:

- recent exercise history must be queryable with low latency from local storage
- denormalized read models are acceptable where they simplify core screens

### Product Requirement: Fast Recall Without Prescription

System implication:

- maintain lightweight local ranking and retrieval features derived from session history
- use history to power quick access and autofill, not exercise recommendation

### Product Requirement: Custom Exercises

System implication:

- user-defined exercises must behave identically to canonical exercises in storage and queries
- no workflow should depend on centralized exercise taxonomy approval

### Product Requirement: Bodyweight / Weighted / Assisted Support

System implication:

- set schema must represent load mode explicitly
- bodyweight should not be encoded as magic numeric values

### Product Requirement: Offline Reliability

System implication:

- local-first persistence
- sync queue
- idempotent mutation protocol
- conflict strategy that is understandable and conservative

## Recommended Architecture

MVP0 should ship as a modular monolith with a local-first client.

### Client

- iOS-first app, or cross-platform mobile client if the team prefers
- local database for sessions, sets, exercises, and read models
- sync engine for background upload and reconciliation
- recall engine for immediate local ranking and autofill

### Backend

- single application service exposing auth, tracking APIs, and sync endpoints
- relational primary database
- object storage only if attachments are added later
- async job worker for derived summaries and recall feature updates

### Why This Shape

- fastest path to shipping
- low operational complexity
- clear future seam to split services if growth requires it
- strong support for offline-first UX

## High-Level Components

## Client-Side Components

### 1. Local Store

Stores:

- user profile basics
- exercise catalog
- custom exercises
- sessions
- exercise blocks or ordering metadata
- sets
- notes
- derived history summaries
- sync operation log

Recommended implementation:

- SQLite-backed mobile database
- repository layer for domain queries

### 2. Workout Domain Layer

Encapsulates:

- start session
- add exercise
- log set
- repeat set
- edit set
- delete set
- reorder sets
- finish session

This layer should own business rules instead of scattering them across UI code.

### 3. Recall Engine

Provides:

- fast recall of recently used or historically related exercises
- first-set defaults
- recent-history snippets

For MVP0, most of this can run on-device from local history plus occasional server refresh.

### 4. Sync Engine

Responsibilities:

- queue mutations
- retry failed uploads
- fetch remote changes
- reconcile acknowledgements
- mark records as synced

### 5. Read Model Builder

Produces view-friendly projections such as:

- latest performance per exercise
- recent sessions list
- exercise summaries
- inferred session-type labels
- app-shell surfaces such as home resume state, history list items, and lightweight profile summaries

## Backend Components

### 1. Auth and Identity Module

Handles:

- account creation
- sign-in
- device session tokens

Keep profile scope small in MVP0.
But it should still create a clean path to later usernames, profile surfaces, avatars, and share permissions.

### 2. Tracking API Module

Owns:

- exercises
- sessions
- sets
- history queries
- sync endpoints

### 3. Recall/Derived Data Worker

Computes or refreshes:

- exercise frequency stats
- sequence-transition counts
- session similarity clusters
- denormalized summaries for fast reads

### 4. Future Integration Boundary

Expose stable workout identifiers and shareable summaries later, but do not implement social consumers in MVP0.

## Core Domain Model

## Main Entities

### User

- `id`
- `created_at`
- `unit_preference`
- `bodyweight_unit_preference`

### Exercise

- `id`
- `owner_user_id` nullable for global canonical exercises
- `name`
- `normalized_name`
- `equipment_type`
- `is_bodyweight`
- `muscle_tags` optional
- `status` active/archived
- `created_at`
- `updated_at`

### Session

- `id`
- `user_id`
- `started_at`
- `ended_at` nullable
- `timezone`
- `title` nullable
- `inferred_session_label` nullable
- `notes` nullable
- `status` in_progress/completed/deleted
- `client_created_at`
- `client_updated_at`
- `server_updated_at`

### Session Exercise

Presentation-level grouping for exercise order within a session.

- `id`
- `session_id`
- `exercise_id`
- `position`
- `collapsed` optional
- `created_at`

This is useful because exercise grouping is a UI concern over a set stream, but the UI still needs stable ordering and exercise-level actions.

### Set Entry

- `id`
- `session_id`
- `session_exercise_id`
- `exercise_id`
- `sequence_index`
- `performed_at`
- `reps`
- `load_mode` bodyweight/weighted/assisted/standard
- `external_load_value` nullable
- `load_unit`
- `set_type` nullable
- `note` nullable
- `is_completed`
- `is_deleted`
- `client_created_at`
- `client_updated_at`
- `server_updated_at`

### Exercise Stats Snapshot

Denormalized per user and exercise:

- `exercise_id`
- `user_id`
- `last_session_id`
- `last_session_started_at`
- `last_sets_summary`
- `best_recent_set_summary`
- `first_set_default`
- `transition_scores`

This can live as a table on the backend and a cached projection on the client.

## Load Modeling

Use explicit load mode representation.

Examples:

- bench press: `load_mode=standard`, `external_load_value=185`
- pull-up bodyweight: `load_mode=bodyweight`, `external_load_value=null`
- weighted pull-up: `load_mode=weighted`, `external_load_value=25`
- assisted pull-up: `load_mode=assisted`, `external_load_value=40`

Do not encode assistance as negative load.

## Sync Model

## Local-First Principles

- local mutation succeeds first
- network sync is asynchronous
- user sees immediate success state
- unsynced state is visible only when it matters

## Mutation Strategy

Each client write becomes:

1. local database transaction
2. appended sync operation with stable operation id
3. background upload to server
4. server acknowledgement
5. local sync status update

### Recommended Sync Operation Shape

- `operation_id`
- `entity_type`
- `entity_id`
- `mutation_type`
- `payload`
- `client_timestamp`
- `device_id`

Operations should be idempotent on the server.

## Conflict Strategy

MVP0 should optimize for simple, predictable behavior.

Recommended rule set:

- same-device offline edits: merge naturally by operation order
- cross-device concurrent edits to the same set or session: last write wins at field level for mutable fields
- deletions beat updates when timestamps clearly indicate later deletion
- preserve tombstones long enough for sync convergence

Because workout logging is usually single-user and single-active-device, this is sufficient for MVP0.

## Deletion Model

Use soft deletes for:

- sessions
- sets
- custom exercises when possible

This supports undo, sync convergence, and auditability.

## API Design

MVP0 can expose either conventional REST or a sync-first RPC shape. The important part is clear, durable contracts.

### Recommended Endpoints

- `POST /v1/auth/signup`
- `POST /v1/auth/login`
- `GET /v1/bootstrap`
- `POST /v1/sync/push`
- `POST /v1/sync/pull`
- `GET /v1/exercises/search`
- `POST /v1/exercises`
- `GET /v1/sessions`
- `GET /v1/sessions/:id`
- `GET /v1/exercises/:id/history`

### Bootstrap Payload

Should include the minimum data to make the app useful immediately:

- user preferences
- canonical exercises
- user's custom exercises
- recent sessions summary
- recent exercise stats snapshots
- lightweight profile basics needed for the `Profile` tab

## Query and Read Models

The active workout screen should not build itself from expensive joins every render. Create direct read models for the core surfaces.

### Read Model: App Shell State

Contains:

- whether an in-progress session exists
- active session id if present
- lightweight counts and badges needed for main-tab presentation
- basic profile summary needed for the MVP0 `Profile` tab

### Read Model: Active Session View

Contains:

- session metadata
- ordered exercise blocks
- sets grouped by exercise
- last-session reference per exercise
- quick-add shortcuts derived from recent or similar history

### Read Model: Home View

Contains:

- in-progress session if any
- repeatable recent sessions
- explicit `Coming Soon` state for future social feed content in MVP0

For MVP0, this is the practical replacement for a tracker-only `Today` screen inside the full app shell.

### Read Model: History View

Contains:

- recent completed sessions
- repeatable prior workouts
- lightweight exercise recall snippets where useful

### Read Model: Exercise Detail

Contains:

- recent sessions for exercise
- best recent set
- latest set progression
- compact trend summaries

### Read Model: Profile Summary

Contains:

- lightweight identity fields
- recent workout summary cards or counts
- profile-ready stats that can later be reused by social surfaces without reshaping tracking data

## Recall and Ranking Design

MVP0 does not need a heavy ML system. A deterministic scoring system is enough.

## Exercise Search Ranking

Score inputs:

- exact text match
- prefix match
- recency
- frequency
- used in current session before
- commonly paired with current session exercises
- present in most similar prior session

## Quick-Add Recall Ranking

Features:

- recency and frequency
- whether the exercise is already part of the current session
- whether exercise appears in similar historical sessions
- whether it commonly co-occurs in the user's own history

Output:

- top 3 to 5 quick-add candidates with optional reason strings such as `recently used` or `from similar workout`

## First-Set Default Inheritance

Hierarchy:

1. last set on this exercise in the current session when repeating
2. first relevant set from most recent prior session for this exercise
3. fallback user defaults or blank fields

The rules should be deterministic and explainable.

## Inferred Session Types

This should be lightweight and descriptive, not prescriptive.

Approach:

- compare current exercise composition against recent completed sessions
- identify the closest recurring pattern
- assign a human-readable label such as `usual upper`, `usual push`, or a top-exercise-based label

This is optional metadata, not a hard domain primitive.

## Performance Considerations

### Client

- all core workout actions should resolve locally in under 100 ms
- active workout screen should render from local state only
- search should debounce minimally or not at all when using local indexes
- app relaunch with an in-progress workout should restore the active session deterministically from local state

### Backend

- sync endpoints should be batch-friendly
- recent-history queries should read from denormalized summaries where possible
- write path should remain simple and transactional

## Security and Privacy

MVP0 still needs basic rigor.

- authenticated API access
- per-user row ownership enforcement
- encrypted transport
- secure token storage on device
- minimal PII collection
- account deletion path later, even if operationally manual at first

Because social is not shipping, privacy complexity is materially lower in MVP0.

## Analytics and Observability

Instrument the product to validate the core thesis.

### Product Metrics

- time from app open to first set logged
- average taps per set logged
- share of sets logged via repeat action
- share of exercise adds from history-based quick-add versus manual search
- session completion rate
- custom exercise creation rate
- offline mutation success rate
- sync failure rate

### Operational Metrics

- sync queue depth
- push/pull latency
- local database error rate
- crash-free active workouts

## Recommended Tech Shape

This is intentionally conservative.

### Client

- React Native or native mobile stack
- SQLite-backed local persistence
- background sync worker

### Server

- one API application
- Postgres
- background jobs for summaries and stats
- managed auth/session infrastructure if desired

Alternative stacks are fine as long as they preserve:

- local-first writes
- relational durability
- explicit sync contracts
- room for future modular separation

## Future MVP1 Boundary for Social

When social is added, the tracking domain should publish a stable, minimal contract.

Recommended future share payload:

- `workout_id`
- `user_id`
- `title`
- `subtitle`
- `hero_stats`
- `completed_at`
- `privacy_eligibility`
- optional `thumbnail_asset_id`

Important rule:

- social consumes workout summaries and stable identifiers
- social does not join directly into raw set-entry storage for feed rendering

This preserves independence and avoids contaminating MVP0 design with MVP1 concerns.

## Delivery Plan

## Phase 1: Core Logging Foundation

- auth
- local database
- exercises
- sessions
- set logging
- repeat set
- history
- app-shell support for `Lift` resume behavior

## Phase 2: Smart Assistance

- previous-session recall
- ranked search
- history-based quick-add shortcuts
- custom exercises
- bodyweight family support
- explicit `Coming Soon` `Home` and `Explore` shell surfaces
- lightweight `Profile` shell surface

## Phase 3: Reliability and Polish

- offline sync hardening
- derived read models
- analytics instrumentation
- undo and deletion polish
- app relaunch and in-progress-session restoration hardening

## Main Risks

### Risk 1: Over-Engineering Before Product Validation

Mitigation:

- keep backend modular but monolithic
- use deterministic heuristics before ML
- ship local-first core flows before advanced analytics

### Risk 2: Weak Offline Behavior Undermines Trust

Mitigation:

- local commit before sync
- aggressive sync retry
- visible but calm sync-status handling
- thorough testing around partial connectivity

### Risk 3: Query Complexity Slows the Core Screen

Mitigation:

- create explicit read models for Home, Active Session, History, and Exercise Detail
- denormalize recent history snippets

### Risk 4: Exercise Taxonomy Drift

Mitigation:

- allow custom exercises immediately
- normalize names in background
- defer canonicalization workflows until real usage data exists

## Recommendation

Build MVP0 as a local-first social app shell with a serious tracking core, a modular monolith backend, and explicit sync contracts.

That gives the team the best path to ship:

- instant logging
- offline trust
- personalized assistance from real user history
- a clean future seam for social features in MVP1
