# PR Response Doc — CineLog Watchlist Feature

## AI Usage
I used AI for step-by-step project support, codebase orientation, and checking whether my planned responses addressed the reviewer’s comments. I verified suggested changes against the actual CineLog code before applying them.

## Comment 1 — Rename
**What I did:** I renamed `save_to_watchlist()` to `add_to_watchlist()` in `services/watchlist_service.py`. This makes the watchlist service follow the same `verb_to_noun` naming convention used by existing service functions like `add_to_collection()` and `remove_from_collection()`.

**How I verified:** I updated the watchlist route import and call site in `routes/watchlist/watchlist.py`, then searched the project for `save_to_watchlist` to confirm no old references remained. I also ran `pytest tests/ -v` and confirmed the test suite passed.

## Comment 2 — Deduplication
**What I did:** I added deduplication logic to `add_to_watchlist()`. Before creating a new `WatchlistEntry`, the function now checks whether an entry already exists for the same `user_id` and `film_id`. If it does, the function raises `AlreadyInWatchlistError` instead of silently creating a duplicate.

**How I verified:** I followed the pattern from `add_to_collection()` and added `test_add_to_watchlist_duplicate_raises` in `tests/test_watchlist.py`. The test adds the same film twice, checks that the second call raises `AlreadyInWatchlistError`, and confirms that only one database row exists for that user and film.

## Comment 3 — Missing test
**What I did:** I created `tests/test_watchlist.py` and added a test for the nonexistent-film case. The test is modeled after `test_add_to_collection_nonexistent_film_raises` from `tests/test_collection.py`.

**How I verified:** I ran `pytest tests/test_watchlist.py -v` to verify the new watchlist tests directly, then ran `pytest tests/ -v` to confirm the full test suite passes.

## Comment 4 — Default visibility
**My position:** I kept `public=True` as the default for watchlist entries.

**Reasoning:** CineLog is described as a community film tracking app, so public watchlists fit the social and discovery purpose of the product. A public default makes it easier for users to share what they want to watch and lets other users discover films through community activity. This is consistent with a film tracking app where collections and watchlists can support recommendations and social browsing.

**Tradeoff acknowledged:** The privacy concern is real. Some users may treat a watchlist as personal, unfinished, or private. A private default would be safer from a privacy-first perspective. A stronger future design would allow callers to set visibility explicitly when adding a film. For now, I kept the existing default but documented the tradeoff.

## Comment 5 — Sort order
**My position:** I kept the current alphabetical sort order for `get_watchlist()`.

**Reasoning:** A watchlist is different from a watched collection. In the collection, date-added order makes sense because the user is reviewing a history of recently watched films. A watchlist is more like a saved planning list, where users may scan for a title they already know they want to watch. Alphabetical order gives stable, predictable results and makes the list easier to browse as it grows.

**Engagement with reviewer's point:** The reviewer’s date-added suggestion is reasonable because recently saved films may be the most relevant to the user. The tradeoff is that date-added ordering can make older saved films harder to find. For the current implementation, I chose alphabetical ordering because the endpoint does not yet support sorting options, and predictable browsing seemed more useful for a basic watchlist. I added a test documenting this behavior so the choice is explicit.

## Comment 6 — Rebase
**What conflicted:** During the rebase onto `upstream/main`, the watchlist work conflicted with the main branch refactor that migrated film IDs from integer IDs to UUID strings. The watchlist model was also missing after replaying the branch on top of the updated main branch.

**How I resolved it:** I restored `WatchlistEntry` in `models.py` using UUID-compatible fields. Specifically, `film_id` now uses `db.String(36)` to match the updated `Film.id`, and I added the missing `watchlist_entries` relationships on both `User` and `Film`. I also kept the unique constraint on `(user_id, film_id)` so duplicate watchlist rows are prevented at the model level as well as in service logic.

**How I verified no conflict remains:** I ran `pytest tests/ -v` and confirmed all 8 tests pass. I also checked the branch history against `upstream/main` and confirmed the branch has a linear commit history with no merge commits.

## PR Description
<!-- Written at the end — feature overview, design decisions, manual testing steps -->