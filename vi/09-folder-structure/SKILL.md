---
name: 09-folder-structure
description: Cấu trúc thư mục tiêu chuẩn của dự án Next.js (my-nextjs-starter).
---
# Cấu Trúc Thư Mục (Folder Structure)

Dự án này áp dụng nguyên tắc Clean Architecture và Separation of Concerns để tách biệt rõ ràng giữa UI Logic và Pure Logic. Tất cả mã nguồn nằm trong thư mục `src/`.

## 1. Phân Rã Thư Mục Cơ Bản

- `src/app/`: **Next.js App Router.** Chứa cấu trúc định tuyến (routes), layout, page, loading, error.
- `src/components/`: **Giao diện (UI Components).**
  - `ui/`: Các component dùng chung (Button, Input) không chứa logic nghiệp vụ.
  - `errors/`: Các component xử lý lỗi màn hình (AppError, ErrorBoundary).
  - `[feature]/`: Các component đặc thù của từng tính năng.
- `src/config/`: **Cấu hình.** Các file cấu hình biến môi trường, thiết lập thư viện bên ngoài.
- `src/constants/`: **Hằng số.** Chứa các giá trị cố định, ví dụ `routes.ts` để định nghĩa URL. Tuyệt đối không hardcode string ở các nơi khác.
- `src/helpers/`: **Hàm tiện ích (Helpers/Utils).** Các hàm thuần túy (pure functions) xử lý logic nhỏ, ví dụ: format ngày tháng, chuỗi, storage.
- `src/lib/`: **Wrappers cho thư viện ngoài.** Nơi khởi tạo hoặc cấu hình các thư viện (ví dụ: utils cho Tailwind/clsx).
- `src/store/`: **Global State.** Quản lý trạng thái UI toàn cục bằng Zustand. (Không dùng cho Server State).

## 2. Service Layer (Kiến trúc API & Server State)

Được chia làm 2 tầng rõ rệt: Tầng thuần Logic (không có React) và Tầng UI (chứa React Hooks).

### Tầng 1: `src/services/` (Tầng Pure Logic - Không dùng React Hook)
Nơi đây nghiêm cấm gọi các hook của React.
- `axios/`: Nơi cấu hình Axios instance, interceptors, xử lý Refresh Token.
- `api/`: Các hàm gọi API (Fetcher) và Mapper (Anti-Corruption Layer). Mọi dữ liệu BE trả về phải được làm sạch ở đây.
- `query/`: Cấu hình của TanStack Query.
  - `queries/`: Chứa các hàm cấu hình trả về đối tượng `queryOptions`.
  - `mutations/`: Chứa các hàm cấu hình trả về đối tượng cài đặt cho mutation.
  - `query-keys/`: Nơi quản lý query keys tập trung (sử dụng thư viện query-key-factory).

### Tầng 2: `src/hooks/` (Tầng UI Logic - Kết nối React)
Chuyên dùng để gọi Hooks và xử lý tương tác trực tiếp với giao diện.
- `queries/`: Các Custom Hooks bọc `useQuery`, lấy cấu hình từ tầng `services/query/queries` để hiển thị data ra UI.
- `mutations/`: Các Custom Hooks bọc `useMutation`, lấy cấu hình từ `services/query/mutations`. Nơi đây xử lý các Side-effects như (Bắn Toast báo lỗi/thành công, Redirect chuyển trang...).
- Chứa các custom hooks chung khác của React (useWindowSize, useDebounce, useAuth...).
