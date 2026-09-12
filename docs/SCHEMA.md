# ProPulse --- Schema Document

**Phiên bản:** 0.1\
**Trạng thái:** Draft / Research Prototype\
**Ngôn ngữ:** Tiếng Việt\
**Dự án:** ProPulse --- Protein Signal Intelligence

------------------------------------------------------------------------

## 1. Mục đích

`SCHEMA.md` định nghĩa cách ProPulse biểu diễn và liên kết các loại dữ
liệu đi qua scientific pipeline.

ProPulse hiện không sử dụng một relational database làm trung tâm. Vì
vậy, `SCHEMA.md` **không phải database schema theo nghĩa truyền thống**.

Thay vào đó, tài liệu này mô tả **data model và data contracts** của
research pipeline:

``` text
Protein Structure
       ↓
Residue Representation
       ↓
Protein Contact Graph
       ↓
Propagation
       ↓
Connectivity
       ↓
Residue Score
       ↓
Ranking
       ↓
Evaluation
```

Mục tiêu của schema là bảo đảm rằng dữ liệu có thể được truy nguyên từ
prediction cuối cùng về protein structure ban đầu và có thể so sánh
classical với quantum một cách nhất quán.

------------------------------------------------------------------------

## 2. Schema Principles

### 2.1. Biological identity must be preserved

Mỗi residue trong graph, connectivity và ranking phải có thể ánh xạ trở
lại residue cụ thể trong protein structure.

### 2.2. Structure and graph are different representations

Protein structure là biological representation.

Protein Contact Graph là mathematical representation được tạo từ
structure.

Không được coi hai representation này là cùng một object.

### 2.3. Propagation output is not prediction

Propagation tạo ra dynamic/information-flow representation.

Connectivity chuyển propagation output thành connectivity
representation.

Ranking mới tạo residue-level prediction.

Không trộn các bước này thành một object duy nhất.

### 2.4. Classical and quantum use compatible data contracts

Classical và quantum propagation phải có thể nhận cùng một graph
representation và tạo output có thể đưa qua cùng
connectivity/ranking/evaluation pipeline khi experiment yêu cầu fair
comparison.

### 2.5. Scientific parameters are data

Các parameter ảnh hưởng đến scientific result phải được ghi nhận cùng
experiment metadata thay vì ẩn trong implementation.

### 2.6. No premature database abstraction

Schema hiện tại không giả định PostgreSQL, ORM hay một database server.

File-based artifacts, in-memory objects và future persistent storage đều
có thể tuân theo cùng conceptual schema.

------------------------------------------------------------------------

## 3. High-Level Data Model

``` text
┌────────────────────┐
│ Protein Structure  │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ Residue            │
│ Representation     │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ Protein Contact    │
│ Graph              │
└─────────┬──────────┘
          │
     ┌────┴─────┐
     ▼          ▼
┌──────────┐ ┌──────────┐
│Classical │ │ Quantum  │
│Propagation│ │Propagation│
└────┬─────┘ └────┬─────┘
     │            │
     └─────┬──────┘
           ▼
┌────────────────────┐
│ Connectivity       │
│ Matrix              │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ Residue Score      │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ Ranking / Top-5    │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│ Evaluation /       │
│ Reference          │
└────────────────────┘
```

------------------------------------------------------------------------

## 4. Core Entities

### 4.1. Protein Structure

Đại diện cho một structure input.

Conceptual fields:

  -----------------------------------------------------------------------
  Field                   Ý nghĩa                 Trạng thái
  ----------------------- ----------------------- -----------------------
  `structure_id`          Identifier của          Bắt buộc
                          structure               

  `source`                Nguồn structure         Bắt buộc

  `chains`                Các chain thuộc         Bắt buộc
                          structure scope         

  `resolution`            Resolution nếu source   Tùy source
                          cung cấp                

  `structure_type`        Ví dụ apo/reference nếu Theo experiment
                          experiment định nghĩa   

  `provenance`            Thông tin nguồn/version Bắt buộc cho
                                                  reproducibility
  -----------------------------------------------------------------------

`structure_id` phải đủ để truy nguyên về structure input thực tế.

Trong challenge, RCSB PDB là nguồn structure chính.

------------------------------------------------------------------------

### 4.2. Chain

Chain là đơn vị định danh trung gian giữa structure và residue.

Conceptual fields:

  Field            Ý nghĩa
  ---------------- -------------------------
  `chain_id`       Chain identifier
  `structure_id`   Structure sở hữu chain
  `residues`       Các residue thuộc chain

