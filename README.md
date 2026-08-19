# CineLog

A community film-tracking API where users can browse films, log what they have watched, build collections, and maintain a personal watchlist.

### CodePath AI201 · Project 6

Developed as part of CodePath AI201 through a simulated code-review workflow. The existing Flask application and initial watchlist feature branch were provided; my work focused on addressing maintainer feedback, improving the watchlist implementation, adding test coverage, resolving issues introduced by an upstream UUID refactor, and documenting design decisions.

---

## What I Worked On

I completed and refined CineLog's watchlist feature while responding to six simulated maintainer review comments.

### Watchlist Service

Implemented and refined the service-layer logic for adding films to a user's watchlist and retrieving saved films.

The completed feature:

- Adds films to a user's watchlist
- Retrieves saved watchlist entries
- Prevents duplicate film entries
- Validates that requested films exist
- Supports public/private watchlist entries
- Returns watchlist films in a stable alphabetical order

### Duplicate Prevention

The initial implementation allowed the same film to be added to a user's watchlist more than once.

I updated the service logic to detect existing entries and raise a dedicated `AlreadyInWatchlistError` rather than creating duplicates.

### Naming and Codebase Consistency

I updated service naming and implementation details to follow the conventions documented in `CONTRIBUTING.md` and match patterns already used elsewhere in CineLog.

This required working within an existing architecture rather than introducing a separate design for the new feature.

### UUID Refactor Resolution

While the watchlist work was in progress, the base application migrated film IDs from integers to UUID strings.

I rebased the feature branch onto the updated `main` branch, restored the watchlist model using UUID-compatible fields, and updated the implementation so the feature remained compatible with the refactored data model.

### Test Coverage

I added dedicated service tests covering watchlist behavior, including:

- successfully adding a film
- preventing duplicate entries
- handling nonexistent films
- retrieving watchlist entries
- preserving the intended alphabetical ordering

After the rebase and UUID updates, the full test suite passed.

---

## Design Decisions

Two review comments involved product and API design choices rather than simple bug fixes.

### Public Watchlists by Default

I kept `public=True` as the default visibility for watchlist entries.

CineLog is designed as a community film-tracking application, so public watchlists support sharing and film discovery. The privacy tradeoff and reasoning are documented in [`pr-response.md`](pr-response.md).

### Alphabetical Watchlist Ordering

Collections are returned newest-first because they represent viewing history.

For watchlists, I retained alphabetical ordering by film title because a watchlist behaves more like a saved planning list than a chronological activity feed. I also added a test to make this behavior explicit.

---

## Code Review Workflow

The project simulated contributing a feature to an existing production-style codebase.

My workflow included:

1. Reading the repository's contribution guidelines and existing architecture.
2. Reviewing six maintainer comments on the feature implementation.
3. Tracing each comment to the relevant model, route, or service logic.
4. Updating the implementation while preserving existing conventions.
5. Adding tests for behavior introduced or clarified during review.
6. Rebasing the feature branch after an upstream UUID refactor.
7. Resolving compatibility issues and rerunning the full test suite.
8. Documenting each review response and design decision.

Detailed responses to the review comments are available in [`pr-response.md`](pr-response.md).

---

## Tech Stack

- Python
- Flask
- SQLAlchemy
- SQLite
- Pytest
- REST APIs

---

## API Overview

### Films

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/films/` | List films with optional genre and year filters |
| `GET` | `/films/<film_id>` | Retrieve a film by UUID |

### Collection

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/collection/<user_id>` | Retrieve a user's watched-film collection |
| `POST` | `/collection/<user_id>/add` | Add a film to the collection |
| `DELETE` | `/collection/<user_id>/remove` | Remove a film from the collection |

### Watchlist

The watchlist feature adds endpoints for saving films and retrieving a user's watchlist, backed by dedicated service-layer validation and duplicate handling.

---

## Project Structure

```text
cinelog-watchlist/
├── app.py
├── models.py
├── routes/
│   ├── films.py
│   ├── collection.py
│   └── watchlist.py
├── services/
│   ├── collection_service.py
│   └── watchlist_service.py
├── tests/
│   ├── test_collection.py
│   └── test_watchlist.py
├── CONTRIBUTING.md
├── pr-response.md
└── requirements.txt
```

---

## Running Locally

Install dependencies:

```bash
pip install -r requirements.txt
```

Run the application:

```bash
python app.py
```

The application starts locally on port `5000`.

Run the full test suite:

```bash
pytest tests/
```

---

## CodePath Project Context

This repository originated from the CodePath AI201 Project 6 simulated code-review exercise. The base CineLog application and initial feature branch were provided. My contribution focused on addressing review feedback, refining and testing the watchlist feature, maintaining consistency with the existing codebase, resolving the UUID migration during rebase, and documenting the resulting engineering decisions.
