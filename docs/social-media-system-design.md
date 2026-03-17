# Social Media System Design for a Lifting App

## Scope

This document covers the social-media side of a lifting app: profiles, social graph, feeds, posts, media, comments, reactions, messaging, notifications, privacy, moderation, search, and the supporting system design. It intentionally avoids workout-tracking design except where the social layer needs to reference workout content.

The core design principle is separation of concerns:

- The social platform should work even if workout tracking is a separate product surface.
- The workout domain can publish events or entities that the social layer references.
- Social features should consume stable identifiers and rendered summaries from the tracking system, not reach deeply into tracking internals.

## Product Goals

- Support a familiar modern social product experience in the orbit of Instagram and Strava, adapted for lifters.
- Allow public creators and private friend circles.
- Handle media-heavy content and engagement patterns.
- Keep the architecture modular so the workout-tracking system can evolve independently.
- Start simple enough for an MVP, but avoid design choices that block later scale.
- Make the overall product feel like one coherent consumer app, not a tracker with a disconnected community tab added later.

## Product Experience Principle

Even when tracking and social ship in phases, the product should read as socially native from day one.

That means:

- identity, profiles, and workout history should feel presentation-ready
- workout summaries should be easy to render as social cards later
- tracking UX should feel compatible with a modern mobile social app aesthetic rather than a utility dashboard

Recommended long-term app shell:

- `Home`
- `Explore`
- `Lift`
- `History`
- `Profile`

This matters even before the full social stack ships because the navigation should already communicate that the product is a consumer social app with a first-class lifting action at its center, not a standalone logging utility.

## Non-Goals

- Detailed workout-logging architecture.
- Wearable ingestion, route maps, exercise analytics, or PR-calculation systems.
- Ad platform design.
- Full recommendation/ML platform beyond simple ranking hooks.

## Social Feature Inventory

### 1. Identity and Accounts

- User registration and login.
- Username, display name, bio, avatar, pronouns, location, links.
- Account states: active, disabled, deleted, banned, shadow-limited.
- Device/session management.
- Account verification for notable creators or gyms.

### 2. Profiles

- Public or private profiles.
- Profile header with avatar, bio, counts, badges.
- Profile tabs:
  - Posts.
  - Tagged posts or mentions.
  - Saved collections.
  - Followers / following.
  - Highlights or pinned content.
- Profile metrics:
  - Follower count.
  - Following count.
  - Post count.
- Pinned posts.
- Linked gym, team, or brand affiliations.

### 3. Social Graph

- Follow / unfollow.
- Follow requests for private accounts.
- Remove follower.
- Block user.
- Mute user.
- Restrict user.
- Close friends / inner circle list.
- Optional friend model later, but follower graph should be primary.

### 4. Posts

- Text posts.
- Photo posts.
- Video posts.
- Carousel posts with multiple assets.
- Caption with hashtags, mentions, tagged users, tagged gym, tagged workout.
- Post visibility:
  - Public.
  - Followers-only.
  - Close-friends-only.
  - Private/draft.
- Post edit and delete.
- Soft delete and recovery window.
- Pin to profile.

### 5. Activity / Workout Shares

These are social posts that reference workout data without coupling social to tracking internals.

- Share a completed workout.
- Share a PR or milestone card.
- Share a gym check-in.
- Share transformations or progress photos.

The social service stores a reference to the workout-domain object and a denormalized render snapshot for feed display.

### 6. Comments and Replies

- Top-level comments.
- Threaded replies.
- Mentions in comments.
- Edit/delete own comments.
- Comment controls:
  - Disable comments on a post.
  - Limit comments to followers / mutuals.
- Pinned comment.

### 7. Reactions / Likes

- Like/unlike posts.
- Like comments.
- Optional richer reactions later, but start with likes only.

### 8. Stories / Ephemeral Content

- 24-hour stories.
- Image/video story uploads.
- Story viewers list.
- Story replies.
- Highlights.
- Close-friends stories.

This can ship after the core feed and posts system.

### 9. Direct Messages

