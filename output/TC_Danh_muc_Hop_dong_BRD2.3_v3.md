# SHS App – Danh mục Hợp đồng – Bộ test (B1 → B5)

- **Tài liệu nguồn:** `doc/BRD/2.3. Danh mục Hợp đồng` (chính). Tham chiếu chéo: `2.3.1 Tổng quan`, `2.3.2 Dòng tiền`, `2.3.3 Xác nhận` (chỉ dùng cho điều hướng và đối chiếu dữ liệu).
- **Ngày lập:** 18/11/2026 · **Người lập:** QA
- **Lưu ý:** file export Confluence không có ảnh mockup, nên toàn bộ expected về giao diện chỉ dựa trên mô tả chữ (xem Q-16).

---

## B1 – PHÂN TÍCH YÊU CẦU

### 1. Tổng quan
- **Mục tiêu:** NĐT xem toàn bộ hợp đồng (HĐ) trái phiếu SHS đang có, xem nhanh các thông số chính của từng HĐ, nhận biết HĐ đang chờ xác nhận và đi tới màn Chi tiết HĐ / tab Xác nhận.
- **Role:** NĐT (khách hàng) đã đăng nhập app SHS. Tài liệu chỉ nêu 1 role.
- **Nền tảng:** App mobile (SH Smart).

### 2. Feature / Màn hình
| Mã | Feature | Nguồn |
|---|---|---|
| F1 | Truy cập Danh mục HĐ (2 đường dẫn) | 2.3 – Mục 1 |
| F2 | Danh sách card HĐ, sắp xếp mới → cũ | 2.3 – Mục 1, 2 |
| F3 | Filter theo Mã TP (khi vào từ Danh mục Trái phiếu) | 2.3 – Mục 1 |
| F4 | Hộp "Hợp đồng cần xác nhận" | 2.3 – Mục 1 (ý 3), Mục 2 |
| F5 | Điều hướng: card → Chi tiết HĐ; dòng cần xác nhận → tab Xác nhận | 2.3 – Mục 1, Mục 2 |

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Đường dẫn 1: Trái phiếu SHS → tab Danh mục nắm giữ → tab Danh mục Hợp đồng → hiển thị HĐ của tất cả mã TP | 2.3 M1 – Đường dẫn | UI/Logic |
| BR-02 | Đường dẫn 2: Trái phiếu SHS → tab Danh mục nắm giữ → chọn 1 card mã TP tại Danh mục Trái phiếu → vào Danh mục HĐ | 2.3 M1 – Đường dẫn | UI/Logic |
| BR-03 | Danh mục HĐ sắp xếp theo thứ tự HĐ mới nhất → cũ nhất | 2.3 M1 – Mô tả | Logic |
| BR-04 | Bấm card HĐ → mở màn Chi tiết HĐ gồm 3 tab: Tổng quan, Dòng tiền, Xác nhận | 2.3 M1 – Mô tả | Logic/UI |
| BR-05 | Vào theo Đường dẫn 2: hiển thị thêm filter riêng cho Mã TP | 2.3 M1 – Mô tả (ý 2) | UI |
| BR-06 | Vào theo Đường dẫn 2: chỉ hiển thị HĐ thuộc mã TP đã chọn | 2.3 M1 – Mô tả (ý 2) | Logic |
| BR-07 | Hộp "Hợp đồng cần xác nhận" chỉ hiển thị khi có ≥ 1 HĐ đang chờ xác nhận | 2.3 M2 – Hộp | Logic |
| BR-08 | Hộp gồm: icon cảnh báo, tiêu đề tĩnh, dòng phụ "N hợp đồng đang chờ xác nhận lệnh" | 2.3 M2 – Hộp | UI |
| BR-09 | N = số HĐ cần xác nhận, **đã áp bộ lọc / Mã TP hiện tại** nếu có | 2.3 M2 – Hộp | Logic |
| BR-10 | Mỗi dòng trong hộp: Mã TP, Mã HĐ và 1 trong 2 trạng thái: "Xác nhận Hợp đồng", "Xác nhận Phiếu lệnh" | 2.3 M2 – Dòng HĐ | Logic/UI |
| BR-11 | Bấm vào dòng trong hộp → mở Chi tiết HĐ và nhảy thẳng vào tab "Xác nhận" | 2.3 M1 (ý 3), M2 | Logic |
| BR-12 | Mã hợp đồng là mã định danh duy nhất của từng HĐ | 2.3 M2 | Logic |
| BR-13 | Status chip hiển thị trạng thái hiện tại của HĐ, gồm 5 giá trị: Chờ xử lý, Hiệu lực, Chờ xác nhận, Hết hiệu lực, Hoàn thiện | 2.3 M2 | Logic/UI |
| BR-14 | Ngày mua = ngày NĐT thực hiện giao dịch mua tạo ra HĐ | 2.3 M2 | Logic |
| BR-15 | Ngày đáo hạn tính theo kỳ hạn NĐT đã chọn khi đặt mua | 2.3 M2 | Logic |
| BR-16 | Thời gian đầu tư = số tháng kỳ hạn của riêng HĐ đó | 2.3 M2 | Logic |
| BR-17 | Số lượng Trái phiếu = khối lượng TP trong HĐ | 2.3 M2 | Logic |
| BR-18 | SL khả dụng = KL còn lại được mapping với HĐ | 2.3 M2 | Logic |
| BR-19 | Tổng tiền đầu tư = Giá mua (theo dữ liệu mã TP) × Số lượng Trái phiếu | 2.3 M2 | Logic (tiền) |
| BR-20 | Tổng tiền nhận về = Tổng tiền đầu tư + Lãi đầu tư dự kiến (lãi tính theo lãi suất coupon của mã TP) | 2.3 M2 | Logic (tiền) |
| BR-21 | Dữ liệu trên card và màn Chi tiết HĐ dùng chung field (Mã HĐ, Ngày mua, Ngày đáo hạn, SL TP) | 2.3.1 M2 – cột Ghi chú | Tích hợp |

### 4. Field list (card HĐ + hộp cần xác nhận)
| Field | Kiểu | Bắt buộc | Ràng buộc / Công thức | Mặc định | Message lỗi |
|---|---|---|---|---|---|
| Hộp "Hợp đồng cần xác nhận" | Block | Có điều kiện | Ẩn khi N = 0 (BR-07) | Ẩn | – |
| Dòng phụ hộp | Text | Có điều kiện | "N hợp đồng đang chờ xác nhận lệnh" (BR-09) | – | – |
| Dòng HĐ trong hộp | Row (clickable) | – | Mã TP + Mã HĐ + nhãn trạng thái (2 giá trị) | – | – |
| Mã hợp đồng | Text, read-only | Có | Duy nhất | – | – |
| Status chip | Label | Có | 5 giá trị (BR-13) | – | – |
| Ngày mua | Date | Có | Ngày giao dịch mua | – | – |
| Ngày đáo hạn | Date | Có | Theo kỳ hạn chọn khi mua | – | – |
| Thời gian đầu tư | Số tháng | Có | Kỳ hạn của HĐ | – | – |
| Số lượng Trái phiếu | Số nguyên | Có | KL trong HĐ | – | – |
| SL khả dụng | Số nguyên | Có | 0 ≤ SL khả dụng ≤ SL TP [GIẢ ĐỊNH] | – | – |
| Tổng tiền đầu tư | Tiền | Có | Giá mua × SL TP | – | – |
| Tổng tiền nhận về | Tiền | Có | Tổng tiền đầu tư + Lãi đầu tư dự kiến | – | – |
| Filter Mã TP | Filter | Có điều kiện | Chỉ có ở Đường dẫn 2 | Mã TP đã chọn [GIẢ ĐỊNH] | – |

> Màn này chỉ để xem, không có ô nhập, nên không có message lỗi validation. Các case lỗi tập trung vào dữ liệu sai, lệch số và điều hướng sai.

