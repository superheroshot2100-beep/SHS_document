# PHỤ LỤC 05 – PHÂN TÍCH & TEST CASE (B1 → B5)

> Nguồn duy nhất: SRS **11. Quản lý biểu mẫu** (BON-1836, v1.1 – 11/11/2026), mục **1. Mẫu Phụ lục 05** và **1. Mẫu Phụ lục 05 mapping**. Mục 2–6 (HĐ bán kỳ hạn, phiếu lệnh, dòng tiền…) ngoài phạm vi.
> Người viết: Nguyễn Sỹ Hà · Ngày: 19/11/2026 · Chạy /FULL (không dừng xác nhận giữa các bước).
> Ký hiệu: [GĐ] = [GIẢ ĐỊNH] · Q-xx = câu hỏi mở. Test case của cùng một trường xếp liền nhau. Expected ghi theo quy tắc (lấy từ đâu, hiển thị thế nào), giá trị cụ thể đối chiếu sheet/bảng Test Data.

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
| BR-01 | UC | EG | High | 5 |
| BR-02 | EP | EG | High | 3 |
| BR-03 | UC | EG | High | 2 |
| BR-04 | UC | EP | High | 2 |
| BR-05 | EG (đối chiếu mẫu) | DT | Low | 6 |
| BR-06 | EP | BVA, EG | Medium | 11 |
| BR-07 | EP | — | Medium | 1 |
| BR-08 | DT | BVA, EG | High | 7 |
| BR-09 | DT | BVA | High | 6 |
| BR-10 | DT | BVA | High | 7 |
| BR-11 | EP | EG | High | 2 |
| BR-12 | EP | EG | High | 2 |
| BR-13 | DT | EG | High | 5 |
| BR-14 | DT | EG | High | 5 |
| BR-15 | EP | EG | Medium | 5 |
| BR-16 | BVA + EP | EG | High | 5 |
| BR-17 | EP | EG | Medium | 6 |
| BR-18 | BVA | EG | High | 7 |
| BR-19 | DT | BVA, EG | High | 4 |
| BR-20 | DT | — | Medium | 2 |
| BR-21 | EP | BVA, EG | Medium | 3 |

> Một TC có thể gắn nhiều BR → tổng cột “Số TC” > tổng TC thực tế.
### 4. Môi trường & dữ liệu test
- Môi trường: UAT Portal SHBond, Chrome bản mới nhất; có quyền query DB (read) để đối chiếu.
- **PC-01 (Precondition chung):** Đăng nhập Portal bằng ACC_MK; dữ liệu master theo bảng dưới đã khai báo; TK 069P000001 có AU1, AU0.
- **SOP-01 – Mở PL05 từ yêu cầu:** 1. Vào [GĐ] Giao dịch > Yêu cầu đặt mua 2. Lọc theo Số TKCK 3. Click “Xem chi tiết” yêu cầu 4. Click mở “Phụ lục 05”.
- **SOP-02 – Tạo yêu cầu đặt mua:** 1. Vào [GĐ] Giao dịch > Đặt mua 2. Nhập Số TKCK, Mã TP, Khối lượng 100 3. Click gửi/xác nhận → yêu cầu “Chờ duyệt”.
- **SOP-03 – Mở PL05 từ hợp đồng:** 1. Vào [GĐ] Hợp đồng 2. Lọc theo Số TKCK 3. Click “Xem chi tiết” 4. Mở “Phụ lục 05”.

| Mã | Mô tả | Thuộc tính đặc thù |
|---|---|---|
| ACC_MK | User Portal có quyền tạo yêu cầu đặt mua [GĐ Q-16] | qa_maker_gd |
| ACC_VW | User Portal chỉ có quyền xem [GĐ Q-16] | qa_viewall |
| ACC_NO | User Portal không có quyền màn Yêu cầu đặt mua | qa_noaccess |
| KH_01 | 069C000101 · CN · Nguyễn Văn Hùng · 15/03/1985 · Nam · CCCD 001085012345 · cấp 10/05/2021 · “Cục Cảnh sát QLHC về TTXH” · “Số 12 ngõ 34 Láng Hạ, Đống Đa, Hà Nội” · 0912345678 | PRO_TYPE = 2: “Chứng chỉ hành nghề môi giới chứng khoán”, số 001234/MGCK, cấp 01/07/2020, hiệu lực 01/07/2030 |
| KH_02 | 069C000102 · CN · Lê Thị Mai · Nữ | PRO_TYPE = 3: INVEST_AMOUNT = 2500000000 |
| KH_03 | 069C000103 · CN · Phạm Quốc Bảo | PRO_TYPE = 4: YEAR = 2025, INVEST_AMOUNT = 1200000000 |
| KH_04 | 069C000104 · CN · Hoàng Minh Tuấn | PRO_TYPE = 1 |
| KH_05 | 069C000105 · CN · Đỗ Thị Hạnh · CUST_MOBILE = NULL | Không có bản ghi CUSTOMER_PRO_INFO |
| KH_06 | 069C000201 · Tổ chức (CUSTOMER_TYPE = 2) · Công ty CP Đầu tư XYZ | — |
| KH_07 | 069C000107 · CN nước ngoài · John Smith · Hộ chiếu C1234567 | PRO_TYPE = 2 |
| SHS | 069P000001 · CUST_ADDRESS = “Số 41 Ngô Quyền, Hoàn Kiếm, Hà Nội” | AU1 (IS_DEFAULT = 1): Nguyễn Văn An · Giám đốc Khối Đầu tư · UQ 01/2026/UQ-SHS · 02/01/2026 · CCCD 001080000001<br>AU0 (IS_DEFAULT = 0): Trần Thị Bình · Trưởng phòng DVKH · UQ 02/2026/UQ-SHS · 05/01/2026 · CCCD 001085000002 |
| TCPH_A | Công ty Cổ phần Tập đoàn ABC · REGISTRATION_ID 0101234567 | — |
| TP_01 | ABC12601 · TCPH_A · Ngày PH 05/06/2026 · BUSINESS_TYPE “Bất động sản” · TERM 36 tháng · RATE_DESCRIPTION “Cố định 10%/năm 4 kỳ đầu; sau đó LS tham chiếu + 3,5%/năm” · REPRESENTATIVE “Công ty CP Chứng khoán DEF” | HAS_COLLATERAL = 1; TSĐB: 5.000.000.000 (GDBĐ-001/2026), 3.000.000.000 (GDBĐ-002/2026); Bảo lãnh: “Ngân hàng TMCP Kiểm Thử”; 3 kỳ TC: 30/09/2026, 30/06/2026, 31/12/2025; phụ lục TP đủ 10 mục, mục Rủi ro 3 dòng |
| TP_02 | ABC12202 · Ngày PH 15/09/2022 | Dữ liệu khác như TP_01 |
| TP_03 | ABC12203 · Ngày PH 16/09/2022 | Dữ liệu khác như TP_01 |
| TP_04 | ABC12604 · Ngày PH 04/06/2026 | Dữ liệu khác như TP_01 |
| TP_06 | ABC12606 · HAS_COLLATERAL = 0 · không có bảo lãnh · 1 kỳ TC (30/09/2026) · REPRESENTATIVE = NULL | — |
| TP_07 | ABC12607 · 4 kỳ TC nhập theo thứ tự 30/06/2026, 31/12/2024, 30/09/2026, 31/12/2025 · HAS_COLLATERAL = 1, 1 TSĐB 7.000.000.000 (GDBĐ-010/2026) | Cùng TCPH_A với TP_01 |
| TP_08 | ABC12608 · 0 kỳ TC · chưa khai báo phụ lục TP · RELEASE_DATE = NULL [nếu DB cho phép] · HAS_COLLATERAL = 1 nhưng bảng TSĐB rỗng | — |

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
| TC | NDT_001, NDT_010 | NDT_002, NDT_005, NDT_011 | NDT_003, NDT_007, NDT_012 | NDT_004, NDT_008, NDT_009, NDT_013 | NDT_014 |

