# Smart CRM – Phân công kỹ thuật viên & Lịch hẹn (Luồng L4)

**Trường Đại học Văn Lang – Khoa Công nghệ Thông tin**
**Chuyên đề 1 – Bài tập 1 – L4-SE**

- **GVHD:** TS. Nguyễn Trí Hải
- **SVTH:** Bùi Đăng Khoa – **MSSV:** 2374802013428
- **Repo:** https://github.com/Bui-DangKhoa/smart-crm-technician-scheduling.git
---

## Thành phần 1 – Bản SRS rút gọn

### 1.1. Giới thiệu và phạm vi

#### 1.1.1. Bối cảnh doanh nghiệp

Công ty Cổ phần Bán lẻ & Dịch vụ Mekong Mobile là chuỗi bán lẻ thiết bị di động và dịch vụ bảo hành – sửa chữa với 24 cửa hàng bán lẻ và 6 trung tâm bảo hành tại TP. Hồ Chí Minh, Cần Thơ và Hà Nội. Với khoảng 180 nhân viên và hơn 65.000 khách hàng, công ty tiếp nhận trung bình 260 yêu cầu bảo hành mỗi tháng.

Hiện nay việc phân công kỹ thuật viên tại các trung tâm bảo hành được thực hiện thủ công theo trí nhớ của Quản lý trung tâm, dẫn đến lệch tải nghiêm trọng giữa các kỹ thuật viên (người nhận 40 phiếu/tháng, người chỉ nhận 12 phiếu/tháng) và khoảng 15% phiếu bị quá hạn cam kết mà không được cảnh báo. Dự án Smart CRM được triển khai nhằm số hóa quy trình điều hành và quản lý dịch vụ bảo hành.

#### 1.1.2. Luồng nghiệp vụ lựa chọn

Luồng nghiệp vụ đã chọn: **L4 – Phân công kỹ thuật viên và lịch hẹn**.

- **Mô tả phạm vi một câu:** "Quản lý trung tâm đặt lịch hẹn giao nhận, phân công kỹ thuật viên theo tay nghề và địa bàn, kỹ thuật viên xác nhận, kiểm tra trùng lịch và cập nhật trạng thái xử lý."
- **Vấn đề giải quyết:** Trực tiếp giải quyết vấn đề **V2** (quá hạn cam kết do quản lý thủ công) và **V3** (khối lượng công việc bị lệch tải) trong case study Mekong Mobile.

#### 1.1.3. Phạm vi chủ ý KHÔNG làm

1. **Tự động gửi SMS/Zalo/Email thông báo:** không tích hợp cổng tin nhắn ngoài để tự động gửi thông báo lịch hẹn tới khách hàng (chỉ quản lý và lưu trạng thái lịch hẹn trên hệ thống web).
2. **Tự động phân công bằng thuật toán AI/ML:** không dùng học máy để tự gán phiếu; Quản lý trung tâm chủ động chọn kỹ thuật viên từ danh sách lọc theo tay nghề.
3. **Tích hợp tổng đài VoIP / gọi điện tự động:** khách hàng không xác nhận lịch hẹn qua tổng đài tự động.
4. **Báo cáo tổng hợp tự động tỷ lệ hoàn thành SLA theo tháng:** không xây dựng module báo cáo phân tích nâng cao hay xuất báo cáo cho Ban giám đốc (thuộc phạm vi luồng L6/L8).

#### 1.1.4. Bảng thuật ngữ chuẩn

| Thuật ngữ nghiệp vụ | Định nghĩa nghiệp vụ | Tên kỹ thuật gợi ý |
|---|---|---|
| Phiếu bảo hành | Một yêu cầu bảo hành/sửa chữa được ghi nhận, có mã duy nhất và vòng đời trạng thái. | `ticket` |
| Kỹ thuật viên | Nhân viên thực hiện sửa chữa, có danh sách tay nghề (proficiency) và trung tâm làm việc. | `technician` |
| Trạng thái phiếu | Vị trí của phiếu trong vòng đời: MỚI → ĐÃ PHÂN CÔNG → ĐANG XỬ LÝ → CHỜ LINH KIỆN → HOÀN TẤT → ĐÃ ĐÓNG. | `status` / `ticket_status` |
| Hạn cam kết (SLA) | Thời điểm chậm nhất phải hoàn tất phiếu, sinh tự động từ thời điểm tiếp nhận theo mức ưu tiên. | `due_date` |
| Nhóm sự cố | Phân loại nguyên nhân bảo hành: Màn hình, Pin, Sạc, Phần mềm, Nước vào, Khác. | `issue_category` |
| Mức ưu tiên | Mức độ khẩn của phiếu: CAO (24h), TRUNG BÌNH (72h), THẤP (120h). | `priority` |
| Lịch hẹn | Khung thời gian đã hẹn giữa khách hàng và kỹ thuật viên để giao – nhận thiết bị. | `appointment` |
| Trung tâm bảo hành | Đơn vị cung cấp dịch vụ bảo hành – sửa chữa thuộc chuỗi Mekong Mobile. | `service_center` |

