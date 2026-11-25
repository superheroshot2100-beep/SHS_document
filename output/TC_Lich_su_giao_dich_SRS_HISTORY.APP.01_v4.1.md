# SBOND.HISTORY.APP.01 – Lịch sử giao dịch (App) – Test design B1→B5 (v4.1)

Nguồn: SRS SBOND.HISTORY.APP.01 – Lịch sử giao dịch (export Confluence 15/11/2026) + ảnh Figma/flow trong SRS  
Người viết: Nguyễn Sỹ Hà · Ngày: 25/11/2026 · Lệnh: /FULL  
Phiên bản: v4.1 – tách Lưu / Đặt lại của bộ lọc; không phụ thuộc dữ liệu test cố định; Steps / Expected tối đa 3 gạch đầu dòng; nhóm Danh sách chỉ kiểm giao diện, rút gọn (xem B5 > Sửa/bổ sung).  
Ký hiệu: [GIẢ ĐỊNH] = giả định để đi tiếp, xem Q-xx; T = ngày nghiệp vụ hiện tại; PC-xx = precondition dùng chung.

## B1. Phân tích yêu cầu
### 1. Tổng quan
- Mục tiêu: NĐT xem lại các giao dịch tiền phát sinh với trái phiếu S-Bond (mua, bán, nhận trái tức, nộp/rút tiền); lọc theo Loại giao dịch và Thời gian giao dịch. Màn chỉ xem, không đổi dữ liệu.
- Actor: KH (NĐT trên SHSmart App). Hệ thống: BE S-Bond (API O5), SHBond Portal (lệnh mua/bán, thanh lý – make/check), Flex (trái tức, nộp/rút tiền).
- Entry: SHSmart > Mở rộng > Sản phẩm đầu tư > S-Bond > tab dưới "Lịch sử". Precondition: KH đã đăng nhập.

