# ProPulse --- Product Requirements Document (PRD)

**Phiên bản:** 0.1\
**Trạng thái:** Draft / Research Prototype\
**Ngôn ngữ:** Tiếng Việt\
**Dự án:** ProPulse --- Protein Signal Intelligence

------------------------------------------------------------------------

## 1. Tổng quan

ProPulse là một research software project nhằm nghiên cứu cách thông tin
và tín hiệu có thể lan truyền qua cấu trúc protein, với trọng tâm là
**Protein Contact Network (PCN)** và các mô hình propagation trên graph.

Mục tiêu nghiên cứu cốt lõi của ProPulse là xây dựng một pipeline có
thể:

**Protein Structure → Protein Contact Graph → Signal Propagation →
Connectivity → Residue Ranking → Allosteric Site Prediction**

Trong pipeline này, ProPulse sẽ đặt các mô hình propagation cổ điển và
quantum cạnh nhau trên cùng một protein graph để kiểm tra giả thuyết
rằng động lực học graph lượng tử, đặc biệt là **Continuous-Time Quantum
Walk (CTQW)**, có thể cung cấp thông tin hữu ích cho việc nhận diện các
vị trí allosteric và các đường truyền tín hiệu dài hạn.

ProPulse được phát triển trước hết như một **scientific research
engine**, không phải một sản phẩm web hoặc một platform hoàn chỉnh. CLI,
visualization, ML/GNN/QML và các lớp sản phẩm khác chỉ là các lớp mở
rộng được xem xét sau khi scientific core hoạt động và được đánh giá
nghiêm túc.

------------------------------------------------------------------------

## 2. Problem --- Vấn đề cần giải quyết

Allostery là hiện tượng trong đó một tác động tại một vị trí của protein
có thể ảnh hưởng đến một vị trí khác ở xa trong không gian. Việc xác
định các vị trí allosteric và hiểu cách tín hiệu truyền qua cấu trúc
protein có ý nghĩa quan trọng đối với việc nghiên cứu chức năng protein
và tìm kiếm các mục tiêu có thể khai thác bằng thuốc.

Thách thức mà ProPulse tập trung vào đặt câu hỏi:

> Liệu việc mô phỏng sự lan truyền thông tin trên protein bằng các mô
> hình quantum có thể giúp nhận diện các đường truyền allosteric hoặc
> các residue allosteric tốt hơn các mô hình propagation cổ điển hay
> không?

Theo Challenge Statement, bài toán yêu cầu bắt đầu từ cấu trúc protein,
mô phỏng sự lan truyền tín hiệu qua cấu trúc và tạo ra dynamic
connectivity/connectivity giữa các residue, từ đó xếp hạng các residue
có khả năng liên quan đến allosteric communication.

Vấn đề kỹ thuật vì vậy không được xem chủ yếu là bài toán quantum
chemistry. ProPulse tiếp cận nó trước hết như một bài toán:

**Information Propagation on Biological Graphs.**

------------------------------------------------------------------------

## 3. Target Users

### 3.1. Người dùng chính

**Researcher / Computational Biologist**

Người cần một công cụ có thể:

-   chuyển cấu trúc protein thành một graph representation có kiểm soát;
-   chạy các mô hình propagation trên cùng một graph;
-   tạo connectivity information giữa các residue;
-   xếp hạng residue;
-   so sánh classical propagation với quantum propagation;
-   tái lập và phân tích các thí nghiệm.

### 3.2. Người dùng phụ

**Quantum / QML Researcher**

Người quan tâm đến:

-   CTQW trên biological graphs;
-   quantum information propagation;
-   quantum connectivity;
-   noise và scalability;
-   khả năng sử dụng quantum dynamics cho bài toán sinh học.

**Structural Biology / Drug Discovery Researcher**

Người quan tâm đến:

-   predicted allosteric residues/sites;
-   communication pathways;
-   diễn giải kết quả prediction trên cấu trúc 3D protein.

------------------------------------------------------------------------

## 4. Product / Research Goals

### Goal 1 --- Xây dựng scientific core

Xây dựng một pipeline tối thiểu và có thể tái lập:

**Protein Structure → Protein Contact Graph → Propagation → Connectivity
→ Ranking**

### Goal 2 --- So sánh classical và quantum propagation

Trên cùng một protein graph, triển khai và so sánh ít nhất:

-   Classical Random Walk;
-   Classical Graph Diffusion;
-   Quantum Continuous-Time Quantum Walk (CTQW).

Mục tiêu là đảm bảo việc so sánh tập trung vào **mô hình propagation**,
thay vì vô tình thay đổi graph representation giữa hai nhánh.

### Goal 3 --- Tạo connectivity representation có thể đánh giá

