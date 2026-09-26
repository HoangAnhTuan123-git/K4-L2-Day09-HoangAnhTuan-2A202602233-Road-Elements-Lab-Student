# QA plan + quality gates

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate.

- **Ai review, review bao nhiêu:** QA Lead (Thành viên 3) thực hiện review 100% blind set và 50% production set.
- **Chọn sample theo rule nào:** Sampling phân tầng ưu tiên rủi ro: 100% ảnh có tag `critical`, `ambiguity`, `low_visibility` và 20% random ảnh `normal`.
- **Issue được ghi ở đâu, đóng thế nào:** Issue được ghi trực tiếp bằng tính năng Issue/Comment trong CVAT trên từng bounding box. Annotator sửa xong đánh dấu Resolved, QA verify đạt thì Close.
- **Khi phát hiện guideline gap thì update và version ra sao:** Ghi nhận vào `08_revision_log.md`, nâng version guideline (v1 → v2 → v3), thông báo trong group trao đổi của nhóm và dán lại bản mới vào Guide của CVAT task.

## Defect severity

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Bỏ sót hoàn toàn đối tượng dễ gây va chạm hoặc phân loại sai nghiêm trọng kích thước | Bỏ sót `Pedestrian` hoặc `Driver` trong bóng tối; nhầm `Truck` thành `Car` | Reject toàn bộ batch, rework ngay lập tức |
| Major | Sai class giữa các loại xe tương đồng hoặc sai thuộc tính rủi ro cao | Nhầm `Pickup` thành `Car`; bỏ quên cờ `occluded` khi bị che > 50% | Rework đối tượng cụ thể |
| Minor | Dung sai bounding box lệch nhẹ $\pm 3\text{px}$ hoặc quên cờ `truncated` ở mép khuất | Box hơi rộng ở mép bánh xe; cắt cụt 1px gương chiếu hậu ngoài | QA tự điều chỉnh trực tiếp |
| Question | Tình huống mập mờ chưa có trong guideline | Vật thể quá mờ ở xa không rõ người hay cọc tiêu | Escalate lên Spec Owner để ra rule |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Defect Rate | $\frac{\text{Số lỗi}}{\text{Tổng số đối tượng kiểm tra}} \times 100\%$ | Đo lường tỷ lệ sai sót tổng quát của annotator |
| Critical Escape Rate | $\frac{\text{Số lỗi Critical lọt lưới}}{\text{Tổng số lỗi Critical}} \times 100\%$ | Đảm bảo an toàn tính mạng trong downstream ADAS |
| Geometry Compliance | $\frac{\text{Số box đạt dung sai } \le 2\text{px}}{\text{Tổng số box kiểm tra}} \times 100\%$ | Đảm bảo độ chính xác tọa độ vị trí vật thể |

## Quality gate

```text
PASS if:
  Critical Defect Escape = 0
  Defect Rate <= 5%
  Geometry Compliance >= 95%
REWORK if:
  Defect Rate trong khoảng 5% - 15% hoặc có 1 lỗi Critical
REJECT / ESCALATE if:
  Có >= 2 lỗi Critical hoặc Defect Rate > 15%
```

Trade-off: Chấp nhận dung sai nhỏ (minor) ở các vật thể xa để giảm chi phí thời gian dán nhãn, nhưng áp dụng "Zero Tolerance" đối với lỗi Critical (bỏ sót người đi bộ hoặc người lái xe) nhằm đảm bảo tiêu chuẩn an toàn ADAS.