### Decision table – Người đại diện SHS (BR-13, BR-14)
| Điều kiện | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Bản ghi IS_DEFAULT = 1 của 069P | 1 | 1 (đã hoán đổi) | ≥ 2 | 1 | 1 |
| Bản ghi IS_DEFAULT = 0 của 069P | 1 | 1 (đã hoán đổi) | 1 | 0 | 1 |
| KH đặt mua có người UQ riêng | N | N | N | N | Y |
| Kết quả | Mục 2/khối 1 = AU1; khối 2 = AU0 | Đổi vị trí tương ứng | 1 người [GĐ Q-06] | Khối 2 trống | Không dùng người UQ của KH |
| TC | DD_003, DD_004, DD_006, DD_007 | DD_009 | DD_005 | DD_008 | DD_010 |

### Decision table – TSBĐ & Bảo lãnh (BR-19, BR-20)
| Điều kiện | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| HAS_COLLATERAL | 1 | 1 | 0 | — |
| Số TSĐB | ≥ 1 | 0 | — | — |
| Có bảo lãnh | — | — | — | Y / N |
| Kết quả | Câu “có”, Giá trị = tổng, đủ Số ký hiệu | Giá trị, Số ký hiệu trống [GĐ] | Câu “không có” [GĐ] | Bảo lãnh theo có/không |
| TC | TS_001, TS_004 | TS_003 | TS_002 | BL_001, BL_002 |

### Use case
- **Luồng chính:** tạo yêu cầu (CN) → Chờ duyệt → gen PL05 → xem tại chi tiết yêu cầu → duyệt → xem tại chi tiết hợp đồng (GEN_001, GEN_002, VIEW_001, VIEW_002).
- **Luồng phụ:** KH tổ chức → không gen (GEN_003, VIEW_003).
- **Ngoại lệ:** hủy trước khi gửi, double-click, dữ liệu thay đổi sau gen, user không có quyền (GEN_004, GEN_005, GEN_007, VIEW_004).

### Error guessing áp dụng (file 03)
- Text: Unicode, dài, NULL, ký tự HTML · Số: định dạng nghìn, âm, đơn vị · Ngày: biên ngày, múi giờ, 1 chữ số · Nút: double-click · Phân quyền: truy cập URL trực tiếp · Dữ liệu: lẫn giữa các lệnh/TP/KH, nhiều bản ghi mặc định.

---
## B4 – TEST CASE

Tổng **82 TC**, viết rút gọn: Precondition chung PC-01, thao tác SOP-01/02/03 xem B2 mục 4. Các trường cùng một khối/câu được gộp vào 1 TC.


### Bố cục PL05

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_UI_001 | BR-05 | Bố cục PL05 | Kiểm tra nội dung tĩnh của PL05 so với mẫu | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem toàn văn bản | KH_01 × TP_01 | - Có quốc hiệu, tiêu ngữ, dòng “Kính gửi” SHS<br>- Đủ 4 phương án tư cách NĐT, mỗi phương án có 1 ô chọn<br>- Mục 2, 3.1, 3.2, 3.3 và đoạn cam kết đúng nguyên văn mẫu<br>- Đủ 3 khối ký: Nhà đầu tư, Xác nhận cung cấp thông tin, Xác nhận tư cách NĐT | Low | UI | EG | Q-20 |
| TC_PL05_UI_002 | BR-05 | Bố cục PL05 | Kiểm tra PL05 không còn placeholder khi đã gen | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem toàn văn bản | KH_01 × TP_01 | - Không còn placeholder dạng “[…]”<br>- Không còn số thứ tự mapping dính sau giá trị<br>- Không hiển thị “null”, “undefined” | Medium | Negative | EG |  |

### Gen PL05

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_GEN_001 | BR-01 | Gen PL05 | Kiểm tra sinh PL05 khi KH cá nhân tạo yêu cầu đặt mua | PC-01 | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở chi tiết yêu cầu (SOP-01) | 069C000101 · ABC12601 · KL 100 | - Yêu cầu ở trạng thái Chờ duyệt<br>- Màn chi tiết yêu cầu có PL05<br>- PL05 mở được, đúng KH và mã TP của yêu cầu | High | Positive | UC | Q-17 |
| TC_PL05_GEN_002 | BR-02 | Gen PL05 | Kiểm tra sinh PL05 khi KH cá nhân có PRO_TYPE khác nhau | PC-01 | 1. Tạo yêu cầu KH_02, KH_03, KH_04 × TP_01<br>2. Mở chi tiết từng yêu cầu | PRO_TYPE 3, 4, 1 | - Mỗi yêu cầu đều có PL05<br>- Việc sinh PL05 không phụ thuộc PRO_TYPE | Medium | Positive | EP |  |
| TC_PL05_GEN_003 | BR-02 | Gen PL05 | Kiểm tra không sinh PL05 khi KH tổ chức tạo yêu cầu | PC-01 | 1. Tạo yêu cầu KH_06 × TP_01 (SOP-02)<br>2. Mở chi tiết yêu cầu | 069C000201 (tổ chức) | - Yêu cầu vẫn tạo được, trạng thái Chờ duyệt<br>- Không sinh PL05 cho yêu cầu này | High | Negative | EP | Q-15 |
| TC_PL05_GEN_004 | BR-01 | Gen PL05 | Kiểm tra không sinh PL05 khi hủy trước khi gửi yêu cầu | PC-01 | 1. Nhập yêu cầu KH_01 × TP_01<br>2. Click Hủy trước khi gửi | KH_01 × TP_01 | - Không tạo yêu cầu mới<br>- Không sinh PL05 | Medium | Negative | EG |  |
| TC_PL05_GEN_005 | BR-01 | Gen PL05 | Kiểm tra chỉ sinh 1 PL05 khi double-click nút gửi | PC-01 | 1. Nhập yêu cầu KH_01 × TP_01<br>2. Double-click nút gửi | KH_01 × TP_01 | - Chỉ tạo 1 yêu cầu<br>- Chỉ sinh 1 PL05 | Medium | Negative | EG |  |
| TC_PL05_GEN_006 | BR-01, BR-11 | Gen PL05 | Kiểm tra PL05 không lẫn dữ liệu khi KH có 2 yêu cầu | PC-01 | 1. Tạo yêu cầu KH_01 × TP_01 và KH_01 × TP_06<br>2. Mở 2 PL05 | ABC12601, ABC12606 | - Mỗi yêu cầu có PL05 riêng<br>- Mỗi PL05 đúng mã TP, TSBĐ của yêu cầu đó<br>- Không lẫn dữ liệu giữa 2 PL05 | High | Negative | EG |  |
| TC_PL05_GEN_007 | BR-01 | Gen PL05 | Kiểm tra PL05 không đổi khi sửa dữ liệu KH sau khi gen | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Sửa địa chỉ 069C000101<br>3. Mở lại PL05 | Địa chỉ mới: “Số 99 Trần Duy Hưng, Hà Nội” | - [GĐ Q-07] PL05 đã sinh giữ nguyên địa chỉ cũ<br>- Không tự cập nhật theo dữ liệu KH mới | Medium | Negative | EG | Q-07 |

