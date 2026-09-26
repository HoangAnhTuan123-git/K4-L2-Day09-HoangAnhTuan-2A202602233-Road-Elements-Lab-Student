# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` khớp từng dòng ở đây.

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `Car` | rectangle | class | — | — | false | Xe con chở người 4–9 chỗ, sedan, SUV, taxi |
| `Motorcycle` | rectangle | class | — | — | false | Xe máy đỗ hoặc không có người điều khiển |
| `Pickup` | rectangle | class | — | — | false | Xe bán tải có cabin kín và thùng chở hàng hở phía sau |
| `Truck` | rectangle | class | — | — | false | Xe tải hạng trung/nặng, xe bồn, container |
| `Bus` | rectangle | class | — | — | false | Xe khách, xe buýt công cộng $\ge 16$ chỗ |
| `Pedestrian` | rectangle | class | — | — | false | Người đi bộ hoặc không ngồi trên xe điều khiển |
| `Driver` | rectangle | class | — | — | false | Người ngồi trên xe điều khiển, ôm trọn cả người và xe |
| `occluded` | — | attribute | true, false | false | false | Đánh dấu vật thể bị che khuất $> 10\%$ diện tích |
| `truncated` | — | attribute | true, false | false | false | Đánh dấu vật thể bị cắt ở rìa/biên khung ảnh |

## Class hay attribute

- **Tại sao là Class:** `Car`, `Motorcycle`, `Pickup`, `Truck`, `Bus`, `Pedestrian`, `Driver` là các thực thể khác biệt về hình học, đặc tính động học và mục tiêu nhận thức trong downstream ADAS. Tách thành các class riêng giúp mô hình học các anchor box và tỷ lệ khung hình khác nhau (xe tải cao to, xe máy thon dài, người thẳng đứng).
- **Tại sao là Attribute:** `occluded` và `truncated` là các trạng thái quan sát phụ thuộc vào góc nhìn camera và bố cục cảnh, có thể xảy ra trên bất kỳ phương tiện nào. Tách thành attribute tránh hiện tượng bùng nổ tổ hợp class (không cần tạo class như `Car_occluded_truncated`).
- **Phòng ngừa Bias từ Default:** Default của `occluded` và `truncated` là `false` (trạng thái bình thường). Annotator được nhắc nhở luôn quét kỹ rìa ảnh (x=0, y=0) để không bỏ sót cờ `truncated`.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): v2.74.1 tại `http://localhost:8080`
- **Tên task calibration**: `team09-calib-v1`
- **Guide của task đã dán `02_guideline.md`?**: Có (dán toàn bộ markdown vào Task Description)
- **Nhóm dùng Track hay Shape, vì sao:** Dùng **Shape**, vì bộ dữ liệu là các ảnh tĩnh độc lập (single-frame 2D detection), không có chuỗi video liên tục.

## Setup test

Thành viên Nguyễn Văn A (chưa tham gia viết code setup) mở task trên CVAT local:
- Hiểu ngay công cụ cần dùng: **Draw new rectangle**.
- Nắm rõ quy tắc quan trọng: `Driver` ôm cả người và xe máy; `Car` phải kéo khít cả bánh xe và gương chiếu hậu; xe chạm mép phải bật `truncated`.
- Không gặp vướng mắc kỹ thuật, giao diện hiển thị đầy đủ 7 class và 2 checkbox attribute.
