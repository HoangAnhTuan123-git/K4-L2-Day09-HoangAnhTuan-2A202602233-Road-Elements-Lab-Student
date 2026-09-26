# Kịch Bản Thuyết Trình — Road Elements Annotation Guideline (Team 09)
*Tài liệu hỗ trợ thuyết trình đi kèm slide `guideline_presentation.html`*
*Thời lượng dự kiến: 5 – 7 phút*

---

## 🎙️ LƯU Ý CHUNG TRƯỚC KHI BẮT ĐẦU:
- Bấm **`F`** trên bàn phím để bật chế độ Toàn màn hình (Fullscreen).
- Bấm **`→`** hoặc **`Space`** để chuyển slide tiếp theo.
- Bấm **`←`** để lùi lại slide trước.
- Bấm **`O`** nếu cần mở menu tổng quan để nhảy nhanh tới slide bất kỳ.

---

### 🌟 SLIDE 1: MỞ ĐẦU & GIỚI THIỆU TỔNG QUAN
⏱️ *Thời lượng: ~30 giây*
👉 *Hành động: Đứng trước màn hình, chào thầy cô và các bạn, phong thái tự tin.*

> **Lời thoại thuyết trình:**
> 
> "Kính chào thầy và toàn thể các bạn! 
> 
> Hôm nay, đại diện cho **Nhóm 09**, em xin phép trình bày bộ **Quy chuẩn gán nhãn 2D Bounding Box cho Phương tiện và Người tham gia giao thông** phục vụ bài toán xe tự hành và hệ thống cảnh báo va chạm thông minh.
> 
> Bộ guideline này hiện đã được nhóm em chuẩn hóa lên **Version v2**, tích hợp đầy đủ 7 class đối tượng, 2 thuộc tính quan sát, và đặc biệt là đã vượt qua các vòng kiểm thử hiệu chuẩn nội bộ cũng như xử lý triệt để các tình huống biên (Edge Cases) thường gặp tại giao thông Việt Nam.
> 
> Ngay sau đây, em xin đi vào chi tiết cấu trúc danh mục gán nhãn."

---

### 📋 SLIDE 2: BẢNG PHÂN LOẠI (7 CLASSES & 2 ATTRIBUTES)
⏱️ *Thời lượng: ~45 giây*
👉 *Hành động: Bấm [→] chuyển slide. Trỏ tay vào bảng phân loại.*

> **Lời thoại thuyết trình:**
> 
> "Thưa các bạn, trên slide là hệ thống Taxonomy chuẩn gồm **7 Object Classes** và **2 Attributes** mà nhóm đã thiết lập trong schema CVAT:
> 
> - Thứ nhất, về phương tiện cơ giới 4 bánh trở lên gồm có: **`Car`** (xe con chở người từ 4 đến 9 chỗ), cùng các xe chuyên dụng như **`Bus`** (xe buýt), **`Truck`** (xe tải) và **`Pickup`** (xe bán tải thùng hở).
> - Thứ hai, về con người và xe 2 bánh: Chúng em tách bạch thành **`Driver`** (người điều khiển xe máy hoặc xe đạp), **`Pedestrian`** (người đi bộ hoặc không ngồi trên xe), và **`Motorcycle`** (áp dụng cho xe máy đang đỗ tĩnh, không có người lái).
> - Cuối cùng là hai thuộc tính quan sát bắt buộc: **`occluded`** (đối tượng bị che khuất trên 10%) và **`truncated`** (đối tượng bị cắt cụt ở rìa ảnh).
> 
> Sau đây là điểm cải tiến quan trọng nhất trong Version v2 của nhóm em."

---

### ⭐ SLIDE 3: ĐIỂM QUAN TRỌNG NHẤT — QUY TẮC MỚI CHO `DRIVER`
⏱️ *Thời lượng: ~60 giây*
👉 *Hành động: Bấm [→] chuyển slide. Nhấn giọng mạnh, chỉ vào hình mô phỏng màu xanh lá bên trái.*

