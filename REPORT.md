# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** NGUYỄN CÔNG KHẢI<br>
**MSSV:** 2A202602243<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33
- Bốn mã ảnh: drive_008, drive_022, drive_033, drive_038
- Số vật thể thực tế: 105
- Mã SHA-256 của gói YOLO của bạn: 60ee35d689b0d78460b4edffa2e364e0747850715a2d850cd1dfbbadea1294f1
- Mã SHA-256 của gói CVAT gốc của bạn: cd82865999c2a3a62db5880d1202bb3ce1fe93713a1e5e5f1bc360635eed2beb
- Nguồn đối chiếu: bộ nhãn đối chiếu do Lab Coach cấp
- Mã SHA-256 của gói đối chiếu: c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:
  - Thời điểm nhận: 2026-09-14 12:00

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: Bài của tôi được hoàn thành độc lập trước khi nhận bộ tham chiếu. Tôi đã gán nhãn 4 ảnh, xuất gói YOLO và CVAT 1.1, tự kiểm tra và ghi báo cáo trước khi so sánh với nguồn đối chiếu.

## 2. Quyết định phân lớp

| Ảnh/vật thể      | Lớp | Dấu hiệu nhìn thấy                                                                                                                                                                    | Quy tắc áp dụng                                                                                    |
| ---------------- | --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| drive_008.jpg#30 | car | Thân xe sedan 4 cửa màu trắng ở tiền cảnh gần camera nhất; thấy rõ cấu trúc 3 khoang riêng biệt (nắp capo, khoang hành khách có cửa số trời, cốp sau); cụm đèn hậu và biển số rõ nét. | Các phương tiện chở người cá nhân kết cấu unibody từ 4-5 chỗ ngồi đều được phân loại vào nhãn car. |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

- Lớp (Class/Label): Trả lời cho câu hỏi "Vật thể này là gì?" - mang tính chất bản thể cố định không đổi.
- Thuộc tính (Attribute): Trả lời cho câu hỏi "Trạng thái của vật thể này ra sao trong bối cảnh cụ thể này?" - mang tính ngữ cảnh và có thể thay đổi tùy frame.
- Ví dụ: Một chiếc sedan luôn luôn có lớp là car nhưng tùy frame có thể có visibility = `clear` nếu nhìn thấy rõ hoặc `occluded` nếu bị che khuất.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa                                                  | Loại lỗi                | Cách phát hiện                                                                                                                                                                                                                         | Sau khi sửa và quy tắc                                                                                                                                                         |
| -------------------------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| drive_008.jpg#1 (xe sedan màu đen ngay phía trên xe trắng #30) | thuộc tính (visibility) | Dùng bộ lọc filter để tìm các đối tượng có thuộc tính `unclear`, sau đó phóng to 100% để kiểm tra trực quan trên ảnh. Nhận thấy toàn bộ thân xe, bánh xe, kính lái và đèn hậu của xe đều hiện rõ, không bị che khuất, không bị mờ nét. | Sửa visibility = `clear`; Quy tắc: Khi đối tượng ở cự ly gần/trung cảnh, cấu trúc viền sắc nét, nhận diện rõ các bộ phận đặc trưng thì bắt buộc phải gán visibility = `clear`. |

- Số hộp `needs_review` trước và sau khi kiểm:
  - Trước khi kiểm: 2 box (chỉ tính ở frame drive_008.jpg).
  - Sau khi kiểm: 1 box.
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ:
  - Vật thể được chọn: drive_008.jpg#4 (nằm ở góc dưới cùng bên phải ảnh).
  - Tình trạng thiếu bằng chứng: Phương tiện chỉ lộ ra một góc rất nhỏ ở đầu xe sát hai cạnh biên của ảnh, diện tích nhìn thấy bé hơn 15% tổng thể xe nên chưa đủ căn cứ khẳng định chắc chắn là car.
  - Cách xin hỗ trợ:
    - Giữ nguyên giá trị visibility = `unclear`, boundary = `truncated` và đặt cờ review_state=`needs_review`.
    - Ghi chú mã ID đối tượng kèm ảnh chụp phóng to gửi lên trao đổi nội bộ của dự án để PM hoặc QA Lead đối chiếu lại với video gốc và đưa ra quyết định cuối cùng.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: 0 0.265422 0.509367 0.099406 0.071109
- Tên lớp và tọa độ điểm ảnh `xyxy`: Lớp: car (ID: 0), Tọa độ tâm (normalized): (0.2654, 0.5094), Kích thước (normalized): (0.0994, 0.0711), Tọa độ điểm ảnh (xyxy): [138.1, 303.2, 201.7, 348.7].
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
  - Đúng định dạng chỉ mang ý nghĩa cấu trúc chuỗi thỏa mãn quy chuẩn kỹ thuật của YOLO (gồm 5 số thực hợp lệ, các giá trị tọa độ nằm trọng đoạn [0, 1]).
  - Sai lớp: Định dạng không thể tự kiểm tra xem đối tượng thực tế bên trong box là xe con (car), xe van hay xe tải (truck).
  - Sai phạm vi/hình học: Box có thể bị bao quá rộng, quá hẹp hoặc bị lệch tâm đối tượng hoặc bao trùm cả vật cản che khuất mà không bám sát mép biên thực của xe.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: drive_022, drive_033, drive_038
