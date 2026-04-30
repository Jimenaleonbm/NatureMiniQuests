# Data Model — MVP (Firebase Firestore)

Goal: keep the schema simple, admin-curated, and scalable enough to add more quests later.

## Collections

### `quests/{questId}` (admin-curated; read-only for clients)
Recommended fields:
- `title`: string
- `description`: string
- `city`: "toronto"
- `isActive`: boolean
- `checkpointIds`: array<string> (ordered)
- `completionRule`: "in_order" (MVP)
- `difficulty`: "easy" | "medium" | "hard" (optional)
- `estimatedMinutes`: number (optional)
- `bounds`: { north, south, east, west } (optional; for filtering)
- `updatedAt`: timestamp

### `checkpoints/{checkpointId}` (admin-curated; read-only for clients)
- `name`: string
- `lat`: number
- `lng`: number
- `radiusM`: number (e.g., 50)
- `minAccuracyM`: number (e.g., 50)
- `hint`: string (optional)
- `city`: "toronto"
- `updatedAt`: timestamp

### `users/{uid}`
- `createdAt`: timestamp
- `lastSeenAt`: timestamp

### `users/{uid}/questProgress/{questId}` (user writeable)
- `status`: "not_started" | "in_progress" | "completed"
- `completedCheckpointIds`: array<string> (ordered or unordered; MVP can store ordered)
- `lastCompletedAt`: timestamp (optional)
- `updatedAt`: timestamp

## Notes on scaling
- Prefer a **subcollection per quest** for progress to avoid a single doc growing without limit.
- For higher integrity later:
  - Write check-ins via Cloud Functions to validate server-side.
  - Store completion timestamps per checkpoint rather than an array.

## Firestore security rules (MVP guidance)
- Users can read `quests` and `checkpoints`.
- Users can read/write only their own `users/{uid}` and `users/{uid}/questProgress/*`.
- Block writes to `quests` and `checkpoints` from clients.

(Implement rules once the Firebase project is wired; keep rules strict early.)
