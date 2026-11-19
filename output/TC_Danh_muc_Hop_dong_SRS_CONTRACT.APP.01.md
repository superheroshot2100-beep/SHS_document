# SBOND.CONTRACT.APP.01 – Danh mục Hợp đồng · Bộ test B1 → B5

- **Nguồn:** `doc/SRS/SBOND.CONTRACT.APP.01 - Danh mục Hợp đồng.doc` (bản export Confluence ngày 18/11/2026), gồm Activity diagram v2.1 và ảnh Figma đính kèm
- **Nền tảng:** SHSmart App (S-Bond). Phần Web chỉ được nhắc tới trong luồng xử lý → ngoài phạm vi
- **Quy ước:** `[GIẢ ĐỊNH]` = điểm tài liệu chưa rõ, tạm giả định để viết tiếp, xem mã Q tương ứng

---

## B1 – PHÂN TÍCH YÊU CẦU

### 1. Tổng quan
- **Mục tiêu:** KH xem danh sách HĐ trái phiếu của mình, biết HĐ nào cần xác nhận, lọc HĐ và mở chi tiết từng HĐ.
- **Role:** KH (NĐT, actor duy nhất trên App). Hệ thống liên quan: BE S-Bond (API A1), Flex (số dư, lưu ký), Portal (khai báo trạng thái HĐ).
- **Tính chất:** màn chỉ đọc, không làm thay đổi dữ liệu (Post-condition).

### 2. Feature / thành phần màn hình
| # | Feature | Mục SRS |
|---|---|---|
| F1 | Điểm vào màn: tab, nút "Xem danh mục hợp đồng" ở Chi tiết TP, noti Bond_02/Bond_06 | Bảng đầu, Luồng B1 |
| F2 | Box "Hợp đồng cần xác nhận" + nút "Kiểm tra" | 2.1 |
| F3 | Chip lọc mã TP (khi vào từ Chi tiết TP) | 2.2 #1 |
| F4 | Bottom sheet "Bộ lọc": Mã TP, TCPH, Kỳ hạn, Lợi suất, Loại TP, Trạng thái HĐ, Đặt lại/Lưu | 2.2 #2–#9 |
| F5 | Danh sách card HĐ (sắp xếp, ẩn HĐ, phân trang) | 2.3, 3, 4 |
| F6 | Điều hướng sang Chi tiết HĐ / tab Xác nhận | Luồng B5–B6, 2.3 #11 |
| F7 | Empty state, popup lỗi, tracking event | 5, 6, 7 |

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Vào màn từ 3 điểm: tab "Danh mục hợp đồng"; nút "Xem danh mục hợp đồng" ở Chi tiết TP (kèm lọc mã TP); noti Bond_02, Bond_06 | Bảng đầu – Entry; Luồng B1 | Logic |
| BR-02 | App gọi A1 `GET /api/v1/bond/term/app/appendix` khi mở màn, khi lọc, khi đổi trang | Mục 3 | Tích hợp |
| BR-03 | Danh sách sắp xếp theo Ngày tạo HĐ từ mới đến cũ (createdAt giảm dần) | BIZ-22 | Logic |
| BR-04 | Không hiển thị HĐ của lệnh không khớp (Rejected) và HĐ bị hủy sau xác nhận thanh toán | BIZ-22; Mục 8 | Logic |
| BR-05 | Box "Hợp đồng cần xác nhận" chỉ hiển thị khi pendingConfirmCount ≥ 1 và hiển thị số n | BIZ-23; 2.1 #1; Luồng B2 | Logic/UI |
| BR-06 | Card có needConfirm = true hiển thị dòng cảnh báo "Hợp đồng có hồ sơ cần xác nhận" | BIZ-23; 2.3 #10 | Logic/UI |
| BR-07 | Nút "Kiểm tra" chuyển sang tab "Xác nhận" của S-Bond | 2.1 #2; Luồng B6 | Logic |
| BR-08 | Chip mã TP hiển thị khi vào từ Chi tiết TP, chỉ hiện HĐ của mã đó; bấm X để bỏ lọc và gọi lại A1 | 2.2 #1; Luồng B3 | Logic |
| BR-09 | V01: bondCode phải thuộc danh mục KH; không có HĐ thì hiển thị empty state I01 | Mục 5 – V01 | Validation |
| BR-10 | Icon lọc mở bottom sheet "Bộ lọc" và đổi màu khi đang lọc | 2.2 #2 | UI |
| BR-11 | Lọc Mã TP: màn tìm riêng; tìm theo cụm từ trong mã/tên TP, không phân biệt hoa thường; gợi ý gồm mã + TCPH; chỉ chọn 1 mã; để trống thì không lọc | 2.2 #3 | Logic |
| BR-12 | TCPH: dropdown chọn nhiều, mặc định "Tất cả tổ chức" | 2.2 #4 | Logic |
| BR-13 | Kỳ hạn: dropdown chọn nhiều, mặc định "Tất cả kỳ hạn" | 2.2 #5 | Logic |
| BR-14 | Lợi suất: thanh trượt 2 đầu, mặc định toàn khoảng min → max; lọc HĐ có lợi suất trong khoảng chọn | 2.2 #6 | Logic |
| BR-15 | V02: lợi suất "từ" ≤ "đến" và nằm trong min–max; UI chặn kéo vượt; API trả BND-100 | Mục 5 – V02 | Validation |
| BR-16 | Loại TP: chip chọn nhiều Tất cả / Niêm yết / Riêng lẻ, mặc định "Tất cả" | 2.2 #7 | Logic |
| BR-17 | Trạng thái HĐ: chip chọn nhiều Tất cả / Chờ xử lý / Hiệu lực / Hoàn thành / Hết hiệu lực, mặc định "Tất cả"; lấy theo Portal | 2.2 #8 | Logic |
| BR-18 | "Đặt lại" đưa bộ lọc về mặc định; "Lưu" đóng bottom sheet và gọi lại A1 | 2.2 #9 | Logic |
| BR-19 | Card HĐ hiển thị 9 field với định dạng: Mã TP (cam), Thời gian đầu tư "n tháng", Trạng thái, TCPH, Mã HĐ, Ngày đáo hạn dd/MM/yyyy, Tổng tiền đầu tư #.### VND (cam), Lợi suất x,xx%/năm, Lợi tức dự kiến #.### VND (xanh) | 2.3 #1–#9 | UI |
| BR-20 | Bấm card chuyển sang Chi tiết HĐ, tab "Tổng quan" (CONTRACT.APP.02) | 2.3 #11; Luồng B5 | Logic |
| BR-21 | Danh sách rỗng hiển thị empty state SBOND.CONTRACT.I01 "Quý khách chưa có hợp đồng / Không tìm thấy hợp đồng phù hợp" | Mục 6 #1 | UI |
| BR-22 | Lỗi BND-901 / BND-999 / AUTH_001 hiển thị popup lỗi chuẩn SHSmart (CTA Đóng / Đăng nhập) | Mục 6 #2 | Tích hợp |
| BR-23 | Ghi event: SBOND.CONTRACT.LIST, .FILTER, .CONFIRM.CHECK, .DETAIL | Mục 7 | Tích hợp |
| BR-24 | Khi có nhắc T-5 hoàn thiện số lượng TP (Bond_08), HĐ vẫn hiển thị bình thường | Mục 8 | Logic |
| BR-25 | Màn không làm thay đổi dữ liệu | Post-Condition | Logic |
| BR-26 | KH chỉ thấy HĐ của chính mình (theo token) | User Story; V01 | Phân quyền |

### 4. Field list
| Field | Kiểu | Bắt buộc | Ràng buộc | Mặc định | Message lỗi |
|---|---|---|---|---|---|
| Mã trái phiếu | Ô tìm → màn tìm | N | Text tự do; tìm theo cụm trong mã/tên TP; không phân biệt hoa thường; chỉ chọn 1 | Trống, placeholder "Tìm mã trái phiếu" | – |
| Tổ chức phát hành | Dropdown chọn nhiều | N | – | "Tất cả tổ chức" | – |
| Kỳ hạn | Dropdown chọn nhiều | N | – | "Tất cả kỳ hạn" | – |
| Lợi suất (%) | Thanh trượt 2 đầu | N | từ ≤ đến; trong min–max | min → max | Chặn kéo; API BND-100 (không có câu hiển thị) |
| Loại trái phiếu | Chip chọn nhiều | N | Tất cả / Niêm yết / Riêng lẻ | "Tất cả" | – |
| Trạng thái hợp đồng | Chip chọn nhiều | N | Tất cả / Chờ xử lý / Hiệu lực / Hoàn thành / Hết hiệu lực | "Tất cả" | – |
| Đặt lại / Lưu | Button | Y | – | – | – |