### 1.2. Các bên liên quan và vai trò

| Vai trò / Actor | Có thể làm | Không thể làm |
|---|---|---|
| **Quản lý trung tâm** | - Tra cứu danh sách phiếu chưa phân công thuộc trung tâm phụ trách, sắp xếp theo SLA.<br>- Phân công phiếu cho kỹ thuật viên thỏa điều kiện tay nghề và cùng trung tâm.<br>- Thay đổi kỹ thuật viên phụ trách (bắt buộc nhập lý do).<br>- Đặt lịch hẹn giao – nhận máy, chọn kỹ thuật viên và khung thời gian. | - Gán phiếu cho kỹ thuật viên không đủ tay nghề hoặc thuộc trung tâm khác.<br>- Xem/quản lý dữ liệu của trung tâm khác.<br>- Xóa vật lý bản ghi phiếu hoặc lịch hẹn. |
| **Kỹ thuật viên** | - Tra cứu danh sách phiếu được phân công cho cá nhân, ưu tiên theo SLA.<br>- Cập nhật trạng thái phiếu theo đúng vòng đời.<br>- Tra cứu lịch sử sửa chữa thiết bị để phát hiện lỗi lặp lại (≥ 3 lần). | - Tự phân công hoặc gán lại phiếu cho kỹ thuật viên khác.<br>- Chuyển trạng thái ngược vòng đời.<br>- Xem danh sách phiếu của kỹ thuật viên khác. |
| **Khách hàng** | - Tra cứu thông tin lịch hẹn giao – nhận máy đã đăng ký.<br>- Xác nhận lịch hẹn, chuyển trạng thái lịch hẹn sang "ĐÃ XÁC NHẬN". | - Tự thay đổi kỹ thuật viên phụ trách hoặc can thiệp quy trình phân công.<br>- Cập nhật trạng thái phiếu bảo hành. |
| **Hệ thống nhắc hạn** | - Tự động tính hạn cam kết SLA (`due_date`) theo mức ưu tiên.<br>- Tự động quét và cảnh báo/đánh dấu phiếu sắp hoặc đã quá hạn. | - Tự động thay đổi kỹ thuật viên xử lý.<br>- Tự động đóng phiếu khi chưa có xác nhận của con người. |

### 1.3. Yêu cầu chức năng

#### 1.3.1. Bộ User Stories

**US1 (MUST)** – Là Quản lý trung tâm, tôi muốn xem danh sách phiếu bảo hành chưa phân công sắp xếp theo hạn cam kết, để ưu tiên xử lý các phiếu sắp quá hạn.
- **AC1 (Luồng chính):** GIVEN hệ thống có các phiếu ở trạng thái MỚI, WHEN Quản lý mở danh sách chưa phân công, THEN hệ thống hiển thị danh sách xếp theo `due_date` từ gần nhất đến xa nhất.
- **AC2 (Ngoại lệ):** GIVEN phiếu có `due_date` nhỏ hơn thời gian hiện tại, WHEN danh sách hiển thị, THEN hệ thống đánh dấu màu đỏ cảnh báo "Quá hạn SLA".

**US2 (MUST)** – Là Quản lý trung tâm, tôi muốn gán phiếu bảo hành cho kỹ thuật viên đủ điều kiện kèm số phiếu họ đang giữ, để giao đúng người và chia đều tải công việc.
- **AC1 (Luồng chính – QT-08):** GIVEN phiếu thuộc nhóm sự cố "Màn hình", WHEN Quản lý mở form gán kỹ thuật viên, THEN hệ thống chỉ hiển thị kỹ thuật viên cùng `center_id` và có `proficiency >= 3` đối với nhóm "Màn hình".
- **AC2 (Ngoại lệ – QT-07):** GIVEN phiếu đã có kỹ thuật viên A xử lý, WHEN Quản lý chọn gán lại cho kỹ thuật viên B, THEN hệ thống bắt buộc mở ô nhập "Lý do thay đổi" (tối thiểu 10 ký tự).

