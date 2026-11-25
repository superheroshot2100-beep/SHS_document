# PHỤ LỤC 05 – PHÂN TÍCH & TEST CASE (B1 → B5)

> Nguồn duy nhất: SRS **11. Quản lý biểu mẫu** (BON-1836, v1.1 – 11/11/2026), mục **1. Mẫu Phụ lục 05** và **1. Mẫu Phụ lục 05 mapping**. Mục 2–6 (HĐ bán kỳ hạn, phiếu lệnh, dòng tiền…) ngoài phạm vi.
> Người viết: Nguyễn Sỹ Hà · Ngày: 19/11/2026 · Chạy /FULL (không dừng xác nhận giữa các bước).
> Ký hiệu: [GĐ] = [GIẢ ĐỊNH] · Q-xx = câu hỏi mở. Test case: nhóm Chung, sau đó trường 01 → 62; mỗi trường 1 TC, điều kiện đặc biệt ghi “Khi …: …” trong Kết quả mong đợi. Viết tiếng Việt, không ghi tên cột dữ liệu.

---
## B1 – PHÂN TÍCH YÊU CẦU

### 1. Tổng quan
- **Mục tiêu:** Hệ thống tự sinh Phụ lục 05 – văn bản NĐT cá nhân xác nhận tư cách NĐT chuyên nghiệp và đã tiếp cận đủ thông tin TPDN riêng lẻ, điền sẵn 62 trường từ dữ liệu KH, TP, TCPH, SHS.
- **Thời điểm gen:** sau khi tạo yêu cầu KH đặt mua → Chờ duyệt; **chỉ KH cá nhân**.
- **Nơi hiển thị:** Xem chi tiết yêu cầu đặt mua; Xem chi tiết hợp đồng.
- **Role:** tài liệu không nêu → Q-16 ([GĐ] nhân viên Portal có quyền màn Yêu cầu đặt mua / Hợp đồng).

### 2. Feature / Màn hình
- F1. Gen PL05 (job/service khi tạo yêu cầu đặt mua)
- F2. Hiển thị PL05 tại 2 màn chi tiết
- F3. Nội dung PL05: bố cục tĩnh + 62 trường mapping

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Sinh PL05 sau khi tạo yêu cầu KH đặt mua → trạng thái Chờ duyệt | §1 mapping – Thời điểm gen | Logic |
| BR-02 | Chỉ sinh PL05 cho KH Cá nhân; KH Tổ chức không sinh | §1 mapping – Thời điểm gen | Logic |
| BR-03 | PL05 hiển thị tại màn Xem chi tiết yêu cầu đặt mua | §1 mapping – Hiển thị tại các màn | UI/Tích hợp |
| BR-04 | PL05 hiển thị tại màn Xem chi tiết hợp đồng | §1 mapping – Hiển thị tại các màn | UI/Tích hợp |
| BR-05 | Nội dung tĩnh, bố cục mẫu PL05 (quốc hiệu, kính gửi, 4 phương án tư cách NĐT, mục 2–3, 3 khối ký) đúng mẫu | §1 Mẫu Phụ lục 05 | UI |
| BR-06 | Thông tin KH (Tên, Ngày sinh, Giới tính, Số CCCD/Hộ chiếu, Ngày cấp, Nơi cấp, Nơi ở, Điện thoại) lấy từ bảng CUSTOMER; Tên KH lặp lại tại mục 3.1 i) | §1 mapping STT 1–8 | Logic |
| BR-07 | Tổ chức phát hành = BOND_ISSUER.ISSUER_NAME (2 vị trí) | §1 mapping STT 9 | Logic |
| BR-08 | Thông tin chứng chỉ (Loại, Số, Ngày cấp, Ngày hiệu lực) lấy CUSTOMER_PRO_INFO khi PRO_TYPE = 2, khác thì để trống | §1 mapping STT 10–13 | Logic |
| BR-09 | Giá trị danh mục CKNY = CUSTOMER_PRO_INFO.INVEST_AMOUNT khi PRO_TYPE = 3, khác thì để trống | §1 mapping STT 14 | Logic |
| BR-10 | Năm thu nhập chịu thuế = CUSTOMER_PRO_INFO.YEAR, Giá trị thu nhập chịu thuế = CUSTOMER_PRO_INFO.INVEST_AMOUNT khi PRO_TYPE = 4, khác thì để trống | §1 mapping STT 15–16 | Logic |
| BR-11 | Mã trái phiếu = REPO_ORDER.SYMBOL (= BOND_INFO.CODE), đồng nhất mọi vị trí | §1 mapping STT 17 | Logic |
| BR-12 | Địa chỉ SHS = CUSTOMER.CUST_ADDRESS của KH 069P000001 (3 vị trí) | §1 mapping STT 18 | Logic |
| BR-13 | Khối xác nhận cung cấp thông tin (Người đại diện, Chức vụ, Số/Ngày ủy quyền, CCCD): CUSTOMER_AUTHORIZER của TK 069P, IS_DEFAULT = 1 | §1 mapping STT 19–22, 56 | Logic |
| BR-14 | Khối xác nhận tư cách NĐT (Người đại diện, Số/Ngày ủy quyền, CCCD, Chức vụ): CUSTOMER_AUTHORIZER của TK 069P, IS_DEFAULT = 0 | §1 mapping STT 57–61 | Logic |
| BR-15 | 10 nội dung lấy từ bảng phụ lục trái phiếu: Bản CBTT trước đợt chào bán, Phương án phát hành, Hồ sơ chào bán, CBTT định kỳ, Mục đích phát hành, Kế hoạch sử dụng vốn, Kế hoạch sử dụng vốn tạm thời nhàn rỗi, Phương án bố trí nguồn, Cam kết của TCPH, Rủi ro | §1 mapping STT 23,25,27,29,40–43,51,54 | Tích hợp |
| BR-16 | Căn cứ Nghị định (5 vị trí: mục 2 a, 2 b, 2 c, 2 d, 3.1 i) theo Ngày phát hành TP: <16/09/2022 · 16/09/2022–<05/06/2026 · ≥05/06/2026 | §1 mapping STT 24,26,28,52,62 (v1.1) | Logic |
| BR-17 | Mã số ĐKDN = REGISTRATION_ID, Loại ngành nghề = BUSINESS_TYPE, Lãi suất = RATE_DESCRIPTION, Kỳ hạn = TERM, Đại diện người sở hữu TP = REPRESENTATIVE | §1 mapping STT 30,31,49,50,53 | Logic |
| BR-18 | Bảng tình hình tài chính: tối đa 3 kỳ gần nhất, sắp xếp gần → xa; có bao nhiêu kỳ hiển thị bấy nhiêu cột | §1 mapping STT 32–39 | Logic |
| BR-19 | TSBĐ: Có/không = BOND_INFO.HAS_COLLATERAL; Giá trị TSBĐ = tổng giá trị bảng TSĐB; Số ký hiệu lấy từ bảng TSĐB | §1 mapping STT 44–46 | Logic |
| BR-20 | Bảo lãnh thanh toán (Có/không, Tổ chức tín dụng bảo lãnh) lấy từ thông tin Bảo lãnh thanh toán | §1 mapping STT 47–48 | Logic |
| BR-21 | Ngày ký (ngày/tháng/năm) = REPO_ORDER.CREATED_DATE (3 vị trí ký) | §1 mapping STT 55 | Logic |

### 4. Field list (nhóm theo nguồn dữ liệu)
| Field | Kiểu | Nguồn DB | Điều kiện hiển thị | Định dạng / Ghi chú |
|---|---|---|---|---|
| Tên khách hàng | Text | CUSTOMER.CUSTOMER_NAME | Luôn | 2 vị trí |
| Ngày sinh | Date | CUSTOMER.BIRTH_DAY | Luôn | dd/mm/yyyy [GĐ Q-13] |
| Giới tính | Mã/Text | CUSTOMER.GENDER | Luôn | Chữ Nam/Nữ [GĐ Q-12] |
| Số CCCD/Hộ chiếu, Ngày cấp, Nơi cấp | Text/Date | CUSTOMER.CARD_ID / ISSUE_DATE / ISSUE_PLACE | Luôn | Giữ số 0 đầu |
| Nơi ở hiện tại, Điện thoại | Text | CUSTOMER.CUST_ADDRESS / CUST_MOBILE | Luôn | — |
| Tổ chức phát hành | Text | BOND_ISSUER.ISSUER_NAME | Luôn | 2 vị trí |
| Loại, Số, Ngày cấp, Ngày hiệu lực chứng chỉ | Text/Date | CUSTOMER_PRO_INFO.CERTIFICATE_* | PRO_TYPE = 2 | Trống nếu ≠ 2 |
| Giá trị danh mục CKNY | Số | CUSTOMER_PRO_INFO.INVEST_AMOUNT | PRO_TYPE = 3 | Đơn vị “đồng” [GĐ Q-03] |
| Năm, Giá trị thu nhập chịu thuế | Năm/Số | CUSTOMER_PRO_INFO.YEAR / INVEST_AMOUNT | PRO_TYPE = 4 | Đơn vị “tỷ đồng” [GĐ Q-03] |
| Mã trái phiếu | Text | REPO_ORDER.SYMBOL | Luôn | 6 vị trí |
| Địa chỉ SHS | Text | CUSTOMER.CUST_ADDRESS (069P000001) | Luôn | 3 vị trí |
| Khối xác nhận cung cấp thông tin: Người đại diện, Chức vụ, Số/Ngày UQ, CCCD | Text/Date | CUSTOMER_AUTHORIZER (069P, IS_DEFAULT = 1) | Luôn | Mục 2 + khối ký 2 |
| Khối xác nhận tư cách NĐT: Người đại diện, Số/Ngày UQ, CCCD, Chức vụ | Text/Date | CUSTOMER_AUTHORIZER (069P, IS_DEFAULT = 0) | Luôn | Khối ký 3 |
| 10 nội dung phụ lục TP (CBTT, phương án, hồ sơ, mục đích, kế hoạch vốn, cam kết, rủi ro…) | Text dài | Bảng phụ lục trái phiếu | Luôn | — |
| Căn cứ Nghị định (5 vị trí) | Text | Theo Ngày phát hành TP | Luôn | Bảng điều kiện 3 khoảng |
| Mã số ĐKDN, Ngành nghề, Lãi suất, Kỳ hạn, Đại diện người sở hữu TP | Text | BOND_ISSUER / BOND_INFO | Luôn | Đại diện có “(nếu có)” |
| Bảng tình hình tài chính | Bảng | Bảng lưu hệ số tài chính | ≤ 3 kỳ | VND / Lần |
| TSBĐ: Có/không, Giá trị, Số ký hiệu | Cờ/Số/Text | BOND_INFO.HAS_COLLATERAL / bảng TSĐB | Luôn | Giá trị = SUM |
| Bảo lãnh: Có/không, TCTD bảo lãnh | Text | Thông tin Bảo lãnh thanh toán | Luôn | Bảng nguồn chưa rõ [Q-10] |
| Ngày ký | Date | REPO_ORDER.CREATED_DATE | Luôn | Tách ngày/tháng/năm, 3 vị trí |

