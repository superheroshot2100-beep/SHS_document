# SBOND.HISTORY.WEB.01 – Lịch sử giao dịch (Web) – Test design B1→B5

Nguồn: SRS SBOND.HISTORY.WEB.01 – Lịch sử giao dịch (export Confluence 15/11/2026) + ảnh luồng và 4 ảnh Figma Web trong SRS  
Người viết: Nguyễn Sỹ Hà · Ngày: 26/11/2026 · Lệnh: /FULL  
Phiên bản: v1 · Kênh: SHSmart Web  
Ký hiệu: [GĐ] = [GIẢ ĐỊNH], tạm giả định, chờ trả lời câu hỏi Q-xx; PC-xx = điều kiện có sẵn dùng chung; SOP-xx = thao tác dùng chung; D = ngày nghiệp vụ hiện tại theo BE (bộ này lấy D = 26/11/2026); "Bổ sung B5" = TC thêm ở bước tự review.

## B1. Phân tích yêu cầu
### 1. Tổng quan
- Mục đích: KH xem lại các giao dịch tiền gắn với trái phiếu S-Bond trên tiểu khoản sub88 (Mua TP, Bán TP, Nhận trái tức, Nộp tiền, Rút tiền), lọc theo Loại giao dịch và Thời gian giao dịch.
- Người dùng: KH trên SHSmart Web. Hệ thống liên quan: BE S-Bond (API O5), SHBond Portal (lệnh mua / thanh lý), Flex (trái tức, nộp / rút tiền).
- Vào màn từ: "Sản phẩm đầu tư" > Trái phiếu > sub tab "Lịch sử" (cạnh Danh sách chào bán / Danh sách nắm giữ / Xác nhận).
- Màn chỉ xem, không đổi dữ liệu; dòng giao dịch không có thao tác.

### 2. Feature
- Mở tab & tải mặc định · Bảng 3 cột (Thời gian / Nội dung / Biến động) · Phạm vi & sắp xếp giao dịch · Lọc Loại giao dịch (chọn nhiều) · Lọc Thời gian giao dịch (popup lịch 2 tháng, V01, V02) · Kết hợp bộ lọc · Phân trang · Bảng trống & lỗi · Edge case hoàn tiền / Rejected · Phân quyền · Tracking · API O5

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Mở sub tab "Lịch sử" (tab tô cam) → Web gọi O5 với bộ lọc mặc định: Loại GD = Tất cả (nhãn "Loại giao dịch"), Thời gian = 1 tháng gần nhất đến ngày hiện tại (tạm thời), page = 1 | Luồng bước 1; 2.1 #1 | Logic |
| BR-02 | Bảng 3 cột Thời gian / Nội dung / Biến động; Thời gian định dạng HH:mm dd/MM/yyyy (transTime) | Luồng bước 3; 2.1 #2–4 | UI |
| BR-03 | Phạm vi: giao dịch tiền gắn TP S-Bond của KH – Mua TP, Bán TP (theo lệnh đặt mua / thanh lý trên Portal), Nhận trái tức, Nộp tiền, Rút tiền (theo khai báo trên Flex); chỉ tiểu khoản sub88 | BIZ-32 | Logic |
| BR-04 | Thời gian giao dịch = thời gian make/check của lệnh trên Portal; sắp xếp mới → cũ | BIZ-32; 2.1 #2; Luồng bước 3 | Logic |
| BR-05 | Biến động (amount): dương = tiền vào, "+#.###" màu xanh; âm = tiền ra, "-#.###" màu đỏ | BIZ-32; 2.1 #3 | Logic |
| BR-06 | Nội dung (message) theo mẫu: "Lãi trái phiếu {Mã TP} chốt ngày dd/MM/yyyy"; "Trả tiền mua mã {Mã TP} ngày dd/MM/yyyy"; "Chuyển tiền tới {Số TK} (Online)" | 2.1 #4 | UI |
| BR-07 | Dòng giao dịch chỉ hiển thị, không có thao tác | 2.1 #8; BIZ-32 | UI |
| BR-08 | Dropdown "Loại giao dịch": checkbox chọn nhiều, giá trị Tất cả / Nhận trái tức / Mua trái phiếu / Bán trái phiếu / Nộp tiền / Rút tiền; map INTEREST / BUY / SELL / DEPOSIT / WITHDRAW; nhãn: chưa chọn → "Loại giao dịch", 1 loại → tên loại, > 1 → "Đã chọn n loại giao dịch"; đổi lựa chọn → gọi O5 | 2.2 #1; Luồng bước 5; BIZ-33 | Validation |
| BR-09 | Tick "Tất cả" → chọn tất cả các loại | 2.2 #1 | Logic |
| BR-10 | Ô "Thời gian giao dịch" dd/MM/yyyy → dd/MM/yyyy + icon lịch; bấm → popup lịch 2 tháng liền kề ("Tháng M, yyyy", tuần bắt đầu T2, mũi tên tháng trước / sau); bấm ngày 1 = Từ ngày, ngày 2 = Đến ngày, khoảng giữa tô màu | 2.2 #2; 2.3 #1; Luồng bước 6 | UI |
| BR-11 | Popup lịch: "Đóng" → bỏ thay đổi; "Chọn" → áp dụng khoảng, gọi O5 với page = 1 | 2.3 #2; Luồng bước 6 | Logic |
| BR-12 | V01: Từ ngày ≤ Đến ngày; không chọn ngày sau ngày hiện tại (BE kiểm); lịch chặn chọn ngày không hợp lệ | Mục 5 V01 | Validation |
| BR-13 | V02: khoảng ngày tối đa 6 tháng (tạm thời); vi phạm → inline E01 "Khoảng thời gian tra cứu tối đa 6 tháng (tạm thời)" | Mục 5 V02; Mục 6 #2 | Validation |
| BR-14 | Kết hợp bộ lọc: O5 nhận đồng thời transType và fromDate / toDate; lọc không có kết quả → bảng trống (A2) | Mục 2.2 mapping; Luồng 1.1 A2 | Logic |
| BR-15 | Phân trang "Trước / số trang / Sau": gọi O5 theo page / size; hiển thị khi > 1 trang (data.totalPage) | Luồng bước 4; 2.1 #10 | Logic |
| BR-16 | Không có giao dịch (totalCount = 0) → bảng trống "Không có giao dịch" (SBOND.HISTORY.I01) | Luồng bước 2; 2.1 #9; Mục 6 #1 | UI |
| BR-17 | Lỗi hệ thống / hết phiên (BND-901 / BND-999 / AUTH_001) → popup lỗi chuẩn SHSmart: "Hệ thống đang bận" / "Có lỗi xảy ra. Vui lòng thử lại sau" / "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"; CTA Đóng / Đăng nhập | Luồng bước 7; Mục 6 #3 | Lỗi |
| BR-18 | V03: phiên hợp lệ, dữ liệu chỉ của KH đăng nhập (AUTH_001 / BND-403); màn không đổi dữ liệu | Mục 5 V03; Post-Condition | Phân quyền |
| BR-19 | Ngày nghiệp vụ, giờ lấy theo Backend (BIZ-11) | BIZ-11 | Logic |
| BR-20 | Sự kiện FE: SBOND.HISTORY.LIST (mở tab / đổi trang), SBOND.HISTORY.FILTER.APPLY (đổi Loại GD / chọn khoảng ngày) | Mục 7 | Tích hợp |
| BR-21 | Edge: hủy GD sau xác nhận thanh toán → ghi nhận và hiển thị giao dịch hoàn tiền; lệnh không khớp (Rejected) → không phát sinh, không hiển thị giao dịch mua (Dự thảo BA – Q28) | Mục 8 | Logic |
| BR-22 | API O5 GET /api/v1/bond/app/transactions: dùng khi mở tab, đổi Loại GD / Thời gian GD, đổi trang; request transType, fromDate / toDate, page, size; response data.content[].transTime / message / amount, data.totalCount, data.totalPage | Mục 3; Mục 2 mapping | Tích hợp |

### 4. Field list
| Vùng | Trường | Kiểu | Bắt buộc | Ràng buộc / Mapping | Mặc định | Khi lỗi |
|---|---|---|---|---|---|---|
| Bộ lọc | Loại giao dịch | Dropdown checkbox chọn nhiều | Không | transType: INTEREST / BUY / SELL / DEPOSIT / WITHDRAW | Tất cả, nhãn "Loại giao dịch" | — |
| Bộ lọc | Thời gian giao dịch | Ô ngày từ → đến + popup lịch | Không | fromDate / toDate dd/MM/yyyy; Từ ≤ Đến; ≤ ngày hiện tại; ≤ 6 tháng | 1 tháng gần nhất → ngày hiện tại (26/10/2026 → 26/11/2026) [GĐ Q-04] | Lịch chặn (V01); E01 inline (V02) |
| Bảng | Thời gian | DateTime | Có | transTime, HH:mm dd/MM/yyyy | — | — |
| Bảng | Nội dung | Text | Có | message | — | — |
| Bảng | Biến động | Currency | Có | amount, +#.### xanh / -#.### đỏ | — | — |
| Bảng | Phân trang | Pagination | Có điều kiện | page, size; totalPage | Trang 1 | — |
| Bảng | Bảng trống | Empty state | Có điều kiện | totalCount = 0 | — | I01 "Không có giao dịch" |

### 5. Q&A
Không có câu mức Blocker → /FULL chạy tiếp. Tổng 19 câu (Major 11 · Minor 8).

