# SBOND.DOCCONFIRM.APP.02 – Xác nhận hồ sơ hợp đồng (App) – Test design B1→B5

Nguồn: SRS SBOND.DOCCONFIRM.APP.02 – Xác nhận hồ sơ hợp đồng (Xem hợp đồng & OTP) (export Confluence 15/11/2026) + ảnh Figma trong SRS  
Người viết: Nguyễn Sỹ Hà · Ngày: 26/11/2026 · Lệnh: /FULL  
Phiên bản: v2 (rút gọn: mỗi thông tin 1 case, câu chữ dễ hiểu)  
Ký hiệu: [GIẢ ĐỊNH] = tạm giả định, chờ trả lời câu hỏi Q-xx; PC-xx = điều kiện có sẵn dùng chung; SOP-xx = thao tác dùng chung.

## B1. Phân tích yêu cầu
### 1. Tổng quan
- Mục đích: KH đọc hồ sơ của HĐ, tick "đã đọc", nhập OTP để xác nhận. Xác nhận xong thì hồ sơ chuyển "Đủ hồ sơ", hệ thống lưu lại và gửi thông báo cho KH.
- Người dùng: KH trên app SHSmart. Hệ thống liên quan: BE S-Bond, dịch vụ OTP, Portal SHBond (xem trạng thái hồ sơ).
- Vào màn từ: Chi tiết HĐ > tab "Xác nhận" > nút "Xem hợp đồng".
- Viết tắt: HĐ đặt bán = Hợp đồng đặt bán TP; Phiếu lệnh = Phiếu lệnh đặt bán TP; T = ngày đặt mua; E = ngày đáo hạn; giờ hệ thống = giờ của BE.

### 2. Feature
- Xem hồ sơ · Đọc hết và tick · Nhập OTP · Kết quả xác nhận · Thông báo và lưu vết · Thời hạn xác nhận · Hồ sơ có bản mới · Chống bấm trùng · Quay lại · Lỗi · Bảo mật · Tracking · API

### 3. Business Rule
| BR ID | Mô tả | Nguồn | Loại |
|---|---|---|---|
| BR-01 | Bấm "Xem hợp đồng" thì mở màn xem hồ sơ của đúng HĐ đó | Luồng bước 1, 3 | Logic |
| BR-02 | Chỉ hiện hồ sơ đang "Cần xác nhận" (bảng 4.1). HĐ có cả 2 hồ sơ thì hiện cả 2 và chỉ xác nhận 1 lần OTP | BIZ-28, BIZ-29, mục 4.1 | Logic |
| BR-03 | Hồ sơ đã điền sẵn thông tin KH và HĐ, là bản mới nhất | 3.1 #3 | Logic |
| BR-04 | Phải cuộn đến cuối mọi hồ sơ mới tick được ô "đã đọc". Phải tick mới bấm được "Xác nhận" | V02, V03, BIZ-09 | Validation |
| BR-05 | Bấm "Xác nhận" thì gửi OTP và mở màn OTP (tiêu đề "Mã xác thực OTP", ô nhập, đồng hồ đếm ngược, gửi lại, nút "Xác nhận") | Luồng bước 6, 3.2 | Logic |
| BR-06 | OTP sai, hết hạn hoặc nhập sai quá số lần thì báo lỗi theo dịch vụ OTP. Hồ sơ không đổi | V04 | Validation |
| BR-07 | Gửi lại OTP khi đồng hồ về 00:00. Gửi lại quá số lần thì báo lỗi | Luồng thay thế A2 | Logic |
| BR-08 | Xác nhận thành công: hồ sơ chuyển "Đủ hồ sơ", HĐ vẫn "Hiệu lực", hiện "Xác nhận thành công" rồi về tab "Xác nhận" | Luồng bước 9–10, mục 3.3, S01 | Logic |
| BR-09 | HĐ hết hồ sơ cần xác nhận thì rời tab "Xác nhận", bỏ cảnh báo ở Danh mục HĐ; số mẫu biểu liên quan tăng (3/4/5); yêu cầu thanh lý chuyển "KH đã xác nhận" | BIZ-30, BIZ-28, mục 8 | Logic |
| BR-10 | Gửi noti và email Bond_04 khi xác nhận thành công | Mục 6.1 | Tích hợp |
| BR-11 | Lưu lại lịch sử xác nhận (thời gian, nền tảng, số TKCK, họ tên, loại hồ sơ, bản hồ sơ). Không ai xóa được | BIZ-10 | Logic |
| BR-12 | Thời hạn xác nhận: HĐ đặt bán từ T+3 đến trước E-5; Phiếu lệnh từ E-5 đến trước 11h ngày E. Tính theo giờ hệ thống. Quá hạn thì báo popup E01 | BIZ-29, BIZ-11, V01, E01 | Logic |
| BR-13 | Hồ sơ có bản mới trong lúc KH đang xem thì không ghi nhận, báo popup I01, bấm "Xem lại" để đọc bản mới | BIZ-31, V05, I01 | Logic |
| BR-14 | Chống bấm trùng: nút bị khóa khi đang xử lý; gửi trùng thì chỉ ghi nhận 1 lần | BIZ-12, V06, A3 | Logic |
| BR-15 | Bấm quay lại ở màn xem hồ sơ hoặc màn OTP thì về Chi tiết HĐ, không nhớ tick, hồ sơ không đổi | Luồng thay thế A1 | Logic |
| BR-16 | Link hồ sơ hết hạn, lỗi hệ thống, hết phiên đăng nhập thì hiện popup lỗi chuẩn. Hồ sơ không đổi | A4, mục 6 #5 | Lỗi |
| BR-17 | Trường hợp đặc biệt khi KH đang xem: giao dịch bị hủy thì báo E01; Portal đã chuyển "Đủ hồ sơ" thì theo kết quả hệ thống trả về | Mục 9 | Logic |
| BR-18 | Chỉ xem và xác nhận được HĐ của chính mình; không xác nhận được nếu chưa qua OTP | V01, Q20 trong SRS | Bảo mật |
| BR-19 | Ghi nhận 4 sự kiện tracking: mở hồ sơ, tick đã đọc, gửi OTP, xác nhận OTP | Mục 7 | Tích hợp |
| BR-20 | API: O4 lấy hồ sơ (GET /api/v1/bond/app/order/documents/filter); A3 xác nhận (POST /api/v1/bond/term/app/appendix/confirm, body appendixId) | Mục 3 | Tích hợp |

### 4. Field list
| Màn | Trường | Kiểu | Bắt buộc | Quy tắc | Khi lỗi |
|---|---|---|---|---|---|
| Xem hồ sơ | Tiêu đề "Mẫu biểu liên quan" + nút quay lại | Header | Có | Quay lại về Chi tiết HĐ | — |
| Xem hồ sơ | Tên hồ sơ (tab) | Tab | Khi có 2 hồ sơ | "Hợp đồng đặt bán TP" / "Phiếu lệnh đặt bán TP" | — |
| Xem hồ sơ | Nội dung hồ sơ | PDF | Có | Đã điền thông tin KH + HĐ, bản mới nhất | Link hết hạn → popup lỗi |
| Xem hồ sơ | Ô tick "đã đọc" | Checkbox | Có | Mặc định khóa; mở khi cuộn hết hồ sơ | — |
| Xem hồ sơ | Nút "Xác nhận" | Nút | Có | Mặc định khóa; mở khi đã tick | — |
| OTP | Ô nhập OTP | Ô nhập | Có | Theo dịch vụ OTP | OTP-001/002/003 |
| OTP | Đồng hồ + gửi lại | Text + icon | Có | mm:ss; gửi lại khi về 00:00 | OTP-003 |
| OTP | Nút "Xác nhận" | Nút | Có | Mở khi nhập đủ OTP; khóa khi đang xử lý | E01 / I01 / lỗi chuẩn |
| Kết quả | Thông báo "Xác nhận thành công" | Màn/popup | Có | Có Số HĐ, Mã TP | — |

