# BỘ TEST – MÀN HÌNH DANH MỤC HỢP ĐỒNG (TPRL – SHSmart APP)

- **Nguồn:** [DOC-HD] `2.3. Danh mục Hợp đồng` · [BRD] `[BRD] TPRL – SHSmart APP` (bản cập nhật 10 Nov 2026), mục 2.2.1.2.2, 2.2.2, 2.2.3, 2.3.1, 2.3.2.1, 2.3.3, 2.4, 2.6.1, 2.6.2
- **Pipeline:** /FULL (B1 → B5). Không có Q&A mức Blocker → chạy liền mạch.
- **Ngày lập:** 18/11/2026

---

# B1 – PHÂN TÍCH YÊU CẦU

## 1. Tổng quan
- **Mục tiêu:** NĐT xem danh sách các hợp đồng (HĐ) trái phiếu sBond đang/đã nắm giữ, theo dõi trạng thái HĐ, nhận biết HĐ cần xác nhận và điều hướng sang Chi tiết HĐ.
- **Role:** Khách hàng (NĐT) đã đăng nhập app SHSmart, có HĐ trái phiếu sBond. Không có role quản trị trên màn này.
- **Hệ thống liên quan:** Back-end S-Bond (trạng thái lệnh Pre-matched / Matched / Rejected), sub88, VSD/HNX (qua nghiệp vụ).

## 2. Feature / Thành phần màn hình
| # | Thành phần | Nguồn |
|---|---|---|
| F1 | Điều hướng vào màn (2 đường dẫn) | DOC-HD §1; BRD 2.3.1, 2.3.2.1 |
| F2 | Hộp "Hợp đồng cần xác nhận" + các dòng HĐ cần xác nhận + button | DOC-HD §1, §2; BRD 2.3.3 |
| F3 | Filter riêng theo mã TP (khi vào từ Danh mục Trái phiếu) | DOC-HD §1; BRD 2.3.3 |
| F4 | Danh sách card HĐ (thông tin, sắp xếp, status-chip) | DOC-HD §2; BRD 2.3.3 |
| F5 | Điều hướng card → Chi tiết HĐ (tab Tổng quan / Dòng tiền / Xác nhận) | DOC-HD §1; BRD 2.3.3 |
| F6 | Vòng đời trạng thái HĐ hiển thị trên danh sách | BRD 2.2.1.2.1, 2.2.1.2.2, 2.2.2.1, 2.2.3, 2.6.1 |
| F7 | Cập nhật SL khả dụng khi KH chuyển nhượng TP tại quầy | BRD 2.6.2 |

## 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Truy cập qua: Trái phiếu SHS → tab Danh mục nắm giữ → tab Danh mục Hợp đồng | DOC-HD §1; BRD 2.3.1 | Logic |
| BR-02 | Truy cập qua: Trái phiếu SHS → tab Danh mục nắm giữ → chọn card mã TP tại Danh mục Trái phiếu (button ">") → Danh mục HĐ tự filter theo mã TP đó | DOC-HD §1; BRD 2.3.2.1 | Logic |
| BR-03 | Khi vào từ Danh mục Trái phiếu: hiển thị thêm filter riêng cho mã TP; chỉ hiển thị HĐ thuộc mã TP đó | DOC-HD §1; BRD 2.3.3 | Logic |
| BR-04 | Danh sách sắp xếp HĐ mới nhất → cũ nhất (BRD: theo Ngày tạo HĐ) | DOC-HD §1; BRD 2.3.3 | Logic |
| BR-05 | Hộp "Hợp đồng cần xác nhận" chỉ hiển thị khi có ≥ 1 HĐ đang chờ xác nhận | DOC-HD §1, §2; BRD 2.3.3 | Logic/UI |
| BR-06 | Hộp gồm icon cảnh báo, tiêu đề tĩnh, dòng phụ "N hợp đồng đang chờ xác nhận lệnh"; N = số HĐ cần xác nhận, đã áp filter mã TP hiện tại (nếu có) | DOC-HD §2 | Logic/UI |
| BR-07 | Mỗi dòng trong hộp hiển thị Mã TP, Mã HĐ, 1 trong 2 nhãn: "Xác nhận Hợp đồng", "Xác nhận Phiếu lệnh" | DOC-HD §2 | UI/Logic |
| BR-08 | Bấm vào dòng trong hộp → mở Chi tiết HĐ, nhảy thẳng vào tab "Xác nhận" | DOC-HD §2 | Logic |
| BR-09 | Button của hộp (DOC-HD: "Xác nhận"; BRD: "Kiểm tra") → điều hướng tab "Xác nhận" | DOC-HD §1; BRD 2.3.3 | Logic |
| BR-10 | Card HĐ hiển thị: Mã HĐ, status-chip, Ngày mua, Ngày đáo hạn, Thời gian đầu tư (tháng), Số lượng TP, SL khả dụng, Tổng tiền đầu tư, Tổng tiền nhận về | DOC-HD §2 | UI |
| BR-11 | Status-chip chỉ nhận 1 trong 5 giá trị: Chờ xử lý, Hiệu lực, Chờ xác nhận, Hết hiệu lực, Hoàn thiện | DOC-HD §2; BRD 2.3.3 | UI/Logic |
| BR-12 | Tổng tiền đầu tư = Giá mua (theo dữ liệu mã TP) × Số lượng TP của HĐ | DOC-HD §2 | Logic (tính toán) |
| BR-13 | Tổng tiền nhận về = Tổng tiền đầu tư + Lãi đầu tư dự kiến (lãi theo lãi suất coupon của mã TP) | DOC-HD §2 | Logic (tính toán) |
| BR-14 | Ngày đáo hạn tính theo kỳ hạn NĐT chọn khi đặt mua; Thời gian đầu tư = số tháng kỳ hạn của riêng HĐ | DOC-HD §2 | Logic |
| BR-15 | Bấm card HĐ → màn Chi tiết HĐ, tab mặc định "Tổng quan"; có các tab Tổng quan, Dòng tiền (dự kiến), Xác nhận | DOC-HD §1; BRD 2.3.3 | Logic |
| BR-16 | Đặt mua thành công (lệnh Pre-matched) → HĐ xuất hiện trong danh sách, trạng thái "Chờ xử lý" | BRD 2.2.1.2.1 | Tích hợp |
| BR-17 | Không khớp lệnh (Rejected) → HĐ KHÔNG còn hiển thị trong danh sách | BRD 2.2.1.2.2 | Tích hợp |
| BR-18 | Xác nhận thanh toán thành công (Matched) → HĐ trạng thái "Hiệu lực" | BRD 2.2.1.2.2 | Tích hợp |
| BR-19 | T+3 (xác nhận HĐ Leg 2) / E-5 (xác nhận Phiếu lệnh đặt bán) → HĐ chuyển sang trạng thái cần xác nhận; hộp "Hợp đồng cần xác nhận" xuất hiện | BRD 2.2.2.1 | Tích hợp |
| BR-20 | COT ngày E: thiếu biểu mẫu HOẶC số dư HĐ không đủ bán lại → "Hết hiệu lực" | BRD 2.2.3 | Tích hợp |
| BR-21 | COT ngày E: đủ biểu mẫu VÀ đủ số dư, nghiệp vụ thanh lý → "Hoàn thành" (DOC-HD: "Hoàn thiện") | BRD 2.2.3 | Tích hợp |
| BR-22 | Nghiệp vụ hủy giao dịch sau khi đã xác nhận thanh toán → HĐ KHÔNG còn hiển thị | BRD 2.6.1 | Tích hợp |
| BR-23 | Loại xác nhận hiển thị phụ thuộc trạng thái xác nhận HĐ Leg 2 × Phiếu lệnh đặt bán (bảng quyết định BRD 2.2.2.2) | BRD 2.2.2.2 | Logic |
| BR-24 | Chuyển nhượng TP tại quầy: SL khả dụng tăng/giảm. TĂNG: ưu tiên HĐ đáo hạn gần → xa; cùng ngày đáo hạn → HĐ có KL thiếu cao nhất. GIẢM: ưu tiên HĐ đáo hạn xa → gần; cùng ngày đáo hạn → HĐ có KL thấp nhất | BRD 2.6.2 | Logic |
| BR-25 | [GIẢ ĐỊNH – ngầm định bảo mật] KH chỉ xem được HĐ của chính tài khoản mình (UI và API) | Không có trong tài liệu | Phân quyền |