ProPulse cần tạo được connectivity information ở cấp residue, bao gồm
mục tiêu đầu ra là **N × N connectivity matrix**, sau đó chuyển thành
residue-level score/ranking.

### Goal 4 --- Predict và rank allosteric residues

Từ connectivity information, ProPulse phải có khả năng tạo ranking
residue và hỗ trợ xác định **top-5 candidate residues**, phù hợp với yêu
cầu của challenge.

### Goal 5 --- Đánh giá scientific hypothesis

Đánh giá liệu quantum propagation có tạo ra signal hữu ích cho
allosteric-site prediction và có cải thiện so với classical baselines
hay không.

ProPulse không được giả định trước rằng quantum propagation sẽ tốt hơn.
Classical và quantum phải được xem là các hypotheses/model families cần
được kiểm chứng bằng cùng một evaluation protocol.

### Goal 6 --- Reproducibility

Các experiment cần có thể truy nguyên được:

-   protein structure được sử dụng;
-   residue/chain mapping;
-   graph construction;
-   propagation model;
-   parameters;
-   connectivity metric;
-   ranking procedure;
-   evaluation result.

------------------------------------------------------------------------

## 5. Non-Goals

Các mục sau **không thuộc mục tiêu của ProPulse core ở giai đoạn hiện
tại**:

### 5.1. Quantum chemistry

ProPulse không nhằm mục tiêu mô phỏng:

-   electronic structure;
-   molecular orbitals;
-   ground-state energy;
-   VQE;
-   QPE;
-   UCCSD;
-   quantum chemistry nói chung,

trừ khi một nghiên cứu tương lai chứng minh rằng các phương pháp này cần
thiết cho bài toán cụ thể.

### 5.2. Classical Molecular Dynamics là solution chính

Challenge không cho phép sử dụng classical MD trajectories như đầu vào
chính. ProPulse vì vậy không xây dựng core prediction pipeline dựa trên
MD trajectories.

Trọng tâm vẫn là **static protein structure → graph → propagation**.

### 5.3. Web platform

ProPulse v0.x không nhằm xây dựng một web application hoặc cloud
platform hoàn chỉnh.

### 5.4. GUI là core requirement

Visualization và 3D inspection có thể hữu ích cho interpretability,
nhưng không phải điều kiện để scientific core hoạt động.

### 5.5. ML/GNN/QML trong core đầu tiên

GNN, QML và các mô hình machine learning sẽ được xem là research
extensions sau khi propagation và connectivity core được kiểm chứng.

Không dùng ML để che khuất câu hỏi liệu signal có thực sự đến từ
propagation model hay không.

### 5.6. Production-scale infrastructure

Database, distributed backend, Kubernetes, production API và các
infrastructure phức tạp không phải mục tiêu của prototype hiện tại.

------------------------------------------------------------------------

## 6. Core Features

### F1 --- Protein Structure Input

ProPulse nhận protein structure làm đầu vào và chuyển đổi cấu trúc thành
representation phù hợp cho graph construction.

Nguồn cấu trúc chính của challenge là **RCSB PDB**.

### F2 --- Residue Representation

Hệ thống cần xác định và quản lý residue-level representation, bao gồm:

-   residue identity;
-   chain;
-   residue numbering / mapping;
-   coordinate information cần thiết cho graph construction.

Chi tiết representation sẽ được xác định trong architecture/schema
documentation và các design decisions tương ứng.

### F3 --- Protein Contact Graph

Từ residue representation, ProPulse xây dựng Protein Contact Graph:

**G = (V, E, W)**

trong đó:

-   node đại diện cho residue;
-   edge đại diện cho structural contact;
-   weight, nếu sử dụng, biểu diễn strength/cost của contact.

Định nghĩa contact, cutoff và weighting scheme là các research/design
decisions cần được đánh giá và ghi nhận rõ ràng.

### F4 --- Classical Propagation

ProPulse cung cấp các classical baselines, trước mắt gồm:

-   Random Walk / Markov propagation;
-   Graph Diffusion.

Các mô hình này đóng vai trò baseline khoa học để so sánh với quantum
propagation.

### F5 --- Quantum Propagation

ProPulse triển khai **Continuous-Time Quantum Walk (CTQW)** trên protein
graph.

Quantum layer sử dụng Qiskit làm quantum computing framework/backend
chính trong giai đoạn đầu.

### F6 --- Connectivity Matrix

Từ propagation process, ProPulse tạo connectivity representation giữa
các residue.

Output mục tiêu:

**N × N connectivity matrix**

Connectivity metric cụ thể phải được định nghĩa rõ ràng và được áp dụng
nhất quán khi so sánh các propagation models.

### F7 --- Residue Ranking

Connectivity information được chuyển thành residue-level score và
ranking.

Output tối thiểu:

**Top-5 candidate residues**

### F8 --- Evaluation

