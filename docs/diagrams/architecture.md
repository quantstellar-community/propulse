# Architecture Diagram

## Mục đích

Architecture Diagram mô tả cấu trúc nội bộ ở mức hệ thống của ProPulse,
các thành phần chính, trách nhiệm của chúng và hướng phụ thuộc giữa các
layer.

Diagram này nằm dưới System Context Diagram và trên mức implementation
detail. Nó tập trung vào **cách ProPulse được tổ chức để thực hiện
research pipeline**, không mô tả chi tiết từng class, function hoặc thư
viện cụ thể.

## Kiến trúc tổng thể

``` mermaid
flowchart TB
    subgraph CORE["ProPulse Core Research Pipeline"]
        S["Structure Layer<br/>Protein Structure Processing"]
        G["Graph Layer<br/>Protein Contact Network"]

        subgraph P["Propagation Layer"]
            C["Classical Propagation"]
            Q["Quantum Propagation"]
        end

        K["Connectivity Layer<br/>Connectivity Representation"]
        R["Ranking Layer<br/>Residue & Site Ranking"]
        E["Evaluation Layer<br/>Benchmark & Analysis"]
    end

    S --> G
    G --> C
    G --> Q
    C --> K
    Q --> K
    K --> R
    R --> E

    CFG["Configuration &<br/>Experiment Metadata"]
    REP["Reporting &<br/>Visualization"]

    CFG --> S
    CFG --> G
    CFG --> C
    CFG --> Q
    CFG --> K
    CFG --> R
    CFG --> E

    K --> REP
    R --> REP
    E --> REP
```

## Các thành phần chính

### 1. Structure Layer

Chịu trách nhiệm chuyển cấu trúc protein đầu vào thành một biểu diễn
residue-level có thể được sử dụng bởi các bước tiếp theo.

Trách nhiệm chính:

-   đọc và kiểm tra cấu trúc;
-   lựa chọn chain/domain;
-   chuẩn hóa residue identity;
-   xử lý các trường hợp cấu trúc không đầy đủ;
-   duy trì mapping giữa residue trong graph và residue trong cấu trúc
    gốc.

Layer này không quyết định propagation model.

### 2. Graph Layer

Xây dựng Protein Contact Network từ biểu diễn cấu trúc.

Trách nhiệm chính:

-   xác định các residue nodes;
-   xây dựng structural contacts;
-   tính hoặc gán edge weights;
-   kiểm tra tính hợp lệ của graph;
-   xuất graph cùng metadata và các giả định construction.

Đây là boundary quan trọng của scientific comparison: classical và
quantum propagation phải nhận cùng graph khi được so sánh.

### 3. Propagation Layer

Là trung tâm của nghiên cứu signal propagation.

Layer này gồm hai nhánh:

**Classical Propagation**

Cung cấp các baseline như random walk, graph diffusion và các
propagation model cổ điển phù hợp.

**Quantum Propagation**

Cung cấp continuous-time quantum walk (CTQW) và các biến thể quantum
propagation được nghiên cứu trong tương lai.

Hai nhánh không được coi là hai pipeline độc lập về dữ liệu. Chúng cùng
nhận graph từ Graph Layer và đưa kết quả về Connectivity Layer.

### 4. Connectivity Layer

Chuyển kết quả propagation thành biểu diễn connectivity có thể phân tích
và so sánh.

Các đầu ra có thể bao gồm:

-   residue-to-residue connectivity;
-   connectivity matrix hoặc sparse representation;
-   seed-conditioned scores;
-   các propagation-derived features.

Connectivity Layer không tự quyết định biological interpretation.

### 5. Ranking Layer

Chuyển evidence ở mức residue thành candidate sites ở mức cấu trúc.

Trách nhiệm chính:

-   tính residue-level scores;
-   spatial/structural clustering;
-   áp dụng separation constraints;
-   tạo candidate sites;
-   xếp hạng candidate sites;
-   hỗ trợ top-five ranking khi benchmark yêu cầu.