### 5. Q&A
| Q ID | Câu hỏi | Lý do hỏi | Mức | Giả định đề xuất |
|---|---|---|---|---|
| Q-01 | HĐ đang cần xác nhận ("Xác nhận Hợp đồng"/"Xác nhận Phiếu lệnh") có status chip gì trên card? Có phải luôn là "Chờ xác nhận"? | Không có bảng mapping giữa 2 trạng thái trong hộp và 5 status chip | Major | Chip = "Chờ xác nhận" |
| Q-02 | "Mới nhất → cũ nhất" sắp theo field nào: Ngày mua hay thời điểm tạo HĐ? Nếu 2 HĐ cùng ngày mua thì xếp thế nào? | Không có tiêu chí sắp xếp nên không xác định được expected | Major | Ngày mua giảm dần; cùng ngày thì thời điểm tạo HĐ giảm dần |
| Q-03 | Công thức Ngày đáo hạn: Ngày mua + N tháng? Xử lý ngày cuối tháng (31/08 + 6 tháng) và ngày nghỉ thế nào? | Chỉ ghi "tính theo kỳ hạn" | Major | Ngày mua + N tháng theo lịch; tháng đích không có ngày đó thì lấy ngày cuối tháng; không dời ngày nghỉ |
| Q-04 | Công thức Lãi đầu tư dự kiến: 2.3 ghi "theo lãi suất coupon mã TP"; 2.3.1 ghi "Lợi suất = lãi suất HĐ" và "cùng cách tính Tiền lãi dự kiến ở màn Đặt mua". Áp dụng lãi suất nào, cơ sở ngày nào, có trừ thuế/phí không? | Hai tài liệu mâu thuẫn, không có công thức số | Major | Kiểm tra bằng đối chiếu: Tổng nhận về = Tổng đầu tư + "Lãi đầu tư dự kiến" ở tab Tổng quan; lãi = giá trị đã hiển thị ở màn Đặt mua khi tạo HĐ |
| Q-05 | Tổng tiền đầu tư có cộng phí giao dịch không? (2.3: Giá × SL; 2.3.1 có field "Phí giao dịch" riêng) | Ảnh hưởng số tiền hiển thị | Major | Không cộng phí |
| Q-06 | SL khả dụng giảm khi nào (đặt bán, thanh lý trước hạn, phong tỏa, chuyển nhượng)? Tổng tiền đầu tư/nhận về tính theo SL TP hay SL khả dụng? | Không định nghĩa "mapping" | Major | Giảm khi có lệnh bán/thanh lý đang xử lý; tiền tính theo SL TP |
| Q-07 | Mục 1 gọi là "Tab Hợp đồng cần xác nhận" + "button Xác nhận"; Mục 2 gọi là "Hộp" + "bấm vào dòng". Giao diện thực tế là gì? | Thuật ngữ không thống nhất | Minor | Là hộp; bấm vào dòng để đi tới tab Xác nhận |
| Q-08 | Filter Mã TP (Đường dẫn 2) có cho đổi sang mã khác / xóa filter không? Danh sách giá trị lấy từ đâu? | Chỉ ghi "hiển thị thêm filter riêng" | Major | Mặc định là mã đã chọn; cho đổi sang các mã TP mà KH đang có HĐ |
| Q-09 | Khi KH không có HĐ nào thì hiển thị gì (empty state, text)? | Không mô tả | Minor | Hiển thị "Không có dữ liệu", không có hộp cần xác nhận |
| Q-10 | Danh sách có phân trang / lazy load không? Mỗi lần tải bao nhiêu HĐ? | Không mô tả | Minor | Lazy load khi cuộn |
| Q-11 | 1 HĐ cần cả "Xác nhận Hợp đồng" và "Xác nhận Phiếu lệnh" thì hiện 1 hay 2 dòng? N đếm theo HĐ hay theo chứng từ? | BR-09 ghi "số hợp đồng" nhưng 2.3.3 ghi có 2 chứng từ cần bổ sung | Major | 1 HĐ = 1 dòng (hiện chứng từ của bước hiện tại); N đếm theo HĐ |
| Q-12 | Định dạng ngày và tiền trên card? | Không mô tả | Minor | Ngày `dd/mm/yyyy` (theo ví dụ 2.3.1); tiền phân cách hàng nghìn bằng "." |
| Q-13 | HĐ "Hết hiệu lực"/"Hoàn thiện" có hiển thị vĩnh viễn hay chỉ trong một khoảng thời gian? "Hoàn thiện" nghĩa là gì so với "Hiệu lực"? | Không định nghĩa từng trạng thái | Minor | Hiển thị tất cả; chỉ kiểm tra text chip khớp trạng thái trên hệ thống |
| Q-14 | Tiêu đề tĩnh của hộp có text chính xác là gì? Khi N = 1 vẫn dùng câu "1 hợp đồng đang chờ xác nhận lệnh"? | Thiếu nguyên văn | Minor | Tiêu đề "Hợp đồng cần xác nhận"; câu giữ nguyên khi N = 1 |
| Q-15 | Khi mất mạng hoặc API lỗi thì hiển thị message gì? | Không mô tả | Minor | Thông báo lỗi chung của app, cho phép thử lại |
| Q-16 | Xin bản mockup (ảnh không có trong file export) | Cần để kiểm tra layout, vị trí field | Minor | Kiểm tra theo danh sách field trong mục 2 |
| Q-18 | Bấm card HĐ thì Chi tiết HĐ mở ở tab nào mặc định (Tổng quan?) | Tài liệu chỉ nêu riêng trường hợp nhảy thẳng tab Xác nhận | Minor | Tab Tổng quan |
| Q-17 | Dữ liệu HĐ cập nhật lúc nào: realtime, khi mở lại màn hay khi pull-to-refresh? | Ảnh hưởng expected sau khi đặt mua / xác nhận | Major | Tải lại khi vào màn và khi pull-to-refresh |

> **Không có Q&A mức Blocker**, nên pipeline chạy tiếp B2 → B5.

### 6. Top 5 rủi ro nghiệp vụ
1. **Sai số tiền** (Tổng tiền đầu tư / nhận về) do lệch công thức lãi (Q-04) hoặc lẫn phí (Q-05). KH thấy sai số tiền sẽ dẫn tới khiếu nại.
2. **Sai Ngày đáo hạn** ở các ngày cuối tháng (Q-03), làm KH kỳ vọng sai ngày nhận tiền.
3. **Bỏ sót HĐ cần xác nhận** (hộp không hiện hoặc N đếm sai khi đang lọc). Nếu không xác nhận thì không bán hoặc đáo hạn được HĐ (2.3.3).
4. **Lộ dữ liệu HĐ của KH khác** (IDOR qua API danh sách / chi tiết HĐ).
5. **SL khả dụng sai** khiến KH đặt bán vượt khối lượng còn lại.

---

## B2 – CHIẾN LƯỢC TEST

### 1. Phạm vi
- **In-scope:** màn Danh mục HĐ trên App: 2 đường dẫn, danh sách và sắp xếp, filter Mã TP, hộp cần xác nhận, toàn bộ field trên card, điều hướng tới Chi tiết HĐ / tab Xác nhận, đối chiếu dữ liệu card ↔ tab Tổng quan, bảo mật cơ bản (dữ liệu theo KH).
- **Out-of-scope:** nội dung chi tiết các tab Tổng quan / Dòng tiền / Xác nhận (2.3.1–2.3.3); luồng ký xác nhận và OTP; luồng đặt mua; bản Web; performance và load test.

### 2. Loại test
Functional · UI · Integration (đồng bộ trạng thái và dữ liệu với Chi tiết HĐ) · Security cơ bản (IDOR, session) · Error guessing (mạng, double-tap, refresh).

### 3. Kỹ thuật & ước lượng
| BR ID | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC |
|---|---|---|---|---|
| BR-01, BR-02 | UC | EG | High | 4 |
| BR-03 | UC | BVA (cùng ngày), EG | Medium | 3 |
| BR-04 | UC | EG (double-tap, back) | High | 4 |
| BR-05, BR-06 | EP | DT, EG | High | 6 |
| BR-07, BR-09 | DT | BVA (N = 0/1/>1), ST | High | 7 |
| BR-08, BR-10, BR-11 | EP | UC | High/Medium | 5 |
| BR-12 | EG | – | Medium | 1 |
| BR-13 | EP (5 trạng thái) | ST | Medium | 6 |
| BR-14 → BR-18 | EP | BVA | Medium/High | 9 |
| BR-19, BR-20 | EP | BVA, EG (làm tròn) | High | 5 |
| BR-21 | UC | – | Medium | 1 |
| Bảo mật / hệ thống | EG | – | High/Medium | 4 |

### 4. Môi trường & dữ liệu test
- **Môi trường:** UAT, app SH Smart bản build mới nhất, 1 máy Android và 1 máy iOS. Có quyền xem BO SHBond (hoặc DB) để đối chiếu HĐ.
- **Đường dẫn dùng chung:**
  - **ĐD1** = Đăng nhập app SHS → Trái phiếu SHS → tab Danh mục nắm giữ → tab Danh mục Hợp đồng.
  - **ĐD2(X)** = Đăng nhập app SHS → Trái phiếu SHS → tab Danh mục nắm giữ → tại Danh mục Trái phiếu, chọn card mã TP X.
- **Bộ dữ liệu.** Mã TP và mã HĐ dưới đây là tên mẫu, khi chạy thay bằng mã thật trên UAT nhưng giữ nguyên các thuộc tính.

