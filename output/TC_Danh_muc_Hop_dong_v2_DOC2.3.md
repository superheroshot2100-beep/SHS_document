# BỘ TEST – MÀN HÌNH DANH MỤC HỢP ĐỒNG (v2 – chỉ theo tài liệu 2.3)

- **Nguồn duy nhất:** `doc\BRD\2.3.+Danh+mục+Hợp+đồng.doc`: [DOC §1] Mô tả tổng quan, [DOC §2] Mô tả chi tiết, [MOCK-1] mockup có filter mã TP, [MOCK-2] mockup không filter.
- Tài liệu ghi chú: *mockup phục vụ trực quan hóa, không phản ánh giao diện và luồng cuối cùng* → TC chỉ dựa trên mockup được đánh dấu [GIẢ ĐỊNH – mockup], Priority thấp.
- **Pipeline:** /FULL (B1 → B5). Không có Q&A mức Blocker → chạy liền mạch.
- **Khác bản v1:** bỏ toàn bộ nghiệp vụ lấy từ BRD tổng (vòng đời trạng thái theo S-Bond, chuyển nhượng tại quầy, bảng quyết định Leg 2 × Phiếu lệnh). Bổ sung các điểm thấy trong mockup.
- **Ngày lập:** 18/11/2026

---

# B1 – PHÂN TÍCH YÊU CẦU

## 1. Tổng quan
- **Mục tiêu:** NĐT xem danh sách hợp đồng (HĐ) trái phiếu đang nắm giữ, nhận biết HĐ cần xác nhận và đi tới Chi tiết HĐ.
- **Role:** Khách hàng đã đăng nhập app SHS, có HĐ trái phiếu. Không có role khác.
- **Vị trí:** Màn "Danh mục nắm giữ" → tab "Danh mục Hợp đồng" (MOCK-1, MOCK-2).

## 2. Thành phần màn hình
| # | Thành phần | Nguồn |
|---|---|---|
| F1 | 2 đường dẫn vào màn | DOC §1 |
| F2 | Hộp "Hợp đồng cần xác nhận" (icon, tiêu đề, dòng phụ N, các dòng HĐ, button "Xác nhận") | DOC §1, §2; MOCK |
| F3 | Filter mã TP (chip "Mã trái phiếu <mã>" + nút "Tất cả ✕") | DOC §1; MOCK-1 |
| F4 | Danh sách card HĐ: thông tin, sắp xếp, status-chip | DOC §1, §2; MOCK |
| F5 | Điều hướng card / dòng hộp → Chi tiết HĐ | DOC §1, §2 |

## 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Truy cập: Log in app SHS → Trái phiếu SHS → tab Danh mục nắm giữ → tab Danh mục Hợp đồng | DOC §1 | Logic |
| BR-02 | Truy cập: Trái phiếu SHS → tab Danh mục nắm giữ → chọn 1 card mã TP tại Danh mục Trái phiếu → Danh mục HĐ hiển thị thêm filter riêng cho mã TP và chỉ hiển thị HĐ thuộc mã TP đó | DOC §1 | Logic |
| BR-03 | Danh sách HĐ sắp xếp mới nhất → cũ nhất | DOC §1 | Logic |
| BR-04 | Bấm card HĐ → màn Chi tiết HĐ gồm các tab Tổng quan, Dòng tiền, Xác nhận | DOC §1 | Logic |
| BR-05 | Hộp "Hợp đồng cần xác nhận" chỉ hiển thị khi có ít nhất 1 HĐ đang chờ xác nhận | DOC §1, §2 | Logic/UI |
| BR-06 | Hộp gồm icon cảnh báo, tiêu đề tĩnh, dòng phụ "N hợp đồng đang chờ xác nhận lệnh"; N = số HĐ cần xác nhận, đã áp bộ lọc / mã TP hiện tại (nếu có) | DOC §2 | Logic/UI |
| BR-07 | Mỗi dòng trong hộp hiển thị Mã TP, Mã HĐ và 1 trong 2 trạng thái cần xác nhận: "Xác nhận Hợp đồng", "Xác nhận Phiếu lệnh" | DOC §2 | UI/Logic |
| BR-08 | Bấm vào dòng trong hộp → mở Chi tiết HĐ, nhảy thẳng vào tab "Xác nhận" | DOC §2 | Logic |
| BR-09 | Bấm button "Xác nhận" → đưa về tab Xác nhận trong màn Chi tiết HĐ | DOC §1 | Logic |
| BR-10 | Mã hợp đồng là mã định danh duy nhất của từng HĐ | DOC §2 | UI |
| BR-11 | Status-chip hiển thị trạng thái hiện tại của HĐ, gồm: Chờ xử lý, Hiệu lực, Chờ xác nhận, Hết hiệu lực, Hoàn thiện | DOC §2 | UI/Logic |
| BR-12 | Ngày mua = ngày xác lập HĐ, ngày NĐT thực hiện giao dịch mua tạo ra HĐ | DOC §2 | Logic |
| BR-13 | Ngày đáo hạn tính theo kỳ hạn NĐT đã chọn khi đặt mua | DOC §2 | Logic |
| BR-14 | Thời gian đầu tư = số tháng kỳ hạn của riêng HĐ (NĐT chọn khi đặt mua) | DOC §2 | Logic |
| BR-15 | Số lượng Trái phiếu = khối lượng TP trong HĐ | DOC §2 | Logic |
| BR-16 | SL khả dụng = KL còn lại được mapping với HĐ | DOC §2 | Logic |
| BR-17 | Tổng tiền đầu tư = Giá mua (theo dữ liệu mã TP) × Số lượng Trái phiếu của HĐ | DOC §2 | Logic (tính toán) |
| BR-18 | Tổng tiền nhận về = Tổng tiền đầu tư + Lãi đầu tư dự kiến; lãi tính theo lãi suất coupon của mã TP | DOC §2 | Logic (tính toán) |
| BR-19 | [Mockup] Filter mã TP hiển thị dạng chip "Mã trái phiếu <mã>" kèm nút "Tất cả ✕" | MOCK-1 | UI |
| BR-20 | [Mockup] Hiển thị card: không filter → card có Mã TP phía trên Mã HĐ; có filter → không lặp Mã TP; ngày dd/mm/yyyy; tiền có "." phân cách + "đ"; Tổng tiền đầu tư màu đỏ, Tổng tiền nhận về màu xanh; Mã HĐ dài cắt "…" | MOCK-1, MOCK-2 | UI |
| BR-21 | [GIẢ ĐỊNH – ngầm định bảo mật] KH chỉ xem được HĐ của chính mình (UI và API) | Không có trong tài liệu | Phân quyền |