**US3 (SHOULD)** – Là Quản lý trung tâm, tôi muốn đặt lịch hẹn giao – nhận máy và được cảnh báo/chặn khi kỹ thuật viên bị trùng lịch, để tránh hẹn chồng cho khách.
- **AC1 (Luồng chính):** GIVEN kỹ thuật viên A chưa có lịch hẹn trong khung 14:00 – 15:00 ngày 30/10, WHEN Quản lý chọn A và lưu lịch hẹn khung đó, THEN hệ thống lưu bản ghi `appointment` thành công.
- **AC2 (Ngoại lệ):** GIVEN kỹ thuật viên A đã có lịch hẹn khung 14:00 – 15:00 ngày 30/10, WHEN Quản lý cố lưu thêm lịch hẹn mới cho A trong cùng khung giờ, THEN hệ thống từ chối lưu và báo lỗi trùng lịch.

**US4 (MUST)** – Là Kỹ thuật viên, tôi muốn xem danh sách phiếu bảo hành được gán cho tôi sắp theo hạn cam kết, để ưu tiên xử lý phiếu sắp quá hạn trước.
- **AC1 (Luồng chính):** GIVEN kỹ thuật viên A đã đăng nhập, WHEN truy cập màn hình cá nhân, THEN hệ thống chỉ hiển thị phiếu gán cho A, sắp ưu tiên theo `due_date`.
- **AC2 (Ngoại lệ):** GIVEN kỹ thuật viên A chưa được gán phiếu nào, WHEN mở màn hình, THEN hệ thống hiển thị "Bạn hiện chưa có phiếu bảo hành nào cần xử lý".

**US5 (MUST)** – Là Kỹ thuật viên, tôi muốn chuyển trạng thái phiếu bảo hành theo đúng vòng đời, để các bên theo dõi tiến độ.
- **AC1 (Luồng chính – QT-06):** GIVEN phiếu ở trạng thái "ĐÃ PHÂN CÔNG", WHEN kỹ thuật viên chọn "Bắt đầu sửa", THEN hệ thống chuyển sang ĐANG XỬ LÝ và ghi log vào `ticket_status_log`.
- **AC2 (Ngoại lệ):** GIVEN phiếu đang ở trạng thái "HOÀN TẤT", WHEN kỹ thuật viên cố chuyển về "ĐANG XỬ LÝ", THEN hệ thống từ chối và báo lỗi vi phạm vòng đời.

**US6 (COULD)** – Là Kỹ thuật viên, tôi muốn xem lịch sử sửa chữa của thiết bị, để phát hiện thiết bị lặp lại cùng một sự cố từ 3 lần trở lên.

**US7 (COULD)** – Là Khách hàng, tôi muốn xác nhận lịch hẹn giao – nhận máy, để trung tâm biết tôi đã nắm thông tin.

#### 1.3.2. Bảng yêu cầu chức năng

| Mã | Yêu cầu chức năng |
|---|---|
| FR1 | Xem danh sách phiếu bảo hành chưa phân công, sắp theo hạn cam kết, đánh dấu phiếu quá hạn. |
| FR2 | Gán phiếu cho kỹ thuật viên cùng trung tâm có `proficiency >= 3`; đổi người phải nhập lý do. |
| FR3 | Đặt lịch hẹn giao – nhận máy và chặn khi kỹ thuật viên bị trùng lịch. |
| FR4 | Kỹ thuật viên xem danh sách phiếu được gán, sắp theo hạn cam kết. |
| FR5 | Cập nhật trạng thái phiếu theo đúng vòng đời và ghi vào `ticket_status_log`. |
| FR6 | Xem lịch sử sửa chữa của thiết bị, cảnh báo khi lỗi lặp từ 3 lần trở lên. |
| FR7 | Ghi nhận khách hàng xác nhận lịch hẹn giao – nhận. |

### 1.4. Yêu cầu phi chức năng

