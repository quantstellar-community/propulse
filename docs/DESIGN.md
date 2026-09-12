# ProPulse --- Design Document

**Phiên bản:** 0.1\
**Trạng thái:** Draft / Research Prototype\
**Ngôn ngữ:** Tiếng Việt\
**Dự án:** ProPulse --- Protein Signal Intelligence

------------------------------------------------------------------------

## 1. Mục đích

`DESIGN.md` mô tả cách ProPulse được thiết kế để một researcher có thể
tương tác với hệ thống, thực hiện một research run, đọc kết quả và
chuyển từ kết quả tính toán sang diễn giải trên cấu trúc protein.

Theo Documentation & Diagram Baseline Guide, Design Document tập trung
vào **cách sản phẩm được thiết kế và trải nghiệm bởi người dùng**. Vì
ProPulse hiện là một scientific research engine, không phải UI-heavy
application, tài liệu này ưu tiên:

-   researcher experience;
-   command-line interaction;
-   experiment flow;
-   output presentation;
-   interpretability;
-   consistency và clarity.

Các chi tiết về module boundaries và technical architecture không thuộc
tài liệu này; chúng được quản lý trong `ARCHITECTURE.md`.

------------------------------------------------------------------------

## 2. Design Philosophy

### 2.1. Research-first

ProPulse được thiết kế trước hết để hỗ trợ một quá trình nghiên cứu có
thể kiểm chứng.

Người dùng phải có thể hiểu:

``` text
Input
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

mà không cần suy đoán hệ thống đã làm gì ở bên trong.

### 2.2. Core-first

Interface không được che khuất scientific core.

CLI, visualization và các tiện ích khác chỉ là phương tiện để researcher
tương tác với scientific engine.

### 2.3. Scientific transparency

ProPulse không được trình bày một prediction như một sự thật đã được
chứng minh.

Kết quả nên phân biệt rõ:

-   model output;
-   known reference;
-   baseline comparison;
-   empirical result;
-   interpretation.

### 2.4. Reproducibility by default

Một research run cần có đủ thông tin để người dùng biết:

-   structure nào được sử dụng;
-   graph được tạo thế nào;
-   propagation model nào được chạy;
-   parameters nào được sử dụng;
-   connectivity metric nào được dùng;
-   ranking được tạo ra thế nào.

### 2.5. Comparable by design

Khi so sánh classical và quantum, trải nghiệm sử dụng phải làm rõ rằng
hai model đang được đánh giá trên cùng biological graph và cùng
evaluation protocol khi thiết kế experiment cho phép.

------------------------------------------------------------------------

## 3. Primary User Experience

Researcher experience mục tiêu:

``` text
Chọn protein
    ↓
Chọn / xác định experiment
    ↓
Xây dựng protein graph
    ↓
Chạy propagation
    ↓
Tạo connectivity
    ↓
Tạo ranking
    ↓
Xem evaluation
    ↓
Kiểm tra residue trên cấu trúc
    ↓
Lưu kết quả / tái lập experiment
```

Một research run nên trả lời được ba câu hỏi:

1.  **Hệ thống đã làm gì?**
2.  **Hệ thống dự đoán điều gì?**
3.  **Prediction đó có evidence tốt đến đâu?**

------------------------------------------------------------------------

## 4. Interaction Model

### 4.1. CLI-first

Ở giai đoạn hiện tại, CLI là interface chính của ProPulse.

Mental model mục tiêu:

``` text
propulse <command> <options> <arguments>
```

Ví dụ về mặt UX:

``` bash
propulse analyze 4OBE.pdb
```

Tuy nhiên, việc expose executable command và packaging là
implementation/distribution concern. Trong giai đoạn prototype, cùng
workflow có thể được gọi thông qua Python/uv.

Ví dụ:

``` bash
uv run python run.py 4OBE.pdb
```

hoặc khi module entry point đã được thiết lập:

``` bash
uv run python -m propulse 4OBE.pdb
```

Các interface này phải hướng tới cùng một scientific workflow, không tạo
ra các implementation path khác nhau.

### 4.2. Progressive disclosure

Interface nên bắt đầu đơn giản.

Một user mới có thể chạy một experiment cơ bản mà không phải hiểu toàn
bộ parameter space.

Người dùng nghiên cứu nâng cao có thể tiếp cận:

-   graph construction parameters;
-   propagation parameters;
-   quantum parameters;
-   connectivity metric;
-   ranking/evaluation settings.

Không nên bắt user cung cấp hàng chục tham số khi default configuration
đã đủ cho một baseline experiment.

### 4.3. Explicit over implicit

Các lựa chọn có ảnh hưởng đến scientific result phải được thể hiện rõ.

Ví dụ:

``` text
Protein structure
Graph/contact configuration
Propagation model
Propagation parameters
Connectivity metric
Ranking method
Evaluation configuration
```

Không nên âm thầm thay đổi một scientific assumption chỉ vì interface
muốn "dễ dùng".

------------------------------------------------------------------------

## 5. Input Design

Input chính của ProPulse là protein structure.

Nguồn structure trong challenge là RCSB PDB.

Người dùng cần có khả năng xác định rõ:

-   structure identifier hoặc input file;
-   chain/domain scope nếu experiment yêu cầu;
-   structure version/source;
-   các lựa chọn graph construction liên quan.

Input representation phải giữ được mapping từ kết quả cuối cùng trở lại
residue trong protein structure.

### Input principle

> Không để mất biological identity khi chuyển từ structure sang graph.

Một residue trong ranking phải có thể truy nguyên về residue cụ thể
trong structure.

------------------------------------------------------------------------

## 6. Output Design

Output được thiết kế theo ba tầng.

### 6.1. Machine-readable output

Các kết quả quan trọng cần có format phù hợp cho downstream analysis.

Các output cốt lõi gồm:

``` text
Connectivity matrix
Residue-level scores
Residue ranking
Top-5 candidates
Experiment metadata
```

### 6.2. Human-readable summary

Sau mỗi research run, người dùng nên nhận được summary ngắn gọn, ví dụ:

``` text
Protein: 4OBE
Model: CTQW

