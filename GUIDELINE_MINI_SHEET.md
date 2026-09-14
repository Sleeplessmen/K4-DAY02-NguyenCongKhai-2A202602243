# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** NGUYỄN CÔNG KHẢI<br>
**MSSV:** 2A202602243<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

|  Mã | Lớp              | Gán khi nhìn thấy                                       | Không gán vào lớp này                             |
| --: | ---------------- | ------------------------------------------------------- | ------------------------------------------------- |
|   0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
|   1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng      | ô tô con; thân xe buýt; xe van kín một khối       |
|   2 | `bus` (xe buýt)  | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế           | xe van nhỏ; xe tải; ô tô con                      |
|   3 | `van` (xe van)   | thân hộp nhỏ, kín, dùng chở người hoặc hàng             | thân xe buýt; khoang hàng tách biệt như xe tải    |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính                          | Giá trị                                                 | Ý nghĩa                             |
| ----------------------------------- | ------------------------------------------------------- | ----------------------------------- |
| `visibility` (mức nhìn thấy)        | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy            |
| `boundary` (quan hệ mép ảnh)        | `inside` (trong ảnh), `truncated` (bị cắt)              | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại)      | đánh dấu quyết định cần quay lại    |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: drive_008.jpg#31 (object 31 trong frame)
- Dấu hiệu nhìn thấy:
  - Kích thước thân xe nhỏ/trung bình, thân xe dạng hộp khối đứng liền khối ngắn.
  - Phần đuôi có cửa hậu mở thẳng đứng.
  - Chiều dài tổng thể ngắn, chỉ tương đương hoặc nhỉnh hơn xe con (car) một chút và nhỏ hơn rất nhiều so với so buýt (bus).
- Quy tắc áp dụng:
  - Các dòng xe dạng hộp chở khách nhỏ chở hàng mini có chiều dài dưới 6m và dưới 15 chỗ được phân loại là van; không xếp vào bus.
- Quyết định: Gán nhãn object là van; các thuộc tính đi kèm: visibility = clear, boundary: inside, review_state: confident.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì?
  - Chuyển thuộc tính review_state = needs_review và để người phụ trách QA/QC hoặc quản lý dự án kiểm tra lại.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: drive_022.jpg#3 (object 3 trong frame)
- Dấu hiệu nhìn thấy:
  - Xe có cấu trúc phân tách rõ rệt thành 2 khối riêng biệt:
    - Cabin điều khiển phía trước: Dạng cabin vuông vắn, vát phẳng.
    - Thùng chở hàng phía sau: Là dạng thùng hình hộp chữ nhật lớn màu trắng, cao vượt hẳn lên so với nóc cabin lái.
  - Gầm xe cao, khoảng sáng gầm lớn.
- Quy tắc áp dụng:
  - Phương tiện có cabin độc lập kết hợp với thùng chở hàng chuyên dụng phía sau được định danh là xe tải (truck), ko xếp vào nhóm xe con (car) hay xe van.
- Quyết định: Gán nhãn cho object là truck; các thuộc tính đi kèm: visibility = unclear, boundary = inside, review_state = confident.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? 
  - Kiểm tra các frame lân cận khi xe di chuyển lại gần camera để quan sát rõ hơn trục bánh xe, logo hãng hoặc biển số đăng ký.
  - Nếu trong trường hợp bị che khuất hoặc quá mờ không phân biệt được là xe tải nhỏ hay xe bán tải, chuyển review_state = needs_review để đối chiếu với guideline phân loại xe tải và xe van/xe con của trưởng nhóm QA/QC.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứn

- Ảnh và mã vật thể: drive_008.jpg#4 (object 4 trong frame)
- Dấu hiệu nhìn thấy khi phóng 100%:
  - Chỉ nhìn thấy một phần rất nhỏ của phương tiện (mép vòm bánh xe, góc cản trước hoặc một phần đèn pha sẫm màu).
  - Phần lớn thân xe, khoang lái, biển số và toàn bộ phần đuôi xe đều nằm hoàn toàn bên ngoài khung hình.
  - Tỷ lệ diện tích quan sát được ước tính chiếm dưới 15% tổng thể chiếc xe.
- Giá trị `visibility`: `unclear`
- Giá trị `boundary`: `truncated`
- Trạng thái `review_state`: `needs_review`
- Lý do:
  - Xe bị cắt ngang trực tiếp bởi cả mép phải và mép đáy của frame.
  - Phân hiển thị quá ít và bị thiếu góc toàn cảnh, khiến việc khẳng định chắc chắn 100% đây là loại xe gì là chưa đủ cơ sở rõ ràng.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [] Đã xử lý mọi hộp `needs_review`.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [x] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: 105 — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
