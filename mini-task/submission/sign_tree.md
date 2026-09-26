# Traffic sign tree

Họ tên: Hoàng Anh Tuấn · Chế độ: cá nhân · Nếu nhóm — các thành viên: Không có (chế độ cá nhân)

Viết sau mini-task traffic sign (phút ~155). Dựa vào những biển **bạn đã vẽ** trong 7 ảnh core, không chép danh sách
43 class. Đã hoàn thiện toàn bộ nội dung.

## 1. Cây của bạn

Chỉ liệt kê class có trong ảnh core. Đếm số box của từng class. Class có 1 box trong cả batch là ứng viên "hiếm".

| family | class (`sign_class`) | Số box trong core | Phổ biến / hiếm | Ảnh ví dụ |
|---|---|---|---|---|
| prohibitory | 02 speed limit 50 | 3 | Phổ biến | 00026.png |
| prohibitory | 01 speed limit 30 | 1 | Hiếm | 00088.png |
| mandatory | 38 keep right | 2 | Phổ biến | 00054.png |
| danger | 18 danger | 2 | Phổ biến | 00073.png |
| danger | 25 construction | 1 | Hiếm | 00108.png |
| other | 12 priority road | 3 | Phổ biến | 00206.png |
| other | 32 restriction ends | 1 | Hiếm | 00223.png |

## 2. Hai quyết định merge/split

Mỗi quyết định: giữ tách hay gộp, vì sao, và cái giá nếu chọn sai (model downstream nhầm gì).

**Quyết định 1 — `09 no overtaking` và `10 no overtaking (trucks)`: tách hay gộp?**
Giữ tách biệt hoàn toàn 2 class này. Lý do: Đối tượng áp dụng biển cấm vượt xe tải (`10 no overtaking (trucks)`) chỉ hướng tới phương tiện tải trọng lớn (trucks), xe con (ego vehicle) vẫn được phép vượt bình thường. Nếu gộp chung thành một class `no_overtaking`, hệ thống ADAS của xe con sẽ hiểu nhầm lệnh cấm và tự động hãm phanh / không cho phép chuyển làn vượt xe phía trước, gây ức chế và ùn tắc giao thông vô lý.

**Quyết định 2 — nhóm `other` của GTSDB khi dùng ở Việt Nam.**
GTSDB xếp biển hết hạn chế (`06`, `32`, `41`, `42`) vào `other`. QCVN 41:2024/BGTVT xếp biển hết hiệu lực (`DP.133`–`DP.135`) vào nhóm **biển báo cấm**. Cây của bạn theo cách nào, và cần rule gì để hai người label giống nhau?
Cây của tôi giữ nguyên phân loại theo GTSDB quốc tế: xếp biển hết hạn chế vào nhóm `other` để tương thích hoàn toàn với ontology và pre-trained weights ban đầu. Để hai annotator label nhất quán, thiết lập rule: "Mọi biển hình tròn nền trắng có vạch chéo xám hủy bỏ hiệu lực đều bắt buộc gán family là `other`, không tự ý chuyển sang `prohibitory` theo thói quen QCVN".

## 3. Chính sách cho class hiếm và biển không đọc được

Khi gặp biển không có trong 43 class, hoặc quá nhỏ để đọc: bạn chọn `sign_family`, `sign_class`, `readable` thế nào?
Dẫn một box cụ thể (ảnh + vị trí) làm bằng chứng.
- Nếu nhìn rõ hình dạng hình học (tròn viền đỏ, tam giác viền đỏ) nhưng biểu tượng bên trong bị nhòe/quá nhỏ (< 15px): chọn đúng `sign_family` (ví dụ `prohibitory`), gán `sign_class = __undefined__` (hoặc `unknown`), và đặt thuộc tính `readable = false`.
- Nếu hoàn toàn không thể nhận dạng hình học do góc nghiêng gắt hoặc bị che khuất > 80%: gán `sign_family = unknown`, `sign_class = __undefined__`, `readable = false`.
- Bằng chứng cụ thể: Biển báo ở hậu cảnh xa trong ảnh `00073.png` tại tọa độ góc trên bên phải (x ≈ 1120, y ≈ 310) có kích thước xấp xỉ 12×12px, chỉ nhận biết được đốm tròn viền đỏ, gán `sign_family = prohibitory`, `sign_class = __undefined__`, `readable = false`.

## 4. Dòng decision log tương ứng

Id của dòng trong `decision_log.csv` ghi rule ở mục 2 hoặc 3: `DL-SIGN-01` và `DL-SIGN-02`.