Top candidates:
1. Chain A / Residue ...
2. Chain A / Residue ...
3. Chain A / Residue ...
4. Chain A / Residue ...
5. Chain A / Residue ...

Connectivity output: ...
Evaluation: ...
```

Summary không được thay thế raw scientific output.

### 6.3. Visualization-ready output

Kết quả cần có đủ residue/chain identifiers và mapping information để có
thể được kiểm tra trên cấu trúc 3D bằng công cụ visualization bên ngoài
như PyMOL.

PyMOL là công cụ hỗ trợ diễn giải và kiểm tra trực quan, không phải
dependency bắt buộc của scientific core.

------------------------------------------------------------------------

## 7. Result Presentation

ProPulse nên trình bày kết quả theo thứ tự:

``` text
Experiment context
        ↓
Model used
        ↓
Prediction
        ↓
Comparison
        ↓
Evaluation
        ↓
Interpretation
```

Không nên chỉ đưa ra:

``` text
Top-5 residues
```

mà không cho biết:

-   model nào tạo ranking;
-   ranking được tính từ representation nào;
-   prediction được đánh giá với reference nào;
-   classical baseline cho kết quả ra sao.

### Classical vs Quantum

Khi hai model được chạy trong cùng experiment, kết quả nên được trình
bày cạnh nhau:

``` text
                 Classical      Quantum
Connectivity        ...            ...
Top-1               ...            ...
Top-5               ...            ...
Enrichment          ...            ...
Statistical test    ...            ...
```

Mục tiêu là giúp researcher đánh giá **difference in evidence**, không
phải chỉ nhìn một quantum score rồi kết luận quantum advantage.

------------------------------------------------------------------------

## 8. Interpretability

Interpretability là một phần quan trọng của trải nghiệm ProPulse.

Người dùng cần có khả năng đi ngược:

``` text
Predicted residue
       ↓
Residue score
       ↓
Connectivity
       ↓
Propagation behavior
       ↓
Protein graph
       ↓
