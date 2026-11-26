# SBOND.HOLDING.WEB.03 – Danh mục Trái phiếu linh hoạt (Web) – Test design B1→B5

Nguồn: SRS SBOND.HOLDING.WEB.03 – Danh mục Trái phiếu linh hoạt (export Confluence 15/11/2026) + ảnh luồng và ảnh Figma Web trong SRS  
Người viết: Nguyễn Sỹ Hà · Ngày: 26/11/2026 · Lệnh: /FULL  
Phiên bản: v1 · Kênh: SHSmart Web  
Ký hiệu: [GIẢ ĐỊNH] / [GĐ] = tạm giả định, chờ trả lời câu hỏi Q-xx; PC-xx = điều kiện có sẵn dùng chung; SOP-xx = thao tác dùng chung; "Bổ sung B5" = TC thêm ở bước tự review.

## B1. Phân tích yêu cầu
### 1. Tổng quan
- Mục đích: KH xem các mã trái phiếu linh hoạt đang lưu ký trong TKCK (mọi tiểu khoản trừ sub88), mỗi mã 1 dòng; lọc theo Mã TP / TCPH / Loại TP; bấm "Chi tiết" sang Chi tiết TP linh hoạt (HOLDING.WEB.04).
- Người dùng: KH trên SHSmart Web. Hệ thống liên quan: BE S-Bond (API S7, C2), Flex (số dư, lưu ký TP).
- Vào màn từ: "Sản phẩm đầu tư" > "Danh sách nắm giữ" > "Danh mục trái phiếu" > chip "Trái phiếu linh hoạt"; hoặc bấm noti Bond_05.
- Màn chỉ xem, không đổi dữ liệu. Cụm số dư / Nộp tiền phía trên thuộc SRS SBOND.OFFERING.WEB.01 (ngoài phạm vi).
- Khác tab SH Bond (HOLDING.WEB.01): không có SL HĐ, Tổng tiền đầu tư, Tổng tiền thực nhận, sắp xếp; nguồn dữ liệu là số dư lưu ký Flex (không phải HĐ kỳ hạn).

