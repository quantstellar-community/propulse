# ProPulse --- Workflow

**Phiên bản:** 0.1\
**Trạng thái:** Draft / Research Prototype\
**Ngôn ngữ:** Tiếng Việt\
**Dự án:** ProPulse --- Protein Signal Intelligence

------------------------------------------------------------------------

## 1. Mục đích

`WORKFLOW.md` định nghĩa cách một công việc trong ProPulse đi từ lúc
được xác định cho đến khi hoàn thành và được tích hợp.

Workflow của ProPulse không chỉ là quy trình Git.

Đây là quy trình kết hợp giữa:

``` text
Research Question
        ↓
Task / Issue
        ↓
Assignment
        ↓
Development / Experiment
        ↓
Review
        ↓
Testing / Validation
        ↓
Merge
        ↓
Research Artifact
        ↓
Done
```

Mục tiêu là bảo đảm rằng code, experiment và kết luận khoa học đều có
thể được truy nguyên.

------------------------------------------------------------------------

## 2. Workflow Philosophy

ProPulse là một research software project.

Vì vậy workflow phải bảo vệ đồng thời ba thứ:

``` text
Scientific validity
        +
Software correctness
        +
Reproducibility
```

Không xem một task là hoàn thành chỉ vì:

``` text
code chạy được
```

Một thay đổi chỉ thực sự hoàn thành khi chúng ta hiểu:

-   nó giải quyết vấn đề gì;
-   nó thay đổi thành phần nào;
-   giả định khoa học nào đang được sử dụng;
-   cách kiểm tra nó;
-   kết quả có tái lập được hay không;
-   và nó có làm thay đổi kết luận research hay không.

------------------------------------------------------------------------

## 3. Scope

Workflow này áp dụng cho:

-   feature;
-   bug fix;
-   refactor có ảnh hưởng scientific core;
-   research experiment;
-   benchmark;
-   propagation method;
-   graph representation;
-   connectivity metric;
-   ranking method;
-   data-processing change;
-   test;
-   documentation thay đổi hành vi hoặc research protocol.

Workflow không thay thế:

-   `RULES.md` --- quy tắc project;
-   `ARCHITECTURE.md` --- cấu trúc hệ thống;
-   `SCHEMA.md` --- data model;
-   `TECH_STACK.md` --- technology stack;
-   `ROLES.md` --- trách nhiệm ổn định của từng role;
-   `TEAM.md` --- team structure.

------------------------------------------------------------------------

## 4. High-Level Workflow

``` text
┌──────────────────────┐
│ Research Need        │
│ / Bug / Feature      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Task / Issue         │
│ Define objective     │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Assignment            │
│ Owner + scope         │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Plan / Hypothesis     │
│ Expected behavior     │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Development /         │
│ Experiment            │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Review                │
│ Code + Scientific     │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Testing / Validation  │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Merge                 │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Record Result /       │
│ Artifact              │
└──────────┬───────────┘
           ↓
        DONE
```

------------------------------------------------------------------------

## 5. Workflow States

Một task có thể đi qua các trạng thái:

``` text
Backlog
  ↓
Ready
  ↓
In Progress
  ↓
Review
  ↓
Validation
  ↓
Done
```

Một task có thể quay lại trạng thái trước đó.

Ví dụ:

``` text
Review
  ↓
In Progress
```

nếu review phát hiện vấn đề cần sửa.

Hoặc:

``` text
Validation
  ↓
In Progress
```

nếu experiment/test cho thấy implementation chưa đúng.

Đây không phải failure của workflow.

Đó là một phần bình thường của research development.

------------------------------------------------------------------------

## 6. Stage 1 --- Research Need / Problem Identification

Mọi công việc nên bắt đầu từ một nhu cầu rõ ràng.

Nguồn có thể là:

-   research question;
-   hypothesis;
-   challenge requirement;
-   bug;
-   missing functionality;
-   experiment;
-   benchmark requirement;
-   reproducibility issue;
-   architectural problem;
-   documentation gap.

Ví dụ:

``` text
Research Question:
CTQW có tạo ra connectivity signal hữu ích hơn
classical diffusion trên cùng protein graph hay không?
```

hoặc:

``` text
Engineering Problem:
Connectivity matrix hiện chưa bảo toàn mapping
giữa matrix index và residue identity.
```

