---
name: 07-design-system
description: Quy chuẩn về hệ thống thiết kế, a11y, layout grid và đa ngôn ngữ.
---

# Design System, Accessibility (a11y) & i18n Baseline

Tài liệu này quy định các tiêu chuẩn chung về giao diện để đảm bảo tính tiếp cận, responsive và hỗ trợ đa ngôn ngữ.

## 1. Accessibility (a11y) Baseline
Mọi component dùng chung (như Button, Modal, Input) **PHẢI** đáp ứng các tiêu chuẩn sau:

- **Focus Ring:** Luôn có viền focus (focus ring) rõ ràng cho người dùng bàn phím. Không được dùng CSS `outline: none` để xóa viền focus.
  - Sử dụng Tailwind class: `focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-foreground`
- **ARIA Attributes:**
  - Nếu một Button chỉ chứa Icon (không có chữ), BẮT BUỘC phải có thuộc tính `aria-label`.
  - Nếu Icon chỉ mang tính trang trí, dùng `aria-hidden="true"`.
  - Mọi trường nhập liệu (Form field) phải được liên kết với thẻ `<label>` qua id, hoặc dùng `aria-label`.
  - Các vùng cập nhật động (như thông báo lỗi, loading) cần có `aria-live="polite"` hoặc `aria-busy="true"`.
- **Điều hướng Bàn Phím (Keyboard Navigation):**
  - Mọi yếu tố tương tác phải focus được bằng phím `Tab`.
  - **Modal/Dialog:** Bắt buộc có cơ chế "Focus Trap" (nhốt focus trong modal khi mở), và trả lại focus cho nút trigger sau khi đóng.
  - **Dropdown/Menu:** Phải hỗ trợ phím `Escape` để đóng.

## 2. Responsive Grid & Layout
- Áp dụng nguyên tắc **Mobile-first**: Viết các class cơ bản cho màn hình điện thoại trước, sau đó dùng các breakpoint (`sm:`, `md:`, `lg:`) để ghi đè giao diện cho màn hình lớn.
- **Không hardcode `width` tĩnh bằng pixel** cho các thành phần layout lớn. Sử dụng hệ thống Grid/Flexbox của Tailwind.
- Wrap content bằng container chuẩn: `max-w-6xl mx-auto px-4 sm:px-6 lg:px-8`.

## 3. i18n Framework (Đa Ngôn Ngữ)
Dự án phải sẵn sàng cho việc mở rộng đa ngôn ngữ (Đặc biệt là hỗ trợ tốt Tiếng Việt ngay từ đầu).

- **Không hardcode chuỗi text hiển thị trong JSX**.
  ```tsx
  // ❌ Sai
  <button>Đăng nhập</button>
  
  // ✅ Đúng
  <button>{t("auth.login")}</button>
  ```
- Tổ chức file dịch thuật trong thư mục `src/locales/` (vd: `vi.json`, `en.json`).
- Format Dữ Liệu: 
  - Không tự format ngày tháng, số, hoặc tiền tệ bằng tay.
  - Sử dụng các API chuẩn của trình duyệt: `Intl.DateTimeFormat` và `Intl.NumberFormat` kèm theo locale hiện tại để format chính xác.
- Font chữ: Đảm bảo sử dụng font chữ hỗ trợ đầy đủ bộ Unicode Tiếng Việt (ví dụ: Inter, Roboto, Be Vietnam Pro).
