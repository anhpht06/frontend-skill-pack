---
name: 05-tanstack-query
description: Kiến trúc 2 tầng quản lý API Data Fetching với TanStack Query v5.
---

# Data Fetching & Server State with TanStack Query v5

Tài liệu này quy định cách thức gọi API và lưu trữ dữ liệu Server State bằng TanStack Query. Chúng ta sử dụng **Kiến trúc 2 tầng** để tách biệt hoàn toàn Logic và UI.

## 1. Tầng Pure Logic (`src/services/query/`)
Tầng này CHỈ chứa cấu hình, tuyệt đối không được gọi React Hook (không import `useQuery` hay `useMutation` ở đây).

- **`query-keys/`**: Quản lý chìa khóa (keys).
  - Bắt buộc dùng thư viện `@lukemorales/query-key-factory` để tạo key.
  - Không viết string key thủ công rải rác.
- **`queries/`**: Cấu hình đọc dữ liệu.
  - Định nghĩa các hàm trả về `queryOptions`.
  - Quy tắc hàm Fetcher: **Luôn viết Arrow Function** `queryFn: () => userApi.getMe()` để giữ Context an toàn, không viết `queryFn: userApi.getMe`.
- **`mutations/`**: Cấu hình ghi dữ liệu.
  - Định nghĩa các hàm trả về đối tượng cấu hình cho mutation (chứa `mutationFn`).

## 2. Tầng UI Logic (`src/hooks/`)
Tầng này chuyên dùng để gọi React Hooks và kết nối với UI.

- **`hooks/queries/`**:
  - Tạo các Custom Hook (vd: `useUserQuery`) bọc lấy `useQuery`.
  - Hook này sẽ import object cấu hình từ tầng Pure Logic phía trên.
  - Không gọi API trực tiếp trong component. Mọi component muốn lấy dữ liệu đều phải gọi qua custom hook.
- **`hooks/mutations/`**:
  - Tạo Custom Hook (vd: `useLoginMutation`) bọc `useMutation`.
  - Đây là nơi xử lý các side effects cho UI như: Bắn Toast thông báo (`onSuccess`, `onError`), điều hướng trang (`router.push`), hoặc đóng Modal.

## 3. Quy Tắc Cấu Hình Cache (`staleTime`)
Việc cài đặt `staleTime` (thời gian dữ liệu bị coi là cũ) là vô cùng quan trọng:
- Dữ liệu hiếm khi thay đổi (User profile, settings): `5 - 10 phút`.
- Dữ liệu thay đổi thường xuyên (Bảng tin, thông báo): `30 giây - 1 phút`.
- Dữ liệu Real-time (Chat, chứng khoán): `0` (nên kết hợp với WebSocket).

## 4. Optimistic Update (Cập nhật lạc quan)
- Sử dụng pattern này cho các thao tác như "Thả tim", "Like", "Check Todo" để giao diện phản hồi ngay lập tức mà không cần chờ API trả về.
- Xử lý trong hàm `onMutate` của Custom Mutation Hook: cập nhật UI ngay lập tức, và revert lại data cũ ở `onError` nếu API gọi thất bại.
