# CHECKLIST REVIEW + MA TRẬN TRUY VẾT (RTM)

## 1. Checklist tự review (B5 / /REVIEW)

### A. Độ phủ
- [ ] 100% BR có ít nhất 1 TC (đối chiếu RTM)
- [ ] Mỗi field có đủ: rỗng, hợp lệ, không hợp lệ, biên
- [ ] Có luồng chính + luồng phụ + luồng ngoại lệ
- [ ] Có case phân quyền cho từng role (nếu có nhiều role)
- [ ] Decision table: mỗi rule có TC · State transition: có cả chuyển trạng thái không hợp lệ
- [ ] Đã áp dụng Error guessing phù hợp (file 03)

### B. Chất lượng từng test case
- [ ] Đủ 12 cột, ID đúng format, không trùng
- [ ] 1 TC = 1 mục tiêu
- [ ] Title theo mẫu "Kiểm tra … khi …"
- [ ] Test data cụ thể, chạy được ngay
- [ ] Expected đo được, không dùng từ mơ hồ đứng một mình
- [ ] Negative case kiểm cả "báo lỗi" lẫn "không thực hiện hành động"
- [ ] Message lỗi đúng nguyên văn tài liệu hoặc có [GIẢ ĐỊNH]

### C. Tính nhất quán
- [ ] Không có TC trùng nội dung khác ID
- [ ] Priority hợp lý (luồng tiền/bảo mật = High)
- [ ] Thuật ngữ, tên nút, tên màn hình thống nhất với tài liệu
- [ ] Sắp xếp đúng thứ tự (UI → Positive → Validation → Boundary → Negative → Permission)

## 2. Mẫu RTM

| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ | Ghi chú |
|---|---|---|---|---|---|
| BR-01 | SĐT 10 số, bắt đầu 0 | 9 | TC_REG_SDT_001–009 | Đủ | |
| BR-02 | Mật khẩu 8–20, có hoa + số | 4 | TC_REG_MK_001–004 | **Thiếu** | Chưa có case 20/21 ký tự |

## 3. Chỉ số chất lượng bộ test case

| Chỉ số | Mục tiêu |
|---|---|
| Requirement coverage | 100% |
| Tỷ lệ Positive : Negative | ≈ 30 : 70 |
| Tỷ lệ High / Medium / Low | ≈ 25 / 50 / 25 |
| TC bị reviewer sửa/loại | < 10% |
| Q&A mở mức Blocker trước khi test | 0 |

## 4. Mẫu báo cáo sau test (Test Summary)

| Mục | Nội dung |
|---|---|
| Phạm vi | Module, build/version, môi trường |
| Thời gian | Từ – đến |
| Kết quả | Tổng TC · Pass · Fail · Blocked · N/A · % Pass |
| Bug | Tổng · theo Severity (Critical/Major/Minor/Trivial) · Đã fix / Còn mở |
| Rủi ro còn lại | Bug mở nghiêm trọng, phần chưa test được, lý do |
| Kết luận | Đạt / Không đạt Exit criteria → Đề xuất release hay không |