## 4. Field list (card HĐ & hộp cần xác nhận)
| Field | Kiểu | Bắt buộc hiển thị | Ràng buộc / Công thức | Mặc định | Message lỗi |
|---|---|---|---|---|---|
| Mã hợp đồng | Text | Có | Định danh duy nhất | – | – |
| Status-chip | Enum | Có | 5 giá trị BR-11 | – | – |
| Ngày mua | Date | Có | Ngày xác lập HĐ = ngày giao dịch mua [GIẢ ĐỊNH dd/mm/yyyy – Q-11] | – | – |
| Ngày đáo hạn | Date | Có | Theo kỳ hạn NĐT chọn khi đặt mua | – | – |
| Thời gian đầu tư | Số (tháng) | Có | Số tháng kỳ hạn của HĐ | – | – |
| Số lượng Trái phiếu | Số nguyên | Có | KL TP trong HĐ | – | – |
| SL khả dụng | Số nguyên | Có | KL còn lại mapping với HĐ; biến động theo BR-24 | – | – |
| Tổng tiền đầu tư | Tiền | Có | Giá mua × SL | – | – |
| Tổng tiền nhận về | Tiền | Có | Tổng tiền đầu tư + Lãi dự kiến (coupon) | – | – |
| Hộp: dòng phụ | Text động | Khi có HĐ cần XN | "N hợp đồng đang chờ xác nhận lệnh" | – | – |
| Hộp: nhãn dòng | Enum | Có | "Xác nhận Hợp đồng" / "Xác nhận Phiếu lệnh" | – | – |
| Filter mã TP | Chip/Filter | Khi vào từ Danh mục TP | = mã TP của card đã chọn | – | – |

> Màn hình chỉ hiển thị, không có field nhập liệu → không áp dụng validation input; EP/BVA áp dụng cho dữ liệu hiển thị (số lượng, tiền, N).

## 5. Q&A
| Q ID | Câu hỏi | Lý do hỏi | Mức | Giả định đề xuất (dùng trong TC) |
|---|---|---|---|---|
| Q-01 | Danh sách field trên card khác nhau: DOC-HD (Ngày mua, SL TP, SL khả dụng, Tổng tiền nhận về) vs BRD 2.3.3 (Lợi suất, Tổ chức phát hành, Lợi tức dự kiến, không có Ngày mua/SL). Bản nào là chốt? | Ảnh hưởng toàn bộ TC hiển thị card | Major | Theo DOC-HD (tài liệu màn hình). TC field BRD đánh dấu [GIẢ ĐỊNH] |
| Q-02 | HĐ cần xác nhận hiển thị chip "Chờ xác nhận" (DOC-HD, BRD 2.3.3.3) hay "Hiệu lực" + dòng "Hợp đồng có hồ sơ cần xác nhận" (BRD 2.2.2.1)? | Mâu thuẫn trực tiếp, ảnh hưởng expected trạng thái | Major | Chip "Chờ xác nhận" |
| Q-03 | Trạng thái cuối là "Hoàn thiện" (DOC-HD) hay "Hoàn thành" (BRD 2.2.3)? | Text chip | Minor | "Hoàn thiện" |
| Q-04 | Button trong hộp tên "Xác nhận" (DOC-HD) hay "Kiểm tra" (BRD)? Khi có nhiều HĐ cần XN, button đưa tới tab Xác nhận của HĐ nào, hay tới màn "Xác nhận" (BRD 2.4)? | Expected điều hướng không xác định | Major | Button "Kiểm tra" → màn Xác nhận (BRD 2.4) liệt kê HĐ cần XN |
| Q-05 | HĐ cần xác nhận cả HĐ Leg 2 và Phiếu lệnh cùng lúc → hộp hiển thị 1 dòng hay 2 dòng? Nhãn nào? N đếm theo HĐ hay theo biểu mẫu? (BRD còn dấu ":question:" ở dòng Leg2 cần XN) | Ảnh hưởng N và nhãn | Major | 1 dòng/HĐ, nhãn "Xác nhận Hợp đồng"; N đếm theo HĐ |
| Q-06 | Công thức Lãi đầu tư dự kiến: dùng lãi suất coupon (DOC-HD) hay Lãi suất HĐ/Lợi suất (BRD)? Cơ sở ngày (360/365, thực tế/365), làm tròn thế nào? | Không tính tay được expected | Major | So khớp với giá trị BE/S-Bond; BA cung cấp bảng số liệu mẫu |
| Q-07 | Tổng tiền đầu tư có gồm phí giao dịch không? (Đặt mua phong tỏa Giá × SL × (Phí suất + 1)) | Sai lệch số tiền hiển thị | Minor | Không gồm phí (theo công thức DOC-HD) |
| Q-08 | "Mới nhất" theo thời điểm tạo HĐ hay Ngày mua? Tie-break khi cùng thời điểm? | Kết quả sắp xếp | Minor | Theo thời điểm tạo HĐ giảm dần |
| Q-09 | Empty state khi KH không có HĐ / filter mã TP không còn HĐ: hiển thị text gì? | Chưa có trong tài liệu | Minor | Hiển thị trạng thái rỗng, không lỗi; text chờ BA |
| Q-10 | Danh mục HĐ có nút "Filter" chung (BRD 10 Nov "bổ sung các trường thông tin Filter", link "Tại đây" không có trong bản export)? Filter mã TP có xóa được không, có giữ khi quay lại? | Phạm vi filter chưa rõ | Major | Chỉ test filter mã TP; Filter chung để Out-of-scope chờ tài liệu |
| Q-11 | Định dạng hiển thị: ngày (dd/mm/yyyy?), tiền (phân cách hàng nghìn, đơn vị "đ"/"VND"), thời gian đầu tư ("N tháng")? | Expected UI | Minor | dd/mm/yyyy; dấu "." phân cách hàng nghìn; "N tháng" |
| Q-12 | Danh sách nhiều HĐ: có phân trang / lazy-load không? | Hiệu năng, hiển thị đủ | Minor | Cuộn hiển thị đủ toàn bộ HĐ |
| Q-13 | HĐ "Chờ xử lý" (chưa lưu ký TP): SL khả dụng hiển thị bao nhiêu? Bấm card có vào được Chi tiết? | Expected dữ liệu | Major | SL khả dụng = 0; vẫn vào được Chi tiết |
| Q-14 | Trạng thái HĐ đổi khi KH đang mở màn: tự cập nhật hay cần kéo refresh / vào lại? | Expected realtime | Minor | Cập nhật khi vào lại màn hoặc pull-to-refresh |
| Q-15 | Sau khi KH xác nhận xong toàn bộ biểu mẫu, HĐ trở về chip nào? | Expected chuyển trạng thái | Major | Rời hộp; chip "Hiệu lực" |
| Q-16 | BR-24: khi KL tăng vượt KL thiếu của HĐ ưu tiên thì phần dư phân bổ thế nào? "KL thấp nhất" khi giảm là Số lượng TP của HĐ hay SL khả dụng? | Không tính được expected | Major | Phần dư chuyển HĐ ưu tiên kế tiếp; "KL" = SL khả dụng |
| Q-17 | Danh mục HĐ có bao gồm HĐ Trái phiếu linh hoạt / HĐ mua Offline? (BRD 2.3.3.1 nhắc luồng Online/Offline) | Phạm vi dữ liệu | Minor | Chỉ HĐ sBond; HĐ Offline hiển thị như Online |

## 6. Rủi ro nghiệp vụ chính (Top 5)
1. **Hiển thị sai số tiền** (Tổng tiền đầu tư / nhận về) → KH khiếu nại, rủi ro pháp lý (Q-06, Q-07 chưa chốt).
2. **Trạng thái HĐ lệch với S-Bond** (chưa cập nhật sau Rejected / Hủy / COT ngày E) → KH tưởng còn tài sản hoặc mất tài sản.
3. **Bỏ sót HĐ cần xác nhận** (hộp không hiện, N sai) → KH không xác nhận kịp trước ngày E → HĐ "Hết hiệu lực", mất quyền bán lại.
4. **SL khả dụng phân bổ sai** khi chuyển nhượng tại quầy → HĐ đủ KL bị đánh giá thiếu khi tất toán.
5. **Lộ HĐ của KH khác** qua API (IDOR) → rủi ro bảo mật dữ liệu tài chính.

---

# B2 – CHIẾN LƯỢC TEST

## 1. Phạm vi
**In-scope**
- Màn Danh mục Hợp đồng trên app SHSmart: điều hướng vào, hộp cần xác nhận, filter mã TP, card HĐ, sắp xếp, status-chip, tính toán số tiền, điều hướng sang Chi tiết HĐ.
- Hiển thị trên danh sách theo các sự kiện BE: Pre-matched, Rejected, Matched, T+3/E-5, COT ngày E, Hủy giao dịch, chuyển nhượng tại quầy.
- Bảo mật cơ bản: chỉ thấy HĐ của mình (UI + API).

