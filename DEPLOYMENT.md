# Thông tin Triển khai 

## URL Công khai (Public URL)
https://day12-trantronggiang-production.up.railway.app

## Nền tảng
Railway

## Các lệnh Kiểm thử (Test Commands)

### Kiểm tra tính khả dụng (Health Check)
```bash
curl https://day12-trantronggiang-production.up.railway.app/health
# Kết quả mong đợi: {"status": "ok", ...}
```

### Kiểm tra API Agent (Yêu cầu xác thực)
```bash
curl -X POST https://day12-trantronggiang-production.up.railway.app/ask \
  -H "X-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"question": "Xin chào, hôm nay bạn thế nào?"}'
```

## Các Biến Môi Trường Đã Thiết Lập
- `PORT` (Được Railway tự động điều phối cổng)
- `REDIS_URL` (Sử dụng liên kết tới dịch vụ external Redis trên Railway)
- `AGENT_API_KEY` (Khóa dùng để xác thực request bên ngoài truyền vào Hệ Thống)
- `OPENAI_API_KEY` (Khóa API OpenAI để logic AI Agent xử trí)
- `ENVIRONMENT` (Bật flag "production")

## Ảnh chụp màn hình đính kèm
- [Bảng điều khiển Deployment](screenshots/dashboard.png)
- [Service đang chạy](screenshots/running.png)
- [Kết quả API test](screenshots/test.png)