### Hiển thị PL05

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_VIEW_001 | BR-03 | Hiển thị PL05 | Kiểm tra hiển thị PL05 tại màn chi tiết yêu cầu đặt mua | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Đăng nhập ACC_VW<br>2. Mở chi tiết yêu cầu (SOP-01) | KH_01 × TP_01 | - Màn chi tiết yêu cầu có mục “Phụ lục 05”<br>- Mở được PL05<br>- Đúng KH và mã TP của yêu cầu | High | Positive | UC | Q-16 |
| TC_PL05_VIEW_002 | BR-04 | Hiển thị PL05 | Kiểm tra hiển thị PL05 tại màn chi tiết hợp đồng | Yêu cầu KH_01 × TP_01 đã duyệt thành hợp đồng | 1. Đăng nhập ACC_VW<br>2. Mở chi tiết hợp đồng (SOP-03) | KH_01 × TP_01 | - Màn chi tiết hợp đồng có mục “Phụ lục 05”<br>- Mở được PL05<br>- Nội dung giống PL05 tại màn chi tiết yêu cầu | High | Positive | UC | Q-16 |
| TC_PL05_VIEW_003 | BR-02, BR-04 | Hiển thị PL05 | Kiểm tra màn chi tiết hợp đồng không có PL05 khi KH tổ chức | Hợp đồng KH_06 × TP_01 đã hình thành | 1. Mở chi tiết hợp đồng (SOP-03) | KH_06 | - [GĐ Q-15] Không có mục PL05 tại màn chi tiết hợp đồng | Medium | Negative | EP | Q-15 |
| TC_PL05_VIEW_004 | BR-03 | Hiển thị PL05 | Kiểm tra chặn xem PL05 khi user không có quyền | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Đăng nhập ACC_NO<br>2. Dán URL chi tiết yêu cầu / PL05 | ACC_NO | - Không thấy menu Yêu cầu đặt mua<br>- Truy cập URL trực tiếp bị chặn<br>- Không tải được PL05 | Medium | Permission | EG | Q-16 |

### PL05 – Thông tin KH

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_KH_001 | BR-06 | PL05 – Thông tin KH | Kiểm tra Tên khách hàng | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem dòng “Tên tôi là” và mục 3.1 i) | KH_01 | - Tên KH tại dòng “Tên tôi là” lấy từ CUSTOMER_NAME<br>- Tên KH tại mục 3.1 i) giống dòng “Tên tôi là” | High | Positive | EP |  |
| TC_PL05_KH_002 | BR-06 | PL05 – Thông tin KH | Kiểm tra Ngày sinh | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Ngày tháng năm sinh | KH_01 | - Ngày sinh lấy từ BIRTH_DAY<br>- Định dạng dd/mm/yyyy [GĐ Q-13] | Medium | Positive | EP | Q-13 |
| TC_PL05_KH_003 | BR-06 | PL05 – Thông tin KH | Kiểm tra Giới tính khi KH Nam và Nữ | Yêu cầu Chờ duyệt: KH_01, KH_02 | 1. Mở PL05 của KH_01 và KH_02<br>2. Xem Giới tính | KH_01 Nam · KH_02 Nữ | - Giới tính lấy từ GENDER<br>- Hiển thị dạng chữ (Nam/Nữ), không hiển thị mã [GĐ Q-12] | Medium | Positive | EP | Q-12 |
| TC_PL05_KH_004 | BR-06 | PL05 – Thông tin KH | Kiểm tra Số CCCD khi có số 0 đầu | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Số CCCD/Hộ chiếu | CARD_ID = 001085012345 | - Số CCCD lấy từ CARD_ID<br>- Giữ số 0 đầu, đủ 12 số | High | Boundary | EG |  |
| TC_PL05_KH_005 | BR-06 | PL05 – Thông tin KH | Kiểm tra Số hộ chiếu khi KH nước ngoài | Yêu cầu Chờ duyệt: KH_07 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Số CCCD/Hộ chiếu | CARD_ID = C1234567 | - Số hộ chiếu lấy từ CARD_ID<br>- Giữ nguyên chữ và số | Medium | Positive | EP |  |
| TC_PL05_KH_006 | BR-06 | PL05 – Thông tin KH | Kiểm tra Ngày cấp giấy tờ | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Ngày cấp | KH_01 | - Ngày cấp lấy từ ISSUE_DATE<br>- Định dạng dd/mm/yyyy [GĐ Q-13] | Medium | Positive | EP |  |
| TC_PL05_KH_007 | BR-06 | PL05 – Thông tin KH | Kiểm tra Nơi cấp giấy tờ | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Nơi cấp | KH_01 | - Nơi cấp lấy từ ISSUE_PLACE<br>- Hiển thị đủ, đúng chính tả | Medium | Positive | EP |  |
| TC_PL05_KH_008 | BR-06 | PL05 – Thông tin KH | Kiểm tra Nơi ở hiện tại | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Nơi ở hiện tại | KH_01 | - Nơi ở hiện tại lấy từ CUST_ADDRESS | Medium | Positive | EP |  |
| TC_PL05_KH_009 | BR-06 | PL05 – Thông tin KH | Kiểm tra Nơi ở hiện tại khi dài 200 ký tự | PC-01; CUST_ADDRESS của 069C000101 dài 200 ký tự | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem Nơi ở hiện tại | Chuỗi 200 ký tự có dấu | - Hiển thị đủ địa chỉ, không bị cắt<br>- Tự xuống dòng, không tràn lề | Low | Boundary | BVA |  |
| TC_PL05_KH_010 | BR-06 | PL05 – Thông tin KH | Kiểm tra Điện thoại liên hệ | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Điện thoại liên hệ | KH_01 | - Điện thoại lấy từ CUST_MOBILE<br>- Giữ số 0 đầu | Medium | Positive | EP |  |
| TC_PL05_KH_011 | BR-06 | PL05 – Thông tin KH | Kiểm tra Điện thoại liên hệ khi để trống | Yêu cầu Chờ duyệt: KH_05 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Điện thoại liên hệ | CUST_MOBILE = NULL | - Điện thoại để trống, không hiển thị “null”<br>- PL05 vẫn sinh, các trường khác hiển thị đủ | Medium | Negative | EG |  |