### 5. Q&A
| Q ID | Câu hỏi | Lý do | Mức | Giả định đề xuất | TC liên quan |
|---|---|---|---|---|---|
| Q-01 | Kết quả thành công: SRS ghi "Đóng → về tab Xác nhận"; Figma là màn kết quả có 2 nút "Về Danh mục nắm giữ" / "Về Danh sách chào bán". Làm theo bên nào? | SRS và Figma khác nhau | Major | Theo Figma | TC_XNHS_UI_003, TC_XNHS_XN_003 |
| Q-02 | HĐ có 2 hồ sơ: xác nhận 1 lần thì cập nhật cả 2? Gửi mấy noti Bond_04, lưu mấy dòng lịch sử? | API A3 chỉ nhận 1 appendixId | Major | 1 lần OTP cập nhật cả 2; 1 noti; 2 dòng lịch sử | TC_XNHS_XN_002 |
| Q-03 | API A3 chưa kiểm OTP (SRS Q20). Gọi A3 không qua OTP thì có bị chặn không? | Rủi ro bỏ qua OTP | Major | Bị chặn, không ghi nhận | TC_XNHS_BM_003 |
| Q-04 | OTP gồm mấy số, hết hạn sau bao lâu, sai tối đa mấy lần, gửi lại tối đa mấy lần? | SRS chỉ ghi "theo dịch vụ OTP" | Major | Theo màn OTP đặt mua (ORDER.APP.05) | TC_XNHS_OTP_002, TC_XNHS_OTP_005, TC_XNHS_OTP_007 |
| Q-05 | HĐ có 2 hồ sơ: phải cuộn hết cả 2 mới tick được? Chuyển qua lại giữa 2 hồ sơ có giữ trạng thái? | Quyết định khi nào mở ô tick | Major | Phải cuộn hết cả 2; có giữ | TC_XNHS_DOC_002 |
| Q-06 | Đúng 11:00:00 ngày E còn xác nhận được không? | Mốc hết hạn | Major | Không; chỉ trước 11:00:00 | TC_XNHS_TH_003, TC_XNHS_TH_005 |
| Q-07 | T+3, E-5 là ngày làm việc hay ngày lịch? | Mốc mở/gộp hồ sơ | Major | Ngày làm việc | TC_XNHS_TH_001, TC_XNHS_TH_002 |
| Q-08 | Mất mạng sau khi nhập OTP: app báo gì? Bấm lại có bị ghi nhận 2 lần không? | Rủi ro xác nhận trùng | Major | Báo lỗi chuẩn; bấm lại chỉ ghi nhận 1 lần | TC_XNHS_CL_002 |
| Q-09 | Chữ cạnh ô tick: SRS "Đã đọc và hiểu nội dung HĐ"; Figma "Bằng việc ấn nút 'Xác nhận', quý khách xác nhận đã đọc, hiểu rõ và đồng ý…". Dùng chữ nào? | SRS và Figma khác nhau | Minor | Theo Figma | TC_XNHS_DOC_003 |
| Q-10 | Figma có thông báo "Vui lòng đọc hết tài liệu/hợp đồng…" (bị cắt). Khi nào hiện, nội dung đầy đủ? | SRS không có | Minor | Hiện khi chạm ô tick lúc chưa đọc hết | TC_XNHS_DOC_001 |
| Q-11 | Popup lỗi chuẩn dùng chữ nào cho từng mã lỗi (BND-901, BND-999, AUTH_001, mất mạng, link hết hạn)? | SRS gộp chung | Minor | BND-901 "Hệ thống đang bận"; BND-999, mất mạng, link hết hạn "Có lỗi xảy ra. Vui lòng thử lại sau"; AUTH_001 "Phiên đăng nhập không hợp lệ hoặc đã hết hạn" | TC_XNHS_LOI_001, TC_XNHS_LOI_002, TC_XNHS_LOI_004 |
| Q-12 | Portal chuyển "Đủ hồ sơ" thủ công khi KH đang xem: app báo gì? | SRS ghi "theo API" | Minor | Hiện thông báo API trả về, không ghi lịch sử online | TC_XNHS_DB_002 |
| Q-13 | Xem lịch sử xác nhận ở đâu (Portal hay DB)? | Cần nơi kiểm | Minor | Portal, theo Số HĐ | TC_XNHS_XN_007 |
| Q-14 | Link hồ sơ hết hạn sau bao lâu? | Cần để test link hết hạn | Minor | Dev rút ngắn trên UAT hoặc giả lập | TC_XNHS_LOI_001 |
| Q-15 | 2 điện thoại cùng tài khoản cùng xác nhận 1 HĐ thì máy thứ 2 thấy gì? | SRS không nêu | Minor | Báo theo API, không ghi nhận lần 2 | TC_XNHS_CL_003 |

### 6. Rủi ro chính
| # | Rủi ro | Tác động | Mức |
|---|---|---|---|
| 1 | Xác nhận được mà không qua OTP hoặc không đọc hết hồ sơ | Rủi ro pháp lý | Cao |
| 2 | Xác nhận bản hồ sơ cũ hoặc xác nhận sau khi hết hạn | KH đồng ý giá cũ / HĐ đã hết hiệu lực | Cao |
| 3 | HĐ có 2 hồ sơ nhưng chỉ cập nhật 1 | HĐ vẫn treo cảnh báo, KH lỡ hạn | Cao |
| 4 | Bấm trùng / mất mạng làm ghi nhận 2 lần | Lịch sử, thông báo bị trùng | Cao |
| 5 | Xem hoặc xác nhận được HĐ của người khác | Lộ thông tin KH | Cao |

## B2. Chiến lược test
- **In-scope:** Màn xem hồ sơ, đọc và tick, OTP, kết quả xác nhận, thông báo Bond_04, lịch sử xác nhận, thời hạn, hồ sơ có bản mới, chống bấm trùng, quay lại, lỗi, bảo mật, tracking, API O4/A3.
- **Out-of-scope:** Bản Web; chi tiết dịch vụ OTP dùng chung; từng ô dữ liệu trong file mẫu PDF; nội dung email; màn Danh sách HĐ cần xác nhận (chỉ kiểm kết quả sau xác nhận); hiệu năng.
- **Loại test:** Chức năng, giao diện, API, tích hợp (app – BE – OTP – Portal – thông báo), bảo mật cơ bản, đoán lỗi (bấm nhiều lần, mất mạng, đổi giờ điện thoại).

