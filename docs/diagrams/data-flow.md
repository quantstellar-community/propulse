# Data Flow Diagram

## Mục đích

Data Flow Diagram mô tả cách dữ liệu được **tiếp nhận, biến đổi, truyền
qua và tạo ra** trong pipeline nghiên cứu của ProPulse.

Trọng tâm của diagram là data lineage từ cấu trúc protein đầu vào đến
kết quả dự đoán và đánh giá. Chi tiết về component architecture thuộc
`architecture.md`; chi tiết về entity và quan hệ dữ liệu thuộc `er.md`.

## Luồng dữ liệu chính

``` mermaid
flowchart LR
    A[(Unbound Protein Structure)] 
        --> B[Structure Processing]

    B -->|Residue-level representation| C[Protein Contact Graph]

    C -->|Same graph| D[Classical Propagation]
    C -->|Same graph| E[Quantum Propagation]

    D -->|Classical propagation results| F[Connectivity]
    E -->|Quantum propagation results| F

    F -->|Connectivity representation| G[Residue Scoring]

    G -->|Scored residues| H[Spatial / Structural Clustering]

    H -->|Candidate regions| I[Site Ranking]

    I --> J[(Prediction Results)]

    K[(Benchmark / Reference Data)]
        --> L[Evaluation]

    J --> L
    L --> M[(Evaluation Results)]

    N[Experiment Configuration]
        --> B
    N --> C
    N --> D
    N --> E
    N --> F
    N --> G
    N --> H
    N --> I
    N --> L
```

## Data Transformations

### 1. Protein Structure → Residue Representation

**Input:** cấu trúc protein chưa gắn ligand/allosteric reference dùng
cho đánh giá.

**Transformation:**

-   đọc cấu trúc;
-   lựa chọn chain/domain;
-   xác định residue;
-   chuẩn hóa biological identity;
-   xử lý missing hoặc không hợp lệ theo policy đã khai báo.

**Output:** residue-level structural representation có mapping về cấu
trúc nguồn.

### 2. Residue Representation → Protein Contact Graph

**Input:** residue representation.

**Transformation:**

-   xác định residue nodes;
-   xác định structural contacts;
-   tính edge weights;
-   kiểm tra graph;
-   lưu graph construction metadata.

**Output:**

\[ G=(V,E,W) \]

cùng mapping giữa graph nodes và residue identities.

Đây là điểm tạo ra **shared graph** cho cả classical và quantum
propagation.

### 3. Protein Contact Graph → Propagation

Graph được truyền vào hai nhánh propagation:

-   **Classical:** random walk, diffusion và các baseline graph-based
    phù hợp.
-   **Quantum:** continuous-time quantum walk và các quantum propagation
    model được nghiên cứu.

Hai nhánh sử dụng cùng graph input khi được dùng trong một controlled
comparison.

### 4. Propagation → Connectivity

Kết quả propagation được chuyển thành connectivity representation.

Có thể bao gồm:

-   residue-to-residue connectivity;
-   connectivity matrix;
-   sparse connectivity representation;
-   seed-conditioned residue evidence;
-   các propagation-derived features.

Connectivity definition phải được ghi nhận cùng experiment
configuration.

### 5. Connectivity → Ranking

Connectivity được chuyển thành residue-level evidence và sau đó thành
candidate sites.

Luồng biến đổi:

**Connectivity → Residue Scores → Spatial/Structural Clustering →
Candidate Sites → Ranking**

Kết quả cuối cùng là các candidate allosteric regions được xếp hạng.

### 6. Prediction Results → Evaluation

Prediction results được đối chiếu với benchmark/reference data độc lập.

Evaluation có thể bao gồm:

-   residue/site matching;
-   top-(k) performance;
-   enrichment;
-   ranking metrics;
-   stability và sensitivity;
-   classical--quantum comparison;
-   resource and scaling analysis.