Không nên bắt đầu bằng:

``` text
"Chúng ta thử thêm thư viện X xem sao."
```

Technology phải phục vụ research/engineering need.

------------------------------------------------------------------------

## 7. Stage 2 --- Task / Issue Definition

Research need được chuyển thành một task/issue có scope rõ ràng.

Một task tốt nên trả lời:

``` text
What?
Why?
Scope?
Expected output?
How to validate?
```

Template tối thiểu:

``` text
Title:
Objective:
Context:
Scope:
Expected Output:
Validation:
Dependencies:
Scientific Assumptions:
```

Ví dụ:

``` text
Title:
Implement residue-level contact graph

Objective:
Convert an input protein structure into a residue-level graph.

Scope:
Structure → residues → nodes → contacts → edges.

Expected Output:
A graph with stable residue identity mapping.

Validation:
Graph invariants + known structure sanity checks.

Scientific Assumptions:
Residue-level graph is the structural representation
used by the propagation layer.
```

------------------------------------------------------------------------

## 8. Stage 3 --- Assignment

Mỗi task phải có một owner chịu trách nhiệm đưa task đến trạng thái
Done.

Owner chịu trách nhiệm:

-   hiểu scope;
-   đặt câu hỏi khi requirement chưa rõ;
-   thực hiện work;
-   cập nhật status;
-   chuẩn bị test/validation;
-   cung cấp context cho reviewer;
-   ghi nhận kết quả research nếu task là experiment.

Owner không nhất thiết là người duy nhất thực hiện mọi phần của task.

Một task lớn có thể được chia thành sub-tasks.

------------------------------------------------------------------------

## 9. Stage 4 --- Plan / Hypothesis

Trước khi thay đổi scientific core, cần xác định rõ:

``` text
Current behavior
      ↓
Expected behavior
      ↓
Hypothesis
      ↓
Implementation / Experiment
      ↓
Validation
```

Đối với research task, cần phân biệt:

### \[Fact\]

Điều đã được chứng minh hoặc là requirement của project.

### \[Inference\]

Kết luận suy ra từ dữ liệu hoặc kết quả experiment.

### [Hypothesis](#hypothesis-1)

Điều ProPulse đang kiểm chứng.

Ví dụ:

``` text
[Hypothesis]
CTQW có thể tạo ra propagation pattern hữu ích
cho việc phát hiện residue liên quan đến allosteric communication.
```

Không được viết hypothesis như một fact trước khi experiment xác nhận.

------------------------------------------------------------------------

## 10. Stage 5 --- Development / Experiment

Development và research experiment có cùng workflow nền tảng nhưng khác
output.

### Software Development

``` text
Task
 ↓
Implementation
 ↓
Unit Tests
 ↓
Review
 ↓
Validation
```

### Research Experiment

``` text
Research Question
 ↓
Experimental Setup
 ↓
Run
 ↓
Result
 ↓
Analysis
 ↓
Comparison
 ↓
Conclusion
```

Experiment không được chỉ lưu:

``` text
final score
```

Mà cần bảo đảm có đủ context để biết kết quả được tạo ra như thế nào.

------------------------------------------------------------------------

## 11. Core Scientific Pipeline

Workflow nghiên cứu chính của ProPulse phải phản ánh scientific
architecture:

``` text
Protein Structure
       ↓
Residue Representation
       ↓
Protein Contact Graph
       ↓
Graph Representation
       ↓
┌───────────────┴───────────────┐
↓                               ↓
Classical Propagation         CTQW
↓                               ↓
Classical Connectivity       Quantum Connectivity
└───────────────┬───────────────┘
                ↓
        Residue Scoring
                ↓
             Ranking
                ↓
        Top-5 Candidates
                ↓
       Benchmark / Validation
```

Classical và quantum phải bắt đầu từ cùng graph representation khi mục
tiêu là so sánh propagation methods.

Nếu graph representation thay đổi, đó phải được ghi nhận như một biến
experimental factor.

------------------------------------------------------------------------

## 12. Classical vs Quantum Comparison Workflow

Đây là một workflow nghiên cứu đặc biệt quan trọng của ProPulse.

``` text
                    Same Protein
                         ↓
                 Same Graph G
                         ↓
              ┌──────────┴──────────┐
              ↓                     ↓
       Classical Model         Quantum Model
              ↓                     ↓
       Classical Score        Quantum Score
              ↓                     ↓
              └──────────┬──────────┘
                         ↓
                    Comparison
                         ↓
              Biological Evaluation
```