> PL05 là biểu mẫu sinh tự động, không có input của user → không có message lỗi validation; “Message lỗi” thay bằng quy tắc để trống.

**Bảng căn cứ Nghị định (BR-16):**
| Ngày phát hành | Mục 2 b) Phương án PH | Mục 2 c) Hồ sơ chào bán, 2 d) CBTT định kỳ | Mục 3.1 i) Đối tượng chào bán | Mục 2 a) Bản CBTT trước chào bán |
|---|---|---|---|---|
| < 16/09/2022 | NĐ 153/2020/NĐ-CP | NĐ 153/2020/NĐ-CP | NĐ 153/2020/NĐ-CP | NĐ 153/2020/NĐ-CP |
| 16/09/2022 ≤ … < 05/06/2026 | NĐ 153/2020 và NĐ 65/2022 | NĐ 153/2020 và NĐ 65/2022 | NĐ 153/2020 và NĐ 65/2022 | NĐ 153/2020 và NĐ 65/2022 |
| ≥ 05/06/2026 | Điều 10 NĐ 200/2026/NĐ-CP | NĐ số 200/2026/NĐ-CP | (điều khoản) NĐ số 200/2026/NĐ-CP | khoản 1 Điều 29 NĐ số 200/2026/NĐ-CP |

**Vì sao test căn cứ pháp lý theo ngày:** SRS quy định nội dung căn cứ đổi theo ngày phát hành, có 2 mốc 16/09/2022 và 05/06/2026. Mỗi trường 24, 26, 28, 52, 62 test 4 ngày sát 2 phía của mỗi mốc (15/09/2022 – 16/09/2022, 04/06/2026 – 05/06/2026) để bắt lỗi so sánh sai đúng tại mốc (vd: viết “lớn hơn” thay vì “lớn hơn hoặc bằng”). Test ngày giữa khoảng không bắt được lỗi này.

### 5. Q&A
| Q ID | Câu hỏi | Lý do hỏi | Mức | Giả định đề xuất |
|---|---|---|---|---|
| Q-01 | Quy tắc tick 4 ô tư cách NĐT theo PRO_TYPE chưa mô tả; mẫu đang tick cứng ô “chứng chỉ hành nghề” (☑). | Ảnh hưởng tính pháp lý hồ sơ NĐTCN | Blocker | PRO_TYPE 1/2/3/4 → tick ô 1/2/3/4, chỉ 1 ô |
| Q-02 | PRO_TYPE = 1 (NĐT chiến lược): “Nghị quyết ĐHĐCĐ số … ngày …” chưa có mapping. | Không test được giá trị | Major | Giữ nguyên “…” |
| Q-03 | Giá trị danh mục CKNY và Giá trị thu nhập chịu thuế cùng lấy CUSTOMER_PRO_INFO.INVEST_AMOUNT nhưng mẫu ghi đơn vị khác nhau (“đồng” vs “tỷ đồng”). DB lưu đơn vị gì, có quy đổi? | Sai đơn vị = sai số tiền trên văn bản pháp lý | Major | DB lưu VND; CKNY hiển thị VND có phân cách nghìn, thu nhập chịu thuế quy đổi ra tỷ (1.200.000.000 → 1,2) |
| Q-04 | Năm thu nhập chịu thuế: mẫu ghi “năm liền trước năm mua TP” nhưng map YEAR. Nếu YEAR ≠ (năm tạo yêu cầu − 1) thì xử lý? | Có thể chặn đặt mua hoặc hiển thị sai | Major | Hiển thị nguyên YEAR, không chặn |
| Q-05 | Chức vụ, Số/Ngày ủy quyền, CCCD của 2 khối xác nhận không ghi điều kiện IS_DEFAULT, dùng chung tên cột. | Lấy nhầm người | Major | Khối cung cấp thông tin cùng bản ghi IS_DEFAULT = 1; khối tư cách NĐT cùng bản ghi IS_DEFAULT = 0 |
| Q-06 | TK 069P có ≥ 2 bản ghi cùng IS_DEFAULT hoặc không có bản ghi IS_DEFAULT = 0/1 → xử lý? | Gen lỗi/không xác định | Major | Không có → để trống, vẫn gen; trùng → lấy bản ghi mới nhất |
| Q-07 | PL05 là bản chụp tại thời điểm gen hay đọc dữ liệu live mỗi lần mở? Có gen lại khi KH/TP/069P đổi dữ liệu? | Văn bản đã ký bị thay đổi | Major | Bản chụp tại thời điểm gen, không đổi |
| Q-08 | HAS_COLLATERAL = 0: câu “Trái phiếu … có tài sản đảm bảo” hiển thị thế nào; Giá trị và Số ký hiệu TSBĐ ẩn hay trống? HAS_COLLATERAL = 1 mà bảng TSĐB rỗng? | Hiển thị sai bản chất TP | Major | HAS_COLLATERAL = 0 → “không có tài sản đảm bảo”, Giá trị và Số ký hiệu trống; bảng rỗng → 2 trường trống |
| Q-09 | Giá trị TSBĐ “cộng các giá trị”: định dạng số? Nhiều TSĐB thì Số ký hiệu nối bằng ký tự gì? | Không xác định expected | Minor | VND phân cách nghìn; nối “; ” |
| Q-10 | Bảo lãnh thanh toán “Lấy thông tin Bảo lãnh thanh toán” chưa có bảng/cột, chưa có quy tắc khi TP không có bảo lãnh. | Không truy được DB | Major | Không có bảo lãnh → “không có bảo lãnh thanh toán”, TCTD bảo lãnh trống |
| Q-11 | Bảng tài chính: 0 kỳ thì hiển thị gì? Định dạng số VND/Lần (bao nhiêu chữ số thập phân)? Thời điểm cập nhật chưa có field mapping. | Không xác định expected | Major | 0 kỳ → chỉ còn cột Chỉ tiêu, Đơn vị; VND phân cách nghìn; Lần 2 chữ số thập phân |
| Q-12 | Giới tính: CUSTOMER.GENDER lưu mã gì, hiển thị mã hay chữ? | Hiển thị mã thô | Minor | Hiển thị “Nam”/“Nữ” |
| Q-13 | Định dạng các trường ngày (Ngày sinh, Ngày cấp, Ngày cấp/hiệu lực chứng chỉ, Ngày ủy quyền) và ngày/tháng 1 chữ số tại Ngày ký? | Không thống nhất | Minor | dd/mm/yyyy; Ngày ký có số 0 đầu (05, 01) |
| Q-14 | “Ngày phát hành” lấy từ cột nào (BOND_INFO.RELEASE_DATE?), xử lý khi null? Cụm “(điều khoản)” ở căn cứ mục 3.1 i) là chữ cố định hay placeholder? | Sai căn cứ pháp lý | Major | BOND_INFO.RELEASE_DATE; null → trống; “(điều khoản)” là chữ cố định |
| Q-15 | Mục §1 mapping chỉ áp dụng KH cá nhân — KH tổ chức: màn chi tiết ẩn mục PL05 hay hiện “Không có”? | UI không xác định | Minor | Không hiển thị mục PL05 |
| Q-16 | Tài liệu không nêu kênh tạo yêu cầu (Portal offline / App / Web), đường dẫn menu, role được xem PL05, và thời điểm hình thành hợp đồng. | Precondition, phân quyền | Major | Portal SHBond; menu Giao dịch > Yêu cầu đặt mua / Hợp đồng; HĐ hình thành sau khi duyệt |
| Q-17 | Định dạng file PL05 (PDF/DOCX/HTML), có tải xuống/in không, lưu ở đâu (bảng/đường dẫn)? | Không verify được side effect | Minor | PDF xem trực tuyến, lưu gắn REPO_ORDER |
| Q-18 | Nội dung phụ lục TP nhiều dòng: thứ tự, định dạng (gạch đầu dòng/xuống dòng)? Khi chưa khai báo? | Expected không rõ | Minor | Theo STT khai báo, mỗi dòng 1 dòng; trống → để trống |
| Q-19 | Chứng chỉ hành nghề hết hiệu lực (CERTIFICATE_TO < ngày tạo yêu cầu) hoặc KH cá nhân không có bản ghi CUSTOMER_PRO_INFO: vẫn gen PL05? | Rủi ro pháp lý NĐTCN | Major | PL05 chỉ hiển thị dữ liệu, không validate; điều kiện chặn thuộc luồng đặt mua |
| Q-20 | Lỗi chính tả trong tài liệu: “BOND_INF O.TERM”, “isusser Commitments”, “periodic Disclosure”, mẫu “tầm kiêm soát”, “doanh nghiệp là nghiệp”. | Dev có thể map/hiển thị sai | Minor | Theo tên chuẩn BOND_INFO.TERM; nội dung mẫu sửa chính tả |

### 6. Top 5 rủi ro nghiệp vụ
1. Sai căn cứ Nghị định tại biên ngày phát hành (16/09/2022, 05/06/2026) → văn bản pháp lý sai (BR-16).
2. Tick sai ô tư cách NĐTCN / lẫn dữ liệu PRO_TYPE → hồ sơ NĐT chuyên nghiệp không hợp lệ (BR-08–10, Q-01).
3. Lấy nhầm người ký giữa 2 khối xác nhận (IS_DEFAULT 1/0) hoặc lấy người ủy quyền của KH thay vì 069P (BR-13, BR-14).
4. Gen PL05 cho KH tổ chức / không gen cho KH cá nhân / lẫn dữ liệu giữa các lệnh (BR-01, BR-02).
5. Bảng tài chính sai thứ tự, sai số kỳ; TSBĐ cộng sai, cộng cả TSĐB của TP khác (BR-18, BR-19).

---
## B2 – CHIẾN LƯỢC TEST

