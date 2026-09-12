# ProPulse --- Architecture Document

**Phiên bản:** 0.1\
**Trạng thái:** Draft / Research Prototype\
**Ngôn ngữ:** Tiếng Việt\
**Dự án:** ProPulse --- Protein Signal Intelligence

------------------------------------------------------------------------

## 1. Mục đích

`ARCHITECTURE.md` mô tả cấu trúc kỹ thuật của ProPulse, các module
chính, boundary giữa chúng, luồng dữ liệu và các nguyên tắc kiến trúc
cần được giữ trong quá trình phát triển.

ProPulse được thiết kế như một **modular monolith** ở giai đoạn hiện
tại: một codebase, một scientific core, các module có trách nhiệm rõ
ràng và không chia thành microservices hoặc infrastructure phân tán.

Mục tiêu của architecture là bảo vệ scientific pipeline:

``` text
Protein Structure
       ↓
Residue Representation
       ↓
Protein Contact Graph
       ↓
Propagation
   ┌───┴────┐
Classical  Quantum
   └───┬────┘
       ↓
Connectivity
       ↓
Ranking
       ↓
Evaluation
```

Architecture phải hỗ trợ research iteration nhanh nhưng không làm mất
tính reproducibility, modularity và khả năng so sánh giữa classical và
quantum propagation.

------------------------------------------------------------------------

## 2. Architectural Principles

### 2.1. Scientific Core First

`src/propulse/` là trung tâm của hệ thống.

Các lớp như CLI, visualization, notebooks và experiment scripts không
được chứa scientific logic cốt lõi nếu logic đó có thể thuộc reusable
core.

### 2.2. Biological → Graph → Dynamics → Prediction

Architecture phản ánh trực tiếp research abstraction:

``` text
Biological structure
        ↓
Mathematical graph
        ↓
Propagation dynamics
        ↓
Connectivity representation
        ↓
Residue prediction
```

Mỗi bước có boundary riêng.

### 2.3. Classical và Quantum là hai nhánh song song

Propagation layer không được thiết kế theo kiểu quantum là lớp bao ngoài
của classical hoặc classical chỉ là utility cho quantum.

``` text
              propagation
              /          \
       classical         quantum
       /        \            |
 random_walk  diffusion     CTQW
```

Hai nhánh phải có thể được chạy trên cùng protein graph để phục vụ fair
comparison.

### 2.4. Same Graph, Different Propagation

Khi một experiment so sánh classical và quantum, graph representation
phải được giữ nhất quán.

Mục tiêu là cô lập sự khác biệt do propagation model thay vì để graph
construction trở thành một confounding factor.

### 2.5. Explicit Boundaries

Module chỉ chịu trách nhiệm cho concern của chính nó.

Không tạo một `utils/` tổng quát để giải quyết các chức năng chưa được
phân loại rõ.

### 2.6. Reproducibility

Architecture phải cho phép truy nguyên:

``` text
Ranking
  ↓
Connectivity
  ↓
Propagation
  ↓
Graph
  ↓
Residue representation
  ↓
Input structure
```

Các metadata và configuration cần thiết cho research run phải có thể
được lưu ở lớp experiment/output phù hợp.

------------------------------------------------------------------------

## 3. System Overview

ProPulse là một modular monolith:

``` text
                         ProPulse
                            │
                    ┌───────┴───────┐
                    │ Scientific    │
                    │ Core          │
                    └───────┬───────┘
                            │
       ┌────────────────────┼────────────────────┐
       ↓                    ↓                    ↓
  Structure               Graph             Propagation
                                               │
                                      ┌────────┴────────┐
                                      ↓                 ↓
                                  Classical          Quantum
                                  RW/Diffusion         CTQW
                                      └────────┬────────┘
                                               ↓
                                         Connectivity
                                               ↓
                                            Ranking
```

Các thư mục bên ngoài core:

``` text
tests/
experiments/
data/
results/
notebooks/
docs/
```

