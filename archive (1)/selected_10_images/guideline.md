# Annotation Guideline — Road Vehicles & Human Elements Detection

**Version:** v1

---

## 1. Objective + Scope

- **Objective:** Cung cấp dữ liệu bounding box chuẩn xác cho bài toán nhận diện phương tiện giao thông và người tham gia giao thông phục vụ hệ thống cảnh báo va chạm (ADAS) và giám sát giao thông thông minh.
- **In-Scope (Bắt buộc gán nhãn):**
  - Mọi phương tiện cơ giới di chuyển hoặc đang đỗ trên đường: `Car`, `Motorcycle`, `Pickup`, `Truck`, `Bus`.
  - Người tham gia giao thông: `Pedestrian` (người đi bộ), `Driver` (người điều khiển xe máy/xe đạp/xe hở).
  - Phương tiện bị che khuất một phần (occluded) hoặc bị cắt ở rìa ảnh (truncated) miễn là còn đủ dấu hiệu nhận dạng (≥ 20% nhìn thấy hoặc nhận diện được loại phương tiện).
- **Out-of-Scope (Bỏ qua - Ignore):**
  - Xe đồ chơi, mô hình quảng cáo.
  - Hình ảnh xe cộ hoặc người in trên pano, áp phích, thân xe buýt.
  - Bóng phản chiếu của xe trên mặt đường ướt hoặc cửa kính.
  - Xe hoặc người ở khoảng cách cực xa có kích thước < 15 pixel và không thể phân biệt đặc trưng bằng mắt thường.

---

## 2. Annotation Unit

- **Đơn vị nhãn:** Bounding box 2D hình chữ nhật (`rectangle`) cho từng cá thể độc lập (instance-level).
- **Nguyên tắc instance:** Mỗi phương tiện hoặc người là một bounding box riêng biệt.
  - Với xe máy có người ngồi lái: vẽ **1 box `Motorcycle`** ôm toàn bộ thân xe máy và **1 box `Driver`** ôm người điều khiển.

---

## 3. Geometry Rule

- **Loại hình:** Rectangle (hộp chữ nhật song song với trục tọa độ x-y).
- **Độ khít (Tightness):** Hộp phải ôm **khít toàn bộ các pixel nhìn thấy** (visible pixels) của đối tượng:
  - Bao gồm: Thân xe, bánh xe, gương chiếu hậu, đèn xe, giá nóc (roof rack), thùng hàng phía sau.
  - **KHÔNG bao gồm:** Bóng đổ (shadow) của xe trên mặt đường, khói xả, vệt sáng đèn pha rọi ra ngoài.
- **Dung sai (Tolerance):** Độ lệch mép hộp không quá **±2 pixel** so với điểm biên ngoài cùng của vật thể.
- **Không vẽ Amodal:** Chỉ vẽ trên phần nhìn thấy, không tưởng tượng phần bị che khuất ngầm dưới lòng đất hay sau xe khác.

---

## 4. Taxonomy & Attribute Definition

### 4.1 Danh sách Class (Object Classes)

| Class | Định nghĩa & Tiêu chí nhận diện | Ví dụ phương tiện |
|---|---|---|
| **`Car`** | Xe con, xe du lịch chở người từ 4–9 chỗ, sedan, hatchback, SUV, crossover, xe taxi, xe cảnh sát con. | Toyota Vios, Camry, Honda CR-V, Mazda 3, Kia Morning. |
| **`Pickup`** | Xe bán tải: có cabin kín phía trước cho hành khách và **thùng chở hàng hở (open cargo bed)** tách biệt phía sau. Kể cả thùng có nắp đậy thấp ngang mép thùng vẫn là Pickup. | Ford Ranger, Toyota Hilux, Mitsubishi Triton, Isuzu D-Max. |
| **`Truck`** | Xe tải chở hàng hạng trung và nặng: thùng xe lớn, xe ben, xe bồn, xe đầu kéo container, xe tải chở vật liệu xây dựng (khung gầm tải lớn). | Hyundai Porter, Isuzu Forward, Howo, xe container. |
| **`Bus`** | Xe khách chở nhiều người (thường ≥ 16 chỗ), xe buýt công cộng, xe đưa đón học sinh/công nhân, xe khách liên tỉnh giường nằm. | Xe buýt nội đô, Hyundai Universe, Ford Transit chở khách (≥ 16 chỗ). |
| **`Motorcycle`** | Xe 2 bánh hoặc 3 bánh gắn động cơ: xe máy số, xe tay ga, xe phân khối lớn, xe ba gác máy. | Honda Wave, Lead, SH, Yamaha Exciter, Vespa. |
| **`Pedestrian`** | Người đi bộ đứng, đi, chạy, đẩy xe nôi/xe đẩy hàng trên vỉa hè hoặc lòng đường. | Người băng qua đường, người đi bộ trên vỉa hè. |
| **`Driver`** | Người đang trực tiếp điều khiển xe máy, xe đạp hoặc phương tiện hở buồng lái (ngồi trên yên hoặc cầm lái). | Người lái xe máy, shipper đang lái xe, người đạp xe. |

### 4.2 Thuộc tính (Attributes)

| Attribute | Kiểu | Giá trị | Tiêu chí đánh dấu (`true`) |
|---|---|---|---|
| **`occluded`** | Checkbox | `true` / `false` | Đánh dấu khi đối tượng bị **vật khác che khuất một phần** (bị xe khác che, bị cây xanh, cột đèn, biển báo hoặc người che mất > 10% diện tích). |
| **`truncated`** | Checkbox | `true` / `false` | Đánh dấu khi đối tượng **chạm hoặc vượt ra ngoài mép ảnh** (bị cắt cụt đầu, đuôi, nóc hoặc bánh xe do góc nhìn camera). |