**Out-of-scope**
- Nội dung chi tiết các tab Tổng quan / Dòng tiền / Xác nhận (chỉ kiểm điều hướng).
- Luồng đặt mua, OTP, xác nhận biểu mẫu (dùng làm bước chuẩn bị dữ liệu).
- Nội dung Noti/Email (Bond_01…Bond_08), Trái phiếu linh hoạt, Lịch sử giao dịch.
- Nút "Filter" chung (chờ Q-10). Hiệu năng/tải.

## 2. Loại test
| Loại | Áp dụng |
|---|---|
| Functional (UI + Logic) | Có – trọng tâm |
| Integration (BE S-Bond → App) | Có – vòng đời trạng thái |
| Data/Calculation | Có – Tổng tiền đầu tư, Tổng tiền nhận về |
| Security cơ bản | Có – IDOR API, session hết hạn |
| Compatibility | Nhẹ – 1 thiết bị iOS + 1 Android |
| API | Bổ trợ cho Security (endpoint lấy danh sách HĐ – chờ tài liệu API) |

## 3. Kỹ thuật & ước lượng
| BR ID | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC (ước) |
|---|---|---|---|---|
| BR-01, BR-02 | UC | EG | High | 3 |
| BR-03 | UC | EP, EG | Medium | 4 |
| BR-04 | EP | EG | Medium | 2 |
| BR-05, BR-06 | EP, BVA (N = 0/1/2) | DT (có/không filter) | High | 7 |
| BR-07, BR-23 | DT | – | High | 6 |
| BR-08, BR-09 | UC | EG | High | 3 |
| BR-10, BR-11, BR-14 | EP | EG | Medium/Low | 8 |
| BR-12, BR-13 | EP, BVA | EG (làm tròn, số lớn) | High | 5 |
| BR-15 | UC | EG (double-tap, back) | Medium | 3 |
| BR-16…BR-22 | ST | EG | High | 10 |
| BR-24 | DT | BVA | High | 5 |
| BR-25 | EG (Security) | – | High | 3 |

## 4. Môi trường & dữ liệu
- **Môi trường:** UAT SHSmart, build mới nhất; truy cập được S-Bond UAT (hoặc đội BE hỗ trợ chuyển trạng thái lệnh / chạy job T+3, E-5, COT ngày E).
- **Thiết bị:** iPhone (iOS bản hiện hành) + Android (bản hiện hành).
- **Tài khoản:**

| TK | Mô tả |
|---|---|
| KH-A | NĐTCN, có sub88, có bộ dữ liệu DS-A |
| KH-B | NĐTCN, có sub88, chưa có HĐ nào |
| KH-C | Có HĐ nhưng không HĐ nào cần xác nhận (chỉ "Hiệu lực") |
| KH-D | Tài khoản khác, có HĐ riêng (dùng test IDOR) |
| KH-E | Có 3 HĐ cùng mã TP (DS-E) – test chuyển nhượng tại quầy |

- **DS-A (KH-A)** – chuẩn bị qua S-Bond/BE. Mã TP mẫu, mã HĐ thật thay theo môi trường:

| Ký hiệu HĐ | Mã TP | Thời điểm tạo | Trạng thái | Cần XN |
|---|---|---|---|---|
| HĐ-A01 | SBOND2601 | 10/11/2026 09:00 | Chờ xử lý | – |
| HĐ-A02 | SBOND2601 | 05/11/2026 10:00 | Hiệu lực | – |
| HĐ-A03 | SBOND2602 | 01/11/2026 14:00 | Chờ xác nhận | HĐ Leg 2 |
| HĐ-A04 | SBOND2602 | 20/10/2026 09:30 | Chờ xác nhận | Phiếu lệnh (Leg 2 đã XN) |
| HĐ-A05 | SBOND2601 | 01/08/2026 08:00 | Hết hiệu lực | – |
| HĐ-A06 | SBOND2602 | 01/07/2026 08:00 | Hoàn thiện | – |

- **DS-E (KH-E), mã TP SBOND2603:**

| HĐ | Ngày đáo hạn | Số lượng TP | SL khả dụng | KL thiếu |
|---|---|---|---|---|
| HĐ-E1 | 01/03/2027 | 1.000 | 800 | 200 |
| HĐ-E2 | 01/06/2027 | 1.000 | 500 | 500 |
| HĐ-E3 | 01/06/2027 | 2.000 | 1.200 | 800 |

## 5. Exit criteria
- 100% TC High đã chạy, Pass ≥ 95%; không còn bug Critical/Major mở.
- 100% BR có TC Pass hoặc bug đã được chấp nhận.
- Q&A Major (Q-01, Q-02, Q-04, Q-05, Q-06, Q-13, Q-15, Q-16) được BA trả lời, TC [GIẢ ĐỊNH] đã cập nhật.

---

# B3 – TEST CONDITION

## 1. Điều hướng & filter (UC)
| Mã | Condition |
|---|---|
| TCO-01-01 | Vào màn qua tab Danh mục nắm giữ → Danh mục Hợp đồng: hiển thị toàn bộ HĐ, không có filter mã TP |
| TCO-02-01 | Vào màn qua card mã TP (">") ở Danh mục Trái phiếu: có filter mã TP |
| TCO-03-01 | Filter = SBOND2601: chỉ HĐ của SBOND2601 |
| TCO-03-02 | Filter = SBOND2602: chỉ HĐ của SBOND2602 (đổi mã TP) |
| TCO-03-03 | Vào lại qua tab Danh mục HĐ sau khi đã vào bằng filter: không còn filter |
| TCO-03-04 | Mã TP chỉ có HĐ bị Rejected/Hủy (không hiển thị) → danh sách rỗng [Q-09] |

## 2. Hộp "Hợp đồng cần xác nhận" (EP/BVA + DT)
| Mã | N (số HĐ cần XN trong phạm vi) | Có filter | Kết quả |
|---|---|---|---|
| TCO-05-01 | 0 (KH-C) | Không | Không hiển thị hộp |
| TCO-05-02 | 1 | Có (SBOND2602 → giảm còn 1 bằng cách XN HĐ-A04 trước) | Hiện hộp, "1 hợp đồng đang chờ xác nhận lệnh" |
| TCO-05-03 | 2 (KH-A) | Không | Hiện hộp, "2 hợp đồng…" |
| TCO-06-01 | 2 toàn TK, filter SBOND2601 → 0 trong phạm vi | Có | Không hiển thị hộp |
| TCO-06-02 | 2 toàn TK, filter SBOND2602 → 2 | Có | "2 hợp đồng…" |
| TCO-06-03 | Thành phần tĩnh: icon cảnh báo + tiêu đề "Hợp đồng cần xác nhận" | – | Hiển thị đủ |
| TCO-05-04 | HĐ cuối cùng được xác nhận xong → N từ 1 về 0 | – | Hộp biến mất |

## 3. Bảng quyết định loại xác nhận (BR-23, BRD 2.2.2.2)
| Rule | R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|---|
| HĐ Leg 2 | Chưa cần XN | Cần XN | Đã XN | Cần XN | Đã XN | Đã XN |
| Phiếu lệnh đặt bán | Chưa cần XN | Chưa cần XN | Chưa cần XN | Cần XN | Cần XN | Đã XN |
| **HĐ trong hộp?** | Không | Có | Không | Có | Có | Không |
| **Nhãn dòng** | – | Xác nhận Hợp đồng | – | [Q-05] Xác nhận Hợp đồng | Xác nhận Phiếu lệnh | – |

## 4. Card & tính toán (EP/BVA)
| Field | Lớp hợp lệ | Biên / EG |
|---|---|---|
| Tổng tiền đầu tư | Giá 102.500 × SL 1.000 = 102.500.000 | SL = 1 (min); số lớn 100.000 × 500.000 = 50.000.000.000; giá lẻ 100.333 × 3 = 300.999 |
| Tổng tiền nhận về | 102.500.000 + Lãi BE (vd 4.500.000) = 107.000.000 | Lãi dự kiến = 0 → bằng Tổng tiền đầu tư |
| Status-chip | 5 giá trị BR-11 | Giá trị ngoài danh sách (Rejected, Pre-matched thô) không được hiển thị |
| Mã HĐ | Duy nhất | Mã dài → không vỡ layout [EG] |
| Thời gian đầu tư | 3 tháng / 6 tháng | khớp Ngày mua → Ngày đáo hạn |
| SL khả dụng | ≤ Số lượng TP | HĐ Chờ xử lý → 0 [Q-13] |

