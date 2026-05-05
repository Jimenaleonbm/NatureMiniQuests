# Toronto Nature Mini Quests — MVP Plan (Don Valley / Evergreen Brick Works)

## Product statement
A mobile app (iOS + Android) that encourages outdoor exploration in Toronto by offering **admin-curated mini quests**. Users unlock progress by **manually checking in** at real-world **checkpoints**.

## MVP goals (what we must prove)
1. Users understand the quest loop quickly: *pick quest → go to checkpoint → check in → progress*.
2. Check-ins feel reliable (distance + accuracy gates) and satisfying (immediate UI feedback).
3. A single content pack (Evergreen / Brick Works to start) is enough to validate retention and fun.

## MVP scope (must-have)
### Platforms
- Mobile app supporting **iOS + Android**.

### Map & location
- Interactive map showing real-world geography.
- Show user location (foreground only — **no background tracking** in MVP).
- Show checkpoint markers and checkpoint radius zones on the map.

### Quests
- Admin-curated quest list (Evergreen content pack first).
- Quest detail view with ordered checkpoints.
- **Manual check-in** button.
- Ordered checkpoint completion rule (**in order**).
- Persist progress per user (on-device for MVP; upgradeable to a remote backend later).
- Basic reward UI on quest completion (badge/xp placeholder).

### Guardrails (anti-cheat-lite)
- Check-in allowed only when:
  - within checkpoint radius
  - GPS accuracy <= threshold
  - optionally speed <= threshold

## Out of scope (explicitly not in MVP)
- Background location tracking / step counting
- Fog-of-war squares/hex reveal
- User-generated quests
- Social features (friends, teams, chat)
- Real-time multiplayer
- Full offline maps / offline tile packs
- Advanced anti-spoofing

## Technology decision (open)
The technology stack has not been decided. Key capability requirements for any chosen stack:

- Cross-platform iOS + Android support from a single codebase (or two native codebases if preferred).
- Access to device GPS / location services (foreground only for MVP).
- An interactive map component that can render markers and radius circles.
- Local or remote persistence for user progress data.
- Ability to bundle or fetch admin-curated quest content.

Evaluation criteria: developer familiarity, community/library maturity, map integration quality, and ease of on-device testing.

A specific stack will be chosen before development begins and documented here once decided.

## Milestones (6-week plan)

### Week 1 — Foundations

**Deliverables**
- Mobile app bootstrapped and runnable on iOS + Android (simulator/emulator OK).
- Navigation skeleton:
  - **Quests** (list)
  - **Quest Detail** (checkpoint list + status)
  - **Map** screen placeholder (real map comes Week 2).
- Local content pack: a versioned data file (e.g. JSON) with:
  - 1 quest for **Evergreen / Brick Works**
  - 3–6 ordered checkpoints (id, name, lat, lng, radiusM, order/index).
- Local progress persistence:
  - Save per-user progress on-device for MVP.
  - In-order completion rule enforced (only the “next” checkpoint can be completed).
- Data/service boundary for future scaling:
  - Create a small repository/service layer (e.g. `QuestRepository`) with a local implementation so a remote backend can be added later without rewriting UI.

**Exit criteria**
- App runs without errors on both iOS and Android.
- Quests list loads from local data and shows basic quest info + progress (e.g. `0/5`).
- Quest Detail shows checkpoints in order with clear states: **completed / next / locked**.
- Progress persists across app restarts.
- Simple dev-only controls exist to speed testing (at least **Reset progress**, and a temporary **Complete next checkpoint** button until real check-in exists).

**Demo / manual test**
1. Open app → Quests → see Evergreen quest.
2. Open quest → see ordered checkpoints and “next” highlighted.
3. Tap “Complete next checkpoint” → it becomes completed; next checkpoint unlocks.
4. Close/reopen app → progress remains.
5. Tap “Reset progress” → progress returns to 0.

---

### Week 2 — Maps + foreground location

**Deliverables**
- Implement Map screen using a map library appropriate for the chosen stack.
- Foreground location:
  - Request permission
  - Get live position updates in foreground
  - Show user location on the map
- Render Evergreen content on map (from local data):
  - **Checkpoint markers**
  - **Zones**: checkpoint **radius circles** (from `radiusM`)
- Map UX basics:
  - “Center on me” button
  - Tap marker shows a small callout (name + status: completed/next/locked)
- Dev-only debug overlay for tuning check-in later:
  - GPS accuracy (m)
  - distance to “next” checkpoint (m)

**Exit criteria**
- Map loads reliably on iOS + Android.
- Location permission flow has clear states (loading / denied / granted).
- User location is visible and updates in foreground.
- All checkpoint markers + radius circles render from the data.

**Demo / manual test**
1. Fresh install → open Map → allow location → see user location + Evergreen markers.
2. Deny permission on a test run → Map shows “permission required” state (no crash).
3. Tap marker → see name + status.
4. See radius circles around checkpoints; confirm radius visually looks reasonable.

---

### Week 3 — Quest list + quest detail + map integration

**Deliverables**
- Quests list (from local data) with:
  - quest title + short description
  - progress summary (e.g., `2/5`)
  - “Start / Continue” primary action
- Quest Detail screen with:
  - ordered checkpoint list
  - clear states: **completed / next / locked**
  - “Go to Map” action (deep link to Map focusing on the quest area)