| KH | Mô tả dữ liệu |
|---|---|
| KH01 | Có tài khoản TPRL, **không có HĐ** nào |
| KH02 | 8 HĐ, 3 mã TP (bảng bên dưới) |
| KH03 | Đúng **1 HĐ**: HD31, mã `TPA`, SL = **1**, Giá mua 102.500, trạng thái cần "Xác nhận Hợp đồng" |
| KH04 | ≥ **30 HĐ**, **không có HĐ nào cần xác nhận** (dùng cho kiểm tra cuộn / lazy load và trường hợp hộp ẩn) |
| KH05 | 2 HĐ **cùng Ngày mua** 12/11/2026: HD51 tạo lúc 09:00, HD52 tạo lúc 14:00 |
| KH06 | HD61: Ngày mua **31/08/2026**, kỳ hạn 6 tháng |
| KH07 | KH khác, có HĐ HD71 (dùng cho case bảo mật) |
| KH08 | 1 HĐ HD81 (mã `TPA`) cần bổ sung **cả** HĐ đặt bán và Phiếu lệnh đặt bán |

**Chi tiết KH02** (Giá mua mã TP: `TPA` = 102.500 đ, `TPB` = 100.000 đ, `TPC` = 101.000 đ):
| HĐ | Mã TP | Ngày mua | Kỳ hạn | SL TP | SL khả dụng | Trạng thái | Trạng thái cần XN |
|---|---|---|---|---|---|---|---|
| HD06 | TPA | 16/11/2026 | 6 | 300 | 300 | Chờ xử lý | – |
| HD01 | TPA | 10/11/2026 | 6 | 1.000 | 1.000 | Chờ xác nhận | Xác nhận Hợp đồng |
| HD02 | TPA | 05/11/2026 | 12 | 500 | 500 | Chờ xác nhận | Xác nhận Phiếu lệnh |
| HD03 | TPB | 01/10/2026 | 3 | 200 | 200 | Chờ xác nhận | Xác nhận Hợp đồng |
| HD04 | TPA | 15/07/2026 | 6 | 2.000 | 1.500 (đã đặt bán 500) | Hiệu lực | – |
| HD08 | TPC | 01/06/2026 | 12 | 800 | 0 (đang thanh lý toàn bộ) | Hiệu lực | – |
| HD07 | TPB | 20/03/2026 | 6 | 400 | 400 | Hoàn thiện | – |
| HD05 | TPB | 15/01/2026 | 6 | 600 | 0 | Hết hiệu lực | – |

→ Thứ tự đúng theo ĐD1 [GIẢ ĐỊNH Q-02]: HD06, HD01, HD02, HD03, HD04, HD08, HD07, HD05.
→ Hộp cần XN: ĐD1 có N = 3 (HD01, HD02, HD03); ĐD2(TPA) có N = 2; ĐD2(TPB) có N = 1; ĐD2(TPC) không có hộp.

> **Thứ tự chạy:** các TC làm thay đổi dữ liệu (TC_DMHD_XN_011, XN_012, CARD_014, SEC_005) chạy **sau cùng**, hoặc chạy trên một bản sao dữ liệu KH02/KH03, để không làm sai expected của các TC khác.

### 5. Exit criteria
- 100% TC High đã chạy và Pass; ≥ 95% tổng TC Pass.
- Không còn bug Critical/Major đang mở ở các nhóm tiền, ngày và bảo mật.
- Q&A Major (Q-01 → Q-06, Q-08, Q-11, Q-17) đã có trả lời và các TC [GIẢ ĐỊNH] liên quan đã cập nhật lại.

---

## B3 – TEST CONDITION

### 1. EP – Status chip (BR-13)
| Lớp | Giá trị | Condition |
|---|---|---|
| Hợp lệ | Chờ xử lý / Hiệu lực / Chờ xác nhận / Hết hiệu lực / Hoàn thiện | TCO-13-01 → 05: mỗi trạng thái hiển thị đúng text chip |
| Không hợp lệ | Trạng thái khác (VD: đã hủy) | TCO-13-06 [EG]: không hiển thị nhãn lạ hoặc trống → Q-13 |

### 2. Decision table – Hộp "Hợp đồng cần xác nhận" (BR-07, BR-09)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Đường dẫn | ĐD1 | ĐD1 | ĐD1 | ĐD2 | ĐD2 |
| Số HĐ cần XN (toàn KH) | 0 | 1 | 3 | 3 | 3 |
| Số HĐ cần XN thuộc mã TP đang lọc | – | – | – | 2 (TPA) | 0 (TPC) |
| **Kết quả: Hộp** | Ẩn | Hiện, N = 1 | Hiện, N = 3 | Hiện, N = 2, chỉ dòng TPA | Ẩn |
| Condition | TCO-07-01 | TCO-07-02 | TCO-09-01 | TCO-09-02 | TCO-09-03 |

### 3. State transition – HĐ trong hộp (BR-07, BR-09, BR-13) [GIẢ ĐỊNH Q-17]
| Từ \ Sự kiện | Đặt mua thành công | Hệ thống yêu cầu XN | KH hoàn tất XN | Đáo hạn/tất toán |
|---|---|---|---|---|
| (chưa có) | Chờ xử lý (thêm card trên cùng) | ✖ | ✖ | ✖ |
| Chờ xử lý | ✖ | Chờ xác nhận (vào hộp) | ✖ | ✖ |
| Chờ xác nhận | ✖ | ✖ | Rời hộp; N giảm 1 | ✖ |
| Hiệu lực | ✖ | ✖ | ✖ | Hết hiệu lực |
→ Condition: TCO-ST-01 (HĐ mới xuất hiện), TCO-ST-02 (XN xong HĐ duy nhất thì hộp ẩn), TCO-ST-03 (XN 1/3 thì N = 2), TCO-ST-04 (HĐ Hiệu lực không vào hộp).

### 4. EP/BVA – Field tính toán
| Field | Hợp lệ | Không hợp lệ / rủi ro | Biên |
|---|---|---|---|
| SL khả dụng (BR-18) | = SL TP; < SL TP | > SL TP; âm | 0; = SL TP |
| Tổng tiền đầu tư (BR-19) | 102.500 × 1.000 = 102.500.000 | Cộng phí (Q-05); tính theo SL khả dụng | SL = 1 → 102.500 |
| Ngày đáo hạn (BR-15) | 10/11/2026 + 6 tháng = 10/05/2027 | Lệch 1 ngày | 31/08/2026 + 6 tháng → 28/02/2027 [GIẢ ĐỊNH Q-03] |
| Thời gian đầu tư (BR-16) | 3 / 6 / 12 tháng | Hiển thị kỳ hạn của mã TP thay cho kỳ hạn của HĐ | – |
| Tổng tiền nhận về (BR-20) | Tổng đầu tư + Lãi dự kiến | Thiếu lãi; lãi tính theo kỳ hạn khác | Kỳ hạn ngắn nhất / dài nhất |

### 5. Use case
- **Luồng chính:** ĐD1 → xem danh sách → bấm card → Chi tiết HĐ (TCO-01-01, TCO-04-01).
- **Luồng phụ:** ĐD2 → danh sách đã lọc → bấm card (TCO-02-01, TCO-06-01, TCO-04-02); bấm dòng trong hộp → tab Xác nhận (TCO-11-01).
- **Luồng ngoại lệ:** KH không có HĐ (TCO-ND-01); mất mạng (TCO-EG-01); session hết hạn (TCO-EG-02).

### 6. Error guessing (theo file 03)
- Danh sách: rỗng, 1 bản ghi, nhiều bản ghi (cuộn), cùng ngày → TCO-03-02, TCO-03-03, TCO-ND-01, TCO-ND-02
- Nút/thao tác: double-tap card, back giữ filter → TCO-04-03, TCO-04-04
- Hệ thống: mất mạng, refresh sau khi có HĐ mới → TCO-EG-01, TCO-ST-01
- Phân quyền: IDOR qua API danh sách / chi tiết, session hết hạn → TCO-SEC-01, TCO-SEC-02, TCO-EG-02
- Tài chính: làm tròn tiền, định dạng hàng nghìn → TCO-19-03, TCO-UI-04

---
## B4 – TEST CASE

**Precondition chung (PC chung):** app SH Smart bản build UAT mới nhất; KH đăng nhập bằng tài khoản trong bộ dữ liệu ở B2; tài khoản TPRL đang hoạt động; mạng ổn định (trừ khi TC ghi khác). ĐD1/ĐD2(X) định nghĩa tại B2 mục 4.