### PL05 – Tư cách NĐTCN

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_NDT_001 | BR-05 | PL05 – Tư cách NĐTCN | Kiểm tra ô tick khi PRO_TYPE = 1 | Yêu cầu Chờ duyệt: KH_04 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem 4 ô tư cách NĐT | KH_04 | - [GĐ Q-01] Chỉ tick ô “NĐT chiến lược”<br>- 3 ô còn lại không tick<br>- [GĐ Q-02] Số và ngày Nghị quyết giữ “…” | High | Negative | DT | Q-01, Q-02 |
| TC_PL05_NDT_002 | BR-05, BR-08 | PL05 – Tư cách NĐTCN | Kiểm tra ô tick khi PRO_TYPE = 2 | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem 4 ô tư cách NĐT | KH_01 | - [GĐ Q-01] Chỉ tick ô “chứng chỉ hành nghề”<br>- 3 ô còn lại không tick | High | Positive | DT | Q-01 |
| TC_PL05_NDT_003 | BR-05, BR-09 | PL05 – Tư cách NĐTCN | Kiểm tra ô tick khi PRO_TYPE = 3 | Yêu cầu Chờ duyệt: KH_02 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem 4 ô tư cách NĐT | KH_02 | - [GĐ Q-01] Chỉ tick ô “danh mục CKNY”<br>- Ô “chứng chỉ hành nghề” không bị tick cứng | High | Negative | DT | Q-01 |
| TC_PL05_NDT_004 | BR-05, BR-10 | PL05 – Tư cách NĐTCN | Kiểm tra ô tick khi PRO_TYPE = 4 | Yêu cầu Chờ duyệt: KH_03 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem 4 ô tư cách NĐT | KH_03 | - [GĐ Q-01] Chỉ tick ô “thu nhập chịu thuế”<br>- 3 ô còn lại không tick | High | Negative | DT | Q-01 |
| TC_PL05_NDT_005 | BR-08 | PL05 – Tư cách NĐTCN | Kiểm tra thông tin chứng chỉ khi PRO_TYPE = 2 | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem Loại, Số, Ngày cấp, Ngày hiệu lực chứng chỉ | KH_01 | - Loại chứng chỉ lấy từ CERTIFICATE_TYPE<br>- Số chứng chỉ lấy từ CERTIFICATE_NO<br>- Ngày cấp lấy từ CERTIFICATE_FROM<br>- Ngày hiệu lực lấy từ CERTIFICATE_TO<br>- Ngày định dạng dd/mm/yyyy | High | Positive | DT |  |
| TC_PL05_NDT_006 | BR-08 | PL05 – Tư cách NĐTCN | Kiểm tra Ngày hiệu lực chứng chỉ khi đã hết hạn | PC-01; CERTIFICATE_TO của 069C000101 = 18/11/2026; tạo yêu cầu ngày 19/11/2026 | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem Ngày hiệu lực chứng chỉ | CERTIFICATE_TO = 18/11/2026 | - [GĐ Q-19] Ngày hiệu lực hiển thị đúng giá trị đã hết hạn<br>- PL05 vẫn sinh, không tự sửa dữ liệu | Medium | Boundary | BVA | Q-19 |
| TC_PL05_NDT_007 | BR-09 | PL05 – Tư cách NĐTCN | Kiểm tra Giá trị danh mục CKNY khi PRO_TYPE = 3 | Yêu cầu Chờ duyệt: KH_02 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem dòng danh mục CKNY | INVEST_AMOUNT = 2500000000 | - Giá trị danh mục CKNY lấy từ INVEST_AMOUNT<br>- [GĐ Q-03] Hiển thị đơn vị đồng, có phân cách hàng nghìn | High | Positive | DT | Q-03 |
| TC_PL05_NDT_008 | BR-10 | PL05 – Tư cách NĐTCN | Kiểm tra Năm và Giá trị thu nhập chịu thuế khi PRO_TYPE = 4 | Yêu cầu Chờ duyệt: KH_03 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem dòng thu nhập chịu thuế | YEAR = 2025 · INVEST_AMOUNT = 1200000000 | - Năm lấy từ YEAR<br>- Giá trị thu nhập chịu thuế lấy từ INVEST_AMOUNT<br>- [GĐ Q-03] Giá trị hiển thị đơn vị tỷ đồng | High | Positive | DT | Q-03 |
| TC_PL05_NDT_009 | BR-10 | PL05 – Tư cách NĐTCN | Kiểm tra Năm thu nhập chịu thuế khi không phải năm liền trước | PC-01; YEAR của 069C000103 = 2023; tạo yêu cầu năm 2026 | 1. Tạo yêu cầu KH_03 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem dòng thu nhập chịu thuế | YEAR = 2023 | - [GĐ Q-04] Hiển thị đúng năm đã khai báo<br>- Không tự đổi thành năm liền trước | Medium | Boundary | BVA | Q-04 |
| TC_PL05_NDT_010 | BR-08, BR-09, BR-10 | PL05 – Tư cách NĐTCN | Kiểm tra thông tin NĐT để trống khi PRO_TYPE = 1 | Yêu cầu Chờ duyệt: KH_04 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 1 | KH_04 | - Thông tin chứng chỉ trống<br>- Giá trị danh mục CKNY trống<br>- Thông tin thu nhập chịu thuế trống | High | Negative | DT |  |
| TC_PL05_NDT_011 | BR-09, BR-10 | PL05 – Tư cách NĐTCN | Kiểm tra CKNY và thu nhập chịu thuế để trống khi PRO_TYPE = 2 | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 1 | KH_01 | - Giá trị danh mục CKNY trống<br>- Năm, Giá trị thu nhập chịu thuế trống<br>- Không hiển thị 0 hoặc “null” | High | Negative | DT |  |
| TC_PL05_NDT_012 | BR-08, BR-10 | PL05 – Tư cách NĐTCN | Kiểm tra chứng chỉ và thu nhập chịu thuế để trống khi PRO_TYPE = 3 | Yêu cầu Chờ duyệt: KH_02 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 1 | KH_02 | - Thông tin chứng chỉ trống<br>- Năm, Giá trị thu nhập chịu thuế trống | High | Negative | DT |  |
| TC_PL05_NDT_013 | BR-08, BR-09 | PL05 – Tư cách NĐTCN | Kiểm tra chứng chỉ và CKNY để trống khi PRO_TYPE = 4 | Yêu cầu Chờ duyệt: KH_03 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 1 | KH_03 | - Thông tin chứng chỉ trống<br>- Giá trị danh mục CKNY trống | High | Negative | DT |  |
| TC_PL05_NDT_014 | BR-08, BR-09, BR-10 | PL05 – Tư cách NĐTCN | Kiểm tra thông tin NĐT khi KH không có bản ghi NĐTCN | Yêu cầu Chờ duyệt: KH_05 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 1 | KH_05 | - [GĐ Q-19] PL05 vẫn sinh<br>- Toàn bộ thông tin tư cách NĐT trống<br>- Không ô nào được tick | Medium | Negative | EG | Q-19 |

### PL05 – TCPH & Trái phiếu

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_TP_001 | BR-07 | PL05 – TCPH & Trái phiếu | Kiểm tra Tổ chức phát hành | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem dòng NĐT chiến lược và mục 3.1 a) | TP_01 | - Tổ chức phát hành lấy từ ISSUER_NAME của mã TP<br>- Giống nhau ở dòng NĐT chiến lược và mục 3.1 a) | High | Positive | EP |  |
| TC_PL05_TP_002 | BR-11 | PL05 – TCPH & Trái phiếu | Kiểm tra Mã trái phiếu | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2, 3.1 i), 3.2, 3.3 | TP_01 | - Mã TP lấy từ REPO_ORDER.SYMBOL<br>- Giống nhau ở mọi vị trí | High | Positive | EP |  |
| TC_PL05_TP_003 | BR-17 | PL05 – TCPH & Trái phiếu | Kiểm tra Mã số đăng ký doanh nghiệp | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 a) | TCPH_A | - Mã số đăng ký doanh nghiệp lấy từ REGISTRATION_ID | Medium | Positive | EP | Q-20 |
| TC_PL05_TP_004 | BR-17 | PL05 – TCPH & Trái phiếu | Kiểm tra Loại ngành nghề | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 a) | TP_01 | - Loại ngành nghề lấy từ BUSINESS_TYPE | Medium | Positive | EP |  |
| TC_PL05_TP_005 | BR-17 | PL05 – TCPH & Trái phiếu | Kiểm tra Lãi suất trái phiếu | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 g) | TP_01 | - Lãi suất lấy từ RATE_DESCRIPTION<br>- Hiển thị đủ nội dung, không bị cắt | Medium | Positive | EP |  |
| TC_PL05_TP_006 | BR-17 | PL05 – TCPH & Trái phiếu | Kiểm tra Kỳ hạn trái phiếu | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 g) | TP_01 | - Kỳ hạn lấy từ BOND_INFO.TERM | Medium | Positive | EP | Q-20 |
| TC_PL05_TP_007 | BR-17 | PL05 – TCPH & Trái phiếu | Kiểm tra Đại diện người sở hữu trái phiếu | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 k) | TP_01 | - Đại diện người sở hữu TP lấy từ REPRESENTATIVE<br>- Giữ chữ “(nếu có)” | Medium | Positive | EP |  |
| TC_PL05_TP_008 | BR-17 | PL05 – TCPH & Trái phiếu | Kiểm tra Đại diện người sở hữu trái phiếu khi để trống | Yêu cầu Chờ duyệt: KH_01 × TP_06 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 k) | REPRESENTATIVE = NULL | - Đại diện người sở hữu TP để trống, không “null”<br>- Giữ chữ “(nếu có)” | Low | Negative | EG |  |

