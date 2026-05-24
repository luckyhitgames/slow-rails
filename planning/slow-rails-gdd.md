---
type: GDD
status: draft
created: 2026-05-18
updated: 2026-05-24
working_title: slow-rails
jam: GameDev.tv Jam 2026
jam_page: https://itch.io/jam/gamedevtv-jam-2026
jam_window: 2026-05-15 → 2026-05-25 21:00 UTC
---

# Slow Rails — GameDev.tv Jam 2026 GDD

Working title — rename freely. Live calendar in the Version
ladder section below.

## Pitch

Place tiles to grow a landscape with a trail winding through
it. Your traveler walks one tile per day, stopping at points of
interest along the way. Scoring rewards the **best journey** —
variety of stops, set bonuses, the right detours — not the
fastest path. A solo Tokaido in a painterly landscape: slow is
the point.

> **Pivot note (2026-05-22):** original concept was a train on
> rails. Theme reframed to hiking when the Kenney Map Pack —
> the chosen v0.4 art source — turned out to have no vehicles.
> Mechanics (tile-laying, one-tile-per-day movement, N/E/S/W
> edge connections, POI scoring) are unchanged. Code-level
> identifiers (`rail_edges`, etc.) keep their original names
> through v0.3 and may be renamed during the v0.4 vibe pass.
> Repo name `slow-rails` is intentionally not yet renamed; see
> LUC-40 for pivot tracking.

## Inspirations

- **Dorfromantik** (Toukana Interactive) — tile-laying puzzle
  where edge-matched tiles extend a procedural landscape.
  Single-player, peaceful, score-chasing. **Primary mechanical
  cousin** — the tile-laying loop. Their hex geometry isn't
  load-bearing for us; the painterly landscape *look* is what
  we're borrowing.
- **Tokaido** (board game) — slowness rewarded; end-of-game
  tableau scoring across categories. **Primary tonal cousin.**
- **Mini Metro** (Dinosaur Polo Club) — minimalist *UX*
  inspiration (clean interactions, no chrome). **NOT a visual
  reference** — the painterly direction is the Mini Metro
  escape (see Differentiation below).
- **Ticket to Ride** / **Carcassonne** — tile / route placement
  as the playful verb.

## Theme fit

Jam theme: **Connections** (confirmed).

The trail network IS the literal connection layer. Tiles are
landscape; paths on tile edges are the connection medium. The
player's verb is *connect* (place a tile that joins another
tile's path); the traveler *walks* the connections you've made.
End-game scoring rewards the *quality* of those connections,
not their density.

Two visible progression analogs stack: **network growth** (the
trail extends across the map) + **territory coverage** (the map
fills in as tiles are placed).

## Differentiation from Mini Metro

Mini Metro overlap was the original concept's biggest design
risk — both games are "connect things with lines." The hiking
reframe widens the gap further. The escape lives in three
places:

1. **Mechanically:** Mini Metro is about *flow* (passengers
   shuttling in real-time, throughput optimization). This game
   is about *journey* (single trip, contemplative, end-game
   tableau).
2. **Visually:** Mini Metro is clinical minimalism (white field,
   primary colors, geometry). This game is painterly landscape
   built from Kenney's Map Pack (forest, mountain, water,
   plain, trail). Square tiles with painted terrain + a hiker
   icon read as a walking map, not as a transit diagram.
3. **Tonally:** Mini Metro has tension (line overload,
   passenger grumbling, real-time pressure). This game has zero
   tension — turn-based, no fail state, the journey always
   completes.

## Design decisions (resolved)

- **Path-laying mechanic: tile-based** (Dorfromantik lineage).
  Tiles drawn from a deck; each tile has path segments on some
  edges; placement requires edge alignment with an adjacent
  placed tile. POIs live on tiles. *Why:* simpler engineering,
  naturally landscape-flavored, escapes Mini Metro visually.
- **Commit-as-you-go (no Build/Journey phase split).** Place a
  tile → traveler walks onto it next day. *Why:* halves the UI
  work; very natural with tile-laying.
- **Square tiles** (over hex). *Why:* Godot's `TileMap` handles
  squares natively; `(x, y)` coords beat axial hex math by a
  meaningful margin for v0.1 implementation time (~3–5 hours
  saved → v0.3 polish budget). The landscape aesthetic comes
  from painted art, not tile geometry. 4 path edges (N/E/S/W)
  vs 6 is a depth trade-off the player won't notice at v0.2.

## Core loop

1. Draw the next tile from the deck.
2. Place it on any empty cell adjacent to an already-placed
   tile, with at least one path edge matching the neighbor's
   path.
3. Advance one day → traveler walks forward along the path to
   the next tile in their route.
4. If the new tile has a POI, collect its category token + score
   ticker.
5. Continue until the deck empties (or N days pass).
6. End-of-journey scoring screen: per-category totals + bonuses.

Placement and travel are intertwined — no separate build phase.

## Win / fail

- **Win condition:** no fail state; the journey always
  completes. Score is the score. Players chase high score across
  multiple runs.
