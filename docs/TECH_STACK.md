# ProPulse --- Technical Stack

**Phiên bản:** 0.1\
**Trạng thái:** Draft / Research Prototype\
**Ngôn ngữ:** Tiếng Việt\
**Dự án:** ProPulse --- Protein Signal Intelligence

------------------------------------------------------------------------

## 1. Mục đích

`TECH_STACK.md` định nghĩa các công nghệ được ProPulse sử dụng, vai trò
của từng công nghệ và phạm vi sử dụng của chúng.

Tài liệu này trả lời:

> **ProPulse dùng công nghệ gì?**

Nó không phải Architecture Decision Record (ADR). Vì vậy, document này
không cố gắng ghi lại toàn bộ lịch sử hoặc lý do của mọi quyết định công
nghệ.

Các lựa chọn được ghi ở đây phản ánh technical baseline hiện tại của
ProPulse. Khi research hoặc engineering requirements thay đổi, stack có
thể được cập nhật.

------------------------------------------------------------------------

## 2. Stack Overview

``` text
Runtime
└── Python 3.12.11

Environment / Dependency Management
└── uv

Scientific Computing
├── NumPy
└── SciPy

Structural Bioinformatics
└── BioPython

Graph / Network Analysis
└── NetworkX

Data Analysis
└── pandas

Visualization
└── Matplotlib

Quantum Computing
├── Qiskit
└── Qiskit Aer

Development
├── pytest
└── Ruff
```

Triết lý chung:

``` text
Python
  ↓
Scientific Python ecosystem
  ↓
Protein / Graph processing
  ↓
Classical + Quantum propagation
  ↓
Connectivity / Ranking
  ↓
Research evaluation
```

------------------------------------------------------------------------

## 3. Runtime

### Python 3.12.11

Python là runtime chính của ProPulse.

``` text
Python = 3.12.11
```

Python được sử dụng cho:

-   scientific core;
-   structure processing;
-   graph processing;
-   classical propagation;
-   quantum propagation;
-   connectivity;
-   ranking;
-   experiments;
-   tests;
-   research tooling.

Python version được pin ở project level để giữ development environment
nhất quán.

File liên quan:

``` text
.python-version
```

------------------------------------------------------------------------

## 4. Environment and Dependency Management

### uv

`uv` là công cụ quản lý Python environment và dependencies của ProPulse.

Vai trò:

-   quản lý Python version;
-   tạo/quản lý project environment;
-   resolve dependencies;
-   lock dependencies;
-   chạy commands trong project environment.

Các artifact chính:

``` text
pyproject.toml
uv.lock
.python-version
.venv/
```

Workflow ưu tiên:

``` bash
uv run ...
```

Ví dụ:

``` bash
uv run pytest
uv run ruff check .
uv run python ...
```

`.venv/` là local development environment và không được commit vào
repository.

------------------------------------------------------------------------

## 5. Scientific Computing

### NumPy

NumPy là nền tảng numerical computing của ProPulse.

Vai trò dự kiến:

-   arrays;
-   vectors;
-   matrices;
-   numerical operations;
-   numerical representations của graph/propagation outputs;
-   connectivity matrices.

NumPy là một dependency nền tảng của scientific core.

------------------------------------------------------------------------

### SciPy

SciPy hỗ trợ các numerical/scientific operations mà NumPy đơn thuần
không cung cấp đầy đủ.

Vai trò dự kiến:

-   linear algebra;
-   matrix operations;
-   numerical methods;
-   sparse structures/operations khi phù hợp;
-   scientific computation liên quan đến propagation.

SciPy đặc biệt quan trọng khi ProPulse làm việc với graph Laplacian,
matrix operators và các bài toán numerical linear algebra.

------------------------------------------------------------------------

## 6. Structural Bioinformatics

### BioPython

BioPython là thư viện chính cho các thao tác biological/structural data
cần thiết trong ProPulse.

Vai trò dự kiến:

-   đọc protein structure;
-   parsing structure information;
-   residue-level representation;
-   biological identifiers;
-   sequence/structure-related processing khi cần.

BioPython nằm ở structural/biological data layer.

Nó không chịu trách nhiệm cho graph propagation hoặc quantum simulation.

------------------------------------------------------------------------

## 7. Graph Computing

### NetworkX

NetworkX là graph library chính của ProPulse ở giai đoạn hiện tại.

Vai trò:

-   biểu diễn protein contact graph;
-   node/edge management;
-   graph traversal;
-   graph properties;
-   graph analysis;
-   hỗ trợ xây dựng các representation cần cho propagation.

Conceptual representation:

``` text
Protein Structure
       ↓
Residue
       ↓
NetworkX Graph
       ↓
Propagation
```

NetworkX là implementation/tooling layer cho graph representation;
mathematical definition của protein contact network vẫn thuộc scientific
design của ProPulse.

------------------------------------------------------------------------

## 8. Data Analysis

### pandas

pandas được sử dụng cho tabular data và research analysis.

Vai trò:

-   residue tables;
-   benchmark metadata;
-   experiment summaries;
-   evaluation tables;
-   result aggregation;
-   export/import tabular artifacts khi phù hợp.

