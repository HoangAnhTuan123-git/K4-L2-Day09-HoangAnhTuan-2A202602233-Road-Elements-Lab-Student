# Annotation Guideline — Road Vehicles & Human Elements Detection

**Version:** v1

---

## 1. Objective + Scope

- **Objective:** Cung cấp dữ liệu bounding box chuẩn xác cho bài toán nhận diện phương tiện giao thông và người tham gia giao thông phục vụ hệ thống cảnh báo va chạm (ADAS) và giám sát giao thông thông minh.
- **In-Scope (Bắt buộc gán nhãn):**
  - Mọi phương tiện cơ giới di chuyển hoặc đang đỗ trên đường: `Car`, `Motorcycle`, `Pickup`, `Truck`, `Bus`.
  - Người tham gia giao thông: `Pedestrian` (người đi bộ/không ngồi trên xe), `Driver` (người ngồi trên xe điều khiển xe, bao gồm cả người và xe).
  - Phương tiện bị che khuất một phần (occluded) hoặc bị cắt ở rìa ảnh (truncated) miễn là còn đủ dấu hiệu nhận dạng (≥ 20% nhìn thấy hoặc nhận diện được loại phương tiện).
- **Out-of-Scope (Bỏ qua - Ignore):**
  - Xe đồ chơi, mô hình quảng cáo.
  - Hình ảnh xe cộ hoặc người in trên pano, áp phích, thân xe buýt.
  - Bóng phản chiếu của xe trên mặt đường ướt hoặc cửa kính.
  - Xe hoặc người ở khoảng cách cực xa có kích thước < 15 pixel và không thể phân biệt đặc trưng bằng mắt thường.

---

## 2. Annotation Unit

- **Đơn vị nhãn:** Bounding box 2D hình chữ nhật (`rectangle`) cho từng cá thể độc lập (instance-level).
- **Quy tắc quan trọng cho `Driver` và `Pedestrian`:**
  - **`Driver` CHỈ áp dụng khi:** Người đang **ngồi trên xe** và **trực tiếp điều khiển xe** (xe máy, xe đạp, v.v.).
  - **Box `Driver` bao gồm CẢ NGƯỜI LÁI XE VÀ XE:** Vẽ 1 bounding box duy nhất trùm kín toàn bộ người điều khiển cùng toàn bộ phương tiện họ đang cưỡi/lái thành một khối thống nhất.
  - **Không ngồi trên xe -> `Pedestrian`:** Bất kỳ ai không ngồi trên xe (đang đi bộ, chạy, đứng cạnh xe, dắt bộ xe máy/xe đạp) đều bắt buộc gán nhãn là **`Pedestrian`**.
- **Quy tắc quan trọng cho `Car`:**
  - Label toàn bộ chiếc xe, bắt buộc ôm trọn vẹn cả **bánh xe** (tiếp xúc mặt đường) và **gương chiếu hậu** (hai bên xe).

---

## 3. Geometry Rule

- **Loại hình:** Rectangle (hộp chữ nhật song song với trục tọa độ x-y).
- **Độ khít (Tightness):** Hộp phải ôm **khít toàn bộ các pixel nhìn thấy** (visible pixels) của đối tượng:
  - **Đối với `Car`:** Bounding box phải bao phủ **toàn bộ xe, gồm cả bánh xe và gương chiếu hậu**, cản trước, cản sau, giá nóc (nếu có).
  - **Đối với `Driver`:** Bounding box kéo từ đỉnh đầu/mũ bảo hiểm của người lái xuống tới điểm tiếp đất thấp nhất của bánh xe, và từ điểm trước nhất tới điểm sau cùng của người + xe.
  - **Đối với các phương tiện khác (`Truck`, `Bus`, `Pickup`, `Motorcycle` không người lái):** Ôm sát toàn bộ thân xe, bánh xe, gương, thùng hàng.
  - **KHÔNG bao gồm:** Bóng đổ (shadow) của xe trên mặt đường, khói xả, vệt sáng đèn pha rọi ra ngoài.
- **Dung sai (Tolerance):** Độ lệch mép hộp không quá **±2 pixel** so với điểm biên ngoài cùng của vật thể.
- **Không vẽ Amodal:** Chỉ vẽ trên phần nhìn thấy, không tưởng tượng phần bị che khuất ngầm dưới lòng đất hay sau xe khác.

---

## 4. Taxonomy & Attribute Definition

### 4.1 Danh sách Class (Object Classes)

