# SBOND.HISTORY.APP.01 – Lịch sử giao dịch (App) – Test design B1→B5 (v3)

Nguồn: SRS SBOND.HISTORY.APP.01 – Lịch sử giao dịch (export Confluence 15/11/2026) + ảnh Figma/flow trong SRS  
Người viết: Nguyễn Sỹ Hà · Ngày: 25/11/2026 · Lệnh: /FULL  
Phiên bản: v3 – phần lọc gộp thành 1 case tổng quan (TC_LSGD_BL_001); nhóm Danh sách chỉ kiểm giao diện, rút gọn (xem B5 > Sửa/bổ sung).  
Ký hiệu: [GIẢ ĐỊNH] = giả định để đi tiếp, xem Q-xx; G01… = mã dữ liệu test; PC-xx / SOP-xx = precondition / thao tác dùng chung.

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
| Q-02 | "Tối đa 6 tháng" tính biên thế nào: 25/05/2026 → 25/11/2026 hợp lệ? 24/05/2026 → 25/11/2026 vi phạm? | Biên V02 | Major | So theo tháng lịch: fromDate ≥ toDate − 6 tháng thì hợp lệ (25/05 hợp lệ, 24/05 vi phạm) | TC_LSGD_BL_001, TC_LSGD_API_009, TC_LSGD_API_010 |
| Q-03 | V02 kiểm ở đâu? Lịch cho chọn khoảng > 6 tháng rồi báo inline ở Bộ lọc, hay chặn ngay trên lịch? Khi đang lỗi, nút "Lưu" của Bộ lọc disable hay bấm được nhưng không áp dụng? | SRS ghi "Inline dưới ô ngày" + error code O5 – chưa rõ FE hay BE chặn | Major | Lịch cho chọn; về Bộ lọc hiển thị E01 dưới ô ngày; bấm "Lưu" không đóng bottom sheet, không gọi O5 | TC_LSGD_BL_001 |
| Q-04 | Mã BND-1xx cụ thể cho V01/V02 là gì? HTTP status? BE có chặn toDate > ngày hiện tại không? | Cần để viết Expected API | Major | HTTP 400 + error code dạng BND-1xx; BE chặn cả khoảng > 6 tháng, fromDate > toDate, toDate tương lai | TC_LSGD_ERR_007, TC_LSGD_API_006, TC_LSGD_API_007, TC_LSGD_API_008, TC_LSGD_API_010 |
| Q-05 | Trên lịch, đã chọn Từ ngày rồi bấm 1 ngày nhỏ hơn: lịch disable ngày đó, đổi chỗ 2 ngày, hay coi là Từ ngày mới? | V01 chỉ ghi "Lịch chặn chọn ngày không hợp lệ" | Major | Ngày nhỏ hơn được coi là Từ ngày mới (chọn lại từ đầu); không bao giờ tạo khoảng Từ > Đến | TC_LSGD_BL_001 |
| Q-06 | Chọn Từ = Đến (1 ngày) thao tác thế nào: bấm cùng 1 ngày 2 lần? | V01 cho phép "≤" | Minor | Bấm cùng 1 ngày 2 lần → khoảng 1 ngày, hợp lệ | TC_LSGD_BL_001 |
| Q-07 | Mới chọn 1 ngày (hoặc vừa "Đặt lại" trên lịch) rồi bấm "Lưu" thì sao? | "Đặt lại → bỏ chọn" để lịch trống | Major | "Lưu" disable đến khi chọn đủ 2 ngày | TC_LSGD_BL_001 |
| Q-08 | Bộ lọc: bấm "Đặt lại" rồi bấm quay lại (không Lưu) → danh sách giữ bộ lọc cũ? | "Đặt lại (chưa gọi API)" + "quay lại không áp dụng" | Minor | Giữ bộ lọc cũ; chỉ "Lưu" mới áp dụng | TC_LSGD_BL_001 |
| Q-09 | Icon lọc "đang lọc" trông thế nào (Dự thảo BA)? Điều kiện: bộ lọc khác mặc định? | Chưa có Figma trạng thái | Minor | Bộ lọc khác mặc định → icon đổi sang trạng thái đang lọc (vd chấm/cam) | TC_LSGD_BL_001 |
| Q-10 | Icon ⓘ (Luồng A1) hiển thị nội dung gì, dạng popup hay màn? | Cột "Xử lý" trống | Minor | Chưa test được; TC để Blocked | TC_LSGD_NAV_002 |
| Q-11 | Hủy GD sau xác nhận thanh toán – revert tiền: BE có ghi nhận GD hoàn tiền không, thuộc loại GD nào, dấu gì? | Mục 8 còn dấu "?" | Major | Có GD hoàn tiền, số tiền dương xanh; GD mua gốc vẫn giữ | TC_LSGD_NV_009 |
| Q-12 | transType chọn nhiều gửi thế nào (BUY,SELL hay lặp tham số)? "Tất cả" gửi gì (không gửi / ALL)? | Không có request mẫu | Major | Nhiều giá trị nối bằng dấu phẩy; "Tất cả" = không gửi transType | TC_LSGD_BL_001, TC_LSGD_API_005 |
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
| Q-23 | Thay đổi trong Bộ lọc, bấm quay lại, mở lại: hiển thị thay đổi chưa lưu hay bộ lọc đang áp dụng? | "Giữ lựa chọn hiện tại" có 2 cách hiểu | Minor | Hiển thị bộ lọc đang áp dụng | TC_LSGD_BL_001 |
| Q-24 | Lịch có giới hạn ngày quá khứ xa nhất không? | Chưa quy định | Minor | Không giới hạn, chỉ ràng buộc 6 tháng | — |
| Q-25 | Mở lịch: hiển thị tháng nào? Khoảng đang áp dụng có được tô sẵn? | Chưa quy định | Minor | Tháng của Đến ngày; khoảng đang áp dụng được tô sẵn | TC_LSGD_UI_005, TC_LSGD_BL_001 |
| Q-26 | SRS tham chiếu "Q24", "Q25" (log Q&A của BA) nhưng không có nội dung trong tài liệu. | Thiếu ngữ cảnh quyết định | Minor | Coi như đã chốt theo nội dung SRS hiện tại | — |
| Q-27 | Mất mạng / API chậm: popup gì, có loading không? | Mục 6 chỉ có lỗi hệ thống và token | Minor | Có loading; mất mạng → popup lỗi chuẩn SHSmart | TC_LSGD_ERR_005, TC_LSGD_ERR_006, TC_LSGD_ERR_008 |
| Q-28 | transTime trả theo múi giờ nào? App có quy đổi theo múi giờ thiết bị không? | BIZ-11: giờ lấy theo BE | Minor | Luôn hiển thị giờ Việt Nam, không quy đổi theo thiết bị | TC_LSGD_NV_013 |
| Q-29 | Nội dung giao dịch dài: xuống dòng hay cắt "..."? | Chưa quy định | Minor | Xuống dòng, không cắt | TC_LSGD_UI_007 |
| Q-30 | Chọn đủ 5 chip con có tự chuyển về "Tất cả" không? | BIZ-33 không nêu | Minor | Không tự chuyển; kết quả bằng Tất cả | TC_LSGD_BL_001 |
| Q-31 | Rời tab Lịch sử rồi quay lại: bộ lọc về mặc định? | Luồng B1 ghi mở tab gọi O5 mặc định | Minor | Về mặc định | TC_LSGD_BL_001 |
| Q-32 | Popup lỗi chuẩn: mã nào ứng với nội dung nào (BND-901 / BND-999 / AUTH_001)? | Mục 6 #3 gộp 3 mã, 3 nội dung | Minor | BND-901 = "Hệ thống đang bận"; BND-999 = "Có lỗi xảy ra. Vui lòng thử lại sau"; AUTH_001 = "Phiên đăng nhập không hợp lệ hoặc đã hết hạn" | TC_LSGD_ERR_001, TC_LSGD_ERR_002, TC_LSGD_ERR_003 |
| Q-33 | O5 xác định KH bằng token hay có tham số định danh (account/custodyCd)? Nếu có tham số, BE có kiểm khớp token? | Cần để test IDOR (V03 BND-403) | Major | Xác định theo token; tham số định danh lạ bị bỏ qua hoặc trả BND-403 | TC_LSGD_PQ_002, TC_LSGD_API_018 |
| Q-34 | O5 khi thiếu fromDate/toDate, sai format ngày, page ≤ 0, page > totalPage trả gì? | Chưa có đặc tả lỗi tham số | Minor | Thiếu ngày → BE dùng mặc định 1 tháng; sai format / page ≤ 0 → 400; page > totalPage → content rỗng | TC_LSGD_API_011, TC_LSGD_API_013, TC_LSGD_API_014, TC_LSGD_API_022 |
| Q-35 | Đang ở tháng hiện tại, mũi tên tháng sau: disable hay cho sang tháng sau (toàn ngày bị chặn)? | Chưa quy định | Minor | Cho sang tháng sau, mọi ngày disable | TC_LSGD_BL_001 |
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
| BR-09 | UC | EG | Medium | 2 / 0 |
| BR-10 | EP | — | Low | 1 / 0 |
| BR-11 | DT | EP, PW, EG | Medium | 4 / 3 |
| BR-12 | UC | EG | Medium | 2 / 0 |
| BR-13 | ST | EG | Medium | 2 / 0 |
| BR-14 | BVA | EG | Medium | 3 / 2 |
| BR-15 | BVA | EP | High | 2 / 2 |
| BR-16 | UC | DT, EG | Medium | 2 / 0 |
| BR-17 | EG | BVA | Medium | 4 / 1 |
| BR-18 | EG | — | High | 4 / 3 |
| BR-19 | EP | EG | Medium | 6 / 2 |
| BR-20 | UC | — | Low | 3 / 0 |
| BR-21 | UC | — | Low | 6 / 0 |
| BR-22 | EG | DT | High | 2 / 1 |
| BR-23 | EP | BVA, EG | High | 0 / 4 |

