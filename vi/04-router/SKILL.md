---
name: Routing & Navigation (Next.js)
description: Hướng dẫn quy tắc định tuyến, chuyển trang và cấu trúc file trong Next.js App Router.
---

# Routing & Navigation with Next.js App Router

Tài liệu này định nghĩa các quy tắc khi làm việc với hệ thống Router của Next.js.

## 1. Quản lý Route Name
- Toàn bộ các chuỗi định tuyến (URL paths) **BẮT BUỘC** phải được khai báo tập trung tại file `src/constants/routes.ts`.
- **Tuyệt đối không hardcode chuỗi URL trực tiếp trong Component.**
  ```typescript
  // src/constants/routes.ts
  export const ROUTES = {
    HOME: "/",
    LOGIN: "/login",
    PROFILE: (id: string) => `/profile/${id}`,
  };

  // ❌ Sai
  <Link href="/login">Đăng nhập</Link>
  
  // ✅ Đúng
  <Link href={ROUTES.LOGIN}>Đăng nhập</Link>
  ```

## 2. Cấu trúc thư mục App Router (`app/`)
Tuân thủ nghiêm ngặt các quy ước file đặc biệt của Next.js:
- `page.tsx`: Giao diện chính của route (Server Component mặc định).
- `layout.tsx`: Layout bọc ngoài các trang con (Dùng chung Navbar, Sidebar).
- `loading.tsx`: Hiển thị Skeleton UI trong lúc chờ lấy dữ liệu.
- `error.tsx`: Phải tích hợp `AppError` kèm nút thử lại. Chú ý file này phải có `"use client"`.
- `not-found.tsx`: Hiển thị khi truy cập sai URL hoặc gọi hàm `notFound()`.

## 3. Bảo vệ Route (Protected Routes)
- Việc kiểm tra xác thực (User đã đăng nhập chưa) để chặn truy cập vào các trang nội bộ **phải được thực hiện ở tầng Server qua `middleware.ts`**.
- Không thực hiện việc check Auth và Redirect lắt nhắt bên trong từng Component Client (sẽ gây giật nháy màn hình).

## 4. Chuyển trang (Navigation)
- Dùng `<Link>`: Cho các thao tác chuyển trang thông thường từ UI (tốt cho SEO và prefetching).
- Dùng `router.push()` (từ `useRouter` của `next/navigation`): Sử dụng **sau khi hoàn thành một action** (ví dụ: Nút submit form, sau khi đăng nhập thành công).
- Dùng `redirect()` (từ `next/navigation`): Sử dụng khi cần chuyển hướng trực tiếp từ **Server Component** hoặc Route Handler/Server Action.

## 5. Trạng thái Loading
- Ưu tiên sử dụng `loading.tsx` với **Skeleton UI** phù hợp với bố cục trang.
- Tránh dùng spinner toàn trang (Full-page spinner) gây trải nghiệm người dùng kém (block UI).