| Q ID | Câu hỏi | Lý do | Mức | Giả định đề xuất | TC liên quan |
|---|---|---|---|---|---|
| Q-01 | BIZ-33 ghi "Đặt lại về mặc định; Lưu áp dụng", nhưng mục 2 và Figma Web chỉ có dropdown checkbox và popup lịch "Đóng / Chọn". Dropdown Loại GD áp dụng ngay khi tick hay cần nút xác nhận? Có nút "Đặt lại" không? | BIZ-33 khác UI | Major | Tick là gọi O5 ngay (luồng bước 5); không có "Lưu" / "Đặt lại" | LGD_001–014 |
| Q-02 | Mặc định "Tất cả": các checkbox có được tick sẵn không (Figma: không tick ô nào)? Bỏ tick hết thì gửi O5 thế nào? | Ảnh hưởng request và nhãn | Major | Mặc định không tick ô nào, nhãn "Loại giao dịch", O5 lấy tất cả loại; bỏ hết = Tất cả | UI_006, LGD_011 |
| Q-03 | Tick "Tất cả" thì nhãn hiện gì? Đang Tất cả bỏ 1 loại thì ô "Tất cả" có tự bỏ tick? Tick đủ 5 loại thì "Tất cả" có tự tick? | Hành vi checkbox chưa rõ | Minor | Nhãn "Đã chọn 5 loại giao dịch"; bỏ 1 loại → "Tất cả" bỏ tick; tick đủ 5 → "Tất cả" tự tick | LGD_007–010 |
| Q-04 | "1 tháng gần nhất" tính thế nào (cùng ngày tháng trước hay 30 ngày)? Có tính cả 2 đầu? Chữ "(tạm thời)" là gì? | Ảnh hưởng GD nằm ở biên | Major | Từ ngày = cùng ngày tháng trước, Đến ngày = D, gồm cả 2 đầu (Figma 13/09 → 13/10) | UI_001, DS_001, DS_002 |
| Q-05 | 6 tháng tính theo tháng lịch hay số ngày? Vượt 6 tháng: lịch chặn chọn hay cho chọn rồi báo E01? Chữ "(tạm thời)" có nằm trong câu hiển thị? E01 kiểm FE hay chỉ BE (BND-1xx)? | V02 ghi "inline", V01 ghi "lịch chặn" | Major | Tính theo tháng lịch: 26/05/2026 → 26/11/2026 hợp lệ; vượt → E01 inline trong popup, "Chọn" không áp dụng, không gọi O5; hiển thị đúng nguyên văn mục 6 | TG_009–011, API_004 |
| Q-06 | Đã chọn Từ ngày, bấm tiếp ngày trước đó thì sao (chặn / đảo / chọn lại Từ)? Chỉ chọn 1 ngày rồi bấm "Chọn" thì sao? | V01 chỉ ghi "lịch chặn" | Minor | Ngày trước Từ ngày bị chặn làm Đến ngày; chưa chọn Đến ngày → nút "Chọn" disable | TG_012, TG_013 |
| Q-07 | Ô ngày có cho gõ tay không? Có giới hạn ngày quá khứ xa nhất? | SRS không nêu | Minor | Không cho gõ tay; không giới hạn quá khứ ngoài V02 | TG_014 |
| Q-08 | Cột Thời gian: 2.1 ghi "thời điểm ghi nhận/khớp", BIZ-32 ghi "make/check của lệnh trên Portal". Lấy make hay check? GD từ Flex (trái tức, nộp / rút) lấy thời điểm nào? Múi giờ? | Quyết định GD rơi vào khoảng lọc nào | Major | Lệnh Portal: thời điểm check (duyệt); GD Flex: thời điểm Flex ghi nhận; giờ VN (UTC+7) | DS_011 |
| Q-09 | Figma Web dòng "Chuyển tiền tới 069C888888-88 (Online)" hiện "+34.731.400" màu xanh – trái BIZ-32 (tiền ra = âm, đỏ). Đúng là gì? | Figma mâu thuẫn BIZ | Major | Theo BIZ-32: Rút tiền hiện âm, đỏ | DS_009 |
| Q-10 | SRS chưa có mẫu Nội dung cho Bán trái phiếu và Nộp tiền. Câu hiển thị là gì? | Không viết được expected nguyên văn | Major | Hiển thị đúng message O5 trả về; có mã TP (Bán) / số TK (Nộp) và ngày | DS_007, DS_008 |
| Q-11 | O5 dùng path "/bond/app/transactions" cho cả Web? Tham số là transType hay transTypes[] (nhiều giá trị gửi thế nào)? page bắt đầu 0 hay 1? size mặc định bao nhiêu (Figma 5 dòng)? | Ảnh hưởng TC API và phân trang | Major | Dùng chung endpoint; transTypes lặp nhiều giá trị; page bắt đầu 1; size = 10 | DS_001, PT_001–005, API_001–009 |
| Q-12 | GD hoàn tiền (hủy sau xác nhận thanh toán) thuộc loại nào trong bộ lọc, nội dung gì, dấu gì? GD mua gốc còn hiển thị không? | Mục 8 chỉ ghi "hiển thị giao dịch hoàn tiền" | Major | Dòng mới dương (xanh), hiện khi lọc Tất cả; dòng mua gốc vẫn giữ | EC_001 |
| Q-13 | Lệnh Rejected "không hiển thị" đang là Dự thảo BA (Q28 của SRS) – đã chốt chưa? | Chưa chốt | Major | Không hiển thị dòng Mua | EC_002 |
| Q-14 | 2 GD cùng HH:mm thì sắp theo gì? | Thứ tự không xác định | Minor | Theo giây (mới trước) | DS_004 |
| Q-15 | Trên sub88 có GD tiền khác 5 loại (phí, thuế TNCN trên trái tức…) không, có hiển thị không? Trái tức hiển thị trước hay sau thuế? | Phạm vi BIZ-32 | Major | Chỉ 5 loại; số tiền = số Flex thực ghi nhận | DS_005, DS_013 |
| Q-16 | Lỗi tài liệu: bảng 2.1 STT nhảy 4 → 8; mục 2.2 dẫn "popup lịch (3.3)" (đúng là 2.3); Figma màn 1 tiêu đề "Biến động" / "Nội dung" lệch cột; lịch Figma 2 tháng cùng ghi "Tháng 3, 2024"; Figma Web chưa có empty state (Q24 của SRS) | Cần sửa tài liệu | Minor | Theo bảng 2.1: Thời gian · Nội dung · Biến động; 2 tháng liền kề khác nhau | UI_002, UI_007, ER_001 |
| Q-17 | Đổi sub tab rồi quay lại / F5: giữ bộ lọc hay về mặc định? | SRS không nêu | Minor | Về mặc định | LC_004, LC_005 |
| Q-18 | Đổi Loại GD có đưa về page = 1 không (SRS chỉ ghi cho Thời gian)? | SRS không nêu | Minor | Có, page = 1 | LGD_012 |
| Q-19 | Mã lỗi nào ra câu nào (BND-901 / BND-999 / AUTH_001)? Mất mạng hiển thị gì? | Mục 6 gộp 3 mã / 3 câu | Minor | BND-901 → "Hệ thống đang bận"; BND-999 → "Có lỗi xảy ra. Vui lòng thử lại sau"; AUTH_001 → "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"; mất mạng → popup lỗi chuẩn | ER_002–005 |

### 6. Rủi ro chính
| # | Rủi ro | Tác động | Mức |
|---|---|---|---|
| 1 | Sai dấu / màu biến động (Figma đang để Rút tiền là dương) | KH hiểu sai tiền vào / ra, khiếu nại | Cao |
| 2 | Lẫn GD của tiểu khoản khác sub88 hoặc của KH khác (IDOR, cache khi đổi tài khoản) | Sai dữ liệu, lộ thông tin tài chính | Cao |
| 3 | Sai mốc thời gian (make vs check, ngày client vs BE, múi giờ) → GD rơi ra / vào khoảng lọc sai, sắp xếp sai | KH tưởng thiếu / thừa giao dịch | Cao |
| 4 | Hoàn tiền / lệnh Rejected hiển thị sai (thiếu dòng hoàn, hiện dòng mua của lệnh không khớp) | KH tưởng mất tiền | Cao |
| 5 | Bộ lọc chọn nhiều + "Tất cả" + giới hạn 6 tháng xử lý sai, request gửi sai | Kết quả lọc sai | Trung bình |

## B2. Chiến lược test
- **In-scope:** Mở tab & bộ lọc mặc định, bảng 3 cột (định dạng, dấu, màu, nội dung 5 loại GD), phạm vi sub88, sắp xếp, lọc Loại GD, lọc Thời gian GD (popup lịch, V01, V02), kết hợp bộ lọc, phân trang, bảng trống, popup lỗi, edge hoàn tiền / Rejected, phân quyền, tracking, API O5.
- **Out-of-scope:** Bản App (HISTORY.APP.01); các sub tab khác; Noti / Email Bond_01, Bond_07; nghiệp vụ tạo lệnh trên Portal / Flex (chỉ dùng để tạo dữ liệu và đối chiếu); hiệu năng.
- **Loại test:** Chức năng, giao diện, API, tích hợp (Portal / Flex → BE → Web), phân quyền, bảo mật cơ bản, tương thích trình duyệt, đoán lỗi (mất mạng, đổi bộ lọc nhanh, đổi giờ máy).

| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC (Web / API) |
|---|---|---|---|---|
| BR-01 | UC | EG | High | 6 / 1 |
| BR-02 | EG | PW | Low | 4 / 1 |
| BR-03 | EP | EG | High | 9 / 0 |
| BR-04 | EP | EG | High | 4 / 0 |
| BR-05 | EP | — | High | 9 / 0 |
| BR-06 | EP | — | Medium | 5 / 0 |
| BR-07 | EG | — | Low | 1 / 0 |
| BR-08 | EP | EG | Medium | 13 / 2 |
| BR-09 | DT | — | Medium | 4 / 0 |
| BR-10 | UC | EG | Medium | 7 / 0 |
| BR-11 | UC | EG | Medium | 6 / 0 |
| BR-12 | BVA | EP | Medium | 5 / 2 |
| BR-13 | BVA | — | Medium | 3 / 1 |
| BR-14 | DT | — | Medium | 5 / 0 |
| BR-15 | BVA | EG | Medium | 7 / 1 |
| BR-16 | EP | — | Medium | 3 / 0 |
| BR-17 | EG | — | Medium | 4 / 0 |
| BR-18 | EG | — | High | 6 / 1 |
| BR-19 | EG | — | Medium | 1 / 1 |
| BR-20 | UC | — | Low | 2 / 0 |
| BR-21 | ST | — | High | 3 / 0 |
| BR-22 | EP | BVA | Medium | 2 / 9 |

- **Môi trường:** UAT SHSmart Web; Chrome bản mới nhất (chính) + Edge, Firefox, Safari; DevTools; Proxyman/Charles; Postman; tài khoản BO Portal và quyền khai báo trên Flex UAT để tạo giao dịch.
- **Exit criteria:** Chạy hết TC High; không còn bug Critical/Major mở; Q&A Major đã có trả lời (hoặc PO chấp nhận giả định); mọi BR có TC Pass.

