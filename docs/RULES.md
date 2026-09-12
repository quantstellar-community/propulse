# ProPulse --- Project Rules

**Phiên bản:** 0.1\
**Trạng thái:** Draft / Research Prototype\
**Ngôn ngữ:** Tiếng Việt\
**Dự án:** ProPulse --- Protein Signal Intelligence

------------------------------------------------------------------------

## 1. Mục đích

`RULES.md` định nghĩa các nguyên tắc và quy tắc mà mọi thay đổi trong
ProPulse phải tuân theo.

Các rules này nhằm bảo vệ ba mục tiêu:

1.  **Scientific correctness** --- code phản ánh đúng mô hình và
    assumption đã được chấp thuận.
2.  **Reproducibility** --- experiment và result có thể được tái lập và
    truy nguyên.
3.  **Maintainability** --- scientific core giữ được boundary rõ ràng
    khi project phát triển.

`RULES.md` không thay thế `ARCHITECTURE.md`, `SCHEMA.md` hoặc research
documentation. Nó định nghĩa **những điều phải tuân thủ khi xây dựng và
thay đổi hệ thống**.

------------------------------------------------------------------------

## 2. Core Principles

### Rule 2.1 --- Core First

Scientific core luôn được ưu tiên trước:

-   CLI polish;
-   GUI;
-   web;
-   API;
-   infrastructure;
-   ML/QML extensions.

Không thêm abstraction chỉ để làm project trông hoàn chỉnh hơn.

### Rule 2.2 --- Research First

Mọi implementation phải phục vụ một research requirement hoặc
engineering requirement có lý do rõ ràng.

Không xây feature chỉ vì nó có thể hữu ích trong tương lai.

### Rule 2.3 --- Scientific Honesty

Không được code hoặc trình bày hệ thống theo cách mặc định rằng quantum
propagation có quantum advantage.

Quantum advantage là hypothesis cần được kiểm chứng bằng experiment.

### Rule 2.4 --- Reproducibility

Kết quả quan trọng phải có đủ provenance và configuration để tái lập.

### Rule 2.5 --- Explicit Assumptions

Mọi scientific assumption ảnh hưởng đến kết quả phải được xác định rõ.

Không âm thầm đưa một assumption mới vào core.

------------------------------------------------------------------------

## 3. Architecture Rules

### Rule 3.1 --- Respect Module Boundaries

Core modules phải giữ đúng responsibility:

``` text
structure
    → Protein / residue representation

graph
    → Protein Contact Graph

propagation
    → Signal / information propagation

connectivity
    → Connectivity representation

ranking
    → Residue scoring / ranking
```

Một module không được trở thành nơi chứa logic của module khác.

### Rule 3.2 --- Dependency Direction

Dependency direction mục tiêu:

``` text
structure
    ↓
graph
    ↓
propagation
    ↓
connectivity
    ↓
ranking
```

Không tạo circular dependency.

### Rule 3.3 --- No Generic `utils/`

Không tạo `utils/` để chứa các function không có responsibility rõ ràng.

Nếu một function không biết thuộc module nào, trước tiên phải xem xét
lại abstraction.

### Rule 3.4 --- Keep Core Independent

`src/propulse/` không được phụ thuộc ngược vào:

``` text
experiments/
results/
notebooks/
```

Experiment và analysis code có thể gọi core; core không gọi ngược chúng.

### Rule 3.5 --- External Interfaces Stay Outside Scientific Logic

CLI, notebook và visualization là interface/supporting layers.

Scientific implementation phải nằm trong core module phù hợp.

------------------------------------------------------------------------

## 4. Scientific Modeling Rules

### Rule 4.1 --- Protein Structure Is the Starting Point

Core pipeline phải giữ abstraction:

``` text
Protein Structure
    ↓
Protein Contact Graph
    ↓
Propagation
```

### Rule 4.2 --- Graph Representation Must Be Explicit

Graph phải có representation rõ ràng:

``` text
G = (V, E, W)
```

nếu weighted graph được sử dụng.

Node identity phải giữ mapping về residue.

### Rule 4.3 --- Do Not Hard-Code Unsettled Scientific Decisions

Các vấn đề chưa được chốt như:

-   residue coordinate representation;
-   contact definition;
-   contact cutoff;
-   edge weighting;
-   connectivity metric;
-   ranking formula;

không được tự ý coi là fixed scientific truth.

Nếu cần implementation tạm thời để experiment, parameter phải được
explicit và ghi nhận trong experiment configuration/provenance.

### Rule 4.4 --- Same Graph for Fair Comparison

Khi so sánh classical và quantum:

``` text
Same Protein
     ↓
Same Graph
     ↓
┌───────────────┐
│               │
Classical      Quantum
│               │
└───────┬───────┘
        ↓
Connectivity / Evaluation
```

không được thay đổi graph representation giữa hai nhánh nếu mục tiêu
experiment là so sánh propagation model.

### Rule 4.5 --- Classical Is a Baseline, Not a Strawman

Classical baselines phải được implement và cấu hình nghiêm túc.

Không cố tình chọn hoặc cấu hình classical model yếu để làm quantum
model có vẻ tốt hơn.

### Rule 4.6 --- Quantum Is a Hypothesis, Not a Conclusion

CTQW phải được đánh giá bằng empirical evidence.

Không gọi một kết quả là "quantum advantage" nếu experiment chưa chứng
minh improvement phù hợp với metric và baseline.

------------------------------------------------------------------------

## 5. Data Rules

### Rule 5.1 --- Preserve Biological Identity

Không dùng matrix index hoặc graph index làm biological identity.

Mapping phải giữ được:

``` text
Matrix Index
    ↓
Node ID
    ↓
Residue ID
    ↓
Chain + Residue Number
    ↓
Structure
```

### Rule 5.2 --- No Data Hard-Coding

Protein structures, benchmark annotations và experiment outputs không
được hard-code vào source code core.

### Rule 5.3 --- Raw and Processed Data Are Separate

Tuân thủ:

``` text
data/
├── raw/
└── processed/
```

Raw input không được bị ghi đè bởi processing.

### Rule 5.4 --- Provenance Is Required

Research artifact quan trọng phải có thông tin đủ để biết:

-   source;
-   input structure;
-   graph configuration;
-   propagation model;
-   parameters;
-   connectivity metric;
-   ranking method;
-   software/environment information khi cần.

### Rule 5.5 --- Apo / Holo Separation

Trong challenge workflow:

``` text
Apo
 ↓
Prediction input

Holo / Reference
 ↓
Validation
```

Không đưa thông tin của unknown holo allosteric site vào prediction
input.

### Rule 5.6 --- Preserve Mapping Across Structures

Khi so sánh apo/holo hoặc các structure khác nhau, residue mapping phải
được explicit.

Không giả định rằng cùng một residue number luôn có cùng biological
identity giữa các structure.

------------------------------------------------------------------------

## 6. Propagation Rules

### Rule 6.1 --- Separate Classical and Quantum Implementations

Structure:

``` text
propagation/
├── classical/
│   ├── random_walk.py
│   └── diffusion.py
│
└── quantum/
    └── ctqw.py
```

phải được duy trì về mặt conceptual boundary.

### Rule 6.2 --- Propagation Does Not Rank Residues

Propagation layer tạo propagation output.

Nó không trực tiếp quyết định final Top-5 prediction.

Pipeline phải giữ:

``` text
Propagation
    ↓
Connectivity
    ↓
Ranking
```

### Rule 6.3 --- Backend Leakage Must Be Minimized

Qiskit-specific objects không được lan rộng sang structure, graph,
connectivity hoặc ranking layer nếu không cần thiết.

### Rule 6.4 --- Numerical Stability Matters

Các implementation propagation phải xem xét:

-   numerical precision;
-   matrix size;
-   sparse vs dense representation khi phù hợp;
-   normalization/invariants của model;
-   deterministic behavior khi model yêu cầu.

Không tối ưu premature trước khi correctness được xác nhận.

------------------------------------------------------------------------

## 7. Connectivity Rules

### Rule 7.1 --- Connectivity Is an Explicit Layer

Không tính connectivity một cách ngầm bên trong ranking.

### Rule 7.2 --- Metric Must Be Traceable

Mỗi connectivity output phải biết:

``` text
metric
parameters
propagation source
graph source
node mapping
```

### Rule 7.3 --- Do Not Assume Matrix Properties

Không mặc định connectivity matrix:

-   symmetric;
-   normalized;
-   dense;
-   sparse;

nếu scientific definition của metric chưa yêu cầu.

### Rule 7.4 --- Preserve Matrix Mapping

Mọi matrix phải có mapping giữa index và graph node/residue.

------------------------------------------------------------------------

## 8. Ranking Rules

### Rule 8.1 --- Ranking Is Separate from Connectivity

Connectivity matrix và residue ranking là hai representation khác nhau.

### Rule 8.2 --- Ranking Must Be Deterministic When Possible

Với cùng input và configuration, ranking nên deterministic trừ khi
experiment cố ý sử dụng stochastic process.

Nếu có stochasticity, random seed/configuration phải được ghi nhận.