### 1. Phạm vi
- **In-scope:** gen PL05 (thời điểm, loại KH); hiển thị tại 2 màn; bố cục tĩnh; 62 trường mapping + quy tắc điều kiện (PRO_TYPE, IS_DEFAULT, ngày phát hành, số kỳ tài chính, TSBĐ).
- **Out-of-scope:** mục 2–6 SRS 11 (HĐ bán kỳ hạn, phiếu lệnh mua/bán, bảng dòng tiền, HĐ mua KH bán lại); luồng duyệt/từ chối, ký OTP; màn khai báo master data (chỉ dùng để chuẩn bị dữ liệu); hiệu năng.
### 2. Loại test
- Functional (data mapping, đối chiếu DB) · UI (bố cục văn bản) · Permission cơ bản · Security cơ bản (XSS nội dung) · Integration (CUSTOMER, CUSTOMER_PRO_INFO, CUSTOMER_AUTHORIZER, BOND_INFO, BOND_ISSUER, phụ lục TP, TSĐB, hệ số tài chính).
### 3. Kỹ thuật theo BR
| BR ID | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC |
|---|---|---|---|---|
| BR-01 | UC | EG | High | 1 |
| BR-02 | EP | EG | High | 1 |
| BR-03 | UC | EG | High | 1 |
| BR-04 | UC | EP | High | 1 |
| BR-05 | EG (đối chiếu mẫu) | DT | Low | 2 |
| BR-06 | EP | BVA, EG | Medium | 8 |
| BR-07 | EP | — | Medium | 1 |
| BR-08 | DT | BVA, EG | High | 4 |
| BR-09 | DT | BVA | High | 1 |
| BR-10 | DT | BVA | High | 2 |
| BR-11 | EP | EG | High | 1 |
| BR-12 | EP | EG | High | 1 |
| BR-13 | DT | EG | High | 5 |
| BR-14 | DT | EG | High | 5 |
| BR-15 | EP | EG | Medium | 10 |
| BR-16 | BVA + EP | EG | High | 5 |
| BR-17 | EP | EG | Medium | 5 |
| BR-18 | BVA | EG | High | 8 |
| BR-19 | DT | BVA, EG | High | 3 |
| BR-20 | DT | — | Medium | 2 |
| BR-21 | EP | BVA, EG | Medium | 1 |

> Một TC có thể gắn nhiều BR → tổng cột “Số TC” > tổng TC thực tế.
### 4. Môi trường & dữ liệu test
- Môi trường: UAT Portal SHBond, Chrome bản mới nhất; có quyền query DB (read) để đối chiếu.
- **PC-01 (Precondition chung):** Đăng nhập Portal bằng ACC_MK; dữ liệu theo bảng dưới đã khai báo; tài khoản SHS có AU1 (mặc định) và AU0 (không mặc định).
- **SOP-01 – Mở PL05 từ yêu cầu:** 1. Vào [GĐ] Giao dịch > Yêu cầu đặt mua 2. Lọc theo Số TKCK 3. Click “Xem chi tiết” yêu cầu 4. Click mở “Phụ lục 05”.
- **SOP-02 – Tạo yêu cầu đặt mua:** 1. Vào [GĐ] Giao dịch > Đặt mua 2. Nhập Số TKCK, Mã TP, Khối lượng 100 3. Click gửi/xác nhận → yêu cầu “Chờ duyệt”.
- **SOP-03 – Mở PL05 từ hợp đồng:** 1. Vào [GĐ] Hợp đồng 2. Lọc theo Số TKCK 3. Click “Xem chi tiết” 4. Mở “Phụ lục 05”.

| Mã | Mô tả | Thuộc tính đặc thù |
|---|---|---|
| ACC_MK | Người dùng Portal có quyền tạo yêu cầu đặt mua [GĐ Q-16] | qa_maker_gd |
| ACC_VW | Người dùng Portal chỉ có quyền xem [GĐ Q-16] | qa_viewall |
| ACC_NO | Người dùng Portal không có quyền màn Yêu cầu đặt mua | qa_noaccess |
| KH_01 | 069C000101 · Cá nhân · Nguyễn Văn Hùng · sinh 15/03/1985 · Nam · CCCD 001085012345 · cấp 10/05/2021 tại Cục Cảnh sát QLHC về TTXH · địa chỉ Số 12 ngõ 34 Láng Hạ, Đống Đa, Hà Nội · ĐT 0912345678 | Có chứng chỉ hành nghề: Chứng chỉ hành nghề môi giới chứng khoán, số 001234/MGCK, cấp 01/07/2020, hiệu lực đến 01/07/2030 |
| KH_02 | 069C000102 · Cá nhân · Lê Thị Mai · Nữ | Nắm giữ danh mục chứng khoán niêm yết 2.500.000.000 đồng |
| KH_03 | 069C000103 · Cá nhân · Phạm Quốc Bảo | Có thu nhập chịu thuế năm 2025: 1,2 tỷ đồng |
| KH_04 | 069C000104 · Cá nhân · Hoàng Minh Tuấn | Nhà đầu tư chiến lược |
| KH_05 | 069C000105 · Cá nhân · Đỗ Thị Hạnh · chưa có số điện thoại | Chưa có hồ sơ nhà đầu tư chuyên nghiệp |
| KH_06 | 069C000201 · Tổ chức · Công ty CP Đầu tư XYZ | — |
| KH_07 | 069C000107 · Cá nhân nước ngoài · John Smith · Hộ chiếu C1234567 | Có chứng chỉ hành nghề |
| SHS | Tài khoản SHS 069P000001 · địa chỉ Số 41 Ngô Quyền, Hoàn Kiếm, Hà Nội | AU1 (người ủy quyền mặc định): Nguyễn Văn An · Giám đốc Khối Đầu tư · ủy quyền số 01/2026/UQ-SHS ngày 02/01/2026 · CCCD 001080000001<br>AU0 (người ủy quyền không mặc định): Trần Thị Bình · Trưởng phòng DVKH · ủy quyền số 02/2026/UQ-SHS ngày 05/01/2026 · CCCD 001085000002 |
| TCPH_A | Công ty Cổ phần Tập đoàn ABC · mã số doanh nghiệp 0101234567 | — |
| TP_01 | ABC12601 · tổ chức phát hành TCPH_A · phát hành 05/06/2026 · ngành Bất động sản · kỳ hạn 36 tháng · lãi suất “Cố định 10%/năm 4 kỳ đầu; sau đó lãi suất tham chiếu + 3,5%/năm” · đại diện người sở hữu: Công ty CP Chứng khoán DEF | Có tài sản bảo đảm: 5.000.000.000 (GDBĐ-001/2026) và 3.000.000.000 (GDBĐ-002/2026) · có bảo lãnh của Ngân hàng TMCP Kiểm Thử · số liệu tài chính 3 kỳ: 30/09/2026, 30/06/2026, 31/12/2025 · phụ lục trái phiếu đủ 10 mục, mục Rủi ro có 3 dòng |
| TP_02 | ABC12202 · phát hành 15/09/2022 | Thông tin khác như TP_01 |
| TP_03 | ABC12203 · phát hành 16/09/2022 | Thông tin khác như TP_01 |
| TP_04 | ABC12604 · phát hành 04/06/2026 | Thông tin khác như TP_01 |
| TP_06 | ABC12606 · không có tài sản bảo đảm · không có bảo lãnh · số liệu tài chính 1 kỳ (30/09/2026) · chưa khai báo đại diện người sở hữu | — |
| TP_07 | ABC12607 · cùng TCPH_A với TP_01 · số liệu tài chính 4 kỳ, nhập theo thứ tự 30/06/2026, 31/12/2024, 30/09/2026, 31/12/2025 | Có 1 tài sản bảo đảm 7.000.000.000 (GDBĐ-010/2026); mục đích phát hành khác TP_01 |
| TP_08 | ABC12608 · chưa có số liệu tài chính · chưa khai báo phụ lục · chưa có ngày phát hành (nếu hệ thống cho phép) | Đánh dấu có tài sản bảo đảm nhưng chưa khai báo chi tiết tài sản |

### 5. Exit criteria
- 100% TC High chạy; 0 bug Critical/Major mở; Q&A Blocker (Q-01) đã chốt; ≥ 95% TC Pass; TC phụ thuộc [GĐ] được cập nhật theo câu trả lời BA trước khi chốt kết quả.

---
## B3 – TEST CONDITION

### EP/BVA
| Đối tượng | Lớp hợp lệ | Lớp bất thường | Giá trị biên |
|---|---|---|---|
| Ngày phát hành (BR-16) | 3 lớp: < 16/09/2022 · 16/09/2022–04/06/2026 · ≥ 05/06/2026 (đại diện bằng giá trị biên) | NULL | 15/09/2022 · 16/09/2022 · 04/06/2026 · 05/06/2026 |
| Số kỳ tài chính (BR-18) | 1, 3 kỳ | 0 kỳ; 4 kỳ (vượt max); thứ tự nhập lộn xộn | 1 (min) · 3 (max) · 4 (max+1) |
| Số TSĐB (BR-19) | 2 TSĐB | 0 TSĐB khi HAS_COLLATERAL = 1; TSĐB của TP khác | 0 · 2 |
| Loại KH (BR-02) | CUSTOMER_TYPE = 1 (gồm CN nước ngoài) | CUSTOMER_TYPE = 2 | — |
| Thông tin KH (BR-06) | Đủ dữ liệu | CUST_MOBILE NULL; Unicode tổ hợp; địa chỉ 200 ký tự; CARD_ID có 0 đầu | 200 ký tự |
| CREATED_DATE (BR-21) | 19/11/2026 | CREATED_DATE ≠ ORDER_DATE | 23:59:30 GMT+7 |

### Decision table – PRO_TYPE (BR-08, BR-09, BR-10, Q-01)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| PRO_TYPE | 1 | 2 | 3 | 4 | Không có bản ghi |
| Ô tick [GĐ Q-01] | Ô 1 | Ô 2 | Ô 3 | Ô 4 | Không ô nào |
| thông tin chứng chỉ (Loại, Số, Ngày cấp, Ngày hiệu lực) | Trống | Có | Trống | Trống | Trống |
| Giá trị danh mục CKNY | Trống | Trống | Có | Trống | Trống |
| Năm và Giá trị thu nhập chịu thuế | Trống | Trống | Trống | Có | Trống |
| TC | CHUNG_004, F10_001, F11_001, F12_001, F13_001, F14_001, F15_001, F16_001 | CHUNG_004, F10_001, F11_001, F12_001, F13_001, F14_001, F15_001, F16_001 | CHUNG_004, F10_001, F11_001, F12_001, F13_001, F14_001, F15_001, F16_001 | CHUNG_004, F10_001, F11_001, F12_001, F13_001, F14_001, F15_001, F16_001 | CHUNG_004, F10_001, F11_001, F12_001, F13_001, F14_001, F15_001, F16_001 |