Quan hệ:

``` text
Protein Structure
      1
      │
      ├──────< Chain
                   1
                   │
                   └──────< Residue
```

------------------------------------------------------------------------

### 4.3. Residue

Residue là node biological cơ bản của ProPulse.

Conceptual fields:

  -----------------------------------------------------------------------
  Field                   Ý nghĩa                 Trạng thái
  ----------------------- ----------------------- -----------------------
  `residue_id`            Internal stable         Bắt buộc
                          identifier              

  `structure_id`          Structure chứa residue  Bắt buộc

  `chain_id`              Chain chứa residue      Bắt buộc

  `residue_name`          Tên residue             Bắt buộc

  `residue_number`        Residue numbering từ    Bắt buộc
                          structure               

  `insertion_code`        Insertion code nếu có   Tùy structure

  `coordinates`           Coordinate              Bắt buộc
                          representation cần cho  
                          graph construction      

  `sequence_index`        Vị trí trong sequence   Tùy representation
                          representation nếu có   

  `mapping_metadata`      Thông tin mapping       Khi cần
  -----------------------------------------------------------------------

### Biological identity

`residue_id` là identifier nội bộ của ProPulse.

Biological identity vẫn phải giữ:

``` text
structure_id
+
chain_id
+
residue_number
+
insertion_code
```

khi các field này tồn tại.

Không nên dùng một integer index của matrix làm biological identity.

Ví dụ:

``` text
Matrix index 57
```

không tự nó có nghĩa là:

``` text
Residue 57
```

Mapping phải được lưu rõ ràng.

------------------------------------------------------------------------

## 5. Residue Coordinate Representation

Graph construction cần một representation của vị trí residue trong không
gian.

Schema chỉ yêu cầu:

``` text
Residue
   ↓
coordinate representation
```

đủ để tính structural contacts.

**Chưa chốt**:

-   Cα coordinate;
-   centroid;
-   heavy-atom representation;
-   minimum atom-atom distance;
-   hay representation khác.

Đây là scientific/design decision và phải được ghi nhận ở tài liệu hoặc
experiment tương ứng trước khi trở thành fixed schema assumption.

------------------------------------------------------------------------

## 6. Protein Contact Graph

Protein Contact Graph là mathematical representation của protein
structure.

Conceptual definition:

``` text
G = (V, E, W)
```

### 6.1. Graph

  -----------------------------------------------------------------------
  Field                               Ý nghĩa
  ----------------------------------- -----------------------------------
  `graph_id`                          Identifier của graph

  `structure_id`                      Structure nguồn

  `nodes`                             Residue nodes

  `edges`                             Structural contacts

  `directed`                          Graph directed/undirected nếu model
                                      yêu cầu

  `weighted`                          Có sử dụng edge weights hay không

  `construction_metadata`             Parameters/provenance của graph
                                      construction
  -----------------------------------------------------------------------

### 6.2. Node

Một graph node ánh xạ một-một về một residue trong graph scope.

Conceptual representation:

``` text
Node
├── node_id
└── residue_id
```

`node_id` có thể khác `residue_id`.

Điều này cho phép mathematical indexing độc lập với biological identity.

### 6.3. Edge

Một edge biểu diễn structural contact giữa hai residue nodes.

Conceptual fields:

  Field                Ý nghĩa
  -------------------- -----------------------------------
  `source_node`        Node đầu
  `target_node`        Node cuối
  `weight`             Contact weight nếu graph weighted
  `contact_metadata`   Metadata về contact construction

Với graph undirected, `(i, j)` và `(j, i)` biểu diễn cùng structural
relation về mặt biological.

------------------------------------------------------------------------

## 7. Contact Construction Metadata

Graph construction là một nguồn scientific variability quan trọng.

Schema phải có khả năng ghi nhận các thông tin như:

``` text
contact method
distance / contact criterion
cutoff
weighting scheme
included chains
included residues
excluded residues
structure source
```

Schema **không chốt một cutoff cụ thể trong v0.1**.

Ví dụ conceptual metadata:

``` text
construction_method = ...
distance_metric = ...
cutoff = ...
weighting = ...
```

Các giá trị cụ thể thuộc research experiment/design decision.

------------------------------------------------------------------------

## 8. Propagation Configuration

Propagation configuration mô tả cách một experiment chạy propagation.

