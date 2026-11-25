# SBOND.DOCCONFIRM.APP.01 – Danh sách hợp đồng cần xác nhận (App) – Test design B1→B5

Nguồn: SRS SBOND.DOCCONFIRM.APP.01 – Danh sách hợp đồng cần xác nhận (export Confluence 15/11/2026) + ảnh Figma/flow trong SRS  
Người viết: Nguyễn Sỹ Hà · Ngày: 25/11/2026 · Lệnh: /FULL  
Phiên bản: v1  
Ký hiệu: [GIẢ ĐỊNH] = giả định để đi tiếp, xem Q-xx; T = ngày đặt mua thành công; E = ngày đáo hạn; N = ngày/giờ nghiệp vụ BE; PC-xx / SOP-xx = precondition / thao tác dùng chung.
## B1. Phân tích yêu cầu
### 1. Tổng quan
- Mục tiêu: NĐT xem ở một nơi các HĐ đang có hồ sơ cần xác nhận (HĐ Leg 2 từ T+3, Phiếu lệnh đặt bán từ E-5) để xác nhận kịp hạn; bộ đếm (badge) trên tab "Xác nhận"; lọc theo Mã trái phiếu, Tổ chức phát hành; bấm card → Chi tiết HĐ – tab Xác nhận (SBOND.CONTRACT.APP.02) → "Xem hợp đồng" (SBOND.DOCCONFIRM.APP.02). Màn chỉ xem, không đổi dữ liệu.
- Actor: KH (NĐT trên SHSmart App). Hệ thống: BE S-Bond (API A0, A1, A2/O4; job SBOND.JOB.T3_DOC_REQUEST, SBOND.JOB.E5_REPRICE gửi yêu cầu xác nhận).
- Entry: tab dưới "Xác nhận" (SHSmart > Mở rộng > Sản phẩm đầu tư > S-Bond); noti Bond_03; nút "Kiểm tra" ở box "Hợp đồng cần xác nhận" – Danh mục HĐ (SBOND.CONTRACT.APP.01). Precondition: KH đã đăng nhập.
- Ký hiệu ngày: T = ngày đặt mua thành công của HĐ; E = ngày đáo hạn HĐ (ngày thực hiện Leg 2); N = ngày/giờ nghiệp vụ hiện tại của BE (BIZ-11).

### 2. Feature
- F1 Danh sách card HĐ cần xác nhận · F2 Badge bộ đếm trên tab · F3 Màn trống · F4 Kéo làm mới · F5 Bottom sheet Bộ lọc (Mã trái phiếu, Tổ chức phát hành, Đặt lại/Lưu) · F6 Bấm card → Chi tiết HĐ tab Xác nhận → xác nhận → tải lại · F7 Phạm vi/thời hạn hiển thị (T+3, E-5, COT 11h ngày E, edge case) · F8 Lỗi hệ thống / phiên / phân quyền (V01) · F9 Tracking event · F10 API A0, A1, A2

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Mở tab "Xác nhận" / bấm noti Bond_03 / bấm "Kiểm tra" ở Danh mục HĐ → App gọi A0 + A1 và hiển thị danh sách HĐ cần xác nhận | Description; Entry; Luồng 1 bước 1 | Logic/Tích hợp |
| BR-02 | Phạm vi danh sách: chỉ HĐ trạng thái Hiệu lực có Trạng thái hồ sơ = "Thiếu hồ sơ" (HĐ Leg 2 từ T+3; Phiếu lệnh đặt bán từ E-5) và chưa quá hạn xác nhận; hồ sơ "Chờ gửi KH xác nhận" (chưa tới mốc) không hiển thị | BIZ-27; Mục 8; 2.2 #3 | Logic |
| BR-03 | Badge số (đỏ) trên icon tab "Xác nhận" = số HĐ trong danh sách (data.totalCount – A1), đếm theo HĐ; = 0 → ẩn badge; cập nhật mỗi lần tải danh sách | BIZ-27; Luồng 1 bước 2–3; 2.1 #4 | UI/Logic |
| BR-04 | Card HĐ: Mã TP (cam – symbol); chip Thời gian đầu tư "n tháng" (term/termType); chip trạng thái "Hiệu lực" (status); TCPH (issuerName); Số HĐ (orderNo); Ngày đáo hạn dd/MM/yyyy (matureDate); Tổng tiền đầu tư #.### VND cam (totalInvestmentAmount); Lợi suất x,xx%/năm (rate); Lợi tức dự kiến #.### VND xanh (interest); banner ⚠ "Hợp đồng có hồ sơ cần xác nhận" | 2.2 #1–10; Luồng 1 bước 3 | UI/Tích hợp |
| BR-05 | Sắp xếp card: TBU Q06 – dự thảo BA: ngày đáo hạn gần nhất trước | 2.2 (Sắp xếp) | Logic |
| BR-06 | Không có HĐ cần xác nhận (totalCount = 0) → màn trống: hình minh họa + "Hiện tại không có hợp đồng nào cần xác nhận" (SBOND.DOCCONFIRM.I02); không hiển thị badge | Luồng 1 bước 2; 2.3; V02; Mục 6 #1 | UI |
| BR-07 | Bấm card → mở Chi tiết hợp đồng (bottom sheet) tại tab "Xác nhận" của đúng HĐ (A2/O4 với id = data.content[].id – SBOND.CONTRACT.APP.02) | Luồng 1 bước 4; 2.2 #11 | Logic/Tích hợp |
| BR-08 | Xác nhận thành công (SBOND.DOCCONFIRM.APP.02) → quay về tab Xác nhận, tải lại A1; hồ sơ chuyển Đủ hồ sơ; HĐ không còn hồ sơ cần xác nhận → bỏ cờ cảnh báo và rời tab Xác nhận | Luồng 1 bước 5; BIZ-30 | Logic |
| BR-09 | Thời hạn xác nhận: HĐ Leg 2 từ T+3 đến trước E-5 (từ E-5 gộp với Phiếu lệnh đặt bán); Phiếu lệnh đặt bán từ E-5 đến trước COT 11h ngày E; quá hạn → HĐ không còn trong tab | BIZ-29; Mục 9 #3 | Logic |
| BR-10 | A3: HĐ quá hạn khi KH đang ở màn → BE không trả HĐ đó ở lần tải sau; nếu KH vẫn mở được → xử lý ở SBOND.DOCCONFIRM.APP.02 (E01) | 1.1 A3 | Logic |
| BR-11 | Kéo xuống để làm mới → gọi lại bước 1 (A0, A1) | Luồng 1 bước 6 | Logic |
| BR-12 | Icon lọc trên header → bottom sheet "Bộ lọc"; nút quay lại trên header bottom sheet → đóng, không áp dụng thay đổi | Luồng 1 bước 6; A1; 2.1 #2; 2.4 #1 | Logic |
| BR-13 | Mã trái phiếu: textbox, không bắt buộc, nhập text tự do; tìm theo cụm từ nằm trong mã, không phân biệt hoa/thường; để trống = không lọc; request A1 symbol | BIZ-34; 2.4 #2 | Logic/Validation |
| BR-14 | Tổ chức phát hành: dropdown chọn nhiều, mặc định "Tất cả tổ chức", danh sách từ A0 issuers[]; request A1 issuerCode[] | BIZ-34; 2.4 #3 | Logic/Tích hợp |
| BR-15 | "Đặt lại" → về mặc định (chưa gọi API); "Lưu" → đóng bottom sheet, gọi A1 với bộ lọc mới (symbol, issuerCode[]) | Luồng 1 bước 6; 2.4 #4–5; BIZ-34 | Logic |
| BR-16 | Header: tiêu đề "SH Bond" + nút quay lại (về màn trước); icon ⓘ – nội dung chưa có (TBU Q03) | 2.1 #1, #3; A2 | UI |
| BR-17 | BIZ-11: ngày nghiệp vụ, giờ lấy theo Backend, không theo thiết bị (mốc T+3, E-5, COT 11h ngày E) | BIZ-11 | Logic |
| BR-18 | V01: phiên đăng nhập hợp lệ; dữ liệu chỉ của KH đăng nhập; vi phạm → popup lỗi chuẩn → Đăng nhập (AUTH_001 / BND-403) | Mục 5 V01 | Phân quyền/Bảo mật |
| BR-19 | Lỗi hệ thống / token (BND-901, BND-999, AUTH_001) → popup lỗi chuẩn SHSmart: "Hệ thống đang bận" / "Có lỗi xảy ra. Vui lòng thử lại sau" / "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"; CTA Đóng / Đăng nhập | Luồng 1 bước 7; Mục 6 #2 | Tích hợp/Bảo mật |
| BR-20 | Edge case: hủy GD sau xác nhận thanh toán → revert, HĐ không hiển thị (noti Bond_07); E-5 sinh version mới Phiếu lệnh đặt bán (giá Leg 2 đổi) → hồ sơ về Thiếu hồ sơ, HĐ (lại) xuất hiện (noti Bond_03); quá COT 11h ngày E chưa xác nhận → HĐ Hết hiệu lực, rời tab (noti/email Bond_05) | Mục 9 | Logic |
| BR-21 | Tracking event FE: SBOND.DOCCONFIRM.LIST (mở/làm mới), .OPEN (bấm card), .NOTI.OPEN (mở từ noti Bond_03), .FILTER.APPLY (Lưu bộ lọc) | Mục 7 | Tích hợp |
| BR-22 | API: A0 GET /api/v1/bond/term/app/appendix/filters (mở màn); A1 GET /api/v1/bond/term/app/appendix (mở màn, lọc, kéo làm mới, sau xác nhận thành công); A2/O4 GET /api/v1/bond/term/app/appendix/detail + GET /api/v1/bond/app/order/documents/filter (bấm card) | Mục 3 | Tích hợp |
| BR-23 | Post-condition: màn không đổi dữ liệu (mở, làm mới, lọc không đổi trạng thái HĐ/hồ sơ) | Post-Condition; Mục 8 | Logic |

### 4. Field list
| Màn | Field | Kiểu | Bắt buộc | Ràng buộc / định dạng | Mapping | Message / khi lỗi |
|---|---|---|---|---|---|---|
| Danh sách | Tiêu đề "SH Bond" + nút quay lại | Header | Y | Quay lại màn trước | — | — |
| Danh sách | Icon lọc | Icon | N | Mở bottom sheet "Bộ lọc" | — | — |
| Danh sách | Icon ⓘ | Icon | N | Nội dung chưa có (TBU Q03) | — | — |
| Danh sách | Badge tab "Xác nhận" | Badge số (đỏ) | C | Số nguyên ≥ 1; = 0 ẩn; đếm theo HĐ | data.totalCount – A1 | — |
| Card | Mã trái phiếu | Text (cam) | Y | VD BHB291193 | data.content[].symbol | — |
| Card | Thời gian đầu tư | Chip | Y | "n tháng" | data.content[].term / termType | — |
| Card | Trạng thái HĐ | Status chip | Y | "Hiệu lực" | data.content[].status | — |
| Card | Tổ chức phát hành | Text | Y | Tên TCPH | issuerName (A1 chưa có – Q-03) | — |
| Card | Số hợp đồng | Text | Y | VD 20240610BHBV12501.0000000 | data.content[].orderNo | — |
| Card | Ngày đáo hạn | Date | Y | dd/MM/yyyy | data.content[].matureDate | — |
| Card | Tổng tiền đầu tư | Number (cam) | Y | #.### VND | data.content[].totalInvestmentAmount | — |
| Card | Lợi suất | Percentage | Y | x,xx%/năm | data.content[].rate | — |
| Card | Lợi tức dự kiến | Number (xanh) | Y | #.### VND | data.content[].interest | — |
| Card | Banner cảnh báo | Banner (icon ⚠) | Y | "Hợp đồng có hồ sơ cần xác nhận" | status = chờ xác nhận | — |
| Card | Bấm card | Area | Y | → CONTRACT.APP.02 tab Xác nhận | A2: id = data.content[].id | — |
| Màn trống | Hình minh họa + text | Empty state | C | "Hiện tại không có hợp đồng nào cần xác nhận" | data.totalCount = 0 | SBOND.DOCCONFIRM.I02 |
| Bộ lọc | Tiêu đề "Bộ lọc" + nút quay lại | Header | Y | Đóng, không áp dụng thay đổi | — | — |
| Bộ lọc | Mã trái phiếu | Textbox | N | Placeholder "Mã trái phiếu" (Figma: "Nhập mã trái phiếu" – Q-08); contains, không phân biệt hoa/thường; trống = không lọc | A1: symbol | — |
| Bộ lọc | Tổ chức phát hành | Dropdown chọn nhiều | N | Mặc định "Tất cả tổ chức" | A1: issuerCode[]; danh sách A0 issuers[] | — |
| Bộ lọc | Nút "Đặt lại" | Button | Y | Về mặc định, chưa gọi API | — | — |
| Bộ lọc | Nút "Lưu" | Button | Y | Đóng bottom sheet, gọi A1 | A1 | — |