### 4.1. Danh mục HĐ – Giao diện

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_UI_001 | BR-13, BR-14→BR-20 | Danh mục HĐ – Giao diện | Kiểm tra card HĐ hiển thị đủ field khi mở Danh mục HĐ | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Quan sát card HD04 | KH02 / HD04 | Card HD04 hiển thị đủ 9 field có nhãn: Mã hợp đồng, Status chip, Ngày mua, Ngày đáo hạn, Thời gian đầu tư, Số lượng Trái phiếu, SL khả dụng, Tổng tiền đầu tư, Tổng tiền nhận về; không có field trống hoặc null | Medium | UI | EP | Layout tham chiếu Q-16 |
| TC_DMHD_UI_002 | BR-08 | Danh mục HĐ – Giao diện | Kiểm tra bố cục hộp "Hợp đồng cần xác nhận" khi có HĐ chờ xác nhận | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Quan sát hộp "Hợp đồng cần xác nhận" | KH02 (3 HĐ cần XN) | Hộp có: (1) icon cảnh báo; (2) tiêu đề tĩnh "Hợp đồng cần xác nhận"; (3) dòng phụ "3 hợp đồng đang chờ xác nhận lệnh"; (4) danh sách dòng HĐ bên dưới | Low | UI | EP | [GIẢ ĐỊNH] text tiêu đề – Q-14 |
| TC_DMHD_UI_003 | BR-05 | Danh mục HĐ – Giao diện | Kiểm tra hiển thị filter Mã TP khi vào từ Danh mục Trái phiếu | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD2(TPA)<br>2. Quan sát vùng phía trên danh sách HĐ | KH02 / Mã TP = TPA | Có filter Mã TP, giá trị đang chọn = "TPA" | Medium | UI | EP | [GIẢ ĐỊNH] giá trị mặc định – Q-08 |
| TC_DMHD_UI_004 | BR-14, BR-15 | Danh mục HĐ – Giao diện | Kiểm tra định dạng ngày khi hiển thị Ngày mua và Ngày đáo hạn trên card | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Quan sát Ngày mua, Ngày đáo hạn của HD04 | KH02 / HD04 | Ngày mua = "15/07/2026"; Ngày đáo hạn = "15/01/2027" (dạng dd/mm/yyyy, có số 0 đứng đầu ngày/tháng) | Low | UI | EG | [GIẢ ĐỊNH] định dạng – Q-12 |
| TC_DMHD_UI_005 | BR-19, BR-20 | Danh mục HĐ – Giao diện | Kiểm tra định dạng số tiền khi hiển thị Tổng tiền đầu tư trên card | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Quan sát Tổng tiền đầu tư của HD04 | KH02 / HD04 (205.000.000) | Hiển thị "205.000.000" (dấu "." phân cách hàng nghìn), không có phần thập phân, không bị cắt chữ hay xuống dòng | Low | UI | EG | [GIẢ ĐỊNH] định dạng – Q-12 |

### 4.2. Danh mục HĐ – Điều hướng

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_NAV_001 | BR-01 | Danh mục HĐ – Điều hướng | Kiểm tra mở Danh mục HĐ khi đi theo đường dẫn tab Danh mục nắm giữ | PC chung; đăng nhập KH02 | 1. Chọn Trái phiếu SHS<br>2. Chọn tab Danh mục nắm giữ<br>3. Chọn tab Danh mục Hợp đồng | KH02 (8 HĐ, 3 mã TP) | Hiển thị màn Danh mục HĐ với đủ 8 card (HD01–HD08) của cả 3 mã TPA, TPB, TPC; không có filter Mã TP; số card khớp số HĐ của KH02 trên BO SHBond | High | Positive | UC |  |
| TC_DMHD_NAV_002 | BR-02 | Danh mục HĐ – Điều hướng | Kiểm tra mở Danh mục HĐ khi chọn card mã TP tại Danh mục Trái phiếu | PC chung; đăng nhập KH02 | 1. Chọn Trái phiếu SHS<br>2. Chọn tab Danh mục nắm giữ<br>3. Tại Danh mục Trái phiếu, chọn card mã TPA | KH02 / Mã TP = TPA | Chuyển sang màn Danh mục HĐ; hiển thị 4 card của TPA (HD06, HD01, HD02, HD04) | High | Positive | UC |  |
| TC_DMHD_NAV_003 | BR-04, BR-21 | Danh mục HĐ – Điều hướng | Kiểm tra mở Chi tiết HĐ khi bấm card HĐ | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ (ĐD1) | 1. Bấm card HD04 | KH02 / HD04 | Mở màn Chi tiết HĐ; có 3 tab Tổng quan, Dòng tiền, Xác nhận; "Mã hợp đồng" ở Chi tiết = mã HD04 | High | Positive | UC | Tab mặc định – Q-18 |
| TC_DMHD_NAV_004 | BR-04, BR-06 | Danh mục HĐ – Điều hướng | Kiểm tra mở Chi tiết HĐ khi bấm card trong danh sách đã lọc theo Mã TP | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ theo ĐD2(TPA) | 1. Bấm card HD02 | KH02 / HD02 (TPA) | Mở Chi tiết HĐ của HD02 (Mã HĐ khớp HD02), có đủ 3 tab | Medium | Positive | UC |  |
| TC_DMHD_NAV_005 | BR-04 | Danh mục HĐ – Điều hướng | Kiểm tra mở Chi tiết HĐ khi bấm nhanh 2 lần liên tiếp vào card | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ (ĐD1) | 1. Bấm nhanh 2 lần liên tiếp (< 0,5 giây) vào card HD01<br>2. Bấm Back 1 lần | KH02 / HD01 | Chỉ mở 1 màn Chi tiết HD01; sau 1 lần Back quay về Danh mục HĐ (không phải back 2 lần) | Low | Negative | EG |  |
| TC_DMHD_NAV_006 | BR-05, BR-06 | Danh mục HĐ – Điều hướng | Kiểm tra giữ filter Mã TP khi quay lại từ Chi tiết HĐ | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ theo ĐD2(TPA) | 1. Bấm card HD01<br>2. Bấm Back | KH02 / TPA | Quay về Danh mục HĐ; filter vẫn = TPA; vẫn chỉ hiển thị 4 card TPA | Medium | Negative | EG | [GIẢ ĐỊNH] giữ filter khi back |