## 4. Field list (card HĐ & hộp)
| Field | Kiểu | Bắt buộc hiển thị | Ràng buộc / Công thức | Ví dụ (mockup) |
|---|---|---|---|---|
| Mã TP (trên card) | Text | Khi không filter [mockup] | – | BHB12501 |
| Mã hợp đồng | Text | Có | Duy nhất | 20240610BHB12501.000000… |
| Status-chip | Enum | Có | 5 giá trị BR-11 | (mockup: "Đã tất toán", "Đang nắm giữ Outright" – xem Q-01) |
| Ngày mua | Date | Có | Ngày giao dịch mua | 10/06/2024 |
| Ngày đáo hạn | Date | Có | Theo kỳ hạn HĐ | 10/06/2025 |
| Thời gian đầu tư | Số tháng | Có | Kỳ hạn HĐ | 12 tháng |
| Số lượng Trái phiếu | Số nguyên | Có | KL trong HĐ | 10 |
| SL khả dụng | Số nguyên | Có | KL còn lại mapping HĐ | 10 |
| Tổng tiền đầu tư | Tiền | Có | Giá mua × SL | 1.088.348.310 đ |
| Tổng tiền nhận về | Tiền | Có | Tổng tiền đầu tư + Lãi dự kiến | 1.191.741.399 đ |
| Hộp – dòng phụ | Text động | Khi N ≥ 1 | "N hợp đồng đang chờ xác nhận lệnh" | 1 hợp đồng đang chờ xác nhận lệnh |
| Hộp – nhãn dòng | Enum | Có | "Xác nhận Hợp đồng" / "Xác nhận Phiếu lệnh" | (mockup: "Xác nhận HĐ Leg 2" – xem Q-04) |

> Màn chỉ hiển thị, không có field nhập liệu → EP/BVA áp dụng cho dữ liệu hiển thị (N, số lượng, tiền).

## 5. Q&A
| Q ID | Câu hỏi | Lý do hỏi | Mức | Giả định đề xuất (dùng trong TC) |
|---|---|---|---|---|
| Q-01 | Mockup có chip "Đã tất toán", "Đang nắm giữ Outright", không nằm trong 5 trạng thái DOC §2. Danh sách trạng thái chốt là gì? | Mâu thuẫn spec – mockup, ảnh hưởng expected chip | Major | Theo 5 trạng thái DOC §2 |
| Q-02 | Điều kiện để HĐ "đang chờ xác nhận" là gì? HĐ trong hộp có luôn mang chip "Chờ xác nhận" không? (DOC ghi "các trạng thái chi tiết dưới tài liệu" nhưng chỉ có 2 nhãn) | Không biết cách chuẩn bị dữ liệu và expected chip | Major | HĐ trong hộp ⇔ chip "Chờ xác nhận" |
| Q-03 | Ý nghĩa và điều kiện chuyển của các trạng thái Chờ xử lý / Hiệu lực / Hết hiệu lực / Hoàn thiện? HĐ bị từ chối / hủy có hiển thị không? | Tài liệu không mô tả vòng đời → chưa viết được TC chuyển trạng thái | Major | Chỉ test hiển thị đúng trạng thái BE trả; TC chuyển trạng thái để sau khi BA trả lời |
| Q-04 | Nhãn dòng trong hộp: "Xác nhận Hợp đồng" (DOC) hay "Xác nhận HĐ Leg 2" (mockup)? | Text expected | Minor | Theo DOC: "Xác nhận Hợp đồng" |
| Q-05 | HĐ cần xác nhận cả Hợp đồng lẫn Phiếu lệnh → hộp hiển thị 1 dòng hay 2 dòng? Nhãn nào? N đếm theo HĐ hay theo dòng? | Ảnh hưởng N và nhãn | Major | 1 dòng/HĐ, nhãn "Xác nhận Hợp đồng"; N đếm theo HĐ |
| Q-06 | "Button Xác nhận" (DOC §1) là button trên từng dòng (mockup "Xác nhận >") hay có thêm button chung của hộp? Bấm vùng dòng và bấm button có khác nhau không? | Expected điều hướng | Major | Button nằm trên từng dòng; bấm dòng hay button đều mở tab Xác nhận của HĐ đó |
| Q-07 | "Giá mua theo dữ liệu mã TP" là giá tại thời điểm mua của HĐ hay giá hiện tại của mã TP? Công thức lãi dự kiến (cơ sở ngày, làm tròn)? | Không tính tay được expected số tiền | Major | Giá mua tại thời điểm mua (không đổi theo giá hiện tại); lãi dự kiến so khớp số liệu BE/BA cung cấp |
| Q-08 | Tổng tiền đầu tư có gồm phí giao dịch không? | Sai lệch số tiền | Minor | Không gồm phí (đúng công thức DOC) |
| Q-09 | "Mới nhất" theo Ngày mua hay thời điểm tạo HĐ? Tie-break khi cùng ngày? MOCK-1 hiển thị HĐ 2024 trước HĐ 2025 (ngược yêu cầu) | Expected thứ tự | Minor | Ngày mua giảm dần |
| Q-10 | Nút "Tất cả ✕" (mockup) có xóa filter mã TP không? Sau khi xóa, N trong hộp có tính lại? Quay lại từ Chi tiết HĐ có giữ filter? | Expected filter | Major | Xóa filter → hiển thị toàn bộ HĐ, N tính lại; giữ filter khi Back từ Chi tiết |
| Q-11 | Empty state khi KH không có HĐ hoặc mã TP đang filter không có HĐ? | Chưa có text | Minor | Hiển thị trạng thái rỗng, không lỗi |
| Q-12 | Mã HĐ bị cắt "…" (mockup) – KH xem mã đầy đủ ở đâu? | UX | Minor | Xem đầy đủ ở Chi tiết HĐ |
| Q-13 | Định dạng ngày, tiền, màu số tiền theo mockup có là chuẩn chốt? | Expected UI | Minor | Theo mockup |
| Q-14 | Card hiển thị Mã TP khi không filter và ẩn khi có filter (MOCK-2 vs MOCK-1) – có phải rule? | Expected UI | Minor | Đúng như mockup |
| Q-15 | Nút "Bộ lọc" trên header (mockup) có áp dụng cho tab Danh mục HĐ? "Đã áp bộ lọc" ở BR-06 gồm những bộ lọc nào? | Phạm vi filter | Major | Chỉ test filter mã TP; "Bộ lọc" chung Out-of-scope |
| Q-16 | Danh sách nhiều HĐ: phân trang hay lazy-load? | Hiển thị đủ | Minor | Cuộn hiển thị đủ |
| Q-17 | SL khả dụng có luôn ≤ Số lượng TP? HĐ "Chờ xử lý" hiển thị SL khả dụng bao nhiêu? | Expected dữ liệu | Minor | ≤ SL TP; khớp số liệu BE |
| Q-18 | Bấm card HĐ mở Chi tiết HĐ ở tab nào mặc định? | Expected điều hướng | Minor | Tab "Tổng quan" |

