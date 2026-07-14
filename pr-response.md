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
**How I verified:** I added a test for theerror, and verified that it was raised when adding a film that doesn't exist in the database.

## Comment 4 — Default visibility

**My position:** I think we should default to private watchlists for now, and add a feature to make them public later.
**Reasoning:** Watchlists are more sensitive than collections (as it shows user's intent) and it also means users might be sharing their watchlists before they are necessarily ready if the default is public.
**Tradeoff acknowledged:** We are advertising as a community film tracker, so sharing watchlists will be impossible within the app for now and should be added as a feature later to be more inline with our app's purpose. Collections should remain public for now to keep with the community idea of the app.

## Comment 5 — Sort order

**My position:**
**Reasoning:**
**Engagement with reviewer's point:**

## Comment 6 — Rebase

**What conflicted:**
**How I resolved it:**
**How I verified no conflict remains:**

## PR Description

<!-- Written at the end — feature overview, design decisions, manual testing steps -->