### 4.3. Danh mục HĐ – Danh sách

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_LIST_001 | BR-03 | Danh mục HĐ – Danh sách | Kiểm tra thứ tự HĐ khi KH có nhiều HĐ khác Ngày mua | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Ghi lại thứ tự card từ trên xuống | KH02 (8 HĐ) | Thứ tự: HD06 (16/11/2026) → HD01 → HD02 → HD03 → HD04 → HD08 → HD07 → HD05 (15/01/2026) | Medium | Positive | UC | [GIẢ ĐỊNH] sắp theo Ngày mua – Q-02 |
| TC_DMHD_LIST_002 | BR-03 | Danh mục HĐ – Danh sách | Kiểm tra thứ tự HĐ khi 2 HĐ có cùng Ngày mua | PC chung; đăng nhập KH05 | 1. Vào màn theo ĐD1<br>2. Quan sát thứ tự HD51, HD52 | KH05: HD51 (12/11/2026 09:00), HD52 (12/11/2026 14:00) | HD52 nằm trên HD51; thứ tự giữ nguyên sau 3 lần vào lại màn | Low | Boundary | BVA | [GIẢ ĐỊNH] Q-02 |
| TC_DMHD_LIST_003 | BR-13 | Danh mục HĐ – Danh sách | Kiểm tra status chip khi HĐ ở trạng thái Chờ xử lý | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Quan sát status chip của card HD06 | KH02 / HD06 | Chip hiển thị "Chờ xử lý", khớp trạng thái HD06 trên BO SHBond | Medium | Positive | EP |  |
| TC_DMHD_LIST_004 | BR-13 | Danh mục HĐ – Danh sách | Kiểm tra status chip khi HĐ ở trạng thái Hiệu lực | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Quan sát status chip của card HD04 | KH02 / HD04 | Chip hiển thị "Hiệu lực", khớp BO SHBond | Medium | Positive | EP |  |
| TC_DMHD_LIST_005 | BR-13 | Danh mục HĐ – Danh sách | Kiểm tra status chip khi HĐ đang chờ xác nhận | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Quan sát status chip của card HD01 | KH02 / HD01 (cần "Xác nhận Hợp đồng") | Chip hiển thị "Chờ xác nhận" | Medium | Positive | EP | [GIẢ ĐỊNH] mapping – Q-01 |
| TC_DMHD_LIST_006 | BR-13 | Danh mục HĐ – Danh sách | Kiểm tra status chip khi HĐ ở trạng thái Hết hiệu lực | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Quan sát status chip của card HD05 | KH02 / HD05 | Chip hiển thị "Hết hiệu lực"; HD05 vẫn có trong danh sách | Medium | Positive | EP | Q-13 |
| TC_DMHD_LIST_007 | BR-13 | Danh mục HĐ – Danh sách | Kiểm tra status chip khi HĐ ở trạng thái Hoàn thiện | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Quan sát status chip của card HD07 | KH02 / HD07 | Chip hiển thị "Hoàn thiện", khớp BO SHBond | Medium | Positive | EP | Q-13 |
| TC_DMHD_LIST_008 | BR-13 | Danh mục HĐ – Danh sách | Kiểm tra status chip khi trạng thái HĐ trên hệ thống nằm ngoài 5 giá trị đã định nghĩa | PC chung; KH02 có thêm HĐ HD09 bị cập nhật (trên DB UAT) sang trạng thái ngoài danh sách, VD "Đã hủy" | 1. Vào màn theo ĐD1<br>2. Quan sát card HD09 | KH02 / HD09 | Không hiển thị chip trống, mã kỹ thuật (VD "CANCELLED") hay text lạ; app không crash. Ghi nhận cách xử lý thực tế để đưa vào Q-13 | Low | Negative | EG | [GIẢ ĐỊNH] cần BA xác nhận – Q-13 |
| TC_DMHD_LIST_009 | BR-01 | Danh mục HĐ – Danh sách | Kiểm tra màn Danh mục HĐ khi KH chưa có HĐ nào | PC chung; đăng nhập KH01 | 1. Vào màn theo ĐD1 | KH01 (0 HĐ) | Hiển thị trạng thái rỗng "Không có dữ liệu"; không có card nào; không có hộp "Hợp đồng cần xác nhận"; không có lỗi hay loading treo | Medium | Boundary | BVA | [GIẢ ĐỊNH] text empty – Q-09 |
| TC_DMHD_LIST_010 | BR-01, BR-03 | Danh mục HĐ – Danh sách | Kiểm tra danh sách khi KH có đúng 1 HĐ | PC chung; đăng nhập KH03 | 1. Vào màn theo ĐD1 | KH03 / HD31 | Danh sách chỉ có 1 card là HD31 | Low | Boundary | BVA |  |
| TC_DMHD_LIST_011 | BR-01, BR-03 | Danh mục HĐ – Danh sách | Kiểm tra cuộn danh sách khi KH có từ 30 HĐ trở lên | PC chung; đăng nhập KH04 | 1. Vào màn theo ĐD1<br>2. Cuộn đến cuối danh sách<br>3. Đếm số card, đối chiếu BO SHBond | KH04 (≥ 30 HĐ) | Tải đủ toàn bộ HĐ (số card = số HĐ trên BO); không trùng card; không mất card; vẫn sắp mới → cũ khi tải thêm | Medium | Boundary | EG | [GIẢ ĐỊNH] lazy load – Q-10 |
| TC_DMHD_LIST_012 | BR-12 | Danh mục HĐ – Danh sách | Kiểm tra Mã hợp đồng không trùng khi KH có nhiều HĐ | PC chung; đăng nhập KH04 | 1. Vào màn theo ĐD1<br>2. Ghi lại Mã hợp đồng của toàn bộ card | KH04 | Không có 2 card cùng Mã hợp đồng; mỗi mã khớp 1 HĐ trên BO SHBond | Medium | Negative | EG |  |

### 4.4. Danh mục HĐ – Card HĐ

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_CARD_001 | BR-14 | Danh mục HĐ – Card HĐ | Kiểm tra Ngày mua khi HĐ được tạo từ giao dịch đặt mua | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem Ngày mua của HD01<br>3. Đối chiếu ngày giao dịch đặt mua của HD01 trên BO SHBond | KH02 / HD01 | Ngày mua = "10/11/2026", trùng ngày giao dịch mua trên BO | Medium | Positive | EP |  |
| TC_DMHD_CARD_002 | BR-15 | Danh mục HĐ – Card HĐ | Kiểm tra Ngày đáo hạn khi HĐ kỳ hạn 6 tháng | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem Ngày đáo hạn của HD01 | HD01: Ngày mua 10/11/2026, kỳ hạn 6 tháng | Ngày đáo hạn = "10/05/2027"; khớp Ngày đáo hạn ở tab Tổng quan của HD01 | High | Positive | EP | [GIẢ ĐỊNH] công thức – Q-03 |
| TC_DMHD_CARD_003 | BR-15 | Danh mục HĐ – Card HĐ | Kiểm tra Ngày đáo hạn khi ngày mua là ngày cuối tháng | PC chung; đăng nhập KH06 | 1. Vào màn theo ĐD1<br>2. Xem Ngày đáo hạn của HD61 | HD61: Ngày mua 31/08/2026, kỳ hạn 6 tháng | Ngày đáo hạn = "28/02/2027"; không hiển thị ngày không tồn tại (31/02) và không nhảy sang 03/03/2027 | Medium | Boundary | BVA | [GIẢ ĐỊNH] Q-03 |
| TC_DMHD_CARD_004 | BR-16 | Danh mục HĐ – Card HĐ | Kiểm tra Thời gian đầu tư khi HĐ kỳ hạn 12 tháng | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem Thời gian đầu tư của HD02 | HD02: kỳ hạn chọn khi đặt mua = 12 tháng | Thời gian đầu tư = "12 tháng" | Medium | Positive | EP |  |
| TC_DMHD_CARD_005 | BR-16 | Danh mục HĐ – Card HĐ | Kiểm tra Thời gian đầu tư khi 2 HĐ cùng mã TP có kỳ hạn khác nhau | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD2(TPA)<br>2. So sánh Thời gian đầu tư của HD01 và HD02 | HD01 = 6 tháng; HD02 = 12 tháng (cùng TPA) | HD01 hiển thị "6 tháng", HD02 hiển thị "12 tháng" (lấy theo kỳ hạn của từng HĐ, không lấy chung theo mã TP) | Medium | Positive | EP |  |
| TC_DMHD_CARD_006 | BR-17 | Danh mục HĐ – Card HĐ | Kiểm tra Số lượng Trái phiếu khi HĐ có khối lượng 1.000 | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem Số lượng Trái phiếu của HD01 | HD01: KL = 1.000 | Số lượng Trái phiếu = "1.000", khớp KL của HD01 trên BO SHBond | High | Positive | EP |  |
| TC_DMHD_CARD_007 | BR-18 | Danh mục HĐ – Card HĐ | Kiểm tra SL khả dụng khi HĐ chưa có lệnh bán/thanh lý | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem SL khả dụng của HD01 | HD01: SL TP 1.000; không có lệnh bán | SL khả dụng = "1.000" (bằng Số lượng Trái phiếu) | Medium | Positive | EP | [GIẢ ĐỊNH] Q-06 |
| TC_DMHD_CARD_008 | BR-18 | Danh mục HĐ – Card HĐ | Kiểm tra SL khả dụng khi đã đặt bán một phần khối lượng | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem Số lượng Trái phiếu và SL khả dụng của HD04 | HD04: SL TP 2.000; đã đặt bán 500 | Số lượng Trái phiếu = "2.000"; SL khả dụng = "1.500"; SL khả dụng không lớn hơn Số lượng Trái phiếu | High | Positive | EP | [GIẢ ĐỊNH] Q-06 |
| TC_DMHD_CARD_009 | BR-18 | Danh mục HĐ – Card HĐ | Kiểm tra SL khả dụng khi toàn bộ khối lượng đang thanh lý | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem SL khả dụng của HD08 | HD08: SL TP 800; đang thanh lý 800 | SL khả dụng = "0" (không âm, không trống) | Medium | Boundary | BVA | [GIẢ ĐỊNH] Q-06 |
| TC_DMHD_CARD_010 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra Tổng tiền đầu tư khi HĐ có SL 1.000 và giá mua 102.500 | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem Tổng tiền đầu tư của HD01 | HD01: Giá mua TPA = 102.500; SL = 1.000 | Tổng tiền đầu tư = "102.500.000" (= 102.500 × 1.000); không cộng phí giao dịch | High | Positive | EP | [GIẢ ĐỊNH] không gồm phí – Q-05 |
| TC_DMHD_CARD_011 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra Tổng tiền đầu tư khi SL khả dụng nhỏ hơn Số lượng Trái phiếu | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Xem Tổng tiền đầu tư của HD04 | HD04: Giá 102.500; SL TP 2.000; SL khả dụng 1.500 | Tổng tiền đầu tư = "205.000.000" (tính theo SL TP); không phải 153.750.000 (tính theo SL khả dụng) | High | Negative | EP | [GIẢ ĐỊNH] Q-06 |
| TC_DMHD_CARD_012 | BR-19 | Danh mục HĐ – Card HĐ | Kiểm tra Tổng tiền đầu tư khi HĐ có SL = 1 | PC chung; đăng nhập KH03 | 1. Vào màn theo ĐD1<br>2. Xem Tổng tiền đầu tư của HD31 | HD31: Giá 102.500; SL = 1 | Tổng tiền đầu tư = "102.500" | Medium | Boundary | BVA |  |
| TC_DMHD_CARD_013 | BR-20 | Danh mục HĐ – Card HĐ | Kiểm tra Tổng tiền nhận về khi HĐ có lãi đầu tư dự kiến | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Ghi Tổng tiền đầu tư (A) và Tổng tiền nhận về (B) của HD01<br>3. Bấm card HD01, ghi "Lãi đầu tư dự kiến" (L) ở tab Tổng quan | KH02 / HD01 (A = 102.500.000) | B = A + L (VD minh hoạ: L = 3.075.000 thì B = 105.575.000); B > A | High | Positive | EP | Q-04: chưa có công thức số của L |
| TC_DMHD_CARD_014 | BR-20 | Danh mục HĐ – Card HĐ | Kiểm tra Lãi đầu tư dự kiến khi đối chiếu với màn Đặt mua lúc tạo HĐ | PC chung; đăng nhập KH02; tester tạo HĐ mới cho TPA kỳ hạn 6 tháng, SL 100 và ghi lại "Tiền lãi dự kiến" (L0) ở màn Đặt mua | 1. Vào màn theo ĐD1<br>2. Xem Tổng tiền đầu tư (A) và Tổng tiền nhận về (B) của HĐ vừa tạo<br>3. Tính B − A | TPA; SL = 100; kỳ hạn 6 tháng | B − A = L0; giá trị không đổi sau khi HĐ chuyển trạng thái | High | Positive | UC | [GIẢ ĐỊNH] công thức lãi – Q-04 |
| TC_DMHD_CARD_015 | BR-20 | Danh mục HĐ – Card HĐ | Kiểm tra Tổng tiền nhận về khi 2 HĐ cùng mã TP có kỳ hạn khác nhau | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD2(TPA)<br>2. Tính (Tổng nhận về − Tổng đầu tư) / Tổng đầu tư cho HD01 và HD02 | HD01: 6 tháng; HD02: 12 tháng | Tỷ lệ lãi của HD02 (12 tháng) lớn hơn HD01 (6 tháng); không có 2 HĐ khác kỳ hạn mà tỷ lệ lãi bằng nhau | Medium | Negative | EG | [GIẢ ĐỊNH] lãi tăng theo kỳ hạn – Q-04 |
| TC_DMHD_CARD_016 | BR-21 | Danh mục HĐ – Card HĐ | Kiểm tra dữ liệu card khớp tab Tổng quan khi mở Chi tiết HĐ | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Ghi Mã HĐ, Ngày mua, Ngày đáo hạn, SL TP, Tổng tiền đầu tư, Tổng tiền nhận về của HD04<br>3. Bấm card HD04, xem tab Tổng quan | KH02 / HD04 | 6 giá trị ở tab Tổng quan trùng khớp với card; Ngày đáo hạn ở tab Tổng quan có dạng "15/01/2027 (6 tháng)" | Medium | Integration | UC | Theo 2.3.1 |