được xem là supporting project surfaces, không phải các thành phần của
scientific runtime core.

------------------------------------------------------------------------

## 4. Repository Architecture

Structure baseline:

``` text
ProPulse/
│
├── pyproject.toml
├── uv.lock
├── .python-version
├── README.md
├── LICENSE
├── .gitignore
│
├── src/
│   └── propulse/
│       ├── __init__.py
│       ├── __main__.py
│       │
│       ├── structure/
│       │   ├── __init__.py
│       │   ├── loader.py
│       │   └── residue.py
│       │
│       ├── graph/
│       │   ├── __init__.py
│       │   ├── contact.py
│       │   └── graph.py
│       │
│       ├── propagation/
│       │   ├── __init__.py
│       │   ├── classical/
│       │   │   ├── __init__.py
│       │   │   ├── random_walk.py
│       │   │   └── diffusion.py
│       │   └── quantum/
│       │       ├── __init__.py
│       │       └── ctqw.py
│       │
│       ├── connectivity/
│       │   ├── __init__.py
│       │   └── matrix.py
│       │
│       └── ranking/
│           ├── __init__.py
│           └── residue.py
│
├── tests/
│   ├── structure/
│   ├── graph/
│   ├── propagation/
│   │   ├── classical/
│   │   └── quantum/
│   ├── connectivity/
│   └── ranking/
│
├── experiments/
│   └── README.md
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── README.md
│
├── results/
│   └── README.md
│
├── notebooks/
│   └── README.md
│
└── docs/
    └── README.md
```

Đây là architecture baseline. Không phải mọi file phải được implement
đầy đủ ngay ở milestone đầu tiên.

------------------------------------------------------------------------

## 5. Core Modules

### 5.1. `structure/`

Trách nhiệm:

``` text
Protein Structure
       ↓
Residue Representation
```

Module này xử lý việc đọc structure và tạo representation cần thiết cho
các bước tiếp theo.

Các file hiện tại:

``` text
structure/
├── loader.py
└── residue.py
```

`loader.py` chịu trách nhiệm structure loading.

`residue.py` chịu trách nhiệm residue-level representation.

Module này không chịu trách nhiệm xây propagation model hoặc ranking.

------------------------------------------------------------------------

### 5.2. `graph/`

Trách nhiệm:

``` text
Residue Representation
       ↓
Protein Contact Graph
```

Các file:

``` text
graph/
├── contact.py
└── graph.py
```

`contact.py` liên quan đến contact construction.

`graph.py` quản lý graph representation.

Graph abstraction mục tiêu:

``` text
G = (V, E, W)
```

trong đó node đại diện cho residue và edge đại diện cho structural
contact.

Chi tiết contact definition, cutoff và weighting scheme là
research/design decisions riêng và không được hard-code thành assumption
chưa được xác minh chỉ vì architecture đã được tạo.

------------------------------------------------------------------------

### 5.3. `propagation/`

Đây là **research core** của ProPulse.

``` text
propagation/
├── classical/
│   ├── random_walk.py
│   └── diffusion.py
│
└── quantum/
    └── ctqw.py
```

#### Classical

Bao gồm các baseline:

``` text
Random Walk
Graph Diffusion
```

#### Quantum

Bao gồm:

``` text
Continuous-Time Quantum Walk (CTQW)
```

Quantum implementation sử dụng Qiskit làm quantum framework/backend
chính theo technical direction hiện tại của project.

Propagation module nhận graph/operator representation và trả về
propagation-related output cho connectivity layer.

Propagation module không chịu trách nhiệm quyết định final biological
ranking.

------------------------------------------------------------------------

### 5.4. `connectivity/`

Trách nhiệm:

``` text
Propagation Output
       ↓
Connectivity Representation
```

File:

``` text
connectivity/
└── matrix.py
```

Output mục tiêu của challenge là một **N × N connectivity matrix**.