### 6. Evaluation Layer

Đánh giá kết quả theo benchmark protocol.

Trách nhiệm chính:

-   residue/reference mapping;
-   benchmark labels;
-   metrics;
-   controls;
-   ablations;
-   sensitivity analysis;
-   statistical analysis;
-   comparison giữa classical và quantum methods.

Evaluation không được trở thành một phần của prediction input.

## Supporting Layers

### Configuration & Experiment Metadata

Cung cấp cấu hình và metadata cho toàn bộ pipeline.

Các thông tin cần được kiểm soát gồm:

-   structure input;
-   graph construction;
-   propagation parameters;
-   connectivity definition;
-   ranking configuration;
-   evaluation configuration;
-   software/environment information.

Configuration là cơ chế giúp các experiment có thể tái lập và audit
được.

### Reporting & Visualization

Nhận các artifact từ Connectivity, Ranking và Evaluation để tạo output
cho researcher.

Có thể bao gồm:

-   connectivity matrices;
-   residue scores;
-   ranked candidate sites;
-   benchmark summaries;
-   experiment reports;
-   structural overlays và các visualization artifacts.

Reporting không thay đổi scientific computation đã được thực hiện.

## Dependency Direction

Kiến trúc giữ dependency theo một hướng chính:

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

Supporting components cung cấp configuration hoặc consume outputs, nhưng
không được phá vỡ hướng phụ thuộc của core pipeline.

Đặc biệt:

-   Graph không phụ thuộc vào propagation model.
-   Classical và Quantum không phụ thuộc lẫn nhau.
-   Connectivity không phụ thuộc vào việc propagation là classical hay
    quantum.
-   Ranking không phụ thuộc vào implementation cụ thể của propagation.
-   Evaluation không được cung cấp thông tin benchmark như hidden
    predictive features.

## Architectural Principles

### Một graph, nhiều propagation models

Để comparison có ý nghĩa:

**Same Structure → Same Graph → Different Propagation → Comparable
Connectivity → Same Ranking/Evaluation**

Graph representation không được âm thầm thay đổi giữa classical và
quantum experiments.

### Scientific core trước, intelligence layer sau

ProPulse ưu tiên xác minh propagation hypothesis trước khi đưa ML, GNN
hoặc QML vào core pipeline.

Nếu prediction performance tăng sau khi thêm ML, cần có ablation để xác
định improvement đến từ:

-   graph representation;
-   propagation;
-   connectivity representation;
-   hoặc learning model.

### Modular nhưng không over-engineered

Các layer có boundary rõ ràng nhưng architecture không được tạo
abstraction chỉ vì khả năng mở rộng trong tương lai.

Mỗi component phải có trách nhiệm khoa học hoặc kỹ thuật cụ thể.

### Traceability

Một prediction cần có khả năng truy ngược:

**Candidate Site → Residue Scores → Connectivity → Propagation → Graph →
Source Structure**

Điều này giúp kết quả có thể được kiểm tra ở cả góc độ software và
scientific reasoning.

## Phạm vi của Diagram

Architecture Diagram này **không** mô tả:

-   chi tiết class/function;
-   API implementation;
-   dependency graph của từng Python package;
-   quantum circuit topology;
-   database schema;
-   runtime deployment;
-   execution sequence của một experiment.

Các nội dung trên thuộc các tài liệu hoặc diagram chuyên biệt.

## Quan hệ với các Diagram khác

-   `system-context.md` --- ProPulse và các hệ thống bên ngoài.
-   `architecture.md` --- cấu trúc nội bộ của ProPulse.
-   `data-flow.md` --- dữ liệu được biến đổi và di chuyển qua pipeline.
-   `sequence.md` --- thứ tự thực thi của một experiment.
-   `er.md` --- entities và quan hệ dữ liệu.
-   `deployment.md` --- môi trường runtime và computational resources.
