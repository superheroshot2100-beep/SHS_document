# KỸ THUẬT THIẾT KẾ TEST + CHECKLIST THEO LOẠI ĐỐI TƯỢNG

## 1. Chọn kỹ thuật theo loại yêu cầu

| Mã | Kỹ thuật | Dùng khi | Ví dụ ngắn |
|---|---|---|---|
| **EP** | Phân vùng tương đương | Field có miền giá trị | Tuổi 18–60: lớp <18, 18–60, >60 |
| **BVA** | Giá trị biên | Có min/max, độ dài | 17, 18, 19 · 59, 60, 61 |
| **DT** | Decision table | Nhiều điều kiện kết hợp ra kết quả | Giảm giá = VIP × Đơn > 1tr × Có voucher |
| **ST** | State transition | Đối tượng có trạng thái | Đơn: Nháp → Chờ duyệt → Đã duyệt / Từ chối → Hủy |
| **UC** | Use case / luồng | Quy trình nghiệp vụ nhiều bước | Luồng chính, luồng phụ, luồng ngoại lệ |
| **EG** | Error guessing | Luôn áp dụng bổ sung | Double-click, mất mạng, back trình duyệt |
| **PW** | Pairwise | Nhiều tham số × nhiều giá trị | 3 trình duyệt × 3 OS × 2 ngôn ngữ → ~9 tổ hợp |

**Quy tắc chọn:** mỗi BR ít nhất 1 kỹ thuật chính + EG. Field có ràng buộc → luôn EP + BVA.

### Mẫu Decision table (B3)
| Điều kiện / Rule | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| Khách VIP | Y | Y | N | N |
| Đơn > 1.000.000đ | Y | N | Y | N |
| **Kết quả: giảm** | 10% | 5% | 3% | 0% |
→ mỗi cột R = ít nhất 1 test case.

### Mẫu State transition (B3)
| Từ \ Hành động | Gửi duyệt | Duyệt | Từ chối | Hủy |
|---|---|---|---|---|
| Nháp | Chờ duyệt | ✖ | ✖ | Đã hủy |
| Chờ duyệt | ✖ | Đã duyệt | Từ chối | Đã hủy |
| Đã duyệt | ✖ | ✖ | ✖ | ✖ |
→ test cả ô hợp lệ **và** ô ✖ (chuyển trạng thái không được phép).

## 2. Checklist Error Guessing theo loại field

**Text (họ tên, địa chỉ...)**
- Rỗng · chỉ khoảng trắng · khoảng trắng đầu/cuối (có trim không?)
- min-1, min, max, max+1 ký tự
- Ký tự đặc biệt `!@#$%^&*`, emoji 😀, tiếng Việt có dấu, Unicode tổ hợp
- Script/HTML: `<script>alert(1)</script>` · SQL: `' OR 1=1 --`
- Copy-paste chuỗi dài

**Số (số lượng, tiền)**
- 0, âm, số thập phân, số rất lớn, chữ, ký tự `e`, dấu `,` `.` theo locale
- Định dạng hiển thị: phân cách hàng nghìn, làm tròn

**Ngày/giờ**
- Ngày quá khứ / tương lai / hôm nay, 29/02 năm nhuận, 31/04
- Từ ngày > Đến ngày · múi giờ · nhập tay sai format

**Email / SĐT**
- Thiếu `@`, thiếu domain, 2 dấu `@`, viết hoa, trùng tài khoản đã tồn tại
- SĐT: 9/10/11 số, đầu số không hợp lệ, có `+84`, có khoảng trắng/dấu gạch

**Mật khẩu**
- Biên độ dài, thiếu từng điều kiện (hoa/thường/số/ký tự đặc biệt)
- Hiển thị dạng `•••`, nút ẩn/hiện, xác nhận mật khẩu không khớp, không lưu plaintext

**Dropdown / Radio / Checkbox**
- Giá trị mặc định, không chọn, chọn tất cả, danh sách rỗng, danh sách rất dài, giá trị bị vô hiệu sau khi chọn

**Upload file**
- Đúng/sai định dạng, đổi đuôi file giả, dung lượng = max / > max, 0 byte, tên file tiếng Việt / ký tự đặc biệt, upload nhiều file, hủy giữa chừng

**Nút / Submit**
- Double-click (tạo trùng?), click khi đang loading, Enter để submit, nút disable khi chưa đủ điều kiện

**Danh sách / Tìm kiếm / Phân trang**
- Không có dữ liệu, 1 bản ghi, đúng số bản ghi/trang, trang cuối
- Tìm kiếm: chính xác, gần đúng, có dấu/không dấu, hoa/thường, ký tự đặc biệt, không kết quả
- Sắp xếp, lọc kết hợp, giữ bộ lọc khi quay lại

**Phân quyền**
- Role không có quyền: ẩn menu **và** chặn truy cập bằng URL/API trực tiếp
- Xem/sửa dữ liệu của user khác (IDOR), session hết hạn

**Hệ thống / Môi trường**
- Mất mạng giữa thao tác, refresh / back trình duyệt, mở 2 tab cùng sửa 1 bản ghi
- Responsive (mobile/tablet), trình duyệt chính

**Nghiệp vụ tài chính (bổ sung)**
- Ngoài giờ giao dịch, ngày nghỉ lễ, số dư không đủ, vượt hạn mức, làm tròn tiền, đặt lệnh trùng, idempotency khi retry