### Rule 8.3 --- Top-5 Must Be Traceable

Mỗi Top-5 candidate phải truy nguyên được:

``` text
Rank
→ Residue
→ Score
→ Connectivity
→ Propagation
→ Graph
→ Structure
```

### Rule 8.4 --- Ranking Is Not Ground Truth

Predicted ranking không được ghi nhận như biological ground truth.

Ground truth/reference phải được lưu riêng.

------------------------------------------------------------------------

## 9. Experiment Rules

### Rule 9.1 --- Experiments Live Outside Core

Experiment-specific code thuộc:

``` text
experiments/
```

không thuộc `src/propulse/` nếu nó không phải reusable scientific
functionality.

### Rule 9.2 --- Experiments Must Declare Configuration

Experiment phải xác định rõ:

-   input;
-   graph configuration;
-   propagation model;
-   parameters;
-   connectivity metric;
-   ranking method;
-   evaluation method.

### Rule 9.3 --- One Change at a Time When Testing Scientific Effects

Khi kiểm tra ảnh hưởng của một parameter/model change, cố gắng giữ các
yếu tố khác cố định.

Ví dụ:

``` text
same protein
same graph
same evaluation
different propagation model
```

khi mục tiêu là so sánh propagation.

### Rule 9.4 --- No Cherry-Picking

Không chỉ báo cáo những experiment cho kết quả tốt mà bỏ qua các
experiment liên quan có kết quả ngược lại.

### Rule 9.5 --- Record Failed Experiments

Experiment thất bại hoặc không cho kết quả hữu ích vẫn có giá trị nghiên
cứu và nên được ghi nhận khi có relevance.

------------------------------------------------------------------------

## 10. Testing Rules

### Rule 10.1 --- Test Scientific Invariants

Tests phải ưu tiên các property/invariant của scientific model, không
chỉ test coverage.

Ví dụ:

-   graph structure consistency;
-   residue mapping consistency;
-   matrix dimensions;
-   valid normalization khi model yêu cầu;
-   deterministic behavior khi applicable.

### Rule 10.2 --- Test Each Core Module

Test structure:

``` text
tests/
├── structure/
├── graph/
├── propagation/
│   ├── classical/
│   └── quantum/
├── connectivity/
└── ranking/
```

### Rule 10.3 --- Unit Tests Before Large Benchmarks

Một scientific component phải có correctness tests cơ bản trước khi dùng
nó cho benchmark lớn.

### Rule 10.4 --- Small Synthetic Graphs Are Valuable

Trước khi chạy protein graph lớn, nên kiểm tra propagation trên graph
nhỏ có behavior có thể dự đoán hoặc kiểm chứng.

### Rule 10.5 --- Do Not Test Only the Happy Path

Các trường hợp invalid input, disconnected graph, missing mapping hoặc
incompatible dimensions cần được xử lý rõ ràng khi relevant.

------------------------------------------------------------------------

## 11. Code Quality Rules

### Rule 11.1 --- Readability Over Cleverness

Scientific code phải dễ đọc và dễ audit.

Không dùng abstraction hoặc optimization phức tạp nếu nó làm khó việc
kiểm tra mathematical logic.

### Rule 11.2 --- Small Functions

Function nên có responsibility rõ ràng.

### Rule 11.3 --- Explicit Types Where Useful

Type hints nên được sử dụng ở public/core interfaces khi giúp làm rõ
data contract.

Không bắt buộc type-heavy design ở mọi internal function.

### Rule 11.4 --- Documentation for Scientific Logic

Code chứa mathematical/scientific implementation quan trọng phải giải
thích:

-   model đang tính gì;
-   input/output;
-   assumption liên quan;
-   notation cần thiết;
-   reference nếu có.

Không biến comment thành một bản sao của implementation.

### Rule 11.5 --- No Dead Code

Không giữ code cũ/commented-out implementation trong core chỉ để "phòng
khi cần".

Git đã giữ history.

------------------------------------------------------------------------

## 12. Dependency Rules

### Rule 12.1 --- Minimal Dependencies

Chỉ thêm dependency khi có use case rõ ràng.

### Rule 12.2 --- Scientific Dependencies

Stack baseline hiện tại:

``` text
Python 3.12.11
uv
NumPy
SciPy
BioPython
NetworkX
pandas
Matplotlib
Qiskit
Qiskit Aer
```

### Rule 12.3 --- Development Dependencies

Baseline development tools:

``` text
pytest
ruff
```

Các tool khác chỉ được thêm khi có nhu cầu thực tế.

### Rule 12.4 --- No Global Dependency Assumptions