- Map integration:
  - Map renders **all checkpoints** but visually differentiates:
    - completed (e.g., green)
    - next (e.g., highlighted / pulsing)
    - locked (e.g., dim)
  - Tapping a checkpoint on the map can open a small bottom sheet / panel with details and status
- State management cleanup:
  - a single source of truth for “current quest” + progress (so list/detail/map stay in sync)

**Exit criteria**
- Starting/continuing a quest updates UI consistently across Quests list, Quest Detail, and Map.
- “Next checkpoint” is always computed correctly (in-order).
- Map highlight always matches the same “next checkpoint” shown in Quest Detail.

**Demo / manual test**
1. Start quest → see progress `0/N` and next checkpoint highlighted on Map + in Detail.
2. Complete next checkpoint via dev button (still OK until Week 4 real check-in) → progress updates everywhere.
3. Navigate Quests → Detail → Map → back; no state resets or inconsistencies.

---

### Week 4 — Manual check-in (real gating) + progress persistence

**Deliverables**
- Manual check-in flow (Quest Detail and/or Map):
  - “Check in” button for the **next** checkpoint only
  - Success/failure feedback (toast/modal + reason)
- Gating rules (anti-cheat-lite baseline):
  - within checkpoint radius (`radiusM`)
  - GPS accuracy ≤ threshold (configurable, e.g., 25–50m)
  - (optional) speed ≤ threshold if available
- Core calculations implemented and reusable:
  - distance-to-checkpoint (Haversine)
  - “is eligible to check in” function returning structured reasons
- Persist progress locally (same as Week 1) but now driven by real check-ins:
  - `completedCheckpointIds`
  - `completedAt` timestamps (optional but useful later)
- Remove/disable the Week 1 dev “Complete next checkpoint” shortcut (or keep behind a dev flag only)

**Exit criteria**
- You can physically stand near a checkpoint and successfully check in.
- You cannot check in when too far away or when accuracy is too poor, and the UI clearly explains why.
- In-order rule is enforced (no skipping).
- Progress survives app restart and stays consistent across Quests/Detail/Map.

**Demo / manual test**
1. Go near a real checkpoint → tap Check in → success → checkpoint marked completed.
2. Try Check in while far away → blocked with “Too far” reason.
3. Try Check in with poor accuracy (or simulate) → blocked with “Low GPS accuracy” reason.
4. Restart app → completion state persists.

---

### Week 5 — Completion + polish (basic rewards, basic tuning)

**Deliverables**
- Quest completion experience:
  - Detect “all checkpoints completed”
  - Show a **Quest Complete** modal (badge/reward placeholder)
  - Mark quest as completed in local progress
- Simple “rewards” (lightweight, local):
  - Store a basic **XP counter** (e.g., +100 per quest) and/or “badges earned”
  - Display on a simple Profile screen (can be minimal)
- UX polish for the core loop:
  - Clear “Next checkpoint” guidance in Quest Detail and Map
  - Better empty/error states (no location permission, location temporarily unavailable)
  - Small copy tweaks so the flow is obvious
- Optional analytics:
  - Define event names in code (even if no backend yet), e.g.:
    - `quest_started`
    - `checkpoint_checked_in_success`
    - `checkpoint_checked_in_fail`
    - `quest_completed`

**Exit criteria**
- Completing the last checkpoint reliably triggers completion state + modal.
- Quest cannot be “completed twice” accidentally (idempotent completion).
- Profile shows XP/badges consistent with completed quests.
- Core flow feels understandable without developer knowledge.

**Demo / manual test**
1. Complete final checkpoint → Quest Complete modal appears.
2. Return to Quests list → quest shows completed state.
3. Open Profile → XP/badge updated.
4. Try to re-check-in to completed checkpoints → no unintended side effects.

---

### Week 6 — Beta hardening (stability + testing only)

**Deliverables**
- Permission + location edge cases hardened:
  - “Permission denied” and “Permission revoked” handling
  - Graceful handling when location is unavailable / temporarily fails
- Real-device testing pass (minimum):
  - 1 iOS device + 1 Android device (or best available)
  - Document known issues and quick mitigations
- Performance + reliability improvements:
  - Reduce unnecessary location updates / rerenders
  - Basic loading states and error boundaries where needed
- UX/copy tightening:
  - Make the “pick quest → go → check in → progress” loop obvious
  - Improve feedback text for check-in failures (too far / poor accuracy)
- Release readiness checklist:
  - README-style “How to run” updated if needed
  - Simple test script: “How to test the Evergreen quest on-site”

**Exit criteria**
- No crashes during a full on-site quest run.
- Check-in flow behaves consistently across iOS and Android.
- All critical edge cases have a user-friendly message (not silent failure).
- You have a short, repeatable manual QA checklist for future content packs/cities.

**Demo / manual test**
1. Run the full Evergreen quest end-to-end outdoors on iOS + Android.
2. Toggle location permission off/on → app responds gracefully.
3. Simulate poor GPS accuracy → check-in blocks with clear reason.
4. Relaunch app mid-quest → progress intact and next checkpoint correct.

## Definition of Done (per issue/PR)
- Works on iOS and Android
- Manual test steps included in PR description
- No regressions in quest check-in flow
- Docs updated if behavior, data model, or tech decisions change
