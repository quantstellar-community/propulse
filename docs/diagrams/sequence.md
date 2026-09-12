# Sequence Diagram

## Mục đích

Sequence Diagram mô tả thứ tự thực thi của một experiment ProPulse từ
lúc researcher cung cấp input cho đến khi hệ thống trả về prediction và
evaluation results.

Diagram này tập trung vào **trình tự tương tác giữa các thành phần trong
một lần chạy experiment**. Cấu trúc module thuộc `architecture.md`, còn
sự biến đổi dữ liệu thuộc `data-flow.md`.

## End-to-End Experiment Sequence

``` mermaid
sequenceDiagram
    actor R as Researcher
    participant PP as ProPulse
    participant S as Structure Processing
    participant G as Graph Construction
    participant C as Classical Propagation
    participant Q as Quantum Propagation
    participant K as Connectivity
    participant RK as Ranking
    participant E as Evaluation
    participant REF as Benchmark / Reference Data

    R->>PP: Cấu hình experiment + unbound structure
    PP->>S: Parse và validate structure
    S-->>PP: Residue-level representation

    PP->>G: Xây dựng Protein Contact Graph
    G-->>PP: Graph + residue mapping + metadata

    par Classical path
        PP->>C: Propagation trên graph
        C-->>PP: Classical propagation results
    and Quantum path
        PP->>Q: Quantum propagation trên cùng graph
        Q-->>PP: Quantum propagation results
    end

    PP->>K: Tạo connectivity representation
    K-->>PP: Connectivity matrix / sparse representation

    PP->>RK: Tính residue scores và candidate sites
    RK-->>PP: Ranked candidate sites

    PP->>REF: Lấy benchmark / reference information
    REF-->>PP: Evaluation reference

    PP->>E: So sánh prediction với reference
    E-->>PP: Metrics + analysis + comparison

    PP-->>R: Prediction + evaluation report + artifacts
```

## Trình tự chính

### 1. Experiment Configuration

Researcher cung cấp:

-   unbound protein structure;
-   chain/domain selection nếu cần;
-   graph configuration;
-   propagation parameters;
-   connectivity và ranking configuration;
-   evaluation configuration.

Configuration phải đủ để experiment có thể được tái lập.

### 2. Structure Processing

ProPulse đọc và kiểm tra structure, sau đó tạo residue-level
representation.

Kết quả phải giữ được mapping:

**Graph/Residue Index ↔ Biological Residue Identity ↔ Source Structure**

### 3. Graph Construction

Residue representation được chuyển thành Protein Contact Graph.

Graph cùng metadata trở thành input chung cho các propagation models
trong controlled comparison.

### 4. Classical and Quantum Propagation

Hai nhánh được thực thi độc lập nhưng sử dụng cùng graph:

``` text
                 Protein Contact Graph
                         │
              ┌──────────┴──────────┐
              ↓                     ↓
      Classical Propagation   Quantum Propagation
              │                     │
              └──────────┬──────────┘
                         ↓
                    Connectivity
```

Mục tiêu của sequence này không phải giả định hai nhánh luôn phải chạy
cùng lúc trong production. Việc chạy tuần tự, song song hoặc chỉ chạy
một model phụ thuộc experiment configuration.

### 5. Connectivity

Propagation results được chuyển thành connectivity representation theo
metric đã khai báo.

Output có thể là:

-   connectivity matrix;
-   sparse connectivity representation;
-   seed-conditioned scores;
-   các propagation-derived features.

### 6. Ranking

Connectivity được chuyển thành:

**Residue Scores → Candidate Regions → Ranked Candidate Sites**

Kết quả này tạo ra prediction trước khi benchmark evaluation được thực
hiện.

### 7. Evaluation

Prediction được đối chiếu với benchmark/reference data độc lập.

Evaluation có thể tạo ra:

-   top-(k) metrics;
-   enrichment;
-   ranking comparison;
-   sensitivity analysis;
-   classical--quantum comparison;
-   resource/scaling analysis.

Reference information không được đưa ngược vào prediction path ngoài
những gì experiment protocol công khai cho phép.

### 8. Result Reporting

ProPulse trả về cho researcher:

-   ranked candidate sites;
-   connectivity artifacts;
-   evaluation metrics;
-   comparison results;
-   experiment metadata;
-   reproducibility information;
-   optional visualization artifacts.

## Scientific Invariants

Sequence phải duy trì các invariant quan trọng:

### Shared graph

Classical và quantum propagation nhận cùng graph trong một controlled
comparison.

### Reference isolation

Benchmark/reference data được sử dụng cho evaluation, không phải hidden
predictive input.

### Traceability

Kết quả phải có thể truy ngược:

**Prediction → Ranking → Connectivity → Propagation → Graph →
Structure**

### Reproducibility

Experiment configuration và relevant metadata phải được giữ cùng result
artifacts.

### No automatic quantum advantage

Sequence không giả định quantum branch tốt hơn classical branch.
Comparison và evaluation mới quyết định kết luận thực nghiệm.

## Failure and Validation Points

Các bước có thể dừng experiment khi phát hiện:

-   structure không hợp lệ;
-   residue mapping không xác định được;
-   graph construction thất bại;
-   propagation không hội tụ hoặc không thể tính toán;
-   connectivity output không hợp lệ;
-   ranking không tạo được candidate sites;
-   benchmark mapping không đủ để đánh giá.

Failure phải được ghi nhận thay vì âm thầm bỏ qua hoặc sửa dữ liệu mà
không có provenance.

## Phạm vi của Diagram

Sequence Diagram này không mô tả:

-   implementation-level function calls;
-   chi tiết quantum circuit;
-   database internals;
-   deployment topology;
-   class hierarchy.

Các nội dung đó thuộc các tài liệu chuyên biệt.

## Quan hệ với các Diagram khác

-   `system-context.md` --- context và external entities.
-   `architecture.md` --- component structure.
-   `data-flow.md` --- data transformation và lineage.
-   `sequence.md` --- execution order của experiment.
-   `er.md` --- data entities và relationships.
-   `deployment.md` --- runtime environment và computational resources.
