# ER Diagram

## Mục đích

ER Diagram mô tả **mô hình dữ liệu khái niệm** của ProPulse và các quan
hệ chính giữa những data entity được tạo ra hoặc sử dụng trong research
pipeline.

Diagram này không mô tả database schema, SQL tables hay implementation
classes. Mục tiêu là xác định các entity cần được giữ nhất quán xuyên
suốt từ structure input đến experiment và evaluation.

## Conceptual Data Model

``` mermaid
erDiagram
    PROTEIN_STRUCTURE ||--o{ RESIDUE : contains
    PROTEIN_STRUCTURE ||--o{ CONTACT_GRAPH : generates
    CONTACT_GRAPH ||--|{ GRAPH_NODE : contains
    RESIDUE ||--o| GRAPH_NODE : maps_to

    CONTACT_GRAPH ||--o{ PROPAGATION_RUN : used_by
    PROPAGATION_RUN ||--o{ CONNECTIVITY : produces
    CONNECTIVITY ||--o{ RESIDUE_SCORE : produces
    RESIDUE ||--o{ RESIDUE_SCORE : receives

    RESIDUE_SCORE ||--o{ CANDIDATE_SITE : contributes_to
    CANDIDATE_SITE ||--o{ PREDICTION : ranked_in

    BENCHMARK_REFERENCE ||--o{ EVALUATION : provides_reference_for
    PREDICTION ||--o{ EVALUATION : evaluated_by

    EXPERIMENT ||--o{ PROPAGATION_RUN : records
    EXPERIMENT ||--o{ PREDICTION : produces
    EXPERIMENT ||--o{ EVALUATION : records
```

## Entity chính

### Protein Structure

Đại diện cho cấu trúc protein được sử dụng trong experiment.

Thông tin khái niệm có thể bao gồm:

-   structure identifier;
-   source;
-   chain/domain selection;
-   structure type;
-   provenance metadata.

Trong challenge-oriented workflow, unbound structure là prediction
input; reference/bound structure không được tự động xem như prediction
input.

### Residue

Đại diện cho một residue có biological identity trong structure.

Residue là đơn vị quan trọng để duy trì mapping giữa biological
structure và computational graph.

Graph index không thay thế residue identity.

### Contact Graph

Đại diện cho Protein Contact Network được xây dựng từ structure.

Một graph có:

-   node set;
-   edge set;
-   edge weights;
-   graph-construction policy;
-   construction metadata.

Một graph cụ thể phải có provenance về structure mà nó được tạo ra từ.

### Graph Node

Đại diện cho node computational trong Contact Graph.

Mỗi graph node có thể map về một `Residue`, nhưng:

**Graph Node ID ≠ Biological Residue Identity**

Điều này giúp tránh mất thông tin biological identity khi sử dụng
index-based computation.

### Propagation Run

Đại diện cho một lần chạy propagation trên một Contact Graph.

Một run phải xác định propagation family/model, ví dụ:

-   Classical;
-   Quantum.

Các model parameters và execution metadata thuộc về run hoặc experiment
configuration tương ứng.

Classical và quantum runs trong controlled comparison phải tham chiếu
cùng graph.

### Connectivity

Đại diện cho kết quả connectivity được suy ra từ một propagation run.

Có thể biểu diễn dưới dạng:

-   dense connectivity matrix;
-   sparse matrix;
-   seed-conditioned connectivity;
-   hoặc một representation được định nghĩa rõ trong experiment.

Connectivity là artifact trung gian giữa propagation và ranking.

### Residue Score

Đại diện cho evidence hoặc score ở mức residue được suy ra từ
connectivity.

Một score phải có khả năng truy nguyên về:

**Residue → Connectivity → Propagation Run → Contact Graph → Protein
Structure**

### Candidate Site

Đại diện cho một vùng cấu trúc được hình thành từ các residue có
evidence liên quan.

Candidate site có thể được xác định thông qua:

-   spatial clustering;
-   structural proximity;
-   separation constraints;
-   ranking policy.

