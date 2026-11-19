# SBOND.CONTRACT.APP.02 – Chi tiết Hợp đồng (App) – Test design B1→B5

Nguồn: SRS SBOND.CONTRACT.APP.02 – Chi tiết Hợp đồng (export Confluence 15/11/2026) + ảnh Figma trong SRS  
Phạm vi: bottom sheet Chi tiết HĐ (3 tab), màn Mẫu biểu, điểm chuyển sang luồng Xác nhận lệnh, API A2/O4.

## B1. Phân tích yêu cầu
### 1. Tổng quan
- Mục tiêu: KH xem chi tiết 1 HĐ trái phiếu: tổng quan, mẫu biểu, dòng tiền dự kiến, hồ sơ cần xác nhận.
- Actor: KH (NĐT trên SHSmart App). Hệ thống liên quan: BE S-Bond (A2, O4), Flex (số dư, lưu ký), ĐVVH (phát hành hồ sơ).
- Entry: bấm card HĐ ở Danh mục HĐ (APP.01). Post: chỉ xem, không đổi dữ liệu; bấm "Xác nhận" → luồng Xác nhận lệnh (ngoài phạm vi).

### 2. Feature
- F1 Mở/đóng bottom sheet · F2 Tab Tổng quan · F3 Mẫu biểu liên quan · F4 Tab Dòng tiền dự kiến · F5 Tab Xác nhận + điểm chuyển OTP · F6 Số lượng khả dụng (BIZ-25) + nhắc T-5 · F7 Lỗi/phân quyền · F8 Tracking event

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Bấm card HĐ ở Danh mục HĐ → App gọi A2 / O4 (contractNo) → mở bottom sheet "Chi tiết hợp đồng", tab "Tổng quan" active mặc định | Description; Luồng B1; Entry | Logic |
| BR-02 | Đóng / quay lại → về Danh mục HĐ; xem chi tiết không làm thay đổi dữ liệu | Luồng B6; Post-Condition | Logic |
| BR-03 | Tab Tổng quan hiển thị 12 field theo mapping A2/O4: Số HĐ, Mẫu biểu liên quan, Ngày mua, Ngày đáo hạn, Tổng tiền đầu tư, Thời gian đầu tư, Lợi suất, Số lượng TP, Số lượng khả dụng, Phí giao dịch, Lãi đầu tư dự kiến, Tổng tiền nhận về | Mục 2.1 | UI/Tích hợp |
| BR-04 | Định dạng: ngày dd/MM/yyyy; tiền #.### VND; lợi suất x,xx%/năm; số lượng #.###; thời gian "n tháng" | Mục 2.1, 2.2 | Validation/UI |
| BR-05 | BIZ-20: Tổng tiền nhận về = gốc đầu tư + lãi dự kiến theo kỳ hạn, chưa tính thuế; hiển thị chữ xanh | BIZ-20; 2.1 #12; 2.2 #4 | Logic |
| BR-06 | Mẫu biểu liên quan – "Xem" → màn "Mẫu biểu liên quan": tab theo loại (Hợp đồng mua, Phiếu lệnh đặt mua, Phụ lục…), viewer PDF, viewUrl từ documents[] (O4) | 2.1 #2; Luồng B2 | Logic/Tích hợp |
| BR-07 | Chọn tab "Dòng tiền dự kiến" → gọi A2 → tổng hợp: Lợi tức dự kiến (xanh), Lợi suất dự kiến, Tổng tiền đầu tư, Tổng tiền nhận về | Luồng B3; 2.2 #1–4 | Logic/Tích hợp |
| BR-08 | Bảng dòng tiền: Ngày (dd/MM/yyyy) \| Lệnh (Mua trái phiếu / Nhận trái tức / Bán trái phiếu) \| Dự kiến (+/-#.### VND, xanh/đỏ) | 2.2 #5–7 | UI/Logic |
| BR-09 | A2 không có dòng tiền → empty state trong tab: "Chưa có dòng tiền dự kiến" (SBOND.CONTRACT.I03) | Mục 6 #2 | UI |
| BR-10 | BIZ-24: Tab "Xác nhận" chỉ hiển thị khi HĐ còn hồ sơ cần xác nhận; ngược lại ẩn tab | BIZ-24; Luồng B4; 2.3 | Logic |
| BR-11 | Tab Xác nhận: Mã TP, Số HĐ; dòng "Hợp đồng đặt bán TP" (SELL_CONTRACT) và "Phiếu lệnh đặt bán TP" (SELL_ORDER_SLIP) chỉ hiện khi KH chưa xác nhận hồ sơ đó; nhãn "Cần xác nhận"; dòng chỉ là nhãn | 2.3 #1–4 | Logic/UI |
| BR-12 | Nút "Xem hợp đồng" luôn enable → màn Mẫu biểu liên quan: scroll đến cuối, tick cam kết | 2.3 #5 | Logic |
| BR-13 | Xem từng hồ sơ → đánh dấu "Đã đọc"; nút "Xác nhận" chỉ enable khi đã xem đủ hồ sơ cần xác nhận; bấm → luồng Xác nhận lệnh (OTP) | Luồng B5; V02; BIZ-24; 2.3 #5 | Validation/Logic |
| BR-14 | V01: contractNo phải thuộc KH đăng nhập; vi phạm → popup lỗi chuẩn [G-Code], BND-403 | Mục 5 V01 | Phân quyền |
| BR-15 | Lỗi hệ thống / token: BND-901, BND-999, AUTH_001 → popup lỗi chuẩn SHSmart, CTA Đóng / Đăng nhập | Mục 6 #3 | Tích hợp/Bảo mật |
| BR-16 | Popup Info SBOND.CONTRACT.I02 "Hồ sơ đã phát hành", CTA "Đóng" | Mục 6 #1 | UI |
| BR-17 | BIZ-25: KL khả dụng tăng/giảm khi KH chuyển nhượng TP tại quầy. TĂNG: ưu tiên HĐ đáo hạn gần → xa (cùng ngày: HĐ thiếu KL nhiều nhất). GIẢM: ưu tiên HĐ đáo hạn xa → gần (cùng ngày: HĐ có KL thấp nhất) | BIZ-25; 2.1 #9; Mục 8 | Logic |
| BR-18 | Nhắc T-5 hoàn thiện số lượng TP → gửi noti Bond_08 | Mục 8 | Tích hợp |
| BR-19 | Tracking event: SBOND.CONTRACT.DETAIL / DOCS.VIEW / CASHFLOW / CONFIRM | Mục 7 | Tích hợp |
| BR-20 | API: A2 GET /api/v1/bond/term/app/appendix/detail; O4 GET /api/v1/bond/app/order/documents/filter | Mục 3 | Tích hợp |

### 4. Field list
| Tab | Field | Kiểu | Bắt buộc | Ràng buộc / định dạng | Mapping | Message / khi rỗng |
|---|---|---|---|---|---|---|
| Tổng quan | Số HĐ | Text | Y | — | data.orderNo – A2 | — |
| Tổng quan | Mẫu biểu liên quan – "Xem" | Link | Y | Mở màn Mẫu biểu, tab theo loại | data[].businessType, viewUrl – O4 | documents rỗng: chưa rõ (Q-19) |
| Tổng quan | Ngày mua | Date | Y | dd/MM/yyyy | data.orderDate – A2 | — |
| Tổng quan | Ngày đáo hạn | Date | Y | dd/MM/yyyy | data.orderDate – A2 (nghi sai, Q-01) | — |
| Tổng quan | Tổng tiền đầu tư | Number | Y | #.### VND | data.totalInvestmentAmount – A2 | — |
| Tổng quan | Thời gian đầu tư | Text | Y | n tháng | data.termType – A2 | — |
| Tổng quan | Lợi suất | Number | Y | x,xx%/năm | data.rate – A2 | — |
| Tổng quan | Số lượng TP | Number | Y | #.### | data.totalQuantity – A2 | — |
| Tổng quan | Số lượng khả dụng | Number | Y | #.###; BIZ-25 | data.totalQuantity – A2 (nghi sai, Q-02) | — |
| Tổng quan | Phí giao dịch | Number | Y | #.### VND | data.transactionFee – A2 | — |
| Tổng quan | Lãi đầu tư dự kiến | Number | Y | #.### VND | data.interest – A2 | — |
| Tổng quan | Tổng tiền nhận về | Number (xanh) | Y | #.### VND; BIZ-20 | data.interest – A2 (nghi sai, Q-03) | — |
| Dòng tiền | Lợi tức dự kiến | Number (xanh) | Y | #.### VND | data.interest – A2 | — |
| Dòng tiền | Lợi suất (dự kiến) | Number | Y | x,xx%/năm | data.rate – A2 | — |
| Dòng tiền | Tổng tiền đầu tư | Number | Y | #.### VND | data.totalInvestmentAmount – A2 | — |
| Dòng tiền | Tổng tiền nhận về | Number | Y | #.### VND; BIZ-20 | data.totalReceive – A2 | — |
| Dòng tiền | Bảng – Ngày / Lệnh / Dự kiến | Date / Enum / Number | Y | dd/MM/yyyy; 3 giá trị enum; +/-#.### VND xanh/đỏ | data.cashFlows[] – A2 | Rỗng → I03 "Chưa có dòng tiền dự kiến" |
| Xác nhận | Mã TP / Số HĐ | Text | Y | — | data.symbol / data.orderNo – A2 | — |
| Xác nhận | Hợp đồng đặt bán TP | Row + nhãn | C | "Cần xác nhận" | confirmDocuments[type=SELL_CONTRACT] | — |
| Xác nhận | Phiếu lệnh đặt bán TP | Row + nhãn | C | "Cần xác nhận" | confirmDocuments[type=SELL_ORDER_SLIP] | — |
| Xác nhận | Nút "Xem hợp đồng" / "Xác nhận" | Button | Y | Xem hợp đồng luôn enable; Xác nhận enable khi đọc đủ (V02) | — | Disable khi chưa đọc đủ |

### 5. Q&A
| Q ID | Câu hỏi | Lý do | Mức | Giả định đề xuất |
|---|---|---|---|---|
| Q-01 | Ngày đáo hạn (2.1 #4) map data.orderDate – trùng field với Ngày mua. Field thật là gì? | Nếu đúng như SRS thì Ngày đáo hạn = Ngày mua → sai nghiệp vụ | Major | Có field riêng cho ngày đáo hạn (vd maturityDate); Expected = ngày đáo hạn thực của HĐ |
| Q-02 | Số lượng khả dụng (2.1 #9) map data.totalQuantity – trùng với Số lượng TP. Field thật là gì? | BIZ-25 yêu cầu khả dụng khác Số lượng TP sau chuyển nhượng | Major | Có field riêng (vd availableQuantity) |
| Q-03 | Tổng tiền nhận về: tab Tổng quan map data.interest, tab Dòng tiền map data.totalReceive. Dùng field nào? | map interest → hiển thị bằng Lãi dự kiến, sai BIZ-20 | Major | Cả 2 tab dùng data.totalReceive; 2 tab phải bằng nhau |
| Q-04 | BIZ-20 "gốc đầu tư" = Tổng tiền đầu tư (đã gồm phí?) hay tổng mệnh giá? Figma: 1.021.111.000 + 103.393.999 ≠ 1.103.393.999 | Ảnh hưởng công thức kiểm Tổng tiền nhận về | Major | Gốc = Tổng tiền đầu tư (totalInvestmentAmount); số Figma là dữ liệu mẫu |
| Q-05 | Luồng xác nhận đang mâu thuẫn: (a) Luồng B5 + V02 + Figma: xem từng hồ sơ → "Đã đọc" → nút "Xác nhận" ở tab enable → OTP; (b) 2.3 #5: "Xem hợp đồng" → Mẫu biểu: scroll cuối, tick cam kết, "Xác nhận" → OTP; (c) BIZ-24: nút "Xác nhận" enable khi CÒN biểu mẫu chưa xác nhận. Figma vẽ nút "Xem hợp đồng" màu xám (giống disable) dù SRS ghi luôn enable | Quyết định toàn bộ Expected của nhóm TC_HDCT_XN | Major | Theo (a)+Figma: tab Xác nhận có nút "Xem hợp đồng" (enable) → viewer hồ sơ cần xác nhận; scroll đến cuối + tick cam kết → hồ sơ "Đã đọc ✓"; khi tất cả "Đã đọc" nút đổi thành "Xác nhận" (cam, enable) → OTP |
| Q-06 | Thế nào là "đã xem" 1 hồ sơ: mở ra / scroll đến cuối / scroll + tick cam kết? | Ảnh hưởng điều kiện enable nút Xác nhận (V02) | Major | Scroll đến cuối trang cuối + tick cam kết |
| Q-07 | Trạng thái "Đã đọc" có giữ khi đóng bottom sheet rồi mở lại HĐ không? | Ảnh hưởng TC đóng/mở lại | Minor | Không giữ – mở lại thì hồ sơ về "Cần xác nhận" |
| Q-08 | Popup I02 "Hồ sơ đã phát hành": sự kiện kích hoạt để trống ("–"). Hiện khi nào? SRS CTA chỉ "Đóng", Figma có thêm "Liên hệ CSKH" + hotline 1900 638 588; nội dung SRS "liên hệ CSKH:" thiếu số | Không xác định được bước tái hiện popup | Major | Chưa giả định được trigger → TC_HDCT_POP chỉ kiểm nội dung/CTA khi popup xuất hiện; có thể Blocked |
| Q-09 | Tên tham số A2 / O4 (contractNo hay orderNo)? contractNo không tồn tại / thiếu tham số trả mã lỗi gì? | Cần để viết request API và Expected lỗi | Major | Query param orderNo; không tồn tại → popup lỗi chuẩn (BND-403 hoặc 404); thiếu tham số → 400 |
| Q-10 | Bảng dòng tiền sắp xếp theo thứ tự nào? | Không có trong SRS | Minor | Ngày tăng dần (cũ → mới) |
| Q-11 | Giá trị enum data.cashFlows[].businessType (SRS tham chiếu Q20 – không có trong tài liệu)? | Cần để kiểm mapping nhãn | Minor | BUY → Mua trái phiếu; COUPON → Nhận trái tức; SELL → Bán trái phiếu |
| Q-12 | Figma dòng tiền dùng dấu phẩy "-10,000 VND", SRS quy định "+/-#.### VND". Theo bên nào? | Lệch định dạng | Minor | Theo SRS: dấu chấm phân cách nghìn |
| Q-13 | Thời gian đầu tư map termType – giá trị gốc là gì (6M / 6 / SIX_MONTH)? Figma "6 tháng" nhưng Ngày mua 20/08/2025 → Đáo hạn 20/08/2026 (12 tháng) | Kiểm mapping + tính nhất quán | Minor | termType = số tháng; hiển thị "{n} tháng" khớp khoảng Ngày mua–Ngày đáo hạn |
| Q-14 | Field Required = Y nhưng API trả null/thiếu → hiển thị gì? | Edge case dữ liệu xấu | Minor | Hiển thị "--", không crash |
| Q-15 | Lợi suất có >2 chữ số thập phân thì làm tròn thế nào? | Định dạng x,xx | Minor | Làm tròn half-up 2 chữ số |
| Q-16 | BIZ-25: KL tăng lớn hơn phần thiếu của HĐ ưu tiên / KL giảm lớn hơn khả dụng của HĐ ưu tiên → có phân bổ tràn sang HĐ kế tiếp không? Có chặn khả dụng > Số lượng TP / < 0? | Ảnh hưởng Expected case biên | Major | Phân bổ tràn sang HĐ kế tiếp theo đúng thứ tự ưu tiên; 0 ≤ khả dụng ≤ Số lượng TP |
| Q-17 | Noti Bond_08: nội dung, kênh, giờ gửi; chỉ gửi khi khả dụng < Số lượng TP? T-5 tính ngày lịch hay ngày làm việc? | Không có trong SRS | Minor | Gửi 1 lần tại T-5 (ngày lịch) khi khả dụng < Số lượng TP |
| Q-18 | HĐ ở trạng thái nào được mở chi tiết (Chờ xử lý / Hiệu lực / Hoàn thành / Hết hiệu lực)? | Phạm vi dữ liệu test | Minor | Mọi HĐ hiển thị trên Danh mục HĐ đều mở được |
| Q-19 | documents[] rỗng thì link "Xem" ẩn, disable hay mở màn trống? Danh sách đầy đủ loại mẫu biểu và tab mặc định? | Field Required = Y nhưng có HĐ chưa phát hành hồ sơ | Minor | Không hiển thị link "Xem"; tab đầu = Hợp đồng mua |
| Q-20 | SRS có "Phí giao dịch" (2.1 #10) nhưng Figma tab Tổng quan không có | Lệch UI | Minor | Theo SRS: có hiển thị |
| Q-21 | Tab Dòng tiền gọi lại A2 mỗi lần chọn tab hay dùng dữ liệu đã tải khi mở chi tiết? | Ảnh hưởng TC gọi API / độ tươi dữ liệu | Minor | Gọi lại A2 mỗi lần chọn tab |
| Q-22 | Sau xác nhận thành công/thất bại (luồng OTP, ngoài phạm vi) quay lại chi tiết HĐ: tab Xác nhận có ẩn / giữ nguyên? | Kiểm chuyển trạng thái BIZ-24 | Minor | Thành công → ẩn tab; thất bại → giữ tab, hồ sơ "Cần xác nhận" |
| Q-23 | A2 thành công nhưng O4 lỗi (hoặc ngược lại) → màn hiển thị gì? | Gọi 2 API song song | Minor | Vẫn hiển thị Tổng quan từ A2; bấm "Xem" hiện popup lỗi chuẩn |
| Q-24 | Khi A2 không có dòng tiền: phần tổng hợp (Lợi tức, Lợi suất, Tổng tiền…) có hiển thị không? | Mục 6 chỉ nói empty state | Minor | Phần tổng hợp vẫn hiển thị, chỉ bảng thay bằng empty state |
| Q-25 | viewUrl của mẫu biểu có yêu cầu token / có thời hạn (signed URL) không? | Rủi ro lộ hồ sơ của KH khác | Major | viewUrl chỉ truy cập được bằng phiên của chính KH; URL hết hạn sau phiên |

### 6. Rủi ro chính
| # | Rủi ro | Tác động | Mức |
|---|---|---|---|
| 1 | Mapping API sai trong SRS: Ngày đáo hạn, Số lượng khả dụng, Tổng tiền nhận về đều trỏ nhầm field | Hiển thị sai số liệu tiền / ngày đáo hạn cho KH → khiếu nại | High |
| 2 | Công thức BIZ-20 chưa chốt "gốc"; Figma tự mâu thuẫn | Sai Tổng tiền nhận về – con số KH quan tâm nhất | High |
| 3 | Luồng xác nhận hồ sơ có 3 mô tả khác nhau (Luồng B5 / 2.3 #5 / BIZ-24) + Figma | Dev và QA hiểu khác nhau; KH có thể xác nhận khi chưa đọc hồ sơ (rủi ro pháp lý) | High |
| 4 | IDOR: contractNo / viewUrl truy cập được hồ sơ KH khác | Lộ dữ liệu cá nhân, hợp đồng tài chính | High |
| 5 | BIZ-25 phân bổ KL khả dụng nhiều HĐ, case tràn chưa định nghĩa | Khả dụng sai → ảnh hưởng bán/thanh lý ở luồng khác | Medium |

## B2. Chiến lược test
- **In-scope:** 3 tab bottom sheet, màn Mẫu biểu liên quan, điều kiện enable và điểm chuyển sang OTP, hiển thị BIZ-25, noti Bond_08, popup lỗi/info, phân quyền V01, tracking event, API A2/O4.
- **Out-of-scope:** nhập/xác thực OTP và màn kết quả (luồng Xác nhận lệnh – chỉ dùng làm bước tiền đề ở XN_020/021), Danh mục HĐ (APP.01), bản Web (WEB.02), logic Flex nội bộ, nội dung pháp lý trong PDF.
- **Loại test:** Functional, UI, API, Integration, Security cơ bản (IDOR, token), Error guessing (mạng, double-tap, dữ liệu xấu).

| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC |
|---|---|---|---|---|
| BR-01, BR-02 | UC | EG | High | NAV 9, UI 2 |
| BR-03, BR-04 | EP | BVA, EG | High | TQ 20, UI 2 |
| BR-05 | DT | EG | High | 4 + API 1 |
| BR-06 | UC | EP, EG | High | MB 10 + API 4 |
| BR-07, BR-08, BR-09 | EP | BVA, EG | High | DT 18 + API 2 |
| BR-10, BR-11 | DT | EG | High | 8 + API 2 |
| BR-12, BR-13 | ST | BVA, EG | High | 13 |
| BR-14 | EG | — | High | PQ 3 + API 6 |
| BR-15 | EP | EG | Medium | ERR 6 + API 4 |
| BR-16 | UC | — | Low | 2 |
| BR-17 | DT | BVA | High | 7 |
| BR-18 | BVA | DT | Medium | 4 |
| BR-19 | UC | — | Low | 4 |
| BR-20 | EP | EG | High | API 22 |

- **Môi trường & dữ liệu:** UAT; iOS + Android (1 máy màn hẹp 360dp); Charles/Proxyman (xem/sửa request, mock lỗi); Postman; công cụ debug analytics; tài khoản BO để chuyển nhượng tại quầy. Dữ liệu: xem bảng dưới.
- **Exit criteria:** 100% TC High đã chạy; 0 bug Critical/Major mở; Q&A Major đã có trả lời hoặc được PO chấp nhận giả định; RTM 100% BR có TC Pass.

### Dữ liệu test
| Mã DL | KH | Số HĐ / Mã TP | Trạng thái HĐ | Ngày mua → Đáo hạn | Tổng tiền đầu tư / Phí | Kỳ hạn / Lợi suất | SL TP / Khả dụng | Lãi dự kiến | Hồ sơ cần xác nhận (confirmDocuments) | Mẫu biểu (documents[]) | Dòng tiền (cashFlows) | Dùng cho |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| H01 | KH01 | 20240610BGB1231245325345 / TPQ12312 | Hiệu lực | 20/08/2025 → 20/08/2026 | 1.021.111.000 / 1.111.000 | 12 tháng / 9,5% | 10 / 10 | 103.393.999 | SELL_CONTRACT + SELL_ORDER_SLIP (cả 2 chưa xác nhận) | Hợp đồng mua, Phiếu lệnh đặt mua, Phụ lục | 4 dòng: 20/08/2025 BUY -1.021.111.000; 20/02/2026 COUPON +48.000.000; 20/08/2026 COUPON +55.393.999; 20/08/2026 SELL +1.021.111.000 | Luồng chính, Tổng quan, Dòng tiền, Xác nhận |
| H02 | KH01 | 20250115BGB0000000000002 / TPQ20001 | Hiệu lực | 15/01/2026 → 15/01/2027 | 12.345.678.900 / 0 | 12 tháng / 8% | 1.250 / 1.250 | 987.654.312 | Không còn (đã xác nhận cả 2) | Hợp đồng mua, Phiếu lệnh đặt mua | 2 dòng | Ẩn tab Xác nhận; số lớn; số nguyên lợi suất |
| H03 | KH01 | 20250301BGB0000000000003 / TPQ20002 | Hiệu lực | 05/03/2026 → 05/09/2026 | 100.000.000 / 100.000 | 6 tháng / 9,555% | 1 / 1 | 4.777.500 | Chỉ SELL_ORDER_SLIP | Hợp đồng mua, Phiếu lệnh đặt mua | 2 dòng | Tab Xác nhận 1 hồ sơ; làm tròn lợi suất; ngày 1 chữ số |
| H04 | KH01 | 20250401BGB0000000000004 / TPQ20003 | Chờ xử lý | 01/04/2026 → 01/10/2026 | 50.000.000 / 50.000 | 6 tháng / 8,2% | 1 / 1 | 2.050.000 | Không còn | [] (chưa phát hành) | [] (rỗng) | Empty state dòng tiền; documents rỗng |
| H05 | KH01 | 20250501BGB0000000000005 / TPQ12312 | Hiệu lực | 10/05/2026 → 10/05/2027 | 1.000.000.000 / 1.000.000 | 12 tháng / 9% | 10 / 7 | 90.000.000 | Không còn | Hợp đồng mua, Phiếu lệnh đặt mua | 4 dòng | Khả dụng ≠ Số lượng TP |
| H06 | KH01 | 20240101BGB0000000000006 / TPQ19999 | Hoàn thành | 01/01/2025 → 01/07/2025 | 200.000.000 / 200.000 | 6 tháng / 8% | 2 / 0 | 8.000.000 | Không còn | Hợp đồng mua, Phiếu lệnh đặt mua | 3 dòng | HĐ đã tất toán (Q-18) |
| H07 | KH01 | 20250601BGB0000000000007 / TPQ20004 | Hiệu lực | 01/06/2026 → 01/06/2027 | 300.000.000 / 300.000 | 12 tháng / 9% | 3 / 3 | 27.000.000 | Chỉ SELL_CONTRACT | Hợp đồng mua, Phiếu lệnh đặt mua | 3 dòng | Tab Xác nhận chỉ HĐ đặt bán |
| H08 | KH01 | 20250701BGB0000000000008 / TPQ20005 | Hiệu lực | 01/07/2026 → 01/01/2027 | 100.000.000 / 0 | 6 tháng / 8% | 1 / 1 | 4.000.000 (Ngày đáo hạn = null – mock) | Không còn | Hợp đồng mua | 2 dòng (1 dòng businessType = "OTHER" – mock) | Phí 0; null field; enum lạ |
| H09 | KH01 | 20250801BGB0000000000009 / TPQ20006 | Hiệu lực | 01/08/2026 → 01/08/2028 | 500.000.000 / 500.000 | 24 tháng / 9% | 5 / 5 | 90.000.000 | Không còn | Hợp đồng mua, Phiếu lệnh đặt mua | 25 dòng (trả lãi hằng tháng) | Danh sách dòng tiền dài |
| H20 | KH02 | 20250901BGB0000000000020 / TPQ20007 | Hiệu lực | 01/09/2026 → 01/09/2027 | 200.000.000 / 200.000 | 12 tháng / 9% | 2 / 2 | 18.000.000 | SELL_CONTRACT | Hợp đồng mua | 3 dòng | IDOR – HĐ của KH khác |
| H10 / H11 | KH03 | H10 đáo hạn 10/03/2027; H11 đáo hạn 10/09/2027 (cùng TPQ30001) | Hiệu lực | — | — | — | H10: 10 / 8 (thiếu 2); H11: 10 / 7 (thiếu 3) | — | — | — | — | BIZ-25 TĂNG – khác ngày đáo hạn |
| H12 / H13 | KH04 | Cùng đáo hạn 10/12/2027 (TPQ30002) | Hiệu lực | — | — | — | H12: 10 / 9 (thiếu 1); H13: 10 / 6 (thiếu 4) | — | — | — | — | BIZ-25 TĂNG – cùng ngày |
| H14 / H15 | KH05 | H14 đáo hạn 10/03/2027; H15 đáo hạn 10/09/2027 (TPQ30003) | Hiệu lực | — | — | — | H14: 10 / 10; H15: 10 / 10 | — | — | — | — | BIZ-25 GIẢM – khác ngày |
| H16 / H17 | KH06 | Cùng đáo hạn 10/12/2027 (TPQ30004) | Hiệu lực | — | — | — | H16: 10 / 5; H17: 10 / 8 | — | — | — | — | BIZ-25 GIẢM – cùng ngày |

### Precondition dùng chung
| Mã | Nội dung |
|---|---|
| PC-01 | KH01 đã đăng nhập SHSmart App (build UAT), đang ở S-Bond > Nắm giữ > tab "Danh mục hợp đồng"; dữ liệu theo sheet "Dữ liệu test" |
| PC-02 | PC-01 + đã bấm card H01, bottom sheet "Chi tiết hợp đồng" đang mở ở tab "Tổng quan" |
| PC-03 | PC-02 + đang ở tab "Dòng tiền dự kiến" |
| PC-04 | PC-02 + đang ở tab "Xác nhận"; 2 hồ sơ H01 đang "Cần xác nhận" |
| PC-API | Postman, môi trường UAT; Header Authorization: Bearer <token KH01 hợp lệ> |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên |
|---|---|---|---|
| Lợi suất | 9,5 → 9,50; 8 → 8,00 | — | 9,555 → 9,56 (làm tròn) |
| Tiền | 1.021.111.000 | null → "--" | 0 VND; 12.345.678.900 |
| Ngày | 20/08/2025 | null → "--" | 05/03/2026 (1 chữ số) |
| Số lượng | 10 | — | 1.250 (≥ 1.000); khả dụng 0 |
| cashFlows | 4 dòng | enum lạ "OTHER" | 0 dòng (empty); 25 dòng |
| Hồ sơ cần xác nhận | 2 | — | 0 (ẩn tab); 1 |
| T-n noti Bond_08 | T-5 | — | T-6, T-4 |

### Decision table – hiển thị tab Xác nhận (BIZ-24)
| Điều kiện / Rule | R1 | R2 | R3 | R4 |
|---|---|---|---|---|
| SELL_CONTRACT chưa xác nhận | Y | Y | N | N |
| SELL_ORDER_SLIP chưa xác nhận | Y | N | Y | N |
| → Tab Xác nhận | Hiện | Hiện | Hiện | Ẩn |
| → Dòng HĐ đặt bán TP | Có | Có | Không | — |
| → Dòng Phiếu lệnh đặt bán TP | Có | Không | Có | — |
| TC | XN_001, H01 | XN_004, H07 | XN_003, H03 | XN_002, H02 |

### State transition – hồ sơ cần xác nhận (V02, giả định Q-05/Q-06)
| Trạng thái \ Sự kiện | Mở/Xem | Đọc đủ 1 hồ sơ | Ngoại lệ | Thoát / Kết quả OTP |
|---|---|---|---|---|
| Cần xác nhận | Mở viewer, chưa cuộn hết → tick disable (XN_010) | Cuộn hết + tick → Đã đọc ✓ (XN_014) | Cuộn hết, không tick / bỏ tick → giữ (XN_012, XN_013) | Đóng bottom sheet → giữ (XN_019) |
| Đã đọc ✓ (chưa đủ) | — | Hồ sơ còn lại đọc xong → tất cả Đã đọc (XN_015) | — | Đóng bottom sheet → reset Cần xác nhận (XN_019) |
| Tất cả Đã đọc | — | Bấm Xác nhận → OTP (XN_017) | Double-tap → 1 OTP (XN_018) | OTP OK → tab ẩn (XN_020); OTP fail → giữ (XN_021) |

### Decision table – phân bổ KL khả dụng (BIZ-25)
| Điều kiện / Rule | R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|---|
| Chiều | GIẢM | GIẢM | TĂNG | TĂNG | TĂNG | GIẢM |
| Ngày đáo hạn các HĐ | Khác | Cùng | Khác | Cùng | Khác | Khác |
| KL vs phần thiếu/khả dụng HĐ ưu tiên | Đủ | Đủ | Đủ | Đủ | Vượt | Vượt |
| → HĐ được ghi | Xa nhất | KL thấp nhất | Gần nhất | Thiếu nhiều nhất | Gần nhất, dư sang HĐ kế | Xa nhất, dư sang HĐ kế |
| TC | KL_001 | KL_002 | KL_003 | KL_004 | KL_005 | KL_006 |

### Error guessing áp dụng
- Double-tap card / nút Xác nhận · mất mạng khi mở chi tiết, đổi tab, tải PDF · API chậm, lỗi 1 trong 2 API · null field, enum lạ, danh sách dài · đổi tài khoản trên cùng máy, sửa request (IDOR), SQLi tham số · màn hẹp, số dài.

## B4. Test case
### 4.1 App (109 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDCT_UI_001 | BR-01, BR-10 | Chi tiết HĐ – Layout | Kiểm tra layout mặc định bottom sheet khi mở HĐ còn hồ sơ cần xác nhận | PC-01 | 1. Bấm card H01 | KH01 – H01 | - Bottom sheet trượt lên, nền Danh mục HĐ phía sau bị làm mờ<br>- Header: nút "<" bên trái, tiêu đề "Chi tiết hợp đồng" ở giữa<br>- 3 tab theo thứ tự: "Tổng quan" \| "Dòng tiền dự kiến" \| "Xác nhận"<br>- Tab "Tổng quan" active (chữ cam + gạch chân cam) | Low | UI | EG | Đối chiếu Figma màn 3 |
| TC_HDCT_UI_002 | BR-03 | Chi tiết HĐ – Layout | Kiểm tra thứ tự nhãn và vị trí giá trị các field tab Tổng quan khi mở H01 | PC-02 | 1. Quan sát tab Tổng quan từ trên xuống | KH01 – H01 | - 12 nhãn theo thứ tự: Số HĐ, Mẫu biểu liên quan, Ngày mua, Ngày đáo hạn, Tổng tiền đầu tư, Thời gian đầu tư, Lợi suất, Số lượng TP, Số lượng khả dụng, Phí giao dịch, Lãi đầu tư dự kiến, Tổng tiền nhận về<br>- Nhãn căn trái, giá trị căn phải<br>- "Xem" màu xanh dương, gạch chân<br>- Có đường kẻ ngăn cách trước dòng "Tổng tiền nhận về" | Low | UI | EG | [GIẢ ĐỊNH] Phí giao dịch hiển thị theo SRS – Q-20 |
| TC_HDCT_UI_003 | BR-07, BR-08 | Chi tiết HĐ – Layout | Kiểm tra layout tab Dòng tiền dự kiến khi HĐ có dòng tiền | PC-02 | 1. Bấm tab "Dòng tiền dự kiến" | KH01 – H01 | - Box 1: trái "Lợi tức dự kiến" (giá trị xanh lá), phải "Lợi suất dự kiến" (giá trị xanh lá)<br>- 2 ô cạnh nhau: "Tổng tiền đầu tư" \| "Tổng tiền nhận về"<br>- Header bảng: "Ngày" (trái) \| "Dự kiến" (phải)<br>- Mỗi dòng: Ngày – Lệnh – Số tiền; phân cách bằng đường kẻ | Low | UI | EG | Đối chiếu Figma màn 4 |
| TC_HDCT_UI_004 | BR-11, BR-12 | Chi tiết HĐ – Layout | Kiểm tra layout tab Xác nhận khi HĐ còn 2 hồ sơ cần xác nhận | PC-02 | 1. Bấm tab "Xác nhận" | KH01 – H01 | - Dòng: "Mã TP" \| "Số HĐ" \| "Hợp đồng đặt bán TP" \| "Phiếu lệnh đặt bán TP"<br>- 2 dòng hồ sơ có nhãn "Cần xác nhận" bên phải (chữ xám)<br>- Nút "Xem hợp đồng" full-width ở đáy | Low | UI | EG | Figma vẽ nút xám – Q-05 |
| TC_HDCT_UI_005 | BR-03 | Chi tiết HĐ – Layout | Kiểm tra hiển thị Số HĐ khi Số HĐ dài 24 ký tự trên màn hẹp | PC-01; thiết bị màn 5.4" (iPhone 13 mini) hoặc 360dp Android | 1. Bấm card H01 | Số HĐ = 20240610BGB1231245325345 (24 ký tự) | - Số HĐ hiển thị đủ 24 ký tự trên 1 dòng hoặc xuống dòng gọn, không bị cắt "..."<br>- Không đè lên nhãn "Số HĐ" | Low | UI | EG |  |
| TC_HDCT_NAV_001 | BR-01, BR-20 | Chi tiết HĐ – Mở / Đóng | Kiểm tra mở Chi tiết HĐ khi bấm card H01 ở Danh mục HĐ | PC-01; bật proxy (Charles/Proxyman) để xem request | 1. Bấm card H01<br>2. Xem request trên proxy<br>3. Quan sát màn hình | KH01 – H01 | - App gọi A2 GET /api/v1/bond/term/app/appendix/detail và O4 GET /api/v1/bond/app/order/documents/filter với tham số = 20240610BGB1231245325345<br>- Bottom sheet "Chi tiết hợp đồng" mở, tab "Tổng quan" active<br>- Số HĐ = 20240610BGB1231245325345 | High | Positive | UC | [GIẢ ĐỊNH] tên tham số – Q-09 |
| TC_HDCT_NAV_002 | BR-01 | Chi tiết HĐ – Mở / Đóng | Kiểm tra mở đúng HĐ được chọn khi danh sách có nhiều HĐ | PC-01 | 1. Bấm card H02 (card thứ 2 trong danh sách) | KH01 – H02 | - Bottom sheet hiển thị Số HĐ = 20250115BGB0000000000002<br>- Không hiển thị dữ liệu của H01 | Medium | Positive | EP |  |
| TC_HDCT_NAV_003 | BR-02 | Chi tiết HĐ – Mở / Đóng | Kiểm tra quay về Danh mục HĐ khi bấm nút "<" trên bottom sheet | PC-02 | 1. Bấm nút "<" trên header bottom sheet | KH01 – H01 | - Bottom sheet đóng<br>- Hiển thị màn Danh mục HĐ, tab "Danh mục hợp đồng" vẫn active<br>- Danh sách giữ nguyên vị trí cuộn và bộ lọc đang áp dụng | Medium | Positive | UC | [GIẢ ĐỊNH] giữ vị trí cuộn/bộ lọc |
| TC_HDCT_NAV_004 | BR-02 | Chi tiết HĐ – Mở / Đóng | Kiểm tra đóng bottom sheet khi bấm nút Back hệ thống Android | PC-02; thiết bị Android | 1. Bấm nút Back cứng / vuốt Back của Android | KH01 – H01 | - Bottom sheet đóng, quay về Danh mục HĐ<br>- App không thoát khỏi S-Bond | Low | Positive | EG | [GIẢ ĐỊNH] hành vi Back hệ thống |
| TC_HDCT_NAV_005 | BR-02 | Chi tiết HĐ – Mở / Đóng | Kiểm tra xem chi tiết không làm thay đổi dữ liệu HĐ khi duyệt qua 3 tab rồi đóng | PC-02; bật proxy | 1. Bấm tab "Dòng tiền dự kiến"<br>2. Bấm tab "Xác nhận"<br>3. Bấm "<" để đóng<br>4. Bấm lại card H01<br>5. Xem request trên proxy | KH01 – H01 | - Proxy chỉ có request GET (A2, O4), không có POST/PUT/DELETE<br>- Lần mở lại: toàn bộ giá trị tab Tổng quan giống lần 1<br>- Tab Xác nhận vẫn hiển thị, 2 hồ sơ vẫn "Cần xác nhận" | Medium | Negative | UC |  |
| TC_HDCT_NAV_006 | BR-01 | Chi tiết HĐ – Mở / Đóng | Kiểm tra số lần mở bottom sheet khi bấm card H01 2 lần liên tiếp nhanh | PC-01; bật proxy | 1. Bấm card H01 2 lần liên tiếp trong < 0,5 giây | KH01 – H01 | - Chỉ 1 bottom sheet được mở (không chồng 2 lớp)<br>- A2 chỉ được gọi 1 lần | Medium | Negative | EG |  |
| TC_HDCT_NAV_007 | BR-01 | Chi tiết HĐ – Mở / Đóng | Kiểm tra dữ liệu không bị lẫn khi mở HĐ khác ngay sau khi đóng HĐ trước | PC-02 | 1. Bấm "<" đóng H01<br>2. Bấm card H02 ngay lập tức<br>3. Quan sát tab Tổng quan | KH01 – H01, H02 | - Hiển thị Số HĐ 20250115BGB0000000000002, Tổng tiền đầu tư 12.345.678.900 VND<br>- Không có giá trị nào của H01 nháy lên trước khi tải xong | Medium | Negative | EG |  |
| TC_HDCT_NAV_008 | BR-01 | Chi tiết HĐ – Mở / Đóng | Kiểm tra giữ đúng dữ liệu tab Tổng quan khi chuyển qua lại giữa các tab | PC-02 | 1. Bấm tab "Dòng tiền dự kiến"<br>2. Bấm tab "Tổng quan" | KH01 – H01 | - Tab Tổng quan hiển thị lại đủ 12 field, giá trị giống lúc mở (Số HĐ 20240610BGB1231245325345, Tổng tiền đầu tư 1.021.111.000 VND) | Low | Positive | EG |  |
| TC_HDCT_NAV_009 | BR-01, BR-10 | Chi tiết HĐ – Mở / Đóng | Kiểm tra mở Chi tiết HĐ khi HĐ ở trạng thái Hoàn thành | PC-01 | 1. Bấm card H06 (trạng thái Hoàn thành) | KH01 – H06 | - Bottom sheet mở, tab Tổng quan hiển thị dữ liệu H06<br>- Chỉ có 2 tab "Tổng quan" \| "Dòng tiền dự kiến" | Medium | Positive | EP | [GIẢ ĐỊNH] mọi trạng thái đều mở được – Q-18 |
| TC_HDCT_TQ_001 | BR-03 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra giá trị Số HĐ khi mở chi tiết H01 | PC-02; có response A2 của H01 | 1. Đối chiếu dòng "Số HĐ" với data.orderNo trong response A2 | KH01 – H01 | - "Số HĐ" = 20240610BGB1231245325345 = data.orderNo | Medium | Positive | EP |  |
| TC_HDCT_TQ_002 | BR-03, BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra định dạng Ngày mua khi A2 trả orderDate dạng ISO | PC-02 | 1. Đối chiếu dòng "Ngày mua" với data.orderDate | H01: orderDate = 2025-08-20 | - "Ngày mua" = 20/08/2025 | Medium | Positive | EP |  |
| TC_HDCT_TQ_003 | BR-03 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Ngày đáo hạn khi HĐ có ngày đáo hạn khác Ngày mua | PC-02 | 1. Đối chiếu dòng "Ngày đáo hạn" với ngày đáo hạn của HĐ trên BO/Flex | H01: đáo hạn 20/08/2026 | - "Ngày đáo hạn" = 20/08/2026<br>- Khác "Ngày mua" (20/08/2025) | High | Negative | EG | SRS map data.orderDate cho cả 2 field – Q-01 |
| TC_HDCT_TQ_004 | BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra định dạng ngày khi ngày và tháng có 1 chữ số | PC-01 | 1. Bấm card H03<br>2. Quan sát Ngày mua, Ngày đáo hạn | H03: orderDate = 2026-03-05; đáo hạn 2026-09-05 | - Ngày mua = 05/03/2026<br>- Ngày đáo hạn = 05/09/2026 (đủ 2 chữ số ngày/tháng) | Low | Boundary | BVA |  |
| TC_HDCT_TQ_005 | BR-03, BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra định dạng Tổng tiền đầu tư khi giá trị có 10 chữ số | PC-02 | 1. Đối chiếu dòng "Tổng tiền đầu tư" với data.totalInvestmentAmount | H01: totalInvestmentAmount = 1021111000 | - "Tổng tiền đầu tư" = 1.021.111.000 VND (dấu chấm phân cách nghìn, không số thập phân) | High | Positive | EP |  |
| TC_HDCT_TQ_006 | BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra định dạng số tiền khi giá trị có 11 chữ số | PC-01 | 1. Bấm card H02<br>2. Quan sát "Tổng tiền đầu tư" | H02: totalInvestmentAmount = 12345678900 | - "Tổng tiền đầu tư" = 12.345.678.900 VND, hiển thị 1 dòng, không bị cắt | Medium | Boundary | BVA |  |
| TC_HDCT_TQ_007 | BR-03, BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Thời gian đầu tư khi HĐ kỳ hạn 12 tháng | PC-02 | 1. Đối chiếu dòng "Thời gian đầu tư" với data.termType | H01: kỳ hạn 12 tháng | - "Thời gian đầu tư" = 12 tháng<br>- Khớp khoảng Ngày mua → Ngày đáo hạn | Medium | Positive | EP | [GIẢ ĐỊNH] giá trị termType – Q-13 |
| TC_HDCT_TQ_008 | BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra định dạng Lợi suất khi giá trị có 1 chữ số thập phân | PC-02 | 1. Quan sát dòng "Lợi suất" | H01: rate = 9.5 | - "Lợi suất" = 9,50%/năm (dấu phẩy thập phân, đủ 2 chữ số) | Medium | Positive | EP |  |
| TC_HDCT_TQ_009 | BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra định dạng Lợi suất khi giá trị là số nguyên | PC-01 | 1. Bấm card H02<br>2. Quan sát "Lợi suất" | H02: rate = 8 | - "Lợi suất" = 8,00%/năm | Low | Boundary | BVA |  |
| TC_HDCT_TQ_010 | BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra làm tròn Lợi suất khi giá trị có 3 chữ số thập phân | PC-01 | 1. Bấm card H03<br>2. Quan sát "Lợi suất" | H03: rate = 9.555 | - "Lợi suất" = 9,56%/năm | Low | Boundary | BVA | [GIẢ ĐỊNH] làm tròn half-up – Q-15 |
| TC_HDCT_TQ_011 | BR-03 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Số lượng TP khi mở chi tiết H01 | PC-02 | 1. Đối chiếu dòng "Số lượng TP" với data.totalQuantity | H01: totalQuantity = 10 | - "Số lượng TP" = 10 | Medium | Positive | EP |  |
| TC_HDCT_TQ_012 | BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra định dạng Số lượng TP khi giá trị từ 1.000 trở lên | PC-01 | 1. Bấm card H02<br>2. Quan sát "Số lượng TP", "Số lượng khả dụng" | H02: SL = 1250, khả dụng = 1250 | - "Số lượng TP" = 1.250<br>- "Số lượng khả dụng" = 1.250 | Low | Boundary | BVA |  |
| TC_HDCT_TQ_013 | BR-03, BR-17 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Số lượng khả dụng khi khác Số lượng TP (đã chuyển nhượng tại quầy) | PC-01 | 1. Bấm card H05<br>2. Quan sát "Số lượng TP" và "Số lượng khả dụng" | H05: SL = 10, khả dụng = 7 | - "Số lượng TP" = 10<br>- "Số lượng khả dụng" = 7 (không hiển thị 10) | High | Negative | EG | SRS map totalQuantity cho cả 2 field – Q-02 |
| TC_HDCT_TQ_014 | BR-03, BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Phí giao dịch khi HĐ có phí 1.111.000 VND | PC-02 | 1. Quan sát dòng "Phí giao dịch" | H01: transactionFee = 1111000 | - "Phí giao dịch" = 1.111.000 VND | Medium | Positive | EP | [GIẢ ĐỊNH] Figma chưa có field – Q-20 |
| TC_HDCT_TQ_015 | BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Phí giao dịch khi phí bằng 0 | PC-01 | 1. Bấm card H08<br>2. Quan sát "Phí giao dịch" | H08: transactionFee = 0 | - "Phí giao dịch" = 0 VND (không để trống, không "--") | Low | Boundary | BVA | [GIẢ ĐỊNH] |
| TC_HDCT_TQ_016 | BR-03, BR-04 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Lãi đầu tư dự kiến khi mở chi tiết H01 | PC-02 | 1. Quan sát dòng "Lãi đầu tư dự kiến" | H01: interest = 103393999 | - "Lãi đầu tư dự kiến" = 103.393.999 VND | High | Positive | EP |  |
| TC_HDCT_TQ_017 | BR-05 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Tổng tiền nhận về khi tính theo công thức gốc + lãi dự kiến (BIZ-20) | PC-02 | 1. Quan sát dòng "Tổng tiền nhận về"<br>2. Tính Tổng tiền đầu tư + Lãi đầu tư dự kiến | H01: 1.021.111.000 + 103.393.999 | - "Tổng tiền nhận về" = 1.124.504.999 VND, chữ xanh lá, cỡ chữ lớn hơn các dòng khác | High | Positive | DT | [GIẢ ĐỊNH] gốc = Tổng tiền đầu tư – Q-04 |
| TC_HDCT_TQ_018 | BR-05 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Tổng tiền nhận về khi so với giá trị đã trừ thuế TNCN | PC-02 | 1. Quan sát "Tổng tiền nhận về"<br>2. So với giá trị đã trừ thuế 5% trên lãi | H01: sau thuế = 1.021.111.000 + 103.393.999 × 95% = 1.119.335.299 | - Hiển thị 1.124.504.999 VND<br>- Không hiển thị 1.119.335.299 VND | High | Negative | DT | BIZ-20 "chưa tính thuế" |
| TC_HDCT_TQ_019 | BR-05 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra Tổng tiền nhận về khi so sánh giữa tab Tổng quan và tab Dòng tiền | PC-02 | 1. Ghi lại "Tổng tiền nhận về" tab Tổng quan<br>2. Bấm tab "Dòng tiền dự kiến"<br>3. So sánh với ô "Tổng tiền nhận về" | KH01 – H01 | - 2 giá trị bằng nhau = 1.124.504.999 VND<br>- Tab Tổng quan không hiển thị 103.393.999 (giá trị của data.interest) | High | Negative | EG | Mapping 2 tab khác nhau – Q-03 |
| TC_HDCT_TQ_020 | BR-03 | Chi tiết HĐ – Tab Tổng quan | Kiểm tra hiển thị khi A2 trả null cho field bắt buộc | PC-01; mock A2 của H08: ngày đáo hạn = null | 1. Bấm card H08<br>2. Quan sát dòng "Ngày đáo hạn" | H08 (mock) | - "Ngày đáo hạn" hiển thị "--"<br>- App không crash, các field khác hiển thị đủ | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-14 |
| TC_HDCT_MB_001 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra mở màn Mẫu biểu liên quan khi bấm "Xem" | PC-02 | 1. Bấm "Xem" ở dòng "Mẫu biểu liên quan" | KH01 – H01 (3 loại mẫu biểu) | - Mở màn "Mẫu biểu liên quan", nút "X" góc trái<br>- 3 tab: "Hợp đồng mua" \| "Phiếu lệnh đặt mua" \| "Phụ lục"<br>- Tab "Hợp đồng mua" active, viewer hiển thị PDF hợp đồng của H01 | High | Positive | UC | [GIẢ ĐỊNH] tab mặc định – Q-19 |
| TC_HDCT_MB_002 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra hiển thị đúng PDF khi chuyển sang tab Phiếu lệnh đặt mua | PC-02; đã mở màn Mẫu biểu của H01 | 1. Bấm tab "Phiếu lệnh đặt mua" | KH01 – H01 | - Tab "Phiếu lệnh đặt mua" active<br>- Viewer hiển thị PDF Phiếu lệnh (viewUrl của phần tử businessType tương ứng trong documents[]) | Medium | Positive | EP |  |
| TC_HDCT_MB_003 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra cuộn ngang thanh tab khi có 3 tab trở lên trên màn hẹp | PC-02; đã mở màn Mẫu biểu H01; thiết bị 360dp | 1. Vuốt ngang thanh tab<br>2. Bấm tab "Phụ lục" | KH01 – H01 | - Thanh tab cuộn ngang, thấy đủ chữ "Phụ lục"<br>- Viewer hiển thị PDF Phụ lục | Low | UI | EG |  |
| TC_HDCT_MB_004 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra cuộn và phóng to PDF khi mẫu biểu có nhiều trang | PC-02; đã mở màn Mẫu biểu H01 | 1. Vuốt dọc đến trang cuối PDF<br>2. Pinch-zoom 2 ngón | KH01 – H01 – Hợp đồng mua (≥ 3 trang) | - Cuộn được đến trang cuối, thanh cuộn hiển thị vị trí<br>- Phóng to được, chữ không vỡ | Low | Positive | EG | [GIẢ ĐỊNH] viewer hỗ trợ zoom |
| TC_HDCT_MB_005 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra số tab Mẫu biểu khi documents[] chỉ có 2 loại | PC-01 | 1. Bấm card H02<br>2. Bấm "Xem" | H02: documents[] = Hợp đồng mua, Phiếu lệnh đặt mua | - Chỉ 2 tab "Hợp đồng mua" \| "Phiếu lệnh đặt mua"<br>- Không có tab "Phụ lục" | Medium | Positive | EP |  |
| TC_HDCT_MB_006 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra dòng Mẫu biểu liên quan khi HĐ chưa có hồ sơ (documents[] rỗng) | PC-01 | 1. Bấm card H04<br>2. Quan sát dòng "Mẫu biểu liên quan" | H04: documents[] = [] | - Không hiển thị link "Xem"<br>- Không mở được màn Mẫu biểu trống | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-19 |
| TC_HDCT_MB_007 | BR-06, BR-15 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra viewer khi viewUrl trả lỗi 404 | PC-02; mock viewUrl Hợp đồng mua của H01 trả 404 | 1. Bấm "Xem" | H01 (mock) | - Hiển thị popup lỗi chuẩn SHSmart "Có lỗi xảy ra. Vui lòng thử lại sau", CTA "Đóng"<br>- Không quay loading vô hạn, không hiển thị trang trắng | Medium | Negative | EG | [GIẢ ĐỊNH] dùng popup lỗi chuẩn |
| TC_HDCT_MB_008 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra quay lại Chi tiết HĐ khi bấm "X" ở màn Mẫu biểu | PC-02; đã mở màn Mẫu biểu H01 | 1. Bấm "X" | KH01 – H01 | - Đóng màn Mẫu biểu, quay lại bottom sheet tab "Tổng quan" của H01<br>- Dữ liệu tab Tổng quan giữ nguyên | Medium | Positive | UC |  |
| TC_HDCT_MB_009 | BR-06 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra tải PDF khi mất mạng | PC-02; tắt Wi-Fi và 4G | 1. Bấm "Xem"<br>2. Bật lại mạng<br>3. Bấm "Xem" lần nữa | KH01 – H01 | - Bước 1: thông báo mất kết nối chuẩn SHSmart, không treo màn<br>- Bước 3: PDF tải và hiển thị bình thường | Medium | Negative | EG |  |
| TC_HDCT_MB_010 | BR-06, BR-14 | Chi tiết HĐ – Mẫu biểu liên quan | Kiểm tra nội dung mẫu biểu khi mở từ HĐ H01 | PC-02 | 1. Bấm "Xem"<br>2. Đọc số hợp đồng / tên KH trên PDF Hợp đồng mua | KH01 – H01 | - PDF có số hợp đồng 20240610BGB1231245325345 và thông tin KH01<br>- Không phải hồ sơ của HĐ/KH khác | High | Negative | EG |  |
| TC_HDCT_DT_001 | BR-07, BR-20 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra gọi A2 và hiển thị dữ liệu khi chọn tab Dòng tiền dự kiến | PC-02; bật proxy | 1. Bấm tab "Dòng tiền dự kiến"<br>2. Xem request trên proxy | KH01 – H01 | - App gọi A2 GET /api/v1/bond/term/app/appendix/detail của H01<br>- Tab hiển thị phần tổng hợp 4 chỉ tiêu + bảng 4 dòng | High | Positive | UC |  |
| TC_HDCT_DT_002 | BR-07 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra Lợi tức dự kiến khi mở tab Dòng tiền của H01 | PC-03 | 1. Quan sát "Lợi tức dự kiến" | H01: interest = 103393999 | - "Lợi tức dự kiến" = 103.393.999 VND, chữ xanh lá | Medium | Positive | EP |  |
| TC_HDCT_DT_003 | BR-07 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra Lợi suất dự kiến khi so với Lợi suất tab Tổng quan | PC-03 | 1. Quan sát "Lợi suất dự kiến" | H01: rate = 9.5 | - "Lợi suất dự kiến" = 9,50%/năm (giống tab Tổng quan) | Medium | Positive | EP |  |
| TC_HDCT_DT_004 | BR-07 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra Tổng tiền đầu tư tab Dòng tiền khi so với tab Tổng quan | PC-03 | 1. Quan sát ô "Tổng tiền đầu tư" | KH01 – H01 | - "Tổng tiền đầu tư" = 1.021.111.000 VND | Medium | Positive | EP |  |
| TC_HDCT_DT_005 | BR-07, BR-05 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra Tổng tiền nhận về tab Dòng tiền khi tính theo gốc + lợi tức dự kiến | PC-03 | 1. Quan sát ô "Tổng tiền nhận về" | H01: 1.021.111.000 + 103.393.999 | - "Tổng tiền nhận về" = 1.124.504.999 VND = data.totalReceive | High | Positive | DT | [GIẢ ĐỊNH] – Q-04 |
| TC_HDCT_DT_006 | BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra số dòng của bảng dòng tiền khi cashFlows có 4 phần tử | PC-03 | 1. Đếm số dòng trong bảng<br>2. Đối chiếu với data.cashFlows[] của A2 | H01: 4 phần tử cashFlows | - Bảng có đúng 4 dòng<br>- Mỗi dòng: Ngày dd/MM/yyyy (paymentDate) – Lệnh – Số tiền | High | Positive | EP |  |
| TC_HDCT_DT_007 | BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra dòng tiền khi businessType là Mua trái phiếu | PC-03 | 1. Quan sát dòng đầu tiên của bảng | H01: 20/08/2025 BUY -1021111000 | - "20/08/2025" \| "Mua trái phiếu" \| "-1.021.111.000 VND" chữ đỏ | High | Positive | EP | [GIẢ ĐỊNH] enum BUY – Q-11 |
| TC_HDCT_DT_008 | BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra dòng tiền khi businessType là Nhận trái tức | PC-03 | 1. Quan sát dòng thứ 2 | H01: 20/02/2026 COUPON +48000000 | - "20/02/2026" \| "Nhận trái tức" \| "+48.000.000 VND" chữ xanh lá | Medium | Positive | EP | [GIẢ ĐỊNH] enum COUPON – Q-11 |
| TC_HDCT_DT_009 | BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra dòng tiền khi businessType là Bán trái phiếu | PC-03 | 1. Quan sát dòng cuối | H01: 20/08/2026 SELL +1021111000 | - "20/08/2026" \| "Bán trái phiếu" \| "+1.021.111.000 VND" chữ xanh lá | Medium | Positive | EP | [GIẢ ĐỊNH] enum SELL – Q-11 |
| TC_HDCT_DT_010 | BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra bảng khi cashFlows có businessType ngoài 3 giá trị | PC-01; mock A2 của H08 có 1 dòng businessType = "OTHER" | 1. Bấm card H08<br>2. Bấm tab "Dòng tiền dự kiến" | H08 (mock) | - App không crash<br>- Dòng lạ hiển thị cột Lệnh = "--"; các dòng khác hiển thị bình thường | Low | Negative | EG | [GIẢ ĐỊNH] |
| TC_HDCT_DT_011 | BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra thứ tự dòng trong bảng khi HĐ có nhiều ngày thanh toán | PC-03 | 1. Quan sát cột Ngày từ trên xuống | KH01 – H01 | - Thứ tự: 20/08/2025 → 20/02/2026 → 20/08/2026 (Nhận trái tức) → 20/08/2026 (Bán trái phiếu) | Low | Positive | EP | [GIẢ ĐỊNH] ngày tăng dần – Q-10 |
| TC_HDCT_DT_012 | BR-04, BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra dấu phân cách nghìn khi hiển thị số tiền trong bảng dòng tiền | PC-03 | 1. Quan sát cột Dự kiến | KH01 – H01 | - Số tiền dạng "+48.000.000 VND" (dấu chấm), không phải "+48,000,000 VND" | Low | UI | EP | Figma dùng dấu phẩy – Q-12 |
| TC_HDCT_DT_013 | BR-09 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra empty state khi HĐ chưa có dòng tiền | PC-01 | 1. Bấm card H04<br>2. Bấm tab "Dòng tiền dự kiến" | H04: cashFlows = [] | - Vùng bảng hiển thị "Chưa có dòng tiền dự kiến"<br>- Không hiển thị header bảng rỗng | Medium | Negative | EP |  |
| TC_HDCT_DT_014 | BR-09, BR-07 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra phần tổng hợp khi HĐ chưa có dòng tiền | PC-01 | 1. Bấm card H04<br>2. Bấm tab "Dòng tiền dự kiến"<br>3. Quan sát phần tổng hợp | H04 | - Vẫn hiển thị Lợi tức dự kiến 2.050.000 VND, Lợi suất 8,20%/năm, Tổng tiền đầu tư 50.000.000 VND, Tổng tiền nhận về 52.050.000 VND | Low | Negative | EG | [GIẢ ĐỊNH] – Q-24 |
| TC_HDCT_DT_015 | BR-08 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra cuộn bảng dòng tiền khi có 25 dòng | PC-01 | 1. Bấm card H09<br>2. Bấm tab "Dòng tiền dự kiến"<br>3. Cuộn đến cuối bảng | H09: 25 dòng | - Cuộn được, thấy đủ 25 dòng<br>- Dòng cuối không bị thanh home indicator che | Low | Boundary | EG |  |
| TC_HDCT_DT_016 | BR-07 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra số lần gọi A2 khi chọn lại tab Dòng tiền | PC-02; bật proxy | 1. Bấm tab "Dòng tiền dự kiến"<br>2. Bấm tab "Tổng quan"<br>3. Bấm lại tab "Dòng tiền dự kiến" | KH01 – H01 | - Proxy ghi nhận 2 lần gọi A2 tại bước 1 và 3<br>- Dữ liệu hiển thị giống nhau | Low | Positive | EG | [GIẢ ĐỊNH] – Q-21 |
| TC_HDCT_DT_017 | BR-07, BR-15 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra mở tab Dòng tiền khi A2 trả BND-999 | PC-02; mock A2 trả BND-999 cho lần gọi kế tiếp | 1. Bấm tab "Dòng tiền dự kiến" | H01 (mock) | - Popup lỗi chuẩn SHSmart "Có lỗi xảy ra. Vui lòng thử lại sau", CTA "Đóng"<br>- Không hiển thị số liệu cũ hoặc bảng rỗng như dữ liệu thật | Medium | Negative | EG |  |
| TC_HDCT_DT_018 | BR-07 | Chi tiết HĐ – Tab Dòng tiền dự kiến | Kiểm tra mở tab Dòng tiền khi mất mạng | PC-02; tắt Wi-Fi và 4G | 1. Bấm tab "Dòng tiền dự kiến" | KH01 – H01 | - Thông báo mất kết nối chuẩn SHSmart<br>- Không hiển thị empty state "Chưa có dòng tiền dự kiến" (tránh hiểu nhầm HĐ không có dòng tiền) | Medium | Negative | EG |  |
| TC_HDCT_XN_001 | BR-10 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra hiển thị tab Xác nhận khi HĐ còn 2 hồ sơ cần xác nhận | PC-01 | 1. Bấm card H01<br>2. Quan sát thanh tab | H01: SELL_CONTRACT + SELL_ORDER_SLIP chưa xác nhận | - Có 3 tab, tab thứ 3 là "Xác nhận" | High | Positive | DT |  |
| TC_HDCT_XN_002 | BR-10 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra ẩn tab Xác nhận khi HĐ không còn hồ sơ cần xác nhận | PC-01 | 1. Bấm card H02<br>2. Quan sát thanh tab | H02: không còn hồ sơ cần xác nhận | - Chỉ 2 tab "Tổng quan" \| "Dòng tiền dự kiến"<br>- Không có tab "Xác nhận" | High | Negative | DT |  |
| TC_HDCT_XN_003 | BR-10, BR-11 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra tab Xác nhận khi chỉ còn Phiếu lệnh đặt bán TP cần xác nhận | PC-01 | 1. Bấm card H03<br>2. Bấm tab "Xác nhận" | H03: chỉ SELL_ORDER_SLIP | - Có dòng "Phiếu lệnh đặt bán TP" – "Cần xác nhận"<br>- Không có dòng "Hợp đồng đặt bán TP" | High | Positive | DT |  |
| TC_HDCT_XN_004 | BR-10, BR-11 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra tab Xác nhận khi chỉ còn Hợp đồng đặt bán TP cần xác nhận | PC-01 | 1. Bấm card H07<br>2. Bấm tab "Xác nhận" | H07: chỉ SELL_CONTRACT | - Có dòng "Hợp đồng đặt bán TP" – "Cần xác nhận"<br>- Không có dòng "Phiếu lệnh đặt bán TP" | High | Positive | DT |  |
| TC_HDCT_XN_005 | BR-11 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra Mã TP và Số HĐ khi mở tab Xác nhận của H01 | PC-04 | 1. Quan sát dòng "Mã TP", "Số HĐ" | H01: symbol = TPQ12312 | - "Mã TP" = TPQ12312<br>- "Số HĐ" = 20240610BGB1231245325345 | Medium | Positive | EP |  |
| TC_HDCT_XN_006 | BR-11 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra nhãn trạng thái hồ sơ khi mới vào tab Xác nhận | PC-04 | 1. Quan sát nhãn bên phải 2 dòng hồ sơ | KH01 – H01 | - Cả 2 dòng hiển thị "Cần xác nhận" | Medium | Positive | ST |  |
| TC_HDCT_XN_007 | BR-11 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra phản hồi khi bấm vào dòng hồ sơ cần xác nhận | PC-04 | 1. Bấm vào dòng "Hợp đồng đặt bán TP" | KH01 – H01 | - Không mở màn nào, không đổi trạng thái dòng<br>- Vẫn ở tab Xác nhận | Low | Negative | EG | SRS: dòng chỉ hiển thị nhãn |
| TC_HDCT_XN_008 | BR-12 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra nút "Xem hợp đồng" bấm được khi chưa xem hồ sơ nào | PC-04 | 1. Bấm "Xem hợp đồng" | KH01 – H01 | - Nút phản hồi, mở màn Mẫu biểu liên quan | Medium | Positive | EP | SRS "luôn enable" vs Figma màu xám – Q-05 |
| TC_HDCT_XN_009 | BR-12 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra nội dung màn Mẫu biểu khi mở từ nút Xem hợp đồng | PC-04 | 1. Bấm "Xem hợp đồng"<br>2. Quan sát các tab của màn Mẫu biểu | KH01 – H01 | - Có tab "Hợp đồng đặt bán TP" và "Phiếu lệnh đặt bán TP"<br>- Có ô tick cam kết ở cuối nội dung | High | Positive | UC | [GIẢ ĐỊNH] – Q-05 |
| TC_HDCT_XN_010 | BR-12 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra ô tick cam kết khi chưa cuộn đến cuối hồ sơ | PC-04; đã bấm "Xem hợp đồng" | 1. Cuộn đến khoảng giữa PDF "Hợp đồng đặt bán TP"<br>2. Bấm vào ô tick cam kết | KH01 – H01 | - Ô tick disable, không tick được | High | Negative | ST | [GIẢ ĐỊNH] – Q-06 |
| TC_HDCT_XN_011 | BR-12 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra ô tick cam kết khi đã cuộn đến cuối hồ sơ | PC-04; đã bấm "Xem hợp đồng" | 1. Cuộn đến cuối trang cuối PDF "Hợp đồng đặt bán TP"<br>2. Bấm ô tick cam kết | KH01 – H01 | - Ô tick enable và chuyển sang trạng thái đã tick | Medium | Positive | ST | [GIẢ ĐỊNH] – Q-06 |
| TC_HDCT_XN_012 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra trạng thái hồ sơ khi đã cuộn hết nhưng không tick cam kết | PC-04; đã bấm "Xem hợp đồng" | 1. Cuộn đến cuối "Hợp đồng đặt bán TP"<br>2. Không tick, bấm "X" quay về tab Xác nhận | KH01 – H01 | - Dòng "Hợp đồng đặt bán TP" vẫn "Cần xác nhận"<br>- Không có nút "Xác nhận" enable | High | Negative | ST | [GIẢ ĐỊNH] – Q-06 |
| TC_HDCT_XN_013 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra trạng thái hồ sơ khi tick rồi bỏ tick cam kết | PC-04; đã bấm "Xem hợp đồng" | 1. Cuộn đến cuối "Hợp đồng đặt bán TP"<br>2. Tick cam kết<br>3. Bỏ tick<br>4. Bấm "X" quay về tab Xác nhận | KH01 – H01 | - Dòng "Hợp đồng đặt bán TP" vẫn "Cần xác nhận" | Medium | Negative | ST | [GIẢ ĐỊNH] |
| TC_HDCT_XN_014 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra nút Xác nhận khi mới đọc 1 trong 2 hồ sơ | PC-04 | 1. Bấm "Xem hợp đồng"<br>2. Cuộn đến cuối "Hợp đồng đặt bán TP", tick cam kết<br>3. Bấm "X" quay về tab Xác nhận | KH01 – H01 | - "Hợp đồng đặt bán TP" = "Đã đọc ✓" (xanh lá)<br>- "Phiếu lệnh đặt bán TP" = "Cần xác nhận"<br>- Nút đáy vẫn "Xem hợp đồng"; chưa có nút "Xác nhận" enable | High | Negative | ST | [GIẢ ĐỊNH] – Q-05, V02 |
| TC_HDCT_XN_015 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra nút Xác nhận enable khi đã đọc đủ 2 hồ sơ | PC-04 | 1. Bấm "Xem hợp đồng"<br>2. Đọc hết + tick cam kết "Hợp đồng đặt bán TP"<br>3. Chuyển tab "Phiếu lệnh đặt bán TP", đọc hết + tick cam kết<br>4. Bấm "X" quay về tab Xác nhận | KH01 – H01 | - Cả 2 dòng = "Đã đọc ✓"<br>- Nút đáy đổi thành "Xác nhận" màu cam, enable | High | Positive | ST | [GIẢ ĐỊNH] – Q-05 |
| TC_HDCT_XN_016 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra nút Xác nhận khi HĐ chỉ có 1 hồ sơ cần xác nhận | PC-01 | 1. Bấm card H03 → tab "Xác nhận"<br>2. Bấm "Xem hợp đồng"<br>3. Đọc hết + tick cam kết "Phiếu lệnh đặt bán TP"<br>4. Bấm "X" | H03 | - Dòng "Phiếu lệnh đặt bán TP" = "Đã đọc ✓"<br>- Nút "Xác nhận" màu cam, enable | Medium | Boundary | BVA | [GIẢ ĐỊNH] – Q-05 |
| TC_HDCT_XN_017 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra chuyển sang luồng Xác nhận lệnh khi bấm Xác nhận | PC-04; đã đọc đủ 2 hồ sơ (như XN_015) | 1. Bấm "Xác nhận" | KH01 – H01 | - Hiển thị popup "Mã xác thực OTP" với 4 ô nhập, đồng hồ đếm ngược, nút "Xác nhận"<br>- KH nhận OTP qua kênh đã đăng ký | High | Integration | UC | Luồng OTP ngoài phạm vi – chỉ kiểm điểm chuyển |
| TC_HDCT_XN_018 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra số lần gửi OTP khi bấm Xác nhận 2 lần liên tiếp nhanh | PC-04; đã đọc đủ 2 hồ sơ; bật proxy | 1. Bấm "Xác nhận" 2 lần trong < 0,5 giây | KH01 – H01 | - Chỉ 1 popup OTP<br>- Chỉ 1 request gửi OTP, KH nhận 1 mã | High | Negative | EG |  |
| TC_HDCT_XN_019 | BR-13 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra trạng thái Đã đọc sau khi đóng và mở lại chi tiết HĐ | PC-04 | 1. Đọc hết + tick "Hợp đồng đặt bán TP"<br>2. Bấm "<" đóng bottom sheet<br>3. Bấm lại card H01 → tab "Xác nhận" | KH01 – H01 | - Cả 2 dòng về "Cần xác nhận"; nút "Xem hợp đồng" | Medium | Negative | ST | [GIẢ ĐỊNH] không lưu – Q-07 |
| TC_HDCT_XN_020 | BR-10 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra ẩn tab Xác nhận sau khi xác nhận OTP thành công | PC-04; đã đọc đủ 2 hồ sơ | 1. Bấm "Xác nhận"<br>2. Nhập OTP đúng → màn "Xác nhận thành công"<br>3. Bấm "Về Danh mục nắm giữ"<br>4. Mở tab "Danh mục hợp đồng", bấm card H01 | KH01 – H01; OTP đúng | - Chi tiết H01 chỉ còn 2 tab, không có tab "Xác nhận" | High | Integration | ST | [GIẢ ĐỊNH] – Q-22 |
| TC_HDCT_XN_021 | BR-10 | Chi tiết HĐ – Tab Xác nhận | Kiểm tra tab Xác nhận vẫn còn sau khi xác nhận OTP thất bại | PC-04; đã đọc đủ 2 hồ sơ | 1. Bấm "Xác nhận"<br>2. Nhập OTP sai đến khi hiện "Xác nhận thất bại"<br>3. Quay lại Danh mục HĐ, bấm card H01 | KH01 – H01; OTP = 0000 | - Vẫn có tab "Xác nhận"<br>- 2 hồ sơ "Cần xác nhận" | Medium | Negative | ST | [GIẢ ĐỊNH] – Q-22 |
| TC_HDCT_KL_001 | BR-17 | Chi tiết HĐ – Số lượng khả dụng (BIZ-25) | Kiểm tra ghi GIẢM khả dụng vào HĐ đáo hạn xa nhất khi các HĐ khác ngày đáo hạn | BO thực hiện chuyển nhượng tại quầy cho KH (chức năng 7.6.1 Yêu cầu chuyển nhượng Offline) và Flex đã thanh toán bù trừ; KH đăng nhập App | 1. BO ghi giảm 2 TP TPQ30003 của KH05<br>2. KH05 mở Chi tiết H14, xem "Số lượng khả dụng"<br>3. Mở Chi tiết H15, xem "Số lượng khả dụng" | KH05: H14 (đáo hạn 10/03/2027) 10/10; H15 (10/09/2027) 10/10; giảm 2 | - H15 (xa hơn): khả dụng = 8<br>- H14: khả dụng = 10<br>- Số lượng TP cả 2 HĐ vẫn = 10 | High | Positive | DT |  |
| TC_HDCT_KL_002 | BR-17 | Chi tiết HĐ – Số lượng khả dụng (BIZ-25) | Kiểm tra ghi GIẢM vào HĐ có KL thấp nhất khi cùng ngày đáo hạn | BO thực hiện chuyển nhượng tại quầy cho KH (chức năng 7.6.1 Yêu cầu chuyển nhượng Offline) và Flex đã thanh toán bù trừ; KH đăng nhập App | 1. BO ghi giảm 2 TP TPQ30004 của KH06<br>2. KH06 mở Chi tiết H16 và H17 | KH06: H16 khả dụng 5, H17 khả dụng 8, cùng đáo hạn 10/12/2027; giảm 2 | - H16: khả dụng = 3<br>- H17: khả dụng = 8 | High | Positive | DT |  |
| TC_HDCT_KL_003 | BR-17 | Chi tiết HĐ – Số lượng khả dụng (BIZ-25) | Kiểm tra ghi TĂNG khả dụng vào HĐ đáo hạn gần nhất khi các HĐ khác ngày đáo hạn | BO thực hiện chuyển nhượng tại quầy cho KH (chức năng 7.6.1 Yêu cầu chuyển nhượng Offline) và Flex đã thanh toán bù trừ; KH đăng nhập App | 1. BO ghi tăng 1 TP TPQ30001 cho KH03<br>2. KH03 mở Chi tiết H10 và H11 | KH03: H10 (10/03/2027) khả dụng 8/10; H11 (10/09/2027) khả dụng 7/10; tăng 1 | - H10 (gần hơn): khả dụng = 9<br>- H11: khả dụng = 7 | High | Positive | DT |  |
| TC_HDCT_KL_004 | BR-17 | Chi tiết HĐ – Số lượng khả dụng (BIZ-25) | Kiểm tra ghi TĂNG vào HĐ thiếu KL nhiều nhất khi cùng ngày đáo hạn | BO thực hiện chuyển nhượng tại quầy cho KH (chức năng 7.6.1 Yêu cầu chuyển nhượng Offline) và Flex đã thanh toán bù trừ; KH đăng nhập App | 1. BO ghi tăng 1 TP TPQ30002 cho KH04<br>2. KH04 mở Chi tiết H12 và H13 | KH04: H12 thiếu 1 (9/10), H13 thiếu 4 (6/10), cùng 10/12/2027; tăng 1 | - H13: khả dụng = 7<br>- H12: khả dụng = 9 | High | Positive | DT |  |
| TC_HDCT_KL_005 | BR-17 | Chi tiết HĐ – Số lượng khả dụng (BIZ-25) | Kiểm tra phân bổ TĂNG khi KL tăng vượt phần thiếu của HĐ ưu tiên | BO thực hiện chuyển nhượng tại quầy cho KH (chức năng 7.6.1 Yêu cầu chuyển nhượng Offline) và Flex đã thanh toán bù trừ; KH đăng nhập App; dữ liệu KH03 về ban đầu | 1. BO ghi tăng 4 TP TPQ30001 cho KH03<br>2. KH03 mở Chi tiết H10 và H11 | KH03: H10 thiếu 2, H11 thiếu 3; tăng 4 | - H10: khả dụng = 10 (không vượt Số lượng TP 10)<br>- H11: khả dụng = 9 (nhận phần dư 2) | Medium | Boundary | BVA | [GIẢ ĐỊNH] phân bổ tràn – Q-16 |
| TC_HDCT_KL_006 | BR-17 | Chi tiết HĐ – Số lượng khả dụng (BIZ-25) | Kiểm tra phân bổ GIẢM khi KL giảm vượt khả dụng của HĐ ưu tiên | BO thực hiện chuyển nhượng tại quầy cho KH (chức năng 7.6.1 Yêu cầu chuyển nhượng Offline) và Flex đã thanh toán bù trừ; KH đăng nhập App; dữ liệu KH05 về ban đầu | 1. BO ghi giảm 12 TP TPQ30003 của KH05<br>2. KH05 mở Chi tiết H14 và H15 | KH05: H14 10/10, H15 10/10; giảm 12 | - H15: khả dụng = 0 (không âm)<br>- H14: khả dụng = 8 | Medium | Boundary | BVA | [GIẢ ĐỊNH] phân bổ tràn – Q-16 |
| TC_HDCT_KL_007 | BR-17 | Chi tiết HĐ – Số lượng khả dụng (BIZ-25) | Kiểm tra Số lượng TP không đổi khi khả dụng thay đổi do chuyển nhượng | BO thực hiện chuyển nhượng tại quầy cho KH (chức năng 7.6.1 Yêu cầu chuyển nhượng Offline) và Flex đã thanh toán bù trừ; KH đăng nhập App | 1. Sau KL_001, KH05 mở Chi tiết H15<br>2. Quan sát "Số lượng TP" và "Số lượng khả dụng" | KH05 – H15 | - "Số lượng TP" = 10<br>- "Số lượng khả dụng" = 8 | Medium | Negative | EG |  |
| TC_HDCT_NOTI_001 | BR-18 | Chi tiết HĐ – Nhắc T-5 (Bond_08) | Kiểm tra gửi noti Bond_08 tại T-5 khi HĐ chưa đủ số lượng TP | H05 (khả dụng 7/10) chỉnh ngày đáo hạn = ngày hệ thống + 5 ngày; job nhắc chạy | 1. Chạy job nhắc T-5 (hoặc chờ đến giờ chạy)<br>2. Kiểm tra thông báo trên App của KH01 | KH01 – H05 | - KH01 nhận noti Bond_08 nhắc hoàn thiện số lượng TP của HĐ 20250501BGB0000000000005 | Medium | Integration | BVA | [GIẢ ĐỊNH] điều kiện gửi + nội dung – Q-17 |
| TC_HDCT_NOTI_002 | BR-18 | Chi tiết HĐ – Nhắc T-5 (Bond_08) | Kiểm tra không gửi Bond_08 khi HĐ đã đủ số lượng TP | H01 (khả dụng 10/10) chỉnh đáo hạn = hôm nay + 5; job nhắc chạy | 1. Chạy job nhắc T-5<br>2. Kiểm tra thông báo của KH01 | KH01 – H01 | - Không có noti Bond_08 cho H01 | Low | Negative | DT | [GIẢ ĐỊNH] – Q-17 |
| TC_HDCT_NOTI_003 | BR-18 | Chi tiết HĐ – Nhắc T-5 (Bond_08) | Kiểm tra gửi Bond_08 khi còn 6 ngày đến đáo hạn (T-6) | H05 chỉnh đáo hạn = hôm nay + 6; job nhắc chạy | 1. Chạy job nhắc<br>2. Kiểm tra thông báo của KH01 | KH01 – H05 | - Không có noti Bond_08 | Low | Boundary | BVA | [GIẢ ĐỊNH] ngày lịch – Q-17 |
| TC_HDCT_NOTI_004 | BR-18 | Chi tiết HĐ – Nhắc T-5 (Bond_08) | Kiểm tra gửi lại Bond_08 khi còn 4 ngày đến đáo hạn (T-4) | Đã gửi Bond_08 cho H05 ở NOTI_001; chỉnh đáo hạn = hôm nay + 4 | 1. Chạy job nhắc<br>2. Kiểm tra thông báo của KH01 | KH01 – H05 | - Không có noti Bond_08 mới (chỉ 1 noti từ T-5) | Low | Boundary | BVA | [GIẢ ĐỊNH] – Q-17 |
| TC_HDCT_POP_001 | BR-16 | Chi tiết HĐ – Popup I02 | Kiểm tra nội dung popup Hồ sơ đã phát hành khi popup xuất hiện | Popup SBOND.CONTRACT.I02 đang hiển thị (trigger chưa xác định) | 1. Quan sát popup | KH01 | - Tiêu đề "Hồ sơ đã phát hành"<br>- Nội dung "Hệ thống đã phát hành hồ sơ cho Quý khách. Vui lòng liên hệ CSKH: … nếu cần hỗ trợ thêm."<br>- CTA "Đóng" | Low | UI | EG | Trigger/hotline/CTA "Liên hệ CSKH" – Q-08; Blocked nếu chưa có trả lời |
| TC_HDCT_POP_002 | BR-16 | Chi tiết HĐ – Popup I02 | Kiểm tra đóng popup Hồ sơ đã phát hành khi bấm Đóng | Như POP_001 | 1. Bấm "Đóng" | KH01 | - Popup đóng, quay lại màn trước đó; dữ liệu không đổi | Low | Positive | UC | Q-08 |
| TC_HDCT_ERR_001 | BR-15 | Chi tiết HĐ – Lỗi hệ thống | Kiểm tra mở chi tiết khi A2 trả BND-901 | PC-01; mock A2 trả BND-901 | 1. Bấm card H01 | H01 (mock) | - Popup lỗi chuẩn SHSmart "Hệ thống đang bận", CTA "Đóng"<br>- Không mở bottom sheet với dữ liệu rỗng | Medium | Negative | EP | [GIẢ ĐỊNH] không mở bottom sheet |
| TC_HDCT_ERR_002 | BR-15 | Chi tiết HĐ – Lỗi hệ thống | Kiểm tra mở chi tiết khi A2 trả BND-999 | PC-01; mock A2 trả BND-999 | 1. Bấm card H01 | H01 (mock) | - Popup lỗi chuẩn "Có lỗi xảy ra. Vui lòng thử lại sau", CTA "Đóng"<br>- Bấm "Đóng" → ở lại Danh mục HĐ | Medium | Negative | EP |  |
| TC_HDCT_ERR_003 | BR-15 | Chi tiết HĐ – Lỗi hệ thống | Kiểm tra mở chi tiết khi phiên đăng nhập hết hạn (AUTH_001) | PC-01; để App chờ quá thời gian phiên hoặc revoke token KH01 trên server | 1. Bấm card H01<br>2. Bấm CTA "Đăng nhập" | KH01 | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", CTA "Đăng nhập"<br>- Bước 2: chuyển màn đăng nhập SHSmart; không hiển thị dữ liệu H01 | High | Security | EP |  |
| TC_HDCT_ERR_004 | BR-01 | Chi tiết HĐ – Lỗi hệ thống | Kiểm tra mở chi tiết khi mất mạng | PC-01; tắt Wi-Fi và 4G | 1. Bấm card H01 | KH01 | - Thông báo mất kết nối chuẩn SHSmart<br>- Không mở bottom sheet trống, không crash | Medium | Negative | EG |  |
| TC_HDCT_ERR_005 | BR-01 | Chi tiết HĐ – Lỗi hệ thống | Kiểm tra trạng thái chờ khi A2 phản hồi chậm | PC-01; proxy throttle A2 delay 10 giây | 1. Bấm card H01<br>2. Quan sát trong lúc chờ | H01 | - Hiển thị loading/skeleton trong bottom sheet<br>- Không hiện giá trị "0 VND" hoặc rỗng trước khi có dữ liệu<br>- Sau 10 giây hiển thị đủ dữ liệu | Low | Negative | EG |  |
| TC_HDCT_ERR_006 | BR-06, BR-15 | Chi tiết HĐ – Lỗi hệ thống | Kiểm tra màn chi tiết khi A2 thành công nhưng O4 lỗi | PC-01; mock O4 trả BND-999, A2 bình thường | 1. Bấm card H01<br>2. Bấm "Xem" ở Mẫu biểu liên quan | H01 (mock) | - Bước 1: tab Tổng quan hiển thị đủ dữ liệu từ A2<br>- Bước 2: popup lỗi chuẩn "Có lỗi xảy ra. Vui lòng thử lại sau" | Medium | Negative | EG | [GIẢ ĐỊNH] – Q-23 |
| TC_HDCT_PQ_001 | BR-14 | Chi tiết HĐ – Phân quyền | Kiểm tra chặn xem HĐ của KH khác khi sửa contractNo trong request | PC-01; proxy bật breakpoint request A2 | 1. Bấm card H01<br>2. Tại breakpoint, sửa tham số thành Số HĐ của H20 (KH02)<br>3. Cho request đi tiếp | KH01; H20 = 20250901BGB0000000000020 | - A2 trả BND-403<br>- App hiển thị popup lỗi chuẩn [G-Code]<br>- Không hiển thị bất kỳ dữ liệu nào của H20 | High | Security | EG | V01 |
| TC_HDCT_PQ_002 | BR-14 | Chi tiết HĐ – Phân quyền | Kiểm tra không còn dữ liệu của KH trước khi đổi tài khoản trên cùng thiết bị | KH01 đã mở chi tiết H01, sau đó đăng xuất | 1. Đăng nhập KH02<br>2. Vào Danh mục HĐ, bấm card H20<br>3. Quan sát chi tiết | KH01 → KH02 | - Chỉ hiển thị dữ liệu H20 của KH02<br>- Không còn H01 hay dữ liệu cache nào của KH01 | High | Security | EG |  |
| TC_HDCT_PQ_003 | BR-14 | Chi tiết HĐ – Phân quyền | Kiểm tra mở chi tiết khi contractNo không tồn tại | PC-01; proxy breakpoint request A2 | 1. Bấm card H01<br>2. Sửa tham số thành 99999999XXX0000000000000<br>3. Cho request đi tiếp | KH01 | - Popup lỗi chuẩn [G-Code], không mở bottom sheet trống | Medium | Negative | EG | [GIẢ ĐỊNH] mã lỗi – Q-09 |
| TC_HDCT_EVT_001 | BR-19 | Chi tiết HĐ – Tracking event | Kiểm tra ghi event SBOND.CONTRACT.DETAIL khi mở chi tiết HĐ | PC-01; mở công cụ debug analytics | 1. Bấm card H01 | KH01 – H01 | - Ghi nhận 1 event SBOND.CONTRACT.DETAIL kèm Số HĐ H01 | Low | Integration | UC | [GIẢ ĐỊNH] tham số event |
| TC_HDCT_EVT_002 | BR-19 | Chi tiết HĐ – Tracking event | Kiểm tra ghi event SBOND.CONTRACT.DOCS.VIEW khi bấm Xem mẫu biểu | PC-02; mở debug analytics | 1. Bấm "Xem" | KH01 – H01 | - Ghi nhận 1 event SBOND.CONTRACT.DOCS.VIEW | Low | Integration | UC |  |
| TC_HDCT_EVT_003 | BR-19 | Chi tiết HĐ – Tracking event | Kiểm tra ghi event SBOND.CONTRACT.CASHFLOW khi mở tab Dòng tiền | PC-02; mở debug analytics | 1. Bấm tab "Dòng tiền dự kiến" | KH01 – H01 | - Ghi nhận 1 event SBOND.CONTRACT.CASHFLOW | Low | Integration | UC |  |
| TC_HDCT_EVT_004 | BR-19 | Chi tiết HĐ – Tracking event | Kiểm tra ghi event SBOND.CONTRACT.CONFIRM khi bấm Xác nhận | PC-04; đã đọc đủ 2 hồ sơ; mở debug analytics | 1. Bấm "Xác nhận" | KH01 – H01 | - Ghi nhận 1 event SBOND.CONTRACT.CONFIRM | Low | Integration | UC |  |

### 4.2 API (22 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_HDCT_API_001 | BR-20, BR-03 | Chi tiết HĐ – API A2 | Kiểm tra A2 trả đủ field khi gọi với HĐ của chính KH | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345<br>2. Kiểm tra response | KH01 – H01 | - HTTP 200<br>- data.orderNo = 20240610BGB1231245325345; data.symbol = TPQ12312<br>- Có đủ: orderDate, totalInvestmentAmount = 1021111000, termType, rate = 9.5, totalQuantity = 10, transactionFee = 1111000, interest = 103393999, totalReceive, cashFlows (4 phần tử), confirmDocuments (2 phần tử) | High | Positive | EP | [GIẢ ĐỊNH] tên param – Q-09 |
| TC_HDCT_API_002 | BR-05 | Chi tiết HĐ – API A2 | Kiểm tra giá trị totalReceive khi gọi A2 cho H01 | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345<br>2. Tính totalInvestmentAmount + interest | KH01 – H01 | - totalReceive = 1124504999 | High | Positive | DT | [GIẢ ĐỊNH] – Q-04 |
| TC_HDCT_API_003 | BR-03 | Chi tiết HĐ – API A2 | Kiểm tra field ngày đáo hạn khi gọi A2 cho H01 | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345 | KH01 – H01 | - Có field ngày đáo hạn = 2026-08-20<br>- orderDate = 2025-08-20 | High | Negative | EG | Q-01 |
| TC_HDCT_API_004 | BR-03, BR-17 | Chi tiết HĐ – API A2 | Kiểm tra A2 có field số lượng khả dụng riêng khi khác totalQuantity | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20250501BGB0000000000005 | KH01 – H05 | - totalQuantity = 10<br>- Field khả dụng = 7 | High | Negative | EG | Q-02 |
| TC_HDCT_API_005 | BR-08 | Chi tiết HĐ – API A2 | Kiểm tra dấu amount trong cashFlows khi gọi A2 cho H01 | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345<br>2. Duyệt data.cashFlows[] | KH01 – H01 | - BUY: amount = -1021111000<br>- COUPON: amount > 0 (48000000; 55393999)<br>- SELL: amount = 1021111000<br>- paymentDate đúng định dạng ngày ISO | Medium | Positive | EP | [GIẢ ĐỊNH] enum – Q-11 |
| TC_HDCT_API_006 | BR-09 | Chi tiết HĐ – API A2 | Kiểm tra A2 trả cashFlows rỗng khi HĐ chưa có dòng tiền | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20250401BGB0000000000004 | KH01 – H04 | - HTTP 200<br>- data.cashFlows = [] (mảng rỗng, không null, không thiếu key) | Medium | Negative | EP |  |
| TC_HDCT_API_007 | BR-10 | Chi tiết HĐ – API A2 | Kiểm tra confirmDocuments khi HĐ không còn hồ sơ cần xác nhận | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20250115BGB0000000000002 | KH01 – H02 | - HTTP 200<br>- confirmDocuments = [] (hoặc không có phần tử chờ xác nhận) | High | Negative | DT |  |
| TC_HDCT_API_008 | BR-11 | Chi tiết HĐ – API A2 | Kiểm tra confirmDocuments khi chỉ còn Phiếu lệnh đặt bán | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20250301BGB0000000000003 | KH01 – H03 | - confirmDocuments có đúng 1 phần tử type = SELL_ORDER_SLIP<br>- Không có SELL_CONTRACT | High | Positive | DT |  |
| TC_HDCT_API_009 | BR-06, BR-20 | Chi tiết HĐ – API O4 | Kiểm tra danh sách mẫu biểu khi gọi O4 cho H01 | PC-API | 1. Gọi GET /api/v1/bond/app/order/documents/filter?orderNo=20240610BGB1231245325345 | KH01 – H01 | - HTTP 200<br>- 3 phần tử, mỗi phần tử có businessType (Hợp đồng mua / Phiếu lệnh đặt mua / Phụ lục) và viewUrl không rỗng | High | Positive | EP | [GIẢ ĐỊNH] tên param – Q-09 |
| TC_HDCT_API_010 | BR-06 | Chi tiết HĐ – API O4 | Kiểm tra file trả về khi gọi viewUrl của H01 | PC-API; lấy viewUrl Hợp đồng mua từ API_009 | 1. Gọi GET <viewUrl> với token KH01 | KH01 – H01 | - HTTP 200<br>- Content-Type: application/pdf; file mở được, đúng HĐ H01 | Medium | Positive | EP |  |
| TC_HDCT_API_011 | BR-06 | Chi tiết HĐ – API O4 | Kiểm tra O4 khi HĐ chưa phát hành hồ sơ | PC-API | 1. Gọi GET /api/v1/bond/app/order/documents/filter?orderNo=20250401BGB0000000000004 | KH01 – H04 | - HTTP 200<br>- data = [] | Medium | Negative | EP |  |
| TC_HDCT_API_012 | BR-14 | Chi tiết HĐ – API bảo mật | Kiểm tra A2 khi KH01 truy vấn HĐ của KH02 | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20250901BGB0000000000020 bằng token KH01 | KH01 → H20 (của KH02) | - HTTP 403, error code BND-403<br>- Response không chứa dữ liệu H20 | High | Security | EG | V01 |
| TC_HDCT_API_013 | BR-14 | Chi tiết HĐ – API bảo mật | Kiểm tra O4 khi KH01 truy vấn mẫu biểu của KH02 | PC-API | 1. Gọi GET /api/v1/bond/app/order/documents/filter?orderNo=20250901BGB0000000000020 bằng token KH01 | KH01 → H20 | - HTTP 403, error code BND-403<br>- Không trả viewUrl | High | Security | EG |  |
| TC_HDCT_API_014 | BR-14 | Chi tiết HĐ – API bảo mật | Kiểm tra truy cập viewUrl khi dùng token KH khác hoặc không có token | PC-API; viewUrl Hợp đồng mua H01 lấy từ API_009 | 1. Gọi GET <viewUrl> bằng token KH02<br>2. Gọi GET <viewUrl> không có token | KH02; không token | - Cả 2 lần: bị từ chối (401/403), không tải được PDF | High | Security | EG | [GIẢ ĐỊNH] – Q-25 |
| TC_HDCT_API_015 | BR-15 | Chi tiết HĐ – API bảo mật | Kiểm tra A2 khi không gửi Authorization | Postman, UAT | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345 không có header Authorization | — | - HTTP 401, error code AUTH_001<br>- Không trả data | High | Security | EP |  |
| TC_HDCT_API_016 | BR-15 | Chi tiết HĐ – API bảo mật | Kiểm tra A2 khi token đã hết hạn | Postman; token KH01 đã hết hạn | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345 | Token hết hạn | - HTTP 401, error code AUTH_001 | High | Security | EP |  |
| TC_HDCT_API_017 | BR-15 | Chi tiết HĐ – API bảo mật | Kiểm tra O4 khi token đã hết hạn | Postman; token KH01 đã hết hạn | 1. Gọi GET /api/v1/bond/app/order/documents/filter?orderNo=20240610BGB1231245325345 | Token hết hạn | - HTTP 401, error code AUTH_001 | Medium | Security | EP |  |
| TC_HDCT_API_018 | BR-14 | Chi tiết HĐ – API bảo mật | Kiểm tra A2 khi orderNo không tồn tại | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=99999999XXX0000000000000 | — | - HTTP 4xx kèm error code (BND-403 hoặc mã not found)<br>- Không trả data | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-09 |
| TC_HDCT_API_019 | BR-20 | Chi tiết HĐ – API bảo mật | Kiểm tra A2 khi thiếu tham số orderNo | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail không có query param | — | - HTTP 400, có error code + message<br>- Không trả danh sách HĐ | Medium | Negative | EP | [GIẢ ĐỊNH] – Q-09 |
| TC_HDCT_API_020 | BR-14 | Chi tiết HĐ – API bảo mật | Kiểm tra A2 khi orderNo chứa chuỗi SQL injection | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=' OR 1=1 -- | orderNo = ' OR 1=1 -- | - HTTP 400/403<br>- Không trả dữ liệu HĐ nào, không lộ stack trace/SQL error | High | Security | EG |  |
| TC_HDCT_API_021 | BR-15 | Chi tiết HĐ – API bảo mật | Kiểm tra format lỗi khi BE gặp lỗi hệ thống | PC-API; nhờ Dev stub lỗi DB cho A2 | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345 | H01 (stub) | - HTTP 5xx, error code BND-999<br>- Message không lộ chi tiết kỹ thuật | Low | Negative | EG |  |
| TC_HDCT_API_022 | BR-02 | Chi tiết HĐ – API bảo mật | Kiểm tra A2 không làm thay đổi dữ liệu khi gọi lặp lại | PC-API | 1. Gọi GET /api/v1/bond/term/app/appendix/detail?orderNo=20240610BGB1231245325345<br>2. Gọi lại lần 2<br>3. So sánh response | KH01 – H01 | - 2 response giống hệt nhau<br>- confirmDocuments vẫn 2 phần tử chờ xác nhận | Low | Positive | EG |  |

## B5. Tự review + RTM
### RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ |
|---|---|---|---|---|
| BR-01 | Bấm card HĐ ở Danh mục HĐ → App gọi A2 / O4 (contractNo) → mở bottom s… | 9 | TC_HDCT_UI_001, TC_HDCT_NAV_001, TC_HDCT_NAV_002, TC_HDCT_NAV_006, TC_HDCT_NAV_007, TC_HDCT_NAV_008, TC_HDCT_NAV_009, TC_HDCT_ERR_004, TC_HDCT_ERR_005 | Đủ |
| BR-02 | Đóng / quay lại → về Danh mục HĐ; xem chi tiết không làm thay đổi dữ l… | 4 | TC_HDCT_NAV_003, TC_HDCT_NAV_004, TC_HDCT_NAV_005, TC_HDCT_API_022 | Đủ |
| BR-03 | Tab Tổng quan hiển thị 12 field theo mapping A2/O4: Số HĐ, Mẫu biểu li… | 15 | TC_HDCT_UI_002, TC_HDCT_UI_005, TC_HDCT_TQ_001, TC_HDCT_TQ_002, TC_HDCT_TQ_003, TC_HDCT_TQ_005, TC_HDCT_TQ_007, TC_HDCT_TQ_011, TC_HDCT_TQ_013, TC_HDCT_TQ_014, TC_HDCT_TQ_016, TC_HDCT_TQ_020, TC_HDCT_API_001, TC_HDCT_API_003, TC_HDCT_API_004 | Đủ |
| BR-04 | Định dạng: ngày dd/MM/yyyy; tiền #.### VND; lợi suất x,xx%/năm; số lượ… | 13 | TC_HDCT_TQ_002, TC_HDCT_TQ_004, TC_HDCT_TQ_005, TC_HDCT_TQ_006, TC_HDCT_TQ_007, TC_HDCT_TQ_008, TC_HDCT_TQ_009, TC_HDCT_TQ_010, TC_HDCT_TQ_012, TC_HDCT_TQ_014, TC_HDCT_TQ_015, TC_HDCT_TQ_016, TC_HDCT_DT_012 | Đủ |
| BR-05 | BIZ-20: Tổng tiền nhận về = gốc đầu tư + lãi dự kiến theo kỳ hạn, chưa… | 5 | TC_HDCT_TQ_017, TC_HDCT_TQ_018, TC_HDCT_TQ_019, TC_HDCT_DT_005, TC_HDCT_API_002 | Đủ |
| BR-06 | Mẫu biểu liên quan – "Xem" → màn "Mẫu biểu liên quan": tab theo loại (… | 14 | TC_HDCT_MB_001, TC_HDCT_MB_002, TC_HDCT_MB_003, TC_HDCT_MB_004, TC_HDCT_MB_005, TC_HDCT_MB_006, TC_HDCT_MB_007, TC_HDCT_MB_008, TC_HDCT_MB_009, TC_HDCT_MB_010, TC_HDCT_ERR_006, TC_HDCT_API_009, TC_HDCT_API_010, TC_HDCT_API_011 | Đủ |
| BR-07 | Chọn tab "Dòng tiền dự kiến" → gọi A2 → tổng hợp: Lợi tức dự kiến (xan… | 10 | TC_HDCT_UI_003, TC_HDCT_DT_001, TC_HDCT_DT_002, TC_HDCT_DT_003, TC_HDCT_DT_004, TC_HDCT_DT_005, TC_HDCT_DT_014, TC_HDCT_DT_016, TC_HDCT_DT_017, TC_HDCT_DT_018 | Đủ |
| BR-08 | Bảng dòng tiền: Ngày (dd/MM/yyyy) \| Lệnh (Mua trái phiếu / Nhận trái t… | 10 | TC_HDCT_UI_003, TC_HDCT_DT_006, TC_HDCT_DT_007, TC_HDCT_DT_008, TC_HDCT_DT_009, TC_HDCT_DT_010, TC_HDCT_DT_011, TC_HDCT_DT_012, TC_HDCT_DT_015, TC_HDCT_API_005 | Đủ |
| BR-09 | A2 không có dòng tiền → empty state trong tab: "Chưa có dòng tiền dự k… | 3 | TC_HDCT_DT_013, TC_HDCT_DT_014, TC_HDCT_API_006 | Đủ |
| BR-10 | BIZ-24: Tab "Xác nhận" chỉ hiển thị khi HĐ còn hồ sơ cần xác nhận; ngư… | 9 | TC_HDCT_UI_001, TC_HDCT_NAV_009, TC_HDCT_XN_001, TC_HDCT_XN_002, TC_HDCT_XN_003, TC_HDCT_XN_004, TC_HDCT_XN_020, TC_HDCT_XN_021, TC_HDCT_API_007 | Đủ |
| BR-11 | Tab Xác nhận: Mã TP, Số HĐ; dòng "Hợp đồng đặt bán TP" (SELL_CONTRACT)… | 7 | TC_HDCT_UI_004, TC_HDCT_XN_003, TC_HDCT_XN_004, TC_HDCT_XN_005, TC_HDCT_XN_006, TC_HDCT_XN_007, TC_HDCT_API_008 | Đủ |
| BR-12 | Nút "Xem hợp đồng" luôn enable → màn Mẫu biểu liên quan: scroll đến cu… | 5 | TC_HDCT_UI_004, TC_HDCT_XN_008, TC_HDCT_XN_009, TC_HDCT_XN_010, TC_HDCT_XN_011 | Đủ |
| BR-13 | Xem từng hồ sơ → đánh dấu "Đã đọc"; nút "Xác nhận" chỉ enable khi đã x… | 8 | TC_HDCT_XN_012, TC_HDCT_XN_013, TC_HDCT_XN_014, TC_HDCT_XN_015, TC_HDCT_XN_016, TC_HDCT_XN_017, TC_HDCT_XN_018, TC_HDCT_XN_019 | Đủ |
| BR-14 | V01: contractNo phải thuộc KH đăng nhập; vi phạm → popup lỗi chuẩn [G-… | 9 | TC_HDCT_MB_010, TC_HDCT_PQ_001, TC_HDCT_PQ_002, TC_HDCT_PQ_003, TC_HDCT_API_012, TC_HDCT_API_013, TC_HDCT_API_014, TC_HDCT_API_018, TC_HDCT_API_020 | Đủ |
| BR-15 | Lỗi hệ thống / token: BND-901, BND-999, AUTH_001 → popup lỗi chuẩn SHS… | 10 | TC_HDCT_MB_007, TC_HDCT_DT_017, TC_HDCT_ERR_001, TC_HDCT_ERR_002, TC_HDCT_ERR_003, TC_HDCT_ERR_006, TC_HDCT_API_015, TC_HDCT_API_016, TC_HDCT_API_017, TC_HDCT_API_021 | Đủ |
| BR-16 | Popup Info SBOND.CONTRACT.I02 "Hồ sơ đã phát hành", CTA "Đóng" | 2 | TC_HDCT_POP_001, TC_HDCT_POP_002 | Đủ |
| BR-17 | BIZ-25: KL khả dụng tăng/giảm khi KH chuyển nhượng TP tại quầy. TĂNG: … | 9 | TC_HDCT_TQ_013, TC_HDCT_KL_001, TC_HDCT_KL_002, TC_HDCT_KL_003, TC_HDCT_KL_004, TC_HDCT_KL_005, TC_HDCT_KL_006, TC_HDCT_KL_007, TC_HDCT_API_004 | Đủ |
| BR-18 | Nhắc T-5 hoàn thiện số lượng TP → gửi noti Bond_08 | 4 | TC_HDCT_NOTI_001, TC_HDCT_NOTI_002, TC_HDCT_NOTI_003, TC_HDCT_NOTI_004 | Đủ |
| BR-19 | Tracking event: SBOND.CONTRACT.DETAIL / DOCS.VIEW / CASHFLOW / CONFIRM | 4 | TC_HDCT_EVT_001, TC_HDCT_EVT_002, TC_HDCT_EVT_003, TC_HDCT_EVT_004 | Đủ |
| BR-20 | API: A2 GET /api/v1/bond/term/app/appendix/detail; O4 GET /api/v1/bond… | 5 | TC_HDCT_NAV_001, TC_HDCT_DT_001, TC_HDCT_API_001, TC_HDCT_API_009, TC_HDCT_API_019 | Đủ |

### Checklist
| Tiêu chí | Kết quả | Ghi chú |
|---|---|---|
| A. 100% BR có TC | Đạt | 20/20 BR |
| A. Luồng chính + phụ + ngoại lệ | Đạt | NAV/MB/DT/XN + ERR/PQ |
| A. Decision table mỗi rule có TC; ST có chuyển trạng thái không hợp lệ | Đạt | DT XN R1–R4, BIZ-25 R1–R6; XN_010/012/013/019 |
| A. Phân quyền | Đạt | 1 role KH → kiểm IDOR app + API, đổi tài khoản |
| B. ID đúng format, không trùng | Đạt | 131 ID duy nhất |
| B. 1 TC = 1 mục tiêu | Đạt | Đã tách định dạng/biên/mapping thành TC riêng |
| B. Title "Kiểm tra … khi …" | Đạt | — |
| B. Test data cụ thể | Đạt | Số HĐ, giá trị tiền, ngày cụ thể; bộ H01–H20 |
| B. Negative kiểm cả báo lỗi + không thực hiện | Đạt | ERR/PQ/API có "không mở/không trả data" |
| B. Message nguyên văn / [GIẢ ĐỊNH] | Đạt | Lấy từ Mục 6; text chưa có → [GIẢ ĐỊNH] + Q |
| C. Priority hợp lý | Đạt | Tiền, bảo mật, xác nhận hồ sơ = High |
| C. Thứ tự UI → Positive → … → Permission | Đạt | Sắp theo nhóm màn/tab, trong nhóm theo thứ tự quy tắc |

### Sửa/bổ sung trong vòng review
- Tách "Tổng tiền nhận về" thành 3 TC: công thức (TQ_017), không trừ thuế (TQ_018), nhất quán 2 tab (TQ_019).
- Bổ sung TC biên làm tròn lợi suất (TQ_010), ngày 1 chữ số (TQ_004), khả dụng = 0 (KL_006), T-6/T-4 (NOTI_003/004).
- Bổ sung kiểm viewUrl bị truy cập chéo (API_014) và lỗi 1 trong 2 API (ERR_006).

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 131 (App 109 · API 22) | — |
| High / Medium / Low | 46 (35%) / 52 (39%) / 33 (25%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 52 : 79 (39 : 61) | ≈ 30 : 70 |
| Theo Type | Positive 52 · Negative 42 · Boundary 12 · Security 10 · UI 8 · Integration 7 | — |
| Requirement coverage | 20/20 = 100% | 100% |
| Q&A mở | 25 (Blocker 0 · Major 10 · Minor 15) | Blocker = 0 |
| TC có [GIẢ ĐỊNH] | 51 | — |

**Lưu ý chất lượng:** High đang cao hơn mục tiêu vì màn này gồm số tiền, hồ sơ pháp lý và phân quyền. Nhóm XN (13 TC) dựa trên giả định Q-05/Q-06 – cần BA chốt trước khi chạy.
