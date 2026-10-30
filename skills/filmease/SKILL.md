---
name: filmease
description: Use FilmEase for grounded movie discovery, streaming availability, ratings, reviews, watchlists, viewing history, taste profiles, and streaming preferences.
---

# FilmEase

Use FilmEase as the factual and durable movie layer while you remain the conversational model.

## Discovery

1. Interpret the user's full conversation yourself.
2. Prefer `discover_movies` and fill its structured intent directly. Write a rich `semanticSearchText`; do not merely repeat the raw query.
3. Resolve user-named anchor films with `search_films`, then put their canonical IDs in `explicitMovieAnchorIds`. Use string `explicitMovieAnchors` only as a compatibility fallback. Put optional stylistic examples you inferred in `inferredMovieAnchors`.
4. Distinguish `viewerRegion` (streaming availability) from `originCountry` (where a film is from).
5. Select and explain the best few candidates from FilmEase results. Do not name a movie as a FilmEase recommendation unless it appeared in tool output.
6. Never claim streaming availability from memory. Use only FilmEase provider data.
7. Treat one successful `discover_movies` call as complete. Each candidate's authoritative `personalState` includes seen and watchlist membership. When `excludeWatched=true`, FilmEase has already excluded explicitly watched, rated, reviewed, and recorded-watch movies. Do not fetch the taste profile, resolve returned titles again, or call `get_my_movie` per candidate. Returned `movieId` values are canonical.

Use `search_films` to resolve titles supplied by the user before a personal-state read or write. Never re-resolve candidates returned by `discover_movies`. If multiple plausible results are returned for a user-supplied title, ask which film they meant.

For any request asking whether or where a specifically named movie is streaming, call `check_title_streaming_availability` directly. It owns exact title/year resolution, saved preferences, and canonical-ID availability lookup. Never route an exact-title availability request through `discover_movies`.

## Personal movie state

- Use `get_my_movie` for rating, review, watchlist/seen state, actual watch dates, rewatches, companions, and availability on the user's saved services. Its `streamingAvailability` is authoritative for that exact canonical movie ID.
- Use `list_my_movies` for watchlist, rated, reviewed, and watched lists. Keep pages modest and follow `nextOffset` when the user wants more.
- Do not describe `addedAt` as a watch date. Only entries in `watches` are viewing events.
- A review is private unless the user explicitly asks to publish it.

## Writes and confirmation

- Resolve the exact movie first.
- Confirm before clearing a rating/review, removing a watchlist entry, or deleting a watch record.
- `record_watch` may also save an optional rating and review atomically.
- Convert relative dates such as “last night” using the user's timezone and send an ISO timestamp.
- If the user provides no watch date, state that the current time will be used before recording.
- Companion labels are private. Never infer or attach another FilmEase account by arbitrary user ID.

## Preferences and personalization

- `get_my_streaming_preferences` returns the user's viewer region and provider keys.
- `set_my_streaming_preferences` replaces the complete saved set. Read existing preferences first unless the user clearly supplied the complete replacement.
- Use `get_my_taste_profile` with a bounded limit. Do not request a user's entire history when a summary will do.
- Prefer `discover_movies` when the live conversation contains rich context you can use to rank candidates. Use `get_personalized_recommendations` for FilmEase's existing durable recommendation session. If it returns `needsTasteProfile`, use `discover_movies` for the current request and offer to build the user's taste profile afterward.

## Privacy

Never request, accept, or expose a raw `userId`. Authentication determines the account. Do not reveal private reviews, ratings, taste evidence, or companion information belonging to another user.
