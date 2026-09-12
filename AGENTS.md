# AGENTS.md

ProPulse: quantum-inspired protein signal intelligence research project (from the Cleveland Clinic challenge "Unlocking undruggable targets: quantum simulation of allosteric signal propagation").

## Current state — read this first
- **All `src/propulse/**/*.py` files are empty stubs (0 bytes). No tests exist yet.** This is a spec-first repo: docs are complete, implementation has not started. Do not assume existing behavior — build from the docs. `tests/` dirs mirror the module tree but are empty.
- The source of truth is `docs/` (written in Vietnamese, NOT at repo root despite README listing them as root-level files): `PRD.md` (What/Why), `DESIGN.md`, `ARCHITECTURE.md`, `SCHEMA.md`, `RULES.md`, `TECH_STACK.md`, `WORKFLOW.md`. Read `docs/RULES.md` before any core change.

## Commands (uv-managed env, Python 3.12.11)
- `uv run pytest` — run tests
- `uv run ruff check .` — lint
- `uv run ruff format .` — format
- `uv run python -m propulse` — run the package
- Always use `uv run`, never the global interpreter. Commit `uv.lock`; use `uv sync` after dependency changes.

## Architecture
- `src/propulse/` is the scientific core. Strict one-way layering: `structure/ → graph/ → propagation/ → connectivity/ → ranking/`. No circular imports, no generic `utils/`.
- `propagation/classical/` (random_walk.py, diffusion.py) and `propagation/quantum/` (ctqw.py) must stay conceptually separated.
- Core must NOT import `experiments/`, `results/`, `notebooks/` — those may call core, never the reverse.
- Qiskit-specific objects stay inside `propagation/quantum/`; do not leak them into structure/graph/connectivity/ranking.
- Experiments go in `experiments/`; data in `data/raw/` + `data/processed/` (raw never overwritten); tests in `tests/` mirroring the module tree.

## Scientific rules that must not be violated (docs/RULES.md)
- **Fair comparison**: classical and quantum run on the SAME graph and SAME evaluation protocol. Never change graph representation between the two branches.
- **No leakage**: apo structure = prediction input only; holo/reference = validation only. Never feed holo information into prediction.
- **No data hard-coding**: no PDB structures, benchmark annotations, or results embedded in `src/propulse/`.
- **Preserve biological identity** through every layer: matrix index → node → residue → chain+number → structure. Never use a matrix/graph index as the biological identity.
- Unsettled scientific choices (contact definition, cutoff, edge weighting, Hamiltonian, connectivity metric, ranking formula) must be explicit, parameterized, and recorded in provenance — never hard-coded as fixed truths.
- Classical baselines must be strong, not strawmen. "Quantum advantage" is a hypothesis to test, never an assumption.
- No ML/GNN/QML in the v0.1 core. No new dependencies without a documented scientific need (keep to the TECH_STACK baseline).

## Benchmark
4 systems (apo input → holo reference): KRAS G12C (4OBE→6OIM), BCR-ABL1 (1OPL→5MO4), cardiac myosin (5TBY→6C1H), c-Myc (1NKP). Required outputs: N×N connectivity matrix + top-5 residue ranking.

## Testing style (once tests exist)
- Prefer scientific-invariant tests (graph consistency, residue mapping, matrix dims, normalization, determinism) over coverage.
- Validate on small synthetic graphs before large proteins; cover non-happy paths (disconnected graphs, invalid input, missing mappings).

## Priorities & git
- Priority order: scientific correctness > research validity > reproducibility > maintainability > performance > convenience.
- Small focused commits ("Add contact graph", "Implement CTQW simulation"), never mixed concerns. Never commit `.venv/` or local machine artifacts.