# Toronto Nature Mini Quests — MVP Plan (Don Valley / Evergreen Brick Works)

## Product statement
A mobile app (iOS + Android) that encourages outdoor exploration in Toronto by offering **admin-curated mini quests**. Users unlock progress by **manually checking in** at real-world **checkpoints** (trailheads/POIs). The map is **stylized and dark**, and content becomes meaningful through quests rather than grid-based reveal.

## MVP goals (what we must prove)
1. Users understand the quest loop quickly: *pick quest → go to checkpoint → check in → progress*. 
2. Check-ins feel reliable (distance + accuracy gates) and satisfying (immediate UI feedback).
3. A single content pack (Don Valley / Evergreen Brick Works) is enough to validate retention and fun.

## MVP scope (must-have)
### Platforms
- React Native app (Expo) supporting **iOS + Android**.

### Map & location
- Mapbox map with a **dark style**.
- Show user location (foreground only — **no background tracking** in MVP).
- Show checkpoint markers on the map.

### Quests
- Admin-curated quest list for Toronto.
- Quest detail view with ordered checkpoints.
- **Manual check-in** button.
- Ordered checkpoint completion rule (**in order**).
- Persist progress per user in Firestore.
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

## Recommended tech stack
- **React Native** with **Expo** (single codebase, iOS + Android)
- **Node.js** + **npm** (or yarn/pnpm) for dependency management
- **Mapbox** (map rendering + styling)
- **Firebase**
  - Auth (anonymous for MVP)
  - Firestore (quests/checkpoints + user progress)
  - Cloud Functions (optional; later for server-side validation)
  - Crashlytics/Analytics (optional but recommended)

## Milestones (6-week plan)
### Week 1 — Foundations
- React Native (Expo) project setup (`npx create-expo-app`)
- Firebase project + Auth (anonymous)
- Firestore connection
- Define data model + seed sample quest/checkpoints

### Week 2 — Mapbox + location
- Mapbox map screen with dark style
- Foreground location permission handling
- Show user puck
- Render checkpoint markers from Firestore

### Week 3 — Quest list + quest detail
- Quest list (active quests for Toronto)
- Quest detail (ordered checkpoints with status)
- Map highlights next checkpoint

### Week 4 — Manual check-in + progress persistence
- Distance + accuracy gate logic
- In-order checkpoint completion logic
- Save to `users/{uid}/questProgress/{questId}`
- UI feedback (success/failure)

### Week 5 — Completion + polish
- Quest completion state + reward modal
- Basic anti-cheat-lite (speed/teleport heuristics optional)
- Basic analytics events (quest started/completed)

### Week 6 — Beta hardening
- Permissions edge cases
- Device testing iOS/Android
- Crashlytics + basic performance checks
- Tighten copy/UI for clarity

## Definition of Done (per issue/PR)
- Works on iOS and Android
- Manual test steps included in PR description
- No regressions in quest check-in flow
- Docs updated if behavior/data model changes