> **Lời thoại thuyết trình:**
> 
> "Đây là quy tắc trọng tâm mà cả lớp cần đặc biệt lưu ý khi thực hiện gán nhãn hoặc đánh giá chéo:
> 
> **Quy tắc mới cho `Driver`: Chúng ta CHỈ ĐÁNH DẤU NGƯỜI LÁI, KHÔNG CẦN ĐÁNH DẤU PHƯƠNG TIỆN.**
> 
> - **Cách làm chuẩn (bên trái):** Bounding box chỉ ôm khít cơ thể người lái xe máy hoặc xe đạp, kéo **từ đầu người tới chân**. Chiếc xe máy họ đang điều khiển bên dưới chúng ta hoàn toàn bỏ qua, không vẽ box.
> - **Lỗi thường gặp cần tránh (bên phải):** Trong phiên bản cũ, nhiều bạn thường kéo một box khổng lồ trùm kín cả người lẫn chiếc xe xuống tận mặt đất, hoặc vẽ thêm nhãn `Motorcycle` đè lên xe đang chạy. Điều này là **SAI** theo chuẩn v2, vì nó làm sai lệch tỷ lệ khung hình của con người và gây nhiễu cho mô hình phát hiện người dễ bị tổn thương trên đường.
> 
> Tóm lại: Thấy người đang lái xe 2 bánh -> Chỉ vẽ 1 box quanh người đó từ đầu tới chân và đặt nhãn là `Driver`."

---

### 🔀 SLIDE 4: PHÂN ĐỊNH DRIVER vs PEDESTRIAN vs MOTORCYCLE
⏱️ *Thời lượng: ~45 giây*
👉 *Hành động: Bấm [→] chuyển slide. Lần lượt quét qua 3 cột phân loại.*

> **Lời thoại thuyết trình:**
> 
> "Để không bị nhầm lẫn trong các cảnh giao thông phức hợp, nhóm em đưa ra ma trận quyết định 3 trường hợp rõ ràng:
> 
> 1. **Trường hợp 1:** Người đang **ngồi trên xe và trực tiếp điều khiển** xe máy/xe đạp -> Gán nhãn **`Driver`** (chỉ ôm người lái).
> 2. **Trường hợp 2:** Bất kỳ ai **không ngồi trên xe** -> Bắt buộc là **`Pedestrian`**. Ví dụ người đi bộ, đứng đợi bên đường, và đặc biệt là người **đang dắt bộ xe máy/xe đạp**. Nếu họ dắt xe, người là `Pedestrian`, còn chiếc xe được dắt bên cạnh gán riêng là `Motorcycle`.
> 3. **Trường hợp 3:** Xe máy đang dựng chân chống đỗ bên đường hoặc vỉa hè không có người -> Gán nhãn **`Motorcycle`**.
> 
> Ngoài ra, nếu xe máy chở 2 hoặc 3 người: người cầm lái phía trước là `Driver`, còn người ngồi sau nếu nhìn rõ sẽ gán nhãn riêng là `Pedestrian`."

---

### 📐 SLIDE 5: QUY CHUẨN HÌNH HỌC CHO XE Ô TÔ (`CAR`)
⏱️ *Thời lượng: ~45 giây*
👉 *Hành động: Bấm [→] chuyển slide. Nhấn mạnh vào 2 yếu tố bánh xe và gương.*

> **Lời thoại thuyết trình:**
> 
> "Tiếp theo là quy chuẩn về độ khít hình học cho xe con (`Car`):
> 
> - **Hai yêu cầu bắt buộc phải ôm trọn:**
>   - Thứ nhất: **Bánh xe tiếp xúc mặt đường**. Mép dưới cùng của box phải kéo chạm điểm tiếp đất của lốp xe, tuyệt đối không cắt cụt gầm xe hay lốp xe.
>   - Thứ hai: **Hai gương chiếu hậu**. Phải mở rộng box bao phủ trọn vẹn cả 2 tai gương chìa ra hai bên thân xe.
> - **Những thứ tuyệt đối KHÔNG vẽ vào:** Đó là **bóng đổ** của xe trên mặt đường nhựa, **bóng phản chiếu** trên vũng nước mưa, và vệt đèn pha rọi sáng trong đêm.
> - Dung sai cho phép không được lệch quá **±2 pixel** so với đường biên thực tế nhìn thấy."