Benchmark/reference data chỉ phục vụ evaluation và không được âm thầm
trở thành prediction input.

## Data Artifacts

  -----------------------------------------------------------------------
  Artifact                            Vai trò
  ----------------------------------- -----------------------------------
  **Protein Structure**               Cấu trúc protein đầu vào.

  **Residue Representation**          Biểu diễn residue đã chuẩn hóa và
                                      mapping về structure source.

  **Protein Contact Graph**           Graph dùng làm nền cho propagation.

  **Propagation Results**             Kết quả của classical hoặc quantum
                                      dynamics.

  **Connectivity Representation**     Biểu diễn connectivity được suy ra
                                      từ propagation.

  **Residue Scores**                  Evidence ở mức residue.

  **Candidate Sites**                 Các vùng cấu trúc được hình thành
                                      từ residue scores.

  **Prediction Results**              Danh sách candidate sites và
                                      ranking.

  **Benchmark / Reference Data**      Dữ liệu độc lập dùng để đánh giá
                                      prediction.

  **Evaluation Results**              Metrics, comparisons, sensitivity
                                      và analysis artifacts.

  **Experiment Configuration**        Parameters, assumptions và metadata
                                      cần để tái lập experiment.
  -----------------------------------------------------------------------

## Prediction Data vs Reference Data

ProPulse duy trì ranh giới rõ ràng giữa hai loại dữ liệu:

``` text
Prediction Path
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
Ranking
      ↓
Prediction Results

Evaluation Path
Benchmark / Reference Data
      ↓
Evaluation
      ↑
Prediction Results
```

Reference data không được đi vào prediction path nếu experiment không
định nghĩa rõ điều đó.

Đối với challenge-oriented evaluation, cấu trúc unbound là prediction
input; cấu trúc/reference bound được dùng để đánh giá và đối chiếu sau
prediction.

## Configuration and Provenance

Mỗi experiment cần giữ đủ thông tin để truy nguyên kết quả:

-   source structure và phiên bản;
-   chain/domain selection;
-   residue representation;
-   graph construction policy;
-   edge-weight function;
-   propagation model;
-   model parameters;
-   connectivity definition;
-   ranking configuration;
-   benchmark/evaluation configuration;
-   software và environment;
-   runtime/resource information khi phù hợp.

Mục tiêu là hỗ trợ lineage:

**Result → Evaluation → Ranking → Connectivity → Propagation → Graph →
Residue Representation → Source Structure**

## Data Flow Principles

### Shared graph

Classical và quantum methods phải nhận cùng graph khi comparison được
thiết kế để đo ảnh hưởng của propagation model.

### No hidden reference leakage

Thông tin dùng để đánh giá không được trở thành predictive feature ngoài
protocol đã khai báo.

### Biological identity preservation

Graph index chỉ là computational identifier. Residue identity phải luôn
có mapping ngược về structure source.

### Reproducible transformation

Mỗi transformation quan trọng phải có configuration và metadata đủ để
tái tạo.

### Separation of computation and interpretation

Propagation tạo ra computational evidence. Ranking tạo candidate sites.
Evaluation xác định mức độ phù hợp với reference. Không bước nào được tự
động biến computational score thành biological confirmation.

## Phạm vi của Diagram

Data Flow Diagram này không mô tả:

-   class/function implementation;
-   chi tiết quantum circuit;
-   database schema;
-   runtime deployment topology;
-   execution timing giữa các service.

Các nội dung đó thuộc architecture, ER và sequence/deployment diagrams.

## Quan hệ với các Diagram khác

-   `system-context.md` --- ranh giới hệ thống và external entities.
-   `architecture.md` --- cấu trúc nội bộ và component boundaries.
-   `data-flow.md` --- luồng và biến đổi dữ liệu.
-   `sequence.md` --- thứ tự thực thi theo thời gian.
-   `er.md` --- entity và quan hệ dữ liệu.
-   `deployment.md` --- môi trường runtime và computational resources.
