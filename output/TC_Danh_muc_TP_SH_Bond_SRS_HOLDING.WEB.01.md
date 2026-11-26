# SBOND.HOLDING.WEB.01 – Danh mục trái phiếu SH Bond (Web) – Test design B1→B5

Nguồn: SRS SBOND.HOLDING.WEB.01 – Danh mục trái phiếu SH Bond (export Confluence 15/11/2026) + ảnh luồng và ảnh Figma Web trong SRS  
Người viết: Nguyễn Sỹ Hà · Ngày: 26/11/2026 · Lệnh: /FULL  
Phiên bản: v1 · Kênh: SHSmart Web  
Ký hiệu: [GIẢ ĐỊNH] = tạm giả định, chờ trả lời câu hỏi Q-xx; PC-xx = điều kiện có sẵn dùng chung; SOP-xx = thao tác dùng chung; "Bổ sung B5" = TC thêm ở bước tự review.

## B1. Phân tích yêu cầu
### 1. Tổng quan
- Mục đích: KH xem các mã TP SH Bond đang nắm giữ, mỗi mã 1 dòng (gộp các HĐ kỳ hạn của mã), lọc theo Mã TP / TCPH / Loại TP, xem Chi tiết và đi sang Danh mục HĐ của mã.
- Người dùng: KH trên SHSmart Web. Hệ thống liên quan: BE S-Bond (API S6, C2), Flex (số dư, lưu ký TP).
- Vào màn từ: "Sản phẩm đầu tư" > "Danh sách nắm giữ" > tab "Danh mục trái phiếu" > chip "SH Bond" (mặc định).
- Màn chỉ xem, không đổi dữ liệu. Cụm số dư / Nộp tiền phía trên thuộc SRS SBOND.OFFERING.WEB.01 (ngoài phạm vi).

