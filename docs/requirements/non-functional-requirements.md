# Đặc tả Yêu cầu Phi chức năng (Non-functional Requirements)

**Đề tài:** Xây dựng hệ thống quản lý phòng gym
**Phiên bản:** 1.0 — Bản nháp
**Tài liệu liên quan:** `functional-requirements.md`

---

## 1. Tổng quan

### 1.1. Cơ sở phân loại

Các yêu cầu được phân nhóm theo mô hình chất lượng sản phẩm **ISO/IEC 25010**, bổ sung nhóm Pháp lý và Vận hành.

### 1.2. Quy ước

- Mã yêu cầu: `NFR-<Nhóm>-<STT>` (VD: `NFR-PERF-01`)
- Độ ưu tiên (MoSCoW): **M** = Must, **S** = Should, **C** = Could
- Mỗi yêu cầu có tiêu chí đo lường (metric) để kiểm thử được
- Cột "Liên quan" tham chiếu mã module trong `functional-requirements.md`

### 1.3. Danh sách nhóm

| Mã | Nhóm | Đặc tính ISO/IEC 25010 |
|----|------|------------------------|
| PERF | Hiệu năng | Performance Efficiency |
| SCAL | Khả năng mở rộng | Performance Efficiency (Capacity) |
| AVAIL | Tính sẵn sàng & Tin cậy | Reliability |
| SEC | Bảo mật | Security |
| USAB | Khả năng sử dụng | Usability |
| COMP | Tương thích | Compatibility |
| MAINT | Khả năng bảo trì | Maintainability |
| PORT | Khả năng triển khai | Portability |
| AI | Chất lượng thành phần AI | — (đặc thù hệ thống) |
| LEGAL | Pháp lý & Quyền riêng tư | — |
| OPS | Vận hành & Giám sát | — |

---

## 2. Yêu cầu phi chức năng chi tiết

### 2.1. PERF – Hiệu năng

- **NFR-PERF-01 (M):** API nghiệp vụ thông thường có thời gian phản hồi ≤ 500 ms (p95).
- **NFR-PERF-02 (M):** Xác thực check-in bằng QR hoàn tất trong ≤ 1 giây, tính từ lúc quét đến lúc hiển thị kết quả. *(CHK)*
- **NFR-PERF-03 (S):** Nhận diện khuôn mặt hoàn tất trong ≤ 2 giây. *(CHK)*
- **NFR-PERF-04 (S):** Trích xuất chỉ số từ ảnh InBody hoàn tất trong ≤ 15 giây, xử lý bất đồng bộ và hiển thị trạng thái tiến trình. *(BODY)*
- **NFR-PERF-05 (S):** Sinh gợi ý `WorkoutPlan` / `MealPlan` hoàn tất trong ≤ 30 giây, xử lý bất đồng bộ. *(PLAN)*
- **NFR-PERF-06 (M):** Landing page đạt LCP ≤ 2,5 giây và Lighthouse Performance ≥ 80. *(LAND)*
- **NFR-PERF-07 (S):** Tin nhắn chat được chuyển đến người nhận trong ≤ 1 giây khi cả hai đang online. *(CHAT)*
- **NFR-PERF-08 (M):** Truy vấn danh sách có phân trang, tối đa 50 bản ghi mỗi trang.

### 2.2. SCAL – Khả năng mở rộng

- **NFR-SCAL-01 (M):** Hệ thống phục vụ tối thiểu 200 người dùng đồng thời mà không vi phạm NFR-PERF-01.
- **NFR-SCAL-02 (M):** Hệ thống chịu được 100 lượt check-in / 10 phút vào giờ cao điểm.
- **NFR-SCAL-03 (S):** Các service backend là stateless để có thể scale ngang (horizontal scaling).
- **NFR-SCAL-04 (C):** Thiết kế dữ liệu cho phép mở rộng sang mô hình nhiều chi nhánh (multi-branch).

### 2.3. AVAIL – Tính sẵn sàng & Tin cậy

- **NFR-AVAIL-01 (M):** Uptime ≥ 99% trong giờ hoạt động của phòng gym.
- **NFR-AVAIL-02 (M):** Khi AI Service lỗi, các chức năng lõi (đăng nhập, check-in, thanh toán) vẫn hoạt động bình thường.
- **NFR-AVAIL-03 (S):** Khi Face Recognition lỗi, hệ thống chuyển sang QR hoặc check-in thủ công (fallback). *(CHK)*
- **NFR-AVAIL-04 (M):** Xử lý IPN thanh toán có tính idempotent: callback lặp lại không tạo `Payment` trùng. *(PAY)*
- **NFR-AVAIL-05 (M):** Các giao dịch liên quan `Payment` và `Membership` đảm bảo tính toàn vẹn (ACID).
- **NFR-AVAIL-06 (M):** Cơ sở dữ liệu được sao lưu tự động hằng ngày và lưu trữ tối thiểu 7 ngày.
- **NFR-AVAIL-07 (S):** RPO ≤ 24 giờ, RTO ≤ 4 giờ.

### 2.4. SEC – Bảo mật

