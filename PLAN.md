# PLAN.md — Codexgrams Android Demo

Status: Planning complete; implementation not started.

## 1. Product, scope, and constraints

Build a functional Instagram-style **Android demo** for a few friends/test accounts. Users can share photos and short videos, follow people, interact with posts, message each other, and manage their profiles.

### Non-negotiables

- Android only; iOS and web are deferred.
- Google sign-in only, using Clerk.
- Convex for application data and backend logic.
- Expo Router with four native tabs: **Home, Messages, Explore, Profile**.
- NativeWind styling; modern, minimal, light-only UI.
- No subscriptions, billing, paid tiers, or gated features.
- **Zero paid services:** remain within free allowances; do not enable paid overages.
- Preserve existing workspace changes during implementation.

### Included

- Google signup/login and logout.
- Required unique username and display name; optional avatar and bio.
- One image or video per post, selected from the device library.
- Optional captions; videos up to 30 seconds.
- Following/unfollowing, liking/unliking, flat chronological comments.
- Deleting your own posts and comments.
- Real-time one-to-one text messaging, unread counts, and DM push notifications.
- Profile viewing/editing and follower/following lists.
- Blocking, reporting, an admin-only report review screen, and account deletion.
- Authorization for profiles, posts, and media delivery.

### Excluded

Stories, Reels-style browsing, carousels, camera capture, filters, post/comment editing, threaded comments, hashtags, saves, private accounts, group chats, message attachments, typing indicators, read receipts, social push alerts, persistent offline queues, monetization, and public store launch.

## 2. Existing project and architecture

At planning time, the inspected project has a root stack and placeholder screen in `src/app/`. Clerk, Convex, and NativeWind are not installed. Existing uncommitted changes must be preserved.

### Confirmed versions

| Dependency | Lockfile version |
|---|---|
| Expo | 57.0.27 |
| Expo Router | 57.0.25 |
| React Native | 0.86.3 |
| React | 19.2.3 |

