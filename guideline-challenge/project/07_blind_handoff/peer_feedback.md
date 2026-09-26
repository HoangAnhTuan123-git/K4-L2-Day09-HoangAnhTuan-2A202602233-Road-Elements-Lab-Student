# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Thay mọi placeholder mới
là xong (gate G5).

- **Nhóm peer:** Nhóm 4 (Hệ thống ADAS Giám sát Đô thị)
- **Người label blind:** Lê Văn Cường (Annotator Lead Nhóm 4)

## 1. Peer trả lời

1. Rule nào rõ nhất / giúp quyết định nhanh nhất? 
   Quy tắc `Driver` mới (chỉ bao bọc người điều khiển từ đỉnh đầu/mũ bảo hiểm đến bàn chân, không bao gồm thân xe máy/xe đạp) rất dứt khoát, dễ thao tác và không còn bị lưỡng lự xem có lấy bánh xe hay không như quy tắc cũ. Quy tắc loại trừ bóng nước phản chiếu trên mặt đường ở mẫu BDD17 cũng rất rõ ràng.

2. Rule nào mơ hồ hoặc phải tự suy diễn? 
   Quy tắc xác định lốp xe trong bóng tối (BDD18) khi ánh đèn pha xe đối diện rọi ngược gây lóa: ranh giới tiếp giáp giữa đáy lốp xe và mặt đường khó thấy rõ, phải ước lượng dựa trên vệt đèn phản chiếu trên vạch kẻ đường.

3. Sample nào khiến guideline "vỡ"? 
   Sample BDD26 (ban đêm phương tiện bị che khuất một phần trong bóng tối): ban đầu phân vân không biết người lái xe máy có cần đánh dấu thêm xe máy bên dưới hay không, nhưng đọc kỹ guideline mục 2 thấy ghi rõ tách biệt nên đã tuân thủ đúng. Ngoài ra tình huống gặp tai nạn xe ngã đổ cần có rule rõ hơn trong văn bản chính.

4. Attribute / default nào trong CVAT dễ gây thao tác sai? 
   Thuộc tính `occluded` khi mặc định là `false`: khi vẽ các xe bị che khuất liên tiếp trong đoàn xe đông đúc, annotator rất dễ quên tick chọn `occluded=true` nếu không có checklist kiểm tra tự động trước khi submit.

5. Một thay đổi cụ thể giúp annotator mới ít hỏi hơn? 
   Thêm hình ảnh minh họa thực tế cho trường hợp ban đêm có đèn rọi ngược (chỉ rõ đường viền đáy lốp xe) và tình huống xe máy bị ngã/tai nạn giao thông ngay trong bản guideline chính.

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng |
|---|---|---|---|
| Ranh giới đáy bánh xe trong bóng tối lóa đèn pha (BDD18) | Guideline gap (chưa có hướng dẫn mẹo nhận biết qua vệt sáng gầm xe) | Accept + revise: Bổ sung mẹo căn chỉnh tiếp xúc mặt đường bằng đèn phản quang và gầm xe vào mục 3 của Guideline v3 | BDD18 d2; Feedback câu 2 của Peer |
| Tình huống tai nạn xe máy ngã đổ trên đường | Guideline gap (chưa bao hàm trạng thái xe ngã đổ không người lái) | Accept + revise: Thêm quy tắc và case card EC-09 vào Guideline v3 | Clarification log câu 1; EC-09 image.png |
| Quên tick `occluded=true` trong cảnh đông đúc | Execution error (giao diện CVAT mặc định false) | Add escalation rule: Bổ sung checklist QA bắt buộc lọc thuộc tính trước khi submit task | Feedback câu 4 của Peer |