| BR | Kỹ thuật chính | Kỹ thuật phụ | Priority | Số TC (App / API) |
|---|---|---|---|---|
| BR-01 | UC | EG | High | 2 / 0 |
| BR-02 | DT | — | High | 7 / 1 |
| BR-03 | EP | — | High | 1 / 0 |
| BR-04 | BVA | ST | High | 4 / 0 |
| BR-05 | UC | — | High | 3 / 0 |
| BR-06 | EP | BVA | High | 3 / 0 |
| BR-07 | BVA | — | Medium | 2 / 0 |
| BR-08 | UC | — | High | 4 / 0 |
| BR-09 | ST | — | High | 2 / 0 |
| BR-10 | UC | — | Medium | 1 / 0 |
| BR-11 | EP | — | High | 1 / 0 |
| BR-12 | BVA | EG | High | 6 / 1 |
| BR-13 | ST | — | High | 2 / 1 |
| BR-14 | EG | — | High | 3 / 1 |
| BR-15 | UC | — | Medium | 2 / 0 |
| BR-16 | EG | — | Medium | 4 / 0 |
| BR-17 | ST | — | Medium | 2 / 0 |
| BR-18 | EG | — | High | 4 / 1 |
| BR-19 | UC | — | Low | 1 / 0 |
| BR-20 | EP | — | High | 0 / 5 |

- **Môi trường:** UAT; 1 máy iOS + 1 máy Android; Proxyman/Charles để xem và sửa request; Postman; tài khoản BO trên Portal (xem trạng thái hồ sơ, đổi giờ hệ thống, hủy giao dịch); điện thoại + email nhận OTP/thông báo của tài khoản test.
- **Exit criteria:** Chạy hết TC High; không còn bug nghiêm trọng mở; các câu hỏi Major đã có trả lời; mọi BR có TC Pass.

### Dữ liệu cần có trên môi trường
| Mã | Điều kiện dữ liệu cần có trên UAT | Dùng cho |
|---|---|---|
| TK1 | Tài khoản chính, đăng nhập được app UAT, nhận được OTP và email | Hầu hết TC |
| TK2 | Tài khoản khác, có HĐ-E | Bảo mật |
| HĐ-A | Của TK1, chỉ HĐ đặt bán cần xác nhận. VD Mã TP TPQ12312, Số HĐ 20240610BGB1231245325345 | Luồng 1 hồ sơ |
| HĐ-B | Của TK1, cả HĐ đặt bán và Phiếu lệnh cần xác nhận. VD Mã TP BHB291193, Số HĐ 20240610BHBV12501.0000000 | Luồng 2 hồ sơ |
| HĐ-C | Của TK1, HĐ đặt bán đã xác nhận, chỉ Phiếu lệnh cần xác nhận | Luồng Phiếu lệnh |
| HĐ-D | Của TK1, không có hồ sơ cần xác nhận (3 HĐ: chưa tới T+3; HĐ đặt bán đã xác nhận, chưa tới E-5; cả 2 đã xác nhận) | Bảng 4.1 #1, #3, #6 |
| HĐ-E | Của TK2, có hồ sơ cần xác nhận | Bảo mật |
| HĐ mới | HĐ mới đặt mua của TK1, BO đổi được giờ hệ thống tới T+3 / E-5 / ngày E | Thời hạn |

### Precondition / thao tác dùng chung
| Mã | Nội dung |
|---|---|
| PC-01 | TK1 đã đăng nhập, đang ở Chi tiết HĐ-A > tab "Xác nhận" |
| PC-02 | PC-01 + đã bấm "Xem hợp đồng", đang ở màn xem hồ sơ |
| PC-03 | PC-02 + đã cuộn hết và tick, bấm "Xác nhận", đang ở màn OTP |
| SOP-01 | Xác nhận đủ bước: "Xem hợp đồng" → cuộn hết → tick → "Xác nhận" → nhập OTP đúng → "Xác nhận" |
| SOP-02 | Kiểm trên Portal theo Số HĐ: trạng thái từng hồ sơ và lịch sử xác nhận |

## B3. Test condition
### EP / BVA
| Đối tượng | Lớp hợp lệ | Lớp không hợp lệ | Biên |
|---|---|---|---|
| Cuộn hồ sơ | Cuộn đến cuối | Chưa cuộn / cuộn giữa chừng | Còn 1 dòng / chạm cuối |
| OTP | Đúng, còn hạn | Sai, hết hạn | Sai tối đa-1 lần rồi đúng / sai đủ số lần tối đa |
| Thời hạn Phiếu lệnh | Trước 11h ngày E | Từ 11h ngày E | 10:59 / 11:00 |
| Thời hạn HĐ đặt bán | T+3 đến trước E-5 | Trước T+3 | T+3 / E-5 (chuyển sang gộp) |

### Decision table – hồ sơ hiển thị để xác nhận (BIZ-28, bảng 4.1)
| Trường hợp (bảng 4.1) | #1 | #2 | #3 | #4 | #5 | #6 |
|---|---|---|---|---|---|---|
| HĐ đặt bán | Chưa cần | Cần | Đã xác nhận | Cần | Đã xác nhận | Đã xác nhận |
| Phiếu lệnh | Chưa cần | Chưa cần | Chưa cần | Cần | Cần | Đã xác nhận |
| Hiện hồ sơ | Không (không có tab) | HĐ đặt bán | Không | Cả 2 | Phiếu lệnh | Không |
| TC | HS_004 | HS_001 | HS_004 | HS_002 | HS_003 | HS_004 |

### State transition – hồ sơ (Mục 8, BIZ-30, BIZ-31, Mục 9)
| Hồ sơ \ Sự kiện | Xác nhận OTP xong | Có bản mới | Quá 11h ngày E | Hủy giao dịch |
|---|---|---|---|---|
| Thiếu hồ sơ | → Đủ hồ sơ (XN_001) | → Thiếu hồ sơ bản mới (VS_001) | → HĐ hết hiệu lực (TH_005) | → HĐ hết hiệu lực (DB_001) |
| Đủ hồ sơ | ✖ không ghi nhận lần 2 (CL_003) | — | — | — |
→ Mỗi ô có TC ghi trong ngoặc

### Use case
- Luồng chính: Xem hợp đồng → cuộn hết → tick → Xác nhận → OTP → thành công.
- Luồng phụ: 2 hồ sơ; chỉ Phiếu lệnh; gửi lại OTP; quay lại; Xem lại bản mới.
- Luồng lỗi: quá hạn; OTP sai; bản mới; hủy giao dịch; lỗi hệ thống; mất mạng; HĐ người khác.

### Error guessing áp dụng
- Bấm nhiều lần · mất mạng · tắt app giữa chừng · đổi giờ điện thoại · 2 máy cùng tài khoản · đổi tài khoản.

