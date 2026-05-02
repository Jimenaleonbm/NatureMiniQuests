# Copilot Cloud Agent instructions for this repo

## What this repository is

**NatureMiniQuests** is currently a *planning/docs-only* repository for a mobile MVP called **“Toronto Nature Mini Quests”** (Don Valley / Evergreen Brick Works content pack).

The docs describe:
- The **product concept**: admin-curated mini quests with ordered checkpoints and **manual check-in**.
- A **Firebase/Firestore**-backed data model for quests/checkpoints and per-user progress.
- A sample **quest content pack** for Don Valley / Evergreen Brick Works.

> Important: As of commit `a3f32d5` (default branch `main`), there is **no application source code** and **no build/test/CI configuration** in the repository. The only tracked files are Markdown documents.

## High-level repo facts

- **Repo size**: very small (GitHub reports ~7 KB).
- **Type**: documentation/specification repository (Markdown only).
- **Languages**: Markdown.
- **Target runtime/framework (intended, not yet implemented)**: mobile app for iOS + Android. The MVP plan currently references **Flutter**, but PRs may update docs to **React Native**—treat this as a *product decision in docs*, not as an implemented code migration.
- **Backend (intended)**: Firebase (Auth + Firestore), Mapbox for maps.

## Project layout (trust this; don’t waste time searching first)

### Repository root
- `README.md` — currently just the project title.
- `docs/` — all substantive documentation.

### `docs/`
- `docs/README.md` — docs index.
- `docs/MVP.md` — MVP plan, tech stack, milestones, definition of done.
- `docs/USER_STORIES.md` — MVP user stories and acceptance criteria.
- `docs/DATA_MODEL.md` — Firestore collections and fields + security rules guidance.
- `docs/QUEST_CONTENT_DON_VALLEY.md` — sample quest pack content and authoring checklist.

### What is *not* present (as of now)
- No `.github/workflows/*` (no CI).
- No `.github/` directory at all (404 when listing).
- No `package.json`, `android/`, `ios/`, `expo/`, `src/`, `lib/`, etc.
- No lint/test/build tool configs.

If you think you need any of the above, **double-check whether the repo has changed since these instructions were written**, but assume they are correct until proven otherwise.

## Build / test / run / lint instructions

There is currently **nothing to build, run, test, or lint**.

### Always do this instead
- If your change is documentation-only (most likely):
  - Validate with basic Markdown hygiene:
    - Ensure links are relative and correct (especially within `docs/`).
    - Keep headings consistent and avoid broken internal anchors.
    - Prefer consistent terminology across docs.

### If you add code in the future
If the repository later gains a real app codebase, **update this file** to include:
- required versions (Node, Java, Xcode, etc.)
- exact commands (`npm i`, `npm test`, etc.)
- any platform prerequisites (Android SDK, CocoaPods)
- CI workflow names and how to replicate locally

Until then, do **not** invent build commands in PRs.

## Validation and CI

- There are **no GitHub Actions workflows** at the moment.
- Validation is therefore primarily **content correctness and consistency**.

Recommended manual validation before proposing doc changes:
1. Search for inconsistent terminology (e.g., “Flutter” vs “React Native”) and update all relevant docs.
2. Ensure Firebase/Firestore naming is consistent (collection paths, field names).
3. Ensure MVP constraints remain consistent across documents (manual check-in, in-order checkpoints, foreground-only location).

## Domain rules (to avoid incorrect changes)

These invariants appear across multiple documents and should stay consistent unless the user explicitly changes the product requirements:
- **Manual check-in** (button-driven), not passive/background tracking.
- Checkpoints completed **in order**.
- Quests/checkpoints are **admin-curated**.
- Progress persisted under a per-user path like `users/{uid}/questProgress/{questId}`.
- Map/Location requirements: show user location; MVP is **foreground only**.

## How to work efficiently in this repo

- Start by reading `docs/README.md` to see the canonical doc set.
- Then open `docs/MVP.md` for the authoritative statement of platform/tech stack and milestones.
- Use repo-wide search only if instructions above are incomplete or you suspect the repository has materially changed.

## Notes for PRs

When submitting documentation PRs:
- Include a brief checklist of files updated.
- Call out any intentionally preserved assumptions vs newly changed decisions.
- Avoid adding speculative code or commands that do not exist in the repo.