- 1:1 conversations.
- Group conversations.
- Text, media, link previews, shared posts, shared workout cards.
- Message requests from non-followed users.
- Read receipts.
- Typing indicators.
- Mute conversation.
- Leave group.
- Report/block from DM.

### 10. Feed

- Home feed.
- Following feed.
- Creator/discovery feed later.
- Profile feed.
- Hashtag feed.
- Gym/location feed later.

### 11. Notifications

- Followed you.
- Follow request.
- Follow request accepted.
- Liked your post.
- Commented on your post.
- Replied to your comment.
- Mentioned you.
- Tagged you.
- DM received.
- Story reply.
- Milestone/social prompts later.

Delivery channels:

- In-app inbox.
- Push.
- Email for selected events.

### 12. Search and Discovery

- Search users by username/display name.
- Search hashtags.
- Search gyms/locations later.
- Discover suggested users and communities.
- Trending content later.

### 13. Privacy and Safety

- Public/private profile toggle.
- Audience selection per post/story.
- Block, mute, restrict.
- Report user/post/comment/message.
- Content moderation pipeline.
- Rate limiting and anti-spam.
- Minor safety / age gating if needed.

### 14. Moderation and Trust

- Abuse reports.
- Review queues.
- Content takedowns.
- Spam detection.
- Impersonation handling.
- Device/IP/account reputation signals.

### 15. Saved and Shared Content

- Save/bookmark post.
- Share post to DM.
- Copy link.
- External link sharing if content is public.

## Product Boundaries Between Social and Tracking

The social system should not own workout truth. It should only consume references.

Recommended contract:

- Tracking system owns:
  - Workout records.
  - Exercise sets/reps/weight.
  - PR calculations.
  - Programs.
  - Training analytics.
- Social system owns:
  - Social post wrapping.
  - Feed distribution.
  - Engagement.
  - Privacy.
  - Messaging.
  - Notifications.
- Integration boundary:
  - Social receives a `workout_id` or `achievement_id`.
  - Social fetches or is pushed a display snapshot:
    - title
    - subtitle
    - hero stats
    - media thumbnail
    - privacy eligibility
  - Social stores snapshot data for feed rendering and historical consistency.

This avoids feed performance depending on live joins into workout storage.

## High-Level Architecture

### Core Services

For an MVP, some of these can begin in a modular monolith with clear module boundaries. At scale, they separate cleanly into services.

1. Identity Service
   - auth, sessions, user core identity
2. Profile Service
   - profile metadata, visibility settings, avatar, bio
3. Social Graph Service
   - follows, blocks, mutes, close friends, follow requests
4. Content Service
   - posts, captions, tags, stories, saved posts
5. Media Service
   - upload orchestration, metadata, transcoding pipeline, CDN publishing
6. Engagement Service
   - likes, comments, comment replies
7. Feed Service
   - fanout, ranking, pagination, feed retrieval
8. Messaging Service
   - conversations, messages, delivery state
9. Notification Service
   - in-app notification generation and delivery
10. Search Service
   - user search, hashtag indexing, discovery support
11. Moderation / Trust Service
   - reports, decisions, abuse heuristics, takedown enforcement
12. Integration Service or Event Bus Consumers
   - consumes workout events and produces social-ready share cards

### Supporting Infrastructure

- API gateway / BFF for mobile clients.
- Relational database for strongly consistent core records.
- Redis for caching, counters, session/state acceleration, rate limiting.
- Object storage for raw media.
- CDN for media delivery.
- Queue/event bus for asynchronous workflows.
- Search index for text/user lookup.
- Data warehouse / analytics pipeline for product metrics and ranking features.

## Recommended MVP Architecture

Start with:

- A modular monolith application.
- PostgreSQL as primary database.
- Redis for cache, rate limiting, and ephemeral state.
- S3-compatible object storage for media.
- CDN in front of media.
- Background job system for fanout, notifications, transcoding callbacks, moderation tasks.
- OpenSearch/Elasticsearch or Postgres trigram search for initial user search.

Do not begin with many microservices. The team will move faster with:

- one deployable app
- one primary relational database
- one cache
- one async jobs system

The important part is clean module boundaries and event contracts, not network boundaries.