Mục tiêu không phải chứng minh quantum luôn tốt hơn.

Câu hỏi đúng là:

> Quantum propagation có cung cấp thông tin hữu ích mà classical
> propagation không cung cấp, hoặc cung cấp với chất lượng/chi phí tốt
> hơn hay không?

Nếu classical model tốt hơn, workflow phải cho phép ghi nhận kết quả đó.

Nếu quantum không tạo improvement, đó vẫn là một kết quả research hợp
lệ.

------------------------------------------------------------------------

## 13. Stage 6 --- Code Review

Mọi thay đổi có ảnh hưởng đáng kể đến codebase nên được review trước khi
merge.

Review có hai chiều:

### Engineering Review

Kiểm tra:

-   architecture;
-   module boundaries;
-   correctness;
-   readability;
-   tests;
-   dependency impact;
-   maintainability.

### Scientific Review

Kiểm tra:

-   assumption;
-   mathematical formulation;
-   graph representation;
-   propagation definition;
-   metric;
-   interpretation;
-   benchmark methodology;
-   possibility of leakage;
-   validity của comparison.

Một implementation có thể:

``` text
Code đúng
```

nhưng:

``` text
Research methodology sai
```

và ngược lại.

Vì vậy ProPulse không xem code review là đủ cho research validation.

------------------------------------------------------------------------

## 14. Stage 7 --- Testing

Testing của ProPulse có nhiều tầng.

``` text
Unit Test
    ↓
Module Test
    ↓
Scientific Invariant
    ↓
Integration Test
    ↓
Experiment Validation
```

### Unit Test

Kiểm tra behavior của một function/class/module.

### Module Test

Kiểm tra một scientific component hoàn chỉnh.

Ví dụ:

``` text
Structure loader
Graph construction
Random walk
Diffusion
CTQW
Connectivity
Ranking
```

### Scientific Invariant

Kiểm tra các property phải đúng theo formulation.

Ví dụ:

-   kích thước matrix;
-   residue identity mapping;
-   graph consistency;
-   normalization;
-   symmetry khi formulation yêu cầu;
-   probability constraints khi phù hợp;
-   deterministic behavior khi cấu hình yêu cầu deterministic behavior.

### Integration Test

Kiểm tra pipeline:

``` text
Structure
 → Graph
 → Propagation
 → Connectivity
 → Ranking
```

### Experiment Validation

Kiểm tra result trên benchmark hoặc controlled experiment.

------------------------------------------------------------------------

## 15. Stage 8 --- Research Validation

Một scientific change chưa Done chỉ vì test pass.

Cần xác định:

``` text
Does the implementation produce
the behavior we scientifically intended?
```

Validation có thể gồm:

-   sanity check;
-   synthetic graph;
-   known analytical case;
-   benchmark protein;
-   classical comparison;
-   parameter sensitivity;
-   robustness test;
-   ablation.

Đặc biệt, các thay đổi liên quan đến contact definition, Hamiltonian,
propagation time hoặc connectivity metric có thể thay đổi kết quả
biological.

Các thay đổi này cần được xem là research-sensitive changes.

------------------------------------------------------------------------

## 16. Stage 9 --- Benchmark Validation

Benchmark phải được thực hiện theo protocol đã xác định.

Core challenge workflow:

``` text
Apo Structure
     ↓
Graph Construction
     ↓
Propagation
     ↓
Connectivity
     ↓
Residue Ranking
     ↓
Top-5
     ↓
Validation against reference
```

Holo structure/reference không được âm thầm đưa vào input nếu protocol
yêu cầu blind prediction từ apo structure.

Benchmark evaluation cần phân biệt:

``` text
Prediction Input
        ≠
Validation Reference
```

Điều này đặc biệt quan trọng để tránh data leakage.

------------------------------------------------------------------------

## 17. Stage 10 --- Experiment Reproducibility

Mỗi experiment quan trọng phải có đủ metadata để tái lập.

Tối thiểu nên xác định:

``` text
protein:
  pdb_id:
  chain:

structure:
  source:
  representation:

graph:
  node_definition:
  edge_definition:
  parameters:

propagation:
  method:
  parameters:
  time:
  steps:

connectivity:
  metric:

ranking:
  metric:

software:
  propulse_version:
  environment:

result:
  artifact:
```