## 6. Rủi ro chính (Top 5)
1. **Sai số tiền** Tổng tiền đầu tư / nhận về (Q-07, Q-08 chưa chốt) → KH khiếu nại.
2. **Hộp cần xác nhận không hiện hoặc N sai**, nhất là khi đang filter → KH bỏ lỡ hạn xác nhận.
3. **Status-chip sai / lệch mockup** (Q-01, Q-02) → KH hiểu sai tình trạng tài sản.
4. **Filter mã TP lọc sai** → KH tưởng mất HĐ.
5. **Lộ HĐ của KH khác** qua API.

---

# B2 – CHIẾN LƯỢC TEST

## 1. Phạm vi
**In-scope:** tab Danh mục Hợp đồng trên app: 2 đường vào, hộp cần xác nhận, filter mã TP, card HĐ (thông tin, sắp xếp, chip, tính tiền), điều hướng sang Chi tiết HĐ, bảo mật cơ bản.

**Out-of-scope:** nội dung các tab Chi tiết HĐ (chỉ kiểm điều hướng), luồng xác nhận + OTP, vòng đời trạng thái (chờ Q-03), nút "Bộ lọc" chung (chờ Q-15), header Số dư TK / Tổng giá trị TP, hiệu năng.

## 2. Loại test
Functional, UI, Data/Calculation, Security cơ bản, Compatibility nhẹ (1 iOS + 1 Android).

## 3. Kỹ thuật & ước lượng
| BR ID | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC |
|---|---|---|---|---|
| BR-01, BR-02 | UC | EG | High | 7 |
| BR-03 | EP | EG | Medium | 3 |
| BR-04 | UC | EG | Medium | 4 |
| BR-05, BR-06 | BVA (N = 0/1/2), DT (có/không filter) | EG | High | 7 |
| BR-07…BR-09 | EP, UC | EG | High | 6 |
| BR-10…BR-16 | EP | EG | Medium | 8 |
| BR-17, BR-18 | EP, BVA | EG | High | 6 |
| BR-19, BR-20 | UC | – | Low | 7 |
| BR-21 | EG | – | High | 3 |

## 4. Môi trường & dữ liệu
- UAT SHSmart App, build mới nhất; iOS + Android.
- **Tài khoản:**

| TK | Mô tả |
|---|---|
| KH-A | Có bộ HĐ DS-A |
| KH-B | Chưa có HĐ nào |
| KH-C | Có HĐ nhưng không HĐ nào cần xác nhận |
| KH-D | Tài khoản khác có HĐ riêng (test bảo mật) |
| KH-E | Có 1 HĐ cần xác nhận đồng thời Hợp đồng và Phiếu lệnh (Q-05) |

- **DS-A (KH-A)** – mã TP theo mockup, mã HĐ thay bằng mã thật trên UAT:

| Ký hiệu | Mã TP | Ngày mua | Kỳ hạn | Trạng thái | Cần xác nhận |
|---|---|---|---|---|---|
| HĐ-A01 | BHB12502 | 20/10/2025 | 6 tháng | Chờ xử lý | – |
| HĐ-A02 | BHB12501 | 15/09/2025 | 12 tháng | Chờ xác nhận | Xác nhận Hợp đồng |
| HĐ-A03 | BHB12502 | 10/08/2025 | 12 tháng | Chờ xác nhận | Xác nhận Phiếu lệnh |
| HĐ-A04 | BHB12503 | 01/04/2025 | 12 tháng | Hiệu lực | – |
| HĐ-A05 | BHB12501 | 12/03/2025 | 6 tháng | Hiệu lực | – |
| HĐ-A06 | BHB12501 | 10/06/2024 | 12 tháng | Hoàn thiện | – |
| HĐ-A07 | BHB12502 | 01/05/2024 | 12 tháng | Hết hiệu lực | – |

Theo mã TP: BHB12501 = A02, A05, A06 · BHB12502 = A01, A03, A07 · BHB12503 = A04.
HĐ-A06: SL 10, Giá mua 108.834.831 đ, Lãi dự kiến (BE) 103.393.089 đ (khớp số liệu MOCK).

## 5. Exit criteria
- 100% TC High đã chạy, Pass ≥ 95%; không còn bug Critical/Major mở.
- Q&A Major (Q-01, 02, 03, 05, 06, 07, 10, 15) đã được trả lời; TC [GIẢ ĐỊNH] đã cập nhật.

---

# B3 – TEST CONDITION

## 1. Điều hướng & filter (UC)
| Mã | Condition |
|---|---|
| TCO-01-01 | Vào qua tab Danh mục HĐ → đủ 7 HĐ, không filter |
| TCO-02-01 | Vào qua card mã TP BHB12501 → filter BHB12501, chỉ A02, A05, A06 |
| TCO-02-02 | Vào lại qua tab → mất filter |
| TCO-02-03 | Đổi sang card mã TP khác → filter đổi theo |
| TCO-02-04 | Back từ Chi tiết HĐ → giữ filter [Q-10] |
| TCO-19-01 | Chip filter "Mã trái phiếu BHB12501" + "Tất cả ✕" |
| TCO-19-02 | Bấm "Tất cả ✕" → bỏ filter, N tính lại [Q-10] |

## 2. Hộp cần xác nhận (BVA N + DT filter)
| Mã | Phạm vi | N | Kết quả |
|---|---|---|---|
| TCO-05-01 | KH-C, không filter | 0 | Ẩn hộp |
| TCO-05-02 | KH-A, filter BHB12501 | 1 | "1 hợp đồng đang chờ xác nhận lệnh", chỉ dòng A02 |
| TCO-05-03 | KH-A, không filter | 2 | "2 hợp đồng…", dòng A02, A03 |
| TCO-06-01 | KH-A, filter BHB12503 (TK vẫn có 2) | 0 | Ẩn hộp |
| TCO-06-02 | KH-A, filter BHB12502 | 1 | Chỉ dòng A03, không có A02 |
| TCO-07-01 | HĐ cần xác nhận Hợp đồng | – | Nhãn "Xác nhận Hợp đồng" |
| TCO-07-02 | HĐ cần xác nhận Phiếu lệnh | – | Nhãn "Xác nhận Phiếu lệnh" |
| TCO-07-03 | HĐ cần cả 2 (KH-E) | 1 | 1 dòng [Q-05] |
| TCO-05-04 | Xác nhận xong HĐ cuối | 1 → 0 | Hộp biến mất |
| TCO-05-05 | HĐ Hết hiệu lực / Hoàn thiện | – | Không vào hộp |

