---
name: 03-service-and-auth
description: Kiến trúc 3 tầng cho API, Anti-Corruption Layer, và quản lý Auth Token nâng cao.
---

# Service Layer, Anti-Corruption Layer & Auth Token Management

Tài liệu này quy định kiến trúc xử lý API và quản lý Token xác thực.

## 1. Kiến Trúc 3 Tầng Service

### Tầng 1 — Axios Instance (`src/services/axios/`)
- Mọi cấu hình chung (`baseURL`, `timeout`, `withCredentials`) phải đặt ở đây.
- **Bắt buộc dùng `axiosInstance`** cho mọi request. Nghiêm cấm dùng trực tiếp `axios.get()` hay `fetch()`.
- Xử lý Interceptor 401: Khi token hết hạn, request phải được đưa vào **Failed Queue** để chờ refresh token, sau đó tự động retry.

### Tầng 2 — API Fetcher & Anti-Corruption Layer (`src/services/api/*.api.ts`)
- Mỗi domain nghiệp vụ có 1 file riêng (vd: `user.api.ts`, `product.api.ts`).
- **Mapper Function (Trạm kiểm lâm):** Mọi dữ liệu từ Backend trả về phải đi qua một hàm Mapper để làm sạch trước khi ném cho UI hoặc TanStack Query.
- **Không dùng `any`**. Sử dụng `unknown` và Safe Casting (`Record<string, unknown>`).
- **Bảo vệ mảng (Anti-Corruption):** Nếu BE trả về null/chuỗi cho một trường đáng lý là mảng, ép kiểu về mảng rỗng để tránh lỗi `list.map is not a function`.
  ```typescript
  list: Array.isArray(data.list) ? data.list : []
  ```

### Tầng 3 — Logger (`src/services/logger/`)
- Bắt lỗi không quăng exception (handled errors) phải được log qua `LoggerService.captureError(...)`. Không gọi trực tiếp SDK của Sentry.

---

## 2. Quản Lý Auth Token (Advanced)

Đây là quy tắc thiết kế hệ thống có JWT + Refresh Token.

### Token Holder Pattern
- Token JWT được giữ và quản lý tập trung trong một class/module (ví dụ `TokenManager`). Các UI component không tự quản lý token.
- **Proactive Refresh:** Tự động gọi API refresh token ngầm khi thời gian sống (TTL) của token chỉ còn ~60 giây.
- Khi nhận lỗi `401 TOKEN_INVALID` (do expired bất ngờ), tự động kích hoạt callback lấy token mới và thực hiện retry.

### Operation Lease (Bảo vệ Thao Tác Dài)
- Các thao tác dài như Upload file lớn, Batch job phải xin một `op_id` (Operation Lease).
- Trong lúc thao tác dài đang chạy, nếu có một tiến trình refresh token xảy ra song song, SDK **KHÔNG ĐƯỢC hủy** thao tác dài đó.
- Thao tác dài sẽ tiếp tục sử dụng token cũ (vẫn còn hạn) cho đến khi hoàn thành.

### Phân Biệt Lỗi 401
Hệ thống phải phân biệt rạch ròi 2 loại lỗi HTTP 401:
- `401 TOKEN_INVALID`: Token hết hạn → Thực hiện cơ chế refresh → Retry.
- `401 APP_REVOKED`: Tài khoản bị khóa, đổi mật khẩu, bị thu hồi quyền → **KHÔNG refresh**, ném Exception thẳng lên tầng UI để văng ra màn hình đăng nhập.

### Unit Test Bắt Buộc
Mọi module quản lý Token phải thỏa mãn các test case sau:
1. Token sắp hết hạn → tự động gọi refresh callback TRƯỚC KHI gửi request tiếp theo.
2. Nhận `401 TOKEN_INVALID` giữa một upload dài có Operation Lease → không hủy upload, thực hiện refresh token trong nền.
3. Nhận `401 APP_REVOKED` → không retry, ném exception rõ ràng.
