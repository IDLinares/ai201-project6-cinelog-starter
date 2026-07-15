# PR Response Doc — CineLog Watchlist Feature

## AI Usage

**What I gave AI:** I asked Claude to review and provide counterpoints to my initial reasoning about keeping the WatchlistEntry models public by default.
**What I changed:** Its reasoning resonated with me, sepcifically about sensitivity and allowing for explicity consenting, and I decided to keep the default as private. If I were fully implementing this feature, I would add a way to toggle visiblity on a per-entry basis, so when a user is ready to share their watchlists, they can do so.

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

**What I did:** I added a new route to remove a film from a user's watchlist ( `DELETE /watchlist/<user_id>/remove` ). Added the function, `remove_from_watchlist`, to the `watchlist_service.py` file. Lastly, I added two tests for the new route, one for the happy path, `test_remove_from_watchlist_removes_entry`, and one for the error path, `test_remove_from_watchlist_nonexistent_film_raises`. It follows the pattern used for the collection feature, so a `NotInWatchlistError` is raised if the film is not in the user's watchlist.
**How I verified:** I made sure the app still ran as expected and all tests in the test suite passed.

## Stretch feature - Edge Case Test

**What I did:**: I added an edge case test like the one for collections to check if a watchlist is in order since I decided the sort order of the watchlist would be "date added" (newest first). The test is `test_get_watchlist_returns_newest_first` in the `test_watchlist.py` file. I also needed to add a new SQLAlchemy relationship to the Film model the same as the one used for the collection feature and test but for watchlists.
**How I verified:** I made sure the app still ran as expected and all tests in the test suite passed.

## PR Description

### Overview

This PR adds a **watchlist feature** to CineLog, allowing users to save films they want to watch in the future. This is distinct from the existing collection feature, which tracks films a user has already watched and rated. A watchlist entry stores the film, the date it was added, and a visibility flag indicating whether the entry is public or private.

The feature includes three endpoints, a new `WatchlistEntry` model, a full service layer with error handling, and a test suite covering the happy path, deduplication, error cases, and sort order.

---

### New Endpoints

| Method | Endpoint                      | Description                                                        |
| ------ | ----------------------------- | ------------------------------------------------------------------ |
| GET    | `/watchlist/<user_id>`        | Returns the user's watchlist, sorted by date added (newest first)  |
| POST   | `/watchlist/<user_id>/add`    | Adds a film to the watchlist. Body: `{ "film_id": "<uuid>" }`      |
| DELETE | `/watchlist/<user_id>/remove` | Removes a film from the watchlist. Body: `{ "film_id": "<uuid>" }` |

Error responses:

- `404` if the `film_id` does not exist in the database
- `400` if the film is already in the user's watchlist (add)
- `404` if the film is not in the user's watchlist (remove)

---

### Design Decisions

#### Default watchlist visibility: private (`public=False`)

Unlike a collection (past behavior), a watchlist reflects a user's intent on what they plan to watch which can be more sensitive. Defaulting to public would mean users are sharing before they have decided to, which is a poor default for a new, undocumented feature. The tradeoff is that watchlist sharing is not usable out of the box; a per-entry visibility toggle should be added in a follow-up to align with CineLog's community purpose.

#### Sort order: date added, descending (newest first)

The watchlist is sorted by `date_added` descending so the most recently added films appear at the top. This gives users immediate feedback on what they just added, and older entries naturally sink to the bottom, surfacing films they have been putting off the longest. It gives insight into the user's interests over time. A search or filter could complement this in the future for larger watchlists.

---

### Manual Testing

**Prerequisites:** App running locally (`python app.py`), a REST client (curl, Postman, or similar), and a valid `user_id` and `film_id` from the database. You can retrieve films via `GET /films/`.

#### 1. Add a film to the watchlist

```text
POST /watchlist/<user_id>/add
Body: { "film_id": "<valid-film-uuid>" }

Expected: 201 with the new WatchlistEntry as JSON
```

#### 2. View the watchlist

```text
GET /watchlist/<user_id>

Expected: 200 with a list of film dicts, each including date_added and public fields,
sorted newest first
```

#### 3. Add the same film again (deduplication)

```text
POST /watchlist/<user_id>/add
Body: { "film_id": "<same-film-uuid>" }

Expected: 400 with error "Film '...' is already in this user's watchlist"
```

#### 4. Add a film with a nonexistent ID

```text
POST /watchlist/<user_id>/add
Body: { "film_id": "00000000-0000-0000-0000-000000000000" }

Expected: 404 with error "No film found with id '...'"
```

#### 5. Remove a film from the watchlist

```text
DELETE /watchlist/<user_id>/remove
Body: { "film_id": "<valid-film-uuid>" }

Expected: 200 with message "Removed from watchlist"
```

#### 6. Remove a film that is not in the watchlist

```text
DELETE /watchlist/<user_id>/remove
Body: { "film_id": "<film-uuid-not-in-watchlist>" }

Expected: 404 with error "Film '...' is not in this user's watchlist"
```

#### 7. Verify sort order

Add two films with a delay between them (or use the test suite's `test_get_watchlist_returns_newest_first`). Confirm the most recently added film appears first in the `GET /watchlist/<user_id>` response.