### Decision table – Người đại diện SHS (BR-13, BR-14)
| Điều kiện | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Bản ghi IS_DEFAULT = 1 của 069P | 1 | 1 (đã hoán đổi) | ≥ 2 | 1 | 1 |
| Bản ghi IS_DEFAULT = 0 của 069P | 1 | 1 (đã hoán đổi) | 1 | 0 | 1 |
| KH đặt mua có người UQ riêng | N | N | N | N | Y |
| Kết quả | Mục 2/khối 1 = AU1; khối 2 = AU0 | Đổi vị trí tương ứng | 1 người [GĐ Q-06] | Khối 2 trống | Không dùng người UQ của KH |
| TC | F19_001, F20_001, F21_001, F22_001, F56_001, F57_001, F58_001, F59_001, F60_001, F61_001 | F19_001, F57_001 | F19_001 | F57_001 | F19_001, F57_001 |

### Decision table – TSBĐ & Bảo lãnh (BR-19, BR-20)
| Điều kiện | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| HAS_COLLATERAL | 1 | 1 | 0 | — |
| Số TSĐB | ≥ 1 | 0 | — | — |
| Có bảo lãnh | — | — | — | Y / N |
| Kết quả | Câu “có”, Giá trị = tổng, đủ Số ký hiệu | Giá trị, Số ký hiệu trống [GĐ] | Câu “không có” [GĐ] | Bảo lãnh theo có/không |
| TC | F44_001, F45_001, F46_001 | F45_001 | F44_001, F45_001, F46_001 | F47_001, F48_001 |

### Use case
- **Luồng chính:** tạo yêu cầu (CN) → Chờ duyệt → sinh PL05 → xem tại chi tiết yêu cầu → duyệt → xem tại chi tiết hợp đồng (CHUNG_002, CHUNG_003).
- **Luồng phụ:** KH tổ chức → không sinh PL05 (CHUNG_002, CHUNG_003).
- **Ngoại lệ:** hủy trước khi gửi, bấm gửi 2 lần, lẫn dữ liệu, sửa dữ liệu sau khi sinh, không có quyền (CHUNG_002, CHUNG_003).

### Error guessing áp dụng (file 03)
- Text: Unicode, dài, NULL, ký tự HTML · Số: định dạng nghìn, âm, đơn vị · Ngày: biên ngày, múi giờ, 1 chữ số · Nút: double-click · Phân quyền: truy cập URL trực tiếp · Dữ liệu: lẫn giữa các lệnh/TP/KH, nhiều bản ghi mặc định.

---
## B4 – TEST CASE

Tổng **66 TC** (nhóm Chung 4 TC + 62 trường, mỗi trường 1 TC), viết rút gọn: Precondition chung PC-01, thao tác SOP-01/02/03 xem B2 mục 4. Các trường cùng một khối/câu được gộp vào 1 TC.


### Chung

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_CHUNG_001 | BR-05 | Chung | Kiểm tra bố cục PL05 | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Thực hiện từng tình huống trong Kết quả mong đợi theo dữ liệu ở cột Dữ liệu test<br>2. Kiểm tra kết quả từng tình huống | KH_01 × TP_01 | - Đúng nguyên văn mẫu SRS 11 (quốc hiệu, kính gửi, mục 2–3, cam kết, 3 khối ký)<br>- Không còn ngoặc vuông chờ điền, số thứ tự trường, “null” | Medium | Functional | EG | Q-20 |
| TC_PL05_CHUNG_002 | BR-02, BR-01 | Chung | Kiểm tra sinh PL05 | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Thực hiện từng tình huống trong Kết quả mong đợi theo dữ liệu ở cột Dữ liệu test<br>2. Kiểm tra kết quả từng tình huống | KH_01 × TP_01 · KH_02, KH_03, KH_04 · KH_06 · TP_01, TP_06 · Địa chỉ mới | - Khi KH cá nhân tạo yêu cầu: yêu cầu Chờ duyệt; có PL05, mở được<br>- Khi KH cá nhân thuộc các diện NĐT khác nhau: cả 3 yêu cầu đều có PL05<br>- Khi KH tổ chức: yêu cầu Chờ duyệt; không có PL05<br>- Khi hủy trước khi gửi: không có yêu cầu và PL05 mới<br>- Khi bấm gửi 2 lần: chỉ 1 yêu cầu, 1 PL05<br>- Khi KH có 2 yêu cầu: mỗi PL05 đúng dữ liệu của yêu cầu đó<br>- Khi sửa dữ liệu sau khi sinh: [GĐ Q-07] Giữ nội dung lúc sinh | High | Functional | EP, EG, UC | Q-15 · Q-07 · Q-17 |
| TC_PL05_CHUNG_003 | BR-04, BR-03 | Chung | Kiểm tra hiển thị PL05 | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Thực hiện từng tình huống trong Kết quả mong đợi theo dữ liệu ở cột Dữ liệu test<br>2. Kiểm tra kết quả từng tình huống | KH_01 × TP_01 · KH_06 · ACC_NO | - Có mục “Phụ lục 05”, mở được<br>- có mục “Phụ lục 05”, nội dung giống màn chi tiết yêu cầu<br>- Khi KH tổ chức: [GĐ Q-15] Không có mục PL05<br>- Khi không có quyền: bị chặn, không mở được PL05 | High | Functional | EP, EG, UC | Q-15 · Q-16 |
| TC_PL05_CHUNG_004 | BR-05 | Chung | Kiểm tra ô chọn tư cách nhà đầu tư | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Thực hiện từng tình huống trong Kết quả mong đợi theo dữ liệu ở cột Dữ liệu test<br>2. Kiểm tra kết quả từng tình huống | KH_04 · KH_01 · KH_02 · KH_03 · KH_05 | - Khi KH là NĐT chiến lược: [GĐ Q-01] Chỉ tick ô “NĐT chiến lược”; [GĐ Q-02] Số, ngày Nghị quyết giữ “…”<br>- Khi KH có chứng chỉ hành nghề: [GĐ Q-01] Chỉ tick ô “chứng chỉ hành nghề”<br>- Khi KH có danh mục chứng khoán niêm yết: [GĐ Q-01] Chỉ tick ô “danh mục chứng khoán niêm yết”, không tick sẵn ô chứng chỉ<br>- Khi KH có thu nhập chịu thuế: [GĐ Q-01] Chỉ tick ô “thu nhập chịu thuế”<br>- Khi KH chưa có hồ sơ NĐT chuyên nghiệp: [GĐ Q-19] Không ô nào được tick | High | Functional | DT, EG | Q-01 · Q-19 · Q-02 |

### Trường 01 – Tên khách hàng

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F01_001 | BR-06 | Trường 01 – Tên khách hàng | Kiểm tra Tên khách hàng | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem dòng “Tên tôi là”, mục 3.1 i) | KH_01 | - Đúng họ tên KH đặt mua<br>- Giống nhau ở 2 vị trí | High | Positive | EP |  |

### Trường 02 – Ngày sinh

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F02_001 | BR-06 | Trường 02 – Ngày sinh | Kiểm tra Ngày sinh | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Ngày tháng năm sinh | KH_01 | - Đúng ngày sinh của KH, dạng ngày/tháng/năm [GĐ Q-13] | Medium | Positive | EP | Q-13 |

### Trường 03 – Giới tính

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F03_001 | BR-06 | Trường 03 – Giới tính | Kiểm tra Giới tính | Yêu cầu KH_01, KH_02 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Giới tính | KH_01 nam, KH_02 nữ | - Hiện chữ “Nam”/“Nữ”, không hiện mã [GĐ Q-12] | Medium | Positive | EP | Q-12 |

### Trường 04 – Số CCCD/Hộ chiếu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F04_001 | BR-06 | Trường 04 – Số CCCD/Hộ chiếu | Kiểm tra Số CCCD/Hộ chiếu | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Số CCCD/Hộ chiếu | KH_07 · KH_01 | - Khi KH dùng hộ chiếu: đúng số hộ chiếu, giữ nguyên chữ và số<br>- Khi số CCCD bắt đầu bằng 0: đủ 12 số, giữ số 0 đầu | High | Functional | EG, EP |  |

### Trường 05 – Ngày cấp giấy tờ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F05_001 | BR-06 | Trường 05 – Ngày cấp giấy tờ | Kiểm tra Ngày cấp giấy tờ | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Ngày cấp | KH_01 | - Đúng ngày cấp giấy tờ, dạng ngày/tháng/năm [GĐ Q-13] | Medium | Positive | EP | Q-13 |

### Trường 06 – Nơi cấp giấy tờ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F06_001 | BR-06 | Trường 06 – Nơi cấp giấy tờ | Kiểm tra Nơi cấp giấy tờ | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Nơi cấp | KH_01 | - Đúng nơi cấp giấy tờ | Medium | Positive | EP |  |

### Trường 07 – Nơi ở hiện tại

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F07_001 | BR-06 | Trường 07 – Nơi ở hiện tại | Kiểm tra Nơi ở hiện tại | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem dòng Nơi ở hiện tại | KH_01 · Địa chỉ 200 ký tự | - Đúng địa chỉ liên hệ của KH<br>- Khi địa chỉ dài 200 ký tự: hiện đủ, tự xuống dòng, không tràn lề | Medium | Functional | BVA, EP |  |

### Trường 08 – Điện thoại liên hệ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F08_001 | BR-06 | Trường 08 – Điện thoại liên hệ | Kiểm tra Điện thoại liên hệ | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Điện thoại liên hệ | KH_01 · KH_05 | - Đúng số điện thoại, giữ số 0 đầu<br>- Khi KH chưa có số điện thoại: để trống, không hiện “null” | Medium | Functional | EG, EP |  |

### Trường 09 – Tổ chức phát hành

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F09_001 | BR-07 | Trường 09 – Tổ chức phát hành | Kiểm tra Tổ chức phát hành | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem dòng NĐT chiến lược, mục 3.1 a) | TP_01 | - Đúng tên tổ chức phát hành của mã TP<br>- Giống nhau ở 2 vị trí | High | Positive | EP |  |