## 3. Card & tính tiền (EP/BVA)
| Field | Hợp lệ | Biên / EG |
|---|---|---|
| Tổng tiền đầu tư | 108.834.831 × 10 = 1.088.348.310 | SL = 1; giá lẻ 100.333 × 3 = 300.999 (không làm tròn); số lớn 100.000.000 × 500 = 50.000.000.000; không cộng phí; không đổi khi giá mã TP thay đổi |
| Tổng tiền nhận về | 1.088.348.310 + 103.393.089 = 1.191.741.399 | – |
| Ngày đáo hạn / Thời gian đầu tư | 10/06/2024 + 12 tháng = 10/06/2025 | 12/03/2025 + 6 tháng = 12/09/2025 |
| Status-chip | 5 giá trị | Giá trị ngoài danh sách (mockup) không xuất hiện [Q-01] |
| Mã HĐ | Hiển thị | Mã dài cắt "…", không đè chip |

## 4. Error guessing (chọn từ file 03)
0 / 1 / nhiều HĐ (25), double-tap card và button, mất mạng, tải lại sau khi BE đổi dữ liệu, session hết hạn, IDOR.

---

# B4 – TEST CASE

## Precondition dùng chung
- **PC-01:** Đã chuẩn bị dữ liệu như B2 mục 4 trên UAT.
- **PC-02:** Đăng nhập app SHS bằng tài khoản ghi trong cột Precondition.
- **PC-03 (DMHĐ không filter):** Đang ở màn Danh mục HĐ: Trái phiếu SHS → tab Danh mục nắm giữ → tab Danh mục Hợp đồng.
- **PC-04 (DMHĐ có filter):** Đang ở màn Danh mục HĐ: Trái phiếu SHS → tab Danh mục nắm giữ → tab Danh mục Trái phiếu → chọn card mã TP ghi trong Test Data.

## 4.1 UI / Layout
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_UI_001 | BR-20 | DMHĐ – Layout | Kiểm tra bố cục tab Danh mục HĐ khi vào không có filter | KH-A; PC-03 | 1. Quan sát màn hình từ trên xuống | DS-A | Tab "Danh mục Hợp đồng" đang chọn (gạch chân); hộp "Hợp đồng cần xác nhận" nằm trên danh sách card; không có chip filter mã TP; mỗi card có Mã TP phía trên Mã HĐ | Low | UI | EP | [GIẢ ĐỊNH – mockup] Q-14 |
| TC_DMHD_UI_002 | BR-10, BR-11, BR-12, BR-13, BR-14, BR-15, BR-16, BR-17, BR-18 | DMHĐ – Card | Kiểm tra đủ thông tin trên card HĐ khi HĐ ở trạng thái Hiệu lực | KH-A; PC-03 | 1. Quan sát card HĐ-A05 | HĐ-A05 | Card có: Mã HĐ, status-chip, và đủ 7 nhãn – giá trị: "Ngày mua", "Ngày đáo hạn", "Thời gian đầu tư", "Số lượng Trái phiếu", "SL khả dụng", "Tổng tiền đầu tư", "Tổng tiền nhận về"; giá trị khớp dữ liệu HĐ-A05 trên BE | Medium | UI | EP | |
| TC_DMHD_UI_003 | BR-06 | DMHĐ – Hộp XN | Kiểm tra thành phần tĩnh của hộp cần xác nhận khi có HĐ cần xác nhận | KH-A; PC-03 | 1. Quan sát hộp | DS-A | Có icon cảnh báo, tiêu đề "Hợp đồng cần xác nhận", dòng phụ "2 hợp đồng đang chờ xác nhận lệnh", các dòng HĐ, mỗi dòng có button "Xác nhận >" | Low | UI | EP | Q-06 |
| TC_DMHD_UI_004 | BR-20 | DMHĐ – Card | Kiểm tra định dạng ngày và thời gian đầu tư trên card | KH-A; PC-03 | 1. Quan sát card HĐ-A06 | HĐ-A06: mua 10/06/2024, 12 tháng | Ngày mua "10/06/2024"; Ngày đáo hạn "10/06/2025"; Thời gian đầu tư "12 tháng" | Low | UI | EP | [GIẢ ĐỊNH – mockup] Q-13 |
| TC_DMHD_UI_005 | BR-20 | DMHĐ – Card | Kiểm tra định dạng và màu số tiền trên card | KH-A; PC-03 | 1. Quan sát Tổng tiền đầu tư, Tổng tiền nhận về của HĐ-A06 | HĐ-A06 | Tổng tiền đầu tư "1.088.348.310 đ" màu đỏ; Tổng tiền nhận về "1.191.741.399 đ" màu xanh lá; có dấu "." phân cách hàng nghìn | Low | UI | EP | [GIẢ ĐỊNH – mockup] Q-13 |
| TC_DMHD_UI_006 | BR-10, BR-20 | DMHĐ – Card | Kiểm tra hiển thị Mã HĐ khi mã dài hơn chiều rộng card | KH-A; PC-03 | 1. Quan sát dòng Mã HĐ của HĐ-A06 | Mã HĐ dạng 20240610BHB12501.0000001102 | Mã HĐ cắt bằng "…" trên 1 dòng; không đè lên status-chip; không vỡ layout | Low | UI | EG | Q-12 |
| TC_DMHD_UI_007 | BR-20 | DMHĐ – Card | Kiểm tra card không lặp Mã TP khi đang filter mã TP | KH-A; PC-04 với BHB12501 | 1. Quan sát card | BHB12501 | Card chỉ hiển thị Mã HĐ ở dòng đầu, không có dòng Mã TP riêng | Low | UI | EP | [GIẢ ĐỊNH – mockup] Q-14 |
| TC_DMHD_UI_008 | BR-17, BR-20 | DMHĐ – Card | Kiểm tra hiển thị số tiền khi Tổng tiền đầu tư hàng chục tỷ | KH-A có thêm HĐ: Giá 100.000.000 × SL 500; PC-03 | 1. Quan sát card HĐ đó | Tổng tiền đầu tư = 50.000.000.000 | Hiển thị "50.000.000.000 đ" đầy đủ; không cắt số, không dạng 5E+10, không tràn card | Low | UI | EG | |