### Dữ liệu cần có trên môi trường (D = 26/11/2026)
| Mã | Điều kiện dữ liệu | Dùng cho |
|---|---|---|
| TK1 | Tài khoản 069C888888, đăng nhập được SHSmart Web UAT, có tiểu khoản sub88 với các GD dưới đây | Hầu hết TC |
| GD1 | 24/11/2026 09:15 · Nhận trái tức · +34.731.400 · "Lãi trái phiếu TPQ12312 chốt ngày 20/11/2026" | Trong 1 tháng |
| GD2 | 21/11/2026 10:20 · Mua trái phiếu · -134.731.400 · "Trả tiền mua mã TPQ12312 ngày 21/11/2026" (lệnh trên Portal, check lúc 10:20) | Trong 1 tháng |
| GD3 | 19/11/2026 14:00 · Bán trái phiếu (thanh lý BHB291193) · +105.000.000 · nội dung theo O5 [GĐ Q-10] | Trong 1 tháng |
| GD4 | 16/11/2026 08:30 · Nộp tiền · +50.000.000 · nội dung theo O5 [GĐ Q-10] | Trong 1 tháng |
| GD5 | 14/11/2026 16:45 · Rút tiền · -1.543.489 · "Chuyển tiền tới 069C888888-88 (Online)" | Trong 1 tháng |
| GD6 | 17/10/2026 11:00 · Mua trái phiếu · -50.000.000 · "Trả tiền mua mã ABC12601 ngày 17/10/2026" | Ngoài 1 tháng, trong 6 tháng |
| GD7 | 29/06/2026 09:00 · Nhận trái tức · +2.000.000 · "Lãi trái phiếu BHB291193 chốt ngày 25/06/2026" | Khoảng cũ, biên 6 tháng |
| GD8 | 20/05/2026 10:00 · Nộp tiền · +5.000.000 | Ngoài 6 tháng |
| GD9 | 23/11/2026 13:00 · Nộp tiền +10.000.000 vào tiểu khoản thường 069C888888 (không phải sub88) | Phạm vi sub88 |
| → Kỳ vọng mặc định | 26/10/2026 → 26/11/2026, Tất cả: 5 dòng theo thứ tự GD1, GD2, GD3, GD4, GD5 | DS_002 |
| TK2 | Tài khoản không có GD nào trên sub88 | Bảng trống |
| TK3 | 25 GD trên sub88 trong 26/10/2026 → 26/11/2026, giờ khác nhau; trong đó 2 GD cùng 22/11/2026 10:20 (giây 15 và 45) | Phân trang, sắp xếp |
| TK4 | Tài khoản khác, có GD "Trả tiền mua mã DEF12602 ngày 20/11/2026" (TK1 không có mã này) | Phân quyền |
| Lưu ý | Mã, số tiền, ngày là VD. Tester đổi D theo ngày chạy thật và dời các ngày tương ứng, giữ đúng cấu trúc (trong / ngoài 1 tháng, 6 tháng, tiểu khoản khác) | — |

### Precondition / thao tác dùng chung
| Mã | Nội dung |
|---|---|
| PC-01 | TK1 đã đăng nhập SHSmart Web UAT, vào "Sản phẩm đầu tư" > Trái phiếu > sub tab "Lịch sử"; bảng đã tải xong, bộ lọc mặc định; ngày BE = 26/11/2026 |
| PC-02 | PC-01 + mở DevTools tab Network (lọc "transactions") |
| PC-03 | Postman, header Authorization = Bearer <token TK1> lấy từ request O5 trên Web |
| SOP-01 | Chọn khoảng ngày: bấm ô "Thời gian giao dịch" → dùng mũi tên tới tháng cần → bấm ngày Từ → bấm ngày Đến → bấm "Chọn" |
| SOP-02 | Giả lập lỗi: dùng Proxyman sửa response O5 thành mã lỗi cần test (BND-901 / BND-999 / AUTH_001) |
| SOP-03 | Đối chiếu nguồn: Portal (lệnh mua / thanh lý: thời gian make, check, số tiền); Flex (trái tức, nộp / rút: thời gian, số tiền, tiểu khoản) |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên | Condition |
|---|---|---|---|---|
| Loại giao dịch | Không chọn (tất cả); 1 loại (5 lớp); 2 loại; Tất cả | Mã không hợp lệ qua API ("ABC") | 0 / 1 / 2 / 5 loại | TCO-BR08-01 → 10 |
| Từ ngày / Đến ngày | Từ < Đến; Từ = Đến; Đến = D | Từ > Đến; ngày > D; chỉ 1 ngày; sai format (API) | D (chọn được) / D+1 (chặn) | TCO-BR12-01 → 07 |
| Độ dài khoảng | 1 tháng; 6 tháng – 1 ngày | > 6 tháng | 27/05 (hợp lệ) / 26/05 (hợp lệ, đúng 6 tháng) / 25/05 (E01) | TCO-BR13-01 → 03 |
| Biến động | Dương; âm | — | Số rất lớn 999.999.999.999 | TCO-BR05-01 → 03 |
| Phân trang (10/trang [GĐ Q-11]) | page trong phạm vi | page > totalPage (API) | 5 GD (1 trang, không phân trang); 25 GD (3 trang, trang cuối 5 dòng) | TCO-BR15-01 → 05 |
| Tiểu khoản | sub88 | Tiểu khoản thường | — | TCO-BR03-06 |

### Decision table – kết hợp bộ lọc (dữ liệu TK1)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Loại GD | Tất cả | Mua trái phiếu | Mua trái phiếu | Nhận trái tức | Nhận trái tức |
| Thời gian | Mặc định | Mặc định | 17/10 → 26/11 | 01/06 → 30/06 | 25/11 → 26/11 |
| **Kết quả** | GD1–GD5 | GD2 | GD2, GD6 | GD7 | I01 "Không có giao dịch" |
| TC | DS_002 | LGD_002 | LC_001 | TG_005 (Tất cả) / LC_003 | LC_002 |

### Decision table – checkbox "Tất cả" (BR-09)
| Thao tác | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| Trạng thái trước | Chưa chọn | Tất cả đang tick | 4 loại đã tick | Tất cả đang tick |
| Hành động | Tick "Tất cả" | Bỏ 1 loại | Tick loại thứ 5 | Bỏ tick "Tất cả" |
| **Kết quả [GĐ Q-03]** | Tick đủ 5 loại | "Tất cả" bỏ tick, 4 loại | "Tất cả" tự tick | Về chưa chọn, nhãn "Loại giao dịch" |
| TC | LGD_007 | LGD_010 | LGD_009 | LGD_008 |

### State transition – giao dịch mua trên màn Lịch sử
| Trạng thái lệnh \ Sự kiện | Xác nhận thanh toán | Hủy sau xác nhận thanh toán | Không khớp (Rejected) |
|---|---|---|---|
| Chờ thanh toán | Có dòng "Trả tiền mua mã …" âm (DS_012) | — | Không có dòng nào (EC_002) |
| Đã thanh toán | — | Thêm dòng hoàn tiền dương, dòng mua giữ nguyên (EC_001) | ✖ |

### Use case
- Luồng chính: Mở tab → xem bảng mặc định → lọc Loại GD → chọn khoảng ngày → đổi trang (DS_001–002, LGD_001, TG_001, PT_002).
- Luồng phụ: chọn nhiều loại, Tất cả, bỏ chọn; Đóng popup; điều hướng tháng; đổi bộ lọc ở trang 2.
- Luồng lỗi: không có GD (I01); lọc không ra (A2); vượt 6 tháng (E01); ngày tương lai; BND-901 / BND-999 / AUTH_001; mất mạng; chưa đăng nhập; tài khoản khác.

### Error guessing áp dụng
- Bộ lọc: tick / bỏ tick nhanh, đổi khi đang ở trang 2, Esc / click ra ngoài popup, gõ tay ô ngày · Ngày giờ: đổi giờ máy client, biên ngày D / D+1, 2 GD cùng phút · Số liệu: số rất lớn, nội dung dài, dấu âm dương · Hệ thống: mạng chậm, Offline, hết phiên, đổi tài khoản, gọi API thẳng, tham số sai · Trình duyệt.

