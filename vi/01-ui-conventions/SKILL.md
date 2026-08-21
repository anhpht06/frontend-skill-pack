---
name: 01-ui-conventions
description: Hướng dẫn quy tắc viết và tổ chức UI Component cho dự án React/Next.js.
---

# Frontend UI Conventions (React / Next.js)

Tài liệu này định nghĩa các quy tắc cốt lõi khi xây dựng và tổ chức UI Component trong dự án. AI Agent và Developer PHẢI tuân thủ các quy tắc này.

## 1. Quy tắc đặt tên
- **Component**: Luôn sử dụng `PascalCase` (vd: `Button.tsx`, `UserProfile.tsx`).
- **File & Thư mục**: Luôn sử dụng `kebab-case` (vd: `user-profile.tsx`, `components/ui/`).
- **Hook**: Luôn sử dụng `camelCase` và bắt đầu bằng chữ `use` (vd: `useAuth`, `useWindowSize`).

## 2. Server Component vs Client Component
- Mặc định, mọi component trong Next.js App Router là **Server Component**.
- **Chỉ thêm `"use client"`** ở đầu file khi component cần:
  - Tương tác với người dùng (onClick, onChange...).
  - Sử dụng React hooks (useState, useEffect, useContext...).
  - Sử dụng các API của trình duyệt (window, document...).
- Giữ các component ở dạng Server Component nhiều nhất có thể để tối ưu hiệu suất.

## 3. Styling & Tailwind CSS
- Sử dụng **Tailwind CSS** cho toàn bộ styling.
- **KHÔNG nối chuỗi className thủ công**. Luôn sử dụng thư viện `clsx` kết hợp với `tailwind-merge` (thường được wrap trong hàm tiện ích `cn()`) để xử lý className có điều kiện và tránh xung đột class.
  ```tsx
  // ❌ Sai
  <div className={`btn ${isActive ? 'bg-blue-500' : 'bg-gray-500'} ${className}`}>

  // ✅ Đúng
  import { cn } from "@/lib/utils";
  <div className={cn("btn", isActive ? "bg-blue-500" : "bg-gray-500", className)}>
  ```

## 4. Cấu trúc thư mục Component
- `components/ui/`: Chứa các component cơ bản, dùng chung toàn hệ thống, không chứa logic nghiệp vụ (vd: Button, Input, Modal). Thường được tạo ra từ thư viện như shadcn/ui.
- `components/errors/`: Chứa các component hiển thị lỗi (vd: AppError, NotFound).
- `components/[feature]/`: Chứa các component cụ thể cho một tính năng, có thể chứa logic nghiệp vụ (vd: `components/auth/LoginForm.tsx`).

## 5. Tách biệt UI và Logic
- **KHÔNG viết logic phức tạp trực tiếp bên trong JSX.**
- Nếu một component có quá nhiều logic xử lý state, tính toán, hoặc gọi API, hãy tách phần logic đó ra một **Custom Hook** riêng biệt. Component chỉ nên chịu trách nhiệm hiển thị (View).

## 6. Xử lý Lỗi (Error Boundary)
- Khi xảy ra lỗi trong component, **TUYỆT ĐỐI KHÔNG để màn hình trắng**.
- Luôn sử dụng component `AppError` (hoặc tương đương) để hiển thị thông báo lỗi thân thiện cho người dùng, kèm theo nút "Thử lại".
- Tích hợp `AppError` với các file `error.tsx` của Next.js.

## 7. Sử dụng Icon (Lucide React)
- Sử dụng thư viện `lucide-react` cho các icon.
- **Chỉ import cụ thể từng icon**, không import toàn bộ thư viện để tối ưu bundle size.
  ```tsx
  // ❌ Sai
  import * as Icons from "lucide-react";
  
  // ✅ Đúng
  import { X, Check } from "lucide-react";
  ```