### 5. Q&A
| Q ID | Câu hỏi | Lý do hỏi | Mức | Giả định đề xuất |
|---|---|---|---|---|
| Q-01 | Câu chữ của box là gì? Mục 2.1 ghi tiêu đề "Hợp đồng cần xác nhận" + "Quý khách có hợp đồng chờ xác nhận lệnh"; Luồng B2 ghi "Quý khách có n hợp đồng chờ xác nhận"; Figma không có tiêu đề | 3 nguồn mâu thuẫn, ảnh hưởng Expected | Major | Theo Figma: "Quý khách có {n} hợp đồng chờ xác nhận", không có tiêu đề |
| Q-02 | pendingConfirmCount có đúng bằng số HĐ needConfirm = true không? Có phụ thuộc bộ lọc / chip mã TP không? Có tính HĐ Rejected/hủy không? | Ảnh hưởng số n và việc hiện/ẩn box khi đang lọc | Major | Đếm trên toàn bộ HĐ đang hiển thị được, không phụ thuộc lọc |
| Q-03 | "Kiểm tra" đi tới tab "Xác nhận" ở bottom nav S-Bond hay tab "Xác nhận" trong Chi tiết HĐ? (SRS ghi "Q18" nhưng chưa đóng) | Ảnh hưởng Expected điều hướng | Major | Tab "Xác nhận" ở bottom nav S-Bond |
| Q-04 | Card có thêm trạng thái "Chờ xác nhận" không? (SRS 2.3 #3 ghi "+ Chờ xác nhận?") | Bộ lọc không có giá trị này | Major | Chỉ có 4 trạng thái như bộ lọc |
| Q-05 | Placeholder ô Mã TP: SRS ghi "Tìm mã trái phiếu", Figma ghi "Nhập mã trái phiếu" | Lệch text | Minor | Theo SRS |
| Q-06 | Thứ tự field trong Bộ lọc: SRS đặt Kỳ hạn, Lợi suất trước Loại TP; Figma đặt Loại TP trước Kỳ hạn | Lệch layout | Minor | Theo Figma |
| Q-07 | Tên tham số A1 không thống nhất: symbol hay bondCode; issuerCode hay issuerCodes[]; status hay statuses[]. Mapping response lẫn `items[]` và `data.content[]`. Cần sheet API19_DanhMucHD bản mới (tên tham số, enum, HTTP status) | Ảnh hưởng case API | Major | symbol, issuerCodes[], tenors[], yieldFrom/yieldTo, bondTypes[], statuses[]; response `data.content[]` |
| Q-08 | Phân trang: page size bao nhiêu? Cơ chế cuộn vô hạn hay "Xem thêm"? | Chưa có trong SRS | Major | Cuộn tới cuối thì tự tải trang tiếp |
| Q-09 | Chip mã TP có đồng bộ với ô Mã TP trong Bộ lọc không? Bấm X có xóa các tiêu chí lọc khác không? Chọn Mã TP trong Bộ lọc có hiện chip không? | Hành vi kết hợp chưa mô tả | Major | Chip = giá trị symbol; X chỉ bỏ symbol; chip chỉ xuất hiện khi vào từ Chi tiết TP |
| Q-10 | Min/max lợi suất lấy từ đâu (Figma để 1–10)? Bước nhảy bao nhiêu? Ô số có cho nhập tay không? Biên có tính cả 2 đầu (inclusive) không? | Cần để viết case BVA | Major | min/max do hệ thống trả; bước 0,01; ô chỉ hiển thị; inclusive |
| Q-11 | Khi A1 trả BND-100, App hiển thị câu gì? (Mục 6 không có) | Thiếu message | Minor | Popup lỗi chuẩn "Có lỗi xảy ra. Vui lòng thử lại sau" |
| Q-12 | Empty state có 2 câu: điều kiện hiển thị từng câu? | Ảnh hưởng Expected | Major | Không lọc → "Quý khách chưa có hợp đồng"; có lọc/chip → "Không tìm thấy hợp đồng phù hợp" |
| Q-13 | Chip "Tất cả" và dropdown chọn nhiều: chọn giá trị cụ thể thì "Tất cả" có tự bỏ chọn? Bỏ chọn hết có tự về "Tất cả"? | Chưa mô tả | Minor | Có, cả hai |
| Q-14 | "Đặt lại" có gọi A1 ngay không? Thoát bottom sheet bằng nút back (không Lưu) thì sao? Bộ lọc có được giữ khi vào Chi tiết HĐ rồi quay lại không? | Chưa mô tả | Major | Đặt lại không gọi A1; back = hủy thay đổi; giữ bộ lọc trong phiên |
| Q-15 | Lợi tức dự kiến do BE tính hay FE tính? Mapping TCPH là `items[].issuerName` hay `data.content[].issuerName`? | Ảnh hưởng nơi kiểm số liệu | Minor | BE tính; FE chỉ format |
| Q-16 | Mã status từ Portal ứng với nhãn và màu nào? Figma chỉ có màu của "Hiệu lực" | Thiếu spec UI | Minor | Hỏi Design bổ sung |
| Q-17 | Quy tắc làm tròn lợi suất/số tiền khi API trả nhiều hơn 2 chữ số thập phân? | Chưa mô tả | Minor | Làm tròn 2 chữ số theo half-up |
| Q-18 | Vào từ noti Bond_02/Bond_06 có lọc theo HĐ/mã TP hoặc cuộn tới HĐ liên quan không? | Ảnh hưởng Expected | Major | Mở danh mục, không lọc |
| Q-19 | Hai HĐ có createdAt trùng tuyệt đối thì sắp xếp theo tiêu chí phụ nào? | Kết quả không ổn định | Minor | Không kiểm thứ tự giữa 2 HĐ trùng |
| Q-21 | Danh sách TCPH / Kỳ hạn trong dropdown lấy từ đâu (toàn bộ hay chỉ giá trị có trong HĐ của KH)? Lọc Kỳ hạn áp lên kỳ hạn HĐ (term) hay kỳ hạn TP? | Ảnh hưởng dữ liệu test | Major | Lấy từ master; lọc theo term của HĐ |
| Q-23 | HĐ "bị hủy sau xác nhận thanh toán" có ứng với trạng thái Portal nào? Phân biệt thế nào với "Hết hiệu lực" (vẫn hiển thị)? | Tránh nhầm ẩn/hiện | Major | Hủy là trạng thái riêng, không thuộc 4 trạng thái lọc |
| Q-24 | Màn tìm Mã TP: gợi ý lấy từ toàn bộ TP hay chỉ TP KH có HĐ? Có trim khoảng trắng không? Câu hiển thị khi không có kết quả? Số ký tự tối thiểu để bắt đầu gợi ý? Cần tên TP mẫu | Thiếu spec màn App_Search | Major | Chỉ TP KH có HĐ; có trim; không hiển thị gợi ý |
| Q-25 | Mất mạng hoặc timeout khi gọi A1 hiển thị câu gì? Có nút thử lại không? | Thiếu spec | Minor | Popup lỗi chuẩn SHSmart |
| Q-26 | Logic kết hợp: AND giữa các tiêu chí, OR trong cùng 1 tiêu chí? | Ảnh hưởng kết quả lọc | Major | AND giữa tiêu chí, OR trong tiêu chí |
| Q-27 | Mục 6 #2: BND-901, BND-999, AUTH_001 ứng với câu nào? Có phải theo đúng thứ tự liệt kê không? | Map code → message | Minor | Theo thứ tự liệt kê |

> Q-20, Q-22 đã tự đóng trong quá trình phân tích nên không còn trong bảng (Q-20: Thời gian đầu tư luôn tính theo tháng – theo SRS 2.3 #2; Q-22: phần Web ngoài phạm vi). Mức Blocker: 0 → chạy tiếp B2–B5.

### 6. Rủi ro nghiệp vụ chính
1. **Lộ HĐ của KH khác** (IDOR qua tham số symbol hoặc cache khi đổi tài khoản) – rủi ro bảo mật, pháp lý.
2. **Hiển thị HĐ đã Rejected/hủy** → KH tưởng mình còn tài sản, khiếu nại.
3. **Box/cảnh báo xác nhận sai** → KH bỏ sót hồ sơ cần xác nhận, quá hạn xác nhận.
4. **Lọc sai** (logic AND/OR, biên lợi suất) → KH đọc sai danh mục đầu tư.
5. **Format tiền/lãi sai** (dấu phân cách, làm tròn) → hiểu sai số tiền.

---

## B2 – CHIẾN LƯỢC TEST

### 1. Phạm vi
- **In-scope:** màn Danh mục HĐ trên SHSmart App (iOS, Android): F1–F7, API A1, phân quyền theo token, tracking event.
- **Out-of-scope:** nội dung màn Chi tiết HĐ (CONTRACT.APP.02), tab Xác nhận, luồng OTP, nội dung noti/email Bond_xx, Web, hiệu năng/tải, cụm tài sản phía trên tab (thuộc SBOND.OFFERING.APP.01).

### 2. Loại test
Functional · UI · API · Permission/Security cơ bản · Integration (Portal → App, tracking) · Error guessing hệ thống (mất mạng, double-click).

### 3. Kỹ thuật theo BR
| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC dự kiến |
|---|---|---|---|---|
| BR-01, BR-07, BR-20 | UC | EG | High | 8 |
| BR-02 | EP (phân trang) | BVA, EG | Medium | 6 |
| BR-03 | EP | BVA | High | 5 |
| BR-04, BR-24 | DT | ST, EG | High | 7 |
| BR-05, BR-06 | DT | BVA, ST | High | 10 |
| BR-08, BR-09 | ST | EP | High | 7 |
| BR-10, BR-18 | UC | ST, EG | Medium | 9 |
| BR-11 | EP | EG (security input) | Medium | 11 |
| BR-12, BR-13, BR-16, BR-17 | EP | DT, EG | Medium | 18 |
| BR-14, BR-15 | BVA | EP | High | 10 |
| BR-19 | EP | BVA, EG | Medium | 13 |
| BR-21, BR-22 | EP | EG | Medium | 9 |
| BR-23, BR-25 | EP | EG | Low | 5 |
| BR-26 | EG | – | High | 4 |

### 4. Môi trường & dữ liệu test
- **Môi trường:** UAT SHSmart App, 1 iOS + 1 Android; proxy (Charles/Proxyman) để xem request A1 và mock lỗi; Postman cho A1; quyền xem log tracking; tài khoản Portal để đổi trạng thái HĐ.
- **Tài khoản:**

| TK | Đặc điểm |
|---|---|
| KH01 | Có H01–H08 (bảng dưới), pendingConfirmCount = 3 |
| KH02 | Chưa có HĐ nào |
| KH03 | Có HĐ, pendingConfirmCount = 0 |
| KH04 | Có đúng 1 HĐ, needConfirm = true |
| KH05 | Số HĐ = page size + 1 (Q-08) |
| KH06 | Có HĐ mã ABC12601 (KH01 không nắm mã này) |
| KH07 | Chỉ có HĐ Rejected và HĐ hủy sau xác nhận TT |

- **Dữ liệu HĐ của KH01** [GIẢ ĐỊNH – dựng trên UAT]:

| HĐ | Mã TP | TCPH | Loại | Kỳ hạn | Lợi suất | Trạng thái | needConfirm | createdAt | Hiển thị |
|---|---|---|---|---|---|---|---|---|---|
| H01 | BHB291193 | Công ty Bảo hiểm nhân thọ ABC | Riêng lẻ | 12 | 8,50 | Hiệu lực | true | 10/11/2026 15:30 | Có |
| H02 | BHB291193 | ABC | Riêng lẻ | 12 | 8,00 | Chờ xử lý | true | 10/11/2026 08:00 | Có |
| H03 | TPQ12312 | Công ty CP XYZ | Niêm yết | 6 | 9,00 | Hiệu lực | true | 05/11/2026 09:00 | Có |
| H04 | TPQ12312 | XYZ | Niêm yết | 6 | 7,99 | Hoàn thành | false | 01/11/2026 10:00 | Có |
| H05 | TPQ12312 | XYZ | Niêm yết | 12 | 9,01 | Hết hiệu lực | false | 20/10/2026 10:00 | Có |
| H06 | BHB291193 | ABC | Riêng lẻ | 6 | 8,50 | Hiệu lực | false | 15/10/2026 10:00 | Có |
| H07 | BHB291193 | ABC | Riêng lẻ | 12 | 8,50 | Lệnh Rejected | – | 11/11/2026 09:00 | **Không** |
| H08 | TPQ12312 | XYZ | Niêm yết | 6 | 9,00 | Hủy sau xác nhận TT | – | 12/11/2026 09:00 | **Không** |

H01: orderNo `20261110BHB291193.0000001`, matureDate 10/11/2027, totalInvestmentAmount 435.339.324, interest 10.017.942.

- **Precondition chung PC-01:** KH01 đã đăng nhập SHSmart App, đang ở S-Bond > Nắm giữ > tab "Danh mục hợp đồng", chưa áp dụng bộ lọc.

### 5. Exit criteria
- 100% TC High đã chạy và Pass; ≥ 95% tổng TC đã chạy.
- Không còn bug Critical/Major đang mở; bug Minor còn mở có kế hoạch xử lý.
- Q&A mức Major đã được BA/PO trả lời; các TC [GIẢ ĐỊNH] đã cập nhật lại Expected.

---

## B3 – TEST CONDITION

### Decision table – Box & cảnh báo (BR-05, BR-06)
| Điều kiện | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| pendingConfirmCount | 3 | 1 | 0 | – | – |
| needConfirm của card | – | – | – | true | false |
| **Box** | Hiện, n = 3 | Hiện, n = 1 | Ẩn | – | – |
| **Dòng cảnh báo card** | – | – | – | Hiện | Ẩn |
| TC | BOX_001 | BOX_002 | BOX_003 | BOX_004 | BOX_005 |

### Decision table – Hiển thị HĐ (BR-04, BR-24)
| Tình trạng HĐ | Chờ xử lý/Hiệu lực/Hoàn thành | Hết hiệu lực | Có nhắc T-5 (Bond_08) | Lệnh Rejected (Bond_01) | Hủy sau xác nhận TT (Bond_07) |
|---|---|---|---|---|---|
| **Hiển thị** | Có | Có | Có | Không | Không |
| TC | TT_001–004 | AN_004 | AN_005 | AN_001 | AN_002, AN_003 |

### State transition – Trạng thái lọc của màn (BR-08, BR-10, BR-18)
| Từ \ Hành động | Mở Bộ lọc + Lưu (có tiêu chí) | Lưu (mặc định) | Đặt lại + Lưu | Back không Lưu | X trên chip |
|---|---|---|---|---|---|
| Không lọc | Đang lọc (LOC_002) | Không lọc (LOC_003) | Không lọc | Không lọc | ✖ |
| Đang lọc (bottom sheet) | Đang lọc | Không lọc | Không lọc (LOC_005) | Giữ lọc cũ (LOC_006) | ✖ |
| Có chip mã TP | Đang lọc + chip | – | Không lọc [Q-09] | Giữ chip | Không lọc (CHIP_002) |

### EP/BVA – Bộ lọc
| Field | Hợp lệ | Không hợp lệ / ngoại lệ | Biên |
|---|---|---|---|
| Mã TP | Mã đủ "BHB291193"; cụm giữa "2911"; chữ thường "bhb29"; cụm trong tên TP | Không khớp "XYZ999"; SQL `' OR 1=1 --`; `<script>` | Trống; khoảng trắng đầu/cuối; chọn mã thứ 2 (chỉ 1 mã) |
| TCPH / Kỳ hạn | 1 giá trị; nhiều giá trị | – | Bỏ chọn hết → "Tất cả" |
| Lợi suất | 8,00–9,00 | từ > đến (UI chặn, API BND-100); ngoài min–max | 7,99 / 8,00 / 9,00 / 9,01; từ = đến; min, max |
| Loại TP | Niêm yết; Riêng lẻ; cả 2 | – | Bỏ hết → "Tất cả" |
| Trạng thái | Từng trạng thái (4); kết hợp 2 | Status API không hợp lệ | "Tất cả" khi đang chọn chip cụ thể |

### Use case
- **Luồng chính:** mở tab → A1 → box → danh sách → bấm card → Chi tiết HĐ.
- **Luồng phụ:** vào từ Chi tiết TP (chip); vào từ noti; "Kiểm tra" → tab Xác nhận; lọc/bỏ lọc; phân trang.
- **Ngoại lệ:** danh sách rỗng; lỗi BND-901/999; AUTH_001; mất mạng; HĐ bị hủy khi đang xem.

### Error guessing áp dụng (file 03)
Double-click Lưu · mất mạng khi mở màn/Lưu/tải trang · back không Lưu · IDOR qua symbol · không token · đổi tài khoản trên cùng thiết bị · chuỗi dài / tên dài · số tiền rất lớn · làm tròn · danh sách 1 bản ghi / trang cuối.

---

## B4 – TEST CASE

Tách 2 phần: **B4.1 App** (kiểm trên giao diện, có thể xem request qua proxy) và **B4.2 API** (gọi trực tiếp A1 bằng Postman). Số TC dự kiến ở B2 là ước lượng trước khi tách; số thực tế xem B5.

> Precondition chung **PC-01** và dữ liệu KH01–KH07, H01–H08: xem B2 mục 4.


### B4.1 App – Layout (2 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_UI_001 | BR-01 | Danh mục HĐ – Layout | Kiểm tra layout mặc định màn Danh mục hợp đồng khi KH có HĐ | PC-01 | 1. Mở S-Bond<br>2. Chọn tab "Nắm giữ"<br>3. Chọn tab "Danh mục hợp đồng" | KH01 | - Tab "Danh mục hợp đồng" active (chữ + gạch chân cam)<br>- Header "SH Bond": icon lọc (màu thường), icon (i)<br>- Thứ tự: box HĐ cần xác nhận → danh sách card<br>- Bottom nav: tab "Nắm giữ" active | Low | UI | EG | Đối chiếu Figma màn 1 |
| TC_HDDM_UI_002 | BR-19 | Danh mục HĐ – Layout | Kiểm tra bố cục 1 card HĐ khi hiển thị đủ field | PC-01 | 1. Quan sát card H01 | KH01 – H01 | - Dòng 1: Mã TP (cam) + chip "12 tháng" + nhãn trạng thái (phải)<br>- Dòng 2: Tên TCPH<br>- Dòng 3: Mã HĐ<br>- Lưới 2 cột: Ngày đáo hạn \| Tổng tiền đầu tư<br>- Lưới 2 cột: Lợi suất \| Lợi tức dự kiến<br>- Cuối card: dòng cảnh báo (nếu có) | Low | UI | EG | Đối chiếu Figma |

### B4.1 App – Bộ lọc (9 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_UI_003 | BR-10, BR-12, BR-13, BR-14, BR-16, BR-17, BR-18 | Danh mục HĐ – Bộ lọc | Kiểm tra layout và giá trị mặc định bottom sheet "Bộ lọc" khi mở lần đầu | PC-01 | 1. Click icon lọc | KH01 | - Tiêu đề "Bộ lọc", nút back (<)<br>- Mã trái phiếu: placeholder "Tìm mã trái phiếu"<br>- TCPH: "Tất cả tổ chức"<br>- Loại trái phiếu: chip "Tất cả" active<br>- Kỳ hạn: "Tất cả kỳ hạn"<br>- Lợi suất (%): 2 đầu ở min và max<br>- Trạng thái HĐ: chip "Tất cả" active<br>- Nút "Đặt lại" (viền), "Lưu" (cam) | Low | UI | EG | [GIẢ ĐỊNH] placeholder & thứ tự field – Q-05, Q-06 |
| TC_HDDM_LOC_001 | BR-10 | Danh mục HĐ – Bộ lọc | Kiểm tra mở bottom sheet Bộ lọc khi click icon lọc | PC-01 | 1. Click icon lọc trên header | – | - Bottom sheet "Bộ lọc" mở, phủ lên danh sách<br>- Chưa gọi A1 | Medium | Positive | UC |  |
| TC_HDDM_LOC_002 | BR-18 | Danh mục HĐ – Bộ lọc | Kiểm tra lưu bộ lọc khi chọn 1 tiêu chí | PC-01 | 1. Click icon lọc<br>2. Chọn Trạng thái = "Hoàn thành"<br>3. Click "Lưu" | Trạng thái = Hoàn thành | - Bottom sheet đóng<br>- A1 gửi statuses = Hoàn thành<br>- Chỉ hiển thị H04<br>- Icon lọc màu cam | High | Positive | UC | [GIẢ ĐỊNH] tên tham số – Q-07 |
| TC_HDDM_LOC_003 | BR-18 | Danh mục HĐ – Bộ lọc | Kiểm tra lưu bộ lọc khi giữ toàn bộ giá trị mặc định | PC-01 | 1. Click icon lọc<br>2. Không thay đổi field nào<br>3. Click "Lưu" | Mặc định | - Bottom sheet đóng<br>- A1 không có tham số lọc<br>- Hiển thị 6 card<br>- Icon lọc màu thường | Medium | Boundary | EP |  |
| TC_HDDM_LOC_004 | BR-18 | Danh mục HĐ – Bộ lọc | Kiểm tra nút "Đặt lại" đưa các field về mặc định | PC-01 | 1. Click icon lọc<br>2. Chọn TCPH = Công ty CP XYZ, Loại = Niêm yết, Trạng thái = Hiệu lực<br>3. Click "Đặt lại" | Như step 2 | - Các field về mặc định<br>- TCPH = "Tất cả tổ chức", chip "Tất cả" active, slider min–max<br>- Bottom sheet vẫn mở<br>- Chưa gọi A1 | Medium | Positive | ST | [GIẢ ĐỊNH] Đặt lại không gọi A1 – Q-14 |
| TC_HDDM_LOC_005 | BR-18 | Danh mục HĐ – Bộ lọc | Kiểm tra bỏ toàn bộ lọc khi Đặt lại rồi Lưu | PC-01; đang lọc Trạng thái = Hiệu lực (3 card) | 1. Click icon lọc<br>2. Click "Đặt lại"<br>3. Click "Lưu" | – | - Bottom sheet đóng<br>- A1 không có tham số lọc<br>- Hiển thị 6 card<br>- Icon lọc về màu thường | Medium | Positive | ST |  |
| TC_HDDM_LOC_006 | BR-18 | Danh mục HĐ – Bộ lọc | Kiểm tra đóng Bộ lọc bằng nút back khi chưa Lưu | PC-01; đang lọc Trạng thái = Hiệu lực | 1. Click icon lọc<br>2. Đổi Trạng thái sang "Hoàn thành"<br>3. Click back (<)<br>4. Click icon lọc lần nữa | – | - Sau step 3: không gọi A1, vẫn 3 card Hiệu lực<br>- Sau step 4: Trạng thái hiển thị "Hiệu lực" | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-14 |
| TC_HDDM_LOC_007 | BR-18, BR-12, BR-13, BR-17 | Danh mục HĐ – Bộ lọc | Kiểm tra kết hợp nhiều tiêu chí lọc (AND giữa các tiêu chí) | PC-01 | 1. Click icon lọc<br>2. Chọn TCPH = Công ty Bảo hiểm nhân thọ ABC<br>3. Chọn Kỳ hạn = 12 tháng<br>4. Chọn Trạng thái = Hiệu lực<br>5. Click "Lưu" | ABC + 12 tháng + Hiệu lực | Chỉ hiển thị H01 | High | Positive | DT | [GIẢ ĐỊNH] AND giữa tiêu chí, OR trong 1 tiêu chí – Q-26 |
| TC_HDDM_LOC_008 | BR-18 | Danh mục HĐ – Bộ lọc | Kiểm tra double-click nút "Lưu" | PC-01 | 1. Click icon lọc<br>2. Chọn Trạng thái = Hiệu lực<br>3. Double-click "Lưu" | – | - Chỉ 1 request A1<br>- Bottom sheet đóng 1 lần<br>- Không có card trùng | Low | Negative | EG |  |

### B4.1 App – Mở màn (4 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_NAV_001 | BR-01, BR-02 | Danh mục HĐ – Mở màn | Kiểm tra mở tab Danh mục hợp đồng khi không có bộ lọc | KH01 đăng nhập, đang ở tab "Danh mục Trái phiếu" của Nắm giữ | 1. Click tab "Danh mục hợp đồng"<br>2. Kiểm tra request A1 (proxy/log) | KH01 (H01–H08) | - A1 được gọi, không có tham số lọc<br>- Hiển thị 6 card H01–H06<br>- Không có H07 (Rejected), H08 (hủy) | High | Positive | UC |  |
| TC_HDDM_NAV_002 | BR-01, BR-08 | Danh mục HĐ – Mở màn | Kiểm tra vào màn từ Chi tiết TP qua nút "Xem danh mục hợp đồng" | KH01 đăng nhập; đang ở Chi tiết TP BHB291193 (HOLDING.APP.02) | 1. Click "Xem danh mục hợp đồng"<br>2. Kiểm tra request A1 | symbol = BHB291193 | - Mở tab "Danh mục hợp đồng"<br>- Có chip "Mã trái phiếu BHB291193" + nút X<br>- A1 gửi symbol = BHB291193<br>- Chỉ hiển thị H01, H02, H06 | High | Positive | UC | [GIẢ ĐỊNH] tên tham số symbol – Q-07 |
| TC_HDDM_NAV_003 | BR-01 | Danh mục HĐ – Mở màn | Kiểm tra mở màn khi click notification Bond_02 | KH01 đăng nhập; đã nhận noti Bond_02 | 1. Mở trung tâm thông báo<br>2. Click noti Bond_02 | Noti Bond_02 | - Mở màn Danh mục hợp đồng, gọi A1<br>- Hiển thị 6 card H01–H06<br>- Không có chip lọc | Medium | Positive | UC | [GIẢ ĐỊNH] mở không lọc – Q-18 |
| TC_HDDM_NAV_004 | BR-01 | Danh mục HĐ – Mở màn | Kiểm tra mở màn khi click notification Bond_06 | KH01 đăng nhập; đã nhận noti Bond_06 | 1. Mở trung tâm thông báo<br>2. Click noti Bond_06 | Noti Bond_06 | - Mở màn Danh mục hợp đồng, gọi A1<br>- Hiển thị 6 card H01–H06<br>- Không có chip lọc | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-18 |

### B4.1 App – Điều hướng (3 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_NAV_005 | BR-20 | Danh mục HĐ – Điều hướng | Kiểm tra mở Chi tiết HĐ khi click card | PC-01 | 1. Click vùng card H01 | H01: orderNo 20261110BHB291193.0000001 | - Mở "Chi tiết hợp đồng", tab "Tổng quan" active<br>- Số HĐ = 20261110BHB291193.0000001 | High | Positive | UC |  |
| TC_HDDM_NAV_006 | BR-20, BR-18 | Danh mục HĐ – Điều hướng | Kiểm tra quay lại từ Chi tiết HĐ khi đang áp dụng bộ lọc | PC-01; đã lọc Trạng thái = Hiệu lực (còn H01, H03, H06) | 1. Click card H03<br>2. Click back (<) ở Chi tiết HĐ | Trạng thái = Hiệu lực | - Quay về Danh mục hợp đồng<br>- Vẫn lọc Hiệu lực: H01, H03, H06<br>- Icon lọc vẫn tô màu cam | Medium | Positive | EG | [GIẢ ĐỊNH] giữ bộ lọc – Q-14 |
| TC_HDDM_NAV_007 | BR-07 | Danh mục HĐ – Điều hướng | Kiểm tra điều hướng khi click nút "Kiểm tra" trên box | PC-01 (pendingConfirmCount = 3) | 1. Click nút "Kiểm tra" trên box | KH01 | - Mở tab "Xác nhận" của S-Bond<br>- Có hồ sơ cần xác nhận của H01, H02, H03 | High | Positive | UC | [GIẢ ĐỊNH] đích điều hướng – Q-03 |

### B4.1 App – Box cần xác nhận (10 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_BOX_001 | BR-05 | Danh mục HĐ – Box cần xác nhận | Kiểm tra hiển thị box khi pendingConfirmCount = 3 | PC-01 | 1. Mở tab "Danh mục hợp đồng" | KH01: pendingConfirmCount = 3 | - Box hiển thị phía trên danh sách card<br>- Text: "Quý khách có 3 hợp đồng chờ xác nhận"<br>- Có icon cảnh báo và nút "Kiểm tra" | High | Positive | DT | [GIẢ ĐỊNH] câu chữ – Q-01 |
| TC_HDDM_BOX_002 | BR-05 | Danh mục HĐ – Box cần xác nhận | Kiểm tra hiển thị box khi pendingConfirmCount = 1 | KH04 đăng nhập, ở tab Danh mục hợp đồng | 1. Mở tab "Danh mục hợp đồng" | KH04: pendingConfirmCount = 1 | - Box hiển thị<br>- Text: "Quý khách có 1 hợp đồng chờ xác nhận"<br>- Có nút "Kiểm tra" | Medium | Boundary | BVA | Q-01 |
| TC_HDDM_BOX_003 | BR-05 | Danh mục HĐ – Box cần xác nhận | Kiểm tra ẩn box khi pendingConfirmCount = 0 | KH03 đăng nhập | 1. Mở tab "Danh mục hợp đồng" | KH03: pendingConfirmCount = 0 | - Không hiển thị box<br>- Card đầu tiên nằm ngay dưới thanh tab<br>- Không card nào có dòng cảnh báo | High | Boundary | BVA |  |
| TC_HDDM_BOX_004 | BR-06 | Danh mục HĐ – Box cần xác nhận | Kiểm tra dòng cảnh báo trên card khi needConfirm = true | PC-01 | 1. Quan sát card H01 | H01: needConfirm = true | - Cuối card H01 có icon cảnh báo<br>- Text: "Hợp đồng có hồ sơ cần xác nhận" | High | Positive | DT |  |
| TC_HDDM_BOX_005 | BR-06 | Danh mục HĐ – Box cần xác nhận | Kiểm tra không hiển thị cảnh báo trên card khi needConfirm = false | PC-01 | 1. Quan sát card H04 | H04: needConfirm = false | Card H04 không có dòng "Hợp đồng có hồ sơ cần xác nhận" | Medium | Negative | DT |  |
| TC_HDDM_BOX_006 | BR-05, BR-06 | Danh mục HĐ – Box cần xác nhận | Kiểm tra số n trên box khớp số card có cảnh báo khi không lọc | PC-01 | 1. Đếm số card có dòng cảnh báo<br>2. So sánh với n trên box | KH01 | - Có 3 card cảnh báo: H01, H02, H03<br>- Số card cảnh báo = n trên box = 3 | Medium | Positive | EG | [GIẢ ĐỊNH] n = số HĐ needConfirm – Q-02 |
| TC_HDDM_BOX_007 | BR-05, BR-08 | Danh mục HĐ – Box cần xác nhận | Kiểm tra box khi đang lọc mã TP có HĐ cần xác nhận khác mã đang lọc | KH01 đăng nhập | 1. Vào Danh mục HĐ từ Chi tiết TP TPQ12312<br>2. Quan sát box | symbol = TPQ12312 (H03 cần xác nhận) | - Box vẫn hiển thị<br>- Text: "Quý khách có 3 hợp đồng chờ xác nhận"<br>- n đếm trên toàn bộ HĐ, không theo lọc | Medium | Positive | EG | [GIẢ ĐỊNH] – Q-02 |
| TC_HDDM_BOX_008 | BR-05, BR-06 | Danh mục HĐ – Box cần xác nhận | Kiểm tra cập nhật box và card sau khi KH xác nhận xong 1 HĐ | PC-01 (n = 3) | 1. Click "Kiểm tra"<br>2. Xác nhận hồ sơ của H01 (OTP 1111 trên môi trường test)<br>3. Click "Về Danh mục nắm giữ"<br>4. Mở tab "Danh mục hợp đồng" | H01 | - Box: n = 2<br>- Card H01 hết dòng cảnh báo<br>- H02, H03 vẫn có cảnh báo | High | Positive | ST |  |
| TC_HDDM_BOX_009 | BR-05 | Danh mục HĐ – Box cần xác nhận | Kiểm tra ẩn box sau khi KH xác nhận hết HĐ cần xác nhận | KH04 (n = 1) | 1. Click "Kiểm tra"<br>2. Xác nhận hồ sơ HĐ duy nhất<br>3. Quay lại tab "Danh mục hợp đồng" | KH04 | - Không còn box<br>- Không card nào có dòng cảnh báo | High | Boundary | ST |  |
| TC_HDDM_AN_006 | BR-04, BR-05 | Danh mục HĐ – Box cần xác nhận | Kiểm tra không hiển thị box khi KH chỉ có HĐ Rejected/hủy từng có hồ sơ cần xác nhận | KH07 đăng nhập (chỉ có HĐ Rejected + HĐ hủy, từng có hồ sơ cần xác nhận) | 1. Mở tab "Danh mục hợp đồng" | KH07 | - Không hiển thị box<br>- Không có card nào | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-02 |

### B4.1 App – Sắp xếp (5 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_SORT_001 | BR-03 | Danh mục HĐ – Sắp xếp | Kiểm tra thứ tự danh sách theo Ngày tạo HĐ mới → cũ | PC-01 | 1. Mở tab "Danh mục hợp đồng"<br>2. Ghi lại thứ tự card | KH01: H03 05/11/2026 09:00; H04 01/11/2026 10:00; H05 20/10/2026 | Thứ tự: H01 → H02 → H03 → H04 → H05 → H06 | High | Positive | EP |  |
| TC_HDDM_SORT_002 | BR-03 | Danh mục HĐ – Sắp xếp | Kiểm tra thứ tự khi 2 HĐ tạo cùng ngày khác giờ | PC-01 | 1. Quan sát vị trí H01 và H02 | H01 10/11/2026 15:30; H02 10/11/2026 08:00 | H01 nằm trên H02 | Medium | Boundary | BVA | Q-19: trùng tuyệt đối createdAt chưa có tiêu chí phụ |
| TC_HDDM_SORT_003 | BR-03 | Danh mục HĐ – Sắp xếp | Kiểm tra thứ tự vẫn mới → cũ khi đang lọc | PC-01 | 1. Mở Bộ lọc<br>2. Chọn Trạng thái = Hiệu lực<br>3. Click "Lưu" | Trạng thái = Hiệu lực | Thứ tự: H01 → H03 → H06 | Medium | Positive | EP |  |
| TC_HDDM_SORT_004 | BR-03, BR-02 | Danh mục HĐ – Sắp xếp | Kiểm tra thứ tự liên tục giữa trang 1 và trang 2 | KH05 đăng nhập (số HĐ = page size + 1) | 1. Mở tab "Danh mục hợp đồng"<br>2. Cuộn xuống cuối danh sách | KH05 | - createdAt HĐ đầu trang 2 ≤ HĐ cuối trang 1<br>- Không có card trùng Mã HĐ | Medium | Positive | EG | [GIẢ ĐỊNH] cơ chế phân trang – Q-08 |
| TC_HDDM_SORT_005 | BR-03 | Danh mục HĐ – Sắp xếp | Kiểm tra HĐ vừa đặt mua xuất hiện đầu danh sách | PC-01 | 1. Đặt mua TP BHB291193 thành công (lệnh khớp)<br>2. Mở lại tab "Danh mục hợp đồng" | Lệnh mới tạo HĐ H09 createdAt = thời điểm test | H09 nằm đầu danh sách (trên H01) | Medium | Positive | UC |  |

### B4.1 App – Lọc ẩn HĐ (5 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_AN_001 | BR-04 | Danh mục HĐ – Lọc ẩn HĐ | Kiểm tra không hiển thị HĐ của lệnh không khớp (Rejected) | PC-01 | 1. Mở tab "Danh mục hợp đồng"<br>2. Tìm card có Mã HĐ của H07 | H07: lệnh Rejected, KH đã nhận Bond_01 | Không có card H07 | High | Negative | DT |  |
| TC_HDDM_AN_002 | BR-04 | Danh mục HĐ – Lọc ẩn HĐ | Kiểm tra không hiển thị HĐ bị hủy sau xác nhận thanh toán | PC-01 | 1. Mở tab "Danh mục hợp đồng"<br>2. Tìm card có Mã HĐ của H08 | H08: hủy sau xác nhận TT, KH đã nhận Bond_07 | Không có card H08 | High | Negative | DT |  |
| TC_HDDM_AN_003 | BR-04 | Danh mục HĐ – Lọc ẩn HĐ | Kiểm tra HĐ đang hiển thị biến mất sau khi giao dịch bị hủy sau xác nhận thanh toán | PC-01; H02 đang hiển thị | 1. BE/Ops hủy giao dịch của H02 sau xác nhận thanh toán<br>2. Mở lại tab "Danh mục hợp đồng" | H02 | - Card H02 không còn hiển thị<br>- KH nhận noti/email Bond_07<br>- Số dư khả dụng được hoàn lại (revert sub88) | High | Negative | ST |  |
| TC_HDDM_AN_004 | BR-04 | Danh mục HĐ – Lọc ẩn HĐ | Kiểm tra HĐ trạng thái Hết hiệu lực vẫn hiển thị | PC-01 | 1. Tìm card H05 | H05: Hết hiệu lực | Card H05 hiển thị, nhãn "Hết hiệu lực" | Medium | Positive | EP | Q-23: phân biệt Hết hiệu lực và Hủy |
| TC_HDDM_AN_005 | BR-24 | Danh mục HĐ – Lọc ẩn HĐ | Kiểm tra HĐ vẫn hiển thị khi có nhắc T-5 hoàn thiện số lượng TP | PC-01; H06 đã nhận noti Bond_08 | 1. Mở tab "Danh mục hợp đồng" | H06 | - Card H06 vẫn hiển thị đủ thông tin<br>- Nhãn và vị trí không đổi | Medium | Positive | UC |  |

### B4.1 App – Chip mã TP (6 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_CHIP_001 | BR-08 | Danh mục HĐ – Chip mã TP | Kiểm tra hiển thị chip lọc mã TP khi vào từ Chi tiết TP | KH01; vào từ Chi tiết TP BHB291193 | 1. Quan sát vùng dưới thanh tab | BHB291193 | - Chip nền cam nhạt, icon phễu<br>- Nhãn "Mã trái phiếu", giá trị "BHB291193"<br>- Nút X bên phải | Low | UI | EG |  |
| TC_HDDM_CHIP_002 | BR-08 | Danh mục HĐ – Chip mã TP | Kiểm tra bỏ lọc khi click X trên chip mã TP | KH01; đang có chip BHB291193 (3 card) | 1. Click X trên chip<br>2. Kiểm tra request A1 | BHB291193 | - Chip biến mất<br>- A1 gọi lại, không có symbol<br>- Hiển thị 6 card H01–H06 | High | Positive | ST |  |
| TC_HDDM_CHIP_003 | BR-10, BR-08 | Danh mục HĐ – Chip mã TP | Kiểm tra icon lọc đổi màu khi đang có chip mã TP | KH01; vào từ Chi tiết TP BHB291193 | 1. Quan sát icon lọc trên header | BHB291193 | - Đang có chip: icon lọc màu cam<br>- Sau khi bấm X: icon về màu thường | Low | UI | ST |  |
| TC_HDDM_CHIP_004 | BR-09, BR-21 | Danh mục HĐ – Chip mã TP | Kiểm tra empty state khi mã TP thuộc danh mục KH nhưng không còn HĐ hiển thị | KH07 đăng nhập; vào từ Chi tiết TP của mã có HĐ đều Rejected/hủy | 1. Click "Xem danh mục hợp đồng" | Mã TP của KH07 | - Vẫn hiển thị chip mã TP<br>- Empty state: "Không tìm thấy hợp đồng phù hợp"<br>- Không có card | Medium | Negative | EP | [GIẢ ĐỊNH] câu empty – Q-12 |
| TC_HDDM_CHIP_005 | BR-08, BR-11 | Danh mục HĐ – Chip mã TP | Kiểm tra ô Mã TP trong Bộ lọc khi đang có chip mã TP | KH01; đang có chip BHB291193 | 1. Click icon lọc | BHB291193 | Ô "Mã trái phiếu" hiển thị BHB291193 | Medium | Positive | EG | [GIẢ ĐỊNH] chip đồng bộ ô Mã TP – Q-09 |
| TC_HDDM_CHIP_006 | BR-08, BR-11 | Danh mục HĐ – Chip mã TP | Kiểm tra ô Mã TP trong Bộ lọc sau khi bỏ chip | KH01; vừa click X bỏ chip BHB291193 | 1. Click icon lọc | – | Ô "Mã trái phiếu" trống, hiện placeholder | Medium | Positive | EG | [GIẢ ĐỊNH] – Q-09 |

### B4.1 App – Lọc Mã TP (11 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_MATP_001 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra mở màn tìm "Mã trái phiếu" khi click ô Mã TP | PC-01; đang mở Bộ lọc | 1. Click ô "Mã trái phiếu" | – | - Mở màn "Mã trái phiếu" (Figma App_Search mã TP)<br>- Có ô nhập, bàn phím bật lên | Medium | Positive | UC |  |
| TC_HDDM_MATP_002 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra lọc theo mã TP khi nhập đủ mã và chọn gợi ý | PC-01; đang ở màn "Mã trái phiếu" | 1. Nhập "BHB291193"<br>2. Chọn gợi ý BHB291193<br>3. Click "Lưu" ở Bộ lọc | BHB291193 | - Gợi ý: BHB291193 – Công ty Bảo hiểm nhân thọ ABC<br>- Sau khi chọn: ô Mã TP = BHB291193<br>- Sau Lưu: A1 gửi symbol = BHB291193<br>- Hiển thị H01, H02, H06 | High | Positive | EP |  |
| TC_HDDM_MATP_003 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra gợi ý khi nhập cụm ký tự nằm giữa mã TP | Đang ở màn "Mã trái phiếu" | 1. Nhập "2911" | 2911 | - Gợi ý có BHB291193<br>- Không có TPQ12312 | Medium | Positive | EP |  |
| TC_HDDM_MATP_004 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra tìm mã TP không phân biệt hoa thường | Đang ở màn "Mã trái phiếu" | 1. Nhập "bhb29" | bhb29 | Gợi ý có BHB291193 | Medium | Positive | EP |  |
| TC_HDDM_MATP_005 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra tìm theo cụm từ trong tên TP | Đang ở màn "Mã trái phiếu" | 1. Nhập một cụm có trong tên TP BHB291193 | Cụm tên TP lấy từ Portal [GIẢ ĐỊNH] | Gợi ý có BHB291193 | Medium | Positive | EP | Q-24: chưa có tên TP mẫu |
| TC_HDDM_MATP_006 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra tìm khi từ khóa không khớp mã/tên TP nào | Đang ở màn "Mã trái phiếu" | 1. Nhập "XYZ999" | XYZ999 | - Không có gợi ý<br>- Ô Mã TP ở Bộ lọc vẫn trống | Medium | Negative | EP | [GIẢ ĐỊNH] text khi không có gợi ý – Q-24 |
| TC_HDDM_MATP_007 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra chỉ chọn được 1 mã TP | Đang ở Bộ lọc, ô Mã TP = BHB291193 | 1. Click ô Mã TP<br>2. Nhập "TPQ" và chọn TPQ12312<br>3. Click "Lưu" | TPQ12312 | - Ô Mã TP = TPQ12312 (thay BHB291193, không cộng dồn)<br>- A1 gửi symbol = TPQ12312<br>- Hiển thị H03, H04, H05 | Medium | Negative | EP |  |
| TC_HDDM_MATP_008 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra không lọc theo mã khi để trống ô Mã TP | PC-01 | 1. Click icon lọc<br>2. Không nhập Mã TP, chọn Loại = Niêm yết<br>3. Click "Lưu" | Mã TP trống | - A1 không gửi symbol<br>- Hiển thị H03, H04, H05 | Medium | Boundary | EP |  |
| TC_HDDM_MATP_009 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra nhập chuỗi SQL injection vào ô tìm mã TP | Đang ở màn "Mã trái phiếu" | 1. Nhập chuỗi ở Test Data | ' OR 1=1 -- | - Không có gợi ý<br>- App không crash<br>- Không trả về toàn bộ danh sách TP | Medium | Security | EG |  |
| TC_HDDM_MATP_010 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra nhập script HTML vào ô tìm mã TP | Đang ở màn "Mã trái phiếu" | 1. Nhập chuỗi ở Test Data | <script>alert(1)</script> | - Chuỗi hiển thị dạng text<br>- Không thực thi script<br>- Không có gợi ý | Low | Security | EG |  |
| TC_HDDM_MATP_011 | BR-11 | Danh mục HĐ – Lọc Mã TP | Kiểm tra từ khóa có khoảng trắng đầu/cuối | Đang ở màn "Mã trái phiếu" | 1. Nhập "  BHB291193  " | "  BHB291193  " | Gợi ý có BHB291193 | Low | Boundary | EG | [GIẢ ĐỊNH] có trim – Q-24 |

### B4.1 App – Lọc TCPH (4 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_TCPH_001 | BR-12 | Danh mục HĐ – Lọc TCPH | Kiểm tra lọc khi chọn 1 TCPH | PC-01 | 1. Click icon lọc<br>2. Mở dropdown TCPH, chọn "Công ty Bảo hiểm nhân thọ ABC"<br>3. Click "Lưu" | ABC | - A1 gửi issuerCodes = [ABC]<br>- Chỉ hiển thị H01, H02, H06 | Medium | Positive | EP | Q-07 |
| TC_HDDM_TCPH_002 | BR-12 | Danh mục HĐ – Lọc TCPH | Kiểm tra lọc khi chọn 2 TCPH | PC-01 | 1. Click icon lọc<br>2. Chọn "Công ty Bảo hiểm nhân thọ ABC" và "Công ty CP XYZ"<br>3. Click "Lưu" | ABC, XYZ | - Dropdown hiển thị 2 TCPH đã chọn<br>- Hiển thị H01–H06 | Medium | Positive | EP |  |
| TC_HDDM_TCPH_003 | BR-12 | Danh mục HĐ – Lọc TCPH | Kiểm tra dropdown TCPH khi bỏ chọn hết | Đang mở Bộ lọc, TCPH = ABC | 1. Mở dropdown TCPH<br>2. Bỏ chọn ABC | – | Dropdown hiển thị "Tất cả tổ chức" | Low | Boundary | EG | [GIẢ ĐỊNH] – Q-13 |
| TC_HDDM_TCPH_004 | BR-12 | Danh mục HĐ – Lọc TCPH | Kiểm tra hiển thị dropdown TCPH khi danh sách dài và tên dài | Môi trường có ≥ 20 TCPH [GIẢ ĐỊNH] | 1. Mở dropdown TCPH<br>2. Cuộn xuống cuối | Tên ≥ 60 ký tự | - Cuộn được tới phần tử cuối<br>- Tên dài xuống dòng hoặc "...", không đè checkbox | Low | UI | EG | Q-21: nguồn danh sách TCPH |

### B4.1 App – Lọc Kỳ hạn (3 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_KH_001 | BR-13 | Danh mục HĐ – Lọc Kỳ hạn | Kiểm tra lọc khi chọn 1 kỳ hạn | PC-01 | 1. Click icon lọc<br>2. Chọn Kỳ hạn = 12 tháng<br>3. Click "Lưu" | 12 tháng | - A1 gửi tenors = [12]<br>- Hiển thị H01, H02, H05<br>- Chip trên các card = "12 tháng" | Medium | Positive | EP | [GIẢ ĐỊNH] lọc theo term HĐ – Q-21 |
| TC_HDDM_KH_002 | BR-13 | Danh mục HĐ – Lọc Kỳ hạn | Kiểm tra lọc khi chọn nhiều kỳ hạn | PC-01 | 1. Click icon lọc<br>2. Chọn 6 tháng và 12 tháng<br>3. Click "Lưu" | 6, 12 tháng | Hiển thị H01–H06 | Medium | Positive | EP |  |
| TC_HDDM_KH_003 | BR-13 | Danh mục HĐ – Lọc Kỳ hạn | Kiểm tra dropdown Kỳ hạn khi bỏ chọn hết | Đang mở Bộ lọc, Kỳ hạn = 12 tháng | 1. Mở dropdown Kỳ hạn<br>2. Bỏ chọn 12 tháng | – | Dropdown hiển thị "Tất cả kỳ hạn" | Low | Boundary | EG | [GIẢ ĐỊNH] – Q-13 |

### B4.1 App – Lọc Lợi suất (8 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_LS_001 | BR-14 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra hiển thị HĐ có lợi suất bằng cận dưới | PC-01 | 1. Click icon lọc<br>2. Kéo Lợi suất về 8,00 – 9,00<br>3. Click "Lưu" | Khoảng 8,00–9,00; H02 = 8,00% | Có H02 (8,00%) | High | Boundary | BVA | [GIẢ ĐỊNH] biên inclusive – Q-10 |
| TC_HDDM_LS_002 | BR-14 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra hiển thị HĐ có lợi suất bằng cận trên | PC-01 | 1. Click icon lọc<br>2. Kéo Lợi suất về 8,00 – 9,00<br>3. Click "Lưu" | H03 = 9,00% | Có H03 (9,00%) | High | Boundary | BVA | Q-10 |
| TC_HDDM_LS_003 | BR-14 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra ẩn HĐ có lợi suất nhỏ hơn cận dưới 0,01 | PC-01 | 1. Click icon lọc<br>2. Kéo Lợi suất về 8,00 – 9,00<br>3. Click "Lưu" | H04 = 7,99% | - Không có H04 (7,99%)<br>- Kết quả: H01, H02, H03, H06 | Medium | Boundary | BVA |  |
| TC_HDDM_LS_004 | BR-14 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra ẩn HĐ có lợi suất lớn hơn cận trên 0,01 | PC-01 | 1. Click icon lọc<br>2. Kéo Lợi suất về 8,00 – 9,00<br>3. Click "Lưu" | H05 = 9,01% | Không có H05 (9,01%) | Medium | Boundary | BVA |  |
| TC_HDDM_LS_005 | BR-15 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra lọc khi cận dưới bằng cận trên | PC-01 | 1. Click icon lọc<br>2. Kéo 2 đầu cùng về 8,50<br>3. Click "Lưu" | 8,50 – 8,50 | - Lưu được<br>- Hiển thị H01, H06 | Medium | Boundary | BVA |  |
| TC_HDDM_LS_006 | BR-15 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra chặn kéo đầu "từ" vượt qua đầu "đến" | Đang mở Bộ lọc; Lợi suất 8,00 – 9,00 | 1. Kéo đầu trái sang phải vượt qua đầu phải | – | - Đầu trái dừng tại đầu phải (từ ≤ đến)<br>- Ô "từ" không lớn hơn ô "đến" | Medium | Negative | BVA |  |
| TC_HDDM_LS_007 | BR-15 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra chặn kéo vượt min và max của thanh trượt | Đang mở Bộ lọc | 1. Kéo đầu trái về hết bên trái<br>2. Kéo đầu phải về hết bên phải | min/max theo hệ thống | - Hai đầu dừng đúng tại min, max<br>- Ô số hiển thị min, max | Medium | Boundary | BVA | Q-10: nguồn min/max |
| TC_HDDM_LS_008 | BR-14 | Danh mục HĐ – Lọc Lợi suất | Kiểm tra ô số đồng bộ với vị trí thanh trượt | Đang mở Bộ lọc | 1. Kéo đầu trái tới 8,00<br>2. Kéo đầu phải tới 9,00 | – | - Ô trái: 8,00<br>- Ô phải: 9,00 | Low | UI | EG | Q-10: ô số có cho nhập tay? |

### B4.1 App – Lọc Loại TP (4 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_LTP_001 | BR-16 | Danh mục HĐ – Lọc Loại TP | Kiểm tra lọc khi chọn chip "Niêm yết" | PC-01 | 1. Click icon lọc<br>2. Click chip "Niêm yết"<br>3. Click "Lưu" | Niêm yết | - Chip "Niêm yết" active, "Tất cả" bỏ active<br>- A1 gửi bondTypes = [Niêm yết]<br>- Hiển thị H03, H04, H05 | Medium | Positive | EP | [GIẢ ĐỊNH] hành vi chip Tất cả – Q-13 |
| TC_HDDM_LTP_002 | BR-16 | Danh mục HĐ – Lọc Loại TP | Kiểm tra lọc khi chọn chip "Riêng lẻ" | PC-01 | 1. Click icon lọc<br>2. Click chip "Riêng lẻ"<br>3. Click "Lưu" | Riêng lẻ | Hiển thị H01, H02, H06 | Medium | Positive | EP |  |
| TC_HDDM_LTP_003 | BR-16 | Danh mục HĐ – Lọc Loại TP | Kiểm tra lọc khi chọn cả Niêm yết và Riêng lẻ | PC-01 | 1. Click icon lọc<br>2. Click "Niêm yết" rồi "Riêng lẻ"<br>3. Click "Lưu" | Niêm yết + Riêng lẻ | Hiển thị H01–H06 | Low | Positive | EP |  |
| TC_HDDM_LTP_004 | BR-16 | Danh mục HĐ – Lọc Loại TP | Kiểm tra chip Loại TP khi bỏ chọn hết | Đang mở Bộ lọc, chỉ chip "Niêm yết" active | 1. Click lại chip "Niêm yết" | – | Chip "Tất cả" tự active lại | Low | Boundary | EG | [GIẢ ĐỊNH] – Q-13 |

### B4.1 App – Lọc Trạng thái (7 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_TT_001 | BR-17 | Danh mục HĐ – Lọc Trạng thái | Kiểm tra lọc khi chọn trạng thái "Chờ xử lý" | PC-01 | 1. Click icon lọc<br>2. Click chip "Chờ xử lý"<br>3. Click "Lưu" | Chờ xử lý | - Chỉ hiển thị H02<br>- Nhãn trạng thái trên các card = "Chờ xử lý" | Medium | Positive | EP |  |
| TC_HDDM_TT_002 | BR-17 | Danh mục HĐ – Lọc Trạng thái | Kiểm tra lọc khi chọn trạng thái "Hiệu lực" | PC-01 | 1. Click icon lọc<br>2. Click chip "Hiệu lực"<br>3. Click "Lưu" | Hiệu lực | - Chỉ hiển thị H01, H03, H06<br>- Nhãn trạng thái trên các card = "Hiệu lực" | Medium | Positive | EP |  |
| TC_HDDM_TT_003 | BR-17 | Danh mục HĐ – Lọc Trạng thái | Kiểm tra lọc khi chọn trạng thái "Hoàn thành" | PC-01 | 1. Click icon lọc<br>2. Click chip "Hoàn thành"<br>3. Click "Lưu" | Hoàn thành | - Chỉ hiển thị H04<br>- Nhãn trạng thái trên các card = "Hoàn thành" | Medium | Positive | EP |  |
| TC_HDDM_TT_004 | BR-17 | Danh mục HĐ – Lọc Trạng thái | Kiểm tra lọc khi chọn trạng thái "Hết hiệu lực" | PC-01 | 1. Click icon lọc<br>2. Click chip "Hết hiệu lực"<br>3. Click "Lưu" | Hết hiệu lực | - Chỉ hiển thị H05<br>- Nhãn trạng thái trên các card = "Hết hiệu lực" | Medium | Positive | EP |  |
| TC_HDDM_TT_005 | BR-17 | Danh mục HĐ – Lọc Trạng thái | Kiểm tra lọc khi chọn 2 trạng thái | PC-01 | 1. Click icon lọc<br>2. Click "Hiệu lực" và "Hoàn thành"<br>3. Click "Lưu" | Hiệu lực + Hoàn thành | Hiển thị H01, H03, H04, H06 | Medium | Positive | EP |  |
| TC_HDDM_TT_006 | BR-17 | Danh mục HĐ – Lọc Trạng thái | Kiểm tra click chip "Tất cả" khi đang chọn chip cụ thể | Đang mở Bộ lọc; "Hiệu lực" + "Hoàn thành" active | 1. Click chip "Tất cả" | – | - Chip "Tất cả" active<br>- "Hiệu lực", "Hoàn thành" bỏ active | Low | Positive | EG | [GIẢ ĐỊNH] – Q-13 |
| TC_HDDM_TT_007 | BR-17 | Danh mục HĐ – Lọc Trạng thái | Kiểm tra nhãn và kết quả lọc sau khi trạng thái HĐ đổi trên Portal | PC-01; đang lọc "Hiệu lực" (H01, H03, H06) | 1. Trên Portal đổi H06 từ Hiệu lực sang Hoàn thành<br>2. Mở lại tab "Danh mục hợp đồng" và lọc lại "Hiệu lực" | H06 | - Lọc Hiệu lực: không còn H06<br>- Bỏ lọc: card H06 nhãn "Hoàn thành" | Medium | Positive | ST |  |

### B4.1 App – Card HĐ (13 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_CARD_001 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra Mã TP và chip Thời gian đầu tư trên card | PC-01 | 1. Quan sát card H01<br>2. So với response A1 | H01: symbol BHB291193, term 12 | - Mã TP "BHB291193" màu cam<br>- Chip "12 tháng" ngay bên phải | Medium | Positive | EP |  |
| TC_HDDM_CARD_002 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra nhãn trạng thái trên card | PC-01 | 1. Quan sát nhãn trạng thái H01–H06 | Status theo bảng dữ liệu | - H01, H03, H06: "Hiệu lực"<br>- H02: "Chờ xử lý"<br>- H04: "Hoàn thành"<br>- H05: "Hết hiệu lực" | Medium | Positive | EP | Q-04: có trạng thái "Chờ xác nhận"? |
| TC_HDDM_CARD_003 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra tên TCPH trên card | PC-01 | 1. Quan sát card H01 | issuerName = Công ty Bảo hiểm nhân thọ ABC | Dòng 2 hiển thị "Công ty Bảo hiểm nhân thọ ABC" | Medium | Positive | EP | Q-15: mapping items[] / data.content[] |
| TC_HDDM_CARD_004 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra Mã HĐ trên card | PC-01 | 1. Quan sát card H01 | orderNo = 20261110BHB291193.0000001 | Hiển thị đủ "20261110BHB291193.0000001", không bị cắt | Medium | Positive | EP |  |
| TC_HDDM_CARD_005 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra định dạng Ngày đáo hạn | PC-01 | 1. Quan sát "Ngày đáo hạn" card H01 | matureDate = 2027-11-10 | Hiển thị "10/11/2027" (dd/MM/yyyy) | Medium | Positive | EP |  |
| TC_HDDM_CARD_006 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra định dạng Tổng tiền đầu tư | PC-01 | 1. Quan sát "Tổng tiền đầu tư" card H01 | totalInvestmentAmount = 435339324 | - Hiển thị "435.339.324 VND"<br>- Màu cam | Medium | Positive | EP |  |
| TC_HDDM_CARD_007 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra định dạng Lợi suất | PC-01 | 1. Quan sát "Lợi suất" card H01 | rate = 8.5 | Hiển thị "8,50%/năm" | Medium | Positive | EP |  |
| TC_HDDM_CARD_008 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra định dạng Lợi tức dự kiến | PC-01 | 1. Quan sát "Lợi tức dự kiến" card H01 | interest = 10017942 | - Hiển thị "10.017.942 VND"<br>- Màu xanh lá | Medium | Positive | EP |  |
| TC_HDDM_CARD_009 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra hiển thị số tiền rất lớn | HĐ test có số tiền 13 chữ số [GIẢ ĐỊNH] | 1. Quan sát card | totalInvestmentAmount = 1000000000000 | - Hiển thị "1.000.000.000.000 VND" trên 1 dòng<br>- Không đè cột bên cạnh | Low | Boundary | BVA |  |
| TC_HDDM_CARD_010 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra làm tròn lợi suất có hơn 2 chữ số thập phân | HĐ test rate = 8.555 [GIẢ ĐỊNH] | 1. Quan sát "Lợi suất" | rate = 8.555 | Hiển thị 2 chữ số thập phân, làm tròn theo quy tắc đã chốt (Q-17) | Low | Boundary | BVA | Q-17 |
| TC_HDDM_CARD_011 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra hiển thị tên TCPH dài | HĐ test có tên TCPH ≥ 80 ký tự [GIẢ ĐỊNH] | 1. Quan sát card | Tên TCPH 80 ký tự | - Tên xuống dòng hoặc "..."<br>- Không đè nhãn trạng thái, Mã HĐ | Low | UI | EG |  |
| TC_HDDM_CARD_012 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra màu nhãn trạng thái theo Figma | PC-01 | 1. Quan sát nhãn 4 trạng thái | H01–H06 | - "Hiệu lực": chữ xanh lá, nền xanh đậm<br>- Trạng thái khác: đúng màu Figma | Low | UI | EG | Q-16: Figma chỉ có màu Hiệu lực |
| TC_HDDM_CARD_013 | BR-20 | Danh mục HĐ – Card HĐ | Kiểm tra click vào dòng cảnh báo trên card | PC-01 | 1. Click dòng "Hợp đồng có hồ sơ cần xác nhận" trên card H01 | H01 | Mở Chi tiết HĐ H01, tab "Tổng quan" | Low | Positive | EG |  |

### B4.1 App – Phân trang (4 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_PG_001 | BR-02 | Danh mục HĐ – Phân trang | Kiểm tra tải trang tiếp khi cuộn tới cuối danh sách | KH05 (số HĐ = page size + 1) | 1. Mở tab "Danh mục hợp đồng"<br>2. Cuộn xuống cuối | KH05 | - A1 gọi trang 2<br>- Thêm 1 card vào cuối<br>- Tổng = page size + 1 card | Medium | Positive | BVA | [GIẢ ĐỊNH] – Q-08 |
| TC_HDDM_PG_002 | BR-02 | Danh mục HĐ – Phân trang | Kiểm tra cuộn tiếp ở trang cuối | KH05; đã tải hết trang 2 | 1. Cuộn xuống cuối lần nữa | – | - Không gọi thêm A1<br>- Không lặp card | Low | Boundary | BVA |  |
| TC_HDDM_PG_003 | BR-02, BR-18 | Danh mục HĐ – Phân trang | Kiểm tra trang 2 giữ tham số lọc | KH05; đang lọc Trạng thái = Hiệu lực (kết quả > page size) | 1. Cuộn xuống cuối | – | - Request trang 2 vẫn có statuses = Hiệu lực<br>- Mọi card nhãn "Hiệu lực" | Medium | Positive | EG |  |
| TC_HDDM_PG_004 | BR-02 | Danh mục HĐ – Phân trang | Kiểm tra danh sách khi KH chỉ có 1 HĐ | KH04 (1 HĐ) | 1. Mở tab "Danh mục hợp đồng"<br>2. Cuộn xuống | KH04 | - Hiển thị 1 card<br>- Cuộn không gọi thêm A1 | Low | Boundary | BVA |  |

### B4.1 App – Empty state (3 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_EMPTY_001 | BR-21 | Danh mục HĐ – Empty state | Kiểm tra empty state khi KH chưa có HĐ | KH02 đăng nhập | 1. Mở tab "Danh mục hợp đồng" | KH02 | - Empty state: "Quý khách chưa có hợp đồng"<br>- Không có box, không có card | Medium | Negative | EP | [GIẢ ĐỊNH] chọn câu – Q-12 |
| TC_HDDM_EMPTY_002 | BR-21 | Danh mục HĐ – Empty state | Kiểm tra empty state khi bộ lọc không có kết quả | PC-01 | 1. Click icon lọc<br>2. Chọn TCPH = Công ty CP XYZ, Trạng thái = Chờ xử lý<br>3. Click "Lưu" | XYZ + Chờ xử lý | - Empty state: "Không tìm thấy hợp đồng phù hợp"<br>- Icon lọc vẫn màu cam<br>- Box cần xác nhận vẫn hiển thị | Medium | Negative | DT | Q-02, Q-12 |
| TC_HDDM_EMPTY_003 | BR-21, BR-04 | Danh mục HĐ – Empty state | Kiểm tra empty state khi KH chỉ có HĐ Rejected/hủy | KH07 đăng nhập | 1. Mở tab "Danh mục hợp đồng" | KH07 | - Empty state: "Quý khách chưa có hợp đồng"<br>- Không có card | Medium | Negative | DT | Q-12 |

### B4.1 App – Lỗi (6 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_ERR_001 | BR-22 | Danh mục HĐ – Lỗi | Kiểm tra popup khi A1 trả BND-901 | PC-01; mock A1 trả BND-901 | 1. Mở tab "Danh mục hợp đồng" | BND-901 | - Popup: "Hệ thống đang bận"<br>- Nút "Đóng"<br>- Không hiển thị card | Medium | Negative | EP | [GIẢ ĐỊNH] map code → câu theo thứ tự liệt kê – Q-27 |
| TC_HDDM_ERR_002 | BR-22 | Danh mục HĐ – Lỗi | Kiểm tra popup khi A1 trả BND-999 | PC-01; mock A1 trả BND-999 | 1. Mở tab "Danh mục hợp đồng" | BND-999 | - Popup: "Có lỗi xảy ra. Vui lòng thử lại sau"<br>- Nút "Đóng" | Medium | Negative | EP | Q-27 |
| TC_HDDM_ERR_003 | BR-22 | Danh mục HĐ – Lỗi | Kiểm tra popup khi phiên đăng nhập hết hạn (AUTH_001) | KH01 đăng nhập; token đã hết hạn | 1. Mở tab "Danh mục hợp đồng"<br>2. Click "Đăng nhập" trên popup | AUTH_001 | - Popup: "Phiên đăng nhập không hợp lệ hoặc đã hết hạn"<br>- Nút "Đăng nhập" → màn Đăng nhập<br>- Không hiển thị dữ liệu HĐ | High | Negative | EP |  |
| TC_HDDM_ERR_004 | BR-22 | Danh mục HĐ – Lỗi | Kiểm tra mở màn khi mất mạng | PC-01; tắt Wi-Fi và 4G | 1. Mở tab "Danh mục hợp đồng" | Offline | - Hiện popup lỗi chuẩn SHSmart<br>- Không vỡ layout, không hiện dữ liệu sai<br>- Bật mạng, mở lại tab: tải đủ 6 card | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-25 |
| TC_HDDM_ERR_005 | BR-22, BR-18 | Danh mục HĐ – Lỗi | Kiểm tra Lưu bộ lọc khi mất mạng | PC-01; đang hiển thị 6 card | 1. Click icon lọc, chọn Trạng thái = Hiệu lực<br>2. Tắt mạng<br>3. Click "Lưu" | Offline | - Hiện popup lỗi<br>- Danh sách giữ 6 card cũ | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-25 |
| TC_HDDM_ERR_006 | BR-22, BR-02 | Danh mục HĐ – Lỗi | Kiểm tra lỗi khi tải trang 2 | KH05; mock A1 trang 2 trả BND-999 | 1. Cuộn xuống cuối danh sách | BND-999 | - Popup: "Có lỗi xảy ra. Vui lòng thử lại sau"<br>- Giữ nguyên card trang 1 | Low | Negative | EG |  |

### B4.1 App – Bảo mật (1 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_SEC_001 | BR-26 | Danh mục HĐ – Bảo mật | Kiểm tra không còn dữ liệu cũ khi đổi tài khoản trên cùng thiết bị | KH01 đã xem Danh mục HĐ | 1. Đăng xuất KH01<br>2. Đăng nhập KH02<br>3. Mở tab "Danh mục hợp đồng" | KH01 → KH02 | - Hiển thị empty state của KH02<br>- Không thấy card/box của KH01 (kể cả lúc loading) | High | Security | EG |  |

### B4.1 App – Sự kiện (4 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_EVT_001 | BR-23 | Danh mục HĐ – Sự kiện | Kiểm tra ghi nhận sự kiện SBOND.CONTRACT.LIST | KH01 đăng nhập; bật công cụ xem log tracking | 1. Mở tab "Danh mục hợp đồng" | SBOND.CONTRACT.LIST | - Log có đúng 1 event SBOND.CONTRACT.LIST<br>- Thời điểm log = thời điểm thao tác | Low | Integration | EP |  |
| TC_HDDM_EVT_002 | BR-23 | Danh mục HĐ – Sự kiện | Kiểm tra ghi nhận sự kiện SBOND.CONTRACT.FILTER | KH01; đang có chip BHB291193; bật log tracking | 1. Click X trên chip mã TP BHB291193 | SBOND.CONTRACT.FILTER | - Log có đúng 1 event SBOND.CONTRACT.FILTER<br>- Thời điểm log = thời điểm thao tác | Low | Integration | EP |  |
| TC_HDDM_EVT_003 | BR-23 | Danh mục HĐ – Sự kiện | Kiểm tra ghi nhận sự kiện SBOND.CONTRACT.CONFIRM.CHECK | PC-01; bật log tracking | 1. Click nút "Kiểm tra" | SBOND.CONTRACT.CONFIRM.CHECK | - Log có đúng 1 event SBOND.CONTRACT.CONFIRM.CHECK<br>- Thời điểm log = thời điểm thao tác | Low | Integration | EP |  |
| TC_HDDM_EVT_004 | BR-23 | Danh mục HĐ – Sự kiện | Kiểm tra ghi nhận sự kiện SBOND.CONTRACT.DETAIL | PC-01; bật log tracking | 1. Click card H01 | SBOND.CONTRACT.DETAIL | - Log có đúng 1 event SBOND.CONTRACT.DETAIL<br>- Thời điểm log = thời điểm thao tác | Low | Integration | EP |  |

### B4.2 API – A1 `GET /api/v1/bond/term/app/appendix` (34 TC)

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDDM_API_001 | BR-02, BR-05 | Danh mục HĐ – API A1 | Kiểm tra response A1 khi không truyền tham số lọc | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | Không query param | - HTTP 200<br>- pendingConfirmCount = 3<br>- data.content: 6 phần tử (H01–H06) | High | Positive | EP |  |
| TC_HDDM_API_002 | BR-03 | Danh mục HĐ – API A1 | Kiểm tra thứ tự data.content khi không truyền tham số lọc | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | Không query param | - HTTP 200<br>- Thứ tự: H01 → H02 → H03 → H04 → H05 → H06 (createdAt giảm dần) | High | Positive | EP |  |
| TC_HDDM_API_003 | BR-04 | Danh mục HĐ – API A1 | Kiểm tra A1 không trả HĐ của lệnh Rejected | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | H07 (Rejected) | - HTTP 200<br>- Không có orderNo của H07 | High | Negative | DT |  |
| TC_HDDM_API_004 | BR-04 | Danh mục HĐ – API A1 | Kiểm tra A1 không trả HĐ bị hủy sau xác nhận thanh toán | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | H08 (hủy sau xác nhận TT) | - HTTP 200<br>- Không có orderNo của H08 | High | Negative | DT |  |
| TC_HDDM_API_005 | BR-04, BR-05 | Danh mục HĐ – API A1 | Kiểm tra pendingConfirmCount không tính HĐ Rejected/hủy | Token hợp lệ của KH07 (chỉ có HĐ Rejected + HĐ hủy, từng có hồ sơ cần xác nhận) | 1. Thiết lập Header: Authorization: Bearer <token KH07><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | KH07 | - HTTP 200<br>- pendingConfirmCount = 0<br>- data.content = [] | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-02 |
| TC_HDDM_API_006 | BR-05 | Danh mục HĐ – API A1 | Kiểm tra pendingConfirmCount khi KH không có HĐ cần xác nhận | Token hợp lệ của KH03 | 1. Thiết lập Header: Authorization: Bearer <token KH03><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | KH03 | - HTTP 200<br>- pendingConfirmCount = 0<br>- Mọi phần tử: needConfirm = false | High | Boundary | BVA |  |
| TC_HDDM_API_007 | BR-05 | Danh mục HĐ – API A1 | Kiểm tra pendingConfirmCount khi KH có đúng 1 HĐ cần xác nhận | Token hợp lệ của KH04 | 1. Thiết lập Header: Authorization: Bearer <token KH04><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | KH04 | - HTTP 200<br>- pendingConfirmCount = 1<br>- Đúng 1 phần tử needConfirm = true | Medium | Boundary | BVA |  |
| TC_HDDM_API_008 | BR-06 | Danh mục HĐ – API A1 | Kiểm tra giá trị needConfirm của từng HĐ | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | H01–H06 | - HTTP 200<br>- needConfirm = true: H01, H02, H03<br>- needConfirm = false: H04, H05, H06 | High | Positive | DT |  |
| TC_HDDM_API_009 | BR-05, BR-08 | Danh mục HĐ – API A1 | Kiểm tra pendingConfirmCount khi đang lọc theo mã TP | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?symbol=TPQ12312<br>3. Kiểm tra HTTP status và response body | symbol = TPQ12312 | - HTTP 200<br>- data.content: H03, H04, H05<br>- pendingConfirmCount = 3 (không đổi theo lọc) | Medium | Positive | EG | [GIẢ ĐỊNH] – Q-02 |
| TC_HDDM_API_010 | BR-08, BR-11 | Danh mục HĐ – API A1 | Kiểm tra lọc theo mã TP | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?symbol=BHB291193<br>3. Kiểm tra HTTP status và response body | symbol = BHB291193 | - HTTP 200<br>- data.content: H01, H02, H06 | High | Positive | EP | [GIẢ ĐỊNH] tên tham số – Q-07 |
| TC_HDDM_API_011 | BR-09, BR-26 | Danh mục HĐ – API A1 | Kiểm tra lọc theo mã TP không thuộc danh mục KH | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?symbol=ABC12601<br>3. Kiểm tra HTTP status và response body | symbol = ABC12601 (chỉ KH06 nắm giữ) | - HTTP 200<br>- data.content = []<br>- Không lộ HĐ của KH06 | High | Security | EP | [GIẢ ĐỊNH] mã ABC12601 là dữ liệu test |
| TC_HDDM_API_012 | BR-09 | Danh mục HĐ – API A1 | Kiểm tra lọc theo mã TP không tồn tại | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?symbol=XYZ999<br>3. Kiểm tra HTTP status và response body | symbol = XYZ999 | - HTTP 200<br>- data.content = [] (không lỗi 500) | Medium | Negative | EP | Q-07 |
| TC_HDDM_API_013 | BR-12 | Danh mục HĐ – API A1 | Kiểm tra lọc theo 1 TCPH | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?issuerCodes=ABC<br>3. Kiểm tra HTTP status và response body | issuerCodes = [ABC] | - HTTP 200<br>- data.content: H01, H02, H06 | Medium | Positive | EP | [GIẢ ĐỊNH] mã TCPH & tên tham số – Q-07 |
| TC_HDDM_API_014 | BR-12 | Danh mục HĐ – API A1 | Kiểm tra lọc theo nhiều TCPH | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?issuerCodes=ABC&issuerCodes=XYZ<br>3. Kiểm tra HTTP status và response body | issuerCodes = [ABC, XYZ] | - HTTP 200<br>- data.content: H01–H06 | Medium | Positive | EP | Q-07 |
| TC_HDDM_API_015 | BR-13 | Danh mục HĐ – API A1 | Kiểm tra lọc theo kỳ hạn | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?tenors=12<br>3. Kiểm tra HTTP status và response body | tenors = [12] | - HTTP 200<br>- data.content: H01, H02, H05 | Medium | Positive | EP | Q-07, Q-21 |
| TC_HDDM_API_016 | BR-14 | Danh mục HĐ – API A1 | Kiểm tra lọc lợi suất với HĐ nằm đúng biên | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?yieldFrom=8&yieldTo=9<br>3. Kiểm tra HTTP status và response body | 8 – 9 | - HTTP 200<br>- Có: H01, H02 (8,00), H03 (9,00), H06<br>- Không có: H04 (7,99), H05 (9,01) | High | Boundary | BVA | [GIẢ ĐỊNH] biên inclusive – Q-10 |
| TC_HDDM_API_017 | BR-15 | Danh mục HĐ – API A1 | Kiểm tra lọc lợi suất khi yieldFrom = yieldTo | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?yieldFrom=8.5&yieldTo=8.5<br>3. Kiểm tra HTTP status và response body | 8,5 – 8,5 | - HTTP 200<br>- data.content: H01, H06 | Medium | Boundary | BVA |  |
| TC_HDDM_API_018 | BR-15 | Danh mục HĐ – API A1 | Kiểm tra A1 khi yieldFrom > yieldTo | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?yieldFrom=9&yieldTo=8<br>3. Kiểm tra HTTP status và response body | 9 – 8 | - Error code BND-100<br>- Không trả data.content | Medium | Negative | BVA | [GIẢ ĐỊNH] HTTP status – Q-07, Q-11 |
| TC_HDDM_API_019 | BR-15 | Danh mục HĐ – API A1 | Kiểm tra A1 khi yieldFrom nhỏ hơn min | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?yieldFrom=-1&yieldTo=9<br>3. Kiểm tra HTTP status và response body | yieldFrom = -1 | - Error code BND-100<br>- Không trả data.content | Medium | Negative | BVA | Q-10, Q-11 |
| TC_HDDM_API_020 | BR-15 | Danh mục HĐ – API A1 | Kiểm tra A1 khi yieldTo lớn hơn max | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?yieldFrom=1&yieldTo=101<br>3. Kiểm tra HTTP status và response body | yieldTo = 101 | - Error code BND-100<br>- Không trả data.content | Medium | Negative | BVA | Q-10, Q-11 |
| TC_HDDM_API_021 | BR-16 | Danh mục HĐ – API A1 | Kiểm tra lọc theo loại TP | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?bondTypes=NIEM_YET<br>3. Kiểm tra HTTP status và response body | bondTypes = [NIEM_YET] | - HTTP 200<br>- data.content: H03, H04, H05 | Medium | Positive | EP | [GIẢ ĐỊNH] enum – Q-07 |
| TC_HDDM_API_022 | BR-17 | Danh mục HĐ – API A1 | Kiểm tra lọc theo 1 trạng thái | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?statuses=HIEU_LUC<br>3. Kiểm tra HTTP status và response body | statuses = [HIEU_LUC] | - HTTP 200<br>- data.content: H01, H03, H06 | Medium | Positive | EP | [GIẢ ĐỊNH] enum – Q-07, Q-16 |
| TC_HDDM_API_023 | BR-17 | Danh mục HĐ – API A1 | Kiểm tra lọc theo nhiều trạng thái | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?statuses=HIEU_LUC&statuses=HOAN_THANH<br>3. Kiểm tra HTTP status và response body | statuses = [HIEU_LUC, HOAN_THANH] | - HTTP 200<br>- data.content: H01, H03, H04, H06 | Medium | Positive | EP | Q-07 |
| TC_HDDM_API_024 | BR-17 | Danh mục HĐ – API A1 | Kiểm tra A1 khi statuses có giá trị không hợp lệ | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?statuses=ABC<br>3. Kiểm tra HTTP status và response body | statuses = ABC | - Lỗi validation (BND-100 [GIẢ ĐỊNH])<br>- Không trả data.content | Low | Negative | EP | Q-07 |
| TC_HDDM_API_025 | BR-18 | Danh mục HĐ – API A1 | Kiểm tra kết hợp nhiều tham số lọc (AND) | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?issuerCodes=ABC&tenors=12&statuses=HIEU_LUC<br>3. Kiểm tra HTTP status và response body | ABC + 12 + HIEU_LUC | - HTTP 200<br>- data.content: chỉ H01 | High | Positive | DT | [GIẢ ĐỊNH] – Q-26 |
| TC_HDDM_API_026 | BR-18, BR-21 | Danh mục HĐ – API A1 | Kiểm tra kết hợp tham số lọc không có kết quả | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix?issuerCodes=XYZ&statuses=CHO_XU_LY<br>3. Kiểm tra HTTP status và response body | XYZ + CHO_XU_LY | - HTTP 200<br>- data.content = []<br>- pendingConfirmCount = 3 | Medium | Negative | DT | Q-02 |
| TC_HDDM_API_027 | BR-02, BR-03 | Danh mục HĐ – API A1 | Kiểm tra phân trang không trùng và giữ thứ tự giữa 2 trang | Token hợp lệ của KH05 (số HĐ = page size + 1) | 1. Thiết lập Header: Authorization: Bearer <token KH05><br>2. Gửi request GET /api/v1/bond/term/app/appendix?page=0<br>3. Gửi lại request với page=1<br>4. Kiểm tra HTTP status và response body | page 0, page 1 | - Trang 0: đủ page size phần tử<br>- Trang 1: 1 phần tử<br>- Không trùng orderNo<br>- createdAt cuối trang 0 ≥ đầu trang 1 | Medium | Positive | BVA | [GIẢ ĐỊNH] tên tham số phân trang – Q-07, Q-08 |
| TC_HDDM_API_028 | BR-02 | Danh mục HĐ – API A1 | Kiểm tra gọi trang vượt quá trang cuối | Token hợp lệ của KH05 | 1. Thiết lập Header: Authorization: Bearer <token KH05><br>2. Gửi request GET /api/v1/bond/term/app/appendix?page=99<br>3. Kiểm tra HTTP status và response body | page = 99 | - HTTP 200<br>- data.content = [] | Low | Boundary | BVA | Q-08 |
| TC_HDDM_API_029 | BR-02, BR-18 | Danh mục HĐ – API A1 | Kiểm tra trang 2 khi có tham số lọc | Token hợp lệ của KH05; số HĐ Hiệu lực > page size | 1. Thiết lập Header: Authorization: Bearer <token KH05><br>2. Gửi request GET /api/v1/bond/term/app/appendix?statuses=HIEU_LUC&page=1<br>3. Kiểm tra HTTP status và response body | HIEU_LUC, page 1 | - HTTP 200<br>- Mọi phần tử: status = HIEU_LUC | Medium | Positive | EG | Q-08 |
| TC_HDDM_API_030 | BR-19 | Danh mục HĐ – API A1 | Kiểm tra cấu trúc và kiểu dữ liệu của phần tử data.content | Token hợp lệ của KH01; dữ liệu H01–H08 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | H01 | - symbol = BHB291193; term = 12<br>- status = Hiệu lực<br>- issuerName = Công ty Bảo hiểm nhân thọ ABC<br>- orderNo = 20261110BHB291193.0000001<br>- matureDate = 2027-11-10<br>- totalInvestmentAmount = 435339324 (number)<br>- rate = 8.5 (number)<br>- interest = 10017942 (number)<br>- needConfirm = true (boolean) | High | Positive | EP | Q-07, Q-15: mapping items[]/data.content[] |
| TC_HDDM_API_031 | BR-26, BR-22 | Danh mục HĐ – API A1 | Kiểm tra A1 khi không gửi token | Không có token | 1. Không gửi header Authorization<br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | – | - AUTH_001 (HTTP 401 [GIẢ ĐỊNH])<br>- Không trả data.content | High | Security | EG | Q-07 |
| TC_HDDM_API_032 | BR-22 | Danh mục HĐ – API A1 | Kiểm tra A1 khi token hết hạn | Token của KH01 đã hết hạn | 1. Thiết lập Header: Authorization: Bearer <token hết hạn><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | Token hết hạn | - AUTH_001 (HTTP 401 [GIẢ ĐỊNH])<br>- Không trả data.content | High | Security | EG | Q-07 |
| TC_HDDM_API_033 | BR-26 | Danh mục HĐ – API A1 | Kiểm tra A1 chỉ trả HĐ của KH sở hữu token | Token KH01; KH06 có HĐ mã ABC12601 | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | KH01, KH06 | - HTTP 200<br>- Không có orderNo nào của KH06 | High | Security | EG |  |
| TC_HDDM_API_034 | BR-25 | Danh mục HĐ – API A1 | Kiểm tra gọi A1 không làm thay đổi dữ liệu | Token KH01; đã chụp dữ liệu HĐ của KH01 trong DB trước khi test | 1. Thiết lập Header: Authorization: Bearer <token KH01><br>2. Gửi request GET /api/v1/bond/term/app/appendix<br>3. Kiểm tra HTTP status và response body | Gọi 2 lần liên tiếp | - 2 response giống nhau<br>- DB: status, needConfirm, số tiền của HĐ không đổi | Low | Positive | EG |  |

---

## B5 – TỰ REVIEW & HOÀN THIỆN

### 1. RTM

| BR ID | Mô tả ngắn | TC App | TC API | Tổng | TC IDs | Phủ |
|---|---|---|---|---|---|---|
| BR-01 | Vào màn từ 3 điểm: tab "Danh mục hợp đồng"; nút "Xem danh mục hợp đ... | 5 | 0 | 5 | UI_001, NAV_001, NAV_002, NAV_003, NAV_004 | Đủ |
| BR-02 | App gọi A1 `GET /api/v1/bond/term/app/appendix` khi mở màn, khi lọc... | 7 | 4 | 11 | NAV_001, SORT_004, PG_001, PG_002, PG_003, PG_004, ERR_006, API_001, API_027, API_028, API_029 | Đủ |
| BR-03 | Danh sách sắp xếp theo Ngày tạo HĐ từ mới đến cũ (createdAt giảm dần) | 5 | 2 | 7 | SORT_001, SORT_002, SORT_003, SORT_004, SORT_005, API_002, API_027 | Đủ |
| BR-04 | Không hiển thị HĐ của lệnh không khớp (Rejected) và HĐ bị hủy sau x... | 6 | 3 | 9 | AN_001, AN_002, AN_003, AN_004, AN_006, EMPTY_003, API_003, API_004, API_005 | Đủ |
| BR-05 | Box "Hợp đồng cần xác nhận" chỉ hiển thị khi pendingConfirmCount ≥ ... | 8 | 5 | 13 | BOX_001, BOX_002, BOX_003, BOX_006, BOX_007, BOX_008, BOX_009, AN_006, API_001, API_005, API_006, API_007, API_009 | Đủ |
| BR-06 | Card có needConfirm = true hiển thị dòng cảnh báo "Hợp đồng có hồ s... | 4 | 1 | 5 | BOX_004, BOX_005, BOX_006, BOX_008, API_008 | Đủ |
| BR-07 | Nút "Kiểm tra" chuyển sang tab "Xác nhận" của S-Bond | 1 | 0 | 1 | NAV_007 | Đủ |
| BR-08 | Chip mã TP hiển thị khi vào từ Chi tiết TP, chỉ hiện HĐ của mã đó; ... | 7 | 2 | 9 | NAV_002, BOX_007, CHIP_001, CHIP_002, CHIP_003, CHIP_005, CHIP_006, API_009, API_010 | Đủ |
| BR-09 | V01: bondCode phải thuộc danh mục KH; không có HĐ thì hiển thị empt... | 1 | 2 | 3 | CHIP_004, API_011, API_012 | Đủ |
| BR-10 | Icon lọc mở bottom sheet "Bộ lọc" và đổi màu khi đang lọc | 3 | 0 | 3 | UI_003, CHIP_003, LOC_001 | Đủ |
| BR-11 | Lọc Mã TP: màn tìm riêng; tìm theo cụm từ trong mã/tên TP, không ph... | 13 | 1 | 14 | CHIP_005, CHIP_006, MATP_001, MATP_002, MATP_003, MATP_004, MATP_005, MATP_006, MATP_007, MATP_008, MATP_009, MATP_010, MATP_011, API_010 | Đủ |
| BR-12 | TCPH: dropdown chọn nhiều, mặc định "Tất cả tổ chức" | 6 | 2 | 8 | UI_003, LOC_007, TCPH_001, TCPH_002, TCPH_003, TCPH_004, API_013, API_014 | Đủ |
| BR-13 | Kỳ hạn: dropdown chọn nhiều, mặc định "Tất cả kỳ hạn" | 5 | 1 | 6 | UI_003, LOC_007, KH_001, KH_002, KH_003, API_015 | Đủ |
| BR-14 | Lợi suất: thanh trượt 2 đầu, mặc định toàn khoảng min → max; lọc HĐ... | 6 | 1 | 7 | UI_003, LS_001, LS_002, LS_003, LS_004, LS_008, API_016 | Đủ |
| BR-15 | V02: lợi suất "từ" ≤ "đến" và nằm trong min–max; UI chặn kéo vượt; ... | 3 | 4 | 7 | LS_005, LS_006, LS_007, API_017, API_018, API_019, API_020 | Đủ |
| BR-16 | Loại TP: chip chọn nhiều Tất cả / Niêm yết / Riêng lẻ, mặc định "Tấ... | 5 | 1 | 6 | UI_003, LTP_001, LTP_002, LTP_003, LTP_004, API_021 | Đủ |
| BR-17 | Trạng thái HĐ: chip chọn nhiều Tất cả / Chờ xử lý / Hiệu lực / Hoàn... | 9 | 3 | 12 | UI_003, LOC_007, TT_001, TT_002, TT_003, TT_004, TT_005, TT_006, TT_007, API_022, API_023, API_024 | Đủ |
| BR-18 | "Đặt lại" đưa bộ lọc về mặc định; "Lưu" đóng bottom sheet và gọi lạ... | 11 | 3 | 14 | UI_003, NAV_006, LOC_002, LOC_003, LOC_004, LOC_005, LOC_006, LOC_007, LOC_008, PG_003, ERR_005, API_025, API_026, API_029 | Đủ |
| BR-19 | Card HĐ hiển thị 9 field với định dạng: Mã TP (cam), Thời gian đầu ... | 13 | 1 | 14 | UI_002, CARD_001, CARD_002, CARD_003, CARD_004, CARD_005, CARD_006, CARD_007, CARD_008, CARD_009, CARD_010, CARD_011, CARD_012, API_030 | Đủ |
| BR-20 | Bấm card chuyển sang Chi tiết HĐ, tab "Tổng quan" (CONTRACT.APP.02) | 3 | 0 | 3 | NAV_005, NAV_006, CARD_013 | Đủ |
| BR-21 | Danh sách rỗng hiển thị empty state SBOND.CONTRACT.I01 "Quý khách c... | 4 | 1 | 5 | CHIP_004, EMPTY_001, EMPTY_002, EMPTY_003, API_026 | Đủ |
| BR-22 | Lỗi BND-901 / BND-999 / AUTH_001 hiển thị popup lỗi chuẩn SHSmart (... | 6 | 2 | 8 | ERR_001, ERR_002, ERR_003, ERR_004, ERR_005, ERR_006, API_031, API_032 | Đủ |
| BR-23 | Ghi event: SBOND.CONTRACT.LIST, .FILTER, .CONFIRM.CHECK, .DETAIL | 4 | 0 | 4 | EVT_001, EVT_002, EVT_003, EVT_004 | Đủ |
| BR-24 | Khi có nhắc T-5 hoàn thiện số lượng TP (Bond_08), HĐ vẫn hiển thị b... | 1 | 0 | 1 | AN_005 | Đủ |
| BR-25 | Màn không làm thay đổi dữ liệu | 0 | 1 | 1 | API_034 | Đủ |
| BR-26 | KH chỉ thấy HĐ của chính mình (theo token) | 1 | 3 | 4 | SEC_001, API_011, API_031, API_033 | Đủ |

**Kết quả RTM:** 26/26 BR có test case (phủ 100%). Mỗi TC gắn ít nhất 1 BR. BR-25 chỉ kiểm được ở tầng API; BR-07, BR-10, BR-20, BR-23, BR-24 chỉ có ở App.

### 2. Checklist review (file 05)
| Nhóm | Mục | Kết quả | Ghi chú |
|---|---|---|---|
| A. Độ phủ | 100% BR có TC | Đạt | RTM ở trên |
| | Mỗi field có rỗng / hợp lệ / không hợp lệ / biên | Đạt | Riêng TCPH, Kỳ hạn không có giá trị "không hợp lệ" trên UI; đã phủ phía API (API_024) |
| | Luồng chính + phụ + ngoại lệ | Đạt | NAV, CHIP, ERR, EMPTY |
| | Phân quyền theo role | Đạt | Chỉ có 1 role KH → kiểm cách ly dữ liệu: App SEC_001; API_011, API_031, API_032, API_033 |
| | DT / ST đủ rule và ô không hợp lệ | Đạt | Mỗi cột DT có TC; ô ✖ (X khi không có chip) không thao tác được trên UI |
| | Error guessing | Đạt | Double-click, offline, IDOR, input độc hại, dữ liệu dài/lớn |
| B. Chất lượng TC | Đủ 12 cột, ID không trùng | Đạt | Đã kiểm bằng script |
| | 1 TC = 1 mục tiêu | Đạt | Các case biên lợi suất đã tách 4 TC (LS_001–004) |
| | Title "Kiểm tra … khi …" | Đạt | |
| | Test data cụ thể | Đạt có điều kiện | Một số giá trị enum/API, tên TP mẫu đang là [GIẢ ĐỊNH] |
| | Expected đo được | Đạt | Có số card / mã HĐ cụ thể; case API ghi field response |
| | Negative kiểm "báo lỗi" + "không thực hiện" | Đạt | VD: ERR_003 popup + không hiển thị dữ liệu; API_018 BND-100 + không trả data.content |
| | Message đúng nguyên văn hoặc [GIẢ ĐỊNH] | Đạt | Message box / empty / popup gắn Q-01, Q-12, Q-27 |
| C. Nhất quán | Không trùng | Đạt | |
| | Priority hợp lý | Đạt | Bảo mật, ẩn HĐ, box xác nhận, điều hướng chính = High |
| | Thuật ngữ khớp SRS | Đạt | |
| | Thứ tự UI → Positive → Validation → Boundary → Negative → Permission | Đạt | |

**Đã bổ sung/sửa trong bước tự review:**
- Thêm App AN_006 + API_005 (box / pendingConfirmCount không tính HĐ Rejected/hủy) – trước đó BR-04 × BR-05 chưa có case giao.
- Thêm CARD_013 (bấm vào dòng cảnh báo cũng mở Chi tiết HĐ) – tránh bug vùng bấm.
- Thêm PG_003 (trang 2 giữ tham số lọc) – lỗi phổ biến khi phân trang kết hợp lọc.
- Tách case biên lợi suất 8,00–9,00 thành 4 TC độc lập (từ 1 case gộp ban đầu).

### 3. Thống kê
| Chỉ số | Giá trị | Mục tiêu | Đánh giá |
|---|---|---|---|
| Tổng TC | 146 (App 112 · API 34) | – | |
| High / Medium / Low | 35 / 81 / 30 (24% / 55% / 21%) | 25 / 50 / 25 | Đạt |
| Positive : Không Positive | 72 : 74 (49% : 51%) | 30 : 70 | **Lệch** – màn chỉ đọc, phần lớn rule là hiển thị/lọc nên Positive cao; phần Negative tập trung vào ẩn HĐ, lỗi, bảo mật |
| Theo Type | Positive 72 · Negative 29 · Boundary 25 · UI 9 · Security 7 · Integration 4 | – | |
| TC có [GIẢ ĐỊNH] | 48 | – | Cập nhật lại sau khi đóng Q&A |
| Q&A mở | 25 (Blocker 0 · Major 15 · Minor 10) | Blocker = 0 | Đạt điều kiện bắt đầu test |

### 4. Việc cần làm trước khi chạy test
1. BA/PO trả lời 15 Q&A mức Major, ưu tiên Q-01, Q-02, Q-07, Q-08, Q-12, Q-26 vì chúng ảnh hưởng nhiều TC nhất.
2. Dựng dữ liệu KH01–KH07 trên UAT đúng bảng B2.
3. Xin API doc sheet API19_DanhMucHD bản cập nhật (tham số filter, enum, phân trang, HTTP status) – 34 TC API đang dựa trên tên tham số [GIẢ ĐỊNH].