## B4. Test case
### 4.1 Web (78 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_LSGDW_UI_001 | BR-01, BR-08, BR-10 | Lịch sử GD Web – Giao diện | Kiểm tra giao diện mặc định khi mở sub tab Lịch sử | PC-01 | 1. Quan sát màn hình | TK1 | - Sub tab "Lịch sử" tô cam; 3 tab còn lại màu thường<br>- Dropdown hiện nhãn "Loại giao dịch"<br>- Ô thời gian hiện "26/10/2026 → 26/11/2026" + icon lịch<br>- Bảng có 5 dòng dữ liệu | Low | UI | EG | [GĐ] cách tính 1 tháng – Q-04 |
| TC_LSGDW_UI_002 | BR-02 | Lịch sử GD Web – Giao diện | Kiểm tra tiêu đề và thứ tự cột của bảng khi có dữ liệu | PC-01 | 1. Quan sát hàng tiêu đề bảng | TK1 | - 3 cột theo thứ tự: Thời gian · Nội dung · Biến động<br>- Tiêu đề "Biến động" nằm trên cột số tiền (căn phải), "Nội dung" nằm trên cột nội dung | Low | UI | EG | Figma màn 1 lệch tiêu đề – Q-16 |
| TC_LSGDW_UI_003 | BR-02 | Lịch sử GD Web – Giao diện | Kiểm tra định dạng cột Thời gian khi có giao dịch giờ chiều | PC-01 | 1. Quan sát cột Thời gian dòng GD1 và GD5 | GD1, GD5 | - GD1 hiện "09:15 24/11/2026"<br>- GD5 hiện "16:45 14/11/2026" (định dạng 24 giờ, không AM/PM, không hiện giây) | Low | UI | EP |  |
| TC_LSGDW_UI_004 | BR-05 | Lịch sử GD Web – Giao diện | Kiểm tra định dạng và màu Biến động khi giao dịch tiền vào | PC-01 | 1. Quan sát cột Biến động dòng GD1 | GD1 = +34.731.400 | - Hiện "+34.731.400" màu xanh<br>- Phân cách hàng nghìn bằng dấu chấm, không số thập phân, căn phải | Medium | UI | EP |  |
| TC_LSGDW_UI_005 | BR-05 | Lịch sử GD Web – Giao diện | Kiểm tra định dạng và màu Biến động khi giao dịch tiền ra | PC-01 | 1. Quan sát cột Biến động dòng GD2 | GD2 = -134.731.400 | - Hiện "-134.731.400" màu đỏ<br>- Phân cách hàng nghìn bằng dấu chấm, không số thập phân | Medium | UI | EP |  |
| TC_LSGDW_UI_006 | BR-08 | Lịch sử GD Web – Giao diện | Kiểm tra danh sách giá trị khi mở dropdown Loại giao dịch | PC-01 | 1. Bấm dropdown "Loại giao dịch" | — | - Hiện 6 checkbox theo thứ tự: Tất cả · Nhận trái tức · Mua trái phiếu · Bán trái phiếu · Nộp tiền · Rút tiền<br>- Không ô nào được tick<br>- Bảng phía sau giữ nguyên 5 dòng | Low | UI | EG | [GĐ] trạng thái tick mặc định – Q-02 |
| TC_LSGDW_UI_007 | BR-10 | Lịch sử GD Web – Giao diện | Kiểm tra giao diện popup lịch khi bấm ô Thời gian giao dịch | PC-01 | 1. Bấm ô "Thời gian giao dịch" | Khoảng hiện tại 26/10/2026 → 26/11/2026 | - Popup hiện 2 tháng liền kề "Tháng 10, 2026" và "Tháng 11, 2026"<br>- Hàng thứ T2 · T3 · T4 · T5 · T6 · T7 · CN (bắt đầu T2)<br>- Có mũi tên tháng trước (trái) và tháng sau (phải)<br>- 26/10 và 26/11 được đánh dấu, khoảng giữa tô màu<br>- Có nút "Đóng" và "Chọn" | Low | UI | EG | Figma ghi 2 tháng cùng "Tháng 3, 2024" – Q-16 |
| TC_LSGDW_UI_008 | BR-07 | Lịch sử GD Web – Giao diện | Kiểm tra dòng giao dịch khi bấm vào dòng | PC-01 | 1. Rê chuột lên dòng GD2<br>2. Bấm vào dòng GD2 | GD2 | - Không mở popup / màn chi tiết, không chuyển trang<br>- Không có nút / link trên dòng; con trỏ không đổi thành bàn tay | Low | UI | EG |  |
| TC_LSGDW_UI_009 | BR-02, BR-05 | Lịch sử GD Web – Giao diện | Kiểm tra hiển thị màn trên các trình duyệt chính | TK1 đã đăng nhập trên từng trình duyệt | 1. Mở sub tab "Lịch sử" trên Chrome<br>2. Lặp lại trên Edge, Firefox, Safari<br>3. Mở dropdown và popup lịch trên từng trình duyệt | Chrome, Edge, Firefox, Safari bản mới nhất; 1366×768 và 1920×1080 | - Cả 4 trình duyệt: 3 cột thẳng hàng, số tiền đúng màu, dropdown và popup lịch không bị che / tràn<br>- Không lỗi trong Console | Low | UI | PW | Bổ sung B5 |
| TC_LSGDW_UI_010 | BR-02, BR-05 | Lịch sử GD Web – Giao diện | Kiểm tra layout khi nội dung dài và số tiền rất lớn | PC-01; dùng Proxyman sửa response O5 | 1. Sửa message 1 dòng thành chuỗi 200 ký tự<br>2. Sửa amount dòng đó = 999999999999<br>3. Tải lại sub tab | message 200 ký tự; amount = 999999999999 | - Nội dung xuống dòng hoặc cắt "…" nhưng không đè cột Biến động<br>- Hiện "+999.999.999.999" đủ chữ số, không ký hiệu khoa học (e+) | Low | UI | EG | Bổ sung B5 |
| TC_LSGDW_DS_001 | BR-01, BR-22 | Lịch sử GD Web – Danh sách | Kiểm tra request O5 khi mở sub tab Lịch sử | PC-02 | 1. Bấm sang sub tab "Xác nhận"<br>2. Bấm lại sub tab "Lịch sử"<br>3. Xem request O5 trong Network | TK1 | - Có 1 request GET /api/v1/bond/app/transactions<br>- Tham số: fromDate = 26/10/2026, toDate = 26/11/2026, page = 1, size = 10, transType = tất cả loại (hoặc không gửi)<br>- HTTP 200 | High | Positive | UC | [GĐ] size, cách gửi "Tất cả" – Q-02, Q-11 |
| TC_LSGDW_DS_002 | BR-01, BR-03, BR-04 | Lịch sử GD Web – Danh sách | Kiểm tra danh sách giao dịch khi mở tab với bộ lọc mặc định | PC-01 | 1. Quan sát bảng | TK1, GD1–GD9 | - Hiện đúng 5 dòng theo thứ tự: GD1 (24/11), GD2 (21/11), GD3 (19/11), GD4 (16/11), GD5 (14/11)<br>- Không có GD6 (17/10), GD7, GD8 (ngoài khoảng), GD9 (tiểu khoản thường) | High | Positive | UC |  |
| TC_LSGDW_DS_003 | BR-04 | Lịch sử GD Web – Danh sách | Kiểm tra sắp xếp mới → cũ khi danh sách nhiều ngày | TK3 đã đăng nhập, ở sub tab "Lịch sử", trang 1 | 1. Đọc cột Thời gian 10 dòng trang 1<br>2. Bấm trang "2", đọc tiếp | TK3 – 25 GD | - Trong trang và giữa các trang, Thời gian giảm dần (dòng sau không mới hơn dòng trước)<br>- Dòng cuối trang 1 mới hơn hoặc bằng dòng đầu trang 2 | High | Positive | EP |  |
| TC_LSGDW_DS_004 | BR-04 | Lịch sử GD Web – Danh sách | Kiểm tra thứ tự khi 2 giao dịch cùng phút | TK3 đã đăng nhập, ở sub tab "Lịch sử" | 1. Tìm 2 dòng "10:20 22/11/2026"<br>2. Đối chiếu giây trong response O5 (transTime) | 2 GD 22/11/2026 10:20:15 và 10:20:45 | - GD 10:20:45 nằm trên GD 10:20:15<br>- Thứ tự giữ nguyên khi tải lại tab 3 lần | Low | Positive | EG | Bổ sung B5; [GĐ] Q-14 |
| TC_LSGDW_DS_005 | BR-03, BR-05, BR-06 | Lịch sử GD Web – Danh sách | Kiểm tra hiển thị dòng Nhận trái tức | PC-01 | 1. Quan sát dòng GD1<br>2. Đối chiếu Flex theo SOP-03 | GD1 | - Thời gian "09:15 24/11/2026"<br>- Nội dung "Lãi trái phiếu TPQ12312 chốt ngày 20/11/2026"<br>- Biến động "+34.731.400" xanh, bằng số Flex ghi nhận vào sub88 | High | Positive | EP | [GĐ] số tiền sau thuế – Q-15 |
| TC_LSGDW_DS_006 | BR-03, BR-05, BR-06 | Lịch sử GD Web – Danh sách | Kiểm tra hiển thị dòng Mua trái phiếu | PC-01 | 1. Quan sát dòng GD2<br>2. Đối chiếu lệnh mua trên Portal theo SOP-03 | GD2 | - Nội dung "Trả tiền mua mã TPQ12312 ngày 21/11/2026"<br>- Biến động "-134.731.400" đỏ, bằng số tiền thanh toán của lệnh trên Portal | High | Positive | EP |  |
| TC_LSGDW_DS_007 | BR-03, BR-05, BR-06 | Lịch sử GD Web – Danh sách | Kiểm tra hiển thị dòng Bán trái phiếu | PC-01 | 1. Quan sát dòng GD3<br>2. Đối chiếu lệnh thanh lý trên Portal | GD3 | - Biến động "+105.000.000" xanh, bằng số tiền nhận về của lệnh thanh lý<br>- Nội dung có mã BHB291193 và ngày 19/11/2026, khớp message O5 | High | Positive | EP | [GĐ] mẫu nội dung – Q-10 |
| TC_LSGDW_DS_008 | BR-03, BR-05, BR-06 | Lịch sử GD Web – Danh sách | Kiểm tra hiển thị dòng Nộp tiền | PC-01 | 1. Quan sát dòng GD4<br>2. Đối chiếu Flex | GD4 | - Biến động "+50.000.000" xanh<br>- Nội dung khớp message O5, có ngày 16/11/2026 | High | Positive | EP | [GĐ] mẫu nội dung – Q-10 |
| TC_LSGDW_DS_009 | BR-03, BR-05, BR-06 | Lịch sử GD Web – Danh sách | Kiểm tra hiển thị dòng Rút tiền | PC-01 | 1. Quan sát dòng GD5 | GD5 | - Nội dung "Chuyển tiền tới 069C888888-88 (Online)"<br>- Biến động "-1.543.489" màu đỏ (không phải "+" xanh như Figma) | High | Positive | EP | [GĐ] theo BIZ-32 – Q-09 |
| TC_LSGDW_DS_010 | BR-03 | Lịch sử GD Web – Danh sách | Kiểm tra không hiển thị giao dịch của tiểu khoản khác sub88 | PC-01 | 1. Tìm dòng ngày 23/11/2026 trong bảng<br>2. Lọc Loại = "Nộp tiền" | GD9: Nộp 10.000.000 vào tiểu khoản thường, 23/11/2026 13:00 | - Không có dòng nào ngày 23/11/2026<br>- Lọc "Nộp tiền" chỉ ra GD4, không có +10.000.000 | High | Negative | EP |  |
| TC_LSGDW_DS_011 | BR-04 | Lịch sử GD Web – Danh sách | Kiểm tra Thời gian khi lệnh mua có giờ make khác giờ check | TK1 có lệnh mua mới trên Portal: make 09:00, check 10:30 ngày 26/11/2026 | 1. Mở sub tab "Lịch sử"<br>2. Quan sát cột Thời gian của dòng lệnh này | Lệnh mua TPQ12312, make 09:00, check 10:30 | - Thời gian hiện "10:30 26/11/2026" (giờ check)<br>- Dòng nằm trên GD1 | Medium | Positive | EP | [GĐ] lấy giờ check – Q-08 |
| TC_LSGDW_DS_012 | BR-03, BR-21 | Lịch sử GD Web – Danh sách | Kiểm tra giao dịch mua mới xuất hiện sau khi được xác nhận thanh toán | TK1 vừa đặt mua 10 TP ABC12601 trên Web; lệnh chưa thanh toán | 1. Mở sub tab "Lịch sử", ghi nhận số dòng<br>2. Trên Portal xác nhận thanh toán lệnh<br>3. Bấm sang sub tab khác rồi về "Lịch sử" | Lệnh mua ABC12601, 10.000.000 | - Bước 1: chưa có dòng của lệnh<br>- Bước 3: thêm 1 dòng đầu bảng "Trả tiền mua mã ABC12601 ngày 26/11/2026", "-10.000.000" đỏ | Medium | Integration | ST |  |
| TC_LSGDW_DS_013 | BR-03 | Lịch sử GD Web – Danh sách | Kiểm tra không hiển thị giao dịch tiền ngoài 5 loại trên sub88 | Trên Flex tạo 1 bút toán phí 22.000 trên sub88 của TK1 ngày 25/11/2026; PC-01 | 1. Tải lại sub tab "Lịch sử"<br>2. Tìm dòng "-22.000" | Bút toán phí 22.000 | - Không có dòng "-22.000"<br>- Danh sách vẫn 5 dòng GD1–GD5 | Medium | Negative | EP | [GĐ] Q-15 |
| TC_LSGDW_DS_014 | BR-19, BR-01 | Lịch sử GD Web – Danh sách | Kiểm tra khoảng mặc định khi giờ máy client khác ngày BE | Đổi ngày giờ máy tính sang 05/01/2027; TK1 đã đăng nhập | 1. Mở sub tab "Lịch sử"<br>2. Mở popup lịch | Ngày máy 05/01/2027; ngày BE 26/11/2026 | - Ô thời gian vẫn "26/10/2026 → 26/11/2026"<br>- Trong lịch, ngày sau 26/11/2026 bị chặn (không phải sau 05/01/2027) | Medium | Negative | EG |  |
| TC_LSGDW_LGD_001 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra lọc khi chọn 1 loại Nhận trái tức | PC-02 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick "Nhận trái tức" | — | - O5 gọi lại với transType = INTEREST, page = 1<br>- Bảng chỉ còn GD1<br>- Nhãn dropdown "Nhận trái tức" | Medium | Positive | EP | [GĐ] áp dụng ngay khi tick – Q-01 |
| TC_LSGDW_LGD_002 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra lọc khi chọn 1 loại Mua trái phiếu | PC-02 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick "Mua trái phiếu" | — | - O5 với transType = BUY<br>- Bảng chỉ còn GD2<br>- Nhãn "Mua trái phiếu" | Medium | Positive | EP |  |
| TC_LSGDW_LGD_003 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra lọc khi chọn 1 loại Bán trái phiếu | PC-02 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick "Bán trái phiếu" | — | - O5 với transType = SELL<br>- Bảng chỉ còn GD3<br>- Nhãn "Bán trái phiếu" | Medium | Positive | EP |  |
| TC_LSGDW_LGD_004 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra lọc khi chọn 1 loại Nộp tiền | PC-02 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick "Nộp tiền" | — | - O5 với transType = DEPOSIT<br>- Bảng chỉ còn GD4<br>- Nhãn "Nộp tiền" | Medium | Positive | EP |  |
| TC_LSGDW_LGD_005 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra lọc khi chọn 1 loại Rút tiền | PC-02 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick "Rút tiền" | — | - O5 với transType = WITHDRAW<br>- Bảng chỉ còn GD5<br>- Nhãn "Rút tiền" | Medium | Positive | EP |  |
| TC_LSGDW_LGD_006 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra lọc và nhãn khi chọn 2 loại | PC-02 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick "Nhận trái tức"<br>3. Tick "Mua trái phiếu" | — | - Request cuối có cả INTEREST và BUY<br>- Bảng còn 2 dòng GD1, GD2 (mới → cũ)<br>- Nhãn "Đã chọn 2 loại giao dịch" | Medium | Positive | EP |  |
| TC_LSGDW_LGD_007 | BR-09 | Lịch sử GD Web – Loại giao dịch | Kiểm tra khi tick Tất cả lúc chưa chọn loại nào | PC-02 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick "Tất cả" | — | - 5 loại còn lại được tick<br>- O5 gửi đủ 5 loại (hoặc không lọc loại)<br>- Bảng hiện 5 dòng GD1–GD5<br>- Nhãn "Đã chọn 5 loại giao dịch" | Medium | Positive | DT | [GĐ] nhãn – Q-03 |
| TC_LSGDW_LGD_008 | BR-09 | Lịch sử GD Web – Loại giao dịch | Kiểm tra khi bỏ tick Tất cả lúc đang chọn tất cả | PC-01; đã tick "Tất cả" | 1. Bỏ tick "Tất cả" | — | - Cả 6 ô bỏ tick<br>- Nhãn về "Loại giao dịch"<br>- Bảng vẫn 5 dòng GD1–GD5 | Low | Positive | DT | [GĐ] Q-02, Q-03 |
| TC_LSGDW_LGD_009 | BR-09 | Lịch sử GD Web – Loại giao dịch | Kiểm tra ô Tất cả khi tick lần lượt đủ 5 loại | PC-01 | 1. Bấm dropdown "Loại giao dịch"<br>2. Tick lần lượt Nhận trái tức, Mua trái phiếu, Bán trái phiếu, Nộp tiền, Rút tiền | — | - Sau khi tick loại thứ 5: ô "Tất cả" tự tick<br>- Bảng hiện 5 dòng GD1–GD5 | Low | Positive | DT | [GĐ] Q-03 |
| TC_LSGDW_LGD_010 | BR-09, BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra khi bỏ 1 loại lúc đang chọn Tất cả | PC-02; đã tick "Tất cả" | 1. Bỏ tick "Rút tiền" | — | - Ô "Tất cả" bỏ tick; 4 loại còn tick<br>- O5 gửi 4 loại, không có WITHDRAW<br>- Bảng 4 dòng GD1–GD4, không có GD5<br>- Nhãn "Đã chọn 4 loại giao dịch" | Medium | Positive | DT | [GĐ] Q-03 |
| TC_LSGDW_LGD_011 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra khi bỏ tick hết các loại đã chọn | PC-02; đã tick "Nhận trái tức" | 1. Bỏ tick "Nhận trái tức" | — | - Nhãn về "Loại giao dịch"<br>- O5 gọi lại không lọc loại<br>- Bảng về 5 dòng GD1–GD5 (không báo trống) | Medium | Boundary | BVA | [GĐ] bỏ hết = Tất cả – Q-02 |
| TC_LSGDW_LGD_012 | BR-08, BR-15 | Lịch sử GD Web – Loại giao dịch | Kiểm tra trang hiện tại khi đổi Loại giao dịch lúc đang ở trang 2 | TK3 đã đăng nhập, ở sub tab "Lịch sử", đang ở trang 2; DevTools Network | 1. Tick "Mua trái phiếu" | TK3 | - O5 gọi với page = 1<br>- Phân trang đánh dấu trang 1 (hoặc ẩn nếu chỉ 1 trang)<br>- Bảng chỉ có dòng Mua trái phiếu | Medium | Positive | EG | [GĐ] Q-18 |
| TC_LSGDW_LGD_013 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra kết quả khi tick / bỏ tick nhanh liên tiếp | PC-02; Network "Fast 3G" | 1. Tick "Nhận trái tức"<br>2. Ngay lập tức tick "Mua trái phiếu"<br>3. Ngay lập tức bỏ tick "Nhận trái tức"<br>4. Chờ tải xong | — | - Bảng cuối cùng chỉ có GD2 (khớp lựa chọn cuối "Mua trái phiếu")<br>- Response cũ về sau không ghi đè bảng<br>- Nhãn "Mua trái phiếu" | Low | Negative | EG | Bổ sung B5 |
| TC_LSGDW_LGD_014 | BR-08 | Lịch sử GD Web – Loại giao dịch | Kiểm tra dropdown khi bấm ra ngoài sau khi đã chọn | PC-01 | 1. Bấm dropdown, tick "Nộp tiền"<br>2. Bấm vào vùng trống ngoài dropdown<br>3. Mở lại dropdown | — | - Bước 2: dropdown đóng; bảng giữ kết quả GD4<br>- Bước 3: "Nộp tiền" vẫn được tick | Low | Positive | EG |  |
| TC_LSGDW_TG_001 | BR-10, BR-11 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra lọc khi chọn khoảng ngày hợp lệ và bấm Chọn | PC-02 | 1. Chọn khoảng theo SOP-01 | 17/10/2026 → 26/11/2026 | - Popup đóng; ô hiện "17/10/2026 → 26/11/2026"<br>- O5 gọi với fromDate = 17/10/2026, toDate = 26/11/2026, page = 1<br>- Bảng 6 dòng GD1–GD6, GD6 ở cuối | Medium | Positive | UC |  |
| TC_LSGDW_TG_002 | BR-10 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra tô màu khoảng ngày khi bấm ngày thứ nhất và thứ hai | PC-01 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm 10/11/2026<br>3. Bấm 20/11/2026 | 10/11/2026, 20/11/2026 | - Bước 2: 10/11 được đánh dấu làm Từ ngày<br>- Bước 3: 20/11 đánh dấu Đến ngày; 11/11 → 19/11 được tô màu<br>- Chưa gọi O5 khi chưa bấm "Chọn" | Low | UI | UC |  |
| TC_LSGDW_TG_003 | BR-11 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra khi bấm Đóng sau khi chọn khoảng mới | PC-02 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm 01/11/2026 rồi 10/11/2026<br>3. Bấm "Đóng" | 01/11/2026 → 10/11/2026 | - Popup đóng; ô vẫn "26/10/2026 → 26/11/2026"<br>- Không có request O5 mới<br>- Bảng giữ 5 dòng GD1–GD5<br>- Mở lại popup: khoảng tô là 26/10 → 26/11 | Medium | Negative | UC |  |
| TC_LSGDW_TG_004 | BR-10 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra điều hướng khi bấm mũi tên tháng trước / sau | PC-01 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm mũi tên trái 1 lần<br>3. Bấm mũi tên phải 1 lần | Đang hiện Tháng 10 và Tháng 11, 2026 | - Bước 2: hiện "Tháng 9, 2026" và "Tháng 10, 2026"<br>- Bước 3: về "Tháng 10, 2026" và "Tháng 11, 2026"<br>- Khoảng đã chọn vẫn giữ | Low | Positive | UC |  |
| TC_LSGDW_TG_005 | BR-10, BR-11 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra lọc khi chọn khoảng ngày cũ trong quá khứ | PC-01 | 1. Chọn khoảng theo SOP-01 | 01/06/2026 → 30/06/2026 | - Ô hiện "01/06/2026 → 30/06/2026"<br>- Bảng chỉ có GD7 "Lãi trái phiếu BHB291193 chốt ngày 25/06/2026", "+2.000.000" | Medium | Positive | EP |  |
| TC_LSGDW_TG_006 | BR-12 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra lọc khi Từ ngày bằng Đến ngày | PC-02 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm 24/11/2026 hai lần<br>3. Bấm "Chọn" | 24/11/2026 → 24/11/2026 | - Chấp nhận; ô hiện "24/11/2026 → 24/11/2026"<br>- O5 fromDate = toDate = 24/11/2026<br>- Bảng chỉ có GD1 | Medium | Boundary | BVA | [GĐ] Q-06 |
| TC_LSGDW_TG_007 | BR-12, BR-16 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra chọn Đến ngày bằng ngày hiện tại | PC-01 | 1. Chọn khoảng theo SOP-01 | 26/11/2026 → 26/11/2026 | - Ngày 26/11/2026 chọn được<br>- Bảng trống "Không có giao dịch" (TK1 không có GD ngày 26/11) | Medium | Boundary | BVA |  |
| TC_LSGDW_TG_008 | BR-12 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra chặn chọn ngày sau ngày hiện tại | PC-02 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm 20/11/2026<br>3. Bấm 27/11/2026 | 27/11/2026 (D+1) | - 27/11 trở đi hiện mờ, không chọn được<br>- Đến ngày chưa được đặt; không có request O5 | Medium | Negative | BVA |  |
| TC_LSGDW_TG_009 | BR-13 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra lọc khi khoảng ngày đúng 6 tháng | PC-02 | 1. Chọn khoảng theo SOP-01 | 26/05/2026 → 26/11/2026 | - Không hiện E01; ô hiện "26/05/2026 → 26/11/2026"<br>- O5 gọi với khoảng này<br>- Bảng 7 dòng GD1–GD7; không có GD8 (20/05) | Medium | Boundary | BVA | [GĐ] cách tính 6 tháng – Q-05 |
| TC_LSGDW_TG_010 | BR-13 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra lọc khi khoảng ngày ít hơn 6 tháng 1 ngày | PC-01 | 1. Chọn khoảng theo SOP-01 | 27/05/2026 → 26/11/2026 | - Không hiện E01; bảng 7 dòng GD1–GD7 | Low | Boundary | BVA | [GĐ] Q-05 |
| TC_LSGDW_TG_011 | BR-13 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra báo lỗi khi khoảng ngày vượt 6 tháng | PC-02 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm 25/05/2026, bấm 26/11/2026<br>3. Bấm "Chọn" | 25/05/2026 → 26/11/2026 | - Hiện inline "Khoảng thời gian tra cứu tối đa 6 tháng (tạm thời)" (E01)<br>- Không có request O5; ô vẫn "26/10/2026 → 26/11/2026"<br>- Bảng giữ 5 dòng GD1–GD5 | Medium | Boundary | BVA | [GĐ] chặn hay báo, vị trí E01 – Q-05 |
| TC_LSGDW_TG_012 | BR-12 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra chặn chọn Đến ngày trước Từ ngày | PC-02 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm 20/11/2026<br>3. Bấm 10/11/2026 | Từ 20/11/2026, sau đó 10/11/2026 | - 10/11 không được nhận làm Đến ngày<br>- Không tạo khoảng 20/11 → 10/11; không có request O5 với fromDate > toDate | Medium | Negative | EP | [GĐ] Q-06 |
| TC_LSGDW_TG_013 | BR-12, BR-11 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra nút Chọn khi mới chọn 1 ngày | PC-02 | 1. Bấm ô "Thời gian giao dịch"<br>2. Bấm 15/11/2026<br>3. Bấm "Chọn" | 15/11/2026 | - Nút "Chọn" disable (hoặc không phản hồi)<br>- Không có request O5; ô giữ khoảng cũ | Low | Negative | EP | [GĐ] Q-06 |
| TC_LSGDW_TG_014 | BR-10 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra ô ngày khi gõ tay | PC-01 | 1. Bấm vào phần chữ "26/10/2026" trong ô<br>2. Gõ "31/04/2026" | 31/04/2026 | - Không gõ được, chỉ mở popup lịch<br>- Ô vẫn "26/10/2026 → 26/11/2026" | Low | Negative | EG | [GĐ] Q-07 |
| TC_LSGDW_TG_015 | BR-11, BR-15 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra trang hiện tại khi chọn khoảng ngày lúc đang ở trang 2 | TK3 đã đăng nhập, ở sub tab "Lịch sử", đang ở trang 2; DevTools Network | 1. Chọn khoảng theo SOP-01 | 01/11/2026 → 26/11/2026 | - O5 gọi với page = 1 và khoảng mới<br>- Phân trang đánh dấu trang 1 | Medium | Positive | UC |  |
| TC_LSGDW_TG_016 | BR-11 | Lịch sử GD Web – Thời gian giao dịch | Kiểm tra khi đóng popup bằng phím Esc hoặc bấm ra ngoài | PC-02 | 1. Bấm ô, bấm 01/11/2026 rồi 10/11/2026<br>2. Nhấn Esc<br>3. Lặp bước 1, bấm ra ngoài popup | 01/11/2026 → 10/11/2026 | - Cả 2 cách: popup đóng, ô giữ "26/10/2026 → 26/11/2026"<br>- Không có request O5 | Low | Negative | EG | Bổ sung B5; [GĐ] như "Đóng" |
| TC_LSGDW_LC_001 | BR-14 | Lịch sử GD Web – Kết hợp bộ lọc | Kiểm tra lọc khi kết hợp Loại và Thời gian có kết quả | PC-02 | 1. Tick "Mua trái phiếu"<br>2. Chọn khoảng theo SOP-01 | Mua trái phiếu; 17/10/2026 → 26/11/2026 | - Request cuối: transType = BUY, fromDate = 17/10/2026, toDate = 26/11/2026<br>- Bảng 2 dòng GD2, GD6 | Medium | Positive | DT |  |
| TC_LSGDW_LC_002 | BR-14, BR-16 | Lịch sử GD Web – Kết hợp bộ lọc | Kiểm tra bảng trống khi kết hợp bộ lọc không có kết quả | PC-01 | 1. Tick "Nhận trái tức"<br>2. Chọn khoảng theo SOP-01 | Nhận trái tức; 25/11/2026 → 26/11/2026 | - Bảng trống "Không có giao dịch" (I01)<br>- Không hiện phân trang<br>- Bộ lọc giữ "Nhận trái tức" và "25/11/2026 → 26/11/2026" | Medium | Negative | DT | A2 |
| TC_LSGDW_LC_003 | BR-14 | Lịch sử GD Web – Kết hợp bộ lọc | Kiểm tra giữ Loại giao dịch khi đổi Thời gian | PC-02; đã tick "Nhận trái tức" | 1. Chọn khoảng theo SOP-01 | 01/06/2026 → 30/06/2026 | - Request có transType = INTEREST cùng khoảng mới<br>- Bảng chỉ có GD7; nhãn vẫn "Nhận trái tức" | Medium | Positive | DT |  |
| TC_LSGDW_LC_004 | BR-01, BR-14 | Lịch sử GD Web – Kết hợp bộ lọc | Kiểm tra bộ lọc khi đổi sub tab rồi quay lại | PC-01; đã tick "Mua trái phiếu" và chọn 17/10/2026 → 26/11/2026 | 1. Bấm sub tab "Xác nhận"<br>2. Bấm lại sub tab "Lịch sử" | — | - Nhãn "Loại giao dịch"; ô "26/10/2026 → 26/11/2026"<br>- Bảng 5 dòng GD1–GD5 | Low | Positive | EG | [GĐ] Q-17 |
| TC_LSGDW_LC_005 | BR-01, BR-14 | Lịch sử GD Web – Kết hợp bộ lọc | Kiểm tra bộ lọc khi tải lại trang bằng F5 | PC-01; đã tick "Rút tiền" | 1. Nhấn F5 | — | - Vẫn ở sub tab "Lịch sử"<br>- Bộ lọc về mặc định; bảng 5 dòng | Low | Positive | EG | Bổ sung B5; [GĐ] Q-17 |
| TC_LSGDW_PT_001 | BR-15 | Lịch sử GD Web – Phân trang | Kiểm tra phân trang khi có 25 giao dịch | TK3 đã đăng nhập, ở sub tab "Lịch sử" | 1. Quan sát bảng và phân trang | TK3 – 25 GD | - Trang 1 có 10 dòng mới nhất<br>- Phân trang "Trước 1 2 3 Sau"; trang 1 tô cam; "Trước" mờ | Medium | Positive | BVA | [GĐ] size = 10 – Q-11 |
| TC_LSGDW_PT_002 | BR-15, BR-22 | Lịch sử GD Web – Phân trang | Kiểm tra chuyển trang khi bấm số trang | TK3 ở sub tab "Lịch sử"; DevTools Network | 1. Bấm "2" | TK3 | - O5 gọi với page = 2, giữ bộ lọc hiện tại<br>- Hiện 10 dòng tiếp theo, không trùng dòng nào ở trang 1 | Medium | Positive | EP |  |
| TC_LSGDW_PT_003 | BR-15 | Lịch sử GD Web – Phân trang | Kiểm tra trang cuối khi bấm Sau đến hết | TK3 ở sub tab "Lịch sử" | 1. Bấm "Sau" 2 lần | TK3 | - Ở trang 3: 5 dòng cũ nhất<br>- "Sau" mờ, bấm không đổi trang | Medium | Boundary | BVA |  |
| TC_LSGDW_PT_004 | BR-15 | Lịch sử GD Web – Phân trang | Kiểm tra nút Trước khi đang ở trang 2 | TK3 đang ở trang 2 | 1. Bấm "Trước" | TK3 | - Về trang 1; hiện lại đúng 10 dòng đầu như lúc mở tab | Low | Positive | EP |  |
| TC_LSGDW_PT_005 | BR-15 | Lịch sử GD Web – Phân trang | Kiểm tra không hiện phân trang khi chỉ có 1 trang | PC-01 | 1. Quan sát dưới bảng | TK1 – 5 GD | - Không hiện "Trước / 1 / Sau" | Low | Boundary | BVA |  |
| TC_LSGDW_ER_001 | BR-16 | Lịch sử GD Web – Trống & lỗi | Kiểm tra bảng trống khi tài khoản không có giao dịch | TK2 đã đăng nhập | 1. Vào sub tab "Lịch sử" | TK2 | - Bảng trống "Không có giao dịch"<br>- Không hiện phân trang<br>- Bộ lọc vẫn hiển thị, dùng được | Medium | Negative | EP | Figma Web chưa có empty state – Q-16 |
| TC_LSGDW_ER_002 | BR-17 | Lịch sử GD Web – Trống & lỗi | Kiểm tra popup khi O5 trả BND-901 | PC-01; SOP-02 với BND-901 | 1. Tick "Nhận trái tức" | Response O5 = BND-901 | - Popup lỗi chuẩn SHSmart "Hệ thống đang bận", nút "Đóng"<br>- Bấm "Đóng": popup tắt, vẫn ở sub tab "Lịch sử"<br>- Không hiện dữ liệu sai / bảng lỗi | Medium | Negative | EG | [GĐ] ánh xạ mã → câu – Q-19 |
| TC_LSGDW_ER_003 | BR-17 | Lịch sử GD Web – Trống & lỗi | Kiểm tra popup khi O5 trả BND-999 | PC-01; SOP-02 với BND-999 | 1. Bấm sub tab khác rồi về "Lịch sử" | Response O5 = BND-999 | - Popup "Có lỗi xảy ra. Vui lòng thử lại sau", nút "Đóng"<br>- Không hiện số liệu cũ như dữ liệu mới | Medium | Negative | EG | [GĐ] Q-19 |
| TC_LSGDW_ER_004 | BR-17, BR-18 | Lịch sử GD Web – Trống & lỗi | Kiểm tra popup khi phiên đăng nhập hết hạn | TK1 ở sub tab "Lịch sử"; token hết hạn (để quá thời gian phiên hoặc SOP-02 với AUTH_001) | 1. Bấm trang / đổi Loại giao dịch | AUTH_001 | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", nút "Đăng nhập"<br>- Bấm "Đăng nhập" → màn đăng nhập<br>- Không hiện dữ liệu giao dịch mới | High | Negative | EG |  |
| TC_LSGDW_ER_005 | BR-17 | Lịch sử GD Web – Trống & lỗi | Kiểm tra màn khi mất mạng lúc đổi bộ lọc | PC-01; DevTools Network "Offline" | 1. Tick "Mua trái phiếu"<br>2. Bật lại mạng, tick lại "Mua trái phiếu" | — | - Bước 1: popup lỗi chuẩn; không treo loading; không hiện bảng sai<br>- Bước 2: bảng hiện GD2 | Low | Negative | EG | Bổ sung B5; [GĐ] Q-19 |
| TC_LSGDW_EC_001 | BR-21 | Lịch sử GD Web – Edge case | Kiểm tra hiển thị giao dịch hoàn tiền khi hủy giao dịch sau xác nhận thanh toán | TK1 có lệnh mua TPQ12312 đã thanh toán 21/11 (GD2); BO hủy giao dịch trên Portal ngày 26/11/2026 | 1. Mở sub tab "Lịch sử"<br>2. Đối chiếu Portal | GD2 = -134.731.400 | - Có thêm 1 dòng hoàn tiền "+134.731.400" xanh, ngày 26/11/2026, ở đầu bảng<br>- Dòng GD2 "-134.731.400" vẫn còn | High | Negative | ST | [GĐ] loại, nội dung dòng hoàn – Q-12 |
| TC_LSGDW_EC_002 | BR-21 | Lịch sử GD Web – Edge case | Kiểm tra không hiển thị giao dịch mua khi lệnh không khớp (Rejected) | TK1 đặt mua 5 TP ABC12601 ngày 26/11/2026; lệnh bị Rejected, tiền được giải tỏa | 1. Mở sub tab "Lịch sử"<br>2. Tick "Mua trái phiếu" | Lệnh ABC12601 Rejected | - Không có dòng "Trả tiền mua mã ABC12601 ngày 26/11/2026"<br>- Chỉ có GD2 | High | Negative | ST | [GĐ] Dự thảo BA – Q-13 |
| TC_LSGDW_BM_001 | BR-18 | Lịch sử GD Web – Phân quyền | Kiểm tra truy cập sub tab khi chưa đăng nhập | Trình duyệt chưa đăng nhập SHSmart Web | 1. Dán URL sub tab "Lịch sử" (copy từ phiên đã đăng nhập) vào thanh địa chỉ | URL sub tab Lịch sử | - Chuyển về màn đăng nhập<br>- Không hiện bảng; O5 (nếu gọi) trả 401 AUTH_001, không có dữ liệu | High | Permission | EG |  |
| TC_LSGDW_BM_002 | BR-18 | Lịch sử GD Web – Phân quyền | Kiểm tra không hiện giao dịch của tài khoản khác | PC-01; chọn khoảng 26/05/2026 → 26/11/2026 | 1. Tìm "DEF12602" trong bảng | TK4 có GD mua DEF12602 | - Không có dòng nào chứa "DEF12602"<br>- Số dòng = 7 (GD1–GD7 của TK1) | High | Permission | EP |  |
| TC_LSGDW_BM_003 | BR-18 | Lịch sử GD Web – Phân quyền | Kiểm tra dữ liệu khi đăng xuất và đăng nhập tài khoản khác cùng trình duyệt | PC-01 | 1. Đăng xuất TK1<br>2. Đăng nhập TK4 cùng trình duyệt, cùng tab<br>3. Vào sub tab "Lịch sử" | TK1 → TK4 | - Bảng chỉ có GD của TK4 (có DEF12602)<br>- Không còn dòng nào của TK1 (VD TPQ12312 -134.731.400), kể cả thoáng qua | High | Security | EG |  |
| TC_LSGDW_BM_004 | BR-18 | Lịch sử GD Web – Phân quyền | Kiểm tra O5 khi sửa tham số tài khoản sang tài khoản khác | PC-02; Proxyman bắt request O5 | 1. Sửa request O5 thêm / đổi tham số tài khoản (accountNo, custodyCd) sang TK4<br>2. Gửi lại request | Mã TK của TK4 | - BND-403 (hoặc vẫn chỉ trả GD của TK1 theo token)<br>- Không trả GD nào của TK4 | High | Security | EG |  |
| TC_LSGDW_BM_005 | BR-18 | Lịch sử GD Web – Phân quyền | Kiểm tra màn không đổi dữ liệu khi xem và lọc | PC-02 | 1. Ghi nhận số dư tiền sub88 trên Flex<br>2. Lọc lần lượt 5 loại, đổi khoảng ngày, đổi trang<br>3. Kiểm tra lại số dư | TK1 | - Network chỉ có request GET O5, không có POST / PUT / DELETE<br>- Số dư sub88 không đổi; không phát sinh giao dịch mới | Low | Security | EG |  |
| TC_LSGDW_EVT_001 | BR-20 | Lịch sử GD Web – Tracking | Kiểm tra sự kiện LIST khi mở tab và đổi trang | TK3 đã đăng nhập; công cụ xem tracking (DevTools / tool analytics UAT) | 1. Mở sub tab "Lịch sử"<br>2. Bấm trang "2" | TK3 | - Mỗi bước ghi nhận 1 sự kiện SBOND.HISTORY.LIST<br>- Không ghi nhận FILTER.APPLY | Low | Integration | UC |  |
| TC_LSGDW_EVT_002 | BR-20 | Lịch sử GD Web – Tracking | Kiểm tra sự kiện FILTER.APPLY khi đổi bộ lọc | PC-01; công cụ xem tracking | 1. Tick "Nộp tiền"<br>2. Chọn khoảng 01/11/2026 → 26/11/2026 và bấm "Chọn"<br>3. Mở lịch, chọn khoảng khác, bấm "Đóng" | — | - Bước 1, 2: mỗi bước 1 sự kiện SBOND.HISTORY.FILTER.APPLY<br>- Bước 3: không có sự kiện | Low | Integration | UC |  |