### 5. Q&A
| Q ID | Câu hỏi | Lý do | Mức | Giả định đề xuất | TC liên quan |
|---|---|---|---|---|---|
| Q-01 | Sắp xếp card chốt thế nào (SRS TBU Q06, dự thảo: ngày đáo hạn gần nhất trước)? 2 HĐ cùng ngày đáo hạn thì tiêu chí phụ là gì? | Chưa chốt, ảnh hưởng Expected thứ tự | Major | Ngày đáo hạn tăng dần; cùng ngày → không kiểm thứ tự phụ | TC_XNHD_DS_013, TC_XNHD_DS_014, TC_XNHD_API_004 |
| Q-02 | Nội dung icon ⓘ trên header (TBU Q03)? | Chưa có nội dung | Minor | Để Blocked đến khi có nội dung | TC_XNHD_NAV_006 |
| Q-03 | A1 chưa có issuerName (ghi chú Dev: A2 mới có). Card lấy TCPH từ đâu? | Card bắt buộc hiển thị TCPH | Major | BE bổ sung issuerName vào A1 | TC_XNHD_DS_005, TC_XNHD_API_002 |
| Q-04 | Danh sách có phân trang không? SRS không nêu page/size cho A1 | Ảnh hưởng case nhiều HĐ | Major | Không phân trang, A1 trả đủ toàn bộ HĐ cần xác nhận | TC_XNHD_DS_015 |
| Q-05 | Khi đang lọc, badge = tổng số HĐ cần xác nhận hay số HĐ sau lọc (mapping totalCount của A1 có tham số lọc)? | Badge có thể giảm sai khi lọc | Major | Badge luôn = tổng HĐ cần xác nhận, không đổi theo bộ lọc | TC_XNHD_BG_005 |
| Q-06 | Badge có hiển thị ngay khi KH vào S-Bond ở tab khác (DS chào bán/Nắm giữ) chưa mở tab Xác nhận không? API nào cấp số đếm? | SRS chỉ nói cập nhật mỗi lần tải danh sách | Minor | Có hiển thị khi vào S-Bond | TC_XNHD_BG_006 |
| Q-07 | Lọc không có kết quả hiển thị màn trống I02 hay text khác? | SRS chỉ định nghĩa I02 cho totalCount = 0 | Minor | Dùng I02 | TC_XNHD_BL_005 |
| Q-08 | Placeholder ô Mã trái phiếu: SRS "Mã trái phiếu", Figma "Nhập mã trái phiếu"; label Figma "Mã Trái phiếu" | SRS và Figma lệch | Minor | Theo Figma | TC_XNHD_UI_004 |
| Q-09 | Ô Mã trái phiếu có trim khoảng trắng đầu/cuối, giới hạn độ dài, chặn ký tự đặc biệt không? | SRS chỉ ghi "text tự do" | Minor | Có trim; không giới hạn ký tự ở FE | TC_XNHD_MTP_005, TC_XNHD_MTP_006, TC_XNHD_MTP_007 |
| Q-10 | Dropdown TCPH: hiển thị gì khi chọn nhiều (VD "2 tổ chức")? Bỏ chọn hết → về "Tất cả tổ chức"? Có ô tìm kiếm không? | SRS chưa mô tả hành vi chọn nhiều | Minor | Bỏ chọn hết → "Tất cả tổ chức"; hiển thị danh sách tên đã chọn | TC_XNHD_TCPH_003, TC_XNHD_TCPH_004 |
| Q-11 | Bộ lọc có giữ khi kéo làm mới, sau xác nhận thành công, khi rời tab rồi quay lại không? | Ảnh hưởng dữ liệu sau thao tác | Major | Giữ khi kéo làm mới và sau xác nhận; về mặc định khi rời S-Bond | TC_XNHD_DS_017, TC_XNHD_BL_007 |
| Q-12 | Icon lọc có đổi trạng thái khi đang lọc không? | Không có trong SRS (màn Lịch sử có) | Minor | Không kiểm | — |
| Q-13 | Mở từ noti Bond_03: áp dụng bộ lọc mặc định? Có cuộn/đánh dấu HĐ trong noti không? Noti của HĐ đã quá hạn thì sao? Chưa đăng nhập thì sao? | SRS chỉ ghi điều hướng: Xác nhận | Major | Mở tab Xác nhận với bộ lọc mặc định; HĐ quá hạn không có trong danh sách; chưa đăng nhập → đăng nhập xong vào tab Xác nhận | TC_XNHD_NAV_002, TC_XNHD_NAV_003, TC_XNHD_PQ_003 |
| Q-14 | 1 HĐ cùng lúc có HĐ Leg 2 + Phiếu lệnh đặt bán cần xác nhận: 1 card hay 2 card? Card có "nhãn loại xác nhận" phân biệt không (2.2 #10)? | SRS #10 ghi "Nhãn loại xác nhận / cảnh báo" nhưng chỉ có 1 text | Major | 1 card/HĐ, badge đếm 1; chỉ hiện text cảnh báo cố định | TC_XNHD_BG_003, TC_XNHD_NV_010 |
| Q-15 | T+3, E-5 tính theo ngày làm việc hay ngày lịch? Job T3_DOC_REQUEST/E5_REPRICE chạy lúc mấy giờ? | Quyết định biên xuất hiện | Major | Ngày làm việc; HĐ xuất hiện sau khi job ngày đó chạy | TC_XNHD_NV_001, TC_XNHD_NV_002, TC_XNHD_NV_003, TC_XNHD_NV_004 |
| Q-16 | HĐ Leg 2 chưa xác nhận đến E-5 ("gộp với Phiếu lệnh đặt bán"): KH xác nhận 1 lần cả 2 hồ sơ hay từng hồ sơ? | Ảnh hưởng khi nào HĐ rời tab | Major | Vẫn 1 card; HĐ chỉ rời tab khi cả 2 hồ sơ Đủ | TC_XNHD_NV_010, TC_XNHD_CT_005 |
| Q-17 | COT 11h ngày E: đúng 11:00:00 còn trong danh sách không? HĐ tự biến mất hay chỉ mất ở lần tải sau? | Biên COT | Major | Trước 11:00:00 còn; từ 11:00:00 không trả; mất ở lần tải sau (A3) | TC_XNHD_NV_005, TC_XNHD_NV_006, TC_XNHD_NV_007 |
| Q-18 | Lợi suất có > 2 chữ số thập phân thì làm tròn hay cắt? termType khác tháng (ngày/năm) hiển thị thế nào? | SRS chỉ ghi "x,xx%/năm", "n tháng" | Minor | Làm tròn 2 số; chỉ test termType tháng | TC_XNHD_DS_009 |
| Q-19 | Sau xác nhận thành công: SRS ghi "quay về tab Xác nhận, tải lại", Figma màn thành công chỉ có "Về Danh mục nắm giữ" / "Về Danh sách chào bán". Hành vi đúng? | SRS và Figma lệch | Major | Theo Figma; khi KH vào lại tab Xác nhận, danh sách tải lại không còn HĐ đã xác nhận đủ | TC_XNHD_CT_004 |
| Q-20 | Lỗi tài liệu: 2.1 #2 tham chiếu "mục 3.4" (đúng là 2.4); Mục 7 có 2 dòng STT 4; tham số "status chờ xác nhận" của A1 chưa nêu tên/giá trị | Cần sửa SRS; cần cho TC API | Minor | BE tự lọc HĐ cần xác nhận, không cần tham số status | TC_XNHD_API_002 |
| Q-21 | A1 trả mã lỗi gì khi issuerCode không tồn tại / symbol quá dài? | Chưa có error code | Minor | HTTP 200, danh sách rỗng, totalCount = 0 | TC_XNHD_API_008, TC_XNHD_API_009 |
| Q-22 | A0 issuers[] trả tất cả TCPH hay chỉ TCPH của các HĐ cần xác nhận của KH? | Ảnh hưởng dữ liệu dropdown | Minor | Chỉ TCPH của các HĐ cần xác nhận | TC_XNHD_TCPH_001, TC_XNHD_API_001 |
| Q-23 | Mất mạng / timeout khi mở tab: hiển thị popup nào? | SRS chỉ có popup lỗi chuẩn | Minor | Popup lỗi chuẩn "Có lỗi xảy ra. Vui lòng thử lại sau" | TC_XNHD_ERR_001, TC_XNHD_ERR_006 |
| Q-24 | Mapping mã lỗi → text popup: BND-901 = "Hệ thống đang bận", BND-999 = "Có lỗi xảy ra...", AUTH_001 = "Phiên đăng nhập..." đúng không? | SRS gộp chung 1 dòng | Minor | Như câu hỏi | TC_XNHD_ERR_002, TC_XNHD_ERR_003, TC_XNHD_ERR_004 |
| Q-25 | Đóng bottom sheet Chi tiết HĐ (không xác nhận) có gọi lại A1 không? | Không có trong SRS | Minor | Không gọi lại, danh sách giữ nguyên | TC_XNHD_CT_003 |
| Q-26 | Lọc kết hợp Mã TP + TCPH là AND? | SRS không nêu | Minor | AND | TC_XNHD_BL_004 |

### 6. Rủi ro chính
| # | Rủi ro | Tác động | Mức |
|---|---|---|---|
| 1 | Mốc T+3 / E-5 / COT 11h ngày E chưa định nghĩa ngày làm việc/giờ job; giờ thiết bị thay vì BE | HĐ hiện muộn/sớm hoặc vẫn hiện sau COT → KH xác nhận quá hạn hoặc lỡ hạn → HĐ Hết hiệu lực, chuyển Outright | High |
| 2 | Phạm vi danh sách sai (HĐ Đủ hồ sơ, HĐ đã hủy/revert, hồ sơ Chờ gửi KH vẫn hiện) | KH xác nhận nhầm HĐ không còn hiệu lực; khiếu nại | High |
| 3 | Badge lệch số HĐ (đếm theo hồ sơ thay vì HĐ, không cập nhật sau xác nhận, đổi theo bộ lọc) | KH bỏ sót HĐ cần xác nhận | High |
| 4 | Bấm card mở sai HĐ (id lệch) / IDOR qua A2 | KH xác nhận nhầm HĐ; lộ HĐ của KH khác | High |
| 5 | SRS–Figma lệch (luồng sau xác nhận, placeholder) và field TCPH chưa có ở A1 | FE/BE làm khác nhau, card thiếu TCPH | Medium |

## B2. Chiến lược test
- **In-scope:** Danh sách card (hiển thị, định dạng, sắp xếp), badge, màn trống, kéo làm mới, bottom sheet Bộ lọc (Mã TP, TCPH, Đặt lại/Lưu), bấm card → Chi tiết HĐ tab Xác nhận, cập nhật sau xác nhận, phạm vi/thời hạn hiển thị (T+3, E-5, COT 11h ngày E, edge case), V01, popup lỗi chuẩn, tracking event, API A0/A1/A2.
- **Out-of-scope:** Màn Web (DOCCONFIRM.WEB.01); nội dung Chi tiết HĐ (CONTRACT.APP.02) và luồng Xem HĐ/OTP (DOCCONFIRM.APP.02) – chỉ dùng để tạo bước; nội dung noti/email Bond_03/05/07 (chỉ kiểm điều hướng Bond_03); nội dung icon ⓘ (Q-02); logic job T3/E5 và reprice; hiệu năng.
- **Loại test:** Functional, UI, API, Integration (job BE → App, Portal → App), Security cơ bản (token, IDOR, đổi tài khoản, injection), Error guessing (mạng, double-tap, giờ thiết bị, dữ liệu dài).

| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC (App / API) |
|---|---|---|---|---|
| BR-01 | UC | EG | High | 7 / 0 |
| BR-02 | DT | EP, BVA | High | 7 / 1 |
| BR-03 | BVA | EP, EG | High | 9 / 0 |
| BR-04 | EP | BVA, EG | High | 13 / 1 |
| BR-05 | EP | BVA | Medium | 2 / 1 |
| BR-06 | UC | BVA, EG | Medium | 5 / 1 |
| BR-07 | EG | UC, EP | High | 5 / 1 |
| BR-08 | ST | UC | High | 4 / 0 |
| BR-09 | BVA | ST, DT | High | 7 / 0 |
| BR-10 | ST | EG | High | 2 / 0 |
| BR-11 | UC | EG | Medium | 3 / 0 |
| BR-12 | UC | EG | Medium | 3 / 0 |
| BR-13 | EG | EP, BVA | Medium | 10 / 3 |
| BR-14 | EP | EG, PW | Medium | 7 / 3 |
| BR-15 | UC | EG, EP | Medium | 6 / 0 |
| BR-16 | UC | EG | Low | 3 / 0 |
| BR-17 | BVA | EG | High | 4 / 0 |
| BR-18 | EG | EP, UC | High | 6 / 5 |
| BR-19 | EG | EP | High | 7 / 0 |
| BR-20 | ST | BVA, DT | High | 3 / 0 |
| BR-21 | UC | — | Low | 4 / 0 |
| BR-22 | EP | — | High | 0 / 2 |
| BR-23 | EG | — | Low | 1 / 1 |

- **Môi trường:** UAT; iOS + Android (1 máy màn hẹp 360dp); Proxyman/Charles (xem, sửa, mock request/response); Postman; công cụ debug analytics; tài khoản BO trên SHBond Portal xem trạng thái HĐ/hồ sơ; quyền chạy job SBOND.JOB.T3_DOC_REQUEST, SBOND.JOB.E5_REPRICE và đặt ngày/giờ nghiệp vụ BE (nhóm NV).
- **Exit criteria:** 100% TC High đã chạy; 0 bug Critical/Major mở; Q&A Major có trả lời hoặc PO chấp nhận giả định; RTM 100% BR có TC Pass.

### Dữ liệu cần có trên môi trường
| Mã | Điều kiện dữ liệu cần có trên UAT | Dùng cho |
|---|---|---|
| PC-01 | TK có ≥ 3 HĐ Hiệu lực đang có hồ sơ cần xác nhận, thuộc ≥ 2 mã TP và ≥ 2 TCPH khác nhau, ngày đáo hạn khác nhau; có thêm ≥ 1 HĐ Hiệu lực Đủ hồ sơ và ≥ 1 HĐ Hoàn thành | Phần lớn TC App |
| PC-04 | TK không có HĐ nào cần xác nhận | Màn trống, đổi tài khoản |
| PC-05 | TK có HĐ mới đặt mua thành công; BO chạy được job T3/E5 và đặt ngày/giờ BE | Nhóm NV (thời hạn) |
| Điều kiện riêng | Ghi ngay trong cột Precondition của TC (VD "TK có 12 HĐ cần xác nhận") | Từng TC |

### Precondition / thao tác dùng chung
| Mã | Nội dung |
|---|---|
| N | Ngày/giờ nghiệp vụ hiện tại của BE. T = ngày đặt mua thành công của HĐ; E = ngày đáo hạn HĐ |
| PC-01 | KH đã đăng nhập SHSmart App (UAT), đang ở S-Bond; TK thỏa điều kiện dữ liệu PC-01 |
| PC-02 | PC-01 + đang ở tab "Xác nhận", bộ lọc mặc định |
| PC-03 | PC-02 + đã bấm icon lọc, bottom sheet "Bộ lọc" đang mở |
| PC-04 | KH không có HĐ cần xác nhận đã đăng nhập, đang ở S-Bond |
| PC-05 | KH có HĐ mới đặt mua thành công ngày T đã đăng nhập; BO đặt được ngày/giờ BE và chạy job SBOND.JOB.T3_DOC_REQUEST, SBOND.JOB.E5_REPRICE |
| SOP-01 | Xác nhận hồ sơ: bấm card → "Xem hợp đồng" → đọc hết HĐ → tích ô đồng ý → "Xác nhận" → nhập OTP đúng → màn "Xác nhận thành công" (DOCCONFIRM.APP.02) |
| PC-API | Postman, UAT; Header Authorization: Bearer <token KH của PC-01>; A0 = GET /api/v1/bond/term/app/appendix/filters; A1 = GET /api/v1/bond/term/app/appendix; A2 = GET /api/v1/bond/term/app/appendix/detail |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên |
|---|---|---|---|
| Mã trái phiếu (BIZ-34) | Mã đầy đủ; cụm giữa mã; chữ thường; trống | Không khớp mã nào; ký tự đặc biệt / SQLi; emoji | 1 ký tự; khoảng trắng đầu/cuối; chỉ khoảng trắng |
| TCPH (BIZ-34) | Tất cả tổ chức; 1 TCPH; 2 TCPH | issuerCode không tồn tại (API) | Bỏ chọn hết; danh sách A0 rỗng |
| Badge (BIZ-27) | 1; 3; ≥ 10 | totalCount = 0 → ẩn | 0 / 1; 2 chữ số |
| Mốc HĐ Leg 2 (BIZ-29) | N = T+3 đến trước E-5 | N = T+2 (Chờ gửi KH) | T+2 (không) / T+3 (có); E-6 (có) |
| Mốc Phiếu lệnh đặt bán (BIZ-29) | N = E-5 đến trước 11:00 ngày E | N = ngày E từ 11:00 | E-6 (chưa có PLĐB) / E-5 (có); 10:59 (có) / 11:00 (không) |
| Card – số tiền, lợi suất | Tổng tiền, lợi tức #.### VND; 8,50%/năm | — | ≥ 1 tỷ; lợi suất 1 chữ số thập phân |
| Ngày đáo hạn | dd/MM/yyyy | — | Ngày/tháng 1 chữ số |

### Decision table – HĐ có hiển thị trong tab Xác nhận (BIZ-27, BIZ-29, Mục 8, Mục 9)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 | R6 | R7 |
|---|---|---|---|---|---|---|---|
| Trạng thái HĐ | Hiệu lực | Hiệu lực | Hiệu lực | Hiệu lực | Hiệu lực | Hoàn thành / Hết hiệu lực / đã revert | Hiệu lực |
| Trạng thái hồ sơ | Chờ gửi KH (N < T+3) | Thiếu hồ sơ (T+3 ≤ N < E-5) | Thiếu hồ sơ – PLĐB (E-5 ≤ N < 11:00 ngày E) | Thiếu hồ sơ (N ≥ 11:00 ngày E) | Đủ hồ sơ | Bất kỳ | Đủ hồ sơ → E-5 version mới PLĐB |
| → Hiển thị | Không | Có | Có | Không | Không | Không | Có (lại) |
| TC | NV_001 | NV_002, NV_003 | NV_004, NV_005 | NV_006, NV_007, NV_008 | DS_001, CT_004 | NV_009, NV_013 | NV_004 |

### State transition – hồ sơ của HĐ trong tab Xác nhận (Mục 8, BIZ-30, Mục 9)
| Trạng thái \ Sự kiện | Tới mốc T+3 / E-5 (job) | Xác nhận OTP thành công | Quá hạn (COT 11h ngày E) | Hủy GD (revert) | E-5 version mới PLĐB |
|---|---|---|---|---|---|
| Chờ gửi KH xác nhận (ẩn) | → Thiếu hồ sơ (hiện) | ✖ | ✖ | → Ẩn | ✖ |
| Thiếu hồ sơ (hiện) | — | → Đủ hồ sơ (ẩn) | → HĐ Hết hiệu lực (ẩn) | → Ẩn | → Thiếu hồ sơ version mới (hiện) |
| Đủ hồ sơ (ẩn) | E-5: → Thiếu hồ sơ PLĐB (hiện) | ✖ | — | → Ẩn | → Thiếu hồ sơ (hiện lại) |

### Use case
- Luồng chính: mở tab (tab dưới / noti Bond_03 / "Kiểm tra") → A0 + A1 → badge + card → bấm card → Chi tiết HĐ tab Xác nhận → Xem HĐ + OTP → tải lại, HĐ rời danh sách, badge giảm.
- Luồng phụ: kéo làm mới; lọc Mã TP / TCPH / kết hợp; Đặt lại; đóng bộ lọc không lưu; lọc không kết quả; icon ⓘ.
- Ngoại lệ: không có HĐ (màn trống); HĐ quá hạn khi đang ở màn (A3); hủy GD revert; lỗi hệ thống; hết phiên; mất mạng; IDOR.

### Error guessing áp dụng
- Double-tap card / Lưu · mất mạng khi mở tab, khi kéo làm mới · API chậm · Số HĐ, tên TCPH dài, số tiền lớn trên màn hẹp · đổi giờ thiết bị quanh COT 11h · đổi tài khoản cùng máy · sửa request (IDOR id A2, SQLi symbol) · token hết hạn giữa thao tác · bấm noti khi đã đăng xuất.

## B4. Test case
### 4.1 App (90 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_XNHD_UI_001 | BR-16, BR-03 | Xác nhận HĐ – Layout | Kiểm tra layout mặc định màn Xác nhận khi có HĐ cần xác nhận | PC-02 | - Quan sát màn hình | — | - Header: "<", "SH Bond", icon lọc, icon ⓘ<br>- Danh sách card HĐ; thanh tab dưới 4 tab: DS chào bán, Nắm giữ, Xác nhận, Lịch sử<br>- Tab "Xác nhận" active (màu cam), có badge số đỏ | Low | UI | EG | Đối chiếu Figma màn 1 |
| TC_XNHD_UI_002 | BR-04 | Xác nhận HĐ – Layout | Kiểm tra bố cục 1 card HĐ cần xác nhận | PC-02 | - Quan sát 1 card bất kỳ | — | - Dòng 1: Mã TP (cam) + chip "n tháng" bên trái; chip "Hiệu lực" bên phải<br>- Dòng 2–3: tên TCPH; Số HĐ (chữ đậm); lưới 2 cột: Ngày đáo hạn / Tổng tiền đầu tư, Lợi suất / Lợi tức dự kiến<br>- Cuối card: banner ⚠ "Hợp đồng có hồ sơ cần xác nhận" | Low | UI | EG | Figma màn 1 |
| TC_XNHD_UI_003 | BR-06 | Xác nhận HĐ – Layout | Kiểm tra layout màn trống khi KH không có HĐ cần xác nhận | PC-04 | - Mở tab "Xác nhận" | — | - Hình minh họa + "Hiện tại không có hợp đồng nào cần xác nhận" ở giữa màn<br>- Header và thanh tab dưới vẫn hiển thị; tab "Xác nhận" không có badge | Low | UI | EG | Figma màn trống |
| TC_XNHD_UI_004 | BR-12, BR-13, BR-14 | Xác nhận HĐ – Bộ lọc | Kiểm tra layout mặc định bottom sheet Bộ lọc | PC-02 | - Bấm icon lọc | — | - Header "<" + "Bộ lọc"; nền phía sau làm mờ<br>- Ô "Mã Trái phiếu" trống, placeholder "Nhập mã trái phiếu"; "Tổ chức phát hành" = "Tất cả tổ chức"<br>- Nút "Đặt lại" (viền) và "Lưu" (cam) | Low | UI | EG | Placeholder SRS/Figma lệch – Q-08 |
| TC_XNHD_UI_005 | BR-04 | Xác nhận HĐ – Layout | Kiểm tra card khi số tiền rất lớn trên màn hẹp | PC-02; máy Android 360dp; TK có HĐ Tổng tiền đầu tư từ 10 tỷ trở lên | - Xem card HĐ đó | — | - Tổng tiền đầu tư, Lợi tức dự kiến hiện đủ trên 1 dòng, không bị cắt, không chồng cột bên cạnh | Low | UI | EG |  |
| TC_XNHD_UI_006 | BR-04 | Xác nhận HĐ – Layout | Kiểm tra card khi tên TCPH dài | PC-02; TK có HĐ của TCPH tên dài hơn 1 dòng | - Xem card HĐ đó | — | - Tên TCPH xuống dòng hoặc cắt "..." gọn trong card, không đè lên Số HĐ<br>- Số HĐ vẫn hiện đủ | Low | UI | EG | [GIẢ ĐỊNH] cách hiển thị tên dài chưa có trong SRS |
| TC_XNHD_NAV_001 | BR-01 | Xác nhận HĐ – Điều hướng | Kiểm tra vào tab Xác nhận từ thanh tab dưới | PC-01; đang ở tab "DS chào bán" | - Bấm tab "Xác nhận" | — | - Tab "Xác nhận" active<br>- Hiện danh sách card HĐ cần xác nhận của TK | High | Positive | UC |  |
| TC_XNHD_NAV_002 | BR-01 | Xác nhận HĐ – Điều hướng | Kiểm tra mở màn khi bấm noti Bond_03 lúc app đang chạy nền | PC-05; BO đã chạy job T3 cho HĐ, KH nhận noti Bond_03; app đang chạy nền | - Bấm noti "[SH-Bond] Yêu cầu xác nhận" | — | - Mở S-Bond, tab "Xác nhận" active<br>- Danh sách có card HĐ có Số HĐ trong noti | High | Positive | UC | [GIẢ ĐỊNH] – Q-13 |
| TC_XNHD_NAV_003 | BR-01 | Xác nhận HĐ – Điều hướng | Kiểm tra mở màn khi bấm noti Bond_03 lúc app đã tắt hẳn | Như NAV_002; app đã tắt hẳn; phiên đăng nhập còn hiệu lực | - Bấm noti Bond_03 | — | - App khởi động và vào thẳng tab "Xác nhận"<br>- Có card HĐ trong noti | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-13 |
| TC_XNHD_NAV_004 | BR-01 | Xác nhận HĐ – Điều hướng | Kiểm tra mở màn từ nút "Kiểm tra" ở Danh mục HĐ | PC-01; đang ở tab "Nắm giữ" – Danh mục HĐ, box "Hợp đồng cần xác nhận" hiển thị | - Bấm "Kiểm tra" | — | - Chuyển sang tab "Xác nhận"<br>- Số card bằng số HĐ ghi trong box | High | Positive | UC |  |
| TC_XNHD_NAV_005 | BR-16 | Xác nhận HĐ – Điều hướng | Kiểm tra nút quay lại trên header | PC-02 | - Bấm "<" | — | - Về màn trước đó (màn "Sản phẩm đầu tư") | Low | Positive | UC | [GIẢ ĐỊNH] màn trước |
| TC_XNHD_NAV_006 | BR-16 | Xác nhận HĐ – Điều hướng | Kiểm tra bấm icon ⓘ | PC-02 | - Bấm icon ⓘ, rồi đóng lại | — | - Mở nội dung thông tin màn Xác nhận<br>- Đóng lại, danh sách giữ nguyên | Low | Positive | UC | Chưa có nội dung – Q-02; để Blocked |
| TC_XNHD_NAV_007 | BR-01 | Xác nhận HĐ – Điều hướng | Kiểm tra danh sách tải lại khi chuyển tab rồi quay lại | PC-02 | - Bấm tab "Nắm giữ"<br>- BO cho 1 HĐ khác của TK tới mốc xác nhận (chạy job)<br>- Bấm lại tab "Xác nhận" | — | - Danh sách có thêm card HĐ vừa tới mốc<br>- Badge tăng 1 | Medium | Positive | UC |  |
| TC_XNHD_DS_001 | BR-02 | Xác nhận HĐ – Danh sách | Kiểm tra danh sách chỉ gồm HĐ có hồ sơ cần xác nhận | PC-01 | - Mở tab "Xác nhận"<br>- Đối chiếu danh sách với danh sách HĐ trên Portal | — | - Hiện đủ các HĐ Hiệu lực đang Thiếu hồ sơ<br>- Không có HĐ Hiệu lực Đủ hồ sơ, không có HĐ Hoàn thành | High | Positive | DT |  |
| TC_XNHD_DS_002 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra banner cảnh báo trên card | PC-02 | - Xem lần lượt các card | — | - Card nào cũng có banner ⚠ "Hợp đồng có hồ sơ cần xác nhận" | Medium | Positive | EP |  |
| TC_XNHD_DS_003 | BR-04, BR-02 | Xác nhận HĐ – Danh sách | Kiểm tra chip trạng thái trên card | PC-02 | - Xem chip trạng thái các card | — | - Card nào cũng là chip "Hiệu lực" (chữ xanh) | Medium | Positive | EP |  |
| TC_XNHD_DS_004 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra Mã TP và Thời gian đầu tư trên card | PC-02 | - So 1 card với HĐ tương ứng trên Portal | — | - Mã TP đúng, màu cam (VD "BHB291193")<br>- Chip kỳ hạn đúng số tháng của HĐ (VD "12 tháng") | Medium | Positive | EP |  |
| TC_XNHD_DS_005 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra tên TCPH trên card | PC-02 | - So TCPH của 1 card với HĐ trên Portal | — | - Hiện đúng tên đầy đủ TCPH của mã TP (VD "Công ty Bảo hiểm nhân thọ ABC") | Medium | Positive | EP | A1 chưa có issuerName – Q-03 |
| TC_XNHD_DS_006 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra Số HĐ trên card | PC-02 | - So Số HĐ của 1 card với HĐ trên Portal | — | - Đúng Số HĐ, đủ ký tự, không cắt (VD "20240610BHBV12501.0000000") | High | Positive | EP |  |
| TC_XNHD_DS_007 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra định dạng Ngày đáo hạn khi ngày, tháng 1 chữ số | PC-02; TK có HĐ đáo hạn ngày 1–9 hoặc tháng 1–9 | - Xem card HĐ đó | — | - Dạng dd/MM/yyyy, đủ 2 chữ số (VD "05/06/2027")<br>- Đúng ngày đáo hạn trên Portal | Medium | Boundary | BVA |  |
| TC_XNHD_DS_008 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra Tổng tiền đầu tư và Lợi tức dự kiến trên card | PC-02 | - So 2 số tiền của 1 card với HĐ trên Portal | — | - Tổng tiền đầu tư dạng "#.### VND" màu cam (VD "435.339.324 VND")<br>- Lợi tức dự kiến dạng "#.### VND" màu xanh (VD "10.017.942 VND"); số khớp Portal | High | Positive | EP |  |
| TC_XNHD_DS_009 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra định dạng Lợi suất trên card | PC-02; TK có HĐ lợi suất 8,5% | - Xem card HĐ đó | — | - Hiện "8,50%/năm" (dấu phẩy thập phân, 2 chữ số sau dấu phẩy) | Medium | Boundary | BVA | [GIẢ ĐỊNH] làm tròn – Q-18 |
| TC_XNHD_DS_010 | BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra số tiền trên card khi từ 1 tỷ trở lên | PC-02; TK có HĐ Tổng tiền đầu tư từ 1 tỷ trở lên | - Xem card HĐ đó | — | - Dấu chấm ngăn cách mỗi 3 chữ số (VD "1.250.000.000 VND"), không có phần lẻ | Low | Boundary | BVA |  |
| TC_XNHD_DS_011 | BR-06 | Xác nhận HĐ – Danh sách | Kiểm tra màn trống khi KH không có HĐ cần xác nhận | PC-04 | - Mở tab "Xác nhận" | — | - Hiện "Hiện tại không có hợp đồng nào cần xác nhận"<br>- Không có card nào | Medium | Positive | UC |  |
| TC_XNHD_DS_012 | BR-06, BR-02 | Xác nhận HĐ – Danh sách | Kiểm tra màn trống khi mọi HĐ Hiệu lực đều Đủ hồ sơ | KH chỉ có HĐ Hiệu lực Đủ hồ sơ đã đăng nhập, đang ở S-Bond | - Mở tab "Xác nhận" | — | - Hiện màn trống "Hiện tại không có hợp đồng nào cần xác nhận"<br>- Tab không có badge | Medium | Negative | EP |  |
| TC_XNHD_DS_013 | BR-05 | Xác nhận HĐ – Danh sách | Kiểm tra thứ tự card theo ngày đáo hạn | PC-02 | - Đọc Ngày đáo hạn các card từ trên xuống | — | - Ngày đáo hạn gần nhất ở trên cùng, tăng dần xuống dưới | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-01 |
| TC_XNHD_DS_014 | BR-05 | Xác nhận HĐ – Danh sách | Kiểm tra thứ tự khi 2 HĐ cùng ngày đáo hạn | PC-02; TK có 2 HĐ cần xác nhận cùng ngày đáo hạn | - Mở tab "Xác nhận" 3 lần (kéo làm mới) | — | - 2 HĐ nằm liền nhau, thứ tự giữ ổn định giữa các lần tải | Low | Boundary | BVA | Tiêu chí phụ – Q-01 |
| TC_XNHD_DS_015 | BR-03, BR-04 | Xác nhận HĐ – Danh sách | Kiểm tra danh sách khi có nhiều HĐ cần xác nhận | KH có 12 HĐ cần xác nhận đã đăng nhập | - Mở tab "Xác nhận"<br>- Cuộn đến cuối danh sách | — | - Hiện đủ 12 card, không trùng, không thiếu<br>- Badge "12" | Medium | Boundary | BVA | [GIẢ ĐỊNH] không phân trang – Q-04 |
| TC_XNHD_DS_016 | BR-11 | Xác nhận HĐ – Danh sách | Kiểm tra kéo xuống để làm mới khi có HĐ mới tới mốc | PC-02 | - BO cho 1 HĐ khác của TK tới mốc xác nhận (chạy job)<br>- Kéo danh sách xuống để làm mới | — | - Hiện biểu tượng loading rồi tải lại<br>- Có thêm card HĐ mới; badge tăng 1 | Medium | Positive | UC |  |
| TC_XNHD_DS_017 | BR-11, BR-13 | Xác nhận HĐ – Danh sách | Kiểm tra kéo làm mới khi đang lọc | PC-02; đã lọc Mã TP = BHB | - Kéo danh sách xuống để làm mới | — | - Danh sách sau làm mới vẫn chỉ có HĐ mã chứa "BHB" | Medium | Positive | UC | [GIẢ ĐỊNH] giữ bộ lọc – Q-11 |
| TC_XNHD_DS_018 | BR-23 | Xác nhận HĐ – Danh sách | Kiểm tra mở và làm mới màn không đổi dữ liệu HĐ | PC-02 | - Kéo làm mới 3 lần, lọc rồi Đặt lại + Lưu<br>- Xem trạng thái HĐ, hồ sơ trên Portal | — | - Trạng thái HĐ (Hiệu lực) và hồ sơ (Thiếu hồ sơ) không đổi<br>- Không phát sinh noti/email mới | Low | Negative | EG |  |
| TC_XNHD_BG_001 | BR-03 | Xác nhận HĐ – Badge | Kiểm tra badge bằng số HĐ cần xác nhận | PC-02; TK có đúng 3 HĐ cần xác nhận | - Xem badge trên tab "Xác nhận"<br>- Đếm số card | — | - Badge đỏ hiện "3", bằng số card | High | Positive | EP |  |
| TC_XNHD_BG_002 | BR-03, BR-06 | Xác nhận HĐ – Badge | Kiểm tra badge ẩn khi không có HĐ cần xác nhận | PC-04 | - Mở tab "Xác nhận" | — | - Không có badge trên tab "Xác nhận" (không hiện "0") | Medium | Boundary | BVA |  |
| TC_XNHD_BG_003 | BR-03 | Xác nhận HĐ – Badge | Kiểm tra badge đếm theo HĐ khi 1 HĐ có 2 hồ sơ cần xác nhận | KH có đúng 1 HĐ đang Thiếu cả HĐ Leg 2 và Phiếu lệnh đặt bán (N ≥ E-5) | - Mở tab "Xác nhận" | — | - Badge "1"; chỉ 1 card cho HĐ đó | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-14 |
| TC_XNHD_BG_004 | BR-03, BR-08 | Xác nhận HĐ – Badge | Kiểm tra badge giảm sau khi xác nhận thành công | PC-02; TK có 3 HĐ cần xác nhận, mỗi HĐ 1 hồ sơ | - Xác nhận 1 HĐ theo SOP-01<br>- Vào lại tab "Xác nhận" | — | - Badge "2" | High | Positive | ST |  |
| TC_XNHD_BG_005 | BR-03, BR-15 | Xác nhận HĐ – Badge | Kiểm tra badge khi đang lọc | PC-03; TK có 3 HĐ, 1 HĐ mã BHB291193 | - Nhập Mã TP = BHB291193, bấm "Lưu" | BHB291193 | - Danh sách còn 1 card<br>- Badge vẫn "3" | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-05 |
| TC_XNHD_BG_006 | BR-03 | Xác nhận HĐ – Badge | Kiểm tra badge khi số HĐ có 2 chữ số | KH có 12 HĐ cần xác nhận, đang ở tab "DS chào bán" | - Quan sát tab "Xác nhận" ở thanh tab dưới | — | - Badge hiện "12" đủ 2 chữ số, không bị cắt | Low | Boundary | BVA | Hiển thị khi chưa mở tab – Q-06 |
| TC_XNHD_CT_001 | BR-07 | Xác nhận HĐ – Bấm card / Xác nhận | Kiểm tra bấm card mở Chi tiết HĐ tại tab Xác nhận | PC-02 | - Bấm card đầu tiên | — | - Mở bottom sheet "Chi tiết hợp đồng", tab "Xác nhận" active<br>- Mã TP, Số HĐ trong tab trùng với card đã bấm | High | Positive | UC |  |
| TC_XNHD_CT_002 | BR-07 | Xác nhận HĐ – Bấm card / Xác nhận | Kiểm tra bấm card ở vị trí giữa danh sách mở đúng HĐ | PC-02; TK có ≥ 3 HĐ cần xác nhận | - Bấm card thứ 2<br>- Đóng, bấm card thứ 3 | — | - Mỗi lần mở đúng Mã TP, Số HĐ của card vừa bấm | High | Positive | EP |  |
| TC_XNHD_CT_003 | BR-07 | Xác nhận HĐ – Bấm card / Xác nhận | Kiểm tra đóng Chi tiết HĐ khi chưa xác nhận | PC-02 | - Bấm 1 card<br>- Bấm "<" trên bottom sheet Chi tiết hợp đồng | — | - Về danh sách, card và badge giữ nguyên | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-25 |
| TC_XNHD_CT_004 | BR-08 | Xác nhận HĐ – Bấm card / Xác nhận | Kiểm tra HĐ rời danh sách sau khi xác nhận đủ hồ sơ | PC-02; TK có HĐ chỉ 1 hồ sơ cần xác nhận | - Xác nhận HĐ đó theo SOP-01<br>- Từ màn thành công, vào lại tab "Xác nhận" | — | - Không còn card HĐ vừa xác nhận<br>- Portal: hồ sơ của HĐ = Đủ hồ sơ | High | Positive | ST | SRS/Figma lệch điều hướng sau thành công – Q-19 |
| TC_XNHD_CT_005 | BR-08 | Xác nhận HĐ – Bấm card / Xác nhận | Kiểm tra HĐ còn trong danh sách khi vẫn còn hồ sơ cần xác nhận | KH có 1 HĐ Thiếu cả HĐ Leg 2 và Phiếu lệnh đặt bán; đang ở tab "Xác nhận" | - Xác nhận 1 hồ sơ theo SOP-01<br>- Vào lại tab "Xác nhận" | — | - Card HĐ vẫn còn, vẫn có banner cảnh báo | Medium | Negative | ST | [GIẢ ĐỊNH] xác nhận từng hồ sơ – Q-16 |
| TC_XNHD_CT_006 | BR-08 | Xác nhận HĐ – Bấm card / Xác nhận | Kiểm tra thoát giữa luồng xác nhận khi chưa nhập OTP | PC-02 | - Bấm card → "Xem hợp đồng"<br>- Bấm "<" quay về đến tab "Xác nhận" | — | - Card HĐ vẫn còn, badge giữ nguyên<br>- Portal: hồ sơ vẫn Thiếu hồ sơ | Medium | Negative | UC |  |
| TC_XNHD_CT_007 | BR-07 | Xác nhận HĐ – Bấm card / Xác nhận | Kiểm tra double-tap card | PC-02 | - Bấm nhanh 2 lần vào 1 card | — | - Chỉ mở 1 bottom sheet Chi tiết hợp đồng | Low | Negative | EG |  |
| TC_XNHD_NV_001 | BR-02, BR-09 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ Leg 2 chưa hiển thị trước T+3 | PC-05; BO đặt N = T+2 | - Mở tab "Xác nhận" | VD T = 02/12/2026 → N = 04/12/2026 | - Không có card HĐ đó<br>- Portal: hồ sơ = Chờ gửi KH xác nhận | High | Boundary | BVA | [GIẢ ĐỊNH] ngày làm việc – Q-15 |
| TC_XNHD_NV_002 | BR-02, BR-09 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ Leg 2 hiển thị từ ngày T+3 | PC-05; BO đặt N = T+3, chạy job SBOND.JOB.T3_DOC_REQUEST | - Mở tab "Xác nhận" | VD T = 02/12/2026 → N = 07/12/2026 | - Có card HĐ đó; badge tăng 1<br>- KH nhận noti Bond_03 | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-15 |
| TC_XNHD_NV_003 | BR-09 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ Leg 2 chưa xác nhận vẫn hiển thị ngày E-6 | PC-05; HĐ chưa xác nhận Leg 2; BO đặt N = E-6 | - Mở tab "Xác nhận" | — | - Card HĐ vẫn còn | Medium | Boundary | BVA | [GIẢ ĐỊNH] – Q-15 |
| TC_XNHD_NV_004 | BR-09, BR-20 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ xuất hiện lại từ E-5 khi đã xác nhận Leg 2 | PC-05; HĐ đã xác nhận Leg 2 (Đủ hồ sơ, không có trong tab); BO đặt N = E-5, chạy job SBOND.JOB.E5_REPRICE | - Mở tab "Xác nhận" | — | - Card HĐ xuất hiện lại; badge tăng 1<br>- KH nhận noti Bond_03 | High | Boundary | ST | Edge case Mục 9 #2 |
| TC_XNHD_NV_005 | BR-09, BR-17 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ còn hiển thị ngày E trước 11:00 | PC-05; HĐ Thiếu Phiếu lệnh đặt bán; BO đặt N = ngày E 10:59 | - Mở tab "Xác nhận" | — | - Có card HĐ đó | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-17 |
| TC_XNHD_NV_006 | BR-09, BR-17, BR-20 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ không hiển thị từ 11:00 ngày E | PC-05; HĐ Thiếu Phiếu lệnh đặt bán; BO đặt N = ngày E 11:00 | - Mở tab "Xác nhận" | — | - Không có card HĐ đó; badge giảm 1<br>- KH nhận noti/email Bond_05 | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-17 |
| TC_XNHD_NV_007 | BR-10 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ quá hạn khi KH đang ở màn | PC-05; HĐ Thiếu Phiếu lệnh đặt bán; N = ngày E 10:58; đang ở tab "Xác nhận" thấy card HĐ | - Chờ BE qua 11:00<br>- Kéo danh sách xuống để làm mới | — | - Card HĐ biến mất sau làm mới | High | Negative | ST | Luồng A3 |
| TC_XNHD_NV_008 | BR-10 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra bấm card HĐ đã quá hạn khi chưa làm mới | Như NV_007; sau 11:00 chưa làm mới, card vẫn hiện | - Bấm card HĐ đó → "Xem hợp đồng" | — | - Không cho xác nhận; hiển thị xử lý E01 của SBOND.DOCCONFIRM.APP.02<br>- Portal: hồ sơ không chuyển Đủ hồ sơ | High | Negative | EG | Chi tiết E01 thuộc DOCCONFIRM.APP.02 |
| TC_XNHD_NV_009 | BR-20, BR-02 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ không hiển thị khi GD bị hủy sau xác nhận thanh toán | PC-05; HĐ đang hiển thị trong tab; BO hủy GD (revert) trên Portal | - Kéo danh sách xuống để làm mới | — | - Không còn card HĐ đó<br>- KH nhận noti/email Bond_07 | High | Negative | DT | Edge case Mục 9 #1 |
| TC_XNHD_NV_010 | BR-09, BR-03 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ Leg 2 chưa xác nhận khi tới E-5 | PC-05; HĐ chưa xác nhận Leg 2; BO đặt N = E-5, chạy job E5_REPRICE | - Mở tab "Xác nhận" | — | - Vẫn 1 card cho HĐ đó (không tách 2 card)<br>- Badge không tăng thêm | Medium | Boundary | DT | [GIẢ ĐỊNH] gộp – Q-14, Q-16 |
| TC_XNHD_NV_011 | BR-17 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ vẫn hiển thị khi giờ thiết bị đã qua 11:00 ngày E | PC-05; HĐ Thiếu Phiếu lệnh đặt bán; N = ngày E 10:30; giờ điện thoại chỉnh tay 11:30 | - Mở tab "Xác nhận" | — | - Card HĐ vẫn hiển thị (theo giờ BE) | Medium | Negative | EG |  |
| TC_XNHD_NV_012 | BR-17 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ không hiển thị khi giờ thiết bị chưa tới 11:00 nhưng BE đã qua | PC-05; HĐ Thiếu Phiếu lệnh đặt bán; N = ngày E 11:30; giờ điện thoại chỉnh tay 10:00 | - Mở tab "Xác nhận" | — | - Không có card HĐ đó | Medium | Negative | EG |  |
| TC_XNHD_NV_013 | BR-02 | Xác nhận HĐ – Thời hạn hiển thị | Kiểm tra HĐ không Hiệu lực không hiển thị dù còn hồ sơ thiếu | KH có HĐ Hết hiệu lực (quá COT ngày E, chưa xác nhận) đã đăng nhập | - Mở tab "Xác nhận" | — | - Không có card HĐ đó | Medium | Negative | DT | Edge case Mục 9 #3 |
| TC_XNHD_BL_001 | BR-12 | Xác nhận HĐ – Bộ lọc | Kiểm tra mở bottom sheet Bộ lọc giữ lựa chọn đang áp dụng | PC-02; đã lọc Mã TP = BHB và 1 TCPH | - Bấm icon lọc | — | - Ô Mã TP = "BHB"; TCPH = TCPH đã chọn | Medium | Positive | UC |  |
| TC_XNHD_BL_002 | BR-12 | Xác nhận HĐ – Bộ lọc | Kiểm tra đóng Bộ lọc bằng nút quay lại không áp dụng thay đổi | PC-03 | - Nhập Mã TP = BHB<br>- Bấm "<" trên header bottom sheet | BHB | - Đóng bottom sheet; danh sách giữ nguyên như trước<br>- Mở lại Bộ lọc: ô Mã TP trống | Medium | Negative | UC |  |
| TC_XNHD_BL_003 | BR-15 | Xác nhận HĐ – Bộ lọc | Kiểm tra nút Đặt lại đưa bộ lọc về mặc định | PC-02; đã lọc Mã TP = BHB và 1 TCPH | - Bấm icon lọc, bấm "Đặt lại"<br>- Bấm "Lưu" | — | - Sau Đặt lại: ô Mã TP trống, TCPH = "Tất cả tổ chức", danh sách phía sau chưa đổi<br>- Sau Lưu: hiện lại đủ HĐ cần xác nhận | Medium | Positive | UC |  |
| TC_XNHD_BL_004 | BR-15, BR-13, BR-14 | Xác nhận HĐ – Bộ lọc | Kiểm tra lọc kết hợp Mã TP và TCPH | PC-03; TK có HĐ mã BHB291193 của TCPH A và HĐ mã khác của TCPH A | - Nhập Mã TP = BHB291193<br>- Chọn TCPH = TCPH A, bấm "Lưu" | BHB291193 + TCPH A | - Chỉ còn HĐ mã BHB291193 của TCPH A | Medium | Positive | DT | [GIẢ ĐỊNH] AND – Q-26 |
| TC_XNHD_BL_005 | BR-15, BR-06 | Xác nhận HĐ – Bộ lọc | Kiểm tra lọc khi không có HĐ khớp | PC-03 | - Nhập Mã TP = ZZZ999, bấm "Lưu" | ZZZ999 | - Hiện màn trống "Hiện tại không có hợp đồng nào cần xác nhận"<br>- Bấm icon lọc → Đặt lại → Lưu: hiện lại danh sách | Medium | Negative | UC | [GIẢ ĐỊNH] – Q-07 |
| TC_XNHD_BL_006 | BR-15 | Xác nhận HĐ – Bộ lọc | Kiểm tra double-tap nút Lưu | PC-03; Proxyman bật | - Nhập Mã TP = BHB<br>- Bấm nhanh 2 lần "Lưu" | BHB | - Bottom sheet đóng 1 lần; danh sách lọc đúng<br>- Proxyman: A1 được gọi 1 lần | Low | Negative | EG |  |
| TC_XNHD_BL_007 | BR-15 | Xác nhận HĐ – Bộ lọc | Kiểm tra bộ lọc khi rời S-Bond rồi quay lại | PC-02; đã lọc Mã TP = BHB | - Bấm "<" ra màn "Sản phẩm đầu tư"<br>- Vào lại S-Bond, tab "Xác nhận" | — | - Bộ lọc về mặc định, hiện đủ HĐ cần xác nhận | Low | Positive | EG | [GIẢ ĐỊNH] – Q-11 |
| TC_XNHD_MTP_001 | BR-13 | Xác nhận HĐ – Bộ lọc Mã TP | Kiểm tra lọc Mã TP khi nhập đủ mã | PC-03 | - Nhập Mã TP, bấm "Lưu" | BHB291193 | - Chỉ còn card có mã BHB291193 | Medium | Positive | EP |  |
| TC_XNHD_MTP_002 | BR-13 | Xác nhận HĐ – Bộ lọc Mã TP | Kiểm tra lọc Mã TP khi nhập cụm ký tự giữa mã | PC-03 | - Nhập Mã TP, bấm "Lưu" | 2911 | - Hiện mọi card có mã chứa "2911" (VD BHB291193)<br>- Không có card mã không chứa "2911" | Medium | Positive | EP |  |
| TC_XNHD_MTP_003 | BR-13 | Xác nhận HĐ – Bộ lọc Mã TP | Kiểm tra lọc Mã TP khi nhập chữ thường | PC-03 | - Nhập Mã TP, bấm "Lưu" | bhb291 | - Kết quả giống khi nhập "BHB291" | Medium | Positive | EP |  |
| TC_XNHD_MTP_004 | BR-13 | Xác nhận HĐ – Bộ lọc Mã TP | Kiểm tra lọc khi để trống Mã TP | PC-03 | - Để trống ô Mã TP, bấm "Lưu" | (trống) | - Hiện đủ HĐ cần xác nhận, không lọc theo mã | Medium | Boundary | BVA |  |
| TC_XNHD_MTP_005 | BR-13 | Xác nhận HĐ – Bộ lọc Mã TP | Kiểm tra lọc Mã TP khi có khoảng trắng đầu/cuối | PC-03 | - Nhập Mã TP, bấm "Lưu" | "  BHB291193  " | - Kết quả giống khi nhập "BHB291193" | Low | Boundary | EG | [GIẢ ĐỊNH] trim – Q-09 |
| TC_XNHD_MTP_006 | BR-13, BR-18 | Xác nhận HĐ – Bộ lọc Mã TP | Kiểm tra lọc Mã TP khi nhập chuỗi SQL injection | PC-03 | - Nhập Mã TP, bấm "Lưu" | ' OR 1=1 -- | - Màn trống, không hiện HĐ nào<br>- App không thoát, không popup lỗi hệ thống | Medium | Security | EG | [GIẢ ĐỊNH] – Q-09 |
| TC_XNHD_MTP_007 | BR-13 | Xác nhận HĐ – Bộ lọc Mã TP | Kiểm tra lọc Mã TP khi nhập tiếng Việt có dấu và emoji | PC-03 | - Nhập Mã TP, bấm "Lưu" | BHBđ😀 | - Màn trống; app không thoát | Low | Negative | EG | [GIẢ ĐỊNH] – Q-09 |
| TC_XNHD_TCPH_001 | BR-14 | Xác nhận HĐ – Bộ lọc TCPH | Kiểm tra danh sách dropdown Tổ chức phát hành | PC-03 | - Bấm dropdown "Tổ chức phát hành" | — | - Có lựa chọn "Tất cả tổ chức" (đang chọn)<br>- Danh sách tên TCPH khớp A0 issuers[] | Medium | Positive | EP | [GIẢ ĐỊNH] phạm vi A0 – Q-22 |
| TC_XNHD_TCPH_002 | BR-14 | Xác nhận HĐ – Bộ lọc TCPH | Kiểm tra lọc khi chọn 1 TCPH | PC-03 | - Chọn 1 TCPH (VD TCPH A)<br>- Bấm "Lưu" | TCPH A | - Chỉ còn card có TCPH A | Medium | Positive | EP |  |
| TC_XNHD_TCPH_003 | BR-14 | Xác nhận HĐ – Bộ lọc TCPH | Kiểm tra lọc khi chọn 2 TCPH | PC-03 | - Chọn TCPH A và TCPH B<br>- Bấm "Lưu" | TCPH A, TCPH B | - Hiện card của cả TCPH A và TCPH B; không có TCPH khác | Medium | Positive | PW | Hiển thị khi chọn nhiều – Q-10 |
| TC_XNHD_TCPH_004 | BR-14 | Xác nhận HĐ – Bộ lọc TCPH | Kiểm tra bỏ chọn hết TCPH | PC-03; đã chọn TCPH A trong dropdown | - Bỏ chọn TCPH A<br>- Bấm "Lưu" | — | - Dropdown hiện "Tất cả tổ chức"<br>- Hiện đủ HĐ cần xác nhận | Low | Boundary | BVA | [GIẢ ĐỊNH] – Q-10 |
| TC_XNHD_TCPH_005 | BR-14, BR-19 | Xác nhận HĐ – Bộ lọc TCPH | Kiểm tra dropdown khi A0 lỗi | PC-02; Proxyman mock A0 trả HTTP 500 | - Kéo làm mới<br>- Bấm icon lọc, bấm dropdown TCPH | — | - Danh sách card vẫn hiển thị theo A1<br>- Dropdown chỉ có "Tất cả tổ chức"; app không thoát | Low | Negative | EG | [GIẢ ĐỊNH] hành vi khi A0 lỗi |
| TC_XNHD_ERR_001 | BR-19 | Xác nhận HĐ – Lỗi | Kiểm tra mở tab khi mất mạng | PC-01; tắt Wi-Fi và 4G | - Bấm tab "Xác nhận" | — | - Popup lỗi chuẩn SHSmart, CTA "Đóng"<br>- Không hiện màn trống giả | Medium | Negative | EG | [GIẢ ĐỊNH] popup – Q-23 |
| TC_XNHD_ERR_002 | BR-19 | Xác nhận HĐ – Lỗi | Kiểm tra mở tab khi A1 trả BND-901 | PC-01; Proxyman mock A1 trả BND-901 | - Bấm tab "Xác nhận" | — | - Popup "Hệ thống đang bận", CTA "Đóng"<br>- Bấm "Đóng": đóng popup, không có card | Medium | Negative | EP | [GIẢ ĐỊNH] mapping – Q-24 |
| TC_XNHD_ERR_003 | BR-19 | Xác nhận HĐ – Lỗi | Kiểm tra mở tab khi A1 trả BND-999 | PC-01; Proxyman mock A1 trả BND-999 | - Bấm tab "Xác nhận" | — | - Popup "Có lỗi xảy ra. Vui lòng thử lại sau", CTA "Đóng" | Medium | Negative | EP | [GIẢ ĐỊNH] mapping – Q-24 |
| TC_XNHD_ERR_004 | BR-18, BR-19 | Xác nhận HĐ – Lỗi | Kiểm tra mở tab khi phiên đăng nhập hết hạn | PC-01; token hết hạn (đợi hết phiên hoặc mock A1 trả AUTH_001) | - Bấm tab "Xác nhận"<br>- Bấm "Đăng nhập" trên popup | — | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", CTA "Đăng nhập"<br>- Chuyển màn Đăng nhập; không hiện card | High | Negative | EP |  |
| TC_XNHD_ERR_005 | BR-18, BR-19 | Xác nhận HĐ – Lỗi | Kiểm tra Lưu bộ lọc khi phiên hết hạn | PC-03; token hết hạn | - Nhập Mã TP = BHB, bấm "Lưu" | BHB | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", CTA "Đăng nhập" | Medium | Negative | EG |  |
| TC_XNHD_ERR_006 | BR-11, BR-19 | Xác nhận HĐ – Lỗi | Kiểm tra kéo làm mới khi mất mạng | PC-02; sau khi danh sách hiện, tắt mạng | - Kéo danh sách xuống để làm mới | — | - Popup lỗi chuẩn SHSmart<br>- Đóng popup: danh sách cũ giữ nguyên | Low | Negative | EG | [GIẢ ĐỊNH] – Q-23 |
| TC_XNHD_ERR_007 | BR-01 | Xác nhận HĐ – Lỗi | Kiểm tra mở tab khi A1 phản hồi chậm | PC-01; Proxyman delay A1 10 giây | - Bấm tab "Xác nhận" | — | - Hiện loading trong lúc chờ, không hiện màn trống<br>- Sau 10 giây hiện danh sách card | Low | Negative | EG |  |
| TC_XNHD_PQ_001 | BR-18 | Xác nhận HĐ – Phân quyền | Kiểm tra dữ liệu khi đổi tài khoản trên cùng máy | KH A (PC-01) đang ở tab "Xác nhận"; KH B không có HĐ cần xác nhận | - Đăng xuất KH A, đăng nhập KH B<br>- Vào S-Bond, tab "Xác nhận" | — | - Màn trống, không còn card hay badge của KH A | High | Permission | EG |  |
| TC_XNHD_PQ_002 | BR-18, BR-07 | Xác nhận HĐ – Phân quyền | Kiểm tra mở chi tiết HĐ của KH khác bằng cách sửa request | PC-02; Proxyman bật breakpoint A2; biết id 1 HĐ của KH khác | - Bấm 1 card<br>- Sửa id trong request A2 thành id HĐ của KH khác, gửi | id HĐ KH khác | - Không hiển thị thông tin HĐ của KH khác<br>- BE trả BND-403; app hiện popup lỗi chuẩn | High | Security | EG |  |
| TC_XNHD_PQ_003 | BR-18, BR-01 | Xác nhận HĐ – Phân quyền | Kiểm tra bấm noti Bond_03 khi đã đăng xuất | KH đã nhận noti Bond_03 rồi đăng xuất app | - Bấm noti Bond_03 | — | - Mở màn Đăng nhập, không hiện danh sách HĐ<br>- Đăng nhập xong vào tab "Xác nhận" | Medium | Permission | UC | [GIẢ ĐỊNH] – Q-13 |
| TC_XNHD_EVT_001 | BR-21 | Xác nhận HĐ – Tracking | Kiểm tra event SBOND.DOCCONFIRM.LIST khi mở và làm mới | PC-01; công cụ debug analytics bật | - Bấm tab "Xác nhận"<br>- Kéo làm mới | — | - Mỗi lần ghi 1 event SBOND.DOCCONFIRM.LIST (tổng 2) | Low | Integration | UC |  |
| TC_XNHD_EVT_002 | BR-21 | Xác nhận HĐ – Tracking | Kiểm tra event SBOND.DOCCONFIRM.OPEN khi bấm card | PC-02; debug analytics bật | - Bấm 1 card | — | - Ghi 1 event SBOND.DOCCONFIRM.OPEN | Low | Integration | UC |  |
| TC_XNHD_EVT_003 | BR-21 | Xác nhận HĐ – Tracking | Kiểm tra event SBOND.DOCCONFIRM.NOTI.OPEN khi mở từ noti | Như NAV_002; debug analytics bật | - Bấm noti Bond_03 | — | - Ghi 1 event SBOND.DOCCONFIRM.NOTI.OPEN | Low | Integration | UC |  |
| TC_XNHD_EVT_004 | BR-21 | Xác nhận HĐ – Tracking | Kiểm tra event SBOND.DOCCONFIRM.FILTER.APPLY khi Lưu bộ lọc | PC-03; debug analytics bật | - Nhập Mã TP = BHB, bấm "Lưu" | BHB | - Ghi 1 event SBOND.DOCCONFIRM.FILTER.APPLY | Low | Integration | UC |  |

### 4.2 API A0 / A1 / A2 (15 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_XNHD_API_001 | BR-22, BR-14 | Xác nhận HĐ – API | Kiểm tra A0 trả danh sách TCPH | PC-API | - Gọi GET /api/v1/bond/term/app/appendix/filters | — | - HTTP 200; data.issuers[] mỗi phần tử có mã và tên TCPH<br>- Có đủ TCPH của các HĐ cần xác nhận của KH | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-22 |
| TC_XNHD_API_002 | BR-22, BR-04 | Xác nhận HĐ – API | Kiểm tra A1 trả đủ field của HĐ | PC-API | - Gọi GET /api/v1/bond/term/app/appendix | — | - HTTP 200; mỗi phần tử data.content[] có id, symbol, term, termType, status, issuerName, orderNo, matureDate, totalInvestmentAmount, rate, interest<br>- totalCount = số phần tử content[] | High | Positive | EP | issuerName – Q-03; status – Q-20 |
| TC_XNHD_API_003 | BR-02 | Xác nhận HĐ – API | Kiểm tra A1 chỉ trả HĐ Hiệu lực đang Thiếu hồ sơ | PC-API | - Gọi GET /api/v1/bond/term/app/appendix<br>- So với danh sách HĐ của KH trên Portal | — | - content[] chỉ gồm HĐ Hiệu lực, hồ sơ Thiếu hồ sơ<br>- Không có HĐ Đủ hồ sơ, Hoàn thành, hồ sơ Chờ gửi KH | High | Positive | DT |  |
| TC_XNHD_API_004 | BR-05 | Xác nhận HĐ – API | Kiểm tra A1 sắp xếp theo ngày đáo hạn | PC-API | - Gọi GET /api/v1/bond/term/app/appendix | — | - matureDate phần tử sau ≥ phần tử trước | Medium | Positive | EP | [GIẢ ĐỊNH] – Q-01 |
| TC_XNHD_API_005 | BR-13 | Xác nhận HĐ – API | Kiểm tra A1 lọc symbol theo cụm từ không phân biệt hoa/thường | PC-API | - Gọi GET /api/v1/bond/term/app/appendix?symbol=bhb291 | symbol = bhb291 | - HTTP 200; mọi symbol chứa "BHB291"<br>- totalCount = số phần tử | Medium | Positive | EP |  |
| TC_XNHD_API_006 | BR-13 | Xác nhận HĐ – API | Kiểm tra A1 khi symbol rỗng | PC-API | - Gọi GET /api/v1/bond/term/app/appendix?symbol= | symbol rỗng | - HTTP 200; kết quả giống gọi không có symbol | Medium | Boundary | BVA |  |
| TC_XNHD_API_007 | BR-14 | Xác nhận HĐ – API | Kiểm tra A1 lọc issuerCode nhiều giá trị | PC-API | - Gọi GET /api/v1/bond/term/app/appendix?issuerCode=<mã TCPH A>&issuerCode=<mã TCPH B> | 2 mã TCPH lấy từ A0 | - HTTP 200; chỉ HĐ của TCPH A và TCPH B | Medium | Positive | PW | [GIẢ ĐỊNH] cách truyền mảng |
| TC_XNHD_API_008 | BR-14 | Xác nhận HĐ – API | Kiểm tra A1 khi issuerCode không tồn tại | PC-API | - Gọi GET /api/v1/bond/term/app/appendix?issuerCode=XXX000 | issuerCode = XXX000 | - HTTP 200; content[] rỗng; totalCount = 0 | Low | Negative | EP | [GIẢ ĐỊNH] – Q-21 |
| TC_XNHD_API_009 | BR-13, BR-18 | Xác nhận HĐ – API | Kiểm tra A1 khi symbol là chuỗi SQL injection | PC-API | - Gọi GET /api/v1/bond/term/app/appendix?symbol=' OR 1=1 -- | ' OR 1=1 -- | - Không trả HĐ nào (content[] rỗng) hoặc HTTP 400<br>- Không lộ HĐ của KH khác, không lỗi 500 | Medium | Security | EG | [GIẢ ĐỊNH] – Q-21 |
| TC_XNHD_API_010 | BR-06 | Xác nhận HĐ – API | Kiểm tra A1 khi KH không có HĐ cần xác nhận | Như PC-API, token của KH PC-04 | - Gọi GET /api/v1/bond/term/app/appendix | — | - HTTP 200; content[] rỗng; totalCount = 0 | Medium | Boundary | BVA |  |
| TC_XNHD_API_011 | BR-18 | Xác nhận HĐ – API | Kiểm tra A1 khi không có token | PC-API; bỏ header Authorization | - Gọi GET /api/v1/bond/term/app/appendix | — | - HTTP 401, mã lỗi AUTH_001; không trả dữ liệu | High | Security | EP |  |
| TC_XNHD_API_012 | BR-18 | Xác nhận HĐ – API | Kiểm tra A1 khi token hết hạn | PC-API; token đã hết hạn | - Gọi GET /api/v1/bond/term/app/appendix | — | - Trả AUTH_001; không trả dữ liệu | Medium | Security | EP |  |
| TC_XNHD_API_013 | BR-18 | Xác nhận HĐ – API | Kiểm tra A1 chỉ trả HĐ của KH sở hữu token | PC-API; token KH B (có HĐ cần xác nhận khác KH A) | - Gọi GET /api/v1/bond/term/app/appendix | — | - content[] chỉ có HĐ của KH B, không có Số HĐ nào của KH A | High | Security | EG |  |
| TC_XNHD_API_014 | BR-18, BR-07 | Xác nhận HĐ – API | Kiểm tra A2 khi id là HĐ của KH khác | PC-API | - Gọi GET /api/v1/bond/term/app/appendix/detail?id=<id HĐ của KH khác> | id HĐ KH khác | - HTTP 403, mã lỗi BND-403; không trả thông tin HĐ | High | Security | EG |  |
| TC_XNHD_API_015 | BR-23 | Xác nhận HĐ – API | Kiểm tra gọi A1 nhiều lần không đổi trạng thái hồ sơ | PC-API | - Gọi GET /api/v1/bond/term/app/appendix 5 lần liên tiếp<br>- Xem trạng thái HĐ/hồ sơ trên Portal | — | - 5 lần trả cùng kết quả<br>- Trạng thái HĐ, hồ sơ không đổi | Low | Positive | EG |  |

## B5. Tự review + RTM
### RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ |
|---|---|---|---|---|
| BR-01 | Mở tab "Xác nhận" / bấm noti Bond_03 / bấm "Kiểm tra" ở Danh mục HĐ → … | 7 | TC_XNHD_NAV_001, TC_XNHD_NAV_002, TC_XNHD_NAV_003, TC_XNHD_NAV_004, TC_XNHD_NAV_007, TC_XNHD_ERR_007, TC_XNHD_PQ_003 | Đủ |
| BR-02 | Phạm vi danh sách: chỉ HĐ trạng thái Hiệu lực có Trạng thái hồ sơ = "T… | 8 | TC_XNHD_DS_001, TC_XNHD_DS_003, TC_XNHD_DS_012, TC_XNHD_NV_001, TC_XNHD_NV_002, TC_XNHD_NV_009, TC_XNHD_NV_013, TC_XNHD_API_003 | Đủ |
| BR-03 | Badge số (đỏ) trên icon tab "Xác nhận" = số HĐ trong danh sách (data.t… | 9 | TC_XNHD_UI_001, TC_XNHD_DS_015, TC_XNHD_BG_001, TC_XNHD_BG_002, TC_XNHD_BG_003, TC_XNHD_BG_004, TC_XNHD_BG_005, TC_XNHD_BG_006, TC_XNHD_NV_010 | Đủ |
| BR-04 | Card HĐ: Mã TP (cam – symbol); chip Thời gian đầu tư "n tháng" (term/t… | 14 | TC_XNHD_UI_002, TC_XNHD_UI_005, TC_XNHD_UI_006, TC_XNHD_DS_002, TC_XNHD_DS_003, TC_XNHD_DS_004, TC_XNHD_DS_005, TC_XNHD_DS_006, TC_XNHD_DS_007, TC_XNHD_DS_008, TC_XNHD_DS_009, TC_XNHD_DS_010, TC_XNHD_DS_015, TC_XNHD_API_002 | Đủ |
| BR-05 | Sắp xếp card: TBU Q06 – dự thảo BA: ngày đáo hạn gần nhất trước | 3 | TC_XNHD_DS_013, TC_XNHD_DS_014, TC_XNHD_API_004 | Đủ |
| BR-06 | Không có HĐ cần xác nhận (totalCount = 0) → màn trống: hình minh họa +… | 6 | TC_XNHD_UI_003, TC_XNHD_DS_011, TC_XNHD_DS_012, TC_XNHD_BG_002, TC_XNHD_BL_005, TC_XNHD_API_010 | Đủ |
| BR-07 | Bấm card → mở Chi tiết hợp đồng (bottom sheet) tại tab "Xác nhận" của … | 6 | TC_XNHD_CT_001, TC_XNHD_CT_002, TC_XNHD_CT_003, TC_XNHD_CT_007, TC_XNHD_PQ_002, TC_XNHD_API_014 | Đủ |
| BR-08 | Xác nhận thành công (SBOND.DOCCONFIRM.APP.02) → quay về tab Xác nhận, … | 4 | TC_XNHD_BG_004, TC_XNHD_CT_004, TC_XNHD_CT_005, TC_XNHD_CT_006 | Đủ |
| BR-09 | Thời hạn xác nhận: HĐ Leg 2 từ T+3 đến trước E-5 (từ E-5 gộp với Phiếu… | 7 | TC_XNHD_NV_001, TC_XNHD_NV_002, TC_XNHD_NV_003, TC_XNHD_NV_004, TC_XNHD_NV_005, TC_XNHD_NV_006, TC_XNHD_NV_010 | Đủ |
| BR-10 | A3: HĐ quá hạn khi KH đang ở màn → BE không trả HĐ đó ở lần tải sau; n… | 2 | TC_XNHD_NV_007, TC_XNHD_NV_008 | Đủ |
| BR-11 | Kéo xuống để làm mới → gọi lại bước 1 (A0, A1) | 3 | TC_XNHD_DS_016, TC_XNHD_DS_017, TC_XNHD_ERR_006 | Đủ |
| BR-12 | Icon lọc trên header → bottom sheet "Bộ lọc"; nút quay lại trên header… | 3 | TC_XNHD_UI_004, TC_XNHD_BL_001, TC_XNHD_BL_002 | Đủ |
| BR-13 | Mã trái phiếu: textbox, không bắt buộc, nhập text tự do; tìm theo cụm … | 13 | TC_XNHD_UI_004, TC_XNHD_DS_017, TC_XNHD_BL_004, TC_XNHD_MTP_001, TC_XNHD_MTP_002, TC_XNHD_MTP_003, TC_XNHD_MTP_004, TC_XNHD_MTP_005, TC_XNHD_MTP_006, TC_XNHD_MTP_007, TC_XNHD_API_005, TC_XNHD_API_006, TC_XNHD_API_009 | Đủ |
| BR-14 | Tổ chức phát hành: dropdown chọn nhiều, mặc định "Tất cả tổ chức", dan… | 10 | TC_XNHD_UI_004, TC_XNHD_BL_004, TC_XNHD_TCPH_001, TC_XNHD_TCPH_002, TC_XNHD_TCPH_003, TC_XNHD_TCPH_004, TC_XNHD_TCPH_005, TC_XNHD_API_001, TC_XNHD_API_007, TC_XNHD_API_008 | Đủ |
| BR-15 | "Đặt lại" → về mặc định (chưa gọi API); "Lưu" → đóng bottom sheet, gọi… | 6 | TC_XNHD_BG_005, TC_XNHD_BL_003, TC_XNHD_BL_004, TC_XNHD_BL_005, TC_XNHD_BL_006, TC_XNHD_BL_007 | Đủ |
| BR-16 | Header: tiêu đề "SH Bond" + nút quay lại (về màn trước); icon ⓘ – nội … | 3 | TC_XNHD_UI_001, TC_XNHD_NAV_005, TC_XNHD_NAV_006 | Đủ |
| BR-17 | BIZ-11: ngày nghiệp vụ, giờ lấy theo Backend, không theo thiết bị (mốc… | 4 | TC_XNHD_NV_005, TC_XNHD_NV_006, TC_XNHD_NV_011, TC_XNHD_NV_012 | Đủ |
| BR-18 | V01: phiên đăng nhập hợp lệ; dữ liệu chỉ của KH đăng nhập; vi phạm → p… | 11 | TC_XNHD_MTP_006, TC_XNHD_ERR_004, TC_XNHD_ERR_005, TC_XNHD_PQ_001, TC_XNHD_PQ_002, TC_XNHD_PQ_003, TC_XNHD_API_009, TC_XNHD_API_011, TC_XNHD_API_012, TC_XNHD_API_013, TC_XNHD_API_014 | Đủ |
| BR-19 | Lỗi hệ thống / token (BND-901, BND-999, AUTH_001) → popup lỗi chuẩn SH… | 7 | TC_XNHD_TCPH_005, TC_XNHD_ERR_001, TC_XNHD_ERR_002, TC_XNHD_ERR_003, TC_XNHD_ERR_004, TC_XNHD_ERR_005, TC_XNHD_ERR_006 | Đủ |
| BR-20 | Edge case: hủy GD sau xác nhận thanh toán → revert, HĐ không hiển thị … | 3 | TC_XNHD_NV_004, TC_XNHD_NV_006, TC_XNHD_NV_009 | Đủ |
| BR-21 | Tracking event FE: SBOND.DOCCONFIRM.LIST (mở/làm mới), .OPEN (bấm card… | 4 | TC_XNHD_EVT_001, TC_XNHD_EVT_002, TC_XNHD_EVT_003, TC_XNHD_EVT_004 | Đủ |
| BR-22 | API: A0 GET /api/v1/bond/term/app/appendix/filters (mở màn); A1 GET /a… | 2 | TC_XNHD_API_001, TC_XNHD_API_002 | Đủ |
| BR-23 | Post-condition: màn không đổi dữ liệu (mở, làm mới, lọc không đổi trạn… | 2 | TC_XNHD_DS_018, TC_XNHD_API_015 | Đủ |

### Checklist
| Tiêu chí | Kết quả | Ghi chú |
|---|---|---|
| A. 100% BR có TC | Đạt | 23/23 BR |
| A. Luồng chính + phụ + ngoại lệ | Đạt | NAV/DS/CT + BL/MTP/TCPH + NV (A3, edge case)/ERR/PQ |
| A. Decision table mỗi rule có TC; ST có chuyển trạng thái không hợp lệ | Đạt | DT R1–R7 → NV/DS/CT; ST: hồ sơ còn thiếu (TC_XNHD_CT_005), thoát giữa luồng (TC_XNHD_CT_006), card quá hạn (TC_XNHD_NV_008) |
| A. Phân quyền | Đạt | 1 role KH → token, đổi tài khoản, IDOR app (TC_XNHD_PQ_002) + API (TC_XNHD_API_013, TC_XNHD_API_014) |
| A. Error guessing | Đạt | Mạng, chậm, double-tap, giờ thiết bị, SQLi, emoji, tên/số dài |
| B. Đủ 12 cột, ID đúng format, không trùng | Đạt | 105 ID duy nhất (kiểm bằng script) |
| B. 1 TC = 1 mục tiêu | Đạt | Bộ lọc tách theo trường: Mã TP (MTP_*), TCPH (TCPH_*), nút (BL_*) |
| B. Title "Kiểm tra … khi …" | Đạt | — |
| B. Test data cụ thể | Đạt | Điều kiện dữ liệu ghi trong Precondition; ngày theo T/E/N |
| B. Expected đo được | Đạt | Text, màu, số card, badge, trạng thái Portal, HTTP/mã lỗi |
| B. Negative kiểm cả báo lỗi + không thực hiện | Đạt | TC_XNHD_NV_008, TC_XNHD_CT_006, ERR_*, PQ_*, API_*: có "không xác nhận / không trả dữ liệu / danh sách giữ" |
| B. Message nguyên văn / [GIẢ ĐỊNH] | Đạt | I02 và 3 popup lấy từ Mục 6; mapping mã → text [GĐ] Q-24 |
| C. Priority hợp lý | Đạt | Phạm vi danh sách, mốc thời hạn, badge, mở đúng HĐ, bảo mật = High |
| C. Thứ tự UI → Positive → … → Permission | Đạt | Theo nhóm; trong nhóm theo thứ tự quy tắc |

### Sửa/bổ sung trong vòng review
- Tách mốc thời hạn thành biên riêng (TC_XNHD_NV_001/TC_XNHD_NV_002 T+2/T+3; TC_XNHD_NV_005/TC_XNHD_NV_006 10:59/11:00 ngày E) thay vì 1 TC "đúng thời hạn".
- Bổ sung A3 – HĐ quá hạn khi đang ở màn: làm mới (TC_XNHD_NV_007) và bấm card cũ (TC_XNHD_NV_008).
- Bổ sung BIZ-11 giờ thiết bị lệch BE quanh COT (TC_XNHD_NV_011, TC_XNHD_NV_012) – trước đó BR-17 chưa có TC riêng.
- Bổ sung badge đếm theo HĐ khi 1 HĐ có 2 hồ sơ (TC_XNHD_BG_003) và HĐ còn hồ sơ sau xác nhận (TC_XNHD_CT_005) – theo Q-14, Q-16.
- Bổ sung IDOR qua A2 trên app (TC_XNHD_PQ_002) và API (TC_XNHD_API_014); BR-20 gắn thêm TC_XNHD_NV_006 (quá COT → Bond_05).
- DT cột R6 sửa trỏ đúng TC_XNHD_NV_009, TC_XNHD_NV_013.

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 105 (App 90 · API 15) | — |
| High / Medium / Low | 28 (26%) / 50 (47%) / 27 (25%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 42 : 63 (40% : 60%) | ≈ 30 : 70 |
| Theo Type | Positive 42 · Negative 24 · Boundary 20 · Security 7 · UI 6 · Integration 4 · Permission 2 | — |
| BR được phủ | 23/23 (100%) | 100% |
| Q&A mở | 26 (Blocker 0 · Major 11 · Minor 15) | Blocker = 0 |
| Ghi chú tỷ lệ | Positive cao hơn mục tiêu ~10% do màn chủ yếu hiển thị (card, badge, bộ lọc); phần rủi ro (thời hạn, phân quyền) đã đủ case âm/biên | — |
