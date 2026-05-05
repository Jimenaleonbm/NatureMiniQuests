# User Stories & Use Cases — MVP

This document defines the MVP user stories for **Toronto Nature Mini Quests** focusing on **Don Valley / Evergreen Brick Works**.

## Global assumptions
- App runs on iOS + Android (mobile).
- Location permission: **While Using the App** (foreground only).
- Quests and checkpoints are **admin-curated**.
- Checkpoints are completed **in order**.
- Check-in is **manual** (button).

---

## Use Case 1 — First-time onboarding + permission
**Actor:** New user

**Flow:**
1. User opens app.
2. App shows short intro explaining: select a quest and visit checkpoints to unlock progress.
3. App requests location permission (While Using).
4. Map opens with user location and nearby quest content.

**Acceptance criteria:**
- If permission is denied, user sees clear instructions to enable it and can still browse quests.
- If permission is granted, user location displays within ~5–10 seconds.

---

## Use Case 2 — Browse quest list
**Actor:** User

**Flow:**
1. User opens Quest List.
2. Sees quests (title, short description, progress `X/Y`).
3. Taps a quest to open details.

**Acceptance criteria:**
- Quests load quickly (cached or fetched once).
- Progress reflects user’s saved state.

---

## Use Case 3 — View quest details
**Actor:** User

**Flow:**
1. User views quest detail.
2. Sees ordered checkpoints.
3. Sees which checkpoint is next.
4. Can open map centered on next checkpoint.

**Acceptance criteria:**
- Next checkpoint is clearly highlighted.
- Completed checkpoints are visually distinct.

---

## Use Case 4 — Successful manual check-in
**Actor:** User

**Preconditions:**
- User is within the checkpoint radius.
- GPS accuracy is acceptable.

**Flow:**
1. User taps “Check in”.
2. App validates distance + accuracy (+ optional speed).
3. App marks checkpoint complete and updates UI.
4. App activates next checkpoint.

**Acceptance criteria:**
- Completion is persisted (on-device for MVP; upgradeable to a remote backend later).
- UI updates immediately (optimistic update is OK; must reconcile on failure).

---

## Use Case 5 — Check-in too far away
**Actor:** User

**Flow:**
1. User taps “Check in” while outside radius.
2. App shows failure message and distance remaining.

**Acceptance criteria:**
- Message includes actionable guidance (e.g., “Get within 50m”).

---

## Use Case 6 — GPS accuracy too poor
**Actor:** User

**Flow:**
1. User taps “Check in” while accuracy is poor.
2. App blocks completion and explains why.

**Acceptance criteria:**
- Message suggests waiting or moving to open sky.

---

## Use Case 7 — Out-of-order checkpoint attempt
**Actor:** User

**Flow:**
1. User tries to check in at checkpoint #3 before #2.
2. App blocks and instructs them to complete #2 first.

**Acceptance criteria:**
- No progress is incorrectly recorded.

---

## Use Case 8 — Quest completion reward
**Actor:** User

**Flow:**
1. User completes final checkpoint.
2. App marks quest as completed.
3. App shows reward modal (badge/xp placeholder) and updates profile stats.

**Acceptance criteria:**
- Quest status becomes `completed` in persistent storage.
- Quest list shows completed state.

---

## Use Case 9 — App restart / progress sync
**Actor:** User

**Flow:**
1. User completes 2 checkpoints.
2. Closes app.
3. Reopens app.
4. Progress is restored.

**Acceptance criteria:**
- Progress loads correctly and map markers reflect completion.
