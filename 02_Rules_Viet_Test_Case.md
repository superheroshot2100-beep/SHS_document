# QUY TẮC VIẾT TEST CASE (BẮT BUỘC)

## 1. Template chuẩn (12 cột)

| Cột | Bắt buộc | Quy tắc | Ví dụ |
|---|---|---|---|
| **TC ID** | ✔ | `TC_<MODULE>_<CHỨC NĂNG>_<STT 3 số>` | `TC_REG_SDT_001` |
| **Req ID** | ✔ | ID requirement / BR liên quan | `BR-03` |
| **Module** | ✔ | Tên màn hình / chức năng | Đăng ký tài khoản |
| **Title** | ✔ | `Kiểm tra <hành động/đối tượng> khi <điều kiện>` | Kiểm tra đăng ký khi SĐT có 9 chữ số |
| **Precondition** | ✔ | Trạng thái hệ thống + quyền + dữ liệu có sẵn | User chưa đăng nhập; đang ở màn Đăng ký |
| **Steps** | ✔ | Đánh số, 1 hành động/bước, tối đa ~8 bước | 1. Nhập SĐT 2. Nhập các field khác hợp lệ 3. Click "Đăng ký" |
| **Test Data** | ✔ | Giá trị **cụ thể** | SĐT = `091234567` |
| **Expected Result** | ✔ | Đo được, kiểm chứng được (text, màu, trạng thái, DB, API) | Hiển thị lỗi "Số điện thoại phải gồm 10 chữ số" dưới ô SĐT; không tạo tài khoản |
| **Priority** | ✔ | High / Medium / Low (xem mục 4) | High |
| **Type** | ✔ | Positive / Negative / Boundary / UI / Security / Permission / Integration | Boundary |
| **Technique** | ✔ | EP / BVA / DT / ST / UC / EG / PW | BVA |
| **Note** | | [GIẢ ĐỊNH], link Q&A, môi trường đặc biệt | [GIẢ ĐỊNH] text lỗi theo Q&A-02 |

> Khi chạy test, tester thêm cột: Actual Result, Status (Pass/Fail/Blocked/N/A), Bug ID, Tester, Ngày test.

## 2. Quy tắc nội dung

1. **1 TC = 1 mục tiêu.** Không viết "Kiểm tra SĐT rỗng và sai định dạng" → tách 2 case.
2. **Steps nguyên tử:** động từ đầu câu: *Nhập, Chọn, Click, Kéo, Upload, Gọi API...*
3. **Test data cụ thể:** cấm viết "nhập dữ liệu hợp lệ" → viết `Email = test.qa01@gmail.com`.
4. **Expected Result cấm từ mơ hồ** khi đứng một mình: *đúng, bình thường, thành công, hợp lệ, OK, như mong đợi*. Phải mô tả rõ hiện tượng.
   - ❌ "Đăng ký thành công"
   - ✅ "Chuyển sang màn Xác thực OTP; hiển thị toast 'Đăng ký thành công'; DB bảng `users` có bản ghi status = `PENDING`"
5. **Expected đa lớp (nếu có thể):** UI → Dữ liệu (DB/API response) → Side effect (email, SMS, log, thông báo).
6. **Negative case phải kiểm 2 ý:** có báo lỗi đúng **và** hệ thống không thực hiện hành động (không lưu, không trừ tiền...).
7. Message lỗi lấy **đúng nguyên văn** tài liệu; nếu tài liệu không có → [GIẢ ĐỊNH] + đưa vào Q&A.
8. Precondition dùng chung được → gom vào phần đầu module, không lặp lại từng dòng.

## 3. Thứ tự sắp xếp test case trong 1 chức năng
1. UI / Layout mặc định
2. Positive – luồng chính (happy path)
3. Validation từng field (theo thứ tự field trên màn hình)
4. Boundary
5. Negative – nghiệp vụ / luồng phụ / luồng lỗi
6. Permission / Phân quyền
7. Security cơ bản, Integration, Non-functional (nếu trong phạm vi)

## 4. Định nghĩa Priority
| Priority | Khi nào | Ví dụ |
|---|---|---|
| **High** | Luồng chính, liên quan tiền / dữ liệu / bảo mật / pháp lý; lỗi thì chặn người dùng | Đặt lệnh mua, đăng nhập, thanh toán |
| **Medium** | Validation, luồng phụ, ảnh hưởng một phần | SĐT sai định dạng, lọc danh sách |
| **Low** | UI, text, căn chỉnh, trường hợp hiếm | Placeholder, màu nút |

> Tỷ lệ tham khảo: High 20–30% · Medium 50% · Low 20–30%. Tỷ lệ Positive : Negative ≈ 30 : 70.

## 5. Viết test case API (bổ sung)
- Steps ghi: Method + Endpoint + Header + Body.
- Expected ghi: HTTP status + response body (field chính, message, error code) + thay đổi DB.
- Ví dụ: `POST /api/v1/orders`, body `{"bondCode":"ABC2026","quantity":0}` → `400`, `{"code":"INVALID_QUANTITY"}`, không tạo bản ghi `orders`.
