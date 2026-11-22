# Skill — Cinematic ASCII Animation in One HTML File

Use this workflow when the user requests a premium short-form ASCII animation or wants a visually faithful remake of a scene such as the 12-second Christmas tree film.

## Output contract
- Deliver a **fully self-contained** HTML file, playable when double-clicked and as a static website. No JavaScript frameworks, fonts, images, CSS files, audio files, network calls, or TODOs.
- Exactly **one visible HTML canvas**; an offscreen canvas for glyph composition and character bloom is permitted.
- Every visible foreground object is made exclusively of characters placed in a depth-buffered fixed grid. The only painted shape is the vertical background gradient. No circles, strokes, shapes, radial gradients, shadow sprites, canvas shadows, or pre-rendered visuals.
- Stage is portrait 9:16, centered: `PW = min(windowWidth, windowHeight * 9/16)`. Grid width 124 cells, `CW=PW/124`, `CH=CW*1.62`, `AR=CW/CH`; compensate all vertical geometry using `AR`. Extend the grid to cover wider windows for snow and floor.
- Monospace bold Menlo/Consolas/DejaVu Sans Mono at about `0.95*CH`, centered. DPR max 2. Relayout and resize all typed buffers.
- Compose each frame into typed `chars/colors/depth` arrays with a `put(x,y,char,color,z)` that respects depth; render exactly one `fillText` per filled cell to offscreen canvas; composite to visible canvas. 
- Character bloom is allowed only by compositing the glyph layer twice with screen+blur: `0.006*PW` / alpha .4, `0.02*PW` / alpha .12. Character-only halos use `+ : .` and may only fill empty cells.
- Seeded point clouds generated once; project as `phi=a+t*0.5; x=cx+r*sin(phi); z=r*cos(phi); y=baseY-f*treeH+droop+z*(0.17*AR)`. All animation uses absolute clock time, not incremental accumulated rotation.
- Static gradient `#323a47` → `#394250` at 55% → `#4b5463` at 86% → `#666e7d` at bottom. Never black.
- No on-screen labels or words *during* the movie. A character-built `PLAY` is allowed on the startup screen.
- Launch on click, touch, Enter or Space; Web AudioContext created/resumed directly in that handler. At/after 12 seconds continue gently animating; click or R replays; Space pauses.

## Visual staging and timing
- Floor disc at .845*rows, trunk/tree base at .698*rows, tree tip at .273*rows, champagne star two rows above. Max tree radius `0.32*124` cells.
- Snow: 230 faint to bright `+` with varying slow speeds and lateral drift; always present.
- 0.05–0.39: warm `@` streak falls toward disc, tail of `#` and `|`.
- 0.39–1.4: giant flat pale-gold elliptical stage disc spreads beyond stage edges; double rim, arched petals, ring, spokes and snowflake. Initial concentric impact rings and ~80 sparks.
- 1.05–1.5: warm dark brown shaded 7–10-column trunk rises.
- 1.2–1.8: bright `@` makes a full circuit leaving a rainbow growth ring.
- 1.8–6.15: ~150k seeded tree points grow under a rising, rippling four-layer ring. Six distinct scalloped foliage tiers, widest at base with approx relative radii `1.08,.80,.62,.46,.32,.19`; tier tops are shadowed, tips lit mostly from left. Green only `8 & % # @` with shade 4–70% and *restrained* white frost on outer tips. Red hanging baubles are `@@@` over `@O@`.
- 5.27–6.17: growth ring shrinks, champagne star unfolds into character core and eight rays, extra dotted rays and orbiting pastel glyphs.
- 5.95–8.95: two comets spiral down four revolutions with dense rainbow tails; activate rim fairy lights *only after passage*. Hue ramp `pink(318) → violet(282) → blue(228) → cyan(190) → mint(150) → pale yellow(56) → gold(38) → reverse`, **never** take the full hue circle through red.
- 9–10.5: five 3D ASCII point-cloud gift boxes pop sequentially, one every .26s, in red/gold, gold/red, green/gold, blue/gold, magenta/gold, rotating with the tree.
- 10.55–11.95: a white, profile, right-facing running/leaping reindeer scales out of the tree. Build with 2D signed-distance ellipses and tapered capsules for body, head, ears, antlers, muzzle, four animated two-segment legs and tail. Solid white outline `@#8`, airy interior `+*x:`. Dim nearby tree and hide overlapping lights.
- Dust: ~170 pastel glyph motes rising around tree after 30% growth.
- After 12 sec: continuous rotation, sparkle, snow and running deer; all gifts remain.

## Web Audio synthesis
- No external audio. Voices→bus→dry gain .85 and convolution reverb .32 (2.4s stereo shaped noise, cubic decay)→compressor threshold -16 dB, ratio 4→master gain 1.3.
- Implement 3-partial bells (sine 1×, 2.76×, 5.4× with gains 1/.2/.07); bandpass noise with moving center frequency; sine glides; sine pads. Pitch scale C major pentatonic starting at C5; stereo spread.
- Fire each sound event when previous playhead time crosses the cue time, never using setTimeout. Envelope starts at zero and fades to .0001 before oscillators stop.
- Cues: falling pitch glide and bell at .05; low thump/noise/bells at .39; woody knocks at 1.05; ring whoosh/glide/bell at 1.2; per-step random high bells and quiet broadband leaf grains during growth; rising bells/chord at 5.27; descending filtered noise with 24 alternate-pan bells at 5.95; quiet low pad chord at 8.95; five distinct rising gift pops at 9.05 + .26*i; 7 ascending bells and 8 sleigh shakes at 10.55.

## Quality traps
- Never use radial gradients, circles, lineTo, shadowBlur, HTML images or animations that aren't character cells.
- Avoid flat cone foliage, garish fully saturated gold, white-grey tree, premature decorative lights, frame-rate-dependent movement, character soup, audio clicks and failing first-tap audio.
- Use frosted outer tips sparsely, deep upper-tier shadows, branch scallops and a directional left light.
- Sample absolute clock time, cap animation cost on mobile if the frame rate falls, and generate static point clouds into typed arrays only once.
- Test no JS exceptions, no external network requests, 9:16 1080×1920 layout, phone layout, correct sequence, star/gifts/deer not clipped, hold moving, pause freezing and restart working.

## Reference implementation
- `christmas-tree/ascii-christmas.html` is the editable source.
- `christmas-tree/index.html` is the deployed current version.
- `christmas-tree/classic.html` preserves the previous non-ASCII version.
- Read this SKILL together with the source before making future variations.