## 4.2 Positive – luồng chính
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_NAV_001 | BR-01 | DMHĐ – Điều hướng | Kiểm tra truy cập Danh mục HĐ qua tab Danh mục nắm giữ | KH-A; đang ở Trang chủ app | 1. Chọn "Trái phiếu SHS" 2. Chọn tab "Danh mục nắm giữ" 3. Chọn tab "Danh mục Hợp đồng" | DS-A | Hiển thị đủ 7 card HĐ-A01…A07 của cả 3 mã TP; không có chip filter mã TP | High | Positive | UC | |
| TC_DMHD_NAV_002 | BR-02 | DMHĐ – Điều hướng | Kiểm tra truy cập Danh mục HĐ qua card mã TP ở Danh mục Trái phiếu | KH-A; đang ở tab Danh mục Trái phiếu | 1. Chọn card mã BHB12501 | BHB12501 | Chuyển sang tab Danh mục Hợp đồng; có filter mã TP BHB12501; chỉ hiển thị 3 card HĐ-A02, A05, A06; không có HĐ của BHB12502, BHB12503 | High | Positive | UC | |
| TC_DMHD_NAV_003 | BR-19 | DMHĐ – Filter | Kiểm tra hiển thị chip filter mã TP khi vào từ Danh mục Trái phiếu | KH-A; PC-04 với BHB12501 | 1. Quan sát vùng filter phía trên danh sách card | BHB12501 | Chip hiển thị "Mã trái phiếu" + "BHB12501" và nút "Tất cả ✕" | Low | UI | EP | [GIẢ ĐỊNH – mockup] |
| TC_DMHD_SORT_001 | BR-03 | DMHĐ – Sắp xếp | Kiểm tra thứ tự card khi có nhiều HĐ khác ngày mua | KH-A; PC-03 | 1. Quan sát thứ tự card từ trên xuống | DS-A | Thứ tự: A01 (20/10/2025) → A02 → A03 → A04 → A05 → A06 → A07 (01/05/2024); không phụ thuộc trạng thái, không phụ thuộc mã TP | Medium | Positive | EP | [GIẢ ĐỊNH] khóa sắp xếp – Q-09 |
| TC_DMHD_SORT_002 | BR-03, BR-02 | DMHĐ – Sắp xếp | Kiểm tra thứ tự card khi đang filter mã TP | KH-A; PC-04 với BHB12501 | 1. Quan sát thứ tự card | BHB12501 | Thứ tự: HĐ-A02 (15/09/2025) → HĐ-A05 (12/03/2025) → HĐ-A06 (10/06/2024) | Medium | Positive | EP | Mockup đang ngược thứ tự – Q-09 |
| TC_DMHD_DET_001 | BR-04 | DMHĐ – Chi tiết | Kiểm tra điều hướng khi bấm card HĐ | KH-A; PC-03 | 1. Click card HĐ-A05 | HĐ-A05 | Mở màn Chi tiết HĐ; tab "Tổng quan" được chọn mặc định | Medium | Positive | UC | [GIẢ ĐỊNH] Q-18 |
| TC_DMHD_DET_002 | BR-04, BR-10 | DMHĐ – Chi tiết | Kiểm tra Chi tiết HĐ mở đúng HĐ đã chọn | KH-A; PC-03 | 1. Click card HĐ-A04 2. Đối chiếu mã HĐ trên màn Chi tiết | HĐ-A04 | Mã HĐ trên Chi tiết HĐ trùng đầy đủ mã HĐ-A04 (không bị cắt) | Medium | Positive | UC | Q-12 |
| TC_DMHD_DET_003 | BR-04 | DMHĐ – Chi tiết | Kiểm tra các tab của màn Chi tiết HĐ | KH-A; PC-03 | 1. Click card HĐ-A02 2. Quan sát thanh tab | HĐ-A02 (cần xác nhận) | Có đủ 3 tab: "Tổng quan", "Dòng tiền", "Xác nhận" | Medium | Positive | EP | |
| TC_DMHD_BOX_001 | BR-05, BR-06 | DMHĐ – Hộp XN | Kiểm tra hộp cần xác nhận khi có 2 HĐ cần xác nhận | KH-A; PC-03 | 1. Quan sát hộp | A02, A03 cần xác nhận | Hộp hiển thị; dòng phụ "2 hợp đồng đang chờ xác nhận lệnh"; có đúng 2 dòng: HĐ-A02, HĐ-A03 | High | Positive | BVA | N = 2 |
| TC_DMHD_BOX_002 | BR-07 | DMHĐ – Hộp XN | Kiểm tra thông tin dòng HĐ cần xác nhận Hợp đồng | KH-A; PC-03 | 1. Quan sát dòng HĐ-A02 | HĐ-A02 | Dòng hiển thị Mã TP "BHB12501", Mã HĐ của HĐ-A02, nhãn "Xác nhận Hợp đồng" | High | Positive | EP | Mockup ghi "Xác nhận HĐ Leg 2" – Q-04 |
| TC_DMHD_BOX_003 | BR-07 | DMHĐ – Hộp XN | Kiểm tra thông tin dòng HĐ cần xác nhận Phiếu lệnh | KH-A; PC-03 | 1. Quan sát dòng HĐ-A03 | HĐ-A03 | Dòng hiển thị Mã TP "BHB12502", Mã HĐ của HĐ-A03, nhãn "Xác nhận Phiếu lệnh" | High | Positive | EP | |
| TC_DMHD_BOX_004 | BR-08 | DMHĐ – Hộp XN | Kiểm tra điều hướng khi bấm vào vùng dòng HĐ trong hộp | KH-A; PC-03 | 1. Click vùng Mã HĐ của dòng HĐ-A02 (không click button) | HĐ-A02 | Mở Chi tiết HĐ của HĐ-A02; tab "Xác nhận" được chọn ngay (không dừng ở tab Tổng quan) | High | Positive | UC | |
| TC_DMHD_BOX_005 | BR-09 | DMHĐ – Hộp XN | Kiểm tra điều hướng khi bấm button "Xác nhận" trên dòng HĐ | KH-A; PC-03 | 1. Click button "Xác nhận >" trên dòng HĐ-A03 | HĐ-A03 | Mở Chi tiết HĐ của HĐ-A03; tab "Xác nhận" được chọn | High | Positive | UC | [GIẢ ĐỊNH] Q-06 |