## 5. State transition trạng thái HĐ (ST)
| Từ \ Sự kiện | Pre-matched (đặt mua OK) | Rejected | Matched (XNTT) | T+3 / E-5 | KH xác nhận xong | COT E – thiếu | COT E – đủ | Hủy sau XNTT |
|---|---|---|---|---|---|---|---|---|
| (chưa có) | Chờ xử lý | ✖ | ✖ | ✖ | ✖ | ✖ | ✖ | ✖ |
| Chờ xử lý | ✖ | Ẩn khỏi DS | Hiệu lực | ✖ | ✖ | ✖ | ✖ | ✖ |
| Hiệu lực | ✖ | ✖ | ✖ | Chờ xác nhận | ✖ | Hết hiệu lực | Hoàn thiện | Ẩn khỏi DS |
| Chờ xác nhận | ✖ | ✖ | ✖ | (giữ) | Hiệu lực [Q-15] | Hết hiệu lực | ✖ (thiếu biểu mẫu) | – |
| Hết hiệu lực / Hoàn thiện | ✖ | ✖ | ✖ | ✖ | ✖ | ✖ | ✖ | ✖ |

→ Condition: TCO-ST-01…08 (các ô hợp lệ) + TCO-ST-09 (trạng thái cuối không đổi khi chạy lại job T+3/E-5).

## 6. Phân bổ SL khả dụng khi chuyển nhượng tại quầy (DT, BR-24)
| Rule | Hướng | Điều kiện | HĐ được ghi nhận |
|---|---|---|---|
| R1 | Tăng 100 | Đáo hạn khác nhau | HĐ-E1 (gần nhất): 800 → 900 |
| R2 | Tăng 100 | HĐ-E1 đã đủ (1.000); E2, E3 cùng ngày | HĐ-E3 (thiếu 800 > 500): 1.200 → 1.300 |
| R3 | Tăng 300 | KL tăng > KL thiếu HĐ-E1 (200) | HĐ-E1 → 1.000; 100 dư → HĐ-E3 → 1.300 [GIẢ ĐỊNH Q-16] |
| R4 | Giảm 100 | E2, E3 cùng ngày xa nhất | HĐ-E2 (KL thấp nhất): 500 → 400 |
| R5 | Giảm 100 | KH-E chỉ còn HĐ-E1 (03/2027) và thêm HĐ-E4 đáo hạn 01/09/2027 SLKD 300 | HĐ-E4: 300 → 200 |

## 7. Error guessing (chọn từ file 03)
- Danh sách: 0 bản ghi, 1 bản ghi, nhiều bản ghi (≥ 20), cuộn cuối danh sách.
- Double-tap card / dòng hộp, tap khi đang loading, back hệ thống Android, mất mạng khi tải danh sách.
- Session hết hạn khi đang ở màn; gọi API với ID tài khoản khác (IDOR).
- Số tiền lớn (hàng chục tỷ) không tràn layout; định dạng phân cách hàng nghìn.

---

# B4 – TEST CASE

## Precondition dùng chung
- **PC-01:** Đã chuẩn bị dữ liệu theo B2 mục 4 trên UAT.
- **PC-02:** Đăng nhập app SHSmart bằng KH tương ứng ghi trong cột Precondition.
- **PC-03 (màn DMHĐ):** Đang ở màn Danh mục HĐ theo đường dẫn: Trái phiếu SHS → tab Danh mục nắm giữ → tab Danh mục Hợp đồng.
- **PC-04 (màn DMHĐ có filter):** Đang ở màn Danh mục HĐ theo đường dẫn: Trái phiếu SHS → tab Danh mục nắm giữ → Danh mục Trái phiếu → card mã TP → button ">".

## 4.1 UI / Layout mặc định
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_UI_001 | BR-10 | DMHĐ – Card | Kiểm tra các thông tin hiển thị trên card HĐ khi HĐ ở trạng thái Hiệu lực | KH-A; PC-03 | 1. Quan sát card HĐ-A02 | HĐ-A02 | Card hiển thị đủ 9 thông tin: Mã HĐ, status-chip, Ngày mua, Ngày đáo hạn, Thời gian đầu tư, Số lượng TP, SL khả dụng, Tổng tiền đầu tư, Tổng tiền nhận về; giá trị khớp dữ liệu HĐ-A02 trên S-Bond | Medium | UI | EP | [GIẢ ĐỊNH] theo DOC-HD – Q-01 |
| TC_DMHD_UI_002 | BR-10 | DMHĐ – Card | Kiểm tra card HĐ không hiển thị các trường chỉ có trong BRD khi áp dụng bộ field DOC-HD | KH-A; PC-03 | 1. Quan sát card HĐ-A02 | HĐ-A02 | Không hiển thị Lợi suất, Tổ chức phát hành, Lợi tức dự kiến trên card (nếu BA chốt theo BRD thì đảo ngược expected) | Low | UI | EP | [GIẢ ĐỊNH] Q-01 |
| TC_DMHD_UI_003 | BR-06 | DMHĐ – Hộp XN | Kiểm tra thành phần tĩnh của hộp "Hợp đồng cần xác nhận" khi có HĐ cần xác nhận | KH-A; PC-03 | 1. Quan sát đầu màn hình | DS-A | Hộp hiển thị icon cảnh báo, tiêu đề "Hợp đồng cần xác nhận", dòng phụ "2 hợp đồng đang chờ xác nhận lệnh", danh sách dòng HĐ và button của hộp | Low | UI | EP | Tên button: Q-04 |
| TC_DMHD_UI_004 | BR-14 | DMHĐ – Card | Kiểm tra định dạng ngày và thời gian đầu tư trên card khi HĐ kỳ hạn 6 tháng | KH-A; PC-03 | 1. Quan sát Ngày mua, Ngày đáo hạn, Thời gian đầu tư của HĐ-A02 | HĐ-A02: mua 05/11/2026, kỳ hạn 6 tháng | Ngày mua "05/11/2026"; Ngày đáo hạn đúng ngày đáo hạn theo kỳ hạn 6 tháng trên S-Bond; Thời gian đầu tư "6 tháng" | Low | UI | EP | [GIẢ ĐỊNH] định dạng – Q-11 |
| TC_DMHD_UI_005 | BR-12 | DMHĐ – Card | Kiểm tra định dạng tiền khi Tổng tiền đầu tư có giá trị hàng chục tỷ | KH-A có thêm HĐ: Giá 100.000 × SL 500.000; PC-03 | 1. Quan sát card HĐ trên | Tổng tiền đầu tư = 50.000.000.000 | Hiển thị "50.000.000.000" kèm đơn vị theo thiết kế; không cắt chữ, không tràn card, không hiển thị dạng 5E+10 | Low | UI | EG | [GIẢ ĐỊNH] Q-11 |
| TC_DMHD_UI_006 | BR-10 | DMHĐ – Card | Kiểm tra hiển thị Mã HĐ khi mã có độ dài tối đa | HĐ có mã dài nhất hệ thống sinh; PC-03 | 1. Quan sát card | Mã HĐ dài nhất trên UAT | Mã HĐ hiển thị đầy đủ hoặc xuống dòng/ellipsis theo thiết kế; không đè lên status-chip | Low | UI | EG | |