Ví dụ conceptual record:

``` text
protein:
  pdb_id: 4OBE

propagation:
  method: CTQW
  Hamiltonian: ...
  time: ...
  steps: ...

ranking:
  metric: ...

software:
  propulse_version: ...
```

Mục tiêu:

> Nếu kết quả thay đổi, chúng ta phải có khả năng xác định nó thay đổi
> vì graph, algorithm, parameter, data hoặc software.

------------------------------------------------------------------------

## 18. Stage 11 --- Merge

Một task chỉ được merge khi:

-   scope đã hoàn thành;
-   code review đã xử lý;
-   tests phù hợp đã pass;
-   scientific validation đã được thực hiện nếu task ảnh hưởng research;
-   documentation được cập nhật khi behavior/protocol thay đổi;
-   không còn known blocker.

Merge không có nghĩa:

``` text
Hypothesis = true
```

Merge chỉ có nghĩa:

``` text
Implementation / experiment artifact
đã đạt tiêu chuẩn để tích hợp vào project.
```

------------------------------------------------------------------------

## 19. Stage 12 --- Research Artifact

Sau khi merge, research-sensitive work nên tạo artifact có thể truy
nguyên.

Artifact có thể là:

``` text
result/
├── connectivity/
├── rankings/
├── plots/
├── benchmark/
└── metadata/
```

Tên và cấu trúc cụ thể của artifact có thể thay đổi theo experiment.

Quan trọng nhất là provenance:

``` text
Input
 ↓
Configuration
 ↓
Software
 ↓
Execution
 ↓
Output
```

------------------------------------------------------------------------

## 20. Definition of Done

Một task được xem là **Done** khi tất cả điều kiện phù hợp đã hoàn
thành.

### Engineering

-   [ ] Scope đã hoàn thành.
-   [ ] Architecture không bị phá vỡ.
-   [ ] Code được review.
-   [ ] Tests phù hợp đã pass.
-   [ ] Không có known blocker.

### Scientific

-   [ ] Scientific assumption đã được ghi nhận.
-   [ ] Mathematical behavior đã được kiểm tra khi cần.
-   [ ] Kết quả không bị diễn giải quá mức.
-   [ ] Comparison protocol được giữ công bằng.
-   [ ] Benchmark/validation đã thực hiện khi task yêu cầu.

### Reproducibility

-   [ ] Input được xác định.
-   [ ] Parameters được ghi nhận.
-   [ ] Software/environment được xác định khi cần.
-   [ ] Output artifact có thể truy nguyên.
-   [ ] Documentation được cập nhật nếu protocol/behavior thay đổi.

Không phải task nào cũng cần toàn bộ checklist trên, nhưng owner phải
xác định rõ phần nào áp dụng.

------------------------------------------------------------------------

## 21. Research Experiment Lifecycle

Experiment lớn nên đi theo lifecycle riêng:

``` text
Question
  ↓
Hypothesis
  ↓
Protocol
  ↓
Implementation
  ↓
Controlled Run
  ↓
Result
  ↓
Analysis
  ↓
Comparison
  ↓
Ablation / Robustness
  ↓
Conclusion
  ↓
Record
```

### Question

Câu hỏi cần được trả lời.

### Hypothesis

Dự đoán có thể bị bác bỏ.

### Protocol

Quy định cách experiment được chạy trước khi xem kết quả cuối.

### Controlled Run

Chạy với điều kiện được kiểm soát.

### Result

Raw/derived output.

### Analysis

Phân tích quantitative/qualitative.

### Comparison

So sánh với baseline phù hợp.

### Ablation / Robustness

Kiểm tra kết quả có phụ thuộc vào một assumption/parameter cụ thể hay
không.

### Conclusion

Kết luận phải phân biệt fact, inference và hypothesis.

------------------------------------------------------------------------

## 22. Core Research Order

ProPulse ưu tiên phát triển theo thứ tự:

``` text
1. Structure
       ↓
2. Protein Contact Graph
       ↓
3. Classical Propagation
       ↓
4. Quantum Propagation / CTQW
       ↓
5. Connectivity
       ↓
6. Ranking
       ↓
7. Benchmark
       ↓
8. Classical vs Quantum Comparison
       ↓
9. Robustness / Scalability
       ↓
10. ML / GNN / QML Extension
```

