# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| `v1` | Khởi tạo Guideline v1 với 7 class và 2 attribute | Thiết lập quy chuẩn ban đầu cho toàn bộ nhóm | G1 Topic Lock & Ontology setup |
| `v2` | Chuẩn hóa Driver (chỉ đánh dấu người lái xe máy/xe đạp, không cần đánh dấu phương tiện), Car (bánh xe + gương), hạ ngưỡng kích thước nhỏ $\ge 8\text{px}$ và xử lý chùm xe máy đông đúc | Giải quyết bất đồng calibration và các tình huống edge cases thực tế | BDD10, BDD12, BDD15 trong calibration report và 4 ảnh edge cases |
| `v3` | Bổ sung quy tắc tai nạn ngã xe máy (EC-09: xe đổ gán Motorcycle, người văng gán Pedestrian; chỉ gán Driver khi ngồi trên xe), mẹo căn chỉnh tiếp xúc bánh xe ban đêm và checklist kiểm tra thuộc tính occluded | Phản hồi peer review blind handoff (Nhóm 4) và câu hỏi clarification | Clarification log câu 1, Peer feedback câu 2/4/5, BDD18 d2, BDD26 d2 |