## 4.2 Positive – luồng chính
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_NAV_001 | BR-01 | DMHĐ – Điều hướng | Kiểm tra truy cập Danh mục HĐ qua tab Danh mục nắm giữ | KH-A; đang ở Trang chủ app | 1. Chọn "Trái phiếu SHS" 2. Chọn tab "Danh mục nắm giữ" 3. Chọn tab "Danh mục Hợp đồng" | DS-A | Hiển thị màn Danh mục HĐ; danh sách có 6 card HĐ-A01…A06; không hiển thị filter mã TP | High | Positive | UC | |
| TC_DMHD_NAV_002 | BR-02, BR-03 | DMHĐ – Điều hướng | Kiểm tra truy cập Danh mục HĐ qua card mã TP ở Danh mục Trái phiếu | KH-A; ở tab Danh mục nắm giữ → Danh mục Trái phiếu | 1. Click button ">" trên card mã SBOND2601 | SBOND2601 | Chuyển sang màn Danh mục HĐ; hiển thị filter mã TP = "SBOND2601"; danh sách chỉ gồm HĐ-A01, HĐ-A02, HĐ-A05 | High | Positive | UC | |
| TC_DMHD_SORT_001 | BR-04 | DMHĐ – Sắp xếp | Kiểm tra thứ tự card HĐ khi có nhiều HĐ khác thời điểm tạo | KH-A; PC-03 | 1. Quan sát thứ tự card từ trên xuống | DS-A | Thứ tự: HĐ-A01 → A02 → A03 → A04 → A05 → A06 (mới nhất → cũ nhất), không phụ thuộc trạng thái | Medium | Positive | EP | Q-08 |
| TC_DMHD_SORT_002 | BR-04, BR-03 | DMHĐ – Sắp xếp | Kiểm tra thứ tự card HĐ khi đang áp filter mã TP | KH-A; PC-04 với SBOND2602 | 1. Quan sát thứ tự card | SBOND2602 | Thứ tự: HĐ-A03 → HĐ-A04 → HĐ-A06 | Medium | Positive | EP | |
| TC_DMHD_DET_001 | BR-15 | DMHĐ – Chi tiết | Kiểm tra điều hướng khi bấm card HĐ | KH-A; PC-03 | 1. Click card HĐ-A02 | HĐ-A02 | Mở màn Chi tiết HĐ của HĐ-A02; tab "Tổng quan" được chọn mặc định; có các tab Tổng quan, Dòng tiền, Xác nhận theo điều kiện hiển thị | Medium | Positive | UC | Tab Xác nhận chỉ hiện khi HĐ cần XN (BRD 2.3.3.3) |
| TC_DMHD_DET_002 | BR-15 | DMHĐ – Chi tiết | Kiểm tra dữ liệu Chi tiết HĐ khớp card đã chọn | KH-A; PC-03 | 1. Click card HĐ-A03 2. Đối chiếu Số hợp đồng ở tab Tổng quan | HĐ-A03 | Số hợp đồng ở tab Tổng quan = Mã HĐ của HĐ-A03 trên card | Medium | Positive | UC | |
| TC_DMHD_DET_003 | BR-15 | DMHĐ – Chi tiết | Kiểm tra quay lại từ Chi tiết HĐ về danh sách | KH-A; PC-04 với SBOND2601; đã mở Chi tiết HĐ-A02 | 1. Click nút Back trên header | – | Quay về Danh mục HĐ; vẫn giữ filter "SBOND2601"; vị trí cuộn giữ nguyên | Low | Positive | EG | [GIẢ ĐỊNH] giữ filter – Q-10 |
| TC_DMHD_BOX_001 | BR-05, BR-06 | DMHĐ – Hộp XN | Kiểm tra hộp cần xác nhận hiển thị đúng N khi có 2 HĐ cần xác nhận | KH-A; PC-03 | 1. Quan sát hộp | HĐ-A03, HĐ-A04 cần XN | Hộp hiển thị; dòng phụ "2 hợp đồng đang chờ xác nhận lệnh"; có 2 dòng HĐ-A03, HĐ-A04 | High | Positive | BVA | |
| TC_DMHD_BOX_002 | BR-07 | DMHĐ – Hộp XN | Kiểm tra thông tin trên dòng HĐ trong hộp | KH-A; PC-03 | 1. Quan sát dòng HĐ-A03 | HĐ-A03 | Dòng hiển thị Mã TP "SBOND2602", Mã HĐ của HĐ-A03, nhãn "Xác nhận Hợp đồng" | High | Positive | EP | |
| TC_DMHD_BOX_003 | BR-08 | DMHĐ – Hộp XN | Kiểm tra điều hướng khi bấm dòng HĐ trong hộp | KH-A; PC-03 | 1. Click dòng HĐ-A04 trong hộp | HĐ-A04 | Mở Chi tiết HĐ của HĐ-A04, tab "Xác nhận" đang được chọn (không dừng ở tab Tổng quan); hiển thị biểu mẫu "Phiếu lệnh đặt bán TP" | High | Positive | UC | |
| TC_DMHD_BOX_004 | BR-09 | DMHĐ – Hộp XN | Kiểm tra điều hướng khi bấm button của hộp | KH-A; PC-03 | 1. Click button của hộp ("Kiểm tra"/"Xác nhận") | DS-A | Chuyển sang màn "Xác nhận"; bộ đếm = 2; hiển thị 2 card HĐ-A03, HĐ-A04 | High | Positive | UC | [GIẢ ĐỊNH] Q-04 |

## 4.3 Hiển thị dữ liệu & tính toán (Validation dữ liệu hiển thị)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_CALC_001 | BR-12 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư khi Giá mua và SL là số chẵn | KH-A có HĐ Giá mua 102.500, SL 1.000; PC-03 | 1. Quan sát Tổng tiền đầu tư trên card | 102.500 × 1.000 | Tổng tiền đầu tư = 102.500.000 | High | Positive | EP | |
| TC_DMHD_CALC_002 | BR-12 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư khi SL = 1 | HĐ Giá mua 102.500, SL 1; PC-03 | 1. Quan sát Tổng tiền đầu tư | 102.500 × 1 | Tổng tiền đầu tư = 102.500 | Medium | Boundary | BVA | |
| TC_DMHD_CALC_003 | BR-12 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư khi Giá mua lẻ | HĐ Giá mua 100.333, SL 3; PC-03 | 1. Quan sát Tổng tiền đầu tư | 100.333 × 3 | Tổng tiền đầu tư = 300.999; không làm tròn thành 301.000 | High | Boundary | EG | |
| TC_DMHD_CALC_004 | BR-12 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư không cộng phí giao dịch | HĐ Giá mua 102.500, SL 1.000, phí suất 0,1%; PC-03 | 1. Quan sát Tổng tiền đầu tư | Số tiền phong tỏa khi mua = 102.602.500 | Tổng tiền đầu tư = 102.500.000 (≠ 102.602.500) | Medium | Negative | EG | [GIẢ ĐỊNH] Q-07 |
| TC_DMHD_CALC_005 | BR-13 | DMHĐ – Tính toán | Kiểm tra Tổng tiền nhận về bằng Tổng tiền đầu tư cộng Lãi dự kiến | HĐ như CALC_001; Lãi dự kiến BE trả = 4.500.000 | 1. Quan sát Tổng tiền nhận về 2. Mở Chi tiết HĐ, đối chiếu Lãi đầu tư dự kiến | 102.500.000 + 4.500.000 | Card hiển thị 107.000.000; khớp Tổng tiền nhận về ở tab Tổng quan; Lãi dự kiến khớp bảng tính BA cung cấp | High | Positive | EP | [GIẢ ĐỊNH] công thức lãi – Q-06 |
| TC_DMHD_CARD_001 | BR-14 | DMHĐ – Card | Kiểm tra Thời gian đầu tư khớp kỳ hạn NĐT chọn khi đặt mua | KH-A đặt mua HĐ kỳ hạn 3 tháng; PC-03 | 1. Quan sát Thời gian đầu tư và Ngày đáo hạn | Kỳ hạn 3 tháng | Thời gian đầu tư "3 tháng"; Ngày đáo hạn = ngày đáo hạn của HĐ trên S-Bond (không phải ngày đáo hạn của cả đợt phát hành) | Medium | Positive | EP | |
| TC_DMHD_CARD_002 | BR-10 | DMHĐ – Card | Kiểm tra SL khả dụng không vượt Số lượng TP của HĐ | KH-E; PC-03 | 1. Quan sát Số lượng TP và SL khả dụng các card HĐ-E1…E3 | DS-E | HĐ-E1: 1.000 / 800; HĐ-E2: 1.000 / 500; HĐ-E3: 2.000 / 1.200; SL khả dụng ≤ Số lượng TP | Medium | Positive | EP | |
| TC_DMHD_CARD_003 | BR-10 | DMHĐ – Card | Kiểm tra SL khả dụng khi HĐ ở trạng thái Chờ xử lý | KH-A; PC-03 | 1. Quan sát SL khả dụng HĐ-A01 | HĐ-A01 | SL khả dụng = 0 (TP chưa lưu ký) | Medium | Boundary | EG | [GIẢ ĐỊNH] Q-13 |
| TC_DMHD_CHIP_001 | BR-11 | DMHĐ – Status | Kiểm tra text status-chip của từng trạng thái HĐ | KH-A; PC-03 | 1. Quan sát chip của HĐ-A01…A06 | DS-A | A01 "Chờ xử lý"; A02 "Hiệu lực"; A03, A04 "Chờ xác nhận"; A05 "Hết hiệu lực"; A06 "Hoàn thiện"; mỗi trạng thái 1 màu chip riêng theo thiết kế | Medium | Positive | EP | [GIẢ ĐỊNH] Q-02, Q-03 |
| TC_DMHD_CHIP_002 | BR-11 | DMHĐ – Status | Kiểm tra không hiển thị trạng thái kỹ thuật S-Bond trên chip | KH-A; PC-03 | 1. Quan sát chip toàn bộ card | DS-A | Không chip nào hiển thị "Pre-matched", "Matched", "Rejected" hoặc mã trạng thái thô | Low | Negative | EP | |