Conceptual fields:

  Field                       Ý nghĩa
  --------------------------- -------------------------------------------
  `model_family`              `classical` hoặc `quantum`
  `model_name`                Ví dụ `random_walk`, `diffusion`, `ctqw`
  `parameters`                Model-specific parameters
  `initial_condition`         Initial signal/state
  `time_settings`             Time/step configuration nếu model sử dụng
  `graph_id`                  Graph được sử dụng
  `implementation_metadata`   Backend/version nếu cần reproducibility

Propagation configuration phải tham chiếu đến graph cụ thể.

------------------------------------------------------------------------

## 9. Classical Propagation Result

Classical propagation hiện tại gồm:

``` text
Random Walk
Graph Diffusion
```

Schema không yêu cầu hai model phải có cùng internal numerical
representation.

Tuy nhiên output phải có một contract đủ để connectivity layer xử lý.

Conceptual:

``` text
ClassicalPropagationResult
├── graph_id
├── model
├── parameters
└── propagation_output
```

`propagation_output` có thể là state/vector/trajectory hoặc
representation phù hợp với model.

Không nên hard-code một output type duy nhất khi research model chưa
được chốt hoàn toàn.

------------------------------------------------------------------------

## 10. Quantum Propagation Result

Quantum propagation hiện tại tập trung vào:

``` text
Continuous-Time Quantum Walk (CTQW)
```

Conceptual:

``` text
QuantumPropagationResult
├── graph_id
├── model = ctqw
├── parameters
├── backend
└── propagation_output
```

`backend` có thể chứa information cần thiết để biết experiment sử dụng
simulator/backend nào.

Qiskit là quantum framework/backend chính của ProPulse trong giai đoạn
hiện tại.

Quantum-specific representation không được lan sang biological entities
nếu không cần thiết.

------------------------------------------------------------------------

## 11. Propagation Output Contract

Classical và quantum propagation cần hướng tới một common conceptual
contract:

``` text
Propagation Result
├── graph reference
├── model identity
├── parameters
├── initial condition
├── propagation domain
└── output representation
```

Điều này cho phép:

``` text
Classical Result
       ↓
       ├── Connectivity
       ↓
       └── Ranking

Quantum Result
       ↓
       ├── Connectivity
       ↓
       └── Ranking
```

mà không cần tạo hai connectivity pipeline hoàn toàn khác nhau chỉ vì
propagation backend khác nhau.

------------------------------------------------------------------------

## 12. Connectivity Matrix

Connectivity là representation nằm giữa propagation và prediction.

Mục tiêu output của challenge:

``` text
N × N connectivity matrix
```

Conceptual schema:

``` text
ConnectivityMatrix
├── graph_id
├── propagation_result_id
├── metric
├── matrix
├── node_index_mapping
└── metadata
```

### 12.1. Matrix

``` text
C ∈ R^(N×N)
```

hoặc một representation phù hợp khác tùy connectivity metric.

Schema không giả định trước:

-   matrix phải đối xứng;
-   matrix phải normalized;
-   matrix phải dense;
-   matrix phải sparse.

Những thuộc tính này phụ thuộc vào scientific definition của
connectivity metric.

### 12.2. Node index mapping

Matrix index phải ánh xạ rõ ràng:

``` text
matrix index
    ↓
node_id
    ↓
residue_id
    ↓
structure / chain / residue number
```

Đây là requirement bắt buộc cho interpretability.

------------------------------------------------------------------------

## 13. Connectivity Metric

Connectivity metric là một research variable.

Conceptual metadata:

``` text
metric_name
metric_definition
parameters
normalization
time_window
aggregation
```

Không chốt metric cụ thể trong schema v0.1.

Ví dụ các metric có thể được nghiên cứu:

-   transfer probability;
-   time-integrated transfer;
-   maximum transfer;
-   amplitude-derived quantity.

Đây chỉ là các hướng nghiên cứu minh họa, **không phải schema
requirement hay scientific conclusion**.

------------------------------------------------------------------------

## 14. Residue Score

Residue score là scalar representation được tạo từ connectivity để phục
vụ ranking.

Conceptual:

``` text
ResidueScore
├── residue_id
├── score
├── scoring_method
└── metadata
```

Một residue score phải giữ được:

``` text
score
    ↓
residue identity
```

Không lưu score chỉ theo matrix index mà không có mapping.

------------------------------------------------------------------------

## 15. Ranking

Ranking là ordered representation của residue candidates.

Conceptual:

``` text
Ranking
├── experiment_id
├── method
├── candidates
└── metadata
```

Mỗi candidate:

``` text
RankedResidue
├── rank
├── residue_id
├── score
└── reference_status
```

Output quan trọng:

``` text
Top-5
```