### 4.5. Danh mục HĐ – Filter Mã TP

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_FIL_001 | BR-06 | Danh mục HĐ – Filter Mã TP | Kiểm tra danh sách chỉ gồm HĐ của mã TP đã chọn khi vào từ Danh mục Trái phiếu | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD2(TPB)<br>2. Kiểm tra Mã TP của từng card | KH02 / TPB (HD03, HD07, HD05) | Chỉ hiển thị 3 card HD03, HD07, HD05; không có HĐ nào của TPA hoặc TPC | High | Positive | EP |  |
| TC_DMHD_FIL_002 | BR-06, BR-03 | Danh mục HĐ – Filter Mã TP | Kiểm tra thứ tự HĐ khi danh sách đang lọc theo Mã TP | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD2(TPA)<br>2. Ghi lại thứ tự card | KH02 / TPA | Thứ tự: HD06 → HD01 → HD02 → HD04 | Low | Positive | UC | [GIẢ ĐỊNH] Q-02 |
| TC_DMHD_FIL_003 | BR-05 | Danh mục HĐ – Filter Mã TP | Kiểm tra không hiển thị filter Mã TP khi vào theo tab Danh mục Hợp đồng | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Quan sát vùng phía trên danh sách | KH02 | Không có filter Mã TP; danh sách gồm HĐ của cả 3 mã TP | Medium | Negative | EP |  |
| TC_DMHD_FIL_004 | BR-05, BR-06 | Danh mục HĐ – Filter Mã TP | Kiểm tra danh sách khi đổi filter sang mã TP khác | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ theo ĐD2(TPA) | 1. Mở filter Mã TP<br>2. Chọn TPC | KH02 / TPA → TPC | Danh sách chỉ còn card HD08 (TPC); không còn card TPA | Medium | Positive | EP | [GIẢ ĐỊNH] filter cho đổi – Q-08 |
| TC_DMHD_FIL_005 | BR-05 | Danh mục HĐ – Filter Mã TP | Kiểm tra danh sách giá trị khi mở filter Mã TP | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ theo ĐD2(TPA) | 1. Mở filter Mã TP<br>2. Xem danh sách giá trị | KH02 (đang có HĐ của TPA, TPB, TPC) | Danh sách gồm 3 giá trị TPA, TPB, TPC; không có mã TP mà KH02 chưa từng mua | Low | Negative | EP | [GIẢ ĐỊNH] Q-08 |

