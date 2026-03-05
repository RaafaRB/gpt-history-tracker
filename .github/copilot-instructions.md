# Copilot Instructions for gpt-history-tracker

## Repository Purpose

This repository is a **GPT/AI history tracker** — a living archive of AI-assisted development sessions for software projects. Each session is recorded as a **HIT** (History Iteration Tracker) document: a structured markdown file that captures what was decided, built, or validated in a single development session.

The primary project tracked here is **Pais de Planta**, a plant care application with a FastAPI (Python) backend and a React frontend.

---

## Repository Structure

```
gpt-history-tracker/
├── README.md
└── projects/
    └── pais-de-planta/
        ├── INDEX.md           # Master index of all HITs for the project
        ├── PROJECT_BACKLOG.md # Planned features and schema decisions
        └── HIT_XXX-TOPIC.md  # Individual session history files
```

---

## HIT Document Conventions

### File Naming

HIT files follow this naming pattern:

```
HIT_<zero-padded-number>-<TOPIC-SLUG>.md
```

Examples:
- `HIT_000-BASELINE.md`
- `HIT_029-AUTH-JWT-REFRESH-MULTIUSER.md`
- `HIT_034-DELETE-GARDEN-PLANT-CASCADE.md`

Use uppercase slugs with hyphens. If the topic is not yet defined, use `HIT_XXX.md` or `HIT_XXX-000.md` as a placeholder.

### HIT File Content

Each HIT document should summarize, in clear prose:
- **What was the objective** of the session
- **What was implemented or decided** (models, endpoints, services, frontend changes)
- **Key architectural decisions** and the reasoning behind them
- **Validation / smoke testing** results (what was confirmed to work)
- Any **caveats, cleanup, or future considerations**

HITs are narrative, not code — they describe outcomes in plain language. They are immutable records once finalized.

### INDEX.md

`INDEX.md` lists all HITs in chronological order with a short description. When adding a new HIT, append it to `INDEX.md` using the same format as existing entries:

```
- HIT_XXX-TOPIC - Short description of what was done
```

---

## Project: Pais de Planta

**Stack:**
- Backend: Python / FastAPI / SQLAlchemy / APSScheduler / Firebase Admin SDK
- Frontend: React (with `apiFetch` wrapper for authenticated requests)
- Auth: JWT access tokens + revocable refresh tokens
- Notifications: Firebase Cloud Messaging (FCM) via APSScheduler

**Domain concepts:**
- `User` — registered users with email/password auth
- `Plant` — canonical plant catalog (immutable/shared)
- `GardenPlant` — user-owned instance of a plant in their garden
- `GardenPlantDiagnostic` — health diagnosis for a garden plant
- `GardenPlantTreatment` — treatment snapshot linked to a diagnostic
- `CareSchedule` — scheduled care reminders for a garden plant
- `Notification` — persistent notification record for push delivery
- `UserDevice` — registered FCM device tokens per user

**Key rules:**
- Canonical tables (`plants`, `plant_diseases`, `disease_treatments`) must never be modified or deleted by user actions.
- All user-owned resources must enforce ownership before read/write/delete.
- Database cascade deletes must be explicit (`ondelete="CASCADE"` in SQLAlchemy) and scoped only to user-owned entities.
- The `apiFetch` wrapper on the frontend handles bearer token attachment and 401-triggered refresh rotation automatically.

---

## How to Add a New HIT

1. Determine the next sequential number (check `INDEX.md`).
2. Create `projects/pais-de-planta/HIT_<number>-<TOPIC>.md` with a narrative summary of the session.
3. Update `projects/pais-de-planta/INDEX.md` to include the new entry.
4. Optionally update `PROJECT_BACKLOG.md` if backlog items were completed or added.

---

## Writing Style

- Write in clear, technical English.
- Be concise but complete — each HIT should be understandable without reading the code.
- Avoid bullet-only summaries; prefer connected prose with technical detail.
- Include both backend and frontend impacts when both were changed.