### PL05 – Đại diện SHS

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_DD_001 | BR-12 | PL05 – Đại diện SHS | Kiểm tra Địa chỉ SHS | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 và 2 khối xác nhận | SHS | - Địa chỉ SHS lấy từ CUST_ADDRESS của KH 069P000001<br>- Giống nhau ở mục 2 và 2 khối xác nhận | High | Positive | EP |  |
| TC_PL05_DD_002 | BR-12 | PL05 – Đại diện SHS | Kiểm tra Địa chỉ SHS khi 069P000001 đổi địa chỉ | PC-01; CUST_ADDRESS của 069P000001 = “Số 1 Kiểm Thử, Hà Nội” | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem Địa chỉ SHS | Địa chỉ mới | - PL05 mới hiển thị địa chỉ mới của 069P000001<br>- Không dùng địa chỉ cố định | Medium | Positive | EG | Khôi phục dữ liệu sau test |
| TC_PL05_DD_003 | BR-13 | PL05 – Đại diện SHS | Kiểm tra Người đại diện khối cung cấp thông tin | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 và khối xác nhận cung cấp thông tin | AU1 | - Người đại diện lấy từ CUSTOMER_AUTHORIZER của TK 069P, IS_DEFAULT = 1<br>- Giống nhau ở mục 2 và khối xác nhận cung cấp thông tin | High | Positive | DT |  |
| TC_PL05_DD_004 | BR-13 | PL05 – Đại diện SHS | Kiểm tra Chức vụ, Ủy quyền, CCCD khối cung cấp thông tin | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 và khối xác nhận cung cấp thông tin | AU1 | - Chức vụ, Số/Ngày ủy quyền, CCCD lấy cùng bản ghi IS_DEFAULT = 1 [GĐ Q-05]<br>- Ngày ủy quyền định dạng dd/mm/yyyy | Medium | Positive | DT | Q-05 |
| TC_PL05_DD_005 | BR-13 | PL05 – Đại diện SHS | Kiểm tra khối cung cấp thông tin khi có 2 bản ghi IS_DEFAULT = 1 | PC-01; thêm AU2 Lê Văn Cường, IS_DEFAULT = 1 | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem khối xác nhận cung cấp thông tin | AU1, AU2 | - [GĐ Q-06] PL05 vẫn sinh<br>- Chỉ hiển thị 1 người đại diện, không ghép tên<br>- Chức vụ, ủy quyền, CCCD cùng 1 bản ghi | Medium | Negative | EG | Q-06 |
| TC_PL05_DD_006 | BR-14 | PL05 – Đại diện SHS | Kiểm tra Người đại diện khối xác nhận tư cách NĐT | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem khối xác nhận tư cách NĐT | AU0 | - Người đại diện lấy từ CUSTOMER_AUTHORIZER của TK 069P, IS_DEFAULT = 0 | High | Positive | DT |  |
| TC_PL05_DD_007 | BR-14 | PL05 – Đại diện SHS | Kiểm tra Chức vụ, Ủy quyền, CCCD khối xác nhận tư cách NĐT | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem khối xác nhận tư cách NĐT | AU0 | - Chức vụ, Số/Ngày ủy quyền, CCCD lấy cùng bản ghi IS_DEFAULT = 0 [GĐ Q-05]<br>- Ngày ủy quyền định dạng dd/mm/yyyy | Medium | Positive | DT | Q-05 |
| TC_PL05_DD_008 | BR-14 | PL05 – Đại diện SHS | Kiểm tra khối xác nhận tư cách NĐT khi không có IS_DEFAULT = 0 | PC-01; tạm vô hiệu AU0 | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem khối xác nhận tư cách NĐT | Không có IS_DEFAULT = 0 | - [GĐ Q-06] PL05 vẫn sinh<br>- Khối xác nhận tư cách NĐT để trống<br>- Không lấy người có IS_DEFAULT = 1 | Medium | Negative | EG | Q-06 |
| TC_PL05_DD_009 | BR-13, BR-14 | PL05 – Đại diện SHS | Kiểm tra 2 khối xác nhận khi hoán đổi IS_DEFAULT | PC-01; AU1 → IS_DEFAULT = 0, AU0 → IS_DEFAULT = 1 | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem 2 khối xác nhận | IS_DEFAULT hoán đổi | - Khối cung cấp thông tin hiển thị người có IS_DEFAULT = 1 mới<br>- Khối tư cách NĐT hiển thị người có IS_DEFAULT = 0 mới | Medium | Negative | DT | Khôi phục dữ liệu sau test |
| TC_PL05_DD_010 | BR-13, BR-14 | PL05 – Đại diện SHS | Kiểm tra không lấy người ủy quyền của KH đặt mua | PC-01; 069C000101 có người ủy quyền Phạm Văn Dũng | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem 2 khối xác nhận | Phạm Văn Dũng | - Không hiển thị người ủy quyền của KH đặt mua<br>- 2 khối xác nhận vẫn lấy người ủy quyền của TK 069P | High | Negative | EG |  |

### PL05 – Phụ lục TP

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_PL_001 | BR-15 | PL05 – Phụ lục TP | Kiểm tra 10 nội dung phụ lục trái phiếu | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 2 a)–d), 3.1 c), d), đ), h), 3.2 | Phụ lục ABC12601: mỗi mục khai báo “PLTP-<tên mục>” | - Mỗi mục hiển thị đúng nội dung khai báo trong phụ lục của mã TP:<br>  2 a) Bản CBTT trước chào bán · 2 b) Phương án phát hành · 2 c) Hồ sơ chào bán · 2 d) CBTT định kỳ<br>  3.1 c) Mục đích phát hành · 3.1 d) Kế hoạch sử dụng vốn và vốn tạm thời nhàn rỗi<br>  3.1 đ) Phương án bố trí nguồn · 3.1 h) Cam kết của TCPH · 3.2 Rủi ro | High | Positive | EP | Q-18 |
| TC_PL05_PL_002 | BR-15 | PL05 – Phụ lục TP | Kiểm tra nội dung phụ lục có nhiều dòng | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.2 Rủi ro | Rủi ro: 3 dòng STT 1–3 | - Hiển thị đủ các dòng<br>- [GĐ Q-18] Đúng thứ tự STT khai báo | Medium | Positive | EP | Q-18 |
| TC_PL05_PL_003 | BR-15 | PL05 – Phụ lục TP | Kiểm tra không lấy phụ lục của trái phiếu khác | Yêu cầu Chờ duyệt: KH_01 × TP_07 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 c) | Mục đích ABC12607 = “Mục đích TP07” | - Hiển thị nội dung phụ lục của đúng mã TP trong yêu cầu<br>- Không lấy phụ lục của TP khác cùng TCPH | Medium | Negative | EG |  |
| TC_PL05_PL_004 | BR-15 | PL05 – Phụ lục TP | Kiểm tra phụ lục khi trái phiếu chưa khai báo | Yêu cầu Chờ duyệt: KH_01 × TP_08 | 1. Mở PL05 (SOP-01)<br>2. Xem 10 nội dung phụ lục | TP_08 | - [GĐ Q-18] PL05 vẫn sinh<br>- 10 mục phụ lục để trống, không “null” | Medium | Negative | EG | Q-18 |
| TC_PL05_PL_005 | BR-15 | PL05 – Phụ lục TP | Kiểm tra nội dung phụ lục chứa thẻ HTML | PC-01; Mục đích ABC12601 = “<b>Đầu tư</b><script>alert(1)</script>” | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem mục 3.1 c) | Chuỗi có HTML | - Hiển thị nguyên chuỗi dạng text<br>- Không chạy script, không in đậm<br>- Không vỡ bố cục PL05 | Low | Security | EG |  |

