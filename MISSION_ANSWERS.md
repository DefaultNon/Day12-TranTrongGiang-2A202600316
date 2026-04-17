# Bài tập Ngày 12 - Câu hỏi và Trả lời (Mission Answers)

## Phần 1: Localhost vs Production

### Bài tập 1.1: Các anti-patterns đã tìm thấy
1. **Hardcode thông tin nhạy cảm:** Các API keys và cấu hình nhạy cảm được gắn cứng trực tiếp vào mã nguồn thay vì sử dụng Biến môi trường (`Environment Variables`).
2. **Server Phát triển (Development Servers):** Sử dụng các server mặc định như `flask run` hoặc `uvicorn --reload`. Các server này hoạt động kém trên môi trường production và không có khả năng quản lý multi-worker.
3. **Lưu trạng thái trên bộ nhớ (In-Memory State):** Sử dụng biến global để lưu trạng thái (như bộ đếm rate limit hoặc chi phí). Điều này sẽ dẫn đến sai số khi ứng dụng mở rộng (scale) ra nhiều instances.
4. **Ghi log kém (Poor Logging):** Sử dụng lệnh `print()` cơ bản, thiếu cấu trúc JSON định dạng chuẩn và mốc thời gian, gây khó khăn cho việc truy vết sự cố. 
5. **Không có cơ chế phục hồi:** Thiếu xử lý graceful shutdown, dẫn đến nguy cơ rớt requests khi cập nhật hay khởi động lại server.

### Bài tập 1.3: Bảng so sánh
| Tính năng | Phát triển (Develop) | Thực tế (Production) | Tại sao lại quan trọng? |
|---------|---------|------------|----------------|
| **Cấu hình** | File `.env` tại máy / Hardcode | Biến môi trường bảo mật (12-Factor) | Tránh rò rỉ secret và dễ dàng đồng bộ cấu hình giữa các môi trường. |
| **Chạy Web** | `uvicorn --reload` (dev server) | `uvicorn` với nhiều workers / Gunicorn | Tối ưu hóa cho các request đồng thời; tự động phục hồi khi gặp sự cố (crash). |
| **Trạng thái** | Các biến In-memory | Cơ sở dữ liệu ngoài (Redis) | Cho phép auto-scaling lên nhiều instances; dữ liệu độc lập không bị reset khi restart. |
| **Logging**| `print()` ra màn hình console | Ghi log cấu trúc JSON / APM | Cho phép thu thập, giám sát và phân tích lỗi dễ dàng qua các hệ thống tập trung. |

## Phần 2: Docker

### Bài tập 2.1: Câu hỏi về Dockerfile
1. **Base image:** Sử dụng `python:3.11-slim` đảm bảo dung lượng tối giản, bảo mật cao và tải về nhanh chóng, chỉ chứa những thư viện hệ thống cơ bản nhất so với base `ubuntu` nặng nề.
2. **Thư mục làm việc (Working directory):** `/app` bao bọc toàn bộ mã nguồn một cách tách biệt với hệ thống host OS gốc, đảm bảo tính nhất quán trên nhiều môi trường.

### Bài tập 2.3: So sánh kích thước Image
- Develop: ~950 MB (ảnh `python:3.11` tiêu chuẩn chứa đầy đủ các công cụ build C++ dư thừa và cache của pip)
- Production: ~160 MB (sử dụng multi-stage với `python:3.11-slim`, loại bỏ bộ nhớ cache và các công cụ build không cần thiết)
- Sự khác biệt: Giảm được khoảng ~83% dung lượng.

## Phần 3: Triển khai Đám mây (Cloud Deployment)

### Bài tập 3.1: Triển khai trên Railway
- URL: `https://day12-trantronggiang-2a202600316-production.up.railway.app`
- Ảnh chụp màn hình: `[Đã lưu trong thư mục screenshots]`

## Phần 4: Bảo mật API

### Bài tập 4.1-4.3: Kết quả kiểm thử (Test results)
- Các truy cập trái phép (Unauthorized): Bị từ chối ngay lập tức với mã lỗi `401 Unauthorized`.
- Xác minh rành mạch: Trả về HTTP `200 OK` (JSON) khi call truyền vào đúng header `X-API-Key`.
- Vượt quá Rate Limit (Giới hạn truy cập): Đã chạy giả lập spam và xác nhận hệ thống sẽ chặn bằng mã lỗi `429 Too Many Requests`.

### Bài tập 4.4: Triển khai bảo vệ Chi phí (Cost guard)
Hệ thống theo dõi ngân sách (budget) được xây dựng xử lý qua Redis. Kích thước và số lượng Token gửi lên AI Agent được cập nhật theo mỗi request, tổng cộng chi phí được ghi đồng bộ một cách an toàn vào biến key `cost:YYYY-MM-DD` trên Redis (dùng lệnh nguyên tử `incrbyfloat` để chống lỗi). Hệ thống sẽ chặn lệnh gọi API với mã `503 Service Unavailable` ngay khi mức tiền đạt giới hạn ($10). Có tích hợp fallback về bộ đếm RAM in-memory nội bộ nếu như Redis tạm thời mất kết nối để luôn duy trì hoạt động cho app.

## Phần 5: Mở rộng & Độ tin cậy (Scaling & Reliability)

### Bài tập 5.1-5.5: Ghi chú lúc triển khai
Triển khai Redis song song giúp toàn vẹn hệ thống theo kiến trúc stateless. Các biến theo dõi global (rate limit, budget) đều xử lí đồng nhất qua cache tập trung. Đồng thời, cấu hình probe kiểm tra "health" và "readiness" giám sát chính xác lúc nào app sẵn sàng hoặc đã bị đứt kết nối để báo cáo trạng thái load balancing theo chuẩn xịn của Railway.