- **Môi trường:** UAT; iOS + Android (1 máy màn hẹp 360dp); Proxyman/Charles (xem, sửa, mock request/response); Postman; công cụ debug analytics; tài khoản BO có quyền make/check lệnh trên SHBond Portal; quyền tạo bút toán nộp/rút, trái tức trên Flex UAT; khả năng đặt ngày nghiệp vụ BE (cho NV_012).
- **Exit criteria:** 100% TC High đã chạy; 0 bug Critical/Major mở; Q&A Major có trả lời hoặc PO chấp nhận giả định; RTM 100% BR có TC Pass.

### Dữ liệu test
| Mã DL | KH | Tiểu khoản | Loại (transType) | transTime | amount (VND) | message | Dùng cho |
|---|---|---|---|---|---|---|---|
| G01 | KH01 | 88 | INTEREST | 25/11/2026 08:15 | +34.731.400 | Lãi trái phiếu TPQ12205 chốt ngày 20/11/2026 | Card mới nhất; tiền vào; nội dung nguyên văn |
| G02 | KH01 | 88 | BUY | 24/11/2026 14:30 | -134.731.400 | Trả tiền mua mã TPQ12205 ngày 24/11/2026 | Tiền ra; lọc Mua; Từ = Đến |
| G15 | KH01 | 88 | INTEREST | 20/11/2026 15:10 | +5.000.000 | Lãi trái phiếu TPQ11801 chốt ngày 18/11/2026 | Cùng ngày với G03, giờ muộn hơn |
| G03 | KH01 | 88 | DEPOSIT | 20/11/2026 09:00 | +50.000.000 | Nộp tiền vào tài khoản 069C100001-88 | Nộp tiền |
| G04 | KH01 | 88 | WITHDRAW | 15/11/2026 10:05 | -20.000.000 | Chuyển tiền tới 069C100001-66 (Online) | Rút tiền |
| G05 | KH01 | 88 | SELL | 10/11/2026 16:45 | +1.021.111.000 | Nhận tiền bán mã TPQ11801 ngày 10/11/2026 theo hợp đồng 20250510BGB0000000000011 – thanh lý trước hạn | Bán; giờ chiều; nội dung dài |
| G10 | KH01 | 88 | SELL | 05/11/2026 11:20 | +12.345.678.900 | Nhận tiền bán mã TPQ20001 ngày 05/11/2026 | Số tiền 11 chữ số; ngày 1 chữ số |
| G06 | KH01 | 88 | INTEREST | 26/10/2026 09:00 | +1.000.000 | Lãi trái phiếu TPQ12205 chốt ngày 25/10/2026 | Gần biên dưới mặc định |
| G07 | KH01 | 88 | DEPOSIT | 25/10/2026 00:01 | +500 | Nộp tiền vào tài khoản 069C100001-88 | Biên dưới mặc định; tiền < 1.000; giờ 00:01 |
| G08 | KH01 | 88 | BUY | 24/10/2026 23:59 | -500.000 | Trả tiền mua mã TPQ12205 ngày 24/10/2026 | Ngoài mặc định 1 ngày |
| G11 | KH01 | 88 | INTEREST | 25/05/2026 10:00 | +2.000.000 | Lãi trái phiếu TPQ11801 chốt ngày 25/05/2026 | Biên đúng 6 tháng |
| G12 | KH01 | 88 | DEPOSIT | 24/05/2026 10:00 | +3.000.000 | Nộp tiền vào tài khoản 069C100001-88 | Ngoài 6 tháng |
| G13 | KH01 | 01 | DEPOSIT | 18/11/2026 10:00 | +7.000.000 | Nộp tiền vào tài khoản 069C100001-01 | Không thuộc sub88 → không hiển thị |
| G14 | KH01 | 88 | (lệnh mua TPQ12206) | 19/11/2026 (Rejected) | -200.000.000 (giải tỏa) | — | Lệnh Rejected → không phát sinh GD |
| KH02 | KH02 | 88 | — | — | — | — | Không có GD → màn trống; đổi tài khoản |
| KH03 | KH03 | 88 | 25 INTEREST + 20 DEPOSIT | 45 GD trong 25/10/2026 – 25/11/2026, mỗi GD cách nhau ≥ 1 phút | — | — | Phân trang 20/20/5 |
| G20 | KH04 | 88 | DEPOSIT | 21/11/2026 09:30 | +9.999.000 | Nộp tiền vào tài khoản 069C100004-88 | IDOR – GD của KH khác |

### Precondition / thao tác dùng chung
| Mã | Nội dung |
|---|---|
| PC-01 | KH01 đã đăng nhập SHSmart App (UAT), đang ở S-Bond (thanh tab dưới hiển thị); ngày nghiệp vụ BE = 25/11/2026; dữ liệu theo sheet "Dữ liệu test" |
| PC-02 | PC-01 + đang ở tab "Lịch sử", bộ lọc mặc định, đã hiển thị 9 card (G01, G02, G15, G03, G04, G05, G10, G06, G07) |
| PC-03 | PC-02 + đã bấm icon lọc, bottom sheet "Bộ lọc" đang mở |
| PC-04 | PC-03 + đã bấm ô Thời gian giao dịch, bottom sheet "Thời gian giao dịch" đang mở |
| PC-05 | KH03 đã đăng nhập (UAT), đang ở tab "Lịch sử", bộ lọc mặc định, ngày BE 25/11/2026 |
| PC-API | Postman, UAT; Header Authorization: Bearer <token KH01 hợp lệ>; endpoint O5 = GET /api/v1/bond/app/transactions |
| SOP-01 | Chọn khoảng ngày X → Y: ở bottom sheet "Thời gian giao dịch" bấm ngày X → bấm ngày Y → bấm "Lưu" (về Bộ lọc) → bấm "Lưu" (áp dụng) |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên |
|---|---|---|---|
| Khoảng ngày (V02) | 01/11–15/11/2026 (15 ngày); 25/10–25/11 (mặc định) | 01/01–25/11/2026 (~11 tháng) | 25/05–25/11 (đúng 6 tháng, hợp lệ); 24/05–25/11 (vượt 1 ngày) |
| Từ / Đến (V01) | Từ < Đến; Từ = Đến (24/11) | Đến < Từ (15/11 → 10/11); ngày > hôm nay (26/11) | 25/11 (hôm nay, chọn được); 26/11 (bị chặn) |
| Mặc định 1 tháng | 25/10 – 25/11/2026 | — | G07 25/10 00:01 (trong); G08 24/10 23:59 (ngoài); BE 31/03 → 28/02 |
| Loại GD | 1 chip; 2 chip; 5 chip; Tất cả | transType "ABC"; SQLi | Bỏ chip cuối → Tất cả |
| Số tiền | +34.731.400; -134.731.400 | message chứa HTML; null | +500 (< 1.000); +12.345.678.900 (11 chữ số) |
| Thời gian GD | 16:45 10/11/2026 (24h) | Giờ thiết bị / múi giờ khác | 00:01; 05/11 (ngày 1 chữ số) |
| Phân trang (giả định 20/trang) | page 1, 2 | page 0; -1 | page 3 (5 bản ghi – trang cuối); page 4 (> totalPage) |

