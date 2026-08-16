# EDI codebase refactor plan

Update rules and procedure: see README.md.

## Thread index

| Thread | Topic                      | Status   |
|--------|----------------------------|----------|
| T1     | Agent skill                | PROPOSED |
| T2     | Module decomposition       | PROPOSED |
| T3     | One language, one codebase | PROPOSED |

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
same commit, so the skill never lags the code.

## T2. Module decomposition — PROPOSED

### Requirements and discussion

- [cz 2026-08-13] We need to decompose the code into modules so that
  development lines that would otherwise conflict — e.g. the first-order
  (Born) EDI matrix-element computation and the T-matrix work — live in
  separate directories. Established layouts to learn from: QE's `common/`
  (shared infrastructure) plus per-package dirs (`EPW/`, ...), and the GW
  codes' split into mean-field, kernel, and post-processing layers.
- [rjguo 2026-08-13] Two more development lines need first-class homes in
  the layout: (i) transport — evaluation of transport properties
  (conductivity, mobility, and related tensors) from the scattering
  solutions; this is a physics layer of its own, not generic
  post-processing; (ii) wannier — wannierization and the groundwork for
  Wannier interpolation (projections/overlaps, gauge/rotation matrices,
  real-space Hamiltonian construction, validity checks of the resulting
  frame), which several consumers share and which today lives in scattered
  scripts.

### Design

Proposed layout, QE-style: one shared-infrastructure directory, one
directory per development line.

- `common/` — shared infrastructure: wavefunction and potential readers,
  k-mesh and symmetry utilities, parallel setup, and the on-disk binary
  formats (versioned headers, one definition shared by all layers).
  Interface changes are agreed in a thread here first.
- `edmat/` — first-order (Born) EDI matrix-element computation: defect
  potential setup and the matrix-element build, with no knowledge of which
  solver consumes the output.
- `tmatrix/` — T-matrix solvers and band-tail treatments, consuming `edmat`
  output only through the `common/` formats.
- `transport/` — transport-property evaluation on top of the solver
  outputs: scattering rates assembled into conductivity, mobility, and
  related tensors, including the iterative (IBTE) solve; consumes solver
  output only through `common/` formats. Fortran, per T3; the python
  mobility scripts now in `post/` become referees for it.
- `wannier/` — wannierization and Wannier-interpolation groundwork:
  projection/overlap preparation, gauge/rotation matrices, real-space
  Hamiltonian construction, and validity checks of the resulting frame
  (e.g. the wannierization identity against the band Hamiltonian). Its
  products are read by solvers and `transport/` only through `common/`
  formats.
- `post/` — python post-processing: plotting and generic analysis; reads
  only the documented formats. Transport-property evaluation is not here;
  it lives in `transport/`.
- `skill/` — agent skill, see T1.

Two rules the layout enforces: a commit touches one line's directory plus at
most `common/`; anything needed by two lines moves into `common/` rather
than being duplicated.

## T3. One language, one codebase — PROPOSED

### Requirements and discussion

- [cz 2026-08-16] The refactor should also fix how many languages and how
  many versions the project carries. Every additional language adds a build
  and configuration surface that every user and every developer then has to
  set up. Every parallel version splits both the user base and the
  development effort. A counter-example to avoid is DeepH, where each
  author maintains a separate version of the code: it is hard to use and
  hard to develop against, because no single version is the one that gets
  fixed. We want the opposite — one implementation language for the
  production path, and one line of development that everyone commits to.

### Design

- The production path is Fortran, built inside the QE/EDI build system. A
  physics capability has exactly one implementation, in Fortran; it is not
  maintained in parallel in a second language.
- Python is confined to `post/`: plotting, generic analysis, and referee
  scripts that validate the Fortran path. A referee script may duplicate a
  Fortran calculation, but it is never the production route, and it is not
  developed further once the Fortran path passes validation.
- One line of development: work lands on the shared repository's main line.
  Personal or per-author forks are for preparing changes, not for carrying
  features long-term.
