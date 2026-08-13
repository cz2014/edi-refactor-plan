# EDI codebase refactor plan

Status: DRAFT — collecting requirements from both sides. See README.md for
update rules (public repo: design discussion only, no data or results).

## 1. Goals and constraints

- Modular separation: the core matrix-element builder (`ed_coarse`), the
  scattering/T-matrix solvers, and transport post-processing should be
  separable layers with clean interfaces, so that new physics (e.g. tail
  folding) plugs in without touching the core. [cz 2026-08-12]
- Data contract between the python post-processing layer (`post/`) and the
  Fortran core must be defined: binary formats (e.g. `edmat`) carry versioned
  headers, and readers live in one shared module rather than being duplicated
  per script. [cz 2026-08-12]
- Memory scaling is a first-class constraint: dense storage grows steeply with
  k-mesh size and becomes impractical at production meshes. Block/sector/
  low-rank storage should be a design assumption of the refactor, not a patch
  applied afterwards. [cz 2026-08-12]
- Performance-critical paths (coarse mode) already have optimized variants. The
  refactor should merge them in as the default rather than keeping parallel code
  paths. [cz 2026-08-12]
- Constraint: reproducibility of current results must be preserved. A
  regression suite gates every structural change. [cz 2026-08-12]

## 2. Architecture design

To be designed after requirements from both sides are in. The candidate
decomposition below is a starting point, not a decision.

- `io/formats`: file readers/writers, versioned binary headers, one definition
  of each on-disk format shared by Fortran and python. [cz 2026-08-12]
- matrix-element core: potential/defect setup and the `ed_coarse` matrix-element
  build, with no knowledge of which solver consumes it. [cz 2026-08-12]
- solvers: Born, T-matrix, and tail/folding treatments behind a common solver
  interface over the core's matrix elements. [cz 2026-08-12]
- post-processing/transport: scattering rates, mobility/conductivity, and
  plotting, driven entirely through the documented data contract.
  [cz 2026-08-12]

## 3. Open questions

- Provenance of the reference matrix-element tables used for cross-code
  comparison: which code generated them, and are nonlocal pseudopotential
  terms included in DeltaV? [cz 2026-08-12]
- What is the target build system, and what is the minimum QE version for the
  refactored code? [cz 2026-08-12]

## 4. Requirements from rjguo

(to be filled by rjguo)
