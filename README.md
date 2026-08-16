# edi-refactor-plan — shared refactor planning

Shared planning repo for the refactor of the EDI codebase, co-designed by cz
and rjguo, each working with their own Claude Code session.

This repo is PUBLIC and carries design and process discussion only: no data
files, no benchmark numbers, no unpublished physics results, and no code
copied from the private repo — refer to code by file or subroutine name.
The code itself lives in the private development repo.

## Files

- `refactor_plan.md` — canonical plan, agent-readable.
- `refactor_plan.html` — human-readable twin, served via GitHub Pages:
  <https://cz2014.github.io/edi-refactor-plan/refactor_plan.html>

## How to update (procedure)

1. `git pull` before editing.
2. Edit `refactor_plan.md` following the rules below.
3. Regenerate `refactor_plan.html` in the same commit, so the twins never
   drift.
4. Commit to `main` and push; GitHub Pages republishes automatically.

## Rules

1. The plan is organized as numbered threads (T1, T2, ...), one topic each.
   A thread has two sections: "Requirements and discussion" (the discussion
   area) and "Design" (the design area). New topics get new threads; thread
   numbers are never reused.
2. Every discussion entry is tagged with author and date, e.g.
   `[cz 2026-08-13]`. Design sections are written as untagged, current-state
   prose — the thread's status line records who agreed and when.
3. Do not delete or rewrite the other side's discussion entries. To
   disagree, add a reply entry in the same thread.
4. Converting discussion into design: either side may draft the Design
   section and set the thread to PROPOSED. Only the OTHER side may set it to
   AGREED. An AGREED design is binding for implementation in the private
   repo; to change it, reopen the thread with a new discussion entry.
5. Keep the document current-state: once a thread is AGREED, prune its
   discussion down to the entries that explain the decision.
6. The plan file is written in English.