Project phải chạy trong environment được quản lý bởi `uv`.

Không dựa vào package cài global trên máy developer.

### Rule 12.5 --- Lock Reproducible Environment

`uv.lock` phải được cập nhật khi dependency resolution thay đổi.

Không chỉnh lockfile thủ công nếu không cần thiết.

------------------------------------------------------------------------

## 13. Python / Environment Rules

### Rule 13.1 --- Python Version

Development baseline:

``` text
CPython 3.12.11
```

### Rule 13.2 --- Environment

Sử dụng project environment được quản lý bởi `uv`.

Workflow ưu tiên:

``` bash
uv run ...
```

### Rule 13.3 --- Do Not Upgrade Runtime Casually

Không đổi Python major/minor/patch baseline chỉ vì có phiên bản mới.

Runtime change phải có technical/research reason và được kiểm tra
compatibility.

------------------------------------------------------------------------

## 14. CLI Rules

### Rule 14.1 --- CLI Is an Interface, Not the Core

CLI chỉ orchestration/interface.

Scientific logic phải nằm trong `src/propulse/`.

### Rule 14.2 --- Minimal CLI First

Interface hiện tại nên đơn giản.

Mental model mục tiêu:

``` text
propulse <command> <options> <arguments>
```

nhưng không cần implement toàn bộ command tree trước khi scientific core
ổn định.

### Rule 14.3 --- Same Core, Different Interface

Các cách chạy khác nhau phải gọi cùng scientific core.

Không tạo một implementation riêng cho CLI.

------------------------------------------------------------------------

## 15. Notebook Rules

### Rule 15.1 --- Notebook Is for Exploration

Notebook dùng cho:

-   exploration;
-   visualization;
-   analysis;
-   hypothesis investigation.

### Rule 15.2 --- No Core Algorithm Hidden in Notebook

Scientific implementation đã ổn định phải được đưa vào `src/propulse/`.

Notebook gọi core thay vì copy implementation.

### Rule 15.3 --- Record Inputs

Notebook quan trọng phải ghi rõ input structure, configuration và
version/context cần thiết.

------------------------------------------------------------------------

## 16. Visualization Rules

### Rule 16.1 --- Visualization Supports Interpretation

Visualization phải giúp hiểu:

-   propagation;
-   connectivity;
-   ranking;
-   classical/quantum differences;
-   protein structure mapping.

### Rule 16.2 --- Visualization Is Optional for Core

Core không được phụ thuộc vào GUI hoặc PyMOL để chạy.

### Rule 16.3 --- PyMOL Is External Tooling

PyMOL có thể được dùng để structural inspection/3D interpretation nhưng
không phải mandatory runtime dependency của core.

------------------------------------------------------------------------

## 17. Git Rules

### Rule 17.1 --- Small, Meaningful Commits

Commit nên đại diện cho một logical change.

Ví dụ:

``` text
add residue representation
implement contact graph
add random walk baseline
add CTQW prototype
```

### Rule 17.2 --- Do Not Mix Concerns

Không gom:

``` text
algorithm change
+
unrelated formatting
+
documentation rewrite
```

vào một commit nếu có thể tách.

### Rule 17.3 --- Never Commit Local Environment

Không commit:

``` text
.venv/
```

hoặc local machine artifacts.

### Rule 17.4 --- Protect Research History

Không rewrite shared research history một cách tùy tiện.

------------------------------------------------------------------------

## 18. Documentation Rules

### Rule 18.1 --- Documentation Has Clear Responsibilities

``` text
PRD.md
→ What & Why

DESIGN.md
→ Experience / Interaction

ARCHITECTURE.md
→ Technical Structure

SCHEMA.md
→ Data Model

RULES.md
→ Project / Engineering Rules

TECH_STACK.md
→ Technology
```

### Rule 18.2 --- Do Not Duplicate Documents

Không copy nguyên nội dung của một document sang document khác.

Cross-reference khi cần.

### Rule 18.3 --- Keep Research Claims Honest

Documentation phải phân biệt:

``` text
Fact
Hypothesis
Design Decision
Experiment
Result
Conclusion
```

### Rule 18.4 --- Update Docs with Architectural Changes

Nếu architectural/data contract thay đổi, documentation liên quan phải
được cập nhật.

------------------------------------------------------------------------

## 19. Security and Data Handling

ProPulse hiện không xử lý user secrets hoặc sensitive personal data như
một core requirement.

Tuy nhiên:

-   không commit credentials;
-   không commit API keys;
-   không commit cloud credentials;
-   không đưa secret vào experiment configuration;
-   không đưa sensitive local paths vào shared research artifacts nếu
    không cần.