pandas không phải là core representation thay thế graph hoặc matrix
model.

------------------------------------------------------------------------

## 9. Visualization

### Matplotlib

Matplotlib là visualization baseline của ProPulse.

Vai trò:

-   connectivity matrix visualization;
-   ranking plots;
-   classical vs quantum comparisons;
-   experiment/result plots;
-   research figures.

Visualization là supporting layer.

Scientific core không được phụ thuộc vào việc có Matplotlib mới chạy
được.

------------------------------------------------------------------------

## 10. Quantum Computing

### Qiskit

Qiskit là quantum computing framework chính của ProPulse.

Vai trò:

-   xây dựng quantum circuits;
-   quantum simulation workflow;
-   quantum-state / circuit-based experimentation;
-   triển khai quantum propagation research.

Trong architecture của ProPulse:

``` text
propagation/
└── quantum/
    └── ctqw.py
```

Qiskit-specific implementation nên được giữ chủ yếu trong quantum layer
để hạn chế backend leakage sang các biological/data layers.

------------------------------------------------------------------------

### Qiskit Aer

Qiskit Aer là simulator/backend chính được sử dụng cho quantum
experiments trong giai đoạn đầu.

Vai trò:

-   mô phỏng quantum circuits;
-   thử nghiệm CTQW implementation;
-   benchmark quantum propagation;
-   nghiên cứu noise khi experiment yêu cầu.

Aer cho phép phát triển và kiểm tra quantum workflow trước khi phụ thuộc
vào quantum hardware thực tế.

------------------------------------------------------------------------

## 11. Development Tools

### pytest

`pytest` là testing framework của ProPulse.

Vai trò:

-   unit tests;
-   scientific invariant tests;
-   module-level tests;
-   regression tests;
-   validation của data contracts.

Test structure phản ánh core architecture:

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

------------------------------------------------------------------------

### Ruff

Ruff là công cụ linting và formatting baseline.

Vai trò:

-   lint;
-   phát hiện một số vấn đề code;
-   giữ coding style nhất quán;
-   formatting.

Ruff được ưu tiên như một development tool nhẹ thay vì thêm nhiều công
cụ quality riêng lẻ ở giai đoạn prototype.

------------------------------------------------------------------------

## 12. Dependency Classification

ProPulse phân biệt:

``` text
Runtime / Scientific Dependencies
├── numpy
├── scipy
├── biopython
├── networkx
├── pandas
├── matplotlib
├── qiskit
└── qiskit-aer

Development Dependencies
├── pytest
└── ruff
```

Các development dependencies không phải là scientific runtime
requirements của ProPulse.

------------------------------------------------------------------------

## 13. Technology → Project Layer Mapping

  Công nghệ    Layer           Vai trò
  ------------ --------------- ---------------------------------------
  Python       Runtime         Runtime chính
  uv           Environment     Python/dependency management
  NumPy        Scientific      Numerical arrays/matrices
  SciPy        Scientific      Scientific computing / linear algebra
  BioPython    Structure       Protein structure processing
  NetworkX     Graph           Protein contact graph
  pandas       Analysis        Tabular research data
  Matplotlib   Visualization   Research visualization
  Qiskit       Quantum         Quantum computing
  Qiskit Aer   Quantum         Quantum simulation
  pytest       Development     Testing
  Ruff         Development     Linting / formatting

------------------------------------------------------------------------

## 14. Technology Boundaries

### Python

Là runtime chung, không phải abstraction cho mọi layer.

### uv

Quản lý environment/dependencies, không chứa scientific logic.

### BioPython

Tập trung vào biological/structure data.

### NetworkX

Tập trung vào graph representation/analysis.

### NumPy / SciPy

Tập trung numerical computation.

### Qiskit / Aer

Tập trung quantum propagation và quantum experiments.

### pandas

Tập trung tabular analysis.

### Matplotlib

Tập trung visualization.

### pytest / Ruff

Tập trung development quality.

------------------------------------------------------------------------

## 15. What Is Not in the Current Stack

Các công nghệ sau **không thuộc baseline stack hiện tại**:

``` text
PyMOL
Jupyter-specific scientific runtime
PostgreSQL
SQLAlchemy
FastAPI
React
Docker
Kubernetes
ML frameworks
GNN frameworks
QML frameworks ngoài Qiskit
```

Điều này không có nghĩa chúng bị cấm vĩnh viễn.

Chúng chỉ chưa phải dependencies cần thiết của ProPulse core hiện tại.

------------------------------------------------------------------------

## 16. PyMOL Position

PyMOL không phải core dependency.

Vai trò phù hợp:

``` text
ProPulse
   ↓
Prediction / connectivity output
   ↓
Residue mapping
   ↓
PyMOL
   ↓
3D structural inspection
```

PyMOL có thể được sử dụng bên ngoài core để:

-   xem residue prediction trên protein;
-   kiểm tra spatial relationship;
-   hỗ trợ interpretability;
-   tạo structural figures.