ML/GNN/QML không phải prerequisite của core pipeline.

Core phải có khả năng trả lời:

> Connectivity derived from graph propagation có dự đoán được biological
> signal hay không?

trước khi thêm learning layer.

------------------------------------------------------------------------

## 23. Change Classification

Mọi thay đổi nên được xem xét theo mức ảnh hưởng.

### Type A --- Documentation

Ví dụ:

``` text
README
docs
comments
```

Nếu không thay đổi behavior, workflow có thể nhẹ hơn.

### Type B --- Engineering

Ví dụ:

``` text
refactor
test
internal implementation
performance
```

Cần engineering review và testing phù hợp.

### Type C --- Scientific-Sensitive

Ví dụ:

``` text
contact definition
edge weighting
Hamiltonian
propagation dynamics
time evolution
connectivity metric
ranking metric
benchmark protocol
```

Cần scientific review và validation.

### Type D --- Research Protocol

Ví dụ:

``` text
thay đổi benchmark set
thay đổi evaluation metric
thay đổi negative/background control
thay đổi input/reference separation
```

Phải được ghi nhận rõ vì có thể làm thay đổi khả năng so sánh giữa các
experiment.

------------------------------------------------------------------------

## 24. Parameter Change Workflow

Không nên coi mọi parameter là implementation detail.

Các parameter có thể ảnh hưởng trực tiếp đến scientific result.

Ví dụ:

``` text
Graph
├── contact cutoff
├── edge weighting
└── residue representation

Propagation
├── model
├── Hamiltonian
├── evolution time
└── discretization / simulation parameters

Connectivity
└── metric

Ranking
└── scoring rule
```

Khi một parameter scientific-sensitive thay đổi:

``` text
Parameter Change
      ↓
Record
      ↓
Controlled Experiment
      ↓
Compare
      ↓
Accept / Reject
```

Không chỉ thay đổi parameter rồi lấy result mới làm default mà không có
comparison.

------------------------------------------------------------------------

## 25. Classical Baseline Workflow

Classical baseline phải được phát triển như một research baseline thực
sự.

``` text
Same Graph
    ↓
Classical Propagation
    ↓
Connectivity
    ↓
Ranking
    ↓
Benchmark
```

Các family có thể được nghiên cứu:

``` text
Random Walk
Diffusion
Graph Laplacian
Communicability
```

Không cần triển khai tất cả ngay từ đầu.

Điểm quan trọng là classical model không được biến thành strawman để
quantum dễ thắng.

------------------------------------------------------------------------

## 26. Quantum Workflow

Quantum workflow hiện tại tập trung vào CTQW:

``` text
Protein Graph
      ↓
Graph Hamiltonian
      ↓
CTQW
      ↓
Quantum Evolution
      ↓
Connectivity
      ↓
Ranking
      ↓
Validation
```

Qiskit/Qiskit Aer là implementation/simulation tooling.

Quantum framework không được phép quyết định scientific definition một
cách âm thầm.

Ví dụ:

``` text
Qiskit implementation detail
```

không đồng nghĩa với:

``` text
biological connectivity definition
```

Scientific definition phải được xác định ở research/model layer.

------------------------------------------------------------------------

## 27. Review Gate for Scientific Core

Các thay đổi trong những module sau nên có review nghiêm túc trước khi
merge:

``` text
structure/
graph/
propagation/
connectivity/
ranking/
```

Đặc biệt:

``` text
graph/contact.py
propagation/classical/*
propagation/quantum/*
connectivity/*
ranking/*
```

Reviewer cần hỏi:

``` text
1. Có đúng với mathematical model không?
2. Có đúng với data contract không?
3. Có giữ được residue identity không?
4. Có thay đổi graph topology không?
5. Có thay đổi comparison fairness không?
6. Có ảnh hưởng benchmark không?
7. Có test/invariant tương ứng không?
```

------------------------------------------------------------------------

## 28. Bug Workflow

Bug không nên chỉ được sửa bằng cách thay đổi code cho đến khi test
pass.

Workflow:

``` text
Bug Report
    ↓
Reproduce
    ↓
Isolate
    ↓
Identify Root Cause
    ↓
Write / Update Test
    ↓
Fix
    ↓
Run Regression Tests
    ↓
Review
    ↓
Merge
```

