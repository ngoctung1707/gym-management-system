# Đặc tả Yêu cầu Chức năng (Functional Requirements)

**Đề tài:** Xây dựng hệ thống quản lý phòng gym
**Phiên bản:** 1.0 — Bản nháp

---

## 1. Tổng quan

### 1.1. Tác nhân (Actors)

| Mã | Tác nhân | Nền tảng | Mô tả |
|----|----------|----------|-------|
| A1 | Guest (Khách vãng lai) | Web (Landing page) | Người chưa có tài khoản, tìm hiểu phòng gym |
| A2 | Member (Hội viên) | Mobile App | Khách hàng đã đăng ký tài khoản |
| A3 | Trainer (Huấn luyện viên – PT) | Mobile App | Huấn luyện viên cá nhân |
| A4 | Admin (Chủ phòng gym) | Web Admin | Quản trị toàn bộ hệ thống |
| A5 | AI Service (Hệ thống ngoài) | Backend | Đọc phiếu InBody, gợi ý kế hoạch |

### 1.2. Quy ước

- Mã yêu cầu: `FR-<Module>-<STT>` (VD: `FR-AUTH-01`)
- Độ ưu tiên (MoSCoW): **M** = Must, **S** = Should, **C** = Could

### 1.3. Danh sách Module

| Mã | Module | Tác nhân chính |
|----|--------|----------------|
| AUTH | Xác thực & Tài khoản | Guest, Member, Trainer, Admin |
| LAND | Landing Page | Guest |
| PKG | Gói tập & Đăng ký hội viên | Member, Admin |
| PAY | Thanh toán | Member, Admin |
| CHK | Check-in (QR / Face ID) | Member, Admin |
| BODY | Chỉ số cơ thể (InBody) | Member, Trainer, AI Service |
| PLAN | Kế hoạch tập luyện & dinh dưỡng | Member, Trainer, AI Service |
| APPT | Lịch hẹn với PT | Member, Trainer |
| EXE | Thư viện bài tập & Video | Member, Trainer, Admin |
| CHAT | Nhắn tin Member – PT | Member, Trainer |
| NOTI | Thông báo | Tất cả |
| ADM | Quản trị hệ thống | Admin |
| RPT | Báo cáo & Thống kê | Admin |
| BOT | Chatbot tư vấn (mở rộng) | Guest, Member |

---

## 2. Yêu cầu chức năng chi tiết

### 2.1. AUTH – Xác thực & Tài khoản

- **FR-AUTH-01 (M):** Member đăng ký tài khoản bằng email/số điện thoại + mật khẩu, xác thực OTP.
- **FR-AUTH-02 (M):** Người dùng đăng nhập bằng email/SĐT + mật khẩu.
- **FR-AUTH-03 (M):** Hệ thống phân quyền theo vai trò (RBAC): Member, Trainer, Admin.
- **FR-AUTH-04 (M):** Người dùng đăng xuất, làm mới phiên (refresh token).
- **FR-AUTH-05 (M):** Người dùng quên mật khẩu, đặt lại qua OTP/email.
- **FR-AUTH-06 (M):** Người dùng xem và cập nhật hồ sơ cá nhân (ảnh đại diện, thông tin liên hệ).
- **FR-AUTH-07 (S):** Member đăng ký dữ liệu khuôn mặt (Face Enrollment) phục vụ check-in.
- **FR-AUTH-08 (M):** Tài khoản Trainer do Admin tạo, không tự đăng ký.

### 2.2. LAND – Landing Page (Web)

- **FR-LAND-01 (M):** Guest xem thông tin giới thiệu phòng gym (cơ sở vật chất, giờ mở cửa, địa chỉ).
- **FR-LAND-02 (M):** Guest xem danh sách gói tập và bảng giá.
- **FR-LAND-03 (M):** Guest xem danh sách và hồ sơ PT (chuyên môn, kinh nghiệm, chứng chỉ).
- **FR-LAND-04 (S):** Guest gửi form đăng ký tư vấn / tập thử.
- **FR-LAND-05 (S):** Guest xem liên kết tải ứng dụng mobile.

### 2.3. PKG – Gói tập & Đăng ký hội viên

- **FR-PKG-01 (M):** Member xem danh sách gói tập (`MembershipPackage`): thời hạn, giá, quyền lợi.
- **FR-PKG-02 (M):** Member đăng ký/mua gói tập, hệ thống tạo `Membership` ở trạng thái chờ thanh toán.
- **FR-PKG-03 (M):** Member xem gói đang sử dụng, ngày hết hạn, lịch sử đăng ký.
- **FR-PKG-04 (M):** Member gia hạn gói tập.
- **FR-PKG-05 (S):** Member đăng ký gói tập kèm PT (số buổi PT).
- **FR-PKG-06 (C):** Member yêu cầu bảo lưu (tạm dừng) gói tập, chờ Admin duyệt.
- **FR-PKG-07 (M):** Hệ thống tự động chuyển trạng thái `Membership` khi hết hạn.
- **FR-PKG-08 (M):** Admin thêm/sửa/ẩn gói tập.