| Mã NFR | Nhóm | Yêu cầu | Tiêu chí đo lường | FR/US liên quan |
|---|---|---|---|---|
| NFR1 | Hiệu năng | Danh sách phiếu chưa phân công phản hồi nhanh khi tra cứu. | Thời gian phản hồi < 1,5 giây với 5.000 bản ghi trên môi trường kiểm thử cục bộ. | FR1 / US1 |
| NFR2 | Hiệu năng & Khả năng chịu tải | Kiểm tra trùng lịch và lưu lịch hẹn đáp ứng nhiều yêu cầu đồng thời. | Thời gian phản hồi < 500 ms với 50 thao tác đặt lịch đồng thời. | FR3 / US3 |
| NFR3 | Khả năng bảo trì | Logic phân công kỹ thuật viên được cô lập để dễ thay đổi khi quy tắc nghiệp vụ đổi. | Thay đổi QT-08 hoặc tiêu chí ưu tiên chỉ cần sửa trong `TicketAssignmentService`, không quá 1 module. | FR2 / US2 |
| NFR4 | Bảo mật & Phân quyền | Dữ liệu khách hàng được bảo vệ, API yêu cầu xác thực. | 100% số điện thoại hiển thị cho kỹ thuật viên được che 4 số giữa; 100% API quản lý phiếu yêu cầu JWT. | FR1–FR7 |

### 1.5. Ràng buộc và quy tắc nghiệp vụ

#### 1.5.1. Quy tắc nghiệp vụ áp dụng cho luồng L4 (trích bảng 9.1, file CDTN1_Mekong Mobile_Case study Smart CRM)

| Mã | Quy tắc nghiệp vụ | Luồng liên quan |
|---|---|---|
| QT-03 | Thiết bị được xác định duy nhất bằng số serial hoặc IMEI. Một thiết bị chỉ thuộc về một khách hàng tại một thời điểm. | L2, L4 |
| QT-04 | Hạn cam kết sinh tự động từ thời điểm tiếp nhận theo mức ưu tiên: CAO = 24 giờ, TRUNG_BÌNH = 72 giờ, THẤP = 120 giờ. Chỉ tính ngày làm việc (thứ Hai đến thứ Bảy). | L2, L4 |
| QT-06 | Phiếu chỉ được chuyển trạng thái theo đúng vòng đời ở Hình 6.2. Không được quay lại trạng thái trước. Mọi lần chuyển trạng thái đều phải ghi vào `ticket_status_log`. | L2, L4, L5 |
| QT-07 | Một phiếu tại một thời điểm chỉ được gán cho tối đa một kỹ thuật viên. Việc đổi kỹ thuật viên phải được ghi lại kèm lý do. | L4 |
| QT-08 | Kỹ thuật viên chỉ được phân công phiếu thuộc nhóm sự cố mà mình có tay nghề (proficiency ≥ 3) và cùng trung tâm. | L4 |
| QT-13 | Không được xóa vật lý phiếu bảo hành, đơn hàng hay hồ sơ khách hàng. Chỉ đánh dấu ngừng sử dụng (soft delete) và giữ nguyên lịch sử. | Tất cả |
| QT-14 | Nhân viên chỉ xem được dữ liệu của trung tâm hoặc cửa hàng mình làm việc. Quản lý xem được toàn bộ đơn vị mình phụ trách. Ban giám đốc xem được toàn công ty. | Tất cả |
| QT-15 | Số điện thoại khách hàng hiển thị dạng che (ví dụ 090****567) với mọi vai trò trừ Quản lý và Ban giám đốc. | Tất cả |

#### 1.5.2. Các quy tắc nghiệp vụ phân tích theo yêu cầu đề bài

- **QT-L4-01 (Chống trùng lịch hẹn):** Không cho lưu lịch hẹn giao – nhận nếu kỹ thuật viên được chọn đã có lịch hẹn khác trong cùng khung thời gian (kiểm tra xung đột giao thoa khung giờ trong bản ghi `appointment`).
- **QT-L4-02 (Cảnh báo lặp sự cố thiết bị):** Khi mở chi tiết phiếu hoặc thiết bị, nếu thiết bị đã có từ 3 phiếu cũ trở lên cùng một nhóm sự cố, hệ thống tự động hiển thị nhãn "Thiết bị lặp lại lỗi 3 lần".
- **QT-L4-03 (Toàn vẹn dữ liệu gán):** Chỉ được phân công khi phiếu ở trạng thái "MỚI" hoặc "ĐÃ PHÂN CÔNG". Không phân công cho phiếu đã "HOÀN TẤT" hoặc "ĐÃ ĐÓNG".
- **QT-L4-04 (Định dạng lịch hẹn):** `appointment_time` phải ở tương lai (lớn hơn thời gian hiện tại) và nằm trong giờ làm việc của trung tâm (8:00 – 17:30).

