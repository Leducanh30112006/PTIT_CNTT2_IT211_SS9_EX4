# Phân Tích Chiến Lược Thiết Lập Log Đa Môi Trường (Dev vs Prod)

### 1. Sự khác biệt về mục đích cấu hình giữa Dev và Prod

| Tiêu chí | Môi trường Dev (Development) | Môi trường Prod (Production) |
| :--- | :--- | :--- |
| **Mục đích chính** | Phục vụ lập trình viên theo dõi luồng xử lý, luồng dữ liệu chi tiết và debug lỗi nhanh chóng khi đang code. | Phục vụ vận hành (Ops), giám sát hệ thống dài hạn, kiểm toán (audit) và truy vết sự cố hệ thống thực tế. |
| **Vị trí lưu trữ** | **Console (ConsoleAppender):** Hiện trực tiếp trên Terminal/IDE. Tắt ứng dụng hoặc xóa console là mất log, không cần lưu trữ lâu dài. | **File cứng (RollingFileAppender):** Lưu trên đĩa để bền vững dữ liệu, hỗ trợ các công cụ thu thập log (như ELK Stack, Splunk, Grafana Loki). |
| **Mức độ chi tiết (Log Level)** | **DEBUG (hoặc TRACE):** Ghi nhận chi tiết từng câu lệnh SQL, tham số truyền vào, các bước trung gian của logic nghiệp vụ để dễ tìm lỗi. | **INFO (hoặc WARN/ERROR):** Chỉ ghi lại các sự kiện quan trọng (Khởi động hệ thống, giao dịch thành công, lỗi nghiêm trọng). Tránh làm ngập lụt hệ thống bởi log rác. |
| **Chi phí tài nguyên** | Thấp, do chạy trên máy cá nhân với lượng request rất nhỏ, không áp lực về I/O đĩa hay dung lượng. | Rất cao. Hệ thống chịu tải hàng triệu request, nếu không quản lý kích thước file log sẽ gây **đầy ổ cứng (Disk Full)**, làm sập Server. |

### 2. Ý nghĩa của cơ chế Rotation & Compression trên Production
* **Cắt file (Size-based):** Giới hạn mỗi file log ở mức 10MB giúp việc mở, đọc và phân tích file bằng các tool text editor thông thường không bị crash hoặc lag.
* **Nén file (.gz / .zip):** Tiết kiệm đến 80-90% dung lượng lưu trữ trên ổ cứng server đối với dữ liệu dạng văn bản (text).
* **Dọn dẹp tự động (Time-based / MaxHistory):** Giữ tối đa 30 ngày giúp tuân thủ chính sách lưu trữ dữ liệu, đồng thời tự động xóa các file log quá cũ để giải phóng tài nguyên mà không cần quản trị viên can thiệp thủ công.