### 2. Feature
- Mở màn & bảng gộp theo mã · Phạm vi dữ liệu (trừ sub88) · Công thức Tổng mệnh giá · Tìm Mã TP · Lọc TCPH · Lọc Loại TP (quy tắc chip "Tất cả") · Kết hợp / đặt lại bộ lọc · Phân trang · Chi tiết → HOLDING.WEB.04 · Vào từ noti Bond_05 / HĐ kỳ hạn chuyển Outright · Trống & lỗi · Phân quyền · Tracking · API S7 / C2

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Chọn chip "Trái phiếu linh hoạt" → Web gọi S7 / C2 với bộ lọc mặc định (tất cả) | Luồng bước 1; Entry | Logic |
| BR-02 | Vào màn từ noti Bond_05 → mở danh sách TP linh hoạt, gọi S7 / C2 với bộ lọc mặc định | Entry; Luồng bước 1; Mục 8 | Tích hợp |
| BR-03 | Bảng hiển thị: Mã TP, Loại TP [GĐ Q-01], TCPH, SL TP (#.###), Tổng mệnh giá (#.### VND), nút "Chi tiết" | Mục 2.2 #1–5; Luồng bước 2; Figma | UI |
| BR-04 | Phạm vi (BIZ-21): tất cả TP lưu ký trong TKCK của KH ở mọi tiểu khoản, **trừ sub88** | BIZ-21; Description | Logic |
| BR-05 | Gộp theo mã TP (BIZ-21): 1 mã = 1 dòng; SL TP = tổng KL của mã KH đang nắm giữ trên các sub hợp lệ | BIZ-21; Mục 2.2 #3 | Logic |
| BR-06 | Tổng mệnh giá = SL TP × Mệnh giá niêm yết ban đầu; không biến động theo thị giá / giá giao dịch (BIZ-19) | BIZ-19; Mục 2.2 #4 | Logic |
| BR-07 | Ô "Mã trái phiếu": nhập tự do, tìm gần đúng (bao gồm) trong mã/tên TP, không phân biệt hoa thường, có gợi ý (keyword), chọn 1 mã (symbol); để trống = không lọc; cắt khoảng trắng; chặn nhập quá độ dài (V01, BND-100) | Mục 2.1 #1; Mục 5 V01 | Validation |
| BR-08 | Lọc "Tổ chức phát hành": chọn nhiều; mặc định "Tất cả tổ chức"; đã chọn → "Đã chọn n tổ chức"; không chọn = tất cả; danh sách từ C2 (data[].issuerName); gửi issuerCode | Mục 2.1 #2 | Validation |
| BR-09 | Lọc "Loại trái phiếu": chọn nhiều; mặc định "Tất cả"; Niêm yết / Riêng lẻ; chọn "Tất cả" → bỏ chip khác; chọn Niêm yết/Riêng lẻ → bỏ "Tất cả"; bỏ chọn hết → về "Tất cả"; gửi bondType | Mục 2.1 #3 | Validation |
| BR-10 | Lọc → gọi lại S7 / C2 theo điều kiện; kết hợp các bộ lọc; Lưu / Đặt lại bộ lọc | Luồng bước 3; Mục 7 FILTER; Mục 5 V01 | Logic |
| BR-11 | Phân trang theo page, size (S7 / C2) | Mục 2.2 #6 | Logic |
| BR-12 | Nút "Chi tiết" → Chi tiết TP linh hoạt (HOLDING.WEB.04) của đúng mã | Luồng bước 4; Mục 2.2 #5 | Tích hợp |
| BR-13 | Danh sách rỗng → empty state SBOND.HOLDING.I02: "Quý khách chưa có trái phiếu linh hoạt" / "Không tìm thấy kết quả phù hợp" | Mục 2.3; Mục 6 #1 | UI |
| BR-14 | Lỗi BND-901 / BND-999 / AUTH_001 → popup lỗi chuẩn SHSmart: "Hệ thống đang bận" / "Có lỗi xảy ra. Vui lòng thử lại sau" / "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"; CTA Đóng / Đăng nhập | Mục 6 #2 | Lỗi |
| BR-15 | Chỉ hiển thị TP của KH đang đăng nhập; màn chỉ xem, không đổi dữ liệu | Pre/Post-Condition | Phân quyền |
| BR-16 | Ghi nhận sự kiện FE: SBOND.HOLDING.FLEX.LIST (mở/tải lại), .FILTER (lưu/đặt lại bộ lọc), .DETAIL (mở Chi tiết) | Mục 7 | Tích hợp |
| BR-17 | HĐ kỳ hạn hết hiệu lực tại COT ngày E (thiếu hồ sơ / thiếu số dư) → BE off HĐ, chuyển số dư TP còn lại thành Outright → mã TP xuất hiện trong TP linh hoạt; gửi Noti/Email Bond_05 | Mục 8 | Logic |
| BR-18 | API: S7 GET /api/v1/bond/app/flex-bond-holding (mở màn, lọc, đổi trang); C2 GET /api/v1/bond/common/issuer/list (danh sách TCPH) | Mục 3 | Tích hợp |

### 4. Field list
| Vùng | Trường | Kiểu | Bắt buộc | Ràng buộc / Mapping | Mặc định | Khi lỗi |
|---|---|---|---|---|---|---|
| Bộ lọc | Mã trái phiếu | Ô tìm kiếm + gợi ý | Không | keyword (gợi ý), symbol (mã đã chọn); cắt khoảng trắng; không phân biệt hoa thường; tìm "bao gồm"; tối đa 50 ký tự [GĐ Q-04] | Trống, placeholder "Mã trái phiếu" | Chặn nhập quá độ dài; API BND-100 |
| Bộ lọc | Tổ chức phát hành | Dropdown chọn nhiều | Không | issuerCode; danh sách C2 data[].issuerName | "Tất cả tổ chức" | — |
| Bộ lọc | Loại trái phiếu | Dropdown chọn nhiều | Không | bondType: Niêm yết / Riêng lẻ; "Tất cả" loại trừ chip khác | "Tất cả" | — |
| Bảng | Mã trái phiếu | Text | Có | data.content[].symbol | — | — |
| Bảng | Loại trái phiếu | Text | Có [GĐ Q-01] | Chưa có mapping trong SRS | — | — |
| Bảng | Tổ chức phát hành | Text | Có | data.content[].issuerName | — | — |
| Bảng | Số lượng trái phiếu | Số #.### | Có | data.content[].bondAmount – BIZ-21 | — | — |
| Bảng | Tổng mệnh giá | Số #.### VND | Có | data.content[].totalFaceValue – BIZ-19 | — | — |
| Bảng | Chi tiết | Nút | Có | → HOLDING.WEB.04 | — | — |
| Bảng | Phân trang | Pagination | Có | page, size | Trang 1, 10 dòng [GĐ Q-08] | — |

### 5. Q&A
Không có câu mức Blocker → /FULL chạy tiếp. Tổng 14 câu (Major 7 · Minor 7).

| Q ID | Câu hỏi | Lý do | Mức | Giả định đề xuất | TC liên quan |
|---|---|---|---|---|---|
| Q-01 | Bảng 2.2 không có cột "Loại trái phiếu", nhưng Luồng bước 2 ghi "Mã TP + loại" và Figma Web có cột "Loại trái phiếu" ở vị trí 2. Có cột này không? Field API nào? | SRS và Figma khác nhau; thiếu mapping | Major | Có cột Loại TP ở vị trí 2 (theo Figma), giá trị Niêm yết / Riêng lẻ | UI_002, LT_002, LT_003, API_001 |
| Q-02 | Luồng (sơ đồ "Lọc / Lưu"), V01 "Lưu bộ lọc" và sự kiện FILTER "Lưu / đặt lại" nói có nút Lưu / Đặt lại, nhưng Figma Web để bộ lọc ngay trên bảng, không có 2 nút này. Lọc áp dụng ngay khi chọn hay phải bấm "Lưu"? Đặt lại bằng cách nào? | SRS và Figma khác nhau | Major | Áp dụng ngay khi chọn (theo Figma); "Đặt lại" = bỏ hết điều kiện về mặc định | TK_001, TC_002, LT_002, LC_004 |
| Q-03 | Ô tìm kiếm: "tìm cụm từ trong mã/tên TP" nhưng lại "chọn 1 mã" và gửi symbol. Gõ từ khóa rồi Enter (không chọn gợi ý) có lọc không? Gợi ý chỉ gồm mã KH đang nắm giữ linh hoạt? Tên TP lấy từ đâu (bảng không có cột tên)? User Story ghi "lọc theo tên" | Hành vi tìm kiếm chưa rõ | Major | Gợi ý chỉ gồm mã đang nắm giữ linh hoạt; tìm theo mã và tên TP; phải chọn gợi ý mới lọc; Enter không chọn → không lọc | TK_001 → TK_009, TK_015 |
| Q-04 | V01 "chặn nhập quá độ dài" nhưng không ghi độ dài tối đa của từ khóa | Thiếu biên | Major | Tối đa 50 ký tự (như tab SH Bond) | TK_010 → TK_012, API_005 |
| Q-05 | BIZ-21 "tất cả TP lưu ký": SL TP có gồm phần đang phong tỏa / cầm cố / chờ về / chờ thanh toán không? Lấy số dư nào của Flex? | Ảnh hưởng con số SL và Tổng mệnh giá | Major | SL = tổng số dư lưu ký Flex trả về cho mã (mọi trạng thái) trên các sub ≠ sub88 | DS_003, DS_006, API_001 |
| Q-06 | C2 là API danh sách TCPH dùng chung: trả toàn bộ TCPH hệ thống hay chỉ TCPH của mã KH đang nắm giữ linh hoạt? | Ảnh hưởng nội dung dropdown và case "chọn TCPH không có TP" | Major | C2 trả toàn bộ TCPH; chọn TCPH không có TP → "Không tìm thấy kết quả phù hợp" | TC_001, TC_005, API_003 |
| Q-07 | Mục 8: số dư chuyển Outright về tiểu khoản nào? Hiển thị trong TP linh hoạt từ khi nào (ngay sau COT ngày E hay T+1)? Bấm noti Bond_05 mở thẳng chip "Trái phiếu linh hoạt" với bộ lọc mặc định? | Ảnh hưởng thời điểm kiểm và điều hướng | Major | Chuyển về sub thường (≠ sub88); hiển thị ngay sau khi job COT ngày E chạy xong; noti mở chip TP linh hoạt, bộ lọc mặc định | NT_001 → NT_004 |
| Q-08 | Web phân trang mấy dòng/trang? Figma TP linh hoạt không vẽ thanh phân trang | Thiếu biên | Minor | 10 dòng/trang, kiểu "Trước / 1 2 … / Sau" như tab SH Bond | PT_001 → PT_004, LC_005 |
| Q-09 | Thứ tự sắp xếp mặc định của danh sách? | SRS không ghi | Minor | Không kiểm thứ tự cụ thể; chỉ kiểm không trùng / không thiếu mã giữa các trang | PT_002 |
| Q-10 | Empty state ghi "TBU Q25": hình, CTA? Câu nào dùng khi nào? | SRS chưa chốt | Minor | Chưa có TP → "Quý khách chưa có trái phiếu linh hoạt"; lọc không ra → "Không tìm thấy kết quả phù hợp"; không có CTA | ER_001, TK_008, TC_005, LC_002, LC_003 |
| Q-11 | Tổng mệnh giá: SRS "#.### VND", Figma không có chữ "VND". Thứ tự bộ lọc SRS (Mã, TCPH, Loại) khác Figma (Mã, Loại, TCPH). Figma vẽ nhiều dòng cùng mã BHB291193 – là dữ liệu mẫu? | SRS và Figma khác nhau | Minor | Theo Figma (không chữ "VND", thứ tự Mã – Loại – TCPH); nhiều dòng cùng mã chỉ là dữ liệu mẫu, thực tế 1 mã 1 dòng | UI_001, UI_003, DS_002 |
| Q-12 | 3 mã lỗi và 3 câu thông báo được ghi chung 1 ô. Mã nào ra câu nào, CTA nào? Lỗi C2 / mất mạng xử lý thế nào? | Không ánh xạ 1-1 | Minor | BND-901 → "Hệ thống đang bận" (Đóng); BND-999 → "Có lỗi xảy ra. Vui lòng thử lại sau" (Đóng); AUTH_001 → "Phiên đăng nhập không hợp lệ hoặc đã hết hạn" (Đăng nhập); lỗi C2 / mất mạng → popup lỗi chuẩn | ER_002 → ER_006 |
| Q-13 | Sự kiện DETAIL ghi API S2 / S8 (thuộc HOLDING.WEB.04). Kiểm tracking bằng công cụ nào, payload gồm gì? | Thiếu cách kiểm | Minor | Kiểm tên event trên công cụ tracking của môi trường UAT; không kiểm payload | EVT_001 |
| Q-14 | Mã TP về số dư 0 (bán / chuyển hết) có còn hiện dòng SL = 0 không? | SRS không ghi | Minor | Không hiện | DS_008 |

### 6. Rủi ro chính
| # | Rủi ro | Tác động | Mức |
|---|---|---|---|
| 1 | Tính nhầm số dư sub88 (HĐ kỳ hạn / repo) vào TP linh hoạt, hoặc bỏ sót sub thường | KH thấy sai tài sản, trùng với tab SH Bond | Cao |
| 2 | Gộp sai khi 1 mã nằm ở nhiều sub (2 dòng cùng mã, hoặc chỉ lấy 1 sub) | Sai SL / Tổng mệnh giá | Cao |
| 3 | Tổng mệnh giá tính theo giá giao dịch thay vì mệnh giá ban đầu | Sai số tiền, khiếu nại | Cao |
| 4 | HĐ kỳ hạn off tại COT nhưng số dư Outright không xuất hiện / xuất hiện 2 nơi | KH tưởng mất TP | Cao |
| 5 | API S7 trả dữ liệu của KH khác (đổi tham số, cache khi đổi tài khoản) | Lộ thông tin tài sản | Cao |

## B2. Chiến lược test
- **In-scope:** Bảng TP linh hoạt (gộp theo mã, phạm vi trừ sub88, Tổng mệnh giá), 3 bộ lọc và kết hợp, quy tắc chip "Tất cả", phân trang, mở Chi tiết đúng mã, vào từ noti Bond_05, HĐ kỳ hạn chuyển Outright (mục 8), empty state, popup lỗi, phân quyền, tracking, API S7 / C2.
- **Out-of-scope:** Bản App (HOLDING.APP.03); tab SH Bond (HOLDING.WEB.01); nội dung màn Chi tiết TP linh hoạt (HOLDING.WEB.04); cụm số dư / Nộp tiền (OFFERING.WEB.01); nội dung noti/email Bond_05; job COT (chỉ kiểm kết quả hiển thị); hiệu năng.
- **Loại test:** Chức năng, giao diện, API, tích hợp (Web – BE – Flex), phân quyền, bảo mật cơ bản, tương thích trình duyệt, đoán lỗi.

| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC (Web / API) |
|---|---|---|---|---|
| BR-01 | UC | EG | High | 3 / 0 |
| BR-02 | UC | — | High | 1 / 0 |
| BR-03 | EG | PW | Low | 5 / 0 |
| BR-04 | EP | ST | High | 7 / 1 |
| BR-05 | EP | ST | High | 5 / 1 |
| BR-06 | EP | — | High | 4 / 1 |
| BR-07 | EP | BVA, EG | Medium | 15 / 2 |
| BR-08 | EP | — | Medium | 7 / 2 |
| BR-09 | ST | EP | Medium | 8 / 2 |
| BR-10 | DT | EG | Medium | 9 / 0 |
| BR-11 | BVA | — | Medium | 6 / 1 |
| BR-12 | UC | EG | High | 4 / 0 |
| BR-13 | EP | — | Medium | 5 / 0 |
| BR-14 | EG | — | Medium | 5 / 0 |
| BR-15 | EG | — | High | 5 / 2 |
| BR-16 | UC | — | Low | 3 / 0 |
| BR-17 | ST | UC | High | 3 / 0 |
| BR-18 | EP | BVA | Medium | 3 / 8 |

- **Môi trường:** UAT SHSmart Web; Chrome bản mới nhất (chính) + Edge, Firefox, Safari; DevTools; Proxyman/Charles; Postman; tài khoản BO Portal / Flex UAT để xem và chỉnh số dư theo tiểu khoản.
- **Exit criteria:** Chạy hết TC High; không còn bug Critical/Major mở; Q&A Major đã có trả lời (hoặc PO chấp nhận giả định); mọi BR có TC Pass.

### Dữ liệu cần có trên môi trường
| Mã | Điều kiện dữ liệu | Dùng cho |
|---|---|---|
| TK1 | Tài khoản chính, đăng nhập được SHSmart Web UAT, có Mã A, B, C linh hoạt + Mã D chỉ nằm ở sub88 | Hầu hết TC |
| Mã A | BHB291193 · CTCP Năng lượng Bắc Hà · Riêng lẻ · Mệnh giá 10.000.000<br>- sub01: 6 TP · sub06: 4 TP · sub88: 5 TP (HĐ kỳ hạn SH Bond)<br>→ Kỳ vọng dòng: SL 10 · Tổng MG 100.000.000 (sub88 không tính) | Gộp nhiều sub, loại sub88 |
| Mã B | TPQ12312 · Công ty bảo hiểm nhân thọ ABC · Niêm yết · Mệnh giá 100.000 · mua trên sàn (không qua SH Bond)<br>- sub01: 1.250 TP; giá giao dịch gần nhất 98.500<br>→ Kỳ vọng dòng: SL 1.250 · Tổng MG 125.000.000 | Niêm yết, MG ≠ thị giá |
| Mã C | ABC12601 · Công ty bảo hiểm nhân thọ ABC · Riêng lẻ · Mệnh giá 1.000.000<br>- sub06: 50 TP<br>→ Kỳ vọng dòng: SL 50 · Tổng MG 50.000.000 | 1 TCPH có 2 mã |
| Mã D | XYZ12599 · CTCP Đầu tư XYZ · Riêng lẻ · chỉ có ở sub88: 20 TP | Mã không được hiện |
| TK2 | Tài khoản chỉ có TP trên sub88 (HĐ kỳ hạn SH Bond), không có TP ở sub khác | Empty state |
| TK3 | Tài khoản có 12 mã TP linh hoạt khác nhau | Phân trang |
| TK4 | Có BHB291193 8 TP ở sub01; HĐ kỳ hạn HĐ-E1 (DEF12602 · CTCP Đầu tư DEF · Riêng lẻ · MG 1.000.000 · 30 TP sub88) và HĐ-E2 (BHB291193 · 5 TP sub88), cả 2 có ngày E = ngày test, đang thiếu hồ sơ; chưa có DEF12602 ở sub khác | Chuyển Outright (mục 8) |
| TK5 | Tài khoản khác, có Mã E = GHI12603 (TK1 không có mã này) | Phân quyền |
| Lưu ý | Mã, tên TCPH, số lượng là VD. Tester thay bằng dữ liệu thật trên UAT nhưng giữ đúng cấu trúc (1 mã ở nhiều sub, có phần ở sub88, MG ≠ giá giao dịch) rồi tính lại kỳ vọng | — |

### Precondition / thao tác dùng chung
| Mã | Nội dung |
|---|---|
| PC-01 | TK1 đã đăng nhập SHSmart Web UAT, vào "Sản phẩm đầu tư" > "Danh sách nắm giữ" > "Danh mục trái phiếu" > chip "Trái phiếu linh hoạt"; danh sách đã tải xong, bộ lọc mặc định |
| PC-02 | PC-01 + mở DevTools tab Network (lọc "flex-bond-holding") |
| PC-03 | Postman, header Authorization = Bearer <token TK1> lấy từ request S7 trên Web |
| SOP-01 | Đối chiếu số dư: trên BO Portal / Flex UAT tra số dư TP của TK1 theo từng tiểu khoản cho mã cần kiểm; cộng các sub ≠ sub88 để so với SL trên Web |
| SOP-02 | Giả lập lỗi: dùng Proxyman sửa response S7 (hoặc C2) thành mã lỗi cần test (BND-901 / BND-999 / AUTH_001) |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên | Condition |
|---|---|---|---|---|
| Tiểu khoản chứa TP | sub01, sub06 (sub thường) | sub88 | Mã có ở cả sub thường và sub88; mã chỉ ở sub88 | TCO-BR04-01 → 03 |
| Nguồn TP | Mua qua SH Bond rồi Outright; mua trên sàn | — | — | TCO-BR04-04 |
| Số dư mã | > 0 ở 1 sub; > 0 ở nhiều sub | = 0 (bán hết) | 10 → 6 (giảm 1 sub) | TCO-BR05-01 → 04 |
| Mệnh giá × SL | 10.000.000 × 10; 1.000.000 × 50 | Dùng giá giao dịch 98.500 | Số rất lớn 999.999.999.999.999 | TCO-BR06-01 → 03 |
| Từ khóa Mã TP | Đủ mã; một phần mã; chữ thường; cụm từ trong tên TP; khoảng trắng 2 đầu | Không khớp; chỉ khoảng trắng; mã chỉ ở sub88; mã của KH khác; script / SQL | 50 / 51 ký tự; dán 60 ký tự | TCO-BR07-01 → 15 |
| TCPH | Không chọn (tất cả); 1 TCPH; 2 TCPH | TCPH không có TP linh hoạt của KH | 0 / 1 / n tổ chức | TCO-BR08-01 → 05 |
| Loại TP | Tất cả; Niêm yết; Riêng lẻ; cả 2 | bondType sai (API) | — | TCO-BR09-01 → 07 |
| Phân trang (10/trang) | page trong phạm vi | page = -1; size = 0 | 3 mã (1 trang); 12 mã (2 trang); page = 999 | TCO-BR11-01 → 05 |

### Decision table – kết hợp bộ lọc (dữ liệu TK1)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 | R6 | R7 |
|---|---|---|---|---|---|---|---|
| Mã TP | — | BHB291193 | — | — | — | — | TPQ12312 |
| TCPH | — | — | Công ty BHNT ABC | Công ty BHNT ABC | CTCP NL Bắc Hà | — | — |
| Loại TP | Tất cả | Tất cả | Tất cả | Riêng lẻ | Niêm yết | Niêm yết + Riêng lẻ | Riêng lẻ |
| **Kết quả** | 3 dòng (A, B, C) | A | B, C | C | Empty "Không tìm thấy kết quả phù hợp" | A, B, C | Empty "Không tìm thấy kết quả phù hợp" |
| TC | DS_002 | TK_001 | TC_002 | LC_001 | LC_002 | LT_004 | LC_003 |

### State transition 1 – chip "Loại trái phiếu"
| Trạng thái \ Hành động | Chọn "Tất cả" | Chọn Niêm yết | Chọn Riêng lẻ | Bỏ chọn chip đang chọn |
|---|---|---|---|---|
| {Tất cả} (mặc định) | ✖ giữ {Tất cả} | {Niêm yết} – LT_002 | {Riêng lẻ} – LT_003 | ✖ giữ {Tất cả} – LT_007 |
| {Niêm yết} | {Tất cả} | — | {Niêm yết, Riêng lẻ} – LT_004 | {Tất cả} – LT_006 |
| {Niêm yết, Riêng lẻ} | {Tất cả} – LT_005 | — | — | Bỏ 1 chip → còn chip kia |

### State transition 2 – mã TP trong TP linh hoạt (theo số dư / HĐ kỳ hạn)
| Trạng thái mã \ Sự kiện | Giảm số dư 1 sub | Bán hết | HĐ kỳ hạn off tại COT ngày E (chuyển Outright) |
|---|---|---|---|
| Có ở sub thường | SL giảm tương ứng – DS_007 | Mã biến mất – DS_008 | SL cộng thêm, vẫn 1 dòng – NT_004 |
| Chỉ ở sub88 (không hiện) | — | — | Mã xuất hiện – NT_002; trước COT chưa hiện – NT_003 |

### Use case
- Luồng chính: Chọn chip "Trái phiếu linh hoạt" → xem bảng gộp → bấm "Chi tiết" → HOLDING.WEB.04 (DS_001, DS_002, CTI_001).
- Luồng phụ: lọc từng điều kiện, kết hợp, đặt lại; đổi trang; vào từ noti Bond_05; HĐ kỳ hạn chuyển Outright.
- Luồng lỗi: không có TP linh hoạt; lọc không ra; BND-901 / BND-999 / AUTH_001; lỗi C2; mất mạng; chưa đăng nhập; tài khoản khác.

### Error guessing áp dụng
- Ô tìm kiếm: khoảng trắng, chữ thường, script / SQL, dán chuỗi dài, mã của KH khác · Bộ lọc: đổi nhanh liên tiếp, đổi khi đang ở trang 2, bỏ chọn "Tất cả" · Số liệu: mã ở nhiều sub, phần ở sub88, giá giao dịch đổi, số rất lớn, số dư về 0 · Hệ thống: Offline, hết phiên, đổi tài khoản, gọi API thẳng với tham số của KH khác · Nút: double-click "Chi tiết" · Trình duyệt / độ phân giải.

## B4. Test case
### 4.1 Web (73 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_TPLHW_UI_001 | BR-01, BR-08, BR-09 | TP linh hoạt – Giao diện | Kiểm tra giao diện mặc định khi mở chip Trái phiếu linh hoạt | PC-01 | 1. Quan sát vùng bộ lọc và bảng | TK1 | - Tab "Danh mục trái phiếu" và chip "Trái phiếu linh hoạt" được chọn (màu cam); chip "sBond" không chọn<br>- Ô tìm kiếm trống, placeholder "Mã trái phiếu"<br>- Dropdown Loại TP hiện "Tất cả"; dropdown TCPH hiện "Tất cả tổ chức"<br>- Bảng hiện 3 dòng: BHB291193, TPQ12312, ABC12601 | Low | UI | EG | [GIẢ ĐỊNH] thứ tự bộ lọc theo Figma – Q-11 |
| TC_TPLHW_UI_002 | BR-03 | TP linh hoạt – Giao diện | Kiểm tra tiêu đề và thứ tự cột của bảng khi có dữ liệu | PC-01 | 1. Quan sát hàng tiêu đề bảng | TK1 | - 6 cột theo thứ tự: Mã trái phiếu · Loại trái phiếu · Tổ chức phát hành · SL Trái phiếu · Tổng mệnh giá · Thao tác<br>- Không có cột SL Hợp đồng / Tổng tiền đầu tư / Tổng tiền thực nhận<br>- Mỗi dòng có nút "Chi tiết" ở cột Thao tác | Low | UI | EG | [GIẢ ĐỊNH] có cột Loại TP theo Figma – Q-01 |
| TC_TPLHW_UI_003 | BR-03 | TP linh hoạt – Giao diện | Kiểm tra định dạng các cột số khi có dữ liệu | PC-01 | 1. Quan sát dòng TPQ12312 | Mã B | - SL Trái phiếu "1.250"<br>- Tổng mệnh giá "125.000.000": phân cách hàng nghìn bằng dấu chấm, không có số thập phân<br>- Các cột số căn phải | Low | UI | EG | [GIẢ ĐỊNH] không có chữ "VND" theo Figma – Q-11 |
| TC_TPLHW_UI_004 | BR-03 | TP linh hoạt – Giao diện | Kiểm tra hiển thị khi tên TCPH rất dài | PC-01; Proxyman sửa issuerName của BHB291193 trong response S7 | 1. Tải lại danh sách<br>2. Quan sát cột "Tổ chức phát hành" dòng BHB291193 | issuerName = "Công ty Cổ phần Tập đoàn Đầu tư Phát triển Năng lượng Tái tạo và Hạ tầng Kỹ thuật Đô thị Bắc Hà Miền Bắc Việt Nam Chi nhánh Hà Nội Số 1 Khu vực Phía Bắc" | - Tên xuống dòng hoặc cắt "…" có tooltip tên đầy đủ<br>- Không đè sang cột "SL Trái phiếu"; các cột khác không vỡ bố cục | Low | UI | EG | SRS không ghi cách cắt chữ – đối chiếu Figma |
| TC_TPLHW_UI_005 | BR-03, BR-06 | TP linh hoạt – Giao diện | Kiểm tra hiển thị khi SL và Tổng mệnh giá rất lớn | PC-01; Proxyman sửa response S7 dòng BHB291193 | 1. Tải lại danh sách<br>2. Quan sát dòng BHB291193 | bondAmount = 999999999; totalFaceValue = 999999999999999 | - SL hiện "999.999.999"; Tổng mệnh giá hiện "999.999.999.999.999"<br>- Không hiện dạng 1e+15, không làm tròn, không tràn sang cột "Thao tác" | Low | Boundary | BVA |  |
| TC_TPLHW_UI_006 | BR-03 | TP linh hoạt – Giao diện | Kiểm tra bố cục màn khi đổi trình duyệt và độ phân giải | TK1 đã đăng nhập | 1. Mở chip "Trái phiếu linh hoạt" lần lượt trên Chrome, Edge, Firefox, Safari (macOS)<br>2. Với mỗi trình duyệt đổi cửa sổ 1366×768 và 1920×1080 | TK1 | - Ở cả 8 tổ hợp: đủ 6 cột, không mất nút "Chi tiết", dropdown bộ lọc mở không bị che<br>- Ở 1366: nếu bảng rộng hơn màn thì cuộn ngang được, tiêu đề vẫn khớp cột | Low | UI | PW | Bổ sung B5 |
| TC_TPLHW_DS_001 | BR-01, BR-18 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra request khi chọn chip Trái phiếu linh hoạt | TK1 đã đăng nhập, đang ở chip "sBond"; PC-02 | 1. Bấm chip "Trái phiếu linh hoạt"<br>2. Xem các request vừa gửi | TK1 | - Có 1 request GET /api/v1/bond/app/flex-bond-holding không có symbol, issuerCode, bondType; page = trang đầu<br>- Có 1 request GET /api/v1/bond/common/issuer/list<br>- Cả 2 trả HTTP 200; bảng hiện dữ liệu TP linh hoạt (không còn dữ liệu tab sBond) | High | Positive | UC |  |
| TC_TPLHW_DS_002 | BR-04, BR-05 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra danh sách mã hiển thị khi KH có TP ở nhiều tiểu khoản | PC-01 | 1. Đếm số dòng của bảng<br>2. Đếm số dòng của từng mã | TK1 (Mã A, B, C, D) | - Bảng có đúng 3 dòng: BHB291193, TPQ12312, ABC12601<br>- Mỗi mã chỉ xuất hiện 1 dòng<br>- Không có XYZ12599 | High | Positive | EP | [GIẢ ĐỊNH] 1 mã 1 dòng dù Figma vẽ trùng – Q-11 |
| TC_TPLHW_DS_003 | BR-05 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra cộng dồn SL khi 1 mã nằm ở 2 tiểu khoản thường | PC-01 | 1. Xem dòng BHB291193<br>2. Làm SOP-01 với mã BHB291193 | Mã A: sub01 6 TP, sub06 4 TP | - SL Trái phiếu = "10" (= 6 + 4)<br>- Không hiện 6 hoặc 4; không tách thành 2 dòng<br>- Khớp tổng số dư sub01 + sub06 trên Flex | High | Positive | EP | [GIẢ ĐỊNH] lấy tổng số dư lưu ký – Q-05 |
| TC_TPLHW_DS_004 | BR-04 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra không cộng số dư sub88 khi mã có cả ở sub thường và sub88 | PC-01 | 1. Xem dòng BHB291193<br>2. Làm SOP-01 với mã BHB291193 | Mã A: sub88 5 TP (HĐ kỳ hạn) | - SL Trái phiếu = "10", không phải "15"<br>- Tổng mệnh giá = "100.000.000", không phải "150.000.000" | High | Negative | EP |  |
| TC_TPLHW_DS_005 | BR-04 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra mã không hiển thị khi chỉ nằm ở sub88 | PC-01 | 1. Tìm dòng XYZ12599 trong bảng (duyệt hết các trang) | Mã D: chỉ sub88 20 TP | - Không có dòng XYZ12599<br>- Response S7 không có phần tử symbol = XYZ12599 | High | Negative | EP |  |
| TC_TPLHW_DS_006 | BR-04 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra mã TP mua trên sàn (không qua SH Bond) được hiển thị | PC-01 | 1. Xem dòng TPQ12312<br>2. Làm SOP-01 với mã TPQ12312 | Mã B: Niêm yết, mua trên sàn, sub01 1.250 TP | - Có dòng TPQ12312 · Niêm yết · Công ty bảo hiểm nhân thọ ABC · 1.250 · 125.000.000<br>- Khớp số dư sub01 trên Flex | High | Positive | EP | BIZ-21 "tất cả TP lưu ký" |
| TC_TPLHW_DS_007 | BR-05 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra SL cập nhật khi số dư 1 tiểu khoản giảm | PC-01; trên Flex UAT chuyển / bán 4 TP BHB291193 khỏi sub06 (sub06 còn 0, sub01 vẫn 6) | 1. Bấm chip "sBond" rồi bấm lại "Trái phiếu linh hoạt"<br>2. Xem dòng BHB291193 | Mã A sau thay đổi: sub01 6, sub06 0 | - SL = "6"; Tổng mệnh giá = "60.000.000"<br>- Vẫn 1 dòng BHB291193 | Medium | Positive | ST | Khôi phục số dư sau khi test |
| TC_TPLHW_DS_008 | BR-05 | TP linh hoạt – Hiển thị & phạm vi | Kiểm tra mã biến mất khi số dư về 0 | PC-01; trên Flex UAT bán / chuyển hết 50 TP ABC12601 ở sub06 | 1. Tải lại danh sách<br>2. Mở ô tìm kiếm, nhập "ABC126" | Mã C sau thay đổi: số dư 0 | - Bảng còn 2 dòng BHB291193, TPQ12312; không có dòng ABC12601 SL 0<br>- Gợi ý không có ABC12601 | Medium | Negative | ST | [GIẢ ĐỊNH] không hiện mã SL 0 – Q-14 |
| TC_TPLHW_CT_001 | BR-06 | TP linh hoạt – Tổng mệnh giá | Kiểm tra Tổng mệnh giá bằng SL × mệnh giá ban đầu | PC-01 | 1. Xem cột Tổng mệnh giá dòng BHB291193 và ABC12601<br>2. Tra mệnh giá ban đầu của 2 mã trên BO Portal | Mã A: 10 × 10.000.000; Mã C: 50 × 1.000.000 | - BHB291193: "100.000.000"<br>- ABC12601: "50.000.000" | High | Positive | EP |  |
| TC_TPLHW_CT_002 | BR-06 | TP linh hoạt – Tổng mệnh giá | Kiểm tra Tổng mệnh giá không tính theo giá giao dịch | PC-01 | 1. Xem cột Tổng mệnh giá dòng TPQ12312 | Mã B: 1.250 TP; MG 100.000; giá giao dịch 98.500 | - Hiện "125.000.000" (1.250 × 100.000)<br>- Không hiện "123.125.000" (1.250 × 98.500) | High | Negative | EP |  |
| TC_TPLHW_CT_003 | BR-06 | TP linh hoạt – Tổng mệnh giá | Kiểm tra Tổng mệnh giá không đổi khi giá giao dịch thay đổi trong ngày | PC-01; ghi lại Tổng mệnh giá TPQ12312 = 125.000.000 | 1. Trên BO / bảng giá UAT đổi giá giao dịch TPQ12312 từ 98.500 sang 102.000<br>2. Tải lại danh sách<br>3. Xem dòng TPQ12312 | Giá mới 102.000 | - Tổng mệnh giá vẫn "125.000.000"<br>- Không hiện "127.500.000" | Medium | Negative | EP |  |
| TC_TPLHW_TK_001 | BR-07, BR-10 | TP linh hoạt – Tìm Mã TP | Kiểm tra lọc khi nhập đủ mã TP và chọn gợi ý | PC-02 | 1. Click ô "Mã trái phiếu"<br>2. Nhập "BHB291193"<br>3. Chọn BHB291193 trong gợi ý | BHB291193 | - Bảng còn 1 dòng BHB291193 · SL 10 · 100.000.000<br>- Request S7 có symbol = BHB291193<br>- Ô tìm kiếm hiện "BHB291193" | Medium | Positive | EP | [GIẢ ĐỊNH] áp dụng ngay khi chọn – Q-02 |
| TC_TPLHW_TK_002 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra gợi ý khi nhập một phần mã TP | PC-01 | 1. Nhập "2911" vào ô "Mã trái phiếu"<br>2. Quan sát danh sách gợi ý | 2911 | - Gợi ý có BHB291193<br>- Gợi ý không có TPQ12312, ABC12601 | Medium | Positive | EP |  |
| TC_TPLHW_TK_003 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra gợi ý khi nhập chữ thường | PC-01 | 1. Nhập "bhb291193"<br>2. Quan sát gợi ý | bhb291193 | - Gợi ý có BHB291193 (không phân biệt hoa thường) | Medium | Positive | EP |  |
| TC_TPLHW_TK_004 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra gợi ý khi nhập cụm từ trong tên TP | PC-01; tra tên TP của BHB291193 trên BO Portal | 1. Nhập 1 cụm từ nằm giữa tên TP của BHB291193<br>2. Quan sát gợi ý | Cụm từ trong tên TP của BHB291193 (VD "Bắc Hà") | - Gợi ý có BHB291193<br>- Không có mã có tên không chứa cụm từ | Medium | Positive | EP | [GIẢ ĐỊNH] tìm cả theo tên – Q-03 |
| TC_TPLHW_TK_005 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra cắt khoảng trắng 2 đầu từ khóa | PC-02 | 1. Nhập "  BHB291193  " (2 khoảng trắng mỗi đầu)<br>2. Chọn gợi ý BHB291193 | "  BHB291193  " | - Gợi ý có BHB291193<br>- Request gợi ý gửi keyword = "BHB291193" (không có khoảng trắng); request S7 symbol = BHB291193<br>- Bảng còn 1 dòng BHB291193 | Medium | Positive | EP | V01 |
| TC_TPLHW_TK_006 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra bỏ lọc khi xóa trống ô tìm kiếm | PC-02; đã lọc theo BHB291193 (1 dòng) | 1. Xóa hết nội dung ô "Mã trái phiếu" (hoặc bấm icon xóa) | Ô trống | - Bảng hiện lại 3 dòng A, B, C<br>- Request S7 mới không có symbol | Medium | Positive | EP |  |
| TC_TPLHW_TK_007 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra ô tìm kiếm khi chỉ nhập khoảng trắng | PC-02 | 1. Nhập "   " (3 khoảng trắng)<br>2. Nhấn Enter | "   " | - Không lọc: bảng giữ 3 dòng; không hiện empty state<br>- Không gửi request S7 có symbol / keyword là khoảng trắng<br>- Không có popup lỗi | Medium | Negative | EG | Bổ sung B5 |
| TC_TPLHW_TK_008 | BR-07, BR-13 | TP linh hoạt – Tìm Mã TP | Kiểm tra kết quả khi từ khóa không khớp mã nào | PC-01 | 1. Nhập "ZZZ999"<br>2. Quan sát gợi ý<br>3. Nhấn Enter | ZZZ999 | - Gợi ý trống (không có mã nào)<br>- Nếu Enter lọc: bảng hiện empty state "Không tìm thấy kết quả phù hợp"; nếu Enter không lọc: bảng giữ 3 dòng | Medium | Negative | EP | [GIẢ ĐỊNH] hành vi Enter – Q-03 |
| TC_TPLHW_TK_009 | BR-07, BR-04 | TP linh hoạt – Tìm Mã TP | Kiểm tra gợi ý không có mã chỉ nằm ở sub88 | PC-01 | 1. Nhập "XYZ12599"<br>2. Quan sát gợi ý | Mã D (chỉ sub88) | - Gợi ý không có XYZ12599<br>- Không lọc ra dòng nào của XYZ12599 | High | Negative | EP | [GIẢ ĐỊNH] gợi ý chỉ gồm mã đang nắm giữ linh hoạt – Q-03 |
| TC_TPLHW_TK_010 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra nhập từ khóa đúng 50 ký tự | PC-01 | 1. Nhập chuỗi 50 ký tự vào ô "Mã trái phiếu" | "BHB291193" × 5 + "ABCDE" (50 ký tự) | - Ô nhận đủ 50 ký tự<br>- Không có lỗi; gợi ý trống | Medium | Boundary | BVA | [GIẢ ĐỊNH] max 50 – Q-04 |
| TC_TPLHW_TK_011 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra chặn nhập ký tự thứ 51 | PC-01 | 1. Nhập chuỗi 50 ký tự<br>2. Gõ thêm "F" | "BHB291193" × 5 + "ABCDE" + "F" | - Ô giữ 50 ký tự, không nhận "F"<br>- Không gửi request với từ khóa 51 ký tự | Medium | Boundary | BVA | [GIẢ ĐỊNH] max 50 – Q-04 |
| TC_TPLHW_TK_012 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra dán chuỗi 60 ký tự vào ô tìm kiếm | PC-01 | 1. Copy chuỗi 60 ký tự<br>2. Dán (Ctrl+V) vào ô "Mã trái phiếu" | "BHB291193" × 6 + "ABCDEF" (60 ký tự) | - Ô chỉ giữ 50 ký tự đầu<br>- Không có popup lỗi hệ thống | Medium | Boundary | BVA | [GIẢ ĐỊNH] max 50 – Q-04 |
| TC_TPLHW_TK_013 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra ô tìm kiếm khi nhập chuỗi script | PC-01 | 1. Nhập chuỗi script<br>2. Nhấn Enter | <script>alert(1)</script> | - Không bật hộp thoại alert; chuỗi hiện dạng text trong ô<br>- Gợi ý trống; không có HTTP 500 | Medium | Security | EG |  |
| TC_TPLHW_TK_014 | BR-07 | TP linh hoạt – Tìm Mã TP | Kiểm tra ô tìm kiếm khi nhập chuỗi SQL injection | PC-02 | 1. Nhập chuỗi SQL<br>2. Nhấn Enter | ' OR 1=1 -- | - Gợi ý trống; không trả thêm mã ngoài A, B, C<br>- Response không HTTP 500, không lộ câu lỗi DB | Medium | Security | EG |  |
| TC_TPLHW_TK_015 | BR-07, BR-15 | TP linh hoạt – Tìm Mã TP | Kiểm tra gợi ý khi nhập mã của KH khác | PC-01 | 1. Nhập "GHI12603"<br>2. Quan sát gợi ý | Mã E (chỉ TK5 có) | - Gợi ý không có GHI12603<br>- Không lọc ra dòng nào | High | Security | EG |  |
| TC_TPLHW_TC_001 | BR-08, BR-18 | TP linh hoạt – Lọc TCPH | Kiểm tra danh sách TCPH khi mở dropdown | PC-02 | 1. Click dropdown "Tổ chức phát hành" | TK1 | - Danh sách lấy từ response C2 (data[].issuerName), có "CTCP Năng lượng Bắc Hà", "Công ty bảo hiểm nhân thọ ABC"<br>- Mỗi TCPH có ô chọn; chưa ô nào được chọn | Medium | Positive | EP | [GIẢ ĐỊNH] C2 trả toàn bộ TCPH – Q-06 |
| TC_TPLHW_TC_002 | BR-08, BR-10 | TP linh hoạt – Lọc TCPH | Kiểm tra lọc khi chọn 1 TCPH | PC-02 | 1. Click dropdown TCPH<br>2. Chọn "Công ty bảo hiểm nhân thọ ABC" | Công ty bảo hiểm nhân thọ ABC | - Dropdown hiện "Đã chọn 1 tổ chức"<br>- Bảng còn 2 dòng TPQ12312, ABC12601<br>- Request S7 có issuerCode = mã của Công ty bảo hiểm nhân thọ ABC | Medium | Positive | EP | [GIẢ ĐỊNH] áp dụng ngay – Q-02 |
| TC_TPLHW_TC_003 | BR-08 | TP linh hoạt – Lọc TCPH | Kiểm tra lọc khi chọn 2 TCPH | PC-02 | 1. Click dropdown TCPH<br>2. Chọn "Công ty bảo hiểm nhân thọ ABC"<br>3. Chọn "CTCP Năng lượng Bắc Hà" | 2 TCPH | - Dropdown hiện "Đã chọn 2 tổ chức"<br>- Bảng hiện 3 dòng A, B, C<br>- Request S7 issuerCode gồm 2 mã | Medium | Positive | EP |  |
| TC_TPLHW_TC_004 | BR-08 | TP linh hoạt – Lọc TCPH | Kiểm tra bỏ chọn hết TCPH | PC-02; đang chọn 1 TCPH "Công ty bảo hiểm nhân thọ ABC" | 1. Click dropdown TCPH<br>2. Bỏ chọn "Công ty bảo hiểm nhân thọ ABC" | 0 TCPH | - Dropdown hiện "Tất cả tổ chức"<br>- Bảng hiện lại 3 dòng<br>- Request S7 không có issuerCode | Medium | Positive | BVA |  |
| TC_TPLHW_TC_005 | BR-08, BR-13 | TP linh hoạt – Lọc TCPH | Kiểm tra kết quả khi chọn TCPH không có TP linh hoạt của KH | PC-01 | 1. Click dropdown TCPH<br>2. Chọn "CTCP Đầu tư XYZ" | CTCP Đầu tư XYZ (TK1 chỉ có XYZ12599 ở sub88) | - Bảng hiện empty state "Không tìm thấy kết quả phù hợp"<br>- Không hiện XYZ12599 | Medium | Negative | EP | [GIẢ ĐỊNH] Q-06, Q-10 |
| TC_TPLHW_LT_001 | BR-09 | TP linh hoạt – Lọc Loại TP | Kiểm tra lựa chọn của dropdown Loại trái phiếu | PC-01 | 1. Click dropdown "Loại trái phiếu" | — | - Có 3 lựa chọn theo thứ tự: Tất cả · Niêm yết · Riêng lẻ<br>- "Tất cả" đang được chọn (chữ cam) | Low | UI | EP |  |
| TC_TPLHW_LT_002 | BR-09, BR-10 | TP linh hoạt – Lọc Loại TP | Kiểm tra lọc khi chọn Niêm yết từ trạng thái Tất cả | PC-02 | 1. Click dropdown Loại TP<br>2. Chọn "Niêm yết" | Niêm yết | - "Tất cả" tự bỏ chọn; chỉ "Niêm yết" được chọn<br>- Bảng còn 1 dòng TPQ12312, cột Loại TP = "Niêm yết"<br>- Request S7 có bondType = Niêm yết | Medium | Positive | ST | [GIẢ ĐỊNH] Q-01, Q-02 |
| TC_TPLHW_LT_003 | BR-09 | TP linh hoạt – Lọc Loại TP | Kiểm tra lọc khi chọn Riêng lẻ từ trạng thái Tất cả | PC-02 | 1. Click dropdown Loại TP<br>2. Chọn "Riêng lẻ" | Riêng lẻ | - Chỉ "Riêng lẻ" được chọn<br>- Bảng còn 2 dòng BHB291193, ABC12601, cột Loại TP = "Riêng lẻ"<br>- Request S7 có bondType = Riêng lẻ | Medium | Positive | ST |  |
| TC_TPLHW_LT_004 | BR-09 | TP linh hoạt – Lọc Loại TP | Kiểm tra lọc khi chọn cả Niêm yết và Riêng lẻ | PC-02; đang chọn "Niêm yết" | 1. Click dropdown Loại TP<br>2. Chọn thêm "Riêng lẻ" | Niêm yết + Riêng lẻ | - Niêm yết và Riêng lẻ cùng được chọn; "Tất cả" không chọn<br>- Bảng hiện 3 dòng A, B, C<br>- Request S7 bondType gồm 2 giá trị | Medium | Positive | ST |  |
| TC_TPLHW_LT_005 | BR-09 | TP linh hoạt – Lọc Loại TP | Kiểm tra chọn Tất cả khi đang chọn Niêm yết và Riêng lẻ | PC-02; đang chọn Niêm yết + Riêng lẻ | 1. Click dropdown Loại TP<br>2. Chọn "Tất cả" | Tất cả | - Niêm yết và Riêng lẻ tự bỏ chọn; chỉ "Tất cả" được chọn<br>- Bảng hiện 3 dòng<br>- Request S7 không có bondType | Medium | Positive | ST |  |
| TC_TPLHW_LT_006 | BR-09 | TP linh hoạt – Lọc Loại TP | Kiểm tra về Tất cả khi bỏ chọn chip duy nhất | PC-02; đang chọn "Niêm yết" | 1. Click dropdown Loại TP<br>2. Bỏ chọn "Niêm yết" | — | - "Tất cả" tự được chọn<br>- Bảng hiện lại 3 dòng; request S7 không có bondType | Medium | Positive | ST |  |
| TC_TPLHW_LT_007 | BR-09 | TP linh hoạt – Lọc Loại TP | Kiểm tra bỏ chọn Tất cả khi không có chip nào khác | PC-02 (đang "Tất cả") | 1. Click dropdown Loại TP<br>2. Bấm vào "Tất cả" (đang chọn) | — | - "Tất cả" vẫn được chọn; dropdown không về trạng thái trống<br>- Bảng giữ 3 dòng; không gửi request S7 bondType rỗng | Medium | Negative | ST | [GIẢ ĐỊNH] SRS không ghi rõ ô này |
| TC_TPLHW_LC_001 | BR-10 | TP linh hoạt – Kết hợp bộ lọc | Kiểm tra kết hợp TCPH và Loại TP khi có kết quả | PC-02 | 1. Chọn TCPH "Công ty bảo hiểm nhân thọ ABC"<br>2. Chọn Loại TP "Riêng lẻ" | R4: ABC + Riêng lẻ | - Bảng còn 1 dòng ABC12601<br>- Request S7 cuối có cả issuerCode và bondType = Riêng lẻ | Medium | Positive | DT |  |
| TC_TPLHW_LC_002 | BR-10, BR-13 | TP linh hoạt – Kết hợp bộ lọc | Kiểm tra kết hợp TCPH và Loại TP khi không có kết quả | PC-01 | 1. Chọn TCPH "CTCP Năng lượng Bắc Hà"<br>2. Chọn Loại TP "Niêm yết" | R5: Bắc Hà + Niêm yết | - Bảng hiện empty state "Không tìm thấy kết quả phù hợp"<br>- Không có thanh phân trang | Medium | Negative | DT | [GIẢ ĐỊNH] Q-10 |
| TC_TPLHW_LC_003 | BR-10, BR-13 | TP linh hoạt – Kết hợp bộ lọc | Kiểm tra kết hợp Mã TP và Loại TP khi không khớp | PC-01 | 1. Lọc Mã TP = TPQ12312 (chọn gợi ý)<br>2. Chọn Loại TP "Riêng lẻ" | R7: TPQ12312 + Riêng lẻ | - Bảng hiện empty state "Không tìm thấy kết quả phù hợp"<br>- Ô tìm kiếm vẫn giữ "TPQ12312" | Medium | Negative | DT |  |
| TC_TPLHW_LC_004 | BR-10, BR-16 | TP linh hoạt – Kết hợp bộ lọc | Kiểm tra đặt lại bộ lọc về mặc định | PC-02; đang lọc Mã TP = BHB291193, TCPH = CTCP Năng lượng Bắc Hà, Loại = Riêng lẻ | 1. Bấm "Đặt lại" (hoặc xóa từng điều kiện nếu không có nút) | — | - Ô tìm kiếm trống; TCPH "Tất cả tổ chức"; Loại TP "Tất cả"<br>- Bảng hiện 3 dòng; request S7 không có symbol, issuerCode, bondType | Medium | Positive | UC | [GIẢ ĐỊNH] cách đặt lại – Q-02 |
| TC_TPLHW_LC_005 | BR-10, BR-11 | TP linh hoạt – Kết hợp bộ lọc | Kiểm tra về trang 1 khi đổi bộ lọc lúc đang ở trang 2 | TK3 đã đăng nhập, mở chip TP linh hoạt; PC-02 | 1. Bấm trang "2"<br>2. Chọn Loại TP "Riêng lẻ" | TK3 | - Request S7 mới có page = trang đầu<br>- Thanh phân trang đánh dấu trang 1; không hiện empty state do giữ page = 2 | Medium | Negative | EG | [GIẢ ĐỊNH] về trang 1 khi đổi lọc |
| TC_TPLHW_LC_006 | BR-10 | TP linh hoạt – Kết hợp bộ lọc | Kiểm tra kết quả khi đổi bộ lọc nhanh liên tiếp | PC-02; DevTools đặt "Slow 3G" | 1. Chọn Loại TP "Niêm yết"<br>2. Trong vòng 1 giây chọn thêm "Riêng lẻ" rồi bỏ "Niêm yết" | Kết quả cuối: Riêng lẻ | - Sau khi các request xong: bảng hiện BHB291193, ABC12601 (đúng điều kiện cuối "Riêng lẻ")<br>- Không hiện kết quả của request cũ về sau | Medium | Negative | EG | Bổ sung B5 |
| TC_TPLHW_PT_001 | BR-11 | TP linh hoạt – Phân trang | Kiểm tra phân trang khi KH có 12 mã TP linh hoạt | TK3 đã đăng nhập, mở chip TP linh hoạt | 1. Đếm số dòng trang 1<br>2. Quan sát thanh phân trang | TK3: 12 mã | - Trang 1 có 10 dòng<br>- Thanh phân trang có "1", "2"; trang 1 được đánh dấu | Medium | Boundary | BVA | [GIẢ ĐỊNH] 10 dòng/trang – Q-08 |
| TC_TPLHW_PT_002 | BR-11, BR-18 | TP linh hoạt – Phân trang | Kiểm tra chuyển sang trang cuối | TK3; PC-02 (đăng nhập TK3) | 1. Ghi lại 10 mã trang 1<br>2. Bấm trang "2" | TK3 | - Trang 2 có 2 dòng; không trùng mã nào với trang 1; tổng 12 mã khác nhau<br>- Request S7 page = 2, size = 10 | Medium | Positive | BVA | Q-08, Q-09 |
| TC_TPLHW_PT_003 | BR-11 | TP linh hoạt – Phân trang | Kiểm tra phân trang khi dữ liệu chỉ đủ 1 trang | PC-01 | 1. Quan sát thanh phân trang | TK1: 3 mã | - Chỉ có trang "1" (hoặc ẩn phân trang)<br>- Nút "Trước" / "Sau" bị vô hiệu | Low | Boundary | BVA | Q-08 |
| TC_TPLHW_PT_004 | BR-11 | TP linh hoạt – Phân trang | Kiểm tra nút Trước ở trang đầu và Sau ở trang cuối | TK3; PC-02 | 1. Ở trang 1 bấm "Trước"<br>2. Bấm trang "2"<br>3. Bấm "Sau" | TK3 | - Bước 1 và 3: không gửi request S7 mới; trang không đổi<br>- Nút "Trước" mờ ở trang 1; nút "Sau" mờ ở trang 2 | Low | Negative | BVA |  |
| TC_TPLHW_PT_005 | BR-11 | TP linh hoạt – Phân trang | Kiểm tra phân trang khi số mã giảm sau khi lọc | TK3; đang ở trang 2 | 1. Lọc 1 TCPH chỉ có 3 mã trong TK3 | TCPH có 3 mã của TK3 | - Bảng hiện 3 dòng ở trang 1<br>- Thanh phân trang chỉ còn trang "1" | Medium | Boundary | BVA |  |
| TC_TPLHW_CTI_001 | BR-12, BR-16 | TP linh hoạt – Chi tiết | Kiểm tra mở Chi tiết TP linh hoạt từ dòng đầu tiên | PC-01 | 1. Bấm "Chi tiết" ở dòng BHB291193 | BHB291193 | - Mở Chi tiết trái phiếu linh hoạt (HOLDING.WEB.04)<br>- Mã trái phiếu trong màn chi tiết = "BHB291193"; TCPH = "CTCP Năng lượng Bắc Hà" | High | Positive | UC | Nội dung chi tiết thuộc HOLDING.WEB.04 |
| TC_TPLHW_CTI_002 | BR-12 | TP linh hoạt – Chi tiết | Kiểm tra Chi tiết mở đúng mã khi bấm ở dòng thứ 3 | PC-01 | 1. Bấm "Chi tiết" ở dòng ABC12601 | ABC12601 | - Màn chi tiết hiện Mã trái phiếu = "ABC12601"<br>- Không hiện dữ liệu của BHB291193 / TPQ12312 | High | Positive | EG |  |
| TC_TPLHW_CTI_003 | BR-12 | TP linh hoạt – Chi tiết | Kiểm tra quay lại danh sách sau khi đóng Chi tiết | PC-01; đang lọc Loại TP "Riêng lẻ" (2 dòng) | 1. Bấm "Chi tiết" dòng ABC12601<br>2. Bấm "Đóng" | — | - Quay lại bảng TP linh hoạt, chip "Trái phiếu linh hoạt" vẫn chọn<br>- Bộ lọc vẫn "Riêng lẻ", bảng vẫn 2 dòng BHB291193, ABC12601 | Medium | Positive | EG | Bổ sung B5; [GIẢ ĐỊNH] giữ bộ lọc |
| TC_TPLHW_CTI_004 | BR-12 | TP linh hoạt – Chi tiết | Kiểm tra double-click nút Chi tiết | PC-01 | 1. Double-click nhanh nút "Chi tiết" dòng BHB291193 | BHB291193 | - Chỉ mở 1 màn / popup chi tiết<br>- Không có 2 popup chồng nhau | Low | Negative | EG |  |
| TC_TPLHW_NT_001 | BR-02, BR-01 | TP linh hoạt – Noti & chuyển Outright | Kiểm tra mở màn từ noti Bond_05 | TK4 đã nhận noti Bond_05 (sau NT_002); đang ở màn khác của SHSmart Web | 1. Bấm icon chuông<br>2. Bấm noti Bond_05 | TK4 | - Mở "Danh sách nắm giữ" > "Danh mục trái phiếu", chip "Trái phiếu linh hoạt" được chọn<br>- Bộ lọc mặc định; có request S7 không tham số lọc<br>- Bảng có DEF12602 | High | Positive | UC | [GIẢ ĐỊNH] đích điều hướng – Q-07 |
| TC_TPLHW_NT_002 | BR-17, BR-04 | TP linh hoạt – Noti & chuyển Outright | Kiểm tra mã TP mới xuất hiện khi HĐ kỳ hạn bị off tại COT ngày E | TK4; HĐ-E1 thiếu hồ sơ tại ngày E; job COT ngày E đã chạy xong | 1. Đăng nhập TK4, mở chip TP linh hoạt<br>2. Xem dòng DEF12602<br>3. Làm SOP-01 với DEF12602 | HĐ-E1: DEF12602, 30 TP, MG 1.000.000 | - Có dòng DEF12602 · Riêng lẻ · CTCP Đầu tư DEF · 30 · 30.000.000<br>- Flex: 30 TP DEF12602 nằm ở sub ≠ sub88<br>- TK4 nhận Noti/Email Bond_05 | High | Positive | ST | [GIẢ ĐỊNH] sub nhận và thời điểm – Q-07 |
| TC_TPLHW_NT_003 | BR-17, BR-04 | TP linh hoạt – Noti & chuyển Outright | Kiểm tra mã TP chưa xuất hiện trước khi job COT ngày E chạy | TK4; ngày E, trước giờ COT; HĐ-E1 đang thiếu hồ sơ | 1. Đăng nhập TK4, mở chip TP linh hoạt<br>2. Tìm dòng DEF12602 | HĐ-E1 còn hiệu lực | - Không có dòng DEF12602 (30 TP vẫn ở sub88)<br>- Chưa có noti Bond_05 | High | Negative | ST | Chạy trước NT_002 |
| TC_TPLHW_NT_004 | BR-17, BR-05 | TP linh hoạt – Noti & chuyển Outright | Kiểm tra cộng dồn khi số dư chuyển Outright vào mã đã có ở sub thường | TK4; HĐ-E2 bị off tại COT ngày E; trước đó dòng BHB291193 SL = 8 | 1. Đăng nhập TK4, mở chip TP linh hoạt<br>2. Xem dòng BHB291193 | BHB291193: sub01 8 TP + Outright 5 TP | - Vẫn 1 dòng BHB291193; SL = "13"; Tổng mệnh giá = "130.000.000"<br>- Không có 2 dòng BHB291193 | High | Positive | ST | Bổ sung B5; [GIẢ ĐỊNH] Q-07 |
| TC_TPLHW_ER_001 | BR-13 | TP linh hoạt – Trống & lỗi | Kiểm tra empty state khi KH chưa có TP linh hoạt | TK2 đã đăng nhập | 1. Mở chip "Trái phiếu linh hoạt" | TK2 (chỉ có TP ở sub88) | - Vùng bảng hiện empty state "Quý khách chưa có trái phiếu linh hoạt"<br>- Không có dòng nào, không có thanh phân trang<br>- Response S7: data.content = [] | Medium | Positive | EP | [GIẢ ĐỊNH] hình / CTA – Q-10 |
| TC_TPLHW_ER_002 | BR-14 | TP linh hoạt – Trống & lỗi | Kiểm tra popup lỗi khi S7 trả BND-901 | PC-01; SOP-02 với BND-901 | 1. Tải lại danh sách (bấm chip "sBond" rồi "Trái phiếu linh hoạt") | BND-901 | - Popup lỗi chuẩn SHSmart: "Hệ thống đang bận", nút "Đóng"<br>- Bấm "Đóng": popup tắt; bảng không hiện dữ liệu cũ / sai | Medium | Negative | EG | [GIẢ ĐỊNH] ánh xạ mã → câu – Q-12 |
| TC_TPLHW_ER_003 | BR-14 | TP linh hoạt – Trống & lỗi | Kiểm tra popup lỗi khi S7 trả BND-999 | PC-01; SOP-02 với BND-999 | 1. Chọn Loại TP "Niêm yết" | BND-999 | - Popup "Có lỗi xảy ra. Vui lòng thử lại sau", nút "Đóng"<br>- Bảng không hiện kết quả lọc sai | Medium | Negative | EG | [GIẢ ĐỊNH] Q-12 |
| TC_TPLHW_ER_004 | BR-14, BR-15 | TP linh hoạt – Trống & lỗi | Kiểm tra popup khi phiên đăng nhập hết hạn (AUTH_001) | PC-01; token hết hạn (đợi quá thời gian phiên hoặc SOP-02 với AUTH_001) | 1. Chọn TCPH "Công ty bảo hiểm nhân thọ ABC"<br>2. Bấm "Đăng nhập" trên popup | AUTH_001 | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", nút "Đăng nhập"<br>- Bấm "Đăng nhập" → chuyển màn đăng nhập<br>- Bảng không hiện dữ liệu | High | Negative | EG | [GIẢ ĐỊNH] Q-12 |
| TC_TPLHW_ER_005 | BR-14, BR-08 | TP linh hoạt – Trống & lỗi | Kiểm tra khi C2 lỗi còn S7 thành công | PC-01; Proxyman sửa response C2 thành BND-999 | 1. Tải lại danh sách<br>2. Click dropdown TCPH | C2 = BND-999 | - Hiện popup lỗi chuẩn SHSmart "Có lỗi xảy ra. Vui lòng thử lại sau"<br>- Dropdown TCPH không có TCPH sai; bảng vẫn hiện 3 dòng từ S7 | Medium | Negative | EG | [GIẢ ĐỊNH] SRS không ghi xử lý riêng lỗi C2 – Q-12 |
| TC_TPLHW_ER_006 | BR-14 | TP linh hoạt – Trống & lỗi | Kiểm tra khi mất mạng lúc đổi bộ lọc | PC-02 | 1. DevTools Network đặt "Offline"<br>2. Chọn Loại TP "Riêng lẻ"<br>3. Đặt lại "No throttling", tải lại danh sách | Offline | - Bước 2: có thông báo lỗi (popup chuẩn SHSmart), không treo spinner vô hạn; bảng không hiện kết quả sai<br>- Bước 3: bảng hiện 3 dòng | Medium | Negative | EG | [GIẢ ĐỊNH] Q-12 |
| TC_TPLHW_BM_001 | BR-15 | TP linh hoạt – Phân quyền & bảo mật | Kiểm tra truy cập màn khi chưa đăng nhập | Trình duyệt ẩn danh, chưa đăng nhập | 1. Dán URL màn Danh mục trái phiếu > TP linh hoạt (copy từ phiên TK1) | URL màn | - Chuyển sang màn đăng nhập<br>- Không hiện dữ liệu TP; S7 (nếu được gọi) trả AUTH_001 | High | Permission | EG |  |
| TC_TPLHW_BM_002 | BR-15 | TP linh hoạt – Phân quyền & bảo mật | Kiểm tra dữ liệu khi đổi sang tài khoản khác trên cùng trình duyệt | PC-01 | 1. Đăng xuất TK1<br>2. Đăng nhập TK5<br>3. Mở chip TP linh hoạt | TK5 (Mã E GHI12603) | - Bảng chỉ có dữ liệu của TK5 (có GHI12603)<br>- Không còn BHB291193, TPQ12312, ABC12601 của TK1 (không cache) | High | Permission | EG |  |
| TC_TPLHW_BM_003 | BR-15 | TP linh hoạt – Phân quyền & bảo mật | Kiểm tra màn chỉ xem, không đổi số dư | PC-02; làm SOP-01 ghi số dư BHB291193 trước khi test | 1. Lọc, đổi trang, mở Chi tiết vài lần<br>2. Xem các request đã gửi<br>3. Làm lại SOP-01 | TK1 | - Chỉ có request GET (không có POST / PUT / DELETE)<br>- Số dư BHB291193 trên Flex trước và sau không đổi | Medium | Positive | EG | Post-Condition |
| TC_TPLHW_EVT_001 | BR-16 | TP linh hoạt – Tracking | Kiểm tra ghi nhận sự kiện LIST, FILTER, DETAIL | PC-01; công cụ tracking UAT đang mở | 1. Bấm chip "sBond" rồi "Trái phiếu linh hoạt"<br>2. Chọn Loại TP "Niêm yết"<br>3. Bấm "Chi tiết" dòng TPQ12312 | TK1 | - Bước 1: có 1 sự kiện SBOND.HOLDING.FLEX.LIST<br>- Bước 2: có 1 sự kiện SBOND.HOLDING.FLEX.FILTER<br>- Bước 3: có 1 sự kiện SBOND.HOLDING.FLEX.DETAIL | Low | Integration | UC | [GIẢ ĐỊNH] cách kiểm – Q-13 |

### 4.2 API S7 / C2 (8 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_TPLHW_API_001 | BR-18, BR-04, BR-05, BR-06 | TP linh hoạt – API S7 / C2 | Kiểm tra S7 khi gọi với bộ lọc mặc định | PC-03 | 1. GET /api/v1/bond/app/flex-bond-holding?page=<trang đầu>&size=10 | Token TK1 | - HTTP 200<br>- data.content có 3 phần tử; mỗi phần tử có symbol, issuerName, bondAmount, totalFaceValue<br>- BHB291193: bondAmount = 10, totalFaceValue = 100000000; TPQ12312: bondAmount = 1250, totalFaceValue = 125000000<br>- Không có phần tử XYZ12599 | Medium | Positive | EP | Field Loại TP chưa có trong SRS – Q-01 |
| TC_TPLHW_API_002 | BR-18, BR-07, BR-08, BR-09 | TP linh hoạt – API S7 / C2 | Kiểm tra S7 khi lọc theo symbol, issuerCode và bondType | PC-03 | 1. GET S7 với symbol = BHB291193<br>2. GET S7 với issuerCode = mã của Công ty bảo hiểm nhân thọ ABC<br>3. GET S7 với bondType = Niêm yết | symbol / issuerCode / bondType | - Lần 1: HTTP 200, 1 phần tử BHB291193<br>- Lần 2: HTTP 200, 2 phần tử TPQ12312, ABC12601<br>- Lần 3: HTTP 200, 1 phần tử TPQ12312 | Medium | Positive | EP |  |
| TC_TPLHW_API_003 | BR-18, BR-08 | TP linh hoạt – API S7 / C2 | Kiểm tra C2 trả danh sách TCPH | PC-03 | 1. GET /api/v1/bond/common/issuer/list | Token TK1 | - HTTP 200<br>- data[] có issuerName "CTCP Năng lượng Bắc Hà", "Công ty bảo hiểm nhân thọ ABC"; mỗi phần tử có mã TCPH dùng làm issuerCode | Low | Positive | EP | Q-06 |
| TC_TPLHW_API_004 | BR-18, BR-11 | TP linh hoạt – API S7 / C2 | Kiểm tra S7 khi page và size sai | PC-03 | 1. GET S7 page = 999, size = 10<br>2. GET S7 page = -1<br>3. GET S7 size = 0 | page = 999 / -1; size = 0 | - page = 999: HTTP 200, data.content = []<br>- page = -1 và size = 0: báo lỗi tham số (HTTP 400 / BND-100), không HTTP 500, không trả toàn bộ dữ liệu | Medium | Negative | BVA | Error code tham số chưa có trong SRS |
| TC_TPLHW_API_005 | BR-18, BR-07 | TP linh hoạt – API S7 / C2 | Kiểm tra S7 khi từ khóa dài hơn 50 ký tự | PC-03 | 1. GET S7 với keyword 51 ký tự | "BHB291193" × 5 + "ABCDEF" | - Báo lỗi BND-100 (V01)<br>- Không HTTP 500; không trả dữ liệu | Medium | Negative | BVA | [GIẢ ĐỊNH] max 50 – Q-04 |
| TC_TPLHW_API_006 | BR-18, BR-09 | TP linh hoạt – API S7 / C2 | Kiểm tra S7 khi bondType không hợp lệ | PC-03 | 1. GET S7 với bondType = ABC | bondType = ABC | - Báo lỗi tham số (HTTP 400 / BND-100) hoặc data.content = []<br>- Không HTTP 500; không trả toàn bộ danh sách như không lọc | Medium | Negative | EP | Bổ sung B5 |
| TC_TPLHW_API_007 | BR-18, BR-15 | TP linh hoạt – API S7 / C2 | Kiểm tra S7 và C2 khi không có token | Postman, bỏ header Authorization | 1. GET S7<br>2. GET C2 | — | - Cả 2 trả HTTP 401, error code AUTH_001<br>- Không trả dữ liệu | Medium | Security | EP |  |
| TC_TPLHW_API_008 | BR-18, BR-15 | TP linh hoạt – API S7 / C2 | Kiểm tra S7 khi gửi kèm định danh tài khoản của KH khác | PC-03; lấy số TKCK của TK5 | 1. GET S7 thêm tham số tài khoản (VD accountNo / custodyCd) = TKCK của TK5, token TK1 | Token TK1 + TKCK TK5 | - Chỉ trả dữ liệu của TK1 (bỏ qua tham số) hoặc HTTP 403<br>- Không có GHI12603 của TK5 | High | Security | EG | Tên tham số tài khoản chưa có trong SRS |

## B5. Tự review + RTM
### RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ |
|---|---|---|---|---|
| BR-01 | Chọn chip "Trái phiếu linh hoạt" → Web gọi S7 / C2 với bộ lọc mặc định… | 3 | TC_TPLHW_UI_001, TC_TPLHW_DS_001, TC_TPLHW_NT_001 | Đủ |
| BR-02 | Vào màn từ noti Bond_05 → mở danh sách TP linh hoạt, gọi S7 / C2 với b… | 1 | TC_TPLHW_NT_001 | Đủ |
| BR-03 | Bảng hiển thị: Mã TP, Loại TP [GĐ Q-01], TCPH, SL TP (#.###), Tổng mện… | 5 | TC_TPLHW_UI_002, TC_TPLHW_UI_003, TC_TPLHW_UI_004, TC_TPLHW_UI_005, TC_TPLHW_UI_006 | Đủ |
| BR-04 | Phạm vi (BIZ-21): tất cả TP lưu ký trong TKCK của KH ở mọi tiểu khoản,… | 8 | TC_TPLHW_DS_002, TC_TPLHW_DS_004, TC_TPLHW_DS_005, TC_TPLHW_DS_006, TC_TPLHW_TK_009, TC_TPLHW_NT_002, TC_TPLHW_NT_003, TC_TPLHW_API_001 | Đủ |
| BR-05 | Gộp theo mã TP (BIZ-21): 1 mã = 1 dòng; SL TP = tổng KL của mã KH đang… | 6 | TC_TPLHW_DS_002, TC_TPLHW_DS_003, TC_TPLHW_DS_007, TC_TPLHW_DS_008, TC_TPLHW_NT_004, TC_TPLHW_API_001 | Đủ |
| BR-06 | Tổng mệnh giá = SL TP × Mệnh giá niêm yết ban đầu; không biến động the… | 5 | TC_TPLHW_UI_005, TC_TPLHW_CT_001, TC_TPLHW_CT_002, TC_TPLHW_CT_003, TC_TPLHW_API_001 | Đủ |
| BR-07 | Ô "Mã trái phiếu": nhập tự do, tìm gần đúng (bao gồm) trong mã/tên TP,… | 17 | TC_TPLHW_TK_001, TC_TPLHW_TK_002, TC_TPLHW_TK_003, TC_TPLHW_TK_004, TC_TPLHW_TK_005, TC_TPLHW_TK_006, TC_TPLHW_TK_007, TC_TPLHW_TK_008, TC_TPLHW_TK_009, TC_TPLHW_TK_010, TC_TPLHW_TK_011, TC_TPLHW_TK_012, TC_TPLHW_TK_013, TC_TPLHW_TK_014, TC_TPLHW_TK_015, TC_TPLHW_API_002, TC_TPLHW_API_005 | Đủ |
| BR-08 | Lọc "Tổ chức phát hành": chọn nhiều; mặc định "Tất cả tổ chức"; đã chọ… | 9 | TC_TPLHW_UI_001, TC_TPLHW_TC_001, TC_TPLHW_TC_002, TC_TPLHW_TC_003, TC_TPLHW_TC_004, TC_TPLHW_TC_005, TC_TPLHW_ER_005, TC_TPLHW_API_002, TC_TPLHW_API_003 | Đủ |
| BR-09 | Lọc "Loại trái phiếu": chọn nhiều; mặc định "Tất cả"; Niêm yết / Riêng… | 10 | TC_TPLHW_UI_001, TC_TPLHW_LT_001, TC_TPLHW_LT_002, TC_TPLHW_LT_003, TC_TPLHW_LT_004, TC_TPLHW_LT_005, TC_TPLHW_LT_006, TC_TPLHW_LT_007, TC_TPLHW_API_002, TC_TPLHW_API_006 | Đủ |
| BR-10 | Lọc → gọi lại S7 / C2 theo điều kiện; kết hợp các bộ lọc; Lưu / Đặt lạ… | 9 | TC_TPLHW_TK_001, TC_TPLHW_TC_002, TC_TPLHW_LT_002, TC_TPLHW_LC_001, TC_TPLHW_LC_002, TC_TPLHW_LC_003, TC_TPLHW_LC_004, TC_TPLHW_LC_005, TC_TPLHW_LC_006 | Đủ |
| BR-11 | Phân trang theo page, size (S7 / C2) | 7 | TC_TPLHW_LC_005, TC_TPLHW_PT_001, TC_TPLHW_PT_002, TC_TPLHW_PT_003, TC_TPLHW_PT_004, TC_TPLHW_PT_005, TC_TPLHW_API_004 | Đủ |
| BR-12 | Nút "Chi tiết" → Chi tiết TP linh hoạt (HOLDING.WEB.04) của đúng mã | 4 | TC_TPLHW_CTI_001, TC_TPLHW_CTI_002, TC_TPLHW_CTI_003, TC_TPLHW_CTI_004 | Đủ |
| BR-13 | Danh sách rỗng → empty state SBOND.HOLDING.I02: "Quý khách chưa có trá… | 5 | TC_TPLHW_TK_008, TC_TPLHW_TC_005, TC_TPLHW_LC_002, TC_TPLHW_LC_003, TC_TPLHW_ER_001 | Đủ |
| BR-14 | Lỗi BND-901 / BND-999 / AUTH_001 → popup lỗi chuẩn SHSmart: "Hệ thống … | 5 | TC_TPLHW_ER_002, TC_TPLHW_ER_003, TC_TPLHW_ER_004, TC_TPLHW_ER_005, TC_TPLHW_ER_006 | Đủ |
| BR-15 | Chỉ hiển thị TP của KH đang đăng nhập; màn chỉ xem, không đổi dữ liệu | 7 | TC_TPLHW_TK_015, TC_TPLHW_ER_004, TC_TPLHW_BM_001, TC_TPLHW_BM_002, TC_TPLHW_BM_003, TC_TPLHW_API_007, TC_TPLHW_API_008 | Đủ |
| BR-16 | Ghi nhận sự kiện FE: SBOND.HOLDING.FLEX.LIST (mở/tải lại), .FILTER (lư… | 3 | TC_TPLHW_LC_004, TC_TPLHW_CTI_001, TC_TPLHW_EVT_001 | Đủ |
| BR-17 | HĐ kỳ hạn hết hiệu lực tại COT ngày E (thiếu hồ sơ / thiếu số dư) → BE… | 3 | TC_TPLHW_NT_002, TC_TPLHW_NT_003, TC_TPLHW_NT_004 | Đủ |
| BR-18 | API: S7 GET /api/v1/bond/app/flex-bond-holding (mở màn, lọc, đổi trang… | 11 | TC_TPLHW_DS_001, TC_TPLHW_TC_001, TC_TPLHW_PT_002, TC_TPLHW_API_001, TC_TPLHW_API_002, TC_TPLHW_API_003, TC_TPLHW_API_004, TC_TPLHW_API_005, TC_TPLHW_API_006, TC_TPLHW_API_007, TC_TPLHW_API_008 | Đủ |

### Checklist
| Tiêu chí | Kết quả | Ghi chú |
|---|---|---|
| Mọi BR đều có TC | Đạt | 18/18 BR |
| Field có đủ rỗng / hợp lệ / không hợp lệ / biên | Đạt | Ô tìm kiếm: TK_006, TK_001–005, TK_007–009, TK_013–015, TK_010–012; dropdown: không chọn / 1 / nhiều / không có kết quả |
| Có luồng chính, luồng phụ, luồng lỗi | Đạt | DS_001, DS_002, CTI_001 · lọc, trang, noti, Outright · ER_001–006 |
| Decision table: mỗi rule có TC | Đạt | R1–R7 |
| State transition có cả ô không hợp lệ | Đạt | Chip Loại TP: LT_002–007 (LT_007 = ô ✖); số dư mã: DS_007, DS_008, NT_002–004 |
| Có case phân quyền / bảo mật | Đạt | BM_001–002, TK_013–015, API_007–008 |
| 1 TC = 1 mục tiêu, ID không trùng | Đạt | 81 ID duy nhất |
| Test data cụ thể | Đạt | TK1–TK5, Mã A–E với số TP theo từng sub, mệnh giá, giá giao dịch |
| Expected đo được | Đạt | Giá trị từng cột, tham số request, HTTP status, câu chữ empty state / popup |
| Negative kiểm cả báo lỗi và không thực hiện | Đạt | VD DS_004 "không phải 15", ER_002 "không hiện dữ liệu cũ", API_004 "không trả toàn bộ dữ liệu" |
| Message đúng nguyên văn SRS hoặc có [GIẢ ĐỊNH] | Đạt | I02, BND-901 / BND-999 / AUTH_001 theo mục 6; ánh xạ mã → câu ghi [GIẢ ĐỊNH] Q-12 |
| Thứ tự UI → Positive → … → Security | Đạt | UI trước; trong mỗi nhóm chức năng: Positive → Boundary → Negative; Phân quyền, Tracking, API cuối |

### Sửa / bổ sung ở B5
- Bổ sung 6 TC (đánh dấu "Bổ sung B5"): UI_006, TK_007, LC_006, CTI_003, NT_004, API_006.
  - NT_004: thiếu ô "chuyển Outright vào mã đã có" của state transition 2 (rủi ro tách 2 dòng).
  - LC_006: đổi bộ lọc nhanh – kết quả của request cũ về sau.
  - TK_007: lớp "chỉ khoảng trắng" của ô tìm kiếm.
  - CTI_003: quay lại danh sách sau khi đóng Chi tiết.
  - API_006: bondType không hợp lệ ở tầng API.
  - UI_006: tương thích trình duyệt / độ phân giải.
- Tách DS_004 / DS_005: "phần ở sub88 không được cộng" và "mã chỉ ở sub88 không hiện" là 2 mục tiêu khác nhau.
- Sửa CT_002: thêm số "sai" nếu tính theo giá giao dịch (123.125.000) để tester nhận ra ngay.

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 81 (Web 73 · API 8) | — |
| High / Medium / Low | 20 (25%) / 49 (60%) / 12 (15%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 35 : 46 (43% : 57%) | ≈ 30 : 70 |
| Theo Type | Positive 35 · Negative 25 · Boundary 7 · UI 6 · Security 5 · Permission 2 · Integration 1 | — |
| BR được phủ | 18/18 (100%) | 100% |
| Q&A mở | 14 (Blocker 0 · Major 7 · Minor 7) | Blocker = 0 |
| TC có [GIẢ ĐỊNH] | 34 | — |
