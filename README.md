# ProPulse

## Quantum-Inspired Protein Signal Intelligence

> **Project status:** Early-stage research --- the scientific
> formulation, system architecture, and benchmark strategy are being
> established. The end-to-end executable pipeline and validated
> benchmark results are not yet available.

ProPulse is a research-oriented computational biology project
investigating whether quantum and quantum-inspired graph dynamics can
provide useful, interpretable information about signal propagation
through protein structures.

The core abstraction is deliberately simple:

**Protein structure → Protein Contact Network → Signal Propagation →
Connectivity → Allosteric Site Ranking**

ProPulse treats a protein as a residue-level graph and studies how
information or perturbations propagate across that graph. Classical
diffusion-based methods provide strong, transparent baselines;
continuous-time quantum walks (CTQWs) form the primary quantum model
under investigation.

The central research question is:

> **Can quantum connectivity metrics extracted from protein graphs
> identify biologically meaningful allosteric regions, while providing
> information beyond strong classical graph-based baselines?**

ProPulse does **not** assume that a quantum model produces a quantum
advantage. Any such claim must be demonstrated through controlled
experiments, fair classical comparisons, robustness analysis,
interpretability, and explicit resource accounting.

------------------------------------------------------------------------

## Table of Contents

-   [Motivation](#motivation)
-   [Research Objective](#research-objective)
-   [Core Scope](#core-scope)
-   [Research Pipeline](#research-pipeline)
-   [Scientific Formulation](#scientific-formulation)
-   [Inputs and Outputs](#inputs-and-outputs)
-   [Benchmark Strategy](#benchmark-strategy)
-   [Evaluation](#evaluation)
-   [Architecture](#architecture)
-   [Roadmap](#roadmap)
-   [Current State](#current-state)
-   [Technology](#technology)
-   [Research Principles](#research-principles)
-   [Documentation](#documentation)
-   [References](#references)
-   [License](#license)

------------------------------------------------------------------------

## Motivation

Allosteric regulation depends on the ability of structural perturbations
at one region of a protein to influence functionally important regions
elsewhere. ProPulse investigates this phenomenon through a graph-based
abstraction rather than treating molecular dynamics as the primary
computational representation.

This approach is intended to provide:

-   an explicit structural representation;
-   a common graph for classical and quantum propagation;
-   residue-to-residue connectivity measures;
-   interpretable structural and graph-level outputs;
-   a path toward quantum simulation on reduced protein graphs.

The initial objective is not to replace established structural-biology
methods. It is to determine whether graph dynamics---especially
quantum-walk dynamics---contain useful information for identifying
candidate allosteric regions.

------------------------------------------------------------------------

## Research Objective

ProPulse is being developed as a reproducible pipeline that:

1.  Converts an unbound protein structure into a residue-level contact
    graph.
2.  Establishes classical random-walk and diffusion baselines.
3.  Simulates continuous-time quantum-walk propagation on the same
    graph.
4.  Derives residue-to-residue connectivity representations.
5.  Converts connectivity into residue and site-level scores.
6.  Produces ranked candidate allosteric sites, including a top-five
    submission where required by the benchmark.
7.  Evaluates predictions against independently defined structural or
    functional references.
8.  Reports accuracy, robustness, interpretability, and computational
    resource requirements.

Secondary investigations include graph perturbation, parameter
sensitivity, noise resilience, scalability, coarse-graining,
three-dimensional visualization, and hardware-aware quantum experiments.

------------------------------------------------------------------------

## Core Scope

### In scope

-   Static protein structures represented as residue-level graphs.
-   Geometric or contact-based graph construction.
-   Classical random walks and graph diffusion.
-   Laplacian-based propagation and related structural graph baselines.
-   Continuous-time quantum walks as the primary quantum model.
-   Connectivity matrices and residue-level scoring.
-   Spatial clustering and site-level ranking.
-   Controlled comparison between classical and quantum propagation.
-   Hybrid classical--quantum workflows.
-   Reproducible experiments and benchmark evaluation.

### Out of scope for the initial prototype

-   Quantum chemistry or electronic-structure simulation as the central
    method.
-   VQE, QPE, UCCSD, or molecular Hamiltonian simulation as the primary
    approach.
-   Molecular-dynamics trajectories as an input to the initial
    challenge-oriented formulation.
-   Clinical decision-making or diagnostic use.
-   Treating computational predictions as experimentally confirmed
    allosteric sites.
-   Making quantum hardware a prerequisite for scientific progress.
-   Building a large ML/QML prediction system before the underlying
    propagation hypothesis is validated.

------------------------------------------------------------------------

## Research Pipeline

``` mermaid
flowchart LR
    A[Unbound Protein Structure] --> B[Structure Processing]
    B --> C[Protein Contact Graph]
    C --> D[Classical Propagation]
    C --> E[Quantum Propagation]
    D --> F[Connectivity]
    E --> F
    F --> G[Residue Scoring]
    G --> H[Spatial Site Clustering]
    H --> I[Candidate Site Ranking]
    I --> J[Benchmark & Analysis]
```

The comparison is intentionally controlled:

**Same Protein → Same Graph → Different Propagation Models → Comparable
Connectivity/Ranking → Same Evaluation Protocol**

Changing the graph construction or preprocessing between classical and
quantum methods would confound the scientific comparison.

------------------------------------------------------------------------

## Scientific Formulation

### Protein as a graph

For a protein with (N) modeled residues, ProPulse represents the
structure as a weighted graph

\[ G=(V,E,W), \]

where:

-   (V={v_1,`\ldots`{=tex},v_N}) represents residues;
-   \(E\) represents structural connections;
-   (W=\[w\_{ij}\]) contains non-negative edge weights derived from
    structural relationships.

The exact residue representation, contact definition, and edge-weight
function are configurable experimental parameters and must be recorded
with each result.

### Classical propagation

Let (A) be the weighted adjacency matrix and (D) the degree matrix. A
random-walk transition matrix can be defined as

\[ P=D\^{-1}A. \]

Classical baselines may include:

-   random-walk propagation;
-   graph diffusion;
-   heat-kernel propagation;
-   Laplacian-based methods;
-   structural graph measures such as centrality and communicability.

These baselines are scientifically necessary: a quantum method is only
informative if it provides value beyond credible classical graph
dynamics.

### Continuous-time quantum walk

The primary quantum model is a continuous-time quantum walk defined by a
graph-derived Hamiltonian (H). Its evolution operator is

\[ U(t)=e\^{-iHt}. \]

For an initial residue (i), the transition probability to residue (j) is

\[ p\_{j\|i}(t)=\|`\langle `{=tex}j\|U(t)\|i`\rangle`{=tex}\|\^2. \]

Because quantum transition probabilities can oscillate, ProPulse will
evaluate explicitly defined temporal aggregation schemes, such as
time-averaged transition probability,

\[ `\overline{C}`{=tex}\_{ij} = `\frac{1}{T}`{=tex} `\int`{=tex}*0\^T
p*{j\|i}(t),dt, \]

or a declared discrete approximation.

The Hamiltonian family, time interval, time resolution, normalization,
and aggregation rule are experimental choices---not hidden
implementation details.

### From connectivity to sites

The model first produces residue-level evidence. Candidate sites are
then obtained by combining:

1.  residue-level connectivity scores;
2.  spatial or structural clustering;
3.  separation constraints to avoid repeatedly counting the same
    physical region;
4.  a ranking rule fixed before evaluation.

A high propagation score is evidence of a graph-dynamical pattern. It is
not, by itself, proof of biological allostery.

------------------------------------------------------------------------

## Inputs and Outputs

### Inputs

The initial pipeline is designed around:

-   an unbound protein structure;
-   selected chain(s) or structural domain(s);
-   a declared residue representation;
-   a documented missing-residue policy;
-   graph-construction parameters;
-   propagation parameters;
-   an experiment configuration that records all relevant assumptions.

The prediction input must not use information from the bound structure
that serves as the evaluation reference.

### Outputs

For each protein, the pipeline is expected to produce:

-   residue identities mapped back to the source structure;
-   the constructed graph and metadata;
-   classical propagation/connectivity results;
-   quantum connectivity results;
-   residue-level scores;
-   ranked candidate allosteric sites;
-   top-five candidate sites where required;
-   sensitivity or stability information where available;
-   experiment metadata, runtime, assumptions, and limitations;
-   optional structural visualization artifacts.

Large connectivity matrices may be stored in sparse form when
appropriate.

------------------------------------------------------------------------

## Benchmark Strategy

The initial challenge-oriented benchmark includes:

  Protein system     Unbound structure    Reference / bound structure
  ---------------- ------------------- ------------------------------
  KRAS G12C                       4OBE                           6OIM
  BCR-ABL1                        1OPL                           5MO4
  Cardiac myosin                  5TBY                           6C1H
  c-Myc                           1NKP   Challenge-specific extension

The unbound structures are the prediction inputs. Bound/reference
structures are reserved for evaluation and structural comparison.

The benchmark protocol must explicitly define:

-   residue mapping between structures;
-   construction of evaluation labels;
-   handling of missing residues and alternate locations;
-   chain and mutation handling;
-   site-to-reference matching criteria;
-   treatment of overlapping predictions and ties;
-   background and control definitions.

For research beyond the minimum challenge set, additional proteins
should be curated from appropriate allosteric annotations and mapped to
suitable structural references.

A strong development practice is to keep the final challenge set
isolated from extensive parameter tuning whenever possible.

------------------------------------------------------------------------

## Evaluation

ProPulse is evaluated as a scientific hypothesis, not as a demonstration
of quantum terminology.

The minimum comparison should include:

-   random or degree-matched controls;
-   classical random-walk and diffusion baselines;
-   strong structural graph baselines;
-   the quantum-walk model using the same graph and preprocessing.

Relevant evaluation dimensions include:

-   top-(k) residue/site recall;
-   precision and enrichment at (k=5);
-   AUROC/AUPRC when residue-level labels are well defined;
-   rank-based comparisons;
-   prediction stability under graph and parameter perturbations;
-   runtime and memory;
-   graph-size scaling;
-   for quantum implementations: qubit count, circuit depth, sampling
    cost, and noise sensitivity.

A result should not be called a **quantum advantage** merely because it
achieves a higher predictive score. The comparison must account for the
information available to each method and the computational resources
required.

------------------------------------------------------------------------

## Architecture

ProPulse follows a modular research-software architecture:

### Protein Graph Engine

Responsible for structure parsing, chain selection, residue
normalization, contact detection, edge weighting, graph validation, and
biological identity mapping.

### Propagation Engine

Provides a common abstraction for:

-   classical random walks;
-   diffusion and heat-kernel methods;
-   continuous-time quantum walks;
-   future propagation variants.

### Connectivity and Ranking

Transforms propagation outputs into connectivity representations,
residue scores, candidate pathways, and site-level rankings.

### Evaluation

Handles benchmark labels, structural mapping, metrics, controls,
ablations, statistical analysis, and experiment manifests.

### Visualization and Reporting

Provides matrix views, graph views, structural overlays, ranked results,
and reproducible experiment reports.

### Future Intelligence Layer

Machine learning, GNNs, or QML may later learn from validated
propagation representations. This layer is deliberately downstream of
the core scientific question.

------------------------------------------------------------------------

## Roadmap

### Phase 1 --- Graph Foundation

-   Define residue representation and contact policy.
-   Implement structure parsing and validation.
-   Build graph construction and validation.
-   Establish deterministic experiment configurations and tests.

### Phase 2 --- Classical Baselines

-   Implement random-walk propagation.
-   Implement diffusion/heat-kernel propagation.
-   Add structural graph baselines.
-   Define ranking and benchmark evaluation.
-   Produce the first end-to-end classical report.

### Phase 3 --- Quantum Propagation

-   Implement CTQW using the same graph.
-   Compare graph-derived Hamiltonian choices.
-   Define temporal aggregation and connectivity metrics.
-   Characterize numerical and graph-size limitations.

### Phase 4 --- Benchmark and Robustness

-   Run the benchmark suite.
-   Perform ablations and sensitivity analysis.
-   Evaluate incomplete graphs and perturbations.
-   Study noise and resource requirements.
-   Document where the quantum model helps, matches, or fails.

### Phase 5 --- Hardware-Aware Experiments

-   Reduce or coarse-grain representative graphs.
-   Estimate qubit, depth, sampling, and error-mitigation requirements.
-   Evaluate selected instances on simulators and, where practical,
    quantum hardware.

### Phase 6 --- Research Platform

-   Add interactive structural visualization.
-   Expand the benchmark across diverse protein systems.
-   Investigate validated ML/GNN/QML representations.
-   Explore broader biological graph applications only after the protein
    use case is scientifically established.

------------------------------------------------------------------------

## Current State

ProPulse is currently in the research-definition and foundation stage.

The principal implementation milestones are:

-   executable structure-to-graph pipeline;
-   versioned classical baselines;
-   CTQW implementation;
-   benchmark preparation and residue mapping;
-   evaluation tooling;
-   automated tests;
-   reproducible experiment artifacts.

No predictive performance or quantum advantage is claimed at this stage.

------------------------------------------------------------------------

## Technology

The implementation is Python-first.

The current development stack is centered on:

-   **Python 3.12.11**
-   **uv** for project and environment management
-   **NumPy / SciPy** for numerical computation
-   **Biopython** for structural processing
-   **NetworkX** for graph operations
-   **Qiskit / Qiskit Aer** for quantum simulation
-   **pytest** for testing
-   **Ruff** for code quality
-   **Matplotlib** and optional molecular-visualization tools for
    analysis

The repository is organized as a reusable scientific package under
`src/propulse`, with separate areas for experiments, data, results,
notebooks, tests, and documentation.

------------------------------------------------------------------------

## Research Principles

1.  **Scientific honesty** --- distinguish established facts,
    inferences, hypotheses, and experimental observations.
2.  **Baseline discipline** --- compare quantum propagation against
    credible classical alternatives on identical graph inputs.
3.  **Reproducibility** --- preserve structure, graph, propagation,
    ranking, software, and evaluation metadata.
4.  **Interpretability** --- predictions should remain traceable to
    residues, graph relationships, propagation patterns, and structural
    regions.
5.  **Controlled scope** --- validate the core allosteric-site
    hypothesis before expanding into a larger platform.
6.  **Hardware realism** --- report quantum resource requirements and
    noise sensitivity.
7.  **Biological humility** --- computational predictions identify
    candidates; biological confirmation requires independent evidence.
8.  **Falsifiability** --- experiments must be designed so that the
    quantum hypothesis can fail.

------------------------------------------------------------------------

## Documentation

The repository documentation is organized by responsibility:

-   `PRD.md` --- product/research requirements and objectives.
-   `DESIGN.md` --- research-software experience and design principles.
-   `ARCHITECTURE.md` --- system architecture and module boundaries.
-   `SCHEMA.md` --- data model and scientific data contracts.
-   `RULES.md` --- engineering and scientific rules.
-   `TECH_STACK.md` --- technology choices and their roles.
-   `TEAM.md` --- team structure and collaboration model.
-   `ROLES.md` --- stable responsibilities and ownership.
-   `WORKFLOW.md` --- task, development, review, validation, and
    research workflow.
-   `CONTRIBUTING.md` --- practical contribution guidance.
-   `docs/` --- deeper technical and scientific documentation as the
    project evolves.

The README is the project entry point. Detailed rules and specifications
belong in the dedicated documents above.

------------------------------------------------------------------------

## References

The research direction is informed by the quantum-walk literature,
including:

-   Andrew M. Childs, *On the relationship between continuous- and
    discrete-time quantum walk* (2009).
-   *Quantum Simulation of a Discrete-Time Quantum Stochastic Walk*
    (2020).

The benchmark scope is based on the Cleveland Clinic Challenge brief
supplied with the project. Benchmark labels, matching rules, and
experimental protocols should be treated as explicit research artifacts
rather than implicit assumptions.

------------------------------------------------------------------------

## License

Copyright © 2026 Quantstellar Technologies.

This project is proprietary software.
All rights reserved.

See [LICENSE](./LICENSE) for the full license terms.
