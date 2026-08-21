---
name: 06-form-validation
description: Quản lý Form và Validation bằng React Hook Form và Zod.
---

# Form Management & Validation

Tài liệu này quy định cách xử lý form và kiểm tra tính hợp lệ của dữ liệu đầu vào.

## 1. Công Cụ Tiêu Chuẩn
- Bắt buộc sử dụng `react-hook-form` cho mọi form có từ 2 field (trường dữ liệu) trở lên.
- Sử dụng `zod` (hoặc `yup`) để khai báo Schema Validation.
- Sử dụng `@hookform/resolvers/zod` để kết nối Schema với Form.

## 2. Tổ chức Schema (`src/schemas/`)
- Mọi schema validation phải được tách riêng vào thư mục `src/schemas/`, **tuyệt đối không viết schema trực tiếp bên trong UI component**.
- Điều này giúp tái sử dụng schema (ví dụ: dùng chung schema cho Frontend và Backend) và giữ component gọn gàng.
  ```typescript
  // src/schemas/auth.schema.ts
  import { z } from "zod";

  export const loginSchema = z.object({
    email: z.string().min(1, "Email không được để trống").email("Email không hợp lệ"),
    password: z.string().min(6, "Mật khẩu tối thiểu 6 ký tự"),
  });

  // Tự động suy luận Type từ Schema
  export type LoginFormValues = z.infer<typeof loginSchema>;
  ```

## 3. Quy Tắc Gọi API khi Submit
- Trong hàm `onSubmit`, **KHÔNG gọi trực tiếp hàm API**.
- Luôn luôn gọi hàm `mutate` (hoặc `mutateAsync`) từ Custom Hook của TanStack Query đã khai báo ở tầng UI Logic.
  ```typescript
  // ❌ Sai: Gọi trực tiếp API
  const onSubmit = async (data: LoginFormValues) => {
    await userApi.login(data);
  }

  // ✅ Đúng: Gọi qua Mutation Hook
  const loginMutation = useLoginMutation();
  const onSubmit = (data: LoginFormValues) => {
    loginMutation.mutate(data);
  }
  ```

## 4. Xử Lý Lỗi (Error Messages)
- Lấy chuỗi báo lỗi từ `errors` object của `react-hook-form` (vd: `errors.email?.message`).
- **Không hardcode chuỗi báo lỗi trực tiếp trong JSX**. Chuỗi lỗi phải được định nghĩa từ bên trong Zod Schema.

## 5. Sử Dụng Controller Cho Component Phức Tạp
- Đối với các input HTML gốc (`<input>`, `<select>`), có thể dùng hàm `register(...)`.
- Đối với các Custom Component (ví dụ: UI từ thư viện thứ 3, DatePicker, Select nâng cao), **BẮT BUỘC dùng `<Controller />`** hoặc `useController` để đảm bảo dữ liệu được đồng bộ chính xác với form state.
- **Không dùng `any`** khi định nghĩa các props cho Form Component.
