# PROMPT PIPELINE B1 → B5

> Dùng lệnh ngắn (/B1...) khi đã cài Project Instructions. Prompt đầy đủ bên dưới dùng khi chat ngoài Project hoặc với AI khác.

---

## B1 – PHÂN TÍCH YÊU CẦU
**Prompt:**
```
Đóng vai Senior QA 15 năm. Phân tích tài liệu đính kèm theo định dạng sau, KHÔNG viết test case:
1. Tổng quan: mục tiêu chức năng, đối tượng người dùng (role).
2. Danh sách Feature/Màn hình.
3. Business Rule: bảng [BR ID | Mô tả | Nguồn (mục tài liệu) | Loại: Validation/Logic/Phân quyền/Tích hợp/UI].
4. Field list: [Field | Kiểu | Bắt buộc | Ràng buộc | Mặc định | Message lỗi].
5. Q&A: [Q ID | Câu hỏi | Lý do hỏi | Mức: Blocker/Major/Minor | Giả định đề xuất].
6. Rủi ro nghiệp vụ chính (top 5).
```
**Output mẫu:**
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | SĐT bắt buộc, 10 số, bắt đầu bằng 0 | Mục 3.2 | Validation |

| Q ID | Câu hỏi | Mức | Giả định |
|---|---|---|---|
| Q-01 | SĐT có tự trim khoảng trắng đầu/cuối? | Minor | Có trim |

---

## B2 – CHIẾN LƯỢC TEST
**Prompt:**
```
Dựa trên kết quả B1, lập chiến lược test ngắn gọn:
1. In-scope / Out-of-scope.
2. Loại test áp dụng (Functional, UI, API, Permission, Integration, Security cơ bản, Compatibility).
3. Bảng: [BR ID | Kỹ thuật chính | Kỹ thuật phụ | Priority | Ước lượng số TC].
4. Môi trường & dữ liệu test cần chuẩn bị (tài khoản theo role, dữ liệu mẫu).
5. Tiêu chí hoàn thành (Exit criteria).
```

---

## B3 – THIẾT KẾ TEST CONDITION
**Prompt:**
```
Với từng BR, liệt kê test condition theo kỹ thuật đã chọn ở B2:
- EP/BVA: bảng [Field | Lớp hợp lệ | Lớp không hợp lệ | Giá trị biên].
- Decision table / State transition: vẽ bảng đầy đủ.
- Use case: luồng chính / phụ / ngoại lệ.
- Error guessing: chọn mục phù hợp từ file 03.
Đánh mã condition: TCO-<BR>-<STT>. Chưa viết step.
```
**Output mẫu:**
| Field | Hợp lệ | Không hợp lệ | Biên |
|---|---|---|---|
| SĐT | `0912345678` | rỗng, chữ, đầu `1`, ký tự đặc biệt | 9 số, 10 số, 11 số |

---

## B4 – SINH TEST CASE
**Prompt:**
```
Chuyển toàn bộ test condition B3 thành test case theo đúng template 12 cột và quy tắc trong file 02.
- Sắp xếp theo thứ tự mục 3 file 02.
- Mỗi condition ≥ 1 TC; ghi Req ID + Technique.
- Message lỗi chưa rõ → [GIẢ ĐỊNH] + ghi Q ID ở Note.
Xuất bảng markdown; nếu > 40 TC thì chia theo module, hỏi tôi trước khi viết tiếp phần sau.
```
**Output mẫu:**
| TC ID | Req ID | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique |
|---|---|---|---|---|---|---|---|---|---|
| TC_REG_SDT_001 | BR-01 | Kiểm tra đăng ký khi SĐT hợp lệ 10 số | Chưa đăng nhập, ở màn Đăng ký | 1. Nhập SĐT 2. Nhập field khác hợp lệ 3. Click "Đăng ký" | `0912345678` | Chuyển màn OTP; DB `users` có bản ghi status `PENDING` | High | Positive | EP |
| TC_REG_SDT_002 | BR-01 | Kiểm tra đăng ký khi SĐT có 9 chữ số | Như trên | Như trên | `091234567` | Lỗi "SĐT phải gồm 10 chữ số" dưới ô SĐT; không chuyển màn; không tạo bản ghi | Medium | Boundary | BVA |

---

## B5 – TỰ REVIEW & HOÀN THIỆN
**Prompt:**
```
Review bộ test case B4 theo file 05:
1. Lập RTM: [BR ID | Số TC | TC IDs | Trạng thái phủ: Đủ/Thiếu].
2. Chấm checklist (Đạt/Không đạt) và liệt kê lỗi cụ thể theo TC ID.
3. Viết bổ sung các TC còn thiếu, sửa TC lỗi.
4. Thống kê: tổng TC, % theo Priority, % Positive/Negative, số Q&A còn mở.
```

---

## /REVIEW – REVIEW BỘ TC CÓ SẴN
```
Review bộ test case đính kèm so với tài liệu yêu cầu. Trả:
1. Bảng lỗi: [TC ID | Vấn đề | Loại: Thiếu/Sai nghiệp vụ/Mơ hồ/Trùng/Sai format | Đề xuất sửa].
2. Danh sách BR chưa được phủ + TC đề xuất bổ sung.
3. Điểm chất lượng /10 kèm lý do.
```

## /BUG – VIẾT BUG REPORT
```
Viết bug report từ mô tả: [mô tả]. Định dạng: Title (≤ 15 từ: [Module] Hiện tượng khi Điều kiện) | Env | Precondition | Steps | Actual | Expected | Severity (Critical/Major/Minor/Trivial) | Priority | Evidence cần đính kèm.
```