Architecture không khóa trước một connectivity metric duy nhất. Metric
cần được xác định và đánh giá như một research decision.

Connectivity layer nên độc lập với cách propagation được tạo ra ở mức đủ
để có thể áp dụng cùng logic phân tích cho classical và quantum outputs.

------------------------------------------------------------------------

### 5.5. `ranking/`

Trách nhiệm:

``` text
Connectivity
     ↓
Residue Score
     ↓
Ranking
     ↓
Top-5 Candidates
```

File:

``` text
ranking/
└── residue.py
```

Ranking layer chuyển connectivity information thành residue-level
prediction.

Nó không được chứa implementation của graph construction hoặc
propagation.

------------------------------------------------------------------------

## 6. Core Data Flow

Luồng dữ liệu chính:

``` text
Protein Structure
       │
       ▼
structure.loader
       │
       ▼
Residue Representation
       │
       ▼
graph.contact
       │
       ▼
Protein Contact Graph
       │
       ├───────────────────┐
       ▼                   ▼
Classical              Quantum
Propagation            Propagation
       │                   │
       └─────────┬─────────┘
                 ▼
          Connectivity
                 │
                 ▼
             Ranking
                 │
                 ▼
             Evaluation
```

Một implementation cụ thể có thể có thêm intermediate representations,
nhưng không nên phá vỡ các conceptual boundaries trên.

------------------------------------------------------------------------

## 7. Dependency Direction

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

Về conceptual dependency:

-   `structure` không phụ thuộc vào propagation.
-   `graph` phụ thuộc vào structure representation, không phụ thuộc vào
    ranking.
-   `propagation` phụ thuộc vào graph representation.
-   `connectivity` phụ thuộc vào propagation outputs.
-   `ranking` phụ thuộc vào connectivity representation.
-   Higher-level orchestration có thể gọi nhiều module, nhưng module
    thấp hơn không được gọi ngược lên module prediction.

Tránh circular dependency.

Ví dụ không mong muốn:

``` text
graph → ranking → graph
```

hoặc:

``` text
quantum → ranking → quantum
```

------------------------------------------------------------------------

## 8. Separation of Scientific Concerns

ProPulse giữ các concern sau tách biệt:

``` text
Structure concern
    = protein representation

Graph concern
    = structural connectivity

Propagation concern
    = signal/information dynamics

Connectivity concern
    = dynamic connectivity representation

Ranking concern
    = residue prediction
```

Điều này cho phép thay đổi một scientific component mà không phải viết
lại toàn bộ pipeline.

Ví dụ:

``` text
thay đổi CTQW metric
```

không nên yêu cầu:

``` text
viết lại PDB loader
```

Tương tự:

``` text
thay đổi contact construction
```

không nên yêu cầu:

``` text
viết lại ranking engine
```

------------------------------------------------------------------------

## 9. External Project Surfaces

### `tests/`

Chứa automated tests cho các module trong `src/propulse`.

Structure test nên phản ánh core structure:

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

Tests tập trung vào correctness và invariants của implementation.

------------------------------------------------------------------------

### `experiments/`

Chứa experiment-specific logic, configuration hoặc orchestration không
nên trở thành reusable core.

Ví dụ conceptual:

``` text
experiments/
├── classical_vs_quantum/
├── contact_sensitivity/
└── benchmark/
```

Các experiment directories chỉ nên xuất hiện khi experiment thực sự tồn
tại.

Experiment code có thể gọi `src/propulse`, nhưng core không được phụ
thuộc ngược vào experiment.

------------------------------------------------------------------------

### `data/`

Phân tách:

``` text
data/
├── raw/
└── processed/
```

`raw/` chứa input data chưa qua processing.

`processed/` chứa representation/intermediate data phục vụ pipeline.

Data không được hard-code vào source code.

------------------------------------------------------------------------

### `results/`

Chứa output của experiments:

``` text
connectivity
ranking
evaluation
plots
metadata
```

Results không phải source code và không được import ngược vào scientific
core.