| Class | Định nghĩa & Tiêu chí nhận diện | Yêu cầu Bounding Box đặc biệt | Ví dụ |
|---|---|---|---|
| **`Car`** | Xe con, xe du lịch chở người từ 4–9 chỗ, sedan, hatchback, SUV, crossover, xe taxi, xe cảnh sát con. | **Bắt buộc ôm cả xe gồm bánh xe và 2 gương chiếu hậu.** Không cắt cụt gương xe hay bánh xe. | Toyota Vios, Camry, Honda CR-V, Mazda 3, Kia Morning. |
| **`Driver`** | Người đang **ngồi trên xe và trực tiếp điều khiển xe** (xe máy, xe mô tô, xe đạp). | **Bao gồm CẢ NGƯỜI LÁI XE VÀ XE** trong một bounding box duy nhất. | Người đang ngồi lái xe máy trên đường, shipper đang lái xe. |
| **`Pedestrian`** | Người đi bộ hoặc **bất kỳ ai KHÔNG ngồi trên xe**: đứng, đi, chạy, dắt xe máy, đẩy xe nôi/xe hàng. | Ôm khít cơ thể người (từ đầu đến chân, gồm ba lô/túi xách đang mang). | Người đi bộ, người dắt xe máy hỏng, người đứng chờ đèn đỏ. |
| **`Pickup`** | Xe bán tải: cabin kín phía trước cho hành khách và **thùng chở hàng hở (open cargo bed)** tách biệt phía sau. | Ôm toàn bộ xe gồm bánh xe, gương chiếu hậu và thùng xe. | Ford Ranger, Toyota Hilux, Mitsubishi Triton, Isuzu D-Max. |
| **`Truck`** | Xe tải chở hàng hạng trung và nặng: thùng xe lớn, xe ben, xe bồn, xe đầu kéo container, xe tải chở vật liệu xây dựng. | Ôm toàn bộ đầu kéo và rơ-moóc/thùng hàng kèm theo. | Hyundai Porter, Isuzu Forward, Howo, xe container. |
| **`Bus`** | Xe khách chở nhiều người (thường ≥ 16 chỗ), xe buýt công cộng, xe đưa đón học sinh/công nhân, xe khách liên tỉnh giường nằm. | Ôm toàn bộ thân xe buýt/xe khách, gương chiếu hậu lớn. | Xe buýt nội đô, Hyundai Universe, Ford Transit chở khách (≥ 16 chỗ). |
| **`Motorcycle`** | Xe 2 bánh hoặc 3 bánh gắn động cơ **đang đỗ hoặc không có người ngồi trên xe điều khiển**. | Ôm trọn vẹn thân xe máy đỗ bên đường. | Xe máy dựng ở bãi đỗ, xe máy đỗ vỉa hè không người. |

### 4.2 Thuộc tính (Attributes)

| Attribute | Kiểu | Giá trị | Tiêu chí đánh dấu (`true`) |
|---|---|---|---|
| **`occluded`** | Checkbox | `true` / `false` | Đánh dấu khi đối tượng bị **vật khác che khuất một phần** (bị xe khác che, bị cây xanh, cột đèn, biển báo hoặc người che mất > 10% diện tích). |
| **`truncated`** | Checkbox | `true` / `false` | Đánh dấu khi đối tượng **chạm hoặc vượt ra ngoài mép ảnh** (bị cắt cụt đầu, đuôi, nóc hoặc bánh xe do góc nhìn camera). |

---

## 5. Inclusion / Exclusion Details

1. **Người điều khiển vs Người đi bộ:**
   - Đang ngồi trên yên xe và cầm lái -> **`Driver`** (box bao trùm cả người + xe).
   - Đang dắt bộ xe máy / xe đạp -> Người là **`Pedestrian`**, xe đang dắt là **`Motorcycle`** (tách riêng 2 box).
   - Người đứng cạnh xe, đứng trên vỉa hè -> **`Pedestrian`**.
2. **Xe con (`Car`):**
   - Phải kiểm tra kỹ 2 bên sườn xe: gương chiếu hậu nhô ra ngoài phải nằm trọn trong box.
   - Phải kéo đáy box xuống đúng điểm tiếp xúc giữa lốp xe và mặt đường (không cắt cụt bánh xe, không lấn vào bóng đen).
3. **Xe cõng xe (xe cứu hộ chở ô tô):**
   - Xe cứu hộ bên dưới: Label `Truck`.
   - Ô tô được chở trên sàn xe cứu hộ: Vẫn label `Car` (thuộc tính `occluded: true` vì bánh bị che bởi sàn xe tải).
4. **Vật thể cực nhỏ (< 15px):** Bỏ qua (Ignore) nếu không nhìn rõ kết cấu hoặc loại xe.

---

## 6. Visibility & Occlusion Rule

