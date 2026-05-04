# Service Boundary của nhóm

## 1. Thông tin nhóm

- Tên nhóm: 1A 
- Lớp:CNTT 17-12
- Thành viên: Phạm Thế Hoán, Trịnh Công Hiệp, Nguyễn Văn Hoàn, Nguyễn Đức Triệu
- Service nhóm phụ trách: IoT Ingestion
- Sản phẩm tổng thể của lớp: Hệ thống Trường học thông minh / Hệ thống thông minh (quản lý thiết bị IoT, camera, AI, phân tích,...)

## 2. Actor

Ai tương tác với hệ thống/service?
- Thiết bị IoT (cảm biến nhiệt độ, độ ẩm, GPS, vv)
- Gateway (thiết bị gửi dữ liệu trung gian)
- Khác hệ thống (Core Business, Analytics)
- DevOps / Admin (hệ thống giám sát)

## 3. System Boundary

Nhóm em xây phần nào?
- Xây dựng dịch vụ tiếp nhận dữ liệu từ thiết bị IoT và chuyển tiếp vào hệ thống.

Phần nhóm kiểm soát:

- API nhận dữ liệu từ IoT (HTTP/MQTT)
- Xác thực đầu vào dữ liệu
- Xử lý sơ bộ (định dạng, dấu thời gian, deviceId)
- Ghi dữ liệu vào cơ sở dữ liệu/hàng đợi tin nhắn
- Ghi nhật ký và giám sát

Phần nhóm chỉ tích hợp:
- Cơ sở dữ liệu (MongoDB, PostgreSQL,…)
- Hệ thống môi giới tin nhắn (Kafka, RabbitMQ,…)
- Service Analytics (xử lý dữ liệu)
- Core Business (nghiệp vụ chính)

## 4. Service Boundary

Service của nhóm có trách nhiệm gì?
- Nhận dữ liệu từ thiết bị IoT
- Kiểm tra hợp lệ dữ liệu
- Dữ liệu chuẩn
- Lưu trữ hoặc chuyển tiếp dữ liệu
- Đảm bảo hệ thống chịu tải cao (có thể mở rộng)

Service KHÔNG làm gì?
- Không phân tích dữ liệu (đó là công việc của Analytics)
- Không xử lý logic nghiệp vụ (Core Business doing)
- Không gửi cảnh báo (Dịch vụ thông báo được thực hiện)
- Không xử lý được hình ảnh/video (AI Vision doing)

## 5. Input / Output

### Input

- Dữ liệu từ thiết bị IoT (JSON):

{
  "deviceId": "sensor_01",
  "timestamp": "2026-05-04T10:00:00Z",
  "temperature": 30.5,
  "humidity": 70
}

- Giao thức hỗ trợ:

HTTP (REST API)
MQTT

### Output

- Lưu dữ liệu vào database
- Gửi dữ liệu qua message queue
- Phản hồi cho thiết bị:
{
  "status": "success",
  "message": "Data received"
}

## 6. API dự kiến

## 6. API dự kiến

| Method | Endpoint | Mục đích |
|---|---|---|
| GET | /health | Kiểm tra trạng thái hoạt động của service |
| POST | /ingest | Tiếp nhận dữ liệu từ thiết bị IoT |
| GET | /devices | Lấy danh sách thiết bị đã gửi dữ liệu |
| GET | /data/{deviceId} | Lấy dữ liệu theo từng thiết bị |

## 7. Phụ thuộc service khác

Service này gọi đến service nào?
- Cơ sở dữ liệu (ghi chú dữ liệu)
- Hệ thống môi giới tin nhắn (Kafka/RabbitMQ)
- Dịch vụ Analytics (gửi dữ liệu)

Service nào gọi đến service này?
- Thiết bị IoT / Cổng kết nối
- Camera Stream (nếu có cảm biến liên quan)
- Hoạt động kinh doanh cốt lõi
## 8. Sơ đồ minh họa

Có thể vẽ bằng Mermaid, draw.io, Ludichart hoặc ảnh chụp sơ đồ.

```mermaid
flowchart LR
    Device[IoT Device] --> API[IoT Ingestion Service]
    API --> DB[(Database)]
    API --> MQ[Message Queue]
    MQ --> Analytics[Analytics Service]
    Analytics --> Core[Core Business]
