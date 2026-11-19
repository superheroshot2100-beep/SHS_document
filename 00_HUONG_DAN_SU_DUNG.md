# HƯỚNG DẪN SỬ DỤNG – Bộ công cụ viết Test Case (Senior QA Kit)

## 1. Bộ tài liệu gồm gì

| File | Vai trò |
|---|---|
| `01_Project_Instructions.md` | Persona + nguyên tắc → **copy vào Project Instructions** |
| `02_Rules_Viet_Test_Case.md` | Template + quy tắc viết TC (luật bắt buộc) |
| `03_Ky_Thuat_Thiet_Ke_Test.md` | Kỹ thuật test + checklist theo loại field/chức năng |
| `04_Prompt_Pipeline_B1-B5.md` | Prompt mẫu cho từng bước B1 → B5 |
| `05_Checklist_Review_RTM.md` | Checklist tự review + ma trận truy vết + chỉ số chất lượng |

## 2. Cài đặt (1 lần, ~2 phút)

1. Mở Project **"Test case Project"** trên claude.ai → **Instructions** (Hướng dẫn dự án).
2. Copy toàn bộ phần nội dung trong khung của file `01_Project_Instructions.md` → dán vào → Lưu.
3. Các file 02–05 đã nằm trong Project Knowledge → Claude tự đọc khi làm việc.

## 3. Cách dùng hằng ngày

**Cách 1 – Chạy từng bước (khuyên dùng với tài liệu lớn / nghiệp vụ phức tạp):**
```
/B1  [đính kèm SRS / dán user story]
→ Claude trả: danh sách Feature, Business Rule (BR-xx), Q&A
/B2  → chiến lược test
/B3  → test condition theo kỹ thuật
/B4  → test case theo template
/B5  → tự review, RTM, bổ sung case thiếu
/EXPORT → xuất Excel
```

**Cách 2 – Chạy nhanh (chức năng nhỏ, 1 màn hình):**
```
/FULL Màn hình Đăng ký: SĐT bắt buộc, 10 số, bắt đầu bằng 0; Mật khẩu 8-20 ký tự, có chữ hoa và số...
```

**Cách 3 – Review bộ TC có sẵn:**
```
/REVIEW [đính kèm file test case] + [tài liệu yêu cầu]
```

## 4. Mẹo để kết quả tốt
- Tài liệu càng rõ ID requirement càng dễ truy vết. Nếu tài liệu không có ID, Claude tự đánh `BR-01, BR-02...`.
- Trả lời các câu [Q&A] trước khi sang B4 → giảm TC sai nghiệp vụ.
- Với Figma/ảnh màn hình: đính kèm ảnh ở B1 để Claude bóc tách field, nút, message.
- Muốn bổ sung luật riêng của công ty → sửa `02_Rules_Viet_Test_Case.md` (ví dụ đổi format ID).