### 1.6. Bảng truy vết yêu cầu

| Mã FR | Yêu cầu chức năng | Mã US | Mã UC | MoSCoW | Testcase (BT3) |
|---|---|---|---|---|---|
| FR1 | Xem danh sách phiếu chưa phân công, sắp theo hạn cam kết, đánh dấu phiếu quá hạn. | US1 | UC1 | MUST | |
| FR2 | Gán phiếu cho kỹ thuật viên cùng trung tâm có proficiency >= 3; đổi người phải nhập lý do. | US2 | UC2 | MUST | |
| FR3 | Đặt lịch hẹn giao – nhận máy và chặn khi kỹ thuật viên bị trùng lịch. | US3 | UC3 | SHOULD | |
| FR4 | Kỹ thuật viên xem danh sách phiếu được gán, sắp theo hạn cam kết. | US4 | UC4 | MUST | |
| FR5 | Cập nhật trạng thái phiếu theo đúng vòng đời và ghi vết vào `ticket_status_log`. | US5 | UC5 | MUST | |
| FR6 | Xem lịch sử sửa chữa của thiết bị, cảnh báo khi lỗi lặp từ 3 lần trở lên. | US6 | UC6 | COULD | |
| FR7 | Ghi nhận khách hàng xác nhận lịch hẹn giao – nhận. | US7 | UC7 | COULD | |

---

## Thành phần 2 – Use Case

### 2.1. Use Case Diagram

![Use Case Diagram](docs/usecase.drawio)

> Nguồn: `docs/usecase.drawio`. Các actor: Quản lý trung tâm, Kỹ thuật viên, Khách hàng, Hệ thống nhắc hạn.

### 2.2. Đặc tả chi tiết use case quan trọng nhất – UC2: Gán kỹ thuật viên cho phiếu bảo hành

- **Actor chính:** Quản lý trung tâm
- **Mục tiêu:** Phân công một phiếu bảo hành cụ thể cho kỹ thuật viên đủ điều kiện tay nghề và địa bàn, giúp chia đều tải công việc và đảm bảo xử lý đúng hạn cam kết (SLA).
- **Điều kiện trước:** Quản lý trung tâm đã đăng nhập; phiếu đang ở trạng thái "MỚI" hoặc "ĐÃ PHÂN CÔNG".
- **Điều kiện sau:** Phiếu chuyển sang "ĐÃ PHÂN CÔNG", lưu `technician_id` và ghi 1 bản ghi lịch sử vào `ticket_status_log`.
- **User Story liên quan:** US2 | **Mức ưu tiên:** MUST

**Luồng chính**

1. Quản lý trung tâm truy cập màn hình danh sách phiếu chưa phân công.
2. Quản lý chọn một phiếu cần phân công.
3. Hệ thống hiển thị chi tiết phiếu (mã phiếu, khách hàng, thiết bị, nhóm sự cố, mức ưu tiên, hạn SLA) và tự động lọc các kỹ thuật viên cùng `center_id` có `proficiency >= 3` với nhóm sự cố của phiếu, kèm số phiếu mỗi người đang giữ.
4. Quản lý chọn một kỹ thuật viên từ danh sách gợi ý và bấm "Xác nhận gán".
5. Hệ thống cập nhật `technician_id` cho phiếu, chuyển trạng thái từ "MỚI" sang "ĐÃ PHÂN CÔNG", lưu bản ghi vết vào `ticket_status_log` và hiển thị thông báo phân công thành công.

**Các luồng ngoại lệ**

- **3a. Không có kỹ thuật viên nào đạt `proficiency >= 3` tại trung tâm:**
  - 3a1. Hệ thống cảnh báo: "Không có kỹ thuật viên nào đủ điều kiện tay nghề cho nhóm sự cố này tại trung tâm."
  - 3a2. Hệ thống cho phép Quản lý chọn phân công ngoại lệ (bắt buộc nhập lý do phê duyệt) hoặc chuyển phiếu sang trung tâm bảo hành khác.