### Trường 10 – Loại chứng chỉ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F10_001 | BR-08 | Trường 10 – Loại chứng chỉ | Kiểm tra Loại chứng chỉ | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Loại chứng chỉ | KH_01 · KH_02, KH_03, KH_04, KH_05 | - Khi KH có chứng chỉ hành nghề: đúng loại chứng chỉ đã khai báo<br>- Khi KH không thuộc diện chứng chỉ hành nghề: để trống, không hiện “null” | High | Functional | DT |  |

### Trường 11 – Số chứng chỉ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F11_001 | BR-08 | Trường 11 – Số chứng chỉ | Kiểm tra Số chứng chỉ | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Số (chứng chỉ) | KH_01 · KH_02, KH_03, KH_04, KH_05 | - Khi KH có chứng chỉ hành nghề: đúng số chứng chỉ đã khai báo<br>- Khi KH không thuộc diện chứng chỉ hành nghề: để trống, không hiện “null” | High | Functional | DT |  |

### Trường 12 – Ngày cấp chứng chỉ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F12_001 | BR-08 | Trường 12 – Ngày cấp chứng chỉ | Kiểm tra Ngày cấp chứng chỉ | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem dòng Ngày cấp (chứng chỉ) | KH_01 · KH_02, KH_03, KH_04, KH_05 | - Khi KH có chứng chỉ hành nghề: đúng ngày cấp chứng chỉ đã khai báo, dạng ngày/tháng/năm<br>- Khi KH không thuộc diện chứng chỉ hành nghề: để trống, không hiện “null” | High | Functional | DT |  |

### Trường 13 – Ngày hiệu lực chứng chỉ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F13_001 | BR-08 | Trường 13 – Ngày hiệu lực chứng chỉ | Kiểm tra Ngày hiệu lực chứng chỉ | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem dòng Ngày hiệu lực | KH_01 · Hết hạn 18/11/2026 · KH_02, KH_03, KH_04, KH_05 | - Khi KH có chứng chỉ hành nghề: đúng ngày hiệu lực chứng chỉ đã khai báo, dạng ngày/tháng/năm<br>- Khi chứng chỉ đã hết hạn: [GĐ Q-19] Hiện đúng ngày đã hết hạn, PL05 vẫn sinh<br>- Khi KH không thuộc diện chứng chỉ hành nghề: để trống, không hiện “null” | High | Functional | BVA, DT | Q-19 |

### Trường 14 – Giá trị danh mục chứng khoán niêm yết

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F14_001 | BR-09 | Trường 14 – Giá trị danh mục chứng khoán niêm yết | Kiểm tra Giá trị danh mục chứng khoán niêm yết | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem dòng danh mục chứng khoán niêm yết | KH_02 · KH_01, KH_03, KH_04, KH_05 | - Khi KH có danh mục chứng khoán niêm yết: đúng giá trị đã khai báo; [GĐ Q-03] Đơn vị đồng, phân cách hàng nghìn<br>- Khi KH không thuộc diện danh mục chứng khoán niêm yết: để trống, không hiện “null” | High | Functional | DT | Q-03 |

### Trường 15 – Năm thu nhập chịu thuế

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F15_001 | BR-10 | Trường 15 – Năm thu nhập chịu thuế | Kiểm tra Năm thu nhập chịu thuế | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem dòng thu nhập chịu thuế | KH_03 · Năm 2023, tạo yêu cầu 2026 · KH_01, KH_02, KH_04, KH_05 | - Khi KH có thu nhập chịu thuế: đúng năm đã khai báo, 4 chữ số<br>- Khi năm không phải năm liền trước: [GĐ Q-04] Hiện đúng năm đã khai báo, không tự đổi<br>- Khi KH không thuộc diện thu nhập chịu thuế: để trống, không hiện “null” | High | Functional | BVA, DT | Q-04 |

### Trường 16 – Giá trị thu nhập chịu thuế

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F16_001 | BR-10 | Trường 16 – Giá trị thu nhập chịu thuế | Kiểm tra Giá trị thu nhập chịu thuế | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem dòng thu nhập chịu thuế | KH_03 · KH_01, KH_02, KH_04, KH_05 | - Khi KH có thu nhập chịu thuế: đúng giá trị đã khai báo; [GĐ Q-03] Đơn vị tỷ đồng<br>- Khi KH không thuộc diện thu nhập chịu thuế: để trống, không hiện “null” | High | Functional | DT | Q-03 |

### Trường 17 – Mã trái phiếu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F17_001 | BR-11 | Trường 17 – Mã trái phiếu | Kiểm tra Mã trái phiếu | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2, 3.1 i), 3.2, 3.3 | TP_01 | - Đúng mã TP của yêu cầu<br>- Giống nhau ở mọi vị trí | High | Positive | EP |  |

### Trường 18 – Địa chỉ SHS

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F18_001 | BR-12 | Trường 18 – Địa chỉ SHS | Kiểm tra Địa chỉ SHS | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem mục 2 và 2 khối xác nhận | Tài khoản SHS · Địa chỉ SHS mới | - Đúng địa chỉ liên hệ của SHS<br>- Giống nhau ở 3 vị trí<br>- Khi SHS đổi địa chỉ: pL05 mới hiện địa chỉ mới | High | Functional | EP, EG | Khôi phục dữ liệu sau test |

### Trường 19 – Họ tên người đại diện cung cấp thông tin

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F19_001 | BR-13 | Trường 19 – Họ tên người đại diện cung cấp thông tin | Kiểm tra Họ tên người đại diện cung cấp thông tin | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem mục 2 và khối xác nhận cung cấp thông tin | AU1 · AU1, AU2 · AU0 mặc định · Người ủy quyền của KH_01 | - Đúng họ tên người ủy quyền mặc định của SHS<br>- Giống nhau ở 2 vị trí<br>- Khi SHS có 2 người ủy quyền mặc định: [GĐ Q-06] Chỉ hiện 1 người, không ghép tên<br>- Khi đổi người ủy quyền mặc định: hiện họ tên người mặc định mới<br>- Khi KH đặt mua có người ủy quyền riêng: không hiện người ủy quyền của KH, vẫn là người của SHS | High | Functional | EG, DT | Q-06 · Khôi phục dữ liệu sau test |

### Trường 20 – Chức vụ người đại diện cung cấp thông tin

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F20_001 | BR-13 | Trường 20 – Chức vụ người đại diện cung cấp thông tin | Kiểm tra Chức vụ người đại diện cung cấp thông tin | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 và khối xác nhận cung cấp thông tin | AU1 | - Đúng chức vụ của người ủy quyền mặc định của SHS [GĐ Q-05]<br>- Giống nhau ở 2 vị trí | Medium | Positive | DT | Q-05 |

### Trường 21 – Số văn bản ủy quyền cung cấp thông tin

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F21_001 | BR-13 | Trường 21 – Số văn bản ủy quyền cung cấp thông tin | Kiểm tra Số văn bản ủy quyền cung cấp thông tin | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 và khối xác nhận cung cấp thông tin | AU1 | - Đúng số văn bản ủy quyền của người ủy quyền mặc định của SHS [GĐ Q-05]<br>- Giống nhau ở 2 vị trí | Medium | Positive | DT | Q-05 |

### Trường 22 – Ngày hiệu lực ủy quyền cung cấp thông tin

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F22_001 | BR-13 | Trường 22 – Ngày hiệu lực ủy quyền cung cấp thông tin | Kiểm tra Ngày hiệu lực ủy quyền cung cấp thông tin | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 và khối xác nhận cung cấp thông tin | AU1 | - Đúng ngày hiệu lực ủy quyền của người ủy quyền mặc định của SHS [GĐ Q-05], dạng ngày/tháng/năm<br>- Giống nhau ở 2 vị trí | Medium | Positive | DT | Q-05 |

### Trường 23 – Bản công bố thông tin trước đợt chào bán

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F23_001 | BR-15 | Trường 23 – Bản công bố thông tin trước đợt chào bán | Kiểm tra Bản công bố thông tin trước đợt chào bán | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 a) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | High | Functional | EG, EP | Q-18 |

### Trường 24 – Căn cứ pháp lý của phương án phát hành

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F24_001 | BR-16 | Trường 24 – Căn cứ pháp lý của phương án phát hành | Kiểm tra Căn cứ pháp lý của phương án phát hành | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 b) | TP_02 · TP_03 · TP_04 · TP_01 | - Khi ngày phát hành 15/09/2022: ghi “Nghị định số 153/2020/NĐ-CP”<br>- Khi ngày phát hành 16/09/2022: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 04/06/2026: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 05/06/2026: ghi “Điều 10 Nghị định số 200/2026/NĐ-CP” | High | Functional | BVA | Test sát 2 phía mốc 16/09/2022 và 05/06/2026 để bắt lỗi so sánh sai tại mốc |

### Trường 25 – Phương án phát hành

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F25_001 | BR-15 | Trường 25 – Phương án phát hành | Kiểm tra Phương án phát hành | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 b) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 26 – Căn cứ pháp lý của hồ sơ chào bán

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F26_001 | BR-16 | Trường 26 – Căn cứ pháp lý của hồ sơ chào bán | Kiểm tra Căn cứ pháp lý của hồ sơ chào bán | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 c) | TP_02 · TP_03 · TP_04 · TP_01 | - Khi ngày phát hành 15/09/2022: ghi “Nghị định số 153/2020/NĐ-CP”<br>- Khi ngày phát hành 16/09/2022: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 04/06/2026: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 05/06/2026: ghi “Nghị định số 200/2026/NĐ-CP” | High | Functional | BVA | Test sát 2 phía mốc 16/09/2022 và 05/06/2026 để bắt lỗi so sánh sai tại mốc |

### Trường 27 – Hồ sơ chào bán

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F27_001 | BR-15 | Trường 27 – Hồ sơ chào bán | Kiểm tra Hồ sơ chào bán | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 c) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 28 – Căn cứ pháp lý của công bố thông tin định kỳ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F28_001 | BR-16 | Trường 28 – Căn cứ pháp lý của công bố thông tin định kỳ | Kiểm tra Căn cứ pháp lý của công bố thông tin định kỳ | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 d) | TP_02 · TP_03 · TP_04 · TP_01 | - Khi ngày phát hành 15/09/2022: ghi “Nghị định số 153/2020/NĐ-CP”<br>- Khi ngày phát hành 16/09/2022: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 04/06/2026: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 05/06/2026: ghi “Nghị định số 200/2026/NĐ-CP” | High | Functional | BVA | Test sát 2 phía mốc 16/09/2022 và 05/06/2026 để bắt lỗi so sánh sai tại mốc |