## 4.3 Dữ liệu card & tính toán
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_CALC_001 | BR-17 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư bằng Giá mua nhân Số lượng TP | KH-A; PC-03 | 1. Quan sát Tổng tiền đầu tư của HĐ-A06 | Giá mua 108.834.831 × SL 10 | Tổng tiền đầu tư = 1.088.348.310 đ | High | Positive | EP | |
| TC_DMHD_CALC_002 | BR-18 | DMHĐ – Tính toán | Kiểm tra Tổng tiền nhận về bằng Tổng tiền đầu tư cộng Lãi dự kiến | KH-A; PC-03 | 1. Quan sát Tổng tiền nhận về của HĐ-A06 | 1.088.348.310 + lãi BE 103.393.089 | Tổng tiền nhận về = 1.191.741.399 đ; lãi dự kiến khớp bảng tính BA | High | Positive | EP | [GIẢ ĐỊNH] công thức lãi – Q-07 |
| TC_DMHD_CALC_003 | BR-17 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư khi Số lượng TP = 1 | KH-A có HĐ Giá 108.834.831, SL 1; PC-03 | 1. Quan sát Tổng tiền đầu tư | 108.834.831 × 1 | Tổng tiền đầu tư = 108.834.831 đ | Medium | Boundary | BVA | |
| TC_DMHD_CALC_004 | BR-17 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư không bị làm tròn khi Giá mua lẻ | KH-A có HĐ Giá 100.333, SL 3; PC-03 | 1. Quan sát Tổng tiền đầu tư | 100.333 × 3 | Tổng tiền đầu tư = 300.999 đ (không hiển thị 301.000) | High | Boundary | EG | |
| TC_DMHD_CALC_005 | BR-17 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư không cộng phí giao dịch | KH-A có HĐ-A06 (đã trả phí khi mua) | 1. Quan sát Tổng tiền đầu tư HĐ-A06 2. Đối chiếu số tiền KH đã thanh toán khi mua | Giá × SL = 1.088.348.310 | Tổng tiền đầu tư = 1.088.348.310 đ, không bằng số tiền đã thanh toán gồm phí | Medium | Negative | EG | [GIẢ ĐỊNH] Q-08 |
| TC_DMHD_CALC_006 | BR-17 | DMHĐ – Tính toán | Kiểm tra Tổng tiền đầu tư không đổi khi giá mua hiện tại của mã TP thay đổi | KH-A; PC-03; ghi lại Tổng tiền đầu tư HĐ-A05 | 1. Nghiệp vụ cập nhật giá bán mã BHB12501 trên BE 2. Kéo xuống tải lại Danh mục HĐ | Giá mã TP đổi từ giá cũ sang giá cũ + 1.000 | Tổng tiền đầu tư HĐ-A05 giữ nguyên giá trị đã ghi | High | Negative | EG | [GIẢ ĐỊNH] Q-07 |
| TC_DMHD_CARD_001 | BR-13, BR-14 | DMHĐ – Card | Kiểm tra Ngày đáo hạn và Thời gian đầu tư khi kỳ hạn 12 tháng | KH-A; PC-03 | 1. Quan sát card HĐ-A06 | Mua 10/06/2024, 12 tháng | Thời gian đầu tư "12 tháng"; Ngày đáo hạn "10/06/2025" | Medium | Positive | EP | |
| TC_DMHD_CARD_002 | BR-13, BR-14 | DMHĐ – Card | Kiểm tra Ngày đáo hạn và Thời gian đầu tư khi kỳ hạn 6 tháng | KH-A; PC-03 | 1. Quan sát card HĐ-A05 | Mua 12/03/2025, 6 tháng | Thời gian đầu tư "6 tháng"; Ngày đáo hạn "12/09/2025" | Medium | Positive | EP | |
| TC_DMHD_CARD_003 | BR-12 | DMHĐ – Card | Kiểm tra Ngày mua bằng ngày giao dịch mua tạo HĐ | KH-A; PC-03 | 1. Quan sát Ngày mua HĐ-A04 2. Đối chiếu ngày giao dịch mua trên BE | HĐ-A04 | Ngày mua "01/04/2025" = ngày giao dịch mua trên BE | Medium | Positive | EP | |
| TC_DMHD_CARD_004 | BR-15, BR-16 | DMHĐ – Card | Kiểm tra Số lượng TP và SL khả dụng khớp dữ liệu HĐ | KH-A; PC-03 | 1. Quan sát Số lượng Trái phiếu, SL khả dụng HĐ-A06 2. Đối chiếu BE | HĐ-A06: SL 10, SL khả dụng 10 | Số lượng Trái phiếu = 10; SL khả dụng = 10; SL khả dụng ≤ Số lượng Trái phiếu | Medium | Positive | EP | Q-17 |
| TC_DMHD_CHIP_001 | BR-11 | DMHĐ – Status | Kiểm tra text status-chip của từng trạng thái | KH-A; PC-03 | 1. Quan sát chip HĐ-A01…A07 | DS-A | A01 "Chờ xử lý"; A02, A03 "Chờ xác nhận"; A04, A05 "Hiệu lực"; A06 "Hoàn thiện"; A07 "Hết hiệu lực" | Medium | Positive | EP | Q-01 |
| TC_DMHD_CHIP_002 | BR-11 | DMHĐ – Status | Kiểm tra không xuất hiện trạng thái ngoài danh sách đã định nghĩa | KH-A; PC-03 | 1. Quan sát chip toàn bộ card | DS-A | Không chip nào khác 5 giá trị BR-11 (vd "Đã tất toán", "Đang nắm giữ Outright" như mockup) | Medium | Negative | EP | [GIẢ ĐỊNH] Q-01 |
| TC_DMHD_CHIP_003 | BR-11, BR-07 | DMHĐ – Status | Kiểm tra HĐ có trong hộp cần xác nhận mang chip "Chờ xác nhận" | KH-A; PC-03 | 1. Đối chiếu chip trên card HĐ-A02, HĐ-A03 | HĐ-A02, HĐ-A03 | Card HĐ-A02, HĐ-A03 đều có chip "Chờ xác nhận" | Medium | Positive | EP | [GIẢ ĐỊNH] Q-02 |

## 4.4 Boundary – hộp cần xác nhận
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_BOX_006 | BR-05 | DMHĐ – Hộp XN | Kiểm tra hộp không hiển thị khi không có HĐ cần xác nhận | KH-C; PC-03 | 1. Quan sát đầu danh sách | KH-C | Không có hộp "Hợp đồng cần xác nhận"; card HĐ đầu tiên nằm ngay dưới thanh tab | High | Boundary | BVA | N = 0 |
| TC_DMHD_BOX_007 | BR-06 | DMHĐ – Hộp XN | Kiểm tra N trong hộp tính theo mã TP đang filter | KH-A; PC-04 với BHB12501 | 1. Quan sát hộp | BHB12501: chỉ A02 cần XN | Dòng phụ "1 hợp đồng đang chờ xác nhận lệnh"; chỉ 1 dòng HĐ-A02 | High | Boundary | BVA | N = 1 |
| TC_DMHD_BOX_008 | BR-05, BR-06 | DMHĐ – Hộp XN | Kiểm tra hộp ẩn khi mã TP đang filter không có HĐ cần xác nhận | KH-A; PC-04 với BHB12503 | 1. Quan sát đầu danh sách | BHB12503 (toàn TK vẫn có 2 HĐ cần XN) | Không hiển thị hộp; không hiện "2 hợp đồng…" | High | Negative | DT | |
| TC_DMHD_BOX_009 | BR-06 | DMHĐ – Hộp XN | Kiểm tra hộp không chứa HĐ cần xác nhận của mã TP khác khi đang filter | KH-A; PC-04 với BHB12502 | 1. Quan sát các dòng trong hộp | BHB12502 | Chỉ có dòng HĐ-A03; không có dòng HĐ-A02 (BHB12501) | High | Negative | DT | |
| TC_DMHD_BOX_010 | BR-07, BR-06 | DMHĐ – Hộp XN | Kiểm tra hộp khi 1 HĐ cần xác nhận cả Hợp đồng và Phiếu lệnh | KH-E; PC-03 | 1. Quan sát hộp | HĐ-E1 cần cả 2 | Dòng phụ "1 hợp đồng đang chờ xác nhận lệnh"; 1 dòng HĐ-E1, nhãn "Xác nhận Hợp đồng" | Medium | Boundary | EP | [GIẢ ĐỊNH] Q-05 |
| TC_DMHD_BOX_011 | BR-05 | DMHĐ – Hộp XN | Kiểm tra hộp biến mất sau khi HĐ cần xác nhận cuối cùng được xác nhận | KH-A; PC-04 với BHB12501 (N = 1) | 1. Click dòng HĐ-A02 2. Hoàn tất xác nhận ở tab Xác nhận (OTP hợp lệ) 3. Quay lại Danh mục HĐ | HĐ-A02 | Không còn hộp; card HĐ-A02 không còn chip "Chờ xác nhận" | High | Boundary | BVA | [GIẢ ĐỊNH] Q-02, Q-03 |

