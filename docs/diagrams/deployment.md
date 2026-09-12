# Deployment Diagram

## 1. Mục đích

Deployment Diagram mô tả ProPulse được triển khai và thực thi trên những
môi trường nào, các runtime/resource chính nằm ở đâu, và dữ liệu đi qua
các môi trường đó như thế nào.

Diagram này tập trung vào **runtime environment và deployment
boundary**, không mô tả chi tiết kiến trúc module nội bộ, luồng dữ liệu
nghiên cứu hay thứ tự thực thi.

------------------------------------------------------------------------

## 2. Deployment Overview

``` mermaid
flowchart LR
    R["Researcher / Developer"]

    subgraph LOCAL["Local Development Environment"]
        APP["ProPulse Runtime<br/>Python 3.12 + uv"]
        CLASSICAL["Classical Compute<br/>NumPy / SciPy / NetworkX"]
        QUANTUM["Quantum Simulation<br/>Qiskit / Aer"]
        STORAGE["Data & Artifact Storage<br/>Raw / Processed / Results"]
    end

    subgraph OPTIONAL["Optional Quantum Execution"]
        CLOUD["Quantum Cloud / Remote Simulator"]
        HARDWARE["Quantum Hardware"]
    end

    VIZ["Structural Visualization / Analysis<br/>e.g. PyMOL"]

    R --> APP

    APP --> CLASSICAL
    APP --> QUANTUM
    APP <--> STORAGE

    QUANTUM -. optional .-> CLOUD
    QUANTUM -. optional .-> HARDWARE

    STORAGE --> VIZ
    R --> VIZ
```

------------------------------------------------------------------------

## 3. Deployment Nodes

### Researcher / Developer

Điểm tương tác của người nghiên cứu hoặc developer với ProPulse.

Trách nhiệm chính:

-   cấu hình experiment;
-   chạy pipeline;
-   kiểm tra kết quả;
-   phân tích connectivity và ranking;
-   xem artifact và visualization.

Đây là actor bên ngoài runtime, không phải một module của ProPulse.

### ProPulse Runtime

Môi trường thực thi chính của hệ thống.

Baseline:

-   Python 3.12;
-   uv quản lý environment và dependencies;
-   ProPulse chạy như một Python package/application trong local
    development environment.

Runtime này điều phối toàn bộ research pipeline:

**Structure → Graph → Propagation → Connectivity → Ranking →
Evaluation**

### Classical Compute

Môi trường thực thi các classical propagation và numerical operations.

Các thư viện chính:

-   NumPy;
-   SciPy;
-   NetworkX.

Classical computation là một thành phần nghiên cứu cốt lõi và là
baseline để so sánh với quantum propagation.

### Quantum Simulation

Môi trường thực thi quantum propagation experiments.

Baseline:

-   Qiskit;
-   Qiskit Aer.

Ở giai đoạn đầu, quantum experiments có thể chạy hoàn toàn bằng local
simulation.

Quantum hardware hoặc remote quantum services không phải prerequisite
của ProPulse.

### Data & Artifact Storage

Nơi lưu trữ các artifact phục vụ nghiên cứu và reproducibility:

-   raw protein structures;
-   processed residue data;
-   protein contact graphs;
-   experiment configuration;
-   connectivity matrices;
-   residue/site rankings;
-   evaluation results;
-   reports và metadata.

Storage này không phải database service bắt buộc; trong giai đoạn
core-first, filesystem-based artifacts là đủ.

### Optional Quantum Execution

Lớp deployment mở rộng cho các môi trường quantum bên ngoài local
machine:

-   remote/cloud quantum simulator;
-   quantum hardware.

Các môi trường này chỉ là **execution targets**, không thay đổi
scientific pipeline của ProPulse.

### Structural Visualization / Analysis

Các công cụ bên ngoài dùng để quan sát hoặc phân tích cấu trúc và
prediction.

Ví dụ:

-   PyMOL.

Visualization không nằm trong scientific core và không ảnh hưởng đến
propagation hoặc ranking.

------------------------------------------------------------------------

## 4. Deployment Boundaries

ProPulse được tổ chức thành ba boundary chính:

**Core Local Runtime**

Nơi chạy structure processing, graph construction, classical
propagation, connectivity và ranking.

**Quantum Execution Boundary**

Nơi chạy quantum propagation. Ban đầu có thể nằm hoàn toàn trên local
machine thông qua Qiskit Aer; về sau có thể mở rộng sang remote
simulator hoặc hardware.

**Artifact Boundary**

Nơi lưu giữ dữ liệu đầu vào, intermediate artifacts và kết quả
experiment nhằm đảm bảo traceability và reproducibility.

------------------------------------------------------------------------

## 5. Deployment Principles

### 5.1 Local-first

ProPulse phải có khả năng phát triển và chạy research pipeline cơ bản
trên local environment.

Quantum hardware không được xem là điều kiện để phát triển core
research.

### 5.2 Classical và Quantum dùng chung scientific pipeline

Deployment khác nhau không được tạo ra hai pipeline nghiên cứu độc lập.

Cả classical và quantum propagation đều nhận cùng protein graph và tạo
output có thể so sánh trong cùng evaluation framework.

### 5.3 Quantum execution có tính thay thế

Local simulator, remote simulator và quantum hardware nên được xem là
các execution targets khác nhau của quantum layer.

Scientific interface không nên phụ thuộc cứng vào một hardware cụ thể.

### 5.4 Reproducibility

Mỗi experiment cần có đủ metadata để tái tạo kết quả, bao gồm tối thiểu:

-   input structure;
-   graph construction parameters;
-   propagation method;
-   experiment parameters;
-   software/dependency environment;
-   output artifacts.

### 5.5 Không over-engineering deployment

Ở giai đoạn hiện tại, không cần:

-   microservices;
-   container orchestration;
-   cloud-native infrastructure;
-   distributed database;
-   production API.

Deployment architecture chỉ mở rộng khi research workload thực sự yêu
cầu.

------------------------------------------------------------------------

## 6. Scope

Deployment Diagram này mô tả:

-   nơi ProPulse được thực thi;
-   classical và quantum execution environments;
-   data/artifact storage;
-   optional remote quantum resources;
-   external visualization tools.

Diagram này **không** mô tả:

-   chi tiết module nội bộ;
-   data transformation;
-   temporal execution sequence;
-   database schema;
-   quantum advantage;
-   scalability performance đã được chứng minh.

Các nội dung đó thuộc Architecture, Data Flow, Sequence, ER và
benchmark/evaluation documentation tương ứng.

------------------------------------------------------------------------

## 7. Relationship With Other Diagrams

  -----------------------------------------------------------------------
  Diagram                             Câu hỏi chính
  ----------------------------------- -----------------------------------
  System Context                      ProPulse tương tác với những hệ
                                      thống/actor nào?

  Architecture                        ProPulse gồm những component nào?

  Data Flow                           Dữ liệu biến đổi qua pipeline như
                                      thế nào?

  Sequence                            Các component thực thi theo thứ tự
                                      nào?

  ER                                  Các entity và relationship dữ liệu
                                      là gì?

  **Deployment**                      ProPulse chạy ở đâu và trên những
                                      runtime/resource nào?
  -----------------------------------------------------------------------

Deployment Diagram hoàn thiện góc nhìn thứ sáu của hệ thống:

**Context → Architecture → Data Flow → Sequence → Data Model →
Deployment**

------------------------------------------------------------------------

## 8. Final Design Principle

> **ProPulse should remain locally reproducible, classically
> benchmarkable, and quantum-executable across multiple execution
> environments without changing its scientific pipeline.**

Deployment là lớp hạ tầng hỗ trợ research, không phải nơi quyết định
scientific hypothesis.