ProPulse không cần PyMOL để thực hiện structure → graph → propagation →
connectivity → ranking.

------------------------------------------------------------------------

## 17. Machine Learning Position

ML/GNN/QML không nằm trong baseline stack của v0.1.

Lý do về scope:

``` text
Structure
   ↓
Graph
   ↓
Propagation
   ↓
Connectivity
   ↓
Ranking
```

phải được nghiên cứu và kiểm chứng trước khi thêm một learning layer có
thể làm thay đổi cách prediction được tạo ra.

Các framework ML/QML chỉ nên được thêm khi có research question và
experiment cụ thể.

------------------------------------------------------------------------

## 18. Dependency Addition Rule

Một dependency mới chỉ nên được thêm khi có:

1.  Scientific requirement rõ ràng.
2.  Engineering requirement rõ ràng.
3.  Không có giải pháp đơn giản hơn trong stack hiện tại.
4.  Dependency có compatibility phù hợp với Python 3.12.11.
5.  Dependency không phá vỡ architecture hoặc reproducibility.

Không thêm dependency chỉ vì nó "phổ biến" hoặc "có thể hữu ích sau
này".

------------------------------------------------------------------------

## 19. Versioning and Reproducibility

Project-level version constraints và resolved dependencies được quản lý
qua:

``` text
.python-version
pyproject.toml
uv.lock
```

Khi dependency thay đổi:

``` text
pyproject.toml
      ↓
uv resolution
      ↓
uv.lock
```

Research artifacts quan trọng nên lưu environment/software metadata khi
việc đó cần thiết cho reproducibility.

------------------------------------------------------------------------

## 20. Stack Philosophy

Technology stack của ProPulse tuân theo:

``` text
Minimal
   ↓
Scientific
   ↓
Reproducible
   ↓
Modular
   ↓
Extensible
```

Không tối ưu cho việc có nhiều framework nhất.

Mục tiêu là có **đủ công cụ để nghiên cứu scientific core một cách
nghiêm túc**, nhưng vẫn giữ project nhỏ và dễ thay đổi.

------------------------------------------------------------------------

## 21. Current Stack Summary

``` text
                    ProPulse
                       │
                 Python 3.12.11
                       │
                      uv
                       │
        ┌──────────────┼──────────────┐
        │              │              │
   Bio/Structure      Graph       Numerical
   BioPython         NetworkX    NumPy/SciPy
        │              │              │
        └──────────────┼──────────────┘
                       │
                  Propagation
                  ┌────┴────┐
                  │         │
              Classical   Quantum
                            │
                     Qiskit / Aer
                  └────┬────┘
                       │
                 Connectivity
                       │
                    Ranking
                       │
                  Evaluation
                       │
              pandas / Matplotlib
```

Development support:

``` text
pytest
Ruff
```

------------------------------------------------------------------------

## 22. Relationship with Other Documents

``` text
PRD.md
  → What & Why

DESIGN.md
  → Researcher Experience

ARCHITECTURE.md
  → Technical Structure

SCHEMA.md
  → Data Model

RULES.md
  → Project Rules

TECH_STACK.md
  → Technology Stack
```

`TECH_STACK.md` trả lời **dùng gì**.

Nó không phải nơi lưu toàn bộ lý do lịch sử của các quyết định.

Nếu một technology decision trở thành một architectural decision quan
trọng, decision đó nên được ghi nhận theo cơ chế decision/ADR phù hợp
khi project cần.

------------------------------------------------------------------------

## 23. Future Stack Evolution

Stack có thể phát triển theo research maturity:

``` text
v0.1
Core scientific stack
        ↓
Benchmarking
        ↓
Quantum / noise experiments
        ↓
Visualization
        ↓
ML / GNN / QML
        ↓
Productization
```

Mỗi bước mở rộng phải xuất phát từ nhu cầu thực tế.

Không thêm technology trước khi scientific core yêu cầu nó.

------------------------------------------------------------------------

## 24. Final Principle

> **Technology phục vụ research, không phải research phục vụ
> technology.**

ProPulse không cần một stack lớn.

ProPulse cần một stack đủ mạnh để:

-   xử lý protein structures;
-   xây dựng protein contact graphs;
-   chạy classical propagation;
-   mô phỏng quantum propagation;
-   tạo connectivity;
-   xếp hạng residue;
-   đánh giá prediction;
-   tái lập experiment.

Mọi technology bổ sung trong tương lai phải được đánh giá dựa trên
nguyên tắc này.

------------------------------------------------------------------------

## 25. Source Basis

TECH_STACK này được xây dựng dựa trên:

1.  `PRD.md`.
2.  `DESIGN.md`.
3.  `ARCHITECTURE.md`.
4.  `SCHEMA.md`.
5.  `RULES.md`.
6.  Development environment đã thống nhất của ProPulse.
7.  Technical direction đã thống nhất: Python 3.12.11 + uv + scientific
    Python stack + Qiskit/Qiskit Aer.

Document này mô tả **baseline hiện tại**, không phải cam kết rằng mọi
công nghệ sẽ giữ nguyên trong toàn bộ vòng đời research project.
