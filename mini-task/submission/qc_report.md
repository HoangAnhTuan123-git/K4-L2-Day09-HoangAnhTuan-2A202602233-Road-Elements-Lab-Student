# QC report

Họ tên: Hoàng Anh Tuấn · Chế độ: cá nhân · Nếu nhóm — các thành viên: Không có (chế độ cá nhân)
Guideline dùng: `GUIDE.md` + 4 card, bản phát ngày học.

Viết ở phút 205–225. Đã hoàn thiện toàn bộ nội dung báo cáo.

- **Nhóm**: chọn một bạn cùng nhóm đã khoá xong, chạy `make peer TASK=<task> FILE=<annotations.xml của họ>
  CODE=<mã khoá của họ> NAME=<tên họ>`. Lệnh tự viết `submission/<task>/peer-<tên>.html` và `.txt` — mở file
  `.html` bằng trình duyệt để xem overlay hai bài. Report này là bạn review **bài đã khoá của họ**, không phải
  bản của mình.
- **Cá nhân**: chọn task đầu tiên bạn đã khoá (lane) và mở lại `submission/lane/compare.html` của chính mình,
  sau khi vẽ nó ≥ 2 giờ — coi như bài của người khác, không nhớ lại lúc vẽ đã nghĩ gì.

## 1. Sample plan

Không đủ thời gian xem hết. Chọn **6 sample** và nói vì sao chọn. Lấy theo lát dễ lỗi (ngã tư, crosswalk, đêm/mưa,
lóa, biển nhỏ, điểm chuyển state), không lấy ngẫu nhiên.

| # | Task | Sample (ảnh / frame) | Lát (vì sao chọn) |
|---|---|---|---|
| 1 | lane | bb890202 (ảnh 4) | Đêm/mưa, ánh sáng phản chiếu mặt đường ướt gây nhiễu biên vạch kẻ |
| 2 | lane | bb890202 (ảnh 12) | Khu vực giao lộ, vạch đứt quãng giao cắt với luồng rẽ |
| 3 | drivable | core 03 | Lối rẽ ngõ hẹp, ranh giới giữa lòng đường và vỉa hè mờ nhạt |
| 4 | drivable | core 10 | Đoạn đường có bóng râm lớn tương phản gắt với vùng nắng |
| 5 | traffic_sign | 00073.png | Biển báo ở hậu cảnh xa bị cành cây che khuất một phần |
| 6 | traffic_light | dayClip5 (frame 19) | Điểm chuyển giao trạng thái thời gian giữa Green và Yellow |

## 2. Lỗi tìm thấy

Ít nhất 1 lỗi geometry, 1 lỗi attribute và 1 ca cần vào decision log. Nếu không tìm thấy loại nào, ghi rõ "đã xem,
không có".

- `error_type`: `geometry`, `missing`, `class`, `attribute`, `temporal`, `guideline_gap` (taxonomy của buổi học).
- `severity` (quy ước của lab, không phải thang của doanh nghiệp):
  `critical` = đổi quyết định của ego (state/relevance sai, drivable lấn sang làn ngược chiều, mất lane ngay trước xe);
  `major` = sai attribute hoặc geometry mà model sẽ học theo; `minor` = lệch nhỏ, không đổi nghĩa.
- `action`: `accept`, `rework`, `escalate`.

| Task | Sample | Object | Mô tả lỗi | error_type | severity | action | Downstream sai gì nếu bỏ qua |
|---|---|---|---|---|---|---|---|
| lane | bb890202 ảnh 4 | lane_marking | Điểm cuối polyline lệch nhẹ 2.5px so với mép vạch mờ | geometry | minor | accept | Sai lệch nhỏ không làm lệch quỹ đạo bám làn của xe |
| traffic_sign | 00073.png | traffic_sign | Quên đổi thuộc tính readable từ true sang false ở biển nhỏ xa | attribute | major | rework | Model sẽ học tính năng từ đốm nhòe, gây nhận diện ảo (hallucination) |
| traffic_light | dayClip5 frame 19 | traffic_light | Trạng thái chuyển đổi đèn vàng bóng hơi chớp cần rule quy ước frame | guideline_gap | critical | escalate | Nhầm trạng thái đèn vàng/đỏ có thể khiến xe phanh gấp hoặc vượt đèn |

## 3. Kết luận cho batch

- Accept / rework / escalate cả batch, và lý do: Accept có điều kiện (Conditional Accept). Các lỗi hình học đều nằm trong dung sai cho phép, chỉ cần rework lại thuộc tính `readable` của biển báo nhỏ và chốt quy tắc xử lý frame chuyển trạng thái đèn trong guideline.
- Note cho người label (1–2 câu, nói cách sửa — với cá nhân thì viết cho chính mình): Luôn bật chế độ zoom tối đa ở các điểm biên bị mờ hoặc chóa sáng; kiểm tra danh sách thuộc tính trước khi bấm Save.
- Known limitation phải ghi khi handoff (điều guideline chưa quyết): Chưa có quy chuẩn rõ ràng về việc gán nhãn đèn giao thông ở ngã tư kế tiếp cách xa trên 60 mét.