------------------------------------------------------------------------

### `notebooks/`

Notebook phục vụ:

-   exploration;
-   visualization;
-   analysis;
-   hypothesis investigation.

Notebook phải gọi logic từ `propulse` thay vì trở thành nơi chứa
implementation độc lập của core algorithm.

------------------------------------------------------------------------

### `docs/`

Chứa documentation của project.

Documentation không được trở thành source code thứ hai.

------------------------------------------------------------------------

## 10. Entry Point Boundary

`src/propulse/__main__.py` tồn tại để hỗ trợ module execution:

``` bash
python -m propulse
```

Entry point chỉ là interface vào application/core orchestration.

Scientific implementation không nên nằm trong `__main__.py`.

Trong prototype, việc chạy project thông qua Python/uv vẫn có thể sử
dụng một minimal execution path.

CLI design có thể phát triển sau khi core workflow ổn định.

------------------------------------------------------------------------

## 11. Quantum Backend Boundary

Qiskit thuộc quantum implementation layer:

``` text
propagation/
└── quantum/
    └── ctqw.py
```

Conceptual separation:

``` text
ProPulse
    ↓
Quantum propagation model
    ↓
Qiskit
    ↓
Simulator / backend
```

ProPulse không nên để Qiskit-specific concepts lan rộng sang:

-   structure;
-   graph;
-   connectivity;
-   ranking;

nếu chúng không thực sự cần thiết.

Mục tiêu là giữ quantum backend có thể được thay đổi hoặc mở rộng mà
không phá vỡ biological/data layers.

------------------------------------------------------------------------

## 12. Scientific Traceability

Một prediction phải có thể truy ngược:

``` text
Top-5 Residue
      ↓
Residue Score
      ↓
Connectivity
      ↓
Propagation Output
      ↓
Propagation Model + Parameters
      ↓
Protein Contact Graph
      ↓
Residue Representation
      ↓
Protein Structure
```

Traceability là architectural requirement quan trọng vì ProPulse không
chỉ cần tạo prediction mà còn cần hỗ trợ interpretation và scientific
validation.

------------------------------------------------------------------------

## 13. Configuration and Parameters

Architecture hiện tại không tạo một configuration framework lớn.

Các scientific parameters chỉ nên được đưa vào abstraction khi thực sự
cần thiết.

Những loại parameter có thể xuất hiện trong research workflow gồm:

``` text
Structure selection
Chain/domain scope
Contact construction
Graph weighting
Propagation parameters
Quantum parameters
Connectivity metric
Ranking method
Evaluation settings
```

Parameter phải có nguồn gốc rõ ràng và không nên bị ẩn trong code.

Các parameter ảnh hưởng trực tiếp đến scientific result cần có khả năng
được ghi lại cùng experiment result.

------------------------------------------------------------------------

## 14. Error Boundaries

Mỗi layer phải chịu trách nhiệm phát hiện lỗi thuộc concern của nó.

Ví dụ:

``` text
structure
→ invalid/missing structure data

graph
→ invalid graph construction

propagation
→ invalid propagation configuration

connectivity
→ invalid/incompatible propagation output

ranking
→ invalid scoring/ranking input
```

Lỗi không nên bị bắt và chuyển thành một kết quả "có vẻ hợp lệ".

Scientific failure phải được thể hiện như failure hoặc limitation.

------------------------------------------------------------------------

## 15. Extensibility

Architecture hiện tại phải cho phép mở rộng theo chiều ngang.

Ví dụ propagation:

``` text
propagation/
├── classical/
│   ├── random_walk.py
│   ├── diffusion.py
│   └── future_model.py
│
└── quantum/
    ├── ctqw.py
    └── future_model.py
```

Nhưng extension chỉ được thêm khi có research requirement thực tế.

Không tạo empty abstractions chỉ để "chuẩn bị cho tương lai".

------------------------------------------------------------------------

## 16. What Is Intentionally Not in the Core

