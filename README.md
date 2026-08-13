# edi-refactor-plan — shared refactor planning

Public planning repo for the refactor of the EDI codebase, co-designed by cz
and rjguo, each working with their own Claude Code session. The code itself
lives in the private development repo; this repo holds only the plan.

## Files

- `refactor_plan.md` — canonical plan, agent-readable.
- `refactor_plan.html` — human-readable twin of the same content, served via
  GitHub Pages: <https://cz2014.github.io/edi-refactor-plan/refactor_plan.html>

## Confidentiality rule

This repo is PUBLIC. Design and process discussion only:

- no data files, no benchmark numbers, no unpublished physics results;
- no code copied from the private repo — refer to code by file or subroutine
  name only.

Anything that would violate this stays in the private repo.

## How to update

1. `git pull` BEFORE editing. Update frequency is low, so conflicts are
   unlikely if you always do this first.
2. Humans propose ideas; each side's Claude Code writes them into
   `refactor_plan.md` under the right section, tagged with author and date,
   e.g. `[cz 2026-08-12]`.
3. Whoever edits `refactor_plan.md` must regenerate/update `refactor_plan.html`
   in the same commit, so the twins never drift.
4. Do not delete the other side's entries. To disagree, add a reply entry under
   the same item, or move the point to "Open questions".
5. Keep the document current-state: when a question is settled, fold the answer
   into the design sections and remove the stale discussion.
6. Commit to `main`; GitHub Pages republishes the html automatically.