ProPulse đánh giá predictions bằng known allosteric information và các
background controls phù hợp.

Evaluation cần xem xét:

-   ranking quality;
-   top-1 / top-5 / top-k performance khi phù hợp;
-   enrichment;
-   statistical significance;
-   comparison với classical baselines;
-   robustness.

### F9 --- Experiment Reproducibility

Mỗi experiment quan trọng cần có đủ metadata để tái lập và so sánh.

------------------------------------------------------------------------

## 7. Minimum Viable Research Product (MVRP)

MVRP của ProPulse không phải là một GUI hay một package hoàn chỉnh.

MVRP là một **end-to-end scientific vertical slice**:

``` text
Protein Structure
       ↓
Residue Representation
       ↓
Protein Contact Graph
       ↓
Classical Propagation ─────┐
                           ├──→ Connectivity
Quantum CTQW ──────────────┘
                                ↓
                           Residue Ranking
                                ↓
                         Top-5 Candidates
                                ↓
                           Evaluation
```

MVRP được xem là đạt khi ProPulse có thể chạy pipeline trên ít nhất một
protein benchmark, tạo connectivity outputs và ranking, đồng thời cho
phép so sánh classical và quantum bằng cùng một graph và evaluation
procedure.

------------------------------------------------------------------------

## 8. Initial Benchmark Scope

Theo Challenge Statement, bộ validation tối thiểu gồm ba target protein
với apo structure làm input và holo/drug-bound structure làm
reference/validation:

  Target           Apo input   Holo/reference
  ---------------- ----------- ----------------
  KRAS G12C        4OBE        6OIM
  BCR-ABL1         1OPL        5MO4
  Cardiac Myosin   5TBY        6C1H

**c-Myc (1NKP)** là một extension bắt buộc của submission và được xem
xét riêng theo yêu cầu của challenge.

Ngoài bộ core trên, ProPulse hướng tới việc mở rộng benchmark bằng các
protein có allosteric annotation phù hợp, đặc biệt từ Allosteric
Database (ASD), để đánh giá generalizability.

### Nguyên tắc benchmark

-   Apo structure là input chính.
-   Holo/drug-bound information được dùng cho validation/reference.
-   Không đưa thông tin của unknown holo allosteric pocket vào quá trình
    prediction.
-   Cần kiểm soát residue mapping giữa các structure.
-   Core benchmark và development/expansion benchmark nên được phân biệt
    để hạn chế tuning trực tiếp trên validation targets.

------------------------------------------------------------------------

## 9. Requirements

### R1 --- Structure-to-Graph

ProPulse phải chuyển được protein structure thành residue-level contact
graph.

### R2 --- Same-Graph Comparison

Classical và quantum propagation phải có khả năng chạy trên cùng một
graph representation.

### R3 --- Classical Baseline

Phải có ít nhất một classical propagation baseline; mục tiêu ban đầu là
Random Walk và Graph Diffusion.

### R4 --- Quantum Propagation

Phải có CTQW implementation có thể thực nghiệm và benchmark.

### R5 --- Connectivity

Phải tạo được connectivity matrix ở cấp residue.

### R6 --- Ranking

Phải tạo được residue ranking và top-5 candidates.

### R7 --- Validation

Phải so sánh prediction với known allosteric information và background
controls.

### R8 --- Reproducibility

Phải lưu được các thông tin cần thiết để tái lập experiment.

### R9 --- Interpretability

Kết quả cần có khả năng được ánh xạ ngược về residue và cấu trúc protein
để hỗ trợ phân tích.

### R10 --- Scalability / Noise

Sau khi core hoạt động, hệ thống cần có khả năng hỗ trợ các experiment
về:

-   graph size;
-   coarse-graining;
-   computational scaling;
-   noise resilience;
-   circuit depth khi quantum implementation yêu cầu.

------------------------------------------------------------------------

## 10. Constraints

### Scientific constraints

-   Protein structure là starting point.
-   Elastic Network Hypothesis là assumption nền tảng: topology của
    contact network được xem là driver quan trọng của signal
    propagation.
-   Classical MD trajectories không phải input của core prediction
    pipeline.
-   Comparison phải giữ graph representation và evaluation protocol nhất
    quán.

### Challenge constraints

-   Input lấy từ protein structure.
-   Output bao gồm connectivity matrix và top-5 ranking.
-   Phải so sánh với classical analogs.
-   Cần xem xét noise, scalability và interpretability.
-   Challenge cho phép gate-based, quantum-inspired và hybrid
    approaches.

### Engineering constraints

-   Python 3.12.11 là runtime baseline.
-   uv được dùng để quản lý Python environment và dependencies.
-   Qiskit là quantum framework được lựa chọn cho quantum layer.
-   Core phải giữ modular boundaries rõ ràng.
-   Không thêm infrastructure phức tạp khi chưa có nhu cầu scientific rõ
    ràng.