Đối với scientific bug:

``` text
Bug
 ↓
Affected Results
 ↓
Identify Experiments
 ↓
Re-run if necessary
 ↓
Record Impact
```

Nếu bug làm thay đổi published/internal research result, phải ghi nhận
impact thay vì chỉ sửa code.

------------------------------------------------------------------------

## 29. Performance Workflow

Performance optimization phải đi sau correctness.

``` text
Correctness
    ↓
Profiling
    ↓
Identify Bottleneck
    ↓
Optimize
    ↓
Regression Test
    ↓
Benchmark
```

Không tối ưu một scientific component chỉ vì nó "trông chậm" nếu chưa đo
bottleneck.

Đặc biệt với quantum simulation:

``` text
Graph size
↓
Hamiltonian structure
↓
Simulation method
↓
Circuit/simulation cost
```

phải được đo thay vì giả định.

------------------------------------------------------------------------

## 30. Branch / Integration Workflow

Git workflow có thể giữ đơn giản:

``` text
main
  ↑
feature / experiment branch
```

Một thay đổi lớn nên được phát triển trên branch riêng.

Ví dụ:

``` text
feature/contact-graph
feature/ctqw
experiment/classical-baseline
experiment/quantum-metric
fix/residue-mapping
```

Branch name phải mô tả mục đích.

Không dùng branch như nơi thay thế cho task tracking.

------------------------------------------------------------------------

## 31. Commit Workflow

Commit nên phản ánh một logical change.

Ví dụ:

``` text
Add residue parser
Build contact graph
Add diffusion propagation
Implement CTQW simulator
Add connectivity metric
Add ranking tests
Fix residue mapping
```

Tránh commit kiểu:

``` text
update
fix
stuff
changes
final
final2
```

Đối với research experiment, commit nên giúp truy ngược implementation
state tương ứng với experiment.

------------------------------------------------------------------------

## 32. Documentation Update Workflow

Nếu một thay đổi làm thay đổi:

``` text
What
```

→ cập nhật `PRD.md` nếu scope/product requirement thay đổi.

Nếu thay đổi:

``` text
Experience / interface
```

→ cập nhật `DESIGN.md`.

Nếu thay đổi:

``` text
System structure / module boundary
```

→ cập nhật `ARCHITECTURE.md`.

Nếu thay đổi:

``` text
Data model / contract
```

→ cập nhật `SCHEMA.md`.

Nếu thay đổi:

``` text
Project rule
```

→ cập nhật `RULES.md`.

Nếu thay đổi:

``` text
Technology
```

→ cập nhật `TECH_STACK.md`.

Nếu thay đổi:

``` text
How work moves through the project
```

→ cập nhật `WORKFLOW.md`.

------------------------------------------------------------------------

## 33. Workflow for Adding a New Scientific Component

Ví dụ muốn thêm một propagation method:

``` text
Research Question
       ↓
Define Mathematical Model
       ↓
Define Interface
       ↓
Define Test Cases
       ↓
Implement
       ↓
Validate on Synthetic Graph
       ↓
Compare with Existing Methods
       ↓
Run Protein Benchmark
       ↓
Review
       ↓
Merge
```

Không bắt đầu bằng việc viết implementation trước khi biết:

``` text
input
output
mathematical behavior
validation
```

------------------------------------------------------------------------

## 34. Workflow for a New Connectivity Metric

Một connectivity metric mới phải trải qua:

``` text
Motivation
   ↓
Definition
   ↓
Mathematical Properties
   ↓
Synthetic Validation
   ↓
Classical / Quantum Evaluation
   ↓
Protein Benchmark
   ↓
Sensitivity Analysis
   ↓
Review
   ↓
Adopt / Reject
```

Một metric không nên được chấp nhận chỉ vì nó tạo ra top-5 đẹp trên một
protein.

------------------------------------------------------------------------

## 35. Workflow for Ranking

Ranking là một bước riêng biệt với connectivity.

``` text
Connectivity Matrix
       ↓
Residue Score
       ↓
Ranking
       ↓
Top-k
```

Khi thay đổi ranking metric, phải phân biệt:

``` text
Propagation improvement
```

với:

``` text
Ranking improvement
```