### Decision table – dấu / màu số tiền theo loại GD (BIZ-32)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Loại GD | Nhận trái tức | Mua trái phiếu | Bán trái phiếu | Nộp tiền | Rút tiền |
| Chiều tiền | Vào | Ra | Vào | Vào | Ra |
| → Dấu / màu | + xanh | - đỏ | + xanh | + xanh | - đỏ |
| TC | DS_007 (G01) | DS_008 (G02) | DS_009 (G05) | DS_010 (G03) | DS_011 (G04) |

### Decision table – chip Loại giao dịch (BIZ-33)
| Trạng thái hiện tại / Hành động | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Đang chọn | Tất cả | Tất cả | Trái tức + Mua | Chỉ Trái tức | Trái tức + Mua |
| Hành động | Bấm 1 chip con | Bấm "Tất cả" | Bấm "Tất cả" | Bỏ chip Trái tức | Bỏ chip Mua |
| → Kết quả | Chip con chọn, Tất cả bỏ | Tất cả vẫn chọn | Chỉ Tất cả chọn | Tất cả tự chọn | Chỉ Trái tức chọn |
| TC | BL_001 bước 3 | BL_001 bước 1 | BL_001 bước 5 | BL_001 bước 4–5 | BL_001 bước 4 |

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
### 4.1 App (72 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_LSGD_UI_001 | BR-20, BR-01 | Lịch sử GD – Layout | Kiểm tra layout mặc định màn Lịch sử khi KH có giao dịch | PC-02 | 1. Quan sát màn hình | KH01 | - Header: nút "<" bên trái, tiêu đề "SH Bond" ở giữa, icon lọc và icon ⓘ bên phải<br>- Giữa màn: cụm card giao dịch<br>- Thanh tab dưới 4 tab: DS chào bán \| Nắm giữ \| Xác nhận \| Lịch sử; tab "Lịch sử" active (icon + chữ cam) | Low | UI | EG | Đối chiếu Figma màn 1 |
| TC_LSGD_UI_002 | BR-02 | Lịch sử GD – Layout | Kiểm tra bố cục 1 card giao dịch khi danh sách có dữ liệu | PC-02 | 1. Quan sát card đầu tiên | KH01 – G01 | - Dòng trên: Thời gian GD bên trái (chữ xám), Số tiền bên phải<br>- Đường kẻ phân cách<br>- Dòng dưới: Nội dung giao dịch căn trái | Low | UI | EG | Figma màn 1 |
| TC_LSGD_UI_003 | BR-06 | Lịch sử GD – Layout | Kiểm tra layout màn trống khi KH không có giao dịch | KH02 đã đăng nhập, ngày BE 25/11/2026 | 1. Vào S-Bond > tab "Lịch sử" | KH02 | - Hình minh họa hộp rỗng + text "Không có giao dịch" ở giữa màn<br>- Không có card nào<br>- Header vẫn có icon lọc, icon ⓘ; thanh tab dưới vẫn hiển thị | Low | UI | EG | Figma màn 2 |
| TC_LSGD_UI_004 | BR-09, BR-11, BR-12 | Lịch sử GD – Bộ lọc | Kiểm tra layout mặc định bottom sheet Bộ lọc khi mở lần đầu | PC-02 | 1. Bấm icon lọc | KH01 | - Bottom sheet trượt lên; danh sách phía sau bị làm mờ<br>- Header: "<" + "Bộ lọc"<br>- Nhãn "Loại giao dịch"; 6 chip theo thứ tự: Tất cả, Nhận trái tức, Mua trái phiếu, Bán trái phiếu, Nộp tiền, Rút tiền; chỉ "Tất cả" đang chọn (nền cam)<br>- Nhãn "Thời gian giao dịch"; ô "25/10/2026 → 25/11/2026" + icon lịch<br>- Nút "Đặt lại" (viền) và "Lưu" (nền cam) | Low | UI | EG | Figma lệch SRS (chính tả nhãn, chip, khoảng 12 tháng) – Q-15; [GIẢ ĐỊNH] 1 tháng – Q-01 |
| TC_LSGD_UI_005 | BR-13 | Lịch sử GD – Lịch | Kiểm tra layout bottom sheet Thời gian giao dịch khi mở từ Bộ lọc mặc định | PC-03 | 1. Bấm ô Thời gian giao dịch | KH01 | - Header: "<" + "Thời gian giao dịch"<br>- Tiêu đề "Tháng 11, 2026" + mũi tên tháng trước / sau<br>- Hàng thứ bắt đầu bằng T2<br>- Dòng "Ngày chọn: 25/10/2026 - 25/11/2026"<br>- Nút "Đặt lại" và "Lưu" | Low | UI | EG | [GIẢ ĐỊNH] mở tháng của Đến ngày – Q-25; SRS chưa có Figma màn lịch |
| TC_LSGD_UI_006 | BR-02 | Lịch sử GD – Layout | Kiểm tra hiển thị số tiền 11 chữ số khi dùng thiết bị màn hẹp | PC-02; Android màn 360dp | 1. Cuộn tới card G10 | KH01 – G10 (+12.345.678.900) | - "+12.345.678.900 VND" hiển thị đủ trên 1 dòng, không bị cắt, không chồng lên Thời gian GD | Low | UI | EG |  |
| TC_LSGD_UI_007 | BR-02 | Lịch sử GD – Layout | Kiểm tra hiển thị nội dung giao dịch khi message dài hơn 1 dòng | PC-02 | 1. Cuộn tới card G05 | KH01 – G05 (message 120 ký tự) | - Nội dung hiển thị đủ, tự xuống dòng; card giãn chiều cao; không bị cắt bằng "..." | Low | UI | EG | [GIẢ ĐỊNH] xuống dòng – Q-29 |
| TC_LSGD_NAV_001 | BR-20 | Lịch sử GD – Header | Kiểm tra nút quay lại trên header khi đang ở tab Lịch sử | PC-02 | 1. Bấm "<" trên header | KH01 | - Rời S-Bond, về màn "Sản phẩm đầu tư" | Low | Positive | UC | [GIẢ ĐỊNH] màn trước – Q-22 |
| TC_LSGD_NAV_002 | BR-20 | Lịch sử GD – Header | Kiểm tra bấm icon ⓘ trên header | PC-02 | 1. Bấm icon ⓘ | KH01 | - Mở nội dung thông tin của màn Lịch sử<br>- Đóng nội dung → danh sách giữ nguyên 9 card | Low | Positive | UC | Luồng A1 chưa có nội dung – Q-10; để Blocked đến khi BA bổ sung |
| TC_LSGD_NAV_003 | BR-01 | Lịch sử GD – Header | Kiểm tra vào tab Lịch sử từ thanh tab dưới | PC-01; đang ở tab "DS chào bán" | 1. Bấm tab "Lịch sử" | KH01 | - Tab "Lịch sử" active<br>- Hiển thị 9 card, card đầu là G01 (08:15 25/11/2026) | Medium | Positive | UC |  |
| TC_LSGD_DS_001 | BR-01, BR-05 | Lịch sử GD – Danh sách | Kiểm tra biên 1 tháng khi mở danh sách mặc định | PC-01 | 1. Bấm tab "Lịch sử"<br>2. Cuộn xuống cuối | G07, G08 | - Giao dịch cuối: "00:01 25/10/2026", +500 VND<br>- Không có giao dịch ngày 24/10/2026 | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-01 |
| TC_LSGD_DS_002 | BR-01, BR-05 | Lịch sử GD – Danh sách | Kiểm tra các giao dịch hiển thị khi mở danh sách mặc định | PC-01 | 1. Bấm tab "Lịch sử"<br>2. Cuộn hết danh sách | KH01 | - Có đúng 9 giao dịch:<br>08:15 25/11/2026 · +34.731.400 VND<br>14:30 24/11/2026 · -134.731.400 VND<br>15:10 20/11/2026 · +5.000.000 VND<br>09:00 20/11/2026 · +50.000.000 VND<br>10:05 15/11/2026 · -20.000.000 VND<br>16:45 10/11/2026 · +1.021.111.000 VND<br>11:20 05/11/2026 · +12.345.678.900 VND<br>09:00 26/10/2026 · +1.000.000 VND<br>00:01 25/10/2026 · +500 VND | High | Positive | EP | [GIẢ ĐỊNH] – Q-01 |
| TC_LSGD_DS_003 | BR-03 | Lịch sử GD – Danh sách | Kiểm tra thứ tự giao dịch khi có nhiều ngày | PC-02 | 1. Xem thời gian từ trên xuống | KH01 | - Xếp từ mới đến cũ, đúng thứ tự như TC_LSGD_DS_002 | Medium | Positive | EP |  |
| TC_LSGD_DS_004 | BR-03 | Lịch sử GD – Danh sách | Kiểm tra thứ tự khi 2 giao dịch cùng ngày | PC-02 | 1. Xem 2 giao dịch ngày 20/11/2026 | G15, G03 | - 15:10 nằm trên 09:00 | Medium | Boundary | BVA |  |
| TC_LSGD_DS_005 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra định dạng thời gian khi giờ chiều | PC-02 | 1. Xem giao dịch ngày 10/11/2026 | G05 | - Hiển thị "16:45 10/11/2026" (giờ 24h, không có AM/PM) | Medium | Positive | EP |  |
| TC_LSGD_DS_006 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra định dạng thời gian khi có số 1 chữ số | PC-02 | 1. Xem giao dịch ngày 25/10/2026<br>2. Xem giao dịch ngày 05/11/2026 | G07, G10 | - Hiển thị "00:01 25/10/2026" và "11:20 05/11/2026" (đủ 2 chữ số) | Low | Boundary | BVA |  |
| TC_LSGD_DS_007 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Nhận trái tức | PC-02 | 1. Xem giao dịch 08:15 25/11/2026 | G01 | - "+34.731.400 VND", màu xanh | High | Positive | DT |  |
| TC_LSGD_DS_008 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Mua trái phiếu | PC-02 | 1. Xem giao dịch 14:30 24/11/2026 | G02 | - "-134.731.400 VND", màu đỏ | High | Positive | DT |  |
| TC_LSGD_DS_009 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Bán trái phiếu | PC-02 | 1. Xem giao dịch 16:45 10/11/2026 | G05 | - "+1.021.111.000 VND", màu xanh | High | Positive | DT |  |
| TC_LSGD_DS_010 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Nộp tiền | PC-02 | 1. Xem giao dịch 09:00 20/11/2026 | G03 | - "+50.000.000 VND", màu xanh | High | Positive | DT |  |
| TC_LSGD_DS_011 | BR-02, BR-05 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi Rút tiền | PC-02 | 1. Xem giao dịch 10:05 15/11/2026 | G04 | - "-20.000.000 VND", màu đỏ | High | Negative | DT | Figma để dấu +, màu xanh – Q-13 |
| TC_LSGD_DS_012 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi nhỏ hơn 1.000 | PC-02 | 1. Xem giao dịch 00:01 25/10/2026 | G07 | - "+500 VND", không có dấu chấm | Low | Boundary | BVA |  |
| TC_LSGD_DS_013 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra số tiền khi có 11 chữ số | PC-02 | 1. Xem giao dịch 11:20 05/11/2026 | G10 | - "+12.345.678.900 VND" | Medium | Boundary | BVA |  |
| TC_LSGD_DS_014 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra nội dung giao dịch hiển thị nguyên văn | PC-02 | 1. Xem nội dung 3 giao dịch G01, G02, G04 | G01, G02, G04 | - G01: "Lãi trái phiếu TPQ12205 chốt ngày 20/11/2026"<br>- G02: "Trả tiền mua mã TPQ12205 ngày 24/11/2026"<br>- G04: "Chuyển tiền tới 069C100001-66 (Online)" | Medium | Positive | EP |  |
| TC_LSGD_DS_015 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra nội dung khi có ký tự đặc biệt | PC-01; Dev đổi nội dung G01 trên UAT | 1. Mở tab "Lịch sử"<br>2. Xem giao dịch G01 | <b>Test</b> & "QA" 😀 | - Hiện đúng nguyên chuỗi, chữ không in đậm<br>- App không bị thoát | Medium | Security | EG |  |
| TC_LSGD_DS_016 | BR-02 | Lịch sử GD – Danh sách | Kiểm tra giao dịch khi không có nội dung | PC-01; Dev xóa nội dung G03 trên UAT | 1. Mở tab "Lịch sử"<br>2. Xem giao dịch G03 | G03 | - Vẫn hiện thời gian và số tiền<br>- Dòng nội dung trống, không hiện "null" | Low | Negative | EG | [GIẢ ĐỊNH] – Q-14 |
| TC_LSGD_DS_017 | BR-04 | Lịch sử GD – Danh sách | Kiểm tra bấm vào giao dịch | PC-02 | 1. Bấm giao dịch G01 | G01 | - Không chuyển màn, không mở gì<br>- Danh sách giữ nguyên | Medium | Negative | EG |  |
| TC_LSGD_DS_018 | BR-04 | Lịch sử GD – Danh sách | Kiểm tra nhấn giữ giao dịch | PC-02 | 1. Nhấn giữ giao dịch G02 3 giây | G02 | - Không hiện menu, màn giữ nguyên | Low | Negative | EG |  |
| TC_LSGD_DS_019 | BR-06 | Lịch sử GD – Màn trống | Kiểm tra màn trống khi KH không có giao dịch | KH02 đã đăng nhập | 1. Mở tab "Lịch sử" | KH02 | - Hiện hình minh họa và chữ "Không có giao dịch" | Medium | Positive | UC |  |
| TC_LSGD_DS_020 | BR-06, BR-11 | Lịch sử GD – Màn trống | Kiểm tra màn trống khi lọc không có kết quả | PC-03 | 1. Chọn "Rút tiền"<br>2. Chọn khoảng 01/06/2026 – 30/06/2026 (SOP-01) | KH01 | - Hiện chữ "Không có giao dịch" | Medium | Negative | UC | Luồng A2 |
| TC_LSGD_DS_021 | BR-06, BR-16 | Lịch sử GD – Màn trống | Kiểm tra thoát màn trống khi đặt lại bộ lọc | Đang ở màn trống của DS_020 | 1. Bấm icon lọc<br>2. Bấm "Đặt lại"<br>3. Bấm "Lưu" | KH01 | - Hiện lại 9 giao dịch như TC_LSGD_DS_002 | Medium | Positive | UC | Bổ sung B5 |
| TC_LSGD_NV_001 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra không hiển thị GD của tiểu khoản khác sub88 | PC-02 | 1. Xem toàn bộ danh sách | G13 – tiểu khoản 01, 18/11/2026, +7.000.000 | - Không có card "+7.000.000 VND" ngày 18/11/2026<br>- Danh sách vẫn 9 card | High | Negative | EP | [GIẢ ĐỊNH] – Q-19 |
| TC_LSGD_NV_002 | BR-22 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra không hiển thị GD khi lệnh mua bị Rejected | PC-02 | 1. Xem toàn bộ danh sách | G14 – lệnh mua TPQ12206, -200.000.000, Rejected 19/11/2026 | - Không có card "-200.000.000 VND" hay nội dung chứa TPQ12206<br>- Danh sách vẫn 9 card | High | Negative | EG | Mục 8 |
| TC_LSGD_NV_003 | BR-05, BR-08 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Mua trái phiếu xuất hiện khi lệnh đặt mua được check trên Portal | PC-02; KH01 đã đặt lệnh mua TPQ12205 SL 1 qua App; user BO có quyền make/check | 1. BO make lệnh trên SHBond Portal<br>2. BO check lệnh lúc 10:20 25/11/2026<br>3. Trên App kéo xuống làm mới | TPQ12205; SL 1; tổng tiền 100.500.000 | - Card mới ở đầu danh sách: "10:20 25/11/2026", "-100.500.000 VND" chữ đỏ<br>- Nội dung "Trả tiền mua mã TPQ12205 ngày 25/11/2026"<br>- Lọc "Mua trái phiếu" thấy card này | High | Integration | UC | [GIẢ ĐỊNH] thời gian = thời điểm check – Q-17 |
| TC_LSGD_NV_004 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Mua không hiển thị khi lệnh mới make, chưa check | PC-02; KH01 đã đặt lệnh mua TPQ12205 SL 2 | 1. BO make lệnh trên Portal (không check)<br>2. Trên App kéo xuống làm mới | TPQ12205; SL 2; 201.000.000 | - Không có card "-201.000.000 VND"<br>- Danh sách giữ nguyên 9 card | High | Negative | ST | [GIẢ ĐỊNH] – Q-18 |
| TC_LSGD_NV_005 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Bán trái phiếu xuất hiện khi lệnh thanh lý được duyệt trên Portal | PC-02; KH01 có HĐ TPQ12205 còn hiệu lực; KH01 đã gửi yêu cầu thanh lý | 1. BO make + check lệnh thanh lý trên Portal<br>2. Trên App kéo xuống làm mới<br>3. Lọc "Bán trái phiếu" | TPQ12205; tiền nhận 101.000.000 | - Có card "+101.000.000 VND" chữ xanh, thời gian = thời điểm check<br>- Nội dung khớp message trong response O5<br>- Card xuất hiện khi lọc "Bán trái phiếu" | High | Integration | UC | [GIẢ ĐỊNH] – Q-17 |
| TC_LSGD_NV_006 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Nhận trái tức xuất hiện khi Flex trả trái tức | PC-02; KH01 nắm giữ TPQ12205 trên sub88 | 1. Trên Flex UAT thực hiện trả trái tức TPQ12205 cho KH01, ngày chốt 25/11/2026<br>2. Trên App kéo xuống làm mới | Trái tức 2.500.000 | - Có card "+2.500.000 VND" chữ xanh, nội dung "Lãi trái phiếu TPQ12205 chốt ngày 25/11/2026"<br>- Card xuất hiện khi lọc "Nhận trái tức" | High | Integration | UC |  |
| TC_LSGD_NV_007 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Nộp tiền xuất hiện khi Flex ghi nhận nộp tiền vào sub88 | PC-02 | 1. Trên Flex UAT nộp 10.000.000 vào tiểu khoản 88 của KH01<br>2. Trên App kéo xuống làm mới | 10.000.000 | - Có card "+10.000.000 VND" chữ xanh ở đầu danh sách<br>- Card xuất hiện khi lọc "Nộp tiền" | Medium | Integration | UC |  |
| TC_LSGD_NV_008 | BR-05 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra GD Rút tiền xuất hiện khi Flex ghi nhận rút tiền từ sub88 | PC-02 | 1. Trên Flex UAT rút 5.000.000 từ tiểu khoản 88 của KH01<br>2. Trên App kéo xuống làm mới | 5.000.000 | - Có card "-5.000.000 VND" chữ đỏ ở đầu danh sách<br>- Card xuất hiện khi lọc "Rút tiền" | Medium | Integration | UC |  |
| TC_LSGD_NV_009 | BR-22 | Lịch sử GD – Phạm vi dữ liệu | Kiểm tra hiển thị khi GD bị hủy và hoàn tiền sau xác nhận thanh toán | PC-02; KH01 có lệnh mua TPQ12205 đã xác nhận thanh toán 100.500.000 | 1. BO hủy lệnh, hệ thống revert tiền<br>2. Trên App kéo xuống làm mới | TPQ12205; 100.500.000 | - Có card hoàn tiền "+100.500.000 VND" chữ xanh<br>- Card mua gốc "-100.500.000 VND" vẫn hiển thị | Medium | Integration | EG | [GIẢ ĐỊNH] – Q-11; để Blocked đến khi BA trả lời |
| TC_LSGD_NV_010 | BR-17, BR-01 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra khoảng ngày mặc định khi ngày thiết bị khác ngày BE | PC-01; đổi ngày thiết bị sang 01/01/2027 (tắt tự động) | 1. Bấm tab "Lịch sử"<br>2. Bấm icon lọc | Ngày BE 25/11/2026; thiết bị 01/01/2027 | - Request O5: fromDate = 25/10/2026, toDate = 25/11/2026<br>- Ô ngày "25/10/2026 → 25/11/2026"; danh sách 9 card | Medium | Negative | EG | Bổ sung B5 |
| TC_LSGD_NV_011 | BR-17, BR-14 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra lịch chặn ngày tương lai theo ngày BE khi ngày thiết bị lớn hơn | PC-04; ngày thiết bị 01/01/2027 | 1. Bấm ngày 26/11/2026<br>2. Bấm ngày 30/11/2026 | Ngày BE 25/11/2026 | - Ngày từ 26/11/2026 trở đi ở trạng thái disable<br>- Bấm không chọn được; dòng "Ngày chọn" không đổi | Medium | Negative | EG | Bổ sung B5 |
| TC_LSGD_NV_012 | BR-17, BR-01 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra khoảng mặc định 1 tháng khi ngày BE là ngày cuối tháng 31 | Môi trường UAT đặt ngày nghiệp vụ BE = 31/03/2027; KH01 đăng nhập | 1. Bấm tab "Lịch sử"<br>2. Xem request O5 | Ngày BE 31/03/2027 | - fromDate = 28/02/2027; toDate = 31/03/2027 | Low | Boundary | BVA | [GIẢ ĐỊNH] – Q-01; Bổ sung B5 |
| TC_LSGD_NV_013 | BR-02, BR-17 | Lịch sử GD – Ngày nghiệp vụ | Kiểm tra giờ hiển thị khi thiết bị đặt múi giờ khác Việt Nam | PC-01; đổi múi giờ thiết bị sang UTC+0 | 1. Bấm tab "Lịch sử"<br>2. Xem card G01 | G01 – 08:15 giờ VN | - Card G01 vẫn hiển thị "08:15 25/11/2026" | Low | Negative | EG | [GIẢ ĐỊNH] – Q-28; Bổ sung B5 |
| TC_LSGD_PT_001 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra số card trang đầu khi KH có 45 GD | KH03 đã đăng nhập; Proxyman bắt response | 1. Vào S-Bond > tab "Lịch sử" | KH03 – 45 GD | - Hiển thị 20 card<br>- Response O5 page 1: totalCount = 45, totalPage = 3 | Medium | Positive | EP | [GIẢ ĐỊNH] 20 bản ghi/trang – Q-16 |
| TC_LSGD_PT_002 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra tải trang 2 khi cuộn đến cuối danh sách | PC-05; Proxyman bắt request | 1. Cuộn đến card thứ 20 | KH03 | - Gọi O5 với page = 2, giữ fromDate/toDate<br>- Danh sách thành 40 card, không có card trùng | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_PT_003 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra tải trang cuối khi trang cuối có ít hơn 20 bản ghi | PC-05; đã tải trang 2 | 1. Cuộn đến card thứ 40 | KH03 | - Gọi O5 page = 3<br>- Thêm 5 card, tổng 45 card | Medium | Boundary | BVA | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_PT_004 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra không tải thêm khi đã ở trang cuối | PC-05; đã tải đủ 45 card; Proxyman bắt request | 1. Cuộn đến card 45<br>2. Tiếp tục vuốt lên 3 lần | KH03 | - Không có request page = 4<br>- Danh sách giữ 45 card | Medium | Boundary | BVA |  |
| TC_LSGD_PT_005 | BR-03, BR-07 | Lịch sử GD – Phân trang | Kiểm tra thứ tự mới → cũ giữa 2 trang | PC-05; đã tải trang 2 | 1. So sánh Thời gian GD card 20 và card 21 | KH03 | - Card 20 có thời gian mới hơn card 21<br>- 40 card giảm dần theo thời gian | Medium | Positive | EP |  |
| TC_LSGD_PT_006 | BR-07, BR-11 | Lịch sử GD – Phân trang | Kiểm tra phân trang giữ bộ lọc Loại GD | PC-05; Proxyman bắt request | 1. Bấm icon lọc, chọn "Nhận trái tức", bấm "Lưu"<br>2. Cuộn đến card thứ 20 | KH03 – 25 GD INTEREST | - Request page 2 có transType = INTEREST<br>- Tổng 25 card, đều là GD Nhận trái tức (+, xanh) | Medium | Positive | PW | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_PT_007 | BR-07 | Lịch sử GD – Phân trang | Kiểm tra không tải trùng khi vuốt liên tục ở cuối trang | PC-05; Proxyman bắt request | 1. Cuộn nhanh đến card 20 và vuốt lên liên tục 5 lần trong 2 giây | KH03 | - Chỉ 1 request page = 2<br>- Danh sách 40 card, không trùng | Low | Negative | EG |  |
| TC_LSGD_PT_008 | BR-08 | Lịch sử GD – Làm mới | Kiểm tra kéo làm mới khi có GD mới phát sinh | PC-02 | 1. Trên Flex nộp 1.000.000 vào sub88 KH01 lúc 11:00 25/11/2026<br>2. Trên App kéo xuống làm mới | 1.000.000 | - Gọi lại O5<br>- Card "11:00 25/11/2026" "+1.000.000 VND" ở đầu; tổng 10 card | Medium | Positive | UC |  |
| TC_LSGD_PT_009 | BR-08 | Lịch sử GD – Làm mới | Kiểm tra kéo làm mới giữ bộ lọc đang áp dụng | PC-02; Proxyman bắt request; đã lọc "Nộp tiền" + khoảng 01/11/2026–25/11/2026 | 1. Kéo xuống làm mới | KH01 | - Request O5: transType = DEPOSIT, fromDate = 01/11/2026, toDate = 25/11/2026<br>- Danh sách vẫn chỉ G03 | Medium | Positive | UC |  |
| TC_LSGD_PT_010 | BR-08, BR-07 | Lịch sử GD – Làm mới | Kiểm tra kéo làm mới sau khi đã tải trang 2 | PC-05; đã tải trang 2 (40 card) | 1. Cuộn lên đầu danh sách<br>2. Kéo xuống làm mới | KH03 | - Request O5 page = 1<br>- Danh sách còn 20 card | Low | Positive | EG | [GIẢ ĐỊNH] – Q-20 |
| TC_LSGD_PT_011 | BR-07, BR-19 | Lịch sử GD – Phân trang | Kiểm tra lỗi khi tải trang tiếp | PC-05; Proxyman mock response page 2 = BND-999 | 1. Cuộn đến card 20 | KH03 | - Popup lỗi chuẩn "Có lỗi xảy ra. Vui lòng thử lại sau", CTA "Đóng"<br>- Bấm "Đóng": 20 card trang 1 vẫn hiển thị | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-21 |
| TC_LSGD_BL_001 | BR-09, BR-10, BR-11, BR-12, BR-13, BR-14, BR-15, BR-16 | Lịch sử GD – Bộ lọc | Kiểm tra bộ lọc Loại giao dịch và Thời gian giao dịch | PC-02 | 1. Bấm icon lọc<br>2. Chọn "Bán trái phiếu", bấm "<"<br>3. Lần lượt chọn từng loại (Nhận trái tức / Mua / Bán / Nộp / Rút), mỗi lần bấm "Lưu"<br>4. Chọn "Nhận trái tức" + "Mua trái phiếu", bấm "Lưu"<br>5. Bấm "Tất cả"<br>6. Chọn ngày 01/11/2026 → 15/11/2026, bấm "Lưu" 2 lần<br>7. Chọn ngày 24/05/2026 → 25/11/2026, bấm "Lưu"<br>8. Bấm ngày 26/11/2026 trên lịch<br>9. Chọn "Nộp tiền" + 01/11/2026 → 25/11/2026, bấm "Lưu"<br>10. Mở lại bộ lọc, bấm "Đặt lại", bấm "Lưu" | KH01; ngày nghiệp vụ 25/11/2026 | 1. Mặc định: "Tất cả", 25/10/2026 → 25/11/2026<br>2. Đóng bộ lọc, danh sách giữ 9 GD<br>3. Trái tức: G01, G15, G06 · Mua: G02 · Bán: G05, G10 · Nộp: G03, G07 · Rút: G04<br>4. Hiện G01, G02, G15, G06; "Tất cả" tự bỏ chọn<br>5. Các loại khác tự bỏ chọn<br>6. Hiện G04, G05, G10<br>7. Báo "Khoảng thời gian tra cứu tối đa 6 tháng", không áp dụng<br>8. Không chọn được<br>9. Chỉ hiện G03; icon lọc đổi trạng thái<br>10. Về 9 GD mặc định; icon lọc về bình thường | High | Positive | DT | Gộp theo yêu cầu (v3); [GIẢ ĐỊNH] – Q-01, Q-03, Q-09 |
| TC_LSGD_ERR_001 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra popup khi O5 trả BND-901 | PC-01; Proxyman mock O5 trả BND-901 | 1. Bấm tab "Lịch sử"<br>2. Bấm "Đóng" | KH01 | - Popup lỗi chuẩn SHSmart: "Hệ thống đang bận", CTA "Đóng"<br>- Không hiển thị card nào<br>- Bấm "Đóng" → popup đóng, app không crash | Medium | Negative | EP | [GIẢ ĐỊNH] mapping mã → nội dung – Q-32 |
| TC_LSGD_ERR_002 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra popup khi O5 trả BND-999 | PC-01; Proxyman mock O5 trả BND-999 | 1. Bấm tab "Lịch sử" | KH01 | - Popup "Có lỗi xảy ra. Vui lòng thử lại sau", CTA "Đóng"<br>- Không hiển thị card nào | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-32 |
| TC_LSGD_ERR_003 | BR-18, BR-19 | Lịch sử GD – Lỗi | Kiểm tra popup khi phiên đăng nhập hết hạn lúc mở tab | PC-01; token KH01 đã hết hạn (đợi quá thời gian phiên) | 1. Bấm tab "Lịch sử"<br>2. Bấm "Đăng nhập" | KH01 | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", CTA "Đăng nhập"<br>- Không hiển thị GD nào<br>- Bước 2: chuyển màn Đăng nhập | High | Security | EP | V03; [GIẢ ĐỊNH] – Q-32 |
| TC_LSGD_ERR_004 | BR-18 | Lịch sử GD – Lỗi | Kiểm tra khi phiên hết hạn lúc bấm Lưu bộ lọc | PC-03; token hết hạn trong lúc bottom sheet đang mở | 1. Bấm chip "Mua trái phiếu"<br>2. Bấm "Lưu" | KH01 | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", CTA "Đăng nhập"<br>- Không hiển thị kết quả lọc | Medium | Security | EP |  |
| TC_LSGD_ERR_005 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra khi mất mạng lúc mở tab Lịch sử | PC-01; tắt Wi-Fi và dữ liệu di động | 1. Bấm tab "Lịch sử"<br>2. Bật lại mạng<br>3. Kéo xuống làm mới | KH01 | - Bước 1: popup lỗi chuẩn SHSmart, app không crash<br>- Bước 3: hiển thị 9 card | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-27 |
| TC_LSGD_ERR_006 | BR-19 | Lịch sử GD – Lỗi | Kiểm tra khi mất mạng lúc bấm Lưu bộ lọc | PC-03; tắt mạng sau khi mở Bộ lọc | 1. Bấm chip "Bán trái phiếu"<br>2. Bấm "Lưu" | KH01 | - Popup lỗi chuẩn SHSmart<br>- Đóng popup: danh sách vẫn 9 card mặc định | Low | Negative | EG | [GIẢ ĐỊNH] – Q-27 |
| TC_LSGD_ERR_007 | BR-15, BR-14 | Lịch sử GD – Lỗi | Kiểm tra BE chặn khi khoảng ngày vượt 6 tháng bị sửa qua request | PC-03; Proxyman sửa request O5 fromDate = 01/01/2026, toDate = 25/11/2026 | 1. Bấm "Lưu" | KH01 | - O5 trả lỗi BND-1xx<br>- App hiển thị "Khoảng thời gian tra cứu tối đa 6 tháng"<br>- Không hiển thị GD trước 25/05/2026 (G12) | Medium | Negative | EG | [GIẢ ĐỊNH] mã lỗi – Q-04; Bổ sung B5 |
| TC_LSGD_ERR_008 | BR-01 | Lịch sử GD – Lỗi | Kiểm tra khi O5 phản hồi chậm | PC-01; Proxyman throttle O5 chậm 10 giây | 1. Bấm tab "Lịch sử"<br>2. Đợi phản hồi | KH01 | - Trong lúc chờ: có loading, không có request trùng<br>- Sau khi có phản hồi: 9 card | Low | Negative | EG | [GIẢ ĐỊNH] có loading – Q-27 |
| TC_LSGD_PQ_001 | BR-18 | Lịch sử GD – Phân quyền | Kiểm tra dữ liệu khi đổi tài khoản trên cùng thiết bị | PC-02 (KH01 đã xem 9 card) | 1. Đăng xuất KH01<br>2. Đăng nhập KH02<br>3. Vào S-Bond > tab "Lịch sử" | KH01 → KH02 | - Hiển thị "Không có giao dịch"<br>- Không còn card nào của KH01 (cache) | High | Security | EG | V03 |
| TC_LSGD_PQ_002 | BR-18 | Lịch sử GD – Phân quyền | Kiểm tra không xem được GD KH khác khi sửa request | PC-01; Proxyman thêm vào request O5 tham số định danh của KH04 (vd accountNo = 069C100004) | 1. Bấm tab "Lịch sử" | KH01 token; tham số KH04 | - Popup lỗi chuẩn (BND-403) hoặc chỉ hiển thị 9 card KH01<br>- Không hiển thị G20 "+9.999.000 VND" | High | Security | EG | [GIẢ ĐỊNH] – Q-33 |
| TC_LSGD_EVT_001 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.LIST khi mở tab | PC-01; mở debug analytics | 1. Bấm tab "Lịch sử" | KH01 | - Ghi nhận 1 event SBOND.HISTORY.LIST | Low | Integration | UC |  |
| TC_LSGD_EVT_002 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.LIST khi kéo làm mới | PC-02; mở debug analytics | 1. Kéo xuống làm mới | KH01 | - Ghi nhận thêm 1 event SBOND.HISTORY.LIST | Low | Integration | UC |  |
| TC_LSGD_EVT_003 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.LIST khi tải trang tiếp | PC-05; mở debug analytics | 1. Cuộn đến card 20 | KH03 | - Ghi nhận 1 event SBOND.HISTORY.LIST cho page 2 | Low | Integration | UC |  |
| TC_LSGD_EVT_004 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.FILTER.OPEN khi mở Bộ lọc | PC-02; mở debug analytics | 1. Bấm icon lọc | KH01 | - Ghi nhận 1 event SBOND.HISTORY.FILTER.OPEN | Low | Integration | UC |  |
| TC_LSGD_EVT_005 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.FILTER.APPLY khi Lưu bộ lọc | PC-03; mở debug analytics | 1. Bấm chip "Nộp tiền"<br>2. Bấm "Lưu" | KH01 | - Ghi nhận 1 event SBOND.HISTORY.FILTER.APPLY (kèm 1 event LIST) | Low | Integration | UC |  |
| TC_LSGD_EVT_006 | BR-21 | Lịch sử GD – Tracking | Kiểm tra event SBOND.HISTORY.FILTER.RESET khi Đặt lại | PC-03; mở debug analytics | 1. Bấm "Đặt lại" | KH01 | - Ghi nhận 1 event SBOND.HISTORY.FILTER.RESET; không có event FILTER.APPLY | Low | Integration | UC |  |