Hiện tại không đưa các thành phần sau vào `src/propulse/`:

``` text
benchmark/
visualization/
ml/
qml/
api/
web/
database/
services/
utils/
```

Điều này là intentional.

Nếu một nhu cầu mới xuất hiện, architecture phải được xem xét dựa trên
responsibility thực tế trước khi tạo module mới.

Đặc biệt, không sử dụng `utils/` như nơi chứa các hàm không có boundary
rõ ràng.

------------------------------------------------------------------------

## 17. Architecture and Research Methodology

Architecture phải bảo vệ research methodology:

``` text
Same Protein
      ↓
Same Graph
      ↓
┌───────────────┐
│               │
▼               ▼
Classical      Quantum
│               │
▼               ▼
Connectivity   Connectivity
│               │
└───────┬───────┘
        ▼
   Fair Evaluation
```

Mục tiêu không phải làm quantum path "đặc biệt" về software
architecture.

Mục tiêu là tạo một environment trong đó quantum và classical có thể
được so sánh một cách công bằng.

------------------------------------------------------------------------

## 18. Architecture Success Criteria

Architecture được xem là phù hợp khi:

1.  Protein structure có thể đi vào core mà không phụ thuộc vào
    experiment-specific code.
2.  Structure có thể được chuyển thành residue representation.
3.  Residue representation có thể được chuyển thành protein contact
    graph.
4.  Classical và quantum propagation có thể sử dụng cùng graph
    representation.
5.  Connectivity có thể được tính độc lập với ranking.
6.  Ranking có thể thay đổi mà không phải sửa propagation.
7.  Experiment code không trở thành dependency của core.
8.  Notebook không chứa scientific implementation độc lập.
9.  Prediction có thể trace ngược về input structure.
10. Architecture vẫn đủ đơn giản để research iteration nhanh.

------------------------------------------------------------------------

## 19. Relationship with Other Documents

``` text
PRD.md
  → What & Why

DESIGN.md
  → Researcher interaction / experience

ARCHITECTURE.md
  → Technical structure / boundaries / data flow

SCHEMA.md
  → Data representation

RULES.md
  → Engineering rules

TECH_STACK.md
  → Technologies and roles
```

`ARCHITECTURE.md` không quyết định chi tiết implementation của từng
thuật toán và không thay thế research methodology.

------------------------------------------------------------------------

## 20. Architectural Principle Summary

``` text
                     ProPulse
                        │
                 Modular Monolith
                        │
              ┌─────────┴─────────┐
              │   Scientific Core │
              └─────────┬─────────┘
                        │
          Structure → Graph → Propagation
                              │
                     ┌────────┴────────┐
                     │                 │
                 Classical          Quantum
                     │                 │
                     └────────┬────────┘
                              ↓
                         Connectivity
                              ↓
                           Ranking
                              ↓
                         Evaluation
```

Nguyên tắc cuối cùng:

> **Architecture phải làm cho scientific question trở nên rõ ràng hơn,
> không được làm nó phức tạp hơn.**

ProPulse được xây dựng như một scientific core có boundary rõ ràng,
trong đó protein structure, graph construction, classical propagation,
quantum propagation, connectivity và ranking có thể được nghiên cứu,
kiểm thử và thay đổi một cách độc lập nhất có thể.

------------------------------------------------------------------------

## 21. Source Basis

Architecture này được xây dựng dựa trên:

1.  Structure baseline đã thống nhất cho ProPulse theo mô hình modular
    monolith.
2.  `PRD.md` của ProPulse.
3.  `DESIGN.md` của ProPulse.
4.  Documentation & Diagram Baseline Guide.
5.  Scientific direction của ProPulse: protein structure → PCN →
    propagation → connectivity → ranking.

Các chi tiết chưa được chốt về mặt scientific, chẳng hạn contact
definition, weighting scheme hoặc connectivity metric cụ thể, được cố ý
để mở thay vì biến thành architectural assumption.