Protein structure
```

Điều này đặc biệt quan trọng với allosteric prediction vì prediction
không chỉ cần "đúng", mà còn cần hỗ trợ researcher hiểu residue đó liên
quan thế nào đến propagation.

### 3D interpretation

Khi cần, predicted residues và communication information có thể được đưa
sang công cụ 3D visualization.

ProPulse không yêu cầu GUI 3D trong core version.

------------------------------------------------------------------------

## 9. Error and Uncertainty Communication

ProPulse phải tránh tạo cảm giác certainty giả.

Nếu một experiment có:

-   thiếu residue mapping;
-   structure không đầy đủ;
-   graph disconnected;
-   parameter không hợp lệ;
-   quantum simulation không đủ khả năng scale;
-   result không có statistical support;

hệ thống cần báo rõ trạng thái hoặc limitation.

Không nên biến một failed/weak experiment thành một prediction có vẻ
chắc chắn.

### Nguyên tắc

> Không có evidence đủ mạnh thì không trình bày kết quả như một
> biological conclusion.

------------------------------------------------------------------------

## 10. Consistency

Các model và experiment nên dùng vocabulary thống nhất.

Một số thuật ngữ cốt lõi:

``` text
Protein Structure
Residue
Protein Contact Graph
Node
Edge
Classical Propagation
Quantum Propagation
Random Walk
Graph Diffusion
CTQW
Connectivity
Residue Score
Ranking
Top-5
Allosteric Reference
Baseline
Evaluation
```

Tên residue, chain và structure identifier phải được giữ nhất quán giữa:

``` text
Input
Graph
Connectivity
Ranking
Evaluation
Visualization
```

------------------------------------------------------------------------

## 11. Visualization Principles

Visualization được xem là lớp hỗ trợ research, không phải core
dependency.

Các visualization ưu tiên:

1.  Connectivity matrix / heatmap.
2.  Classical vs quantum comparison.
3.  Residue ranking.
4.  Nếu cần, propagation/pathway visualization.
5.  3D structural inspection thông qua công cụ bên ngoài.

Visualization phải phục vụ câu hỏi khoa học.

Không tạo visualization chỉ để làm giao diện "đẹp" hơn nếu nó không giúp
researcher hiểu:

-   signal propagation;
-   connectivity;
-   residue ranking;
-   classical/quantum difference.

------------------------------------------------------------------------

## 12. Accessibility and Usability

Vì ProPulse hiện chủ yếu là CLI/research software:

-   output text phải rõ ràng;
-   không phụ thuộc vào màu sắc để truyền tải thông tin quan trọng;
-   error message phải có nguyên nhân có thể hành động;
-   file output phải có tên và schema dễ hiểu;
-   các experiment quan trọng phải có metadata rõ ràng.

Nếu sau này phát triển GUI/web interface, accessibility và responsive
behavior sẽ được thiết kế riêng trong một design revision tương ứng.

------------------------------------------------------------------------

## 13. Design Boundaries

`DESIGN.md` không quyết định:

-   cấu trúc module Python;
-   implementation của CTQW;
-   định nghĩa toán học cuối cùng của connectivity metric;
-   contact cutoff cuối cùng;
-   dependency versions;
-   database schema;
-   deployment infrastructure.

Các quyết định đó thuộc các tài liệu khác.

Mental model:

``` text
DESIGN
→ Người dùng tương tác với ProPulse như thế nào?

ARCHITECTURE
→ ProPulse được cấu trúc kỹ thuật như thế nào?

SCHEMA
→ Data được biểu diễn như thế nào?

TECH_STACK
→ Dùng công nghệ gì?

RULES
→ Project phải tuân thủ nguyên tắc kỹ thuật nào?
```

------------------------------------------------------------------------

## 14. Current Design Scope

Design của ProPulse v0.1 tập trung vào:

``` text
Researcher
    ↓
CLI / minimal interface
    ↓
Scientific Core
    ↓
Structured outputs
    ↓
Evaluation
    ↓
Visualization / interpretation
```

Chưa bao gồm:

-   web application;
-   dashboard;
-   user authentication;
-   multi-user collaboration UI;
-   cloud platform;
-   production API;
-   complex visualization platform.

Các thành phần này chỉ được xem xét khi research core tạo ra nhu cầu
thực tế.

------------------------------------------------------------------------

## 15. Design Success Criteria

Design được xem là phù hợp khi researcher có thể:

1.  Hiểu input của experiment.
2.  Biết model nào đang được chạy.
3.  Biết output được tạo ra từ đâu.
4.  Đọc được top-5 prediction.
5.  So sánh classical và quantum một cách rõ ràng.
6.  Truy ngược prediction về residue và structure.
7.  Phân biệt prediction với validation/evidence.
8.  Tái lập research run từ metadata.
9.  Kiểm tra kết quả bằng visualization khi cần.
10. Không cần GUI để thực hiện scientific core workflow.

------------------------------------------------------------------------

## 16. Design Principle Summary

``` text
Research-first
      ↓
Core-first
      ↓
CLI-first
      ↓
Explicit scientific assumptions
      ↓
Reproducible interaction
      ↓
Machine-readable + human-readable outputs
      ↓
Interpretability
      ↓
Visualization when useful
```

ProPulse được thiết kế để researcher **hiểu và kiểm chứng signal
propagation**, không chỉ để nhận một prediction.

------------------------------------------------------------------------

## 17. Relationship with Other Documents

``` text
PRD.md
  → ProPulse cần xây gì và tại sao?

DESIGN.md
  → Researcher trải nghiệm và tương tác với ProPulse thế nào?

ARCHITECTURE.md
  → Scientific software được cấu trúc ra sao?

SCHEMA.md
  → Structure, graph và result data được biểu diễn thế nào?

RULES.md
  → Development phải tuân theo nguyên tắc nào?

TECH_STACK.md
  → Công nghệ nào được sử dụng và đóng vai trò gì?
```

`DESIGN.md` không thay thế các tài liệu trên và không được dùng để chứa
implementation details chỉ vì chúng ảnh hưởng đến UX.