## 4.4 Boundary – hộp cần xác nhận
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_BOX_005 | BR-05 | DMHĐ – Hộp XN | Kiểm tra hộp không hiển thị khi không có HĐ cần xác nhận | KH-C; PC-03 | 1. Quan sát đầu màn hình | KH-C: chỉ HĐ Hiệu lực | Không hiển thị hộp "Hợp đồng cần xác nhận"; danh sách card hiển thị từ đầu màn | High | Boundary | BVA | N = 0 |
| TC_DMHD_BOX_006 | BR-05, BR-06 | DMHĐ – Hộp XN | Kiểm tra hộp khi chỉ còn 1 HĐ cần xác nhận | KH-A đã xác nhận xong HĐ-A04; PC-03 | 1. Quan sát hộp | Chỉ HĐ-A03 cần XN | Hộp hiển thị; dòng phụ "1 hợp đồng đang chờ xác nhận lệnh"; 1 dòng HĐ-A03 | High | Boundary | BVA | N = 1 |
| TC_DMHD_BOX_007 | BR-05, BR-15 | DMHĐ – Hộp XN | Kiểm tra hộp biến mất sau khi xác nhận HĐ cuối cùng | KH-A chỉ còn HĐ-A03 cần XN | 1. Click dòng HĐ-A03 trong hộp 2. Xác nhận biểu mẫu + OTP theo luồng Xác nhận lệnh 3. Quay lại Danh mục HĐ | OTP hợp lệ | Không còn hộp; chip HĐ-A03 đổi sang "Hiệu lực" | High | Boundary | ST | [GIẢ ĐỊNH] Q-15 |
| TC_DMHD_BOX_008 | BR-06, BR-03 | DMHĐ – Hộp XN | Kiểm tra N trong hộp tính theo filter mã TP có HĐ cần xác nhận | KH-A; PC-04 với SBOND2602 | 1. Quan sát hộp | SBOND2602: A03, A04 cần XN | Dòng phụ "2 hợp đồng đang chờ xác nhận lệnh"; chỉ dòng của SBOND2602 | High | Positive | DT | |
| TC_DMHD_BOX_009 | BR-06, BR-03 | DMHĐ – Hộp XN | Kiểm tra hộp ẩn khi mã TP đang filter không có HĐ cần xác nhận dù tài khoản còn HĐ cần xác nhận | KH-A; PC-04 với SBOND2601 | 1. Quan sát đầu màn hình | SBOND2601: không HĐ cần XN; toàn TK có 2 | Không hiển thị hộp; không hiển thị "2 hợp đồng…" | High | Negative | DT | Bắt lỗi N không áp filter |

## 4.5 Bảng quyết định loại xác nhận (BR-23)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_DT_001 | BR-23, BR-05 | DMHĐ – Hộp XN | Kiểm tra HĐ không vào hộp khi Leg 2 và Phiếu lệnh đều chưa cần xác nhận | KH-C có HĐ Hiệu lực trước T+3; PC-03 | 1. Quan sát hộp và chip | R1 | HĐ không có trong hộp (hộp ẩn); chip "Hiệu lực" | Medium | Negative | DT | |
| TC_DMHD_DT_002 | BR-23, BR-07 | DMHĐ – Hộp XN | Kiểm tra nhãn khi chỉ HĐ Leg 2 cần xác nhận | KH-A; PC-03 | 1. Quan sát dòng HĐ-A03 | R2 – HĐ-A03 | Dòng HĐ-A03 có nhãn "Xác nhận Hợp đồng" | High | Positive | DT | |
| TC_DMHD_DT_003 | BR-23 | DMHĐ – Hộp XN | Kiểm tra HĐ rời hộp khi Leg 2 đã xác nhận và Phiếu lệnh chưa đến hạn | KH-A vừa xác nhận Leg 2 của HĐ-A03 (chưa đến E-5) | 1. Quay lại Danh mục HĐ 2. Quan sát hộp | R3 | HĐ-A03 không còn trong hộp; N giảm 1 | High | Positive | DT | |
| TC_DMHD_DT_004 | BR-23, BR-07 | DMHĐ – Hộp XN | Kiểm tra nhãn khi cả Leg 2 và Phiếu lệnh cùng cần xác nhận | KH-A có HĐ-A07 chưa XN Leg 2 và đã tới E-5 | 1. Quan sát hộp | R4 – HĐ-A07 | 1 dòng duy nhất cho HĐ-A07, nhãn "Xác nhận Hợp đồng"; N tính HĐ-A07 là 1 | High | Positive | DT | [GIẢ ĐỊNH] Q-05 |
| TC_DMHD_DT_005 | BR-23, BR-07 | DMHĐ – Hộp XN | Kiểm tra nhãn khi Leg 2 đã xác nhận và Phiếu lệnh cần xác nhận | KH-A; PC-03 | 1. Quan sát dòng HĐ-A04 | R5 – HĐ-A04 | Dòng HĐ-A04 có nhãn "Xác nhận Phiếu lệnh" | High | Positive | DT | |
| TC_DMHD_DT_006 | BR-23 | DMHĐ – Hộp XN | Kiểm tra HĐ rời hộp khi cả Leg 2 và Phiếu lệnh đã xác nhận | KH-A vừa xác nhận Phiếu lệnh HĐ-A04 | 1. Quay lại Danh mục HĐ 2. Quan sát hộp | R6 | HĐ-A04 không còn trong hộp; chip HĐ-A04 "Hiệu lực" | High | Positive | DT | [GIẢ ĐỊNH] Q-15 |

