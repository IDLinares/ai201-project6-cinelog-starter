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

**What I did:**
**How I verified:**

## Comment 4 — Default visibility

**My position:**
**Reasoning:**
**Tradeoff acknowledged:**

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
