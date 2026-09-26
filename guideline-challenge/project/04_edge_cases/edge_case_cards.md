# Edge-case library

---

CASE ID: EC-01
Sample: BDD10
Scene: City street daytime
Observation: Người đang ngồi trên yên xe máy di chuyển trên đường phố
Decision: LABEL
Expected: 1 box nhãn Driver chỉ ôm khít người lái xe máy từ đầu tới chân, occluded=false, truncated=false
Rationale: Downstream ADAS tập trung phát hiện chính xác thực thể con người điều khiển tham gia giao thông
Common mistake: Kéo box trùm cả xe máy hoặc tách rời thành Pedestrian
Diversity: ambiguity

---

CASE ID: EC-02
Sample: BDD12
Scene: City intersection
Observation: Ô tô con dừng đèn đỏ bị xe phía trước che khuất một phần cản xe
Decision: LABEL
Expected: Nhãn Car, occluded=true, truncated=false, box ôm khít bánh xe và gương nhìn thấy
Rationale: Bắt buộc gắn cờ occluded để mô hình học cách nhận biết vật thể bị che
Common mistake: Bỏ quên checkbox occluded khi xe chỉ bị che mất 20%
Diversity: occlusion

---

CASE ID: EC-03
Sample: BDD15
Scene: City sidewalk
Observation: Người dắt bộ xe máy trên vỉa hè
Decision: LABEL
Expected: Tách thành 2 box: 1 box Pedestrian cho người dắt và 1 box Motorcycle cho xe máy
Rationale: Người không ngồi trên xe điều khiển thì động học di chuyển là người đi bộ
Common mistake: Nhầm người dắt xe thành Driver
Diversity: ambiguity

---

CASE ID: EC-04
Sample: BDD17
Scene: Rainy city street
Observation: Ô tô con di chuyển trên mặt đường ướt tạo bóng phản chiếu lớn
Decision: LABEL
Expected: Nhãn Car, box đáy dừng lại ở điểm tiếp xúc lốp xe, không bao gồm vệt bóng nước
Rationale: Kéo box lan ra vệt bóng nước làm sai lệch kích thước 3D thực tế của xe
Common mistake: Kéo mép dưới hộp chữ nhật xuống hết vệt bóng nước
Diversity: conflict

---

CASE ID: EC-05
Sample: BDD18
Scene: Night city street
Observation: Xe ô tô ngược chiều rọi đèn pha chói lóa trong đêm tối
Decision: LABEL
Expected: Nhãn Car, box ôm khít thân xe trong bóng tối, severity critical
Rationale: Xe ban đêm có nguy cơ đâm va cực cao nếu bộ lọc kích thước bị lóa sáng đánh lừa
Common mistake: Chỉ vẽ vùng sáng đèn pha mà bỏ sót toàn bộ thân xe và bánh xe
Diversity: critical

---

CASE ID: EC-06
Sample: BDD24
Scene: Snowy road
Observation: Xe ô tô chạy sát mép trái khung hình bị cắt một phần thân
Decision: LABEL
Expected: Nhãn Car, truncated=true, mép trái box chạm sát x=0
Rationale: Đánh dấu truncated giúp thuật toán nhận biết vật thể chưa vào hết khung nhìn
Common mistake: Quên bật checkbox truncated
Diversity: truncation

---

CASE ID: EC-07
Sample: BDD26
Scene: Night dark street
Observation: Đốm mờ trong bóng tối ở rất xa không rõ là cọc tiêu hay người
Decision: ESCALATE
Expected: Đánh dấu ESCALATE / needs_review nếu < 8px không nhận diện được hình thái
Rationale: Tránh đưa dữ liệu nhiễu vào tập huấn luyện khi con người không thể chắc chắn
Common mistake: Tự đoán mò nhãn khi bằng chứng thị giác không đủ
Diversity: escalation

---

CASE ID: EC-08
Sample: BDD11
Scene: Highway daytime
Observation: Xe tải chở hàng thùng kín loại nhỏ chạy cạnh xe bán tải
Decision: LABEL
Expected: Nhãn Truck (cho xe tải thùng) và Pickup (cho xe có thùng hở)
Rationale: Phân loại đúng công năng và tải trọng phương tiện cho bài toán giao thông
Common mistake: Nhầm lẫn xe tải nhỏ thành xe con
Diversity: ambiguity

---

CASE ID: EC-09
Sample: image.png
Scene: Rainy city street accident scene
Observation: Hiện trường xe máy ngã nằm ngang trên mặt đường ướt, người ngồi bệt cạnh xe và người cúi nâng xe
Decision: LABEL
Expected: Xe ngã nhãn Motorcycle (ôm thân xe nằm ngang, cắt bỏ bóng phản chiếu đèn trên mặt nước); Người ngồi bệt nhãn Pedestrian; Người cúi nâng xe nhãn Pedestrian; Người đi xe máy qua nhãn Driver (chỉ ôm người)
Rationale: Phân định rạch ròi trạng thái tĩnh của xe bị ngã với con người không điều khiển xe và người đang lái xe
Common mistake: Kéo box xe máy trùm bóng nước hoặc nhầm người ngồi bệt thành Driver
Diversity: conflict

---
