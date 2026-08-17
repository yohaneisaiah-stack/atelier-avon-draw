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