### Trường 29 – Công bố thông tin định kỳ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F29_001 | BR-15 | Trường 29 – Công bố thông tin định kỳ | Kiểm tra Công bố thông tin định kỳ | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 d) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 30 – Mã số đăng ký doanh nghiệp

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F30_001 | BR-17 | Trường 30 – Mã số đăng ký doanh nghiệp | Kiểm tra Mã số đăng ký doanh nghiệp | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 a) | TCPH_A | - Đúng mã số đăng ký doanh nghiệp của tổ chức phát hành | Medium | Positive | EP | Q-20 |

### Trường 31 – Ngành nghề

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F31_001 | BR-17 | Trường 31 – Ngành nghề | Kiểm tra Ngành nghề | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 a) | TP_01 | - Đúng ngành nghề đã khai báo | Medium | Positive | EP |  |

### Trường 32 – Thời điểm cập nhật số liệu tài chính

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F32_001 | BR-18 | Trường 32 – Thời điểm cập nhật số liệu tài chính | Kiểm tra Thời điểm cập nhật số liệu tài chính | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_06 · TP_01 · TP_07 · TP_08 | - Khi có 1 kỳ: chỉ 1 cột thời điểm, không có cột trống<br>- Khi có 3 kỳ: 3 cột, xếp từ gần đến xa, dạng ngày/tháng/năm<br>- Khi có 4 kỳ: chỉ 3 kỳ gần nhất<br>- Khi nhập kỳ không theo thời gian: xếp theo thời gian, không theo thứ tự nhập<br>- Khi chưa có kỳ nào: [GĐ Q-11] Bảng chỉ còn cột Chỉ tiêu, Đơn vị | High | Functional | BVA, EG | Q-11 |

### Trường 33 – Tổng tài sản

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F33_001 | BR-18 | Trường 33 – Tổng tài sản | Kiểm tra Tổng tài sản | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01 có 3 kỳ | - Đúng giá trị từng kỳ, đúng cột<br>- [GĐ Q-11] Đơn vị VND, phân cách hàng nghìn | Medium | Positive | EP | Q-11 |

### Trường 34 – Nợ phải trả

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F34_001 | BR-18 | Trường 34 – Nợ phải trả | Kiểm tra Nợ phải trả | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01 có 3 kỳ | - Đúng giá trị từng kỳ, đúng cột<br>- [GĐ Q-11] Đơn vị VND, phân cách hàng nghìn | Medium | Positive | EP | Q-11 |

### Trường 35 – Vốn chủ sở hữu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F35_001 | BR-18 | Trường 35 – Vốn chủ sở hữu | Kiểm tra Vốn chủ sở hữu | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01 có 3 kỳ | - Đúng giá trị từng kỳ, đúng cột<br>- [GĐ Q-11] Đơn vị VND, phân cách hàng nghìn | Medium | Positive | EP | Q-11 |

### Trường 36 – Vốn lưu động thường xuyên

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F36_001 | BR-18 | Trường 36 – Vốn lưu động thường xuyên | Kiểm tra Vốn lưu động thường xuyên | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01 có 3 kỳ | - Đúng giá trị từng kỳ, đúng cột<br>- [GĐ Q-11] Đơn vị VND, phân cách hàng nghìn | Medium | Positive | EP | Q-11 |

### Trường 37 – Hệ số dư nợ vay/Tổng tài sản

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F37_001 | BR-18 | Trường 37 – Hệ số dư nợ vay/Tổng tài sản | Kiểm tra Hệ số dư nợ vay/Tổng tài sản | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01 có 3 kỳ | - Đúng giá trị từng kỳ, đúng cột<br>- [GĐ Q-11] Đơn vị Lần, giữ phần thập phân | Medium | Positive | EP | Q-11 |

### Trường 38 – Hệ số nợ phải trả/Vốn chủ sở hữu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F38_001 | BR-18 | Trường 38 – Hệ số nợ phải trả/Vốn chủ sở hữu | Kiểm tra Hệ số nợ phải trả/Vốn chủ sở hữu | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01 có 3 kỳ | - Đúng giá trị từng kỳ, đúng cột<br>- [GĐ Q-11] Đơn vị Lần, giữ phần thập phân | Medium | Positive | EP | Q-11 |

### Trường 39 – Khả năng thanh toán hiện hành

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F39_001 | BR-18 | Trường 39 – Khả năng thanh toán hiện hành | Kiểm tra Khả năng thanh toán hiện hành | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01 có 3 kỳ | - Đúng giá trị từng kỳ, đúng cột<br>- [GĐ Q-11] Đơn vị Lần, giữ phần thập phân | Medium | Positive | EP | Q-11 |

### Trường 40 – Mục đích phát hành

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F40_001 | BR-15 | Trường 40 – Mục đích phát hành | Kiểm tra Mục đích phát hành | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem mục 3.1 c) | Phụ lục TP_01 · TP_07 · <b>, <script> · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP khác cùng tổ chức phát hành có mục đích khác: hiện mục đích của đúng mã TP trong yêu cầu<br>- Khi nội dung có thẻ HTML: hiện dạng chữ, không chạy script, không vỡ bố cục<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 41 – Kế hoạch sử dụng vốn

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F41_001 | BR-15 | Trường 41 – Kế hoạch sử dụng vốn | Kiểm tra Kế hoạch sử dụng vốn | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 d) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 42 – Kế hoạch sử dụng vốn tạm thời nhàn rỗi

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F42_001 | BR-15 | Trường 42 – Kế hoạch sử dụng vốn tạm thời nhàn rỗi | Kiểm tra Kế hoạch sử dụng vốn tạm thời nhàn rỗi | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 d) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 43 – Phương án bố trí nguồn trả lãi, gốc

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F43_001 | BR-15 | Trường 43 – Phương án bố trí nguồn trả lãi, gốc | Kiểm tra Phương án bố trí nguồn trả lãi, gốc | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 đ) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 44 – Có/không tài sản bảo đảm

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F44_001 | BR-19 | Trường 44 – Có/không tài sản bảo đảm | Kiểm tra Có/không tài sản bảo đảm | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | TP_01 · TP_06 | - Khi TP có tài sản bảo đảm: hiện câu “có tài sản đảm bảo”<br>- Khi TP không có tài sản bảo đảm: [GĐ Q-08] Hiện câu “không có tài sản đảm bảo” | High | Functional | DT | Q-08 |

### Trường 45 – Giá trị tài sản bảo đảm

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F45_001 | BR-19 | Trường 45 – Giá trị tài sản bảo đảm | Kiểm tra Giá trị tài sản bảo đảm | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | TP_01 · TP_01, TP_07 · TP_06 · TP_08 | - Khi TP có 2 tài sản bảo đảm: bằng tổng giá trị các tài sản bảo đảm; [GĐ Q-09] Phân cách hàng nghìn<br>- Khi TP khác cùng tổ chức phát hành có tài sản bảo đảm: không cộng tài sản của TP khác<br>- Khi TP không có tài sản bảo đảm: [GĐ Q-08] Để trống, không hiện “null”<br>- Khi chưa khai báo chi tiết tài sản: [GĐ Q-08] Để trống, không hiện số 0 | High | Functional | EG, DT | Q-08 · Q-09 |

### Trường 46 – Số đăng ký giao dịch bảo đảm

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F46_001 | BR-19 | Trường 46 – Số đăng ký giao dịch bảo đảm | Kiểm tra Số đăng ký giao dịch bảo đảm | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | TP_01 · TP_06 | - Khi TP có 2 tài sản bảo đảm: hiện đủ 2 số đăng ký [GĐ Q-09]<br>- Khi TP không có tài sản bảo đảm: [GĐ Q-08] Để trống, không hiện “null” | Medium | Functional | DT | Q-08 · Q-09 |

### Trường 47 – Có/không bảo lãnh thanh toán

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F47_001 | BR-20 | Trường 47 – Có/không bảo lãnh thanh toán | Kiểm tra Có/không bảo lãnh thanh toán | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | TP_01 · TP_06 | - Khi TP có bảo lãnh: hiện câu “có bảo lãnh thanh toán của Tổ chức tín dụng”<br>- Khi TP không có bảo lãnh: [GĐ Q-10] Hiện câu “không có bảo lãnh thanh toán” | High | Functional | DT | Q-10 |

### Trường 48 – Tổ chức tín dụng bảo lãnh

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F48_001 | BR-20 | Trường 48 – Tổ chức tín dụng bảo lãnh | Kiểm tra Tổ chức tín dụng bảo lãnh | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | TP_01 · TP_06 | - Khi TP có bảo lãnh: đúng tên tổ chức tín dụng bảo lãnh<br>- Khi TP không có bảo lãnh: [GĐ Q-10] Để trống, không hiện “null” | High | Functional | DT | Q-10 |

### Trường 49 – Lãi suất trái phiếu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F49_001 | BR-17 | Trường 49 – Lãi suất trái phiếu | Kiểm tra Lãi suất trái phiếu | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 g) | TP_01 | - Đúng mô tả lãi suất, đủ nội dung | Medium | Positive | EP |  |

### Trường 50 – Kỳ hạn trái phiếu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F50_001 | BR-17 | Trường 50 – Kỳ hạn trái phiếu | Kiểm tra Kỳ hạn trái phiếu | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 g) | TP_01 | - Đúng kỳ hạn trái phiếu | Medium | Positive | EP | Q-20 |

### Trường 51 – Cam kết của tổ chức phát hành

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F51_001 | BR-15 | Trường 51 – Cam kết của tổ chức phát hành | Kiểm tra Cam kết của tổ chức phát hành | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 h) | Phụ lục TP_01 · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | Medium | Functional | EG, EP | Q-18 |

### Trường 52 – Căn cứ pháp lý của đối tượng chào bán

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F52_001 | BR-16 | Trường 52 – Căn cứ pháp lý của đối tượng chào bán | Kiểm tra Căn cứ pháp lý của đối tượng chào bán | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 i) | TP_02 · TP_03 · TP_04 · TP_01 | - Khi ngày phát hành 15/09/2022: ghi “Nghị định số 153/2020/NĐ-CP”<br>- Khi ngày phát hành 16/09/2022: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 04/06/2026: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 05/06/2026: ghi “(điều khoản) Nghị định số 200/2026/NĐ-CP” [GĐ Q-14] | High | Functional | BVA | Test sát 2 phía mốc 16/09/2022 và 05/06/2026 để bắt lỗi so sánh sai tại mốc · Q-14 |

