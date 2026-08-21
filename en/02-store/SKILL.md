---
name: 02-store
description: Guidelines for using Zustand for global state management.
---

# Global State Management with Zustand

This document defines the rules for using Zustand for state management.

## 1. Purpose (UI State vs Server State)
- **Zustand MUST ONLY BE USED for Global UI State** (e.g., `isSidebarOpen`, `themeMode`, `activeModal`).
- **NEVER use Zustand to store Server State** (data returned from APIs). Storing, caching, and synchronizing server data is the responsibility of **TanStack Query**.

## 2. Selector Pattern (Preventing Re-renders)
- Always use the **Selector** pattern when extracting values from the store to prevent unnecessary component re-renders when other states change.
  ```typescript
  // ❌ Bad (Component re-renders when ANY state in the store changes)
  const store = useUIStore();
  const isOpen = store.isSidebarOpen;

  // ✅ Good (Component ONLY re-renders when isSidebarOpen changes)
  const isSidebarOpen = useUIStore(state => state.isSidebarOpen);
  ```

## 3. Persist Rules (Local Storage)
- When using Zustand's `persist` middleware, **NEVER** use `window.localStorage` directly.
- You must use `safeLocalStorage` (defined in `src/helpers/storage.helpers.ts` or equivalent) to avoid browser crashes (e.g., when localStorage is blocked in incognito mode).

## 4. Partialize (Selective Persistence)
- When using `persist`, always use the `partialize` property to only save the states that truly need to be persisted.
- Do not persist temporary states like `isLoading`, `error`, or flags related to the processing state of an action.
  ```typescript
  export const useUIStore = create<UIState>()(
    persist(
      (set) => ({
        isSidebarOpen: true,
        isLoading: false, // Temporary state
        toggleSidebar: () => set((state) => ({ isSidebarOpen: !state.isSidebarOpen })),
      }),
      {
        name: "ui-storage",
        storage: createJSONStorage(() => safeLocalStorage),
        // Only persist isSidebarOpen to storage
        partialize: (state) => ({ isSidebarOpen: state.isSidebarOpen }),
      }
    )
  );
  ```

## 5. File Organization
- Declare store files in the `src/store/` directory.
- Name files following the format `[name].store.ts` (e.g., `ui.store.ts`, `auth.store.ts`).