Candidate site là computational candidate, không phải biological
confirmation.

### Prediction

Đại diện cho kết quả prediction của một experiment.

Prediction có thể chứa:

-   candidate sites;
-   residue ranking;
-   site ranking;
-   top-(k) results.

Trong challenge-oriented submission, top-five candidate sites có thể là
output yêu cầu của benchmark.

### Benchmark Reference

Đại diện cho thông tin độc lập dùng để đánh giá prediction.

Có thể bao gồm:

-   known allosteric regions;
-   functional regions;
-   ligand/reference information;
-   structural reference mapping;
-   benchmark labels.

Entity này phải được phân biệt với prediction input để tránh hidden
leakage.

### Evaluation

Đại diện cho kết quả đánh giá một prediction đối với benchmark
reference.

Có thể bao gồm:

-   performance metrics;
-   enrichment;
-   ranking comparison;
-   statistical analysis;
-   sensitivity;
-   robustness;
-   classical--quantum comparison;
-   resource/scaling measurements.

### Experiment

Đại diện cho một research run ở cấp provenance.

Experiment liên kết các thành phần cần thiết để tái lập và audit một kết
quả:

-   input structure;
-   graph configuration;
-   propagation configuration;
-   connectivity definition;
-   ranking configuration;
-   evaluation configuration;
-   software/environment metadata;
-   generated artifacts.

## Quan hệ cốt lõi

### Structure → Residue

Một protein structure chứa nhiều residue.

Mỗi residue giữ biological identity và mapping về source structure.

### Structure → Contact Graph

Một structure có thể được dùng để tạo nhiều graph variants trong các
experiment khác nhau, ví dụ khi nghiên cứu sensitivity đối với
graph-construction parameters.

### Residue → Graph Node

Graph node đại diện cho residue trong computational graph.

Quan hệ mapping này phải bảo toàn biological identity.

### Graph → Propagation Run

Một Contact Graph có thể được sử dụng bởi nhiều propagation runs.

Điều này cho phép cùng một graph được đánh giá bằng classical và quantum
models.

### Propagation Run → Connectivity

Một propagation run tạo ra một hoặc nhiều connectivity artifacts tùy
theo experiment design.

### Connectivity → Residue Score → Candidate Site

Connectivity cung cấp evidence để tính residue scores, sau đó các
residue scores được kết hợp thành candidate sites.

### Prediction ↔ Benchmark Reference → Evaluation

Prediction được đánh giá bằng reference data độc lập.

Reference data không trở thành một phần của prediction path nếu protocol
không khai báo rõ.

### Experiment → Research Artifacts

Experiment là lớp provenance liên kết configuration, execution và kết
quả để hỗ trợ reproducibility.

## Data Invariants

### Biological identity

Mỗi residue trong computational pipeline phải có mapping rõ ràng về
biological residue identity.

### Shared graph

Classical và quantum propagation trong controlled comparison phải tham
chiếu cùng Contact Graph.

### Reference isolation

Benchmark/reference information phải được tách khỏi prediction input.

### Traceability

Prediction phải có lineage ngược về source structure.

### Experiment reproducibility

Các parameters có ảnh hưởng đến scientific result phải được ghi nhận
cùng experiment.

### No implicit biological interpretation

Candidate Site và Prediction là computational outputs. Chúng không mặc
định là experimentally validated allosteric sites.

## Phạm vi của ER Diagram

ER Diagram này không mô tả:

-   database engine;
-   SQL schema;
-   physical storage format;
-   Python classes;
-   API contracts;
-   runtime deployment.

Những chi tiết đó sẽ được xác định ở implementation hoặc deployment
documentation khi cần.

## Quan hệ với các Diagram khác

-   `system-context.md` --- hệ thống và external entities.
-   `architecture.md` --- component boundaries.
-   `data-flow.md` --- data transformation và lineage.
-   `sequence.md` --- execution order.
-   `er.md` --- conceptual data entities và relationships.
-   `deployment.md` --- runtime environment và computational resources.