## 4.6 Negative – luồng phụ / vòng đời trạng thái (Integration)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_ST_001 | BR-16 | DMHĐ – Vòng đời | Kiểm tra HĐ mới xuất hiện với trạng thái Chờ xử lý sau khi đặt mua thành công | KH-A; trong giờ giao dịch; đủ số dư sub88 | 1. Đặt mua SBOND2601, SL 100, xác nhận OTP 2. Vào Danh mục HĐ | SL = 100 | HĐ mới nằm đầu danh sách; chip "Chờ xử lý"; Số lượng TP = 100; S-Bond lệnh "Pre-matched" | High | Positive | ST | |
| TC_DMHD_ST_002 | BR-17 | DMHĐ – Vòng đời | Kiểm tra HĐ bị ẩn khi lệnh không khớp | HĐ từ ST_001 đang Chờ xử lý | 1. Nghiệp vụ/BE chuyển lệnh sang "Rejected" 2. KH vào lại Danh mục HĐ | Lệnh Rejected | HĐ không còn hiển thị; tổng số card giảm 1; tiền phong tỏa sub88 được giải tỏa | High | Negative | ST | |
| TC_DMHD_ST_003 | BR-18 | DMHĐ – Vòng đời | Kiểm tra trạng thái HĐ khi xác nhận thanh toán thành công | HĐ mới Chờ xử lý | 1. Nghiệp vụ đẩy điện XNTT, lệnh "Matched" 2. KH vào lại Danh mục HĐ | Lệnh Matched | Chip đổi "Chờ xử lý" → "Hiệu lực"; SL khả dụng = Số lượng TP | High | Positive | ST | |
| TC_DMHD_ST_004 | BR-19 | DMHĐ – Vòng đời | Kiểm tra HĐ chuyển sang cần xác nhận tại ngày T+3 | HĐ Hiệu lực, hôm nay = T+3 | 1. BE chạy job gửi yêu cầu Xác nhận HĐ Leg 2 2. KH vào lại Danh mục HĐ | T+3 | Chip "Chờ xác nhận"; HĐ có dòng trong hộp, nhãn "Xác nhận Hợp đồng"; N tăng 1 | High | Positive | ST | [GIẢ ĐỊNH] Q-02 |
| TC_DMHD_ST_005 | BR-19 | DMHĐ – Vòng đời | Kiểm tra HĐ chuyển sang cần xác nhận tại ngày E-5 | HĐ Hiệu lực, Leg 2 đã XN, hôm nay = E-5 | 1. BE chạy job gửi yêu cầu Xác nhận Phiếu lệnh 2. KH vào lại Danh mục HĐ | E-5 | Chip "Chờ xác nhận"; dòng trong hộp nhãn "Xác nhận Phiếu lệnh" | High | Positive | ST | |
| TC_DMHD_ST_006 | BR-20 | DMHĐ – Vòng đời | Kiểm tra trạng thái HĐ khi COT ngày E thiếu biểu mẫu | HĐ chưa xác nhận Phiếu lệnh, hôm nay = E | 1. Chạy xử lý COT ngày E 2. KH vào lại Danh mục HĐ | Biểu mẫu thiếu | Chip "Hết hiệu lực"; HĐ không còn trong hộp cần XN | High | Negative | ST | |
| TC_DMHD_ST_007 | BR-20 | DMHĐ – Vòng đời | Kiểm tra trạng thái HĐ khi COT ngày E đủ biểu mẫu nhưng thiếu số dư TP | HĐ đã XN đủ, SL khả dụng < Số lượng TP, hôm nay = E | 1. Chạy xử lý COT ngày E 2. KH vào lại Danh mục HĐ | SL 1.000, SLKD 800 | Chip "Hết hiệu lực"; không ghi tăng tiền sub88 | High | Negative | ST | |
| TC_DMHD_ST_008 | BR-21 | DMHĐ – Vòng đời | Kiểm tra trạng thái HĐ khi COT ngày E đủ biểu mẫu và đủ số dư | HĐ đã XN đủ, SLKD = Số lượng TP, hôm nay = E | 1. Chạy COT, nghiệp vụ thanh lý (make-check) 2. KH vào lại Danh mục HĐ | SL 1.000, SLKD 1.000 | Chip "Hoàn thiện"; HĐ vẫn hiển thị trong danh sách; sub88 tăng tiền khả dụng | High | Positive | ST | [GIẢ ĐỊNH] Q-03 |
| TC_DMHD_ST_009 | BR-22 | DMHĐ – Vòng đời | Kiểm tra HĐ bị ẩn khi nghiệp vụ hủy giao dịch sau xác nhận thanh toán | HĐ Hiệu lực trong ngày XNTT | 1. Nghiệp vụ đẩy điện Hủy giao dịch cho VSD 2. KH vào lại Danh mục HĐ | Hủy trong ngày | HĐ không còn hiển thị; sub88 được ghi tăng tiền (revert) | High | Negative | ST | |
| TC_DMHD_ST_010 | BR-19, BR-21 | DMHĐ – Vòng đời | Kiểm tra HĐ ở trạng thái cuối không đổi khi job xác nhận chạy lại | HĐ-A06 Hoàn thiện, HĐ-A05 Hết hiệu lực | 1. BE chạy lại job T+3/E-5 2. KH vào lại Danh mục HĐ | HĐ-A05, A06 | Chip giữ nguyên; HĐ-A05, A06 không xuất hiện trong hộp; N không đổi | Medium | Negative | ST | Chuyển trạng thái không hợp lệ |
| TC_DMHD_ST_011 | BR-16 | DMHĐ – Vòng đời | Kiểm tra không tạo HĐ trùng khi double-tap nút xác nhận đặt mua | KH-A; trong giờ giao dịch | 1. Đặt mua SBOND2601 SL 100 2. Double-tap nút xác nhận OTP 3. Vào Danh mục HĐ | SL = 100 | Chỉ 1 HĐ mới "Chờ xử lý"; sub88 chỉ phong tỏa 1 lần | High | Negative | EG | |
| TC_DMHD_EG_001 | BR-01 | DMHĐ – Danh sách | Kiểm tra màn Danh mục HĐ khi KH chưa có HĐ nào | KH-B; PC-03 | 1. Quan sát màn hình | KH-B | Không có card, không có hộp cần XN; hiển thị trạng thái rỗng; không lỗi/crash | Medium | Negative | EG | [GIẢ ĐỊNH] text – Q-09 |
| TC_DMHD_EG_002 | BR-03 | DMHĐ – Filter | Kiểm tra danh sách khi mã TP filter chỉ có HĐ đã bị ẩn | KH-A có mã SBOND2604 chỉ 1 HĐ đã Rejected; card mã TP vẫn còn ở Danh mục TP | 1. Click ">" trên card SBOND2604 | SBOND2604 | Filter "SBOND2604"; danh sách rỗng; không hiển thị HĐ mã khác | Low | Negative | EG | Q-09 |
| TC_DMHD_EG_003 | BR-03 | DMHĐ – Filter | Kiểm tra filter bị bỏ khi vào lại qua tab Danh mục Hợp đồng | KH-A; đã vào DMHĐ với filter SBOND2601 | 1. Quay về tab Danh mục nắm giữ 2. Chọn tab "Danh mục Hợp đồng" | – | Không còn filter mã TP; hiển thị đủ 6 HĐ | Medium | Negative | UC | |
| TC_DMHD_EG_004 | BR-03 | DMHĐ – Filter | Kiểm tra filter cập nhật khi đổi sang mã TP khác | KH-A; đã vào DMHĐ với filter SBOND2601 | 1. Quay lại Danh mục Trái phiếu 2. Click ">" trên card SBOND2602 | SBOND2602 | Filter "SBOND2602"; chỉ HĐ-A03, A04, A06; không còn HĐ SBOND2601 | Medium | Negative | EG | |
| TC_DMHD_EG_005 | BR-15 | DMHĐ – Chi tiết | Kiểm tra không mở 2 màn Chi tiết khi double-tap card HĐ | KH-A; PC-03 | 1. Double-tap nhanh card HĐ-A02 2. Click Back 1 lần | HĐ-A02 | Chỉ mở 1 màn Chi tiết; Back 1 lần về lại Danh mục HĐ | Low | Negative | EG | |
| TC_DMHD_EG_006 | BR-01 | DMHĐ – Danh sách | Kiểm tra màn khi mất mạng lúc tải danh sách | KH-A; tắt mạng thiết bị | 1. Vào Danh mục HĐ 2. Bật mạng 3. Kéo xuống để tải lại | – | Bước 1: thông báo lỗi kết nối, không crash, không hiển thị dữ liệu sai/nửa vời; Bước 3: hiển thị đủ 6 HĐ | Medium | Negative | EG | Text lỗi theo chuẩn chung app |
| TC_DMHD_EG_007 | BR-04 | DMHĐ – Danh sách | Kiểm tra cuộn danh sách khi có nhiều HĐ | KH có 25 HĐ; PC-03 | 1. Cuộn đến cuối danh sách 2. Đếm số card | 25 HĐ | Hiển thị đủ 25 card; không trùng card khi cuộn; thứ tự mới → cũ giữ nguyên | Low | Boundary | EG | [GIẢ ĐỊNH] Q-12 |
| TC_DMHD_EG_008 | BR-11 | DMHĐ – Danh sách | Kiểm tra trạng thái cập nhật khi đang mở màn và BE đổi trạng thái | KH-A đang mở DMHĐ; HĐ-A01 Chờ xử lý | 1. Nghiệp vụ XNTT HĐ-A01 2. Kéo xuống để tải lại | – | Sau tải lại chip HĐ-A01 "Hiệu lực" | Medium | Negative | EG | [GIẢ ĐỊNH] Q-14 |

## 4.7 Chuyển nhượng tại quầy – SL khả dụng (BR-24)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_SLKD_001 | BR-24 | DMHĐ – SL khả dụng | Kiểm tra SL tăng ghi vào HĐ đáo hạn gần nhất | KH-E, DS-E | 1. Nghiệp vụ ghi nhận chuyển nhượng tăng 100 TP SBOND2603 tại quầy 2. KH vào lại Danh mục HĐ | +100 | HĐ-E1: 800 → 900; HĐ-E2 = 500, HĐ-E3 = 1.200 không đổi | High | Positive | DT | R1 |
| TC_DMHD_SLKD_002 | BR-24 | DMHĐ – SL khả dụng | Kiểm tra SL tăng khi các HĐ ưu tiên cùng ngày đáo hạn | KH-E; HĐ-E1 SLKD = 1.000 (đủ) | 1. Ghi nhận tăng 100 TP 2. Vào lại Danh mục HĐ | +100 | HĐ-E3 (thiếu 800): 1.200 → 1.300; HĐ-E2 giữ 500 | High | Positive | DT | R2 |
| TC_DMHD_SLKD_003 | BR-24 | DMHĐ – SL khả dụng | Kiểm tra SL tăng vượt KL thiếu của HĐ ưu tiên | KH-E, DS-E gốc | 1. Ghi nhận tăng 300 TP 2. Vào lại Danh mục HĐ | +300 | HĐ-E1: 800 → 1.000 (không vượt Số lượng TP); HĐ-E3: 1.200 → 1.300 | High | Boundary | BVA | [GIẢ ĐỊNH] Q-16 |
| TC_DMHD_SLKD_004 | BR-24 | DMHĐ – SL khả dụng | Kiểm tra SL giảm khi các HĐ xa nhất cùng ngày đáo hạn | KH-E, DS-E gốc | 1. Ghi nhận giảm 100 TP 2. Vào lại Danh mục HĐ | −100 | HĐ-E2 (KL thấp nhất): 500 → 400; HĐ-E1, HĐ-E3 không đổi | High | Positive | DT | R4; [GIẢ ĐỊNH] Q-16 |
| TC_DMHD_SLKD_005 | BR-24 | DMHĐ – SL khả dụng | Kiểm tra SL giảm ghi vào HĐ đáo hạn xa nhất | KH-E chỉ có HĐ-E1 (01/03/2027, SLKD 800) và HĐ-E4 (01/09/2027, SLKD 300) | 1. Ghi nhận giảm 100 TP 2. Vào lại Danh mục HĐ | −100 | HĐ-E4: 300 → 200; HĐ-E1 giữ 800 | High | Positive | DT | R5 |

