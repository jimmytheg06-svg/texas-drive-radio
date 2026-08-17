# Texas Drive Radio

A mood-picker "radio" landing page for a Texas road-trip playlist site (the
adult-facing half of a two-site project — a kids' version is planned separately).

## What it does

- Hero: driver's-POV photo background (truck dashboard, Texas highway sunset)
  with the headline "How's the drive feeling?"
- Five mood presets (Texas Pride, Date Night Drive, Long Haul Throwback,
  Commute Chill, Need Energy), each with a 3-track playlist
- Optional "Add a route" layer — pairs a mood with a Texas route
  (Dallas → Houston, Austin → San Antonio, DFW → Hill Country,
  Houston → Galveston, Houston → El Paso), showing mileage/drive time
- A docked bottom player bar with real audio playback via the YouTube
  IFrame API — play/pause, skip, live progress bar, auto-advance to the
  next track when one ends
- A live-listener count badge for flavor (not real data — cosmetic only)

## Status / known issues

- **Playback only works when served over a real http(s) origin** (e.g. GitHub
  Pages, Netlify, or `python -m http.server` locally). It will NOT play when
  opened via `file://` or inside a sandboxed in-app preview — the YouTube
  IFrame API needs a proper origin to load.
- iOS Safari requires audio to start muted and get unmuted immediately after
  (autoplay policy) — this is already implemented (see `setPlaying()` and
  `loadCurrentTrack()` in the `<script>` block), but hasn't been fully
  verified on a real device over https yet since testing so far has been
  through the sandboxed preview.
- All 15 track YouTube video IDs were verified against real official
  uploads at the time this was built (Aug 2026) — worth a spot-check if
  picking this up much later, since videos occasionally get taken down or
  re-uploaded under a new ID.
- The hero photo is a single AI-generated image (user-generated via ChatGPT,
  confirmed original/owned by the user), extracted to `images/hero.jpg` and
  referenced from `index.html` via a normal `url(...)` background-image.

## Next steps (not yet built)

- Kids' sibling site (age/route-based picker instead of mood-based)
- Wire the route picker to actually adjust track selection/order (currently
  it only changes display text, not which tracks play)
- Real monetization pieces: display ads, affiliate links, Pinterest-friendly
  content pages per route

## File structure

```
index.html   — the entire site (HTML/CSS/JS, no build step)
images/hero.jpg — hero background photo
```