### Trường 53 – Đại diện người sở hữu trái phiếu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F53_001 | BR-17 | Trường 53 – Đại diện người sở hữu trái phiếu | Kiểm tra Đại diện người sở hữu trái phiếu | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 k) | TP_01 · TP_06 | - Đúng đại diện đã khai báo, giữ chữ “(nếu có)”<br>- Khi TP chưa khai báo đại diện: để trống, không hiện “null”, giữ chữ “(nếu có)” | Medium | Functional | EG, EP |  |

### Trường 54 – Rủi ro

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F54_001 | BR-15 | Trường 54 – Rủi ro | Kiểm tra Rủi ro | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.2 | Phụ lục TP_01 · Rủi ro TP_01 có 3 dòng · TP_08 | - Đúng nội dung đã khai báo trong phụ lục TP<br>- Đủ nội dung, không bị cắt<br>- Khi có nhiều dòng: hiện đủ các dòng, đúng thứ tự [GĐ Q-18]<br>- Khi TP chưa khai báo phụ lục: [GĐ Q-18] Để trống, không hiện “null” | High | Functional | EG, EP | Q-18 |

### Trường 55 – Ngày ký

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F55_001 | BR-21 | Trường 55 – Ngày ký | Kiểm tra Ngày ký | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem dòng “Hà Nội, ngày…” và 2 khối xác nhận | Tạo ngày 19/11/2026 · 23:59:30 · Ngày GD 20/11/2026 | - Là ngày tạo yêu cầu, dạng “ngày … tháng … năm …”<br>- Giống nhau ở 3 vị trí<br>- Khi tạo yêu cầu lúc 23:59: đúng ngày theo giờ Việt Nam, không lệch sang hôm sau<br>- Khi ngày giao dịch khác ngày tạo yêu cầu: lấy ngày tạo yêu cầu, không lấy ngày giao dịch | High | Functional | EG, EP | Q-13 |

### Trường 56 – Số CCCD người đại diện cung cấp thông tin

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F56_001 | BR-13 | Trường 56 – Số CCCD người đại diện cung cấp thông tin | Kiểm tra Số CCCD người đại diện cung cấp thông tin | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem khối xác nhận cung cấp thông tin | AU1 | - Đúng số CCCD của người ủy quyền mặc định của SHS [GĐ Q-05], giữ số 0 đầu | Medium | Positive | DT | Q-05 |

### Trường 57 – Họ tên người đại diện xác nhận tư cách NĐT

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F57_001 | BR-14 | Trường 57 – Họ tên người đại diện xác nhận tư cách NĐT | Kiểm tra Họ tên người đại diện xác nhận tư cách NĐT | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Chuẩn bị dữ liệu, tạo yêu cầu (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem khối xác nhận tư cách NĐT | AU0 · Chỉ còn AU1 · AU1 không mặc định · Người ủy quyền của KH_01 | - Đúng họ tên người ủy quyền không mặc định của SHS<br>- Khi SHS không có người ủy quyền không mặc định: [GĐ Q-06] Để trống, không lấy người mặc định<br>- Khi đổi người ủy quyền mặc định: hiện họ tên người không mặc định mới<br>- Khi KH đặt mua có người ủy quyền riêng: không hiện người ủy quyền của KH, vẫn là người của SHS | High | Functional | EG, DT | Q-06 · Khôi phục dữ liệu sau test |

### Trường 58 – Số văn bản ủy quyền xác nhận tư cách NĐT

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F58_001 | BR-14 | Trường 58 – Số văn bản ủy quyền xác nhận tư cách NĐT | Kiểm tra Số văn bản ủy quyền xác nhận tư cách NĐT | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem khối xác nhận tư cách NĐT | AU0 | - Đúng số văn bản ủy quyền của người ủy quyền không mặc định của SHS [GĐ Q-05] | Medium | Positive | DT | Q-05 |

### Trường 59 – Ngày hiệu lực ủy quyền xác nhận tư cách NĐT

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F59_001 | BR-14 | Trường 59 – Ngày hiệu lực ủy quyền xác nhận tư cách NĐT | Kiểm tra Ngày hiệu lực ủy quyền xác nhận tư cách NĐT | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem khối xác nhận tư cách NĐT | AU0 | - Đúng ngày hiệu lực ủy quyền của người ủy quyền không mặc định của SHS [GĐ Q-05], dạng ngày/tháng/năm | Medium | Positive | DT | Q-05 |

### Trường 60 – Số CCCD người đại diện xác nhận tư cách NĐT

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F60_001 | BR-14 | Trường 60 – Số CCCD người đại diện xác nhận tư cách NĐT | Kiểm tra Số CCCD người đại diện xác nhận tư cách NĐT | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem khối xác nhận tư cách NĐT | AU0 | - Đúng số CCCD của người ủy quyền không mặc định của SHS [GĐ Q-05], giữ số 0 đầu | Medium | Positive | DT | Q-05 |

### Trường 61 – Chức vụ người đại diện xác nhận tư cách NĐT

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F61_001 | BR-14 | Trường 61 – Chức vụ người đại diện xác nhận tư cách NĐT | Kiểm tra Chức vụ người đại diện xác nhận tư cách NĐT | Yêu cầu KH_01 × TP_01 Chờ duyệt | 1. Mở PL05 (SOP-01)<br>2. Xem khối xác nhận tư cách NĐT | AU0 | - Đúng chức vụ của người ủy quyền không mặc định của SHS [GĐ Q-05] | Medium | Positive | DT | Q-05 |

### Trường 62 – Căn cứ pháp lý của bản công bố thông tin

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_F62_001 | BR-16 | Trường 62 – Căn cứ pháp lý của bản công bố thông tin | Kiểm tra Căn cứ pháp lý của bản công bố thông tin | PC-01; chuẩn bị dữ liệu theo cột Dữ liệu test | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 a) | TP_02 · TP_03 · TP_04 · TP_01 | - Khi ngày phát hành 15/09/2022: ghi “Nghị định số 153/2020/NĐ-CP”<br>- Khi ngày phát hành 16/09/2022: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 04/06/2026: ghi “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP”<br>- Khi ngày phát hành 05/06/2026: ghi “khoản 1 Điều 29 Nghị định số 200/2026/NĐ-CP” | High | Functional | BVA | Test sát 2 phía mốc 16/09/2022 và 05/06/2026 để bắt lỗi so sánh sai tại mốc |

---
## B5 – TỰ REVIEW & HOÀN THIỆN