- **Soft fail:** no playable tile in hand (path mismatch on
  every adjacent empty cell). Player discards + draws again
  (capped count). Should be rare with a well-tuned deck.

## Scoring framework

POI types start at 3 (per v0.2 below) and grow if scope permits:

- **v0.2:** 3 POI types (Vista, Cuisine, Encounter), 1pt each
- **v0.3:** add set bonuses (3-of-a-kind = +5; full set across
  all 3 types = +3)
- **Stretch:** 4th–5th category, rarity tiers, regional bonuses

## UI — painterly landscape (Dorfromantik influence)

Implementation north star: square tiles with Kenney Map Pack
art + stock Godot UI chrome. Don't theme; don't hunt fonts.

- **World map:** square grid; painterly tile art; path segments
  rendered on tile edges
- **Tile in hand:** large preview of the next tile,
  lower-right of screen
- **Placement:** click an empty adjacent cell; ghost preview
  before commit
- **Journey UI:** day counter; current traveler position
  highlighted; score tickers fire when POI is visited
- **Scoring postcard:** end-game stylized tableau — **the vibe
  lever**. Lives in v0.3 / v0.4.

Assets:
- Kenney Map Pack (CC0): terrain tiles (forest, mountain,
  water, plain) + trail/path overlays
- Stamp-style POI icons (3 categories × ~2 variants)
- Default Godot UI for chrome
- Stretch: hand-drawn postcard backgrounds for the score screen

## Version ladder (5-minutes-of-fun discipline)

The build follows incremental version targets. **Every version
must be playable and fun for 5 minutes before adding the next
layer.** If a new feature breaks the 5-min-fun bar, back it out.
Ship whichever version is highest when the deadline hits — v0.2
is the minimum acceptable submission; v0.4 is the "polished
jam entry" target; v0.5 is submission day.

### v0.0 — pipeline works (Day 0 / Wed 5/20)

- Godot project scaffolded per `godot-playbooks/godot-project-setup.md`
  Part 1
- One placeholder square tile renders on a blank scene
- First daily `butler push` to a throwaway itch page — **keep
  it Draft (private)** until v0.5 submission. The push
  validates the pipeline; Draft means the page isn't
  externally discoverable.

Not playable. Establishes the deploy chain works **before** any
mechanic work — every hour spent debugging the export pipeline
later is doubly painful.

### v0.1 — the place-and-walk loop (Day 1 / Thu 5/21)

The minimum viable fun:

1. Fixed starting tile, placeholder art (colored squares OK)
2. "Next tile" visible in hand
3. Click any empty adjacent cell to place
4. Traveler walks onto each placed tile in order, one per day
5. Deck empties (~10 tiles) → game ends
6. End screen: "You visited X places. Play again?"

No edge matching, no categories, no UI flourish. Just place,
walk, end, number.

**State lives on the gameplay scene's root node** — `grid`
(Dictionary[Vector2i, Tile]), `deck` (Array),
`train_position`, `score`. Single-scene, so no cross-scene
plumbing is needed yet; AutoLoads and a SignalBus become the
obvious shape once v0.4 adds title/end-screen transitions.

**5-min-fun test:** does placing a tile and watching the
traveler step onto it feel good? If yes, this is the floor —
everything after adds, never replaces.

### v0.2 — the choice (Day 2 / Fri 5/22)

The only required mechanical complication:

- Tiles have paths on 2–4 edges; placement requires connecting
  a path to an adjacent tile's path edge
- 3 background types (plain, forest, water) for visual variety
- 3 POI types (Vista, Cuisine, Encounter) on ~30% of tiles
- End screen lists totals per category

**5-min-fun test:** does the edge-matching constraint create
interesting choices? If matching feels punishing, ease it (some
"open" edges always connect). If too lax, no real choice
exists. **This is the minimum acceptable submission.**

### v0.3 — the replay hook (Day 3 / Sat 5/23)

Reasons to immediately restart:

- Set bonuses (3-of-a-kind = +5, full set across all 3 = +3)
- Randomized tile deck per run
- Real-time score tickers during journey ("+1 Vista!")
- End-game tableau, not a flat list

**5-min-fun test:** does the player want to restart when the
score screen appears? "I almost got the full set" = success.

### v0.4 — the vibe pass (Day 4 / Sun 5/24)

Delight, not features. This is also where the **hiking reframe
lands visually** — see LUC-40 for the pivot tracker.

- Painterly Kenney Map Pack tile art (forest, mountain, plain,
  water) with trail/path overlays
- Hiker sprite replacing the placeholder travel marker
- Stamp-style POI icons (passport-stamp aesthetic — LUC-32)
- Postcard scoring screen with cohesive visual language
- Ambient outdoor loop (wind/birds) + soft footfall on each
  step; passport-stamp click on POI visit
- Arrival chime at journey end

Code-level identifiers (`rail_edges`, etc.) may also be
renamed to `path_edges` here if there's time — cosmetic, low
priority. Skip if it threatens the v0.5 submission window.

**5-min-fun test:** would you screenshot this for someone? This
is the "polished jam entry" target.

