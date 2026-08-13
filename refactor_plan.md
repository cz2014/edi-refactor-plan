# EDI codebase refactor plan

Update rules and procedure: see README.md.

## Thread index

| Thread | Topic                | Status   |
|--------|----------------------|----------|
| T1     | Agent skill          | PROPOSED |
| T2     | Module decomposition | PROPOSED |

Statuses: OPEN (discussing) -> PROPOSED (design drafted, awaiting the other
side) -> AGREED (binding). Reopening an AGREED thread sets it back to OPEN.

## T1. Agent skill — PROPOSED

### Requirements and discussion

- [cz 2026-08-13] We should maintain an agent skill for this code, so that a
  Claude Code session can build, run, and post-process it without
  rediscovering the workflow each time.

### Design

Keep a `skill/` directory at the top level of the code repo, holding the
agent skill: a `SKILL.md` entry point plus any helper scripts or input
templates it needs. The skill documents how to build the code, prepare
inputs, submit runs, and read the outputs. Whoever changes a user-facing
interface (input format, output format, build step) updates `skill/` in the
same commit, so the skill never lags the code. Ownership: cz.

## T2. Module decomposition — PROPOSED

### Requirements and discussion

- [cz 2026-08-13] We need to decompose the code into modules so that
  development lines that would otherwise conflict — e.g. the first-order
  (Born) EDI matrix-element computation and the T-matrix work — live in
  separate directories. Established layouts to learn from: QE's `common/`
  (shared infrastructure) plus per-package dirs (`EPW/`, ...), and the GW
  codes' split into mean-field, kernel, and post-processing layers.

### Design

Proposed layout, QE-style: one shared-infrastructure directory, one
directory per development line.

- `common/` — shared infrastructure: wavefunction and potential readers,
  k-mesh and symmetry utilities, parallel setup, and the on-disk binary
  formats (versioned headers, one definition shared by all layers).
  Ownership: shared; interface changes are agreed in a thread here first.
- `edmat/` — first-order (Born) EDI matrix-element computation: defect
  potential setup and the matrix-element build, with no knowledge of which
  solver consumes the output. Ownership: rjguo (to confirm).
- `tmatrix/` — T-matrix solvers and band-tail treatments, consuming `edmat`
  output only through the `common/` formats. Ownership: rjguo (method core,
  to confirm); cz (tail folding).
- `post/` — python post-processing: scattering rates, transport, plotting;
  reads only the documented formats. Ownership: cz.
- `skill/` — agent skill, see T1. Ownership: cz.

Two rules the layout enforces: a commit touches one line's directory plus at
most `common/`; anything needed by two lines moves into `common/` rather
than being duplicated.