---

### 🏷️ SLIDE 6: BỘ THUỘC TÍNH `OCCLUDED` & `TRUNCATED`
⏱️ *Thời lượng: ~40 giây*
👉 *Hành động: Bấm [→] chuyển slide.*

> **Lời thoại thuyết trình:**
> 
> "Về 2 thuộc tính quan sát:
> 
> - Thuộc tính **`occluded` (Bị che khuất):** Tích chọn `true` khi đối tượng bị xe khác, người đi bộ, cây cối hoặc biển báo che khuất từ **10% diện tích trở lên**. Tuy nhiên, nếu bị che khuất quá 80% mà mắt thường không còn nhận ra loại đối tượng thì chúng ta sẽ bỏ qua (Ignore).
> - Thuộc tính **`truncated` (Bị cắt cụt ở rìa ảnh):** Tích chọn `true` khi đối tượng chạm hoặc vượt ra ngoài 4 cạnh biên của bức ảnh.
> 
> Lưu ý với các bạn: Cả hai thuộc tính này mặc định trên CVAT là `false`, nên chúng ta phải luôn chủ động kiểm tra mép ảnh và các vị trí chen chúc để bật thuộc tính này."

---

### 🚦 SLIDE 7: XỬ LÝ CHÙM XE MÁY ĐÔNG ĐÚC & NGÃ TƯ
⏱️ *Thời lượng: ~45 giây*
👉 *Hành động: Bấm [→] chuyển slide. Nói về đặc thù đường phố Việt Nam.*

> **Lời thoại thuyết trình:**
> 
> "Một trong những thách thức lớn nhất tại giao thông đô thị Việt Nam là các chùm xe máy ken đặc tại ngã tư dừng đèn đỏ:
> 
> - **Nguyên tắc 1:** Phải vẽ từng box `Driver` riêng biệt cho từng người điều khiển. Tuyệt đối không vẽ một box gộp chung cả đám đông.
> - **Nguyên tắc 2:** Việc các bounding box của người lái xe máy đè lên nhau (overlapping) trong đám đông là hoàn toàn bình thường và được chấp nhận.
> - **Nguyên tắc 3:** Người lái xe đi sau bị người phía trước che khuất thân người thì bắt buộc phải tích chọn thuộc tính `occluded: true`.
> - Riêng với xe buýt to lớn đi giữa dòng xe máy: Vẫn vẽ box `Bus` trùm từ nóc xuống mép cản thấp nhất nhìn thấy, và bật `occluded: true` vì phần gầm đã bị dòng xe máy che lấp."

---

### 🔍 SLIDE 8: VẬT THỂ NHỎ Ở XA & ĐIỀU KIỆN ÁNH SÁNG XẤU
⏱️ *Thời lượng: ~40 giây*
👉 *Hành động: Bấm [→] chuyển slide.*

> **Lời thoại thuyết trình:**
> 
> "Về giới hạn kích thước nhận dạng:
> 
> - Ngưỡng tối thiểu nhóm em quy định là **chiều rộng hoặc chiều cao ≥ 8 pixel**. Miễn là mắt người vẫn nhận diện được hình bóng chiếc xe hoặc người lái xe ở xa ngã tư thì **bắt buộc phải gán nhãn**, không được tự ý bỏ qua.
> - Để không bỏ sót, chúng ta nên tận dụng tính năng zoom 400% đến 600% trên CVAT tại các khu vực đường chân trời.
> - Khi gặp điều kiện thời tiết xấu như trời mưa, ban đêm có đèn pha chói lóa, hay đường phủ tuyết: Annotator chỉ vẽ bám theo cấu kiện cơ học của thân xe, loại bỏ hoàn toàn các vệt chói lóa và bóng phản chiếu."

---

### ⚠️ SLIDE 9: 5 LỖI GÁN NHÃN PHỔ BIẾN CẦN TRÁNH
⏱️ *Thời lượng: ~45 giây*
👉 *Hành động: Bấm [→] chuyển slide. Giơ tay đếm ngón hoặc chỉ vào các ô màu đỏ.*