- **Che khuất 10% – 80%:** Label bình thường và tích chọn `occluded: true`.
- **Che khuất > 80% (Heavy Occlusion):**
  - Nếu vẫn nhận diện chắc chắn loại xe/người: Vẫn label box visible pixel và chọn `occluded: true`.
  - Nếu chỉ thấy một đốm màu mơ hồ, không thể xác định loại đối tượng: **BỎ QUA (Ignore)**.
- **Rìa ảnh (Truncation):** Mép box kéo sát tới pixel cuối cùng của khung ảnh (x=0, y=0 hoặc x=width, y=height), và tích chọn `truncated: true`.

---

## 7. Ambiguity & Escalation Path

| Tình huống mập mờ | Quyết định chuẩn | Lý do |
|---|---|---|
| **Người lái xe máy chở thêm người phía sau** | Box **`Driver`** bao trùm người lái chính và chiếc xe. Người ngồi sau (pillion) có thể vẽ riêng box `Pedestrian` nếu lộ rõ thân người. | Người trực tiếp điều khiển xe gắn liền với xe thành một đơn vị di chuyển `Driver`. |
| **Người vừa ngồi trên xe vừa chống chân dừng đèn đỏ** | **`Driver`** (box ôm cả người và xe) | Vẫn đang trong trạng thái vận hành, điều khiển phương tiện trên đường. |
| **Người xuống xe đứng bên cạnh xe máy** | Người là **`Pedestrian`**, xe máy là **`Motorcycle`** | Người không còn ngồi trên xe điều khiển xe. |
| **Gương chiếu hậu của Car quá mờ hoặc bị gập** | Ôm sát mép gương nhìn thấy được | Bắt buộc bao gồm cả gương chiếu hậu theo quy định xe `Car`. |
| **Pickup có gắn nắp thùng cao ngang nóc xe** | **`Pickup`** | Vẫn giữ kết cấu gầm và rãnh phân cách giữa cabin và thùng chở hàng. |

---

## 8. Temporal Rule

- **Quy tắc thời gian:** **Không áp dụng — task ảnh tĩnh (Static 2D Detection).** Mỗi ảnh được gán nhãn độc lập dựa trên frame hiện tại.

---

## 9. Examples & Reference Cases

| Case | Loại tình huống | Expected Output | Ghi chú & Rule |
|---|---|---|---|
| **Xe ô tô con chạy trên đường** | Normal | Label `Car`, `occluded: false`, `truncated: false` | **Box ôm toàn bộ xe, gồm cả bánh xe chạm đất và 2 gương chiếu hậu.** |
| **Người lái xe máy trên đường** | Driver | Label `Driver`, `occluded: false`, `truncated: false` | **1 box duy nhất ôm trọn vẹn cả người lái xe và chiếc xe máy.** |
| **Người dắt bộ xe máy qua đường** | Pedestrian + Motorcycle | 1 box `Pedestrian` (người) + 1 box `Motorcycle` (xe) | Không ngồi trên xe thì người là `Pedestrian`, xe tách riêng. |
| **Xe bán tải bị xe khác che mất bánh** | Occlusion | Label `Pickup`, `occluded: true`, `truncated: false` | Nhận diện rõ thùng hàng hở phía sau; tích `occluded`. |
| **Xe con bị cắt nửa thân ở rìa trái ảnh** | Truncation | Label `Car`, `occluded: false`, `truncated: true` | Mép trái box chạm x=0; gương hoặc bánh phía trong vẫn phải ôm khít; tích `truncated`. |
| **Bóng xe in trên mặt đường nhựa** | Negative | **Không vẽ box** | Bóng đổ không phải là bộ phận cơ học của xe. |

---

## 10. Common Mistakes & Quality Checklist

Trước khi hoàn thành và xuất file (Save/Export), kiểm tra các lỗi thường gặp sau:
1. ❌ **Cắt cụt gương chiếu hậu hoặc bánh xe của `Car`:** Vẽ box chỉ ôm khung vỏ mà bỏ quên gương 2 bên hoặc mép dưới lốp xe.
2. ❌ **Vẽ tách rời xe máy và người lái:** Người đang điều khiển xe máy thì box `Driver` **phải bao gồm cả người lái xe và xe**.
3. ❌ **Gán nhãn `Driver` cho người dắt xe hoặc người đi bộ:** Không ngồi trên xe điều khiển thì bắt buộc là **`Pedestrian`**.
4. ❌ **Lỗi bóng đổ:** Kéo box lan ra vùng bóng đen dưới gầm xe trên mặt đường.
5. ❌ **Quên tích `truncated`:** Bánh xe, gương hoặc đuôi xe chạm mép ảnh nhưng không tích `truncated: true`.