## Core Domain Model

### Users

- `user`
  - `id`
  - `username`
  - `display_name`
  - `status`
  - `created_at`

### Profiles

- `profile`
  - `user_id`
  - `bio`
  - `avatar_media_id`
  - `visibility` enum
  - `message_permission` enum
  - `is_verified`
  - `settings_json`

### Social Graph

- `follow_edge`
  - `follower_user_id`
  - `followed_user_id`
  - `state` enum: pending, accepted
  - `created_at`

- `block_edge`
  - `actor_user_id`
  - `target_user_id`

- `mute_edge`
  - `actor_user_id`
  - `target_user_id`
  - `mute_posts`
  - `mute_stories`

- `close_friend_edge`
  - `owner_user_id`
  - `member_user_id`

### Posts

- `post`
  - `id`
  - `author_user_id`
  - `post_type` enum: text, media, workout_share, milestone, story_reference
  - `caption_text`
  - `visibility` enum
  - `comment_policy` enum
  - `created_at`
  - `edited_at`
  - `deleted_at`
  - `ranking_features_json`

- `post_media`
  - `id`
  - `post_id`
  - `media_id`
  - `position`

- `post_tag_user`
  - `post_id`
  - `tagged_user_id`

- `post_tag_topic`
  - `post_id`
  - `hashtag`

- `post_external_ref`
  - `post_id`
  - `ref_type` enum: workout, achievement, gym
  - `ref_id`
  - `snapshot_json`

### Comments / Likes

- `comment`
  - `id`
  - `post_id`
  - `author_user_id`
  - `parent_comment_id` nullable
  - `body_text`
  - `created_at`
  - `deleted_at`

- `post_like`
  - `post_id`
  - `user_id`
  - `created_at`

- `comment_like`
  - `comment_id`
  - `user_id`
  - `created_at`

### Media

- `media_asset`
  - `id`
  - `owner_user_id`
  - `type` enum: image, video
  - `storage_key`
  - `status` enum: uploaded, processing, ready, failed
  - `width`
  - `height`
  - `duration_ms`
  - `blurhash`
  - `created_at`

### Messaging

- `conversation`
  - `id`
  - `type` enum: direct, group
  - `created_by_user_id`
  - `created_at`

- `conversation_member`
  - `conversation_id`
  - `user_id`
  - `role`
  - `last_read_message_id`
  - `state`

- `message`
  - `id`
  - `conversation_id`
  - `sender_user_id`
  - `message_type` enum: text, media, share_post, share_workout, system
  - `body_text`
  - `created_at`
  - `deleted_at`

### Notifications

- `notification`
  - `id`
  - `recipient_user_id`
  - `actor_user_id`
  - `type`
  - `object_type`
  - `object_id`
  - `read_at`
  - `created_at`

## API Surface

The client should talk to a single API layer that composes data from modules. Internal module boundaries can remain hidden.

### Identity / Profile

- `POST /auth/signup`
- `POST /auth/login`
- `GET /me`
- `PATCH /me/profile`
- `PATCH /me/privacy`
- `GET /users/:username`

### Social Graph

- `POST /users/:id/follow`
- `DELETE /users/:id/follow`
- `POST /users/:id/follow-request/accept`
- `POST /users/:id/block`
- `DELETE /users/:id/block`
- `POST /users/:id/mute`

### Posts

- `POST /posts`
- `GET /posts/:id`
- `PATCH /posts/:id`
- `DELETE /posts/:id`
- `GET /feed/home`
- `GET /users/:id/posts`

### Comments / Likes

- `POST /posts/:id/likes`
- `DELETE /posts/:id/likes`
- `POST /posts/:id/comments`
- `POST /comments/:id/replies`
- `DELETE /comments/:id`

### Media

- `POST /media/upload-url`
- `POST /media/:id/complete`

### Messaging

- `GET /conversations`
- `POST /conversations`
- `GET /conversations/:id/messages`
- `POST /conversations/:id/messages`

### Notifications

- `GET /notifications`
- `POST /notifications/read`

### Search

- `GET /search/users?q=...`
- `GET /search/hashtags?q=...`