### PL05 – Căn cứ Nghị định

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_ND_001 | BR-16 | PL05 – Căn cứ Nghị định | Kiểm tra căn cứ Nghị định khi ngày phát hành 15/09/2022 | Yêu cầu Chờ duyệt: KH_01 × TP_02 | 1. Mở PL05 (SOP-01)<br>2. Xem 5 vị trí căn cứ (mục 2 a)–d), 3.1 i)) | PH 15/09/2022 · tạo yêu cầu 2026 | - 5 vị trí căn cứ hiển thị “Nghị định số 153/2020/NĐ-CP”<br>- Căn cứ theo ngày phát hành, không theo ngày tạo yêu cầu | High | Boundary | BVA |  |
| TC_PL05_ND_002 | BR-16 | PL05 – Căn cứ Nghị định | Kiểm tra căn cứ Nghị định khi ngày phát hành 16/09/2022 | Yêu cầu Chờ duyệt: KH_01 × TP_03 | 1. Mở PL05 (SOP-01)<br>2. Xem 5 vị trí căn cứ (mục 2 a)–d), 3.1 i)) | PH 16/09/2022 | - 5 vị trí căn cứ hiển thị “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP” | High | Boundary | BVA |  |
| TC_PL05_ND_003 | BR-16 | PL05 – Căn cứ Nghị định | Kiểm tra căn cứ Nghị định khi ngày phát hành 04/06/2026 | Yêu cầu Chờ duyệt: KH_01 × TP_04 | 1. Mở PL05 (SOP-01)<br>2. Xem 5 vị trí căn cứ (mục 2 a)–d), 3.1 i)) | PH 04/06/2026 | - 5 vị trí căn cứ hiển thị “Nghị định số 153/2020/NĐ-CP và Nghị định 65/2022/NĐ-CP” | High | Boundary | BVA |  |
| TC_PL05_ND_004 | BR-16 | PL05 – Căn cứ Nghị định | Kiểm tra căn cứ Nghị định khi ngày phát hành 05/06/2026 | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem 5 vị trí căn cứ (mục 2 a)–d), 3.1 i)) | PH 05/06/2026 | - Mục 2 a): “khoản 1 Điều 29 Nghị định số 200/2026/NĐ-CP”<br>- Mục 2 b): “Điều 10 Nghị định số 200/2026/NĐ-CP”<br>- Mục 2 c), 2 d): “Nghị định số 200/2026/NĐ-CP”<br>- Mục 3.1 i): “(điều khoản) Nghị định số 200/2026/NĐ-CP” [GĐ Q-14] | High | Boundary | BVA | Q-14 |
| TC_PL05_ND_005 | BR-16 | PL05 – Căn cứ Nghị định | Kiểm tra căn cứ Nghị định khi chưa có ngày phát hành | Yêu cầu Chờ duyệt: KH_01 × TP_08 | 1. Mở PL05 (SOP-01)<br>2. Xem 5 vị trí căn cứ (mục 2 a)–d), 3.1 i)) | RELEASE_DATE = NULL | - [GĐ Q-14] PL05 vẫn sinh<br>- 5 vị trí căn cứ để trống, không mặc định theo khoảng nào | Medium | Negative | EG | Q-14 |

### PL05 – Bảng tài chính

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_FN_001 | BR-18 | PL05 – Bảng tài chính | Kiểm tra bảng tài chính khi có 1 kỳ | Yêu cầu Chờ duyệt: KH_01 × TP_06 | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_06: 30/09/2026 | - Bảng chỉ có 1 cột thời điểm<br>- Không có cột trống cho kỳ 2, 3 | High | Boundary | BVA |  |
| TC_PL05_FN_002 | BR-18 | PL05 – Bảng tài chính | Kiểm tra bảng tài chính khi có 3 kỳ | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_01: 3 kỳ | - Bảng có 3 cột thời điểm<br>- Sắp xếp từ gần đến xa | High | Boundary | BVA |  |
| TC_PL05_FN_003 | BR-18 | PL05 – Bảng tài chính | Kiểm tra bảng tài chính khi có 4 kỳ | Yêu cầu Chờ duyệt: KH_01 × TP_07 | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_07: 4 kỳ | - Chỉ hiển thị 3 kỳ gần nhất<br>- Không hiển thị kỳ cũ nhất | High | Boundary | BVA |  |
| TC_PL05_FN_004 | BR-18 | PL05 – Bảng tài chính | Kiểm tra bảng tài chính khi chưa có kỳ nào | Yêu cầu Chờ duyệt: KH_01 × TP_08 | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | TP_08: 0 kỳ | - [GĐ Q-11] PL05 vẫn sinh<br>- Bảng chỉ còn cột Chỉ tiêu, Đơn vị tính | Medium | Negative | BVA | Q-11 |
| TC_PL05_FN_005 | BR-18 | PL05 – Bảng tài chính | Kiểm tra thứ tự kỳ khi nhập không theo thời gian | Yêu cầu Chờ duyệt: KH_01 × TP_07 | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | Thứ tự nhập lộn xộn | - Các kỳ sắp xếp theo thời gian từ gần đến xa<br>- Không theo thứ tự nhập | Medium | Negative | EG |  |
| TC_PL05_FN_006 | BR-18 | PL05 – Bảng tài chính | Kiểm tra giá trị 7 chỉ tiêu theo từng kỳ | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | Kỳ 30/09/2026: Tổng TS 1.000.000.000.000; Nợ 600.000.000.000; VCSH 400.000.000.000… | - Đủ 7 chỉ tiêu<br>- Mỗi ô đúng chỉ tiêu, đúng kỳ, không lệch cột<br>- Đơn vị: VND cho 4 chỉ tiêu đầu, Lần cho 3 hệ số | High | Positive | EP |  |
| TC_PL05_FN_007 | BR-18 | PL05 – Bảng tài chính | Kiểm tra định dạng số trong bảng tài chính | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem bảng tài chính mục 3.1 b) | 1000000000000; 0.45 | - [GĐ Q-11] Số tiền có phân cách hàng nghìn<br>- Hệ số giữ phần thập phân, dấu phẩy thập phân | Medium | Positive | EP | Q-11 |