### v0.5 — submission (Day 5 / Mon 5/25 AM → 21:00 UTC)

Pre-submission only — **no new features**:

- Cover image + 3–5 screenshots + 10-second GIF for itch page
- Page copy per `itch-page-craft.md` structure
- Final web export + `butler push`
- itch page Draft → Public; submit before 21:00 UTC
- Tag commit: `git tag jam-submission`

### Beyond v0.5 — only if a miracle happens (or post-jam)

Nice-to-have, not jam-scope:

- Stay/rest day bonus mechanic
- Random encounter cards per stop
- Sketchbook / visited-POI journal page
- Second map / procedural map generation
- 4th–5th POI category
- Rarity tiers per POI

## Cuts (gone — not even stretch for jam)

- Multiplayer (single-player only)
- Multiple trains
- Train physics / track curvature / grades
- Cargo / passenger demand simulation
- Save / load (single session = single journey)
- Persistent meta-progression
- Tutorial scene — UX self-teaches via labels + tooltips

## Tuning placeholders (v0.1 / v0.2 targets)

All first-pass; retune in playtest.

- Deck size: 10–15 tiles for v0.1; 20–25 for v0.2
- POI density: ~30% of tiles tagged with a POI type
- Grid: bounded to fit single screen (no scrolling)
- Pace: 1 day per tile; ends when deck empties

## Risks (acknowledged from concept stage)

- **Mini Metro orbit** — largely defused by the hiking reframe
  (a walking map reads nothing like a transit diagram). Still
  watch during v0.4 vibe pass for any drift back toward
  minimalist abstract.
- **Late theme pivot (2026-05-22)** — train→hiking decided
  ~3 days before submission. v0.4 carries the entire visual
  and audio swap. Risk: ambition outruns the v0.4 budget.
  Mitigation: keep code identifiers as-is; reframe only what
  the player sees and hears.
- **Tile placement UX** — must be tactile and snappy. Budget
  v0.1 Thursday evening for placement-feel iteration; if it
  feels fiddly, the whole game feels fiddly.
- **Scoring legibility** — real-time tickers in v0.3 are
  critical to make decisions feel intentional, not random.
- **6-day sprint reality** — late-entry start; aggressive cuts
  apply. Ship whichever version we reach; v0.2 is the floor.

## Engineering notes (scoped to v0.1 → v0.2)

One gameplay scene; state lives on its root node. Composition
(`game-architecture-patterns.md` §1) and AutoLoads (§2) both
apply — AutoLoads just don't have a use case yet because the
game is single-scene through v0.3. See AutoLoads subsection
below.

- Tiles as **component scenes**: one `TileComponent` exposing
  `background_type`, `rail_edges` (4-bit bitmask: N/E/S/W),
  `poi_type` (nullable)
- Grid state on the gameplay scene root:
  `Dictionary[Vector2i, Tile]` — the logical source of truth.
  Godot's built-in `TileMap` node handles rendering.
- **Local signals** within the gameplay scene
  (`tile_placed`, `train_advanced`, `poi_visited`,
  `journey_ended`, `score_changed`) — defined on the root
  node, connected by children that care. No global event bus.
- Simple enum state machine on the root:
  `WAITING_FOR_PLACEMENT` / `TRAIN_ADVANCING` / `SCORE`
- No `AStar2D` needed — the train's next tile is determined at
  placement time via rail-edge lookup
- Input: mouse only (keyboard optional). Single button — click
  to place.

### AutoLoads

Godot AutoLoads (`Project Settings → AutoLoad`) are the
standard pattern for cross-scene state, global signals, and
long-lived systems (audio, save, RNG, settings) — see
`game-architecture-patterns.md` §2. slow-rails uses them as
the need arises: nothing in v0.1–v0.3 crosses scene
boundaries because the game is one scene, but a `SignalBus`
and `GameSettings` AutoLoad become the obvious shape the
moment v0.4 adds title/end-screen transitions.

Rule of thumb: reach for an AutoLoad when (a) two scenes
need to share live state, (b) a system must outlive a scene
swap (background music, current-run data), or (c) a signal
needs to cross scene boundaries. Don't pre-build them for
hypothetical needs; don't avoid them out of caution either.

## Related

- `game-mechanics-and-genres.md` — brainstorm palette
- `incremental-games.md` — incremental case studies + working
  hypotheses
- `game-architecture-patterns.md` — patterns referenced above
- `jam-rhythm.md` — Day 0–N cadence (in planning repo)
- `asset-pipeline.md` — Kenney CC0 packs + Godot import
  settings
- `itch-page-craft.md` — submission asset capture + page copy
- `web-build-optimization.md` — texture/audio compression for
  HTML5
- `godot-playbooks/godot-project-setup.md` — Wed 5/20
  scaffolding reference
- Jam page: https://itch.io/jam/gamedevtv-jam-2026
- Dorfromantik (Steam):
  https://store.steampowered.com/app/1455840/Dorfromantik/
- Tokaido (BGG):
  https://boardgamegeek.com/boardgame/123540/tokaido