## Feed Design

The home feed is the hardest social feature operationally. Design it deliberately.

### Feed Types

1. Following feed
   - Reverse chronological from followed accounts.
   - Easiest to reason about.
   - Best MVP default.
2. Ranked home feed
   - Candidate posts from followed accounts plus later discovery sources.
   - Ordered by heuristics or ML ranking.
3. Profile feed
   - Posts authored by a single user.
4. Hashtag/topic feed
   - Search/discovery-oriented.

### MVP Feed Strategy

Use a hybrid approach:

- Ship reverse chronological following feed first.
- Maintain a simple home feed that is either:
  - the same as following feed, or
  - a lightly re-ranked version using heuristics.

Example ranking features:

- relationship strength
- recency
- prior engagement between viewer and author
- author quality/spam score
- post type
- predicted probability of like/comment

Do not start with full ML ranking. A deterministic scoring function is enough early on.

### Fanout Model

There are two classic feed approaches:

1. Fanout on write
   - When a user posts, push the post into follower inboxes.
   - Fast reads, expensive writes.
   - Good for most users with moderate follower counts.
2. Fanout on read
   - When a viewer opens feed, pull recent posts from followed accounts and merge.
   - Cheap writes, expensive reads.
   - Better for very high-fanout accounts.

Recommended approach:

- Use hybrid fanout.
- Default to fanout-on-write for normal users.
- For celebrity/high-follower accounts, mark them as pull-based producers and merge their content at read time.

This is how you avoid one large account exploding write amplification.

### Feed Storage

- `feed_inbox`
  - `owner_user_id`
  - `post_id`
  - `producer_user_id`
  - `score`
  - `inserted_at`

For MVP, this can live in Postgres if scale is small. At larger scale:

- write feed candidates to a distributed store or Redis-backed sorted sets
- persist source-of-truth events separately

### Feed Generation Flow

1. Author creates post.
2. Content service stores post.
3. Post-created event emitted.
4. Feed worker resolves eligible audience:
   - followers
   - privacy gates
   - blocks
   - close-friends filters
5. Feed worker fans out to recipient inboxes or marks author as pull-based.
6. Notification worker emits relevant notifications.
7. Search/discovery indexer updates indexes.

### Feed Read Flow

1. Viewer requests `/feed/home`.
2. Feed service loads feed candidates from inbox storage.
3. Feed service merges pull-based producers if needed.
4. Visibility and block rules rechecked defensively.
5. Hydrate posts, author profiles, and media metadata.
6. Return cursor-paginated response.

### Pagination

Use cursor-based pagination, not offset pagination.

- better performance
- stable results under concurrent writes
- easier continuation for mobile scroll

## Privacy Model

Privacy rules affect nearly every read path. Centralize them.

### Profile Visibility

- Public:
  - anyone can view profile and posts marked public
- Private:
  - only approved followers can view follower-only content and full profile content

### Post Visibility

- Public
- Followers-only
- Close-friends-only
- Only-me/draft

### Access Evaluation

A shared policy engine should evaluate:

- is viewer blocked by author
- has viewer blocked author
- is author private
- is viewer an approved follower
- is content restricted to close friends
- is viewer allowed to DM author
- is account suspended or content removed

Do not duplicate access logic inconsistently across endpoints.

## Messaging System Design

DMs are a separate subsystem from feed content.

### Requirements

- Reliable message persistence.
- Near-real-time delivery.
- Read/unread state.
- Media attachment support.
- Message requests and spam controls.

### Design

- Store conversations and messages in relational storage initially.
- Use WebSockets for live delivery and typing indicators.
- Use Redis for presence and ephemeral typing state.
- Use push notifications for offline delivery.

### Message Requests

To reduce spam:

- allow DMs from:
  - everyone
  - followers only
  - nobody
- non-approved senders land in a request inbox
- attachments from untrusted senders may be restricted

### Future Complexity

If DMs become heavy enough, split them into a dedicated service earlier than other modules because their latency and product expectations differ from feed reads.

## Media Pipeline

Media is one of the largest operational costs and performance drivers.

### Upload Flow

