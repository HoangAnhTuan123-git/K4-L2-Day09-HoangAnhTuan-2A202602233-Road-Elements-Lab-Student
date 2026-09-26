# Nếu scale lên 100k frames

Họ tên: Hoàng Anh Tuấn

Mỗi mini-task trả lời một câu: **"Nếu scale lên 100k frames, lỗi nào sẽ trở thành systematic defect?"** Viết ngay sau
khi ghi comparison log của task đó. Dựa vào một lỗi bạn **thật sự** gặp hôm nay. Đã hoàn thiện toàn bộ nội dung.

Mỗi câu trả lời có 3 phần: lỗi (và bằng chứng: task + sample), vì sao nó lặp lại có hệ thống thay vì ngẫu nhiên,
và cách phát hiện sớm (lát nào cần oversample, tín hiệu QC nào).

## Lane

- **Lỗi:** Polyline bị đứt đoạn vô lý hoặc vẽ ngắt quãng khi vạch kẻ đường bị bóng râm của cây cối / tòa nhà che khuất (bằng chứng: task `lane`, sample core `bb890202` ảnh 2 và 4).
- **Vì sao lặp lại có hệ thống:** Trên quy mô 100k frames, tỷ lệ cung đường có cây xanh hoặc bóng nhà cao tầng đổ bóng chiếm tới 30–40%. Hiện tượng bóng đổ tương phản mạnh làm đứt gãy thị giác khiến annotator phân vân giữa "vạch bị mờ tạm thời" và "vạch thực sự kết thúc", dẫn tới việc hàng loạt annotator tự ý cắt ngắn polyline.
- **Cách phát hiện sớm:** Oversample lát cắt `weather=clear + timeofday=daytime + high_contrast_shadows`. Viết script QC hình học đo mật độ endpoint của polyline: nếu có quá nhiều điểm kết thúc lane rơi vào khu vực giữa đường thẳng, tự động gắn cờ yêu cầu review.

## Drivable area

- **Lỗi:** Polygon vùng di chuyển được (`drivable_area`) lấn lên bề mặt vỉa hè hoặc mép bồn cây khi vật liệu lát vỉa hè có màu xám bê tông tương đồng với lòng đường (bằng chứng: task `drivable`, sample core ảnh 3 và 7).
- **Vì sao lặp lại có hệ thống:** Ở các khu đô thị mới hoặc lối rẽ, mép bó vỉa (curb) hạ thấp ngang mặt đường và cùng chất liệu bê tông/nhựa đường. Khi annotator làm việc hàng giờ với tốc độ cao, mắt bị mỏi sẽ không phân biệt được rãnh phân cách nhỏ 2–3px mà vẽ bao trùm luôn cả mép vỉa hè đi bộ.
- **Cách phát hiện sớm:** Oversample lát cắt `scene=urban + surface=concrete + dusk`. Sử dụng thuật toán edge-detection (Canny/Sobel) hoặc mô hình phụ trợ trích xuất đường viền bó vỉa để đối chiếu sai khác với polygon drivable.

## Traffic sign

- **Lỗi:** Gán sai `sign_family` và `sign_class` cho các biển báo ở xa hoặc biển bị cành cây/biển quảng cáo che khuất một phần (bằng chứng: task `traffic_sign`, sample core `00073.png`).
- **Vì sao lặp lại có hệ thống:** Tại Việt Nam và nhiều nước, cây xanh che khuất biển báo xảy ra liên tục trên đường phố. Khi kích thước biển < 20px hoặc bị che > 40%, annotator có xu hướng "đoán mò" ngữ cảnh (ví dụ thấy gần trường học thì tự đoán là biển hạn chế tốc độ) thay vì tuân thủ quy tắc gán `unknown`.
- **Cách phát hiện sớm:** Oversample lát cắt `vegetation_occlusion + small_scale (<25px)`. Chạy rule kiểm tra tự động: mọi biển có độ phân giải thấp hoặc gắn cờ `occluded=true` bắt buộc phải có thuộc tính `readable=false` nếu class được chọn là các biển hiếm.

## Traffic light

- **Lỗi:** Đứt gãy track ID và lệch frame chuyển trạng thái (`state`) do hiện tượng tần số quét đèn LED (flickering) gây ra các frame tối cục bộ (bằng chứng: task `traffic_light`, clip `dayClip5` frame 18-20).
- **Vì sao lặp lại có hệ thống:** Camera hành trình có tốc độ màn trập (shutter speed) cao có thể chụp đúng khoảnh khắc bóng đèn LED đang tắt trong chu kỳ dao động điện xoay chiều. Trên 100k frames, sẽ có hàng ngàn frame đèn trông như bị "tắt" (off) dù thực tế đèn giao thông vẫn đang sáng liên tục. Annotator thiếu kinh nghiệm sẽ tách track hoặc đổi state thành `off` trong đúng 1 frame.
- **Cách phát hiện sớm:** Thiết lập luật kiểm tra máy trạng thái hữu hạn (FSM validator) trong pipeline QC: cấm các bước nhảy trạng thái ngắn 1 frame kiểu `green -> off -> green` hoặc `green -> red` không qua `yellow`, tự động cảnh báo cho kiểm duyệt viên.