> **Lời thoại thuyết trình:**
> 
> "Trước khi bấm lưu và nộp bài, nhóm em đã xây dựng một checklist gồm 5 lỗi phổ biến nhất cần kiểm tra lại:
> 
> 1. ❌ Cắt cụt gương chiếu hậu hoặc bánh xe của `Car`.
> 2. ❌ Vẽ box `Driver` trùm cả xe máy (nhắc lại: chỉ ôm từ đầu tới chân người lái).
> 3. ❌ Quên tích chọn checkbox `occluded` khi các xe chen chúc nhau.
> 4. ❌ Quên tích chọn checkbox `truncated` khi xe chạm sát rìa khung ảnh.
> 5. ❌ Bỏ sót các phương tiện nhỏ ở xa ngã tư có kích thước từ 8 đến 15 pixel.
> 
> Tránh được 5 lỗi này, tập dữ liệu của chúng ta sẽ đạt độ chuẩn xác rất cao trong các vòng chấm điểm QA."

---

### 🎯 SLIDE 10: QUY TRÌNH QA & HỎI ĐÁP (Q&A)
⏱️ *Thời lượng: ~45 giây*
👉 *Hành động: Bấm [→] chuyển slide. Hướng mắt về phía thầy cô và các bạn trong lớp.*

> **Lời thoại thuyết trình:**
> 
> "Để đảm bảo chất lượng chuyển giao sang nhóm phản biện (Peer Team), Team 09 đã thiết lập quy trình kiểm thử 4 lớp:
> 
> 1. **Self-Check:** Mỗi thành viên tự rà soát theo danh sách 5 lỗi trên.
> 2. **Peer Review:** Kiểm tra chéo nội bộ ngẫu nhiên 20% số lượng ảnh.
> 3. **Gold Freeze:** Đã hoàn thành đóng băng bộ 11 quyết định vàng cho tập Blind Pack để làm căn cứ chấm điểm đối chiếu.
> 4. **Handoff:** Sẵn sàng chuyển giao gói dữ liệu cho nhóm bạn kiểm thử độc lập.
> 
> Trên đây là toàn bộ phần trình bày của Nhóm 09 về Quy chuẩn gán nhãn Road Elements. Em xin chân thành cảm ơn thầy và các bạn đã chú ý lắng nghe! 
> 
> Rất mong nhận được những câu hỏi và đóng góp ý kiến từ thầy và các bạn trong lớp!"

---

## 💡 BÍ QUYẾT TRẢ LỜI CÂU HỎI THƯỜNG GẶP (Q&A CHEATSHEET):

1. **Hỏi: Tại sao lại bỏ nhãn chiếc xe máy khi đang có người lái xe mà chỉ gán `Driver` quanh người?**
   - *Trả lời:* "Vì trong bài toán bảo vệ người tham gia giao thông yếu thế (Vulnerable Road Users - VRU), con người là thực thể sinh học cần định vị chính xác vị trí trọng tâm và độ cao cơ thể để tính toán phản xạ phanh AEB. Việc tách riêng người lái từ đầu tới chân giúp mô hình phát hiện người không bị phân tâm bởi hình thù đa dạng của các loại xe máy khác nhau."

2. **Hỏi: Nếu xe máy chở 2 người thì gán thế nào?**
   - *Trả lời:* "Người ngồi trước cầm lái là `Driver` (ôm từ đầu tới chân người lái). Người ngồi sau nếu nhìn thấy rõ thân hình thì gán nhãn riêng là `Pedestrian`."

3. **Hỏi: Kích thước 8 pixel có quá nhỏ không?**
   - *Trả lời:* "Với camera 1080p góc rộng của xe tự hành, các phương tiện cách xa từ 50m đến 80m thường chỉ có độ phân giải từ 8 đến 15 pixel. Nếu bỏ qua các vật thể này, xe tự hành sẽ không kịp lập quỹ đạo di chuyển từ xa ở tốc độ cao, do đó bắt buộc phải gán nhãn nếu mắt thường vẫn nhận diện được hình thái."