1. Client requests upload token / presigned URL.
2. Client uploads directly to object storage.
3. Client completes upload with metadata.
4. Background workers validate file type and size.
5. Transcoding/thumbnail jobs run.
6. Asset marked ready and published via CDN.

### Requirements

- Image resizing for multiple device sizes.
- Video transcoding to streaming-friendly formats.
- Thumbnail generation.
- Blurhash or low-quality placeholders.
- Metadata stripping where appropriate.
- Malware and abuse scanning.

### Delivery

- Serve via CDN.
- Use immutable versioned URLs.
- Avoid proxying large media through the app server.

## Notification System Design

Notifications are event-derived and mostly asynchronous.

### Event Sources

- follows
- follow requests
- likes
- comments
- mentions
- tags
- DM events

### Design Principles

- Write notifications asynchronously from domain events.
- Deduplicate noisy actions where appropriate.
- Batch or aggregate low-value events:
  - “X and 12 others liked your post”
- Store an in-app notification record.
- Fan out to push/email based on user settings.

### Notification Preferences

Users should control:

- push on/off by event type
- email on/off by event type
- quiet hours later

## Search and Discovery

### MVP

- Username prefix search.
- Display-name search.
- Hashtag search.

### Later

- Suggested follows.
- Trending hashtags.
- Discovery ranking.
- Nearby gyms and local communities.

### Indexing

- User profile updates emit index events.
- Post captions/hashtags emit index events.
- Moderation actions remove or suppress indexed content quickly.

## Moderation, Abuse, and Trust

This cannot be an afterthought. Social products fail fast when abuse tooling is weak.

### Must-Have Controls

- report user
- report post
- report comment
- report DM
- block user
- mute user
- restrict user

### Enforcement States

- content removed
- content age-gated
- account warned
- temporary posting limit
- temporary messaging limit
- shadow de-amplification
- suspension
- ban

### Automated Signals

- rapid follow/unfollow churn
- mass DM behavior
- duplicate caption spam
- repeated upload hashes
- device fingerprint reputation
- IP reputation
- high report rate

### Human Review Tooling

Eventually build an internal admin tool with:

- report queue
- user/account history
- moderation actions
- audit trail

## Consistency and Data Strategy

Different features need different guarantees.

### Strong Consistency Needed

- auth/session actions
- follow state changes
- block state changes
- privacy settings
- message persistence

### Eventual Consistency Acceptable

- feed fanout
- notification delivery
- search indexing
- counters
- recommendation updates

Counter values such as likes, followers, and comments should be treated as eventually consistent display values unless a workflow truly requires exact counts.

## Caching Strategy

Use Redis or equivalent for:

- user/profile hot reads
- social graph lookups
- feed page caching for small TTL windows
- conversation unread counts
- rate limiting
- presence/typing state

Do not over-cache content whose visibility can change rapidly without a reliable invalidation model.

## Counters

Common counters:

- follower count
- following count
- post count
- like count
- comment count

Recommended approach:

- maintain durable source-of-truth tables
- update denormalized counters asynchronously
- reconcile periodically with repair jobs

Avoid transactional updates across every high-volume engagement path if you want throughput.

## Event Model

Even in a monolith, publish internal domain events. This gives clean seams for later service splits.

Useful events:

- `user.created`
- `profile.updated`
- `follow.created`
- `follow.accepted`
- `follow.deleted`
- `user.blocked`
- `post.created`
- `post.updated`
- `post.deleted`
- `comment.created`
- `post.liked`
- `message.sent`
- `workout.shared`
- `moderation.action_taken`

Consumers:

- feed fanout
- notifications
- search indexing
- analytics
- moderation heuristics

## Availability and Failure Handling

### Principles

- Posting should succeed even if notifications are delayed.
- Media upload should succeed even if feed fanout is delayed.
- Feed read should degrade gracefully if ranking/discovery features fail.
- DMs need tighter reliability than likes or notifications.

### Examples

- If fanout job fails, retry from durable event log or jobs table.
- If notification push provider fails, keep the in-app notification.
- If search index lags, core profile lookup should still work from primary DB.

## Multi-Region and Scale Considerations

