# Contributing to ProPulse

Cảm ơn bạn đã đóng góp cho **ProPulse --- Protein Signal Intelligence**.

ProPulse là một research software project. Vì vậy, đóng góp không chỉ
cần code chạy đúng mà còn phải giữ được tính đúng đắn khoa học, khả năng
kiểm chứng và khả năng tái lập.

## 1. Trước khi bắt đầu

Hãy đọc các tài liệu nền tảng phù hợp với thay đổi bạn sắp thực hiện:

-   `PRD.md` --- mục tiêu, scope và non-goals.
-   `ARCHITECTURE.md` --- cấu trúc hệ thống và ranh giới module.
-   `SCHEMA.md` --- data model và data contracts.
-   `RULES.md` --- các quy tắc phát triển của project.
-   `WORKFLOW.md` --- quy trình từ task đến Done.
-   `TECH_STACK.md` --- technology stack.

Nếu thay đổi liên quan đến research protocol, hãy xác định rõ
assumption, input, output và cách validation trước khi implementation.

## 2. Chọn loại đóng góp

ProPulse có thể nhận các loại đóng góp như:

-   bug fix;
-   test;
-   documentation;
-   refactor;
-   structure/graph processing;
-   classical propagation;
-   quantum propagation;
-   connectivity metric;
-   residue ranking;
-   benchmark;
-   research experiment.

Đối với scientific core, ưu tiên correctness và reproducibility hơn tốc
độ phát triển.

## 3. Tạo Task / Issue

Mỗi thay đổi đáng kể nên bắt đầu từ một task hoặc issue rõ ràng.

Task nên mô tả tối thiểu:

``` text
Objective:
Scope:
Expected Output:
Validation:
Scientific Assumptions:
```

Không nên bắt đầu một thay đổi lớn chỉ với một ý tưởng mơ hồ như:

``` text
"Thử thêm quantum method."
```

Hãy biến nó thành một câu hỏi hoặc mục tiêu có thể kiểm chứng.

## 4. Development

Giữ thay đổi tập trung vào một mục đích.

Ưu tiên:

``` text
Small scope
     ↓
Clear implementation
     ↓
Test
     ↓
Review
```

Tránh:

-   thay đổi không liên quan trong cùng một commit;
-   thêm abstraction khi chưa cần;
-   thêm dependency chỉ để giải quyết một vấn đề nhỏ;
-   trộn refactor lớn với thay đổi scientific mà không có lý do rõ ràng.

Tuân thủ các boundary đã định nghĩa trong `ARCHITECTURE.md` và các quy
tắc trong `RULES.md`.

## 5. Scientific Changes

Các thay đổi sau được xem là **scientific-sensitive**:

-   residue representation;
-   contact definition;
-   edge weighting;
-   graph construction;
-   Hamiltonian;
-   propagation dynamics;
-   propagation time;
-   connectivity metric;
-   ranking metric;
-   benchmark protocol;
-   validation/reference data.

Với các thay đổi này, cần mô tả rõ:

``` text
What changed?
Why?
What scientific assumption changed?
How was it validated?
```

Không được trình bày một hypothesis chưa được kiểm chứng như một
scientific fact.

## 6. Classical vs Quantum

Khi so sánh classical và quantum propagation, hãy giữ comparison công
bằng.

Trừ khi experiment được thiết kế để nghiên cứu một biến khác, hai phương
pháp nên sử dụng:

``` text
Same Protein
     ↓
Same Graph Representation
     ↓
Different Propagation Model
     ↓
Comparable Connectivity / Ranking
     ↓
Same Evaluation Protocol
```

Mục tiêu của benchmark là kiểm tra liệu quantum propagation có thực sự
cung cấp lợi ích hay không, không phải thiết kế experiment để quantum
luôn thắng.

## 7. Testing

Mọi code thay đổi cần có mức testing phù hợp.

Tối thiểu:

``` text
Implementation
    ↓
Relevant Tests
    ↓
Scientific Validation (when applicable)
```

Đối với scientific core, hãy ưu tiên kiểm tra:

-   shape và dimensionality;
-   residue identity mapping;
-   graph consistency;
-   mathematical invariants;
-   normalization/probability constraints khi phù hợp;
-   deterministic behavior khi được yêu cầu;
-   regression trên các case đã biết.

Nếu một thay đổi làm thay đổi research result, không chỉ kiểm tra code
mà phải đánh giá impact lên experiment.

## 8. Reproducibility

Một experiment quan trọng phải có đủ context để tái lập.

Khi phù hợp, ghi nhận:

``` text
Input structure
Graph definition
Graph parameters
Propagation method
Propagation parameters
Connectivity metric
Ranking metric
Software version
Environment
Output artifact
```

Đặc biệt, nếu kết quả thay đổi, chúng ta phải có khả năng truy nguyên
thay đổi đến:

``` text
data
graph
algorithm
parameter
software
```

## 9. Pull Request

Một Pull Request nên giải thích ngắn gọn:

``` text
What changed?
Why?
How was it tested?
Does it affect scientific behavior?
```

Nếu có research result, nên đính kèm hoặc chỉ rõ artifact liên quan.

Reviewer cần có đủ context để đánh giá cả engineering correctness và
scientific validity khi cần.

## 10. Commit

Commit nên mô tả một logical change.

Ví dụ:

``` text
Add residue parser
Build contact graph
Add diffusion propagation
Implement CTQW simulation
Add connectivity metric
Add ranking tests
Fix residue mapping
```

Tránh commit message quá chung chung như:

``` text
update
fix
changes
final
```

## 11. Documentation

Nếu contribution làm thay đổi:

-   mục tiêu/scope → cập nhật `PRD.md`;
-   architecture → cập nhật `ARCHITECTURE.md`;
-   data contract → cập nhật `SCHEMA.md`;
-   project rule → cập nhật `RULES.md`;
-   technology → cập nhật `TECH_STACK.md`;
-   development workflow → cập nhật `WORKFLOW.md`.

Documentation là một phần của contribution khi behavior hoặc research
protocol thay đổi.

## 12. Điều không nên làm

Không:

-   đưa Molecular Dynamics vào core pipeline nếu chưa có quyết định
    research rõ ràng;
-   thêm ML/GNN/QML vào core chỉ vì nó có vẻ hữu ích;
-   thay đổi graph representation mà không ghi nhận;
-   cherry-pick kết quả đẹp rồi bỏ qua negative result;
-   dùng validation information làm input mà không kiểm tra leakage;
-   kết luận quantum tốt hơn chỉ từ một protein hoặc một metric;
-   hy sinh scientific correctness để có benchmark score đẹp.

## 13. Definition of Done

Một contribution được xem là sẵn sàng để merge khi:

-   [ ] Scope rõ ràng và đã hoàn thành.
-   [ ] Code phù hợp với architecture.
-   [ ] Tests liên quan đã pass.
-   [ ] Scientific assumptions được ghi nhận khi cần.
-   [ ] Scientific validation đã được thực hiện nếu thay đổi ảnh hưởng
    research.
-   [ ] Không có data leakage rõ ràng.
-   [ ] Documentation đã cập nhật nếu cần.
-   [ ] Reviewer có đủ context để đánh giá thay đổi.

## 14. Nguyên tắc cuối cùng

ProPulse là một research project trước khi là một software project.

Vì vậy:

> **Scientific correctness \> research validity \> reproducibility \>
> maintainability \> performance \> convenience.**

Và quan trọng nhất:

> **Experiment phải được phép chứng minh hypothesis của ProPulse là
> sai.**