### PL05 – TSBĐ & Bảo lãnh

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_TS_001 | BR-19 | PL05 – TSBĐ & Bảo lãnh | Kiểm tra TSBĐ khi trái phiếu có 2 TSĐB | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | 5.000.000.000 (GDBĐ-001/2026) + 3.000.000.000 (GDBĐ-002/2026) | - Hiển thị câu “có tài sản đảm bảo”<br>- Giá trị TSBĐ = tổng giá trị các TSĐB của mã TP [GĐ Q-09]<br>- Số ký hiệu hiển thị đủ các TSĐB [GĐ Q-09] | High | Positive | DT | Q-09 |
| TC_PL05_TS_002 | BR-19 | PL05 – TSBĐ & Bảo lãnh | Kiểm tra TSBĐ khi trái phiếu không có TSBĐ | Yêu cầu Chờ duyệt: KH_01 × TP_06 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | HAS_COLLATERAL = 0 | - [GĐ Q-08] Hiển thị câu “không có tài sản đảm bảo”<br>- Giá trị và Số ký hiệu TSBĐ để trống | High | Negative | DT | Q-08 |
| TC_PL05_TS_003 | BR-19 | PL05 – TSBĐ & Bảo lãnh | Kiểm tra TSBĐ khi có cờ TSBĐ nhưng chưa khai báo TSĐB | Yêu cầu Chờ duyệt: KH_01 × TP_08 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | HAS_COLLATERAL = 1, bảng TSĐB rỗng | - [GĐ Q-08] PL05 vẫn sinh<br>- Giá trị và Số ký hiệu TSBĐ để trống, không hiển thị “0” | Medium | Negative | EG | Q-08 |
| TC_PL05_TS_004 | BR-19 | PL05 – TSBĐ & Bảo lãnh | Kiểm tra Giá trị TSBĐ không cộng TSĐB của trái phiếu khác | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) | TP_01 (8 tỷ), TP_07 (7 tỷ) cùng TCPH | - Giá trị TSBĐ chỉ cộng TSĐB của mã TP trong yêu cầu<br>- Không cộng TSĐB của TP khác cùng TCPH | Medium | Negative | EG |  |
| TC_PL05_BL_001 | BR-20 | PL05 – TSBĐ & Bảo lãnh | Kiểm tra Bảo lãnh thanh toán khi trái phiếu có bảo lãnh | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) đoạn bảo lãnh | TP_01 | - Hiển thị câu “có bảo lãnh thanh toán của Tổ chức tín dụng”<br>- Tên tổ chức tín dụng lấy từ thông tin bảo lãnh của mã TP [Q-10] | High | Positive | DT | Q-10 |
| TC_PL05_BL_002 | BR-20 | PL05 – TSBĐ & Bảo lãnh | Kiểm tra Bảo lãnh thanh toán khi trái phiếu không có bảo lãnh | Yêu cầu Chờ duyệt: KH_01 × TP_06 | 1. Mở PL05 (SOP-01)<br>2. Xem mục 3.1 e) đoạn bảo lãnh | TP_06 | - [GĐ Q-10] Hiển thị câu “không có bảo lãnh thanh toán”<br>- Tên tổ chức tín dụng để trống | Medium | Negative | DT | Q-10 |

### PL05 – Ngày ký

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_PL05_DT_001 | BR-21 | PL05 – Ngày ký | Kiểm tra Ngày ký | Yêu cầu Chờ duyệt: KH_01 × TP_01 | 1. Mở PL05 (SOP-01)<br>2. Xem dòng “Hà Nội, ngày…” và 2 khối xác nhận | CREATED_DATE = 19/11/2026 10:15 | - Ngày ký lấy từ REPO_ORDER.CREATED_DATE<br>- Hiển thị dạng “ngày … tháng … năm …”<br>- Giống nhau ở 3 vị trí ký | High | Positive | EP | Q-13 |
| TC_PL05_DT_002 | BR-21 | PL05 – Ngày ký | Kiểm tra Ngày ký khi tạo yêu cầu lúc 23:59 | PC-01; giờ UAT = 19/11/2026 23:59:30 (GMT+7) | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem dòng “Hà Nội, ngày…” | 19/11/2026 23:59:30 | - Ngày ký đúng ngày theo giờ Việt Nam<br>- Không lệch sang ngày hôm sau | Medium | Boundary | EG |  |
| TC_PL05_DT_003 | BR-21 | PL05 – Ngày ký | Kiểm tra Ngày ký khi ngày giao dịch khác ngày tạo yêu cầu | PC-01; tạo yêu cầu ngày 19/11/2026, ngày giao dịch 20/11/2026 | 1. Tạo yêu cầu KH_01 × TP_01 (SOP-02)<br>2. Mở PL05 (SOP-01)<br>3. Xem 3 vị trí ngày ký | CREATED_DATE 19/11 · ORDER_DATE 20/11 | - Ngày ký lấy theo ngày tạo yêu cầu<br>- Không lấy ngày giao dịch | Medium | Negative | EG | Áp dụng nếu màn đặt mua cho chọn ngày GD |

---
## B5 – TỰ REVIEW & HOÀN THIỆN

