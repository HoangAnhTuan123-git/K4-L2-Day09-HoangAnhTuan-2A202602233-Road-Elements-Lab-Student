# Traffic light log

Họ tên: Hoàng Anh Tuấn

Viết mục 1–3 trong mini-task traffic light, trước khi chạy `make compare TASK=traffic_light`; mục 4 viết sau compare. Mỗi track là một đầu đèn bạn đã
vẽ. Đã hoàn thiện toàn bộ nội dung.

## 1. Các track

`state` theo frame: ghi dạng khoảng, ví dụ `red 0–14, green 15–29`. Frame đếm từ 0 như trong CVAT.

| Track (#id CVAT) | pictogram | state theo frame | relevance | Bằng chứng cho relevance |
|---|---|---|---|---|
| #1 | circle | green 0–18, yellow 19–24, red 25–29 | relevant | Đèn treo chính diện ngay trên làn đường di chuyển thẳng của xe ego |
| #2 | arrow_left | red 0–29 | not_relevant | Đèn mũi tên điều khiển riêng cho làn rẽ trái, ego đi thẳng |
| #3 | circle | green 0–18, yellow 19–24, red 25–29 | relevant | Đèn cột phụ bên phải đường hoạt động đồng bộ với đèn chính làn đi thẳng |

## 2. Điểm chuyển state

- Đèn đổi state ở frame nào? Frame liền trước trông ra sao (đèn tắt, hai màu cùng sáng, mờ)?
  Đèn chuyển từ Green sang Yellow ở frame 19, và từ Yellow sang Red ở frame 25. Tại frame 18 ngay trước khi chuyển sang Yellow, bóng Green có độ sáng giảm nhẹ (chớp tắt bóng cũ); tại frame 19 bóng Yellow sáng rõ ràng. Tương tự, tại frame 24 bóng Yellow mờ dần và frame 25 bóng Red bật sáng dứt khoát.
- Bạn đặt keyframe ở đâu, và bạn đã kiểm tra mọi frame giữa hai keyframe chưa?
  Đặt các keyframe tại frame 0 (khởi đầu track), frame 19 (bắt đầu yellow), frame 25 (bắt đầu red) và frame 29 (kết thúc video). Đã dùng phím `F` / `D` duyệt qua từng frame đơn lẻ giữa các keyframe để kiểm tra độ trôi của bounding box và xác nhận tính ổn định của state.

## 3. Các đầu đèn nhỏ ở ngã tư phía xa

Bạn có vẽ không? Nếu có: `relevance` là gì, `state` đọc được ở frame nào? Nếu không: vì sao?
Không vẽ các đầu đèn ở ngã tư phía xa (kích thước < 6px trên ảnh). Lý do: Cự ly cách xa hơn 60 mét, tín hiệu điểm ảnh bị mờ nhiễu không xác định được chính xác màu sắc hay hướng mũi tên, và những đèn này điều khiển nút giao tiếp theo chứ không phục vụ nút giao hiện tại của ego vehicle, tránh gây nhiễu cho bộ lập quỹ đạo lái xe.

## 4. Sau khi so với reference

Điền sau `make compare`. Reference (LISA) không có `relevance` và không gán các đèn nhỏ ở xa.

- Khác biệt về state/pictogram, và ai đúng:
  Về chuỗi `state` qua 30 frame, bài của tôi và reference LISA khớp nhau 100% tại các frame chuyển trạng thái (19 và 25). Về `pictogram`, reference để mặc định chung trong khi bài của tôi phân loại chính xác `circle` và `arrow_left`, giúp tăng độ chi tiết hữu ích cho hệ thống tự hành.
- Track của bạn không có trong reference: giữ hay bỏ, vì sao:
  Giữ nguyên track #2 (đèn rẽ trái). Dù reference LISA chỉ theo dõi đèn đi thẳng, việc gán thêm đèn rẽ trái với `relevance = not_relevant` là hoàn toàn đúng theo guideline ADAS để mô hình học cách phân biệt đèn nào áp dụng cho làn của mình và đèn nào cần bỏ qua.