Ranking không được coi là ground truth.

------------------------------------------------------------------------

## 16. Allosteric Reference / Ground Truth

Evaluation cần một representation riêng cho known reference.

Conceptual:

``` text
AllostericReference
├── reference_id
├── source
├── structure_id
├── residues / region
├── annotation_type
└── provenance
```

Reference có thể biểu diễn:

-   known allosteric residue;
-   allosteric region;
-   allosteric pocket;
-   annotation khác phù hợp với benchmark.

Schema không giả định rằng mọi allosteric site đều có cùng hình thức
annotation.

------------------------------------------------------------------------

## 17. Background / Negative Controls

Challenge yêu cầu prediction được đánh giá không chỉ với known
allosteric information mà còn với random/background và non-functional
surface controls khi phù hợp.

Conceptual:

``` text
BackgroundSet
├── background_id
├── structure_id
├── residues / regions
├── control_type
└── generation_metadata
```

Ví dụ:

``` text
control_type
├── random_residues
└── non_functional_surface
```

Cách sampling cụ thể phải được định nghĩa ở experiment/evaluation
design.

------------------------------------------------------------------------

## 18. Experiment Run

`ExperimentRun` là entity nối các input, configuration và outputs của
một research experiment.

Conceptual:

``` text
ExperimentRun
├── experiment_id
├── input_structure
├── graph
├── propagation
├── connectivity
├── ranking
├── evaluation
├── parameters
└── provenance
```

Một run cần trả lời được:

``` text
What structure?
What graph?
What model?
What parameters?
What connectivity metric?
What ranking method?
What result?
What evaluation?
```

------------------------------------------------------------------------

## 19. Provenance

Provenance là một phần bắt buộc của research data model.

Tối thiểu cần có khả năng truy nguyên:

``` text
Result
  ↓
Experiment
  ↓
Connectivity
  ↓
Propagation
  ↓
Graph
  ↓
Structure
```

Các provenance fields có thể gồm:

``` text
source
version
timestamp
software version
Python version
dependency/backend version
parameters
input identifiers
```

Không phải mọi field đều bắt buộc ở mọi object, nhưng research result
quan trọng phải có đủ information để tái lập.

------------------------------------------------------------------------

## 20. Challenge Benchmark Data Model

Benchmark core có thể được biểu diễn:

``` text
BenchmarkTarget
├── target_id
├── apo_structure
├── holo_reference
├── known_allosteric_reference
└── metadata
```

Core challenge targets:

  Target           Apo input   Holo/reference
  ---------------- ----------- ----------------
  KRAS G12C        4OBE        6OIM
  BCR-ABL1         1OPL        5MO4
  Cardiac Myosin   5TBY        6C1H

`c-Myc / 1NKP` được quản lý như extension riêng theo scope của
challenge.

Schema không cho phép suy ra rằng holo structure là input prediction.

------------------------------------------------------------------------

## 21. Apo / Holo Separation

Một nguyên tắc quan trọng của data model:

``` text
Apo
 ↓
Prediction input

Holo / known reference
 ↓
Validation / evaluation
```

Không được vô tình đưa information của unknown holo allosteric pocket
vào structure/graph input dùng cho blind prediction.

Nếu một experiment sử dụng cả apo và holo, metadata phải thể hiện rõ
role của từng structure.

------------------------------------------------------------------------

## 22. Mapping Between Structures

Apo và holo có thể có khác biệt về:

-   residue numbering;
-   missing residues;
-   chain identity;
-   insertion code;
-   structure coverage.

Vì vậy schema cần một conceptual mapping layer khi evaluation yêu cầu so
sánh giữa structures.

``` text
Apo Residue
      ↓
Residue Mapping
      ↓
Reference / Holo Residue
```

Conceptual fields:

``` text
mapping_source
source_residue_id
target_residue_id
mapping_status
mapping_confidence
```

Chi tiết mapping algorithm không được chốt trong schema v0.1.

------------------------------------------------------------------------

## 23. Matrix / Residue Mapping Contract

Đây là một contract đặc biệt quan trọng:

``` text
Matrix index
      ↓
Graph node
      ↓
Residue
      ↓
Chain + residue number
      ↓
Protein structure
```

Mọi output dựa trên matrix phải giữ mapping này.

Ví dụ:

``` text
C[57, 103]
```

chỉ có ý nghĩa khi hệ thống biết:

``` text
57  → Residue A:X
103 → Residue A:Y
```

Không được để matrix indexing trở thành biological identity.

------------------------------------------------------------------------