## 4.5 Negative – luồng phụ / Error guessing
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_FLT_001 | BR-19, BR-06 | DMHĐ – Filter | Kiểm tra bỏ filter mã TP khi bấm "Tất cả ✕" | KH-A; PC-04 với BHB12501 | 1. Click "Tất cả ✕" trên chip filter | BHB12501 | Chip filter biến mất; hiển thị đủ 7 HĐ; hộp đổi thành "2 hợp đồng đang chờ xác nhận lệnh" | Medium | Negative | UC | [GIẢ ĐỊNH – mockup] Q-10 |
| TC_DMHD_FLT_002 | BR-02 | DMHĐ – Filter | Kiểm tra filter bị bỏ khi vào lại bằng tab Danh mục Hợp đồng | KH-A; đã vào DMHĐ với filter BHB12501 | 1. Chọn tab "Danh mục Trái phiếu" 2. Chọn tab "Danh mục Hợp đồng" | – | Không có filter mã TP; hiển thị đủ 7 HĐ | Medium | Negative | UC | |
| TC_DMHD_FLT_003 | BR-02 | DMHĐ – Filter | Kiểm tra filter đổi theo khi chọn card mã TP khác | KH-A; đã vào DMHĐ với filter BHB12501 | 1. Chọn tab "Danh mục Trái phiếu" 2. Chọn card mã BHB12502 | BHB12502 | Filter "BHB12502"; chỉ HĐ-A01, A03, A07; không còn HĐ của BHB12501 | Medium | Negative | EG | |
| TC_DMHD_FLT_004 | BR-02, BR-04 | DMHĐ – Filter | Kiểm tra giữ filter khi quay lại từ Chi tiết HĐ | KH-A; PC-04 với BHB12501 | 1. Click card HĐ-A05 2. Click nút Back trên header | BHB12501 | Quay về Danh mục HĐ; filter BHB12501 còn nguyên; vẫn 3 card A02, A05, A06 | Low | Negative | EG | [GIẢ ĐỊNH] Q-10 |
| TC_DMHD_EG_001 | BR-01 | DMHĐ – Danh sách | Kiểm tra màn Danh mục HĐ khi KH chưa có HĐ | KH-B; PC-03 | 1. Quan sát màn hình | KH-B | Không có card, không có hộp cần xác nhận; hiển thị trạng thái rỗng; không lỗi/crash | Medium | Negative | EG | [GIẢ ĐỊNH] text – Q-11 |
| TC_DMHD_EG_002 | BR-01 | DMHĐ – Danh sách | Kiểm tra màn khi mất mạng lúc tải danh sách | KH-A; tắt mạng thiết bị | 1. Vào Danh mục HĐ 2. Bật mạng 3. Kéo xuống tải lại | – | Bước 1: báo lỗi kết nối, không crash, không hiện dữ liệu dở dang; Bước 3: hiển thị đủ 7 HĐ | Medium | Negative | EG | Text lỗi theo chuẩn chung app |
| TC_DMHD_EG_003 | BR-04 | DMHĐ – Chi tiết | Kiểm tra không mở 2 màn Chi tiết khi double-tap card HĐ | KH-A; PC-03 | 1. Double-tap nhanh card HĐ-A05 2. Click Back 1 lần | HĐ-A05 | Chỉ mở 1 màn Chi tiết; Back 1 lần về lại Danh mục HĐ | Low | Negative | EG | |
| TC_DMHD_EG_004 | BR-09 | DMHĐ – Hộp XN | Kiểm tra không mở trùng màn khi double-tap button "Xác nhận" trong hộp | KH-A; PC-03 | 1. Double-tap nhanh button "Xác nhận >" dòng HĐ-A02 2. Click Back 1 lần | HĐ-A02 | Chỉ mở 1 màn Chi tiết (tab Xác nhận); Back 1 lần về Danh mục HĐ | Low | Negative | EG | |
| TC_DMHD_EG_005 | BR-03 | DMHĐ – Danh sách | Kiểm tra cuộn danh sách khi có nhiều HĐ | KH có 25 HĐ; PC-03 | 1. Cuộn đến cuối danh sách 2. Đếm số card | 25 HĐ | Đủ 25 card; không trùng card; thứ tự mới → cũ giữ nguyên đến cuối | Low | Boundary | EG | [GIẢ ĐỊNH] Q-16 |
| TC_DMHD_EG_006 | BR-11 | DMHĐ – Danh sách | Kiểm tra dữ liệu cập nhật khi tải lại sau khi BE đổi trạng thái HĐ | KH-A đang mở DMHĐ; HĐ-A01 "Chờ xử lý" | 1. BE cập nhật HĐ-A01 sang "Hiệu lực" 2. Kéo xuống tải lại | HĐ-A01 | Chip HĐ-A01 đổi thành "Hiệu lực"; vị trí HĐ-A01 không đổi | Medium | Negative | EG | Q-03 |
| TC_DMHD_EG_007 | BR-05, BR-11 | DMHĐ – Hộp XN | Kiểm tra HĐ Hết hiệu lực và Hoàn thiện không xuất hiện trong hộp cần xác nhận | KH-A; PC-03 | 1. Quan sát các dòng trong hộp | HĐ-A06, HĐ-A07 | Hộp chỉ có HĐ-A02, HĐ-A03; không có HĐ-A06, HĐ-A07 | Medium | Negative | EP | |