------------------------------------------------------------------------

## 11. Success Criteria

ProPulse sẽ được xem là đạt các milestone chính khi:

### Scientific core

1.  Một protein structure có thể được chuyển thành contact graph.
2.  Classical propagation có thể chạy trên graph.
3.  CTQW có thể chạy trên cùng graph.
4.  Connectivity matrix có thể được tạo từ mỗi propagation model.
5.  Residue ranking và top-5 prediction có thể được sinh ra.

### Scientific evaluation

6.  Classical và quantum được benchmark bằng cùng evaluation protocol.
7.  Prediction được so sánh với known allosteric residues/sites.
8.  Kết quả được so sánh với random/background controls.
9.  Có thể định lượng xem quantum model có cải thiện ranking/prediction
    hay không.

### Research quality

10. Experiment có thể tái lập từ structure + parameters + model
    configuration.
11. Kết quả có thể trace ngược từ ranking → connectivity → propagation →
    graph → structure.
12. Các kết luận được phân biệt rõ giữa fact, hypothesis và empirical
    result.

------------------------------------------------------------------------

## 12. Product Principles

### Principle 1 --- Core First

Scientific core được ưu tiên trước CLI polish, GUI, API và
infrastructure.

### Principle 2 --- Scientific Honesty

ProPulse không giả định quantum advantage trước khi có evidence.

### Principle 3 --- Fair Comparison

Classical và quantum phải được đánh giá trên cùng một biological graph
và cùng protocol khi có thể.

### Principle 4 --- Reproducibility

Mọi kết quả quan trọng phải có provenance và parameters đủ để tái lập.

### Principle 5 --- Minimalism

Chỉ thêm abstraction, dependency hoặc infrastructure khi nó tạo ra giá
trị rõ ràng cho research.

### Principle 6 --- Interpretability

Kết quả cuối cùng phải có khả năng liên hệ trở lại residue và protein
structure.

### Principle 7 --- Research Before Product

ProPulse trước hết là research engine. Productization là bước sau khi
scientific value được chứng minh.

------------------------------------------------------------------------

## 13. Future Extensions

Các hướng sau không thuộc MVRP nhưng có thể được phát triển sau khi core
được kiểm chứng:

-   quantum connectivity metrics nâng cao;
-   noisy quantum simulation;
-   quantum hardware experiments;
-   coarse-grained protein graphs;
-   3D visualization;
-   GNN / QML;
-   hybrid quantum-classical ranking;
-   expanded ASD benchmark;
-   automated benchmark pipelines;
-   researcher-oriented CLI;
-   API / web interface.

Các extension này phải được đánh giá dựa trên giá trị khoa học và không
được làm mờ research question cốt lõi.

------------------------------------------------------------------------

## 14. Core Research Question

Toàn bộ ProPulse có thể được cô đọng thành một câu hỏi:

> **Liệu quantum information propagation trên protein contact networks
> có thể cung cấp một representation của connectivity đủ hữu ích để cải
> thiện việc nhận diện các residue/allosteric sites so với classical
> diffusive propagation hay không?**

Đây là **research hypothesis**, không phải kết luận đã được chứng minh.

ProPulse vì vậy không được xây dựng để "chứng minh quantum tốt hơn".

ProPulse được xây dựng để **kiểm tra một cách công bằng liệu quantum có
thực sự tốt hơn, khác biệt hữu ích hơn, hoặc mang lại information mà
classical propagation không capture được hay không.**

------------------------------------------------------------------------

## 15. Traceability

PRD này là tài liệu định nghĩa **What & Why** của ProPulse.

Các chi tiết implementation và quyết định kỹ thuật phải được quản lý ở
các tài liệu tương ứng:

``` text
PRD.md
  → What & Why

DESIGN.md
  → Product / research experience

ARCHITECTURE.md
  → System structure and module boundaries

SCHEMA.md
  → Data representation

RULES.md
  → Engineering rules

TECH_STACK.md
  → Technologies and their roles
```

Các experiment và empirical results không nên được coi là requirement
chỉ vì một experiment cụ thể cho kết quả tốt. Kết quả thực nghiệm phải
được ghi nhận ở experiment/results documentation tương ứng.

------------------------------------------------------------------------

## 16. Source Basis

PRD này được xây dựng trên:

1.  Cleveland Clinic Challenge Statement --- "Unlocking undruggable
    targets: quantum simulation of allosteric signal propagation".
2.  Documentation & Diagram Baseline Guide của dự án.
3.  Scientific/project direction đã được thống nhất cho ProPulse:
    protein structure → PCN → information propagation → connectivity →
    ranking.

**Lưu ý:** Các mục được định nghĩa như hypothesis, design principle hoặc
future direction không được xem là kết quả khoa học đã được chứng minh.