## 4.8 Permission / Security
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_SEC_001 | BR-25 | DMHĐ – Bảo mật | Kiểm tra danh sách chỉ chứa HĐ của tài khoản đang đăng nhập | KH-A và KH-D đều có HĐ; đăng nhập KH-A; PC-03 | 1. Đối chiếu danh sách Mã HĐ với S-Bond | KH-A, KH-D | Chỉ hiển thị 6 HĐ của KH-A; không có HĐ nào của KH-D | High | Security | EG | [GIẢ ĐỊNH] BR-25 |
| TC_DMHD_SEC_002 | BR-25 | DMHĐ – Bảo mật | Kiểm tra API danh sách HĐ khi truyền mã tài khoản của KH khác | Token hợp lệ của KH-A; bắt được request lấy danh sách HĐ (proxy) | 1. Gửi lại request, thay tham số tài khoản/sub bằng của KH-D | accountId = TK KH-D | HTTP 401/403 hoặc chỉ trả HĐ của KH-A; response không chứa HĐ của KH-D | High | Security | EG | Endpoint chờ tài liệu API |
| TC_DMHD_SEC_003 | BR-25 | DMHĐ – Bảo mật | Kiểm tra màn Danh mục HĐ khi phiên đăng nhập hết hạn | KH-A; PC-03; phiên hết hạn | 1. Kéo xuống để tải lại 2. Click card HĐ-A02 | – | Thông báo hết phiên và chuyển về màn Đăng nhập; không hiển thị dữ liệu HĐ mới | Medium | Security | EG | |

---

# B5 – TỰ REVIEW, RTM & THỐNG KÊ

## 1. RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ | Ghi chú |
|---|---|---|---|---|---|
| BR-01 | Truy cập qua tab | 3 | NAV_001, EG_001, EG_006 | Đủ | |
| BR-02 | Truy cập qua card mã TP | 1 | NAV_002 | Đủ | |
| BR-03 | Filter mã TP | 7 | NAV_002, SORT_002, BOX_008, BOX_009, EG_002, EG_003, EG_004 | Đủ | Filter chung chờ Q-10 |
| BR-04 | Sắp xếp mới → cũ | 3 | SORT_001, SORT_002, EG_007 | Đủ | |
| BR-05 | Hộp hiện khi ≥ 1 HĐ cần XN | 5 | BOX_001, BOX_005, BOX_006, BOX_007, DT_001 | Đủ | N = 0/1/2 |
| BR-06 | Nội dung hộp, N theo filter | 5 | UI_003, BOX_001, BOX_006, BOX_008, BOX_009 | Đủ | |
| BR-07 | Dòng: Mã TP, Mã HĐ, nhãn | 4 | BOX_002, DT_002, DT_004, DT_005 | Đủ | |
| BR-08 | Bấm dòng → tab Xác nhận | 1 | BOX_003 | Đủ | |
| BR-09 | Button hộp | 1 | BOX_004 | Đủ | Phụ thuộc Q-04 |
| BR-10 | Field card | 5 | UI_001, UI_002, UI_006, CARD_002, CARD_003 | Đủ | Phụ thuộc Q-01 |
| BR-11 | 5 status-chip | 3 | CHIP_001, CHIP_002, EG_008 | Đủ | |
| BR-12 | Tổng tiền đầu tư | 5 | CALC_001…004, UI_005 | Đủ | |
| BR-13 | Tổng tiền nhận về | 1 | CALC_005 | Đủ | Phụ thuộc Q-06 |
| BR-14 | Ngày đáo hạn, Thời gian đầu tư | 2 | UI_004, CARD_001 | Đủ | |
| BR-15 | Card → Chi tiết HĐ | 5 | DET_001…003, EG_005, BOX_007 | Đủ | |
| BR-16 | Pre-matched → Chờ xử lý | 2 | ST_001, ST_011 | Đủ | |
| BR-17 | Rejected → ẩn | 1 | ST_002 | Đủ | |
| BR-18 | Matched → Hiệu lực | 1 | ST_003 | Đủ | |
| BR-19 | T+3 / E-5 → cần XN | 3 | ST_004, ST_005, ST_010 | Đủ | |
| BR-20 | COT E thiếu → Hết hiệu lực | 2 | ST_006, ST_007 | Đủ | 2 nhánh OR |
| BR-21 | COT E đủ → Hoàn thiện | 2 | ST_008, ST_010 | Đủ | |
| BR-22 | Hủy sau XNTT → ẩn | 1 | ST_009 | Đủ | |
| BR-23 | DT loại xác nhận | 6 | DT_001…006 | Đủ | 6/6 rule |
| BR-24 | Phân bổ SL khả dụng | 5 | SLKD_001…005 | Đủ | 5/5 rule |
| BR-25 | Chỉ thấy HĐ của mình | 3 | SEC_001…003 | Đủ | BR giả định |

**Requirement coverage: 25/25 BR = 100%.**

## 2. Checklist (file 05)
| Mục | Kết quả | Ghi chú |
|---|---|---|
| A1. 100% BR có TC | Đạt | RTM |
| A2. Field đủ rỗng/hợp lệ/không hợp lệ/biên | Đạt (áp dụng cho dữ liệu hiển thị) | Màn không có input |
| A3. Luồng chính + phụ + ngoại lệ | Đạt | NAV/BOX + EG + ST |
| A4. Phân quyền từng role | Đạt | 1 role KH; có IDOR |
| A5. DT mỗi rule có TC; ST có chuyển không hợp lệ | Đạt | DT 6/6, SLKD 5/5; ST_010 |
| A6. Error guessing | Đạt | EG_001…008 |
| B1. Đủ 12 cột, ID đúng format, không trùng | Đạt | |
| B2. 1 TC = 1 mục tiêu | Đạt | Đã tách BOX_008/BOX_009, ST_006/ST_007 |
| B3. Title "Kiểm tra … khi …" | Đạt | |
| B4. Test data cụ thể | Đạt có điều kiện | Mã HĐ ký hiệu HĐ-Axx – thay bằng mã thật khi chuẩn bị dữ liệu |
| B5. Expected đo được | Đạt | Có giá trị số, text chip, trạng thái S-Bond/sub88 |
| B6. Negative kiểm cả báo lỗi + không thực hiện | Đạt | VD: ST_002 (ẩn + giải tỏa tiền), SEC_002 (403 + không lộ dữ liệu) |
| B7. Message đúng nguyên văn hoặc [GIẢ ĐỊNH] | Đạt | Text empty state/lỗi mạng chưa có → gắn Q-09 |
| C1. Không trùng nội dung | Đạt | |
| C2. Priority hợp lý | Đạt | Tiền, trạng thái, bảo mật = High |
| C3. Thuật ngữ thống nhất tài liệu | Đạt có điều kiện | Tên button hộp, "Hoàn thiện/Hoàn thành", chip "Chờ xác nhận" chờ Q-02/03/04 |
| C4. Thứ tự UI → Positive → … → Permission | Đạt | |

**Đã sửa trong B5:** bổ sung ST_010 (chuyển trạng thái không hợp lệ), ST_011 (double-tap tạo trùng), BOX_009 (N không áp filter), CALC_004 (phí không cộng vào Tổng tiền đầu tư), SLKD_003 (biên KL tăng > KL thiếu).

## 3. Thống kê
| Chỉ số | Giá trị |
|---|---|
| Tổng TC | 65 |
| High / Medium / Low | 36 (55%) / 19 (29%) / 10 (15%) |
| Positive / Negative (gồm Negative, Boundary, Security) / UI | 31 (48%) / 28 (43%) / 6 (9%) |
| TC có [GIẢ ĐỊNH] | 21 |
| Q&A còn mở | 17 (Blocker 0 · Major 9 · Minor 8) |

**Nhận xét lệch chỉ số mục tiêu:**
- High 55% > mục tiêu 25%: màn này chủ yếu hiển thị trạng thái và số tiền của HĐ tài chính, phần lớn là High theo định nghĩa file 02.
- Positive : Negative ≈ 53 : 47 (không tính UI), chưa đạt 30 : 70: màn chỉ hiển thị, không có field nhập nên ít case validation âm. Khi BA chốt Q-09, Q-10 (empty state, Filter chung) sẽ bổ sung thêm case Negative.

## 4. Việc cần BA trả lời trước khi chạy test
Ưu tiên: Q-01 (bộ field card), Q-02 (chip Chờ xác nhận), Q-04 (button hộp), Q-05 (HĐ cần XN 2 biểu mẫu), Q-06 (công thức lãi), Q-16 (phân bổ SL khả dụng).