------------------------------------------------------------------------

## 20. Performance Rules

### Rule 20.1 --- Correctness Before Optimization

Ưu tiên:

``` text
Correctness
    ↓
Reproducibility
    ↓
Clarity
    ↓
Performance optimization
```

### Rule 20.2 --- Measure Before Optimizing

Không tối ưu một bottleneck chỉ dựa trên intuition.

### Rule 20.3 --- Protein Graph Size Matters

Khi graph tăng kích thước, cần quan tâm:

-   memory;
-   matrix representation;
-   computational complexity;
-   quantum circuit depth;
-   simulator limitations.

Các optimization phải được kiểm chứng bằng benchmark.

------------------------------------------------------------------------

## 21. Research Comparison Rules

### Rule 21.1 --- Same Evaluation Protocol

Classical và quantum phải được đánh giá bằng cùng evaluation protocol
khi comparison yêu cầu.

### Rule 21.2 --- Report Baselines

Một quantum result không có baseline tương ứng thì không đủ để kết luận
improvement.

### Rule 21.3 --- Statistical Claims Need Evidence

Không dùng các từ như:

``` text
proven
significant
advantage
superior
```

nếu experiment/statistical analysis chưa hỗ trợ.

### Rule 21.4 --- Negative Results Are Valid

Quantum model không outperform classical model vẫn là một kết quả
research có giá trị.

------------------------------------------------------------------------

## 22. Rule for New Abstractions

Trước khi tạo module/class/framework mới, trả lời:

1.  Nó giải quyết vấn đề nào?
2.  Problem đó đã xuất hiện thực tế chưa?
3.  Nó thuộc boundary nào?
4.  Có thể giải quyết bằng abstraction hiện tại không?
5.  Nó có làm scientific core phức tạp hơn không?
6.  Có test/provenance/documentation tương ứng không?

Nếu chưa có câu trả lời rõ ràng, chưa tạo abstraction.

------------------------------------------------------------------------

## 23. Rule for Scientific Decisions

Một scientific decision quan trọng cần được ghi nhận khi nó ảnh hưởng
đáng kể đến:

-   graph topology;
-   propagation behavior;
-   connectivity;
-   ranking;
-   benchmark validity;
-   comparison fairness.

Ví dụ:

``` text
contact definition
edge weighting
initial signal
CTQW Hamiltonian
connectivity metric
ranking method
evaluation metric
```

Decision phải phân biệt:

``` text
Known fact
Assumption
Hypothesis
Experimental choice
Empirical result
```

------------------------------------------------------------------------

## 24. Definition of Done

Một thay đổi core chỉ được xem là hoàn thành khi phù hợp với scope của
change:

``` text
Implementation
    ↓
Tests
    ↓
Scientific validation
    ↓
Documentation
    ↓
Reproducibility check
```

Không phải mọi change đều cần toàn bộ các bước ở cùng mức độ, nhưng
scientific changes phải có evidence tương ứng trước khi được coi là
validated.

------------------------------------------------------------------------

## 25. Rule Priority

Khi có conflict giữa các mục tiêu, ưu tiên:

``` text
Scientific correctness
        ↓
Research validity
        ↓
Reproducibility
        ↓
Maintainability
        ↓
Performance
        ↓
Convenience / polish
```

Nếu một shortcut làm giảm scientific validity, không sử dụng shortcut đó
chỉ để development nhanh hơn.

------------------------------------------------------------------------

## 26. Final Principle

ProPulse là một research project trước khi là một software product.

Vì vậy:

> **Mọi engineering decision phải giúp chúng ta nghiên cứu information
> propagation trên protein graphs tốt hơn, tái lập được và kiểm chứng
> được.**

Architecture phải bảo vệ science.

Code phải bảo vệ model.

Tests phải bảo vệ correctness.

Data phải bảo vệ traceability.

Documentation phải bảo vệ research context.

Và experiment phải được phép chứng minh rằng hypothesis của chúng ta
**sai**.

------------------------------------------------------------------------

## 27. Source Basis

Rules này được xây dựng dựa trên:

1.  `PRD.md`.
2.  `DESIGN.md`.
3.  `ARCHITECTURE.md`.
4.  `SCHEMA.md`.
5.  Documentation & Diagram Baseline Guide.
6.  Scientific direction đã thống nhất của ProPulse.
7.  Development environment đã thống nhất: Python 3.12.11 + uv +
    scientific/quantum stack hiện tại.

Các rule về scientific methodology phải được cập nhật nếu research
design của ProPulse thay đổi sau khi có evidence mới.