## 4.6 Permission / Security
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_DMHD_SEC_001 | BR-21 | DMHĐ – Bảo mật | Kiểm tra danh sách chỉ chứa HĐ của tài khoản đang đăng nhập | KH-A, KH-D đều có HĐ; đăng nhập KH-A; PC-03 | 1. Đối chiếu danh sách mã HĐ với dữ liệu BE của KH-A | KH-A, KH-D | Chỉ hiển thị 7 HĐ của KH-A; không có HĐ nào của KH-D | High | Security | EG | [GIẢ ĐỊNH] BR-21 |
| TC_DMHD_SEC_002 | BR-21 | DMHĐ – Bảo mật | Kiểm tra API danh sách HĐ khi đổi sang mã tài khoản khác | Token hợp lệ của KH-A; bắt request lấy danh sách HĐ qua proxy | 1. Gửi lại request, thay tham số tài khoản bằng TK của KH-D | accountId = TK KH-D | HTTP 401/403 hoặc chỉ trả HĐ của KH-A; response không có HĐ của KH-D | High | Security | EG | Endpoint chờ tài liệu API |
| TC_DMHD_SEC_003 | BR-21 | DMHĐ – Bảo mật | Kiểm tra màn Danh mục HĐ khi phiên đăng nhập hết hạn | KH-A; PC-03; phiên đã hết hạn | 1. Kéo xuống tải lại 2. Click card HĐ-A05 | – | Thông báo hết phiên, chuyển về màn Đăng nhập; không hiển thị dữ liệu HĐ | Medium | Security | EG | |

---

# B5 – TỰ REVIEW, RTM & THỐNG KÊ

## 1. RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ | Ghi chú |
|---|---|---|---|---|---|
| BR-01 | Vào qua tab | 3 | NAV_001, EG_001, EG_002 | Đủ | |
| BR-02 | Vào qua card mã TP + filter | 5 | NAV_002, SORT_002, FLT_002, FLT_003, FLT_004 | Đủ | |
| BR-03 | Sắp xếp mới → cũ | 3 | SORT_001, SORT_002, EG_005 | Đủ | Q-09 |
| BR-04 | Card → Chi tiết HĐ | 5 | DET_001…003, FLT_004, EG_003 | Đủ | |
| BR-05 | Hộp hiện khi ≥ 1 HĐ cần XN | 5 | BOX_001, BOX_006, BOX_008, BOX_011, EG_007 | Đủ | N = 0/1/2 |
| BR-06 | Nội dung hộp, N theo filter | 7 | UI_003, BOX_001, BOX_007, BOX_008, BOX_009, BOX_010, FLT_001 | Đủ | |
| BR-07 | Dòng: Mã TP, Mã HĐ, nhãn | 4 | BOX_002, BOX_003, BOX_010, CHIP_003 | Đủ | Q-04, Q-05 |
| BR-08 | Bấm dòng → tab Xác nhận | 1 | BOX_004 | Đủ | |
| BR-09 | Button Xác nhận → tab Xác nhận | 2 | BOX_005, EG_004 | Đủ | Q-06 |
| BR-10 | Mã HĐ duy nhất | 3 | UI_002, UI_006, DET_002 | Đủ | |
| BR-11 | 5 status-chip | 5 | UI_002, CHIP_001…003, EG_006, EG_007 | Đủ | Q-01 |
| BR-12 | Ngày mua | 2 | UI_002, CARD_003 | Đủ | |
| BR-13 | Ngày đáo hạn | 3 | UI_002, CARD_001, CARD_002 | Đủ | |
| BR-14 | Thời gian đầu tư | 3 | UI_002, CARD_001, CARD_002 | Đủ | |
| BR-15 | Số lượng TP | 2 | UI_002, CARD_004 | Đủ | |
| BR-16 | SL khả dụng | 2 | UI_002, CARD_004 | Đủ | Q-17 |
| BR-17 | Tổng tiền đầu tư | 7 | UI_002, UI_008, CALC_001, CALC_003…006 | Đủ | Q-07, Q-08 |
| BR-18 | Tổng tiền nhận về | 2 | UI_002, CALC_002 | Đủ | Q-07 |
| BR-19 | [Mockup] chip filter + "Tất cả ✕" | 2 | NAV_003, FLT_001 | Đủ | |
| BR-20 | [Mockup] định dạng card | 6 | UI_001, UI_004…008 | Đủ | |
| BR-21 | Chỉ thấy HĐ của mình | 3 | SEC_001…003 | Đủ | BR giả định |

**Requirement coverage: 21/21 BR = 100%.**

## 2. Checklist (file 05)
| Mục | Kết quả | Ghi chú |
|---|---|---|
| A1. 100% BR có TC | Đạt | |
| A2. Field đủ rỗng / hợp lệ / không hợp lệ / biên | Đạt (dữ liệu hiển thị) | Màn không có input |
| A3. Luồng chính + phụ + ngoại lệ | Đạt | |
| A4. Phân quyền | Đạt | 1 role; có IDOR |
| A5. DT / ST | Đạt một phần | DT filter × N đủ; **chưa có ST** vì tài liệu không mô tả vòng đời trạng thái (Q-03) |
| A6. Error guessing | Đạt | |
| B1–B3. 12 cột, ID, 1 TC = 1 mục tiêu, Title | Đạt | |
| B4. Test data cụ thể | Đạt có điều kiện | Mã HĐ ký hiệu HĐ-Axx → thay mã thật |
| B5–B6. Expected đo được; Negative kiểm 2 ý | Đạt | |
| B7. Message nguyên văn / [GIẢ ĐỊNH] | Đạt | Text empty state, lỗi mạng chưa có (Q-11) |
| C1–C4. Không trùng, Priority, thuật ngữ, thứ tự | Đạt có điều kiện | Nhãn "Xác nhận Hợp đồng" vs mockup "Xác nhận HĐ Leg 2" (Q-04) |

**Đã sửa trong B5:** thêm BOX_009 (hộp lẫn HĐ mã TP khác), CALC_006 (giá mã TP đổi), EG_004 (double-tap button hộp), EG_007 (HĐ trạng thái cuối không vào hộp).

## 3. Thống kê
| Chỉ số | Giá trị |
|---|---|
| Tổng TC | 54 |
| High / Medium / Low | 19 (35%) / 21 (39%) / 14 (26%) |
| Positive / Negative (gồm Negative, Boundary, Security) / UI | 21 (39%) / 25 (46%) / 8 (15%) |
| TC có [GIẢ ĐỊNH] | 22 |
| Q&A mở | 18 (Blocker 0 · Major 8 · Minor 10) |

Positive : Negative ≈ 46 : 54 (không tính UI), chưa đạt 30 : 70 vì màn chỉ hiển thị, không có input.

## 4. Gợi ý
Thư mục `doc\SRS\` có `SBOND.CONTRACT.APP.01 - Danh mục Hợp đồng.doc` và `SBOND.CONTRACT.APP.02 - Chi tiết Hợp đồng.doc` là đặc tả chi tiết cho đúng màn này. Nếu đưa vào, có thể trả lời được nhiều Q&A (trạng thái, sắp xếp, filter, công thức).
