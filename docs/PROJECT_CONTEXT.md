# Atelier Avon Draw — Project context

## Product

Atelier Avon Draw is a focused drawing-practice application by Atelier Avon.
It currently supports timed croquis / gesture-drawing sessions and is intended
to grow into an environment where learners can view prompts, draw repeatedly
under time limits, save work, and eventually upload work for feedback.

The product is not intended to become a control-heavy painting application.
The primary measure of quality is whether drawing with Apple Pencil feels good
and whether the user can stay focused on practice.

## People and devices

- Development and discussion: Windows PC
- Primary physical test device: iPad mini with Apple Pencil
- Secondary mobile device: iPhone 16
- Normal use: installed PWA launched from the home screen

The user will often describe experience rather than implementation, for
example: a line feels jagged, a tool is hidden by the hand, or a pencil should
feel softer. Investigate and translate that intent into appropriate technical
choices.

## UI principles

- Maximize drawing space, especially on iPad mini.
- Ask whether a control must always be visible before adding it.
- Keep drawing tools together and preserve the right/left-handed layout.
- Avoid distracting timer animation. Pause state must remain unmistakable.

## Croquis Pencil direction

The standard pen is being developed as **Croquis Pencil**: thin, smooth,
responsive, and lightly pencil-like. It is not a Procreate clone.

Priority order:

1. Smooth lines
2. Apple Pencil tracking
3. Low input latency
4. Natural pressure response
5. Pencil texture

If texture makes the tool slower or less pleasant, remove the texture. Weak
pressure should be lighter and slightly thinner; strong pressure should be
darker and only slightly thicker.

The drawing pipeline has previously addressed interrupted/dotted lines, missed
short consecutive strokes, missed turns after a vertical stroke, and angular
curves. Relevant mechanisms include Pointer Events, `getCoalescedEvents()`,
`getPredictedEvents()`, resampling, light speed-dependent smoothing, quadratic
curves, pressure, incremental rendering, and command replay. Preserve the
artist's own line and avoid strong stabilization.

## Timer and sessions

The circular countdown is central to practice. The 10-second duration is also a
development shortcut and must remain available alongside other durations.

PR #6, **Fix timer accuracy and interrupt resume behavior**, corrected:

- completion-duration overcounting;
- deadline-based timing under load/background throttling;
- synchronization after returning from the background;
- pause time exclusion;
- restoration of the state that existed before opening abort confirmation.

Treat these as regression-sensitive requirements.

## Existing implementation baseline

The V0.2.4 baseline includes Croquis Pencil improvements, resampling,
speed-dependent smoothing, quadratic curves, pressure-driven opacity,
predicted-event rendering, an iPad-focused layout, handedness, and a circular
timer. The current implementation is intentionally small and mostly contained
in `index.html`, with `manifest.webmanifest` and `sw.js` providing PWA support.

V0.2.5 improves rapid Apple Pencil tap handling, reduces dark segment overlap,
animates the timer ring continuously, and stabilizes the initial home-screen
text size.

V0.2.6 removes periodic width bulges at resampled segment boundaries, delays
the first home-screen paint until the iPad viewport settles, and uses wall-clock
deadlines so elapsed time continues across iOS background suspension.

V0.2.7 prefers stylus Touch Events for Apple Pencil on iPad Safari, with Pointer
Events as a fallback, and narrows pressure-driven width variation while keeping
the stronger pressure-driven opacity response.

V0.2.8 restores coalesced Pointer Events as the primary Apple Pencil path,
uses stylus Touch Events only to recover missed taps, applies slightly stronger
pen-only smoothing, and waits for viewport orientation alignment at PWA launch.

V0.2.9 reverts the V0.2.8 hybrid Pencil input after physical testing found
duplicate dotted marks and missed rapid taps. It restores the V0.2.7
stylus-Touch-Events path as the stable iPad baseline. The launch-size issue is
tracked separately because it occurs before the live page becomes visible.

V0.2.10 begins Croquis Pencil v1 Phase 1 without changing the stable V0.2.9
input path. It separates a Croquis Pencil preset from brush dynamics, adds
curved and smoothed pressure response, keeps width variation narrow, deepens
the 2B-like tone, and adds restrained distance/velocity-aware start and end
tapers. Graphite stamps and paper texture remain deferred until physical-device
evaluation confirms the underlying stroke feel.

V0.2.11 keeps the stable stylus Touch Events input path but stops inserting
dense collinear points between its relatively sparse Apple Pencil samples.
Those artificial points prevented the quadratic renderer from rounding sample
transitions and made circles look polygonal. Stylus samples now remain the
curve controls, while mouse and Pointer Events retain the existing resampling.

V0.2.12 rolls back the V0.2.11 curve-control path after physical testing found
that mouse drawing remained active and iPad input/UI stopped responding after
the first Pencil stroke. It restores the operational V0.2.10 drawing pipeline;
the smoother first V0.2.11 stroke remains evidence for a later isolated curve
implementation that does not alter stroke lifecycle handling.

V0.2.13 preserves the complete V0.2.12 input and stroke lifecycle while adding
a separate set of render-only Apple Pencil curve controls. The original
resampled points still drive stroke state and finalization; only the quadratic
renderer uses the sparse stylus controls. Mouse rendering remains unchanged.

V0.2.14 disables application zoom gestures, adds a portrait phone/tablet
layout with the prompt and controls above a larger lower canvas, and makes
session-result thumbnails openable at full size. Individual results can be
sent to the iOS/iPadOS share sheet or downloaded as PNG on desktop. Landscape
and desktop session layouts retain the existing side-by-side arrangement.

V0.2.15 reorganizes the portrait header into timer/tools, prompt, and session
controls, with handedness mirroring and distinct visual treatment for drawing
tools versus session operations. It adds a 26px eraser-range cursor and
finger-only two-finger Undo / three-finger Redo tap gestures while excluding
stylus input, movement, active drawing, and paused sessions.

Always inspect current code and Git history before relying on this summary.

## Workflow

Normal delivery flow:

1. Inspect current `main` and worktree.
2. Create a focused branch.
3. Implement only the approved scope.
4. Test and inspect the diff.
5. Commit and push.
6. Open and review a PR.
7. Obtain explicit permission before merging to `main`.
8. Confirm Cloudflare Pages after a main update.

Physical-device judgment remains with the user. Reports should distinguish
code inspection, automated checks, and iPad/Apple Pencil verification.

## Hosting and security intent

The intended development/test deployment uses Cloudflare Pages with Cloudflare
Access rather than unrestricted public access. Repository visibility and
GitHub Pages state can change and must be checked directly before publication
work. If GitHub reports the repository as public or GitHub Pages as enabled,
flag the discrepancy before making changes. Do not include secrets in project
documentation.