- Mã ảnh thẩm định: drive_008
- Mô tả một dự đoán trong `detect_result.jpg`:
  - Mô hình bỏ sót (FN) gần như toàn bộ các kích thước nhỏ ở phía xa (làn xe chờ phía trên ngã tư và cụm xe xếp hàng ở góc trên bên phải).
  - Đối với các phương tiện lớn ở gần, mô hình gặp khó trong việc dự đoán trọn ven một bounding box duy nhất mà dễ dự đoán sót hoặc phân mảnh đối tượng.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào?
  - Quy tắc gán nhãn vật thể nhỏ và ở xa: Cần làm rõ ngưỡng kích thước tối thiểu để gán nhãn cho các xe ở xa phía cuối đường, tránh việc tập huấn luyện lúc gán lúc bỏ khiến mô hình bị nhiễu.
  - Quy tắc bao quanh xe buýt dài và xe chở hàng nhô cao: Xác định rõ ràng bounding box cho xe buýt là 1 box duy nhất, và xe tải chở đất đá có tính phần đất đá ngọn vào chiều cao box hay không.
- Minh chứng nào có thể bác bỏ nhận định của bạn?
  - Khi tăng độ phân giải ảnh đầu vào hoặc giảm ngưỡng tin cậy xuống, nếu mô hình hiện được đầy đủ các xe ở xa mà không làm tăng đột biến các dự đoán sai (FP), điều đó chứng minh vấn đề nằm ở cấu hình inference chứ không phải do dữ liệu gán nhãn thiếu nhất quán.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?
  - Dung lượng mẫu quá nhỏ: 3 ảnh train và 1 ảnh val không thể đại diện được cho phân phối dữ liệu thực tế.
  - Nguy cơ overfitting cao: Mô hình chỉ học thuộc lòng vị trí và bối cảnh của 3 ảnh tĩnh.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 48
- IoU trung bình và trung vị: 0.859996 (trung bình), 0.889457 (trung vị)
- Mức đồng thuận lớp: 0.729167
- Số hộp phía bạn không ghép được: 57
- Số hộp phía đối chiếu không ghép được: 2
- Một điểm khác biệt cụ thể:
  - Phía tôi gán nhãn chi tiết và dày đặc hơn rất nhiều so với phía đối chiếu, phân bổ tập trung dày dặc ở các cụm xe nhỏ ở phía xa cuối đường và các xe bị che khuất một phần. Ngược lại, phía đối chiếu chỉ gán các phương tiện rõ ràng ở tiền cảnh và trung cảnh.
- Quy tắc hoặc hành động sửa phát sinh:
  - Thống nhất ngưỡng kích thước tối thiểu gán nhãn: Quy định rõ ràng các phương tiện có chiều dài/chiều rộng nhỏ hơn bao nhiêu pixel hoặc độ che khuất vượt quá bao nhiêu phần trăm thì được phép bỏ qua không gán nhãn.
  - Đồng bộ chuẩn phân loại giữa car và van; van và bus.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?
  - Mức đồng thuận chỉ thể hiện sự tương đồng về mặt quan điểm giữa hai bên gán nhãn, không đại diện cho ground truth.
  - Cả hai bên có thể đều mắc cùng một sai lầm mang tính hệ thống do hiểu sai cùng một điểm mơ hồ trong guideline.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất.
- [x] Có thông tín lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [x] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [x] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

- Sự chênh lệch lớn giữa chỉ số hình học và chỉ số ngữ nghĩa khi đối chiếu nhãn:
  - Chỉ số hình học đạt mức rất cao với IoU trung bình là 0.860 và IoU trung vị là 0.889, chứng minh kỹ thuật vẽ hộp bounding box của hai bên rất khớp nhau về vị trí và phạm vi vật thể.
  - Ngược lại, mức đồng thuận lớp chỉ đạt 0.729, đi kèm sự chênh lệch áp đảo về số lượng hộp không ghép được (57 hộp phía người gán so với chỉ 2 hộp phía đối chiếu).
- Ý nghĩa: Đây là bằng chứng định lương rõ ràng nhất cho thấy sự sai lệch không nằm ở kỹ năng căn chỉnh tọa độ, mà bắt nguồn từ sự bất đồng trong quy ước gán nhãn - cụ thể là tiêu chí phân biệt giữa các dòng xe tương đồng và ngưỡng kích thước/độ che khuất tối thiểu để gán nhãn cho các vật thể ở xa.