### 4.2 API O5 (9 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_LSGDW_API_001 | BR-22, BR-01, BR-02 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi gọi với bộ lọc mặc định | PC-03 | 1. GET /api/v1/bond/app/transactions?fromDate=26/10/2026&toDate=26/11/2026&page=1&size=10 | Token TK1 | - HTTP 200<br>- data.content có 5 phần tử, mỗi phần tử có transTime, message, amount<br>- Phần tử đầu: message "Lãi trái phiếu TPQ12312 chốt ngày 20/11/2026", amount = 34731400; phần tử GD5: amount = -1543489<br>- data.totalCount = 5; data.totalPage = 1 | Medium | Positive | EP | [GĐ] page bắt đầu 1 – Q-11 |
| TC_LSGDW_API_002 | BR-22, BR-08 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi lọc nhiều loại giao dịch | PC-03 | 1. GET O5 khoảng mặc định với transType = BUY và INTEREST | transTypes = BUY, INTEREST | - HTTP 200<br>- data.content chỉ 2 phần tử GD1, GD2; totalCount = 2 | Medium | Positive | EP | [GĐ] cách gửi nhiều giá trị – Q-11 |
| TC_LSGDW_API_003 | BR-22, BR-12 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi fromDate lớn hơn toDate | PC-03 | 1. GET O5 fromDate = 20/11/2026, toDate = 10/11/2026 | 20/11/2026 > 10/11/2026 | - Trả lỗi BND-1xx (V01), không HTTP 500<br>- Không trả data.content | Medium | Negative | EP |  |
| TC_LSGDW_API_004 | BR-22, BR-13 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi khoảng ngày vượt 6 tháng | PC-03 | 1. GET O5 fromDate = 25/05/2026, toDate = 26/11/2026 | Khoảng 6 tháng + 1 ngày | - Trả lỗi BND-1xx tương ứng E01<br>- Không trả GD7, GD8 | Medium | Negative | BVA | [GĐ] Q-05 |
| TC_LSGDW_API_005 | BR-22, BR-12, BR-19 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi toDate sau ngày hiện tại | PC-03 | 1. GET O5 fromDate = 20/11/2026, toDate = 27/11/2026 | toDate = D+1 | - Trả lỗi BND-1xx (V01 phía BE)<br>- Không HTTP 500 | Medium | Negative | BVA |  |
| TC_LSGDW_API_006 | BR-22 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi ngày sai định dạng hoặc không tồn tại | PC-03 | 1. GET O5 fromDate = 2026-11-01<br>2. GET O5 fromDate = 31/04/2026 | "2026-11-01"; "31/04/2026" | - Cả 2: lỗi tham số (HTTP 400 / BND-1xx)<br>- Không HTTP 500, không trả toàn bộ dữ liệu | Low | Negative | EP | Error code cụ thể chưa có |
| TC_LSGDW_API_007 | BR-22, BR-08 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi transType không hợp lệ | PC-03 | 1. GET O5 khoảng mặc định với transType = ABC | transType = ABC | - Lỗi tham số (HTTP 400 / BND-1xx)<br>- Không trả cả 5 loại như khi không lọc | Low | Negative | EP |  |
| TC_LSGDW_API_008 | BR-22, BR-15 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi page lớn hơn totalPage | PC-03 | 1. GET O5 khoảng mặc định, page = 5, size = 10 | page = 5 (totalPage = 1) | - HTTP 200; data.content = []<br>- Không HTTP 500 | Low | Boundary | BVA |  |
| TC_LSGDW_API_009 | BR-22, BR-18 | Lịch sử GD Web – API O5 | Kiểm tra O5 khi không có token | Postman, bỏ header Authorization | 1. GET O5 khoảng mặc định | — | - HTTP 401, error code AUTH_001<br>- Không trả data.content | Medium | Security | EP |  |