Use **NativeWind 4.2.7**, documented as supporting SDK 57. Select compatible stable Clerk and Convex packages during dependency setup and record their resolved versions in the lockfile. [NativeWind documentation](https://www.nativewind.dev/docs/getting-started/installation)

### Responsibilities

- **Clerk:** Google authentication, identities, sessions, and secure session persistence.
- **Convex:** profiles, social data, authorization, real-time subscriptions, file storage, scheduled cleanup, and notification work.
- **Mobile client:** navigation, presentation, media selection/playback, transient form state, and submission feedback.
- **Expo Push Service:** delivery of DM notifications.
- **EAS:** Android development and demo APK builds.

No separate general-purpose API server. Keep screens/layouts in `src/app/`; components, hooks, and utilities outside it. Use Convex queries, mutations, actions, and HTTP actions according to their documented responsibilities.

Check matching [Expo SDK 57 documentation](https://docs.expo.dev/versions/v57.0.0/) and the [Expo documentation index](https://docs.expo.dev/llms.txt) before implementing Expo APIs. Native tabs must follow the [SDK 57 reference](https://docs.expo.dev/versions/v57.0.0/sdk/router/native-tabs/).

### Data and backend interfaces

| Entity | Main information and rules |
|---|---|
| Profiles | Clerk identity, unique normalized username, display name, optional avatar/bio, lifecycle status |
| Posts | Author, media reference/type, optional caption, creation time, publication/deletion status |
| Media | Storage reference, owner, purpose, byte size, dimensions, optional duration, upload status |
| Follows | Follower and followed user; unique pair; no self-follow |
| Likes | User and post; unique pair |
| Comments | Author, post, text, creation time |
| Conversations | Two distinct members; one conversation per unordered pair |
| Messages | Conversation, sender, text, creation time, retry/deduplication identifier |
| Read positions | Conversation member's last-read position |
| Blocks | Blocking user and blocked user; unique pair |
| Reports | Reporter, target, reason, status, timestamps |
| Push devices | Owner, push token, installation identifier, validity status |
| Background jobs | Pending push/cleanup work and retry state where needed |

- Derive identity from verified authentication; never trust a submitted owner or sender ID.
- Check permissions in every backend operation.
- Uniqueness must be enforced transactionally, not only in the UI.
- Paginate feeds, comments, follower lists, conversations, and messages.
- Keep sensitive identity information separate from public profiles.
- Use a configured Clerk-ID allowlist for the initial administrator; users cannot assign themselves administrator access.
- Provide operations for profile onboarding/editing, feed/search, posts/uploads, follows, likes, comments, conversations/messages/read positions, blocks/reports, push-device registration, and deletion.

## 3. User journeys and behavior

### Authentication and onboarding

1. Signed-out users see a minimal Google sign-in screen.
2. Successful authentication ensures an application profile exists without creating duplicates.
3. Incomplete profiles must choose a unique username and display name.
4. Show suggested people to follow, then enter Home.
5. Returning users with complete profiles enter Home directly.
6. Logout clears account-specific local state and unregisters that installation's push association.

Do not request notification permission before the user understands its purpose. Denial must leave chat usable.

### Main tabs

- **Home:** Own posts and followed users' posts, newest first. Header plus button opens the composer. An empty feed offers discovery and creation actions.
- **Messages:** Conversations ordered by latest activity, unread badges, recipient profile access, and one-to-one text chat. Sending from a profile creates or reuses the conversation.
- **Explore:** Recent community posts in a grid and debounced username/display-name search. Tapping a result opens the post or profile.
- **Profile:** Avatar, display name, username, bio, counts, follower/following lists, post grid, edit profile, settings, logout, and account deletion.

### Posts and interactions

- Choose one image or video, preview it, enter an optional caption, and publish.
- Validate supported media and configured limits before uploading.
- Show upload/publish progress; do not display unfinished uploads as published posts.
- Retain composer input while the screen remains open after failure.
- Keep retries safe against duplicate publication.
- Clean up abandoned uploads.
- Video playback is tap-to-play for the demo; pause when off-screen or leaving the screen.
- Likes and follows may update optimistically with rollback on failure.
- Comments remain a flat oldest-first list.
- Confirm destructive actions and remove deleted content from all relevant views.

### Messaging, blocking, and deletion

- Only conversation members can access its messages.
- Deduplicate send retries using a stable client request identifier.
- Opening a conversation updates its read position.
- DM notifications open the relevant conversation after authentication and access checks.
- A block hides both users from each other's social views and prevents new interactions/messages.
- Hide their conversation while blocked; retain history and restore access after unblocking.
- Retain already-created comments/likes in storage, but exclude blocked actors from visible activity and counts.
- Account deletion disables application access immediately, then performs resumable cleanup and Clerk deletion.
- Remove the profile, social relationships, posts, comments, likes, push associations, and owned media.
- Retain sent message text, presenting its sender as "Deleted user."

### Failure handling

- Keep already-loaded content visible during disconnection.
- Show actionable connection, upload, authentication, and submission errors.
- No persistent drafts or automatically queued offline actions.
- Roll back failed optimistic changes.
- Handle missing/deleted content and stale notification links gracefully.
- Backend jobs use bounded retries; duplicate push attempts must not duplicate messages.
- Permission failures must not expose protected content.

## 4. Step-by-step implementation

Complete each step's acceptance checks before moving on. Check boxes indicate completed phases, not authorization to implement everything at once.

### Step 1 — Project and service setup

- [ ] Preserve existing workspace changes; remove obsolete starter configuration only when relevant.
- [ ] Use npm-based commands because the project currently has `package-lock.json`, with no Bun lockfile.
- [ ] Install dependencies through `npx expo install`, following project instructions.
- [ ] Configure NativeWind, light appearance, reusable spacing/type/color tokens, and Android identity.
- [ ] Create Clerk, Convex, and Expo accounts on free plans.
- [ ] Configure Google authentication and callback handling.
- [ ] Store secrets outside source control; expose only client-safe configuration.
- [ ] Configure Android development and installable demo APK build profiles.
- [ ] Remove the stale reset script entry if its referenced file remains absent.

**Acceptance:** App starts on Android; configuration is valid; lint and typecheck pass.

### Step 2 — Prove authentication and protected media

- [ ] Implement Clerk/Convex authentication integration and protected route boundaries.
- [ ] Prove Google sign-in on a real Android build.
- [ ] Prototype authenticated image and video delivery before building the full feed.
- [ ] Verify image requests, video playback/seeking, expired sessions, and blocking.
- [ ] Use protected Convex HTTP actions for media access; do not distribute unrestricted storage URLs.

Convex documents authenticated HTTP actions for protected files, with a 100 MiB response limit. Video playback and bandwidth behavior still require device verification. [Convex serving files](https://docs.convex.dev/file-storage/serve-files)

**Acceptance:** Signed-out and blocked users cannot retrieve protected media. If the approach fails, stop and revise it before continuing; do not weaken privacy or add a paid provider silently.

### Step 3 — Schema, authorization, and onboarding

- [ ] Create the core schema, indexes, authenticated interfaces, and administrator checks.
- [ ] Implement profile provisioning and required onboarding.
- [ ] Add backend tests for ownership, uniqueness, memberships, and access boundaries.
- [ ] Establish configurable demo limits.

**Acceptance:** Two Google accounts produce distinct profiles; duplicate usernames are rejected; incomplete profiles cannot bypass onboarding.

### Step 4 — Navigation and profiles

- [ ] Build the four native tabs and screen/detail navigation.
- [ ] Implement profile editing, profile grids, follows, counts, lists, and people search.
- [ ] Add light-theme empty/loading/error states and accessible controls.

**Acceptance:** Navigation and Android back behavior work; profile updates persist; duplicate/self-follows are prevented.

### Step 5 — Media posting and social feeds

- [ ] Implement library selection, preview, upload, publish, deletion, and abandoned-upload cleanup.
- [ ] Build paginated Home and Explore views using shared post components.
- [ ] Add likes and comments with backend authorization.
- [ ] Ensure deleted media and posts become inaccessible.

**Acceptance:** Two users can publish, discover, follow, like, comment, and delete only their own content.

### Step 6 — Messaging and push

- [ ] Implement conversation reuse, real-time messages, pagination, read positions, and unread counts.
- [ ] Register Android push devices and configure delivery.
- [ ] Send privacy-preserving "New message" notifications, without message text.
- [ ] Suppress notifications for blocked/deleted users and the currently read conversation where practical.
- [ ] Invalidate expired push tokens and handle failed delivery independently of message persistence.

**Acceptance:** Two Android devices exchange messages and receive background notifications; notification denial does not break chat.

### Step 7 — Moderation and account deletion

- [ ] Add blocking/unblocking and reporting entry points.
- [ ] Build an administrator-only report queue with remove-content and dismiss actions.
- [ ] Add confirmed account deletion and resumable cleanup.
- [ ] Apply blocking consistently across feeds, search, profiles, comments, messages, and media.

**Acceptance:** Unauthorized users cannot moderate; blocks are enforced server-side; deletion survives partial cleanup failures.

### Step 8 — Verify and package the demo

- [ ] Run `npx expo lint`, `npx tsc --noEmit`, backend permission tests, and `npx expo-doctor`.
- [ ] Test the agreed flows on real Android devices, including slow/disconnected networks.
- [ ] Review scrolling, image memory use, video lifecycle, keyboard behavior, touch targets, labels, contrast, and larger fonts.
- [ ] Check usage dashboards and test quota-error handling.
- [ ] Run `graphify update .` after code changes.
- [ ] Build and install an APK directly; Google Play publication is deferred.

**Acceptance:** A clean installation supports Google login and all agreed core features without paid services.

## 5. Free limits, assumptions, and risks

### Verified free allowances

Checked against official documentation on October 13, 2026; recheck before implementation.

| Service | Relevant allowance |
|---|---|
| Clerk Hobby | 50,000 monthly retained users; Google social sign-in included |
| Convex Free | 0.5 GB database storage; 1 GB file storage; 1 GB/month file egress; additional compute/I/O limits apply |
| Expo Push Service | No charge for sending notifications |
| EAS Free | 15 Android builds/month, lower-priority queue |

Sources: [Clerk pricing](https://clerk.com/pricing), [Convex limits](https://docs.convex.dev/production/state/limits), [Expo push FAQ](https://docs.expo.dev/push-notifications/faq/), [EAS pricing](https://expo.dev/pricing).

Use **Convex Free**, not paid-overage Starter. Quotas are shared according to the provider's accounting rules; development and test usage also matter. Exhausted quotas may interrupt the demo rather than generate a paid upgrade.

### Recommended demo defaults — ASSUMPTIONS

- Approximately 2–5 test accounts; the earlier 100-user beta assumption is removed.
- Adults-only, English-only, portrait-oriented demo.
- No fixed deadline, committed v2, or recurring spending allowance. The earlier $50/month assumption is removed.
- NativeWind 4.2.7; verify remaining package compatibility during setup.
- Images up to 2 MB; videos up to 10 MB and 30 seconds. Reject oversized files with a clear message; automatic video transcoding is deferred.
- Usernames: 3–30 lowercase letters, digits, or underscores; case-insensitive uniqueness.
- Display names up to 50 characters; bios up to 160; captions up to 2,000; comments/messages up to 1,000. Comments/messages cannot be blank.
- Standard page sizes of 20 records, and 30 for message history.
- No end-to-end message encryption.
- You alone moderate through the administrator allowlist.
- Use a development environment first; add a separate shared-demo environment before distributing the APK.
- Provider dashboards and safe structured logs are sufficient initially; no paid monitoring.
- Log failures and job outcomes without tokens, credentials, message bodies, or private media URLs.
- Keep seeded/demo media small and remove unused files.
- No hardcoded test-account restriction: open signup remains the agreed product behavior, while the APK is shared with a small test group.

### Open risks and implementation gates

- Strict media delivery must pass the Android video proof before feed implementation.
- Client-reported duration and file metadata are not trusted security checks; validate server-side metadata/file types where feasible. Robust video inspection remains a feasibility item.
- Repeated video viewing can exhaust bandwidth with very few users.
- Free quotas do not guarantee uptime; pause testing or reduce usage when exhausted.
- Authentication configuration, push credentials, Android identifiers, and device access remain prerequisites.
- Failed account/media cleanup needs retries and observable status.
- Open signup is not a usage cap; keep demo distribution limited.
- Public launch, legal-policy review, broader moderation operations, and production scalability are deferred.