### 2.4. PAY – Thanh toán

- **FR-PAY-01 (M):** Member thanh toán trực tuyến qua cổng thanh toán (VNPay/MoMo).
- **FR-PAY-02 (M):** Hệ thống nhận callback/IPN, cập nhật trạng thái `Payment` và kích hoạt `Membership`.
- **FR-PAY-03 (M):** Member xem lịch sử thanh toán và hóa đơn.
- **FR-PAY-04 (M):** Admin ghi nhận thanh toán tại quầy (tiền mặt/chuyển khoản).
- **FR-PAY-05 (M):** Admin tra cứu, lọc danh sách giao dịch.
- **FR-PAY-06 (C):** Admin xử lý hoàn tiền.

### 2.5. CHK – Check-in

- **FR-CHK-01 (M):** Member hiển thị mã QR động (có thời hạn) trên app để check-in.
- **FR-CHK-02 (M):** Thiết bị tại quầy quét QR, hệ thống xác thực và ghi nhận `CheckIn`.
- **FR-CHK-03 (S):** Member check-in bằng nhận diện khuôn mặt (Face Recognition) tại quầy.
- **FR-CHK-04 (M):** Hệ thống từ chối check-in nếu `Membership` hết hạn/bị tạm dừng, hiển thị lý do.
- **FR-CHK-05 (M):** Member xem lịch sử check-in (số buổi tập theo tuần/tháng).
- **FR-CHK-06 (M):** Admin xem nhật ký check-in theo thời gian thực.
- **FR-CHK-07 (S):** Admin check-in thủ công khi thiết bị lỗi.

### 2.6. BODY – Chỉ số cơ thể (InBody)

- **FR-BODY-01 (M):** Member chụp/tải ảnh phiếu kết quả InBody lên app.
- **FR-BODY-02 (M):** AI Service trích xuất chỉ số (cân nặng, % mỡ, khối lượng cơ, BMI, BMR, mỡ nội tạng…) từ ảnh.
- **FR-BODY-03 (M):** Member xem lại và chỉnh sửa kết quả trích xuất trước khi lưu `BodyMetric`.
- **FR-BODY-04 (S):** Member nhập chỉ số thủ công (không cần ảnh).
- **FR-BODY-05 (M):** Member xem lịch sử chỉ số dạng bảng và biểu đồ xu hướng.
- **FR-BODY-06 (M):** Trainer xem chỉ số cơ thể của Member mình phụ trách.

### 2.7. PLAN – Kế hoạch tập luyện & dinh dưỡng

- **FR-PLAN-01 (M):** Member khai báo mục tiêu (giảm mỡ, tăng cơ, duy trì), trình độ, số buổi/tuần.
- **FR-PLAN-02 (M):** AI Service gợi ý `WorkoutPlan` dựa trên mục tiêu và `BodyMetric` gần nhất.
- **FR-PLAN-03 (M):** AI Service gợi ý `MealPlan` (calo, macro, thực đơn mẫu).
- **FR-PLAN-04 (M):** Trainer tạo/chỉnh sửa `WorkoutPlan`, `MealPlan` cho Member.
- **FR-PLAN-05 (S):** Trainer duyệt/điều chỉnh kế hoạch do AI gợi ý trước khi áp dụng.
- **FR-PLAN-06 (M):** Member xem kế hoạch theo ngày/tuần, đánh dấu bài tập đã hoàn thành.
- **FR-PLAN-07 (S):** Trainer theo dõi tiến độ thực hiện kế hoạch của Member.

### 2.8. APPT – Lịch hẹn với PT

- **FR-APPT-01 (M):** Trainer thiết lập khung giờ rảnh (`TrainerSchedule`).
- **FR-APPT-02 (M):** Member đặt lịch hẹn (`Appointment`) với PT theo khung giờ trống.
- **FR-APPT-03 (M):** Trainer xác nhận/từ chối lịch hẹn.
- **FR-APPT-04 (M):** Member/Trainer hủy hoặc dời lịch trước thời hạn quy định.
- **FR-APPT-05 (M):** Hệ thống trừ số buổi PT còn lại khi buổi tập hoàn thành.
- **FR-APPT-06 (M):** Member/Trainer xem lịch hẹn dạng lịch (calendar).

### 2.9. EXE – Thư viện bài tập & Video

- **FR-EXE-01 (M):** Member tra cứu bài tập (`Exercise`) theo nhóm cơ, dụng cụ, độ khó.
- **FR-EXE-02 (M):** Member xem video hướng dẫn và mô tả kỹ thuật bài tập.
- **FR-EXE-03 (M):** Admin/Trainer thêm/sửa/xóa bài tập và video.
- **FR-EXE-04 (C):** Member lưu bài tập yêu thích.