### 2. Feature
- Mở màn & bảng gộp theo mã · Công thức 3 cột tiền · Tìm Mã TP · Lọc TCPH · Lọc Loại TP · Kết hợp / đặt lại bộ lọc · Phân trang · Sắp xếp · Chi tiết → Danh mục HĐ · Trống & lỗi · Phân quyền · Tracking · API S6 / C2

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Vào "Danh mục Trái phiếu" thì chip "SH Bond" được chọn mặc định; Web gọi S6 / C2 với bộ lọc mặc định (tất cả) | Entry; Luồng bước 1 | Logic |
| BR-02 | Bảng có 8 cột theo thứ tự: Mã TP, TCPH (tên đầy đủ), SL HĐ (số nguyên), SL TP (#.###), Tổng mệnh giá, Tổng tiền đầu tư, Tổng tiền thực nhận (#.### VND; đầu tư màu cam, thực nhận màu xanh), nút "Chi tiết" | Mục 2.2 #1–8; Luồng bước 2 | UI |
| BR-03 | Gộp theo mã TP (BIZ-18): mỗi dòng = 1 mã TP, cộng dồn các HĐ kỳ hạn KH đang nắm giữ của mã đó: SL HĐ, SL TP, Tổng mệnh giá, Tổng tiền đầu tư, Tổng tiền thực nhận | BIZ-18; Mục 2.2 #3–4 | Logic |
| BR-04 | Tổng mệnh giá = SL TP × Mệnh giá niêm yết ban đầu của mã; không đổi theo thị giá / giá giao dịch (BIZ-19) | BIZ-19; Mục 2.2 #5 | Logic |
| BR-05 | Tổng tiền thực nhận = gốc đầu tư + lãi dự kiến theo kỳ hạn, chưa tính thuế (BIZ-20) | BIZ-20; Mục 2.2 #7 | Logic |
| BR-06 | Tổng tiền đầu tư = tổng tiền đầu tư các HĐ của mã TP (SRS ghi "Dự thảo BA, Q06") | Mục 2.2 #6 | Logic |
| BR-07 | Ô "Mã trái phiếu": nhập tự do, tìm cụm từ nằm trong mã/tên TP, không phân biệt hoa thường, có danh sách gợi ý, chọn 1 mã; để trống = không lọc; cắt khoảng trắng 2 đầu; tối đa 50 ký tự (đề xuất), không cho nhập quá (V01, BND-100) | Mục 2.1 #1; Mục 5 V01 | Validation |
| BR-08 | Lọc "Tổ chức phát hành": chọn nhiều; mặc định "Tất cả tổ chức"; đã chọn → "Đã chọn n tổ chức"; không chọn = tất cả; danh sách từ data.filters.issuers / C2 | Mục 2.1 #2 | Validation |
| BR-09 | Lọc "Loại trái phiếu": chọn nhiều; mặc định "Tất cả"; giá trị Niêm yết / Riêng lẻ | Mục 2.1 #4a | Validation |
| BR-10 | Áp dụng bộ lọc: "Lưu" gọi lại S6 / C2 theo điều kiện đã chọn; "Đặt lại" về mặc định | Luồng bước 3; Mục 7 FILTER | Logic |
| BR-11 | Phân trang "Trước / 1 2 3 … / Sau"; request S6 page / size; tổng số dòng theo data.totalItems | Mục 2.2 #9 | Logic |
| BR-12 | Sắp xếp ↑/↓ cột "Tổng tiền thực nhận" (request S6: sort) | Mục 2.2 #10 | Logic |
| BR-13 | Nút "Chi tiết" mở popup Chi tiết trái phiếu (HOLDING.WEB.02) của đúng mã; từ popup bấm "Xem danh mục hợp đồng" → Danh mục HĐ (CONTRACT.WEB.01) lọc theo mã TP | Luồng bước 4–5; Mục 2.2 #8 | Tích hợp |
| BR-14 | Trạng thái màn: skeleton khi đang gọi S6 / C2; danh sách rỗng → empty state I01 "Quý khách chưa có trái phiếu SH Bond" (chưa có TP) / "Không tìm thấy kết quả phù hợp" (lọc không ra) | Mục 2.3; Mục 6 #1; Luồng bước 6 | UI |
| BR-15 | Lỗi BND-901 / BND-999 / AUTH_001 → popup lỗi chuẩn SHSmart: "Hệ thống đang bận" / "Có lỗi xảy ra. Vui lòng thử lại sau" / "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"; CTA Đóng / Đăng nhập | Mục 6 #2; Luồng bước 6 | Lỗi |
| BR-16 | Chỉ hiển thị TP của KH đang đăng nhập (Pre-condition: KH đã đăng nhập); màn chỉ xem, không đổi dữ liệu (Post-condition) | Pre/Post-Condition; User Story | Phân quyền |
| BR-17 | Ghi nhận 3 sự kiện FE: SBOND.HOLDING.SHBOND.LIST (mở/tải lại), .FILTER (lưu/đặt lại bộ lọc), .DETAIL (bấm "Chi tiết") | Mục 7 | Tích hợp |
| BR-18 | API: S6 GET /api/v1/bond/app/bond-holdings (mở màn, lọc, đổi trang); C2 GET /api/v1/bond/common/issuer/list (danh sách TCPH) | Mục 3; Mục 2.1–2.2 mapping | Tích hợp |

### 4. Field list
| Vùng | Trường | Kiểu | Bắt buộc | Ràng buộc / Mapping | Mặc định | Khi lỗi |
|---|---|---|---|---|---|---|
| Bộ lọc | Mã trái phiếu | Ô tìm kiếm + gợi ý | Không | Cắt khoảng trắng 2 đầu; tối đa 50 ký tự [GĐ Q-05]; không phân biệt hoa thường; tìm "bao gồm" | Trống (placeholder "Mã trái phiếu") | Không cho nhập quá; API BND-100 |
| Bộ lọc | Tổ chức phát hành | Dropdown chọn nhiều | Không | Danh sách từ data.filters.issuers / C2 | "Tất cả tổ chức" | — |
| Bộ lọc | Loại trái phiếu | Dropdown chọn nhiều | Không | Niêm yết / Riêng lẻ (C1 data[].code) | "Tất cả" | — |
| Bảng | Mã trái phiếu | Text | Có | symbol – VD BHB291193 | — | — |
| Bảng | Tổ chức phát hành | Text | Có | issuerName – tên đầy đủ | — | — |
| Bảng | SL Hợp đồng | Số nguyên | Có | numberOfContract – số HĐ đang nắm giữ | — | — |
| Bảng | SL Trái phiếu | Số #.### | Có | bondAmount – tổng KL TP | — | — |
| Bảng | Tổng mệnh giá | Số #.### VND | Có | totalFaceValue – BIZ-19 | — | — |
| Bảng | Tổng tiền đầu tư | Số #.### VND, màu cam | Có | investmentAmount – Q06 của SRS | — | — |
| Bảng | Tổng tiền thực nhận | Số #.### VND, màu xanh | Có | totalAmountReceived – BIZ-20 | — | — |
| Bảng | Chi tiết | Nút | Có | Mở popup HOLDING.WEB.02 | — | — |
| Bảng | Phân trang | Pagination | Có | page / size; totalItems | Trang 1 | — |
| Bảng | Sắp xếp Tổng tiền thực nhận | Sort ↑/↓ | Không | sort (TBU – Dev) | Chưa sắp xếp [GĐ Q-08] | — |

### 5. Q&A
Không có câu mức Blocker → /FULL chạy tiếp. Tổng 15 câu (Major 9 · Minor 6).

| Q ID | Câu hỏi | Lý do | Mức | Giả định đề xuất | TC liên quan |
|---|---|---|---|---|---|
| Q-01 | User Story ghi lọc theo "tên, TCPH, kỳ hạn, lợi suất", nhưng bảng 2.1 chỉ có Mã TP, TCPH, Loại TP (STT nhảy 2 → 4a). Có bộ lọc Kỳ hạn / Lợi suất không? | User Story và bảng 2.1 khác nhau | Major | Chỉ 3 bộ lọc theo bảng 2.1 và Figma Web | Phạm vi (B2) |
| Q-02 | Luồng bước 3: "mở bộ lọc, chọn, bấm Lưu / Đặt lại". Figma Web để bộ lọc ngay trên bảng, không có nút "Lưu" / "Đặt lại". Lọc áp dụng ngay khi chọn hay phải bấm "Lưu"? Đặt lại bằng cách nào? | SRS và Figma khác nhau | Major | Áp dụng ngay khi chọn (theo Figma); "Đặt lại" = bỏ hết điều kiện về mặc định | TC_DMTPW_LC_001, TC_DMTPW_LC_003, TC_DMTPW_LC_005 |
| Q-03 | Loại TP map "Request C1: data[].code" nhưng mục 3 không có API C1. S6 nhận tham số gì cho loại TP? Bảng không có cột Loại TP thì kiểm kết quả lọc qua đâu? | Thiếu API, không có cột đối chiếu | Major | S6 nhận tham số loại TP; đối chiếu "Loại trái phiếu" trong popup Chi tiết | TC_DMTPW_LT_002, TC_DMTPW_LT_003, TC_DMTPW_LT_004 |
| Q-04 | Ô tìm kiếm: "tìm cụm từ nằm trong mã/tên TP" nhưng lại "chọn 1 mã" và gửi symbol. Gõ từ khóa rồi Enter (không chọn gợi ý) có lọc không? Gợi ý chỉ gồm mã đang nắm giữ? Tên TP lấy từ đâu (bảng không có cột tên)? | Hành vi tìm kiếm chưa rõ | Major | Gợi ý chỉ gồm mã đang nắm giữ, tìm theo mã và tên TP; phải chọn gợi ý mới lọc | TC_DMTPW_TK_001 → TC_DMTPW_TK_004, TC_DMTPW_TK_008 |
| Q-05 | V01: độ dài tối đa ghi "đề xuất 50 ký tự", chưa chốt. Dán chuỗi dài hơn thì cắt bớt hay chặn cả chuỗi? | Biên chưa chốt | Minor | Tối đa 50; dán dài hơn thì cắt còn 50 ký tự đầu | TC_DMTPW_TK_007, TC_DMTPW_API_005 |
| Q-06 | Tổng tiền đầu tư ghi "Dự thảo BA, Q06": tính từ số tiền KH thanh toán của HĐ? Có gồm phí giao dịch? | Công thức chưa chốt | Major | Tổng "Tiền đầu tư" của các HĐ đang nắm giữ (đúng số trên Chi tiết HĐ) | TC_DMTPW_CT_003 |
| Q-07 | Phân trang (Q22 của SRS): mỗi trang bao nhiêu dòng? Figma có frame 8 dòng và frame 10 dòng. Chỉ có 1 trang thì có hiện thanh phân trang? | Biên phân trang | Major | 10 dòng/trang; 1 trang vẫn hiện, "Trước" và "Sau" bị khóa | TC_DMTPW_PT_001 → TC_DMTPW_PT_003 |
| Q-08 | Sắp xếp (TBU – Dev): khi mở màn sắp xếp mặc định theo gì? Sắp xếp toàn bộ danh sách (server) hay chỉ trang đang xem? Chu kỳ bấm: ↓ → ↑ → bỏ? | Kết quả sắp xếp | Major | Mặc định theo thứ tự BE trả; sắp xếp toàn bộ danh sách; bấm lần 1 ↓, lần 2 ↑ | TC_DMTPW_SX_001 → TC_DMTPW_SX_003 |
| Q-09 | "Đang nắm giữ" gồm HĐ trạng thái nào? HĐ đã tất toán / đáo hạn / thanh lý / chờ thanh toán có được cộng dồn không? | Quyết định số liệu cộng dồn | Major | Chỉ HĐ trạng thái "Hiệu lực" | TC_DMTPW_DS_003, TC_DMTPW_DS_004 |
| Q-10 | Bộ lọc kết hợp là "và" (AND)? Đổi bộ lọc khi đang ở trang 2 có về trang 1? | Chưa ghi rõ | Major | AND; đổi bộ lọc thì về trang 1 | TC_DMTPW_LC_001, TC_DMTPW_LC_004 |
| Q-11 | Figma khác SRS: (a) Tổng tiền đầu tư SRS màu cam, Figma màu trắng; (b) SRS "#.### VND", Figma không có chữ "VND" trong ô; (c) SRS mặc định "Tất cả tổ chức" / "Tất cả", Figma hiện nhãn "Tổ chức phát hành" / "Loại trái phiếu". Theo bên nào? | SRS và Figma khác nhau | Minor | Theo SRS cho màu và chữ mặc định; không có chữ "VND" theo Figma | TC_DMTPW_UI_001, TC_DMTPW_UI_003 |
| Q-12 | Danh sách TCPH trong dropdown: chỉ TCPH có TP KH đang nắm giữ (data.filters.issuers) hay mọi TCPH (C2 issuer/list)? | Mapping ghi cả 2 nguồn | Minor | Chỉ TCPH có TP KH đang nắm giữ | TC_DMTPW_TC_001 |
| Q-13 | Empty state: SRS ghi câu chữ "TBU Q25". Dùng 2 câu ở I01 cho 2 trường hợp như giả định? | Câu chữ chưa chốt | Minor | Chưa có TP: "Quý khách chưa có trái phiếu SH Bond"; lọc không ra: "Không tìm thấy kết quả phù hợp" | TC_DMTPW_TK_008, TC_DMTPW_LC_002, TC_DMTPW_ER_001 |
| Q-14 | Chi tiết kỹ thuật: (a) Web dùng endpoint ".../bond/app/bond-holdings" có đúng không; (b) empty state map "data.items = []" trong khi danh sách là data.content; (c) sự kiện DETAIL ghi API "S2" không có trong mục 3 | Mapping API không thống nhất | Minor | Dùng endpoint như SRS; rỗng = data.content []; S2 thuộc HOLDING.WEB.02 | TC_DMTPW_API_001, TC_DMTPW_EVT_001 |
| Q-15 | Quay lại từ Danh mục HĐ hoặc chuyển chip "Trái phiếu linh hoạt" rồi về "SH Bond": có giữ bộ lọc / trang / sắp xếp? | Chưa ghi | Minor | Không giữ, về mặc định | TC_DMTPW_CTI_003 |

### 6. Rủi ro chính
| # | Rủi ro | Tác động | Mức |
|---|---|---|---|
| 1 | Cộng dồn sai khi 1 mã có nhiều HĐ, hoặc tính cả HĐ đã tất toán / đáo hạn | KH thấy sai tài sản đang nắm giữ | Cao |
| 2 | Tổng mệnh giá tính theo thị giá; Tổng thực nhận đã trừ thuế hoặc sai lãi | Sai số tiền, khiếu nại | Cao |
| 3 | API S6 trả dữ liệu của KH khác (đổi tham số, cache khi đổi tài khoản) | Lộ thông tin tài sản KH | Cao |
| 4 | Lọc / phân trang / sắp xếp làm thiếu hoặc trùng mã giữa các trang | KH tưởng mất TP | Trung bình |
| 5 | SRS và Figma khác nhau (bộ lọc Lưu/Đặt lại, bộ lọc kỳ hạn/lợi suất, màu, đơn vị) | Dev và QA hiểu khác nhau, phải làm lại | Trung bình |

## B2. Chiến lược test
- **In-scope:** Bảng SH Bond (gộp theo mã, 7 cột số liệu, nút Chi tiết), 3 bộ lọc và kết hợp, phân trang, sắp xếp, đi sang popup Chi tiết và Danh mục HĐ (chỉ kiểm đúng mã, đúng số HĐ), empty state, popup lỗi, phân quyền, tracking, API S6 / C2.
- **Out-of-scope:** Bản App; chip "Trái phiếu linh hoạt"; nội dung chi tiết popup HOLDING.WEB.02 và màn CONTRACT.WEB.01; cụm số dư / Nộp tiền (OFFERING.WEB.01); bộ lọc Kỳ hạn / Lợi suất (chờ Q-01); hiệu năng.
- **Loại test:** Chức năng, giao diện, API, tích hợp (Web – BE – Danh mục HĐ), phân quyền, bảo mật cơ bản, tương thích trình duyệt, đoán lỗi (mất mạng, đổi bộ lọc nhanh, số rất lớn).

| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC (Web / API) |
|---|---|---|---|---|
| BR-01 | UC | EG | High | 2 / 0 |
| BR-02 | EG | PW | Low | 6 / 1 |
| BR-03 | EP | ST | High | 6 / 0 |
| BR-04 | EP | — | High | 2 / 0 |
| BR-05 | EP | — | High | 1 / 0 |
| BR-06 | EP | — | High | 1 / 0 |
| BR-07 | EP | BVA, EG | Medium | 10 / 2 |
| BR-08 | EP | — | Medium | 5 / 2 |
| BR-09 | EP | — | Medium | 5 / 0 |
| BR-10 | DT | EG | Medium | 9 / 0 |
| BR-11 | BVA | EG | Medium | 5 / 1 |
| BR-12 | EP | EG | Medium | 4 / 0 |
| BR-13 | UC | EG | High | 3 / 0 |
| BR-14 | EP | EG | Medium | 4 / 0 |
| BR-15 | EG | — | Medium | 4 / 0 |
| BR-16 | EG | — | High | 6 / 1 |
| BR-17 | UC | — | Low | 1 / 0 |
| BR-18 | EP | BVA | Medium | 4 / 6 |

- **Môi trường:** UAT SHSmart Web; Chrome bản mới nhất (chính) + Edge, Firefox, Safari; DevTools; Proxyman/Charles; Postman; tài khoản BO Portal.
- **Exit criteria:** Chạy hết TC High; không còn bug Critical/Major mở; Q&A Major đã có trả lời (hoặc PO chấp nhận giả định); mọi BR có TC Pass.

### Dữ liệu cần có trên môi trường
| Mã | Điều kiện dữ liệu | Dùng cho |
|---|---|---|
| TK1 | Tài khoản chính, đăng nhập được SHSmart Web UAT, có 3 mã SH Bond đang nắm giữ (Mã A, B, C) + Mã D chỉ có HĐ đã tất toán | Hầu hết TC |
| Mã A | BHB291193 · CTCP Năng lượng Bắc Hà · Riêng lẻ · Mệnh giá 100.000.000<br>- HĐ-A1 Hiệu lực: 10 TP, tiền đầu tư 1.000.000.000, lãi dự kiến 50.000.000, nhận về 1.050.000.000<br>- HĐ-A2 Hiệu lực: 5 TP, tiền đầu tư 500.000.000, lãi dự kiến 25.000.000, nhận về 525.000.000<br>- HĐ-A3 Đã tất toán: 3 TP<br>→ Kỳ vọng dòng: SL HĐ 2 · SL TP 15 · Tổng MG 1.500.000.000 · Tổng đầu tư 1.500.000.000 · Tổng thực nhận 1.575.000.000 | Gộp nhiều HĐ, công thức |
| Mã B | TPQ12312 · Công ty bảo hiểm nhân thọ ABC · Niêm yết · Mệnh giá 100.000<br>- HĐ-B1 Hiệu lực: 1.000 TP, tiền đầu tư 98.500.000, lãi dự kiến 8.000.000, nhận về 106.500.000<br>→ Kỳ vọng dòng: SL HĐ 1 · SL TP 1.000 · Tổng MG 100.000.000 · Tổng đầu tư 98.500.000 · Tổng thực nhận 106.500.000 | 1 HĐ, MG ≠ tiền đầu tư, Niêm yết |
| Mã C | ABC12601 · Công ty bảo hiểm nhân thọ ABC · Riêng lẻ · Mệnh giá 1.000.000<br>- HĐ-C1 Hiệu lực: 50 TP, tiền đầu tư 50.000.000, nhận về 53.000.000 | Lọc TCPH 1 tổ chức có 2 mã |
| Mã D | XYZ12599 · CTCP Đầu tư XYZ · Riêng lẻ · chỉ có HĐ-D1 đã tất toán | Mã không còn nắm giữ |
| TK2 | Tài khoản không có HĐ SH Bond nào (có thể có Trái phiếu linh hoạt) | Empty state |
| TK3 | Tài khoản có 11 mã SH Bond, mỗi mã 1 HĐ, Tổng tiền thực nhận khác nhau từng mã | Phân trang, sắp xếp |
| TK5 | Tài khoản khác, có Mã E = DEF12602 (TK1 không có mã này) | Phân quyền |
| Công cụ | Chrome bản mới nhất + DevTools (Network, throttling Offline); Proxyman/Charles để sửa response; Postman; tài khoản BO Portal để đổi giá giao dịch / trạng thái HĐ | — |
| Lưu ý | Mã, tên TCPH, số tiền là VD. Tester thay bằng dữ liệu thật trên UAT nhưng giữ đúng cấu trúc (số HĐ, trạng thái, MG ≠ tiền đầu tư) rồi tính lại kỳ vọng | — |

### Precondition / thao tác dùng chung
| Mã | Nội dung |
|---|---|
| PC-01 | TK1 đã đăng nhập SHSmart Web UAT, vào "Sản phẩm đầu tư" > "Danh sách nắm giữ" > "Danh mục trái phiếu" > chip "SH Bond"; danh sách đã tải xong, bộ lọc mặc định |
| PC-02 | PC-01 + mở DevTools tab Network (lọc "bond") |
| PC-03 | Postman, header Authorization = Bearer <token TK1> lấy từ request S6 trên Web |
| SOP-01 | Đối chiếu số liệu: mở "Danh mục hợp đồng" của TK1, lọc theo mã TP, xem Chi tiết từng HĐ trạng thái "Hiệu lực"; cộng tay các chỉ tiêu để so với dòng của mã |
| SOP-02 | Giả lập lỗi: dùng Proxyman sửa response S6 thành mã lỗi cần test (BND-901 / BND-999 / AUTH_001) |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên | Condition |
|---|---|---|---|---|
| Từ khóa Mã TP | Đủ mã; một phần mã; chữ thường; cụm từ trong tên TP; có khoảng trắng 2 đầu | Không khớp; chỉ khoảng trắng; script / SQL | 49 / 50 / 51 ký tự; dán 60 ký tự | TCO-BR07-01 → 09 |
| TCPH | Không chọn (tất cả); 1 TCPH; 2 TCPH | TCPH không có TP đang nắm giữ (không được có trong danh sách) | 0 / 1 / n tổ chức | TCO-BR08-01 → 04 |
| Loại TP | Tất cả; Niêm yết; Riêng lẻ; cả 2 | — | — | TCO-BR09-01 → 04 |
| HĐ của mã | 1 HĐ Hiệu lực; nhiều HĐ Hiệu lực | HĐ đã tất toán (không tính); mã chỉ còn HĐ tất toán (không hiện) | 2 HĐ → tất toán 1 còn 1 | TCO-BR03-01 → 04 |
| Phân trang (10/trang) | page trong phạm vi | page = -1; size = 0 | 3 mã (1 trang); 11 mã (2 trang); page = 999 | TCO-BR11-01 → 04 |
| Số tiền | Giá trị thường | — | Rất lớn 999.999.999.999.999 | TCO-BR02-03 |

### Decision table – kết hợp bộ lọc (dữ liệu TK1)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Mã TP | — | BHB291193 | — | — | — |
| TCPH | — | — | Công ty BHNT ABC | Công ty BHNT ABC | CTCP NL Bắc Hà |
| Loại TP | — | — | — | Riêng lẻ | Niêm yết |
| **Kết quả** | 3 dòng (A, B, C) | A | B, C | C | Empty "Không tìm thấy kết quả phù hợp" |
| TC | DS_001 | TK_001 | TC_002 | LC_001 | LC_002 |

### State transition – dòng của 1 mã TP (theo trạng thái HĐ)
| Trạng thái mã \ Sự kiện | Tất toán 1 HĐ (còn HĐ khác) | Tất toán HĐ cuối cùng |
|---|---|---|
| Có ≥ 2 HĐ Hiệu lực | Giảm SL HĐ và số liệu tương ứng (DS_005) | — |
| Có 1 HĐ Hiệu lực | — | Mã biến mất khỏi bảng và gợi ý (DS_004) |

### Use case
- Luồng chính: Mở SH Bond → xem bảng gộp → bấm "Chi tiết" → "Xem danh mục hợp đồng" (DS_001, DS_003, CTI_001, CTI_002).
- Luồng phụ: lọc từng điều kiện, kết hợp, đặt lại; đổi trang; sắp xếp; đóng popup.
- Luồng lỗi: không có TP; lọc không ra; BND-901 / BND-999 / AUTH_001; mất mạng; chưa đăng nhập; tài khoản khác.

### Error guessing áp dụng
- Ô tìm kiếm: khoảng trắng, chữ thường, script / SQL, dán chuỗi dài · Bộ lọc: đổi nhanh liên tiếp, đổi khi đang ở trang 2 · Số liệu: số rất lớn, MG ≠ tiền đầu tư, thị giá đổi, có thuế · Hệ thống: mạng chậm, Offline, hết phiên, đổi tài khoản, gọi API thẳng · Trình duyệt / độ phân giải.

## B4. Test case
### 4.1 Web (59 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMTPW_UI_001 | BR-01, BR-08, BR-09 | Danh mục TP SH Bond – Giao diện | Kiểm tra giao diện mặc định khi mở tab SH Bond | PC-01 | 1. Quan sát màn hình | TK1 | - Tab "Danh mục trái phiếu" và chip "SH Bond" được chọn (màu cam)<br>- Ô tìm kiếm trống, placeholder "Mã trái phiếu"<br>- Dropdown TCPH hiện "Tất cả tổ chức"; dropdown Loại TP hiện "Tất cả"<br>- Bảng hiện 3 dòng: BHB291193, TPQ12312, ABC12601 | Low | UI | EG | [GIẢ ĐỊNH] chữ mặc định theo SRS – Q-11 |
| TC_DMTPW_UI_002 | BR-02, BR-12 | Danh mục TP SH Bond – Giao diện | Kiểm tra tiêu đề và thứ tự cột của bảng khi có dữ liệu | PC-01 | 1. Quan sát hàng tiêu đề bảng | TK1 | - 8 cột theo thứ tự: Mã trái phiếu · Tổ chức phát hành · SL Hợp đồng · SL Trái phiếu · Tổng mệnh giá · Tổng tiền đầu tư · Tổng tiền thực nhận · Thao tác<br>- Chỉ cột "Tổng tiền thực nhận" có icon sắp xếp ↑↓<br>- Mỗi dòng có nút "Chi tiết" ở cột Thao tác | Low | UI | EG | Đối chiếu Figma Web |
| TC_DMTPW_UI_003 | BR-02 | Danh mục TP SH Bond – Giao diện | Kiểm tra định dạng và màu các cột số khi có dữ liệu | PC-01 | 1. Quan sát dòng BHB291193 | Mã A | - SL Hợp đồng "2"; SL Trái phiếu "15"<br>- Tổng mệnh giá "1.500.000.000"; phân cách hàng nghìn bằng dấu chấm, không có số thập phân<br>- Tổng tiền đầu tư màu cam; Tổng tiền thực nhận "1.575.000.000" màu xanh<br>- Các cột số căn phải | Low | UI | EG | [GIẢ ĐỊNH] màu theo SRS, không chữ "VND" theo Figma – Q-11 |
| TC_DMTPW_UI_004 | BR-14 | Danh mục TP SH Bond – Giao diện | Kiểm tra skeleton khi danh sách đang tải | TK1 đã đăng nhập; DevTools Network đặt "Slow 3G" | 1. Bấm chip "Trái phiếu linh hoạt"<br>2. Bấm lại chip "SH Bond"<br>3. Quan sát trong lúc chờ S6 trả về | TK1 | - Trong lúc chờ: vùng bảng hiện skeleton, không hiện dữ liệu cũ hay empty state<br>- S6 trả về: skeleton biến mất, hiện 3 dòng của TK1 | Low | UI | EG |  |
| TC_DMTPW_UI_005 | BR-02 | Danh mục TP SH Bond – Giao diện | Kiểm tra hiển thị khi tên TCPH rất dài | PC-01; BO sửa tên TCPH của Mã A thành chuỗi 150 ký tự (hoặc Proxyman sửa issuerName) | 1. Quan sát cột "Tổ chức phát hành" dòng BHB291193 | issuerName = "Công ty Cổ phần Tập đoàn Đầu tư Phát triển Năng lượng Tái tạo và Hạ tầng Kỹ thuật Đô thị Bắc Hà Miền Bắc Việt Nam Chi nhánh Hà Nội Số 1 Khu vực Phía Bắc" | - Tên xuống dòng hoặc cắt "…" có tooltip tên đầy đủ<br>- Không đè sang cột "SL Hợp đồng"; chiều rộng các cột khác không bị vỡ | Low | UI | EG | SRS không ghi cách cắt chữ – đối chiếu Figma |
| TC_DMTPW_UI_006 | BR-02 | Danh mục TP SH Bond – Giao diện | Kiểm tra bố cục màn khi đổi trình duyệt và độ phân giải | TK1 đã đăng nhập | 1. Mở tab SH Bond lần lượt trên Chrome, Edge, Firefox, Safari (macOS)<br>2. Với mỗi trình duyệt đổi cửa sổ 1366×768 và 1920×1080 | TK1 | - Ở cả 8 tổ hợp: đủ 8 cột, không mất nút "Chi tiết", không có thanh cuộn ngang ở 1920<br>- Ở 1366: nếu bảng rộng hơn màn thì cuộn ngang được, tiêu đề cột vẫn khớp cột | Low | UI | PW | Bổ sung B5 |
| TC_DMTPW_DS_001 | BR-01, BR-18 | Danh mục TP SH Bond – Hiển thị & gộp | Kiểm tra request khi mở tab SH Bond | TK1 đã đăng nhập; DevTools Network mở | 1. Vào "Danh sách nắm giữ" > "Danh mục trái phiếu"<br>2. Xem các request vừa gửi | TK1 | - Chip "SH Bond" được chọn sẵn<br>- Có 1 request GET /api/v1/bond/app/bond-holdings không có symbol, issuerCode, loại TP (lọc tất cả), page = trang đầu<br>- Có 1 request GET /api/v1/bond/common/issuer/list<br>- Cả 2 trả HTTP 200 | High | Positive | UC |  |
| TC_DMTPW_DS_002 | BR-02, BR-03 | Danh mục TP SH Bond – Hiển thị & gộp | Kiểm tra dòng hiển thị khi mã TP chỉ có 1 HĐ | PC-01 | 1. Xem dòng TPQ12312<br>2. Làm SOP-01 với mã TPQ12312 | Mã B | - Dòng hiện: TPQ12312 · Công ty bảo hiểm nhân thọ ABC · 1 · 1.000 · 100.000.000 · 98.500.000 · 106.500.000<br>- Khớp đúng số trên Chi tiết HĐ-B1 | High | Positive | EP |  |
| TC_DMTPW_DS_003 | BR-03 | Danh mục TP SH Bond – Hiển thị & gộp | Kiểm tra gộp và cộng dồn khi mã TP có nhiều HĐ đang nắm giữ | PC-01 | 1. Đếm số dòng có Mã TP = BHB291193<br>2. Xem các cột của dòng đó<br>3. Làm SOP-01 với mã BHB291193 | Mã A (HĐ-A1, HĐ-A2 Hiệu lực; HĐ-A3 Đã tất toán) | - Chỉ có 1 dòng BHB291193<br>- SL HĐ 2 · SL TP 15 · Tổng MG 1.500.000.000 · Tổng đầu tư 1.500.000.000 · Tổng thực nhận 1.575.000.000<br>- Bằng tổng của HĐ-A1 + HĐ-A2; HĐ-A3 không được cộng | High | Positive | EP | [GIẢ ĐỊNH] chỉ tính HĐ "Hiệu lực" – Q-09 |
| TC_DMTPW_DS_004 | BR-03 | Danh mục TP SH Bond – Hiển thị & gộp | Kiểm tra mã TP không hiện khi mọi HĐ của mã đã tất toán | PC-01 | 1. Tìm dòng XYZ12599 trong bảng<br>2. Gõ "XYZ12599" vào ô "Mã trái phiếu" | Mã D | - Bảng không có dòng XYZ12599<br>- Danh sách gợi ý không có XYZ12599 | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-09 |
| TC_DMTPW_DS_005 | BR-03 | Danh mục TP SH Bond – Hiển thị & gộp | Kiểm tra số liệu cập nhật khi 1 HĐ của mã vừa được tất toán | TK1 đang xem dòng BHB291193 (SL HĐ 2); BO tất toán HĐ-A2 | 1. Bấm F5 tải lại trang<br>2. Xem dòng BHB291193 | Mã A, HĐ-A2 | SL HĐ 1 · SL TP 10 · Tổng MG 1.000.000.000 · Tổng đầu tư 1.000.000.000 · Tổng thực nhận 1.050.000.000 | Medium | Positive | ST | [GIẢ ĐỊNH] – Q-09; chạy sau cùng vì đổi dữ liệu Mã A; Bổ sung B5 |
| TC_DMTPW_CT_001 | BR-04 | Danh mục TP SH Bond – Công thức số liệu | Kiểm tra Tổng mệnh giá khi giá mua khác mệnh giá | PC-01 | 1. Xem cột Tổng mệnh giá và Tổng tiền đầu tư dòng TPQ12312 | Mã B: 1.000 TP × MG 100.000; tiền đầu tư 98.500.000 | - Tổng mệnh giá = 1.000 × 100.000 = 100.000.000<br>- Khác Tổng tiền đầu tư (98.500.000) | High | Positive | EP |  |
| TC_DMTPW_CT_002 | BR-04 | Danh mục TP SH Bond – Công thức số liệu | Kiểm tra Tổng mệnh giá không đổi khi giá giao dịch của mã thay đổi | PC-01; ghi lại Tổng MG dòng TPQ12312 = 100.000.000 | 1. BO đổi giá giao dịch / thị giá của TPQ12312 từ 98.500 lên 102.000<br>2. Bấm F5 trên Web<br>3. Xem dòng TPQ12312 | Mã B | - Tổng mệnh giá vẫn 100.000.000<br>- Tổng tiền đầu tư vẫn 98.500.000 | High | Negative | EP |  |
| TC_DMTPW_CT_003 | BR-06 | Danh mục TP SH Bond – Công thức số liệu | Kiểm tra Tổng tiền đầu tư khi mã có nhiều HĐ | PC-01 | 1. Làm SOP-01 với BHB291193, ghi "Tiền đầu tư" của từng HĐ Hiệu lực<br>2. So với cột Tổng tiền đầu tư | Mã A | Tổng tiền đầu tư = 1.000.000.000 + 500.000.000 = 1.500.000.000 | High | Positive | EP | [GIẢ ĐỊNH] công thức – Q-06 |
| TC_DMTPW_CT_004 | BR-05 | Danh mục TP SH Bond – Công thức số liệu | Kiểm tra Tổng tiền thực nhận là gốc cộng lãi dự kiến chưa trừ thuế | PC-01 | 1. Làm SOP-01 với BHB291193, ghi gốc và lãi dự kiến từng HĐ<br>2. So với cột Tổng tiền thực nhận | Mã A: gốc 1.000.000.000 + 500.000.000; lãi 50.000.000 + 25.000.000 | - Tổng tiền thực nhận = 1.575.000.000<br>- Không bị trừ thuế TNCN (nếu trừ 5% lãi sẽ ra 1.571.250.000 → sai) | High | Positive | EP |  |
| TC_DMTPW_CT_005 | BR-02, BR-03 | Danh mục TP SH Bond – Công thức số liệu | Kiểm tra hiển thị khi tổng tiền rất lớn | PC-01; Proxyman sửa response S6 của dòng BHB291193 | 1. Sửa totalFaceValue = 999999999999999, totalAmountReceived = 1050000000000000<br>2. Tải lại trang | totalFaceValue = 999999999999999; totalAmountReceived = 1050000000000000 | - Hiện đủ "999.999.999.999.999" và "1.050.000.000.000.000"<br>- Không hiện dạng 1e+15, không làm tròn, không tràn sang cột bên cạnh | Low | Boundary | BVA |  |
| TC_DMTPW_TK_001 | BR-07, BR-10 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra lọc theo mã khi nhập đủ mã và chọn gợi ý | PC-02 | 1. Gõ "BHB291193" vào ô "Mã trái phiếu"<br>2. Chọn BHB291193 trong gợi ý | Từ khóa = BHB291193 | - Gợi ý có BHB291193<br>- Sau khi chọn: bảng chỉ còn 1 dòng BHB291193<br>- Request S6 có symbol = BHB291193 | Medium | Positive | EP | [GIẢ ĐỊNH] phải chọn gợi ý mới lọc – Q-04 |
| TC_DMTPW_TK_002 | BR-07 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra gợi ý khi nhập một phần ở giữa mã | PC-01 | 1. Gõ "2911" vào ô "Mã trái phiếu" | Từ khóa = 2911 | - Gợi ý có BHB291193<br>- Không có TPQ12312, ABC12601 | Medium | Positive | EP |  |
| TC_DMTPW_TK_003 | BR-07 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra gợi ý khi nhập chữ thường | PC-01 | 1. Gõ "tpq123" vào ô "Mã trái phiếu" | Từ khóa = tpq123 | Gợi ý có TPQ12312 (không phân biệt hoa thường) | Medium | Positive | EP |  |
| TC_DMTPW_TK_004 | BR-07 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra gợi ý khi nhập cụm từ trong tên TP | PC-01; biết tên TP của Mã A trên Portal (VD "Trái phiếu Năng lượng Bắc Hà 2029") | 1. Gõ "năng lượng" vào ô "Mã trái phiếu" | Từ khóa = năng lượng | Gợi ý có BHB291193 | Medium | Positive | EP | [GIẢ ĐỊNH] gợi ý tìm cả theo tên TP – Q-04 |
| TC_DMTPW_TK_005 | BR-07 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra từ khóa có khoảng trắng 2 đầu | PC-02 | 1. Gõ "  BHB291193  " (2 dấu cách mỗi đầu)<br>2. Chọn gợi ý BHB291193 | Từ khóa = "  BHB291193  " | - Gợi ý có BHB291193 (khoảng trắng bị cắt)<br>- Request S6 gửi symbol = "BHB291193", không có khoảng trắng | Medium | Positive | EP | V01 |
| TC_DMTPW_TK_006 | BR-07 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra xóa trống ô tìm kiếm khi đang lọc theo mã | PC-01; đang lọc BHB291193 (TC_DMTPW_TK_001) | 1. Bấm icon "x" hoặc xóa hết chữ trong ô "Mã trái phiếu" | Từ khóa = (trống) | - Bảng hiện lại đủ 3 dòng của TK1<br>- Request S6 không còn symbol | Medium | Positive | EP |  |
| TC_DMTPW_TK_007 | BR-07 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra biên độ dài từ khóa 49, 50, 51 ký tự | PC-01 | 1. Gõ chuỗi 49 ký tự<br>2. Gõ thêm 1 ký tự (50)<br>3. Gõ thêm 1 ký tự (51)<br>4. Xóa hết, dán chuỗi 60 ký tự | 49 ký tự = "BHB291193BHB291193BHB291193BHB291193BHB291193ABCD"; 50 = thêm "E"; 51 = thêm "F"; 60 = "BHB291193" lặp | - 49 và 50 ký tự: nhập được<br>- Ký tự thứ 51 không vào ô (ô giữ 50 ký tự)<br>- Dán 60 ký tự: ô chỉ giữ 50 ký tự đầu<br>- Không hiện popup lỗi | Medium | Boundary | BVA | [GIẢ ĐỊNH] max 50, dán thì cắt – Q-05 |
| TC_DMTPW_TK_008 | BR-07, BR-14 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra tìm kiếm khi từ khóa không khớp mã nào | PC-01 | 1. Gõ "ZZZ999" vào ô "Mã trái phiếu"<br>2. Bấm Enter | Từ khóa = ZZZ999 | - Gợi ý trống (hoặc ghi "Không có kết quả")<br>- Bảng không đổi khi chưa chọn gợi ý, hoặc hiện empty state "Không tìm thấy kết quả phù hợp" nếu Enter có lọc<br>- Không hiện popup lỗi | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-04, Q-13 |
| TC_DMTPW_TK_009 | BR-07 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra từ khóa chỉ gồm khoảng trắng | PC-02 | 1. Gõ 5 dấu cách vào ô "Mã trái phiếu"<br>2. Bấm Enter | Từ khóa = "     " | - Không hiện gợi ý<br>- Bảng vẫn đủ 3 dòng; không gửi S6 có symbol rỗng / khoảng trắng | Low | Negative | EP | V01; Bổ sung B5 |
| TC_DMTPW_TK_010 | BR-07, BR-16 | Danh mục TP SH Bond – Tìm Mã TP | Kiểm tra ô tìm kiếm khi nhập mã script và SQL | PC-01 | 1. Gõ chuỗi 1, xem màn hình<br>2. Xóa, gõ chuỗi 2, xem màn hình | Chuỗi 1 = <script>alert(1)</script>; Chuỗi 2 = ' OR 1=1 -- | - Không bật hộp thoại alert; chuỗi hiện đúng dạng chữ trong ô<br>- Gợi ý trống; bảng không hiện thêm mã ngoài 3 mã của TK1<br>- Không có popup BND-999 | Medium | Security | EG |  |
| TC_DMTPW_TC_001 | BR-08, BR-18 | Danh mục TP SH Bond – Lọc TCPH | Kiểm tra danh sách TCPH khi mở dropdown | PC-01 | 1. Bấm dropdown "Tổ chức phát hành" | TK1 | - Hiện 2 lựa chọn có checkbox: CTCP Năng lượng Bắc Hà; Công ty bảo hiểm nhân thọ ABC<br>- Không có "CTCP Đầu tư XYZ" (Mã D đã tất toán)<br>- Chưa có ô nào được tick | Low | UI | EP | [GIẢ ĐỊNH] chỉ TCPH đang nắm giữ – Q-12 |
| TC_DMTPW_TC_002 | BR-08, BR-10 | Danh mục TP SH Bond – Lọc TCPH | Kiểm tra lọc khi chọn 1 TCPH | PC-02 | 1. Mở dropdown "Tổ chức phát hành"<br>2. Tick "Công ty bảo hiểm nhân thọ ABC" | TCPH = Công ty bảo hiểm nhân thọ ABC | - Dropdown hiện "Đã chọn 1 tổ chức"<br>- Bảng còn 2 dòng: TPQ12312, ABC12601<br>- Request S6 có issuerCode của Công ty bảo hiểm nhân thọ ABC | Medium | Positive | EP |  |
| TC_DMTPW_TC_003 | BR-08 | Danh mục TP SH Bond – Lọc TCPH | Kiểm tra lọc khi chọn 2 TCPH | PC-02 | 1. Mở dropdown "Tổ chức phát hành"<br>2. Tick cả 2 TCPH | TCPH = CTCP Năng lượng Bắc Hà + Công ty bảo hiểm nhân thọ ABC | - Dropdown hiện "Đã chọn 2 tổ chức"<br>- Bảng đủ 3 dòng BHB291193, TPQ12312, ABC12601<br>- Request S6 gửi 2 issuerCode | Medium | Positive | EP |  |
| TC_DMTPW_TC_004 | BR-08 | Danh mục TP SH Bond – Lọc TCPH | Kiểm tra bỏ tick hết TCPH khi đang lọc | PC-01; đang lọc 1 TCPH (TC_DMTPW_TC_002) | 1. Mở dropdown "Tổ chức phát hành"<br>2. Bỏ tick "Công ty bảo hiểm nhân thọ ABC" | TCPH = (không chọn) | - Dropdown về "Tất cả tổ chức"<br>- Bảng hiện lại đủ 3 dòng<br>- Request S6 không còn issuerCode | Medium | Positive | EP |  |
| TC_DMTPW_LT_001 | BR-09 | Danh mục TP SH Bond – Lọc Loại TP | Kiểm tra lựa chọn khi mở dropdown Loại TP | PC-01 | 1. Bấm dropdown "Loại trái phiếu" | TK1 | - Hiện 3 lựa chọn: Tất cả · Niêm yết · Riêng lẻ<br>- "Tất cả" đang được chọn | Low | UI | EP | Đối chiếu Figma Web |
| TC_DMTPW_LT_002 | BR-09, BR-10 | Danh mục TP SH Bond – Lọc Loại TP | Kiểm tra lọc khi chọn Niêm yết | PC-02 | 1. Mở dropdown "Loại trái phiếu"<br>2. Chọn "Niêm yết"<br>3. Bấm "Chi tiết" dòng còn lại, xem "Loại trái phiếu" | Loại TP = Niêm yết | - Bảng chỉ còn TPQ12312<br>- Popup Chi tiết ghi loại Niêm yết<br>- Request S6 có tham số loại TP = Niêm yết | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-03 |
| TC_DMTPW_LT_003 | BR-09 | Danh mục TP SH Bond – Lọc Loại TP | Kiểm tra lọc khi chọn Riêng lẻ | PC-02 | 1. Mở dropdown "Loại trái phiếu"<br>2. Chọn "Riêng lẻ" | Loại TP = Riêng lẻ | - Bảng còn 2 dòng: BHB291193, ABC12601<br>- Không có TPQ12312 | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-03 |
| TC_DMTPW_LT_004 | BR-09 | Danh mục TP SH Bond – Lọc Loại TP | Kiểm tra lọc khi chọn cả Niêm yết và Riêng lẻ | PC-01 | 1. Mở dropdown "Loại trái phiếu"<br>2. Chọn "Niêm yết"<br>3. Chọn thêm "Riêng lẻ" | Loại TP = Niêm yết + Riêng lẻ | - Bảng đủ 3 dòng<br>- Dropdown hiện 2 loại đã chọn (hoặc tự về "Tất cả") | Low | Positive | EP | [GIẢ ĐỊNH] – Q-03 |
| TC_DMTPW_LC_001 | BR-10 | Danh mục TP SH Bond – Kết hợp bộ lọc | Kiểm tra kết hợp TCPH và Loại TP | PC-02 | 1. Chọn TCPH "Công ty bảo hiểm nhân thọ ABC"<br>2. Chọn Loại TP "Riêng lẻ" | TCPH = Công ty bảo hiểm nhân thọ ABC; Loại = Riêng lẻ | - Bảng chỉ còn ABC12601 (thỏa cả 2 điều kiện)<br>- Request S6 cuối có đủ issuerCode và loại TP | Medium | Positive | DT | [GIẢ ĐỊNH] AND, lọc ngay khi chọn – Q-02, Q-10 |
| TC_DMTPW_LC_002 | BR-10, BR-14 | Danh mục TP SH Bond – Kết hợp bộ lọc | Kiểm tra kết hợp bộ lọc không có mã nào thỏa | PC-01 | 1. Chọn TCPH "CTCP Năng lượng Bắc Hà"<br>2. Chọn Loại TP "Niêm yết" | TCPH = CTCP Năng lượng Bắc Hà; Loại = Niêm yết | - Bảng hiện empty state "Không tìm thấy kết quả phù hợp"<br>- Không hiện phân trang; không hiện popup lỗi | Medium | Negative | DT | [GIẢ ĐỊNH] câu chữ – Q-13 |
| TC_DMTPW_LC_003 | BR-10 | Danh mục TP SH Bond – Kết hợp bộ lọc | Kiểm tra Đặt lại khi đang áp dụng cả 3 bộ lọc | PC-02; đã lọc Mã ABC12601 + TCPH ABC + Loại Riêng lẻ | 1. Bấm "Đặt lại" (hoặc bỏ hết 3 điều kiện nếu không có nút) | TK1 | - Ô tìm kiếm trống; TCPH "Tất cả tổ chức"; Loại TP "Tất cả"<br>- Bảng hiện lại đủ 3 dòng<br>- Request S6 giống lúc mở màn | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-02 |
| TC_DMTPW_LC_004 | BR-10, BR-11 | Danh mục TP SH Bond – Kết hợp bộ lọc | Kiểm tra đổi bộ lọc khi đang ở trang 2 | TK3 đã đăng nhập, ở tab SH Bond, đang xem trang 2; DevTools mở | 1. Chọn 1 TCPH bất kỳ có ≥ 1 mã | TK3 | - Bảng về trang 1, nút trang "1" được chọn<br>- Request S6 gửi page = trang đầu | Medium | Boundary | EG | [GIẢ ĐỊNH] – Q-10 |
| TC_DMTPW_LC_005 | BR-10 | Danh mục TP SH Bond – Kết hợp bộ lọc | Kiểm tra kết quả khi đổi bộ lọc liên tiếp rất nhanh | PC-02; DevTools đặt "Fast 3G" | 1. Trong 2 giây: chọn Loại "Niêm yết", đổi sang "Riêng lẻ", rồi chọn TCPH "CTCP Năng lượng Bắc Hà"<br>2. Chờ tải xong | TK1 | - Bảng cuối cùng chỉ có BHB291193 (khớp điều kiện cuối)<br>- Không bị kết quả của request cũ đè lên | Medium | Negative | EG | [GIẢ ĐỊNH] lọc ngay khi chọn – Q-02; Bổ sung B5 |
| TC_DMTPW_PT_001 | BR-11 | Danh mục TP SH Bond – Phân trang | Kiểm tra phân trang khi có 11 mã | TK3 đã đăng nhập, ở tab SH Bond | 1. Xem trang 1<br>2. Bấm "Sau" | TK3 (11 mã) | - Trang 1: 10 dòng; có nút trang 1, 2; "Trước" bị khóa<br>- Trang 2: 1 dòng; "Sau" bị khóa<br>- Không có mã nào lặp giữa 2 trang; tổng 11 mã | Medium | Boundary | BVA | [GIẢ ĐỊNH] 10 dòng/trang – Q-07 |
| TC_DMTPW_PT_002 | BR-11, BR-18 | Danh mục TP SH Bond – Phân trang | Kiểm tra request khi bấm số trang | TK3 ở tab SH Bond; DevTools mở | 1. Bấm nút trang "2"<br>2. Bấm "Trước" | TK3 | - Bấm "2": gửi S6 page = 2, size = 10; nút "2" được chọn<br>- Bấm "Trước": về trang 1, dữ liệu giống lần đầu<br>- totalItems = 11 | Medium | Positive | EP | Kiểm page bắt đầu từ 0 hay 1 theo Dev |
| TC_DMTPW_PT_003 | BR-11 | Danh mục TP SH Bond – Phân trang | Kiểm tra phân trang khi chỉ có 3 mã | PC-01 | 1. Xem vùng phân trang | TK1 (3 mã) | Chỉ có trang "1"; "Trước" và "Sau" bị khóa (hoặc ẩn thanh phân trang) | Low | Boundary | BVA | [GIẢ ĐỊNH] – Q-07 |
| TC_DMTPW_PT_004 | BR-11 | Danh mục TP SH Bond – Phân trang | Kiểm tra dấu "…" khi có nhiều trang | TK3 ở tab SH Bond; Proxyman sửa totalItems = 100 | 1. Xem vùng phân trang<br>2. Bấm trang "5" (nếu hiện) hoặc "Sau" tới trang 5 | totalItems = 100 | - Trang 1: hiện "1 2 3 … 8 9 10" như Figma<br>- Ở trang 5: hiện trang 5 và các trang gần nó, có "…" 2 bên | Low | UI | EG | Chỉ kiểm hiển thị; dữ liệu giả |
| TC_DMTPW_SX_001 | BR-12 | Danh mục TP SH Bond – Sắp xếp | Kiểm tra sắp xếp giảm dần Tổng tiền thực nhận | TK3 ở tab SH Bond; DevTools mở | 1. Bấm icon sắp xếp cột "Tổng tiền thực nhận"<br>2. Xem trang 1 rồi trang 2 | TK3 | - Request S6 có tham số sort giảm dần<br>- Trang 1 và trang 2 nối tiếp giảm dần: dòng cuối trang 1 ≥ dòng trang 2<br>- Mã có Tổng thực nhận lớn nhất nằm đầu trang 1 | Medium | Positive | EP | [GIẢ ĐỊNH] sắp xếp toàn bộ, lần 1 = ↓ – Q-08 |
| TC_DMTPW_SX_002 | BR-12 | Danh mục TP SH Bond – Sắp xếp | Kiểm tra sắp xếp tăng dần khi bấm icon lần 2 | TK3 đang sắp xếp giảm dần (TC_DMTPW_SX_001) | 1. Bấm icon sắp xếp lần nữa | TK3 | - Mã có Tổng thực nhận nhỏ nhất nằm đầu trang 1; các dòng tăng dần<br>- Icon chuyển sang ↑ | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-08 |
| TC_DMTPW_SX_003 | BR-12, BR-10 | Danh mục TP SH Bond – Sắp xếp | Kiểm tra sắp xếp khi đang lọc theo TCPH | TK3 đang sắp xếp giảm dần | 1. Chọn 1 TCPH có ≥ 3 mã | TK3 | - Kết quả lọc vẫn giảm dần theo Tổng tiền thực nhận<br>- Request S6 có cả issuerCode và sort | Low | Positive | EG | [GIẢ ĐỊNH] – Q-08 |
| TC_DMTPW_CTI_001 | BR-13 | Danh mục TP SH Bond – Chi tiết | Kiểm tra mở popup Chi tiết của đúng mã | PC-01 | 1. Bấm "Chi tiết" dòng BHB291193 | Mã A | - Mở popup "Chi tiết trái phiếu"<br>- Mã trái phiếu BHB291193; Tổ chức phát hành CTCP Năng lượng Bắc Hà; Loại Riêng lẻ; Mệnh giá 100.000.000<br>- Có nút "Đóng" và "Xem danh mục hợp đồng" | High | Positive | UC | Nội dung popup kiểm chi tiết ở HOLDING.WEB.02 |
| TC_DMTPW_CTI_002 | BR-13, BR-03 | Danh mục TP SH Bond – Chi tiết | Kiểm tra đi sang Danh mục HĐ từ popup Chi tiết | PC-01; đang mở popup Chi tiết BHB291193 | 1. Bấm "Xem danh mục hợp đồng" | Mã A | - Chuyển sang tab "Danh mục hợp đồng", đã lọc theo mã BHB291193<br>- Hiện đúng 2 HĐ: HĐ-A1, HĐ-A2 (khớp SL HĐ = 2) | High | Integration | UC |  |
| TC_DMTPW_CTI_003 | BR-13 | Danh mục TP SH Bond – Chi tiết | Kiểm tra đóng popup Chi tiết khi đang lọc ở trang 2 | TK3 đang lọc 1 TCPH, sắp xếp ↓, ở trang 2 | 1. Bấm "Chi tiết" 1 dòng<br>2. Bấm "Đóng" | TK3 | - Popup đóng; vẫn ở trang 2 với đúng bộ lọc và sắp xếp cũ<br>- Không gửi lại S6 | Low | Positive | EG | Quay lại từ Danh mục HĐ: xem Q-15; Bổ sung B5 |
| TC_DMTPW_ER_001 | BR-14 | Danh mục TP SH Bond – Trống & lỗi | Kiểm tra empty state khi KH chưa có TP SH Bond | TK2 đã đăng nhập | 1. Vào "Danh mục trái phiếu" > "SH Bond" | TK2 | - Hiện empty state "Quý khách chưa có trái phiếu SH Bond"<br>- Không hiện bảng dữ liệu, không hiện phân trang; không hiện popup lỗi<br>- S6 trả HTTP 200, data.content = [] | Medium | Negative | EP | [GIẢ ĐỊNH] câu chữ – Q-13 |
| TC_DMTPW_ER_002 | BR-15 | Danh mục TP SH Bond – Trống & lỗi | Kiểm tra popup khi S6 trả BND-901 | PC-01; làm SOP-02 với BND-901 | 1. Bấm F5 tải lại trang<br>2. Bấm "Đóng" trên popup | Error code = BND-901 | - Hiện popup lỗi chuẩn "Hệ thống đang bận", nút "Đóng"<br>- Sau khi Đóng: không hiện số liệu cũ hay số 0 giả trong bảng | Medium | Negative | EG | [GIẢ ĐỊNH] ánh xạ mã → câu chữ theo thứ tự SRS |
| TC_DMTPW_ER_003 | BR-15 | Danh mục TP SH Bond – Trống & lỗi | Kiểm tra popup khi S6 trả BND-999 | PC-01; làm SOP-02 với BND-999 | 1. Chọn Loại TP "Niêm yết" | Error code = BND-999 | - Hiện popup "Có lỗi xảy ra. Vui lòng thử lại sau", nút "Đóng"<br>- Bảng không hiện kết quả lọc sai | Medium | Negative | EG | [GIẢ ĐỊNH] ánh xạ mã → câu chữ |
| TC_DMTPW_ER_004 | BR-15, BR-16 | Danh mục TP SH Bond – Trống & lỗi | Kiểm tra popup khi phiên đăng nhập hết hạn | PC-01; xóa token / chờ hết phiên (hoặc SOP-02 với AUTH_001) | 1. Bấm nút trang hoặc đổi bộ lọc<br>2. Bấm "Đăng nhập" trên popup | Error code = AUTH_001 | - Hiện popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", nút "Đăng nhập"<br>- Bấm "Đăng nhập" → về màn đăng nhập SHSmart Web<br>- Không còn dữ liệu danh mục trên màn | High | Negative | EG |  |
| TC_DMTPW_ER_005 | BR-15 | Danh mục TP SH Bond – Trống & lỗi | Kiểm tra khi mất mạng lúc đổi bộ lọc | PC-01 | 1. DevTools Network chọn "Offline"<br>2. Chọn TCPH "CTCP Năng lượng Bắc Hà"<br>3. Bật lại mạng, chọn lại TCPH | TK1 | - Khi Offline: hiện popup lỗi chuẩn, không treo skeleton mãi<br>- Bật lại mạng: lọc ra đúng 1 dòng BHB291193 | Medium | Negative | EG | SRS không ghi câu chữ khi mất mạng |
| TC_DMTPW_BM_001 | BR-16 | Danh mục TP SH Bond – Phân quyền & bảo mật | Kiểm tra dữ liệu khi đổi sang tài khoản khác | TK1 đang xem tab SH Bond | 1. Đăng xuất TK1<br>2. Đăng nhập TK5 trên cùng trình duyệt<br>3. Vào tab SH Bond | TK1, TK5 | - Chỉ hiện mã của TK5 (có DEF12602)<br>- Không còn BHB291193, TPQ12312, ABC12601 của TK1, kể cả lúc đang tải | High | Permission | EG |  |
| TC_DMTPW_BM_002 | BR-16, BR-18 | Danh mục TP SH Bond – Phân quyền & bảo mật | Kiểm tra S6 khi gửi kèm tài khoản của KH khác | PC-03; lấy số TK / custodycd của TK5 | 1. Gọi GET /api/v1/bond/app/bond-holdings với token TK1, thêm tham số tài khoản = TK5 (nếu API có tham số này)<br>2. Gọi S6 với token TK1, symbol = DEF12602 | Token TK1; số TK của TK5; symbol = DEF12602 | - Bước 1: HTTP 403 hoặc chỉ trả dữ liệu TK1; không có DEF12602<br>- Bước 2: data.content = [], không lộ dữ liệu TK5 | High | Security | EG | Hỏi Dev tên tham số tài khoản (nếu có) |
| TC_DMTPW_BM_003 | BR-16 | Danh mục TP SH Bond – Phân quyền & bảo mật | Kiểm tra mở màn khi chưa đăng nhập | Chưa đăng nhập; đã có URL màn Danh sách nắm giữ | 1. Mở tab ẩn danh<br>2. Dán URL màn Danh mục trái phiếu SH Bond, Enter | URL màn Nắm giữ trên UAT | - Chuyển sang màn đăng nhập (hoặc popup AUTH_001)<br>- Không gửi S6 thành công, không hiện dữ liệu | High | Permission | EG |  |
| TC_DMTPW_BM_004 | BR-16 | Danh mục TP SH Bond – Phân quyền & bảo mật | Kiểm tra màn chỉ xem, không đổi dữ liệu khi thao tác | PC-02 | 1. Lần lượt: lọc, tìm mã, đổi trang, sắp xếp, mở/đóng Chi tiết<br>2. Xem cột Method các request bond | TK1 | - Tất cả request của màn là GET<br>- Sau thao tác, SOP-01 cho số liệu không đổi | Medium | Integration | EG | Post-condition |
| TC_DMTPW_EVT_001 | BR-17 | Danh mục TP SH Bond – Tracking | Kiểm tra ghi nhận 3 sự kiện tracking | PC-02; công cụ xem event (theo hướng dẫn Dev) | 1. Mở tab SH Bond<br>2. Chọn 1 TCPH<br>3. Bấm "Chi tiết" dòng BHB291193 | TK1 | - Bước 1: SBOND.HOLDING.SHBOND.LIST<br>- Bước 2: SBOND.HOLDING.SHBOND.FILTER<br>- Bước 3: SBOND.HOLDING.SHBOND.DETAIL<br>- Mỗi sự kiện ghi 1 lần | Low | Integration | UC | Q-14 (S2 của DETAIL) |

### 4.2 API S6 / C2 (6 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMTPW_API_001 | BR-18, BR-02 | Danh mục TP SH Bond – API S6 / C2 | Kiểm tra S6 khi gọi với bộ lọc mặc định | PC-03 | 1. GET /api/v1/bond/app/bond-holdings?page=<trang đầu>&size=10 | Token TK1 | - HTTP 200<br>- data.content có 3 phần tử; mỗi phần tử có symbol, issuerName, numberOfContract, bondAmount, totalFaceValue, investmentAmount, totalAmountReceived<br>- Phần tử BHB291193: numberOfContract = 2, bondAmount = 15, totalFaceValue = 1500000000, totalAmountReceived = 1575000000<br>- data.totalItems = 3 | Medium | Positive | EP |  |
| TC_DMTPW_API_002 | BR-18, BR-07, BR-08 | Danh mục TP SH Bond – API S6 / C2 | Kiểm tra S6 khi lọc theo symbol và issuerCode | PC-03 | 1. GET S6 với symbol = BHB291193<br>2. GET S6 với issuerCode = mã của Công ty bảo hiểm nhân thọ ABC | symbol = BHB291193; issuerCode = <mã TCPH ABC> | - Lần 1: HTTP 200, chỉ 1 phần tử BHB291193<br>- Lần 2: HTTP 200, 2 phần tử TPQ12312, ABC12601 | Medium | Positive | EP |  |
| TC_DMTPW_API_003 | BR-18, BR-08 | Danh mục TP SH Bond – API S6 / C2 | Kiểm tra C2 trả danh sách TCPH | PC-03 | 1. GET /api/v1/bond/common/issuer/list | Token TK1 | - HTTP 200<br>- Danh sách có CTCP Năng lượng Bắc Hà, Công ty bảo hiểm nhân thọ ABC; mỗi phần tử có mã và tên TCPH | Low | Positive | EP | Q-12 |
| TC_DMTPW_API_004 | BR-18, BR-11 | Danh mục TP SH Bond – API S6 / C2 | Kiểm tra S6 khi page và size sai | PC-03 | 1. GET S6 page = 999, size = 10<br>2. GET S6 page = -1<br>3. GET S6 size = 0 | page = 999 / -1; size = 0 | - page = 999: HTTP 200, data.content = []<br>- page = -1 và size = 0: báo lỗi tham số (HTTP 400 / BND-100), không HTTP 500, không trả toàn bộ dữ liệu | Medium | Negative | BVA | Error code tham số chưa có trong SRS |
| TC_DMTPW_API_005 | BR-18, BR-07 | Danh mục TP SH Bond – API S6 / C2 | Kiểm tra S6 khi từ khóa dài hơn 50 ký tự | PC-03 | 1. GET S6 với keyword (hoặc symbol) 51 ký tự | Chuỗi 51 ký tự = "BHB291193" lặp 5 lần + "ABCDEF" | - Báo lỗi BND-100 (V01)<br>- Không HTTP 500; không trả dữ liệu | Medium | Negative | BVA | [GIẢ ĐỊNH] max 50 – Q-05 |
| TC_DMTPW_API_006 | BR-18, BR-16 | Danh mục TP SH Bond – API S6 / C2 | Kiểm tra S6 và C2 khi không có token | Postman, bỏ header Authorization | 1. GET S6<br>2. GET C2 | — | - Cả 2 trả HTTP 401, error code AUTH_001<br>- Không trả dữ liệu danh mục | Medium | Security | EP |  |

## B5. Tự review + RTM
### RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ |
|---|---|---|---|---|
| BR-01 | Vào "Danh mục Trái phiếu" thì chip "SH Bond" được chọn mặc định; Web g… | 2 | TC_DMTPW_UI_001, TC_DMTPW_DS_001 | Đủ |
| BR-02 | Bảng có 8 cột theo thứ tự: Mã TP, TCPH (tên đầy đủ), SL HĐ (số nguyên)… | 7 | TC_DMTPW_UI_002, TC_DMTPW_UI_003, TC_DMTPW_UI_005, TC_DMTPW_UI_006, TC_DMTPW_DS_002, TC_DMTPW_CT_005, TC_DMTPW_API_001 | Đủ |
| BR-03 | Gộp theo mã TP (BIZ-18): mỗi dòng = 1 mã TP, cộng dồn các HĐ kỳ hạn KH… | 6 | TC_DMTPW_DS_002, TC_DMTPW_DS_003, TC_DMTPW_DS_004, TC_DMTPW_DS_005, TC_DMTPW_CT_005, TC_DMTPW_CTI_002 | Đủ |
| BR-04 | Tổng mệnh giá = SL TP × Mệnh giá niêm yết ban đầu của mã; không đổi th… | 2 | TC_DMTPW_CT_001, TC_DMTPW_CT_002 | Đủ |
| BR-05 | Tổng tiền thực nhận = gốc đầu tư + lãi dự kiến theo kỳ hạn, chưa tính … | 1 | TC_DMTPW_CT_004 | Đủ |
| BR-06 | Tổng tiền đầu tư = tổng tiền đầu tư các HĐ của mã TP (SRS ghi "Dự thảo… | 1 | TC_DMTPW_CT_003 | Đủ |
| BR-07 | Ô "Mã trái phiếu": nhập tự do, tìm cụm từ nằm trong mã/tên TP, không p… | 12 | TC_DMTPW_TK_001, TC_DMTPW_TK_002, TC_DMTPW_TK_003, TC_DMTPW_TK_004, TC_DMTPW_TK_005, TC_DMTPW_TK_006, TC_DMTPW_TK_007, TC_DMTPW_TK_008, TC_DMTPW_TK_009, TC_DMTPW_TK_010, TC_DMTPW_API_002, TC_DMTPW_API_005 | Đủ |
| BR-08 | Lọc "Tổ chức phát hành": chọn nhiều; mặc định "Tất cả tổ chức"; đã chọ… | 7 | TC_DMTPW_UI_001, TC_DMTPW_TC_001, TC_DMTPW_TC_002, TC_DMTPW_TC_003, TC_DMTPW_TC_004, TC_DMTPW_API_002, TC_DMTPW_API_003 | Đủ |
| BR-09 | Lọc "Loại trái phiếu": chọn nhiều; mặc định "Tất cả"; giá trị Niêm yết… | 5 | TC_DMTPW_UI_001, TC_DMTPW_LT_001, TC_DMTPW_LT_002, TC_DMTPW_LT_003, TC_DMTPW_LT_004 | Đủ |
| BR-10 | Áp dụng bộ lọc: "Lưu" gọi lại S6 / C2 theo điều kiện đã chọn; "Đặt lại… | 9 | TC_DMTPW_TK_001, TC_DMTPW_TC_002, TC_DMTPW_LT_002, TC_DMTPW_LC_001, TC_DMTPW_LC_002, TC_DMTPW_LC_003, TC_DMTPW_LC_004, TC_DMTPW_LC_005, TC_DMTPW_SX_003 | Đủ |
| BR-11 | Phân trang "Trước / 1 2 3 … / Sau"; request S6 page / size; tổng số dò… | 6 | TC_DMTPW_LC_004, TC_DMTPW_PT_001, TC_DMTPW_PT_002, TC_DMTPW_PT_003, TC_DMTPW_PT_004, TC_DMTPW_API_004 | Đủ |
| BR-12 | Sắp xếp ↑/↓ cột "Tổng tiền thực nhận" (request S6: sort) | 4 | TC_DMTPW_UI_002, TC_DMTPW_SX_001, TC_DMTPW_SX_002, TC_DMTPW_SX_003 | Đủ |
| BR-13 | Nút "Chi tiết" mở popup Chi tiết trái phiếu (HOLDING.WEB.02) của đúng … | 3 | TC_DMTPW_CTI_001, TC_DMTPW_CTI_002, TC_DMTPW_CTI_003 | Đủ |
| BR-14 | Trạng thái màn: skeleton khi đang gọi S6 / C2; danh sách rỗng → empty … | 4 | TC_DMTPW_UI_004, TC_DMTPW_TK_008, TC_DMTPW_LC_002, TC_DMTPW_ER_001 | Đủ |
| BR-15 | Lỗi BND-901 / BND-999 / AUTH_001 → popup lỗi chuẩn SHSmart: "Hệ thống … | 4 | TC_DMTPW_ER_002, TC_DMTPW_ER_003, TC_DMTPW_ER_004, TC_DMTPW_ER_005 | Đủ |
| BR-16 | Chỉ hiển thị TP của KH đang đăng nhập (Pre-condition: KH đã đăng nhập)… | 7 | TC_DMTPW_TK_010, TC_DMTPW_ER_004, TC_DMTPW_BM_001, TC_DMTPW_BM_002, TC_DMTPW_BM_003, TC_DMTPW_BM_004, TC_DMTPW_API_006 | Đủ |
| BR-17 | Ghi nhận 3 sự kiện FE: SBOND.HOLDING.SHBOND.LIST (mở/tải lại), .FILTER… | 1 | TC_DMTPW_EVT_001 | Đủ |
| BR-18 | API: S6 GET /api/v1/bond/app/bond-holdings (mở màn, lọc, đổi trang); C… | 10 | TC_DMTPW_DS_001, TC_DMTPW_TC_001, TC_DMTPW_PT_002, TC_DMTPW_BM_002, TC_DMTPW_API_001, TC_DMTPW_API_002, TC_DMTPW_API_003, TC_DMTPW_API_004, TC_DMTPW_API_005, TC_DMTPW_API_006 | Đủ |

### Checklist
| Tiêu chí | Kết quả | Ghi chú |
|---|---|---|
| Mọi BR đều có TC | Đạt | 18/18 BR |
| Field có đủ rỗng / hợp lệ / không hợp lệ / biên | Đạt | Ô tìm kiếm: TK_006, TK_001–005, TK_008–010, TK_007; dropdown: không chọn / 1 / nhiều |
| Có luồng chính, luồng phụ, luồng lỗi | Đạt | DS_001, CTI_001–002 · lọc, trang, sắp xếp · ER_001–005 |
| Decision table: mỗi rule có TC | Đạt | R1–R5 |
| State transition có TC | Đạt | DS_004, DS_005 |
| Có case phân quyền / bảo mật | Đạt | BM_001–004, TK_010, API_006 |
| 1 TC = 1 mục tiêu, ID không trùng | Đạt | 65 ID duy nhất |
| Test data cụ thể | Đạt | TK1/2/3/5, Mã A–E với số HĐ, số TP, số tiền cụ thể |
| Expected đo được | Đạt | Giá trị từng cột, request/tham số, HTTP status, câu chữ popup |
| Negative kiểm cả báo lỗi và không thực hiện | Đạt | VD ER_002 "không hiện số liệu cũ", BM_002 "không lộ dữ liệu", API_004 "không trả toàn bộ dữ liệu" |
| Message đúng nguyên văn SRS hoặc có [GIẢ ĐỊNH] | Đạt | I01, BND-901 / BND-999 / AUTH_001 theo mục 6; ánh xạ mã → câu có ghi [GIẢ ĐỊNH] |
| Thứ tự UI → Positive → … → Security | Đạt | UI trước; trong mỗi nhóm chức năng: Positive → Boundary → Negative; Phân quyền, API cuối |

### Sửa / bổ sung ở B5
- Bổ sung 5 TC (đánh dấu "Bổ sung B5"): DS_005 (tất toán 1 HĐ → số liệu giảm, thiếu ô state transition), LC_005 (đổi bộ lọc nhanh – race), TK_009 (chỉ khoảng trắng), UI_006 (tương thích trình duyệt), CTI_003 (đóng popup giữ trạng thái).
- Sửa CT_004: thêm số "sai" nếu bị trừ thuế để tester dễ nhận ra.
- Tách DS_003 / DS_004: gộp nhiều HĐ và mã chỉ còn HĐ tất toán là 2 mục tiêu khác nhau.

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 65 (Web 59 · API 6) | — |
| High / Medium / Low | 13 (20%) / 35 (54%) / 17 (26%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 30 : 35 (46% : 54%) | ≈ 30 : 70 |
| Theo Type | Positive 30 · Negative 13 · UI 9 · Boundary 5 · Security 3 · Integration 3 · Permission 2 | — |
| BR được phủ | 18/18 (100%) | 100% |
| Q&A mở | 15 (Blocker 0 · Major 9 · Minor 6) | Blocker = 0 |
| TC có [GIẢ ĐỊNH] | 28 | — |
| Ghi chú | Tỷ lệ Positive cao hơn mục tiêu vì màn chỉ xem, phần lớn là hiển thị / lọc. Phần Negative tập trung vào số liệu sai, lỗi và bảo mật | — |