Do not optimize for this on day one, but avoid dead ends.

### Early Stage

- Single region.
- Primary relational DB with replicas.
- Background workers in same region.
- Global CDN for media.

### Later Stage

- Read replicas for feed/profile reads.
- Partition large tables by time or user ID where useful.
- Separate hot-path services like messaging or feed.
- Multi-region media delivery.
- Potential geo-distributed read architecture for DMs if the product becomes global and latency-sensitive.

## Security Considerations

- Strong auth/session management.
- Signed upload URLs.
- Input validation and output escaping.
- Rate limiting per IP, device, and account.
- Audit logs for moderation/admin actions.
- Encryption at rest for sensitive data.
- TLS everywhere.
- Careful treatment of private profile data and message metadata.

If the product may include minors, safety and compliance requirements expand significantly.

## Analytics and Observability

You need both product analytics and system observability.

### Product Metrics

- daily active users
- posts per user
- comments per post
- like rate
- follow conversion
- feed session length
- DM initiation rate
- notification open rate

### System Metrics

- post creation latency
- feed read latency
- fanout job lag
- media processing failure rate
- push delivery success
- websocket connection counts
- search indexing lag

### Logging and Tracing

- request IDs across API and job workers
- structured logs
- tracing for post-create and feed-read flows

## Suggested Rollout Plan

### Phase 1: Core Social MVP

- accounts and profiles
- public/private profiles
- follow graph
- text/photo posts
- likes
- comments
- reverse chronological following feed
- basic notifications
- media uploads
- block/report basics

### Phase 2: Social Depth

- workout-share post type
- hashtags and search
- saved posts
- post tagging
- profile pins
- simple home-feed ranking
- push notification preferences

### Phase 3: Communication

- 1:1 DMs
- group DMs
- message requests
- media in DMs

### Phase 4: Richer Social Surfaces

- stories
- highlights
- discovery feed
- suggested follows
- gym/community surfaces

## Important Product Decisions to Make Early

These choices shape the system:

1. Is the primary social graph follow-based, friend-based, or both?
   - Recommendation: follow-based first.
2. Are private accounts core behavior or an edge case?
   - Recommendation: treat privacy as first-class from day one.
3. Are workout shares just one post type, or the center of the feed?
   - Recommendation: one post type within a generic post model.
4. Will DMs be launch-critical?
   - Recommendation: not for v1 unless community formation requires them.
5. Will the home feed be ranked at launch?
   - Recommendation: no; use chronological first.

## Recommended Technical Opinion

If you are building the first version of this app, do not try to build “Strava scale architecture” up front. Build a clean modular monolith with strong event boundaries. The difficult part is not whether you have microservices; it is whether you have:

- a sane privacy model
- a flexible post model
- a workable feed architecture
- a solid media pipeline
- abuse controls
- room to reference workout entities without coupling to them

That is enough to ship a serious first version.

## Concrete Social Architecture Recommendation

- One app backend with modules for identity, profiles, graph, posts, engagement, notifications, messaging, and search.
- PostgreSQL as primary source of truth.
- Redis for cache, rate limiting, and ephemeral messaging state.
- Object storage + CDN for media.
- Background job queue for fanout, notifications, moderation, indexing, and media finalization.
- Event-driven integration with the workout domain using stable references and denormalized display snapshots.
- Reverse chronological following feed first, hybrid fanout when scale requires it.
- Privacy and block logic centralized in one access-control layer.

This is the simplest architecture that can grow into a serious consumer social app.

## Open Questions for the Next Design Pass

- What exact product surfaces are in v1: only feed/profile, or DMs too?
- Is there a concept of gyms, teams, or creators as first-class entities?
- Will workout shares dominate the feed, or coexist with general lifestyle posts?
- Is discovery important at launch, or is the app initially invite/community driven?
- Are there special safety needs around age, body image, or harassment controls?

## Next Documents to Write

After this document, the highest-value follow-ups would be:

1. Feed system deep dive.
2. Privacy and access-control design.
3. Messaging architecture.
4. Media pipeline design.
5. Social/tracking integration contract.
6. MVP schema and API specification.