### 2.10. CHAT – Nhắn tin Member – PT

- **FR-CHAT-01 (S):** Member nhắn tin 1–1 với PT phụ trách (thời gian thực).
- **FR-CHAT-02 (S):** Người dùng gửi văn bản và hình ảnh.
- **FR-CHAT-03 (S):** Người dùng xem lịch sử hội thoại, trạng thái đã đọc.
- **FR-CHAT-04 (C):** Admin xử lý báo cáo tin nhắn vi phạm.

### 2.11. NOTI – Thông báo

- **FR-NOTI-01 (M):** Hệ thống gửi push notification nhắc gói tập sắp hết hạn.
- **FR-NOTI-02 (M):** Hệ thống nhắc lịch hẹn PT sắp diễn ra.
- **FR-NOTI-03 (S):** Hệ thống thông báo khi có tin nhắn mới, kế hoạch mới được cập nhật.
- **FR-NOTI-04 (S):** Admin gửi thông báo chung (khuyến mãi, lịch nghỉ lễ) tới Member.
- **FR-NOTI-05 (M):** Người dùng xem danh sách thông báo trong app.

### 2.12. ADM – Quản trị hệ thống (Web Admin)

- **FR-ADM-01 (M):** Admin quản lý Member: xem, tìm kiếm, khóa/mở khóa, xem gói tập và lịch sử.
- **FR-ADM-02 (M):** Admin quản lý Trainer: tạo tài khoản, cập nhật hồ sơ, khóa tài khoản.
- **FR-ADM-03 (M):** Admin phân công Trainer cho Member.
- **FR-ADM-04 (S):** Admin duyệt yêu cầu bảo lưu gói tập.
- **FR-ADM-05 (S):** Admin quản lý nội dung landing page (banner, giới thiệu).
- **FR-ADM-06 (S):** Admin quản lý mã giảm giá / khuyến mãi.
- **FR-ADM-07 (C):** Admin xem nhật ký thao tác hệ thống (audit log).

### 2.13. RPT – Báo cáo & Thống kê

- **FR-RPT-01 (M):** Admin xem doanh thu theo ngày/tháng/năm, theo gói tập.
- **FR-RPT-02 (M):** Admin xem số lượng hội viên mới, đang hoạt động, hết hạn.
- **FR-RPT-03 (S):** Admin xem lưu lượng check-in theo khung giờ (giờ cao điểm).
- **FR-RPT-04 (S):** Admin xem thống kê hiệu suất Trainer (số buổi, số Member).
- **FR-RPT-05 (C):** Admin xuất báo cáo ra Excel/PDF.

### 2.14. BOT – Chatbot tư vấn (Phạm vi mở rộng)

- **FR-BOT-01 (C):** Guest/Member hỏi đáp thông tin phòng gym (giờ mở cửa, gói tập, giá).
- **FR-BOT-02 (C):** Chatbot trả lời câu hỏi cơ bản về tập luyện, dinh dưỡng.
- **FR-BOT-03 (C):** Chatbot chuyển tiếp sang PT/Admin khi không trả lời được.

---

## 3. Ma trận Tác nhân – Module

| Module | Guest | Member | Trainer | Admin | AI Service |
|--------|:-----:|:------:|:-------:|:-----:|:----------:|
| AUTH | ✓ | ✓ | ✓ | ✓ | |
| LAND | ✓ | | | ✓ | |
| PKG | | ✓ | | ✓ | |
| PAY | | ✓ | | ✓ | |
| CHK | | ✓ | | ✓ | |
| BODY | | ✓ | ✓ | | ✓ |
| PLAN | | ✓ | ✓ | | ✓ |
| APPT | | ✓ | ✓ | | |
| EXE | | ✓ | ✓ | ✓ | |
| CHAT | | ✓ | ✓ | ✓ | |
| NOTI | | ✓ | ✓ | ✓ | |
| ADM | | | | ✓ | |
| RPT | | | | ✓ | |
| BOT | ✓ | ✓ | | | ✓ |

---

## 4. Thực thể nghiệp vụ chính (tham chiếu cho ERD / Class Diagram)

`User`, `Member`, `Trainer`, `Admin`, `MembershipPackage`, `Membership`, `Payment`, `CheckIn`, `FaceData`, `BodyMetric`, `WorkoutPlan`, `MealPlan`, `Exercise`, `TrainerSchedule`, `Appointment`, `Conversation`, `Message`, `Notification`, `Promotion`

---

## 5. Vấn đề cần chốt

- Có tách vai trò **Lễ tân (Receptionist)** khỏi Admin không (check-in thủ công, thu tiền tại quầy)?
- Module **CHAT** đưa vào phạm vi chính hay để mở rộng?
- **Face Recognition** chạy tại thiết bị quầy (edge) hay gửi ảnh về server?
- Kế hoạch do AI gợi ý có **bắt buộc** PT duyệt trước khi áp dụng không?