- **NFR-SEC-01 (M):** Mật khẩu được băm bằng bcrypt (cost ≥ 10) hoặc Argon2; không lưu dạng rõ.
- **NFR-SEC-02 (M):** Xác thực bằng JWT: access token hết hạn ≤ 15 phút, refresh token ≤ 7 ngày, có cơ chế thu hồi (revoke). *(AUTH)*
- **NFR-SEC-03 (M):** Phân quyền RBAC được kiểm tra ở backend với mọi endpoint, không chỉ ẩn trên giao diện.
- **NFR-SEC-04 (M):** Trainer chỉ truy cập được dữ liệu của Member mình phụ trách. *(BODY, PLAN)*
- **NFR-SEC-05 (M):** Mọi kết nối sử dụng HTTPS/TLS 1.2 trở lên; WebSocket sử dụng WSS.
- **NFR-SEC-06 (M):** Mã QR check-in là mã động, hết hạn sau ≤ 60 giây, có chữ ký số để chống giả mạo và phát lại (replay). *(CHK)*
- **NFR-SEC-07 (S):** Face check-in có cơ chế chống giả mạo (liveness detection) để chặn ảnh chụp hoặc video. *(CHK)*
- **NFR-SEC-08 (M):** Dữ liệu sinh trắc (`FaceData`) chỉ lưu dạng vector đặc trưng (embedding) đã mã hóa, không lưu ảnh gốc.
- **NFR-SEC-09 (M):** Hệ thống chống OWASP Top 10: SQL Injection, XSS, CSRF, IDOR.
- **NFR-SEC-10 (M):** Có rate limiting: đăng nhập tối đa 5 lần sai / 15 phút; gửi OTP tối đa 3 lần / 10 phút.
- **NFR-SEC-11 (M):** File upload (ảnh InBody, ảnh chat) được kiểm tra định dạng, giới hạn dung lượng ≤ 10 MB và lưu tại object storage.
- **NFR-SEC-12 (M):** Không lưu thông tin thẻ thanh toán; toàn bộ xử lý thẻ thực hiện qua cổng thanh toán. *(PAY)*
- **NFR-SEC-13 (M):** Chữ ký IPN từ cổng thanh toán được xác thực (checksum/HMAC) trước khi xử lý. *(PAY)*
- **NFR-SEC-14 (S):** Ghi audit log cho các thao tác nhạy cảm: khóa tài khoản, hoàn tiền, sửa gói tập. *(ADM)*

### 2.5. USAB – Khả năng sử dụng

- **NFR-USAB-01 (M):** Giao diện hỗ trợ tiếng Việt; thiết kế sẵn khả năng đa ngôn ngữ (i18n).
- **NFR-USAB-02 (M):** Member hoàn thành đăng ký gói tập và thanh toán trong ≤ 5 bước thao tác. *(PKG, PAY)*
- **NFR-USAB-03 (M):** Web Admin và Landing page hiển thị tốt trên màn hình từ 360 px đến 1920 px (responsive).
- **NFR-USAB-04 (S):** Thông báo lỗi rõ ràng, bằng ngôn ngữ người dùng, không hiển thị lỗi kỹ thuật.
- **NFR-USAB-05 (S):** Kích thước vùng chạm tối thiểu 44×44 px; độ tương phản màu đạt chuẩn WCAG 2.1 mức AA.
- **NFR-USAB-06 (S):** Ứng dụng mobile hỗ trợ chế độ Dark mode.
- **NFR-USAB-07 (C):** Dữ liệu đã tải (kế hoạch tập, thư viện bài tập) xem được khi mất kết nối (offline cache). *(PLAN, EXE)*

### 2.6. COMP – Tương thích

- **NFR-COMP-01 (M):** Mobile App hỗ trợ Android 10+ và iOS 15+.
- **NFR-COMP-02 (M):** Web hỗ trợ 2 phiên bản gần nhất của Chrome, Edge, Firefox và Safari.
- **NFR-COMP-03 (M):** Tích hợp cổng thanh toán VNPay/MoMo theo tài liệu chính thức (môi trường sandbox cho đồ án).
- **NFR-COMP-04 (S):** Push notification qua Firebase Cloud Messaging (FCM). *(NOTI)*
- **NFR-COMP-05 (M):** API tuân theo chuẩn RESTful, định dạng JSON, có versioning (`/api/v1/...`).

### 2.7. MAINT – Khả năng bảo trì

- **NFR-MAINT-01 (M):** Kiến trúc phân tầng rõ ràng (Controller – Service – Repository), tách biệt theo module nghiệp vụ.
- **NFR-MAINT-02 (M):** Tài liệu API tự động sinh bằng OpenAPI/Swagger.
- **NFR-MAINT-03 (S):** Độ bao phủ unit test ≥ 60% cho tầng Service.
- **NFR-MAINT-04 (M):** Mã nguồn quản lý bằng Git, tuân theo quy ước commit (Conventional Commits).
- **NFR-MAINT-05 (M):** Thay đổi schema CSDL quản lý bằng migration (Flyway/Liquibase).
- **NFR-MAINT-06 (S):** Thành phần AI tách thành service riêng, giao tiếp qua API, có thể thay đổi model mà không ảnh hưởng hệ thống lõi.
- **NFR-MAINT-07 (M):** Cấu hình (khóa API, chuỗi kết nối) tách khỏi mã nguồn qua biến môi trường.

