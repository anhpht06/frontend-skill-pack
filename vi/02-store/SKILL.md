---
name: Global State Management (Zustand)
description: Hướng dẫn sử dụng Zustand cho việc quản lý trạng thái toàn cục (Global State).
---

# Global State Management with Zustand

Tài liệu này định nghĩa các quy tắc khi sử dụng Zustand để quản lý trạng thái.

## 1. Mục đích sử dụng (UI State vs Server State)
- **Zustand CHỈ ĐƯỢC DÙNG cho Global UI State** (trạng thái giao diện người dùng toàn cục). Ví dụ: `isSidebarOpen`, `themeMode`, `activeModal`.
- **TUYỆT ĐỐI KHÔNG dùng Zustand để lưu trữ Server State** (dữ liệu trả về từ API). Việc lưu trữ, caching và đồng bộ dữ liệu từ server là trách nhiệm của **TanStack Query**.

## 2. Quy tắc Selector (Tránh Re-render)
- Luôn sử dụng pattern **Selector** khi trích xuất giá trị từ store để tránh component bị re-render không cần thiết khi các state khác thay đổi.
  ```typescript
  // ❌ Sai (Component sẽ re-render khi BẤT KỲ state nào trong store thay đổi)
  const store = useUIStore();
  const isOpen = store.isSidebarOpen;

  // ✅ Đúng (Component CHỈ re-render khi isSidebarOpen thay đổi)
  const isSidebarOpen = useUIStore(state => state.isSidebarOpen);
  ```

## 3. Quy tắc Persist (Lưu trữ cục bộ)
- Khi sử dụng middleware `persist` của Zustand, **KHÔNG BAO GIỜ** sử dụng `window.localStorage` trực tiếp.
- Bắt buộc phải sử dụng `safeLocalStorage` (được định nghĩa trong `src/helpers/storage.helpers.ts` hoặc tương đương) để tránh các lỗi crash trên trình duyệt (ví dụ: chế độ ẩn danh chặn localStorage).

## 4. Partialize (Chọn lọc dữ liệu lưu trữ)
- Khi dùng `persist`, luôn sử dụng thuộc tính `partialize` để chỉ lưu những trạng thái thực sự cần thiết.
- Không lưu các trạng thái tạm thời như `isLoading`, `error`, hoặc các cờ (flags) liên quan đến trạng thái đang xử lý của một action.
  ```typescript
  export const useUIStore = create<UIState>()(
    persist(
      (set) => ({
        isSidebarOpen: true,
        isLoading: false, // Trạng thái tạm thời
        toggleSidebar: () => set((state) => ({ isSidebarOpen: !state.isSidebarOpen })),
      }),
      {
        name: "ui-storage",
        storage: createJSONStorage(() => safeLocalStorage),
        // Chỉ lưu isSidebarOpen xuống storage
        partialize: (state) => ({ isSidebarOpen: state.isSidebarOpen }),
      }
    )
  );
  ```

## 5. Tổ chức file
- Khai báo các file store trong thư mục `src/store/`.
- Đặt tên file theo định dạng `[name].store.ts` (ví dụ: `ui.store.ts`, `auth.store.ts`).