---

## 5. Inclusion / Exclusion Details

1. **Xe đang đỗ bên đường:** BẮT BUỘC label nếu nằm trong khung nhìn và rõ hình dạng.
2. **Cụm xe ùn tắc / nối đuôi:** Tách từng xe riêng rẽ thành từng box độc lập, không gộp cụm.
3. **Xe cõng xe (xe cứu hộ chở ô tô):**
   - Xe cứu hộ bên dưới: Label `Truck`.
   - Ô tô được chở trên sàn xe cứu hộ: Vẫn label `Car` (thuộc tính `occluded: true` vì bánh bị che bởi sàn xe tải).
4. **Vật thể cực nhỏ (< 15px):** Bỏ qua (Ignore) nếu không nhìn rõ kết cấu hoặc loại xe.

---

## 6. Visibility & Occlusion Rule

- **Che khuất 10% – 80%:** Label bình thường và tích chọn `occluded: true`.
- **Che khuất > 80% (Heavy Occlusion):**
  - Nếu vẫn nhận diện chắc chắn loại xe (ví dụ nhìn rõ đuôi xe và logo): Vẫn label box visible pixel và chọn `occluded: true`.
  - Nếu chỉ thấy một đốm màu mơ hồ, không thể xác định là `Car`, `Truck` hay `Pickup`: **BỎ QUA (Ignore)**.
- **Rìa ảnh (Truncation):** Mép box kéo sát tới pixel cuối cùng của khung ảnh (x=0, y=0 hoặc x=width, y=height), và tích chọn `truncated: true`.

---

## 7. Ambiguity & Escalation Path

Khi gặp trường hợp tranh chấp không rõ ràng, tuân thủ bảng quyết định sau:

| Tình huống mập mờ | Quyết định chuẩn | Lý do |
|---|---|---|
| **Pickup có gắn nắp thùng cao ngang nóc xe** | **`Pickup`** | Vẫn giữ kết cấu gầm và rãnh phân cách giữa cabin và thùng chở hàng. |
| **Xe van chở hàng (không có cửa sổ hông phía sau)** | Nếu kích thước nhỏ (dưới 7 chỗ) -> **`Car`**. Nếu xe tải van lớn (như Hyundai Solati van, Ford Transit van thùng kín) -> **`Truck`**. | Dựa trên tải trọng và công năng chở hàng vs chở người. |
| **Người ngồi sau xe máy (Pillion passenger)** | Label **`Pedestrian`** hoặc gộp vào vùng người nếu không phân tách được; nếu người cầm lái thì bắt buộc là **`Driver`**. | Đảm bảo tính nhất quán của người điều khiển phương tiện. |
| **Không thể phân biệt giữa Car và Truck do quá mờ** | **Ignore** nếu < 20px; nếu lớn hơn hãy báo escalate cho Lead Annotator. | Tránh tạo nhiễu nhãn sai trong tập huấn luyện. |

---

## 8. Temporal Rule

- **Quy tắc thời gian:** **Không áp dụng — task ảnh tĩnh (Static 2D Detection).** Mỗi ảnh được gán nhãn độc lập dựa trên frame hiện tại.

---

## 9. Examples & Reference Cases

| Case | Loại tình huống | Expected Output | Ghi chú & Rule |
|---|---|---|---|
| **Xe ô tô con đi giữa đường** | Normal | Label `Car`, `occluded: false`, `truncated: false` | Bounding box ôm khít gương, bánh xe chạm đất, không bao gồm bóng râm. |
| **Xe bán tải bị xe khác đỗ che mất bánh** | Occlusion | Label `Pickup`, `occluded: true`, `truncated: false` | Nhận diện rõ thùng hàng hở phía sau; tích `occluded`. |
| **Xe chỉ nhìn thấy nửa đầu bên trái ảnh** | Truncation | Label `Car`, `occluded: false`, `truncated: true` | Mép trái box chạm x=0; tích `truncated`. |
| **Người lái xe máy qua ngã tư** | Composite | 1 box `Motorcycle` + 1 box `Driver` | Box `Motorcycle` ôm toàn bộ xe; box `Driver` ôm người lái (có thể overlap). |
| **Bóng xe in trên mặt đường nhựa** | Negative | **Không vẽ box** | Bóng đổ không phải là bộ phận cơ học của xe. |

---

## 10. Common Mistakes & Quality Checklist

Trước khi hoàn thành và xuất file (Save/Export), kiểm tra các lỗi thường gặp sau:
1. ❌ **Lỗi bóng đổ:** Vẽ box quá rộng xuống tận vùng bóng đen trên đường (Sai dung sai).
2. ❌ **Quên tích `truncated`:** Xe chạm mép ảnh nhưng quên không đánh dấu checkbox `truncated`.
3. ❌ **Quên tích `occluded`:** Xe bị cột đèn hoặc đuôi xe trước che mất một phần nhưng để `occluded: false`.
4. ❌ **Nhầm lẫn giữa `Pickup` và `Car`:** Coi xe bán tải là SUV thông thường (Cần chú ý thùng hàng phía sau).
5. ❌ **Gộp xe và người lái thành một box:** Xe máy phải tách riêng với `Driver`.