### 4.2 API O5 (22 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_LSGD_API_001 | BR-23, BR-01 | Lịch sử GD – API O5 | Kiểm tra O5 trả danh sách khi gọi với khoảng mặc định | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=25/10/2026&toDate=25/11/2026&page=1 | KH01 | - HTTP 200<br>- data.content có 9 phần tử, mỗi phần tử có transTime, amount, message<br>- data.totalCount = 9; data.totalPage = 1 | High | Positive | EP |  |
| TC_LSGD_API_002 | BR-03 | Lịch sử GD – API O5 | Kiểm tra O5 sắp xếp content theo transTime giảm dần | PC-API | 1. Gọi như API_001<br>2. Duyệt data.content[].transTime | KH01 | - Phần tử đầu = G01 (25/11/2026 08:15), cuối = G07 (25/10/2026 00:01)<br>- transTime phần tử sau ≤ phần tử trước | Medium | Positive | EP |  |
| TC_LSGD_API_003 | BR-02, BR-05 | Lịch sử GD – API O5 | Kiểm tra dấu amount theo loại GD | PC-API | 1. Gọi như API_001<br>2. Đối chiếu amount từng GD | KH01 | - G01 = 34731400; G15 = 5000000; G06 = 1000000 (>0)<br>- G02 = -134731400; G04 = -20000000 (<0)<br>- G03 = 50000000; G07 = 500; G05 = 1021111000; G10 = 12345678900 (>0) | High | Positive | DT |  |
| TC_LSGD_API_004 | BR-11 | Lịch sử GD – API O5 | Kiểm tra O5 lọc khi transType = INTEREST | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?transType=INTEREST&fromDate=25/10/2026&toDate=25/11/2026&page=1 | KH01 | - HTTP 200; data.content có đúng 3 phần tử: G01, G15, G06<br>- data.totalCount = 3 | Medium | Positive | EP |  |
| TC_LSGD_API_005 | BR-11 | Lịch sử GD – API O5 | Kiểm tra O5 lọc khi transType nhiều giá trị | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?transType=INTEREST,BUY&fromDate=25/10/2026&toDate=25/11/2026&page=1 | KH01 | - HTTP 200; data.content = G01, G02, G15, G06; totalCount = 4 | Medium | Positive | PW | [GIẢ ĐỊNH] format – Q-12 |
| TC_LSGD_API_006 | BR-11 | Lịch sử GD – API O5 | Kiểm tra O5 khi transType không thuộc danh sách | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?transType=ABC&fromDate=25/10/2026&toDate=25/11/2026&page=1 | transType = ABC | - HTTP 400, error code BND-1xx<br>- Không trả data.content | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-04 |
| TC_LSGD_API_007 | BR-14 | Lịch sử GD – API O5 | Kiểm tra O5 khi fromDate lớn hơn toDate | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=15/11/2026&toDate=10/11/2026&page=1 | 15/11/2026 > 10/11/2026 | - HTTP 400, error code BND-1xx<br>- Không trả data.content | Medium | Negative | EP | V01; [GIẢ ĐỊNH] – Q-04 |
| TC_LSGD_API_008 | BR-14, BR-17 | Lịch sử GD – API O5 | Kiểm tra O5 khi toDate sau ngày hiện tại BE | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=01/11/2026&toDate=26/11/2026&page=1 | toDate = 26/11/2026 | - HTTP 400, error code BND-1xx | Medium | Negative | BVA | V01 "(BE)"; [GIẢ ĐỊNH] – Q-04 |
| TC_LSGD_API_009 | BR-15 | Lịch sử GD – API O5 | Kiểm tra O5 khi khoảng ngày đúng 6 tháng | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=25/05/2026&toDate=25/11/2026&page=1 | 25/05/2026 → 25/11/2026 | - HTTP 200; totalCount = 11; có G08, G11; không có G12 | Medium | Boundary | BVA | [GIẢ ĐỊNH] – Q-02 |
| TC_LSGD_API_010 | BR-15 | Lịch sử GD – API O5 | Kiểm tra O5 khi khoảng ngày vượt 6 tháng 1 ngày | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=24/05/2026&toDate=25/11/2026&page=1 | 24/05/2026 → 25/11/2026 | - HTTP 400, error code BND-1xx<br>- Không trả G12 hay bất kỳ phần tử nào | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-02, Q-04 |
| TC_LSGD_API_011 | BR-23 | Lịch sử GD – API O5 | Kiểm tra O5 khi ngày sai định dạng dd/MM/yyyy | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=2026-11-01&toDate=2026-11-15&page=1 | yyyy-MM-dd | - HTTP 400 kèm error code + message; không trả dữ liệu | Low | Negative | EG | [GIẢ ĐỊNH] – Q-34 |
| TC_LSGD_API_012 | BR-07 | Lịch sử GD – API O5 | Kiểm tra O5 trả trang 2 khi KH có 45 GD | Postman, token KH03 | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=25/10/2026&toDate=25/11/2026&page=2 | KH03 | - HTTP 200; data.content 20 phần tử, không trùng page 1<br>- totalCount = 45; totalPage = 3 | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-16 |
| TC_LSGD_API_013 | BR-07 | Lịch sử GD – API O5 | Kiểm tra O5 khi page lớn hơn totalPage | Postman, token KH03 | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=25/10/2026&toDate=25/11/2026&page=4 | KH03; page = 4 | - HTTP 200; data.content = [] | Low | Boundary | BVA | [GIẢ ĐỊNH] – Q-34 |
| TC_LSGD_API_014 | BR-07 | Lịch sử GD – API O5 | Kiểm tra O5 khi page = 0 | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=25/10/2026&toDate=25/11/2026&page=0 | page = 0 | - HTTP 400 kèm error code; không trả dữ liệu | Low | Negative | BVA | [GIẢ ĐỊNH] – Q-34 |
| TC_LSGD_API_015 | BR-18, BR-19 | Lịch sử GD – API O5 | Kiểm tra O5 khi không gửi Authorization | Postman, UAT | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=25/10/2026&toDate=25/11/2026&page=1 không có header Authorization | — | - HTTP 401, error code AUTH_001<br>- Không trả data | High | Security | EP |  |
| TC_LSGD_API_016 | BR-18, BR-19 | Lịch sử GD – API O5 | Kiểm tra O5 khi token hết hạn | Postman; token KH01 đã hết hạn | 1. Gọi như API_001 | Token hết hạn | - HTTP 401, error code AUTH_001 | High | Security | EP |  |
| TC_LSGD_API_017 | BR-06 | Lịch sử GD – API O5 | Kiểm tra O5 khi KH không có giao dịch | Postman, token KH02 | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=25/10/2026&toDate=25/11/2026&page=1 | KH02 | - HTTP 200; data.totalCount = 0; data.content = [] (không null) | Medium | Negative | EP |  |
| TC_LSGD_API_018 | BR-18 | Lịch sử GD – API O5 | Kiểm tra O5 không trả GD KH khác khi thêm tham số định danh | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?accountNo=069C100004&fromDate=25/10/2026&toDate=25/11/2026&page=1 bằng token KH01 | KH01 token; tham số KH04 | - HTTP 403 BND-403, hoặc HTTP 200 chỉ chứa 9 GD của KH01<br>- Không có G20 | High | Security | EG | [GIẢ ĐỊNH] – Q-33 |
| TC_LSGD_API_019 | BR-23 | Lịch sử GD – API O5 | Kiểm tra O5 khi transType chứa chuỗi SQL injection | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?transType=' OR 1=1 --&fromDate=25/10/2026&toDate=25/11/2026&page=1 | transType = ' OR 1=1 -- | - HTTP 400<br>- Không trả dữ liệu; không lộ stack trace / lỗi SQL | Medium | Security | EG |  |
| TC_LSGD_API_020 | BR-05, BR-22 | Lịch sử GD – API O5 | Kiểm tra O5 không trả GD ngoài sub88 và lệnh Rejected | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?fromDate=01/11/2026&toDate=25/11/2026&page=1 | G13, G14 | - HTTP 200<br>- Không có phần tử +7000000 ngày 18/11/2026 (G13)<br>- Không có phần tử -200000000 / TPQ12206 (G14) | High | Negative | EP | [GIẢ ĐỊNH] – Q-19 |
| TC_LSGD_API_021 | BR-04 | Lịch sử GD – API O5 | Kiểm tra O5 không làm đổi dữ liệu khi gọi lặp lại | PC-API | 1. Gọi như API_001<br>2. Gọi lại lần 2<br>3. So sánh 2 response | KH01 | - 2 response giống hệt nhau (9 phần tử, cùng giá trị) | Low | Positive | EG | Post-Condition |
| TC_LSGD_API_022 | BR-01, BR-23 | Lịch sử GD – API O5 | Kiểm tra O5 khi không gửi fromDate và toDate | PC-API | 1. Gọi GET /api/v1/bond/app/transactions?page=1 | — | - HTTP 200 với khoảng mặc định 25/10/2026 – 25/11/2026 (9 phần tử) | Low | Negative | EG | [GIẢ ĐỊNH] – Q-34; Bổ sung B5 |

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
| BR-09 | Icon lọc → bottom sheet "Bộ lọc" giữ lựa chọn hiện tại; nút quay lại t… | 2 | TC_LSGD_UI_004, TC_LSGD_BL_001 | Đủ |
| BR-10 | Icon lọc đổi trạng thái khi đang lọc (Dự thảo BA) | 1 | TC_LSGD_BL_001 | Đủ |
| BR-11 | Loại GD: chip chọn nhiều – Tất cả / Nhận trái tức (INTEREST) / Mua trá… | 7 | TC_LSGD_UI_004, TC_LSGD_DS_020, TC_LSGD_PT_006, TC_LSGD_BL_001, TC_LSGD_API_004, TC_LSGD_API_005, TC_LSGD_API_006 | Đủ |
| BR-12 | Thời gian GD: ô từ → đến (dd/MM/yyyy) + icon lịch; mặc định 1 tháng gầ… | 2 | TC_LSGD_UI_004, TC_LSGD_BL_001 | Đủ |
| BR-13 | Bottom sheet "Thời gian giao dịch": lịch tháng "Tháng M, yyyy", tuần b… | 2 | TC_LSGD_UI_005, TC_LSGD_BL_001 | Đủ |
| BR-14 | V01: Từ ngày ≤ Đến ngày; không chọn ngày sau ngày hiện tại (BE) – lịch… | 5 | TC_LSGD_NV_011, TC_LSGD_BL_001, TC_LSGD_ERR_007, TC_LSGD_API_007, TC_LSGD_API_008 | Đủ |
| BR-15 | V02: khoảng ngày không vượt quá 6 tháng → inline dưới ô ngày "Khoảng t… | 4 | TC_LSGD_BL_001, TC_LSGD_ERR_007, TC_LSGD_API_009, TC_LSGD_API_010 | Đủ |
| BR-16 | Bộ lọc: "Đặt lại" → về mặc định (chưa gọi API); "Lưu" → đóng bottom sh… | 2 | TC_LSGD_DS_021, TC_LSGD_BL_001 | Đủ |
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
| A. Decision table mỗi rule có TC; ST có chuyển trạng thái không hợp lệ | Đạt | DT dấu tiền R1–R5 (DS_007–011); DT chip và ST lịch gộp trong BL_001 |
| A. Phân quyền | Đạt | 1 role KH → token, đổi tài khoản, IDOR app + API |
| A. Error guessing | Đạt | Mạng, chậm, double-tap, HTML/null, giờ thiết bị, SQLi |
| B. Đủ 12 cột, ID đúng format, không trùng | Đạt | 94 ID duy nhất (kiểm bằng script) |
| B. 1 TC = 1 mục tiêu | Đạt một phần | TC_LSGD_BL_001 gộp toàn bộ phần lọc theo yêu cầu; các nhóm khác vẫn 1 TC = 1 mục tiêu |
| B. Title "Kiểm tra … khi …" | Đạt | — |
| B. Test data cụ thể | Đạt | Bộ G01–G20, KH01–KH04, ngày BE 25/11/2026 |
| B. Expected đo được | Đạt | Số card, mã card, text, màu, tham số request |
| B. Negative kiểm cả báo lỗi + không thực hiện | Đạt | BL_001 bước 7, ERR_*, API_* có "không gọi O5 / không trả data / danh sách giữ" |
| B. Message nguyên văn / [GIẢ ĐỊNH] | Đạt | I01, E01, 3 popup lấy từ Mục 6; mapping mã → text [GĐ] Q-32 |
| C. Priority hợp lý | Đạt | Dấu tiền, phạm vi dữ liệu, bảo mật = High |
| C. Thứ tự UI → Positive → … → Permission | Đạt | Theo nhóm màn; trong nhóm theo thứ tự quy tắc |