## B4. Test case
### 4.1 App (49 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_XNHS_UI_001 | BR-01, BR-04 | Xác nhận hồ sơ – Giao diện | Kiểm tra giao diện màn xem hồ sơ khi mới mở | PC-02 | 1. Quan sát màn hình | HĐ-A | - Tiêu đề "Mẫu biểu liên quan" + nút quay lại<br>- Hiện nội dung hồ sơ<br>- Ô tick và nút "Xác nhận" bị khóa (màu xám) | Low | UI | EG | Đối chiếu Figma |
| TC_XNHS_UI_002 | BR-05 | Xác nhận hồ sơ – Giao diện | Kiểm tra giao diện màn OTP khi mới mở | PC-03 | 1. Quan sát màn hình | HĐ-A | - Tiêu đề "Mã xác thực OTP", ô nhập OTP trống<br>- Đồng hồ đếm ngược + icon gửi lại<br>- Nút "Xác nhận" bị khóa | Low | UI | EG | Đối chiếu Figma |
| TC_XNHS_UI_003 | BR-08 | Xác nhận hồ sơ – Giao diện | Kiểm tra giao diện màn kết quả khi xác nhận thành công | PC-03 | 1. Nhập OTP đúng, bấm "Xác nhận" | HĐ-A | - Icon tích xanh + "Xác nhận thành công"<br>- Số hợp đồng 20240610BGB1231245325345, Mã trái phiếu TPQ12312<br>- 2 nút "Về Danh mục nắm giữ", "Về Danh sách chào bán" | Low | UI | EG | [GIẢ ĐỊNH] theo Figma – Q-01 |
| TC_XNHS_HS_001 | BR-01, BR-02 | Xác nhận hồ sơ – Hiển thị hồ sơ | Kiểm tra hồ sơ hiển thị khi HĐ chỉ có HĐ đặt bán cần xác nhận | PC-01 | 1. Bấm "Xem hợp đồng" | HĐ-A | - Mở màn xem hồ sơ của đúng HĐ-A<br>- Chỉ hiện "Hợp đồng đặt bán TP" | High | Positive | DT |  |
| TC_XNHS_HS_002 | BR-02 | Xác nhận hồ sơ – Hiển thị hồ sơ | Kiểm tra hồ sơ hiển thị khi HĐ có cả 2 hồ sơ cần xác nhận | TK1 ở Chi tiết HĐ-B > tab "Xác nhận" | 1. Bấm "Xem hợp đồng"<br>2. Chuyển qua lại giữa 2 hồ sơ | HĐ-B | - Hiện cả "Hợp đồng đặt bán TP" và "Phiếu lệnh đặt bán TP", chuyển qua lại được<br>- Chỉ có 1 ô tick và 1 nút "Xác nhận" chung | High | Positive | DT |  |
| TC_XNHS_HS_003 | BR-02 | Xác nhận hồ sơ – Hiển thị hồ sơ | Kiểm tra hồ sơ hiển thị khi HĐ chỉ có Phiếu lệnh cần xác nhận | TK1 ở Chi tiết HĐ-C > tab "Xác nhận" | 1. Bấm "Xem hợp đồng" | HĐ-C | - Chỉ hiện "Phiếu lệnh đặt bán TP"; không hiện HĐ đặt bán đã xác nhận | Medium | Positive | DT |  |
| TC_XNHS_HS_004 | BR-02 | Xác nhận hồ sơ – Hiển thị hồ sơ | Kiểm tra Chi tiết HĐ khi HĐ không có hồ sơ cần xác nhận | TK1 đã đăng nhập | 1. Lần lượt mở Chi tiết 3 HĐ-D | 3 HĐ-D | - Cả 3 HĐ không có tab "Xác nhận", không có nút "Xem hợp đồng" | Medium | Negative | DT |  |
| TC_XNHS_HS_005 | BR-03 | Xác nhận hồ sơ – Hiển thị hồ sơ | Kiểm tra nội dung hồ sơ khi mở xem | PC-02 | 1. Đọc phần thông tin KH và HĐ trong hồ sơ | HĐ-A; TK1 | - Họ tên, CCCD, số TKCK đúng của TK1<br>- Số HĐ, Mã TP đúng HĐ-A<br>- Là bản mới nhất (khớp bản trên Portal) | High | Positive | EP |  |
| TC_XNHS_DOC_001 | BR-04 | Xác nhận hồ sơ – Đọc và tick | Kiểm tra ô tick khi cuộn hồ sơ | PC-02 | 1. Chạm ô tick khi chưa cuộn<br>2. Cuộn đến gần cuối, chạm ô tick<br>3. Cuộn đến dòng cuối, chạm ô tick | HĐ-A | - Bước 1, 2: không tick được<br>- Bước 3: tick được | High | Boundary | BVA | Chạm ô tick lúc chưa đọc hết có thể hiện thông báo – Q-10 |
| TC_XNHS_DOC_002 | BR-04, BR-02 | Xác nhận hồ sơ – Đọc và tick | Kiểm tra ô tick khi HĐ có 2 hồ sơ | TK1 ở màn xem hồ sơ HĐ-B | 1. Cuộn hết hồ sơ 1, chạm ô tick<br>2. Cuộn hết hồ sơ 2, chạm ô tick | HĐ-B | - Bước 1: không tick được<br>- Bước 2: tick được | High | Boundary | BVA | [GIẢ ĐỊNH] – Q-05 |
| TC_XNHS_DOC_003 | BR-04 | Xác nhận hồ sơ – Đọc và tick | Kiểm tra nút "Xác nhận" khi tick và bỏ tick | PC-02 + đã cuộn hết | 1. Tick<br>2. Bỏ tick | HĐ-A | - Bước 1: nút "Xác nhận" mở (màu cam)<br>- Bước 2: nút bị khóa lại | Medium | Positive | ST | Chữ cạnh ô tick – Q-09 |
| TC_XNHS_OTP_001 | BR-05 | Xác nhận hồ sơ – OTP | Kiểm tra gửi OTP khi bấm "Xác nhận" | PC-02 + đã cuộn hết và tick | 1. Bấm "Xác nhận" | HĐ-A | - Mở màn OTP, đồng hồ bắt đầu đếm<br>- TK1 nhận được OTP | High | Positive | UC |  |
| TC_XNHS_OTP_002 | BR-05 | Xác nhận hồ sơ – OTP | Kiểm tra nút "Xác nhận" ở màn OTP khi nhập OTP | PC-03 | 1. Nhập thiếu 1 số<br>2. Nhập đủ số | OTP đúng | - Bước 1: nút bị khóa<br>- Bước 2: nút mở | Medium | Boundary | BVA | Số chữ số OTP – Q-04 |
| TC_XNHS_OTP_003 | BR-06 | Xác nhận hồ sơ – OTP | Kiểm tra xác nhận khi nhập OTP sai | PC-03 | 1. Nhập OTP sai, bấm "Xác nhận" | OTP đúng đổi số cuối (VD 1234 → 1235) | - Báo lỗi OTP-001, vẫn ở màn OTP<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | High | Negative | EP |  |
| TC_XNHS_OTP_004 | BR-06 | Xác nhận hồ sơ – OTP | Kiểm tra xác nhận khi OTP đã hết hạn | PC-03 | 1. Chờ đồng hồ về 00:00<br>2. Nhập OTP đã nhận, bấm "Xác nhận" | OTP đã nhận | - Báo lỗi OTP-002<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | Medium | Negative | EP |  |
| TC_XNHS_OTP_005 | BR-06 | Xác nhận hồ sơ – OTP | Kiểm tra xác nhận khi nhập sai OTP quá số lần | PC-03 | 1. Nhập OTP sai đến hết số lần cho phép | OTP sai 1235 | - Lần cuối báo lỗi OTP-003<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | Medium | Boundary | BVA | Số lần tối đa – Q-04 |
| TC_XNHS_OTP_006 | BR-07 | Xác nhận hồ sơ – OTP | Kiểm tra gửi lại OTP | PC-03 | 1. Chạm gửi lại khi đồng hồ chưa về 00:00<br>2. Chờ 00:00, chạm gửi lại<br>3. Nhập OTP mới, bấm "Xác nhận" | — | - Bước 1: không gửi OTP mới<br>- Bước 2: nhận OTP mới, đồng hồ chạy lại từ đầu<br>- Bước 3: xác nhận thành công | Medium | Positive | BVA |  |
| TC_XNHS_OTP_007 | BR-07 | Xác nhận hồ sơ – OTP | Kiểm tra gửi lại OTP khi quá số lần | PC-03 | 1. Gửi lại OTP liên tục (mỗi lần chờ 00:00) đến quá số lần | — | - Báo lỗi OTP-003, không gửi OTP mới<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | Medium | Boundary | BVA | Q-04 |
| TC_XNHS_XN_001 | BR-08 | Xác nhận hồ sơ – Kết quả | Kiểm tra xác nhận thành công khi HĐ có 1 hồ sơ | PC-01 | 1. Làm SOP-01<br>2. Làm SOP-02 | HĐ-A; OTP đúng | - App báo "Xác nhận thành công"<br>- Portal: HĐ đặt bán "Đủ hồ sơ"; HĐ-A vẫn "Hiệu lực" | High | Positive | UC |  |
| TC_XNHS_XN_002 | BR-02, BR-08 | Xác nhận hồ sơ – Kết quả | Kiểm tra xác nhận thành công khi HĐ có 2 hồ sơ | TK1 ở Chi tiết HĐ-B > tab "Xác nhận" | 1. Làm SOP-01 (cuộn hết cả 2 hồ sơ, nhập OTP 1 lần)<br>2. Làm SOP-02 | HĐ-B; OTP đúng | - Chỉ nhập OTP 1 lần, app báo thành công<br>- Portal: cả 2 hồ sơ "Đủ hồ sơ" | High | Positive | DT | [GIẢ ĐỊNH] – Q-02 |
| TC_XNHS_XN_003 | BR-08 | Xác nhận hồ sơ – Kết quả | Kiểm tra nội dung thông báo và điều hướng khi xác nhận thành công | PC-03 | 1. Nhập OTP đúng, bấm "Xác nhận"<br>2. Đóng thông báo / bấm nút trên màn kết quả | HĐ-A | - Nội dung: "Hợp đồng giao dịch trái phiếu TPQ12312 (Số HĐ: 20240610BGB1231245325345) đã được xác nhận thành công."<br>- Về tab "Xác nhận" (hoặc màn theo nút đã bấm), danh sách tải lại | Medium | Positive | UC | [GIẢ ĐỊNH] – Q-01 |
| TC_XNHS_XN_004 | BR-09 | Xác nhận hồ sơ – Kết quả | Kiểm tra HĐ sau khi xác nhận hết hồ sơ | Đã làm TC_XNHS_XN_001 | 1. Mở tab "Xác nhận"<br>2. Mở Danh mục HĐ<br>3. Mở Chi tiết HĐ-A | HĐ-A | - Tab "Xác nhận": không còn HĐ-A, số đếm giảm 1<br>- Danh mục HĐ: HĐ-A không còn cảnh báo "Hợp đồng có hồ sơ cần xác nhận"<br>- Chi tiết HĐ-A: không còn tab "Xác nhận"; số mẫu biểu liên quan từ 3 lên 4 | Medium | Integration | ST |  |
| TC_XNHS_XN_005 | BR-09 | Xác nhận hồ sơ – Kết quả | Kiểm tra yêu cầu thanh lý online khi KH xác nhận xong | TK1 có yêu cầu thanh lý online với hồ sơ "Chờ KH xác nhận" (HĐ-C) | 1. Xác nhận Phiếu lệnh HĐ-C (SOP-01)<br>2. Mở yêu cầu thanh lý trên Portal | HĐ-C | - Trạng thái hồ sơ: "Chờ KH xác nhận" → "KH đã xác nhận" | Medium | Integration | ST |  |
| TC_XNHS_XN_006 | BR-10 | Xác nhận hồ sơ – Kết quả | Kiểm tra noti và email Bond_04 khi xác nhận thành công | Đã làm TC_XNHS_XN_001 | 1. Mở thông báo trên app<br>2. Mở email của TK1 | HĐ-A | - 1 noti: tiêu đề "[SH-Bond] Xác nhận thành công", nội dung "Hợp đồng giao dịch trái phiếu TPQ12312 (Số HĐ: 20240610BGB1231245325345) đã được xác nhận thành công."<br>- 1 email: tiêu đề "[SH-Bond] Xác nhận thành công Hợp đồng giao dịch trái phiếu TPQ12312" | Medium | Integration | UC |  |
| TC_XNHS_XN_007 | BR-11 | Xác nhận hồ sơ – Kết quả | Kiểm tra lịch sử xác nhận khi xác nhận thành công | Đã làm TC_XNHS_XN_001 | 1. Làm SOP-02<br>2. Thử xóa/sửa dòng lịch sử trên Portal | HĐ-A; TK1 | - Có 1 dòng: thời gian xác nhận, nền tảng App, số TKCK và họ tên TK1, loại "Hợp đồng đặt bán TP", bản hồ sơ đã xác nhận<br>- Không xóa/sửa được | Medium | Integration | EP | Nơi xem – Q-13 |
| TC_XNHS_TH_001 | BR-12 | Xác nhận hồ sơ – Thời hạn | Kiểm tra xác nhận HĐ đặt bán vào ngày T+3 | BO đổi giờ hệ thống tới ngày T+3 của HĐ mới | 1. Mở Chi tiết HĐ mới > tab "Xác nhận"<br>2. Làm SOP-01 | HĐ mới | - Có tab "Xác nhận"; xác nhận thành công | Medium | Boundary | BVA | Ngày làm việc hay ngày lịch – Q-07 |
| TC_XNHS_TH_002 | BR-12, BR-02 | Xác nhận hồ sơ – Thời hạn | Kiểm tra hồ sơ khi đến E-5 mà HĐ đặt bán chưa xác nhận | BO đổi giờ hệ thống tới E-5 của HĐ mới (chưa xác nhận) | 1. Bấm "Xem hợp đồng" | HĐ mới | - Hiện cả HĐ đặt bán và Phiếu lệnh, xác nhận chung 1 lần | Medium | Boundary | BVA | Q-07 |
| TC_XNHS_TH_003 | BR-12 | Xác nhận hồ sơ – Thời hạn | Kiểm tra xác nhận Phiếu lệnh lúc 10:59 ngày E | BO đổi giờ hệ thống 10:59 ngày E của HĐ-C | 1. Làm SOP-01 (xong trước 11:00) | HĐ-C | - Xác nhận thành công | Medium | Boundary | BVA | Q-06 |
| TC_XNHS_TH_004 | BR-12 | Xác nhận hồ sơ – Thời hạn | Kiểm tra bấm "Xem hợp đồng" khi đã quá 11h ngày E | TK1 đang ở Chi tiết HĐ-C (mở trước 11h); BO đổi giờ hệ thống 11:05 ngày E | 1. Bấm "Xem hợp đồng"<br>2. Bấm "Đóng" | HĐ-C | - Popup "Quá hạn xác nhận": "Hồ sơ của hợp đồng {Số HĐ} đã quá thời hạn xác nhận. Vui lòng liên hệ CSKH để được hỗ trợ."<br>- Không mở màn xem hồ sơ<br>- Bấm "Đóng": về tab "Xác nhận", không còn HĐ-C | High | Negative | BVA |  |
| TC_XNHS_TH_005 | BR-12 | Xác nhận hồ sơ – Thời hạn | Kiểm tra xác nhận khi qua 11h ngày E lúc đang ở màn OTP | TK1 ở màn OTP của HĐ-C lúc 10:58; BO đổi giờ hệ thống 11:00 | 1. Nhập OTP đúng, bấm "Xác nhận" | HĐ-C | - Popup "Quá hạn xác nhận"<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04<br>- HĐ-C "Hết hiệu lực", KH nhận Bond_05 | High | Negative | BVA | Q-06 |
| TC_XNHS_TH_006 | BR-12 | Xác nhận hồ sơ – Thời hạn | Kiểm tra thời hạn khi giờ điện thoại khác giờ hệ thống | BO đổi giờ hệ thống 11:05 ngày E của HĐ-C; điện thoại chỉnh tay 10:00 | 1. Bấm "Xem hợp đồng" | HĐ-C | - Báo popup "Quá hạn xác nhận" (theo giờ hệ thống, không theo giờ điện thoại) | Medium | Negative | EG |  |
| TC_XNHS_VS_001 | BR-13 | Xác nhận hồ sơ – Hồ sơ có bản mới | Kiểm tra xác nhận khi hồ sơ có bản mới trong lúc KH đang xem | TK1 ở màn OTP của HĐ-C; BO tạo bản mới của Phiếu lệnh | 1. Nhập OTP đúng, bấm "Xác nhận" | HĐ-C | - Popup "Hồ sơ đã được cập nhật": "Hồ sơ của hợp đồng {Số HĐ} vừa có phiên bản mới. Quý khách vui lòng xem lại và xác nhận."<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04<br>- KH nhận Bond_03 | High | Negative | ST |  |
| TC_XNHS_VS_002 | BR-13 | Xác nhận hồ sơ – Hồ sơ có bản mới | Kiểm tra bấm "Xem lại" và xác nhận bản mới | Đang hiện popup ở TC_XNHS_VS_001 | 1. Bấm "Xem lại"<br>2. Cuộn hết, tick, xác nhận OTP | HĐ-C | - Hiện bản mới từ đầu, ô tick bị bỏ và khóa<br>- Xác nhận thành công; Portal ghi nhận bản mới | Medium | Positive | ST |  |
| TC_XNHS_CL_001 | BR-14 | Xác nhận hồ sơ – Chống bấm trùng | Kiểm tra bấm liên tiếp nhiều lần các nút | PC-01 | 1. Bấm nhanh 3 lần "Xem hợp đồng"<br>2. Cuộn hết, tick, bấm nhanh 3 lần "Xác nhận"<br>3. Nhập OTP đúng, bấm nhanh 3 lần "Xác nhận" | HĐ-A | - Mỗi bước chỉ chạy 1 lần: 1 màn xem hồ sơ, 1 OTP, 1 lần xác nhận<br>- Portal: 1 dòng lịch sử; KH nhận 1 Bond_04 | High | Negative | EG |  |
| TC_XNHS_CL_002 | BR-14 | Xác nhận hồ sơ – Chống bấm trùng | Kiểm tra bấm lại xác nhận khi mất mạng ngay sau khi gửi OTP | PC-03 | 1. Nhập OTP đúng, bấm "Xác nhận" rồi tắt mạng ngay<br>2. Bật mạng, bấm "Xác nhận" lại | HĐ-A | - App hiện kết quả thành công<br>- Portal: chỉ 1 dòng lịch sử; KH nhận 1 Bond_04 | High | Negative | EG | [GIẢ ĐỊNH] – Q-08 |
| TC_XNHS_CL_003 | BR-14 | Xác nhận hồ sơ – Chống bấm trùng | Kiểm tra xác nhận cùng lúc trên 2 điện thoại | TK1 đăng nhập trên 2 máy, cả 2 ở màn OTP của HĐ-A | 1. Máy 1 xác nhận thành công<br>2. Máy 2 nhập OTP đúng, bấm "Xác nhận" | HĐ-A | - Máy 2 không báo thành công lần 2<br>- Portal: 1 dòng lịch sử; KH nhận 1 Bond_04 | Medium | Negative | ST | [GIẢ ĐỊNH] – Q-15 |
| TC_XNHS_QL_001 | BR-15 | Xác nhận hồ sơ – Quay lại | Kiểm tra quay lại từ màn xem hồ sơ | PC-02 + đã cuộn hết và tick | 1. Bấm quay lại<br>2. Bấm lại "Xem hợp đồng" | HĐ-A | - Bước 1: về Chi tiết HĐ-A<br>- Bước 2: hồ sơ hiện từ đầu, ô tick bị bỏ và khóa<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | Medium | Positive | UC |  |
| TC_XNHS_QL_002 | BR-15 | Xác nhận hồ sơ – Quay lại | Kiểm tra quay lại từ màn OTP | PC-03 | 1. Bấm quay lại | HĐ-A | - Về Chi tiết HĐ-A<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | Medium | Negative | UC |  |
| TC_XNHS_LOI_001 | BR-16 | Xác nhận hồ sơ – Lỗi | Kiểm tra mở hồ sơ khi link hồ sơ đã hết hạn | PC-01; link hồ sơ đã hết hạn (giả lập bằng Proxyman) | 1. Bấm "Xem hợp đồng"<br>2. Bấm "Đóng", bấm lại "Xem hợp đồng" (bỏ giả lập) | HĐ-A | - Bước 1: popup lỗi chuẩn, không tick được<br>- Bước 2: mở được hồ sơ bằng link mới | Medium | Negative | UC | Chữ popup – Q-11; Q-14 |
| TC_XNHS_LOI_002 | BR-16 | Xác nhận hồ sơ – Lỗi | Kiểm tra khi hệ thống trả lỗi | PC-01; giả lập lỗi BND-999 bằng Proxyman lần lượt ở: mở hồ sơ, gửi OTP, xác nhận OTP | 1. Thực hiện thao tác tương ứng ở mỗi điểm lỗi | HĐ-A | - Mỗi lần hiện popup "Có lỗi xảy ra. Vui lòng thử lại sau"<br>- Không chuyển bước tiếp<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | Medium | Negative | EG | Q-11 |
| TC_XNHS_LOI_003 | BR-16 | Xác nhận hồ sơ – Lỗi | Kiểm tra xác nhận khi phiên đăng nhập đã hết hạn | PC-03; đăng nhập TK1 trên máy khác để hết phiên máy này | 1. Nhập OTP đúng, bấm "Xác nhận" | HĐ-A | - Popup "Phiên đăng nhập không hợp lệ hoặc đã hết hạn", nút "Đăng nhập"<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | Medium | Negative | EG |  |
| TC_XNHS_LOI_004 | BR-16 | Xác nhận hồ sơ – Lỗi | Kiểm tra mở hồ sơ khi mất mạng | PC-01; tắt Wi-Fi và 4G | 1. Bấm "Xem hợp đồng"<br>2. Bật mạng, bấm lại | HĐ-A | - Bước 1: popup lỗi chuẩn, không mở màn xem<br>- Bước 2: mở được hồ sơ | Medium | Negative | EG | Q-11 |
| TC_XNHS_DB_001 | BR-17 | Xác nhận hồ sơ – Trường hợp đặc biệt | Kiểm tra xác nhận khi giao dịch bị hủy lúc KH đang xem | TK1 ở màn OTP của HĐ-A; BO hủy giao dịch HĐ-A | 1. Nhập OTP đúng, bấm "Xác nhận" | HĐ-A | - Popup "Quá hạn xác nhận"<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04<br>- KH nhận Bond_07 | Medium | Negative | ST |  |
| TC_XNHS_DB_002 | BR-17 | Xác nhận hồ sơ – Trường hợp đặc biệt | Kiểm tra xác nhận khi Portal đã chuyển "Đủ hồ sơ" lúc KH đang xem | TK1 ở màn OTP của HĐ-A; BO chuyển HĐ đặt bán sang "Đủ hồ sơ" | 1. Nhập OTP đúng, bấm "Xác nhận" | HĐ-A | - App hiện thông báo hệ thống trả về, không báo thành công<br>- Portal: không có lịch sử xác nhận online; không gửi Bond_04 | Medium | Negative | ST | [GIẢ ĐỊNH] – Q-12 |
| TC_XNHS_BM_001 | BR-18 | Xác nhận hồ sơ – Bảo mật | Kiểm tra xem hồ sơ của HĐ người khác | PC-01; dùng Proxyman đổi mã HĐ khi mở hồ sơ thành HĐ-E | 1. Bấm "Xem hợp đồng" | HĐ-E (của TK2) | - Bị chặn, báo lỗi<br>- Không hiện hồ sơ của HĐ-E | High | Security | EG |  |
| TC_XNHS_BM_002 | BR-18 | Xác nhận hồ sơ – Bảo mật | Kiểm tra xác nhận hồ sơ của HĐ người khác | PC-03; dùng Proxyman đổi appendixId khi xác nhận thành hồ sơ HĐ-E | 1. Nhập OTP đúng, bấm "Xác nhận" | HĐ-E (của TK2) | - Bị chặn, báo lỗi<br>- Portal: hồ sơ HĐ-E và HĐ-A không đổi | High | Security | EG |  |
| TC_XNHS_BM_003 | BR-18 | Xác nhận hồ sơ – Bảo mật | Kiểm tra xác nhận khi chưa nhập OTP | PC-03; dùng Proxyman/Postman gửi thẳng lệnh xác nhận (A3) của HĐ-A, bỏ qua bước OTP | 1. Gửi lệnh xác nhận | HĐ-A | - Bị chặn, không báo thành công<br>- Portal: hồ sơ vẫn "Thiếu hồ sơ", không có lịch sử mới, không gửi Bond_04 | High | Security | EG | [GIẢ ĐỊNH] – Q-03 |
| TC_XNHS_BM_004 | BR-18 | Xác nhận hồ sơ – Bảo mật | Kiểm tra hồ sơ khi đổi tài khoản trên cùng điện thoại | TK1 đã xem hồ sơ HĐ-A, đăng xuất; đăng nhập TK2 | 1. TK2 mở Chi tiết HĐ-E > "Xem hợp đồng" | TK2; HĐ-E | - Chỉ hiện hồ sơ và thông tin của TK2, không còn dữ liệu của TK1 | Medium | Security | EG |  |
| TC_XNHS_EVT_001 | BR-19 | Xác nhận hồ sơ – Tracking | Kiểm tra sự kiện tracking trong luồng xác nhận | PC-01; bật công cụ xem tracking | 1. Làm SOP-01 | HĐ-A | - Ghi nhận đủ 4 sự kiện theo thứ tự: SBOND.DOCCONFIRM.DOC.VIEW, .DOC.READ, .OTP.REQUEST, .OTP.SUBMIT | Low | Integration | UC |  |

