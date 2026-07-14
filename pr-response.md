# PR Response Doc — CineLog Watchlist Feature

## AI Usage

<!-- Fill in at the end — how you used AI tools during this project -->

## Comment 1 — Rename

**What I did:** I renamed the `save_towatchlist` function to `add_to_watchlist`, along with all references to it.
**How I verified:** I performed a project-wide search and replace, and verified that all references to `save_towatchlist` - in `watchlist.py` and `watchlist_service.py` - were changed to `add_to_watchlist` and ensured the app still ran as expected and my test for `add_to_watchlist` passed.

## Comment 2 — Deduplication

**What I did:** I added deduplication to the `add_to_watchlist` function, which raises an `AlreadyInWatchlistError` if the film is already in the user's watchlist. I also added an exception handler to the `add_film` route in `watchlist.py` to return a 400 error if the film is already in the user's watchlist, similar to how it's handled in `add_film` in `collection.py`.
**How I verified:** I added a test for the new error, and verified that it was raised when adding the same film twice.

## Comment 3 — Missing test

**What I did:** I added a test for the `add_to_watchlist` function that verifies that it raises a `FilmNotFoundError` if the film_id does not exist in the database.
**How I verified:** I added a test for the error, and verified that it was raised when adding a film that doesn't exist in the database.

## Comment 4 — Default visibility

**My position:** I think we should default to private watchlists for now, and add a feature to make them public later.
**Reasoning:** Watchlists are more sensitive than collections (as it shows user's intent) and it also means users might be sharing their watchlists before they are necessarily ready if the default is public.
**Tradeoff acknowledged:** We are advertising as a community film tracker, so sharing watchlists will be impossible within the app for now and should be added as a feature later to be more inline with our app's purpose. Collections should remain public for now to keep with the community idea of the app.

## Comment 5 — Sort order

**My position:** I agree with the reviewer that the default sort order should be "date added" (newest first).
**Reasoning:** It gives the user immediate feeback on what films they just added to their watchlist (as it will be at the top of the list). Oldest entries sink to the bottom, so users can see what films they have been putting off watching.
**Engagement with reviewer's point:** It makes sense for a user to quickly see what films they most recently added to their watchlist, as well as, see which ones have been in the watchlist the longest. It gives a better reference for what the user has been interested in over time and what they have postponed watching for some time. In the future, as film lists grow, adding a search/filter could make it more scannable for a specific film.

## Comment 6 — Rebase

**What conflicted:** During the rebase, the .gitignore file had a conflict with pytest_cache not being ignored. I added the pytest_cache directory to the .gitignore and kept it in my rebase as that directory shouldn't be committed. There was also a conflict with changing WatchlistEntries to default to private and changing the sort order to newest-first. I also kept both of these changes in my rebase as explained in my above PR comments. Lastly, there were semantic conflicts with WatchlistEntry that I now needed to resolve where it referenced the old film_id as a db.Integer.
**How I resolved it:** I kept my updated .gitignore in the rebase, along with the updated privacy default and sort order as these were new design decisions I made and supported above. I also resolved the semantic conflicts in the WatchlistEntry modelby changing the film_id to a db.String(36) to match the new UUIDs being used for the Film model.
**How I verified no conflict remains:** I made sure the app still ran as expected all tests in the test suite passed as well. I also verified there were no merge conflicts left during the rebase and that the conflicts were resolved. Lastly, I checked all references to film_id in my chanages refernced the new UUIDs (such as in the docstring in `watchlist_service.py`).

## Stretch Feature - Remove from watchlist

**What I did:** I added a new route to remove a film from a user's watchlist ( `DELETE /watchlist/<user_id>/remove` ). Added the function, `remove_from_watchlist`, to the `watchlist_service.py` file. Lastly, I added two tests for the new route, one for the happy path, `test_remove_from_watchlist_removes_entry`, and one for the error path, `test_remove_from_watchlist_nonexistent_film_raises`.
**How I verified:** I made sure the app still ran as expected and all tests in the test suite passed.

## PR Description

<!-- Written at the end — feature overview, design decisions, manual testing steps -->