### Sửa/bổ sung trong vòng review
- v3 (25/11/2026): gộp phần lọc (LOAI_001–012, TG_001–018, BL_001–011 = 41 TC) thành 1 TC tổng quan TC_LSGD_BL_001 theo yêu cầu. Các trường hợp biên chi tiết của lịch (Từ = Đến, Đến < Từ, chọn 1 ngày, chuyển tháng) không còn TC riêng.
- v2.1 (25/11/2026): nhóm Danh sách / Màn trống rút gọn tiêu đề, bước và Expected; bỏ lặp nội dung giữa các TC (DS_003, DS_021 tham chiếu DS_002).
- v2 (25/11/2026): nhóm Danh sách / Màn trống (DS_001–DS_021) bỏ toàn bộ bước và kết quả kiểm API; Expected viết bằng tiếng Việt thường, liệt kê cụ thể từng giao dịch mong đợi. DS_001 đổi thành kiểm biên 1 tháng trên giao diện. Phần kiểm API giữ ở nhóm API O5.
- Tách hiển thị số tiền thành 5 TC theo từng loại GD (DS_007–DS_011) thay vì 1 TC chung, để mỗi rule của decision table có 1 TC.
- Bổ sung biên mặc định 1 tháng: G07 (25/10 00:01, trong) và G08 (24/10 23:59, ngoài); ngày BE cuối tháng (NV_012).
- Bổ sung kiểm ngày theo BE khi giờ/múi giờ thiết bị sai (NV_010, NV_011, NV_013) – BIZ-11 trước đó chưa có TC riêng.
- Bổ sung BE chặn khoảng > 6 tháng khi bypass FE (ERR_007, API_010) và IDOR (PQ_002, API_018).
- Bổ sung TC_LSGD_DS_021 (thoát màn trống khi đổi bộ lọc).

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 94 (App 72 · API 22) | — |
| High / Medium / Low | 24 (26%) / 40 (43%) / 30 (32%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 28 : 66 (30% : 70%) | ≈ 30 : 70 |
| Theo Type | Positive 28 · Negative 27 · Boundary 11 · Security 9 · Integration 12 · UI 7 | — |
| Requirement coverage | 23/23 = 100% | 100% |
| Q&A mở | 36 (Blocker 0 · Major 14 · Minor 22) | Blocker = 0 |
| TC có [GIẢ ĐỊNH] | 42 | — |
| TC bổ sung ở B5 | 7 (TC_LSGD_DS_021, TC_LSGD_NV_010, TC_LSGD_NV_011, TC_LSGD_NV_012, TC_LSGD_NV_013, TC_LSGD_ERR_007, TC_LSGD_API_022) | — |

**Lưu ý chất lượng:** High 26% hơi dưới mục tiêu vì phần lớn TC lọc/hiển thị là Medium theo định nghĩa file 02; mọi TC về dấu tiền, phạm vi dữ liệu và bảo mật đều High. 42 TC dựa trên giả định – ưu tiên chốt các Q&A Major (Q-01, Q-02, Q-03, Q-13, Q-16) trước khi chạy.
