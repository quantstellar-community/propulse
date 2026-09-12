# System Context Diagram

## Mục đích

System Context Diagram mô tả ProPulse ở cấp độ hệ thống và các tác nhân
hoặc hệ thống bên ngoài tương tác với nó.

Diagram này tập trung vào **ranh giới của ProPulse, các nguồn dữ liệu
đầu vào, môi trường tính toán bên ngoài và các đầu ra chính**. Chi tiết
về module nội bộ, data model và execution flow được mô tả trong các
diagram chuyên biệt khác.

## System Context

``` mermaid
flowchart LR
    R[Researcher / Developer]

    PDB[(Protein Structure Source<br/>RCSB PDB)]
    REF[(Allosteric Reference<br/>Benchmark Data)]
    QC[Quantum Computing /<br/>Simulation Environment]
    VIS[Structural Visualization /<br/>Analysis Tools]

    PP{{ProPulse<br/>Protein Signal Intelligence}}

    R -->|Configure experiments<br/>and inspect results| PP

    PDB -->|Unbound protein structures| PP
    REF -->|Reference annotations<br/>and benchmark labels| PP

    PP -->|Quantum simulation jobs<br/>or reduced instances| QC
    QC -->|Quantum propagation results| PP

    PP -->|Connectivity matrices<br/>ranked candidate sites<br/>experiment reports| R
    PP -->|Structural scores and<br/>propagation results| VIS
```

## External Entities

  -----------------------------------------------------------------------
  Entity                              Role
  ----------------------------------- -----------------------------------
  **Researcher / Developer**          Defines experiments, configures
                                      parameters, executes analyses, and
                                      reviews results.

  **Protein Structure Source**        Provides protein structures used to
                                      construct the input representation.
                                      The initial challenge-oriented
                                      workflow uses unbound structures.

  **Allosteric Reference / Benchmark  Provides independently defined
  Data**                              reference information for
                                      evaluation and benchmarking.

  **Quantum Computing / Simulation    Provides quantum simulation or
  Environment**                       hardware execution for selected
                                      propagation experiments.

  **Structural Visualization /        Supports inspection of residue
  Analysis Tools**                    scores, connectivity patterns,
                                      candidate sites, and structural
                                      overlays.

  **ProPulse**                        Converts protein structural
                                      information into graph-based
                                      propagation analyses and produces
                                      connectivity, ranking, and
                                      evaluation artifacts.
  -----------------------------------------------------------------------

## Primary System Boundary

ProPulse is responsible for the computational research workflow between
structural input and analytical output:

**Protein Structure → Graph-Based Propagation → Connectivity → Candidate
Site Ranking → Evaluation**

The internal mechanisms of this workflow are intentionally omitted from
this diagram. They belong to the Architecture, Data Flow, and Sequence
diagrams.

## Key Interactions

### Inputs

-   Unbound protein structures.
-   Benchmark and reference information.
-   Research configuration and model parameters supplied by the
    researcher.

### External computation

ProPulse may interact with quantum simulation or quantum hardware
environments for selected experiments. Classical computation remains
part of the core workflow and is not treated as an external
prerequisite.

### Outputs

The primary outputs are:

-   residue-level connectivity information;
-   connectivity matrices or sparse equivalents;
-   ranked candidate allosteric sites;
-   benchmark and evaluation results;
-   reproducible experiment reports;
-   optional structural visualization artifacts.

## Design Notes

1.  **System Context is intentionally high-level.** Internal modules
    such as graph construction, random walk, diffusion, CTQW,
    connectivity, and ranking are not represented here.
2.  **Classical and quantum methods share the same ProPulse system
    boundary.** The context diagram does not imply that quantum
    computation is the sole or mandatory execution path.
3.  **Reference data is separated from prediction input.**
    Benchmark/reference information supports evaluation and must not
    silently become predictive input.
4.  **ProPulse does not claim biological confirmation.** Candidate
    allosteric sites are computational predictions requiring independent
    biological validation.
5.  **Detailed structure, data, and execution relationships are
    documented in subsequent diagrams.**

## Related Diagrams

-   `architecture.md` --- internal system components and boundaries.
-   `data-flow.md` --- movement and transformation of data through the
    pipeline.
-   `sequence.md` --- execution sequence of a representative analysis.
-   `er.md` --- conceptual data model and entity relationships.
-   `deployment.md` --- runtime and computational environments.