### 4.2 API O4 / A3 (5 TC)
| TC ID | Req ID | Module | Title | Precondition | Steps | Test Data | Expected Result | Priority | Type | Technique | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|
| TC_XNHS_API_001 | BR-20, BR-02 | Xác nhận hồ sơ – API | Kiểm tra O4 trả hồ sơ cần xác nhận | Postman, token TK1 | 1. Gọi O4 với mã HĐ-A, confirmStatus = 0<br>2. Gọi O4 với mã HĐ-B | HĐ-A; HĐ-B | - HTTP 200<br>- HĐ-A: 1 hồ sơ; HĐ-B: 2 hồ sơ; mỗi hồ sơ có loại, tên, bản, link xem | Medium | Positive | EP |  |
| TC_XNHS_API_002 | BR-20, BR-14 | Xác nhận hồ sơ – API | Kiểm tra A3 xác nhận thành công và gửi trùng | Postman, token TK1, OTP đã xác thực | 1. Gọi A3 với appendixId HĐ-A, Idempotency-Key = K1<br>2. Gọi lại y hệt | HĐ-A; K1 = 3f6c1a2e-0000-4000-8000-000000000001 | - Lần 1: code = 0, status = APPROVED, allowConfirm = 0<br>- Lần 2: kết quả giống lần 1<br>- Portal: 1 dòng lịch sử | High | Positive | EG | Idempotency-Key = mã chống gửi trùng |
| TC_XNHS_API_003 | BR-20 | Xác nhận hồ sơ – API | Kiểm tra A3 khi appendixId sai | Postman, token TK1, OTP đã xác thực | 1. Gọi A3 không có appendixId<br>2. Gọi A3 với appendixId = 999999999 | — | - Cả 2 lần báo lỗi (code ≠ 0)<br>- Không hồ sơ nào đổi trạng thái | Medium | Negative | EP |  |
| TC_XNHS_API_004 | BR-20, BR-12, BR-13 | Xác nhận hồ sơ – API | Kiểm tra A3 khi hồ sơ quá hạn hoặc là bản cũ | Postman, token TK1, OTP đã xác thực; HĐ-C đã quá 11h ngày E; HĐ-B có Phiếu lệnh bản cũ và bản mới | 1. Gọi A3 với hồ sơ HĐ-C<br>2. Gọi A3 với Phiếu lệnh bản cũ của HĐ-B | HĐ-C; HĐ-B | - Bước 1: lỗi tương ứng "Quá hạn xác nhận"<br>- Bước 2: lỗi tương ứng "Hồ sơ đã được cập nhật"<br>- Không hồ sơ nào đổi trạng thái | Medium | Negative | BVA |  |
| TC_XNHS_API_005 | BR-20, BR-18 | Xác nhận hồ sơ – API | Kiểm tra O4 và A3 khi không có token | Postman, bỏ header Authorization | 1. Gọi O4 với HĐ-A<br>2. Gọi A3 với hồ sơ HĐ-A | HĐ-A | - Cả 2 báo lỗi AUTH_001 (HTTP 401)<br>- Không trả hồ sơ, không đổi trạng thái | Medium | Security | EP |  |