### 4.6. Danh mục HĐ – Hộp cần xác nhận

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_XN_001 | BR-07 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra ẩn hộp "Hợp đồng cần xác nhận" khi không có HĐ chờ xác nhận | PC chung; đăng nhập KH04 (không có HĐ cần XN) | 1. Vào màn theo ĐD1 | KH04 (0 HĐ cần XN) | Không hiển thị hộp "Hợp đồng cần xác nhận" (không hiển thị kể cả dạng hộp rỗng hay "0 hợp đồng…"); vẫn hiển thị đủ card của KH04 | High | Negative | DT | DT – R1 |
| TC_DMHD_XN_002 | BR-07, BR-09 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra hộp "Hợp đồng cần xác nhận" khi có đúng 1 HĐ chờ xác nhận | PC chung; đăng nhập KH03 | 1. Vào màn theo ĐD1 | KH03 / HD31 | Hiển thị hộp; dòng phụ "1 hợp đồng đang chờ xác nhận lệnh"; có duy nhất 1 dòng: TPA – HD31 – "Xác nhận Hợp đồng" | High | Boundary | BVA | DT – R2; Q-14 |
| TC_DMHD_XN_003 | BR-09 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra số N khi có nhiều HĐ chờ xác nhận thuộc nhiều mã TP | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD1<br>2. Đọc dòng phụ và đếm số dòng trong hộp | KH02 (HD01, HD02, HD03) | Dòng phụ "3 hợp đồng đang chờ xác nhận lệnh"; hộp có 3 dòng HD01, HD02, HD03; N = số dòng | High | Positive | DT | DT – R3 |
| TC_DMHD_XN_004 | BR-09, BR-06 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra số N khi đang lọc theo Mã TP | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD2(TPA)<br>2. Đọc dòng phụ và các dòng trong hộp | KH02 / TPA (HD01, HD02 cần XN; HD03 thuộc TPB) | Dòng phụ "2 hợp đồng đang chờ xác nhận lệnh"; chỉ có 2 dòng HD01, HD02; không có HD03 | High | Positive | DT | DT – R4 |
| TC_DMHD_XN_005 | BR-07, BR-09 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra ẩn hộp khi mã TP đang lọc không có HĐ chờ xác nhận | PC chung; đăng nhập KH02 | 1. Vào màn theo ĐD2(TPC) | KH02 / TPC (chỉ HD08 – Hiệu lực); KH vẫn có 3 HĐ cần XN ở mã khác | Không hiển thị hộp "Hợp đồng cần xác nhận"; chỉ hiển thị card HD08 | Medium | Negative | DT | DT – R5 |
| TC_DMHD_XN_006 | BR-10 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra nội dung dòng trong hộp khi HĐ cần "Xác nhận Hợp đồng" | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Quan sát dòng của HD01 trong hộp | KH02 / HD01 | Dòng hiển thị Mã TP "TPA", Mã HĐ của HD01, nhãn "Xác nhận Hợp đồng" | Medium | Positive | EP |  |
| TC_DMHD_XN_007 | BR-10 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra nội dung dòng trong hộp khi HĐ cần "Xác nhận Phiếu lệnh" | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Quan sát dòng của HD02 trong hộp | KH02 / HD02 | Dòng hiển thị Mã TP "TPA", Mã HĐ của HD02, nhãn "Xác nhận Phiếu lệnh" | Medium | Positive | EP |  |
| TC_DMHD_XN_008 | BR-10, BR-07 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra hộp không chứa HĐ khi HĐ không ở trạng thái cần xác nhận | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Kiểm tra danh sách dòng trong hộp | KH02: HD04, HD08 (Hiệu lực), HD06 (Chờ xử lý), HD05, HD07 | Hộp không có dòng nào của HD04, HD05, HD06, HD07, HD08; các HĐ này chỉ có trong danh sách card | Medium | Negative | EP |  |
| TC_DMHD_XN_009 | BR-11 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra mở tab Xác nhận khi bấm dòng HĐ trong hộp | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Bấm dòng HD02 trong hộp | KH02 / HD02 | Mở Chi tiết HĐ của HD02 (Mã HĐ khớp); tab đang active = "Xác nhận" (không phải tab Tổng quan) | High | Positive | UC |  |
| TC_DMHD_XN_010 | BR-11, BR-09 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra mở tab Xác nhận khi bấm dòng HĐ trong hộp lúc đang lọc Mã TP | PC chung; đăng nhập KH02; ở màn Danh mục HĐ theo ĐD2(TPA) | 1. Bấm dòng HD01 trong hộp | KH02 / HD01 | Mở Chi tiết HĐ của HD01; tab active = "Xác nhận" | Medium | Positive | UC |  |
| TC_DMHD_XN_011 | BR-07, BR-13 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra ẩn hộp sau khi KH xác nhận xong HĐ chờ xác nhận duy nhất | PC chung; đăng nhập KH03; ở màn Danh mục HĐ (ĐD1) | 1. Bấm dòng HD31 trong hộp<br>2. Tại tab Xác nhận, hoàn tất xác nhận (bấm "Xác nhận", nhập OTP đúng)<br>3. Bấm Back về Danh mục HĐ | KH03 / HD31; OTP UAT | Hộp "Hợp đồng cần xác nhận" không còn hiển thị; chip của HD31 không còn là "Chờ xác nhận"; trạng thái HD31 trên BO SHBond đã cập nhật | High | Positive | ST | [GIẢ ĐỊNH] tự refresh – Q-17 |
| TC_DMHD_XN_012 | BR-09 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra số N giảm sau khi KH xác nhận xong 1 trong 3 HĐ | PC chung; đăng nhập KH02; ở màn Danh mục HĐ (ĐD1) | 1. Bấm dòng HD03 trong hộp<br>2. Hoàn tất xác nhận HD03<br>3. Bấm Back về Danh mục HĐ | KH02 / HD03 | Dòng phụ đổi thành "2 hợp đồng đang chờ xác nhận lệnh"; hộp chỉ còn HD01, HD02 | Medium | Positive | ST | [GIẢ ĐỊNH] Q-17 |
| TC_DMHD_XN_013 | BR-09, BR-10 | Danh mục HĐ – Hộp cần xác nhận | Kiểm tra số dòng trong hộp khi 1 HĐ cần cả xác nhận Hợp đồng và Phiếu lệnh | PC chung; đăng nhập KH08 | 1. Vào màn theo ĐD1<br>2. Đếm số dòng của HD81 trong hộp và đọc N | KH08 / HD81 | HD81 chỉ có 1 dòng (nhãn theo bước hiện tại: "Xác nhận Hợp đồng"); N = 1 | Medium | Boundary | EG | [GIẢ ĐỊNH] – Q-11 |

### 4.7. Danh mục HĐ – Bảo mật / Hệ thống

| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_SEC_001 | BR-01 | Danh mục HĐ – Bảo mật / Hệ thống | Kiểm tra API danh sách HĐ khi truyền mã KH của người khác | PC chung; đăng nhập KH02; có công cụ bắt request (Charles/Proxyman) | 1. Vào màn theo ĐD1, bắt request API lấy danh sách HĐ<br>2. Sửa tham số định danh KH/tài khoản sang của KH07<br>3. Gửi lại request bằng token của KH02 | Token KH02; mã TK của KH07 | API trả 401/403 hoặc danh sách rỗng; response không chứa HD71 hay bất kỳ HĐ nào của KH07 | High | Security | EG | [GIẢ ĐỊNH] endpoint chưa có tài liệu |
| TC_DMHD_SEC_002 | BR-04 | Danh mục HĐ – Bảo mật / Hệ thống | Kiểm tra API chi tiết HĐ khi truyền Mã HĐ của KH khác | PC chung; đăng nhập KH02; có công cụ bắt request | 1. Bấm card HD01, bắt request API chi tiết HĐ<br>2. Đổi Mã HĐ thành HD71 (của KH07)<br>3. Gửi lại request bằng token KH02 | Token KH02; HD71 | API trả 403/404; không trả dữ liệu HD71 | High | Security | EG | [GIẢ ĐỊNH] endpoint |
| TC_DMHD_SEC_003 | BR-01 | Danh mục HĐ – Bảo mật / Hệ thống | Kiểm tra màn Danh mục HĐ khi phiên đăng nhập đã hết hạn | PC chung; đăng nhập KH02; để app ở nền đến khi session hết hạn (hoặc revoke token trên UAT) | 1. Mở lại app<br>2. Vào tab Danh mục Hợp đồng | KH02 | App yêu cầu đăng nhập lại; không hiển thị dữ liệu HĐ cũ trong cache cho phiên đã hết hạn | Medium | Security | EG |  |
| TC_DMHD_SEC_004 | BR-01 | Danh mục HĐ – Bảo mật / Hệ thống | Kiểm tra màn Danh mục HĐ khi mất kết nối mạng | PC chung; đăng nhập KH02 | 1. Bật chế độ máy bay<br>2. Vào màn theo ĐD1 | KH02 | Hiển thị thông báo lỗi kết nối; không hiển thị trạng thái rỗng "Không có dữ liệu" (dễ khiến KH tưởng mình không có HĐ); app không crash | Medium | Negative | EG | [GIẢ ĐỊNH] message – Q-15 |
| TC_DMHD_SEC_006 | BR-01 | Danh mục HĐ – Bảo mật / Hệ thống | Kiểm tra tải lại danh sách khi mạng được khôi phục | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ với thông báo lỗi mất mạng (sau TC_DMHD_SEC_004) | 1. Tắt chế độ máy bay<br>2. Kéo để làm mới (pull-to-refresh) | KH02 | Tải đủ 8 card HD01–HD08 theo thứ tự ở TC_DMHD_LIST_001; hộp cần xác nhận hiển thị N = 3; không còn thông báo lỗi | Medium | Negative | EG | [GIẢ ĐỊNH] có pull-to-refresh – Q-17 |
| TC_DMHD_SEC_005 | BR-03, BR-13 | Danh mục HĐ – Bảo mật / Hệ thống | Kiểm tra HĐ mới xuất hiện khi KH vừa đặt mua thành công | PC chung; đăng nhập KH02; đang ở màn Danh mục HĐ (ĐD1) | 1. Đặt mua TPA, SL 100, kỳ hạn 3 tháng, đến khi thành công<br>2. Quay lại Danh mục HĐ<br>3. Kéo để làm mới | KH02 / TPA; SL 100; 3 tháng | HĐ mới nằm ở đầu danh sách; chip "Chờ xử lý"; Ngày mua = ngày hôm nay; SL = 100; Thời gian đầu tư = "3 tháng" | High | Integration | ST | [GIẢ ĐỊNH] Q-17 |

---

## B5 – TỰ REVIEW & HOÀN THIỆN