Nếu không, chúng ta có thể kết luận sai rằng quantum propagation tốt hơn
trong khi improvement thực tế đến từ ranking rule.

------------------------------------------------------------------------

## 36. Data Leakage Gate

Trước benchmark, cần kiểm tra:

``` text
Input
  ↓
Does input contain information
about validation target?
  ↓
YES → STOP / redesign
NO  → continue
```

Đặc biệt phải xem xét:

-   holo structure;
-   known allosteric pocket;
-   ligand-bound geometry;
-   annotations không được phép dùng trong input;
-   data derived trực tiếp từ validation reference.

Mục tiêu:

> Prediction phải thực sự là prediction.

------------------------------------------------------------------------

## 37. Failure Workflow

Research có thể thất bại.

Workflow không được ép team biến mọi experiment thành positive result.

``` text
Experiment
   ↓
Negative / Null Result
   ↓
Check Implementation
   ↓
Check Protocol
   ↓
Check Baseline
   ↓
Analyze
   ↓
Record
```

Nếu implementation đúng nhưng hypothesis không được hỗ trợ:

``` text
Hypothesis rejected / unsupported
```

được xem là kết quả hợp lệ.

Nguyên tắc:

> **Experiment phải được phép chứng minh hypothesis của ProPulse là
> sai.**

------------------------------------------------------------------------

## 38. Final Review Gate

Trước khi đóng một research-sensitive task, reviewer nên có khả năng trả
lời:

``` text
[ ] Tôi hiểu task đang giải quyết vấn đề gì.
[ ] Tôi hiểu assumption chính.
[ ] Tôi biết input và output.
[ ] Tôi biết cách implementation được validation.
[ ] Tôi biết kết quả có thể tái lập như thế nào.
[ ] Tôi biết comparison có fair hay không.
[ ] Tôi biết change có ảnh hưởng scientific conclusion hay không.
```

Nếu chưa trả lời được, task chưa nên được coi là hoàn toàn Done.

------------------------------------------------------------------------

## 39. Workflow Summary

Workflow chuẩn của ProPulse:

``` text
                RESEARCH / ENGINEERING NEED
                           ↓
                       TASK / ISSUE
                           ↓
                        ASSIGNMENT
                           ↓
                   PLAN / HYPOTHESIS
                           ↓
                 DEVELOPMENT / EXPERIMENT
                           ↓
                  CODE + SCIENTIFIC REVIEW
                           ↓
                    TEST / VALIDATION
                           ↓
                       BENCHMARK
                           ↓
                          MERGE
                           ↓
                 RESULT / ARTIFACT RECORD
                           ↓
                          DONE
```

Với research experiment:

``` text
Question
  ↓
Hypothesis
  ↓
Protocol
  ↓
Run
  ↓
Result
  ↓
Analysis
  ↓
Comparison
  ↓
Conclusion
  ↓
Record
```

Với core scientific pipeline:

``` text
Protein
  ↓
Residue
  ↓
Protein Contact Graph
  ↓
Classical / Quantum Propagation
  ↓
Connectivity
  ↓
Ranking
  ↓
Top-5
  ↓
Benchmark / Validation
```

------------------------------------------------------------------------

## 40. Relationship with Other Documents

``` text
PRD.md
  ↓
Why are we building this?

DESIGN.md
  ↓
How should researchers interact with it?

ARCHITECTURE.md
  ↓
How is the system structured?

SCHEMA.md
  ↓
What data does it operate on?

RULES.md
  ↓
What rules must development follow?

TECH_STACK.md
  ↓
What technologies do we use?

TEAM.md
  ↓
Who is the team?

ROLES.md
  ↓
Who owns which responsibilities?

WORKFLOW.md
  ↓
How does work move from idea to Done?
```

`WORKFLOW.md` không phải task manager.

Nó định nghĩa **đường đi của công việc**.

------------------------------------------------------------------------

## 41. Final Principle

> **ProPulse không chỉ cần code có thể chạy; ProPulse cần một quy trình
> mà từ một research question, chúng ta có thể đi đến một result có thể
> kiểm tra, so sánh và tái lập.**

Workflow tốt phải bảo vệ cả:

``` text
Scientific Integrity
        +
Engineering Quality
        +
Research Reproducibility
```

Và trong mọi trường hợp:

> **Scientific correctness \> research validity \> reproducibility \>
> maintainability \> performance \> convenience.**