## 24. File / Artifact Representation

Schema không bắt buộc một file format duy nhất.

Một research run có thể tạo các artifact như:

``` text
structure
graph
propagation
connectivity
ranking
evaluation
metadata
```

Các artifact có thể được lưu dưới format phù hợp với use case.

Nguyên tắc:

-   machine-readable;
-   deterministic khi có thể;
-   có metadata;
-   giữ identity mapping;
-   có provenance.

File naming convention cụ thể thuộc `RULES.md` hoặc experiment design
khi được chốt.

------------------------------------------------------------------------

## 25. Schema Boundaries

`SCHEMA.md` định nghĩa:

-   data entities;
-   relationships;
-   identity;
-   mappings;
-   conceptual fields;
-   output contracts;
-   provenance requirements.

`SCHEMA.md` không định nghĩa:

-   implementation class hierarchy;
-   function signatures;
-   exact database engine;
-   exact contact cutoff;
-   exact connectivity metric;
-   exact quantum circuit;
-   exact ranking formula.

Những quyết định đó thuộc architecture, design, rules hoặc research
implementation tương ứng.

------------------------------------------------------------------------

## 26. Schema Invariants

Các invariant quan trọng:

### I1 --- Residue identity

Mọi residue node phải map được về một biological residue.

### I2 --- Graph consistency

Graph phải có reference rõ ràng tới structure mà nó được tạo từ.

### I3 --- Propagation consistency

Propagation result phải reference đúng graph đã được sử dụng.

### I4 --- Connectivity consistency

Connectivity matrix phải reference propagation result và node-index
mapping tương ứng.

### I5 --- Ranking consistency

Mọi ranked residue phải reference residue tồn tại trong graph scope.

### I6 --- Evaluation traceability

Mọi evaluation result phải reference prediction và reference/background
data đã dùng.

### I7 --- Provenance

Research result quan trọng phải có provenance đủ để tái lập.

### I8 --- No biological identity by index

Matrix index hoặc graph index không được tự động được coi là biological
residue identifier.

------------------------------------------------------------------------

## 27. Schema Evolution

Schema sẽ phát triển theo research.

Các thay đổi schema cần tránh phá vỡ traceability.

Khi thêm một field hoặc entity mới, cần xác định:

1.  Field/entity phục vụ scientific requirement nào?
2.  Nó thuộc layer nào?
3.  Nó có thay đổi biological identity không?
4.  Nó có ảnh hưởng đến comparison giữa classical và quantum không?
5.  Có cần migration/conversion cho existing artifacts không?

Không thêm field chỉ vì "có thể cần sau này".

------------------------------------------------------------------------

## 28. Relationship with Project Architecture

Schema phản ánh architecture:

``` text
structure/
    ↓
Residue

graph/
    ↓
Protein Contact Graph

propagation/
    ↓
Propagation Result

connectivity/
    ↓
Connectivity Matrix

ranking/
    ↓
Residue Score / Ranking
```

Các supporting surfaces:

``` text
data/
    → structure and derived data artifacts

experiments/
    → ExperimentRun context

results/
    → output artifacts

tests/
    → schema invariants and data contracts
```

------------------------------------------------------------------------

## 29. Schema Summary

``` text
Protein Structure
    │
    └── Chain
          │
          └── Residue
                │
                ▼
          Contact Graph
          │         │
          │         │
          ▼         ▼
      Classical   Quantum
      Propagation Propagation
          │         │
          └────┬────┘
               ▼
       Connectivity Matrix
               │
               ▼
         Residue Scores
               │
               ▼
            Ranking
               │
               ▼
        Evaluation / Reference
```

Core identity chain:

``` text
Structure
  ↓
Chain
  ↓
Residue
  ↓
Node
  ↓
Matrix Index
```

Core research chain:

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
  ↓
Evaluation
```

------------------------------------------------------------------------

## 30. Source Basis

Schema này được xây dựng dựa trên:

1.  `PRD.md` của ProPulse.
2.  `DESIGN.md` của ProPulse.
3.  `ARCHITECTURE.md` của ProPulse.
4.  Documentation & Diagram Baseline Guide.
5.  Challenge Statement của Cleveland Clinic.
6.  Scientific direction đã thống nhất cho ProPulse.

Các chi tiết chưa được chốt về mặt research --- đặc biệt residue
coordinate representation, contact definition, weighting scheme,
connectivity metric và mapping algorithm --- được cố ý giữ ở mức
conceptual để không biến một giả định tạm thời thành contract cố định
của hệ thống.