### 2. Feature
- F1 Danh sách giao dịch (card) · F2 Màn trống · F3 Phân trang + kéo làm mới · F4 Bottom sheet Bộ lọc (Loại GD, Đặt lại/Lưu) · F5 Bottom sheet Thời gian giao dịch (lịch) · F6 Validation V01/V02 · F7 Lỗi hệ thống / phiên / phân quyền (V03) · F8 Tracking event · F9 API O5

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Mở tab "Lịch sử" → App gọi O5 với bộ lọc mặc định: Loại GD = Tất cả; Thời gian = 1 tháng gần nhất (tạm thời) đến ngày hiện tại; page = 1 | Description; Luồng B1; Entry | Logic/Tích hợp |
| BR-02 | Card hiển thị: Thời gian GD (HH:mm dd/MM/yyyy – transTime); Số tiền (+#.### VND xanh / -#.### VND đỏ – amount); Nội dung (nguyên văn message) | 3.1 #4–6 | UI/Tích hợp |
| BR-03 | Sắp xếp theo thời gian giao dịch mới → cũ | BIZ-32; Luồng B3 | Logic |
| BR-04 | Card chỉ hiển thị, không bấm được; màn không làm đổi dữ liệu | Luồng B3; 3.1 #10; Post-Condition | Logic |
| BR-05 | Phạm vi dữ liệu: GD tiền gắn với S-Bond của KH – Mua/Bán (lệnh đặt mua / thanh lý trên Portal), Nhận trái tức, Nộp tiền, Rút tiền (khai báo trên Flex); thời gian GD = thời gian make/check lệnh trên Portal; dương = tiền vào, âm = tiền ra; phạm vi tiểu khoản sub88 | BIZ-32; 3.1 #4–5 | Logic/Tích hợp |
| BR-06 | Không có GD (totalCount = 0) → màn trống "Không có giao dịch" (SBOND.HISTORY.I01); dùng chung khi lọc không có kết quả | Luồng B2; A2; 3.1 #11; Mục 6 #1 | UI |
| BR-07 | Cuộn đến cuối danh sách → tải trang tiếp (page + 1) đến khi hết totalPage | Luồng B4 | Logic/Tích hợp |
| BR-08 | Kéo xuống để làm mới → gọi lại O5 với bộ lọc hiện tại | Luồng B9 | Logic |
| BR-09 | Icon lọc → bottom sheet "Bộ lọc" giữ lựa chọn hiện tại; nút quay lại trên header đóng, không áp dụng thay đổi | Luồng B5; 3.2 #1 | Logic |
| BR-10 | Icon lọc đổi trạng thái khi đang lọc (Dự thảo BA) | Luồng B8; 3.1 #2 | UI |
| BR-11 | Loại GD: chip chọn nhiều – Tất cả / Nhận trái tức (INTEREST) / Mua trái phiếu (BUY) / Bán trái phiếu (SELL) / Nộp tiền (DEPOSIT) / Rút tiền (WITHDRAW); mặc định Tất cả; chọn "Tất cả" → bỏ các chip khác; bỏ hết → về "Tất cả"; request O5 transType | BIZ-33; Luồng B6; 3.2 #2 | Logic/Tích hợp |
| BR-12 | Thời gian GD: ô từ → đến (dd/MM/yyyy) + icon lịch; mặc định 1 tháng gần nhất; bấm → bottom sheet "Thời gian giao dịch"; request O5 fromDate/toDate (dd/MM/yyyy) | BIZ-33; Luồng B7; 3.2 #3 | Logic/Tích hợp |
| BR-13 | Bottom sheet "Thời gian giao dịch": lịch tháng "Tháng M, yyyy", tuần bắt đầu T2, mũi tên tháng trước/sau; bấm ngày 1 = Từ ngày, ngày 2 = Đến ngày, khoảng giữa tô màu; dòng "Ngày chọn: dd/MM/yyyy - dd/MM/yyyy"; "Đặt lại" → bỏ chọn; "Lưu" → về Bộ lọc với khoảng đã chọn; quay lại → về Bộ lọc, không đổi khoảng ngày | 3.3 #1–4; Luồng B7 | UI/Logic |
| BR-14 | V01: Từ ngày ≤ Đến ngày; không chọn ngày sau ngày hiện tại (BE) – lịch chặn chọn ngày không hợp lệ; O5 – BND-1xx | Mục 5 V01 | Validation |
| BR-15 | V02: khoảng ngày không vượt quá 6 tháng → inline dưới ô ngày "Khoảng thời gian tra cứu tối đa 6 tháng" (SBOND.HISTORY.E01); O5 – BND-1xx | Mục 5 V02; Mục 6 #2; BIZ-33 | Validation |
| BR-16 | Bộ lọc: "Đặt lại" → về mặc định (chưa gọi API); "Lưu" → đóng bottom sheet, gọi O5 với bộ lọc mới, page = 1 | Luồng B8; 3.2 #4–5; BIZ-33 | Logic |
| BR-17 | BIZ-11: ngày nghiệp vụ, giờ lấy theo Backend (ngày hiện tại của bộ lọc mặc định và chặn ngày tương lai) | BIZ-11; V01 | Logic |
| BR-18 | V03: phiên hợp lệ; dữ liệu chỉ của KH đăng nhập; vi phạm → popup lỗi chuẩn → Đăng nhập (AUTH_001 / BND-403) | Mục 5 V03 | Phân quyền/Bảo mật |
| BR-19 | Lỗi hệ thống / token (BND-901, BND-999, AUTH_001) → popup lỗi chuẩn SHSmart: "Hệ thống đang bận" / "Có lỗi xảy ra. Vui lòng thử lại sau" / "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"; CTA Đóng / Đăng nhập | Luồng B10; Mục 6 #3 | Tích hợp/Bảo mật |
| BR-20 | Header: tiêu đề "SH Bond" + nút quay lại (về màn trước); icon ⓘ (không bắt buộc, nội dung – Luồng A1) | 3.1 #1, #3; A1 | UI |
| BR-21 | Tracking event: SBOND.HISTORY.LIST (mở / làm mới / tải trang tiếp), FILTER.OPEN, FILTER.APPLY, FILTER.RESET | Mục 7 | Tích hợp |
| BR-22 | Edge case: lệnh không khớp (Rejected) – giải tỏa tiền → không phát sinh GD mua, không hiển thị; hủy GD sau xác nhận thanh toán – revert tiền: chưa chốt | Mục 8 | Logic |
| BR-23 | API O5: GET /api/v1/bond/app/transactions – dùng khi mở tab, Lưu bộ lọc, tải trang tiếp, kéo làm mới; request transType, fromDate, toDate, page; response data.content[] (transTime, amount, message), totalCount, totalPage | Mục 3; mapping 3.1, 3.2 | Tích hợp |

### 4. Field list
| Màn | Field | Kiểu | Bắt buộc | Ràng buộc / định dạng | Mapping | Message / khi lỗi |
|---|---|---|---|---|---|---|
| Danh sách | Tiêu đề "SH Bond" + nút quay lại | Header | Y | — | — | — |
| Danh sách | Icon lọc | Icon | Y | Đổi trạng thái khi đang lọc | — | — |
| Danh sách | Icon ⓘ | Icon | N | Nội dung chưa có (A1) | — | — |
| Danh sách | Thời gian giao dịch | DateTime | Y | HH:mm dd/MM/yyyy | data.content[].transTime – O5 | — |
| Danh sách | Số tiền giao dịch | Currency | Y | +#.### VND (xanh) / -#.### VND (đỏ) | data.content[].amount – O5 | — |
| Danh sách | Nội dung giao dịch | Text | Y | Nguyên văn message BE | data.content[].message – O5 | null/rỗng: chưa rõ (Q-14) |
| Danh sách | Màn trống | Empty state | C | Hình minh họa + "Không có giao dịch" | data.totalCount = 0 – O5 | SBOND.HISTORY.I01 |
| Bộ lọc | Loại giao dịch | Chip chọn nhiều | N | 6 chip; mặc định Tất cả | transType: INTEREST/BUY/SELL/DEPOSIT/WITHDRAW | — |
| Bộ lọc | Thời gian giao dịch | Ô ngày từ → đến | N | dd/MM/yyyy; mặc định 1 tháng; tối đa 6 tháng | fromDate / toDate | "Khoảng thời gian tra cứu tối đa 6 tháng" (E01) |
| Bộ lọc | Nút "Đặt lại" / "Lưu" | Button | Y | Đặt lại chưa gọi API; Lưu gọi O5 page = 1 | — | — |
| Lịch | Lịch tháng | Calendar | Y | "Tháng M, yyyy"; tuần bắt đầu T2; chặn ngày > ngày hiện tại BE | — | Lịch chặn chọn (V01) |
| Lịch | Ngày chọn | Text | Y | "Ngày chọn: dd/MM/yyyy - dd/MM/yyyy" | — | — |
| Lịch | Nút "Đặt lại" / "Lưu" | Button | Y | Đặt lại bỏ chọn; Lưu về Bộ lọc | — | — |

### 5. Q&A
| Q ID | Câu hỏi | Lý do | Mức | Giả định đề xuất | TC liên quan |
|---|---|---|---|---|---|
| Q-01 | "1 tháng gần nhất (tạm thời)" tính thế nào: cùng ngày tháng trước (25/10 → 25/11) hay 30 ngày? Ngày hiện tại 31/03 thì fromDate là ngày nào? | Quyết định biên dữ liệu mặc định | Major | Cùng ngày tháng trước, tính cả 2 đầu; tháng trước không có ngày đó → ngày cuối tháng trước (31/03/2027 → 28/02/2027) | TC_LSGD_DS_001, TC_LSGD_DS_002, TC_LSGD_NV_012 |
| Q-02 | "Tối đa 6 tháng" tính biên thế nào: 25/05/2026 → 25/11/2026 hợp lệ? 24/05/2026 → 25/11/2026 vi phạm? | Biên V02 | Major | So theo tháng lịch: fromDate ≥ toDate − 6 tháng thì hợp lệ (25/05 hợp lệ, 24/05 vi phạm) | TC_LSGD_API_009, TC_LSGD_API_010, TC_LSGD_TG_001 |
| Q-03 | V02 kiểm ở đâu? Lịch cho chọn khoảng > 6 tháng rồi báo inline ở Bộ lọc, hay chặn ngay trên lịch? Khi đang lỗi, nút "Lưu" của Bộ lọc disable hay bấm được nhưng không áp dụng? | SRS ghi "Inline dưới ô ngày" + error code O5 – chưa rõ FE hay BE chặn | Major | Lịch cho chọn; về Bộ lọc hiển thị E01 dưới ô ngày; bấm "Lưu" không đóng bottom sheet, không gọi O5 | TC_LSGD_TG_001 |
| Q-04 | Mã BND-1xx cụ thể cho V01/V02 là gì? HTTP status? BE có chặn toDate > ngày hiện tại không? | Cần để viết Expected API | Major | HTTP 400 + error code dạng BND-1xx; BE chặn cả khoảng > 6 tháng, fromDate > toDate, toDate tương lai | TC_LSGD_ERR_007, TC_LSGD_API_006, TC_LSGD_API_007, TC_LSGD_API_008, TC_LSGD_API_010 |
| Q-05 | Trên lịch, đã chọn Từ ngày rồi bấm 1 ngày nhỏ hơn: lịch disable ngày đó, đổi chỗ 2 ngày, hay coi là Từ ngày mới? | V01 chỉ ghi "Lịch chặn chọn ngày không hợp lệ" | Major | Ngày nhỏ hơn được coi là Từ ngày mới (chọn lại từ đầu); không bao giờ tạo khoảng Từ > Đến | TC_LSGD_TG_001 |
| Q-06 | Chọn Từ = Đến (1 ngày) thao tác thế nào: bấm cùng 1 ngày 2 lần? | V01 cho phép "≤" | Minor | Bấm cùng 1 ngày 2 lần → khoảng 1 ngày, hợp lệ | TC_LSGD_TG_001 |
| Q-07 | Mới chọn 1 ngày (hoặc vừa "Đặt lại" trên lịch) rồi bấm "Lưu" thì sao? | "Đặt lại → bỏ chọn" để lịch trống | Major | "Lưu" disable đến khi chọn đủ 2 ngày | — |
| Q-08 | Bộ lọc: bấm "Đặt lại" rồi bấm quay lại (không Lưu) → danh sách giữ bộ lọc cũ? | "Đặt lại (chưa gọi API)" + "quay lại không áp dụng" | Minor | Giữ bộ lọc cũ; chỉ "Lưu" mới áp dụng | TC_LSGD_BL_002 |
| Q-09 | Icon lọc "đang lọc" trông thế nào (Dự thảo BA)? Điều kiện: bộ lọc khác mặc định? | Chưa có Figma trạng thái | Minor | Bộ lọc khác mặc định → icon đổi sang trạng thái đang lọc (vd chấm/cam) | TC_LSGD_BL_001, TC_LSGD_BL_002 |
| Q-10 | Icon ⓘ (Luồng A1) hiển thị nội dung gì, dạng popup hay màn? | Cột "Xử lý" trống | Minor | Chưa test được; TC để Blocked | TC_LSGD_NAV_002 |
| Q-11 | Hủy GD sau xác nhận thanh toán – revert tiền: BE có ghi nhận GD hoàn tiền không, thuộc loại GD nào, dấu gì? | Mục 8 còn dấu "?" | Major | Có GD hoàn tiền, số tiền dương xanh; GD mua gốc vẫn giữ | TC_LSGD_NV_009 |
| Q-12 | transType chọn nhiều gửi thế nào (BUY,SELL hay lặp tham số)? "Tất cả" gửi gì (không gửi / ALL)? | Không có request mẫu | Major | Nhiều giá trị nối bằng dấu phẩy; "Tất cả" = không gửi transType | TC_LSGD_API_005, TC_LSGD_LOAI_001 |
| Q-13 | Figma: card "Chuyển tiền tới 069C186666-66 (Online)" hiển thị +34.731.400 (xanh). Nội dung này là Rút tiền (tiền ra) → phải âm đỏ? | Mâu thuẫn với BIZ-32 (âm = tiền ra) | Major | Rút tiền = âm, chữ đỏ; Figma là dữ liệu mẫu sai | TC_LSGD_DS_011 |
| Q-14 | message null/rỗng hiển thị gì? Figma màn 3 có card nội dung "BHB1231" và card trống nội dung. | Chưa có quy định | Minor | Card vẫn hiển thị thời gian + số tiền; dòng nội dung để trống | TC_LSGD_DS_016 |
| Q-15 | Figma Bộ lọc lệch SRS: nhãn "Thời gian giao dch" (thiếu chữ), chip "Nhận trái tức" đang chọn thay vì "Tất cả", khoảng 27/10/2025 → 27/10/2026 (12 tháng > tối đa 6 tháng). | Dễ code theo Figma | Minor | Theo SRS: nhãn "Thời gian giao dịch", mặc định Tất cả + 1 tháng | TC_LSGD_UI_004 |
| Q-16 | Mỗi trang O5 trả bao nhiêu bản ghi (page size)? Có tham số size không? | Cần để viết TC phân trang | Major | 20 bản ghi/trang | TC_LSGD_PT_001, TC_LSGD_PT_002, TC_LSGD_PT_003, TC_LSGD_API_012 |
| Q-17 | "Thời gian giao dịch = thời gian make/check": lấy thời điểm make hay check? GD từ Flex (trái tức, nộp/rút) lấy thời gian nào? | Ảnh hưởng hiển thị, sắp xếp và lọc theo ngày | Major | GD Portal = thời điểm check (duyệt); GD Flex = thời điểm Flex ghi nhận | TC_LSGD_NV_003 |
| Q-18 | Lệnh đặt mua/thanh lý mới make, chưa check có hiển thị không? | Rủi ro hiển thị GD chưa xảy ra | Major | Chỉ hiển thị sau khi check | TC_LSGD_NV_004 |
| Q-19 | "Phạm vi tiểu khoản: sub88" – GD tiền ở tiểu khoản khác có bị loại hoàn toàn? | Câu ngắn, chưa rõ phạm vi | Major | Chỉ hiển thị GD trên sub88 | TC_LSGD_NV_001 |
| Q-20 | Kéo làm mới khi đã tải trang 2+: gọi lại page = 1 và thay toàn bộ danh sách? | Luồng B9 không ghi page | Minor | page = 1, danh sách thay mới | TC_LSGD_PT_010 |
| Q-21 | Lỗi khi tải trang tiếp: giữ các trang đã tải? Có cách thử lại? | Chưa có luồng | Minor | Popup lỗi chuẩn; giữ dữ liệu đã tải; cuộn lại để thử tiếp | TC_LSGD_PT_011 |
| Q-22 | Nút quay lại trên header về màn nào? | "Quay lại màn trước" chưa cụ thể | Minor | Về màn "Sản phẩm đầu tư" | TC_LSGD_NAV_001 |
| Q-23 | Thay đổi trong Bộ lọc, bấm quay lại, mở lại: hiển thị thay đổi chưa lưu hay bộ lọc đang áp dụng? | "Giữ lựa chọn hiện tại" có 2 cách hiểu | Minor | Hiển thị bộ lọc đang áp dụng | — |
| Q-24 | Lịch có giới hạn ngày quá khứ xa nhất không? | Chưa quy định | Minor | Không giới hạn, chỉ ràng buộc 6 tháng | — |
| Q-25 | Mở lịch: hiển thị tháng nào? Khoảng đang áp dụng có được tô sẵn? | Chưa quy định | Minor | Tháng của Đến ngày; khoảng đang áp dụng được tô sẵn | TC_LSGD_UI_005, TC_LSGD_TG_001 |
| Q-26 | SRS tham chiếu "Q24", "Q25" (log Q&A của BA) nhưng không có nội dung trong tài liệu. | Thiếu ngữ cảnh quyết định | Minor | Coi như đã chốt theo nội dung SRS hiện tại | — |
| Q-27 | Mất mạng / API chậm: popup gì, có loading không? | Mục 6 chỉ có lỗi hệ thống và token | Minor | Có loading; mất mạng → popup lỗi chuẩn SHSmart | TC_LSGD_ERR_005, TC_LSGD_ERR_006, TC_LSGD_ERR_008 |
| Q-28 | transTime trả theo múi giờ nào? App có quy đổi theo múi giờ thiết bị không? | BIZ-11: giờ lấy theo BE | Minor | Luôn hiển thị giờ Việt Nam, không quy đổi theo thiết bị | TC_LSGD_NV_013 |
| Q-29 | Nội dung giao dịch dài: xuống dòng hay cắt "..."? | Chưa quy định | Minor | Xuống dòng, không cắt | TC_LSGD_UI_007 |
| Q-30 | Chọn đủ 5 chip con có tự chuyển về "Tất cả" không? | BIZ-33 không nêu | Minor | Không tự chuyển; kết quả bằng Tất cả | — |
| Q-31 | Rời tab Lịch sử rồi quay lại: bộ lọc về mặc định? | Luồng B1 ghi mở tab gọi O5 mặc định | Minor | Về mặc định | — |
| Q-32 | Popup lỗi chuẩn: mã nào ứng với nội dung nào (BND-901 / BND-999 / AUTH_001)? | Mục 6 #3 gộp 3 mã, 3 nội dung | Minor | BND-901 = "Hệ thống đang bận"; BND-999 = "Có lỗi xảy ra. Vui lòng thử lại sau"; AUTH_001 = "Phiên đăng nhập không hợp lệ hoặc đã hết hạn" | TC_LSGD_ERR_001, TC_LSGD_ERR_002, TC_LSGD_ERR_003 |
| Q-33 | O5 xác định KH bằng token hay có tham số định danh (account/custodyCd)? Nếu có tham số, BE có kiểm khớp token? | Cần để test IDOR (V03 BND-403) | Major | Xác định theo token; tham số định danh lạ bị bỏ qua hoặc trả BND-403 | TC_LSGD_PQ_002, TC_LSGD_API_018 |
| Q-34 | O5 khi thiếu fromDate/toDate, sai format ngày, page ≤ 0, page > totalPage trả gì? | Chưa có đặc tả lỗi tham số | Minor | Thiếu ngày → BE dùng mặc định 1 tháng; sai format / page ≤ 0 → 400; page > totalPage → content rỗng | TC_LSGD_API_011, TC_LSGD_API_013, TC_LSGD_API_014, TC_LSGD_API_022 |
| Q-35 | Đang ở tháng hiện tại, mũi tên tháng sau: disable hay cho sang tháng sau (toàn ngày bị chặn)? | Chưa quy định | Minor | Cho sang tháng sau, mọi ngày disable | — |
| Q-36 | 2 GD cùng transTime (cùng phút) sắp xếp theo tiêu chí phụ nào? | Ảnh hưởng thứ tự ổn định khi phân trang | Minor | Theo thứ tự BE trả, không trùng/thiếu giữa các trang | — |

### 6. Rủi ro chính
| # | Rủi ro | Tác động | Mức |
|---|---|---|---|
| 1 | Biên "1 tháng" và "6 tháng" chưa định nghĩa, V02 chưa rõ FE hay BE chặn | FE/BE lệch nhau; KH tra cứu sai khoảng hoặc bị chặn sai | High |
| 2 | Dấu và màu số tiền sai theo loại GD (Figma đang để Rút tiền +xanh) | KH hiểu sai dòng tiền vào/ra → khiếu nại | High |
| 3 | Phạm vi dữ liệu: sub88, lệnh Rejected, lệnh chưa check, hoàn tiền | Hiện GD không có thật hoặc thiếu GD thật | High |
| 4 | IDOR / cache khi đổi tài khoản trên cùng máy | Lộ lịch sử tiền của KH khác | High |
| 5 | Phân trang + lọc + kéo làm mới chồng nhau; ngày lấy theo thiết bị thay vì BE | Card trùng/thiếu; khoảng ngày sai | Medium |

## B2. Chiến lược test
- **In-scope:** Danh sách card (hiển thị, định dạng, sắp xếp), màn trống, phân trang, kéo làm mới, bottom sheet Bộ lọc và Thời gian giao dịch, V01/V02/V03, popup lỗi chuẩn, phạm vi dữ liệu (sub88, Rejected, tích hợp Portal/Flex), tracking event, API O5.
- **Out-of-scope:** Màn Web (SBOND.HISTORY.WEB.01); nội dung icon ⓘ (chờ Q-10); Noti/Email Bond_01, Bond_07; nghiệp vụ duyệt lệnh trên Portal và hạch toán Flex (chỉ dùng tạo dữ liệu); hiệu năng tải.
- **Loại test:** Functional, UI, API, Integration (Portal/Flex → App), Security cơ bản (token, IDOR, cache, injection), Error guessing (mạng, double-tap, dữ liệu xấu, giờ thiết bị).

| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC (App / API) |
|---|---|---|---|---|
| BR-01 | UC | EG | High | 7 / 2 |
| BR-02 | EP | BVA, EG | High | 16 / 1 |
| BR-03 | EP | BVA | Medium | 3 / 1 |
| BR-04 | EG | — | Medium | 2 / 1 |
| BR-05 | DT | UC, EG | High | 14 / 2 |
| BR-06 | UC | EG | Medium | 4 / 1 |
| BR-07 | BVA | UC, EG | Medium | 9 / 3 |
| BR-08 | UC | EG | Medium | 4 / 0 |
| BR-09 | UC | EG | Medium | 4 / 0 |
| BR-10 | EP | — | Low | 2 / 0 |
| BR-11 | DT | EP, PW, EG | Medium | 4 / 3 |
| BR-12 | UC | EG | Medium | 2 / 0 |
| BR-13 | ST | EG | Medium | 2 / 0 |
| BR-14 | BVA | EG | Medium | 3 / 2 |
| BR-15 | BVA | EP | High | 2 / 2 |
| BR-16 | UC | DT, EG | Medium | 4 / 0 |
| BR-17 | EG | BVA | Medium | 4 / 1 |
| BR-18 | EG | — | High | 4 / 3 |
| BR-19 | EP | EG | Medium | 6 / 2 |
| BR-20 | UC | — | Low | 3 / 0 |
| BR-21 | UC | — | Low | 6 / 0 |
| BR-22 | EG | DT | High | 2 / 1 |
| BR-23 | EP | BVA, EG | High | 0 / 4 |

- **Môi trường:** UAT; iOS + Android (1 máy màn hẹp 360dp); Proxyman/Charles (xem, sửa, mock request/response); Postman; công cụ debug analytics; tài khoản BO có quyền make/check lệnh trên SHBond Portal; quyền tạo bút toán nộp/rút, trái tức trên Flex UAT; khả năng đặt ngày nghiệp vụ BE (cho NV_012).
- **Exit criteria:** 100% TC High đã chạy; 0 bug Critical/Major mở; Q&A Major có trả lời hoặc PO chấp nhận giả định; RTM 100% BR có TC Pass.

### Dữ liệu cần có trên môi trường
| Mã | Điều kiện dữ liệu cần có trên UAT | Dùng cho |
|---|---|---|
| PC-01 | TK có GD đủ 5 loại (Nhận trái tức, Mua, Bán, Nộp, Rút) trên tiểu khoản 88 trong 1 tháng gần nhất, và có GD cũ hơn 1 tháng | Phần lớn TC App |
| PC-05 | TK có hơn 40 GD trong 1 tháng gần nhất (1 loại có hơn 20 GD) | Phân trang |
| PC-06 | TK không có GD nào | Màn trống, đổi tài khoản |
| Điều kiện riêng | Ghi ngay trong cột Precondition của TC (VD "TK có GD ở tiểu khoản khác 88") | Từng TC |

### Precondition / thao tác dùng chung
| Mã | Nội dung |
|---|---|
| T | Ngày nghiệp vụ hiện tại của hệ thống (BE). VD T = 25/11/2026 thì T−1 tháng = 25/10/2026 |
| PC-01 | KH đã đăng nhập SHSmart App (UAT), đang ở S-Bond; TK có GD đủ 5 loại trong 1 tháng gần nhất và có GD cũ hơn 1 tháng |
| PC-02 | PC-01 + đang ở tab "Lịch sử", bộ lọc mặc định |
| PC-03 | PC-02 + đã bấm icon lọc, bottom sheet "Bộ lọc" đang mở |
| PC-04 | PC-03 + đã bấm ô ngày, bottom sheet "Thời gian giao dịch" đang mở |
| PC-05 | KH có hơn 40 GD trong 1 tháng gần nhất đã đăng nhập, đang ở tab "Lịch sử" |
| PC-06 | KH không có GD nào đã đăng nhập, đang ở S-Bond |
| PC-API | Postman, UAT; Header Authorization: Bearer <token KH của PC-01>; endpoint O5 = GET /api/v1/bond/app/transactions |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên |
|---|---|---|---|
| Khoảng ngày (V02) | Khoảng ngắn; T−1 tháng → T (mặc định) | Khoảng khoảng 11 tháng | T−6 tháng → T (hợp lệ); T−6 tháng−1 ngày → T (vượt) |
| Từ / Đến (V01) | Từ < Đến; Từ = Đến | Đến < Từ; ngày > T | T (chọn được); T+1 (bị chặn) |
| Mặc định 1 tháng | T−1 tháng → T | — | GD đúng ngày T−1 tháng (có); GD trước đó (không); T = ngày 31 |
| Loại GD | 1 loại; 2 loại; Tất cả | transType lạ; SQLi | Bỏ loại cuối → Tất cả |
| Số tiền | Tiền vào (+ xanh); tiền ra (- đỏ) | Nội dung có ký tự đặc biệt; nội dung trống | < 1.000; ≥ 1 tỷ |
| Thời gian GD | Giờ chiều (24h) | Ngày / múi giờ thiết bị sai | Giờ, ngày 1 chữ số |
| Phân trang (giả định 20/trang) | Trang 1, 2 | page 0 | Trang cuối; page > totalPage |

### Decision table – dấu / màu số tiền theo loại GD (BIZ-32)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Loại GD | Nhận trái tức | Mua trái phiếu | Bán trái phiếu | Nộp tiền | Rút tiền |
| Chiều tiền | Vào | Ra | Vào | Vào | Ra |
| → Dấu / màu | + xanh | - đỏ | + xanh | + xanh | - đỏ |
| TC | DS_007 | DS_008 | DS_009 | DS_010 | DS_011 |

### Decision table – chip Loại giao dịch (BIZ-33)
| Trạng thái hiện tại / Hành động | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Đang chọn | Tất cả | Tất cả | Trái tức + Mua | Chỉ Trái tức | Trái tức + Mua |
| Hành động | Bấm 1 chip con | Bấm "Tất cả" | Bấm "Tất cả" | Bỏ chip Trái tức | Bỏ chip Mua |
| → Kết quả | Chip con chọn, Tất cả bỏ | Tất cả vẫn chọn | Chỉ Tất cả chọn | Tất cả tự chọn | Chỉ Trái tức chọn |
| TC | LOAI_001 | — | LOAI_001 | LOAI_001 | — |

### State transition – bottom sheet Thời gian giao dịch (V01, giả định Q-05/Q-07)
| Trạng thái \ Sự kiện | Bấm ngày hợp lệ | Bấm ngày < Từ ngày | Bấm ngày > hôm nay | Đặt lại | Lưu | Quay lại |
|---|---|---|---|---|---|---|
| Chưa chọn ngày | → Đã chọn Từ | — | Bị chặn | Giữ | ✖ disable [GĐ] | Về Bộ lọc, khoảng cũ |
| Đã chọn Từ | → Đủ khoảng | → Từ ngày mới [GĐ] | Bị chặn | → Chưa chọn | ✖ disable [GĐ] | Về Bộ lọc, khoảng cũ |
| Đủ khoảng | → Đã chọn Từ (chọn lại) [GĐ] | → Đã chọn Từ (chọn lại) [GĐ] | Bị chặn | → Chưa chọn | → Về Bộ lọc với khoảng mới | Về Bộ lọc, khoảng cũ |

### Use case
- Luồng chính: mở tab → O5 mặc định → card mới → cũ → cuộn tải trang tiếp → kéo làm mới.
- Luồng phụ: lọc theo Loại GD / Thời gian GD / kết hợp; Đặt lại; lọc không kết quả (A2); icon ⓘ (A1).
- Ngoại lệ: V02 > 6 tháng, ngày tương lai, lỗi hệ thống, hết phiên, mất mạng, IDOR.

### Error guessing áp dụng
- Double-tap Lưu / cuộn nhanh cuối trang · mất mạng khi mở tab, khi Lưu · API chậm · lỗi khi tải trang tiếp · message chứa HTML/emoji, message null · số tiền rất lớn trên màn hẹp · đổi ngày/múi giờ thiết bị · đổi tài khoản cùng máy · sửa request (IDOR, SQLi, khoảng > 6 tháng bypass FE) · token hết hạn giữa thao tác.

## B4. Test case
### 4.1 App (75 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_LSGD_UI_001 | BR-20, BR-01 | Lịch sử GD – Layout | Kiểm tra layout mặc định màn Lịch sử khi có giao dịch | PC-02 | - Quan sát màn hình | — | - Header: "<", "SH Bond", icon lọc, icon ⓘ<br>- Danh sách giao dịch dạng card<br>- Thanh tab dưới; tab "Lịch sử" active (màu cam) | Low | UI | EG | Đối chiếu Figma màn 1 |
| TC_LSGD_UI_002 | BR-02 | Lịch sử GD – Layout | Kiểm tra bố cục 1 card giao dịch | PC-02 | - Quan sát 1 card bất kỳ | — | - Dòng trên: thời gian (trái), số tiền (phải)<br>- Dòng dưới: nội dung giao dịch | Low | UI | EG | Figma màn 1 |
| TC_LSGD_UI_003 | BR-06 | Lịch sử GD – Layout | Kiểm tra layout màn trống khi KH không có giao dịch | PC-06 | - Mở tab "Lịch sử" | — | - Hình minh họa + "Không có giao dịch" ở giữa màn<br>- Header và thanh tab dưới vẫn hiển thị | Low | UI | EG | Figma màn 2 |
| TC_LSGD_UI_004 | BR-09, BR-11, BR-12 | Lịch sử GD – Bộ lọc | Kiểm tra layout mặc định bottom sheet Bộ lọc | PC-02 | - Bấm icon lọc | — | - Header "<" + "Bộ lọc"; nền phía sau làm mờ<br>- 6 loại: Tất cả, Nhận trái tức, Mua trái phiếu, Bán trái phiếu, Nộp tiền, Rút tiền; "Tất cả" chọn sẵn<br>- Ô ngày "T−1 tháng → T"; nút "Đặt lại", "Lưu" | Low | UI | EG | Figma lệch SRS – Q-15; [GIẢ ĐỊNH] 1 tháng – Q-01 |
| TC_LSGD_UI_005 | BR-13 | Lịch sử GD – Lịch | Kiểm tra layout bottom sheet Thời gian giao dịch | PC-03 | - Bấm ô Thời gian giao dịch | — | - Header "<" + "Thời gian giao dịch"; "Tháng M, yyyy", tuần bắt đầu T2, mũi tên chuyển tháng<br>- Dòng "Ngày chọn: dd/MM/yyyy - dd/MM/yyyy" đúng khoảng đang áp dụng<br>- Nút "Đặt lại", "Lưu" | Low | UI | EG | [GIẢ ĐỊNH] – Q-25 |
| TC_LSGD_UI_006 | BR-02 | Lịch sử GD – Layout | Kiểm tra số tiền rất lớn trên màn hẹp | PC-02; máy Android 360dp; TK có GD từ 10 tỷ trở lên | - Xem GD có số tiền lớn nhất | — | - Số tiền hiện đủ trên 1 dòng, không bị cắt, không chồng lên thời gian | Low | UI | EG |  |
| TC_LSGD_UI_007 | BR-02 | Lịch sử GD – Layout | Kiểm tra nội dung giao dịch dài | PC-02; TK có GD nội dung dài hơn 1 dòng | - Xem GD có nội dung dài | — | - Nội dung tự xuống dòng, hiện đủ, không cắt "..." | Low | UI | EG | [GIẢ ĐỊNH] – Q-29 |
| TC_LSGD_NAV_001 | BR-20 | Lịch sử GD – Header | Kiểm tra nút quay lại trên header | PC-02 | - Bấm "<" | — | - Về màn "Sản phẩm đầu tư" | Low | Positive | UC | [GIẢ ĐỊNH] – Q-22 |
| TC_LSGD_NAV_002 | BR-20 | Lịch sử GD – Header | Kiểm tra bấm icon ⓘ | PC-02 | - Bấm icon ⓘ, rồi đóng lại | — | - Mở nội dung thông tin màn Lịch sử<br>- Đóng lại, danh sách giữ nguyên | Low | Positive | UC | Chưa có nội dung – Q-10; để Blocked |
| TC_LSGD_NAV_003 | BR-01 | Lịch sử GD – Header | Kiểm tra vào tab Lịch sử từ thanh tab dưới | PC-01; đang ở tab "DS chào bán" | - Bấm tab "Lịch sử" | — | - Tab "Lịch sử" active<br>- Hiện danh sách GD 1 tháng gần nhất | Medium | Positive | UC |  |
| TC_LSGD_DS_001 | BR-01, BR-05 | Lịch sử GD – Danh sách | Kiểm tra biên 1 tháng của danh sách mặc định | PC-01; TK có GD đúng ngày T−1 tháng và GD trước ngày đó | - Mở tab "Lịch sử", cuộn xuống cuối | VD T = 25/11/2026 → biên 25/10/2026 | - Có GD ngày T−1 tháng<br>- Không có GD trước ngày T−1 tháng | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-01 |
| TC_LSGD_DS_002 | BR-01, BR-05 | Lịch sử GD – Danh sách | Kiểm tra danh sách mặc định khi mở tab | PC-01 | - Mở tab "Lịch sử"<br>- Cuộn hết danh sách | — | - Hiện đủ GD từ T−1 tháng đến T, có đủ 5 loại<br>- Không có GD cũ hơn 1 tháng | High | Positive | EP | [GIẢ ĐỊNH] – Q-01 |
| TC_LSGD_DS_003 | BR-03 | Lịch sử GD – Danh sách | Kiểm tra thứ tự giao dịch | PC-02 | - Đọc thời gian từ trên xuống | — | - GD xếp từ mới đến cũ | Medium | Positive | EP |  |
| TC_LSGD_DS_004 | BR-03 | Lịch sử GD – Danh sách | Kiểm tra thứ tự khi 2 GD cùng ngày | PC-02; TK có 2 GD cùng ngày, khác giờ | - Xem 2 GD cùng ngày | — | - GD giờ muộn hơn nằm trên | Medium | Boundary | BVA |  |
| TC_LSGD_DS_005 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra định dạng thời gian khi giờ chiều | PC-02; TK có GD sau 12h trưa | - Xem GD sau 12h trưa | — | - Dạng "HH:mm dd/MM/yyyy", giờ 24h (VD "16:45 10/11/2026"), không có AM/PM | Medium | Positive | EP |  |
| TC_LSGD_DS_006 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra định dạng thời gian khi có số 1 chữ số | PC-02; TK có GD lúc 0h–9h hoặc ngày 1–9 | - Xem GD đó | — | - Giờ, phút, ngày, tháng đủ 2 chữ số (VD "00:01 05/11/2026") | Low | Boundary | BVA |  |
| TC_LSGD_DS_007 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Nhận trái tức | PC-02 | - Xem 1 GD Nhận trái tức | — | - Số tiền có dấu "+", màu xanh, dạng "+#.### VND" | High | Positive | DT |  |
| TC_LSGD_DS_008 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Mua trái phiếu | PC-02 | - Xem 1 GD Mua trái phiếu | — | - Số tiền có dấu "-", màu đỏ, dạng "-#.### VND" | High | Positive | DT |  |
| TC_LSGD_DS_009 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Bán trái phiếu | PC-02 | - Xem 1 GD Bán trái phiếu | — | - Số tiền có dấu "+", màu xanh | High | Positive | DT |  |
| TC_LSGD_DS_010 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Nộp tiền | PC-02 | - Xem 1 GD Nộp tiền | — | - Số tiền có dấu "+", màu xanh | High | Positive | DT |  |
| TC_LSGD_DS_011 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Rút tiền | PC-02 | - Xem 1 GD Rút tiền | — | - Số tiền có dấu "-", màu đỏ | High | Negative | DT | Figma để dấu +, màu xanh – Q-13 |
| TC_LSGD_DS_012 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi nhỏ hơn 1.000 | PC-02; TK có GD dưới 1.000 đồng | - Xem GD đó | — | - Không có dấu chấm, không có phần lẻ (VD "+500 VND") | Low | Boundary | BVA |  |
| TC_LSGD_DS_013 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi từ 1 tỷ trở lên | PC-02; TK có GD từ 1 tỷ trở lên | - Xem GD đó | — | - Dấu chấm ngăn cách mỗi 3 chữ số (VD "+12.345.678.900 VND") | Medium | Boundary | BVA |  |
| TC_LSGD_DS_014 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra nội dung giao dịch hiển thị nguyên văn | PC-02 | - So nội dung 1 GD mỗi loại với nội dung trên Portal / Flex | — | - Giống hệt, không cắt, không đổi chữ<br>- Đúng mẫu SRS, VD "Lãi trái phiếu {Mã TP} chốt ngày dd/MM/yyyy", "Trả tiền mua mã {Mã TP} ngày dd/MM/yyyy" | Medium | Positive | EP |  |
| TC_LSGD_DS_015 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra nội dung khi có ký tự đặc biệt | PC-01; Dev đổi nội dung 1 GD trên UAT | - Mở tab "Lịch sử", xem GD đó | <b>Test</b> & "QA" 😀 | - Hiện đúng nguyên chuỗi, chữ không in đậm<br>- App không bị thoát | Medium | Security | EG |  |
| TC_LSGD_DS_016 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra giao dịch khi không có nội dung | PC-01; Dev xóa nội dung 1 GD trên UAT | - Mở tab "Lịch sử", xem GD đó | — | - Vẫn hiện thời gian và số tiền<br>- Nội dung trống, không hiện "null" | Low | Negative | EG | [GIẢ ĐỊNH] – Q-14 |
| TC_LSGD_DS_017 | BR-04 | Lịch sử GD – Danh sách | Kiểm tra bấm vào giao dịch | PC-02 | - Bấm 1 GD bất kỳ | — | - Không chuyển màn, không mở gì<br>- Danh sách giữ nguyên | Medium | Negative | EG |  |
| TC_LSGD_DS_018 | BR-04 | Lịch sử GD – Danh sách | Kiểm tra nhấn giữ giao dịch | PC-02 | - Nhấn giữ 1 GD trong 3 giây | — | - Không hiện menu, màn giữ nguyên | Low | Negative | EG |  |
| TC_LSGD_DS_019 | BR-06 | Lịch sử GD – Màn trống | Kiểm tra màn trống khi KH không có giao dịch | PC-06 | - Mở tab "Lịch sử" | — | - Hiện hình minh họa và "Không có giao dịch" | Medium | Positive | UC |  |
| TC_LSGD_DS_020 | BR-06, BR-11 | Lịch sử GD – Màn trống | Kiểm tra màn trống khi lọc không có kết quả | PC-03 | - Chọn "Rút tiền" + 1 khoảng ngày không có GD rút tiền, bấm "Lưu" | Khoảng ngày không có GD Rút tiền | - Hiện "Không có giao dịch" | Medium | Negative | UC | Luồng A2 |
| TC_LSGD_DS_021 | BR-06, BR-16 | Lịch sử GD – Màn trống | Kiểm tra thoát màn trống khi đặt lại bộ lọc | Đang ở màn trống của DS_020 | - Bấm icon lọc, bấm "Đặt lại", bấm "Lưu" | — | - Hiện lại danh sách mặc định | Medium | Positive | UC | Bổ sung B5 |
| TC_LSGD_NV_001 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra không hiển thị GD tiểu khoản khác 88 | PC-02; TK có GD nộp tiền ở tiểu khoản khác 88 trong 1 tháng | - Tìm GD đó trong danh sách | — | - Không hiển thị | High | Negative | EP | [GIẢ ĐỊNH] – Q-19 |
| TC_LSGD_NV_002 | BR-22 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra không hiển thị GD khi lệnh mua bị Rejected | PC-02; TK có lệnh mua bị Rejected trong 1 tháng | - Tìm GD mua của lệnh đó | — | - Không hiển thị | High | Negative | EG | Mục 8 |
| TC_LSGD_NV_003 | BR-05, BR-08 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Mua xuất hiện khi lệnh đặt mua được check trên Portal | PC-02; KH vừa đặt lệnh mua qua App | - BO make + check lệnh trên Portal<br>- Kéo làm mới trên App | — | - GD mới ở đầu: thời gian = lúc check, số tiền "-", màu đỏ<br>- Nội dung "Trả tiền mua mã {Mã TP} ngày dd/MM/yyyy" | High | Integration | UC | [GIẢ ĐỊNH] – Q-17 |
| TC_LSGD_NV_004 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Mua không hiển thị khi lệnh mới make, chưa check | PC-02; KH vừa đặt lệnh mua qua App | - BO chỉ make lệnh trên Portal<br>- Kéo làm mới trên App | — | - Không có GD mua của lệnh đó | High | Negative | ST | [GIẢ ĐỊNH] – Q-18 |
| TC_LSGD_NV_005 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Bán xuất hiện khi lệnh thanh lý được duyệt | PC-02; KH đã gửi yêu cầu thanh lý | - BO make + check lệnh thanh lý trên Portal<br>- Kéo làm mới trên App | — | - Có GD Bán: số tiền "+", màu xanh, thời gian = lúc check | High | Integration | UC | [GIẢ ĐỊNH] – Q-17 |
| TC_LSGD_NV_006 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Nhận trái tức xuất hiện khi Flex trả trái tức | PC-02; KH đang nắm giữ trái phiếu trên tiểu khoản 88 | - Trả trái tức trên Flex UAT<br>- Kéo làm mới trên App | — | - Có GD "+", màu xanh, nội dung "Lãi trái phiếu {Mã TP} chốt ngày dd/MM/yyyy" | High | Integration | UC |  |
| TC_LSGD_NV_007 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Nộp tiền xuất hiện khi Flex ghi nhận nộp tiền | PC-02 | - Nộp tiền vào tiểu khoản 88 trên Flex UAT<br>- Kéo làm mới trên App | 10.000.000 | - Có GD "+10.000.000 VND", màu xanh, ở đầu danh sách | Medium | Integration | UC |  |
| TC_LSGD_NV_008 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Rút tiền xuất hiện khi Flex ghi nhận rút tiền | PC-02 | - Rút tiền từ tiểu khoản 88 trên Flex UAT<br>- Kéo làm mới trên App | 5.000.000 | - Có GD "-5.000.000 VND", màu đỏ, ở đầu danh sách | Medium | Integration | UC |  |
| TC_LSGD_NV_009 | BR-22 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra khi GD bị hủy và hoàn tiền sau xác nhận thanh toán | PC-02; KH có lệnh mua đã xác nhận thanh toán | - BO hủy lệnh, hệ thống hoàn tiền<br>- Kéo làm mới trên App | — | - Có GD hoàn tiền "+", màu xanh<br>- GD mua gốc vẫn hiển thị | Medium | Integration | EG | [GIẢ ĐỊNH] – Q-11; để Blocked |
| TC_LSGD_NV_010 | BR-17, BR-01 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra khoảng ngày mặc định khi ngày thiết bị sai | PC-01; đổi ngày thiết bị lệch khỏi T (tắt tự động) | - Mở tab "Lịch sử", bấm icon lọc | — | - Ô ngày vẫn "T−1 tháng → T" theo ngày hệ thống, không theo thiết bị | Medium | Negative | EG | Bổ sung B5 |
| TC_LSGD_NV_011 | BR-17, BR-14 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra lịch chặn ngày tương lai khi ngày thiết bị lớn hơn T | PC-04; ngày thiết bị sau T | - Bấm các ngày sau T | — | - Các ngày sau T bị khóa, không chọn được | Medium | Negative | EG | Bổ sung B5 |
| TC_LSGD_NV_012 | BR-17, BR-01 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra khoảng mặc định 1 tháng khi T là ngày 31 | UAT đặt ngày nghiệp vụ = 31/03/2027 | - Mở tab "Lịch sử", bấm icon lọc | — | - Ô ngày "28/02/2027 → 31/03/2027" | Low | Boundary | BVA | [GIẢ ĐỊNH] – Q-01; Bổ sung B5 |
| TC_LSGD_NV_013 | BR-02, BR-17 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra giờ hiển thị khi thiết bị khác múi giờ | PC-01; đổi múi giờ thiết bị sang UTC+0 | - Mở tab "Lịch sử", xem 1 GD đã biết giờ | — | - Giờ hiển thị vẫn theo giờ Việt Nam, không bị lệch 7 tiếng | Low | Negative | EG | [GIẢ ĐỊNH] – Q-28; Bổ sung B5 |
| TC_LSGD_PT_001 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra số GD tải lần đầu | PC-05 | - Mở tab "Lịch sử" | — | - Hiện 20 GD đầu tiên | Medium | Positive | EP | [GIẢ ĐỊNH] 20 GD/trang – Q-16 |
| TC_LSGD_PT_002 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra tải trang tiếp khi cuộn đến cuối | PC-05 | - Cuộn đến GD cuối cùng | — | - Tải thêm 20 GD, không trùng GD đã có | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_PT_003 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra tải trang cuối khi còn ít hơn 20 GD | PC-05 | - Cuộn liên tục đến hết | — | - Trang cuối tải đủ số GD còn lại; tổng số GD đúng với dữ liệu | Medium | Boundary | BVA | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_PT_004 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra không tải thêm khi đã ở trang cuối | PC-05; đã tải hết GD | - Tiếp tục vuốt lên 3 lần | — | - Không tải thêm, không có GD trùng | Medium | Boundary | BVA |  |
| TC_LSGD_PT_005 | BR-03, BR-07 | Lịch sử GD – Phân trang | Kiểm tra thứ tự giữa 2 trang | PC-05; đã tải trang 2 | - So GD cuối trang 1 với GD đầu trang 2 | — | - GD cuối trang 1 mới hơn GD đầu trang 2 | Medium | Positive | EP |  |
| TC_LSGD_PT_006 | BR-07, BR-11 | Lịch sử GD – Phân trang | Kiểm tra phân trang khi đang lọc theo loại | PC-05; 1 loại GD có hơn 20 GD | - Lọc theo loại đó<br>- Cuộn đến cuối | — | - Trang sau vẫn chỉ có GD đúng loại đã lọc | Medium | Positive | PW | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_PT_007 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra không tải trùng khi vuốt liên tục ở cuối | PC-05 | - Cuộn đến cuối, vuốt lên liên tục 5 lần trong 2 giây | — | - Chỉ tải thêm 1 trang, không có GD trùng | Low | Negative | EG |  |
| TC_LSGD_PT_008 | BR-08 | Lịch sử GD – Làm mới | Kiểm tra kéo làm mới khi có GD mới | PC-02 | - Nộp tiền vào tiểu khoản 88 trên Flex<br>- Kéo xuống làm mới trên App | 1.000.000 | - GD "+1.000.000 VND" mới hiện ở đầu danh sách | Medium | Positive | UC |  |
| TC_LSGD_PT_009 | BR-08 | Lịch sử GD – Làm mới | Kiểm tra kéo làm mới khi đang lọc | PC-02; đang lọc "Nộp tiền" | - Kéo xuống làm mới | — | - Danh sách vẫn chỉ có GD Nộp tiền | Medium | Positive | UC |  |
| TC_LSGD_PT_010 | BR-08, BR-07 | Lịch sử GD – Làm mới | Kiểm tra kéo làm mới sau khi đã tải trang 2 | PC-05; đã tải trang 2 | - Cuộn lên đầu, kéo xuống làm mới | — | - Danh sách còn 20 GD đầu tiên | Low | Positive | EG | [GIẢ ĐỊNH] – Q-20 |
| TC_LSGD_PT_011 | BR-07, BR-19 | Lịch sử GD – Phân trang | Kiểm tra lỗi khi tải trang tiếp | PC-05; Dev giả lập lỗi khi tải trang 2 | - Cuộn đến cuối trang 1 | — | - Popup "Có lỗi xảy ra. Vui lòng thử lại sau", nút "Đóng"<br>- Đóng popup, 20 GD đã tải vẫn còn | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-21 |
| TC_LSGD_LOAI_001 | BR-09, BR-11, BR-16 | Lịch sử GD – Bộ lọc – Loại giao dịch | Kiểm tra lọc theo Loại giao dịch | PC-03 | - Chọn lần lượt từng loại, mỗi lần bấm "Lưu"<br>- Chọn "Nhận trái tức" + "Mua trái phiếu", bấm "Lưu"<br>- Bấm "Tất cả"; chọn rồi bỏ 1 loại; chọn 1 loại rồi bấm "<" | — | - Mỗi lần chỉ hiện GD đúng loại đã chọn<br>- Chọn 2 loại: hiện GD của cả 2 loại, xếp mới → cũ<br>- "Tất cả" và loại con tự bỏ chọn lẫn nhau; bỏ hết thì về "Tất cả"; bấm "<" không áp dụng | High | Positive | DT |  |
| TC_LSGD_TG_001 | BR-12, BR-13, BR-14, BR-15 | Lịch sử GD – Bộ lọc – Thời gian giao dịch | Kiểm tra lọc theo Thời gian giao dịch | PC-03 | - Chọn khoảng hợp lệ: khoảng ngắn; Từ = Đến; đúng T−6 tháng → T<br>- Chọn khoảng T−6 tháng−1 ngày → T<br>- Bấm ngày T+1; bấm 1 ngày rồi bấm ngày nhỏ hơn; chọn khoảng mới rồi bấm "<" | VD T = 25/11/2026: 01/11–15/11; 24/11–24/11; 25/05–25/11; 24/05–25/11; 26/11 | - Chỉ hiện GD trong khoảng đã chọn; ô ngày hiện đúng khoảng<br>- Báo "Khoảng thời gian tra cứu tối đa 6 tháng", không áp dụng<br>- Không chọn được ngày tương lai; không tạo khoảng Đến < Từ; bấm "<" giữ khoảng cũ | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-02, Q-03, Q-05, Q-06 |
| TC_LSGD_BL_001 | BR-09, BR-10, BR-16 | Lịch sử GD – Bộ lọc – Lưu | Kiểm tra nút Lưu khi lọc kết hợp | PC-03 | - Chọn "Nộp tiền" + 1 khoảng ngày, bấm "Lưu"<br>- Bấm icon lọc để mở lại | — | - Bottom sheet đóng; chỉ hiện GD Nộp tiền trong khoảng đã chọn; icon lọc đổi trạng thái<br>- Mở lại vẫn giữ "Nộp tiền" và khoảng ngày đã chọn | Medium | Positive | UC | [GIẢ ĐỊNH] icon – Q-09 |
| TC_LSGD_BL_002 | BR-09, BR-10, BR-16 | Lịch sử GD – Bộ lọc – Đặt lại | Kiểm tra nút Đặt lại khi đang lọc | PC-02; đang lọc "Nộp tiền" + 1 khoảng ngày | - Mở bộ lọc, bấm "Đặt lại"<br>- Bấm "<"<br>- Mở lại, bấm "Đặt lại", bấm "Lưu" | — | - Bộ lọc về "Tất cả", "T−1 tháng → T"; bottom sheet vẫn mở<br>- Bấm "<": danh sách giữ kết quả lọc cũ<br>- Bấm "Lưu": về danh sách mặc định; icon lọc về bình thường | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-08, Q-09 |
| TC_LSGD_ERR_001 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra popup khi hệ thống trả BND-901 | PC-01; Dev giả lập lỗi BND-901 | - Mở tab "Lịch sử", bấm "Đóng" | — | - Popup "Hệ thống đang bận", nút "Đóng"<br>- Không hiện GD; app không bị thoát | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-32 |
| TC_LSGD_ERR_002 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra popup khi hệ thống trả BND-999 | PC-01; Dev giả lập lỗi BND-999 | - Mở tab "Lịch sử" | — | - Popup "Có lỗi xảy ra. Vui lòng thử lại sau", nút "Đóng"<br>- Không hiện GD | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-32 |
| TC_LSGD_ERR_003 | BR-18, BR-19 | Lịch sử GD – Lỗi | Kiểm tra khi phiên đăng nhập hết hạn lúc mở tab | PC-01; để phiên đăng nhập hết hạn | - Mở tab "Lịch sử", bấm "Đăng nhập" | — | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", nút "Đăng nhập"<br>- Không hiện GD; bấm nút thì về màn Đăng nhập | High | Security | EP | V03; [GIẢ ĐỊNH] – Q-32 |
| TC_LSGD_ERR_004 | BR-18 | Lịch sử GD – Lỗi | Kiểm tra khi phiên hết hạn lúc bấm Lưu bộ lọc | PC-03; để phiên hết hạn khi bộ lọc đang mở | - Chọn 1 loại, bấm "Lưu" | — | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"<br>- Không hiện kết quả lọc | Medium | Security | EP |  |
| TC_LSGD_ERR_005 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra khi mất mạng lúc mở tab | PC-01; tắt Wi-Fi và 4G | - Mở tab "Lịch sử"<br>- Bật lại mạng, kéo làm mới | — | - Mất mạng: popup lỗi chuẩn, app không thoát<br>- Có mạng lại: hiện danh sách | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-27 |
| TC_LSGD_ERR_006 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra khi mất mạng lúc bấm Lưu bộ lọc | PC-03; tắt mạng sau khi mở bộ lọc | - Chọn 1 loại, bấm "Lưu" | — | - Popup lỗi chuẩn<br>- Đóng popup, danh sách cũ giữ nguyên | Low | Negative | EG | [GIẢ ĐỊNH] – Q-27 |
| TC_LSGD_ERR_007 | BR-15, BR-14 | Lịch sử GD – Lỗi | Kiểm tra hệ thống chặn khoảng > 6 tháng khi vượt qua App | PC-03; Dev sửa yêu cầu gửi lên thành khoảng > 6 tháng | - Bấm "Lưu" | — | - Hiện "Khoảng thời gian tra cứu tối đa 6 tháng"<br>- Không hiện GD cũ hơn 6 tháng | Medium | Negative | EG | [GIẢ ĐỊNH] mã lỗi – Q-04; Bổ sung B5 |
| TC_LSGD_ERR_008 | BR-01 | Lịch sử GD – Lỗi | Kiểm tra khi hệ thống phản hồi chậm | PC-01; giả lập mạng chậm 10 giây | - Mở tab "Lịch sử", đợi | — | - Trong lúc chờ có biểu tượng tải<br>- Sau đó hiện danh sách, không bị lặp GD | Low | Negative | EG | [GIẢ ĐỊNH] – Q-27 |
| TC_LSGD_PQ_001 | BR-18 | Lịch sử GD – Phân quyền | Kiểm tra dữ liệu khi đổi tài khoản trên cùng máy | PC-02 (KH A đã xem danh sách) | - Đăng xuất, đăng nhập KH B (không có GD)<br>- Mở tab "Lịch sử" | KH B = tài khoản của PC-06 | - Hiện "Không có giao dịch"<br>- Không còn GD nào của KH A | High | Security | EG | V03 |
| TC_LSGD_PQ_002 | BR-18 | Lịch sử GD – Phân quyền | Kiểm tra không xem được GD KH khác khi sửa yêu cầu | PC-01; Dev sửa yêu cầu gửi lên, thêm mã tài khoản KH khác | - Mở tab "Lịch sử" | — | - Popup lỗi chuẩn hoặc chỉ hiện GD của KH đang đăng nhập<br>- Không hiện GD nào của KH khác | High | Security | EG | [GIẢ ĐỊNH] – Q-33 |
| TC_LSGD_EVT_001 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.LIST khi mở tab | PC-01; mở debug analytics | - Mở tab "Lịch sử" | — | - Ghi nhận 1 event SBOND.HISTORY.LIST | Low | Integration | UC |  |
| TC_LSGD_EVT_002 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.LIST khi kéo làm mới | PC-02; mở debug analytics | - Kéo xuống làm mới | — | - Ghi nhận thêm 1 event SBOND.HISTORY.LIST | Low | Integration | UC |  |
| TC_LSGD_EVT_003 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.LIST khi tải trang tiếp | PC-05; mở debug analytics | - Cuộn đến cuối trang 1 | — | - Ghi nhận 1 event SBOND.HISTORY.LIST | Low | Integration | UC |  |
| TC_LSGD_EVT_004 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.FILTER.OPEN khi mở Bộ lọc | PC-02; mở debug analytics | - Bấm icon lọc | — | - Ghi nhận 1 event SBOND.HISTORY.FILTER.OPEN | Low | Integration | UC |  |
| TC_LSGD_EVT_005 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.FILTER.APPLY khi Lưu bộ lọc | PC-03; mở debug analytics | - Chọn 1 loại, bấm "Lưu" | — | - Ghi nhận 1 event SBOND.HISTORY.FILTER.APPLY | Low | Integration | UC |  |
| TC_LSGD_EVT_006 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.FILTER.RESET khi Đặt lại | PC-03; mở debug analytics | - Bấm "Đặt lại" | — | - Ghi nhận 1 event SBOND.HISTORY.FILTER.RESET; không có FILTER.APPLY | Low | Integration | UC |  |

### 4.2 API O5 (22 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_LSGD_API_001 | BR-23, BR-01 | Lịch sử GD – API O5 | Kiểm tra O5 trả danh sách với khoảng mặc định | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 | — | - HTTP 200; mỗi phần tử có transTime, amount, message<br>- totalCount bằng số GD App hiển thị với bộ lọc mặc định | High | Positive | EP |  |
| TC_LSGD_API_002 | BR-03 | Lịch sử GD – API O5 | Kiểm tra O5 sắp xếp theo transTime giảm dần | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 | — | - transTime phần tử sau ≤ phần tử trước | Medium | Positive | EP |  |
| TC_LSGD_API_003 | BR-02, BR-05 | Lịch sử GD – API O5 | Kiểm tra dấu amount theo loại GD | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 lần lượt với từng transType | — | - INTEREST, SELL, DEPOSIT: amount > 0<br>- BUY, WITHDRAW: amount < 0 | High | Positive | DT |  |
| TC_LSGD_API_004 | BR-11 | Lịch sử GD – API O5 | Kiểm tra O5 lọc khi transType = INTEREST | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1&transType=INTEREST | — | - HTTP 200; chỉ có GD Nhận trái tức | Medium | Positive | EP |  |
| TC_LSGD_API_005 | BR-11 | Lịch sử GD – API O5 | Kiểm tra O5 lọc khi transType nhiều giá trị | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1&transType=INTEREST,BUY | — | - HTTP 200; chỉ có GD Nhận trái tức và Mua trái phiếu | Medium | Positive | PW | [GIẢ ĐỊNH] – Q-12 |
| TC_LSGD_API_006 | BR-11 | Lịch sử GD – API O5 | Kiểm tra O5 khi transType không hợp lệ | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1&transType=ABC | transType = ABC | - HTTP 400, mã lỗi BND-1xx; không trả dữ liệu | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-04 |
| TC_LSGD_API_007 | BR-14 | Lịch sử GD – API O5 | Kiểm tra O5 khi fromDate lớn hơn toDate | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T>&toDate=<T−5 ngày>&page=1 | — | - HTTP 400, mã lỗi BND-1xx; không trả dữ liệu | Medium | Negative | EP | V01; [GIẢ ĐỊNH] – Q-04 |
| TC_LSGD_API_008 | BR-14, BR-17 | Lịch sử GD – API O5 | Kiểm tra O5 khi toDate sau ngày hiện tại | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−5 ngày>&toDate=<T+1>&page=1 | — | - HTTP 400, mã lỗi BND-1xx | Medium | Negative | BVA | [GIẢ ĐỊNH] – Q-04 |
| TC_LSGD_API_009 | BR-15 | Lịch sử GD – API O5 | Kiểm tra O5 khi khoảng ngày đúng 6 tháng | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−6 tháng>&toDate=<T>&page=1 | — | - HTTP 200; trả GD trong khoảng, không có GD trước T−6 tháng | Medium | Boundary | BVA | [GIẢ ĐỊNH] – Q-02 |
| TC_LSGD_API_010 | BR-15 | Lịch sử GD – API O5 | Kiểm tra O5 khi khoảng ngày vượt 6 tháng 1 ngày | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−6 tháng−1 ngày>&toDate=<T>&page=1 | — | - HTTP 400, mã lỗi BND-1xx; không trả dữ liệu | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-02, Q-04 |
| TC_LSGD_API_011 | BR-23 | Lịch sử GD – API O5 | Kiểm tra O5 khi ngày sai định dạng | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=2026-11-01&toDate=2026-11-15&page=1 | yyyy-MM-dd | - HTTP 400 kèm mã lỗi; không trả dữ liệu | Low | Negative | EG | [GIẢ ĐỊNH] – Q-34 |
| TC_LSGD_API_012 | BR-07 | Lịch sử GD – API O5 | Kiểm tra O5 trả trang 2 | PC-API; token KH của PC-05 | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=2 | — | - HTTP 200; 20 phần tử, không trùng trang 1<br>- totalPage, totalCount đúng với dữ liệu | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_API_013 | BR-07 | Lịch sử GD – API O5 | Kiểm tra O5 khi page lớn hơn totalPage | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=<totalPage+1> | — | - HTTP 200; content = [] | Low | Boundary | BVA | [GIẢ ĐỊNH] – Q-34 |
| TC_LSGD_API_014 | BR-07 | Lịch sử GD – API O5 | Kiểm tra O5 khi page = 0 | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=0 | page = 0 | - HTTP 400 kèm mã lỗi | Low | Negative | BVA | [GIẢ ĐỊNH] – Q-34 |
| TC_LSGD_API_015 | BR-18, BR-19 | Lịch sử GD – API O5 | Kiểm tra O5 khi không gửi Authorization | Postman, UAT | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 không có header Authorization | — | - HTTP 401, mã lỗi AUTH_001; không trả dữ liệu | High | Security | EP |  |
| TC_LSGD_API_016 | BR-18, BR-19 | Lịch sử GD – API O5 | Kiểm tra O5 khi token hết hạn | Postman; token đã hết hạn | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 | — | - HTTP 401, mã lỗi AUTH_001 | High | Security | EP |  |
| TC_LSGD_API_017 | BR-06 | Lịch sử GD – API O5 | Kiểm tra O5 khi KH không có giao dịch | Postman; token KH của PC-06 | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 | — | - HTTP 200; totalCount = 0; content = [] | Medium | Negative | EP |  |
| TC_LSGD_API_018 | BR-18 | Lịch sử GD – API O5 | Kiểm tra O5 không trả GD KH khác | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1&accountNo=<mã TK KH khác> | — | - HTTP 403 BND-403, hoặc chỉ trả GD của KH sở hữu token | High | Security | EG | [GIẢ ĐỊNH] – Q-33 |
| TC_LSGD_API_019 | BR-23 | Lịch sử GD – API O5 | Kiểm tra O5 khi transType chứa SQL injection | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1&transType=' OR 1=1 -- | ' OR 1=1 -- | - HTTP 400; không trả dữ liệu, không lộ lỗi hệ thống | Medium | Security | EG |  |
| TC_LSGD_API_020 | BR-05, BR-22 | Lịch sử GD – API O5 | Kiểm tra O5 không trả GD ngoài tiểu khoản 88 và lệnh Rejected | PC-API; TK có GD ở tiểu khoản khác và lệnh Rejected trong 1 tháng | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 | — | - Không có GD của tiểu khoản khác 88<br>- Không có GD của lệnh Rejected | High | Negative | EP | [GIẢ ĐỊNH] – Q-19 |
| TC_LSGD_API_021 | BR-04 | Lịch sử GD – API O5 | Kiểm tra O5 không làm đổi dữ liệu khi gọi lặp lại | PC-API | - Gọi GET /api/v1/bond/app/transactions?fromDate=<T−1 tháng>&toDate=<T>&page=1 2 lần liên tiếp | — | - 2 response giống hệt nhau | Low | Positive | EG | Post-Condition |
| TC_LSGD_API_022 | BR-01, BR-23 | Lịch sử GD – API O5 | Kiểm tra O5 khi không gửi fromDate, toDate | PC-API | - Gọi GET /api/v1/bond/app/transactions?page=1 | — | - HTTP 200; trả GD trong khoảng mặc định T−1 tháng → T | Low | Negative | EG | [GIẢ ĐỊNH] – Q-34; Bổ sung B5 |

## B5. Tự review + RTM
### RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ |
|---|---|---|---|---|
| BR-01 | Mở tab "Lịch sử" → App gọi O5 với bộ lọc mặc định: Loại GD = Tất cả; T… | 9 | TC_LSGD_UI_001, TC_LSGD_NAV_003, TC_LSGD_DS_001, TC_LSGD_DS_002, TC_LSGD_NV_010, TC_LSGD_NV_012, TC_LSGD_ERR_008, TC_LSGD_API_001, TC_LSGD_API_022 | Đủ |
| BR-02 | Card hiển thị: Thời gian GD (HH:mm dd/MM/yyyy – transTime); Số tiền (+… | 17 | TC_LSGD_UI_002, TC_LSGD_UI_006, TC_LSGD_UI_007, TC_LSGD_DS_005, TC_LSGD_DS_006, TC_LSGD_DS_007, TC_LSGD_DS_008, TC_LSGD_DS_009, TC_LSGD_DS_010, TC_LSGD_DS_011, TC_LSGD_DS_012, TC_LSGD_DS_013, TC_LSGD_DS_014, TC_LSGD_DS_015, TC_LSGD_DS_016, TC_LSGD_NV_013, TC_LSGD_API_003 | Đủ |
| BR-03 | Sắp xếp theo thời gian giao dịch mới → cũ | 4 | TC_LSGD_DS_003, TC_LSGD_DS_004, TC_LSGD_PT_005, TC_LSGD_API_002 | Đủ |
| BR-04 | Card chỉ hiển thị, không bấm được; màn không làm đổi dữ liệu | 3 | TC_LSGD_DS_017, TC_LSGD_DS_018, TC_LSGD_API_021 | Đủ |
| BR-05 | Phạm vi dữ liệu: GD tiền gắn với S-Bond của KH – Mua/Bán (lệnh đặt mua… | 16 | TC_LSGD_DS_001, TC_LSGD_DS_002, TC_LSGD_DS_007, TC_LSGD_DS_008, TC_LSGD_DS_009, TC_LSGD_DS_010, TC_LSGD_DS_011, TC_LSGD_NV_001, TC_LSGD_NV_003, TC_LSGD_NV_004, TC_LSGD_NV_005, TC_LSGD_NV_006, TC_LSGD_NV_007, TC_LSGD_NV_008, TC_LSGD_API_003, TC_LSGD_API_020 | Đủ |
| BR-06 | Không có GD (totalCount = 0) → màn trống "Không có giao dịch" (SBOND.H… | 5 | TC_LSGD_UI_003, TC_LSGD_DS_019, TC_LSGD_DS_020, TC_LSGD_DS_021, TC_LSGD_API_017 | Đủ |
| BR-07 | Cuộn đến cuối danh sách → tải trang tiếp (page + 1) đến khi hết totalP… | 12 | TC_LSGD_PT_001, TC_LSGD_PT_002, TC_LSGD_PT_003, TC_LSGD_PT_004, TC_LSGD_PT_005, TC_LSGD_PT_006, TC_LSGD_PT_007, TC_LSGD_PT_010, TC_LSGD_PT_011, TC_LSGD_API_012, TC_LSGD_API_013, TC_LSGD_API_014 | Đủ |
| BR-08 | Kéo xuống để làm mới → gọi lại O5 với bộ lọc hiện tại | 4 | TC_LSGD_NV_003, TC_LSGD_PT_008, TC_LSGD_PT_009, TC_LSGD_PT_010 | Đủ |
| BR-09 | Icon lọc → bottom sheet "Bộ lọc" giữ lựa chọn hiện tại; nút quay lại t… | 4 | TC_LSGD_UI_004, TC_LSGD_LOAI_001, TC_LSGD_BL_001, TC_LSGD_BL_002 | Đủ |
| BR-10 | Icon lọc đổi trạng thái khi đang lọc (Dự thảo BA) | 2 | TC_LSGD_BL_001, TC_LSGD_BL_002 | Đủ |
| BR-11 | Loại GD: chip chọn nhiều – Tất cả / Nhận trái tức (INTEREST) / Mua trá… | 7 | TC_LSGD_UI_004, TC_LSGD_DS_020, TC_LSGD_PT_006, TC_LSGD_LOAI_001, TC_LSGD_API_004, TC_LSGD_API_005, TC_LSGD_API_006 | Đủ |
| BR-12 | Thời gian GD: ô từ → đến (dd/MM/yyyy) + icon lịch; mặc định 1 tháng gầ… | 2 | TC_LSGD_UI_004, TC_LSGD_TG_001 | Đủ |
| BR-13 | Bottom sheet "Thời gian giao dịch": lịch tháng "Tháng M, yyyy", tuần b… | 2 | TC_LSGD_UI_005, TC_LSGD_TG_001 | Đủ |
| BR-14 | V01: Từ ngày ≤ Đến ngày; không chọn ngày sau ngày hiện tại (BE) – lịch… | 5 | TC_LSGD_NV_011, TC_LSGD_TG_001, TC_LSGD_ERR_007, TC_LSGD_API_007, TC_LSGD_API_008 | Đủ |
| BR-15 | V02: khoảng ngày không vượt quá 6 tháng → inline dưới ô ngày "Khoảng t… | 4 | TC_LSGD_TG_001, TC_LSGD_ERR_007, TC_LSGD_API_009, TC_LSGD_API_010 | Đủ |
| BR-16 | Bộ lọc: "Đặt lại" → về mặc định (chưa gọi API); "Lưu" → đóng bottom sh… | 4 | TC_LSGD_DS_021, TC_LSGD_LOAI_001, TC_LSGD_BL_001, TC_LSGD_BL_002 | Đủ |
| BR-17 | BIZ-11: ngày nghiệp vụ, giờ lấy theo Backend (ngày hiện tại của bộ lọc… | 5 | TC_LSGD_NV_010, TC_LSGD_NV_011, TC_LSGD_NV_012, TC_LSGD_NV_013, TC_LSGD_API_008 | Đủ |
| BR-18 | V03: phiên hợp lệ; dữ liệu chỉ của KH đăng nhập; vi phạm → popup lỗi c… | 7 | TC_LSGD_ERR_003, TC_LSGD_ERR_004, TC_LSGD_PQ_001, TC_LSGD_PQ_002, TC_LSGD_API_015, TC_LSGD_API_016, TC_LSGD_API_018 | Đủ |
| BR-19 | Lỗi hệ thống / token (BND-901, BND-999, AUTH_001) → popup lỗi chuẩn SH… | 8 | TC_LSGD_PT_011, TC_LSGD_ERR_001, TC_LSGD_ERR_002, TC_LSGD_ERR_003, TC_LSGD_ERR_005, TC_LSGD_ERR_006, TC_LSGD_API_015, TC_LSGD_API_016 | Đủ |
| BR-20 | Header: tiêu đề "SH Bond" + nút quay lại (về màn trước); icon ⓘ (không… | 3 | TC_LSGD_UI_001, TC_LSGD_NAV_001, TC_LSGD_NAV_002 | Đủ |
| BR-21 | Tracking event: SBOND.HISTORY.LIST (mở / làm mới / tải trang tiếp), FI… | 6 | TC_LSGD_EVT_001, TC_LSGD_EVT_002, TC_LSGD_EVT_003, TC_LSGD_EVT_004, TC_LSGD_EVT_005, TC_LSGD_EVT_006 | Đủ |
| BR-22 | Edge case: lệnh không khớp (Rejected) – giải tỏa tiền → không phát sin… | 3 | TC_LSGD_NV_002, TC_LSGD_NV_009, TC_LSGD_API_020 | Đủ |
| BR-23 | API O5: GET /api/v1/bond/app/transactions – dùng khi mở tab, Lưu bộ lọ… | 4 | TC_LSGD_API_001, TC_LSGD_API_011, TC_LSGD_API_019, TC_LSGD_API_022 | Đủ |

### Checklist
| Tiêu chí | Kết quả | Ghi chú |
|---|---|---|
| A. 100% BR có TC | Đạt | 23/23 BR |
| A. Luồng chính + phụ + ngoại lệ | Đạt | DS/PT/BL + A2 màn trống + ERR/PQ |
| A. Decision table mỗi rule có TC; ST có chuyển trạng thái không hợp lệ | Đạt | DT dấu tiền R1–R5 (DS_007–011); DT chip trong LOAI_001; ST lịch trong TG_001 |
| A. Phân quyền | Đạt | 1 role KH → token, đổi tài khoản, IDOR app + API |
| A. Error guessing | Đạt | Mạng, chậm, double-tap, HTML/null, giờ thiết bị, SQLi |
| B. Đủ 12 cột, ID đúng format, không trùng | Đạt | 97 ID duy nhất (kiểm bằng script) |
| B. 1 TC = 1 mục tiêu | Đạt một phần | Phần lọc: mỗi trường 1 TC tổng quan (LOAI_001, TG_001, BL_001 Lưu, BL_002 Đặt lại) theo yêu cầu; nhóm khác 1 TC = 1 mục tiêu |
| B. Title "Kiểm tra … khi …" | Đạt | — |
| B. Test data cụ thể | Đạt | Không phụ thuộc dữ liệu cố định: điều kiện trong Precondition, ngày theo T |
| B. Expected đo được | Đạt | Text, màu, dấu, thứ tự, trạng thái nút |
| B. Negative kiểm cả báo lỗi + không thực hiện | Đạt | TG_001, ERR_*, API_* có "không gọi O5 / không trả data / danh sách giữ" |
| B. Message nguyên văn / [GIẢ ĐỊNH] | Đạt | I01, E01, 3 popup lấy từ Mục 6; mapping mã → text [GĐ] Q-32 |
| C. Priority hợp lý | Đạt | Dấu tiền, phạm vi dữ liệu, bảo mật = High |
| C. Thứ tự UI → Positive → … → Permission | Đạt | Theo nhóm màn; trong nhóm theo thứ tự quy tắc |

### Sửa/bổ sung trong vòng review
- v4.1 (25/11/2026): tách nút bộ lọc thành 2 case – BL_001 (Lưu), BL_002 (Đặt lại).
- v4 (25/11/2026): toàn bộ TC không còn phụ thuộc sheet dữ liệu test – bỏ mã G01…/KH01…; điều kiện dữ liệu ghi trong Precondition; ngày tính theo T (ngày nghiệp vụ); Expected viết theo quy tắc chung.
- v3.2 (25/11/2026): Steps và Expected của mọi TC tối đa 3 gạch đầu dòng (rút gọn UI_004, UI_005, DS_002, LOAI_001, TG_001, BL_001).
- v3.1 (25/11/2026): tách phần lọc theo trường – LOAI_001 (Loại GD), TG_001 (Thời gian GD), BL_001 (Đặt lại / Lưu, lọc kết hợp); mỗi case ngắn gọn, bước và Expected đánh số tương ứng.
- v3 (25/11/2026): gộp phần lọc (LOAI_001–012, TG_001–018, BL_001–011 = 41 TC) thành 1 TC tổng quan TC_LSGD_BL_001 theo yêu cầu. Các trường hợp biên chi tiết của lịch (Từ = Đến, Đến < Từ, chọn 1 ngày, chuyển tháng) không còn TC riêng.
- v2.1 (25/11/2026): nhóm Danh sách / Màn trống rút gọn tiêu đề, bước và Expected; bỏ lặp nội dung giữa các TC (DS_003, DS_021 tham chiếu DS_002).
- v2 (25/11/2026): nhóm Danh sách / Màn trống (DS_001–DS_021) bỏ toàn bộ bước và kết quả kiểm API; Expected viết bằng tiếng Việt thường, liệt kê cụ thể từng giao dịch mong đợi. DS_001 đổi thành kiểm biên 1 tháng trên giao diện. Phần kiểm API giữ ở nhóm API O5.
- Tách hiển thị số tiền thành 5 TC theo từng loại GD (DS_007–DS_011) thay vì 1 TC chung, để mỗi rule của decision table có 1 TC.
- Bổ sung biên mặc định 1 tháng (DS_001) và ngày nghiệp vụ là ngày 31 (NV_012).
- Bổ sung kiểm ngày theo BE khi giờ/múi giờ thiết bị sai (NV_010, NV_011, NV_013) – BIZ-11 trước đó chưa có TC riêng.
- Bổ sung BE chặn khoảng > 6 tháng khi bypass FE (ERR_007, API_010) và IDOR (PQ_002, API_018).
- Bổ sung TC_LSGD_DS_021 (thoát màn trống khi đổi bộ lọc).

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 97 (App 75 · API 22) | — |
| High / Medium / Low | 25 (26%) / 42 (43%) / 30 (31%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 30 : 67 (31% : 69%) | ≈ 30 : 70 |
| Theo Type | Positive 30 · Negative 27 · Boundary 12 · Security 9 · Integration 12 · UI 7 | — |
| Requirement coverage | 23/23 = 100% | 100% |
| Q&A mở | 36 (Blocker 0 · Major 14 · Minor 22) | Blocker = 0 |
| TC có [GIẢ ĐỊNH] | 44 | — |
| TC bổ sung ở B5 | 7 (TC_LSGD_DS_021, TC_LSGD_NV_010, TC_LSGD_NV_011, TC_LSGD_NV_012, TC_LSGD_NV_013, TC_LSGD_ERR_007, TC_LSGD_API_022) | — |

**Lưu ý chất lượng:** High 26% hơi dưới mục tiêu vì phần lớn TC lọc/hiển thị là Medium theo định nghĩa file 02; mọi TC về dấu tiền, phạm vi dữ liệu và bảo mật đều High. 44 TC dựa trên giả định – ưu tiên chốt các Q&A Major (Q-01, Q-02, Q-03, Q-13, Q-16) trước khi chạy.
