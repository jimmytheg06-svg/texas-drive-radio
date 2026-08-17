# Roadtrip Radio

A road-trip picker "radio" landing page — pick a state, get a famous road
trip in it, and hear a playlist matched to that route's genre/vibe. Started
as a single-state Texas mood-picker; this is Phase 1 of expanding it into a
multi-state road-trip music product.

## What it does

- Hero: driver's-POV photo background with the headline "Where's the road
  taking you?"
- State filter chips (All states, TX, AZ, CA, CO, FL, LA) narrow the road
  trip list below
- 8 road trips across 6 states, each a real named route with mileage/drive
  time and a genre-matched 6-track playlist:
  - **Texas** — Texas Pride (DFW → Hill Country), Date Night Drive
    (Austin → San Antonio), Long Haul Throwback (Houston → El Paso)
  - **Arizona** — Route 66 Desert Run (Seligman → Kingman)
  - **California** — Pacific Coast Highway (San Francisco → Los Angeles)
  - **Colorado** — Rocky Mountain High (Denver → Trail Ridge Road)
  - **Florida** — Overseas Highway (Miami → Key West)
  - **Louisiana** — River Road to New Orleans (Baton Rouge → New Orleans)
- A docked bottom player bar with real audio playback via the YouTube
  IFrame API — play/pause, skip, live progress bar, auto-advance to the
  next track when one ends. The YouTube player itself is positioned
  off-screen (audio only) rather than shown in the player bar.
- A persistent "find on Spotify" link in the player bar for whatever
  track is currently loaded — a fallback source for the exact same song
  if the YouTube video won't play. It's a search link built from
  title + artist, not an embedded Spotify player (see below for why).
- A Screen Wake Lock while music is playing, so the phone's idle-timeout
  doesn't lock the screen and stop playback (see below for what this
  does and doesn't cover).
- A live-listener count badge for flavor (not real data — cosmetic only)

## Status / known issues

- **Track YouTube IDs are NOT verified.** This build environment has no
  network access to YouTube (or to the reference site that inspired this
  pivot), so the video IDs are best-effort picks from training knowledge,
  not confirmed live. Every playlist is now 6 tracks (48 total across 8
  road trips) — the original 15 Texas track IDs *were* verified in an
  earlier session and are more trustworthy; the ~33 tracks added since
  (5 new states plus 3 extra per playlist) are not. **Before showing this
  to anyone outside the team, spot-check every track** — open each one and
  confirm it's the right song and actually embeddable. A bad ID just
  auto-skips to the next track (see `onError` in the `<script>` block) so
  nothing breaks, but a skipped/wrong song undercuts the "music matches
  your exact route" pitch this product is built on.
- **Playback only works when served over a real http(s) origin** (e.g.
  GitHub Pages, Netlify, or `python -m http.server` locally). It will NOT
  play when opened via `file://` or inside a sandboxed in-app preview.
- iOS Safari requires audio to start muted and get unmuted immediately
  after (autoplay policy) — implemented in `setPlaying()` and
  `loadCurrentTrack()`, but not fully verified on a real device yet.
- The hero photo is a single AI-generated image (user-generated via
  ChatGPT, confirmed original/owned by the user), stored at
  `images/hero.jpg`.
- Video ID rot: songs occasionally get taken down or re-uploaded under a
  new ID — worth a periodic spot-check regardless of the point above.
- **The Screen Wake Lock only prevents idle-timeout locking, not true
  background audio.** It requests `navigator.wakeLock` while a track is
  playing, releasing it on pause — this stops the phone from auto-locking
  from sitting idle (e.g. mounted on a dash), which is what was actually
  reported ("when the phone locks the music stops"). It does **not** keep
  audio playing if the user manually switches to another app (Maps,
  texting, etc.) or presses the physical lock/power button — that's the
  cross-origin-iframe background-audio limitation described above, and
  would need the bigger architectural change (native app wrapper, or a
  licensed streaming SDK with real OS-level media session support) to
  actually fix. Wake Lock support also isn't universal (works on Android
  Chrome broadly; iOS Safari requires 16.4+; in-app browsers like the ones
  inside Messages/Instagram/TikTok often lack it entirely). Unsupported
  browsers now show a visible banner ("this browser can't keep the screen
  awake...") above the player bar the first time playback starts, instead
  of failing silently — this was added after an initial report of the
  fix "still" not working, to make it possible to tell whether that's an
  unsupported-browser case or an actual bug.
- **YouTube ads before tracks are expected, not a bug.** Pre-roll ads on
  embedded videos are inserted by YouTube on the video owner's behalf —
  this app has no control over them, and that's by design: the ad revenue
  is how artists/labels get paid for this app using their music for free
  instead of needing a paid sync license. There is no compliant way to
  strip or skip these ads; doing so would violate YouTube's embed Terms
  of Service and risk the embed (or domain) getting throttled/blocked
  entirely. Whether a given track has an ad depends on how that specific
  video was monetized, so it varies by song. YouTube Premium removes ads,
  but only for a listener's own account — not something the site can turn
  on for them. The real fix, if this becomes a dealbreaker, is a paid
  sync license per song or a licensed streaming API (Spotify/Apple Music)
  requiring the listener's own account — both real-money, Phase-3-or-later
  business decisions, not a code fix.
- **Why the Spotify link is a search link, not an embedded player**:
  Spotify's embed widget needs an exact 22-character track ID per song.
  This build environment has no network access to Spotify (same
  restriction as YouTube), and unlike YouTube IDs there's no reasonable
  basis to guess those — fabricating them would mean an almost-certainly-
  wrong ID for every track, which is worse than not having the feature.
  A title+artist search link is always correct by construction. If a
  future session has Spotify access, look up real track IDs and swap
  this for an actual embedded fallback player.

## Roadmap (from the Phase 1 gap assessment)

- **Phase 1 (this build)**: 6 states, 8 road trips, same single-file
  architecture as the original Texas build — proves the "pick a route, hear
  its exact vibe" hook works across multiple genres before investing in
  more content.
- **Phase 2**: pull `roadTrips` out of inline JS into a real JSON data file
  (or small CMS) so adding states doesn't mean hand-editing a single large
  HTML file.
- **Phase 3**: monetize — affiliate links (road-trip gear, hotels along
  the route) first, display ads once there's real traffic, SEO-friendly
  per-route content pages for organic/Pinterest discovery.
- **Phase 4**: fill out remaining states, only once curation pace and
  engagement are proven.

## File structure

```
index.html      — the entire site (HTML/CSS/JS, no build step)
images/hero.jpg — hero background photo
```