### 2.8. PORT – Khả năng triển khai

- **NFR-PORT-01 (M):** Toàn bộ backend được đóng gói bằng Docker, khởi chạy bằng Docker Compose.
- **NFR-PORT-02 (S):** Có pipeline CI/CD (GitHub Actions) tự động build, test và deploy.
- **NFR-PORT-03 (S):** Tách biệt các môi trường `dev`, `staging` và `production`.

### 2.9. AI – Chất lượng thành phần AI

- **NFR-AI-01 (M):** Độ chính xác trích xuất chỉ số InBody ≥ 90% trên tập ảnh kiểm thử (ảnh rõ, đủ sáng). *(BODY)*
- **NFR-AI-02 (M):** Kết quả AI luôn cho phép người dùng xem lại và chỉnh sửa trước khi lưu (human-in-the-loop). *(BODY, PLAN)*
- **NFR-AI-03 (M):** Hệ thống từ chối ảnh không đạt chất lượng (mờ, sai loại phiếu) và yêu cầu chụp lại. *(BODY)*
- **NFR-AI-04 (M):** Kế hoạch gợi ý nằm trong ngưỡng an toàn (VD: calo không thấp hơn BMR) và kèm tuyên bố miễn trừ: "không thay thế tư vấn y tế". *(PLAN)*
- **NFR-AI-05 (S):** Face Recognition đạt FAR ≤ 1% và FRR ≤ 5% trên tập kiểm thử. *(CHK)*
- **NFR-AI-06 (S):** Chi phí gọi API AI được giới hạn theo người dùng (quota) để kiểm soát chi phí.

### 2.10. LEGAL – Pháp lý & Quyền riêng tư

- **NFR-LEGAL-01 (M):** Tuân thủ Nghị định 13/2023/NĐ-CP về bảo vệ dữ liệu cá nhân; dữ liệu sinh trắc và sức khỏe được xếp vào nhóm dữ liệu nhạy cảm.
- **NFR-LEGAL-02 (M):** Thu thập sự đồng ý rõ ràng (explicit consent) trước khi đăng ký khuôn mặt hoặc tải chỉ số cơ thể. *(AUTH, BODY)*
- **NFR-LEGAL-03 (M):** Member được xóa dữ liệu khuôn mặt và yêu cầu xóa tài khoản bất cứ lúc nào.
- **NFR-LEGAL-04 (S):** Có trang Điều khoản sử dụng và Chính sách quyền riêng tư.

### 2.11. OPS – Vận hành & Giám sát

- **NFR-OPS-01 (M):** Log tập trung, có định dạng thống nhất (JSON), gắn `traceId` cho mỗi request.
- **NFR-OPS-02 (S):** Có endpoint health check cho từng service.
- **NFR-OPS-03 (C):** Giám sát metric (CPU, RAM, độ trễ, tỷ lệ lỗi) bằng Prometheus + Grafana.
- **NFR-OPS-04 (S):** Tác vụ định kỳ (quét `Membership` hết hạn, gửi nhắc lịch) chạy qua scheduler và có log kết quả. *(PKG, NOTI)*

---

## 3. Truy vết NFR – Module chức năng

| Module | NFR liên quan chính |
|--------|---------------------|
| AUTH | SEC-01, SEC-02, SEC-10, LEGAL-02 |
| LAND | PERF-06, USAB-03 |
| PKG | USAB-02, AVAIL-05, OPS-04 |
| PAY | AVAIL-04, AVAIL-05, SEC-12, SEC-13, COMP-03 |
| CHK | PERF-02, PERF-03, SCAL-02, AVAIL-03, SEC-06, SEC-07, SEC-08, AI-05 |
| BODY | PERF-04, SEC-04, SEC-11, AI-01, AI-02, AI-03, LEGAL-02 |
| PLAN | PERF-05, SEC-04, AI-02, AI-04 |
| APPT | NFR-OPS-04 |
| CHAT | PERF-07, SEC-05, SEC-11 |
| NOTI | COMP-04, OPS-04 |
| ADM | SEC-03, SEC-14 |

---

## 4. Vấn đề cần chốt

- Các ngưỡng số (200 người dùng đồng thời, uptime 99%) giả định quy mô **1 phòng gym vừa**. Cần xác nhận lại quy mô mục tiêu.
- Face Recognition dùng **dịch vụ có sẵn** (AWS Rekognition, FPT.AI) hay **tự triển khai** (InsightFace)? Lựa chọn này quyết định NFR-SEC-07, NFR-SEC-08 và NFR-AI-05.
- Đọc phiếu InBody dùng **LLM đa phương thức** hay **OCR truyền thống + parser**? Lựa chọn này ảnh hưởng NFR-PERF-04, NFR-AI-01 và chi phí.