### 1. RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ | Ghi chú |
|---|---|---|---|---|---|
| BR-01 | Đường dẫn 1 | 8 | NAV_001, LIST_009, LIST_010, LIST_011, SEC_001, SEC_003, SEC_004, SEC_006 | Đủ |  |
| BR-02 | Đường dẫn 2 | 1 | NAV_002 | Đủ |  |
| BR-03 | Sắp xếp mới → cũ | 6 | LIST_001, LIST_002, LIST_010, LIST_011, FIL_002, SEC_005 | Đủ |  |
| BR-04 | Card → Chi tiết HĐ (3 tab) | 4 | NAV_003, NAV_004, NAV_005, SEC_002 | Đủ |  |
| BR-05 | Hiện filter Mã TP (ĐD2) | 5 | UI_003, NAV_006, FIL_003, FIL_004, FIL_005 | Đủ |  |
| BR-06 | Chỉ HĐ của mã TP (ĐD2) | 6 | NAV_004, NAV_006, FIL_001, FIL_002, FIL_004, XN_004 | Đủ |  |
| BR-07 | Hộp chỉ hiện khi ≥1 HĐ cần XN | 5 | XN_001, XN_002, XN_005, XN_008, XN_011 | Đủ |  |
| BR-08 | Bố cục hộp | 1 | UI_002 | Đủ |  |
| BR-09 | N theo bộ lọc hiện tại | 7 | XN_002, XN_003, XN_004, XN_005, XN_010, XN_012, XN_013 | Đủ |  |
| BR-10 | Dòng hộp: Mã TP, Mã HĐ, 2 trạng thái | 4 | XN_006, XN_007, XN_008, XN_013 | Đủ |  |
| BR-11 | Dòng hộp → tab Xác nhận | 2 | XN_009, XN_010 | Đủ |  |
| BR-12 | Mã HĐ duy nhất | 1 | LIST_012 | Đủ |  |
| BR-13 | Status chip 5 giá trị | 9 | UI_001, LIST_003, LIST_004, LIST_005, LIST_006, LIST_007, LIST_008, XN_011, SEC_005 | Đủ | Chưa có định nghĩa từng trạng thái – Q-13 |
| BR-14 | Ngày mua | 3 | UI_001, UI_004, CARD_001 | Đủ |  |
| BR-15 | Ngày đáo hạn | 4 | UI_001, UI_004, CARD_002, CARD_003 | Đủ | Expected phụ thuộc Q&A (xem Note của TC) |
| BR-16 | Thời gian đầu tư | 3 | UI_001, CARD_004, CARD_005 | Đủ |  |
| BR-17 | Số lượng TP | 2 | UI_001, CARD_006 | Đủ |  |
| BR-18 | SL khả dụng | 4 | UI_001, CARD_007, CARD_008, CARD_009 | Đủ | Expected phụ thuộc Q&A (xem Note của TC) |
| BR-19 | Tổng tiền đầu tư | 5 | UI_001, UI_005, CARD_010, CARD_011, CARD_012 | Đủ | Expected phụ thuộc Q&A (xem Note của TC) |
| BR-20 | Tổng tiền nhận về | 5 | UI_001, UI_005, CARD_013, CARD_014, CARD_015 | Đủ | Expected phụ thuộc Q&A (xem Note của TC) |
| BR-21 | Card khớp Chi tiết HĐ | 2 | NAV_003, CARD_016 | Đủ |  |

→ Độ phủ yêu cầu: **21/21 BR = 100%**. (Cột TC IDs bỏ tiền tố `TC_DMHD_`.)

### 2. Checklist review (file 05)
| Nhóm | Tiêu chí | Kết quả | Ghi chú |
|---|---|---|---|
| A. Độ phủ | 100% BR có ≥ 1 TC | Đạt | 21/21 BR |
| A | Field có đủ hợp lệ / không hợp lệ / biên | Đạt | Màn chỉ để xem: biên áp cho SL (0, 1, = SL TP), N (0, 1, > 1), ngày cuối tháng |
| A | Luồng chính + phụ + ngoại lệ | Đạt | ĐD1, ĐD2, hộp → tab Xác nhận; rỗng, mất mạng, session hết hạn |
| A | Phân quyền theo role | Đạt (một phần) | Tài liệu chỉ có 1 role (NĐT); đã thêm case IDOR SEC_001, SEC_002 |
| A | DT có TC cho từng rule; ST có chuyển trạng thái | Đạt | R1–R5 → XN_001, XN_002, XN_003, XN_004, XN_005; ST → XN_011, XN_012, SEC_005 |
| A | Error guessing | Đạt | Double-tap, back giữ filter, cuộn ≥ 30 HĐ, mất mạng, trạng thái lạ |
| B. Chất lượng | Đủ 12 cột, ID đúng format, không trùng | Đạt | Đã kiểm bằng script |
| B | 1 TC = 1 mục tiêu | Đạt (sau khi sửa) | Tách UI_004 thành UI_004 + UI_005; tách SEC_004 thành SEC_004 + SEC_006 |
| B | Title "Kiểm tra … khi …" | Đạt (sau khi sửa) | Sửa 3 title: UI_004, LIST_012, FIL_005 |
| B | Test data cụ thể | Đạt | Theo bộ KH01–KH08; mã TP/HĐ là tên mẫu, khi chạy map sang mã UAT |
| B | Expected đo được, không dùng từ mơ hồ | Đạt (sau khi sửa) | Sửa XN_001 ("hiển thị bình thường"), LIST_010, FIL_005, XN_002 |
| B | Negative kiểm cả báo lỗi và không thực hiện hành động | Đạt | Màn chỉ xem: negative kiểm "không hiển thị sai" + "không lộ / không lẫn dữ liệu" |
| B | Message lỗi nguyên văn hoặc [GIẢ ĐỊNH] | Đạt | Tài liệu không có message lỗi; empty state và lỗi mạng đều gắn [GIẢ ĐỊNH] (Q-09, Q-15) |
| C. Nhất quán | Không có TC trùng | Đạt | |
| C | Priority hợp lý | Đạt | Tiền (CARD_008, CARD_010, CARD_011, CARD_013, CARD_014), hộp xác nhận, IDOR = High |
| C | Thuật ngữ thống nhất với tài liệu | Đạt | Dùng đúng "Hợp đồng cần xác nhận", "Xác nhận Hợp đồng", "Xác nhận Phiếu lệnh", 5 tên status chip |
| C | Thứ tự UI → Positive → … → Security | Đạt | |

### 3. Các lỗi đã sửa trong B5
| TC ID | Vấn đề | Loại | Đã sửa |
|---|---|---|---|
| TC_DMHD_UI_004 | Gộp 2 mục tiêu (định dạng ngày + định dạng tiền) | Sai format | Tách thành UI_004 (ngày) và UI_005 (tiền) |
| TC_DMHD_SEC_004 | Gộp mất mạng + khôi phục | Sai format | Tách thành SEC_004 và SEC_006 |
| TC_DMHD_LIST_012, FIL_005 | Title không theo mẫu "… khi …" | Sai format | Viết lại title |
| TC_DMHD_XN_001 | Expected có cụm "hiển thị bình thường" | Mơ hồ | Đổi thành "vẫn hiển thị đủ card của KH04" |
| TC_DMHD_XN_013 | Dùng chung KH03 với XN_002 nên dữ liệu mâu thuẫn | Sai dữ liệu | Thêm bộ KH08 / HD81 |
| TC_DMHD_XN_001 | KH04 chưa ghi "không có HĐ cần XN" | Thiếu dữ liệu | Bổ sung mô tả KH04 ở B2 |
| TC_DMHD_NAV_003 | Chưa rõ tab mặc định | Thiếu | Thêm Q-18 |

### 4. Thống kê
| Chỉ số | Giá trị | Mục tiêu | Nhận xét |
|---|---|---|---|
| Tổng TC | **63** | – | |
| Requirement coverage | **21/21 BR (100%)** | 100% | |
| High / Medium / Low | 20 (32%) / 34 (54%) / 9 (14%) | 25 / 50 / 25 | High cao hơn mục tiêu do nhiều field liên quan tiền. Low thấp vì layout chưa có mockup (Q-16) |
| Positive : Non-positive | 31 : 32 (49% : 51%) | 30 : 70 | Màn chỉ xem, không có input nên ít negative hơn. Phần negative tập trung vào dữ liệu sai, bộ lọc và bảo mật |
| Theo Type | UI 5 · Positive 31 · Negative 13 · Boundary 9 · Integration 2 · Security 3 | – | |
| Theo Technique | EP 25 · EG 14 · UC 10 · BVA 7 · DT 4 · ST 3 | – | |
| TC có [GIẢ ĐỊNH] | 31 / 63 | – | Sẽ chốt lại khi BA trả lời Q&A |
| Q&A đang mở | **18** (Blocker 0 · Major 9 · Minor 9) | Blocker = 0 | Đạt điều kiện để bắt đầu test |

### 5. Q&A cần BA trả lời trước khi chạy
Ưu tiên theo mức ảnh hưởng tới expected:
1. **Q-04, Q-05**: công thức Lãi đầu tư dự kiến và phí, ảnh hưởng toàn bộ số tiền (CARD_010 → CARD_015).
2. **Q-01, Q-11**: mapping trạng thái ↔ hộp cần xác nhận (LIST_005, XN_013).
3. **Q-03**: Ngày đáo hạn khi ngày mua là cuối tháng (CARD_003).
4. **Q-06**: định nghĩa SL khả dụng (CARD_007 → CARD_009, CARD_011).
5. **Q-02, Q-08, Q-17**: tiêu chí sắp xếp, filter Mã TP, cơ chế refresh dữ liệu.