## B5. Tự review + RTM
### RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ |
|---|---|---|---|---|
| BR-01 | Mở sub tab "Lịch sử" (tab tô cam) → Web gọi O5 với bộ lọc mặc định: Lo… | 7 | TC_LSGDW_UI_001, TC_LSGDW_DS_001, TC_LSGDW_DS_002, TC_LSGDW_DS_014, TC_LSGDW_LC_004, TC_LSGDW_LC_005, TC_LSGDW_API_001 | Đủ |
| BR-02 | Bảng 3 cột Thời gian / Nội dung / Biến động; Thời gian định dạng HH:mm… | 5 | TC_LSGDW_UI_002, TC_LSGDW_UI_003, TC_LSGDW_UI_009, TC_LSGDW_UI_010, TC_LSGDW_API_001 | Đủ |
| BR-03 | Phạm vi: giao dịch tiền gắn TP S-Bond của KH – Mua TP, Bán TP (theo lệ… | 9 | TC_LSGDW_DS_002, TC_LSGDW_DS_005, TC_LSGDW_DS_006, TC_LSGDW_DS_007, TC_LSGDW_DS_008, TC_LSGDW_DS_009, TC_LSGDW_DS_010, TC_LSGDW_DS_012, TC_LSGDW_DS_013 | Đủ |
| BR-04 | Thời gian giao dịch = thời gian make/check của lệnh trên Portal; sắp x… | 4 | TC_LSGDW_DS_002, TC_LSGDW_DS_003, TC_LSGDW_DS_004, TC_LSGDW_DS_011 | Đủ |
| BR-05 | Biến động (amount): dương = tiền vào, "+#.###" màu xanh; âm = tiền ra,… | 9 | TC_LSGDW_UI_004, TC_LSGDW_UI_005, TC_LSGDW_UI_009, TC_LSGDW_UI_010, TC_LSGDW_DS_005, TC_LSGDW_DS_006, TC_LSGDW_DS_007, TC_LSGDW_DS_008, TC_LSGDW_DS_009 | Đủ |
| BR-06 | Nội dung (message) theo mẫu: "Lãi trái phiếu {Mã TP} chốt ngày dd/MM/y… | 5 | TC_LSGDW_DS_005, TC_LSGDW_DS_006, TC_LSGDW_DS_007, TC_LSGDW_DS_008, TC_LSGDW_DS_009 | Đủ |
| BR-07 | Dòng giao dịch chỉ hiển thị, không có thao tác | 1 | TC_LSGDW_UI_008 | Đủ |
| BR-08 | Dropdown "Loại giao dịch": checkbox chọn nhiều, giá trị Tất cả / Nhận … | 15 | TC_LSGDW_UI_001, TC_LSGDW_UI_006, TC_LSGDW_LGD_001, TC_LSGDW_LGD_002, TC_LSGDW_LGD_003, TC_LSGDW_LGD_004, TC_LSGDW_LGD_005, TC_LSGDW_LGD_006, TC_LSGDW_LGD_010, TC_LSGDW_LGD_011, TC_LSGDW_LGD_012, TC_LSGDW_LGD_013, TC_LSGDW_LGD_014, TC_LSGDW_API_002, TC_LSGDW_API_007 | Đủ |
| BR-09 | Tick "Tất cả" → chọn tất cả các loại | 4 | TC_LSGDW_LGD_007, TC_LSGDW_LGD_008, TC_LSGDW_LGD_009, TC_LSGDW_LGD_010 | Đủ |
| BR-10 | Ô "Thời gian giao dịch" dd/MM/yyyy → dd/MM/yyyy + icon lịch; bấm → pop… | 7 | TC_LSGDW_UI_001, TC_LSGDW_UI_007, TC_LSGDW_TG_001, TC_LSGDW_TG_002, TC_LSGDW_TG_004, TC_LSGDW_TG_005, TC_LSGDW_TG_014 | Đủ |
| BR-11 | Popup lịch: "Đóng" → bỏ thay đổi; "Chọn" → áp dụng khoảng, gọi O5 với … | 6 | TC_LSGDW_TG_001, TC_LSGDW_TG_003, TC_LSGDW_TG_005, TC_LSGDW_TG_013, TC_LSGDW_TG_015, TC_LSGDW_TG_016 | Đủ |
| BR-12 | V01: Từ ngày ≤ Đến ngày; không chọn ngày sau ngày hiện tại (BE kiểm); … | 7 | TC_LSGDW_TG_006, TC_LSGDW_TG_007, TC_LSGDW_TG_008, TC_LSGDW_TG_012, TC_LSGDW_TG_013, TC_LSGDW_API_003, TC_LSGDW_API_005 | Đủ |
| BR-13 | V02: khoảng ngày tối đa 6 tháng (tạm thời); vi phạm → inline E01 "Khoả… | 4 | TC_LSGDW_TG_009, TC_LSGDW_TG_010, TC_LSGDW_TG_011, TC_LSGDW_API_004 | Đủ |
| BR-14 | Kết hợp bộ lọc: O5 nhận đồng thời transType và fromDate / toDate; lọc … | 5 | TC_LSGDW_LC_001, TC_LSGDW_LC_002, TC_LSGDW_LC_003, TC_LSGDW_LC_004, TC_LSGDW_LC_005 | Đủ |
| BR-15 | Phân trang "Trước / số trang / Sau": gọi O5 theo page / size; hiển thị… | 8 | TC_LSGDW_LGD_012, TC_LSGDW_TG_015, TC_LSGDW_PT_001, TC_LSGDW_PT_002, TC_LSGDW_PT_003, TC_LSGDW_PT_004, TC_LSGDW_PT_005, TC_LSGDW_API_008 | Đủ |
| BR-16 | Không có giao dịch (totalCount = 0) → bảng trống "Không có giao dịch" … | 3 | TC_LSGDW_TG_007, TC_LSGDW_LC_002, TC_LSGDW_ER_001 | Đủ |
| BR-17 | Lỗi hệ thống / hết phiên (BND-901 / BND-999 / AUTH_001) → popup lỗi ch… | 4 | TC_LSGDW_ER_002, TC_LSGDW_ER_003, TC_LSGDW_ER_004, TC_LSGDW_ER_005 | Đủ |
| BR-18 | V03: phiên hợp lệ, dữ liệu chỉ của KH đăng nhập (AUTH_001 / BND-403); … | 7 | TC_LSGDW_ER_004, TC_LSGDW_BM_001, TC_LSGDW_BM_002, TC_LSGDW_BM_003, TC_LSGDW_BM_004, TC_LSGDW_BM_005, TC_LSGDW_API_009 | Đủ |
| BR-19 | Ngày nghiệp vụ, giờ lấy theo Backend (BIZ-11) | 2 | TC_LSGDW_DS_014, TC_LSGDW_API_005 | Đủ |
| BR-20 | Sự kiện FE: SBOND.HISTORY.LIST (mở tab / đổi trang), SBOND.HISTORY.FIL… | 2 | TC_LSGDW_EVT_001, TC_LSGDW_EVT_002 | Đủ |
| BR-21 | Edge: hủy GD sau xác nhận thanh toán → ghi nhận và hiển thị giao dịch … | 3 | TC_LSGDW_DS_012, TC_LSGDW_EC_001, TC_LSGDW_EC_002 | Đủ |
| BR-22 | API O5 GET /api/v1/bond/app/transactions: dùng khi mở tab, đổi Loại GD… | 11 | TC_LSGDW_DS_001, TC_LSGDW_PT_002, TC_LSGDW_API_001, TC_LSGDW_API_002, TC_LSGDW_API_003, TC_LSGDW_API_004, TC_LSGDW_API_005, TC_LSGDW_API_006, TC_LSGDW_API_007, TC_LSGDW_API_008, TC_LSGDW_API_009 | Đủ |

### Checklist
| Tiêu chí | Kết quả | Ghi chú |
|---|---|---|
| Mọi BR đều có TC | Đạt | 22/22 BR |
| Field có đủ rỗng / hợp lệ / không hợp lệ / biên | Đạt | Loại GD: 0 / 1 / 2 / 5 loại, mã sai (API_007); Thời gian: Từ = Đến, D, D+1, Từ > Đến, 1 ngày, 6 tháng ± 1 ngày, sai format |
| Có luồng chính, luồng phụ, luồng lỗi | Đạt | DS_001–002, LGD_001, TG_001, PT_002 · Đóng, tháng, Tất cả · ER_001–005, TG_011 |
| Decision table: mỗi rule có TC | Đạt | Kết hợp R1–R5; "Tất cả" R1–R4 |
| State transition có TC | Đạt | DS_012, EC_001, EC_002 |
| Có case phân quyền / bảo mật | Đạt | BM_001–005, ER_004, API_009 |
| 1 TC = 1 mục tiêu, ID không trùng | Đạt | 87 ID duy nhất |
| Test data cụ thể | Đạt | TK1–TK4, GD1–GD9 có ngày giờ, số tiền, nội dung cụ thể |
| Expected đo được | Đạt | Số dòng, giá trị từng ô, màu, tham số request, HTTP status, câu chữ |
| Negative kiểm cả báo lỗi và không thực hiện | Đạt | VD TG_011 "không có request O5, bảng giữ"; TG_003 "không gọi O5"; BM_004 "không trả GD TK4" |
| Message đúng nguyên văn SRS hoặc có [GIẢ ĐỊNH] | Đạt | I01, E01, câu popup lỗi theo mục 6; nội dung Bán / Nộp có [GĐ] Q-10 |
| Thứ tự UI → Positive → … → Security | Đạt | UI trước; trong mỗi nhóm: Positive → Boundary → Negative; Phân quyền, Tracking, API cuối |

### Sửa / bổ sung ở B5
- Bổ sung 7 TC (đánh dấu "Bổ sung B5"): UI_009 (trình duyệt), UI_010 (nội dung dài / số lớn), DS_004 (2 GD cùng phút), LGD_013 (đổi nhanh – race), TG_016 (Esc / bấm ngoài popup), LC_005 (F5), ER_005 (mất mạng).
- Sửa DS_009: ghi rõ "không phải + xanh như Figma" để tester nhận ra lỗi theo Q-09.
- Sửa TG_011: thêm "ô vẫn khoảng cũ, bảng giữ 5 dòng" để kiểm đủ "không thực hiện".
- Tách DS_005 → DS_009 thành 5 TC theo từng loại giao dịch (mỗi loại 1 mục tiêu, nguồn đối chiếu khác nhau).

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 87 (Web 78 · API 9) | — |
| High / Medium / Low | 16 (18%) / 41 (47%) / 30 (34%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 35 : 52 (40% : 60%) | ≈ 30 : 70 |
| Theo Type | Positive 35 · Negative 23 · UI 11 · Boundary 9 · Security 4 · Integration 3 · Permission 2 | — |
| BR được phủ | 22/22 (100%) | 100% |
| Q&A mở | 19 (Blocker 0 · Major 11 · Minor 8) | Blocker = 0 |
| TC có [GIẢ ĐỊNH] | 36 | — |