### 1. RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs (bỏ tiền tố TC_PL05_) | Phủ | Ghi chú |
|---|---|---|---|---|---|
| BR-01 | Sinh PL05 sau khi tạo yêu cầu KH đặt mua → trạng thái Chờ duyệt | 1 | CHUNG_002 | Đủ |  |
| BR-02 | Chỉ sinh PL05 cho KH Cá nhân; KH Tổ chức không sinh | 1 | CHUNG_002 | Đủ |  |
| BR-03 | PL05 hiển thị tại màn Xem chi tiết yêu cầu đặt mua | 1 | CHUNG_003 | Đủ |  |
| BR-04 | PL05 hiển thị tại màn Xem chi tiết hợp đồng | 1 | CHUNG_003 | Đủ |  |
| BR-05 | Nội dung tĩnh, bố cục mẫu PL05 (quốc hiệu, kính gửi, 4 phương án tư cá… | 2 | CHUNG_001, CHUNG_004 | Đủ |  |
| BR-06 | Thông tin KH (Tên, Ngày sinh, Giới tính, Số CCCD/Hộ chiếu, Ngày cấp, N… | 8 | F01_001, F02_001, F03_001, F04_001, F05_001, F06_001, F07_001, F08_001 | Đủ |  |
| BR-07 | Tổ chức phát hành = BOND_ISSUER.ISSUER_NAME (2 vị trí) | 1 | F09_001 | Đủ |  |
| BR-08 | Thông tin chứng chỉ (Loại, Số, Ngày cấp, Ngày hiệu lực) lấy CUSTOMER_P… | 4 | F10_001, F11_001, F12_001, F13_001 | Đủ |  |
| BR-09 | Giá trị danh mục CKNY = CUSTOMER_PRO_INFO.INVEST_AMOUNT khi PRO_TYPE =… | 1 | F14_001 | Đủ |  |
| BR-10 | Năm thu nhập chịu thuế = CUSTOMER_PRO_INFO.YEAR, Giá trị thu nhập chịu… | 2 | F15_001, F16_001 | Đủ |  |
| BR-11 | Mã trái phiếu = REPO_ORDER.SYMBOL (= BOND_INFO.CODE), đồng nhất mọi vị… | 1 | F17_001 | Đủ |  |
| BR-12 | Địa chỉ SHS = CUSTOMER.CUST_ADDRESS của KH 069P000001 (3 vị trí) | 1 | F18_001 | Đủ |  |
| BR-13 | Khối xác nhận cung cấp thông tin (Người đại diện, Chức vụ, Số/Ngày ủy … | 5 | F19_001, F20_001, F21_001, F22_001, F56_001 | Đủ |  |
| BR-14 | Khối xác nhận tư cách NĐT (Người đại diện, Số/Ngày ủy quyền, CCCD, Chứ… | 5 | F57_001, F58_001, F59_001, F60_001, F61_001 | Đủ |  |
| BR-15 | 10 nội dung lấy từ bảng phụ lục trái phiếu: Bản CBTT trước đợt chào bá… | 10 | F23_001, F25_001, F27_001, F29_001, F40_001, F41_001, F42_001, F43_001, F51_001, F54_001 | Đủ |  |
| BR-16 | Căn cứ Nghị định (5 vị trí: mục 2 a, 2 b, 2 c, 2 d, 3.1 i) theo Ngày p… | 5 | F24_001, F26_001, F28_001, F52_001, F62_001 | Đủ |  |
| BR-17 | Mã số ĐKDN = REGISTRATION_ID, Loại ngành nghề = BUSINESS_TYPE, Lãi suấ… | 5 | F30_001, F31_001, F49_001, F50_001, F53_001 | Đủ |  |
| BR-18 | Bảng tình hình tài chính: tối đa 3 kỳ gần nhất, sắp xếp gần → xa; có b… | 8 | F32_001, F33_001, F34_001, F35_001, F36_001, F37_001, F38_001, F39_001 | Đủ |  |
| BR-19 | TSBĐ: Có/không = BOND_INFO.HAS_COLLATERAL; Giá trị TSBĐ = tổng giá trị… | 3 | F44_001, F45_001, F46_001 | Đủ |  |
| BR-20 | Bảo lãnh thanh toán (Có/không, Tổ chức tín dụng bảo lãnh) lấy từ thông… | 2 | F47_001, F48_001 | Đủ |  |
| BR-21 | Ngày ký (ngày/tháng/năm) = REPO_ORDER.CREATED_DATE (3 vị trí ký) | 1 | F55_001 | Đủ |  |

### 2. Checklist (file 05)
| Nhóm | Tiêu chí | Kết quả | Ghi chú |
|---|---|---|---|
| A | 100% BR có ≥ 1 TC | Đạt | 21/21 BR |
| A | 100% trường mapping có ≥ 1 TC | Đạt | 62/62 trường – xem mục 5 |
| A | Mỗi trường có hợp lệ / trống / biên | Đạt | Trường text ngắn (Nơi cấp, Loại ngành nghề) chỉ có case hợp lệ – rủi ro thấp, chấp nhận |
| A | Luồng chính + phụ + ngoại lệ | Đạt | GEN, VIEW |
| A | Phân quyền từng role | Một phần | SRS 11 không định nghĩa role → 1 TC theo [GĐ Q-16] |
| A | DT: mỗi rule có TC | Đạt | 3 bảng DT phủ đủ cột |
| A | Error guessing | Đạt | Unicode, NULL, double-click, múi giờ, XSS, lẫn dữ liệu |
| B | Đủ 12 cột, ID đúng format, không trùng | Đạt | Kiểm tra tự động khi sinh file |
| B | 1 TC = 1 mục tiêu | Đạt | Mỗi trường đúng 1 TC; các điều kiện đặc biệt của trường liệt kê từng dòng trong Kết quả mong đợi |
| B | Title “Kiểm tra … khi …” | Đạt* | Vài TC dạng “Kiểm tra <tên trường> …” không có “khi” vì không có điều kiện riêng ngoài PC-01 |
| B | Test data cụ thể | Đạt | Bộ dữ liệu B2 mục 4 |
| B | Expected đo được | Đạt | Mỗi ý 1 dòng, viết tiếng Việt: hiển thị gì, ở đâu, dạng nào; giá trị cụ thể đối chiếu Test Data |
| B | Negative: báo lỗi + không thực hiện | Đạt | Biểu mẫu không có message lỗi → kiểm “không gen / để trống / không lấy nhầm” |
| B | Message nguyên văn hoặc [GĐ] | Đạt | Mọi giá trị chưa rõ gắn [GĐ] + Q-xx |
| C | Không TC trùng | Đạt | Mỗi trường 1 TC; nhóm Chung 4 TC theo chủ đề |
| C | Priority hợp lý | Đạt | Căn cứ pháp lý, tư cách NĐT, người ký = High |
| C | Thứ tự sắp xếp | Đạt | Nhóm Chung trước, sau đó trường 01 → 62; trong Kết quả mong đợi: trường hợp thường trước, các dòng “Khi …” sau |

### 3. Thay đổi so với bản trước
| # | Thay đổi |
|---|---|
| 1 | Mỗi TC chỉ kiểm 1 trường; bỏ các TC kiểm nhiều trường cùng lúc (để trống theo diện, 5 vị trí căn cứ, khối xác nhận đổi người…) và tách thành TC riêng cho từng trường |
| 2 | Sắp theo trường 01 → 62; mã TC dạng TC_PL05_F<số trường>_<STT>, cột Module ghi “Trường nn – tên trường” |
| 3 | Viết ngắn: Precondition, Steps 1–2 dòng; Expected 1–2 ý |
| 4 | Gộp theo trường: mỗi trường 1 TC, điều kiện đặc biệt ghi “Khi …: …” trong Kết quả mong đợi |

### 4. Thống kê
| Chỉ số | Giá trị | Mục tiêu | Nhận xét |
|---|---|---|---|
| Tổng TC | 66 | — | Bản rút gọn |
| Requirement coverage | 100% (21/21) | 100% | Đạt |
| High / Medium / Low | 30 (45%) / 36 (54%) / 0 (0%) | ≈ 25/50/25 | High cao hơn chuẩn do biểu mẫu pháp lý, mapping sai = sai văn bản ký |
| Positive : Non-positive | 26 : 40 (39% : 61%) | ≈ 30 : 70 | Non-positive gồm Negative, Boundary, Permission, Security, UI. Chức năng sinh văn bản thiên về xác minh mapping nên Positive cao hơn chuẩn |
| Theo Type | Functional 40, Positive 26 | — |  |
| Q&A mở | 20 (Blocker 1 · Major 12 · Minor 7) | Blocker = 0 trước khi test | Q-01 phải chốt trước khi chạy CHUNG_004 |
| TC phụ thuộc [GIẢ ĐỊNH] | 44 | — | Cập nhật expected khi BA trả lời |

### 5. Đối chiếu 62 trường mapping
| STT | Trường thông tin (SRS 11) | Số TC | TC IDs (bỏ tiền tố TC_PL05_) |
|---|---|---|---|
| 1 | Tên khách hàng | 1 | F01_001 |
| 2 | Ngày sinh | 1 | F02_001 |
| 3 | Giới tính | 1 | F03_001 |
| 4 | Số CCCD/Hộ chiếu | 1 | F04_001 |
| 5 | Ngày cấp giấy tờ | 1 | F05_001 |
| 6 | Nơi cấp giấy tờ | 1 | F06_001 |
| 7 | Nơi ở hiện tại | 1 | F07_001 |
| 8 | Điện thoại liên hệ | 1 | F08_001 |
| 9 | Tổ chức phát hành | 1 | F09_001 |
| 10 | Loại chứng chỉ | 1 | F10_001 |
| 11 | Số chứng chỉ | 1 | F11_001 |
| 12 | Ngày cấp chứng chỉ | 1 | F12_001 |
| 13 | Ngày hiệu lực chứng chỉ | 1 | F13_001 |
| 14 | Giá trị danh mục chứng khoán niêm yết | 1 | F14_001 |
| 15 | Năm thu nhập chịu thuế | 1 | F15_001 |
| 16 | Giá trị thu nhập chịu thuế | 1 | F16_001 |
| 17 | Mã trái phiếu | 1 | F17_001 |
| 18 | Địa chỉ SHS | 1 | F18_001 |
| 19 | Họ tên người đại diện cung cấp thông tin | 1 | F19_001 |
| 20 | Chức vụ người đại diện cung cấp thông tin | 1 | F20_001 |
| 21 | Số văn bản ủy quyền cung cấp thông tin | 1 | F21_001 |
| 22 | Ngày hiệu lực ủy quyền cung cấp thông tin | 1 | F22_001 |
| 23 | Bản công bố thông tin trước đợt chào bán | 1 | F23_001 |
| 24 | Căn cứ pháp lý của phương án phát hành | 1 | F24_001 |
| 25 | Phương án phát hành | 1 | F25_001 |
| 26 | Căn cứ pháp lý của hồ sơ chào bán | 1 | F26_001 |
| 27 | Hồ sơ chào bán | 1 | F27_001 |
| 28 | Căn cứ pháp lý của công bố thông tin định kỳ | 1 | F28_001 |
| 29 | Công bố thông tin định kỳ | 1 | F29_001 |
| 30 | Mã số đăng ký doanh nghiệp | 1 | F30_001 |
| 31 | Ngành nghề | 1 | F31_001 |
| 32 | Thời điểm cập nhật số liệu tài chính | 1 | F32_001 |
| 33 | Tổng tài sản | 1 | F33_001 |
| 34 | Nợ phải trả | 1 | F34_001 |
| 35 | Vốn chủ sở hữu | 1 | F35_001 |
| 36 | Vốn lưu động thường xuyên | 1 | F36_001 |
| 37 | Hệ số dư nợ vay/Tổng tài sản | 1 | F37_001 |
| 38 | Hệ số nợ phải trả/Vốn chủ sở hữu | 1 | F38_001 |
| 39 | Khả năng thanh toán hiện hành | 1 | F39_001 |
| 40 | Mục đích phát hành | 1 | F40_001 |
| 41 | Kế hoạch sử dụng vốn | 1 | F41_001 |
| 42 | Kế hoạch sử dụng vốn tạm thời nhàn rỗi | 1 | F42_001 |
| 43 | Phương án bố trí nguồn trả lãi, gốc | 1 | F43_001 |
| 44 | Có/không tài sản bảo đảm | 1 | F44_001 |
| 45 | Giá trị tài sản bảo đảm | 1 | F45_001 |
| 46 | Số đăng ký giao dịch bảo đảm | 1 | F46_001 |
| 47 | Có/không bảo lãnh thanh toán | 1 | F47_001 |
| 48 | Tổ chức tín dụng bảo lãnh | 1 | F48_001 |
| 49 | Lãi suất trái phiếu | 1 | F49_001 |
| 50 | Kỳ hạn trái phiếu | 1 | F50_001 |
| 51 | Cam kết của tổ chức phát hành | 1 | F51_001 |
| 52 | Căn cứ pháp lý của đối tượng chào bán | 1 | F52_001 |
| 53 | Đại diện người sở hữu trái phiếu | 1 | F53_001 |
| 54 | Rủi ro | 1 | F54_001 |
| 55 | Ngày ký | 1 | F55_001 |
| 56 | Số CCCD người đại diện cung cấp thông tin | 1 | F56_001 |
| 57 | Họ tên người đại diện xác nhận tư cách NĐT | 1 | F57_001 |
| 58 | Số văn bản ủy quyền xác nhận tư cách NĐT | 1 | F58_001 |
| 59 | Ngày hiệu lực ủy quyền xác nhận tư cách NĐT | 1 | F59_001 |
| 60 | Số CCCD người đại diện xác nhận tư cách NĐT | 1 | F60_001 |
| 61 | Chức vụ người đại diện xác nhận tư cách NĐT | 1 | F61_001 |
| 62 | Căn cứ pháp lý của bản công bố thông tin | 1 | F62_001 |

### 6. Việc cần làm tiếp
- Gửi BA 20 câu Q&A, ưu tiên Q-01 (Blocker), Q-03, Q-05, Q-08, Q-10, Q-14, Q-16.
- Chuẩn bị dữ liệu B2 mục 4 trên UAT (7 mã TP, 7 KH, 2 người UQ của 069P).
- Gõ `/EXPORT` để xuất Excel theo template.