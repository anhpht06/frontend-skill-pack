---
name: 09-folder-structure
description: Standard folder structure guidelines for the Next.js project (my-nextjs-starter).
---
# Folder Structure

This project applies Clean Architecture and Separation of Concerns principles to clearly separate UI Logic from Pure Logic. All source code is located in the `src/` directory.

## 1. Basic Directory Breakdown

- `src/app/`: **Next.js App Router.** Contains routes, layouts, pages, loading, and error states.
- `src/components/`: **UI Components.**
  - `ui/`: Reusable, generic components (Button, Input) without business logic.
  - `errors/`: Error display components (AppError, ErrorBoundary).
  - `[feature]/`: Feature-specific UI components.
- `src/config/`: **Configurations.** Environment variables, third-party library setups.
- `src/constants/`: **Constants.** Fixed values, e.g., `routes.ts` for URL definitions. Do not hardcode strings elsewhere.
- `src/helpers/`: **Helper Functions.** Pure functions for small logic tasks like formatting dates, strings, or local storage.
- `src/lib/`: **External Library Wrappers.** Initialization or configuration of external libraries (e.g., utils for Tailwind/clsx).
- `src/store/`: **Global State.** UI state management using Zustand. (Not for Server State).

## 2. Service Layer (API & Server State Architecture)

Divided into two distinct tiers: Pure Logic (no React) and UI Logic (contains React Hooks).

### Tier 1: `src/services/` (Pure Logic Tier - No React Hooks)
Calling React hooks is strictly prohibited here.
- `axios/`: Axios instance configuration, interceptors, Refresh Token handling.
- `api/`: API Fetchers and Mappers (Anti-Corruption Layer). All data returned by the BE must be cleaned here.
- `query/`: TanStack Query configurations.
  - `queries/`: Functions returning `queryOptions` objects.
  - `mutations/`: Functions returning mutation configuration objects.
  - `query-keys/`: Centralized query key management (using the query-key-factory library).

### Tier 2: `src/hooks/` (UI Logic Tier - Connects to React)
Exclusively used for calling Hooks and handling direct UI interactions.
- `queries/`: Custom Hooks wrapping `useQuery`, pulling configurations from `services/query/queries` to display data on the UI.
- `mutations/`: Custom Hooks wrapping `useMutation`, pulling configurations from `services/query/mutations`. This is where UI side-effects are handled (Toast notifications, page Redirects...).
- Contains other general custom React hooks (useWindowSize, useDebounce, useAuth...).