## B5. Tự review + RTM
### RTM
| BR ID | Mô tả ngắn | Số TC | TC IDs | Phủ |
|---|---|---|---|---|
| BR-01 | Bấm "Xem hợp đồng" thì mở màn xem hồ sơ của đúng HĐ đó | 2 | TC_XNHS_UI_001, TC_XNHS_HS_001 | Đủ |
| BR-02 | Chỉ hiện hồ sơ đang "Cần xác nhận" (bảng 4.1). HĐ có cả 2 hồ sơ thì hi… | 8 | TC_XNHS_HS_001, TC_XNHS_HS_002, TC_XNHS_HS_003, TC_XNHS_HS_004, TC_XNHS_DOC_002, TC_XNHS_XN_002, TC_XNHS_TH_002, TC_XNHS_API_001 | Đủ |
| BR-03 | Hồ sơ đã điền sẵn thông tin KH và HĐ, là bản mới nhất | 1 | TC_XNHS_HS_005 | Đủ |
| BR-04 | Phải cuộn đến cuối mọi hồ sơ mới tick được ô "đã đọc". Phải tick mới b… | 4 | TC_XNHS_UI_001, TC_XNHS_DOC_001, TC_XNHS_DOC_002, TC_XNHS_DOC_003 | Đủ |
| BR-05 | Bấm "Xác nhận" thì gửi OTP và mở màn OTP (tiêu đề "Mã xác thực OTP", ô… | 3 | TC_XNHS_UI_002, TC_XNHS_OTP_001, TC_XNHS_OTP_002 | Đủ |
| BR-06 | OTP sai, hết hạn hoặc nhập sai quá số lần thì báo lỗi theo dịch vụ OTP… | 3 | TC_XNHS_OTP_003, TC_XNHS_OTP_004, TC_XNHS_OTP_005 | Đủ |
| BR-07 | Gửi lại OTP khi đồng hồ về 00:00. Gửi lại quá số lần thì báo lỗi | 2 | TC_XNHS_OTP_006, TC_XNHS_OTP_007 | Đủ |
| BR-08 | Xác nhận thành công: hồ sơ chuyển "Đủ hồ sơ", HĐ vẫn "Hiệu lực", hiện … | 4 | TC_XNHS_UI_003, TC_XNHS_XN_001, TC_XNHS_XN_002, TC_XNHS_XN_003 | Đủ |
| BR-09 | HĐ hết hồ sơ cần xác nhận thì rời tab "Xác nhận", bỏ cảnh báo ở Danh m… | 2 | TC_XNHS_XN_004, TC_XNHS_XN_005 | Đủ |
| BR-10 | Gửi noti và email Bond_04 khi xác nhận thành công | 1 | TC_XNHS_XN_006 | Đủ |
| BR-11 | Lưu lại lịch sử xác nhận (thời gian, nền tảng, số TKCK, họ tên, loại h… | 1 | TC_XNHS_XN_007 | Đủ |
| BR-12 | Thời hạn xác nhận: HĐ đặt bán từ T+3 đến trước E-5; Phiếu lệnh từ E-5 … | 7 | TC_XNHS_TH_001, TC_XNHS_TH_002, TC_XNHS_TH_003, TC_XNHS_TH_004, TC_XNHS_TH_005, TC_XNHS_TH_006, TC_XNHS_API_004 | Đủ |
| BR-13 | Hồ sơ có bản mới trong lúc KH đang xem thì không ghi nhận, báo popup I… | 3 | TC_XNHS_VS_001, TC_XNHS_VS_002, TC_XNHS_API_004 | Đủ |
| BR-14 | Chống bấm trùng: nút bị khóa khi đang xử lý; gửi trùng thì chỉ ghi nhậ… | 4 | TC_XNHS_CL_001, TC_XNHS_CL_002, TC_XNHS_CL_003, TC_XNHS_API_002 | Đủ |
| BR-15 | Bấm quay lại ở màn xem hồ sơ hoặc màn OTP thì về Chi tiết HĐ, không nh… | 2 | TC_XNHS_QL_001, TC_XNHS_QL_002 | Đủ |
| BR-16 | Link hồ sơ hết hạn, lỗi hệ thống, hết phiên đăng nhập thì hiện popup l… | 4 | TC_XNHS_LOI_001, TC_XNHS_LOI_002, TC_XNHS_LOI_003, TC_XNHS_LOI_004 | Đủ |
| BR-17 | Trường hợp đặc biệt khi KH đang xem: giao dịch bị hủy thì báo E01; Por… | 2 | TC_XNHS_DB_001, TC_XNHS_DB_002 | Đủ |
| BR-18 | Chỉ xem và xác nhận được HĐ của chính mình; không xác nhận được nếu ch… | 5 | TC_XNHS_BM_001, TC_XNHS_BM_002, TC_XNHS_BM_003, TC_XNHS_BM_004, TC_XNHS_API_005 | Đủ |
| BR-19 | Ghi nhận 4 sự kiện tracking: mở hồ sơ, tick đã đọc, gửi OTP, xác nhận … | 1 | TC_XNHS_EVT_001 | Đủ |
| BR-20 | API: O4 lấy hồ sơ (GET /api/v1/bond/app/order/documents/filter); A3 xá… | 5 | TC_XNHS_API_001, TC_XNHS_API_002, TC_XNHS_API_003, TC_XNHS_API_004, TC_XNHS_API_005 | Đủ |

### Checklist
| Tiêu chí | Kết quả | Ghi chú |
|---|---|---|
| Mọi BR đều có TC | Đạt | 20/20 BR |
| Có luồng chính, luồng phụ, luồng lỗi | Đạt | XN_001–002 · 2 hồ sơ, gửi lại OTP, quay lại, Xem lại · quá hạn, OTP sai, bản mới, hủy GD, lỗi, mất mạng |
| Mỗi trường hợp bảng 4.1 có TC | Đạt | HS_001–004 |
| Có case bảo mật | Đạt | BM_001–004, API_005 |
| Mỗi thông tin chỉ 1 TC, không trùng | Đạt | 54 ID duy nhất |
| Dữ liệu test cụ thể | Đạt | HĐ-A…E, TK1/TK2, Số HĐ, Mã TP |
| Kết quả mong đợi kiểm được | Đạt | Chữ trên màn, trạng thái nút, trạng thái Portal, số thông báo |
| Case lỗi kiểm cả báo lỗi và không ghi nhận | Đạt | Dòng "Portal: hồ sơ vẫn Thiếu hồ sơ…" |
| Thông báo đúng nguyên văn SRS | Đạt | E01, I01, S01, Bond_04; chữ lấy từ Figma có [GIẢ ĐỊNH] |

### Thay đổi so với v1
- v2: gộp các case cùng 1 thông tin thành 1 TC (VD: cuộn hồ sơ, tick/bỏ tick, gửi lại OTP, bấm trùng 3 nút, 3 HĐ không có hồ sơ cần xác nhận).
- v2: viết lại câu chữ dễ hiểu, bớt thuật ngữ (thay "revert" = hủy giao dịch, "IDOR" = xem HĐ của người khác, "lưu vết" = lịch sử xác nhận).
- v2: bỏ các case trùng ý hoặc ít giá trị (Back cứng Android, fling, đưa app xuống nền, dán OTP, màn 360dp, 4 TC tracking tách rời).
- v2: rút Q&A từ 25 xuống 15 câu, giữ các câu ảnh hưởng trực tiếp đến kết quả test.

### Thống kê
| Chỉ số | Giá trị | Mục tiêu |
|---|---|---|
| Tổng TC | 54 (App 49 · API 5) | — |
| High / Medium / Low | 18 (33%) / 32 (59%) / 4 (7%) | ≈ 25 / 50 / 25 |
| Positive : Không-positive | 14 : 40 (26% : 74%) | ≈ 30 : 70 |
| Theo Type | Negative 19 · Positive 14 · Boundary 8 · Integration 5 · Security 5 · UI 3 | — |
| BR được phủ | 20/20 (100%) | 100% |
| Q&A mở | 15 (Blocker 0 · Major 8 · Minor 7) | Blocker = 0 |
| TC có [GIẢ ĐỊNH] | 8 | — |
| Ghi chú | Bản v2 gộp mỗi thông tin còn 1 TC; số TC giảm từ 124 (v1) xuống 54. TC High nhiều vì màn xác nhận có giá trị pháp lý | — |