### 1. RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs (bỏ tiền tố TC_PL05_) | Phủ | Ghi chú |
|---|---|---|---|---|---|
| BR-01 | Sinh PL05 sau khi tạo yêu cầu KH đặt mua → trạng thái Chờ duyệt | 5 | GEN_001, GEN_004, GEN_005, GEN_006, GEN_007 | Đủ |  |
| BR-02 | Chỉ sinh PL05 cho KH Cá nhân; KH Tổ chức không sinh | 3 | GEN_002, GEN_003, VIEW_003 | Đủ |  |
| BR-03 | PL05 hiển thị tại màn Xem chi tiết yêu cầu đặt mua | 2 | VIEW_001, VIEW_004 | Đủ |  |
| BR-04 | PL05 hiển thị tại màn Xem chi tiết hợp đồng | 2 | VIEW_002, VIEW_003 | Đủ |  |
| BR-05 | Nội dung tĩnh, bố cục mẫu PL05 (quốc hiệu, kính gửi, 4 phương án tư cá… | 6 | UI_001, UI_002, NDT_001, NDT_002, NDT_003, NDT_004 | Đủ |  |
| BR-06 | Thông tin KH (Tên, Ngày sinh, Giới tính, Số CCCD/Hộ chiếu, Ngày cấp, N… | 11 | KH_001, KH_002, KH_003, KH_004, KH_005, KH_006, KH_007, KH_008, KH_009, KH_010, KH_011 | Đủ |  |
| BR-07 | Tổ chức phát hành = BOND_ISSUER.ISSUER_NAME (2 vị trí) | 1 | TP_001 | Đủ |  |
| BR-08 | Thông tin chứng chỉ (Loại, Số, Ngày cấp, Ngày hiệu lực) lấy CUSTOMER_P… | 7 | NDT_002, NDT_005, NDT_006, NDT_010, NDT_012, NDT_013, NDT_014 | Đủ |  |
| BR-09 | Giá trị danh mục CKNY = CUSTOMER_PRO_INFO.INVEST_AMOUNT khi PRO_TYPE =… | 6 | NDT_003, NDT_007, NDT_010, NDT_011, NDT_013, NDT_014 | Đủ |  |
| BR-10 | Năm thu nhập chịu thuế = CUSTOMER_PRO_INFO.YEAR, Giá trị thu nhập chịu… | 7 | NDT_004, NDT_008, NDT_009, NDT_010, NDT_011, NDT_012, NDT_014 | Đủ |  |
| BR-11 | Mã trái phiếu = REPO_ORDER.SYMBOL (= BOND_INFO.CODE), đồng nhất mọi vị… | 2 | GEN_006, TP_002 | Đủ |  |
| BR-12 | Địa chỉ SHS = CUSTOMER.CUST_ADDRESS của KH 069P000001 (3 vị trí) | 2 | DD_001, DD_002 | Đủ |  |
| BR-13 | Khối xác nhận cung cấp thông tin (Người đại diện, Chức vụ, Số/Ngày ủy … | 5 | DD_003, DD_004, DD_005, DD_009, DD_010 | Đủ |  |
| BR-14 | Khối xác nhận tư cách NĐT (Người đại diện, Số/Ngày ủy quyền, CCCD, Chứ… | 5 | DD_006, DD_007, DD_008, DD_009, DD_010 | Đủ |  |
| BR-15 | 10 nội dung lấy từ bảng phụ lục trái phiếu: Bản CBTT trước đợt chào bá… | 5 | PL_001, PL_002, PL_003, PL_004, PL_005 | Đủ |  |
| BR-16 | Căn cứ Nghị định (5 vị trí: mục 2 a, 2 b, 2 c, 2 d, 3.1 i) theo Ngày p… | 5 | ND_001, ND_002, ND_003, ND_004, ND_005 | Đủ |  |
| BR-17 | Mã số ĐKDN = REGISTRATION_ID, Loại ngành nghề = BUSINESS_TYPE, Lãi suấ… | 6 | TP_003, TP_004, TP_005, TP_006, TP_007, TP_008 | Đủ |  |
| BR-18 | Bảng tình hình tài chính: tối đa 3 kỳ gần nhất, sắp xếp gần → xa; có b… | 7 | FN_001, FN_002, FN_003, FN_004, FN_005, FN_006, FN_007 | Đủ |  |
| BR-19 | TSBĐ: Có/không = BOND_INFO.HAS_COLLATERAL; Giá trị TSBĐ = tổng giá trị… | 4 | TS_001, TS_002, TS_003, TS_004 | Đủ |  |
| BR-20 | Bảo lãnh thanh toán (Có/không, Tổ chức tín dụng bảo lãnh) lấy từ thông… | 2 | BL_001, BL_002 | Đủ |  |
| BR-21 | Ngày ký (ngày/tháng/năm) = REPO_ORDER.CREATED_DATE (3 vị trí ký) | 3 | DT_001, DT_002, DT_003 | Đủ |  |

### 2. Checklist (file 05)
| Nhóm | Tiêu chí | Kết quả | Ghi chú |
|---|---|---|---|
| A | 100% BR có ≥ 1 TC | Đạt | 21/21 BR |
| A | Mỗi trường có hợp lệ / trống / biên | Đạt | Trường text ngắn (Nơi cấp, Loại ngành nghề) chỉ có case hợp lệ – rủi ro thấp, chấp nhận |
| A | Luồng chính + phụ + ngoại lệ | Đạt | GEN, VIEW |
| A | Phân quyền từng role | Một phần | SRS 11 không định nghĩa role → 1 TC theo [GĐ Q-16] |
| A | DT: mỗi rule có TC | Đạt | 3 bảng DT phủ đủ cột |
| A | Error guessing | Đạt | Unicode, NULL, double-click, múi giờ, XSS, lẫn dữ liệu |
| B | Đủ 12 cột, ID đúng format, không trùng | Đạt | Kiểm tra tự động khi sinh file |
| B | 1 TC = 1 mục tiêu | Đạt* | Mục tiêu tính theo khối/câu: chứng chỉ, thông tin ủy quyền, TSBĐ, 10 nội dung phụ lục, căn cứ Nghị định theo 1 ngày |
| B | Title “Kiểm tra … khi …” | Đạt* | Vài TC dạng “Kiểm tra <tên trường> …” không có “khi” vì không có điều kiện riêng ngoài PC-01 |
| B | Test data cụ thể | Đạt | Bộ dữ liệu B2 mục 4 |
| B | Expected đo được | Đạt | Mỗi ý 1 dòng: nguồn DB, vị trí, định dạng; giá trị cụ thể ở Test Data |
| B | Negative: báo lỗi + không thực hiện | Đạt | Biểu mẫu không có message lỗi → kiểm “không gen / để trống / không lấy nhầm” |
| B | Message nguyên văn hoặc [GĐ] | Đạt | Mọi giá trị chưa rõ gắn [GĐ] + Q-xx |
| C | Không TC trùng | Đạt | GEN_003 (gen) và VIEW_003 (màn HĐ) khác mục tiêu |
| C | Priority hợp lý | Đạt | Căn cứ pháp lý, tư cách NĐT, người ký = High |
| C | Thứ tự sắp xếp | Đạt | UI → luồng chính → field → biên → negative → permission theo từng nhóm |

### 3. Rút gọn so với bản trước (117 → 82 TC), độ phủ giữ nguyên
| # | Thay đổi | Lý do vẫn đủ phủ |
|---|---|---|
| 1 | Gộp các trường cùng khối: chứng chỉ (4 → 1), Năm + Giá trị thu nhập chịu thuế (2 → 1), Chức vụ/Ủy quyền/CCCD mỗi khối xác nhận (3 → 1), TSBĐ có/giá trị/số ký hiệu (3 → 1), 10 nội dung phụ lục (10 → 1), Tổ chức phát hành 2 vị trí (2 → 1), Tên KH 2 vị trí (2 → 1), 4 TC nội dung tĩnh (4 → 1) | Cùng nguồn, cùng điều kiện, kiểm trong 1 lần mở PL05 |
| 2 | Bỏ 3 TC EP giữa khoảng ngày phát hành và TC “ngày tạo ≠ ngày phát hành” | 4 giá trị biên đã đại diện đủ 3 lớp; TC biên 15/09/2022 có ngày tạo 2026 |
| 3 | Bỏ TC so sánh PL05 ở 2 màn | Gộp vào TC hiển thị tại màn hợp đồng |
| 4 | Bỏ TC Low ít giá trị: tên Unicode, ngưỡng CKNY 2 tỷ, bảng 2 kỳ, giá trị âm, ô chỉ tiêu trống, 1 TSĐB, ngày/tháng 1 chữ số | Đã có TC biên min/max và TC định dạng cùng trường |

### 4. Thống kê
| Chỉ số | Giá trị | Mục tiêu | Nhận xét |
|---|---|---|---|
| Tổng TC | 82 | — | Bản rút gọn |
| Requirement coverage | 100% (21/21) | 100% | Đạt |
| High / Medium / Low | 37 (45%) / 41 (50%) / 4 (4%) | ≈ 25/50/25 | High cao hơn chuẩn do biểu mẫu pháp lý, mapping sai = sai văn bản ký |
| Positive : Non-positive | 36 : 46 (43% : 57%) | ≈ 30 : 70 | Non-positive gồm Negative, Boundary, Permission, Security, UI. Chức năng sinh văn bản thiên về xác minh mapping nên Positive cao hơn chuẩn |
| Theo Type | Positive 36, Negative 31, Boundary 12, UI 1, Permission 1, Security 1 | — |  |
| Q&A mở | 20 (Blocker 1 · Major 12 · Minor 7) | Blocker = 0 trước khi test | Q-01 phải chốt trước khi chạy NDT_001–004, NDT_014 |
| TC phụ thuộc [GIẢ ĐỊNH] | 28 | — | Cập nhật expected khi BA trả lời |

### 5. Việc cần làm tiếp
- Gửi BA 20 câu Q&A, ưu tiên Q-01 (Blocker), Q-03, Q-05, Q-08, Q-10, Q-14, Q-16.
- Chuẩn bị dữ liệu B2 mục 4 trên UAT (7 mã TP, 7 KH, 2 người UQ của 069P).
- Gõ `/EXPORT` để xuất Excel theo template.