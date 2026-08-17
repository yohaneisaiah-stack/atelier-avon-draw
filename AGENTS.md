# Atelier Avon Draw — Codex working instructions

Read `docs/PROJECT_CONTEXT.md` before planning or implementing changes. Treat the
current repository and GitHub state as authoritative when it differs from that
document.

## Collaboration

- The user is not a programmer. Translate feedback about feel and usability into
  technical options; do not ask the user to choose implementation details.
- Distinguish consultation from implementation. Do not edit files when the user
  is asking for advice, comparison, investigation, or explicitly says not to
  implement. Edit only after clear implementation approval.
- Explain important product tradeoffs in plain Japanese. Ask before making a
  consequential product decision when intent is uncertain.

## Product priorities

- Prioritize concentration and drawing feel over feature count.
- Treat iPad mini, Apple Pencil, Safari/PWA, and iPhone 16 as primary product
  environments. A PC-browser-only check is not sufficient evidence.
- Preserve drawing space and avoid controls that obscure the canvas or the
  drawing hand.
- Keep right/left-handed placement understandable and functional.

## Drawing engine safety

- Croquis Pencil priorities, in order: smoothness, Apple Pencil tracking, low
  latency, natural pressure, then pencil texture.
- Prefer subtle width variation and stronger opacity variation. Avoid abrupt
  pressure-driven width changes.
- Understand the current Pointer Events, coalesced/predicted events,
  resampling, speed-dependent smoothing, quadratic curves, incremental drawing,
  pressure handling, and history replay before changing them.
- Do not replace the drawing pipeline with a simpler implementation without an
  explicit design discussion and approval.
- Preserve the user's gesture; reduce digital angularity without strong stroke
  stabilization.

## Regression-sensitive behavior

Before and after relevant changes, consider Apple Pencil drawing, finger/palm
rejection, Croquis Pencil, eraser, Undo/Redo, clear and undo-after-clear, pause,
skip, abort and abort-cancel, all duration/count settings (especially the 10
second test mode), handedness, session completion, partial results, PWA,
iPad-mini layout, safe areas, and `100dvh`.

Timer changes must preserve PR #6 behavior: deadline-based elapsed time,
background-return synchronization, pause time exclusion, correct completion
duration, and restoration of the pre-abort pause state.

## Testing and reporting

- Run applicable syntax, automated, and diff checks.
- Clearly separate: what code inspection established, what automated tests
  established, and what still needs physical-device verification.
- Never claim Apple Pencil feel or iPad layout is correct solely from automated
  testing.
- Keep unrelated refactors, UI changes, file splits, and feature additions out
  of narrowly scoped fixes.

## Git and deployment

- Inspect the current branch, worktree, remote, and relevant PR state before
  starting. Preserve unrelated user changes.
- Do not implement directly on `main`. Create a focused branch, test, commit,
  push, and open a PR. Do not merge without explicit user permission.
- Do not change Cloudflare, publication, or PWA settings without explaining the
  need and receiving approval.
- Verify actual GitHub visibility, Pages, PR, and deployment state rather than
  relying on historical notes. Never store credentials or secrets in the repo.
