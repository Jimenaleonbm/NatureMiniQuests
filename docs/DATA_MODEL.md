# Data Model — MVP

Goal: keep the schema simple, admin-curated, and scalable enough to add more quests later.

This document describes the **logical data model** — the entities, fields, and relationships the app needs — without prescribing a specific database or storage technology. The chosen persistence layer (e.g. a document database, relational database, or local on-device storage) should map naturally to these structures.

## Entities

### Quest (admin-curated; read-only for users)
Recommended fields:
- `id`: unique identifier
- `title`: string
- `description`: string
- `city`: e.g. "toronto"
- `isActive`: boolean
- `checkpointIds`: ordered list of checkpoint identifiers
- `completionRule`: "in_order" (MVP)
- `difficulty`: "easy" | "medium" | "hard" (optional)
- `estimatedMinutes`: number (optional)
- `bounds`: { north, south, east, west } (optional; for filtering/map framing)
- `updatedAt`: timestamp

### Checkpoint (admin-curated; read-only for users)
- `id`: unique identifier
- `name`: string
- `lat`: number
- `lng`: number
- `radiusM`: number (e.g., 50)
- `minAccuracyM`: number (e.g., 50)
- `hint`: string (optional)
- `city`: e.g. "toronto"
- `updatedAt`: timestamp

### User
- `id`: unique identifier (matches auth identity)
- `createdAt`: timestamp
- `lastSeenAt`: timestamp

### QuestProgress (per user, per quest — user writeable)
Stored under a per-user namespace (e.g. `users/{userId}/questProgress/{questId}`):
- `status`: "not_started" | "in_progress" | "completed"
- `completedCheckpointIds`: ordered list of completed checkpoint identifiers
- `lastCompletedAt`: timestamp (optional)
- `updatedAt`: timestamp

## Notes on scaling
- Prefer a **scoped progress record per quest** to avoid a single user record growing without limit.
- For higher integrity later:
  - Validate check-ins server-side (via a backend function or API) rather than trusting client writes.
  - Store a completion timestamp per individual checkpoint rather than a flat list.

## Data access rules (MVP guidance)
- Users can read quest and checkpoint data.
- Users can read and write only their own progress records.
- Admin-curated data (quests, checkpoints) must not be writable by regular users.
- Access rules should be implemented and kept strict from the start, regardless of the storage technology chosen.

> **Implementation note:** Once a persistence technology is chosen, this section should be updated with the specific schema mapping and access control configuration (e.g. database rules, API authorization policies, or row-level security).