- **4a. Phiếu đã có kỹ thuật viên xử lý và Quản lý chọn gán lại cho người mới:**
  - 4a1. Hệ thống mở popup yêu cầu nhập "Lý do thay đổi kỹ thuật viên" (tối thiểu 10 ký tự).
  - 4a2. Nếu không nhập hoặc nhập dưới 10 ký tự rồi bấm "Lưu", hệ thống từ chối cập nhật và báo lỗi: "Vui lòng nhập lý do đổi kỹ thuật viên (tối thiểu 10 ký tự)".
- **4b. Kỹ thuật viên được chọn đang quá tải (giữ từ 15 phiếu đang xử lý trở lên):**
  - 4b1. Hệ thống hiển thị nhãn cảnh báo màu vàng: "Kỹ thuật viên này đang giữ 15 phiếu (vượt ngưỡng trung bình)."
  - 4b2. Quản lý có thể bấm "Hủy" để chọn người khác hoặc "Tiếp tục gán" để xác nhận phân công.

---

## Thành phần 6 – Bảng khai báo sử dụng công cụ AI

| Công cụ | Dùng vào việc gì | Áp dụng ở phần nào | Đã kiểm chứng thế nào |
|---|---|---|---|
| Gemini Notebook | Gợi ý cấu trúc bản SRS rút gọn và rà soát câu từ các User Story theo khuôn chuẩn INVEST. | Mục 1 & 3 (SRS) – `docs/srs.md` | Đọc lại từng User Story; tự điều chỉnh vế "để <giá trị>" gắn trực tiếp với nỗi đau V2, V3 trong case study Mekong Mobile; tự viết bổ sung 10 tiêu chí Given–When–Then cho luồng chính và luồng ngoại lệ. |
| Gemini Notebook | Gợi ý mã PlantUML cho Sơ đồ Use Case và Sơ đồ Kiến trúc phân lớp 4 tầng. | Mục 2 & Thành phần 3 – `docs/usecase.drawio`, `docs/architecture.drawio` | Tự sửa hướng mũi tên `<<extend>>` hướng về Use Case cơ sở; chuẩn hóa thuật ngữ "phiếu bảo hành"; tự bổ sung khung Legend chú thích ký hiệu và vùng NGOÀI PHẠM VI theo quy định. |
| Gemini Notebook | Gợi ý cú pháp SQL DDL Skeleton cho 6 bảng CSDL quan hệ chuẩn 3NF và cú pháp DBML. | Thành phần 4 (ERD) – `docs/erd.drawio`, `docs/schema.sql` | Kiểm tra đối chiếu nguyên tắc chuẩn hóa 3NF; tự thêm các ràng buộc CHECK (proficiency 1–5, trạng thái vòng đời QT-06); tự khai báo và giải thích 3 chỉ mục INDEX gắn với NFR. |
| Không dùng AI | Phân tích bài toán, xác định các quy tắc nghiệp vụ (QT-04, QT-07, QT-08) và lập luận 3 quyết định kiến trúc gắn với NFR. | Mục 4 & 5 (SRS), Thành phần 3 (Lập luận NFR) | Tự thiết lập các ngưỡng đo được (1,5s / 5.000 bản ghi; 500ms / 50 req) và tự cân nhắc, viết 3 câu lập luận đánh đổi kiến trúc theo đúng khuôn mẫu học phần. |
| Gemini Notebook, Claude | Gợi ý và phác thảo Wireframe 3 màn hình và lập Bảng đối chiếu truy vết hai chiều (Wireframe ↔ ERD ↔ SRS). | Thành phần 5 (Wireframe) – `docs/wireframe.png` | Tự thiết kế bố cục trên Draw.io; tự kiểm tra đảm bảo 100% các trường dữ liệu trên màn hình đều tồn tại trong `schema.sql` và Bảng thuật ngữ SRS. |
| PlantUML | Vẽ architecture diagram, ERD. | `architecture.drawio`, `erd.drawio` | Kiểm tra lại sơ đồ được tạo ra có đúng yêu cầu và đầy đủ các chi tiết của 3 lớp thành phần. |
| Gemini Notebook | Gợi ý viết mã SQL DDL skeleton. | Đoạn mã SQL DDL skeleton | Kiểm tra các câu query SQL có tạo bảng và quan hệ giống bảng ERD không. |

**Cam kết:** Tôi xác nhận đã đọc, hiểu và chịu trách nhiệm về toàn bộ nội dung nộp.

Bùi Đăng Khoa – MSSV 2374802013428 – nộp ngày 08/10/2026.
