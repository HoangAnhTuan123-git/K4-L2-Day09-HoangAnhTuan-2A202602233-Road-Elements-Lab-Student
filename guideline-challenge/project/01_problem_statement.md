# Problem statement + downstream contract

## Bài toán

Nhận diện và phân loại phương tiện giao thông cùng người tham gia giao thông (Car, Motorcycle, Pickup, Truck, Bus, Pedestrian, Driver) trên dữ liệu camera đường phố và cao tốc trong các điều kiện thời tiết phức tạp (mưa, đêm, chạng vạng, tuyết), khó ở việc phân biệt ranh giới che khuất, người điều khiển xe gắn liền với xe, và phương tiện bị cắt ở rìa ảnh.

## Downstream contract

1. **Downstream task / model / user là ai?**
   Mô hình thị giác máy tính 2D Object Detection (YOLOv8/RT-DETR) phục vụ module nhận thức cho hệ thống hỗ trợ lái xe nâng cao (ADAS) và camera giám sát giao thông thông minh (ITS).

2. **Output annotation nào thực sự cần?**
   - Geometry: 2D Bounding box (`rectangle`) ôm khít các pixel nhìn thấy.
   - Classes: `Car`, `Motorcycle`, `Pickup`, `Truck`, `Bus`, `Pedestrian`, `Driver`.
   - Attributes: `occluded` (boolean checkbox), `truncated` (boolean checkbox).

3. **Failure nào gây hậu quả lớn nhất?**
   - Bỏ sót hoàn toàn người điều khiển xe máy (`Driver`) hoặc người đi bộ (`Pedestrian`) ở cự ly gần hoặc trong bóng tối (nguy cơ va chạm chết người trực tiếp).
   - Nhầm lẫn xe tải chở hàng lớn (`Truck`) hoặc xe buýt (`Bus`) thành xe con, dẫn đến ước lượng sai kích thước và khoảng cách an toàn phanh.

4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?**
   Annotator đánh dấu checkbox `needs_review` hoặc tag `image_escalate`, kèm ghi chú mô tả; Lead Reviewer sẽ họp đối chiếu trực tiếp theo decision tree trong Guideline mục 7.

## Scope

- **Trong scope (bắt buộc label):** Mọi xe cơ giới di chuyển hoặc đỗ (`Car`, `Pickup`, `Truck`, `Bus`, `Motorcycle`) và con người (`Pedestrian`, `Driver`), bao gồm cả đối tượng bị che khuất một phần (occluded) hoặc cắt ở rìa (truncated) có kích thước $\ge 8\text{px}$.
- **Ngoài scope (ignore):** Xe đồ chơi, ảnh xe trên biển quảng cáo/pano, bóng đổ trên mặt đường nhựa, phản chiếu gương/nước, vật thể xa $< 8\text{px}$ không nhận dạng được hình thái.
- **Geometry tolerance:** Bounding box ôm sát mép biên ngoài cùng của vật thể (bánh xe tiếp đất, gương chiếu hậu hai bên), dung sai sai lệch $\le \pm 2\text{px}$ mỗi cạnh.

## Output chấm được

Mọi quyết định trong blind test đều được phản ánh trực tiếp trong CVAT XML/COCO export:
- Loại đối tượng: Tên nhãn tương ứng (`Car`, `Driver`, `Pedestrian`, v.v.).
- Bị che / bị cắt: Thuộc tính `occluded="true"`, `truncated="true"`.
- Bỏ qua (Ignore): Không vẽ box (hoặc đánh dấu nhãn Ignore nếu có).
- Geometry: Bounding box tọa độ `[xtl, ytl, xbr, ybr]`.

## Dữ liệu và giới hạn

Sử dụng 15 ảnh từ tập dữ liệu `bdd100k` (gồm 4 ảnh example, 6 ảnh calibration, 5 ảnh blind). Giới hạn đã biết: ảnh BDD100K có nhiều cảnh ban đêm và chạng vạng gây lóa đèn pha hoặc tối đen gầm xe, đòi hỏi annotator phải tăng độ sáng màn hình và soi kỹ mép bánh xe.
